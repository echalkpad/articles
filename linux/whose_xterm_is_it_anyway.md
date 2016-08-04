# whose xterm is it anyway?

[Original URL](http://www.tedunangst.com/flak/post/whose-xterm-is-it-anyway)

> As part of the effort to find idle hogs, I noticed some xterms were heavier than others. 9960 tedu 2 0 7056K 12M sleep select 0:08 0.00% xterm 15257 tedu 2 0 6808K 12M sleep select 0:01 0.00% xterm...

As part of the effort to [find idle hogs](http://www.tedunangst.com/flak/post/rough-idling), I noticed some xterms were heavier than others.

9960 tedu 2 0 7056K 12M sleep select 0:08 0.00% xterm 15257 tedu 2 0 6808K 12M sleep select 0:01 0.00% xterm 10960 tedu 2 0 6924K 12M idle select 0:01 0.00% xterm 25365 tedu 2 0 6796K 12M sleep select 0:01 0.00% xterm

How did inmate 9960 come to acquire 8 whole seconds of CPU time? For that matter, which xterm is it? The answer to the second will likely reveal the first.

Looking around, all my xterms are currently idle. Just as indicated by top. How do we turn a pid into a window?

## brute force

The brute, or even brutal, force technique is to quit each xterm one by one until 9960 goes away. A nicer approach is to send SIGSTOP to each xterm and see which one stops responding. (Alas, if xterm is setgid, you may not be able to SIGCONT it afterwards. Less nice.) Or run 

<span class="code">find /</span>

 in each xterm while watching top to see who lights up. All a bit intrusive, but wasn't it Heisenberg who proved there can be no observation without modification? Actually no, though observer effect is a real thing. Nevertheless, we can do a better job of observing xterms without pummeling them to see which one bruises.

Let's start with ps.

2157 p9 Ss 0:00.02 -ksh (ksh) 17271 p9 R+ 0:00.00 ps 30407 pa Ss 0:00.05 -ksh (ksh) 79 pa S+ 0:00.16 vim kern_sig.c 20564 pb Is+ 0:00.01 -ksh (ksh) 21379 pc Is+ 0:00.03 -ksh (ksh) 236 pd Is 0:00.02 -ksh (ksh) 3583 pd S+ 0:00.38 top

The second column is controlling terminal. So we have some hints. I now know which terminal is running ps, and which is running top, and which is finding out why SIGCONT doesn't work. But no xterms, unless we run ps x.

9960 md Is 0:07.80 xterm 24106 md Is 0:00.66 xterm 24358 md Is 0:00.25 xterm

xterms don't have controlling terminals; instead they control the terminal. But this is still useful info to have.

> fstat -p 9960 USER CMD PID FD MOUNT INUM MODE R/W SZ|DV tedu xterm 9960 text /usr 702660 -rwxr-sr-x r 596224 tedu xterm 9960 wd /home 1611008 drwxr-xr-x r 2560 tedu xterm 9960 0 / 182659 crw----- rw ttyC0 tedu xterm 9960 1 / 183026 crw-rw-rw- w null tedu xterm 9960 2 / 183026 crw-rw-rw- w null tedu xterm 9960 3* unix stream 0x0 tedu xterm 9960 4 / 182379 crw-rw-rw- rw ptyp1

There it is. We're looking at p1.

> pgrep -lf -t p1 6988 -ksh

Another approach is would be to run 

<span class="code">ps -O ppid</span>

 (or 

<span class="code">pgrep -lf -P 9960</span>

) and look for the shell with a parent of 9960, and walk back up. Either way, it's one of the dozen xterms sitting there with an idle shell, which is a hint not an answer. Running around and pasting 

<span class="code">echo $$</span>

 in each shell would find the suspect. Or I could run 

<span class="code">write tedu ttyp1</span>

 and look for the graffiti.

We can also continue further on this path, inspecting the working directory for each shell, and then narrowing our search to those xterms, but maybe it's time to switch techniques.

## just ask

A smarter approach would be to just ask. In theory, every xterm has a __NET_WM_PID_ property that is equal to its pid. This can be retrieved by running _xprop_ and clicking the window. Or using the -id argument. Then we need all the xterm window IDs, which can be obtained via xwininfo.

> xwininfo -root -children | grep XTerm | awk '{print $1}' | \ xargs -n1 -I % sh -c "echo %; xprop -id % _NET_WM_PID" 0xe0000d _NET_WM_PID(CARDINAL) = 24106 0xc0000d _NET_WM_PID(CARDINAL) = 9960 0xa0000d _NET_WM_PID(CARDINAL) = 25365

Armed with the window ID, we can feed it back to xwininfo.

> xwininfo -id 0xc0000d xwininfo: Window id: 0xc0000d "Thanks for flying Vim" Corners: +-2542+15 -3831+15 -3831-12 +-2542-12 -geometry 115x67+-2542-12

Alight, so this xterm is off screen somewhere, but the geometry maybe gives us another hint as to which it is based on size. And it once upon a time ran vim, which fiddles with the title. Interesting, but we'd like something a little more obvious.

> xwd -id 0xc0000d | xwud X Error of failed request: BadMatch (invalid parameter attributes) Major opcode of failed request: 73 (X_GetImage) Serial number of failed request: 95 Current serial number in output stream: 95 xwud: Error => Unable to read dump file header. xwud: Resource temporarily unavailable

Damn. I was hoping for _Woah! A new exact duplicate of 9960 has appeared. So that's which one it is._ but no dice. Depends on the suspect window being on screen. But if we can get all the windows on screen (dwm "0" screen) either this or the above approach can work.

For funsies, there's a Stack Overflow answer dedicated to [finding the pid for an X11 window](http://unix.stackexchange.com/questions/5478/what-process-created-this-x11-window), which is the reverse process.

## inferno

We're moving well past the point of no return now. Instead of using X to spy on our xterm, we can do so ourselves. This can be done using gdb, for instance. Unfortunately, other people would do it that way. How hard can it be to write a one off single purpose debugger?

Step one of our journey is gazing into the xterm source code. Eventually one will discover that there is a _LineData_ structure with a pointer to what appears to be character data. There's an array of these, one for each line. But there is not an obvious pointer to this array. Instead it's accessed using a variety of casts, offsets, and pointer arithmetic, but the base pointer is _visbuf_ in something called _TScreen_, a giant structure that takes over 500 lines of code to declare. That is embedded in an _XTermWidget_, and (thank the heavens!) there is a global pointer to one of these called _term_, bringing our trek to an end.

All we need to do now is write a debugger that iteratively reads each:

<span class="code">((LineData *)(term-&gt;screen.visbuf + offset))-&gt;chardata</span>

.

OpenBSD includes a useful _sysctl_ for examining the address space of another process. Through arcane magic not explained here (procmap), I know the xterm I'm looking at has a text segment of 540672 bytes. We can find it programmatically thusly:

<span>local</span>

 

<span>function</span>

 

<span>findexecbase</span>

(pid, execsize) 

<span>local</span>

 mib 

<span>=</span>

 ffi.new(

<span>“int[3]”</span>

) mib[

<span>0</span>

] 

<span>=</span>

 CTL_KERN mib[

<span>1</span>

] 

<span>=</span>

 KERN_PROC_VMMAP mib[

<span>2</span>

] 

<span>=</span>

 pid 

<span>local</span>

 numents 

<span>=</span>

 

<span>200</span>

 

<span>local</span>

 ents 

<span>=</span>

 ffi.new(

<span>“struct vmentry[?]”</span>

, numents) 

<span>local</span>

 entsize 

<span>=</span>

 ffi.sizeof(

<span>“struct vmentry”</span>

) 

<span>local</span>

 oldsize 

<span>=</span>

 ffi.new(

<span>“size_t[1]”</span>

) oldsize[

<span>0</span>

] 

<span>=</span>

 entsize 

<span>*</span>

 numents 

<span>local</span>

 rv 

<span>=</span>

 C.sysctl(mib, 

<span>3</span>

, ents, oldsize, 

<span>nil</span>

, 

<span>0</span>

) 

<span>if</span>

 rv 

<span>==</span>

 

<span>-1</span>

 

<span>then</span>

 

<span>return</span>

 

<span>nil</span>

 

<span>end</span>

 

<span>for</span>

 i 

<span>=</span>

 

<span>0</span>

, 

<span>tonumber</span>

(oldsize[

<span>0</span>

]) 

<span>/</span>

 numents 

<span>-</span>

 

<span>1</span>

 

<span>do</span>

 

<span>local</span>

 ent 

<span>=</span>

 ents[i] 

<span>if</span>

 (

<span>tonumber</span>

(ent.kve_end) 

<span>-</span>

 

<span>tonumber</span>

(ent.kve_start)) 

<span>==</span>

 execsize 

<span>and</span>

 ent.kve_prot 

<span>==</span>

 PROT_RW 

<span>then</span>

 

<span>return</span>

 ent.kve_start 

<span>end</span>

 

<span>end</span>

 

<span>end</span>

 

<span>local</span>

 addr 

<span>=</span>

 findexecbase(pid, 

<span>540672</span>

)

Using further magic (I'm cheating a bit, but basically 

<span class="code">nm xterm | grep term$</span>

), we know the offset from there to _term_, and then we can start chasing pointers with _ptrace_. Offsets calculated by compiling an xterm with a _printf_ of interesting values.

<span>local</span>

 

<span>function</span>

 

<span>pread</span>

(addr) 

<span>local</span>

 v 

<span>=</span>

 C.ptrace(PT_READ_D, pid, addr, 

<span>0</span>

) v 

<span>=</span>

 

<span>tonumber</span>

(v) 

<span>if</span>

 v 

<span>&lt;</span>

 

<span>0</span>

 

<span>then</span>

 v 

<span>=</span>

 v 

<span>+</span>

 

<span>4294967296</span>

 

<span>end</span>

 

<span>return</span>

 v 

<span>end</span>

 

<span>local</span>

 

<span>function</span>

 

<span>preadptr</span>

(addr) 

<span>local</span>

 p1 

<span>=</span>

 pread(addr) 

<span>local</span>

 p2 

<span>=</span>

 pread(addr 

<span>+</span>

 

<span>4</span>

) 

<span>return</span>

 p1 

<span>+</span>

 p2 

<span>*</span>

 

<span>4294967296</span>

 

<span>end</span>

 rv 

<span>=</span>

 C.ptrace(PT_ATTACH, pid, 

<span>0</span>

, 

<span>0</span>

) rv 

<span>=</span>

 C.waitpid(pid, 

<span>nil</span>

, 

<span>0</span>

) addr 

<span>=</span>

 addr 

<span>+</span>

 

<span>4912632</span>

 

<span>– offset of term</span>

 addr 

<span>=</span>

 preadptr(addr) 

<span>–read term</span>

 addr 

<span>=</span>

 addr 

<span>+</span>

 

<span>392</span>

 

<span>– offset of term-&gt;screen</span>

 addr 

<span>=</span>

 addr 

<span>+</span>

 

<span>15496</span>

 

<span>– offset of screen.visbuf</span>

 addr 

<span>=</span>

 preadptr(addr) 

<span>print</span>

(

<span>“SCREEN DUMP”</span>

) 

<span>for</span>

 row 

<span>=</span>

 

<span>0</span>

, 

<span>10</span>

 

<span>do</span>

 

<span>local</span>

 datadr 

<span>=</span>

 preadptr(addr 

<span>+</span>

 row 

<span>*</span>

 

<span>48</span>

 

<span>+</span>

 

<span>24</span>

) 

<span>local</span>

 s 

<span>=</span>

 { } 

<span>for</span>

 i 

<span>=</span>

 

<span>0</span>

, 

<span>80</span>

 

<span>do</span>

 

<span>local</span>

 v 

<span>=</span>

 pread(datadr 

<span>+</span>

 i 

<span>*</span>

 

<span>4</span>

) 

<span>table.insert</span>

(s, 

<span>string.char</span>

(v)) 

<span>end</span>

 

<span>print</span>

(

<span>table.concat</span>

(s)) 

<span>end</span>

 rv 

<span>=</span>

 C.ptrace(PT_DETACH, pid, 

<span>0</span>

, 

<span>0</span>

)

Let it rip and...

SCREEN DUMP if (row >= 0 && row <= max_row) { result = (LineData *) scrnHeadAddr(screen, buffer, (unsigned) row); if (result != 0) { #if 1 /bin /boot /cdrom /core /dev /etc /home /initrd.img /lacie /lib /lib32 /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vol FIXME - these should be done in setupLineData, result->lineSize = (Dimension) MaxCols(screen); #if OPT_WIDE_CHARS if (screen->wide_chars) { result->combSize = (Char) screen->max_combining; } else { result->combSize = 0; }

Hey! Now that does look familiar. It's the source code to the line getting function in xterm. Now I know exactly which window it is.

## epilogue

This was a pretty big waste of time. As soon as I saw that one xterm was busier than the rest, I knew exactly which one it was: the one I read mail in, which has to redraw the screen for every email. This was trivially confirmed using any of the brute force techniques which work well enough with some educated guesswork guiding them. Learning to script gdb may have been faster, but a lot less fun.
