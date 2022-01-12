(prech3)=
Grepping for Gold
=======================

Location : In front of Frost tower
<br/><br/>
In this cranberry we have to help Greasy GopherCuts in finding some results from a really big network scan file obtained by using nmap
and saved in gnmap format so it's easy to search for useful info by using just the "grep" command.

How do you obtain a gnmap file? Just by using the switch -oG your-file-name.gnmap
```
$nmap -oG output.gnmap www.google.com -p 443 
```

Questions time:
- What port does 34.76.1.22 have open?

```
elf@ca9551e959fa:~$ grep "34\.76\.1\.22" bigscan.gnmap 
Host: 34.76.1.22 ()     Status: Up
Host: 34.76.1.22 ()     Ports: 62078/open/tcp//iphone-sync///      Ignored State: closed (999)
```

- What port does 34.77.207.226 have open?

```
elf@ca9551e959fa:~$ grep "34\.77\.207\.226" bigscan.gnmap 
Host: 34.77.207.226 ()     Status: Up
Host: 34.77.207.226 ()     Ports: 8080/open/tcp//http-proxy///      Ignored State: filtered (999)
```

- How many hosts appear "Up" in the scan?

```
elf@ca9551e959fa:~$ grep "Status: Up" bigscan.gnmap | wc -l 
26054
```


- How many hosts have a web port open?  (Let's just use TCP ports 80, 443, and 8080)

```
elf@ca9551e959fa:~$ grep -E "(80|443|8080)/open/tcp/" bigscan.gnmap | wc -l
14372
```

- How many hosts with status Up have no (detected) open TCP ports?
```
Example: 
Host: 34.76.3.233 ()     Status: Up

echo $((`grep "Up" bigscan.gnmap | wc -l` - `grep "Ports" bigscan.gnmap | wc -l`))
402
```

- What's the greatest number of TCP ports any one host has open?


```
elf@ca9551e959fa:~$ grep " Ports" bigscan.gnmap | awk '{printf "%2d| %s\n",length,$0}'   | sort -n | tail -n 1
371| Host: 34.77.152.226 ()     Ports: 22/open/tcp//ssh///, 25/open/tcp//smtp///, 80/open/tcp//http///, 110/open/tcp//pop3///, 135/open/tcp//msrpc///, 137/open/tcp//netbios-ns///, 139/open/tcp//netbios-ssn///, 143/open/tcp//imap///, 445/open/tcp//microsoft-ds///, 993/open/tcp//imaps///, 995/open/tcp//pop3s///, 3389/open/tcp//ms-wbt-server///      Ignored State: closed (988)
```
12<br /> 
in details with 'awk' we read the length of a line, we 'sort' the output from lower to higher and with 'tail' we print the last line (the longest one).
<br />
<br />
Done.

