![nifi0](/images/nifi0.jpg)

# Installing Apache NiFi on an Azure VM

With Apache NiFi you can automate the data flow between different systems. The following blog post demonstrates how easily Apache NiFi can be used to integrate Azure Platform Services in existing NiFi solutions. We will setup a dataflow that ingests data from Azure IoT Hub and writes the data to Azure Data Lake Store.

## Prerequisites
Before you begin you must have the following:
* an Azure Ubuntu VM running
* Installed Java (if not installed see for example [Java installation] (https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-get-on-ubuntu-16-04) )


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

To access Azure Data Lake Store from Apache NiFi some additional jars are needed. You have to create a directory to store them:

```

sudo mkdir /usr/lib/hdinsight-datalake

```

and copy needed jars from HDinsight. The following files are needed:

| Path | File |
| -----|------|
| /usr/lib//hdinsight-datalake/ | azure-data-lake-store-sdk-2.0.4-SNAPSHOT.jar |
| /usr/lib//hdinsight-datalake/ | hadoop-azure-datalake-2.0.0-SNAPSHOT.jar |
| /usr/hdp/current/hadoop-client/lib/ | jackson-core-2.2.3.jar |
| /usr/hdp/current/hadoop-hdfs-client/lib/ | okhttp-2.4.0.jar |
| /usr/hdp/current/hadoop-hdfs-client/lib/ | okio-1.4.0.jar |

Copy them e. g. using scp:

```

sudo scp sshuser@<mycluster>-ssh.azurehdinsight.net:/usr/lib/hdinsight-datalake/adls2-oauth2-token-provider-1.0.jar  /usr/lib/hdinsight-datalake

```

On my github there is a prepared archive with the above versions (HDI 3.5):


```
#! /bin/bash
# get jars Archive:
sudo wget https://github.com/hau-mal/BigData/blob/master/jars/adl/adl.tar.gz?raw=true 
# Archive with jars in /usr/lib/hdinsight-datalake
sudo tar -xzf adl.tar.gz?raw=true
sudo rm adl.tar.gz?raw=true
```


