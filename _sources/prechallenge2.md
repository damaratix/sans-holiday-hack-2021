(prech2)=
Exif Metadata
==================
Location: Courtyard

In this Cranberry we have 25 `.docx` files. We have to find out which was edited by **Jack Frost**. <br>
We have `exiftool` installed on the machine, a special tool which can read/write files metadata. Let's see the output if we run it with a file.<br>
Usage: `exiftool <filename>`

```Shell
$ exiftool 2021-12-01.docx

ExifTool Version Number : 12.16
File Name : 2021-12-01.docx
[...]
Last Modified By : Santa Claus
Revision Number : 3
```
In one of the last lines, we see that there is a field called "Last Modified By". We could use that to see which file was edited by Jack Frost, using `grep`.

```{note}
`grep`: finds something in a file <br>
`-i`: case insensitive <br>
`-B 40`: report with 40 preceding lines
```
```Shell
$ exiftool *.docx | grep -i frost -B 40

ExifTool Version Number : 12.16
File Name : 2021-12-21.docx
[...]
Last Modified By : Jack Frost

```
We got it. The filename is **2021-12-21.docx**

