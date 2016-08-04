# cyrus-and/gdb-dashboard

[Original URL](https://github.com/cyrus-and/gdb-dashboard)

> Modular visual interface for GDB in Python. This comes as a standalone single-file .gdbinit which, among the other things, enables a configurable dashboard showing the most relevant information...

Modular visual interface for GDB in Python.

This comes as a standalone single-file [`.gdbinit`](https://raw.githubusercontent.com/cyrus-and/gdb-dashboard/master/.gdbinit) which, among the other things, enables a configurable dashboard showing the most relevant information during the program execution. Its main goal is to reduce the number of GDB commands issued to inspect the current program status allowing the programmer to focus on the control flow instead.

## [](https://github.com/cyrus-and/gdb-dashboard#installation)Installation

Just place [`.gdbinit`](https://raw.githubusercontent.com/cyrus-and/gdb-dashboard/master/.gdbinit) in your home directory, for example:

```
wget -P ~ git.io/.gdbinit
```

## [](https://github.com/cyrus-and/gdb-dashboard#screenshot)Screenshot

[![Screenshot](https://camo.githubusercontent.com/a45a4a05692295918e27689833d87c83b096f89f/687474703a2f2f692e696d6775722e636f6d2f4973587030524b2e706e67)](https://camo.githubusercontent.com/a45a4a05692295918e27689833d87c83b096f89f/687474703a2f2f692e696d6775722e636f6d2f4973587030524b2e706e67)

## [](https://github.com/cyrus-and/gdb-dashboard#features)Features

- Single GDB init file.

- Interaction with GDB using the native [Python API](https://sourceware.org/gdb/onlinedocs/gdb/Python-API.html).

- Several default modules are included to address the most basic needs: source code, assembly, registers, etc.).

- User-defined modules can be easily developed by extending a [Python class](https://github.com/cyrus-and/gdb-dashboard#custom-modules).

- Additional configuration files (both [GDB](https://sourceware.org/gdb/onlinedocs/gdb/Command-Files.html) and Python) are read from `~/.gdbinit.d/`.

- Fully stylable user interface and dynamic command prompt.

- No GDB command has been redefined, instead all the features are available as subcommands of the main `dashboard` command.

## [](https://github.com/cyrus-and/gdb-dashboard#default-modules)Default modules

Follows the list of bundled default modules. Refer to the GDB help system for the full syntax.

- `assembly` shows the disassembled code surrounding the program counter. The instructions constituting the current statement are marked, if available.

- `history` lists the last entries of the GDB value history.

- `memory` allows to inspect memory regions.

- `registers` shows the CPU registers and their values.

- `source` shows the program source code, if available.

- `stack` shows the current stack trace including the function name and the file location, if available. Optionally list the frame arguments and locals too.

- `threads` lists the currently available threads.

- `expressions` watches user expressions.

## [](https://github.com/cyrus-and/gdb-dashboard#commands)Commands

The GDB documentation is available at `help dashboard`. Just like any GDB command, abbreviations are possible. Moreover, the alias `db` resolves to `dashboard`.

### [](https://github.com/cyrus-and/gdb-dashboard#dashboard)dashboard

This is the root command and it is used to manually redisplay the dashboard.

### [](https://github.com/cyrus-and/gdb-dashboard#dashboard--enabled-onoff)dashboard -enabled [on|off]

Enable or disable the automatic display of the dashboard whenever the target program stops. The dashboard is enabled by default and even when it is disabled, it can be manually displayed with `dashboard`.

### [](https://github.com/cyrus-and/gdb-dashboard#dashboard--layout-directive)dashboard -layout [`<directive>`...]

By default, all the modules are enabled and placed within the dashboard in alphabetical order. As the number of modules grows, it is important to decide which modules will be part of the dashboard, and where.

Each directive is in the form `[!]<module>`, when the `!` is present then the corresponding module is disabled by default. The order of directives denotes the display order within the dashboard. For example:

```
dashboard -layout source !assembly stack
```

Modules which do not appear in the list are disabled and placed after the last element in alphabetical order.

When executed without arguments, this command lists all the available modules.

### [](https://github.com/cyrus-and/gdb-dashboard#dashboard--style-name-value)dashboard -style [`<name>` [`<value>`]]

Access to the stylable attributes of the dashboard, see [Stylable attributes](https://github.com/cyrus-and/gdb-dashboard#stylable-attributes). For example, to change the prompt to something more familiar:

```
dashboard -style prompt '(gdb)'
```

The argument is parsed as a Python literal and converted to the proper type.

When only the name is specified this command shows the current value, whereas without arguments prints all the attributes.

### [](https://github.com/cyrus-and/gdb-dashboard#modules-subcommands)Modules subcommands

Every module adds its own subcommand `dashboard <module>` which is used to toggle the enable flag and to redisplay the dashboard.

Modules may also declare additional subcommands, see `help dashboard <module>` from GDB.

Moreover, if a module declare some stylable attributes then the command `dashboard <module> -style` will be available. Its functioning is equivalent to the [`dashboard -style`](https://github.com/cyrus-and/gdb-dashboard#dashboard--style-name-value) command but it does apply to a module.

## [](https://github.com/cyrus-and/gdb-dashboard#configuration)Configuration

Files in `~/.gdbinit.d/` are executed in alphabetical order, but the preference is given to Python files. If there are subdirectories, they are walked recursively. The idea is to keep separated the custom modules definition from the configuration itself.

The main configuration file can be placed in `~/.gdbinit.d/` (say `~/.gdbinit.d/init`) and should be used to tune the dashboard styles and modules configuration but also the usual GDB parameters.

The alternative is to hard code changes in the provided [`.gdbinit`](https://raw.githubusercontent.com/cyrus-and/gdb-dashboard/master/.gdbinit), to do so just add new modules and GDB settings under `# Default modules` and `# Better GDB defaults` respectively.

## [](https://github.com/cyrus-and/gdb-dashboard#stylable-attributes)Stylable attributes

There is number of attributes that can be used to customize the aspect of the dashboard and of its modules. They are documented within the GDB help system. For what concerns the dashboard itself it can be reached with:

```
help dashboard -style
```

Whereas for modules:

```
help dashboard <module> -style
```

### [](https://github.com/cyrus-and/gdb-dashboard#ansi-escape-codes)ANSI escape codes

Colors and text styles are specified using [ANSI](https://en.wikipedia.org/wiki/ANSI_escape_code) escape codes. For example setting a style to `1;31` will produce `^[[1;31m`, which will result in displaying the text red (`31`) and bright (`1`). The ansi output can be disabled by setting the `ansi` attribute (note that this will not affect the command prompt).

### [](https://github.com/cyrus-and/gdb-dashboard#dividers)Dividers

A divider is basically a terminal-wide horizontal line with an optional label. Primary dividers are those used to separate the modules, whereas secondary dividers may be used inside modules to logically separate different sections. When a section or module is empty then the styles used for the divider are those with the `off` qualifier.

### [](https://github.com/cyrus-and/gdb-dashboard#common-styles)Common styles

These are general purpose [ANSI](https://en.wikipedia.org/wiki/ANSI_escape_code) styles defined for convenience and used within the default modules.

- `style_selected_1`
- `style_selected_2`
- `style_low`
- `style_high`
- `style_error`

## [](https://github.com/cyrus-and/gdb-dashboard#custom-modules)Custom modules

The idea of custom modules is that they provide ways to access readonly information from the the target program status; it is safe to assume that they will be queried during the program execution only.

Custom modules must inherit the `Dashboard.Module` class and define some methods:

- `label` returns the module label which will appear in the divider.

- `lines` return a list of strings which will form the module content. When a module is temporarily unable to produce its content, it should return an empty list; its divider will then use the styles with the `off` qualifier.

The name of a module is automatically obtained by the class name.

Modules are instantiated once at initialization time and kept during the whole the GDB session.

Optionally, a module may include a description which will appear in the GDB help system by specifying a Python docstring for the class.

Optionally, a module may define stylable attributes by defining the `attributes` method returning a dictionary in which the key is the attribute name and the value is another dictionary:

1. `default` is the initial value for this attribute.

2. `doc` is the documentation of this attribute which will appear in the GDB help system. This key can be omitted.

3. `name` is the name of the attribute of the Python object, defaults to the key value.

4. `type` is the type of this attribute, it is used to coerce the value passed as an argument to the proper type, or raise an exception. This key defaults to the `str` type.

5. `check` is a control callback which accept the coerced value and returns `True` if the value satisfies the constraint and `False` otherwise. This key is optional, when omitted no check is performed.

Optionally, a module may declare subcommands by defining the `commands` method returning a dictionary in which the key is the command name and the value is another dictionary:

1. `action` is the callback to be executed which accepts the raw input string from the GDB prompt. Callbacks may raise exceptions to notify erroneous situations which message will be shown automatically to the user.

2. `doc` is the command documentation.

3. `completion` is the completion policy, one of the `gdb.COMPLETE_*` constants defined in the [reference manual](https://sourceware.org/gdb/onlinedocs/gdb/Commands-In-Python.html). This key is optional and defaults to `None` which is equivalent to `gdb.COMPLETE_NONE`.

### [](https://github.com/cyrus-and/gdb-dashboard#common-functions)Common functions

A number of auxiliary common functions are defined in the global scope, they can be found in the provided [`.gdbinit`](https://raw.githubusercontent.com/cyrus-and/gdb-dashboard/master/.gdbinit) and concern topics like [ANSI](https://en.wikipedia.org/wiki/ANSI_escape_code) output, divider formatting, conversion callbacks, etc. They should be more or less self-documented, some usage examples can be found within the bundled default modules.

### [](https://github.com/cyrus-and/gdb-dashboard#example)Example

Default modules already provide a good example, but here is a simple module which may be used as a template for new custom modules, it allows the programmer to note down some snippets of text during the debugging session.

```
class Notes(Dashboard.Module):
 """Simple user-defined notes."""

 def __init__(self):
 self.notes = []

 def label(self):
 return 'Notes'

 def lines(self):
 out = []
 for note in self.notes:
 out.append(note)
 if self.divider:
 out.append(divider())
 return out[:-1] if self.divider else out

 def add(self, arg):
 if arg:
 self.notes.append(arg)
 else:
 raise Exception('Cannot add an empty note')

 def clear(self, arg):
 self.notes = []

 def commands(self):
 return {
 'add': {
 'action': self.add,
 'doc': 'Add a note.'
 },
 'clear': {
 'action': self.clear,
 'doc': 'Remove all the notes.'
 }
 }

 def attributes(self):
 return {
 'divider': {
 'doc': 'Divider visibility flag.',
 'default': True,
 'type': bool
 }
 }
```

To use the above just save it in a Python file, say `notes.py`, inside `~/.gdbinit.d/`, the the following commands (together with the help) will be available:

```
dashboard notes
dashboard notes add
dashboard notes clear
dashboard notes -style
```

## [](https://github.com/cyrus-and/gdb-dashboard#resources)Resources

## [](https://github.com/cyrus-and/gdb-dashboard#license)License

Copyright (c) 2015 Andrea Cardaci [cyrus.and@gmail.com](mailto:cyrus.and@gmail.com)

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
