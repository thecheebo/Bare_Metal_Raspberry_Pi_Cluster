# Hadoop Spark Raspberry Pi Cluster (4 Pi's)

##### Table of Contents  
[Purchase](#Purchase)  
[Set Up](#Set Up)  




<a name="Purchase"/>
## Headers


This is a walkthrough on how I built a bare-metal a networked cluster of four Raspberry Pis (4 b 4gb).

<img src="https://i.imgur.com/5XSXLmu.jpg" alt="4 Raspberry Pi's 4s" width=50% height=50%>



<img src="https://i.imgur.com/WLjUSeF.jpg" alt="Assembled RPi's Cluster" width=50% height=50%>

[Amazon Link to Rpi Cluster Rack: $22 at time of writing](https://www.amazon.com/iUniker-Raspberry-Cluster-Heatsink-4-Layers/dp/B07CTG5N3V/ref=sr_1_1_sspa?dchild=1&keywords=iUniker+cluster&qid=1630015504&s=electronics&sr=1-1-spons&psc=1&smid=A2NBN5PD2G7LDZ&spLa=ZW5jcnlwdGVkUXVhbGlmaWVyPUFCNkM1S0Q0UFBOMk4mZW5jcnlwdGVkSWQ9QTAxMTAwNDNST1ZFWUpWTEQ3QzkmZW5jcnlwdGVkQWRJZD1BMDM1MTQ1NjJMVjJDR1oyQTQyTFUmd2lkZ2V0TmFtZT1zcF9hdGYmYWN0aW9uPWNsaWNrUmVkaXJlY3QmZG9Ob3RMb2dDbGljaz10cnVl
)



■■■■■■■■■■ STEPS ■■■■■■■■■■

a)  Visit: https://www.raspberrypi.org/software/
Download Raspberry Pi Imager for your appropriate OS. (Choices are: windows macOS, ubuntu for x86). Install it.
b)  Visit: https://www.raspberrypi.org/software/operating-systems/
Download Raspberry Pi OS Lite, it is the smallest of the 3 image files at around 450mb. 

<img src="https://i.imgur.com/Q0AWvxK.jpg" alt="Raspberry Pi Imager" width=50% height=50%>

c)  Open the Raspberry Pi Imager. Find your downloaded OS lite file. Mount it. Select your SD card.
It will ask if you want to format it (WARNING: Please back up any files on your micro sd card).
Wait couple minutes. When it is done it will give you a notification. You can safely remove your SD card.

d)  Insert the micro SD card into the Raspberry Pi and wait for it to finish booting (3 minutes). 

e)  Turn off the power source. Remove the micro SD card and plug it back into your computer.

f)  If you are on windows it should auto load the folder, if not open it up the folder and look for "cmdline.txt" and open it.

g)  Add the following to the end of the line of text:
"cgroup_memory=1 cgroup_enable=memory ip=192.168.X.XX::192.168.X.X:255.255.255.0:RaspberryPiXX:eth0:off" (replace all X's)
Replace the first IP address to the one you want to set it as. Replace the mask as necessary. Replace the Name as necessary.
Reference(ip=<client-ip>:<server-ip>:<gw-ip>:<netmask>:<hostname>:<device>:<autoconf>)

h) Go back to the folder and open "config.txt". At the bottom of the file: add "arm_64bit=1"

f) Open Powershell on windows, and go to the drive of your micro SD Card, could be anything from D-E-F-G-H... then type in the appropriate letter drive like: "F:"
  Then type in "new-item ssh" and you should see a response like this:

  <img src="https://i.imgur.com/VEG9OVv.jpg" alt="PowerShell" width=50% height=50%>









