# Hadoop Spark Raspberry Pi Cluster (4 Pi's)

This is a walkthrough on how I built a bare-metal a networked cluster of four Raspberry Pis (4 b 4gb).

##### Table of Contents  
[Materials](#materials)  
[Inital Set Up](#setup)  
[Kubernetes](#kube)





abc
<a name="materials"/>
## Materials

* [4 Raspberry Pi 4 Model B 4gb: $55](https://www.adafruit.com/product/4296)
* [64gb Micro SD Card: $9](https://www.amazon.com/SanDisk-128GB-MicroSDXC-Ultra-Memory/dp/B07XDCZ9J3/ref=sr_1_6?dchild=1&keywords=sandisk+64gb+micro+sd+card&qid=1630199018&sr=8-6)
* [Power Supply 3A: $7](https://www.amazon.com/Power-Supply-Adapter-Switch-Raspberry/dp/B07TSDJSQH/ref=sr_1_8?dchild=1&keywords=3a+power+supply+adapter+raspberry+pi&qid=1630198961&sr=8-8)
* [4x Lan Cables - 6 inch: $7](https://www.amazon.com/gp/product/B07ZNWRQBT/ref=ppx_yo_dt_b_asin_title_o01_s00?ie=UTF8&psc=1)
* [5 Port switch: $14](https://www.amazon.com/Ethernet-Splitter-Optimization-Unmanaged-TL-SG105/dp/B00A128S24/ref=sr_1_4?dchild=1&keywords=5+port+hub&qid=1630198782&sr=8-4)
* [Cluster Case: $22](https://www.amazon.com/iUniker-Raspberry-Cluster-Heatsink-4-Layers/dp/B07CTG5N3V/ref=sr_1_1_sspa?dchild=1&keywords=iUniker+cluster&qid=1630015504&s=electronics&sr=1-1-spons&psc=1&smid=A2NBN5PD2G7LDZ&spLa=ZW5jcnlwdGVkUXVhbGlmaWVyPUFCNkM1S0Q0UFBOMk4mZW5jcnlwdGVkSWQ9QTAxMTAwNDNST1ZFWUpWTEQ3QzkmZW5jcnlwdGVkQWRJZD1BMDM1MTQ1NjJMVjJDR1oyQTQyTFUmd2lkZ2V0TmFtZT1zcF9hdGYmYWN0aW9uPWNsaWNrUmVkaXJlY3QmZG9Ob3RMb2dDbGljaz10cnVl
)


This is a walkthrough on how I built a bare-metal a networked cluster of four Raspberry Pis (4 b 4gb).

<img src="https://i.imgur.com/5XSXLmu.jpg" alt="4 Raspberry Pi's 4s" width=50% height=50%>



<img src="https://i.imgur.com/WLjUSeF.jpg" alt="Assembled RPi's Cluster" width=50% height=50%>



abc
<a name="setup"/>
## Initial Set Up

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

f)  If you are on windows it should auto load the folder, if not open it up the folder and look for `"cmdline.txt"` and open it.

g)  Add the following to the end of the line of text:
```"cgroup_memory=1 cgroup_enable=memory ip=192.168.X.XX::192.168.X.X:255.255.255.0:RaspberryPiXX:eth0:off"``` (replace all X's)
Replace the first IP address to the one you want to set it as. Replace the mask as necessary. Replace the Name as necessary.
Reference(ip=<client-ip>:<server-ip>:<gw-ip>:<netmask>:<hostname>:<device>:<autoconf>)

h) Go back to the folder and open `config.txt`. At the bottom of the file: add `arm_64bit=1`

f) Open Powershell on windows, and go to the drive of your micro SD Card, could be anything from D-E-F-G-H... then type in the appropriate letter drive like: `"F:"`
  Then type in `"new-item ssh"` and you should see a response like this:

  <img src="https://i.imgur.com/VEG9OVv.jpg" alt="PowerShell" width=50% height=50%>
  
g) Insert Micro SD Card back into the Rpi. Plug the power in. Wait a sec. SSH back into the IP address you set at step G. `ssh pi@192.168.0.31` (Your address is likely to be different). Enter `yes` at the first prompt, then the default password is `raspberry`.
  
h) We will now configure the [IP tables](https://linux.die.net/man/8/iptables) (Link to Man pages).
  
  ```
  sudo iptables -F 
  sudo update-alternatives --set iptables /usr/sbin/iptables-legacy 
  sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy 
  sudo reboot
  ```
  
abc
<a name="kube"/>
## Kubernetes
  
a) SSH back into your Raspberry Pi that will be your master node. For me it is `ssh pi@192.168.0.31`
  
b) Install Kubernetes (specifically K3s. You can read the difference [here](https://www.civo.com/blog/k8s-vs-k3s)). 
  ```
  curl -sfL https://get.k3s.io | K3S_KUBECONFIG_MODE="644" sh -s -
  
  #Save this token from the following command to be used later in YOURTOKEN
  
  sudo cat /var/lib/rancher/k3s/server/node-token

  ```
 c) SSH into all your other Raspberry Pi computers.
  
  ```
  curl -sfL https://get.k3s.io | K3S_TOKEN="YOURTOKEN" K3S_URL="https://[your IP address]:6443" K3S_NODE_NAME="pick a name" sh 
  ```
  curl -sfL https://get.k3s.io | K3S_TOKEN="YOURTOKEN" K3S_URL="https://[your IP address]:6443" K3S_NODE_NAME="pick a name" sh 







