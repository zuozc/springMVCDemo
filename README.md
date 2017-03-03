# springMVCDemo
A demo of SpringMVC

## Install Java

1. `wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u101-b13/jdk-8u101-linux-x64.tar.gz`

2. `tar -zxf jdk-8u101-linux-x64.tar.gz -C /opt/jdks/`

3. `ln -s /opt/jdks/jdk1.8.0_101 /opt/jdk1.8`

4. `ln -s /opt/jdks/jdk1.8 /opt/jdk`

5. Add below content to your .bashrc
  ```
export JAVA_HOME=/opt/jdk
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=$PATH:${JAVA_HOME}/bin
  ```

6. `java -version`

# Install and Setup Maven

1. Download Maven
  
  You need to have your java environment setup. Then go to [Maven Download](http://maven.apache.org/download.cgi) to check the Maven version you want to use. In my project, I use Maven 3.3.9.

  `curl -L http://mirror.metrocast.net/apache/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz -o /tmp/apache-maven-3.3.9-bin.tar.gz`

2. Check md5sum
  
  `md5sum /tmp/apache-maven-3.3.9-bin.tar.gz | grep $(curl -s https://www.apache.org/dist/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz.md5) -w`

3. Unpack the tar file
  
  `tar zxf /tmp/apache-maven-3.3.9-bin.tar.gz -C /opt/`. You may need `sudo` to do this.

4. Setup Environment Variable
  
  ```
export MAVEN_HOME=/opt/apache-maven-3.3.9
export PATH=$PATH:${MAVEN_HOME}/bin
  ```

5. Verify - `mvn -v`

## Install and Setup Tomcat

### Create user and group for tomcat

1. `mkdir /opt/tomcat` ## install tomcat in this dir and use this dir as home dir of user tomcat

2. `groupadd tomcat` ## create a linux tomcat

3. `useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat` ## create a new tomcat user. We'll make this user a member of the tomcat group, with a home directory of /opt/tomcat (where we will install Tomcat), and with a shell of /bin/false (so nobody can log into the account)

### Download and Unpack

1. Download tomcat
  
  You need to have your java environment setup. Then go to [Mysql Download](http://tomcat.apache.org/download-80.cgi) to check the Tomcat version you want to use. In my project, I use Tomcat 8.5.11.

  `curl -L http://mirrors.advancedhosters.com/apache/tomcat/tomcat-8/v8.5.11/bin/apache-tomcat-8.5.11.tar.gz -o /tmp/apache-tomcat-8.5.11.tar.gz`

2. Check md5sum
  
  `md5sum /tmp/apache-tomcat-8.5.11.tar.gz | grep $(curl -s https://www.apache.org/dist/tomcat/tomcat-8/v8.5.11/bin/apache-tomcat-8.5.11.tar.gz.md5) -w`

3. Unpack the tar file to `/opt/tomcat/`. You may need `sudo` to do this.
  
  `tar xzf /tmp/apache-tomcat-8.5.11.tar.gz -C /opt/tomcat --strip-components=1` ## unpack tomcat

### Update permissions

The ppurpose is to make the tomcat can only be operated by user `tomcat` and `root`.

```
chgrp -R tomcat /opt/tomcat
cd /opt/tomcat
chmod -R g+r conf
chmod g+x conf
chown -R tomcat webapps/ work/ temp/ logs/
```

### Create systemd service

1. Create systemd service: `touch /lib/systemd/system/tomcat.service`
  
  ```
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target

[Service]
Type=forking

Environment=JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre
Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
Environment=CATALINA_HOME=/opt/tomcat
Environment=CATALINA_BASE=/opt/tomcat
Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh

User=tomcat
Group=tomcat
UMask=0007
RestartSec=30
Restart=always

[Install]
WantedBy=multi-user.target
  ```

2. Verify the service

  ```
systemctl daemon-reload ## daemon-reload so the service file update will take effect.
systemctl start tomcat
systemctl status tomcat
systemctl enable tomcat
  ```

### Web Management interface configuration

1. `vim /opt/tomcat/conf/tomcat-users.xml` ## add an admin user so that we can access the web management interface

2. `vim /opt/tomcat/webapps/manager/META-INF/context.xml` ## Add the remote IP you use to connect to `Manager` app.

3. `vim /opt/tomcat/webapps/host-manager/META-INF/context.xml` ## Add the remote IP you use to connect to `Host Manager` app.

4. `systemctl restart tomcat` ## restart to make the update take effect.

### Reference

1. [How To Install Apache Tomcat 8 on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-apache-tomcat-8-on-ubuntu-16-04)

2. [How To Install Apache Tomcat 8 on CentOS 7](https://www.digitalocean.com/community/tutorials/how-to-install-apache-tomcat-8-on-centos-7)
