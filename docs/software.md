## 1. **Random Numbers**

Sometimes we need to do something random or generate random numbers. At this time, you can use the `random` module.

For example, here's how to randomly display names on an OLED display:

``` python
from mpython import *
import random

names = ["Mary", "Yolanda", "Damien", "Alia", "Kushal", "Mei Xiu", "Zoltan"]

oled.DispChar(random.choice(names), 40, 20)
oled.show()
oled.fill(0)
```

The list (names) contains seven names defined as strings. The random.choice method takes a list of names as a parameter and returns a randomly selected item.

**Can you modify the list to include your own name?**

### 1.1. Display Numbers Randomly<br>

Random numbers are very useful. They are very common in the game. Why do we still have dice?

MicroPython comes with several useful random number methods. Here's how to make a simple dice:

```python
from mpython import *
import random

oled.DispChar(str(random.randint(1, 6)), 60, 20)
oled.show()
oled.fill(0)
```

Every time the dashboard is restarted, it will display a number between 1 and 6. randint() returns an integer, we need to use str() to convert the integer into a string (for example, 6 -> "6"). oled.DispChar() writes random numbers to oled.

If you want to set a random range or increasing base, you can use random.randrange():

``` python
from mpython import *
import random

oled.DispChar(str(random.randrange(0, 10, 2)), 60, 20)
oled.show()
oled.fill(0)
```

random.randrange(start, end, step). _**start**_ is the starting value of the random number, _**end**_ is the ending value of the random number, and _**step**_ is the increasing base. The above example randomly displays even numbers in the range (0,10).

Sometimes you need numbers with decimal points. You can use random.random() to generate a random floating point number from 0.0 to 1.0. If you need the result of adding larger random floating point numbers use random.uniform():

```python
from mpython import *
import random

oled.DispChar(str(random.random()), 30, 10)
oled.DispChar(str(random.uniform(1, 20)), 30, 30)
oled.show()
oled.fill(0)
```

### 1.2. Random Seeds
The random number in MicroPython is actually a stable sequence of results obtained by a stable algorithm, not a random sequence. The seed is the first value that the algorithm starts calculating. So it will appear that as long as the seed is the same, all subsequent "random" results and sequences will be exactly the same.

Specify a random number seed, usually used in conjunction with other random number generation functions

Sometimes you want to have repeatable random behavior: a reproducible source of randomness. It's like saying you need the same five random values every time you roll a die.

Example:

``` python
import random
from mpython import *

for i in range(0, 2):
    random.seed(8)

    for j in range(8):
        oled.DispChar(str(random.randint(1, 10)), j * 16, i * 16)
        oled.show()
        print(random.randint(1, 10))

oled.fill(0)
```

### 1.3. Falling Snow Effect
Combined with the random number generation learned above, we can use the control panel OLED screen to create the effect of falling snowflakes.

``` python
from mpython import *
from random import randint

class snow():
    def __init__(self):                
        self.x = randint(0, 127)         # Randomly generate the starting coordinate point of the snowflake
        self.y = randint(0, 10)
        self.r = randint(1, 2)           # Randomly generate the size of the snowflake radius
        self.vx = randint(-2, 2)         # Randomly generate the x,y movement path of the snowflake
        self.vy = randint(1, 3)         

    def refresh(self):                 
        self.x += self.vx               # Move the coordinate down, snowflake falls
        self.y += self.vy
        if self.x > 128 or self.x < 0:
            self.x = randint(0, 127)
        if self.y > 63 or self.y < 0:
            self.y = 0
            
    def run(self):
        self.refresh()
        oled.fill_circle(self.x, self.y, self.r, 1)     # Draw the snowflake
balls = []
for x in range(20):              # Generate 20 snowflake points
    balls.append(snow())        
while True:
    sleep_ms(50)                 # Refresh time
    oled.fill(0)                 # Clear the screen
    for b in balls:              # Snowflake falls
        b.run()
    oled.show()                  # Display OLED
```
## 2. **Save**

Sometimes you need to store useful information. This information is stored as data: a representation of the information (in digital form when stored on a computer). If you store data on your computer, it should remain even if you turn the device off and on again.

The microPython bit allows you to do this using a very simple file system.

#### What is a file system?

It is a method of storing and organizing data in a persistent manner - any data stored in the file system should survive device restarts. As the name suggests, data stored in a file system is organized into files.

Computer files are named digital resources stored on a file system. These resources contain useful information as data. This is exactly how paper documents work. It is a named container that contains useful information. Often, paper and digital files are named to indicate what they contain. On computers, files are usually ended with a suffix like `.txt` for text files, `.jpg` for JPEG images, and `.mp3` for sound data encoded as MP3.

Some file systems, such as those on your laptop or PC, allow you to organize files into directories: named containers that group related files and subdirectories together. However, the file system provided by MicroPython is a flat file system. Flat file systems have no directories - all files are stored in just one place.

The Python programming language contains an easy-to-use and powerful way to work with your computer's file system. MicroPython on mPython implements a useful subset of these features, making it easy to read and write files on the device, while also providing consistency with other Python versions.

### 2.1. Open the file

The following are relevant operation instructions for Python File operations.

```python
open(path+filename, mode), e.g., f = open('/tmp/hello', 'w')
```