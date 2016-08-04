# Lehm

[Original URL](https://mustardamus.github.io/lehm/)

> Commands Once you've installed Lehm with npm install lehm -g you'll have access to the lehm executable. It only has three commands. If you need a refresher on them, just run it without any command,...

## Commands

Once you've installed Lehm with `npm install lehm -g` you'll have access to the `lehm` executable. It only has three commands. If you need a refresher on them, just run it without any command, which will be the same as `lehm --help`.

`lehm list` $ lehm list Templates Path: ~/templates

```
js-component
js-component: component.js | template.html | style.css

Node.js Module - Scaffold for a Node.js Module
node-module: {{ moduleName }}/{{ moduleName }}.js | {{ moduleName }}/package.json
```

List all templates and their metas and files that are found in the configured templates path. Also see [Template Metas](https://mustardamus.github.io/lehm/#metas).

`lehm create [name]` $ lehm create ? Choose Template (Use arrow keys) ❯ js-component Node.js Module (node-module) - Scaffold for a Node.js Module

```
$ lehm create node-module
? Name of your Node Module: Lehm
? ...
```

Start creating files from a template. If you do not pass the `[name]`, you will be presented with a list of all templates to choose from.

`lehm config` $ lehm config ? Templates Path /Users/mustardamus/Code/templates ? Handlebars Delimiters ({{ }}) Saved.

Save the templates path and handlebars delimiters to `~/.lehmrc`. Also see [Configuration](https://mustardamus.github.io/lehm/#configuration).

## Template Tags

Lehm uses [Handlebars.js](http://handlebarsjs.com) to parse and transform the templates. By default the delimiters are `{{ }}`, but you can change them if they clash with the content of your templates. See how in the [Configuration](https://mustardamus.github.io/lehm/#configuration) or [Template Metas](https://mustardamus.github.io/lehm/#metas) section.

### Create template directory

Lets learn the Template Tags by creating our first template, lets call it `js-component`.

We create it in the default Templates Directory, which is in the Home folder of the current user, under the sub-directory `~/templates`. Again, you can [configure it](https://mustardamus.github.io/lehm/#configuration) if you want it to be in another path.

Lets use the `lehm list` command to check if the newly created template was picked up.

```
~ $ mkdir -p templates/js-component
~ $ cd templates/js-component
~/templates/js-component $ touch component.js
~/templates/js-component $ touch template.html
~/templates/js-component $ touch style.css
~/templates/js-component $ lehm list
Templates Path: ~/templates

js-component
js-component: component.js | style.css | template.html
```

### Comments and Variable Declarations

Lehm slightly extends Handlebars Comments. Comments are indicated by an `!` inside the delimiters. In generated files, the comments will be removed.

If the comment does not include a `=` and/or `#` character, it will be a plain comment and not parsed.

#### `variableName = default value`

You can assign a `default value` to a `variableName` by using the `=` character. When you create the template with `lehm create` you will be offered a `default value`, you either can hit enter to use it or overwrite it with another string. Note that the `default value` is always a string, with no need for `' '` or `" "`.

```
~/templates/js-component $ cat component.js
{{! this is a plain comment }}
{{! componentName # Name of your component }}
{{! namespace = components }}
{{! initialComment = A JS Component # First line comment }}

~/templates/js-component $ cd
~ $ mkdir test-comp && cd test-comp
~/test-comp $ lehm create js-component
? Name of your component: test-comp
? namespace (components): app
? First line comment (A JS Component): neat :)
```

#### `variableName # useful description`

You can assign a `useful description` to `variableName` with the `#` character. When you create your template, the description will be shown in the prompts instead of the `variableName`.

#### `variableName = default # description`

A combination of both, `default value` and `useful description`, assigned to the `variableName`. The `description` must always come last.

### Using, re-using and declaring Variables on the fly

The `component.js` template from above won't have any content, because we only have declared some variables but actually never used them. Lets try that.

Variables are sitting between the delimiters, by default they are `{{ }}`, but they can be set to custom delimeters either using the `lehm config` command or by having a `.lehmrc` somewhere. See the the [Configuration section](https://mustardamus.github.io/lehm/#configuration) for details. Another way is to set per-template-delimiters with [Template Metas](https://mustardamus.github.io/lehm/#metas).

Variables don't need a declaration, just by using a variable will declared it, but without a default value or description. So use a good name for it to make it clear what the variable is about, like the `htmlContent` variable in the `template.html` example.

Lehm parses all files in the template for variables before prompting for the values. That means that you can and must re-use variables across files. If in one file a variable has been declared without a default value or description, and in another a default value is set, it will be assigned to the variable. However, once the default value or description has been assigned, it can not be overwritten by a third declaration.

```
~/templates/js-component $ cat component.js
{{! this is a plain comment }}
{{! componentName # Name of your component }}
{{! namespace = components }}
{{! initialComment = A JS Component # First line comment }}
// {{ initialComment }}

~/templates/js-component $ cat template.html
<div class="{{ namespace }} {{ componentName }}">
 {{ htmlContent }}
</div>

~/templates/js-component $ cd
~ $ rm -rf test-comp && mkdir test-comp && cd test-comp
~/test-comp $ lehm create js-component
? Name of your component: test-comp
? namespace (components): app
? First line comment (A JS Component): neat :)
? htmlContent: Pretty kewl
~/test-comp $ cat component.js
// neat :)

~/test-comp $ cat template.html
<div class="app test-comp">
 Pretty kewl
</div>
```

### Boolean Variables with `#if` and `#unless`

Lehm will parse Handlebars' `#if` and `#unless` block-statements and turn them into boolean variables that can either be `true` or `false`.

In the prompt you can choose the `true` or `false` value from a list with your arrow keys.

If you use `#if` in your template, the default value of the variable will be `true`. If you use `#unless` the default value will be `false`.

You can use the `else` statement to cover both, `true` and `false` cases like shown in the example file `style.css`.

You can assign a description to a boolean variable with the `#` character in a comment like you do with simple variables. Setting a default value would make no sense, just use either `#if` or `#unless`, depending if you want the default value to be `true` or `false`.

Inside of the block-statements you can use variables normally, or nest other `#if` or `#unless` block-statements.

```
~/templates/js-component $ cat style.css
.{{ componentName }} {
 {{#if darkStyle }}
 background: black;
 color: white;
 {{else}}
 background: white;
 color: black;
 {{/if}}
}

~/templates/js-component $ cd
~ $ rm -rf test-comp && mkdir test-comp && cd test-comp
~/test-comp $ lehm create js-component
? Name of your component: test-comp
? namespace (components): app
? First line comment (A JS Component): neat :)
? htmlContent: Pretty kewl
? darkStyle (Use arrow keys)
❯ true
 false
~/test-comp $ cat style.css
.test-comp {
 background: black;
 color: white;
}
```

### Use variables inside file- and folder-names

File- and folder-names are parsed for template tags as well. So whenever you use the delimiters you declare a variable just like you would do in a file's content. This is very useful for creating named files and folders.

You even can set the default value and the description of the variable. Just use the Handlebars comment `!` in combination with `=` and/or `#` in any file's content of the template like you've seen before.

```
~/templates $ mkdir node-module && cd node-module
~/templates/node-module $ mkdir "{{ moduleName }}"
~/templates/node-module $ cd "{{ moduleName }}"
~/templates/node-module/{{ moduleName }} $ echo "{{! moduleName # Name of your Node Module }}\n// :O">"{{ moduleName }}.js"
~/templates/node-module/{{ moduleName }} $ ls
{{ moduleName }}.js
~/templates/node-module/{{ moduleName }} $ cat "{{ moduleName }}.js"
{{! moduleName # Name of your Node Module }}
// :O

~/templates/node-module/{{ moduleName }} $ cd
~ lehm create node-module
? Name of your Node Module: clay
~ cat clay/clay.js
// :O
```

## Template Hooks

With the `before` and `after` hook you can handle work that template parsing alone would not get done.

These are functions that are stored in the same file as the template-metas, a `.js` file that has the same name as the parent folder. `node-module.js` in the example.

### `before`

The `before` hook is called before any file is generated, but after the template-files have been parsed for variables and the user has been prompted for values.

Most of the time you want to extend Handlebars with custom helpers (`utils.Handlebars.registerHelper`) that you can use in your template-files. Another common task is to manipulate the `variables` that are passed into the template.

The hook function is called with the following parameters:

`srcPath` - The full path where the template-files are located.

`distPath` - The full path where files will be generated.

`variables` - An object with variable-names as keys, and the user entered input as values. Modify a value in this object, and it will be used in template-files.

`utils` - An object with instances of the following tools:

- [utils.Handlebars](http://handlebarsjs.com) - A slightly modified Handlebars.js. This version is aware of custom delimiters. Most of the time you want to use `utils.Handlebars.registerHelper` to register custom helpers you can use in your template-files. It has a extra function `utils.Handlebars.transform(str, data)` to transform template strings with custom delimiters (see example).
- [utils.Inquirer](https://github.com/SBoudrias/Inquirer.js) - With Inquirer.js you are able to prompt the user and execute different actions depending on the answers.
- [utils.Shell](https://github.com/shelljs/shelljs) - With Shell.js you are able to execute system commands. Useful for all kinds of stuff: download files, install dependencies, etc.
- [utils.Fs](https://github.com/jprichardson/node-fs-extra) - With fs-extra you can do any kind of action you can do with the normal Node.js' fs module, and a couple useful more.
- [utils._](https://lodash.com/docs) - With Lodash you receive a rather big toolbelt for all sorts of things to work with Arrays, Objects, Functions and Strings.
- [utils.Chalk](https://github.com/chalk/chalk) - With Chalk you can style terminal output. This is useful to show additional infos to the user nicely formatted.

`cb` - The callback you need to call when everything is ready to proceed generating the files. Call this function without any parameter to continue. Call it with a error message, a String, to abort the generation process and show the message.

```
~/templates/node-module $ cat node-module.js
module.exports = {
 delimiters: '',

 before: function (srcPath, distPath, variables, utils, cb) {
 utils.Handlebars.registerHelper('loud', (val) => {
 return utils._.toUpper(val) + '!'
 })

 utils.Inquirer.prompt([{
 type: 'list',
 name: 'abort',
 message: 'Abort?',
 choices: ['No', 'Yes']
 }]).then((answers) => {
 if (answers.abort === 'Yes') {
 cb('Aborted by user')
 } else {
 cb()
 }
 })
 },

 after: function (srcPath, distPath, variables, utils) {
 utils.Shell.cd(distPath + '/' + variables.moduleName)
 utils.Shell.exec('npm init -y')
 utils.Shell.exec('npm install lodash --save')

 let str = utils.Handlebars.transform('')
 console.log(utils.Chalk.blue(str))
 }
}

~/templates/node-module $ cd && rm -rf clay
~ $ lehm create node-module
? Name of your Node Module: clay
? Abort? No
Wrote to ~/templates/clay/package.json:
...
clay@1.0.0 ~/templates/clay
└── lodash@4.13.1
...
LETS ROCK!
~ $ ls clay
node_modules package.json clay.js
```

### `after`

The `after` hook is called after all the files have been generated.

Here you can finish the generation process, like installing dependencies or move files around or show any additional info that is useful to the user.

It has has the same function parameters like the `before` hook, except there is no `cb` to indicate to continue. As far as Lehm is concerned, the process is done when all files are generated.

## Handlebar Helpers

Lehm extends Handlebars with these basic helpers, so you can use them in your template-files without the need for a `before` hook. But remember, you can add new helpers by using the `utils.Handlebars.registerHelper` method in the `before` hook.

`{{ lowerCase "LOWERCASE" }} = lowercase`

`{{ upperCase "uppercase" }} = UPPERCASE`

`{{ snakeCase "snake-case" }} = snake_case`

`{{ camelCase "camel_case" }} = camelCase`

`{{ kebabCase "kebabCase" }} = kebab-case`

`{{ capitalize "capitalize" }} = Capitalize`

`{{ pluralize "user" }} = users`

`{{ singularize "users" }} = user`

`{{ combine "singularize,capitalize" "users" }} = User` With this helper you can combine multiple helpers that take a single String as parameter. That means you can combine any helper from above and any custom helper you may have written. The first argument is a comma seperated String with all helpers that you want to apply one after the other. The second argument is your String you want to transform (ie a variable in your template-files).

If you write custom helpers you need to make sure that the variable is always the last parameter, just like in the combine helper above.

## Configuration

There are only two things you could configure with Lehm: the path where the templates are found, and which delimiters to use. Remember, you can overwrite the delimiters specifically for a template with the [Template Metas](https://mustardamus.github.io/lehm/#metas).

Out of the box Lehm looks in the path `~/templates` for templates, and `{{ }}` are used as default delimiters.

You can permanently overwrite these defaults by creating a `.lehmrc` file in your users home folder. This is a JSON formatted file with the following fields:

`templatesPath` - Full path where the global templates can be found.

`handlebarsDelimiters` - A String to define the delimiters used by Handlebars.js. The delimiters must have a space in between them.

For convenience you can use the `lehm config` command to set these values. It will save them to `~/.lehmrc`. Easy-peasy.

If you use the `lehm` command, it will look for a `.lehmrc` in the current working directory before checking the home directory or using the defaults.

That way you can have a project-specific configuration for Lehm. Use a relative path in `templatesPath` to store the templates in the same directory as your project.

That's useful if you want to have global temlpates for scaffolding whole projects, but want to have components templates for a specific project stored in the same folder to keep it all together, and for other people to use.

```
~ $ lehm list
Templates Path: /Users/mustardamus/templates

js-component
js-component: component.js | style.css | template.html

node-module
node-module: /.js
~ $ mkdir -p templates-alt/test
~ $ echo "">templates-alt/test/test.txt
~ $ lehm config
? Templates Path: ~/templates-alt
? Handlebars Delimiters: 
Saved.
~ $ cat .lehmrc
{
 "templatesPath": "/Users/mustardamus/templates-alt",
 "handlebarsDelimiters": ""
}

~ $ lehm list
Templates Path: /Users/mustardamus/templates-alt

test
test: test.txt
~ $ mkdir -p project/tpl/local/
~ $ cd project
~/project $ echo ":)">tpl/local/only.txt
~/project $ cat .lehmrc
{
 "templatesPath": "./tpl",
 "handlebarsDelimiters": ""
}

~/project $ lehm list
Templates Path: ~/project/tpl

local
local: only.txt
```
