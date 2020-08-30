# Protect websites with the power of SSH

> Originally Written: 2015-05-17

This only works on systems that you have control of. If you're on a shared hosting system this is not for you.

The basis of this trick is based on using PuTTY, an SSH connection, and a proxy application to tunnel your 
browsers internet connection through the web server.

## Requirements

* SSH access to the web server
* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/) (any other application that has SSH tunneling would also work)
* [Proxy SwitchySharp for Google Chrome](https://chrome.google.com/webstore/detail/proxy-switchysharp/dpplabbmogkhghncfbfdeeokoefdjegm)
* [Proxy Selector for Firefox](https://addons.mozilla.org/en-US/firefox/addon/proxy-selector/)

## PuTTY Setup


* **Connect to your web server via SSH** using PuTTY
* **Right click along the top title bar** of the PuTTY window
* Select **Change Settings**
* Along the left sidebar of items **go to Connection -> SSH -> Tunnels**
* In **Source port** enter **8081** or any port number of your choosing. 
    * VLC, Skype, and other various applications commonly use 8080 and it may cause conflicts when trying to bind.
* **Leave Destination blank**
* Select the **Dynamic radio selector**
* Leave the next row of **radio selectors on Auto**
* **Click Add**

## Browser Setup

### Google Chrome

* Ensure the Proxy Switchysharp extension or equivilent is installed
* Within Google Chrome **right click on the Proxy Switchysharp** icon in the top right corner and select **Options**
* Make sure you are on the **Proxy Profiles tab**
* Select **New Profile**
    * Profile Name: 127.0.0.1:8081
    * Manual Configuration
        * SOCKS Host: 127.0.0.1 Port 8081
    * Save
    * Close

Back in Google Chrome, left click on the Proxy Switchysharp icon in the top right and select 127.0.0.1:8081.

### Mozilla Firefox

* Ensure the Proxy Selector add-on or equivilent is installed
* Within Firefox click on the addon icon in the top right and select **Manage Proxies**
* Click **Add**
    * Proxy Label: 127.0.0.1:8081
    * Manual proxy configuration
        * SOCKS Host: 127.0.0.1 Port 8081
* Click **OK**

Back in Firefox, click on the Proxy addon icon and select 127.0.0.1:8081.


## Confirming Browser Setup

Once you have done that head over to [http://whatismyip.org/](http://whatismyip.org/) and ensure that the IP address it returns is 
the same as your web servers IP address.

* If the IP address is NOT the same as your web server, the proxy is not working.
* If you get a message that you can no longer connect to the internet, go back into Proxy options and ensure you put 
    in the correct port number that you use during the PuTTY setup phase.

If your IP address does match your web servers IP address, congratulations the proxy portion is complete.

## Now What?

Well from here we need to edit your web server software to only allow connections from your web servers IP address. 
That way, the only way to access the portion of your website under the proxy protection is via connecting to your web 
server directly and accessing it through the proxy.

In the examples below I will go off the basis that 54.56.47.5 is my web servers IP address.

### Apache 

Apache is fairly straight forward, even a slight .htaccess modification would do.

```
Order deny,allow
Deny from all
Allow from 54.56.47.5
```

You can also protect a subfolder within a public folder. For example i want to protect a folder named admin in 
the root of the main website

```
<Directory /var/www/public_html/admin/>
    RewriteEngine On
    RewriteCond %{REQUEST_URI} ^(.*)?administrator(.*)$
    RewriteCond %{REMOTE_ADDR} !^54\.56\.47\.5$
    RewriteCond %{HTTP:X-Forwarded-For} !^54\.56\.47\.5$
    RewriteRule ^(.*)$ - [R=403,L]
</Directory>
```

Restart Apache and try to access the folder you have now protected. If you are still connected to the ssh server 
with proxy on you should have access. If you go to the proxy addon and go back to System Proxy (or Direct Connection) 
and attempt to view the same page, you should get a 403 denied error, or some type of error depending on server 
configuration.

If you still have access to the page with the proxy turned off, something isn't right with your apache configuration. 
Have a look at the code above and look further into what changes you have made.

## WordPress protection

This is the code required for Apache to protect a WordPress installation

```
RewriteEngine on
RewriteCond %{REQUEST_URI} ^(.*)?wp-login\.php(.*)$ [OR]
RewriteCond %{REQUEST_URI} ^(.*)?wp-admin(.*)$
RewriteCond %{REMOTE_ADDR} !^54\.56\.47\.5$
RewriteCond %{HTTP:X-Forwarded-For} !^54\.56\.47\.5$
RewriteRule ^(.*)$ - [R=403,L]
```
