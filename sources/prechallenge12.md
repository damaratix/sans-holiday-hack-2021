(prech12)=
Python Game
=======================

This terminal is a bit different from the others. It's a videogame...but the controls are in Python! <br>

Everything you need to know about the game is already well-explained in the interface. Let's begin with the various levels!

## Levels
### Level 0 - Done!

The first level is already completed. You can examine and edit the code to better understand the various functions.

### Level 1 - Move!

It's time to start writing code.

```Python
import elf, munchkins, levers, lollipops, yeeters, pits #Libraries used in the game
elf.moveLeft(10) #Moves the elf left by 10 boxes
elf.moveUp(11) #Moves the elf up by 11 boxes
```
Well..yes, this is pretty self-explaining.

### Level 2 - Pathfinding Moving

Instead of directly commanding the elf, we can tell him to move to the position of and object, if there are no obstacles in the path.

```Python
import elf, munchkins, levers, lollipops, yeeters, pits
lollipop1 = lollipops.get(1) #Get position of lollopop1 and store it in lollipop1 variable
lollipop0 = lollipops.get(0) #Get position of lollopop0 and store it in lollipop0 variable
elf.moveTo(lollipop1.position) #Move to lollipop1 position
elf.moveTo(lollipop0.position) #Move to lollipop0 position
elf.moveTo({"x":2, "y":2}) #Cartesian grid move
```

### Level 3 - A Danger for our Elf!

These levels are starting to get dangerous for our precious elf. We have to shut down that yeeter at any cost! <br>
You can click on the lever in the objects list to see it's requirements to be turned off.

```Py
import elf, munchkins, levers, lollipops, yeeters, pits
lever0 = levers.get(0) #Same thing as for lollipops, but with levers!
lollipop0 = lollipops.get(0)
elf.moveLeft(6)
lever0.pull(lever0.data() + 2) #Add to to lever0.data(), and pull lever0
elf.moveLeft(4)
elf.moveUp(11)
```

### Level 4 - Different Data Types

Don't be afraid of all these levers! We can turn them off like we did with the last one. <br>
We see each lever's requirements in objects tab, and give them what they want. <br>

More info about each data type [**here**](https://www.freecodecamp.org/news/the-python-guide-for-beginners/#types).

```Py
import elf, munchkins, levers, lollipops, yeeters, pits
# Complete the code below:
lever0, lever1, lever2, lever3, lever4 = levers.get()
# Move onto lever4
elf.moveLeft(2)
# This lever wants a str object:
lever4.pull("A String")
# Need more code below:
elf.moveUp(2)
lever3.pull(True) #Bool
elf.moveUp(2)
lever2.pull(1) #Integer
elf.moveUp(2)
lever1.pull(["obj1", "obj2"]) #List
elf.moveUp(2)
lever0.pull({"obj1":1, "obj2":2}) #Dictionary
elf.moveUp(2)
```

### Level 5 - Operations

In this level we have to contatenate two strings, do a bool comparison, append a value and access a dictionary.

```Py
import elf, munchkins, levers, lollipops, yeeters, pits
# Fix/Complete Code below
lever0, lever1, lever2, lever3, lever4 = levers.get()
# Solve for each lever, moving to the space
# on the lever before calling leverN.pull()
elf.moveLeft(2)
lever4.pull(lever4.data() + " concatenate") #Concatenate lever4.data() with " concatenate"
elf.moveUp(2)
lever3.pull(not lever3.data()) #Boolean operator NOT. The reverse of lever3.data()
elf.moveUp(2)
lever2.pull(lever2.data() + 1) #Add 1 to lever2.data()
elf.moveUp(2)
lever1_answer = lever1.data() #Set lever1_answare to lever1.data()
lever1_answer.append(1) #Append integer 1 to lever1_answer
lever1.pull(lever1_answer) #Pass lever1_answer to lever1
elf.moveUp(2)
answer = lever0.data() #Set answare to lever0.data()
answer["strkey"] = "strvalue" #Add a value "strvalue" under the key "strkey". This is a dictionary
lever0.pull(answer) #Pass nswer to lever0
elf.moveUp(2)
```

### Level 6 - IFs

Now we have to compare two values using `if statements`. We check for the type of the variable, which is given randomly by the game, and compare it with known types. If the type is met, do something.

```Py
import elf, munchkins, levers, lollipops, yeeters, pits
# Fix/Complete the below code
lever0 = levers.get(0)
data = lever0.data()

if type(data) == bool:
    data = not data
elif type(data) == int:
    data = data * 2
elif type(data) == list:
    idx = 0
    for ele in data:           #Loop. For every element inside data
        data[idx] = ele + 1    
        idx = idx + 1
elif type(data)== str:
    data = data + data
elif type(data)== dict:
    data["a"] = data["a"] + 1
      
elf.moveUp(2)
lever0.pull(data)
elf.moveUp(2)
```

### Level 7 - Loops

Mhh...this seems like a long path. We have to use a `loop` if we want to stay within the line limit.

```Py
import elf, munchkins, levers, lollipops, yeeters, pits
for num in range(3): #We repeat this three times
    elf.moveLeft(3)
    elf.moveUp(12)
    elf.moveLeft(3)
    elf.moveDown(12)
```

### Level 8 - The Maze and the Munchkin

To solve this level we have to create a code that mixes everything we learned in the past levels.

```Py
import elf, munchkins, levers, lollipops, yeeters, pits
lollipop0, lollipop1, lollipop2, lollipop3 = lollipops.get()
munchkin = munchkins.get(0)
elf.moveTo(lollipop0.position)  # ---Elf Moving---
elf.moveTo(lollipop1.position)
elf.moveTo(lollipop2.position)
elf.moveTo(lollipop3.position) 
elf.moveTo({"x":2, "y":2})      # ---End Elf Moving---
mydict = munchkin.ask().items() #Declares the dictionary
for k, v in mydict:             #Retrives key from the value
    if v == "lollipop": munchkin.answer(k) #Answer if the value is lollipop
elf.moveUp(2)
```

Wow, we won! The challenge is complete, but if you want, there are some bonus levels ready to be solved! Good Luck!