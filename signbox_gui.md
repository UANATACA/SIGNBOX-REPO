# What it is

<div style="text-align: justify">
SignBox is a high performance, enterprise-grade solution for automated digital signature of any type of file. SignBox is a turnkey system that can be easily integrated with any business application thanks to its high-level web API based on the http RESTful paradigm.
</div>

# How it works
<div style="text-align: justify">
SignBox Optimizer is a server system exposing http RESTful APIs by means of which, business applications are enabled to require the digital signature of files batch. The system provides options for several digital signature formats including time stamping and long term validation. 
<br></br>
For environments demanding high performance SignBox can be coupled with a pool of SignBox Optimizer, a component performing the most computationally expensive workload of the signature process, thus reducing the data traffic on the local network and make the most of the cryptographic hardware acceleration.
Signature keys are stored remotely at Uanataca Trusted Service Center.
</div>
<br></br>
![img](https://i.ibb.co/qjxqHtg/Grupo-12227.png)


# Configuration

SignBox Optimizer can be supplied as a Virtual Machine or as a Docker image.



## Hardware requirements


**CPU:** modern multicore (minimum 4 core)

**RAM:** 8GB

**HDD:** 200 GB


## SIGNBOX on Virtual Machine (OVA)
<div style="text-align: justify">
The Virtual Machine is supplied in an OVA file. SignBox Optimizer image is compatible with common virtual environments like VMWare, AWS, Azure or VirtualBox.
</div>
</br>

> STEP 1: Import SignBox Optimizer (VM) in the virtual environment.
<div style="text-align: justify">
Adjust the system requirements for optimal usage considering host terminal resources described in <a href="#section/Configuration/Hardware-requirements"> hardware requirements</a>.
</div>

</br>

> STEP 2: Network configuration.
<div style="text-align: justify">
The network settings are configured on the file `ifcfg-ens160`, which can be found in the path **/etc/sysconfig/network-scripts**. Edit the file and insert the correct IP address, network mask, gateway and DNS for your network.
</div>
Then restart network services with command **service network restart**.

Example:

![img](https://i.ibb.co/ccwRbbh/signbox-docker5.png)

</br>



## SIGNBOX on Docker


This configuration requires a server with Linux operating system.

</br>


> STEP 1: Install Docker, Docker-Compose and Python.

*Docker*

Run the following commands in this order.

	yum install -y yum-utils device-mapper-persistent-data lvm2
	yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
	yum install docker-ce
	usermod -aG docker $(whoami)
	systemctl enable docker.service
	systemctl start docker.service

*Docker-Compose & Python tools*

Run the following commands in this order.


	yum install epel-release
	yum install -y python-pip
	pip install --upgrade pip
	yum upgrade python*
	yum install gcc
	pip install --upgrade setuptools
	yum install python-devel
	pip install docker-compose

Run command **docker-compose version** to check the installation. The outcome should show this information:


![img](https://i.ibb.co/WphfmXH/signbox-docker1.png) 

</br>

> STEP 2: Create folders to store settings and logs outside docker image.

Create a local folder named **SignBox_Optimizer** and extract all .zip content here.

Move SignBox Optimizer folder to the path **/opt** in the server.

The outcome should look like this:

![img](https://i.ibb.co/q1P6k94/signbox-docker2.png)

Create server folders to store service settings and logs.

	mkdir -p /opt/SignBox_Optimizer/common/etc
	mkdir -p /opt/SignBox_Optimizer/common/etc/logs

Extract `img.zip` and `tsa.zip` content and move to path **/opt/SignBox_Optimizer/common/etc** in the server. This should look like this:

![img](https://i.ibb.co/RbWZcdd/signbox-docker4-1.png)

</br>

> STEP 3: Modify the `Docker-Compose.yml` file by adding the highlighted folders.

	cd /opt/SignBox_Optimizer

![img](https://i.ibb.co/sbvLXwW/signbox-docker3.png)

</br>

> STEP 4: Load Docker SignBox image.

Run the following commands:

	cd /opt/SignBox_Optimizer
	tar -xzf docker_signbox.tar.gz 
	docker image load -i signbox.tar 

</br>

> STEP 5: Launch the service.

Run:

	docker-compose up -d

</br>



## Signature Image Configuration


SignBox allow 

The parameters of the image to be placed on a document when is signed, can be adjusted on the `alias.ini` file which is located at: 

**/opt/bit4id/de/etc/img** (Virtual Machine)

**/opt/SignBox_Optimizer/common/etc/img** (Docker)

The `alias.ini` file contains the size parameters of each stored image as well as the signature associated text, all represented by an "alias" which is included as a parameter in the **SIGN** API call.

This is how the `alias.ini` file should look like (three different images are stored):

![img](https://i.ibb.co/23g2D1j/signbox-image.png)

Where:

<html>
<table>
  <tr>
    <th>Parameter</th><th>Description</th><th>Example</th>
  </tr>
  <tr>
    <td>[alias]</td><td>img_bookmark for SIGN Call</td><td>bit4id</td>
  </tr>
  <tr>
    <td>y2</td><td>Top Y Coordinate (measured from page bottom)</td><td>180</td>
  </tr>
  <tr>
    <td>x2</td><td>Right X Coordinate (measured from page left side)</td><td>500</td>
  </tr>
  <tr>
    <td>size_x</td><td>Horizontal size in pt</td><td>512</td>
  </tr>
  <tr>
    <td>size_y</td><td>Vertical size in pt</td><td>512</td>
  </tr>
  <tr>
    <td>y1</td><td>Bottom Y Coordinate (measured from page bottom)</td><td>80</td>
  </tr>
  <tr>
    <td>x1</td><td>Left X Coordinate (measured from page left side)</td><td>150</td>
  </tr>
  <tr>
    <td>paragraph_format</td><td>Text details structure</td><td>[{ "font" : ["Universal-Bold", 50]...</td>
  </tr>
</table> 
</br> 
</html>

**PLEASE NOTE THAT THE IMAGE ADDITION TO A SIGNATURE IS ALWAYS OPTIONAL.**


As can be seen, in case of image addition the .argb files must present in the same **/img** directory where `alias.ini` is placed. Those files are created from the original .png or .jpg files by using the **ARGB Tool.**


## Signature Image Creation

The ARGB Tool is performed through an executable file that is accessible at /ARGB\_TOOL/bin. In a Windows terminal, it just requires to extract the provided ARGB_TOOL.zip on any location, then open a command prompt and move the original image files to **/ARGB\_TOOL/bin** for conversion. 

Then execute:

	argb utils make-ini bit4id.png [--out-dir directory] [--alias aliasname] [--position 150,80,500,180] 

The result of this command will consist in two files:

- An `alias.ini` file with all image parameters.
- A `bit4id.argb` file that must be moved to the Docker/VM **/img**  directory.



