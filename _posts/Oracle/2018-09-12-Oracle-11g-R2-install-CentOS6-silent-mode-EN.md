---
toc: true
title: " Oracle 11g r2 install on CentOS6 in Silent Mode"
description: "In this post, I will show you how I installed Oracle 11g r2 on CentOS6.6 in Silent Mode."
categories: [Oracle]
tags: [Oracle Databse, 11g r2, Oracle silent mode install, CentOS6]
redirect_from:
  - /2018/09/12/
---

> In this post, I will show you how I installed Oracle 11g r2 on CentOS6.6 in Silent Mode.

### Assumption & Background

1. You have VM
2. Make sure you allocate more than 4Gb to SWAP partition during CentOS installation step
3. You successfully installed CentOS6.6 on VM somehow (For download CentOS6.6 see below)

### Download CentOS6.5 64bit

- Go to the following URL and Download minimal version of CentOS6.6
- http://vault.centos.org/6.5/isos/x86_64/

### Download Oracle 11g r2 64bit Linux

- Go to the following URL and Download
- https://www.oracle.com/technetwork/database/enterprise-edition/downloads/112010-linx8664soft-100572.html

### Ready to install Oracle

Now Copy linux.x64_11gR2_database_1of2.zip linux.x64_11gR2_database_2of2.zip into your root home directory

### Pre-conditions before run install

When you try to install oracle on linux environment, you need to check the followings

```md
- Kernel Parameter should meet oracle installer's mininum requirement
- User Resource Limit should meet oracle installer's mininum requirement
- List of Packages should be installed (See below)
- Enough SWAP partitoin. (We will set 4Gb )
- The reason is simply oracle checks those stuffs before run install. If not satisfied, it will not start
- So you don't have a choice but to follow
- Add your hostname in /etc/hosts ("vi /etc/hosts " and put your hostname at the end of the first line. Then save it)
```

### Install packages for oracle installation

```md
yum -y update
yum install -y net-tools binutils compat-libcap* gcc* glibc* ksh libgcc* libstdc* libaio* make* sysstat* unixODBC* elfutils-libelf-devel unzip wget compat-libstdc++-33-3.2.3

you can now donwload those files below from my git url
git clone https://github.com/marindie/share.git

rpm -Uvh --nodeps pdksh-5.2.14-30.3.x86_64.rpm
rpm -Uvh glibc-2.12-1.212.el6_10.3.i686.rpm --nodeps --force
```

### ADD HOSTNAME

```md
- sed -i'' -e '\$a\YOUR_IP_ADDR my_hostname' /etc/hosts

```

### Oracle Environment Setting For This Oracle Installation (root user)

```md

# Goup Add
groupadd oinstall
groupadd dba
groupadd oper

# Create User
useradd -m -d /home/oracle -g oinstall -G dba,oper oracle
passwd oracle

# Create Directory For Tablespace (Use this path to create tablespace data file)
mkdir -p /ORACLE/product/11.2/db
mkdir -p /ORACLE/oradata/ORADB

# Define Oracle Environment for oracle user

# Plain Text
vi /home/oracle/.bash_profile
export ORACLE_BASE=/ORACLE
export ORACLE_HOME=$ORACLE_BASE/product/11.2/db
export TNS_ADMIN=$ORACLE_HOME/network/admin
export ORACLE_SID=ORADB
export ORACLE_TERM=xterm
export PATH=/usr/sbin:$PATH
export PATH=$PATH:$ORACLE_HOME/bin
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib:/usr/local/lib
export CLASSPATH=/JRE:/jlib:/rdbms/jlib
export NLS_LANG=American_America.AL32UTF8

# sed command
sed -i'' -r -e "/^# User specific environment and startup programs/a\export ORACLE_BASE=\/ORACLE\nexport ORACLE_HOME=\$ORACLE_BASE\/product\/11.2\/db\nexport TNS_ADMIN=\$ORACLE_HOME\/network\/admin\nexport ORACLE_SID=ORADB\nexport ORACLE_TERM=xterm\nexport PATH=\/usr\/sbin:\$PATH\nexport PATH=\$PATH:\$ORACLE_HOME\/bin\nexport LD_LIBRARY_PATH=\$ORACLE_HOME\/lib:\/lib:\/usr\/lib:\/usr\/local\/lib\nexport CLASSPATH=\$ORACLE_HOME\/JRE:\$ORACLE_HOME\/jlib:\$ORACLE_HOME\/rdbms\/jlib\nexport NLS_LANG=American_America.AL32UTF8\n" /home/oracle/.bash_profile

source /home/oracle/.bash_profile

```

### Kernel Parameter

```md

# Plain Text
# There are more Kelnel Parameters that oracle check before install. However, the list below is enough to pass the oracle installer's mininum requirement
vi /etc/sysctl.conf
kernel.sem = 250 32000 100 128
fs.file-max = 6815744
net.ipv4.ip_local_port_range = 9000 65500
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048586
fs.aio-max-nr = 1048576

# sed command
sed -i'' -e "\$a\kernel.sem = 250 32000 100 128\nfs.file-max = 6815744\nnet.ipv4.ip_local_port_range = 9000 65500\nnet.core.rmem_default = 262144\nnet.core.rmem_max = 4194304\nnet.core.wmem_default = 262144\nnet.core.wmem_max = 1048586\nfs.aio-max-nr = 1048576" /etc/sysctl.conf

# and then apply kernel parameter change
sysctl -p

```

### USER RESOURCE LIMIT Setting

```md
# Plain Text
vi /etc/security/limits.conf
*               soft    nofile          8192
*               hard    nofile          65536
*               soft    nproc           8192
*               hard    nproc           16384
*               soft    core            20480

# sed command
sed -i'' -e "\$a\*               hard    nofile          65536\n*               soft    nproc           8192\n*               hard    nproc           16384\n*               soft    core            20480\n" /etc/security/limits.conf
```

### Unzip Files to /ORACLE and change ownership to oracle

### Give Read and Execute permissions to files

```md
unzip -q linux.x64_11gR2_database_1of2.zip -d /ORACLE
unzip -q linux.x64_11gR2_database_2of2.zip -d /ORACLE
chown -R oracle:oinstall /ORACLE
chmod -R 755 /ORACLE
```

### Switch user that will install oracle instance

```md
su - oracle
```

### Use response file to install oracle instance in silent mode

```md
# When we install oracle in linux, we use command runInstaller.
# Since I prefer to use CLI than GUI, I will use silent mode.
# That way I can install oracle without GUI (X Window, in this case)
# Oracle provide us three response files that can used to create oracle instance, listener, and database. So I will use response file

# Copy responfile from "Directory_where_you_unziped_downloaded_files/response" directory. In my case /ORACLE/database/response
# There is db_install.rsp file which can be used to install oracle instance.
cp /ORACLE/database/response/db_install.rsp /ORACLE/database/response/oracle_install.rsp

# Then I put some basic information needed for creating oracle instance.
# Plain Text (location of the file may be different if you unzip download file to different location)
vi /ORACLE/database/response/oracle_install.rsp 
oracle.install.option=INSTALL_DB_SWONLY
UNIX_GROUP_NAME=oinstall
INVENTORY_LOCATION=/ORACLE/oraInventory
SELECTED_LANGUAGES=en,ko
ORACLE_HOME=/ORACLE/product/11.2/db
ORACLE_BASE=/ORACLE
oracle.install.db.InstallEdition=EE
oracle.install.db.DBA_GROUP=dba
oracle.install.db.OPER_GROUP=oper
DECLINE_SECURITY_UPDATES=true

# sed command
sed -i'' -r -e "s/^oracle.install.option=.*/oracle\.install\.option=INSTALL_DB_SWONLY/" /ORACLE/database/response/oracle_install.rsp
sed -i'' -r -e "s/^UNIX_GROUP_NAME=.*/UNIX_GROUP_NAME=oinstall/" /ORACLE/database/response/oracle_install.rsp
sed -i'' -r -e "s/^INVENTORY_LOCATION=.*/INVENTORY_LOCATION=\/ORACLE\/oraInventory/" /ORACLE/database/response/oracle_install.rsp
sed -i'' -r -e "s/^SELECTED_LANGUAGES=.*/SELECTED_LANGUAGES=en,ko/" /ORACLE/database/response/oracle_install.rsp
sed -i'' -r -e "s/^ORACLE_HOME=.*/ORACLE_HOME=\/ORACLE\/product\/11.2\/db/" /ORACLE/database/response/oracle_install.rsp
sed -i'' -r -e "s/^ORACLE_BASE=.*/ORACLE_BASE=\/ORACLE/" /ORACLE/database/response/oracle_install.rsp
sed -i'' -r -e "s/^oracle.install.db.InstallEdition=.*/oracle\.install\.db\.InstallEdition=EE/" /ORACLE/database/response/oracle_install.rsp
sed -i'' -r -e "s/^oracle.install.db.DBA_GROUP=.*/oracle\.install\.db\.DBA_GROUP=dba/" /ORACLE/database/response/oracle_install.rsp
sed -i'' -r -e "s/^oracle.install.db.OPER_GROUP=.*/oracle\.install\.db\.OPER_GROUP=oper/" /ORACLE/database/response/oracle_install.rsp
sed -i'' -r -e "s/^DECLINE_SECURITY_UPDATES=.*/DECLINE_SECURITY_UPDATES=true/" /ORACLE/database/response/oracle_install.rsp

# Check the result
grep "oracle.install.option=" /ORACLE/database/response/oracle_install.rsp
grep "UNIX_GROUP_NAME=" /ORACLE/database/response/oracle_install.rsp
grep "INVENTORY_LOCATION=" /ORACLE/database/response/oracle_install.rsp
grep "SELECTED_LANGUAGES=" /ORACLE/database/response/oracle_install.rsp
grep "ORACLE_HOME=" /ORACLE/database/response/oracle_install.rsp
grep "ORACLE_BASE=" /ORACLE/database/response/oracle_install.rsp
grep "oracle.install.db.InstallEdition=" /ORACLE/database/response/oracle_install.rsp
grep "oracle.install.db.DBA_GROUP=" /ORACLE/database/response/oracle_install.rsp
grep "oracle.install.db.OPER_GROUP=" /ORACLE/database/response/oracle_install.rsp
grep "DECLINE_SECURITY_UPDATES=" /ORACLE/database/response/oracle_install.rsp

```

### Check Oracle install Pre-requisites

```md
# The following will check whether your environment is good enough to install oracle instance.
# You must check log that oracle generate to see if there is critical errors that oracle can not continue install.
# Be aware that you do not have to satisfy all the Pre-requisites.
# You only need to satisfy requirement which is critical.
# Also At the end of each log, there are "INFO: Advice is blar blar". The one you are expecting is "INFO: Advice is CONTINUE" which is ready to install.
/ORACLE/database/runInstaller -silent -executePrereqs -responseFile /ORACLE/database/response/oracle_install.rsp
```

### Run Installer (Silent Mode)

```md
# When you create oracle instance, listener, and database, you need to run as oracle user who has belongs to oper group
# Make sure your oracle user have oper group
# If there is no critical errors. It will display some warnings and give you log path.
# Where the path is within your oracle installation path, not /tmp
# Then "tail -f log_file" to see the installation process.
# When it finished install, it will give you the following orders that you must run as root.
# Ex). /ORACLE/oraInventory/orainstRoot.sh
# Ex). /ORACLE/product/11.2/db/root.sh
# run those command and Ctrl-c to get out of the tail and switch to oracle user "su - oracle"
/ORACLE/database/runInstaller -silent -responseFile /ORACLE/database/response/oracle_install.rsp

# When installation finished, run the following as root and switch back to oracle user
exit 
/ORACLE/oraInventory/orainstRoot.sh
/ORACLE/product/11.2/db/root.sh

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

# If you connect database as sysdba, it will try to connect to $ORACLE_SID database.
# Since we set ORACLE_SID as ORADB, it will try to connect to ORADB. 
# There is no database yet, so it will say "ORACLE not available"
# if you are insterested to see the status, connect to as sysdba "sqlplus / as sysdba"
```

### Create Listener (Silent Mode)

```md
cp /ORACLE/database/response/netca.rsp /ORACLE/database/response/oracle_netca.rsp 
netca /silent /responseFile /ORACLE/database/response/oracle_netca.rsp

vi /ORACLE/product/11.2/db/network/admin/listener.ora

SID_LIST_LISTENER =
        (SID_LIST =
            (SID_DESC =
                 (SID_NAME = MYORA)
                 (ORACLE_HOME = /ORACLE/product/11.2/db)
            )
        )

LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
      (ADDRESS = (PROTOCOL = TCP)(HOST = localhost)(PORT = 1521))
    )
  )

ADR_BASE_LISTENER = /ORACLE

# If you want to check whether listener is working, type "lsnrctl status"
# sqlplus user/pwd@MYORA should work. if not check SID_LIST_LISTENER again with tnsnames.ora 
# But Since there is no database, it will show some errors. But it's ok. We will create database After this.

```

### Modify sqlnet.ora

```sql
--add the following option in sqlnet.ora
--It will solve Spring Boot "Got minus one from a read call" Error
tcp.validnode_checking = no
```

### Create Database (Silent Mode)

```md
# For Creating Database, I used template, not responsefile. You can also use responsefile if you want
# Also you can change those options in a way you want.
dbca -silent -createDatabase -templateName General_Purpose.dbc -gdbname ORADB -sid ORADB -sysDBAUserName sys -sysDBAPassword password -responseFile NO_VALUE -characterSet AL32UTF8 -memoryPercentage 20 -emConfiguration NONE
```

### Connect to Database as sysdba

```md
sqlplus / as sysdba
# if it does not show anything like "ORACLE not available". Then it's working.
# The Thing is, when you create database, oracle try to mount and open the database. So If the database is created without errors, then the status should not be "not available"
```

Cheers. Thanks for reading. If there is some syntax errors or questions, please leave a comment.

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
