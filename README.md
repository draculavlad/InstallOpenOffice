# InstallOpenOffice
## for centos7

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
