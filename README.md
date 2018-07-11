# RBE2001_template
Template code for the RBE 2001 final project

# Arduino and the ESP32 Toolchain

## Windows  
download:

http://users.wpi.edu/~glipin/.uploads/RBE-arduino.zip

And extract it somewhare on your computer. Run Arduino in the extracted folder.

## Linux / Mac /Windows full install

see: https://github.com/espressif/arduino-esp32#installation-instructions

## Arduino Libraries

Open Arduino and select Sketch->Libraries -> Manage Libraries

Search for and install:
ESP32Servo
Esp32SimplePacketComs
SimplePacketComs
EspWii
WiiChuck

# Set up your project

## Create your private repository (Once per term per team)

In the browser, click the "+" button next to your user Icon in the upper right corner of any github page. 

Select Import repository. 

Use the URL of this repository as the source, and BE SURE TO SELECT PRIVATE! 

Set the name during the import step as "RBE2001"

## Add your team mates as collaborators

In your new repository, under Settings -> Collaborators

Search and add your team mates. 

# Develop Code for your project

Open Eclipse and set the workspace to Arduino mode. 

## Clone your project

In eclipse, open Window->Show View-> Other -> Git ->Git Repositories

In the browser, Click the clone or download button in your new project and select clone (Be sure HTTPS not SSH is selected, unless you have set up your SSH keys). 

Copy the clone URL.

In Eclipse, right click in the git repositories and select clone. If your URL is still copied in the clip board, Eclipse will autofill most the clone information. You need to change 2 things. 1) Add your username and password. 2) set the target directory to your Arduino libraries folder. It is usually in \Documents\Arduino\libraries for windows, or $HOME/Arduino/libraries for Unix systems. 

## Open your project and start working

To open the project you will be using Sloeber and the "Linked Example" development model. Your code is set up as a library. your launch file is the .ino, which is found in your projects "examples" directory. 

In Eclipse, Right click in the Project View and select New->Arduino Sketch

Give it a name, and select the ESP32 toolchain. 
Board: ESP32 Dev Module
Upload Protocol: Default
Port (the port your device is on)
Core Debug Level: None
Flash Frequency 80mhz
Flash Mode: QIO
Flash Size: 4mb
Upload Speed: 921600

Then hit next

Select Sample sketch in the "Select Code:" dropdown. Select "Link to selected examples" and check the check box for RBE2001 examples ->Final Project

Hit finish. The code that you should edit is in your project under libraries/RBE2001/

### If the ESP32 toolchain is not an option, or your library is not visible
In eclipse, Use the Arduino -> Perferences menu
Under toolchains, select New.. and search for where you extracted Arduino. 
Under Libraries, select New.. and search for the directory in which you cloned your GIT clone.

# Commit your changes

When you make a change to any line of code, you should commit your changes. If you have worked for ~1 hour, then you should make a commit with a message describing your work. To do so, go back to Git Repositories, and right click on your repository->Commit

Messages should consist of 60 charrectors of short description describing what changed, 2 new lines, then a t least one full sentance describing the justification for the change. If you have more than one fime, only commit one at a time. using the "+" and "-" buttons in the Git Staging section. 


# 2001 Final Project Commands

## Warehouse Robot

### Get Status
| |ID | byte |
|--- |--- | --- |
| downstream Bytes |4 | 0 |
| Contents downstream |2012 | --- |
| upstream Bytes |4 | 1 |
| Contents upstream |2012 | status value|

Request the status of the robot. The byte value represents the robot state.

| State description | value |
|--- |--- |
| Ready for new task | 0 |
| Heading to pickup | 1 |
| Picking up | 2 |
| Heading to Dropoff | 3 |
| Dropping off | 4 |
| Heading to safe zone| 5 |
| Fault: failed pickup| 6 |
| Fault: failed dropoff| 7 |
| Fault: excessive load| 8 |
| Fault: obstructed path| 9 |
| Fault: E Stop pressed | 10 |

### Clear Faults 

| |ID | byte |
|--- |--- | --- |
| downstream Bytes |4 | 0 |
| Contents downstream |1871 | --- |
| upstream Bytes |4 | 0 |
| Contents upstream |1871 | ---|

Clear all faults on the robot. No data is transmitted, but the state of the robot should be effected. 

### Pick Order

| |ID | float |float |float |float |float |float |
|--- |--- | --- |--- | --- |--- | --- | --- |
| downstream Bytes |4 | 4 | 4 | 4 | 4 | 4 | 4 |
| Contents downstream |1936 | pickup area | pickup x | pickup z | drop off area | drop off  x | drop off  z |
| upstream Bytes |4 | 0 |
| Contents upstream |1936 | ---|

An order is placed with the robot. The first 3 values are the pickup location, followed by the x and Z values in MM of the shelf where the pallet is located. The second 3 values are the drop off location, followed by the X and Z values in MM of the shelf where the pallet is to be deposited. 

### Get Location 
| |ID | float |float |float |float |float |float |float |float |
|--- |--- | --- |--- | --- |--- | --- | --- |--- | --- | 
| downstream Bytes |4 | 0 |
| Contents downstream |1994 | --- |
| upstream Bytes |4 | 4 | 4 | 4 | 4 | 4 | 4 |4 | 4 |
| Contents upstream |1994 | X Location | Y Location | Z Location | azimuth | elevation | tilt | bounding cylinder radius | bounding cylinder height | 

Request for the position and orentation of the robot. Location is in MM from the loading point in location 0 to the center bottom of the bounding cylinder of the robot. The size of the robot is reported in MM measured in positive values from center bottom of the bounding cylinder that envelops the robot. The center is defined as the turning center of the robot. The radius is the distance to the furthest point on the robot from the turning center. Maximum radius is 100mm. Direction of travil is +X in the robots coordinate frame.

### Direct Drive
| |ID | float |float |float |float |float |float |float |float |
|--- |--- | --- |--- | --- |--- | --- | --- |--- |--- |
| downstream Bytes |4 | 4 | 4 | 4 | 4 | 4 | 4 |4 |4 |
| Contents downstream |1786  | delta X Location | delta Y Location |delta  Z Location | delta azimuth | delta elevation | delta tilt | # Miliseconds this update should take | Session ID|
| upstream Bytes |4 | 4 |
| Contents upstream |1786  | value from 0-1 to indicate progress on current session  |

This is a command to drive a robot directly. The values represent a relative motion from current location. +X is forward for the robot.Angle values are in degrees and translation values are in Milimeters.  Azimuth values range from -180 to 180 with 0 along the robots X dimention. Elevation values are rotation values from -90 to 90 about the Y dimention with 0 being along the x dimenttion. Tilt values are rotations from -180 to 180 about the X dimention with 0 being aligned with the X-Y plane. 

The session ID is a random integer from 0 - 100000 indicating the current drive command. It will change when the target is updateted. It is used to differentiate between new commands. 

The command should return the current progress on the current command ID. 
