---
layout: post
category: Linux 
title: Offline install MySQL on CentOS
tagline: by Snail
tags: 
  - CentOS
  - Linux
published: true
---

# Offline Install MySQL on CentOS

## Install

Recently this problem came across to me: I have to install MySQL Server on a Server (CentOS) with access only to LAN where `yum` is useless.   
Here is the solution I found useful:

**Step 1. Download MySQL**

Download the package you desire from [here](https://dev.mysql.com/downloads/mysql/) as follows..

![download_mysql](https://image.jellythink.com/jellythinkcentosmysqlinstall1.png)

**Step 2. Remove the built-in mariaDB**

1. query the installed packages 
     
`rpm -qa | grep -i mariadb`  
This should print some names.

1. remove them  
   
`rpm -e --nodeps <package_names_listed_above>`  
Replace `<package_names_listed_above>` with the names shown with the previous command.

**Step 3. Create a New User for MySQL Management (Optional)**

1. Create new accounts/ account groups
   
```bash
# create a new user group called mysql
groupadd mysql
# create a user under mysql group
useradd -g mysql mysql -d /home/mysql
# initialize the password for him
passwd mysql
```

1. Create directories for data/log/etc.

The directories shall be consistant with what you configure in `my.cnf`, which will be created later.
```bash
mkdir /home/mysql/3306/data
mkdir /home/mysql/3306/log
mkdir /home/mysql/3306/tmp
```


**Step 4. Upload the software to the server**

Move the software to `/usr/local`, create symbolic link and enable only usr mysql with the access. 
```bash
# unzip the package
tar -xvf mysql-5.7.21-linux-glibc2.12-x86_64.tar
tar -zxvf mysql-5.7.21-linux-glibc2.12-x86_64.tar.gz

# create a symbolic link
ln -s mysql-5.7.21-linux-glibc2.12-x86_64 mysql

chown -R mysql:mysql mysql/
```

**Step 5. Create a config file `my.cnf`**

```bash
cd /etc
vi my.cnf
```
A complete example (also what I am using) of `my.cnf` will be provided at the end of this blog.

**Step 6. Install MySQL**

```bash
cd /usr/local/mysql/bin
./mysqld --initialize --user=mysql
```
After this step, a randomly initialized password for root user will be generated in `home/mysql/3306/log/error.log`,
 or the directory you specify at `my.cnf`

**Step 7. Setup auto-start service**

```bash
cp ./support-files/mysql.server /etc/rc.d/init.d/mysqld

chmod +x /etc/rc.d/init.d/mysqld

chkconfig --add mysqld

# check if the service is added successfully
chkconfig --list mysqld

# switch to user:mysql before the next step
su mysql

# start the service
service mysqld start
```

**Step 8. Configure environment variables**

```bash
# make sure you have logged in as mysql
vi ~/.bash_profile

# append this line to the file
export PATH=$PATH:/usr/local/mysql/bin

source ~/.bash_profile
```

**Step 9. Login and reset password**

```bash
# input the password generated
mysql -uroot -p

# reset a password friendly to human
set password for root@localhost=password("123456");
```

## Upgrade/uninstall

A method is also provided if you wanna upgrade or unintall the mysql that was installed in this way. Please backup import files before you go.

**Step 1. Delete related service**

```bash
chkconfig --list | grep -i mysql
chkconfig --del <related service>
```

**Step 2. Empty related directories**

```bash
rm -rf /home/mysql/3306/data/*
rm -rf /home/mysql/3306/log/*
rm -rf /home/mysql/3306/tmp/*
```

**Step 3. Remove installed software**

```bash
# you can find where you installed them with these two commands
whereis mysql
find / -name mysql

# get rid of  them
rm -rf /usr/local/mysql
```



## An example for `my.cnf`

```conf
[client]                                        
port = 3306                                    
socket = /home/mysql/3306/tmp/mysql.sock                       

[mysqld]                                       
server-id = 1                                  
port = 3306                                    
basedir = /usr/local/mysql                      
datadir = /home/mysql/3306/data                     
tmpdir  = /home/mysql/3306/tmp        
socket = /home/mysql/3306/tmp/mysql.sock        
pid-file = /home/mysql/3306/log/mysql.pid      
skip_name_resolve = 1                          
character-set-server = utf8mb4                  
transaction_isolation = READ-COMMITTED         
collation-server = utf8mb4_general_ci 
init_connect='SET NAMES utf8mb4'
lower_case_table_names = 1 
max_connections = 400  
max_connect_errors = 1000 
explicit_defaults_for_timestamp = true 
max_allowed_packet = 128M                
interactive_timeout = 1800               
wait_timeout = 1800                   
tmp_table_size = 16M   
max_heap_table_size = 128M     
query_cache_size = 0      
query_cache_type = 0

read_buffer_size = 2M                      
read_rnd_buffer_size = 8M   
sort_buffer_size = 8M     
binlog_cache_size = 1M    

back_log = 130       

log_error = /home/mysql/3306/log/error.log    
slow_query_log = 1                             
long_query_time = 1                        
slow_query_log_file = /home/mysql/3306/log/slow.log               
log_queries_not_using_indexes = 1              
log_throttle_queries_not_using_indexes = 5     
min_examined_row_limit = 100                    
expire_logs_days = 5                         


log-bin = mysql-bin                          
binlog_format = ROW                           
binlog_row_image = minimal                  

innodb_open_files = 500                        
innodb_buffer_pool_size = 64M                  
innodb_log_buffer_size = 2M                    
innodb_flush_method = O_DIRECT                  
innodb_write_io_threads = 4                    
innodb_read_io_threads = 4
innodb_lock_wait_timeout = 120                  
innodb_log_file_size = 32M                     
```