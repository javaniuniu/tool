### jdk install and config environment
>https://download.oracle.com/otn/java/jdk/8u221-b11/230deb18db3e4014bb8e3e8324f81b43/jdk-8u221-linux-x64.tar.gz
```
JAVA_HOME=/Users/minp/env_set/jdk1.8.0_221
PATH=$PATH:$JAVA_HOME/bin
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export JAVA_HOME PATH CLASSPATH
```

### maven intall and config env
>http://ftp.cuhk.edu.hk/pub/packages/apache.org/maven/maven-3/3.6.1/binaries/apache-maven-3.6.1-bin.tar.gz
```
export M2_HOME=/Users/minp/env_set/apache-maven-3.6.1
export PATH=$PATH:$M2_HOME/bin
```
## set
```
$ vi ~/.bashrc
$ source ~/.bashrc
or
vi ~/.bash_profire
source ~/.bash_profire

...others
```
