# What it is

<div style="text-align: justify">
The high-performance solution for Bulk, Interactive and LTV signature service.  Our best option for signing large number of documents and transactions.
<br></br>
SignBox API is an enterprise-grade solution for automated electronic signature of any type of file or document. A turnkey system that can be easily integrated with any business application without altering the user experience and guaranteeing the legal security of your electronically signed documents. SignBox API is a high-level web API based on the http RESTful paradigm.
</div>

# How it works

<div style="text-align: justify">
The API is given with SignBox Optimizer that is a server system exposing http RESTful API by means of which, business applications are enabled to require the electronic signature of large number of documents.
<br></br>
SignBox Optimizer performs the most computationally expensive workload of the signature process, thus reducing the data traffic on the local network and make the most of the cryptographic hardware acceleration. The documents to be signed are processed in the customer business layer and are not send to Uanataca Services, instead is sent a hash of the document created using a hash algorithm. For environments demanding high performance, SignBox can be coupled with a pool of SignBox Optimizer.
<br></br>
The system provides options for several electronic signature formats including time stamping and long term validation. The electronic signatures are performed in Uanataca Trusted Service Center side, where signature keys are stored in a Qualified Electronic Signature Creation Device (QSCD) system.

</div>
<br></br>

![img](https://raw.githubusercontent.com/UANATACA/SIGNBOX-REPO/main/img/signbox_hiw.png)


# Sandbox Environment


For testing purposes, Uanataca provides integrators of a pre-configured test-mode SignBox Optimizer accessible at the following URL:

</br>

	https://signbox.developers.uanataca.com

</br>

Want to configure your own test-mode Optimizer? Find instructions in the <a href='#section/Configuration'>configuration</a> section.


# Configuration

SignBox Optimizer can be supplied as a **Docker**.
See the configuration description in:<br>
<a href="#section/Configuration/SignBox-Optimizer-on-Docker"> SignBox Optimizer on Docker</a><br>



## Hardware requirements


**CPU:**  modern multicore (minimum 4 core)

**RAM:**  8GB

**HDD:**  200 GB



## SignBox Optimizer on Docker


This configuration requires a server with a Linux operating system.

The commands show belown are suitable for Ubuntu. Each Linux distribution will require it's own commands.

<a href="#section/Video-tutorials/Docker-Optimizer-Configuration"><img src="https://raw.githubusercontent.com/UANATACA/SIGNBOX-REPO/main/img/img4.png"></a><a href="#section/Video-tutorials/Docker-Optimizer-Configuration"><b>&nbsp;Watch on video!</b></a>


> STEP 1: Install Docker and Docker-Compose.

*Docker and Docker-Compose*

Run the following commands in this order.

    # Add Docker's official GPG key:
    sudo apt-get update
    sudo apt-get install ca-certificates curl
    sudo install -m 0755 -d /etc/apt/keyrings
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    sudo chmod a+r /etc/apt/keyrings/docker.asc

    # Add the repository to Apt sources:
    echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
      $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update

    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

    sudo systemctl start docker


Run command **sudo docker run hello-world** to check the installation.

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

	rm -rf /opt/signbox_optimizer/signbox.tar


</br>

> STEP 5: Launch the service.

Run:

	docker-compose up -d --scale api=4 --scale cryptosvc=8

Check service status:

	docker-compose ps

                 Name                                 Command                State                 Ports
	----------------------------------------------------------------------------------------------------------------------
	signbox_optimizer_nginx_1               /docker-entrypoint.sh ngin ...   Up      0.0.0.0:443->443/tcp,:::443->443/tcp,
                                                                                 	 0.0.0.0:80->80/tcp,:::80->80/tcp
	signbox_optimizer_signbox_api_1         signbox start                    Up
	signbox_optimizer_signbox_cryptosvc_1   signbox start                    Up

All services must be UP.

</br>
  

## Signature Image Configuration


A visual signature can be placed as an image in the signed document. The visual signature is composed by an image and text. SignBox allows to create and store multiple templates. The parameters can be adjusted on the `alias.ini` file which is located at: 

**/opt/signbox_optimizer/img** or custom mapped volume (Docker)

**/opt/bit4id/de/etc/img** (Virtual Machine)

The `alias.ini` file contains templates with size parameters of each stored image as well as the signature associated text, all represented by an "alias" which is included as a parameter in the <a href="#tag/Signature/paths/~1api~1sign/post"> SIGN</a> API call.


<html>
<table>
  <tr>
    <th>Parameter</th><th>Description</th><th>Example</th>
  </tr>
  <tr>
    <td>[alias]</td><td> <b>img_bookmark</b> parameter in <a href="#tag/Signature/paths/~1api~1sign/post"> SIGN</a> Call</td><td>uanataca</td>
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
    <td>paragraph_format</td><td>Text details structure. <a href="#paragraph">See description</a></td><td>[{ "font" : ["Universal-Bold", 50]...</td>
  </tr>
</table> 
</br> 
</html>

Image .argb files must be locateed in the same **/img** directory where `alias.ini` is placed. Those files are created from the .png or .jpg files by using the <a href="#section/Configuration/ARGB-Tool">ARGB Tool</a>.

This is how the `alias.ini` file should look like (three different images are stored in this example):

![img](https://github.com/UANATACA/SIGNBOX-REPO/blob/main/img/signbox-image.png?raw=true)

   
<div id="paragraph" style="padding-top: 60px;"><h2>Paragraph format<h2></div>


The signature image permits the addition of identifying text with the signer's associated data. This parameter allows to define the style and the content of the signature image, according to the following JSON object:

	[{ "font": [< FONTFAMILY >,< FONTSIZE >],"align" : < ALIGNVALUE >, "format": [< LINE1 >,< LINE2 >,...,< LINEN > ]}]

`font` (optional): defines the text format to include in the signature image. The parameters < FONTFAMILY > and < FONTSIZE > correspond to the font style and size respectively. The supported styles are the following:

* **Universal** (default): This style belongs to the Sans-Serif category (normal)
* **Univeral-Bold**: Universal font in bold
* **Univeral-Italic**: Universal font in italic
* **Universal-BoldItalic**: Universal font in bold and italic
* **Times**: This style belongs to the Serif category
* **Times-Bold**: Times font in bold
* **Times-Italic**: Times font in italic
* **Times-BoldItalic**: Times font in bold and italic

< FONTSIZE > specifies the character size, defined as points. The sofware adjusts automatically the font size according to the dimensions of the signature image.

`align` (optional): defines the text alignment related to an image (if exists). It can assume the following values:

* **right**: right text alignment (default)
* **left**: left text alignment
* **middle**: overlapped text

`format`: defines the text lines that will appear on the signature image. These lines can be created from static text and information included in the signer's digital certificate:

* **$(CN)s** : CommonName
* **$(L)s** : Locality
* **$(S)s** : stateOrProvinceName
* **$(OU)s** : organizationalUnitName
* **$(E)s** : email
* **$(Email)s** : email included in the Subject Alternative Name
* **$(Issuer)s** : Certificate CA Issuer Common Name 
* **$(date)s** : Date and time of the signature. Compliant to ISO 8601 standard.
* **$(reason)s** : “bit4-reason” parameter value
* **$(location)s** : “bit4-location” parameter value

Example:

	[{ "font" : [" Universal ",50]," align ":" right","format ": [" Digitally Signed by $(CN)s","O=$(O)s","C=$(C)s","S=$(S)s","Date: $(date)s","CustomField1: CustomValue1 ","CustomField2: CustomValue1 ","CustomField3: CustomValue3 "]}]

From this configuration, the image paragraph should look like as shown below:

![img](https://raw.githubusercontent.com/UANATACA/SIGNBOX-REPO/main/img/paragraph_format.JPG)


## ARGB Tool

ARGB Tool is a Windows software that converts a PNG or JPG image to ARGB image type and generates the settings to include in the `alias.ini` file.

Download: <a href="https://cdn.bit4id.com/es/uanataca/public/signbox/argb-graphic_signature.zip"> ARGB Tool</a>
</br>

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



## Webhooks

SignBox API requires that the customer business develop a webhook to manage the service callbacks. There are two callbacks in the service that must be set in the parameters `url_out` and `urlback` of the <a href="#tag/Signature/paths/~1api~1sign/post"> SIGN</a> API call.


**urlback**

The service logs are sent as a string in a HTTP POST request to the webhook url defined in this parameter.
Each signature generates a single string composed by the error message and the signature job identifier. For a successful signature the string only contains the job id.

Successful response:<br>

	id=104.2

Error response:

	message=Error%3A+Pin+invalid&exception=Error&id=980.4

**url_out**

In a successful signature process, the result signed file is sent as a binary file in a HTTP POST request to the webhoook url defined in this parameter.
</br>

> Sample code

In this sample, signed files are saved with the original filename in a folder named 'signbox-files'.
For the logs, a new log file is generated everyday containing daily logs. Log files are saved in a folder name 'logs', inside signbox-files folder.

The **url_out** parameter is defined as:  

	{host}/result_{filename}

The **urlback** parameter is defined as:

	{host}/servicelogs

where {filename} is the filename of the document to be signed, and {host} is the IP or domaing from the server exposing the webhook.


*Python*

	import web
	import os
	
	urls = (
	        '/result_(.+), 'url_out',
	        '/servicelogs', 'urlback'
	        )
	
	app = web.application(urls, globals())
	app = app.wsgifunc()
	
	class url_out:
		def POST(self, name):
		    data = web.data()
		    os.chdir('/signbox-files')
		    f = open("%s" % name,'w')
		    f.write(data)
		    f.close()
		    return ''

	class urlback:
		def POST(self,name):
		    data = web.data()
		    os.chdir('/signbox-files/logs')
		    f = open("%Y%m%d.txt" % name, 'a+')
		    f.write(str(data))
		    f.write(str("\n"))
		    f.close()
		    return ''
	
	if __name__ == "__main__":
	    app.run()


*PHP*

	<?php
	
	//url_out

	$post = file_get_contents('php://input',true);
	$file_handle = fopen('/signbox-files/', 'w');
	fwrite($file_handle, $post);
	fclose($file_handle);
	

	//urlback

	$post = file_get_contents('php://input');
	$line = $post.PHP_EOL;
	$myfile = fopen("/signbox-files/logs/%Y%m%d.txt", "a") or die("Unable to open file!");
	fwrite($myfile, $line );
	fclose($myfile);
	
	?>

## Rootstore configuration

As a requirement for LTV/LTVLITE signature level, we must add to the optimizer every chain of trust for each certificate that is involved in the signature.

In case that you don't make use of LTV/LVTLITE signature level, this process is NOT necessary.

This process vary depending on the selected optimizer install method.

Every certificate introduced in the optimizer regarding the rootstore needs to be formatted as a PEM file and introduced in a specific folder.
Below an example of the general structure that is needed.

Note: Number of lines below the header are not representative of a real certificate.

```
-----BEGIN CERTIFICATE-----
MIIIWjCCBkKgAwIBAgIIICfKLtFjrRMwDQYJKoZIhvcNAQELBQAwgbkxCzAJBgNV
BAYTAkVTMUQwQgYDVQQHDDtCYXJjZWxvbmEgKHNlZSBjdXJyZW50IGFkZHJlc3Mg
YXQgd3d3LnVhbmF0YWNhLmNvbS9hZGRyZXNzKTEWMBQGA1UECgwNVUFOQVRBQ0Eg
Uy5BLjEVMBMGA1UECwwMVFNQLVVBTkFUQUNBMRswGQYDVQQDDBJVQU5BVEFDQSBS
K+0fx83luCN81YLsUpdpc3e0URG7eDMKNG54WvtW
-----END CERTIFICATE-----
```

### *Docker*


> STEP 1: Load certificates in the optimizer

Load the PEM files in the following path or your mapped volume:

	/signbox_optimizer/etc/trusted_roots/certs

>STEP 2: Restart the service

After all the desired certificates have been loaded into the optimizer, we must fully restart the services with

	docker compose down

Followed by

	docker compose up -d


# Logs

Service logs are stored inside the containers.

To read logs in Docker version of SignBox Optimizer run this command:

	docker-compose logs





# Postman collection

A postman collection is available as a support for a quick start.<br>
It is only required to edit `host`variable in Postman environment with the IP or domain of SignBox Optimizer.

<a href="https://cdn.bit4id.com/es/uanataca/public/signbox/Uanataca_SignBox_Postman.zip"> SignBox Postman collection download</a>


# Video tutorials

Need a better understanding of SignBox API? Check the video tutorials below and follow step-by-step instructions! They will guarantee you use our API efficiently for the best experience in document signatures.

## Docker Optimizer Configuration

The Docker Optimizer configuration requires a SignBox Optimizer image package and a server using Linux Operating System with Internet access. Please check our <a href="#section/Configuration/SignBox-Optimizer-on-Docker">documentation</a>.<br></br>

<figure class="video_container">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/WbfeRqXPXV0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</figure>

# Error codes

<table id="error-codes" ">
  <thead>
    <tr>
      <th>Message</th>
      <th>Reason</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>ProcessTerminated: process ended with exit code X <put_remote_file('',)> </td>
      <td>Webhook malfunctioning</td>
    </tr>
    <tr>
      <td>Token not found </td>
      <td>Non existent certificate</td>
    </tr>
	<tr>
      <td>Invalid credentials</td>
      <td>Incorrect credentials</td>
    </tr>
	<tr>
      <td>PasswordProtected</td>
      <td>Input PDF protected with password</td>
    </tr>
	<tr>
      <td>Error: Pin invalid</td>
      <td>Incorrect pin value</td>
    </tr>
	<tr>
      <td>Error: Token locked</td>
      <td>Certificate credentials locked</td>
    </tr>
	<tr>
      <td>Error: Invalid Otp</td>
      <td>Wrong or expired OTP code</td>
    </tr>
	<tr>
      <td>Exception: Invalid Environment. Supported values are: []</td>
      <td>Incorrect environment value</td>
    </tr>
	<tr>
      <td>Exception: Remote Signature Failed: 401 Unauthorized</td>
      <td>Wrong billing credentials</td>
    </tr>
	<tr>
      <td>Exception: Invalid TSA alias</td>
      <td>Incorrect timestamping alias</td>
    </tr>
	<tr>
      <td>Exception: Invalid Image alias</td>
      <td>Incorrect image alias</td>
    </tr>
	<tr>
      <td>Exception: Remote Signature Failed: 400 Bad Request</td>
      <td>Insufficient credits</td>
    </tr>
	<tr>
      <td>Exception: Need tsa_bookmark for ltv signature </td>
      <td>Performing LTV with timestamping disabled</td>
    </tr>
	<tr>
      <td>Exception: UnknownIssuer</td>
      <td>Certificate of any hierarchy missing</td>
    </tr>
	<tr>
      <td>Exception: Time Stamping Failed: 400 Bad Request</td>
      <td>Non existent timestamping</td>
    </tr>
  </tbody>
</table>

<div id="APIReference" style="padding-top: 60px;"><h1>API Reference<h1></div>

