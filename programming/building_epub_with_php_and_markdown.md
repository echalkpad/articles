# Building ePub with PHP and Markdown

[Original URL](http://www.sitepoint.com/building-epub-with-php-and-markdown/)

> The ePub format is a publishing standard built on top of XHTML, CSS, XML and more. And since PHP is well suited for working with HTML and friends, why not use it to build ebooks? In this article...

The ePub format is a publishing standard built on top of XHTML, CSS, XML and more. And since PHP is well suited for working with HTML and friends, why not use it to build ebooks? In this article we'll see what goes into building a tool for creating ePub packages starting from a set of content files. Maybe it's your next best selling cyber-sci-fi novel or documentation for your latest code project... because we all write good documentation for our projects, don't we?

In order to make things easy on the writers, our tool will be able to parse the Markdown syntax and the resulting markup will be placed into HTML templates using the [RainTPL library](http://www.raintpl.com/ "RainTPL - easy php templating engine").

The sample tool I wrote for this article is named md2epub and has become a real [open source project on Github](https://github.com/vtardia/md2epub "vtardia/md2epub - GitHub"). It's far from perfect at the moment, but feel free to fork and improve it!

## Quick Start ePub Theory

The specifications for the ePub format are detailed by the [IDPF (International Digital Publishing Forum)](http://idpf.org/epub "EPUB | International Digital Publishing Forum"), and we'll refer to version 2.0.1 of the specs which is the most widely used today. Other useful resources are the [Wikipedia ePub Page](http://en.wikipedia.org/wiki/EPUB "EPUB - Wikipedia, the free encyclopedia") and the [Epub Format Construction Guide](http://www.hxa.name/articles/content/epub-guide_hxa7241_2007.html "Epub Format Construction Guide - HXA7241 - 2007") by [Harrison Ainsworth](http://www.hxa.name/ "HXA7241").

Basically, an ePub book is a zipped archive with a well defined structure. The contents includes XHTML documents, CSS stylesheets, images (GIFs, JPEGs, PNGs, and SVGs), and Open Type fonts. There are other specific files used to describe the content of the book; these are the package and container files:

```
myBook/
 META-INF/
 container.xml
 mimetype
 content.opf
 toc.ncx
 Stylesheet.css
 BookCover.jpg
 HomePage.xhtml
 Chapter1.xhtml
 ...
 ChapterN.xhtml
 Index.xhtml
```

The first four files are specific for the ePub package, the others are our content. The `mimetype` file must contain _application/epub+zip_ in ASCII without an end-of-line character. It must be the first file included in the archive and must not be compressed; we cannot do this with PHP, but I'll show you a work-around for this later.

The file `META-INF/container.xml` is fixed in name and location. The scope of this file is to tell where the OPF file (usually `content.opf`) is located relative to the ebook's root directory.

`content.opf` is an XML file that contains the book's meta data, the references to all the content resources, and the the order in which the contents must be loaded by the reader application.

The `toc.ncx` file is an optional but recommended XML navigation map of the ebook.

There are also other optional files that are not taken in consideration for now. And I won't tell you how to add DRM, sorry.

## Our First eBook

So how do we prepare our content for ePub-lishing? I've set up a test book directory like this:

```
mybook/
 01-first-chapter.md
 02-second-chapter.md
 book.json
 cover.jpg
 coverpage.md
 index.md
 style.css
 titlepage.md
 media/
 *.jpg
```

Most of the files are content files for the publication, markdown text and images. We then have a `style.css` file (the name and position should be fixed for now). Keep in mind that the ePub format understands only a subset of the CSS 2.1 specs.

At last we have a special file called `book.json`. This file contains all the data that our `md2epub` tool will need to generate the standard package files. Most of the keys in this file have a direct match with the OPF and NCX files. The JSON file is structured like this:

```
{
 "id": "com.acme.books.MyUniqueBookID",
 "title": "Sample eBook Title",
 "language": "en",

 "authors": [
 { "name": "John Smith", "role": "aut"},
 { "name": "Jane Appleseed", "role": "dsr"}
 ],

 "description": "Brief description of the book",
 "subject": "List of keywords, pertinent to content, separated by commas",
 "publisher": "ACME Publishing Inc.",
 "rights": "Copyright (c) 2013, Someone",
 "date": "2013-02-27",
 "relation": "http://www.acme.com/books/MyUniqueBookIDWebEdition/",

 "files": {
 "coverpage": "coverpage.md",
 "title-page": "titlepage.md",
 "include": [
 { "id":"ncx", "path":"toc.ncx" },
 "cover.jpg",
 "style.css",
 "*.md",
 "media/*"
 ],
 "index": "index.md",
 "exclude": []
 },

 "spine": {
 "toc": "ncx",
 "items": [
 "coverpage",
 "title-page",
 "copyright",
 "foreword",
 "|^c\d{1,2}-.*$|",
 "index"
 ]
 }
}
```

The first three groups of keys from `id` to `relation` maps to the `<metadata>` section of the OPF file. They contain information about the book, the author, the publisher, and so on. The keys `id`, `title`, and `language` are required. All the others are optional and their meaning is self-explanatory, except maybe for `relation` which is used to link to another publication using an ISBN format or a site URL. The value of `id` must be unique, and I've chosen the reverse domain format _com.publisher.series.BookID_ although you can use whatever format you like.

The `files` key of the JSON maps to the <manifest> section of the OPF. This section contains references to every file used in the book, including fonts, stylesheets, images, and special files. Every item in this section needs a unique id, path to the resource (href), and its mimetype. `md2epub` will take care of the media type and will generate a suitable ID if it's not provided.

I also wanted it to be able to specify bulk file inclusions using wildcards such as _\_.md _and_ media/**, although the exclude list is not parsed at the moment either.

The spine JSON key maps to `<spine>` in the OPF, it describes the order in which the various chapters should appear in the book.

Starting with these data our `md2epub` will:

- create a well organized temporary book directory
- copy all the referenced files
- convert and copy all the markdown file to XHTML
- generate the ePub required files
- create the zip/epub archive and cleanup

The final file can (and should) be validated by the [official EPUB Validator](http://validator.idpf.org/ "EPUB Valiator (beta)") which you can also download and run locally as a Java console application.

## Md2Epub – The Making of

In order to stay on topic in this article, I'll discuss only the most epub-specific code and leave the "utility code" for you to explore.

The complete program consists of a console PHP script `md2epub.php` that collects the user's input, creates the working directory, and passes all of the data to the `EBook` class. The `md2epub` file is a shell wrapper.

The `EBook` class performs all of the operations and checks. It uses the RainTPL library for template parsing and the [PHP Markdown Extra library](http://michelf.ca/projects/php-markdown/extra/ "Michel Fortin - PHP Markdown Extra") as a content filter. An interesting thing about this last component is that the `Markdown()` function is provided to the `EBook` class as an external filter to be applied to `*.md` files. In this way we can inject any other text filters such as Textile or a wiki syntax parser.

The share directory contains common files, in particular the XHTML and XML template files; you can edit the markup of these files to customize your specific book type. The `mimetype.zip` file is part of the work-around I've mentioned earlier – since the PHP Zip library does not allow us to specify a compression level when creating an archive, I've created this archive stub using the command line zip program:

```
zip -0 -D -X mimetype.zip mimetype
```

PHP will copy this basic archive and add the other files in sequence.

## Exploring the EBook Object

The constructor of the `EBook` class takes two arguments: the source directory of the book and an optional array of parameters. The parameters specify the name of the JSON file to parse (which defaults to `book.json`) and the directory that will contain the content files of the compiled book. I chose `OEBPS`, which stands for Open eBook Publication Structure, as it's a convention used by many of the ebooks I've explored.

The role of the `parseFiles()` method is to normalize the files section loaded from the JSON. The wildcard paths are expanded using `glob()` and the IDs and media types are generated. At the end, it gives us an associative array where each file is represented in this way:

```
'cover' => Array (
 'path' => 'cover.jpg',
 'type' => 'image/jpeg'
)
```

Each file must not be listed more than once. The path is relative to the content directory, the directory that will contain the OPF file. Markdown files are of type text/plain in this step and they will be converted later.

The `parseSpine()` method does a similar job. It sets a NCX/TOC file and compiles a list of declared IDs. The IDs can be regular expressions. For example, I've specified the pattern `^cd{1,2}-.*$` that matches the IDs for the chapters (_c01-first-chapter_, _c02-second-chapter_, etc). The beginning "c" is important since XML `id` attributes cannot start with a number.

The powerful `makeEpub()` method then starts the transformation process for our ebook. The data needed is:

- the target .epub file path
- the temporary directory in which compile and copy all the resources
- the source directory for template files
- the optional content filters, in this case Markdown linked to the md extension

It first checks the input parameters to be sure it has the right filesystem permissions, and then calls four helper methods in sequence, corresponding to the required steps:

- create the META-INF directory and data
- export all the content files, compile templates and apply filters
- create the OPF manifest file
- create the NCX navigation file (if needed)
- create the compressed epub archive

The `createMetaInf()` method is the simplest and helps us to understand the template logic behind the others:

```
<?php
protected function createMetaInf($workDir)
{
 // create destination directory
 if (!mkdir("$workDir/META-INF")) {
 throw new Exception('Unable to create content META-INF directory');
 }

 // compile file
 $tpl = $this->initTemplateEngine(
 array(
 'tpl_dir' => "{$this->params['templates_dir']}/",
 'tpl_ext' => 'xml',
 'cache_dir' => sys_get_temp_dir() . '/'
 )
 );
 $tpl->assign('ContentDirectory', $this->params['content_dir']);
 $container = $tpl->draw('book/META-INF/container', true);

 // write compiled file to destination
 if (file_put_contents("$workDir/META-INF/container.xml", $container) === false) {
 throw new Exception("Unable to create content META-INF/container.xml");
 }
}
```

The `container.xml` template has only one variable to set which is the path of the `content.opf` file relative to the base directory of the ebook. The syntax is `{$VariableName}`.

```
<?xml version="1.0"?>
<container version="1.0" xmlns="urn:oasis:names:tc:opendocument:xmlns:container">
 <rootfiles>
 <rootfile full-path="{$ContentDirectory}/content.opf"
 media-type="application/oebps-package+xml"/>
 </rootfiles>
</container>
```

First the RainTPL library is initialized with the templates directory, the extension of the template files, and a temporary cache directory. Then the template variable is assigned (`$tpl->assign()`) and the `draw()` method parses the provided template file. Its second parameter tells `draw()` whether to return the result. The compiled file is then saved to the destination path.

The `createOpf()` method has the same logic and some more variables to assign. I chose the pattern `Book<Varname>` so the optional meta data is assigned in the loop:

```
<?php
$optParams = array(
 'authors',
 'date',
 'description',
 'publisher',
 'relation',
 'rights',
 'subject'
);

foreach ($optParams as $p) {
 if (isset($this->$p)) {
 $tpl->assign('Book' . ucfirst($p), $this->$p);
 }
}
```

And they are inserted in the template with a conditional syntax:

```
{if="$BookDescription"}<dc:description>{$BookDescription}</dc:description>{/if}
```

The files and spine variables are formatted in the method and are inserted in the template with a loop:

```
<manifest>
 {loop="$BookFiles"}<item id="{$key}" href="{$value.path}" media-type="{$value.type}"/>
 {/loop}
</manifest>
```

The `processBookFiles()` is called before the `createOpf()` and runs a big loop with all the referenced resources (`$this->files`). For each resource it can choose between two actions:

- process and filter the file
- copy the file path "as is" in the destination directory

The filter applied to a file must be a callable PHP function or class method that takes one parameter: the content. The file content is loaded from the original source using `file_get_contents()` and the filter is applied using the native `call_user_func()`:

```
<?php
$content = call_user_func($filters[$ext], $content);
```

The processed content is then injected in an XHTML template, similar to the previous methods. In this case there is also the possibility to use a custom template. If there is a file named `.xhtml` in the `templates` directory, that file will be used overriding the default `page.xhtml`. Also, if a `style.css` file is present then the `{$BookStyle}` template variable is set and the CSS is linked to the page.

The resulting file is then written to the destination directory and the manifest entry for that file is updated with the new path and media type, ready for `createOpf()`.

The `createNcx()` is a little tricky because it has to look into the items of the spine section and extract the content of the H1 and H2 tags. The `toc.ncx` file has this structure, first a standardized header:

```
<?xml version="1.0" encoding="UTF-8"?>
<ncx xmlns="http://www.daisy.org/z3986/2005/ncx/" version="2005-1">
 <head>
 <meta name="dtb:uid" content="{$BookID}"/>

 <!-- 1 = chapters only, 2 = subchapters, 3 = third level section -->
 <meta name="dtb:depth" content="2"/>

 <!-- Both required but unused, can be 0 -->
 <meta name="dtb:totalPageCount" content="0"/>
 <meta name="dtb:maxPageNumber" content="0"/>
 </head>

 <docTitle>
 <text>{$BookTitle}</text>
 </docTitle>
```

The customized `dtb:uid` meta indicates our book ID, and the `dtb:depth` meta tag says how deep our navigation is. I choose a fixed value of 2 here so in the parsing code I have to extract the first two level headers.

After the header there's the `<navMap>` that is generated using a template loop.

```
{$playOrder=1}
{loop="$BookChapters"}
 <navPoint id="{$key}" playOrder="{$playOrder++}">
 <navLabel>
 <text>{$value.title}</text>
 </navLabel>
 <content src="{$value.path}"/>
 {if="isset($value.sections)"}{loop="$value.sections"}
 <navPoint id="{$key}" playOrder="{$playOrder++}">
 <navLabel>
 <text>{$value.title}</text>
 </navLabel>
 <content src="{$value.path}"/>
 </navPoint>
 {/loop}{/if}
 </navPoint>
{/loop}
```

Each H1 becomes a `<navPoint>`, the `id` is the corresponding resource ID (ex _c01-first-chapter_, etc), the H1 content becomes the label, and the `playOrder` attribute indicates the global reading order. In our example, each nav point can have nested nav points for H2 subtitles.

In the method, a double loop generates an array structured like this:

```
$chapters['c01-first-chapter'] = array(
 'title' => 'Title for chapter 1',
 'path' => '01-first-chapter.xhtml',
 'sections' => array(
 'section-1-1' => array(
 'title' => 'Title for subsection 1.1',
 'path' => '01-first-chapter.xhtml#section-1-1',
 )
 )
)
```

In the external loop, each item is loaded as a `SimpleXML` object:

```
<?php
$doc = simplexml_load_file("$workDir/{$this->params['content_dir']}/{$this->files[$item]['path']}");
if ($doc && isset($doc->body->h1)) {
 // chapter title
 $chapters[$item] = array(
 'title' => $doc->body->h1,
 'path' => $this->files[$item]['path']
 );

 // subchapter title
 foreach ($doc->body->h2 as $section) {
 if (!empty($section['id'])) {
 $section_id = (string) $section['id'];
 $chapters[$item]['sections'][$section_id] = array(
 'title' => $section,
 'path' => $this->files[$item]['path'] . '#' . $section['id']
 );
 }
 }
}
```

If the document has a H1 header, a first level item is inserted into the `$chapters` array. Then the second level headers are queried, each header that has a non-empty `id` attribute is included in the sections for each chapter. The `$chapters` array becomes `{$BookChapters}` in the template. The template is then rendered and saved to its destination.

Now we have a destination working directory somewhere at `/tmp/path/com.publisher.BookID` that can be packed by the final step `createArchive()`:

```
<?php
protected function createArchive($workDir, $epubFile)
{
 $excludes = array('.DS_Store', 'mimetype');

 $mimeZip = "{$this->params['templates_dir']}/mimetype.zip";
 $zipFile = sys_get_temp_dir() . '/book.zip';

 if (!copy($mimeZip, $zipFile)) {
 throw new Exception("Unable to copy temporary archive file");
 }

 $zip = new ZipArchive();
 if ($zip->open($zipFile, ZipArchive::CREATE) != true) {
 throw new Exception("Unable open archive '$zipFile'");
 }

 $files = new RecursiveIteratorIterator(new RecursiveDirectoryIterator($workDir), RecursiveIteratorIterator::SELF_FIRST);
 foreach ($files as $file) {
 if (in_array(basename($file), $excludes)) {
 continue;
 }
 if (is_dir($file)) {
 $zip->addEmptyDir(str_replace("$workDir/", '', "$file/"));
 } elseif (is_file($file)) {
 $zip->addFromString(
 str_replace("$workDir/", '', $file),
 file_get_contents($file)
 );
 }
 }
 $zip->close();

 rename($zipFile, $epubFile);
}
```

The `$excludes` array contains special and system files that we don't want in the archive. A temporary path for an empty `$zipFile` is created, and then the `$zipFile` path is filled with a copy of our basic `mimetype.zip` that is loaded in a `ZipArchive` object. At this point the content of the working directory is recursively loaded into the `$files` array using the `RecursiveIteratorIterator` and `RecursiveDirectoryIterator` objects. In the following loop the structure of the working directory is replicated in the zip archive. As a finishing touch, the archive is moved to the destination path indicated by the caller.

## Summary

When all is said and done, we have our shiny new eBook. We have a good chance that it will be validated by the EpubCheck app and, most importantly, we have another useful tool to add to our PHP-super-hero belt! And this is only a starting point, you can play with the code to add filters, features, themes and styles and more if you like. Keep calm, have fun, and write those eBooks!

Image via [Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")
