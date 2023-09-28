# Mysql

## Install

Step 1: Install MariaDB Server
To install MariaDB server, follow these steps:

1. Open a terminal.
2. Run the following command to install MariaDB server:

```
sudo apt install mariadb-server
```

3. During the installation, you'll be prompted to set a password for the MariaDB root user. Make sure to remember this password, as it will be required later.

Step 2: Access MariaDB Command Line
To access the MariaDB command line, follow these steps:

1. Open a new terminal.
2. Run the following command:

```
sudo mysql -u root -p
```

3. Enter the password you set for the MariaDB root user.

Step 3: Create a Database
Now that you're in the MariaDB command line, let's create a database:

Run the following command to create a new database:

```
CREATE DATABASE <DATABASE_NAME>;
```

Step 4: Create a Database User and Grant Privileges
Next, let's create a user and grant necessary privileges:

1. Run the following command to create a new user:

```
CREATE USER '<DATABASE_USER_NAME>'@'localhost' IDENTIFIED BY 'DATABASE_PASSWORD';
```

2. Grant the user all privileges on the Gitea database:

```
GRANT ALL PRIVILEGES ON <DATABASE_NAME>.* TO '<DATABASE_USER_NAME>'@'localhost';
```

4. Flush the privileges to apply the changes:

```
FLUSH PRIVILEGES;
```

Step 5: Exit MariaDB Command Line
To exit the MariaDB command line, simply type:

```
exit
```

# 
