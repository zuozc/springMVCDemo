# springMVCDemo
A demo of SpringMVC

# Setup Maven

You need to have your java environment setup. Then go to [Maven Download](http://maven.apache.org/download.cgi) to check the Maven version you want to use. In my project, I use Maven 3.3.9.

1. Download - `curl -L http://mirror.metrocast.net/apache/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz -o /tmp/apache-maven-3.3.9-bin.tar.gz`

2. Chech md5sum - `md5sum /tmp/apache-maven-3.3.9-bin.tar.gz | grep $(curl -s https://www.apache.org/dist/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz.md5) -w`

3. Unpack the tar file - `tar zxf /tmp/apache-maven-3.3.9-bin.tar.gz -C /opt/`. You may need `sudo` to do this.

4. Setup Environment Variable

  ```
export MAVEN_HOME=/opt/apache-maven-3.3.9
export PATH=$PATH:${MAVEN_HOME}/bin
  ```

5. Verify - `mvn -v`

