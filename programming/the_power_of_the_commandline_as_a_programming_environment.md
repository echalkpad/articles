# The power of the command-line as a programming environment

[Original URL](http://pindexis.github.io/command-line/2015/12/23/power-of-commmand-line.html)

> While the programming world is full of sophisticated IDEs and text editors that are easy to use. Lot of people including myself still prefer the command-line as their main programming environment....

While the programming world is full of sophisticated IDEs and text editors that are easy to use. Lot of people including myself still prefer the command-line as their main programming environment. And that's for a good reason. I think there are a couple of concepts that make the command-line a powerful development environment:

- Composition: This is the heart of UNIX, composing small programs that do one thing well together to form a bigger one, similar to connecting Lego blocks.<br>
  While there is a learning curve of getting used with different commands, the big advantages of the composition philosophy are flexibility and consistency. For a person who spend most of his time writing code, compromising a learning curve for flexibility is a rewarding choice. You'll eventually be a winner since you won't have to fight against a typical IDE monster to accomplish a behavior that is not shipped by default. And most importantly, you won't have to learn/install a new environment by language. This is what I mean by consistency, you won't have to learn how to perform a recursive regex search for each IDE you manipulate. A grep is a grep wherever you are.

- Extensibility: This is another source of power and a consequence of a simple composition protocol ("Write programs to handle text streams, because that is a universal interface"). If you want to add a custom tool to your toolkit, just build it. The simple communication protocol make it quite easy to compose a new program with the rest of programs, using any language, and any library. You don't have to stick with language X just because you're using an X IDE. Take this opportunity to learn a new language and effectively employ it.

- Keyboard Instead of Mouse(or typing rather than selecting): Getting used to Mouse-free development for programs/tools that you frequently use takes time but increases productivity. This is not as a strong point as the previous ones, but it's worth mentioning.

The command-line gives you power with a cost. For a person who use his computer as a music player, this power is not needed. But for a programmer, it's certainly a good thing.

Now why the command-line is not the main choice among developers? Personally, I think it's because it's not easy to use([in contrast with simple](http://www.infoq.com/presentations/Simple-Made-Easy)) and lacks some important features, in contrast with other modern IDEs and text editors(Atom,sublime...). The good thing is that these issues are not fundamental ones but can be resolved. We just need to reinvent the command-line to be a framework of development rather than just an input/output system. featuring an extensible ground for customization and extensibility, a good command autocomplete system, real-time check (and evaluation), modern interface(supporting more colors), built-in panes, A help (extract -> tar -xvzf), an intuitive shortcuts system, a package manager etc... The text editor will be just a tool in the box with one responsibility of editing files. File navigation, Find/Replace, panes etc... are handled by other programs.
