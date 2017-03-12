![nifi Header] (/images/nifi0.jpg)

#Ingesting Data from Azure IoT Hub to Azure Data Lake Store with Apache NiFi

With Apache NiFi you can automate the data flow between different systems. The following blog post demonstrates how easily Apache NiFi can be used to integrate Azure Platform Services in existing NiFi solutions. We will setup a dataflow that ingests data from Azure IoT Hub and writes the data to Azure Data Lake Store.
##Prerequisites
Before you begin you must have the following:
* Azure IoT Hub with devices sending data to it. If you don’ t have a running solution follow the instructions at [Get started with preconfigured solutions] (http://docs.microsoft.com/en-us/azure/iot-suite/iot-suite-getstarted-preconfigured-solutions). For this blog post I used the *remote monitoring* preconfigured solution.
* Azure Data Lake Store Account. Follow the instructions at [Get started with Azure Data Lake Store using the Azure Portal] (http://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal).
* Apache NiFi installed with access to Azure Data Lake Store. You can install it for example on an Azure Linux VM. Some additional jars and configurations for the Azure Data Lake Store authentication are needed. For instructions on this, see [Connecting to Azure Data Lake from a NiFi dataflow] (http://community.hortonworks.com/articles/71916/connecting-to-azure-data-lake-from-a-nifi-dataflow.html) to configure Apache NiFi for Azure Data Lake Store access. Additional Note: You can copy the core-site.xml and hdfs-site.xml from one of your HDInsight Head Nodes to the machine where NiFi is running. You also should set the *dfs.adls.home.mountpoint* properly, directing to root or a data directory.

##Create the NiFi dataflow
You are now ready to create the dataflow. The final flow will look like this:

![final flow] (/images/nifi1.png)

For reading data from Azure IoT Hub we will use the *GetAzureEventHub* Processor. For the configuration information and an additional setting we have to open the Endpoints blade for the IoT Hub in Azure portal. Click on build in Endpoints *Events*. Here we see the needed configurations for the Processor and here you will also create a new consumer group *nifi* in the blade.

![IoT Hub Endpoints] (/images/nifi2.png)

At this point you have all information to configure the *GetAzureEventHub Processor* as follows:

* **Event Hub Name**: The Event Hub Compatible Name
* **Event Hub Namespace**: The Event Hub Compatible Endpoint, in this example: sb://*iothub-ns-nifidemofc-122975-f260a5499a*.servicebus.windows.net/
* **Number of Event Hub Partitions**: The number of Partitions.
* **Event Hub Consumer Group**: You should create a new consumer group. Enter the name of the created Consumer Group
* **Shared Access Policy Name**: Copy the name from the Shared access policies blade.
* **Shared Access Primary Key**: Copy the corresponding Key from the Shared access policies blade.

![GetAzureEventHub Processor] (/images/nifi3.png)

To ensure that you do not create a single file for every message you should configure a *MergeContent* processor between the *GetAzureEventHub Processor* and the *PutHDFS* processor.

In the last step you configure the *PutHDFS* processor:

* **Hadoop Configuration Resources**: <path>/core-site.xml,<path>/hdfs-site.xml
* **Additional Classpath Resources**: <path to additional resources>, as described in [Connecting to Azure Data Lake from a NiFi dataflow] (http://community.hortonworks.com/articles/71916/connecting-to-azure-data-lake-from-a-nifi-dataflow.html)
* **Directory**: <target dir>

![PutHDFS Processor] (/images/nifi4.png)

For production use you should consider partitioning and compressing the data (configure the Directory and compression codec accordingly).

The Flow Files will appear in the directory you have specified when you run the dataflow. A File Preview should look like this:

![FilePreview] (/images/nifi4.png)

##Conclusion
In this post, we have seen how easy it is to implement a cold path ingesting data from Azure IoT Hub to Azure Data Lake Store. No coding is required. You can also use Apache NiFi for dataflows integrating with other Services like Azure Event Hub, Apache Kafka or Apache HBase. 
