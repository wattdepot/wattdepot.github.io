---
layout: container
title: Technology Overview
---

# Technology overview

We develop OPQ hardware and software technology as a series of "generations", each informally given the name of an Oahu surf spot by one of the developers. 

# Generation 1: Pipeline

Our first generation system named after the infamous North Shore break [Banzai Pipeline](http://en.wikipedia.org/wiki/Banzai_Pipeline).

## OPQBox design overview

As our first generation technology, Pipeline's OPQBox has modest design goals:

  1. Unit price below $75
  2. Easy to install and safe to operate
  3. 16/24 bit resolution and 50+ samples per grid cycle
  4. Support for frequency, voltage, and THD measurement
  5. Onboard processing and local storage
  6. Inter-device synchronization via NTP

The following image shows a block diagram of OPQBox:

<img src="../images/hardware-block-diagram.png" class="center-block img-responsive">

In order to make our device safe to operate, it is galvanically isolated from the power grid via a UL-listed wall plug transformer. This transformer is used both for powering the meter and monitoring the power grid. The output of the transformer's secondary windings is passed through a low-pass filter which is responsible for scaling down the voltage to the analog-to-digital converter(ADC) range, as well as filtering out frequencies above 1Mhz. Furthermore this filter protects the sensitive inputs of the ADC.

The output of the filter is digitized using an ADC on an MSP430AFE microcontroller. This microcontroller is specifically designed for power metering. As illustrated below, we combine an analog front-end, a 24bit SAR ADC capable of sampling at 4kHz, and a 16bit MCU into a single integrated circuit to reduce the bill of materials. 

<img src="../images/board3.png" class="center-block img-responsive">

For our sensor network to scale, a significant amount of processing must be performed on the device itself. This includes power quality event detection, as well as buffering of historic data leading up to the event. Furthermore, event data needs to be uploaded to the cloud for further analysis. 

For this generation, we use a Raspberry Pi single board computer for the main processing unit. The MSP430AFE sends digitized samples to the Raspberry Pi via the SPI protocol. Data is compared to the expected waveform, and if the deviation is significant, an event containing the raw data is send to the cloud via a USB 802.11 adapter. 

We have designed a custom protocol for data transmission to reduce the bandwidth required \cite{opq-protocol}. The device and cloud service communicate using server-side events (SSE) over HTTP, which enables the device to be sent commands from the cloud even though it will be located behind a home wireless router.








