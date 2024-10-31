## Getting Started

[Download Software ](https://mpythonboard.bitandbrick.com){ .md-button }

# 1. REPL
One of the main advantages of using MicroPython is the interactive REPL. 

_**REPL**_ stands for read-evaluate-output loop. REPL is a great help for learning a new programming language because it can respond immediately to programs written by beginners, which means you can execute the code and see the results immediately without having to compile and upload it first. Cumbersome steps

## 1.1.Serial port connection
To access via USB-serial, you need to use serial terminal software.

Set the serial port baud rate to 115200 and you can start playing MicroPython.

Once the connection is established through the serial port, you can test whether it is working properly by pressing the Enter key a few times. If it is working, you will be able to see the Python REPL prompt, represented by >>>.

## 1.2. Using REPL

Once you're prompted, you're ready to try it! After pressing the Enter key, you can type anything at the prompt. MicroPython will run the code you enter and print the results (if any); if there is an error in the entered text, an error message will be printed.

_Try typing the following at the prompt:_

``` python
print('hello mPython')
hello mPython
```

Note that you do not need to type >>>the arrows, they indicate that you should type text after this prompt and the next line will be the content of the response.

If you already know some python, you can now try some basic commands. For example:

You can try downloading mPython to display characters on an OLED display:

``` python
from mpython import *
oled.DispChar('hello,world!',0,0)
oled.show()
```
In block code , 


oled.DispChar(str,x,y) stris the string to be displayed, xand , yare the x and y coordinates of the display starting point. Then oled.show()after refreshing the screen with , the string can be displayed on the OLED display. You can try displaying arbitrary strings elsewhere.

##### 1.2.1. Line editing

You can use the left and right arrow keys to move the cursor to edit the currently entered line; press the Home key or ctrl-A to move the cursor to the beginning of the line, and press End or ctrl-E to move to the end of the line; the Delete key or Escape The space key is used to delete.

##### 1.2.2.Input history
The REPL remembers a certain number of the first few lines of text you enter (up to 8 lines on ESP32). To recall the previous line, use the up and down arrow keys.

##### 1.2.3. Tab key
Tab key to view a list of all members in the module. This is useful for finding out what functions and methods a module or object has. Assuming you imported machine in the above example then type .and press Tab to see a list of all members of the machine module:
machine.

| __class__ | __name__ | _ADC_ | DAC |
|-----------|----------|-------|-----|
| DEEPSLEEP | DEEPSLEEP_RESET | EXT0_WAKE | |
| EXT1_WAKE | HARD_RESET | I2C | |
| PIN_WAKE | PWM | PWRON_RESET | Pin |
| RTC | SLEEP | SOFT_RESET | SPI |
| Signal | TIMER_WAKE | TOUCHPAD_WAKE | Timer |
| TouchPad | UART | ULP_WAKE | WDT |
| WDT_RESET | deepsleep | disable_irq | enable_irq |
| freq | idle | mem16 | mem32 |
| mem8 | reset | reset_cause | sleep |
| time_pulse_us | unique_id | wake_reason | machine. |

##### 1.2.4. Line continuation and automatic indentation
Some of what you type will need to "continue", that is, more lines of text will be needed to generate a correct Python statement. In this case, the prompt will change to ``...`` and the cursor will automatically indent the correct amount so that you can immediately start typing the next line. Try this by defining the following function:

``` python
def toggle(p):
   p.value(not p.value())
```

Above, you need to press the Enter key three times in a row to complete the compound statement (i.e. three lines with just dots). Another way to complete a compound statement is to press the backspace key to get to the beginning of the line and then press the Enter key. (If you make a mistake and want to exit, press ctrl-C and all lines will be ignored.)

You just defined the function to flip the pin level. The pin object you created earlier should still exist (if not, you'll need to recreate it) and you can flip the LED using:

toggle(pin)

Now let's toggle the LED in a loop (if you don't have an LED, then you can print some text instead of calling toggle and see the effect):

``` python
import time
while True:
    toggle(pin)
    time.sleep_ms(500)
``` 

This will flip the LED at 1Hz (half a second on, half a second off). To stop the toggle press ctrl-C, this will raise a keyboard interrupt exception and exit the loop.

#### 1.2.5.Paste mode

Pressing ctrl-E will enter special paste mode, which allows you to copy and paste a large block of text into the REPL. If you press ctrl-E you will see the paste mode prompt:

paste mode; Ctrl-C to cancel, Ctrl-D to finish

---
You can then paste (or type) your text. Note that there are no special keys or commands that work in paste mode (such as Tab or Backspace), they are just accepted as-is. Press ctrl-D to finish entering text and execute.

# 2.Display

The control board has an onboard 1.3-inch OLED display with a resolution of 128x64.

### 2.1 Text Display

To display text, first import the `mpython` module:

``` python
from mpython import *
```


``` python 
# To visualize how characters are displayed on the screen, choose pixel inversion mode
oled.DispChar('hello, world!',0,0,mode=TextMode.rev)
# By default, the background pixels are turned off
oled.DispChar('hello, world!',0,16,mode=TextMode.normal)
oled.show()
```

The DispChar(str,x,y) function writes text content into the FrameBuffer. x and y are the starting xy coordinates for the OLED. oled.show() refreshes the screen.

To display  "hello, world" text:

![OLED](olde-1.jpg)
``` python
oled.DispChar('hello, world', 32, 16)
```


### 2.2 Basic Shape Drawing

Example: Drawing lines

```python
Copy code
def testdrawline():
    for i in range(0,64):
        oled.line(0,0,i*2,63,1)
        oled.show()
    # Other line drawing code...


Display Pictures
``` python
Copy code
# Load bitmap data
bmp = bytearray([...])  # Bitmap data array
oled.bitmap(0, 0, bmp, 128, 64, 1)
oled.show()
```

### 2.3 Dynamic Display

``` python
images = []
for n in range(1,7):
    with open('scatman.%s.pbm' % n, 'rb') as f:
        f.readline() # Magic number
        f.readline() # Creator comment
        f.readline() # Dimensions
        data = bytearray(f.read())
    fbuf = framebuf.FrameBuffer(data, 128, 64, framebuf.MONO_HLSB)
    images.append(fbuf)
```


``` python
oled.invert(1)
while True:
    for i in images:
        oled.blit(i, 0, 0)
        oled.show()
        time.sleep(0.1)
```

_For more detailed information on OLED display operations and shape drawing, please refer to bitandbrick tutorials_

--- ---

# 3. RGB LED

mPython controls three onboard WS2812 lamp beads. WS2812 is a low-power RGB three-color lamp integrated with a current control chip. It can achieve 256 levels of brightness display and complete true color display of 16,777,216 colors. It uses a special single-line communication method to control the color of RGB lights, which is easy to use.

### 3.1 Onboard RGB LED

#### Example: Light up RGB LED

```python
from mpython import *
rgb[0] = (255, 0, 0)  # Set to red, full brightness
rgb[1] = (0, 128, 0)  # Set to green, half brightness
rgb[2] = (0, 0, 64)   # Set to blue, quarter brightness
rgb.write()
```

First import the mpython module:

from mpython import *

After importing the mpython module, a NeoPixel object rgb will be created for control. To control the onboard RGB, you only need to operate on the rgb object.

Set color:

```python
rgb[0] = (255, 0, 0)  # Set to red, full brightness
rgb[1] = (0, 128, 0)  # Set to green, half brightness
rgb[2] = (0, 0, 64)   # Set to blue, quarter brightness
```

rgb[n] = (r, g, b) can set the color of each pixel, n which is the number of onboard RGB lights. The first light is 0. r, g, b are the color brightness values, and the range is 0~255.

rgb.fill(rgb_buf) can fill the color of all pixels, such as: rgb.fill((255,0,0)), all RGB lights are set to red, full brightness.

Output color to RGB light

rgb.write()

### 3.2 External ribbons
Example: Light up the external ribbon

from mpython import *
import neopixel

np = neopixel.NeoPixel(Pin(Pin.P15), n=24, bpp=3, timing=1)

#### Functions for creating effects...

If you need to use an external ribbon, you must first create a neopixel object, define the parameters pin, and then control the LED on the ribbon through the object. For more detailed usage, please refer to the neopixel module.

Hint: mPyhton provides an enhanced version of the neopixel module, which is encapsulated with richer neopixel display effects and is easy to operate. For detailed instructions, please visit bitandbrick tutorials

wiki.mpythonboard.com
    wiki_Home Page|wiki_Hardware|wiki_Software

Hardware Overview — mPython board 2.2.2 documentation


The python board is a micropython microcontroller board with full support for Micropython/Python software features.

Technical Specifications

mPython board has the following hardware features : 

ESP32 MIcrocontroller 
Processor : Tensilica LX6 dual core microprocessor(one for handling high speed connections and other for independent application development.) 
Main frequency :upto 240 MHz clock frequency.
Flash 8MB
Wi-Fi Standard : FCC/CE/TELEC/KCC
Wi_Fi Alliance : 802.11 b/g/n/d/e/i/k/r (802.11n,high speed 150 Mbps),A-MPDU and A-MSDU packed,support 0.4us protective interval.
Frequency Range : 2.4~2.5 GHz
Bluetooth Protocol : Comply to Bluetooth 4.2 BR/EDR and BLE standard.
Bluetooth Audio Streaming : CVSD and SBC audio low power :10uA
Power supply mode : Micro USB
Operating Voltage :3.3 V
Maximum Operating System :200mA
Maximum Load Current : 1000mA

Mpython board Integrated Hardware Specifications


3-Axis Accelerometer MSA300,Range ±2/4/8/16G
Geomagnetic Sensor-MMC5983MA,Range ± Gauss;Accuracy 0.4mG,Electronic Compass Error±0.5°
Light Sensor
Microphone
3x ws2812 LED, RGB
1.3” OLED Panel, support 16*16 characters display, Resolution 128x64
Passive Buzzer
Supports 2x touch switch (User A/B), 1x Reset Button, 6x Touch Button
Supports 1x crocodile clip interface: resistive sensor input
           Interface for external device expansion
20x digital I/O, (it supports 12x PWM, 6x Touch Pad)
5x 12 bit ADC, P0~P4
1x external hardware input via crocodile clip interface :EXT/GND
supports I2C, UART, SPI communication protocol


Components and Layout

Python board interface pin configuration

# Sensors
The mpythonboard is equipped with an AI Camera which is a very unique part of the Mpythonboard,it is also equipped with  a bundle of sensors.
The sensors are easy to connect.
It will help students to create and explore different project ideas.

The sensors include humidity and temperature, barometric sensor,force sensor,magnetic fields,pH sensor and conductivity sensor,light,sound,ultrasonic sensors,It also includes vibration sensor,voltage meter,soil moisture,gesture,color,air pressure,joystick and rotate dimmer,it also has RFID sensor.
The mpythonboard board provides a wide range of sensors which can help explore across all subject area whether it is Physics,Chemistry or Biology giving hand-on experience to the students and making learning more engaging.
Sensors images link
##RGB
mPython Board built-in with three WS2812 LED, WS2812 is a low-power RGB tri-color LED integrated WS2811 driver, an integrated current control chip, it can achieve 256-level brightness display and complete true color display of 16777216 colors. A special single-line communication method is used to control the color of RGB lights, which is easy to use.
Key buttons-There are two push buttons A and B on the upper edge of the control panel.
 Low level when the button is pressed, otherwise high level.
The process of pressing the keys on the control panel A and B is as follows. When pressed, the level changes from high to low, and the moment when the high level (1) changes to the low level (0) is called the falling edge. When the button is released, the level changes from low to high, and the moment when the low level (0) changes to the high level (1) is called the rising edge. We can get the current key state by getting the level change.
# Touchpad 
The 6 goldfinger on the front of the mPython Board as touchpads for expansion, indicated as P、Y、T、H、O、N in sequence.
Microphone -The microphone built-in the mPython Board, use it to perceive the sound changes in the surrounding environment.
# Light Sensor
The built-in light sensor on the mPython Board, uses it to sense light changes in the surrounding environment.
Accelerometer-The acceleration sensor can measure the acceleration due to gravity. During the acceleration process, the sensor uses Newton's second law to obtain the acceleration value by measuring the inertial force received by the mass. The accelerometer on the mPython Board can measure acceleration, with a measurement range of -2g to + 2g.。
It is a 3-axis measurement of the mPython Board with positive or negative values on each axis. As the positive axis approaches the direction of gravitational acceleration, its value increases toward the positive direction, and conversely decreases toward the negative direction.
X - Tilt forward and backward.
Y - Tilt left and right。
Z - Flip up and down。