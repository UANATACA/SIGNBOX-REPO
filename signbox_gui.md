# What it is

<div style="text-align: justify">
SignBox is a high performance, enterprise-grade solution for automated digital signature of any type of file. SignBox is a turnkey system that can be easily integrated with any business application thanks to its high-level web API based on the http RESTful paradigm.
</div>

# How it works
<div style="text-align: justify">
The service is given with SignBox Optimizer that is a server system exposing http RESTful APIs by means of which, business applications are enabled to require the digital signature of files batch.
<br></br>
SignBox Optimizer performs the most computationally expensive workload of the signature process, thus reducing the data traffic on the local network and make the most of the cryptographic hardware acceleration. The documnets to be signed are processed in the customer business layer and are not send to Uanataca Services, instead is sent a hash of the document created using a hash algorithm. For environments demanding high performance, SignBox can be coupled with a pool of SignBox Optimizer.
<br></br>
The system provides options for several digital signature formats including time stamping and long term validation. 
The signature is performed in Uanataca Trusted Service Center where signature keys are secure remotely stored.
</div>
<br></br>

![img](https://i.ibb.co/FzHZzfx/signbox-hiw.png)


# Configuration

SignBox Optimizer can be supplied as a Docker or as a Virtual Machine image.



## Hardware requirements


**CPU:** modern multicore (minimum 4 core)

**RAM:** 8GB

**HDD:** 200 GB



## SignBox Optimizer on Docker


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


## SignBox Optimizer on Virtual Machine (OVA)
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

The network settings are configured on the file `ifcfg-ens160`, which can be found in the path **/etc/sysconfig/network-scripts**. Edit the file and insert the correct IP address, network mask, gateway and DNS for your network.

Then restart network services with command **service network restart**.

Example:

![img](https://i.ibb.co/ccwRbbh/signbox-docker5.png)

> **Proxy network** settings

The Proxy settings are configured in the file `settings.ini` which can be found in path **/opt/bit4id/de/etc**. Edit the file and insert proxy address, port and credentials if are needed. 

It is possible to include url exceptions for services that don't use proxy network. Exceptions must be included in regular expression format.

Example:

![img](https://i.ibb.co/ZmRWP0W/signbox-ova1.png)
</br>


## Signature Image Configuration


A visual signature can be placed as an image in the signed document. The visual signature is composed by an image and text. SignBox allows to create and store multiple templates. The parameters can be adjusted on the `alias.ini` file which is located at: 

**/opt/bit4id/de/etc/img** (Virtual Machine)

**/opt/SignBox_Optimizer/common/etc/img** (Docker)

The `alias.ini` file contains templates size parameters of each stored image as well as the signature associated text, all represented by an "alias" which is included as a parameter in the **SIGN** API call.


<html>
<table>
  <tr>
    <th>Parameter</th><th>Description</th><th>Example</th>
  </tr>
  <tr>
    <td>[alias]</td><td> <b>img_bookmark</b> parameter in <a href="#tag/API-Reference/paths/~1api~1sign/post"> SIGN</a> Call</td><td>uanataca</td>
  </tr>
  <tr>
    <td>x1</td><td>Left X Coordinate (measured from page left side)</td><td>150</td>
  </tr>
  <tr>
    <td>y1</td><td>Bottom Y Coordinate (measured from page bottom)</td><td>80</td>
  </tr>
  <tr>
    <td>x2</td><td>Right X Coordinate (measured from page left side)</td><td>500</td>
  </tr>
  <tr>
    <td>y2</td><td>Top Y Coordinate (measured from page bottom)</td><td>180</td>
  </tr>
  <tr>
    <td>img</td><td>Image argb filename</td><td>uanataca.argb</td>
  </tr>
  <tr>
    <td>size_x</td><td>Horizontal image size in pt</td><td>512</td>
  </tr>
  <tr>
    <td>size_y</td><td>Vertical iamge size in pt</td><td>512</td>
  </tr>

  <tr>
    <td>paragraph_format</td><td>Text details structure</td><td>[{ "font" : ["Universal-Bold", 50]...</td>
  </tr>
</table> 
</br> 
</html>

Image .argb files must be locateed in the same **/img** directory where `alias.ini` is placed. Those files are created from the .png or .jpg files by using the **ARGB Tool.**

This is how the `alias.ini` file should look like (three different images are stored):

![img](https://i.ibb.co/23g2D1j/signbox-image.png)


## ARGB Tool

ARGB is a Windows tool that converts a PNG or JPG image to ARGB image type and generates the settings to include in the `alias.ini` file.

<a href="https://cdn.bit4id.com/es/uanataca/public/signbox/argb-graphic_signature.zip"> ARGB Tool download</a>


> STEP 1: Extract zip content

Extract the zip content in a local folder.
</br>

> STEP 2: Convert image and create alias settings

The ARGB Tool is performed through an executable file that is accessible in a Windows terminal at:<br>
**..\argb-graphic_signature\argb-tool\bin**

It just requires to copy the original image to this path and open a command prompt. 
Then run this command with the position <x1,y1,x2,y2> coordenates:

	argb utils make-ini image_filename.png [--out-dir folder_name] [--alias your_alias_name] [--position 80,80,380,230] 

The result of this command will consist in two files:

- An `alias.ini` file with all image parameters
- A `image_filename.argb` file

Both files must be moved to the **/img** directory in SignBox Optimizer (see <a href="#section/Configuration/Signature-Image-Configuration"> image folders path</a>)



## URL Out Service


## URL Back Service


# Logs

Service logs files are stored in a local folder in SignBox Optimizer.

Docker path:

**./common/etc/logs**

Virtual Machine path:

**/var/log/de**




# Postman collection

A postman collection is available as a support for a quick start.<br>
It is only required to edit `host`variable in Postman environment with the IP or domain of SignBox Optimizer.

<a href="https://cdn.bit4id.com/es/uanataca/public/signbox/Uanataca_SignBox_Postman.zip"> SignBox Postman collection download</a>