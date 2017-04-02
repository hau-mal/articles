
# Ingesting weather data to Azure Data Lake and Microsoft Azure DocumentDB (Database as a Service for MongoDB) with Apache NiFi

## Prerequisites
Before you begin you must have the following:
1. Set up an account at [Open Weather Map](https://openweathermap.org/) to get credentials for the API access.
2. Set up Apache NiFi. This can be done on a VM or on an Edge-Node. Follow the steps under [VM Installation](nifi-install-azure-vm.md) or [Edge-Node-Installation](nifi-install-HDIEdgeNode.md).
3. Set up an Azure Data Lake Store
4. Set up a Mongo compatible Document DB
![nifi-weather1](/images/nifi-weather1.PNG)

![nifi-weather2](/images/nifi-weather2.PNG)

Copy the mongo db connection string (you need this later to configure the NiFi PutMongo processor:
![nifi-weather3](/images/nifi-weather3.PNG)

## Create the NiFi Dataflow
You are now ready to create the dataflow. The final flow will look like this:
![nifi-weather5](/images/nifi-weather5.PNG)

Configure the *GetHTTP* processor:
![nifi-weather6](/images/nifi-weather6.PNG)

![nifi-weather7](/images/nifi-weather7.PNG)

![nifi-weather8](/images/nifi-weather8.PNG)


To partition the data via ingest date configure the directory as follows:

```
SELECT * FROM c
WHERE c.name = "SEATTLE"
```

## View the data
You can query the data in the Document DB. In the Azure portal navigate to your DocumentDB and open the query explorer. Enter the following query:

```
/tenant_01/raw/external/weather/ingest_date=${now():format('yyyy-MM-dd')}
```

![nifi-weather4](/images/nifi-weather4.PNG)

## Conclusion



