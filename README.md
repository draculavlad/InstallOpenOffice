# InstallOpenOffice for centos

## References##
* https://gist.github.com/scottvrosenthal/11187116

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

## Download and install openoffice language pack##
```shell
cd /opt/
wget http://downloads.sourceforge.net/project/openofficeorg.mirror/localized/ja/3.4.1/Apache_OpenOffice_incubating_3.4.1_Linux_x86-64_langpack-rpm_ja.tar.gz
wget http://downloads.sourceforge.net/project/openofficeorg.mirror/localized/zh-CN/3.4.1/Apache_OpenOffice_incubating_3.4.1_Linux_x86-64_langpack-rpm_zh-CN.tar.gz
wget http://downloads.sourceforge.net/project/openofficeorg.mirror/localized/zh-TW/3.4.1/Apache_OpenOffice_incubating_3.4.1_Linux_x86-64_langpack-rpm_zh-TW.tar.gz
wget http://downloads.sourceforge.net/project/openofficeorg.mirror/stable/3.4.1/Apache_OpenOffice_incubating_3.4.1_Linux_x86-64_langpack-rpm_en-US.tar.gz
tar zxf Apache_OpenOffice_incubating_3.4.1_Linux_x86-64_langpack-rpm_en-US.tar.gz 
tar zxf Apache_OpenOffice_incubating_3.4.1_Linux_x86-64_langpack-rpm_zh-CN.tar.gz 
tar zxf Apache_OpenOffice_incubating_3.4.1_Linux_x86-64_langpack-rpm_zh-TW.tar.gz 
tar zxf Apache_OpenOffice_incubating_3.4.1_Linux_x86-64_langpack-rpm_ja.tar.gz
cd /opt/en-US/RPMS/ && rpm -ivh *.rpm 
cd /opt/ja/RPMS/ && rpm -ivh *.rpm 
cd /opt/zh-CN/RPMS/ && rpm -ivh *.rpm
cd /opt/zh-TW/RPMS/ && rpm -ivh *.rpm
cd /opt/
rm -rf en-US
rm -rf ja
rm -rf zh-CN
rm -rf zh-TW
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
cd /etc/rc.d/init.d/ && ./openoffice start
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

## uninstall openoffice 3.4##
rpm -e openoffice.org3-zh-TW-3.4.1-9593.x86_64 && \
rpm -e openoffice.org3-zh-CN-3.4.1-9593.x86_64 && \
rpm -e openoffice.org3-ja-3.4.1-9593.x86_64 && \
rpm -e openoffice.org3-writer-3.4.1-9593.x86_64 && \
rpm -e openoffice.org3-base-3.4.1-9593.x86_64 && \
rpm -e openoffice.org3-calc-3.4.1-9593.x86_64 && \
rpm -e openoffice.org3-draw-3.4.1-9593.x86_64 && \
rpm -e openoffice.org3-impress-3.4.1-9593.x86_64 && \
rpm -e openoffice.org3-en-US-3.4.1-9593.x86_64 && \
rpm -e openoffice.org3-math-3.4.1-9593.x86_64 && \
rpm -e openoffice.org3-3.4.1-9593.x86_64 && \
\
rpm -e ooobasis3.4-en-US-base-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-en-US-impress-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-base-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-core04-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-draw-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-writer-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-ogltrans-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-graphicfilter-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-ooolinguistic-3.4.1-9593.x86_64 && \
\
rpm -e ooobasis3.4-ja-base-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-ja-impress-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-ja-calc-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-ja-math-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-ja-binfilter-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-ja-draw-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-ja-res-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-ja-help-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-ja-writer-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-ja-3.4.1-9593.x86_64 && \
\
rpm -e ooobasis3.4-zh-CN-calc-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-zh-CN-math-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-zh-CN-binfilter-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-zh-CN-draw-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-zh-CN-res-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-zh-CN-base-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-zh-CN-impress-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-zh-CN-help-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-zh-CN-writer-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-zh-CN-3.4.1-9593.x86_64 && \
\
rpm -e ooobasis3.4-zh-TW-draw-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-zh-TW-res-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-zh-TW-help-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-zh-TW-writer-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-zh-TW-base-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-zh-TW-impress-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-zh-TW-calc-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-zh-TW-math-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-zh-TW-binfilter-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-zh-TW-3.4.1-9593.x86_64 && \
\
rpm -e ooobasis3.4-en-US-binfilter-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-en-US-calc-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-en-US-math-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-en-US-draw-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-en-US-res-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-en-US-help-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-en-US-writer-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-en-US-3.4.1-9593.x86_64 && \
\
rpm -e ooobasis3.4-impress-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-core03-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-core07-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-math-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-gnome-integration-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-ooofonts-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-xsltfilter-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-core02-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-core06-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-binfilter-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-onlineupdate-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-testtool-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-calc-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-core05-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-images-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-javafilter-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-pyuno-3.4.1-9593.x86_64 && \
rpm -e ooobasis3.4-core01-3.4.1-9593.x86_64 && \
\
rpm -e openoffice.org-ure-3.4.1-9593.x86_64
