
# Ingesting weather data to Azure Data Lake and Microsoft Azure DocumentDB (Database as a Service for MongoDB) with Apache NiFi

## Prerequisites
Before you begin you must have the following:
1. Set up an account at [Open Weather Map](https://openweathermap.org/) to get credentials for the API access.
2. Set up Apache NiFi. This can be done on a VM or on an Edge-Node. Follow the steps under [VM Installation](nifi-install-azure-vm.md) or [Edge-Node-Installation](nifi-install-HDIEdgeNode.md).
3. Set up an Azure Data Lake Store
4. Set up a Mongo compatible Document DB
![nifi-weather1](/images/nifi-weather1.png)

![nifi-weather2](/images/nifi-weather2.png)

Copy the mongo db connection string (you need this later to configure the NiFi PutMongo processor:
![nifi-weather3](/images/nifi-weather3.png)

## Create the NiFi Dataflow
You are now ready to create the dataflow. The final flow will look like this:
![nifi-weather5](/images/nifi-weather5.png)

![nifi-weather6](/images/nifi-weather6.png)

![nifi-weather7](/images/nifi-weather7.png)

![nifi-weather8](/images/nifi-weather8.png)

## View the data
![nifi-weather4](/images/nifi-weather4.png)

## Conclusion



