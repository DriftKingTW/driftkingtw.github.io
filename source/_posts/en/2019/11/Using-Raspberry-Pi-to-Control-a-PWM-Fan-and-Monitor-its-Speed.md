---
title: Using Raspberry Pi to Control a PWM Fan and Monitor its Speed
tags:
  - RaspberryPi/樹莓派
  - DIY
keywords:
  - Raspberry Pi
  - PWM Fan Control
  - Tachometer
  - read tach
abbrlink: '36297e41'
date: 2019-11-25 22:00:00
---

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1574826956/blog/2019/11/Using%20Raspberry%20Pi%20to%20Control%20a%20PWM%20Fan%20and%20Monitor%20its%20Speed/Fur_chan_w_Noctua_fan_2.jpg)

A lot of people uses fan to cool down their Raspberry Pi 4, but the fan's noise is quiet problem (in a quiet room). In most case pi didn't need that amount of cooling performance, so we can use [PWM (Pulse Width Modulation)](https://en.wikipedia.org/wiki/Pulse-width_modulation) to control fan speed and reduce the noise.<!--more-->

# Wiring

I use [Noctua NF-A4 5V PWM](https://noctua.at/en/nf-a4x10-5v-pwm), a 40mm, 5V PWM fan. Here's the wiring diagram:

- Yellow: 5V
- Black: GND
- Green: RPM signal
- Blue: PWM signal input

{% colorquote info %}
If your fan doesn't support PWM control, then you can see [#138 Variable Speed Cooling Fan for Raspberry Pi using PWM and PID controller](https://www.youtube.com/watch?v=oJ32CMxliCQ) or [PWM Regulated Fan Based on CPU Temperature for Raspberry Pi](https://www.instructables.com/id/PWM-Regulated-Fan-Based-on-CPU-Temperature-for-Ras/) to use a BJT transistor to use PWM control.
{% endcolorquote %}

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1574608109/blog/2019/11/Using%20Raspberry%20Pi%20to%20Control%20a%20PWM%20Fan%20and%20Monitor%20its%20Speed/IMG_93F334AD3BFC-1.jpg)

The fan's RPM signal is an OC(Open-Collector) circuit design (in most fan), so you'll need a pull-up resistor to measure output waveform according to [Noctua PWM white paper](https://noctua.at/media/wysiwyg/Noctua_PWM_specifications_white_paper.pdf).

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1574608621/blog/2019/11/Using%20Raspberry%20Pi%20to%20Control%20a%20PWM%20Fan%20and%20Monitor%20its%20Speed/IMG_6F5175335AA4-1.jpg)

{% colorquote danger %}
Warning! The Pi has 3.3V GPIO, so your pull-up resistor must be connect to 3.3V ONLY! You will fry your Pi if Vcc is connected to 5V.
{% endcolorquote %}

The pull-up resistor that I use is 1kΩ 1/4W. The white paper says the maximum current is 5mA. According to Ohm's Law: $V=IR$, the resistor should be 660Ω or larger to protect your fan.

Most micro-controller or SBC already have a decent circuit to generate PWM signal, you don't need special circuit to generate it.

I use a Molex 2510 4PIN (2.54mm) connector to connect the fan. (Need to cut out some fool-proof board to fit.)

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1574610745/blog/2019/11/Using%20Raspberry%20Pi%20to%20Control%20a%20PWM%20Fan%20and%20Monitor%20its%20Speed/IMG_20191123_141251.jpg)

Here's a photo testing hardware and program on Pi 3B.`

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1574610248/blog/2019/11/Using%20Raspberry%20Pi%20to%20Control%20a%20PWM%20Fan%20and%20Monitor%20its%20Speed/IMG_20191124_234259.jpg)

# Program Control

### PWM GPIO Test

I use [Wiring Pi](http://wiringpi.com/) to test PWM signal. If you don't want to test it, jump to [Use PWM to Control Fan Speed](#Use-PWM-to-Control-Fan-Speed).

The Pi4 haven't update to latest version of wiring-pi yet, need to update it manually.

```shell
cd /tmp
wget https://project-downloads.drogon.net/wiringpi-latest.deb
sudo dpkg -i wiringpi-latest.deb
```

Version check：

`gpio -v`

{% colorquote info %}
The latest version when I write this post is 2.52
{% endcolorquote %}

Read GPIO status：

`gpio readall`

Set pin 1 to PWM mode (using wiring-pi pin number: [Pi GPIO Pinout](https://pinout.xyz/)).

`gpio mode 1 pwm`

Set PWM signal (number from 0~1023)

`gpio pwm 1 [number]`

Check the fan is working as you expect.

### Use PWM to Control Fan Speed


The control script is on: [DriftKingTW/Raspberry-Pi-PWM-Fan-Contorl](https://github.com/DriftKingTW/Raspberry-Pi-PWM-Fan-Contorl/blob/master/fan_contorl.py)

`mkdir -p Scripts`

`cd Scripts/`

Download the python script:

```shell
wget https://raw.githubusercontent.com/DriftKingTW/Raspberry-Pi-PWM-Fan-Contorl/master/fan_contorl.py
```

`python fan_control.py`

And make it auto start: 

Edit `rc.local`

```shell
sudo vim /etc/rc.local
```

Add the following line after `exit 0` :

```shell
python /home/pi/Scripts/fan_control.py &
```

{% colorquote info %}
The route might be different for you. Make sure you're using absolute path. The '&' symbol means this script runs in background.
{% endcolorquote %}

If you don't want to know the theory part, than go ahead to: [Reading RPM Signal](#Reading-RPM-Signal)

PWM uses square wave's duty-cycle to reduce the average value of voltage, if the duty cycle is 50%, the fan will spin at 50% of it's full speed; duty cycle 75%, fan 75%, and so on. More detail: [Pulse-width modulation](https://en.wikipedia.org/wiki/Pulse-width_modulation)

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1574611525/blog/2019/11/Using%20Raspberry%20Pi%20to%20Control%20a%20PWM%20Fan%20and%20Monitor%20its%20Speed/IMG_AABD9EE6FAB5-1.jpg)

And we can take a look at the control script:

```python
import RPi.GPIO as GPIO
import time
import signal
import sys
import os

# Configuration
FAN_PIN = 18            # BCM pin used to drive PWM fan
WAIT_TIME = 1           # [s] Time to wait between each refresh
PWM_FREQ = 25000        # [Hz] 25kHz for Noctua PWM control

# Configurable temperature and fan speed
MIN_TEMP = 40
MAX_TEMP = 70
FAN_LOW = 40
FAN_HIGH = 100
FAN_OFF = 0
FAN_MAX = 100

# Get CPU's temperature
def getCpuTemperature():
    res = os.popen('vcgencmd measure_temp').readline()
    temp =(res.replace("temp=","").replace("'C\n",""))
    #print("temp is {0}".format(temp)) # Uncomment for testing
    return temp

# Set fan speed
def setFanSpeed(speed):
    fan.start(speed)
    return()

# Handle fan speed
def handleFanSpeed():
    temp = float(getCpuTemperature())
    # Turn off the fan if temperature is below MIN_TEMP
    if temp < MIN_TEMP:
        setFanSpeed(FAN_OFF)
        #print("Fan OFF") # Uncomment for testing
    # Set fan speed to MAXIMUM if the temperature is above MAX_TEMP
    elif temp > MAX_TEMP:
        setFanSpeed(FAN_MAX)
        #print("Fan MAX") # Uncomment for testing
    # Caculate dynamic fan speed
    else:
        step = (FAN_HIGH - FAN_LOW)/(MAX_TEMP - MIN_TEMP)   
        temp -= MIN_TEMP
        setFanSpeed(FAN_LOW + ( round(temp) * step ))
        #print(FAN_LOW + ( round(temp) * step )) # Uncomment for testing
    return ()

try:
    # Setup GPIO pin
    GPIO.setwarnings(False)
    GPIO.setmode(GPIO.BCM)
    GPIO.setup(FAN_PIN, GPIO.OUT, initial=GPIO.LOW)
    fan = GPIO.PWM(FAN_PIN,PWM_FREQ)
    setFanSpeed(FAN_OFF)
    # Handle fan speed every WAIT_TIME sec
    while True:
        handleFanSpeed()
        time.sleep(WAIT_TIME)

except KeyboardInterrupt: # trap a CTRL+C keyboard interrupt
    setFanSpeed(FAN_HIGH)
    #GPIO.cleanup() # resets all GPIO ports used by this function
```

### Reading RPM Signal

Github: [DriftKingTW/Raspberry-Pi-PWM-Fan-Contorl](https://github.com/DriftKingTW/Raspberry-Pi-PWM-Fan-Contorl/blob/master/read_fan_speed.py)

```shell
wget https://raw.githubusercontent.com/DriftKingTW/Raspberry-Pi-PWM-Fan-Contorl/master/read_fan_speed.py
```

Run script and you will get RPM value every second:

`python read_fan_speed.py`

```python
import RPi.GPIO as GPIO
import time

# Pin configuration
TACH = 24       # Fan's tachometer output pin
PULSE = 2       # Noctua fans puts out two pluses per revolution
WAIT_TIME = 1   # [s] Time to wait between each refresh

# Setup GPIO
GPIO.setmode(GPIO.BCM)
GPIO.setwarnings(False)
GPIO.setup(TACH, GPIO.IN, pull_up_down=GPIO.PUD_UP) # Pull up to 3.3V

# Setup variables
t = time.time()
rpm = 0

# Caculate pulse frequency and RPM
def fell(n):
    global t
    global rpm

    dt = time.time() - t
    if dt < 0.005: return # Reject spuriously short pulses

    freq = 1 / dt
    rpm = (freq / PULSE) * 60
    t = time.time()

# Add event to detect
GPIO.add_event_detect(TACH, GPIO.FALLING, fell)

try:
    while True:
        print "%.f RPM" % rpm
        rpm = 0
        time.sleep(1)   # Detect every second

except KeyboardInterrupt: # trap a CTRL+C keyboard interrupt
    GPIO.cleanup() # resets all GPIO ports used by this function
```

# Thermal Performance

My Pi4's 40mm fan mount haven't arrived yet, I'll update this section when I get it.

# Reference

[Noctua PWM specifications white paper](https://noctua.at/media/wysiwyg/Noctua_PWM_specifications_white_paper.pdf)

[NF A4x10 5V PWM](https://noctua.at/en/nf-a4x10-5v-pwm/specification)

[Wiring Pi](http://wiringpi.com/)

[wiringPi updated to 2.52 for the Raspberry Pi 4B](http://wiringpi.com/wiringpi-updated-to-2-52-for-the-raspberry-pi-4b/)

[#138 Variable Speed Cooling Fan for Raspberry Pi using PWM and PID controller](https://www.youtube.com/watch?v=oJ32CMxliCQ)

[rxseger/tachfan.py](https://gist.github.com/rxseger/2b27e661221b6f350b859d13f256cf29)

[raspberry-gpio-python](https://sourceforge.net/p/raspberry-gpio-python/wiki/PWM/)