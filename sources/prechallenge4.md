(prech4)=
Logic Munchers
==================

In this Cranberry we have to play a game about logic expression at `Intermediate / Poutpurri` difficulty. <br>
When you click play you will find a table full of logical expressions. You can control the green troll with `WASD` or `arrow keys` and when it is in a box with a `TRUE` expression, press `SPACE`. <br>
In the meanwhile, some pink things called *Trollogs* will roam in the map, changing the expression where they transit, and eating you when you meet them.


## Types of Expressions

There are four types of expressions:
1. Boolean Logic
2. Arithmetic Expressions
3. Number Conversions
4. Bitwise Operations

The **potpourri** method combines them all.

### Boolean Logic
These are plain boolean logic. <br>
Some examples:
* True or True --> True
* True --> True
* not True --> False
* True and False --> False

### Arithmetic Expressions
Plain Maths. If the equation is true, the box is true too. <br>
Some examples:
* 9 < 1 --> False
* 3 <= 6 --> True
* 6 - 8 = -2 --> True

### Number Convertions
You are given a number in `binary` system (left), and a number in `deciaml` system. If they match, the box is true. <br>
Some examples:
* 0b0111 = 9 --> False (Binary: 0111 = Decimal: 9 | False)(7 = 9 | False)
* 0b1000 = 8 --> True (Binary: 1000 = Decimal: 8 | True)(8 = 8 | True)
* 0b0000 = 0 --> True (Binary: 0000 = Decimal: 0 | true)(0 = 0 | True)

### Bitwise Operations
You have to perform a bit slide or a comparison in binary numbers. If the contidion is true, the box is true.

```{admonition} Remember!
`||` is boolean `OR`, while `&` is boolean `AND`
```

Some examples: <br>
Binary slide
* 0b0111 >> 1 = 0b0011 --> True (0b0111, slide the number to the right (`>>`) by `1` position. It becomes 0b0011).
* 0b1000 >> 2 = 0b0001 --> False  (0b1000, slide the number to the right (`>>`) by `2` positions. It becomes 0b0010).

Comparison
* 0b0110 || 0b0100 = 0b0110 --> True
    * Is 0b0110 == 0b0110? True.
    * Is 0b0100 == 0b0110? False.
    * True `OR` False --> True
* 0b0001 & 0b0101 = 0b0001 --> False
    * Is 0b0001 == 0b0001? True.
    * Is 0b0101 == 0b0001? False.
    * True `AND` False --> False

You get it.

## The Game

Move around the grid and select the True statements, while staying away from the trollogs. When the level is complete, you wil get alerted.

![TheGame](images/prech4_1.png)
