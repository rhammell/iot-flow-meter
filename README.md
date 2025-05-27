# iot-flow-meter
This repository contains the project files used to build an IoT enabled flow meter. This project was submitted to Hackster.io as part of the [Cellular IoT Challenge](https://www.hackster.io/contests/soracomcontest#category-331), where it was selected as the grand prize winner. 

Read the [project page](https://www.hackster.io/rhammell/river-monitoring-with-an-iot-flow-meter-9af852) on Hackster.io for a complete project description and build tutorial. 

# Overview

<p align="center">
  <img width="600" src="img/overview.jpg">
  <br>
  <span><i>IoT flow meter deployed to a local river</i></span>
</p>

This project demonstrates how to build an internet connected flow meter that continuously measures a river's flow rate and makes real-time data available to users online.

The system can be deployed to remote rivers with a one-time setup, and can be left to run on its own, automatically collecting data without the need for users to be present. Multiple meters can be placed throughout a river to collect many points of data.

Measurement data is pushed to the cloud through the system's cellular network connectivity, enabled by a Soracom Global SIM card, and can be viewed online through Soracom Harvest. AWS IoT and CloudWatch services are used to send email notifications to users when the river's flow rate reaches a critical level.

# Background

<p align="center">
  <img width="600" src="img/background.gif">
</p>

The flow rate of a river measures the volume of water that passes through a specific point over time. This rate rises and falls as water from rainfall flows in and out of the river system.

Measuring a river's flow rate can yield valuable information for a variety purposes:

- Hydrology: Long term trends in flow rate give insights into the watershed system that feeds the river and how it changes over time.
- Ecology: Speed of river water determines if it can support different species of plants or animals.
- Flood Warning: Spikes in the flow rate can give early warning for incoming flooding and can be correlated to rainfall reports.
- Recreation Safety: Real-time measurements can warn canoers/kayakers if it is safe to travel along the river.

Currently, measuring the flow rate of remote river areas is a very hands-on process. Surveyors need to hike out to the river of interest carrying their measuring equipment, set it up, take a measurement, disassemble it, and hike back.

Because of the effort required to collect a measurement, it is only done periodically for certain rivers. Without continuous monitoring many of the insights that flow rate provides are lost.

This project aims to solve this problem by making flow rate data easier to collect and more readily available to users who need it.

# How it Works

<p align="center">
  <a target="_blank" href="https://www.youtube.com/watch?v=L7kpcUOq6Nk">
    <img width="600" src="https://img.youtube.com/vi/L7kpcUOq6Nk/0.jpg">
  </a>
  <br>
  <span><i>IoT Flow Meter Overview Video</i></span>
</p>

The IoT flow meter consists of a sensor and electronics, connected to a PVC pipe support structure, that can be deployed to a river to collect flow rate data.

A flow meter sensor is connected to the structure's vertical pipe at an adjustable height where it is held submerged underwater. The electronics are contained in an upper housing at the top of the structure outside of the water.

<p align="center">
  <img width="600" src="img/how_it_works_1.jpg">
  <br>
  <span><i>IoT flow meter structure and components</i></span>
</p>
<p align="center">
  <img width="600" src="img/how_it_works_2.jpg">
  <br>
  <span><i>Flow meter sensor connected to structure</i></span>
</p>
<p align="center">
  <img width="600" src="img/how_it_works_3.jpg">
  <br>
  <span><i>IoT flow meter deployed to a local river</i></span>
</p>

The system utilizes a YF-S201 liquid flow meter sensor, which contains a magnetic pinwheel that is spun by incoming water.

As more water enters the sensor the wheel spins faster, and this rate of spin (Hz) can be used to calculate how many liters of water flow through the sensor per minute (L/min), called the flow rate.

<p align="center">
  <img width="600" src="img/how_it_works_4.jpg">
  <br>
  <span><i>YF-S201 liquid flow meter sensor</i></span>
</p>
<p align="center">
  <img width="600" src="img/how_it_works_5.jpg">
  <br>
  <span><i>Magnetic pinwheel housed within the sensor</i></span>
</p>
<p align="center">
  <img width="600" src="img/how_it_works_6.jpg">
  <br>
  <span><i>3D printed bracket and funnel attached to sensor</i></span>
</p>

Note: This flow rate only tells the liters of water passing through the sensor itself, not the entire river. The sensor's flow rate is strongly correlated to the river's actual flow rate, but is not measuring it directly. The IoT flow meter provides only the sensor's flow rate and uses this as an approximation for the entire river.

A 3D printed bracket is used to connect the sensor to the PVC pipe structure and a 3D printed funnel guides river water into the mouth of the sensor.

The electronics of the system include a Raspberry Pi, 3G USB modem with a Soracom Global SIM, and external battery, which are contained in a 3D printed housing. The sensor is connected to the Pi by water-sealed jumper wires that run the length of the vertical pipe into the housing.

<p align="center">
  <img width="600" src="img/how_it_works_7.jpg">
  <br>
  <span><i>Raspberry Pi, 3G USB modem with Soracom Global SIM, and external battery</i></span>
</p>
<p align="center">
  <img width="600" src="img/how_it_works_8.jpg">
  <br>
  <span><i>Electronics in 3D printed housing</i></span>
</p>

The Pi runs a Python script where ever minute a flow rate measurement is collected from the sensor and, using the cellular connectivity provided by the modem and SIM card, pushed to the cloud.

The script makes an HTTP POST request to Soracom's Unified Endpoint which forwards the data along to two services: Soracom Harvest and Soracom Funnel.

<p align="center">
  <img width="600" src="img/how_it_works_9.jpg">
  <br>
  <span><i>Overview of information flow</i></span>
</p>

Soracom Harvest allows for easy real-time visualization of the incoming data. The console displays a live graph showing updated data values as they stream in. Users can monitor how the data changes over a selected window of time.

<p align="center">
  <img width="600" src="img/how_it_works_10.jpg">
  <br>
  <span><i>Soracom Harvest console</i></span>
</p>
<p align="center">
  <img width="600" src="img/how_it_works_11.jpg">
  <br>
  <span><i>AWS Cloudwatch console</i></span>
</p>
<p align="center">
  <img width="600" src="img/how_it_works_12.jpg">
  <br>
  <span><i>Email alarm notification</i></span>
</p>

Soracom Funnel takes the data and forwards it further on it by sending it to an AWS IoT endpoint, where an AWS IoT rule is then used to push the data to AWS CloudWatch.

A CloudWatch alarm is configured to monitor when the flow rate surpasses a critical value and automatically sends out an email notification to any subscribed users letting them know that the alarm has been triggered. This feature allows users to stay aware of important changes in the river's flow rate without having to monitor it live.
