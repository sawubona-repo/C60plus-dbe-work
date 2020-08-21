A STEP-BY-STEP TUTORIAL:
<h1>INSTALL XXXXX ON AMAZON AWS LIGHTSAIL</h1>


![](./images/title-graphic1.jpeg) 


TODOS:  This article is still a work-in-progress...
- xxx
- xxx
- xxx


<br>
##TABLE OF CONTENTS:

- [**PREREQUISITES:** What you need before you start](#prerequisites)
- [**STEP #1:** Create a new AWS Ubuntu server instance](#step-1)
- [**STEP #2:** Connect to your new server and update it](#step-2)
- [**STEP #3:** Create and assign a new static IP address](#step-3)
- [**STEP #4:** Add a new DNS Zone](#step-4)
- [**STEP #5:** Configure the DNS Zone](#step-5)
- [**STEP #6:** Open the HTTPS port in the AWS Firewall](#step-6)
- [**STEP #7:** Point your domain name to AWS](#step-7)
- [**STEP #8:** Install Dokku](#step-8)
- [**STEP #9:** Deploy your first Dokku app (static website)](#step-9)
- [**STEP #10:** Deploy your second Dokku app (Node JS)](#step-10)
- [**STEP #11:** xxxxxxxxxxxxxxxxxx](#step-11)
- [**STEP #12:** xxxxxxxxxxxxxxxxxx](#step-12)
- [**STEP #13:** xxxxxxxxxxxxxxxxxx](#step-13)
- [**STEP #14:** xxxxxxxxxxxxxxxxxx](#step-14)
- [**STEP #15:** xxxxxxxxxxxxxxxxxx](#step-15)
- [**STEP #16:** xxxxxxxxxxxxxxxxxx](#step-16)

<br>
##PREREQUISITES:

1. You will need your own registered domain name (i.e. mydomain.com) and will need administrative access to modify the DNS settings for it on your registrar's website.

2. You will need to sign-up for an AWS account and be able to login with full administration privileges to LightSail (www.amazonlightsail.com).

3. If not already installed, you will need to have GIT on the pc that you will use to deploy your app. Installation instructions [here](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).


[<h6>back to table of contents</h6>](#table-of-contents)

<br>
<br>
##STEP #1
<h4>CREATE A NEW AWS UBUNTU SERVER INSTANCE</h4>



- Login to your LightSail account. Click the **CREATE INSTANCE** button. 

- Under 'Pick your instance image', click the **BASE OS** button. Then, choose **UBUNTU**.

![](./images/createInstanceBtn1.jpeg) 

- Scroll down and you will see: "You are using the default SSH key pair for connecting to your instance." We will use the **DEFAULT** SSH KEY. 

- Scroll down and CHOOSE YOUR INSTANCE PLAN. Click on the **$5/month** instance plan to begin with. You can always up-size to a larger instance later. 

- Scroll down and NAME YOUR INSTANCE. You can keep the default name, or give it any name you wish. I will name mine: **DOKKU-1**.

- Finally, hit the **CREATE** button.

In a few minutes, your new server instance will be ready.


[<h6>back to table of contents</h6>](#table-of-contents)

<br>
<br>
##STEP #2
<h4>CONNECT TO YOUR NEW SERVER AND UPDATE IT</h4>

![](./images/dokku1-connect.jpeg) 

- Find your new server instance under the Computing banner. On the right-hand side, you will see three vertical dots. Click them and select **CONNECT** from the list. A terminal emulator window should pop-up with a command prompt.

- In the command prompt, type: 

~~~
sudo apt update
~~~

Then:

~~~
sudo apt upgrade
~~~

You will be asked: **Do you want to continue? [Y/n]**. Type:
~~~
yes
~~~

This will install updates for your Ubuntu Linux server. This could take about 5 minutes or so. Be patient.

- Finally, type: 

~~~
sudo reboot
~~~

This will reboot the server and disconnect you from the terminal window. You can now close the terminal window. 

Your Ubuntu Linux server is now updated with the latest OS updates.


[<h6>back to table of contents</h6>](#table-of-contents)

<br>
<br>
##STEP #3
<h4>CREATE AND ASSIGN A STATIC IP </h4>

By default, your new server instance will get a new (dynamic) IP address everytime your server is rebooted. We don't want this. We want a static IP address.

- Click on the CREATE OTHER RESOURCES button and choose STATIC IP from the menu.

![](./images/createOther-staticIP.jpeg) 

- Scroll down to ATTACH TO AN INSTANCE. Select the instance you just created from the SELECT AN INSTANCE menu. Mine is: **DOKKU-1**. Yours will be whatever name you gave your instance. 

- (optional) Under NAME YOUR STATIC IP, you can enter in a custom name. I used the default name.

- Click the **CREATE** button.


[<h6>back to table of contents</h6>](#table-of-contents)

<br>
<br>
##STEP #4
<h4>ADD A NEW DNS ZONE</h4>
Next, we want to create a new DNS ZONE for our domain name.

- Click the CREATE OTHER RESOURCES button again, choose DNS ZONE from the menu.

![](./images/createOther-dnsZone.jpeg) 

- Enter your domain name in the box under ENTER THE DOMAIN NAME YOU HAVE REGISTERED. You do not need the "WWW." before your domain name or any DOT. For example: **mydomain.com**.

- Scroll down and click the **CREATE DNS ZONE** button.


[<h6>back to table of contents</h6>](#table-of-contents)

<br>
<br>
##STEP #5
<h4>CONFIGURE THE DNS ZONE</h4>
You should now be on the DETAILS page of your new DNS ZONE. Let's add some DNS Records to our new DNS ZONE.



![](./images/dnsZone-ARecords.jpeg) 

<br>
**First DNS Entry:**

- Click on the link-button:  **+ ADD ANOTHER**

~~~
TYPE: A
SUB-DOMAIN: MYDOMAIN.COM (NO WWW. OR DOT BEFORE NAME)
DESTINATION IP: (CHOOSE YOUR SERVER INSTANCE FROM THE MENU)
~~~
    
- Click **SAVE**.

<br>
**Second DNS Entry:**

- Click on the link-button:  **+ ADD ANOTHER**

~~~
TYPE: A
SUB-DOMAIN: WWW.MYDOMAIN.COM (NO WWW OR DOT BEFORE NAME)
DESTINATION IP: (CHOOSE YOUR SERVER INSTANCE FROM THE MENU)
~~~
    
- Click **SAVE**.

<br>
**Third DNS Entry:<br>Adding additional DNS entries for each new Dokku app:**

For each additional Dokku app that you add to your server, you will want to create a new A-RECORD for each new sub-domain.

For example, if you create a new Dokku app called: **'SUPERDOOPER'**, you will need to create the following DNS entry:

    TYPE: A
    SUB-DOMAIN: SUPERDOOPER.MYDOMAIN.COM
    DESTINATION IP: (CHOOSE YOUR SERVER INSTANCE FROM THE MENU)

- Click **SAVE**.


[<h6>back to table of contents</h6>](#table-of-contents)

<br>
<br>
##STEP #6
<h4>OPEN HTTPS PORT IN THE AWS FIREWALL</h4>

In order for web visitors to be able to visit your sites over HTTPS,
we need to open a port in the AWS firewall.


- Click on the AMAZON LIGHTSAIL logo in the upper-right-hand corner. This will take you back to the main screen.

- Find your new server instance. You will see three vertical dots. Click them and select **MANAGE** from the list.

- Click the **NETWORKING** link. Scroll down to the FIREWALL settings. You should see the following settings:

~~~
	APPLICATION			PROTOCOL		PORT RANGE

	SSH					TCP				22
	HTTP				TCP				80
~~~

- Click the button/link: **+ ADD ANOTHER**

- Under APPLICATION, select **HTTPS** from the menu. The PROTOCOL and PORT RANGE fields are set for you automatically.

- Click **SAVE** to lock in the new settings.


[<h6>back to table of contents</h6>](#table-of-contents)

<br>
<br>
##STEP #7
<h4>POINT YOUR DOMAIN NAME TO AWS</h4>

Now that we have created a static IP address and a DNS ZONE, we can configure the domain on your registrar's website to point to AWS.

- In a new brower window, you will need to login to your registrar's website. The registrar is the company that you used when you registered the domain name. My domain name was registered on GoDaddy.com, but your registrar should have similar settings.

- Once logged in, you will want to find the domain name that you want to point to AWS. Select it and look for settings similar to: MANAGE or MANAGE DNS SETTTINGS.

- Look for NAMESERVERS or USE CUSTOM NAMESERVERS. We need to enter in some addresses that will point this domain's DNS to AWS. We will get theses addresses from the AWS/LightSail website.

- Go back to the AWS/LightSail website. Click on the logo in the upper-right-hand corner so that you are on the main page. Sroll down to the DNS ZONE you created with your domain name. Click on the three vertical dots and choose MANAGE.

- Scroll down to the bottom of the page until you see NAMESERVERS. These are the namesevers that you will need to enter into your registrar's CUSTOM NAMESERVERS entry box. Enter all four of them. Be sure to hit SAVE.

Now, your domain name is pointed to AWS.


[<h6>back to table of contents</h6>](#table-of-contents)

<br>
<br>
##STEP #8
<h4>INSTALL DOKKU</h4>

- Click on the AMAZON LIGHTSAIL logo in the upper-right-hand corner. This will take you back to the main screen.

- Use default SSH key. Download key to your local machine and store it in: ~/.ssh/

- On LightSail, click on your new instance name. This will take you to MANAGE settings. 

- Assign the new instance a Static IP.

- Go to MANAGE

- Connect to new instance using SSH. ($ ssh -i ~/.ssh/AWS/LightsailDefaultPrivateKey.pem ubuntu@emveeoh.com).

- Run UPDATE and UPGRADE on Ubuntu to make the installation current. ($ sudo apt update && sudo apt upgrade).

- Install DOKKU with curl script found at: http://dokku.viewdocs.io/dokku


[<h6>back to table of contents</h6>](#table-of-contents)

<br>
<br>
##STEP #9
<h4>CREATE YOUR RSA PRIVATE/PUBLIC KEY PAIR FOR SSH CONNECTION TO DOKKU</h4>

These step-by-step directions were sourced from a Digital Ocean tutorial:
[Digital Ocean - RSA private/public key creation](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2) 

- On your local machine, run this terminal command: $ ssh-keygen -t rsa

- Enter file in which to save the key (/home/mvo/.ssh/id_rsa). Give it a name. Example: dokku1-key

- Pass phrase is optional. You will have to type this each time you connect. Leave it blank if you want faster login.

- Two files were created for you. Both have the same name, but one will have the extension .pub. Grab these files and put them in: ~/.ssh/

You now have the public/private key you need to SSH into your Dokku server.


[<h6>back to table of contents</h6>](#table-of-contents)

<br>
<br>
##STEP #10
<h4>ADD THE PUBLIC KEY TO DOKKU</h4>

- Get IP Address for new LightSail instance. Open this URL in browser. Should be a static IP.

- You should see: Dokku Setup and it should already have the PUBLIC KEY text pasted into the Public Key box. Erase any text that might appear before the text "ssh-rsa". This is not part of the key. 

- In the HOSTNAME box, enter in a domain name that you have registered.

- Check the box "Use virtualhost naming for apps".

- Click "Finish Setup". 

Your Dokku instance is now ready to receive site/app uploads.


[<h6>back to table of contents</h6>](#table-of-contents)

<br>
<br>
##STEP #11
<h4>xxxxxxxxxxxxxxxxxxxxxxx</h4>


[<h6>back to table of contents</h6>](#table-of-contents)

<br>
<br>
##STEP #12
<h4>xxxxxxxxxxxxxxxxxxxxxxx</h4>


[<h6>back to table of contents</h6>](#table-of-contents)

<br>
<br>
##STEP #13
<h4>xxxxxxxxxxxxxxxxxxxxxxx</h4>


[<h6>back to table of contents</h6>](#table-of-contents)

<br>
<br>
##STEP #14
<h4>xxxxxxxxxxxxxxxxxxxxxxx</h4>


[<h6>back to table of contents</h6>](#table-of-contents)

<br>
<br>
##STEP #15
<h4>xxxxxxxxxxxxxxxxxxxxxxx</h4>


[<h6>back to table of contents</h6>](#table-of-contents)

<br>
<br>
##STEP #16
<h4>xxxxxxxxxxxxxxxxxxxxxxx</h4>


[<h6>back to table of contents</h6>](#table-of-contents)

<br>
<br>
