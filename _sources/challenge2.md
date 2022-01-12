(ch2)=
# Track the Elf!
## Where in the world is Caramel Santaigo?
<br>
To start this challenge go to Santa's Courtyard and speak with Tangle Coalbox. <br> 
Click on the Cranberry and press the green button "start game". You will see the first location of the game, Santa's Castle.
<br>
<br>

![Image1](images/ch2_1.png)

## The Standard Method
Your objective is to try to follow the elf's moves and find it. When you find him/her, you will have to guess the name. <br>
+++
During the game you are going to find some hints, which you can insert into the *InterRink* and receive it's name in exchange.
The game is pretty simple, there are 3 options:
1. Investigate <br>
    You can use this button to get **3** hints from the game, which contains informations about the elf's location and/or the elf's name
2. Visit InterRink <br>
    In this form you can input the information you got about the elf, and it returns the name of the elf who most corrisponds to the description.
3. Depart by Sleigh <br>
    Using this button you can move on to the next stage of the game. It will show you three possibile destinations, and you have to guess which one is the most likely the elf went to.

For example, you could get hints like these:
> They were excited that their phone was going to work on the 1500 MHz LTE band <br>

> They were dressed for 6.0°C and partly cloudy conditions. The elf mentioned something about Stack Overflow and
Rust. <br>

> They said, if asked, they would describe their next location as "only milder vanilla." 

Now that we have these hints, we can work on them. For example, Hint #1 can be solved with a quick search. <br>
Turns out that *LTE 1500MHz* is used in Japan. [Wikipedia Link](https://en.wikipedia.org/wiki/LTE_frequency_bands)<br>
<br>
And this is enough for the location. If we click on Depart by sleigh, these three locations are available:
* Tokyo, Japan
* Vienna, Austria
* Montréal, Canada

Well...only Tokyo is in Japan, so that's the place we were looking for! <br>
<br>
And this goes on for a while. There are other types of hints you could receive form the game, like, for example, some geo coordinates. A tool that can help you understand this other types of data is [**OSINT Framework**](https://osintframework.com/).

In some cases the elf will give you a photo, which contains geolocalization data in the metadatas. We can use `exiftool` to view it.
* If you don't have it, download end install exiftool. Linux is recommended (`apt-get install exiftool`)
* Download the image of the elf on you computer
* Run `exiftool <image name>` 

There you have all the 'hidden' data the image contains. In the bottom part you will see some GPS info, which you can use to track the elf.


<br>

+++
Mh? This sounds a bit too long and *not suitable for an hacking challenge*? Well, in this case, let's welcome

## The... "Alternative" Method!
<br>

Let's play a bit with the web browser and see if we can find where the game stores the session/savegame.
1. Open **Developer Tools** by pressing **F12** or with **Right click -> Inspect Element**
2. Move to **Storage** tab.
3. Expand **Cookies** and see what's in there.

We have an entry for *caramel.kringlecastle.com*, which is the domain where this game is hosted from.
By clicking on it you will find a cookie named **"Cookiepella"**, and it's corrisponding value is a strange long string..Mh.

<br>
If you completed the Pre-challenge Cranberry you will se an hint in your badge:
<br> 

> *While Flask cookies can't generally be forged without the secret, they can often be decoded and read.* (https://gist.github.com/chriselgee/b9f1861dd9b99a8c1ed30066b25ff80b)

In Chris Elgee's short guide he manages to decode a string very similar to the one we found in the cookie.
Let's follow his procedure. You can use pyhton3 commands if you have access to a python installation, or [CyberChef](https://gchq.github.io/CyberChef/). <br>
For CyberChef follow these steps:
1. Enter the cookie in the input field
2. Apply `From Base64` with alphabet `URL Safe`
3. Apply `Zlib inflate` and click bake.

You will now see in the output field our decoded cookie, which contains all the data we need to cheat :) <br>
*(this is an example, yours will be different!)*
  
> {"elf":"*<mark>Jingle Ringford</mark>","elfHints":["The elf mentioned something about Stack Overflow and Golang.","The elf got really heated about using spaces for indents.","They kept checking their Snapchat app.","Oh, I noticed they had a Star Wars themed phone case.","hard"],"location":"Santa's Castle","options":[["Reykjav\u00edk, Iceland","Copenhagen, Denmark","Prague, Czech Republic"],["Prague, Czech Republic","Reykjav\u00edk, Iceland","Antwerp, Belgium"],["Prague, Czech Republic","New York, USA","Antwerp, Belgium"],["Placeholder","London, England","Montr\u00e9al, Canada"]],"randomSeed":657,"route":[<mark>"Reykjav\u00edk, Iceland","Antwerp, Belgium","New York, USA","Placeholder"</mark>],"victoryToken":"{ hash:\"9ba6eca18fbb1cb3177f99dbb9f0051607fac4e71f3171cfffa7e7e70caa25dd\", resourceId: \"dcc818d4-30b5-4978-ab01-f76a986465e4\"}"}

And there you go, you have all the information you need to complete the game.


 

    

