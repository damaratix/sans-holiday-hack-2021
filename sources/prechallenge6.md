(prech6)=
Holiday Hero
=======================

Location: (Netwar)

Chimeny Scissor challenged us in playing with the Holiday Hero terminal. <br/>
He said that playing with that we could full up Santa’s sleigh!<br />
True or not, we accepted the challenge of course and it turned out it was a game to play with 2 players and getting you 
crazy in coordinating in order to win it.
<br/><br/>
Ooook, .. no worry, time to find the way for bypassing the 2 players requirement. <br>
Chimeny suggested that it was possible to play in single mode just by changing 2 parameters, client-side parameters, this means
that we have all it was needed inside our browser :). <br>
<br>
Chimney added that one of the 2 is sent to the server. <br>
<br/>
The challenge is located at: [https://hero.kringlecastle.com](https://hero.kringlecastle.com).
In order to study the logic of the game, we choose to play directly on the server. <br>
Actually, the game is served by the "hero" server and just embedded inside the page of 2021.kringelcon.com by using a 
simple HTML iframe. 
This will arise some weird behaviour lately, but let's see the logic of the game. 
<br/>
<br/>

## The First Parameter

Reading at the HTTP Requests and Responses sent from our browser and the server, we saw a particular endpoint

```Http
GET /discoverc?roomid=c_5682048266 HTTP/2

HTTP/2 200 OK
Server: nginx/1.21.4
Date: Sun, 19 Dec 2021 02:34:45 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 23
Set-Cookie: HOHOHO=%7B%22single_player%22%3Afalse%7D; Path=/; Max-Age=2592000; Secure; SameSite=None
Via: 1.1 google
Alt-Svc: clear

{"is_valid_room":false}
```
<br/>

This endpoint is called when you create the room to play (in which to invite the second player). <br>
Of course first thing to do is to set the value to "true" and see what happen in the game. 

We are interested at the value of the HOHOHO Cookie.
You can change it directly in your Browser looking in the "Inspect" Panel and searching for Storage -> Cookie. <br>
Apparently ... nothing happens but let's keep it and notice that this should be the parameter Chimeny Scissor talked about saying that there were rumors about 2 parameters, 1 of them sent to the server. <br>
Browser to work with session ... send and receive Cookie at every requests they do. 
<br/>

![before the script change](images/sleigh-before.png)

<br />
<br/>
Let's look for second parameter !

## The Second Parameter
Diving into the javascript files, needed to play the game in the broswer, we found another curious variable: "single_player_mode" in 
<br/>
```
curl 'https://hero.kringlecastle.com/assets/js/holidayhero.min.js' -H 'User-Agent: Your Kringle Fan :)' 
```
<br/>
Just by changing its value in the console view of the Inspect Panel  will raise the single player mode (to note: we're playing in https://hero.kringlecastle.com). 
<br/>
yes .. ok .. BUT we have to play inside the iframe located in 2021.kringlecon.com to have the challenge completed and get other hints under our user session
and it turned out that doing the same easy trick in the javascript console .. nothing happen!!
<br/>Do you remember that we are playing inside an iframe? This means that we are isolated from the main window and the javascript console. 
<br/><br />
So, we have just two way to change it: <br/> 

### 1\. By hand using the Tools available in the Browser

```{warning}
Every web browser differs a little from the others. <br>
We had no issues at all completing this challenge via browser using **Chrome**, while we couldn't find a way to do the same thing with **Firefox**. <br>
```
As I said, with the browser's built-in console we can edit the `single_player_mode` variable. <br>
Press F12 to enter dev tools. Then go to **Console** section. <br> <br>
 **Since we are playing in a frame inside KringleCon website, we <ins>have to change to target of the console</ins>. Select *hero.kringlecastle.com***.

 ![chromedev](images/prech6_chromedev.png)

 Now go to the bottom input field, and write:
 ```JavaScript
single_player_mode = true
 ```
The `single_player_mode` variable is now set to true.

<br/>

### 2\. Automatically by using Burp Suite  

we can do all it needs automatically by using an interceptor HTTP Proxy that modifies the script on the fly injecting the correct value.  

Easy enough isn't it? ... yep, but we have a constraint here: <br/>
because just by setting `single_player_mode=!1 (bool:false)`  ⇒ `single_player_mode=!0 (bool: true)`   
will raise an error in console log because the script, while loading, needs single_player_mode=false in order to correctly initialize the game and all its vars.<br/>
<br/>
We have to find the correct place where to inject what we want to obtain. 
<br />
By analyzing the file js it appears a function that evaluates if it's required to enable the single player mode (and that's just what we are looking for).
(line 77-78 javascript deminified)
<br />

```JavaScript
spi = setInterval(function() {
    single_player_mode && (clearInterval(spi), player2_label.showMessage("P2: COMPUTER (On)"), player2_power_button.anims.play("power_on"), toastmessage.showMessage("Player 2 (COMPUTER) has joined!"), player2_power_button.anims.pause())
}, 100);
```

So, we found the constraint just here. <br>
Actually, that function requires all the game elements fully loaded otherwise it raises an exception and the game will break. 
(note: player2_label and toastmessage are objects with methods and they need to be initialized BEFORE to use them). 
<br/>
<br/>
We want to enable single_player_mode in an ASYNC way, that is: after all elements have been initialized in the document.<br /> 
Best thing to do is to use the setTimeout function (run only once) after a minimum time frame of 50 seconds to be sure everything has been loaded. 

By using Burp Proxy we can tell it to modify (on the fly) a particular string in the response body to somewhat more useful for us, for example, let’s try:  

```JavaScript
spi = setInterval(function()
```
into:

```JavaScript
setTimeout(function(){console.log("here we go!");single_player_mode=!0},50000);spi = setInterval(function()
```

![after the change](images/sleigh-after.png)
<br/>
<br />

Done!
