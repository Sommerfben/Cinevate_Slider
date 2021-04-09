# Cinevate Slider
A short reverse engineering of the Cinevate atlas 10 camera slider controller protocol. 

# System Overview
The Cinvate Atlas 10 camera slider is a motion control system developed in a partnership between Cinevate and Ditogear. This was sold as an add on to the Atlas slider and looked like this when mounted to the system

![image](https://user-images.githubusercontent.com/32988623/114131324-4d678880-98b7-11eb-861a-d920e9adc9d5.png)

This is what the controller looks like

![image](https://user-images.githubusercontent.com/32988623/114130695-fa410600-98b5-11eb-9aa9-bdd4e55e1dee.png)

When the system is set up, the hardware configuration looks like this. Each hardware block is connected to the other via a custom protocol carried over standard Cat5 cable. This document works to reverse engineer the connection between the remote and the communications hub in the hopes of building a custom controller for the system in the future. 

![image](https://user-images.githubusercontent.com/32988623/114152390-18692f00-98d3-11eb-89b6-e1433f259ecf.png)


The primary goal of this reverse engineering is to provide an opensource control interface to allow for usage with DIY or other motion control systems, i.e. a pan tilt head or a turn table. 

# The Protocol
Although I must admit I was hoping for a bit more of a challenge, the control protocol is remarkably simple. It functions on the same principal as the SparkFun easy stepper, which is a two-pin control scheme. The first pin is simply logic high or logic low and controls the direction of the movement, while the other pin is a pulse train where every positive edge makes the motor take a step. For example, here is a scope capture of the system moving left at full speed. The spikes on the blue signal are just cross talk from my poor probing technique, it is normally a very solid DC logic level. 

![image](https://user-images.githubusercontent.com/32988623/114132223-cf0be600-98b8-11eb-9950-c9ee19b66443.png)

Now that we know what the signals look like, we can take some additional measurements and characterize the various options and modes of the controller. The details of the specification are as follows


| Parameter  | Value |
| ------------- | ------------- |
| Duty cycle  | 50%  |
| Maximum Frequency  | 23.8kHz (I suspect this could be pushed higher for most setups as the slider is rated for ~40lbs of camera so its probably speed limited for max load)  |
| Minimum Frequency  | 261Hz (I assume this can be as low as you want)  |
| Logic High  | 3.3V  |
| Logic Low  | 0V  |

# The Hardware
This is the pinout of the RJ45/Cat5 port on the controller

![image](https://user-images.githubusercontent.com/32988623/114148486-a989d700-98ce-11eb-99c5-125c60e0401d.png)

I cut a spare Cat5 cable I had laying around and probed out the connections. I think Cat5 is supposed to have a standardized color order for the inner conductors but I'm not 100% sure how well that is usually implemented. 

![IMG_0589-(2)-annotated](https://user-images.githubusercontent.com/32988623/114149199-80b61180-98cf-11eb-8d78-263a85e275f3.jpg)

#  Conclusion
We now know how to drive the slider at any speed in either direction. Since the communication is so simple, we can use an Arduino to generate the control pulses and interface with other motion control hardware. I tested this with a NodeMCU since the logic levels are only 3.3V which is lower than the regular controller but still high enough to trigger each step. This would probably work with a regular Arduino with their 5V logic levels, but the wave form I measured on the scope only had 4V peak to peak so there is a ***very*** small chance that the Arduino could damage it.  
If you're interested in seeing some more scope captures of the controller running in various modes, I've uploaded a PDF including all the scope captures I took in the captures folder. 
I've also uploaded high res photos of the controller PCB in the Photos folder.

There remains some questions such as the min/max operating voltage, min/max current draw, what that green pins function is and why Dito made some of the questionable design decisions they did but the system has been reverse engineered to the point that it can now be fully integrated into another motion control system and as such I'm happy with the state of affairs. 

I will be building a pan/tilt head and controller for the system in the future and will come back and update this repo with links when I create the new repo for that
