(prech5)=
IPv6 Sandbox
==================

Location: Talks lobby

we found here Jewel Logging offering to help us with some tricks in using Ducky Scripts if we help him in recovering his password
that he left as sticky note exposed on one service in the network (what ????? =:-0 ).
<br/>
Ok, we will help you Jewel. Challenge accepted.  
<br/>It all starts from here:  
[(https://gist.github.com/chriselgee/c1c69756e527f649d0a95b6f20337c2f
)](https://gist.github.com/chriselgee/c1c69756e527f649d0a95b6f20337c2f)
<br/>actually, you do not need anything else to solve this challenge ;)
<br/>
<br/>
let's take note of our IPV6 so we can esclude it from the network scan we will do in few moments
![ip1](images/ipv6-1.png)
<br/>
We are fe80::42:c0ff:fea8:a003%eth0.
<br/>

Now let's find link local adresses available in the network with the ping commands

| address | description | 
| --- | --- | --- |
|ff02::1|All nodes on the local network segment |
|ff02::2|All routers on the local network segment |
![ip2](images/ipv6-2.png)
<br/>
It seems there are 2 others hosts (and that we are having some network issues also,
the DUP in the ping response is usually caused by faulty networks but in our case all worked fine)
<br/><br/>
launching nmap against the second host
![ip3](images/ipv6-3.png)
<br/><br/>
revealed there was 2 services running on that machine an the call to snoop in port 9000 was absolutely the rigth one:  
![ip4](images/ipv6-4.png)

the password is: **PieceOnEarth** <br>
Done!
