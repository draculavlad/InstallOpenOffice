# InstallOpenOffice for centos

## Install jdk 7##
```shell
wget --no-cookies \
> --no-check-certificate \
> --header "Cookie: oraclelicense=accept-securebackup-cookie" \
> "http://download.oracle.com/otn-pub/java/jdk/7u55-b13/jdk-7u55-linux-x64.rpm" \
> -O jdk-7-linux-x64.rpm

rpm –Uvh jdk-7-linux-x64.rpm
```

## edit /etc/profile##
* append stuff below into /etc/profile
```
export JAVA_HOME=/usr/java/jdk1.7.0_55
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=.:$JAVA_HOME/jre/lib:$JAVA_HOME/lib:$JAVA_HOME/lib/tools.jar
```

## make /etc/profile conf effect##
```shell
source /etc/profile
```

## install dependency##
```shell
yum groupinstall -y "Development Tools"
yum install -y giflib-devel libjpeg-devel freetype-devel t1lib-devel
```

## Download and install openoffice 3.4.1##
```shell
wget http://downloads.sourceforge.net/project/openofficeorg.mirror/stable/3.4.1/Apache_OpenOffice_incubating_3.4.1_Linux_x86-64_install-rpm_en-US.tar.gz
tar zxf Apache_OpenOffice_incubating_3.4.1_Linux_x86-64_install-rpm_en-US.tar.gz
cd en-US/RPMS/
rpm -ivh *.rpm
```

## create start script##
* vi /etc/rc.d/init.d/openoffice
```shell
#!/bin/bash
# openoffice.org  headless server script
#
# chkconfig: 2345 80 30
# description: headless openoffice server script
# processname: openoffice
# 
# Author: Vic Vijayakumar
# Modified by Federico Ch. Tomasczik
#
OOo_HOME=//opt/openoffice.org3/program
SOFFICE_PATH=$OOo_HOME/soffice
PIDFILE=/var/run/openoffice-server.pid

set -e

case "$1" in
start)
if [ -f $PIDFILE ]; then
  echo "OpenOffice headless server has already started."
  sleep 5
  exit
fi
  echo "Starting OpenOffice headless server"
  $SOFFICE_PATH -headless -nologo -nofirststartwizard -accept="socket,host=127.0.0.1,port=8100;urp" & > /dev/null 2>&1
  touch $PIDFILE
;;
stop)
if [ -f $PIDFILE ]; then
  echo "Stopping OpenOffice headless server."
  killall -9 soffice && killall -9 soffice.bin
  rm -f $PIDFILE
  exit
fi
  echo "Openoffice headless server is not running."
  exit
;;
*)
echo "Usage: $0 {start|stop}"
exit 1
esac
exit 0
```

## make script work, run and check openoffice##
```shell
chmod +x /etc/rc.d/init.d/openoffice
chkconfig --add openoffice
chkconfig openoffice on
cd /etc/rc.d/init.d/ && ./openoffice
netstat -nap | grep office
```
* you might get something like this
* tcp        0      0 127.0.0.1:8100          0.0.0.0:*  LISTEN     2467/soffice.bin 


## incase you want to install swftools##
wget http://www.swftools.org/swftools-0.9.1.tar.gz
tar zxvf swftools-0.9.1.tar.gz
cd swftools-0.9.1
./configure
make
make install
