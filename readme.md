#STRATUX/FLARM HUD (Maturaarbeit 2018)

## Introduction

This project aims to bring an affordable heads up display system into ANY cockpit.

The focus is to improve traffic awareness and to reduce the amount of time pilots reference tablets or an EFB.

_*NOTE:*_ This project relies on having a [Stratux](http://stratux.me/) build with AHRS and GPS and a FLARM antenna using this image as of 10/10 2018: https://github.com/PepperJo/stratux/releases/download/v1.4r5-flarm2/stratux-v1.4r5-flarm-588304c8f5.img.zip


### Wearabel HUD

Using the "AR Glasses" 3.5inches LCD Monitor and a Raspberry Pi 3.

![AR Glasses](media/hud_wearable.jpg)

Estimated cost is $230

- $40 for RaspberryPi 3
- $49 for AR Glasses 
- $39 for 3.5inch RPi LCD 
- $20 for Gesture control from Nevma project
- $49 for PowerBar
- $30 Cables / plastic for side pieces / srews

(I used an old Flightcom Venture type headset it has to have a screw for adjustment in order to attach the plastic connection piece on either side)

(Requires your aircraft to have a "12 Volt" cigarette power outlet)


## In Flight Controls
In order to change the windows I use the gesture control project from NEVMA
https://www.hackster.io/platisd/nevma-gesture-control-for-the-masses-9cff03
I use the proximity script since it is less touchy. So you will have to scroll through the windows. 
You will also have to make chages in "heads_up_display.py" because I can't find a plus key in Arduino

if event.key in [pygame.K_KP_PLUS, pygame.K_PLUS]:

to 

if event.key in [pygame.K_RIGHT:

Sketch:

#include <SparkFun_APDS9960.h>
#include <Keyboard.h>
// Global Variables
SparkFun_APDS9960 apds = SparkFun_APDS9960();
uint8_t proximity_data = 0;
int LED_PIN = 3; // JJD LED for if then

void setup() {

pinMode(LED_PIN,OUTPUT); //configure LED_PIN as OUTPUT 

 
 // Initialize Serial port
 Serial.begin(9600);
 Serial.println();
 Serial.println(F("------------------------------------"));
 Serial.println(F("SparkFun APDS-9960 - ProximitySensor"));
 Serial.println(F("------------------------------------"));
 
 // Initialize APDS-9960 (configure I2C and initial values)
 if ( apds.init() ) {
 Serial.println(F("APDS-9960 initialization complete"));
 } else {
 Serial.println(F("Something went wrong during APDS-9960 init!"));
 }
 
 // Adjust the Proximity sensor gain
 if ( !apds.setProximityGain(PGAIN_2X) ) {
 Serial.println(F("Something went wrong trying to set PGAIN"));
 }
 
 // Start running the APDS-9960 proximity sensor (no interrupts)
 if ( apds.enableProximitySensor(false) ) {
 Serial.println(F("Proximity sensor is now running"));
 } else {
 Serial.println(F("Something went wrong during sensor init!"));
 }
}

void loop() {
 
 // Read the proximity value
 if ( !apds.readProximity(proximity_data) ) {
 Serial.println("Error reading proximity value");
 
 } else {

 }
//Max value is 255 to make it more or less sensetive change the value in WINDOW OFF, Proximity under and WINDOW ON!! Proximity over 
 if ( proximity_data < 100 ) {
 Serial.print("WINDOW OFF, Proximity under 100: ");
 Serial.println(proximity_data); 
 Keyboard.releaseAll();
 } else {
 Serial.print("WINDOW ON!! Proximity over 100: ");
 Serial.println(proximity_data);
 Keyboard.press(KEY_RIGHT_ARROW);
 }
 delay(600);

}

You may use a number pad as input device. I used velcro to secure the number pad to my dashboard.

| Key       | Action                                                                       |
| --------- | ---------------------------------------------------------------------------- |
| Backspace | Tell the Stratux that you are in a level position. Resets the AHRS to level. |
| +         | Next view                                                                    |
| -         | Previous view                                                                |
| =         | Toggle rendering debug information                                           |
| Esc       | Send shutdown commands to both the HUD controller _*and*_ the Stratux        |
| q         | (_Full keyboard only_) Quit to the command line.                             |

## Software changes to fit better on to the glass
views/altitude.py

views/skid_and_gs.py

views/groundspeed.py


Look for "self.__text_y_pos__ = (text_half_height << 2) + \
            center_y - text_half_height"

### Software changes on raspberry
/boot/config.txt

disable_overscan=1

overscan_left=200

overscan_right=400

Overscan_top=400

Overscan_bottom=-100

## Parts List HUD only

- [Rottay Mechanical Keypad](https://www.amazon.com/Number-Rottay-Mechanical-Numeric-backlit/dp/B076FTSY6J/ref=sr_1_3?ie=UTF8&qid=1529215627&sr=8-3&keywords=mechanical+keypad)
- [NEVMA New Mini ATMEGA32U4 Module Board Compatible For Arduino SS Micro ATMEGA32U4](https://www.ebay.de/itm/New-Mini-ATMEGA32U4-Module-Board-Compatible-For-Arduino-SS-Micro-ATMEGA32U4/272546371430?ssPageName=STRK%3AMEBIDX%3AIT&_trksid=p2060353.m2749.l2649)
- [NEVMA GY-9960LLC APDS-9960 RGB and Gesture Sensor Module I2C Breakout for Arduino](https://www.amazon.com/Number-Rottay-Mechanical-Numeric-backlit/dp/B076FTSY6J/ref=sr_1_3?ie=UTF8&qid=1529215627&sr=8-3&keywords=mechanical+keypad)
- [NEVMA PCB](https://www.pcbway.com/project/shareproject/Nevma__Gesture_control_for_the_masses.html)
- [Adafruit DIY HDMI Cable Parts - Straight HDMI Plug Adapter AF3548](https://www.ebay.de/itm/Adafruit-DIY-HDMI-Cable-Parts-Straight-HDMI-Plug-Adapter-AF3548/273042335737?ssPageName=STRK%3AMEBIDX%3AIT&_trksid=p2060353.m2749.l2649)
- [Adafruit DIY HDMI Cable Parts - Straight HDMI Socket Adapter AF3551](https://www.ebay.de/itm/Adafruit-DIY-HDMI-Cable-Parts-Straight-HDMI-Socket-Adapter-AF3551/273026213365?ssPageName=STRK%3AMEBIDX%3AIT&_trksid=p2060353.m2749.l2649)
- [Adafruit DIY HDMI Cable Parts - 20 cm HDMI Ribbon Cable AF3561](https://www.ebay.de/itm/Adafruit-DIY-HDMI-Cable-Parts-20-cm-HDMI-Ribbon-Cable-AF3561/282810093975?ssPageName=STRK%3AMEBIDX%3AIT&_trksid=p2060353.m2749.l2649)
- [delock kabel usb 2.0 micro-b buchse zum einbau > usb 2.0 micro-b stecker 25 cm](https://www.ebay.de/itm/delock-kabel-usb-2-0-micro-b-buchse-zum-einbau-usb-2-0-micro-b-stecker-25-cm/162698540334?ssPageName=STRK%3AMEBIDX%3AIT&_trksid=p2060353.m2749.l2649)
- [Virtual Reality VR AR Brille Gläser Arbox Holographischer Effekt 3D](https://www.ebay.de/itm/Virtual-Reality-VR-AR-Brille-Gläser-Arbox-Holographischer-Effekt-3D/382299251509?ssPageName=STRK%3AMEBIDX%3AIT&_trksid=p2060353.m2749.l2649)
- [Anker PowerCore 20100mAh Externer Akku (Powerbank) mit 2 Ports und 4,8-A-Output](https://www.ebay.de/itm/Anker-PowerCore-20100mAh-Externer-Akku-Powerbank-mit-2-Ports-und-4-8-A-Output/253034763977?ssPageName=STRK%3AMEBIDX%3AIT&_trksid=p2060353.m2749.l2649)

### HUD Build

## Install instructions

## License

This project is covered by the GPL v3 license.

Please see [LICENSE](LICENSE)
