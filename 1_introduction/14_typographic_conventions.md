<!--- @file
  1.4 Typographic conventions

  Copyright (c) 2012-2018, Intel Corporation. All rights reserved.<BR>

  Redistribution and use in source (original document form) and 'compiled'
  forms (converted to PDF, epub, HTML and other formats) with or without
  modification, are permitted provided that the following conditions are met:

  1) Redistributions of source code (original document form) must retain the
     above copyright notice, this list of conditions and the following
     disclaimer as the first lines of this file unmodified.

  2) Redistributions in compiled form (transformed to other DTDs, converted to
     PDF, epub, HTML and other formats) must reproduce the above copyright
     notice, this list of conditions and the following disclaimer in the
     documentation and/or other materials provided with the distribution.

  THIS DOCUMENTATION IS PROVIDED BY TIANOCORE PROJECT "AS IS" AND ANY EXPRESS OR
  IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF
  MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO
  EVENT SHALL TIANOCORE PROJECT  BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
  SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
  PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS;
  OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY,
  WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR
  OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS DOCUMENTATION, EVEN IF
  ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

-->

## 1.4 Typographic conventions

This document uses the typographic and illustrative conventions described below:

| Typographic Convention | Typographic convention description                                                                                                                                                                                                                                             |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Plain text             | The normal text typeface is used for the vast majority of the descriptive text in a specification.                                                                                                                                                                             |
| [Plain text (blue)]()  | Any _plain text_ that is underlined and in blue indicates an active link to the crossreference. Click on the word to follow the hyperlink.                                                                                                                                     |
| **Bold**               | In text, a **Bold** typeface identifies a processor register name. In other instances, a **Bold** typeface can be used as a running head within a paragraph.                                                                                                                   |
| _Italic_               | In text, an _Italic_ typeface can be used as emphasis to introduce a new term or to indicate a manual or specification name.                                                                                                                                                   |
| `BOLD Monospace`       | Computer code, example code segments, and all prototype code segments use a `BOLD Monospace` typeface with a dark red color. These code listings normally appear in one or more separate paragraphs, though words or segments can also be embedded in a normal text paragraph. |
| [`Bold Monospace`]()   | Words in a [`Bold Monospace`]() typeface that is underlined and in blue indicate an active hyper link to the code definition for that function or type definition. Click on the word to follow the hyper link.                                                                 |
| `$(VAR)`               | This symbol VAR defined by the utility or input files.                                                                                                                                                                                                                         |
| **_Italic Bold_**      | In code or in text, words in **_Italic Bold_** indicate placeholder names for variable information that must be supplied (i.e., arguments).                                                                                                                                    |

**********
**Note:** Due to management and file size considerations, only the first
occurrence of the reference on each page is an active link. Subsequent
references on the same page will not be actively linked to the definition and
will use the standard, non-underlined **BOLD Monospace** typeface. Find the
first instance of the name (in the underlined **BOLD Monospace** typeface) on
the page and click on the word to jump to the function or type definition.
**********

The following typographic conventions are used in this document to illustrate
the Extended Backus-Naur Form.

| [item]          | Square brackets denote the enclosed item is optional.                                                                                                                                                  |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `{item}`        | Curly braces denote a choice or selection item, only one of which may occur on a given line.                                                                                                           |
| `<item>`        | Angle brackets denote a name for an item.                                                                                                                                                              |
| `(range-range)` | Parenthesis with characters and dash characters denote ranges of values, for example, (a-zA-Z0-9) indicates a single alphanumeric character, while (0-9) indicates a single digit.                     |
| "item"          | Characters within quotation marks are the exact content of an item, as they must appear in the output text file.                                                                                       |
| `?`             | The question mark denotes zero or one occurrences of an item.                                                                                                                                          |
| `*`             | The star character denotes zero or more occurrences of an item.                                                                                                                                        |
| `+`             | The plus character denotes one or more occurrences of an item.                                                                                                                                         |
| `item{n}`       | A superscript number, n, is the number occurrences of the item that must be used. Example: (0-9)8 indicates that there must be exactly eight digits, so 01234567 is valid, while 1234567 is not valid. |
| `item{n,}`      | A superscript number, n, within curly braces followed by a comma "," indicates the minimum number of occurrences of the item, with no maximum number of occurrences.                                   |
| `item{,n}`      | A superscript number, n, within curly brackets, preceded by a comma ","indicates a maximum number of occurrences of the item.                                                                          |
| `item{n,m}`     | A super script number, n, followed by a comma "," and a number, m, indicates that the number of occurrences can be from n to m occurrences of the item, inclusive.                                     |
