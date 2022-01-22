(prech9)=
YARA Analysis
==================

Location: Entry

Fitzy Shortstack asks for help with YARA rules trying to prevent malware attacks inside the network.

```{note}
YARA rules are used to classify and identify malware samples by creating descriptions of malware families based on textual or binary patterns.

You define the rules in a json file in which you specify `strings` (aka: patterns)
that will match `conditions`  that help in successfully identifying a piece of malware
in an executable.

[https://yara.readthedocs.io/en/v3.4.0/writingrules.html](https://yara.readthedocs.io/en/v3.4.0/writingrules.html)
```

![YaraRule Cranberry](images/yara.png)

what's available here?
```
snowball2@8b0922e50660:~$ ls -l 
total 24
-rwxr-xr-x 1 snowball2 snowball2 16688 Nov 24 15:51 the_critical_elf_app
drwxr-xr-x 1 root      root       4096 Dec  2 14:25 yara_rules
```

and there is the configuration file for YARA in `yara_rule/rules.yar`.
```
snowball2@8b0922e50660:~$ ls -l yara_rules/ 
total 4000
-rwxr-xr-x 1 root root 4094217 Nov 23 15:48 rules.yar
```

## Mantra
```{admonition} Remember!
“anytime you see URL as an input, test for SSRF” Dr. Petabyte in SSFR challenge 10<br/> 
"always run the strings command on a binary first!" is the 2nd mantra to remember for binary files!
```

```
snowball2@14f0cb20a8a0:~$ strings "the_critical_elf_app" 
candycane
naughty string
This is critical for the execution of this program!!
HolidayHackChallenge{NotReallyAFlag}
dastardly string
:*3$"
candy_grabber
jack_frost_function
this_is_one_merry_vari
....... (cutted)
```
the strings command shows what string are readable in an binary file. It could be a really useful tool in many cases. 


Ok, it seems we have gathered some infos so let's run the app and see what happen
```
snowball2@98fa20601b54:~$ ./the_critical_elf_app 
yara_rule_135 ./the_critical_elf_app
```

it seems rule_135 have some bad matching in file `yara_rules/rules.yar` 
```
rule yara_rule_135 {
   meta:
      description = "binaries - file Sugar_in_the_machinery"
      author = "Sparkle Redberry"
      reference = "North Pole Malware Research Lab"
      date = "1955-04-21"
      hash = "19ecaadb2159b566c39c999b0f860b4d8fc2824eb648e275f57a6dbceaf9b488"
   strings:
      $s = "candycane"
   condition:
      $s
}
```

oh .... actually our executable **has a string "candycane"** inside the executable!  
Yara is complaining for this one. 
We cannot modify YARA configuration file because we are not root but we can do something in the binary file of our app

## Vi as Hex editor
we can use vi with the switch -b (open file in binary mode) and then we could use xxd to edit some bytes directly in there. 

Open vi in binary mode 
```
snowball2@edcb119ed5b5:~$ vi -b the_critical_elf_app
```

inside the vi editor screen type
```
:%!xxd ( convert the file to more readable format .. hexdec )
```
```{attention}
Be careful to write in the left-hand side of the display, that is in the hex directly because changes in the right part, the readable one, are ignored !!! 
```

![YaraViHex](images/yara-vi-hex.png)

we postion our cursor on row 00002000 and change the far right byte: 41 ('a') in: 61('A')
in this way our string is **candycAne** and YARA doesn't complaint anymore .... (about it).

save end exit
```
ESC
:%!xxd -r 
:wq
```

we can verify if we really have changed the string, by using the `strings` command: 
```
snowball2@b12dde445fbc:~$ strings the_critical_elf_app | grep candy
candycAne
candy_grabber
(cutted)
```

Relaunch .. and bang again !
```
snowball2@edcb119ed5b5:~$ ./the_critical_elf_app 
yara_rule_1056 ./the_critical_elf_app
```

Let’s see the new condition .. 

```
rule yara_rule_1056 {
   meta:
        description = "binaries - file frosty.exe"
        author = "Sparkle Redberry"
        reference = "North Pole Malware Research Lab"
        date = "1955-04-21"
        hash = "b9b95f671e3d54318b3fd4db1ba3b813325fcef462070da163193d7acb5fcd03"
    strings:
        $s1 = {6c 6962 632e 736f 2e36}     //it’s =  libc.so.6
        $hs2 = {726f 6772 616d 2121}       //it’s =  rogram!!
    condition:
        all of them
}
```
we have both strings but it is sufficient to change only of them and for sure we do not touch the link to libc.so !!!!!
So we repeat the previous steps an we change just the string **rogram!!** to **rogrAm!!** as seen before
just edit line 00002050 changing just 1 byte from '41' to '61'
```
00002050: 6973 2070 726f 6772 416d 2021 0000 0000  is program!!....
in
00002050: 6973 2070 726f 6772 616d 2021 0000 0000  is progrAm!!....
```

Relaunch .. and bang again !
```
snowball2@edcb119ed5b5:~$ ./the_critical_elf_app 
yara_rule_1732 ./the_critical_elf_app
```

## Change file size
this time it seems more serious stuff
```
rule yara_rule_1732 {
   meta:
      description = "binaries - alwayz_winter.exe"
      author = "Santa"
      reference = "North Pole Malware Research Lab"
      date = "1955-04-22"
      hash = "c1e31a539898aab18f483d9e7b3c698ea45799e78bddc919a7dbebb1b40193a8"
   strings:
      $s1 = "This is critical for the execution of this program!!" fullword ascii
      $s2 = "__frame_dummy_init_array_entry" fullword ascii
      $s3 = ".note.gnu.property" fullword ascii
      $s4 = ".eh_frame_hdr" fullword ascii
      $s5 = "__FRAME_END__" fullword ascii
      $s6 = "__GNU_EH_FRAME_HDR" fullword ascii
      $s7 = "frame_dummy" fullword ascii
      $s8 = ".note.gnu.build-id" fullword ascii
      $s9 = "completed.8060" fullword ascii
      $s10 = "_IO_stdin_used" fullword ascii
      $s11 = ".note.ABI-tag" fullword ascii
      $s12 = "naughty string" fullword ascii
      $s13 = "dastardly string" fullword ascii
      $s14 = "__do_global_dtors_aux_fini_array_entry" fullword ascii
      $s15 = "__libc_start_main@@GLIBC_2.2.5" fullword ascii
      $s16 = "GLIBC_2.2.5" fullword ascii
      $s17 = "its_a_holly_jolly_variable" fullword ascii
      $s18 = "__cxa_finalize" fullword ascii
      $s19 = "HolidayHackChallenge{NotReallyAFlag}" fullword ascii
      $s20 = "__libc_csu_init" fullword ascii
   condition:
      uint32(1) == 0x02464c45 and filesize < 50KB and
      10 of them
}
```

There are 3 conditions in a logic AND so it is sufficient that only one of them returns false to escape the check.
Let's review the three conditions: 

1. Checks the header of the file for ELF extension (0x02464c45). ELF is an acronym for Executable and Linkable Format, is a common standard file extension used for executable, 
object code, core dumps and shared libraries 
<br/>**NO** (we can't do anything here)

2. Filesize<br/> **YES** (we can do something here)

3. String modified <br/>**NO** (avoid this crazy way)

we can use the `truncate` command to change the size of a file
```
snowball2@7e2bf07f6a1f:~$ truncate -s +50K the_critical_elf_app 
```

and finally
```
snowball2@7e2bf07f6a1f:~$ ./the_critical_elf_app 
Machine Running.. 
Toy Levels: Very Merry, Terry
Naughty/Nice Blockchain Assessment: Untampered
Candy Sweetness Gauge: Exceedingly Sugarlicious
Elf Jolliness Quotient: 4a6f6c6c7920456e6f7567682c204f76657274696d6520417070726f766564
```


Done!

 
