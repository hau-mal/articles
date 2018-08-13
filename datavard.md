# Datavard

## Settings on cluster level


Spark config in the Cluster configuration or create an init script with the parameters:

Current defaults

    spark.sql.hive.metastore.version 1.2.1
    spark.sql.hive.metastore.jars builtin
    
Parameters to access ADLS where the SPARK SQL Database is locacted and staging area for the WEBHDFS access.
e.g.  /sap/sapxyz999/stage     /sap/sapxyz999/database
    
    spark.hadoop.dfs.adls.oauth2.access.token.provider.type ClientCredential
    spark.hadoop.dfs.adls.oauth2.client.id <client_id>
    spark.hadoop.dfs.adls.oauth2.credential <secret>
    spark.hadoop.dfs.adls.oauth2.refresh.url https://login.microsoftonline.com/<tenant>/oauth2/token

## Create a database

    %sql
    CREATE DATABASE IF NOT EXISTS sapxyz999
      COMMENT "sapxyz99 Database maintained by Datavard Glue"
      LOCATION "adl://<your-datalakestore>.azuredatalakestore.net/<dabase-dir>"

## Create user token
Currently only named AAD user are supported

## Next steps
Advanced Configurations
- configure mountpoints
- create and test ACLs on the database
- adapt cluster settings
