---
layout: userguide
title: Roadmap
---

# Introduction

We develop OPQ hardware and software technology as a series of "generations", each named by an Oahu surf spot. 

# G1: Pipeline

Our first generation system named after the famous north shore surf break [Banzai Pipeline](http://en.wikipedia.org/wiki/Banzai_Pipeline).

As of July, 2014, development of Pipeline is nearing completion and we plan to conduct a pilot deployment in August, 2014.

## OPQBox

As our first generation hardware, Pipeline's OPQBox has modest design goals:

  1. Unit price below $75
  2. Easy to install and safe to operate
  3. 16/24 bit resolution and 50+ samples per grid cycle
  4. Support for frequency and voltage measurement
  5. Onboard processing and local storage
  6. Inter-device synchronization via NTP

The following image shows a block diagram of Pipeline's OPQBox:

<img src="../images/hardware-block-diagram.png" class="center-block img-responsive">

In order to make OPQBox safe to operate, it is galvanically isolated from the power grid via a UL-listed wall plug transformer. This transformer is used both for powering the meter and monitoring the power grid. The output of the transformer's secondary windings is passed through a low-pass filter which is responsible for scaling down the voltage to the analog-to-digital converter(ADC) range, as well as filtering out frequencies above 1Mhz. Furthermore this filter protects the sensitive inputs of the ADC.

The output of the filter is digitized using an ADC on an MSP430AFE microcontroller. This microcontroller is specifically designed for power metering. As illustrated below, we combine an analog front-end, a 24bit SAR ADC capable of sampling at 4kHz, and a 16bit MCU into a single integrated circuit to reduce the bill of materials. 

<img src="../images/board3.png" class="center-block img-responsive">

For our system to scale, a significant amount of processing must be performed on the device itself. This includes power quality event detection, as well as buffering of historic data leading up to the event. Furthermore, event data needs to be uploaded to the cloud for further analysis. 

For Pipeline, we use a Raspberry Pi single board computer for the main processing unit. The MSP430AFE sends digitized samples to the Raspberry Pi via the SPI protocol. Data is compared to the expected waveform, and if the deviation is significant, an event containing the raw data is send to the cloud via a USB 802.11 adapter. 

## OPQHub

Our first generation OPQHub also has modest design goals:

  1. Receive and store events from OPQBoxes.
  2. Support basic configuration by users.
  3. Provide map-based location "coarsening" for privacy 
  4. Provide very basic alert-based analytics to inform users when events occur. 
  5. Implement the ITEC algorithm for determining event impact.

OPQHub implements location privacy by presenting users with a map overlaid with zoomable tiles allowing them to select a location with resolutions from 500 square feet (typically revealing the actual building containing the device) to 1 square mile (revealing only the neighborhood containing the device):

<img src="../images/cloud-grid.png" class="center-block img-responsive">

After configuration, OPQHub can push events to the user as specified in the user's preferences.  Beyond simple numerical data, the service can provide interpretation of the data, such as whether the frequency and/or severity of events is relatively low or high, and if high, actions that the user might want to consider.  

These actions could include: (1) contacting the utility to request service (contact information supplied in the email); (2) Communicating with neighbors to see if they have power quality problems (via the creation of public annotations of their events); or (3) advice regarding actions (such as installation of UPS line conditioning for sensitive electronics, or unplugging them during events such as thunderstorms) depending upon the frequency/severity of power quality problems.

## Communication Protocol

Our first generation system also includes the design and implementation of the [OPQ Communication Protocol](https://github.com/openpowerquality/opq/wiki/OPQ-Communication-Protocol) for data transmission to reduce the bandwidth required. 

The device and cloud service communicate using server-side events (SSE) over HTTP, which enables the device to be sent commands from the cloud even though it will be located behind a home wireless router.

# G2: Bowls

Our second generation system is named after the famous south shore surf break [Ala Moana Bowls](http://www.surf-oahu.com/surf_breaks/south_shore/bowls_kaisers.php).

As of July, 2014, we are in the early stages of requirements development and design for Bowls.
 
## OPQBox

Proposed enhancements for OPQBox include:

  1. Upgrade sampling to support IEEE PQ standards, including 256 samples per waveform and locking to 60Hz input.
  2. Internally fused UL listed dual secondary transformer.
  3. Improvements to enclosure design, including: add "Open Power Quality" to box top; provide screws to secure box; provide gasket through box for AC wire.
  4. Incorporate Li-Po battery and update firmware/software to write out waveform when power to device is cut.
  5. Measure THD
  6. Replace Raspberry PI by TI CC3200 (dev kit)
  7. Add 64K EEPROM for nonvolatile memory
  8. Use [KiCad](http://www.kicad-pcb.org/display/KICAD/KiCad+EDA+Software+Suite) for design
  
## OPQHub

Proposed enhancements for OPQHub include:

  1. Update to Bootstrap 3.x.
  2. Update to Play 2.3.
  3. API for external access to public data
  4. Remodularize to separate acquisition from analysis from storage. 
  
## Communication protocol

Proposed enhancements for the communication protocol include:

  1. Support bi-directional communication so that OPQHub can control data sent from OPQBox based upon characteristics of alert.
  
  
  


 
 








