![nifi0](/images/nifi0.png)

#Installing Apache NiFi on an Azure VM

With Apache NiFi you can automate the data flow between different systems. The following blog post demonstrates how easily Apache NiFi can be used to integrate Azure Platform Services in existing NiFi solutions. We will setup a dataflow that ingests data from Azure IoT Hub and writes the data to Azure Data Lake Store.
##Prerequisites
Before you begin you must have the following:
* Install Java


##Installing Java
https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-get-on-ubuntu-16-04

Java Install:
sudo apt-get update
sudo apt-get install default-jre
sudo apt-get install default-jdk
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer

Set Java Home in
/etc/environment

source /etc/environment



## Install NiFi
You are now ready to install Appache NiFi.

NiFi Install:

```
#!/bin/bash

# Install Apache NiFi

sudo mkdir /usr/bin/nifi
cd /usr/bin/nifi
sudo wget ftp://apache.mirrors.tds.net/pub/apache.org/nifi/1.1.2/nifi-1.1.2-bin.tar.gz
sudo tar -xzf nifi-1.1.2-bin.tar.gz
sudo /usr/bin/nifi/nifi-1.1.2/bin/nifi.sh install dataflow
sudo /usr/bin/nifi/nifi-1.1.2/bin/nifi.sh start 

echo "nifi installation done"

```





Copy jars needed for Azure Data Lake Store

sudo mkdir /usr/lib/hdinsight-datalake

Copy needed jars
eg: sudo scp sshuser@<mycluster>-ssh.azurehdinsight.net:/usr/lib/hdinsight-datalake/adls2-oauth2-token-provider-1.0.jar  /usr/lib/hdinsight-datalake

| Path | File |
| -----|------|
| /usr/lib//hdinsight-datalake/ | azure-data-lake-store-sdk-2.0.4-SNAPSHOT.jar |
| /usr/lib//hdinsight-datalake/ | hadoop-azure-datalake-2.0.0-SNAPSHOT.jar |
| /usr/hdp/current/hadoop-client/lib/ | jackson-core-2.2.3.jar |
| /usr/hdp/current/hadoop-hdfs-client/lib/ | okhttp-2.4.0.jar |
| /usr/hdp/current/hadoop-hdfs-client/lib/ | okio-1.4.0.jar |


Prepared archive on my github:


```
#! /bin/bash
# get jars Archive:
sudo wget https://github.com/hau-mal/BigData/blob/master/jars/adl/adl.tar.gz?raw=true 
# Archive with jars in /usr/lib/hdinsight-datalake
sudo tar -xzf adl.tar.gz?raw=true
sudo rm adl.tar.gz?raw=true
```



For production use you should consider 



##Conclusion
In this post, we have seen how easy it is to 
