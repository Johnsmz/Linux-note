# Install phpmyadmin

## Step 1: Install LAMP Stack on Debian 11

phpMyAdmin requires the [LAMP stack](https://phoenixnap.com/kb/what-is-a-lamp-stack) to work correctly. This section shows you how to install the supporting
 software to turn your Debian 11 system into a web server.

If you already have a LAMP stack installed, you can skip directly to the [Download phpMyAdmin](https://phoenixnap.com/kb/how-to-install-phpmyadmin-on-debian-10#ftoc-heading-8) section.

### Step 1.1: Update Software Packages and Install wget

Open a terminal window and update your software package lists with the following command:

```
sudo apt update
```

Enter the following command to install the **wget** tool:

```
sudo apt install wget -y
```

The **wget** utility allows you to download files directly from the terminal window.

You now have the tools to install a LAMP stack and phpMyAdmin.

### Step 1.2: Install Apache

Apache is a web server software that processes requests and transmits data over an HTTP network. [Install Apache](https://phoenixnap.com/kb/how-to-install-apache-maven-on-debian-9) by entering the following command in the terminal:

```
sudo apt install apache2 -y
```

The process takes a few moments to complete. Enter the following command to confirm the Apache service is running:

```
systemctl status apache2
```

The report shows a green status message that says **active (running)**.

### Step 1.3: Install PHP on Debian 11

The PHP programming language and coding environment are essential for running a [web application](https://phoenixnap.com/glossary/web-application) like phpMyAdmin. [Install core PHP packages](https://phoenixnap.com/kb/install-php-on-ubuntu) and Apache and MySQL plugins with the following command:

```
sudo apt -y install php php-cgi php-mysqli php-pear php-mbstring libapache2-mod-php php-common php-phpseclib php-mysql
```

Once the installation process is complete, verify that PHP has been installed:

```
php --version
```

The system displays the current PHP version along with the release date.

### Step 1.4: Install and Set Up MariaDB on Debian 11

This guide uses the MariaDB open-source relational database 
management system instead of MySQL. MariaDB and MySQL are compatible, 
and many commands and features are identical.

To install MariaDB, enter the following command:

```
sudo apt install mariadb-server mariadb-client -y
```

Once the process is complete, verify the MariaDB installation:

```
systemctl status mariadb
```

Like with Apache, you see an **active (running)** status.

Before installing phpMyAdmin, you need to configure the MariaDB database.

#### Secure MariaDB

Configure basic MariaDB security features by launching a built-in script:

```
sudo mysql_secure_installation
```

As you have not yet set a root password for your database, hit **Enter** to skip the initial query. Complete the following queries:

- **Switch to unix_socket authentication [Y/n]** - Enter **n** to skip.
- **Set root password? [Y/n]** - Type **`y`** and press **Enter** to create a strong root password for your database. If you already have a root password, enter **n** to answer the **Change the root password** question.
- **Remove anonymous users? [Y/n]** - Type **`y`** and press **Enter.**
- **Disallow root login remotely? [Y/n]** - Type **`y`** and press **Enter.**
- **Remove test database and access to it? [Y/n]** - Type **`y`** and confirm with **Enter.**
- **Reload privilege tables now? [Y/n]** - Type **`y`** and confirm with **Enter.**

The output shows the MariaDB installation is now secure.

#### Create a New MariaDB User

The phpMyAdmin utility needs a designated user to connect to your 
database. Creating a new MariaDB user improves security and allows you 
to control the level of permissions granted to this user.

Use our detailed guide to [create a new MariaDB user and grant privileges](https://phoenixnap.com/kb/how-to-create-mariadb-user-grant-privileges). Once you set up a MariaDB user, start the phpMyAdmin installation process.

## Step 2: Download phpMyAdmin

Use the **`wget`** command to retrieve the latest stable version of phpMyAdmin:

```
wget -P Downloads https://www.phpmyadmin.net/downloads/phpMyAdmin-latest-all-languages.tar.gz
```

To download the English version only, use this command instead:

```
wget -P Downloads https://www.phpmyadmin.net/downloads/phpMyAdmin-latest-english.tar.gz
```

The **`-P`** option instructs **`wget`** to save the files directly in the *Downloads* directory. Use any directory to download the file and remember the path.

## Step 3: Check phpMyAdmin GPG Key

Each downloaded archive has a corresponding *.asc file* that contains its unique key signature. Once both files are in the same folder, the signature can be verified.

1. To verify the GPG key for phpMyAdmin, download the phpMyAdmin keyring to the directory you used previously. In our case *Downloads*:

```
wget -P Downloads https://files.phpmyadmin.net/phpmyadmin.keyring
```

2. Access the *Downloads* directory and import the keyring:

```
cd Downloads
```

```
gpg --import phpmyadmin.keyring
```

3. Download the corresponding GPG *.asc* file for your version of phpMyAdmin and stay in the same directory:

```
wget https://www.phpmyadmin.net/downloads/phpMyAdmin-latest-all-languages.tar.gz.asc
```

4. Stay in the same directory (in our case *Downloads*) and verify the *.asc* file against the keyring you downloaded:

```
gpg --verify phpMyAdmin-latest-all-languages.tar.gz.asc
```

The system responds by displaying GPG key information.

You can now compare the GPG key to the developer credentials on the [phpMyAdmin documentation page](https://phpmyadmin.readthedocs.io/en/latest/setup.html#verifying-phpmyadmin-releases).

## Step 4: Unpack and Configure phpMyAdmin

1. Create a phpMyAdmin directory in the Apache web root directory:

```
sudo mkdir /var/www/html/phpMyAdmin
```

2. Access the *Downloads* directory and unpack the **phpMyAdmin tar.gz** files to the newly created directory:

```
sudo tar xvf phpMyAdmin-latest-all-languages.tar.gz --strip-components=1 -C /var/www/html/phpMyAdmin
```

The terminal shows no response when the file is unpacked.

3. Create a default configuration file:

```
sudo cp /var/www/html/phpMyAdmin/config.sample.inc.php /var/www/html/phpMyAdmin/config.inc.php
```

4. Use the [nano text editor](https://phoenixnap.com/kb/use-nano-text-editor-commands-linux) (or your preferred text editor) to add a secret passphrase to the *config.inc.php* file:

```
sudo nano /var/www/html/phpMyAdmin/config.inc.php
```

Locate the following line:

```
$cfg['blowfish_secret'] = '';
```

Add a secret passphrase between the single quotes. For example:

```
$cfg['blowfish_secret'] = 'My_Secret_Passphras3!';
```

Use a complex passphrase of your choice and then exit and save the file (**`Ctrl+x`**).

5. Change the permissions for the *config.inc.php* file:

```
sudo chmod 660 /var/www/html/phpMyAdmin/config.inc.php
```

6. Change ownership of the *phpMyAdmin* directory:

```
sudo chown -R www-data:www-data /var/www/html/phpMyAdmin
```

7. Restart Apache:

```
sudo systemctl restart apache2
```

## Step 5: Access phpMyAdmin from Browser

Use a web browser and navigate to the ***localhost/your_phpMyAdmin_directory*** adress to access phpMyAdmin. In our case:

```
localhost/phpMyAdmin
```

The system shows the phpMyAdmin login screen and establishes a 
connection to the local Apache, MariaDB, and PHP files that you have 
created.

Log in to phpMyAdmin with the **username** and **password** for the MariaDB user you had created.

Conclusion

You have now installed phpMyAdmin on your Debian 11 system. Access 
the GUI from a browser and start administering your databases more 
efficiently.

#### Code section 1

```bash
sudo apt update
sudo apt install wget apache2 -y
sudo apt -y install php php-cgi php-mysqli php-pear php-mbstring libapache2-mod-php php-common php-phpseclib php-mysql
sudo apt install mariadb-server mariadb-client -y
sudo mysql_secure_installation
wget -P Downloads https://www.phpmyadmin.net/downloads/phpMyAdmin-latest-all-languages.tar.gz
wget -P Downloads https://files.phpmyadmin.net/phpmyadmin.keyring
cd Downloads
gpg --import phpmyadmin.keyring
wget https://www.phpmyadmin.net/downloads/phpMyAdmin-latest-all-languages.tar.gz.asc
gpg --verify phpMyAdmin-latest-all-languages.tar.gz.asc
sudo mkdir /var/www/html/phpMyAdmin
sudo cp /var/www/html/phpMyAdmin/config.sample.inc.php /var/www/html/phpMyAdmin/config.inc.php
```