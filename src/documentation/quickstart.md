---
layout: userguide
title: Quick Start
---

# Obtain an OPQBox

The first step in getting started is to obtain an OPQBox. 

We are currently manufacturing OPQBoxes on an as-needed basis.  To obtain cost and availability information, please [contact us](contact.html).

# OPQBox configuration

Once you have obtained your OPQBox, you begin the configuration process by providing it with information about your WiFi network and the location of your chosen OPQHub using the provided USB stick.   Once your OPQBox is communicating with the OPQHub, you will use the OPQHub to complete the configuration process.

## Edit config.txt

(Instructions on how to edit the config.txt file)


### Example wifi-config.txt
    [wifi_config]
    ssid = name_of_your_network
    key = password_for_your_network
    security = network_security_type

Looking at the configuration file line by line we see:  

##### Line 1 - `[wifi_config]`
* This line never changes. 
* It tells the OPQBox that this file is meant to configure the wireless network.

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

## Power up OPQBox

(Explanation of what should happen after device is plugged in. Do LEDs indicate anything?  If not, they should.  How
can the user know that the device has successfully connected to WiFi?)

# OPQHub accounts

## Device registration

(Explain how to login to the OPQHub and verify that the device is communicating successfully.)

## Location configuration

(Explain how to specify the current location of the device, which will be associated with all events until changed by the user.)

# Configure analytics

(Explain how to set thresholds, alerts, etc.)

