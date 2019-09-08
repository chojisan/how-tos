## Change Mac Address Randomly

openssl rand -hex 6 | sed 's/\(..\)/\1:/g; s/.$//' | xargs sudo ifconfig en0 ether

## How to install Homebrew

1. Open terminal
2. run command on terminal. this will install xcode command line tool
```
xcode-select --install

sudo xcode-select --switch /Library/Developer/CommandLineTools
```
3.
4. 
```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

or

ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
## How to install Git

```
brew install git
git config user.name "name"
git config user.email "email"
```

## How to install Composer

## How to install Vagrant

## How to install Virtualbox

## How to install Docker

## How to install Apache

The latest macOS 10.12 Sierra comes with Apache 2.4 pre-installed, however, it is no longer a simple task to use this version with Homebrew because Apple has removed some required scripts in this release. However, the solution is to install Apache 2.4 via Homebrew and then configure it to run on the standard ports (80/443).

If you already have the built-in Apache running, it will need to be shutdown first, and any auto-loading scripts removed. It really doesn't hurt to just run all these commands in order - even if it's a fresh installation:

```
$ sudo apachectl stop
$ sudo launchctl unload -w /System/Library/LaunchDaemons/org.apache.httpd.plist 2>/dev/null
$ brew install httpd24 --with-privileged-ports --with-http2
```


This step takes a little while as it builds Apache from source. Upon completion you should see a message like:
🍺  /usr/local/Cellar/httpd24/2.4.23_2: 212 files, 4.4M, built in 1 minute 45 seconds

This is important because you will need that path in the next step. In this example the path was /usr/local/Cellar/httpd24/2.4.23_2. If you get a newer version, simply use that path in the next line:

$ sudo cp -v /usr/local/Cellar/httpd24/2.4.23_2/homebrew.mxcl.httpd24.plist /Library/LaunchDaemons
$ sudo chown -v root:wheel /Library/LaunchDaemons/homebrew.mxcl.httpd24.plist
$ sudo chmod -v 644 /Library/LaunchDaemons/homebrew.mxcl.httpd24.plist
$ sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.httpd24.plist

You now have installed Homebrew's Apache, and configured it to auto-start with a privileged account. It should already be running, so you can try to reach your server in a browser by pointing it at your localhost, you should see a simple header that says "It works!".

Troubleshooting Tips

If you get a message that the browser can't connect to the server, first check to ensure the server is up.
$ ps -aef | grep httpd
You should see a few httpd processes if Apache is up and running.

Try to restart Apache with:
$ sudo apachectl -k restart

You can watch the Apache error log in a new Terminal tab/window during a restart to see if anything is invalid or causing a problem:
$ tail -f /usr/local/var/log/apache2/error_log

If that doesn't work, check to ensure you have Listen: 80 in your /usr/local/etc/apache2/2.4/httpd.conf configuration file.

Apache is controlled via the apachectl command so some useful commands to use are:
$ sudo apachectl start
$ sudo apachectl stop
$ sudo apachectl -k restart

The -k will force a restart immediately rather than asking politely to restart when apache is good and ready
Apache Configuration

Now that we have a working web server, we will want to do is make some configuration changes so it works better as a local development server.

Firs we'll configure it to use the to change the document root for Apache. This is the folder where Apache looks to serve file from. By default, the document root is configured as /Library/WebServer/Documents. As this is a development machine, let's assume we want to change the document root to point to a folder in our own home directory. To do this, we will need to edit Apache's configuration file.
/usr/local/etc/apache2/2.4/httpd.conf

For simplicity we'll use the built-in TextEditor application to make all our edits. You can launch this from the Terminal by using the open -e command followed by the path to the file:
$ open -e /usr/local/etc/apache2/2.4/httpd.conf

Search for the term DocumentRoot, and you should see the following line:
DocumentRoot "/usr/local/var/www/htdocs"

Change this to point to your user directory where your_user is the name of your user account:
DocumentRoot /Users/your_user/Sites
Y
ou also need to change the <Directory> tag reference right below the DocumentRoot line. This should also be changed to point to your new document root also:
<Directory /Users/your_user/Sites>

We removed the optional quotes around the directory paths as TextEdit will probably try to convert those to smart-quotes and that will result in a Syntax error when you try to restart Apache. Even if you edit around the quotes and leave them where they are, saving the document may result in their conversion and cause an error.

In that same <Directory> block you will find an AllowOverride setting, this should be changed as follows:
# AllowOverride controls what directives may be placed in .htaccess files.
# It can be "All", "None", or any combination of the keywords:
#   AllowOverride FileInfo AuthConfig Limit
#
AllowOverride All

Also we should now enable mod_rewrite which is commented out by default. Search for mod_rewrite.soand uncomment the line by removing the leading #:
LoadModule rewrite_module libexec/mod_rewrite.so
User & Group

Now we have the Apache configuration pointing to a Sites folder in our home directory. One problem still exists, however. By default, apache runs as the user daemon and group daemon. This will cause permission problems when trying to access files in our home directory. About a third of the way down the httpd.conf file there are two settings to set the User and Group Apache will run under. Change these to match your user account (replace your_user with your real username), with a group of staff:
User your_user
Group staff
Sites Folder

Now, you need to create a Sites folder in the root of your home directory. You can do this in your terminal, or in Finder. In this new Sites folder create a simple index.html and put some dummy content in it like: <h1>My User Web Root</h1>.
$ mkdir ~/Sites
$ echo "<h1>My User Web Root</h1>" > ~/Sites/index.html
Restart apache to ensure your configuration changes have taken effect:
$ sudo apachectl -k restart

If you receive an error upon restarting Apache, try removing the quotes around the DocumentRoot and Directory designations we set up earlier. Any error involving apr_sockaddr_info_get() may be resolved by searching your httpd.conf file for the line ServerName localhost and removing the #. Once you have done this, try resetting Apache again.
Pointing your browser to http://localhost should display your new message. If you have that working, we can move on!

PHP Installation
We will proceed by installing PHP 5.5, PHP 5.6, PHP 7.0, and PHP 7.1 and using a simple script to switch between them as we need.
You can get a full list of available options to include by typing brew options php55, for example. In this example we are just including Apache support via --with-apache to build the required PHP modules for Apache.
$ brew install php55 --with-apache
$ brew unlink php55
$ brew install php56 --with-apache
$ brew unlink php56
$ brew install php70 --with-apache
$ brew unlink php70
$ brew install php71 --with-apache
This may take some time as your computer is actually compiling PHP from source.
You must reinstall each PHP version with reinstall command rather than install if you have previously installed PHP through Brew.
Also, you may have the need to tweak configuration settings of PHP to your needs. A common thing to change is the memory setting, or the date.timezone configuration. The php.ini files for each version of PHP are located in the following directories:
/usr/local/etc/php/5.5/php.ini
/usr/local/etc/php/5.6/php.ini
/usr/local/etc/php/7.0/php.ini
/usr/local/etc/php/7.1/php.ini
PHP 7.1 is currently in a Release Candidate state at the time of this writing, but it's expected to be released before the end of 2016. You can install as many or as few PHP versions as you like, it's nice to have options right?
Apache PHP Setup - Part 1
You have successfully installed your PHP versions, but we need to tell Apache to use them. You will again need to edit the /usr/local/etc/apache2/2.4/httpd.conf file and search for #LoadModule php5_module.
You will notice that each PHP version added a LoadModule entry, however these are all pointing to very specific versions. We can replace these with some more generic paths (exact versions may differ):
LoadModule php5_module        /usr/local/Cellar/php55/5.5.38_11/libexec/apache2/libphp5.so
LoadModule php5_module        /usr/local/Cellar/php56/5.6.26_3/libexec/apache2/libphp5.so
LoadModule php7_module        /usr/local/Cellar/php70/7.0.11_5/libexec/apache2/libphp7.so
LoadModule php7_module        /usr/local/Cellar/php71/7.1.0-rc.3_8/libexec/apache2/libphp7.so
Modify the paths as follows:
LoadModule php5_module    /usr/local/opt/php55/libexec/apache2/libphp5.so
LoadModule php5_module    /usr/local/opt/php56/libexec/apache2/libphp5.so
LoadModule php7_module    /usr/local/opt/php70/libexec/apache2/libphp7.so
LoadModule php7_module    /usr/local/opt/php71/libexec/apache2/libphp7.so

We can only have one module processing PHP at a time, so for now, comment out all but the php56 entry:
#LoadModule php5_module    /usr/local/opt/php55/libexec/apache2/libphp5.so
LoadModule php5_module    /usr/local/opt/php56/libexec/apache2/libphp5.so
#LoadModule php7_module    /usr/local/opt/php70/libexec/apache2/libphp7.so
#LoadModule php7_module    /usr/local/opt/php71/libexec/apache2/libphp7.so
This will tell Apache to use PHP 5.6 to handle PHP requests. (We will add the ability to switch PHP versions later).
Also you must set the Directory Indexes for PHP explicitly, so search for this block:
<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>
and replace it with this:
<IfModule dir_module>
    DirectoryIndex index.php index.html
</IfModule>

<FilesMatch \.php$>
    SetHandler application/x-httpd-php
</FilesMatch>
Save the file and restart Apache again, now that we have installed PHP:
$ sudo apachectl -k restart
Validating PHP Installation
The best way to test if PHP is installed and running as expected is to make use of phpinfo(). This is not something you want to leave on a production machine, but it's invaluable in a development environment.
Simply create a file called info.php in your Sites/ folder you created earlier. In that file, just enter the line:
<?php phpinfo();
Point your browser to http://localhost/info.php and you should see a shiny PHP information page:

If you see a similar phpinfo result, congratulations! You now have Apache and PHP running successfully. You can test the other PHP versions by commenting the LoadModule ... php56 ... entry and uncommenting one of the other ones. Then simply restart apache and reload the same page.
PHP Switcher Script
We hard-coded Apache to use PHP 5.6, but we really want to be able to switch between versions. Luckily, some industrious individuals have already done the hard work for us and written a very handy little PHP switcher script.
We will install the sphp script into brew's standard /usr/local/bin:
$ curl -L https://gist.github.com/w00fz/142b6b19750ea6979137b963df959d11/raw > /usr/local/bin/sphp
$ chmod +x /usr/local/bin/sphp
Check Your Path
Homebrew should have added its preferred /usr/local/bin and /usr/local/sbin to your path as part of its installation process. Quickly test this by typing:
$ echo $PATH
/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
If you don't see this, you might need to add these manually to your path. Depending on your shell your using, you may need to add this line to ~/.profile, ~/.bash_profile, or ~/.zshrc. We will assume you are using the default bash shell, so add this line to a your .profile (create it if it doesn't exist) file at the root of your user directory:
export PATH=/usr/local/bin:/usr/local/sbin:$PATH
At this point, I strongly recommend closing ALL your terminal tabs and windows. This will mean opening a new terminal to continue with the next step. This is strongly recommended because some really strange path issues can arise with existing terminals (trust me, I have seen it!).
Apache PHP Setup - Part 2
Although we configured Apache to use PHP earlier, we now need to change this again to point the PHP module to a location used by the PHP switcher script. You will again need to edit the /usr/local/etc/apache2/2.4/httpd.conf file and search for the LoadModule php text that you edited earlier.
It's quite IMPORTANT at this stage to fully stop your Apache sever, and start it again. Do not just restart it!
$ sudo apachectl -k stop
$ sudo apachectl start
Then, you must replace the current block of LoadModule entries (including any commented out ones):
#LoadModule php5_module    /usr/local/opt/php55/libexec/apache2/libphp5.so
LoadModule php5_module    /usr/local/opt/php56/libexec/apache2/libphp5.so
#LoadModule php7_module    /usr/local/opt/php70/libexec/apache2/libphp7.so
#LoadModule php7_module    /usr/local/opt/php71/libexec/apache2/libphp7.so
with this:
# Brew PHP LoadModule for `sphp` switcher
LoadModule php5_module /usr/local/lib/libphp5.so
#LoadModule php7_module /usr/local/lib/libphp7.so
The Commented out line for php7_module is important and required if you are installing PHP 7.0 or 7.1 as it uses a unique PHP module handler. The script will automatically handle uncommenting and commenting the appropriate PHP module.
Testing the PHP Switching
After you have completed these steps, you should be able to switch your PHP version by using the command sphp followed by a two digit value for the PHP version:
$ sphp 55
You will probably have to enter your administrator password, and it should give you some feedback:
$ sphp 55
PHP version 55 found
Unlinking old binaries...
Linking new binaries...
Linking /usr/local/Cellar/php55/5.5.38_11... 17 symlinks created
Linking new modphp addon...
Fixing LoadModule...
Updating version file...
Restarting homebrew Apache...
Restarting non-root homebrew Apache...
Done.

PHP 5.5.38 (cli) (built: Oct  4 2016 15:48:32)
Copyright (c) 1997-2015 The PHP Group
Zend Engine v2.5.0, Copyright (c) 1998-2015 Zend Technologies
Test to see if your Apache is now running PHP 5.5 by again pointing your browser to http://localhost/info.php. With a little luck, you should see something like this:

Updating PHP and other Brew Packages
Brew makes it super easy to update PHP and the other packages you install. The first step is to update Brew so that it gets a list of available updates:
$ brew update
This will spit out a list of available updates, and any deleted formulas. To upgrade the packages simply type:
$ brew upgrade
You will need to switch to each of your installed PHP versions and run update again to get updates for each PHP version and ensure you are running the version of PHP you intend.
Activating Specific/Latest PHP Versions
Due to the way our PHP linking is set up, only one version of PHP is linked at a time, only the current activeversion of PHP will be updated to the latest version. You can see the current active version by typing:
$ php -v
And you can see the specific versions of PHP available by typing:
$ brew info php55
homebrew/php/php55: stable 5.5.30 (bottled), HEAD
https://php.net
Conflicts with: php53, php54, php56, php70
/usr/local/Cellar/php55/5.5.28 (498 files, 51M)
  Poured from bottle
/usr/local/Cellar/php55/5.5.29_3 (327 files, 49M)
  Poured from bottle
/usr/local/Cellar/php55/5.5.30 (327 files, 49M)
  Poured from bottle
  ...
You will see all the versions of PHP 5.5 brew has available and then you can switch to a specific version by typing:
$ brew switch php55 5.5.28
OK, that wraps up Part 1 of this 3 part series You now have a fully functional Apache 2.4 installation with a quick-and-easy way to toggle between PHP 5.5, 5.6, 7.0, and 7.1. Check out Part 2 to find out how to setup your environment with MySQL, Virtual Hosts, APC caching, YAML, and Xdebug. Also take a gander at Part 3 to find out how to setup SSL for your Apache Virtual Hosts.
————————————————

PHP update
Default is v5.5
use home-brew php

I use this: https://github.com/Homebrew/homebrew-php
The command is:
$ xcode-select --install

$ brew tap homebrew/dupes
$ brew tap homebrew/versions
$ brew tap homebrew/homebrew-php

$ brew options php56
$ brew install php56
Then config in your .bash_profile or .bashrc
# Homebrew PHP CLI
export PATH="$(brew --prefix homebrew/php/php56)/bin:$PATH"



http://php-osx.liip.ch/

One Line Installation
All you need to do to install the whole thing is opening up a Shell and execute the following line:
PHP 7.1 (release canditate)
curl -s http://php-osx.liip.ch/install.sh | bash -s 7.1
PHP 7.0 (Current stable)
curl -s http://php-osx.liip.ch/install.sh | bash -s 7.0
PHP 5.6 (Current stable)
curl -s http://php-osx.liip.ch/install.sh | bash -s 5.6
PHP 5.5 (Old stable)
curl -s http://php-osx.liip.ch/install.sh | bash -s 5.5
PHP 5.4 (End of life)
curl -s http://php-osx.liip.ch/install.sh | bash -s 5.4
PHP 5.3 (End of life)
curl -s http://php-osx.liip.ch/install.sh | bash -s 5.3
It will ask you for your password. We install the packager in /usr/local/packer and PHP into /usr/local/php5 and for this, we need your password. We don't do anything bad with it.
Frequently Asked Questions
Why does php -v on the command line still show my old version?
php-osx doesn't overwrite the php binaries installed by Apple, but installs everything in /usr/local/php5. The new php binary is therefore in /usr/local/php5/bin/php.
You can also adjust your PATH do include that directory, eg. write into your ~/.profile file the following
export PATH=/usr/local/php5/bin:$PATH

Why is Europe/Zurich the default timezone? Where to change that?
Because we are based in Zurich (and Fribourg and Lausanne). Change the file in/usr/local/php5/php.d/99-liip-developer.ini to adjust that.
It doesn't take my php.ini values
We introduced our own ini file to be able to easily overwrite the standard values with our recommended values. You can find it in /usr/local/php5/php.d/99-liip-developer.ini
As this is read last, it will overwrite values defined in other files. You can also just delete the file.
Why is the MySQL Socket configured to use /tmp/mysql.sock and how to change that?
If you download the native binary from mysql.com, that server uses /tmp/mysql.sock as its socket location (see this). Therefore we choose that value as default.
You can change that value in /usr/local/php5/php.d/99-liip-developer.ini
How to install pecl extensions?
sudo /usr/local/php5/bin/pecl install pecl_http
Where is the memcached daemon?
We started not including the memcached daemon anymore. But you can install it by hand with
 sudo /usr/local/packager/packager.py install tools-memcached
How can I help?
This project is hosted on github here https://github.com/liip/build-entropy-php/. The website itself and install.sh is hosted on https://github.com/liip/php-osx/. It's all Open Source, so feel free to fork it and send Pull Requests, if you want to change/fix something.
I have more questions. Where to put them?
You have basically three options:
* Open a ticket at https://github.com/liip/php-osx/issues
* Write a tweet, we monitor that and are reachable at @php_osx
* Write on Stack Overflow with the tags "php osx". We do monitor that, as well
Additional important notes
32 bit only systems (no workie)
It only works on a 64-bit-capable system. This means not on very old hardware with Intel Core Duo and Intel Core Solo processors (see http://support.apple.com/kb/ht3696 for the overview). If anyone wants to make it work on those systems too, please tell us how (We tried, but didn't came up with anything running in a decent amount of time)
php.ini
The php.ini is located in /usr/local/php5/lib/php.ini, additional config files are located in /usr/local/php5/php.d/. The php.ini is based on php.ini-development
Liip php.ini
The Liip php.ini for development is located in /usr/local/php5/php.d/99-liip-developer.ini.
Re-installs into new directory
All installed files go in a new directory with every update, you have to manually maintain/merge your changes from the old directory! (like pear installs)
apc, twig and xslcache
apc, twig and xslcache need to be enabled manually in /usr/local/php5/php.d/50-extension-$name.ini.
xdebug
xdebug settings needs to be configured (idekey etc) manually in /usr/local/php5/php.d/50-extension-xdebug.ini.
apache error with ulimit
If you are seeing "/usr/sbin/apachectl: line 82: ulimit: open files: cannot modify limit: Invalid argument" when loading apache, here is a fix
More history and all that
See this blog post for even more background info.
(Alternative) Installation by "hand"
If you don't trust us, here's the code of install.sh, which you can run by yourself (instead of the one-liner above):
curl -s -o /tmp/packager.tgz http://php-osx.liip.ch/packager/packager.tgz
sudo tar -C /usr/local  -xzf /tmp/packager.tgz
sudo /usr/local/packager/packager.py install 5.4-frontenddev
If you already have installed the packager and just want to update the PHP package, you can do it with the following line.
sudo /usr/local/packager/packager.py install tools-frontenddev

Included extensions
bcmath bz2 calendar Core ctype curl date dom dtrace ereg exif fileinfo filter ftp gd gettext hash iconv imap intl json ldap libxml mbstring mcrypt memcache memcached mhash mongo mssql mysql mysqli mysqlnd OAuth odbc openssl pcntl pcre PDO pdo_dblib pdo_mysql pdo_pgsql pdo_sqlite pgsql Phar posix Reflection session shmop SimpleXML soap sockets solr SPL SQLite sqlite3 standard sysvmsg sysvsem sysvshm tidy tokenizer wddx xdebug xhprof xml xmlreader xmlrpc xmlwriter xsl zip zlib Xdebug
available but disabled by default: apc, xslcache, twig, uploadprogress
See this output of phpinfo() for more info.
Uninstall
Change in /etc/apache2/httpd.conf
LoadModule php5_module /usr/local/php5/libphp5.so
to
LoadModule php5_module        libexec/apache2/libphp5.so
also delete the file +php-osx.conf and +entropy-php.conf in /etc/apache2/other
and optionally remove /usr/local/php5-*


—————————————————

Part 2: macOS 10.12 Sierra Web Development Environment
This is an updated version of our prior OS X development series. The newly released macOS 10.12 Sierra requires significant changes compared to prior releases, necessitating a thorough revamp in the process. The main change is why now use Homebrew's Apache, rather than the built-in version, but it should continue to work on prior OS X versions.
In Part 1 of this 3-part series, we covered configuring Apache on macOS to work better with your local user account, as well as the installation process for installing multiple versions of PHP.
In this Part 2, we will cover installing MySQL, Virtual Hosts, APC caching, YAML, and Xdebug. After finishing this tutorial, be sure to check out how to enable SSL in Part 3 of the series.
This guide is intended for experienced web developers. If you are a beginner developer, you will be better served using MAMP or MAMP Pro.

MySQL
Although not required for development of Grav, there are times you definitely need an installation of MySQL. In the original guide, we used the Oracle MySQL installation package. However, we now have switched to MariaDBwhich is a drop-in replacement for MySQL and is easily installed and updated with Brew. Detailed information on the HomeBrew installation process can be found on the mariadb.org site but the essentials are as follows:
Install MariaDB with Brew:
$ brew install mariadb
$ mysql_install_db
After a successful installation, you can start the server:
$ mysql.server start
You should get some positive feedback on that action:
Starting MySQL
. SUCCESS!
Download SequelPro and install it. (it's awesome and free!). You should be able to automatically create a new connection via the Socket option without changing any settings.

It is advisable to change the MySQL server password and secure your installation. The simplest way to do this is to use the provided script:
$ /usr/local/bin/mysql_secure_installation
Just answer the questions and fill them in as is appropriate for your environment.
If you need to stop the server, you can use the simple command:
$ mysql.server stop
Apache Virtual Hosts
A very handy development option is to have multiple virtual hosts set up for you various projects. This means that you can set up names such as grav.mydomain.com which point to your Grav setup, or project-x.mydomain.com for a project-specific URL.
Apache generally performs name-based matching, so you don't need to configure multiple IP addresses. Detailed information can be found on the apache.org site.
Apache already comes preconfigured to support this behavior but it is not enabled. First you will need to uncomment the following lines in your /usr/local/etc/apache2/2.4/httpd.conf file:
LoadModule vhost_alias_module libexec/mod_vhost_alias.so
and:
# Virtual hosts
Include /usr/local/etc/apache2/2.4/extra/httpd-vhosts.conf
Then you can edit this referenced file and configure it to your needs:
$ open -e /usr/local/etc/apache2/2.4/extra/httpd-vhosts.conf
This file has some instructions already but the important thing to remember is that these rules are matched in order. When you set up virtual hosts, you will lose your older document root, so you will need to add back support for that first as a virtual host.
<VirtualHost *:80>
    DocumentRoot "/Users/your_user/Sites"
    ServerName localhost
</VirtualHost>

<VirtualHost *:80>
    DocumentRoot "/Users/your_user/Sites/grav-admin"
    ServerName grav-admin.dev
</VirtualHost>
As you set up your .dev virtual hosts, you may receive a warning such as Warning: DocumentRoot [/Users/your_user/Sites/grav-admin] does not exist when restarting Apache. This just lets you know that the source directory listed for your virtual hosts is not present on the drive. It's an issue that can be resolved by editing this file with the corrected DocumentRoot.
Dnsmasq
In the example virtualhost we setup above, we defined a ServerName of grav-admin.dev. This by default will not resolve to your local machine, but it's often very useful to be able to setup various virtual hosts for development purposes. You can do this by manually adding entries to /etc/hosts ever time, or you can install and configure Dnsmasq to automatically handle wildcard *.dev names and forward all of them to localhost (127.0.0.1).
First we install it with brew:
$ brew install dnsmasq
Then we setup *.dev hosts:
$ echo 'address=/.dev/127.0.0.1' > /usr/local/etc/dnsmasq.conf
Start it and ensure it auto-starts on reboot in the future:
$ sudo brew services start dnsmasq
And lastly, add it to the resolvers:
$ sudo mkdir -v /etc/resolver
$ sudo bash -c 'echo "nameserver 127.0.0.1" > /etc/resolver/dev'
Now you can test it out by pinging some bogus .dev name:
ping bogus.dev
PING bogus.dev (127.0.0.1): 56 data bytes
64 bytes from 127.0.0.1: icmp_seq=0 ttl=64 time=0.044 ms
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.118 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.045 ms
Voila! we have successfully setup wildcard forwarding of all *.dev DNS names to localhost.
APC Cache
Caching in PHP is a big part of the performance equation. There are two types of caching typically available, and both have a big impact on speed and performance.
The first type of cache is called an opcode cache, and this is what takes your PHP script and compiles it for faster execution. This alone can typically result in a 3X speed increase!.
The second type of cache is a user cache, and this is a data-store that PHP can use to quickly store and retrieve data from. These typically run in memory which means they are transient, but very fast.
For PHP 5.5 and newer runs best with Zend OPcache by default but you can still use APCu Cache as a data store.
Install OPcache and APCu
Switch to PHP 5.5 mode, then run the following brew commands:
$ sphp 55
$ brew install php55-opcache
$ brew install php55-apcu
If you have installed PHP 5.6, you will need to repeat the process:
$ sphp 56
$ brew install php56-opcache
$ brew install php56-apcu
If you have installed PHP 7.0, you will need to repeat the process:
$ sphp 70
$ brew install php70-opcache
$ brew install php70-apcu
And lastly, for PHP 7.1:
$ sphp 71
$ brew install php71-opcache
$ brew install php71-apcu
Restart Apache with the standard sudo apachectl -k restart command to pick up your changes.
Point your browser to http://localhost/info.php and ensure you see a reference to Zend OPcache in the Zend Engine block.

You actually have quite a few options when it comes to caching. There are also brews available for XCache, Memcache. To find an exhaustive list of all the available packages simply type:
$ brew search php55
or
$ brew search php56
or
$ brew search php70
or
$ brew search php71
YAML
With recent versions of Grav, we now make use of the native PECL YAML library that allow YAML processing to be done by highly efficient libYAML C library rather than by they Symfony PHP library. This can result in a 5X improvement in YAML processing times! Luckily this is a simple process to install for any PHP version:
For PHP 5.5:
sphp 55
brew install php55-yaml
and for PHP 5.6:
sphp 56
brew install php56-yaml
and for PHP 7.0:
sphp 70
brew install --HEAD homebrew/php/php70-yaml
and for PHP 7.1:
sphp 71
brew install php71-yaml
Xdebug
One of the most important aspects of any kind of development is the ability to debug and fix your code. PHP comes with limited support to dump variables or log to a file, but for more complex situations you need something more powerful. Xdebug provides is a debugging and profiling extension for PHP that provides an HTML-friendly output for the var_dump() method that improves the readability of the default version.
It also provides other useful dumping methods as well as displaying stack traces. One of the best features however, is the ability to remote debug your code. This means you can set breakpoints, and step through your PHP code inspecting as you go. Full documentation on Xdebug contains extensive information about all the functionality available.
Installation of Xdebug is just as easy as the previous installation of APC or YAML:
For PHP 5.5:
sphp 55
brew install php55-xdebug
and for PHP 5.6:
sphp 56
brew install php56-xdebug
and for PHP 7.0:
sphp 70
brew install php70-xdebug
and for PHP 7.1:
sphp 71
brew install php71-xdebug
Again, you will need to use the sudo apachectl -k restart command to pick up your changes. You should also check the http://localhost/info.php to ensure that Xdebug information is displayed:

W00fz created a great tool for quickly enabling/disabling xdebug. Install this with brew:
$ brew install xdebug-osx
You should now be all set with a Rockin' PHP development environment! To find out how to enable SSL on Apache, check out Part 3 in the series.
NOTE: The brew installation actually creates configuration files in /usr/local/etc/php/5.5/conf.d, /usr/local/etc/php/5.6/conf.d, and /usr/local/etc/php/7.0/conf.d respectively. If you want to uninstall a PHP extension, simply rename the .ini file to .ini.bak and restart apache. Alternatively, you can simply use brew to uninstall it, and reinstall it again when you need it.



Part 3: macOS 10.12 Sierra Web Development Environment
This is an updated version of our prior OS X development series. The newly released macOS 10.12 Sierra requires significant changes compared to prior releases, necessitating a thorough revamp in the process. The main change is why now use Homebrew's Apache, rather than the built-in version, but it should continue to work on prior OS X versions.
In Part 1 of this 2-part series, we covered configuring Apache on macOS Sierra 10.12 to work better with your local user account, as well as the installation process for installing multiple versions of PHP. In Part 2, we covered installing MySQL, Virtual Hosts, APC caching, YAML, and Xdebug.
In this Part 3, we will cover getting your site setup with SSL support for this setup.
This guide is intended for experienced web developers. If you are a beginner developer, you will be better served using MAMP or MAMP Pro.
SSL
It is often important to be able to test your local site setup under SSL (e.g. https://yoursite.com). There are a few steps that are needed to accomplish this with your Homebrew-based Apache setup. The first step is to make some modifications to your httpd.conf:
$ open -e /usr/local/etc/apache2/2.4/httpd.conf
In this file you should uncomment both the socache_shmcb_module, ssl_module, and also the include for the httpd-ssl.conf by removing the leading # symbol on those lines:
LoadModule socache_shmcb_module libexec/mod_socache_shmcb.so
...
LoadModule ssl_module libexec/mod_ssl.so
...
Include /usr/local/etc/apache2/2.4/extra/httpd-ssl.conf
After saving this file, you should then open up your /usr/local/etc/apache2/2.4/extra/httpd-vhosts.conf to add appropriate SSL based virtual hosts.
$ open -e /usr/local/etc/apache2/2.4/extra/httpd-vhosts.conf
Here you can create a VirtualHost entry for each virtual host that you wish to provide SSL support for.
<VirtualHost *:443>
    DocumentRoot "/Users/your_user/Sites"
    ServerName localhost
    SSLEngine on
    SSLCertificateFile "/usr/local/etc/apache2/2.4/server.crt"
    SSLCertificateKeyFile "/usr/local/etc/apache2/2.4/server.key"
</VirtualHost>
In this example we have created the VirtualHost for localhost, but it could be any of your existing or even a new VirtualHost. The important parts are the the 443 port, along with SSLEngine on and the SSLCertificateFile and SSLCertificateKeyFile entries that point to the certificate we now need to generate.
Certificates
To get this all to work with Apache, we need to create a self-signed certificate that we have already referenced in the VirtualHost definition.
The following commands will often prompt you for information regarding the certificates. You should fill these in with sensible values, however, the Common Name should match the ServerName entry in your httpd-vhosts.conf file you just added.
First generate a key and certificate:
$ cd /usr/local/etc/apache2/2.4
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout server.key -out server.crt
Then all you need to do now is double check your Apache configuration syntax:
$ sudo apachectl configtest
If all goes well, restart Apache:
$ sudo apachectl -k restart
You can tail -f /usr/local/var/log/apache2/error_log, the Apache error log while you restart to see if you have any errors.
Now simply point your browser at https://localhost. If you are prompted about a self-signed certificate, in Chrome you can hit the Advanced option on that page and proceed while in Firefox you need to expand the I Understand the Risks and add as exception. This is due to the fact that the self-signed certificates are not signed by any authority and for this reasons the browsers add warnings about it. Although, since you are the one who created the certificate, you understand it's safe to accept it.

