# Using the OS X 10.10 Hypervisor Framework: A Simple DOS Emulator

[Original URL](http://www.pagetable.com/?p=764)

> Since Version 10.10 (Yosemite), OS X contains Hypervisor.framework, which provides a thin user mode abstraction of the Intel VT features. It enables apps to use virtualization without the need of a...

Since Version 10.10 (Yosemite), OS X contains [Hypervisor.framework](https://developer.apple.com/library/mac/releasenotes/MacOSX/WhatsNewInOSX/Articles/MacOSX10_10.html), which provides a thin user mode abstraction of the Intel VT features. It enables apps to use virtualization without the need of a kernel extension (KEXT) – which makes them compatible with the [OS X App Store guidelines](https://developer.apple.com/app-store/review/guidelines/mac/).

The idea is that the OS takes care of memory management (including nested paging) as well as scheduling virtual CPUs like normal threads. All we have to do is create a virtual CPU (or more!), set up all its state, assign it some memory, and run it... and then handle all "VM exits" – Intel lingo for hypervisor traps.

There is no real documentation, but the headers contain a decent amount of information. Here are some declarations from `Hypervisor/hv.h`:

<span class="s1">/*!</span>

<span class="s1">* </span>

<span class="s2">@function</span>

<span class="s1"> hv_vm_create</span>

<span class="s1">* </span>

<span class="s2">@abstract</span>

<span class="s1"> Creates a VM instance for the current task</span>

<span class="s1">* </span>

<span class="s2">@param</span>

<span class="s1">flagsRESERVED</span>

<span class="s1">* </span>

<span class="s2">@result</span>

<span class="s1"> 0 on success or error code</span>

<span class="s3">extern</span>

<span class="s1"> hv_return_t hv_vm_create(hv_vm_options_t flags) __HV_10_10;</span>

<span class="s1">/*!</span>

<span class="s1">* </span>

<span class="s2">@function</span>

<span class="s1"> hv_vm_map</span>

<span class="s1">* </span>

<span class="s2">@abstract</span>

<span class="s1"> Maps a region in the virtual address space of the current task</span>

<span class="s1">* into the guest physical address space of the VM</span>

<span class="s1">* </span>

<span class="s2">@param</span>

<span class="s1">uvaPage aligned virtual address in the current task</span>

<span class="s1">* </span>

<span class="s2">@param</span>

<span class="s1">gpaPage aligned address in the guest physical address space</span>

<span class="s1">* </span>

<span class="s2">@param</span>

<span class="s1">size Size in bytes of the region to be mapped</span>

<span class="s1">* </span>

<span class="s2">@param</span>

<span class="s1">flagsREAD, WRITE and EXECUTE permissions of the region</span>

<span class="s1">* </span>

<span class="s2">@result</span>

<span class="s1"> 0 on success or error code</span>

<span class="s3">extern</span>

<span class="s1"> hv_return_t hv_vm_map(hv_uvaddr_t uva, hv_gpaddr_t gpa, size_t size,</span>

<span class="s1">hv_memory_flags_t flags) __HV_10_10;</span>

<span class="s1">/*!</span>

<span class="s1">* </span>

<span class="s2">@function</span>

<span class="s1"> hv_vcpu_create</span>

<span class="s1">* </span>

<span class="s2">@abstract</span>

<span class="s1"> Creates a vCPU instance for the current thread</span>

<span class="s1">* </span>

<span class="s2">@param</span>

<span class="s1">vcpu Pointer to the vCPU ID (written on success)</span>

<span class="s1">* </span>

<span class="s2">@param</span>

<span class="s1">flagsRESERVED</span>

<span class="s1">* </span>

<span class="s2">@result</span>

<span class="s1"> 0 on success or error code</span>

<span class="s3">extern</span>

<span class="s1"> hv_return_t hv_vcpu_create(hv_vcpuid_t *vcpu,</span>

<span class="s1">hv_vcpu_options_t flags) __HV_10_10;</span>

<span class="s1">/*!</span>

<span class="s1">* </span>

<span class="s2">@function</span>

<span class="s1"> hv_vcpu_run</span>

<span class="s1">* </span>

<span class="s2">@abstract</span>

<span class="s1"> Executes a vCPU</span>

<span class="s1">* </span>

<span class="s2">@param</span>

<span class="s1">vcpuvCPU ID</span>

<span class="s1">* </span>

<span class="s2">@result</span>

<span class="s1"> 0 on success or error code</span>

<span class="s4">* </span>

<span class="s1">@discussion</span>

<span class="s1">* Call blocks until the next VMEXIT of the vCPU</span>

<span class="s1">* Must be called by the owning thread</span>

<span class="s3">extern</span>

<span class="s1"> hv_return_t hv_vcpu_run(hv_vcpuid_t vcpu) __HV_10_10;</span>

So let's create a virtual machine that runs simple DOS applications in 16 bit real mode, and trap all "`int`" DOS system calls – similar to [DOSBox](http://www.dosbox.com).

First, we need to create a VM:

```
hv_vm_create(HV_VM_DEFAULT);
```

This creates a VM for the current Mach task (i.e. UNIX process). It's implicit, so it doesn't return anything. Then we allocate some memory and assign it to the VM:

```
#define VM_MEM_SIZE (1 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 1024 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 1024)
void *vm_mem = valloc(VM_MEM_SIZE);
hv_vm_map(vm_mem, 0, VM_MEM_SIZE, HV_MEMORY_READ | 
 HV_MEMORY_WRITE | 
 HV_MEMORY_EXEC);
```

And we need to create a virtual CPU:

```
hv_vcpuid_t vcpu;
hv_vcpu_create(&vcpu, HV_VCPU_DEFAULT);
```

Now comes the annoying part: Set up the CPU state. If the state is illegal or inconsistent, the CPU will refuse to run. You will need to refer to the [Intel Manual 3C](http://www.intel.com/content/dam/www/public/us/en/documents/manuals/64-ia-32-architectures-software-developer-vol-3c-part-3-manual.pdf) for all the context. Luckily, most virtual machines start from 16 bit real mode, and mode changes will be done by the boot loader or operating system inside the VM, so you won't have to worry about setting up any other state than real mode state. Real mode state setup looks something like this:

```
hv_vmx_vcpu_write_vmcs(vcpu, VMCS_GUEST_CS_SELECTOR, 0);
hv_vmx_vcpu_write_vmcs(vcpu, VMCS_GUEST_CS_LIMIT, 0xffff);
hv_vmx_vcpu_write_vmcs(vcpu, VMCS_GUEST_CS_ACCESS_RIGHTS, 0x9b);
hv_vmx_vcpu_write_vmcs(vcpu, VMCS_GUEST_CS_BASE, 0);

hv_vmx_vcpu_write_vmcs(vcpu, VMCS_GUEST_DS_SELECTOR, 0);
hv_vmx_vcpu_write_vmcs(vcpu, VMCS_GUEST_DS_LIMIT, 0xffff);
hv_vmx_vcpu_write_vmcs(vcpu, VMCS_GUEST_DS_ACCESS_RIGHTS, 0x93);
hv_vmx_vcpu_write_vmcs(vcpu, VMCS_GUEST_DS_BASE, 0);

[...]

hv_vmx_vcpu_write_vmcs(vcpu, VMCS_GUEST_CR0, 0x20);
hv_vmx_vcpu_write_vmcs(vcpu, VMCS_GUEST_CR3, 0x0);
hv_vmx_vcpu_write_vmcs(vcpu, VMCS_GUEST_CR4, 0x2000);
```

After that, we should populate RAM with the code we want to execute:

```
FILE *f = fopen(argv[1], "r");
fread((char *)vm_mem + 0x100, 1, 64 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 1024, f);
fclose(f);
```

...and assign the GPRs the proper initial state – including the instruction pointer, which will point to the code:

```
hv_vcpu_write_register(vcpu, HV_X86_RIP, 0x100);
hv_vcpu_write_register(vcpu, HV_X86_RFLAGS, 0x2);
hv_vcpu_write_register(vcpu, HV_X86_RSP, 0x0);
```

The virtual CPU is fully set up, we can now run it!

```
hv_vcpu_run(vcpu);
```

This call runs the virtual CPU (while blocking the calling thread) until its time slice expires or a "VM exit" happens. A VM exit is a hypervisor-class exception, i.e. an event in the VM that the hypervisor wants to trap. We can trap events like exceptions, certain privileged instructions (`CPUID`, `HLT`, `RDTSC`, `RDMSR`, ...) and control register ([CR0, CR2, CR3, CR4, ...](http://www.pagetable.com/?p=364)) accesses.

After `hv_vcpu_run()` returns, we need to read the exit reason and act upon it, and run the virtual CPU again. Here is a minimal loop to handle VM exits:

```
for (;;) {
    hv_vcpu_run(vcpu);

    uint64_t exit_reason = hv_vmx_vcpu_read_vmcs(vcpu, VMCS_EXIT_REASON);

    switch (exit_reason) {
        case EXIT_REASON_EXCEPTION:
            [...]
            break;
        case EXIT_REASON_EXT_INTR:
        case EXIT_REASON_EPT_FAULT:
            break;
        default:
            exit(1);
    }
}
```

`EXIT_REASON_EXT_INTR` is caused by host interrupts (usually it means that the time slice is up), so we will just ignore it. `EXIT_REASON_EPT_FAULT` happens every time the guest accesses a page for the first time, or when the guest accesses an unmapped page – this way we can emulate MMIO. In our case, we can also ignore those.

For emulating DOS, we are catching `EXIT_REASON_EXCEPTION`, which is caused by the `int` instruction (if caught). We can get the number of the interrupt from the virtual CPU state without decoding instructions:

```
uint8_t interrupt_number = hv_vmx_vcpu_read_vmcs(vcpu, VMCS_IDT_VECTORING_INFO) & 0xFF;
```

...and emulate the system call. We can read and write GPRs using the `hv_vcpu_read_register()` and `hv_vcpu_write_register()` calls.

## hvdos – a simple DOS Emulator for OS X

The full source of _hvdos_, a simple DOS emulator using the OS X Hypervisor framework, is available at [github.com/mist64/hvdos](https://github.com/mist64/hvdos).

It contains an adapted version of the [libcpu](https://github.com/libcpu/libcpu) DOS system call library and manages to run (parts of) some `.COM` files. A good demo is the [pkunzjr.com](https://github.com/libcpu/libcpu/blob/2fa4a9574a3320bd3953d1b238c36f55090405fb/test/bin/x86/pkunzjr.com?raw=true) ZIP decompression tool.

## Creating your own Hypervisor

_hvdos_ can serve as a template for your own Hypervisor.framework experiments. It contains wrapper functions for error handling, a header that defines all Intel VT constants (taken from FreeBSD), complete 16 bit real mode initialization, as well as a few helper functions to set up the fields `VMCS_PIN_BASED_CTLS`, `VMCS_PRI_PROC_BASED_CTLS`, `VMCS_SEC_PROC_BASED_CTLS` and `VMCS_ENTRY_CTLS` properly. These are needed to define, among other things, which events cause VM exits.

You can easily add more CPUs by creating one POSIX thread per virtual CPU. For every thread, you create a virtual CPU and run a VM exit main loop.

You can for example start writing an IBM PC emulator by running [Bochs BIOS](http://bochs.sourceforge.net/doc/docbook/user/rom-images.html) and trapping I/O accesses, or running MS-DOS without BIOS by trapping BIOS `int` calls.

Or you could bridge an existing open source solution (QEMU, QEMU+KVM, VirtualBox, DOSBox, ...) to use Hypervisor.framework...
