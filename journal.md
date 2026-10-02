# Research + List of Stuff
## Oct 1st, 2026

First journal!

Before starting any engineering project, you must first research, so that's what I did for like 30 minutes.
The goal of this project is to make a flight controller for a TVC capable rocket so here's some of the things
I found! I'll prob note some things down about each one...

First is this one: https://www.apogeerockets.com/Electronics-Payloads/Dual-Deployment/EasyMega

Then this one https://bps.space/pages/avionics

(specifically the SIGNAL R2)

and then AVA! (which i saw while scrolling youtube shorts)

https://www.youtube.com/watch?v=qaWvCy2DRSA

So after looking over these here are the things i've abosrbed

- The rocket needs to know where it is, but depending on the stage of flight the sensors used to know this defers. So the types of sensors i've got are Accelerometers which measure accelereration, gyroscopes which measure rotation and angular velocity, imu's, which I'm used to from robotics which combine the accelerometers and gyroscopes, and barometers which measure the pressure changes which can help measure altitude, and then a GPS reciever to track the location of it, this is apparently useful for rescuing rockets.
- The rocket needs to process these sensors using some sort of processor, chip or microcontroller. The one's i've seen are from companies like NXP(like the ava!) or STM. Higher demanding algorithms and programs like the one I'm planning to implement,  the extended kalman filter used for sensor fusion demand more powerful processors
- The rocket needs to be able to controll it's subsystems, things like flaps, the servo's for the TVC controll (commonly called a gymbal), we need servo's to snap out the motors to replace them, and also a servo to act as a physical latch for the parachute system.
- The rocket needs to be able to load code onto the flight controller, for this we'd use a usb'c cable, maybe one for every microcontroller or we could use a parent-child node architecture, i'm thinking the main chip can channel the instructions to the two smaller ones for ease of use, obviously we would have serial pins to debug but that could be an option
- The computer has to be **easy to debug** and have **multiple failoverss**

So here's an architecture that I'm thinking of:

We have three chips
- Main Processing Unit, this one will control the main functions of the board
- Communication/Logging Processing Unit, which will control the sd card functions, logging, telemtry and communication to ground control
- Failover Proccesing Uint: this will run the kalman filtering, and in the precense of a fialure, will take over, this is probably possible with some sort of state sharing...

All of these will be STM32H7's, which is overkill for this so i'll prob change it later

NOTE i changed this
main is goint to be STM32H723VGT6's except the comms and log one which will be STM32F411CEU6 and the failover which will be the STM32G031 and have it's own BMP581

The purpose of the failsafe is to detect apogee and fire the chute.

IMU's, like the AVA i'll pack a triple IMU structure 
I think i'll go with 2 LSM6DSOX. This is from STM so i expect a nice software stack and according to some research it has low gyro drigt, and has super fast comm's with (i^2c??), hmm i thought this was a slow protocol. I'll also add a BMI088, because I do expect this rocket to vibrate a lot, so this is a good failsafe. I think I'll go with the inputs fromt he 2 LSM's and then when we detect vibrations or something switch over, and we can still have redunduncy from one of the chips.

NOTE: I am now going with the same chip but only one and one ICM-45686 and the other BMI

For the Barometer, I'll go with two of them, in my experience with robotics these tend to not fuck up as much as the IMU's so triple isn't required. After doing some research it seems bosch has the BMP581 which is good cause it's compact, cheap and has good integrations! but the cons are that it's pretty sensitive to high vibration and it can sometimes glitch. and for the other one i'll go with the insane TDK invensense ICP-20100. which is a "powerhouse" and has a on chip filtering thingy that can clean up data before it even hits our filter

NOW let's do the storage stuff!

So obviouslty microSD is super duper slow, (hundreds of ms's at some times!) so to bypass this we'll use some flash memory, W25Q128 which gives us like 16mb of memory. 50kb/s is pretty good! Obviously we can't store multiple flights in the flash so remember to keep that in mind. For the other microSD we'll use it on SDMMC.

Hello World?? <- COMMUNICATION!!!

For telemtry we'll use LORA ona Ebyte E22-900M22S. doing some research there are certain bands which are license exempt and app the best if 915mhz. It gives us km's of range and on the ground station we'll just have another one of these.

For the gps we'll use a MAX-M10S

and KABOOM, there's the parts selection done, i think 

-- nevermind it's not done yet.

yo HOW could i have forgot the big pyro channels: AO3400A

i'll do six of these,

ill also add a magnetomorer, it's cheap and it gives a nice roll / heading ref like this! LIS2MDL

aaaand Bluetooth: DA14531MOD-00F01002




