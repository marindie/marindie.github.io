---
layout: post
title: "Oracle Database Client 11g r2 install on CentOS6 in Silent Mode"
description: "In this post, I will show you how I installed Oracle Database Client 11g r2 on CentOS6.6 in Silent Mode."
categories: [Oracle]
tags: [Oracle Database Client, 11g r2, Oracle silent mode install, CentOS6]
redirect_from:
  - /2018/09/12/
---

> In this post, I will show you how I installed Oracle 11g r2 on CentOS6.6 in Silent Mode.

* Kramdown table of contents
{:toc .toc}

# Assumption & Background

1. You have VM
2. You successfully installed CentOS6.6 on VM somehow (For download CentOS6.6 see below)

# Download CentOS6.5 64bit

- Go to the following URL and Download minimal version of CentOS6.6
- http://vault.centos.org/6.5/isos/x86_64/

# Download Oracle Databse Client 11g r2 64bit Linux (11.2.0.1)

- ORACLE Databae Client 11.2.0.1 DOWNLOAD
- http://www.oracle.com/technetwork/database/enterprise-edition/downloads/112010-linx8664soft-100572.html
- I don't know why but it seems that 11.2.0.4 version is no longer available. So I used 11.2.0.1
- You may find it, but I guess it would be really really difficult to get one. 

# Oracle Documentation URL

- https://docs.oracle.com/cd/E11882_01/install.112/e24325/toc.htm#CIHFICFD

# Ready to install Oracle 

- Now Copy linux_11gR2_client_x86_64.zip into your root home directory

# Pre-conditions before run install.

When you try to install oracle on linux environment, you need to check the followings

- Make sure you set Environment variables needed for Oracle installer.
- Oracle installer will use those Environment variables, when it install oracle database client.
- Make sure you installed required packages for the installer
- Set Target oracle installation path. For this post, it would be /home/oracle/app/product/11.2/client

# Install packages for oracle installation

- yum -y update
- yum -y install unixODBC binutils* compat* gcc* glibc* ksh libgcc* libstdc* libaio* make* sysstat*

# ADD HOSTNAME

- sed -i'' -e "\$a\YOUR_IP_ADDR my_local_machine_name" /etc/hosts or
- vi /etc/hosts and edit like "127.0.0.1 my_local_machine_name"

# Oracle Environment Setting For This Oracle Installation (root user)

```bash

# Goup Add
groupadd oinstall
groupadd dba

# Create User
useradd -m -d /home/oracle -g dba -G oinstall oracle
passwd oracle

# Create Directory For Tablespace (Use this path to create tablespace data file)
mkdir -p /home/oracle/app/product/11.2/client

# Define Oracle Environment for oracle user
# Plain Text
# If you want to set global environment variables, use /etc/profile file 
# If you want to set user level environment variables, use /home/"user_name"/.bash_profile (most of cases the default user's folder name would be like this)
vi /etc/profile
export TMP=/tmp
export TMPDIR=/tmp
export ORACLE_BASE=/home/oracle/app
export ORACLE_SID=ORADB
export ORACLE_HOME=${ORACLE_BASE}/product/11.2/client
export TNS_ADMIN=${ORACLE_HOME}/network/admin
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:${LD_LIBRARY_PATH}
export PATH=$ORACLE_HOME/bin:${PATH}
export SQLPATH=$ORACLE_HOME:${SQLPATH}
export NLS_LANG=American_America.AL32UTF8

# sed command
sed -i'' -r -e "/^# User specific environment and startup programs/a\export TMP=\/tmp\nexport TMPDIR=\/tmp\nexport ORACLE_BASE=\/home\/oracle\/app\nexport ORACLE_SID=ORADB\nexport ORACLE_HOME=\${ORACLE_BASE}\/product\/11.2\/client\nexport TNS_ADMIN=\${ORACLE_HOME}\/network\/admin\nexport LD_LIBRARY_PATH=\$ORACLE_HOME\/lib:\${LD_LIBRARY_PATH}\nexport PATH=\$ORACLE_HOME\/bin:\${PATH}\nexport SQLPATH=\$ORACLE_HOME:\${SQLPATH}\nexport NLS_LANG=American_America.AL32UTF8" /etc/profile

# check the modification result
cat /etc/profile

```

# Unzip Files to /home/oracle and change ownership to oracle.
# Give Read and Execute permissions to files

```bash
unzip -q linux_11gR2_client_x86_64.zip -d /home/oracle
chown -R oracle:dba /home/oracle
chmod -R 755 /home/oracle
```

# Switch user that will install oracle instance
```bash
su - oracle
```

# Use response file to install oracle instance in silent mode

```bash

# When we install oracle in linux, we use command runInstaller.
# Since I prefer to use CLI than GUI, I will use silent mode.
# That way I can install oracle database client without GUI (X Window, in this case)
# There would be two response files. One for databse client and another for listner.

# Copy responfile from "Directory_where_you_unziped_downloaded_files/response" directory. In my case /home/oracle/database/response
# There is _install.rsp file which can be used to install oracle instance.
cp /home/oracle/database/response/client_install.rsp /home/oracle/database/response/client_test_install.rsp

# Then I put some basic information needed for creating oracle instance.
# Plain Text (location of the file may be different if you unzip download file to different location)
vi /home/oracle/database/response/client_test_install.rsp 
UNIX_GROUP_NAME=dba
INVENTORY_LOCATION=/home/oracle/oraInventory
SELECTED_LANGUAGES=en,ko
ORACLE_BASE=/home/oracle/app
ORACLE_HOME=/home/oracle/app/product/11.2/client
oracle.install.client.installType=Administrator

# sed command
sed -i'' -r -e "s/^UNIX_GROUP_NAME=.*/UNIX_GROUP_NAME=dba/" /home/oracle/client/response/client_test_install.rsp
sed -i'' -r -e "s/^INVENTORY_LOCATION=.*/INVENTORY_LOCATION=\/home\/oracle\/app\/oraInventory/" /home/oracle/client/response/client_test_install.rsp
sed -i'' -r -e "s/^SELECTED_LANGUAGES=.*/SELECTED_LANGUAGES=en,ko/" /home/oracle/client/response/client_test_install.rsp
sed -i'' -r -e "s/^ORACLE_HOME=.*/ORACLE_HOME=\/home\/oracle\/app\/product\/11.2\/client/" /home/oracle/client/response/client_test_install.rsp
sed -i'' -r -e "s/^ORACLE_BASE=.*/ORACLE_BASE=\/home\/oracle\/app/" /home/oracle/client/response/client_test_install.rsp
sed -i'' -r -e "s/^oracle.install.client.installType=.*/oracle\.install\.client\.installType=Administrator/" /home/oracle/client/response/client_test_install.rsp

# Check the result
grep "^UNIX_GROUP_NAME=" /home/oracle/client/response/client_test_install.rsp 
grep "^INVENTORY_LOCATION=" /home/oracle/client/response/client_test_install.rsp 
grep "^SELECTED_LANGUAGES=" /home/oracle/client/response/client_test_install.rsp 
grep "^ORACLE_HOME=" /home/oracle/client/response/client_test_install.rsp 
grep "^ORACLE_BASE=" /home/oracle/client/response/client_test_install.rsp 
grep "^oracle.install.client.installType=" /home/oracle/client/response/client_test_install.rsp

```

# Check Oracle install Pre-requisites

```bash
# The following will check whether your environment is good enough to install oracle instance.
# You must check log that oracle generate to see if there is critical errors that oracle can not continue install.
# Be aware that you do not have to satisfy all the Pre-requisites.
# You only need to satisfy requirement which is critical.
# Also At the end of each log, there are "INFO: Advice is blar blar". The one you are expecting is "INFO: Advice is CONTINUE" which is ready to install.
/home/oracle/client/runInstaller -silent -executePrereqs -responseFile /home/oracle/client/response/client_test_install.rsp
```

# Run Installer (Silent Mode)

```bash
# When you create oracle instance, listener, and database, you need to run as oracle user who has belongs to oper group
# Make sure your oracle user have oper group
# If there is no critical errors. It will display some warnings and give you log path.
# Where the path is within your oracle installation path, not /tmp
# Then "tail -f log_file" to see the installation process.
# When it finished install, it will give you the following orders that you must run as root.
# Ex). /ORACLE/oraInventory/orainstRoot.sh
# Ex). /ORACLE/product/11.2/db/root.sh
# run those command and Ctrl-c to get out of the tail and switch to oracle user "su - oracle"
/home/oracle/client/runInstaller -silent -responseFile /home/oracle/client/response/client_test_install.rsp

# When installation finished, run the following as root and switch back to oracle user
exit 
/home/oracle/app/oraInventory/orainstRoot.sh
/home/oracle/app/product/11.2/client/root.sh

# If you have tnsnames.ora file to copy, then put it in your 
cp tnsnames.ora $TNS_ADMIN
chown oracle:dba $TNS_ADMIN/tnsnames.ora

# For manual creation, use the following sample.
su - oracle
cd $TNS_ADMIN
vi tnsnames.ora
# Use this sample. Note that I assumed that the server's database name is "ORADB" in this scenario.
# Server's Port number is 1521
# You can change your_hostname part to IP address of your server.
MYORA=
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = your_hostname)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVICE_NAME = ORADB)
    )
  )
# Save the file
```

# Connect to server database using SQLPLUS

```bash
# Check the connection using sqlplus
# Note, sqlplus will look for the names in tnsnames.ora file. So you need to write MYORA, not ORADB.
# That way sqlplus can parse all the information need for connecting to server.
# You should know at least one valid user account who has CONNECT privilege.
sqlplus user/password@MYORA
```

Cheers. Thanks for reading.

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io