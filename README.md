# Ophir.php <span style="float: right;">[![Build Status](https://travis-ci.org/thephpjo/ophir.php.svg?branch=dev)](https://travis-ci.org/thephpjo/ophir.php)</span>
## PHP script that converts ODT to HTML
ophir.php is a lightweight script that parses an <b>open document</b> file and outputs a 
<b><i>simple</i> HTML</b> string, with very few tags (contrarily to most other tools that do the same thing).

## Features
Currently, the script can convert the following:
 - Text Formatting (__bold__, _italic_, _(<u>underlined</u>)_
 - images (using data URIs or extracting them into a seperate folder)
 - links
 - headings (h1, h2, ...)
 - lists (ul and li)
 - tables (table tr and td)
 - _(annotations)_
 - _(footnotes)_

Ophir.php can also **ignore** or **remove** some tags on demand.
This can be useful if you want to extract *only unformatted text* from a document,
or if you don't want tables, footnotes or annotations in the resulting HTML,
or if the application that generated the ODT file produced unnecessary formatting informations ...

## Limitations
Everything that is not mentioned in the feature section is not supported.

## How to use this script
This script requires libzip and XMLReader, that are usually installed by default with php5.
If you meet these requirements, just put ophir.php on your server, and use it like that:

```php
require("vendor/autoload.php");

$ophir = new \lovasoa\ophir\Ophir();

$ophir->setConfiguration(Ophir::FOOTNOTE,		Ophir::NONE); //Do not import footnotes
$ophir->setConfiguration(Ophir::ANNOTATION,		Ophir::NONE); //Do not import annotations

$ophir->setConfiguration(Ophir::LISTS,			Ophir::SIMPLE); //Import lists, but prints them as simple text (no ul or li tags will be generated)
$ophir->setConfiguration(Ophir::LINK,			Ophir::SIMPLE); //Import links, but prints them as simple text (only extract text from the links)

$ophir->setConfiguration(Ophir::IMAGE,			Ophir::ALL);	// Display all the images

/*
Available features are:
Ophir::HEADINGS
Ophir::LISTS
Ophir::TABLE
Ophir::FOOTNOTE
Ophir::LINK
Ophir::IMAGE
Ophir::NOTE
Ophir::ANNOTATION

Available modes are:
Ophir::ALL 		// print feature completely
Ophir::SIMPLE 	// only print text-content
Ophir::NONE 	// ignore: Do not echo content

$ophir->setConfiguration({FEATURE},{MODE});
*/

echo $ophir->odt2html("/path/to/file.odt");
```


## License
Ophir.php is under LGPLv3.
You can use it in both your free and proprietary software projects, but if you change some of the code, you have to share your improvements.
Full license informations available at http://www.gnu.org/licenses/lgpl.html. 

## Other scripts
This script was coded in one afternoon to answer to my personal needs. More professional tools, with more features, that produce uglier HTML files are available: 

 - https://github.com/lovasoa/ophir_odt_import A drupal 7 module I created that uses ophir.php to import ODT files to Drupal.

 - http://odt2xhtml.eu.org/ (in PHP)

 - http://www.artofsolving.com/opensource/jodconverter (in java)

 -  http://www.openoffice.org/udk/python/python-bridge.html (in Python)


# UNDER CONSTRUCTION
Ophir.php is currently being developed actively.

Current limitations:
- annotations and footnotes are treated as paragraphs
    - we have to think about how to display these. Maybe add a special class?
- underlined text is not working
    - The ODT definition does not map to the CSS Definition
    ```XML
    <!-- ODT XML -->
    <style:style style:name="T6" style:family="text">
        <style:text-properties  style:text-underline-style="solid"
                                style:text-underline-width="auto"
                                style:text-underline-color="font-color"/>
    </style:style>
    ```
    ```CSS
    // CSS
    .ophir-T6 {
        text-decoration: underline;
    }
    ```
- floated text is not working
    - OphirHelper::getStyleStyle has to also interpret the style:paragraph-properties element inside of it
    ```XML
    <!-- ODT XML -->
            <style:style style:name="P11" style:family="paragraph" style:parent-style-name="Standard">
                <style:paragraph-properties fo:text-align="center" style:justify-single-word="false"/>
                <!-- (...) -->
            </style:style>
    ```
- Tests are failing at the moment. They have to be rewritten

If you have solutions to these problems, please open a issue or create a pull-request