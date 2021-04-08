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

![img](https://raw.githubusercontent.com/UANATACA/SIGNBOX-REPO/main/img/signbox_hiw.png)


# Configuration

SignBox Optimizer can be supplied as a **Docker** or as a **Virtual Machine** image.



## Hardware requirements


**CPU:** modern multicore (minimum 4 core)

**RAM:** 8GB

**HDD:** 200 GB



## SignBox Optimizer on Docker


This configuration requires a server with Linux operating system.

</br>


> STEP 1: Install Docker and Docker-Compose.

*Docker*

Run the following commands in this order.

	sudo yum update -y
	yum install -y yum-utils device-mapper-persistent-data lvm2
	yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
	sudo yum install -y docker-ce docker-ce-cli containerd.io
	sudo systemctl start docker



*Docker-Compose*

Run the following commands in this order.


	sudo curl -L "https://github.com/docker/compose/releases/download/1.28.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
	chmod +x /usr/local/bin/docker-compose


Run command **docker-compose version** to check the installation. The outcome should show this information:


![img](https://raw.githubusercontent.com/UANATACA/SIGNBOX-REPO/main/img/signbox-docker1.png) 

</br>

> STEP 2: Extract and copy SignBox Optimizer zip content to the server.

Extract all `signbox_optimizer_docker.zip` content in a local folder.

Move SignBox Optimizer folder to the path **/opt** in the server.

The outcome should look like this:

![img](https://raw.githubusercontent.com/UANATACA/SIGNBOX-REPO/main/img/signbox-docker2.png)

</br>

> STEP 3: Mapping volumes (for environments with a pool of SignBox Optimizer).

In high performance environments with a pool of SignBox Optimizer, service settings and logs must be stored in a shared volume outside Optimizer servers. These volumes must be defined in `docker-compose.yml` file in each SignBox Optimizer.

	cd /opt/signbox_optimizer

Docker-compose.yml settings file:

![img](https://github.com/UANATACA/SIGNBOX-REPO/blob/main/img/signbox-docker3.png?raw=true)

</br>

> STEP 4: Load SignBox Docker image.

Run the following commands:

	cd /opt/signbox_optimizer
	docker image load -i signbox.tar 

Remove image file:

	rm /opt/signbox_optimizer/signbox.tar


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

![img](https://github.com/UANATACA/SIGNBOX-REPO/blob/main/img/signbox-docker5.png?raw=true)

> **Proxy network** settings

The Proxy settings are configured in the file `settings.ini` which can be found in path **/opt/bit4id/de/etc**. Edit the file and insert proxy address, port and credentials if are needed. 

It is possible to include url exceptions for services that don't use proxy network. Exceptions must be included in regular expression format.

Example:

![img](https://raw.githubusercontent.com/UANATACA/SIGNBOX-REPO/c4c4b74b4c4183cee57dc9be3a9eaf159ee2f4a1/img/signbox-ova1.png)
</br>


## Signature Image Configuration


A visual signature can be placed as an image in the signed document. The visual signature is composed by an image and text. SignBox allows to create and store multiple templates. The parameters can be adjusted on the `alias.ini` file which is located at: 

**/opt/signbox_optimizer/img** or custom mapped volume (Docker)

**/opt/bit4id/de/etc/img** (Virtual Machine)

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
    <td>size_y</td><td>Vertical image size in pt</td><td>512</td>
  </tr>

  <tr>
    <td>paragraph_format</td><td>Text details structure. <a href="#paragraph">See description below</a></td><td>[{ "font" : ["Universal-Bold", 50]...</td>
  </tr>
</table> 
</br> 
</html>

Image .argb files must be locateed in the same **/img** directory where `alias.ini` is placed. Those files are created from the .png or .jpg files by using the **ARGB Tool.**

This is how the `alias.ini` file should look like (three different images are stored):

![img](https://github.com/UANATACA/SIGNBOX-REPO/blob/main/img/signbox-image.png?raw=true)

   
<div id="paragraph" style="padding-top: 60px;"><h2>Paragraph format<h2></div>


The signature image permits the addition of identifying text with the signer's associated data. This parameter allows to define the style and the content of the signature image, according to the following JSON object:

  [{ "font": [< FONTFAMILY >,< FONTSIZE >]," align" : < ALIGNVALUE >, "format": [< LINE1 >,< LINE2 >,...,< LINEN > ]}]

**font** (optional) defines the text format to include in the signature image. The parameters < FONTFAMILY > and < FONTSIZE > correspond to the font style and size respectively. The supported styles are the following:

* "Universal" (default): This style belongs to the Sans-Serif category (normal)
* "Univeral-Bold": Universal font in bold
* "Univeral-Italic": Universal font in italic
* “Universal-BoldItalic”: Universal font in bold and italic
* "Times": This style belongs to the Serif category
* "Times-Bold": Times font in bold
* "Times-Italic": Times font in italic
* "Times-BoldItalic": Times font in bold and italic

< FONTSIZE > specifies the character size, defined as points. The sofware adjusts automatically the font size according to the dimensions of the signature image.

**align** (optional) defines the text alignment related to an image (if exists). It can assume the following values:

* right: right text alignment (default)
* left: left text alignment
* middle: overlapped text

**format** defines the text lines that will appear on the signature image. These lines can be created from the information included in the signer's digital certificate:

* $(CN)s : CommonName
* $(L)s : Locality
* $(S)s : stateOrProvinceName
* $(OU)s : organizationalUnitName
* $(E)s : email
* $(Email)s : email includen in the Subject Alternative Name
* $(Issuer)s : Certificate CA Issuer Common Name 
* $(date)s : Date and time of the signature. Compliant to ISO 860 standard.
* $(reason)s : “bit4-reason” parameter value
* $(location)s : “bit4-location” parameter value

**Example:**

  [{ "font" : [" Universal ",50]," align ":" right","format ": [" Digitally Signed by $(CN)s","O=$(O)s","C=$(C)s","S=$(S)s","Date: $(date)s","CustomField1: CustomValue1 ","CustomField2: CustomValue1 ","CustomField3: CustomValue3 "]}]

From this configuration, the image paragraph should look like as shown below:

![img](https://raw.githubusercontent.com/UANATACA/SIGNBOX-REPO/main/img/paragraph_format.JPG)


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

**/opt/signbox_optimizer/logs** or custom mapped volume 

Virtual Machine path:

**/var/log/de**




# Postman collection

A postman collection is available as a support for a quick start.<br>
It is only required to edit `host`variable in Postman environment with the IP or domain of SignBox Optimizer.

<a href="https://cdn.bit4id.com/es/uanataca/public/signbox/Uanataca_SignBox_Postman.zip"> SignBox Postman collection download</a>