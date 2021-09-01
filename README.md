# Bare-Metal Raspberry Pi Cluster (4 Pi's)

This is a walkthrough on how I built a bare-metal a networked cluster of four Raspberry Pis (4 b 4gb).

##### Table of Contents  
[Materials](#materials)  
[Inital Set Up](#initial-set-up)  
[Kubernetes](#kubernetes)   
[License](#license) 








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

Hints:
You can type `vcgencmd measure_temp` in the command line to check the temperatures of your CPU. With the heatsink and fan installed it should be cooler than your typical 50 degrees. I was running at 40ish degrees.

You can also type `top` or `htop` to see your cpu usage.



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
  
## Kubernetes
  
a) SSH back into your Raspberry Pi that will be your master node. For me it is `ssh pi@192.168.0.31`
  
b) Install Kubernetes (specifically K3s. You can read the difference [here](https://www.civo.com/blog/k8s-vs-k3s)). 
  ```
  curl -sfL https://get.k3s.io | K3S_KUBECONFIG_MODE="644" sh -s -
  
  #Save this token from the following command to be used later in YOURTOKEN
  
  sudo cat /var/lib/rancher/k3s/server/node-token

  ```
  
  If you sucessfully installed K3s you will see this output:
  
  <img src="https://i.imgur.com/ZhCnhCt.jpg" alt="K3s" width=50% height=50
       
 c) SSH into all your other Raspberry Pi computers. Replace YOURTOKEN with the value you saved from above, your ip address, and pick a name.
  
  ```
  curl -sfL https://get.k3s.io | K3S_TOKEN="YOURTOKEN" K3S_URL="https://[your IP address]:6443" K3S_NODE_NAME="pick a name" sh 
  ```
 d) After you have sucessfully done this for each of your raspberry pi nodes. You can go back to the master node and type in `kubectl get nodes`. You should see all connected nodes with a ready status and the time it has been connected.
       
<img src="https://i.imgur.com/TJvaVtX.jpg" alt="Kube confirmation" width=50% height=50%>
  
## License
  
Copyright 2021 Albert Yeh

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.  
  
  
  
    
## MariaDB

Next we are goign to install the M of our LE'M'P stack. MariaDB.
  
Now that you have a web server up and running, you need to install the database system to be able to store and manage data for your site.

In Debian 10, the metapackage mysql-server, which was traditionally used to install the MySQL server, was replaced by default-mysql-server. This metapackage references MariaDB, a community fork of the original MySQL server by Oracle, and it’s currently the default MySQL-compatible database server available on debian-based package manager repositories.

For longer term compatibility, however, it’s recommended that instead of using the metapackage you install MariaDB using the program’s actual package, mariadb-server.

To install this software, run:

sudo apt install mariadb-server
 
When the installation is finished, it’s recommended that you run a security script that comes pre-installed with MariaDB. This script will remove some insecure default settings and lock down access to your database system. Start the interactive script by running:

sudo mysql_secure_installation
 
This script will take you through a series of prompts where you can make some changes to your MariaDB setup. The first prompt will ask you to enter the current database root password. This is not to be confused with the system root. The database root user is an administrative user with full privileges over the database system. Because you just installed MariaDB and haven’t made any configuration changes yet, this password will be blank, so just press ENTER at the prompt.

The next prompt asks you whether you’d like to set up a database root password. Because MariaDB uses a special authentication method for the root user that is typically safer than using a password, you don’t need to set this now. Type N and then press ENTER.

From there, you can press Y and then ENTER to accept the defaults for all the subsequent questions. This will remove anonymous users and the test database, disable remote root login, and load these new rules so that MariaDB immediately respects the changes you have made.
When you’re finished, log in to the MariaDB console by typing:

sudo mariadb

```
  Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 53
Server version: 10.3.29-MariaDB-0+deb10u1 Raspbian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
  ```
