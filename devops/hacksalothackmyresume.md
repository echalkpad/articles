# hacksalot/HackMyResume

[Original URL](https://github.com/hacksalot/HackMyResume)

> Create polished résumés and CVs in multiple formats from your command line or shell. Author in clean Markdown and JSON, export to Word, HTML, PDF, LaTeX, plain text, and other arbitrary...

[![Build status](https://camo.githubusercontent.com/d6f6c62998b8bdfbf851c26fffe11ff478f69e89/68747470733a2f2f696d672e736869656c64732e696f2f7472617669732f70616c6f6d616a732f70616c6f6d612e7376673f7374796c653d666c61742d737175617265)](https://travis-ci.org/hacksalot/HackMyResume)

_Create polished résumés and CVs in multiple formats from your command line or shell. Author in clean Markdown and JSON, export to Word, HTML, PDF, LaTeX, plain text, and other arbitrary formats. Fight the power, save trees. Compatible with [FRESH](https://github.com/fluentdesk/FRESCA) and [JRS](http://jsonresume.org) resumes._

[![](https://github.com/hacksalot/HackMyResume/raw/master/assets/resume-bouqet.png)](https://github.com/hacksalot/HackMyResume/blob/master/assets/resume-bouqet.png)

HackMyResume is a dev-friendly, local-only Swiss Army knife for resumes and CVs. Use it to:

1. **Generate** HTML, Markdown, LaTeX, MS Word, PDF, plain text, JSON, XML, YAML, print, smoke signal, carrier pigeon, and other arbitrary-format resumes and CVs, from a single source of truth--without violating DRY.
2. **Convert** resumes between [FRESH](https://github.com/fluentdesk/FRESCA) and [JSON Resume](http://jsonresume.org) formats.
3. **Validate** resumes against either format.

HackMyResume is built with Node.js and runs on recent versions of OS X, Linux, or Windows.

## [](https://github.com/hacksalot/HackMyResume#features)Features

- OS X, Linux, and Windows.
- Store your resume data as a durable, versionable JSON or YAML document.
- Generate polished resumes in multiple formats without violating [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself).
- Output to HTML, Markdown, LaTeX, PDF, MS Word, JSON, YAML, plain text, or XML.
- Validate resumes against the FRESH or JSON Resume schema.
- Support for multiple input and output resumes.
- Use from your command line or [desktop](http://fluentcv.com).
- Free and open-source through the MIT license.
- Updated daily.

## [](https://github.com/hacksalot/HackMyResume#install)Install

Install HackMyResume with NPM:

```
[sudo] npm install hackmyresume -g
```

Note: for PDF generation you'll need to install a copy of [wkhtmltopdf](http://wkhtmltopdf.org/) for your platform.

## [](https://github.com/hacksalot/HackMyResume#installing-themes)Installing Themes

HackMyResume supports both [FRESH](https://github.com/fluentdesk/fresh-themes) and [JSON Resume](https://www.npmjs.com/search?q=jsonresume-theme)-style résumé themes.

- FRESH themes currently come preinstalled with HackMyResume.
- JSON Resume themes can be installed from NPM, GitHub, or manually.

To install a JSON Resume theme, just `cd` to the folder where you want to store your themes and run one of:

```
# Install with NPM
npm install jsonresume-theme-[theme-name]

# Install with GitHub
git clone https://github.com/[user-or-org]/[repo-name]
```

Then when you're ready to generate your resume, just reference the location of the theme folder as you installed it:

```
hackmyresume BUILD resume.json TO out/resume.all -t node_modules/jsonresume-theme-classy
```

Note: You can use install themes anywhere on your file system. You don't need a package.json or other NPM/Node infrastructure.

## [](https://github.com/hacksalot/HackMyResume#getting-started)Getting Started

To use HackMyResume you'll need to create a valid resume in either [FRESH](https://github.com/fluentdesk/FRESCA) or [JSON Resume](http://jsonresume.org) format. Then you can start using the command line tool. There are four basic commands you should be aware of:

- **build** generates resumes in HTML, Word, Markdown, PDF, and other formats. Use it when you need to submit, upload, print, or email resumes in specific formats.

  ```
  # hackmyresume BUILD <INPUTS> TO <OUTPUTS> [-t THEME]
  hackmyresume BUILD resume.json TO out/resume.all
  hackmyresume BUILD r1.json r2.json TO out/rez.html out/rez.md foo/rez.all
  ```

- **new** creates a new resume in FRESH or JSON Resume format.

  ```
  # hackmyresume NEW <OUTPUTS> [-f <FORMAT>]
  hackmyresume NEW resume.json
  hackmyresume NEW resume.json -f fresh
  hackmyresume NEW r1.json r2.json -f jrs
  ```

- **convert** converts your source resume between FRESH and JSON Resume formats. Use it to convert between the two formats to take advantage of tools and services.

  ```
  # hackmyresume CONVERT <INPUTS> TO <OUTPUTS>
  hackmyresume CONVERT resume.json TO resume-jrs.json
  hackmyresume CONVERT 1.json 2.json 3.json TO out/1.json out/2.json out/3.json
  ```

- **validate** validates the specified resume against either the FRESH or JSON Resume schema. Use it to make sure your resume data is sufficient and complete.

  ```
  # hackmyresume VALIDATE <INPUTS>
  hackmyresume VALIDATE resume.json
  hackmyresume VALIDATE r1.json r2.json r3.json
  ```

## [](https://github.com/hacksalot/HackMyResume#supported-output-formats)Supported Output Formats

HackMyResume supports these output formats:

Output Format       | Ext        | Notes
------------------- | ---------- | -------------------------------------------------------------------------------------------------------------------------
HTML                | .html      | A standard HTML 5 + CSS resume format that can be viewed in a browser, deployed to a website, etc.
Markdown            | .md        | A structured Markdown document that can be used as-is or used to generate HTML.
LaTeX               | .tex       | A structured LaTeX document (or collection of documents) that can be processed with pdflatex, xelatex, and similar tools.
MS Word             | .doc       | A Microsoft Word office document (XML-driven; WordProcessingML).
Adobe Acrobat (PDF) | .pdf       | A binary PDF document driven by an HTML theme (through wkhtmltopdf).
plain text          | .txt       | A formatted plain text document appropriate for emails or copy-paste.
JSON                | .json      | A JSON representation of the resume.
YAML                | .yml       | A YAML representation of the resume.
RTF                 | .rtf       | Forthcoming.
Textile             | .textile   | Forthcoming.
image               | .png, .bmp | Forthcoming.

## [](https://github.com/hacksalot/HackMyResume#use)Use

Assuming you've got a JSON-formatted resume handy, generating resumes in different formats and combinations easy. Just run:

```
hackmyresume BUILD <INPUTS> <OUTPUTS> [-t theme].
```

Where `<INPUTS>` is one or more .json resume files, separated by spaces; `<OUTPUTS>` is one or more destination resumes, and `<THEME>` is the desired theme (default to Modern). For example:

```
# Generate all resume formats (HTML, PDF, DOC, TXT, YML, etc.)
hackmyresume BUILD resume.json TO out/resume.all -t modern

# Generate a specific resume format
hackmyresume BUILD resume.json TO out/resume.html
hackmyresume BUILD resume.json TO out/resume.pdf
hackmyresume BUILD resume.json TO out/resume.md
hackmyresume BUILD resume.json TO out/resume.doc
hackmyresume BUILD resume.json TO out/resume.json
hackmyresume BUILD resume.json TO out/resume.txt
hackmyresume BUILD resume.json TO out/resume.yml

# Specify 2 inputs and 3 outputs
hackmyresume BUILD in1.json in2.json TO out.html out.doc out.pdf
```

You should see something to the effect of:

```
*** HackMyResume v0.9.0 ***
Reading JSON resume: foo/resume.json
Applying MODERN Theme (7 formats)
Generating HTML resume: out/resume.html
Generating TXT resume: out/resume.txt
Generating DOC resume: out/resume.doc
Generating PDF resume: out/resume.pdf
Generating JSON resume: out/resume.json
Generating MARKDOWN resume: out/resume.md
Generating YAML resume: out/resume.yml
```

## [](https://github.com/hacksalot/HackMyResume#advanced)Advanced

### [](https://github.com/hacksalot/HackMyResume#applying-a-theme)Applying a theme

You can specify a predefined or custom theme via the optional `-t` parameter. For a predefined theme, include the theme name. For a custom theme, include the path to the custom theme's folder.

```
hackmyresume BUILD resume.json TO out/rez.all -t modern
hackmyresume BUILD resume.json TO OUT.rez.all -t ~/foo/bar/my-custom-theme/
```

As of v1.0.0, available predefined themes are `positive`, `modern`, `compact`, `minimist`, and `hello-world`.

### [](https://github.com/hacksalot/HackMyResume#merging-resumes)Merging resumes

You can **merge multiple resumes together** by specifying them in order from most generic to most specific:

```
# Merge specific.json onto base.json and generate all formats
hackmyresume BUILD base.json specific.json TO resume.all
```

This can be useful for overriding a base (generic) resume with information from a specific (targeted) resume. For example, you might override your generic catch-all "software developer" resume with specific details from your targeted "game developer" resume, or combine two partial resumes into a "complete" resume. Merging follows conventional [extend()](https://api.jquery.com/jquery.extend/)-style behavior and there's no arbitrary limit to how many resumes you can merge:

```
hackmyresume BUILD in1.json in2.json in3.json in4.json TO out.html out.doc
Reading JSON resume: in1.json
Reading JSON resume: in2.json
Reading JSON resume: in3.json
Reading JSON resume: in4.json
Merging in4.json onto in3.json onto in2.json onto in1.json
Generating HTML resume: out.html
Generating WORD resume: out.doc
```

### [](https://github.com/hacksalot/HackMyResume#multiple-targets)Multiple targets

You can specify **multiple output targets** and HackMyResume will build them:

```
# Generate out1.doc, out1.pdf, and foo.txt from me.json.
hackmyresume BUILD me.json TO out1.doc out1.pdf foo.txt
```

### [](https://github.com/hacksalot/HackMyResume#using-all)Using .all

The special `.all` extension tells HackMyResume to generate all supported output formats for the given resume. For example, this...

```
# Generate all resume formats (HTML, PDF, DOC, TXT, etc.)
hackmyresume BUILD me.json TO out/resume.all
```

..tells HackMyResume to read `me.json` and generate `out/resume.md`, `out/resume.doc`, `out/resume.html`, `out/resume.txt`, `out/resume.pdf`, and `out/resume.json`.

### [](https://github.com/hacksalot/HackMyResume#validating)Validating

HackMyResume can also validate your resumes against either the [FRESH / FRESCA](https://github.com/fluentdesk/FRESCA) or [JSON Resume](http://jsonresume.org) formats. To validate one or more existing resumes, use the `validate` command:

```
# Validate myresume.json against either the FRESH or JSON Resume schema.
hackmyresume VALIDATE resumeA.json resumeB.json
```

HackMyResume will validate each specified resume in turn:

```
*** HackMyResume v0.9.0 ***
Validating JSON resume: resumeA.json (INVALID)
Validating JSON resume: resumeB.json (VALID)
```

### [](https://github.com/hacksalot/HackMyResume#converting)Converting

HackMyResume can convert between the [FRESH](https://github.com/fluentdesk/FRESCA) and [JSON Resume](http://jsonresume.org) formats. Just run:

```
hackmyresume CONVERT <INPUTS> <OUTPUTS>
```

where is one or more resumes in FRESH or JSON Resume format, and is a corresponding list of output file names. HackMyResume will autodetect the format (FRESH or JRS) of each input resume and convert it to the other format (JRS or FRESH).

### [](https://github.com/hacksalot/HackMyResume#prettifying)Prettifying

HackMyResume applies [js-beautify](https://github.com/beautify-web/js-beautify)-style HTML prettification by default to HTML-formatted resumes. To disable prettification, the `--nopretty` or `-n` flag can be used:

```
hackmyresume BUILD resume.json out.all --nopretty
```

### [](https://github.com/hacksalot/HackMyResume#silent-mode)Silent Mode

Use `-s` or `--silent` to run in silent mode:

```
hackmyresume BUILD resume.json -o someFile.all -s
hackmyresume BUILD resume.json -o someFile.all --silent
```

## [](https://github.com/hacksalot/HackMyResume#contributing)Contributing

HackMyResume is a community-driven free and open source project under the MIT License. Contributions are encouraged and we respond to all PRs and issues, usually within 24 hours. See [CONTRIBUTING.md](https://github.com/hacksalot/HackMyResume/blob/master/CONTRIBUTING.md) for details.

## [](https://github.com/hacksalot/HackMyResume#license)License

MIT. Go crazy. See [LICENSE.md](https://github.com/hacksalot/HackMyResume/blob/master/LICENSE.md) for details.
