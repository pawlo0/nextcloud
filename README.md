# Install Nextcloud on Ubuntu

## 1. Installing Nextcloud
I'll be installing Nextcloud using Snap

`sudo snap install nextcloud`

Check the instalation (optional)

`snap changes nextclould`

## 2. Configuring an Admin account
This can be done through the web interface, but can also easily be done in the command line in order to avoid a small windows of time where the administrator registration page would be accessible to anyone visiting your server's IP address or domain name.
To configure Nextcloud with a new administrator account, use the below

`sudo nextcloud.manual-install user password`

## 3. Adjusting the trusted domains
When installing from the command line, Nextcloud restricts the host names that the instance respond to. By default, it will only respond to requests made to the "localhost". As we'll be accessing through a server's domain name, we'll need to adjust this to accept these type of requests.

To view the current settings:

`sudo nextcloud.occ config:system:get trusted_domains

If you just installed, only `localhost` should be present as the first value in the array.
We can add an entry for our server's domain name or IP address by typing:

`sudo nextcloud.occ config.system:set trusted_domains 1 --value=example.com`

If we query the trusted domains again, we will see that we now have to entries.

## 4. Port fowarding
In order to access Nextcloud from external network, need to configure port fowarding in the router. 

Go to your router and add the IP of the server to be accessible from the outside. On my router it is called "port mapping". Select TCP/UDP for ports 80 and 443.

## 5. Securing the Nextcloud Web Interface with SSL
We'll be setting up SSL with Let's Encrypt.
Start by opening the ports in the firewall that Let's Encrypt uses to validae doamin ownership. This will make your Nextcloud login page publicly accessible, but since we already have an admin account configured, no one will be able to hijack the installation unlees they know the user and pass.

`sudo ufw allow 80,443/tcp`

Next, request a Let's Encrypt certificate by typing:

`sudo nextcloud.enable-https lets-encrypt`

It will ask that certain conditions must be in place. Type `y` to continue.

Next, you will be asked to provide an email address to use for recovery operations. Enter your email and press `Enter` to continue.

Finally, enter the domain name associated with your Nextcloud server `example.com`

If everything went well, the internal Apache instance will be restarted to implement SSL.

## 6. Logging in to the Nextcloud Web interface
Visit your server's domain name in your browser.

Since you have already configure an admin account through the command line, you'll be taken to the login page. Enter the credentials you created for the admin user. Click Log In. Your instalation is now complete.

## 7. Manually add files directly into data folder
In case you're installing this for recovering a previous install and you have the files backed up somewhere, here instructions to manually add them into the data folder. 
Locate the data folder, as this was a snap install, it should be somewhere in this folder `/var/snap/nextcloud/common/nextcliud/data/user/files`
In order to access this filder need to be root:

`sudo su`

Copy the file you from the backup drive. Once done type `exit` to drop root user. Finally indexing the files so nextcloud recognizes it and add them to database:

`sudo nextcloud.occ files:scan --all -vvv`

