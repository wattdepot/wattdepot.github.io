---
layout: userguide
title: Quick Start
---

# Quick Start

## Obtain your device

To obtain an OPQ device, [contact us](contact.html).

## Register your device
To register your device, visit [here]() and complete the following steps:

## Installation
1. Choose a location near a wireless access point
2. Place your OPQ device face-up on a flat surface
3. Plug the device directly into a wall outlet (do not plug into an extension cord or surge protector)

## Connecting to WiFi
### Screencast
<iframe width="640" height="360" src="//www.youtube.com/embed/rSb7kvgfjk4?rel=0" frameborder="0" allowfullscreen></iframe>

### Prerequisites
* usb flash drive  
* `ssid` - The name of the wireless network you wish to connect to
* `key` - The password or key required by the network you wish to connect to
* `security` - The type of security the wireless network is using. Available options include:
  * `wep`
  * `wpa` (this includes wpa2)
  * `none`

### Steps to Connect  
1. Create a text file called `wifi-config.txt`
2. Add wireless configuration information to `wifi-config.txt`
3. Copy `wifi-config.txt` to a usb drive
4. Power on the OPQ device
5. Wait for 2 minutes so device is fully booted
5. Insert usb drive into available usb slot on OPQ device
6. Wait for 2 minutes while the device configures the wireless network
7. Remove the usb drive from the OPQ device
8. Restart the OPQ device (unplug and plug back in)

### Example wifi-config.txt
    [wifi_config]
    ssid = name_of_your_network
    key = password_for_your_network
    security = network_security_type

Looking at the configuration file line by line we see:  
##### Line 1 - `[wifi_config]`
* This line never changes. 
* It tells the OPQ device that this file is meant to configure the wireless network.

##### Line 2 - `ssid = name_of_your_network`
* This stores the name of the wireless network you wish to connect to
* Replace `name_of_your_network` with the name of the wireless network you wish to connect to

##### Line 3 - `key = password_for_your_network`
* This stores the key or password of the wireless network you wish to connect to
* Replace `password_for_your_network` with the correct password/key for the network you wish to connect to

##### Line 4 - `security = wpa`
* This stores the type of security the wireless network is using
* Replace `network_security_type` with one of `wep`, `wpa`, or `none`
* Note that `security = network_security_type` also covers `wpa2`

#### Troubleshooting
* The wifi configuration process creates a file on your usb drive called `wifi-config.log`
* This file lists all steps taken while setting up the wireless interface and should also include all error messages

