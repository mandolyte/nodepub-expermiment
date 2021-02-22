# Journal

## 2021-02-22 en_tw experiment

Goal: create an ebook of the unfoldingWord Translation Word Markdown files.

Constraints: Use minimal Pandoc setup and judge the quality / results.

### Process

First, downloaded zip of repo at:
https://git.door43.org/unfoldingword/en_tw

Next, extracted the zip and moved the content folder to this repo. On examination, there are three content folders (kt, names, and other). To keep this first attempt simple, will just use other.

Script in `en_tw/scripts/run_ebook.sh`. The epub created is fairly plain with no table of contents.

To Do:
- add table of contents
- fix the links between articles. Does Jesse have a scripture reference fixer scripts I can use to make the URI between articles actually work?
- use Kindle Preview app to create a Kindle version and send it to my Kindle
- 

## 2021-02-22 Obsidian+Pandoc continued...

Some history on this idea already:
https://forum.obsidian.md/t/pandoc-plugin/3433

Enhancement request:
https://forum.obsidian.md/t/run-the-preview-through-pandoc-and-give-users-the-ability-to-control-options/3499

From the reddit group:
https://www.reddit.com/r/ObsidianMD/comments/hxea89/export_notes_as_pdfs/

A post for the reddit group:
```
I have started looking into how to create an ebook using Obsidian. After searching for a while for prior work, I think I'll study how to effectively use Pandoc. If that works, then perhaps a plugin wrapping Pandoc would work.

Anyone else looked at using Obsidian for an ebook?
```
Here: https://www.reddit.com/r/ObsidianMD/comments/lpp2ew/ebook_creation/

One response was a pointer to a "micro book", which is actually a published Obsidian vault. Very interesting, but not a book that could be read on epub or Kindle devices.

### Pandoc ebook demo
https://pandoc.org/demos.html

The ebook demo uses this command to turn the Pandoc manual into an ebook:
`pandoc MANUAL.txt -o MANUAL.epub`

I saved the raw text for the manual into `Manual.md` at [[Manual]];

I used the command: `pandoc Manual.md -o Pandoc.epub`; was able to easily import and view in Calibre.

Here is the main page about ebooks in Pandoc: https://pandoc.org/epub.html

NOTE: I've seen "Kindlegen" referenced a number of times, but per Amazon, it has been replaced with:
https://www.amazon.com/gp/feature.html?ie=UTF8&docId=1000765261
And epub is the favored format to take as input to it.



## 2021-02-19 Obsdidian+Pandoc

Some info...

Wondered if asciidoc had ever been considered. I knew of its existence, but until I tried to put version 2 of the Pro Git book into Obsidian and discovered they have moved from Markdown to Asciidoc, I had never really studied it. It has some aspects that might make it easier to learn. So here are some links and comments for posterity...

Comparison of Markdown and Asciidoc: https://docs.asciidoctor.org/asciidoc/latest/asciidoc-vs-markdown/

Built-in support for EPUB3: https://asciidoctor.org/docs/asciidoctor-epub3/

Requested feature for Obsidian: https://forum.obsidian.md/t/asciidoc-support/716

Md to Adoc conversion via Pandoc: https://matthewsetter.com/convert-markdown-to-asciidoc-withpandoc/

Adoc to Md via Pandoc from the Obsidian article above:
```
asciidoctor -b docbook -a leveloffset=+1 -o - foo.adoc | \
pandoc --atx-headers --wrap=preserve -t gfm -f docbook - > foo.md
```

Asciidoc tooling: https://docs.asciidoctor.org/asciidoctor/latest/tooling/
This includes vs code support, web browser support via an extension (chrome, firefox are both supported)

Various supported converters: https://docs.asciidoctor.org/asciidoctor/latest/converters/

Alternative workflow (adoc to docbook to epub): https://theantway.com/2017/06/how-to-convert-asciidoc-book-to-epubmobi-formats/



## 2021-02-19 Preliminary Thoughts

Given:
1. That the structure of an ebook is fairly rigid
2. That the content must be HTML
3. That I found no native way in Obsidian to translate Markdown to HTML

Then making a plugin for Obsidian to make an ebook seems like a long difficult road. For a programmer, some scripting (to convert md-to-html) and running a tweaked `example.js` would work.

It would be less effort if it could be just scripting... some non-programmers might be able to work with it. So to this end, let's try:
- use Obsidian to author all the content
- use [pandoc](https://pandoc.org/epub.html) to create the ebook itself


## 2021-02-19 Setup

Began work on this experiment today. 

1. Cloned the nodepub package (elsewhere)
2. Ran `npm i` to install all dependencies
3. Ran `npm run example`:

```sh
mando@DESKTOP-0V8P6MM MINGW64 ~/Projects/mandolyte/nodepub (master)
$ npm run example

> nodepub@2.2.0 example C:\Users\mando\Projects\mandolyte\nodepub
> node example/example.js

No errors. See the 'example' subfolder.
```
4. The `example.js` created a subfolder `example-EPUB-files`.
5. This subfolder has a file named `mimetype` with a single line `application/epub+zip`.
6. It also has the epub folders `META-INF` and `OEBPF`.

The meta folder has a single XML file named `container.xml`:
```xml
<?xml version='1.0' encoding='UTF-8' ?>
<container version='1.0' xmlns='urn:oasis:names:tc:opendocument:xmlns:container'>
  <rootfiles>
    <rootfile full-path='OEBPF/ebook.opf' media-type='application/oebps-package+xml'/>
  </rootfiles>
</container>
```
The `OEBPF` folder has all the content.

- has files:
	- `cover.xhtml` which is XHTML and brings in the image used for the cover
	- `ebook.opf` is an XML file which includes:
		- some dublin core metadata
		- a manifest
		- a "spine" element which arranges the order of the content (referenced as IDs from the manifest element above)
		- a guide element which calls out the table of contents
	- `navigation.ncx`This also arranges the order of the content in a more detailed way

- has folders:
	- `content` which has all the sections/chapters, each XHTML
	- `css` which has one CSS file named `ebook.css`
	- `images` which has the images (PNG files) 


**Question**: Which of the files are boilerplate (or semi-boilerplate)? And which are created from scratch? I should be able to inspect the `example.js` source code to determine the answers.

Looks like this package does a LOT of boilerplate in the code.

For example, in the script, here is the content for the copyright page:
```xml 
<h1>[[TITLE]]</h1>
<h2>[[AUTHOR]]</h2>
<h3>&copy; [[COPYRIGHT]]</h3>
<p>
	All rights reserved.
</p>
<p>
	No part of this book may be reproduced in any form or by any electronic or
	mechanical means, including information storage and retrieval systems, without
	written permission from the author, except where covered by fair usage or
	equivalent.
</p>
<p>
	This book is a work of fiction.
	Any resemblance to actual persons	(living or dead) is entirely coincidental.
</p>
```
But the XHTML file created `s2.xhtml` has a lot more in it.