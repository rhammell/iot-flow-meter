# iot-flow-meter
This repository contains the code and tutorial for buiding an IoT enabled flow meter. This project was submitted to Hackster.io as part of the [Cellular IoT Challenge](https://www.hackster.io/contests/soracomcontest#category-331), where it was selected as the grand prize winner. 

See the original Hackster.io project page [here](https://www.hackster.io/rhammell/river-monitoring-with-an-iot-flow-meter-9af852).

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
    <img width="400" src="https://img.youtube.com/vi/L7kpcUOq6Nk/0.jpg">
  </a>
  <br>
  <span><i>IoT Flow Meter Overview Video</i></span>
</p>

The IoT flow meter consists of a sensor and electronics, connected to a PVC pipe support structure, that can be deployed to a river to collect flow rate data.

A flow meter sensor is connected to the structure's vertical pipe at an adjustable height where it is held submerged underwater. The electronics are contained in an upper housing at the top of the structure outside of the water.

<p align="center">
  <img width="400" src="img/how_it_works_1.jpg">
  <br>
  <span><i>IoT flow meter structure and components</i></span>
</p>
<p align="center">
  <img width="400" src="img/how_it_works_2.jpg">
  <br>
  <span><i>Flow meter sensor connected to structure</i></span>
</p>
<p align="center">
  <img width="400" src="img/how_it_works_3.jpg">
  <br>
  <span><i>IoT flow meter deployed to a local river</i></span>
</p>

The system utilizes a YF-S201 liquid flow meter sensor, which contains a magnetic pinwheel that is spun by incoming water.

As more water enters the sensor the wheel spins faster, and this rate of spin (Hz) can be used to calculate how many liters of water flow through the sensor per minute (L/min), called the flow rate.

<p align="center">
  <img width="400" src="img/how_it_works_4.jpg">
  <br>
  <span><i>YF-S201 liquid flow meter sensor</i></span>
</p>
<p align="center">
  <img width="400" src="img/how_it_works_5.jpg">
  <br>
  <span><i>Magnetic pinwheel housed within the sensor</i></span>
</p>
<p align="center">
  <img width="400" src="img/how_it_works_6.jpg">
  <br>
  <span><i>3D printed bracket and funnel attached to sensor</i></span>
</p>

Note: This flow rate only tells the liters of water passing through the sensor itself, not the entire river. The sensor's flow rate is strongly correlated to the river's actual flow rate, but is not measuring it directly. The IoT flow meter provides only the sensor's flow rate and uses this as an approximation for the entire river.

A 3D printed bracket is used to connect the sensor to the PVC pipe structure and a 3D printed funnel guides river water into the mouth of the sensor.

The electronics of the system include a Raspberry Pi, 3G USB modem with a Soracom Global SIM, and external battery, which are contained in a 3D printed housing. The sensor is connected to the Pi by water-sealed jumper wires that run the length of the vertical pipe into the housing.

<p align="center">
  <img width="400" src="img/how_it_works_7.jpg">
  <br>
  <span><i>Raspberry Pi, 3G USB modem with Soracom Global SIM, and external battery</i></span>
</p>
<p align="center">
  <img width="400" src="img/how_it_works_8.jpg">
  <br>
  <span><i>Electronics in 3D printed housing</i></span>
</p>

The Pi runs a Python script where ever minute a flow rate measurement is collected from the sensor and, using the cellular connectivity provided by the modem and SIM card, pushed to the cloud.

The script makes an HTTP POST request to Soracom's Unified Endpoint which forwards the data along to two services: Soracom Harvest and Soracom Funnel.

<p align="center">
  <img width="400" src="img/how_it_works_9.jpg">
  <br>
  <span><i>Overview of information flow</i></span>
</p>

Soracom Harvest allows for easy real-time visualization of the incoming data. The console displays a live graph showing updated data values as they stream in. Users can monitor how the data changes over a selected window of time.

<p align="center">
  <img width="400" src="img/how_it_works_10.jpg">
  <br>
  <span><i>Soracom Harvest console</i></span>
</p>
<p align="center">
  <img width="400" src="img/how_it_works_11.jpg">
  <br>
  <span><i>AWS Cloudwatch console</i></span>
</p>
<p align="center">
  <img width="400" src="img/how_it_works_12.jpg">
  <br>
  <span><i>Email alarm notification</i></span>
</p>



Soracom Funnel takes the data and forwards it further on it by sending it to an AWS IoT endpoint, where an AWS IoT rule is then used to push the data to AWS CloudWatch.

A CloudWatch alarm is configured to monitor when the flow rate surpasses a critical value and automatically sends out an email notification to any subscribed users letting them know that the alarm has been triggered. This feature allows users to stay aware of important changes in the river's flow rate without having to monitor it live.

# Architecture and Operational Excellence

The IoT flow meter is meant to be a solution to a problem that traditionally requires more hands-on, expensive equipment to collect data. While this prototype has shown that it can be fielded and provide results, there are further things to consider in moving to a production version.

Cost: The total cost for the system is ~$120, with the largest expenses being the Raspberry Pi ($35) and the 3G Modem ($60). This price could likely brought down with bulk supplies. This total cost is reasonable when considering the man-hours saved by using this IoT device instead of paying surveyors/engineers to collect measurements manually.

Scalability: The system could be scaled up to many more units depending on demand. Building a mass amount of units would become easier by transitioning to an pre-fabricated circuit that contained all necessary electronics, and developing a better pipe structure that was pre-assembled.

If scaled up to 1000's of units running worldwide, Soracom and AWS IoT could still remain the backbone of how data is collected and distributed. Individual accounts and access may need to be set up for each user, and AWS IoT can accomplish this though IAM user groups, which grant permissions to view/edit data to only specific users.

Reliability: Consistent and accurate measurements that reflect the actual flow rate is important to this problem. More testing needs to be done to better understand the different effects that may influence the sensor's readings like depth and placement in the river.

The system relies on having a reliable cellular network as well. Currently, if an attempted request to Soracom fails due to a network timeout, that data is lost. Further updates to the tool would include backing up measurements so they could be saved and pushed to the cloud when the connection is reestablished.

Security: The IoT flow meter is designed to be left unattended, which puts its physical security at risk. Currently, there is no way to determine what happens to the system if it goes offline or is missing, but a future version may include a GPS module that includes the system's current coordinates with each data push. This would help track the location of the system at any time.

# Build Instructions

Below are instructions on how to prototype, build, and receive notifications from the IoT flow meter.

Begin this project by first building a prototype circuit to test the liquid flow meter's functionality, enabling internet connectivity, and pushing measurements to the cloud. Next build the pipe structure and attach the components so its ready to be deployed. Finally, setup AWS CloudWatch and subscribe to email alerts when a critical flow rate occurs.

These instructions assume you have access to the Rasberry Pi's terminal, either through SSH or a keyboard/mouse/monitor setup. The Pi must also have an internet connection either through WiFi or ethernet for the initial setup.

# Prototype Circuit

## Step 1: Setup the Test Circuit

Using the male-to-female jumper wires, make the following connections from the YF-S201 liquid flow meter's wires to the Raspberry Pi GPIO Pins:

- Red (DC Power) to Pin 1 (3.3V)
- Black (Ground) to Pin 6 (Ground)
- Yellow (Output) to Pin 7 (GPIO4)

<p align="center">
  <img width="400" src="img/build_instructions_1.jpg">
  <br>
  <span><i>Schematic of circut setup showing pin connections</i></span>
</p>
<p align="center">
  <img width="400" src="img/build_instructions_2.jpg">
  <br>
  <span><i>Circuit setup</i></span>
</p>

## Setp 2: Test Sensor Measurements

A Python script can be ran to take readings from the sensor that is now connected. Open the Raspberry Pi terminal and enter the following commands to create and navigate to a flowmeter directory on the Pi's desktop where the script will be stored:

    $ cd /home/pi/Desktop
    $ mkdir flowmeter
    $ cd flowmeter

Using Python's IDLE, or another text editor, copy the following Python code and save it as flowmeter.py in the folder that was just created:

    import json
    import time
    from datetime import datetime
    import RPi.GPIO as GPIO
      
    class FlowMeter():
        ''' Class representing the flow meter sensor which handles input pulses
            and calculates current flow rate (L/min) measurement
        '''
        
        def __init__(self):
            self.flow_rate = 0.0
            self.last_time = datetime.now()
      
        def pulseCallback(self, p):
            ''' Callback that is executed with each pulse 
                received from the sensor 
            '''
           
            # Calculate the time difference since last pulse recieved
            current_time = datetime.now()
            diff = (current_time - self.last_time).total_seconds()
           
            # Calculate current flow rate
            hertz = 1. / diff
            self.flow_rate = hertz / 7.5
           
            # Reset time of last pulse
            self.last_time = current_time
        
        def getFlowRate(self):
            ''' Return the current flow rate measurement. 
                If a pulse has not been received in more than one second, 
                assume that flow has stopped and set flow rate to 0.0
            '''
           
            if (datetime.now() - self.last_time).total_seconds() > 1:
                self.flow_rate = 0.0
            
            return self.flow_rate
      
    def main():
        ''' Main function for repeatedly collecting flow rate measurements
            and sending them to the SORACOM API
        '''
       
        # Configure GPIO pins
        INPUT_PIN = 7
        GPIO.setmode(GPIO.BOARD)
        GPIO.setup(INPUT_PIN, GPIO.IN, pull_up_down=GPIO.PUD_UP)
       
        # Init FlowMeter instance and pulse callback
        flow_meter = FlowMeter()
        GPIO.add_event_detect(INPUT_PIN,
                              GPIO.RISING,
                              callback=flow_meter.pulseCallback,
                              bouncetime=20)
       
        # Begin infinite loop
        while True:
      
            # Get current timestamp and flow meter reading
            timestamp = str(datetime.now())
            flow_rate = flow_meter.getFlowRate()
            print('Timestamp: %s' % timestamp)
            print('Flow rate: %f' % flow_rate)
           
            # Delay
            time.sleep(5)
      
    if __name__ == '__main__':
       main()

This script defines a FlowMeter() object class that is used to track pulses received from the sensor as its pinwheel spins and calculate the current flow rate.

Each new pulse triggers the pulseCallback() method, where the rate of pulses per second (Hz) is divided by a constant to calculate the flow rate in liters per minute (L/min). This conversion is defined in the sensor's datasheet. The getFlowRate() method returns the current flow rate.

The main() function is called when the script is launched and defines the GPIO pins used by the sensor, and initiates an instance of the FlowMeter() class.

An infinite loop is entered where on each pass a flow rate measurement and its timestamp collected and printed out to the console. The time.sleep(5) statement, which controls the delay between loops, is set to 5 seconds. The loop will run continuously until canceled by the user.

Before deploying the final IoT flow meter to a river, edit this script to adjust this sleep time to the frequency you want to collect river measurements. A value of 60 will take measurements at one minute intervals.

Launch the Python script with the following command:

    $ python3 flowmeter.py

Flow rate measurements and timestamps are printed out to the console every 5 seconds:

    Timestamp: 2019-05-20 22:00:41.267727
    Flow rate: 0.000000
    Timestamp: 2019-05-20 22:00:46.278729
    Flow rate: 0.000000
    Timestamp: 2019-05-20 22:00:51.283864
    Flow rate: 0.000000

The flow rate should be zero while the sensor's pinwheel is stopped. Try blowing into the sensor to spin the wheel and watch how the flow rate value changes.

Press Ctrl+C to stop the script.

