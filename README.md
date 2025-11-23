# Raspi-Tips
Some HW / SW - Tips for Raspberry Pi


## On/Off - Button

Since Raspi 4 (3?) Raspi supports being switched on/off with momentary switches, buttons.   
Switching On ( to my knowledge ) always has to be done with grounding GPIO 3, Switching off can be done with different GPIOs, 
but if you only want ONE button for both, then you have to use GPIO 3.   


### Hardware

Simple connection between GPIO 3 and GND to a button

    1        o  o      2
    3        o  o      4
    5 GPIO3 -o  o- GND 6
    7        o  o      8

See also e.g. [here for the pins](https://pinout.xyz/pinout/wiringpi)

### Software

* **ON**-Switch : nothing needed
* **OFF**-Switch : edit `` /boot/firmware/config.txt`` : put ``dtoverlay=gpio-shutdown`` at the end

Done...

* Switching ON : Just a short button-press
* Switching OFF : Short button-press brings a menu, if you have connected a mouse/keyboard you can select, otherwise just press 2 secs

See also: https://www.circuitbasics.com/using-a-switch-to-startup-and-shutdown-the-raspberry-pi/


## Checking/Reading Switch from Bash

Used for e.g. in our Repair-Cafe-Setup to distinguish on boot-up, if we want to start automatically the RepairCafe-Server and Dashboard or do something else...    
( The startup in $HOME/.config/autostart/repairview.desktop starts a bash-script, which then detects the state of the switch and then starts the browser or something else...)

### Hardware

Just connect a switch to GND and to any general IO, I have used e.g. GPIO 17 on Pin 11, which is beside Pin 9 which GND

Simple connection between GPIO 3 and GND to a button

     1         o  o       2
     3         o  o       4
     5         o  o       6
     7         o  o       8
     9 GND    -o  o      10
    11 GPIO17 -o  o      12

See also e.g. [here for the pins](https://pinout.xyz/pinout/wiringpi)

### Software

#### Precondition

* Install gpiod ```sudo apt install gpiod -y```

This installs the following programs:

* gpiodetect
* gpiofind
* gpioget
* gpioinfo
* gpiomon
* gpioset

To be able to call those programs w/o sudo, you should put(add) yourself into the GPIO-group:   

```sudo usermod -aG gpio "$USER"```  

#### Usage

    #! /usr/bin/bash
    # File: switch.sh

    pin_switch=17  # Switch connected on GPIO 17
    gpiochip="gpiochip0"

    pinctrl set ${pin_button} pu      # Set to input, internally connected to + Voltage  (PU=pull up)
    switchstate=$(gpioget ${gpiochip} ${pin_switch})
    if [ ${switchstate} == "1" ]; then
        echo "Switch not closed"
    else
        echo "Switch closed"
    endif
    
**Explanation :**   

GPIO is configured for Pull-Up.   

* When the switch is NOT closed ==> you will read a "1" from the GPIO,
* When the Switch is closed  ==> it will connect the GPIO to GND and you will read a "0"


See also : (in German) https://raspithek.de/tutorial/bash/raspberry-pi-gpio-steuerung-in-der-bash/ 


