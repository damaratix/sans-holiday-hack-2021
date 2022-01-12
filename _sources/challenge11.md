(ch11)=
Wireshark
====================


Location: Frost tower talks lobby 

### The Evil Bit
This challenge was introduced by Tom Liston with an interesting talk and ... we were ready to 
fall in some looping rabbit hole still remembering the mess with the big/little endian in 2020 year challenge ah ah ah...<br />
<br />
Nope, this time everything goes smoothly and it was funny enough reading about the RFC 3514 :) 

![wireshark](images/wireshark.png)
<br/><br />
By opening the pcap found [(here)](http://downloads.holidayhackchallenge.com/2021/jackfrosttower-network.zip)
and setting as a filter: 
<br/>
```
http.request.method == POST
```

we obtained all the  HTTP packets sent by the users network and it was easy enough to spot out what happened.
<br /><br />
The majority of the complaints were about the room: 1024.
<br/>Actually, 3 Trolls seemed complaining about a human woman far too angry.
Their names were: Flud Hagg Yaqh
<br />
<br />
Done!
