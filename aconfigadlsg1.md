
# Configuring access to ADLS Gen1 in Azure Databricks

If you want to use Azure AD Credential Passthrough to Azure Data Lake Storage Gen1 you loose the convenience of mountpoints. 
With some Spark configs you can optimize usability, even improve interoperability Hive based data. 

You have to set the following Spark configs for your HighConcurrency cluster(s):

    dfs.adls.home.hostname <adlsname>.azuredatalakestore.net
    dfs.adls.home.mountpoint /<mountdir>

where *adlsname* is your Azure Data Lake Store and */mountdir* the directory serving as a mountpoint.


With this you can now reference *adl://home* instead of the full path *adlsname.azuredatalakestore.net/mountdir*,  e. g. create a schema: 

    %sql
    CREATE DATABASE IF NOT EXISTS raw COMMENT 'Raw data'
      LOCATION "adl://home/hive/raw.db";

Create a managed Spark table:

     %sql
     USE raw;
     CREATE TABLE a
        (a STRING);

Read data via python:

    %python
    rawText = spark.read.text("adl://home/data/raw/external/weather")


