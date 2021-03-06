# Using a Hive shared metastore on Azure

## Best Practices
General best practices are
* Use a custom Metastore in an Azure SQL DB whenever possible, this will help you separate Compute and Metadata
* Start with Azure SQL DB S2 tier which will give you 50 DTU and 250 GB of storage, you can always scale the database up in case you see bottlenecks
* Ensure that the Metastore created for one HDInsight cluster version is not shared across different HDInsight cluster versions. This is due to different Hive versions has different schemas. Example - Hive 1.2 and Hive 2.1 clusters trying to use same Metastore. Also prevent implicit upgrades.
* Back-up your custom Metastore periodically for OOPS recovery and DR needs
* Keep Metastore and HDInsight cluster in same region
* Monitor your Metastore for performance and availability with Azure SQL DB Monitoring tools [Azure Portal , Azure Log Analytics]
* If you want to share your metadata between HDInsight Spark clusters and Databricks use full qualified paths for table locations  referencing Azure Data Lake or Blob Storage locations, e. g. abfss://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name> as there is currently no mount-option in HDInsight.

## HDInsight and Azure Databricks configurations

Set hive-metastore.schema.verification to true, this prevents Hive metastore client from implicitly modifying the metastore database schema when the metastore client version does not match the metastore database version.  Also consider to set the hive warehouse dir explicitly. Hive-site configuration for HDInsight: 

    "hive-site": {
                  	"javax.jdo.option.ConnectionDriverName": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                            "javax.jdo.option.ConnectionURL": "jdbc:sqlserver://<servername>.database.windows.net;database=<database-name>;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                            "javax.jdo.option.ConnectionUserName": "[parameters('hiveUserName')]",
                            "javax.jdo.option.ConnectionPassword": "[parameters('hivePassword')]",
                            "hive.metastore.warehouse.dir": "adl://<your-adls>.azuredatalakestore.net/<your-hive-warehouse-dir>",
                            "hive.metastore.schema.verification": true
                            
 Azure Databricks init-script modification:
 
     "hive.metastore.schema.verification" = "true"


## How to prevent metadata loss?
Consider some best practices to prevent metadata loss:
* Metastore object definitions (Hive or Spark SQL Tables, Views, …) are part of the CI/CD pipeline
* Use an Azure SQL DB as it offers point in time restore capabilities
* Only grant necessary permissions to the hive user - setup a reasonable user concept (see the following table). For HDInsight the Azure SQL DB user needs a CREATE TABLE authorization, as this is checked from the HiveConfigurationValidator process before cluster creation.

For Azure SQL DB you should consider minimum three users:
* An Azure SQL DB admin
* a metastore admin
* the metastore user, used for HDInsight or Azure Databricks clusters

User||permissions|comment|
--- | --- | --- | --- 
|Database admin|||do not use the DB admin as hive metastore admin|
|Hive Metastore admin|CREATE USER msadmin WITH PASSWORD = 'pwd';|GRANT ALTER ANY USER TO msadmin; ALTER ROLE db_owner ADD MEMBER msadmin;|Metastore admin, e. g. used for Metastore administration and explicit schema upgrades.|
|Metastore User 1 |CREATE USER projectuser1rw WITH PASSWORD = 'pwd';|ALTER ROLE db_datawriter ADD MEMBER projectuser1rw; ALTER ROLE db_datareader ADD MEMBER projectuser1rw; GRANT CREATE TABLE TO projectuser1rw;|Normal metastore user used for HDInsight cluster & Azure Databricks|
|Metastore read only user |CREATE USER projectuser1ro WITH PASSWORD = 'pwd';|ALTER ROLE db_datareader ADD MEMBER projectuser1ro; GRANT CREATE TABLE TO projectuser1ro;|limited user, not able to update metastore tables|

If you are creating dedicated metastore users per cluster, you are able to track metastore changes via [Azure SQL DB auditing](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing) capabilities.

## Data loss prevention
* Core layer file write and delete access should be restricted to ETL operations (e. g. orchestrated via Data Factory), so DROP TABLE statements should fail for Hive or Spark users (SP or User Identities have only read access to Core folders). A drop table statement will not work if the user has no delete privilege in the underlying file-system.


## References
* [Hive Metastore in HDInsight tips & tricks](https://blogs.msdn.microsoft.com/azuredatalake/2017/03/24/hive-metastore-in-hdinsight-tips-tricks-best-practices/)
* [Azure Databricks External Hive Metastore](https://docs.azuredatabricks.net/user-guide/advanced/external-hive-metastore.html#id1)
* [Hive access control](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+Based+Hive+Authorization)
* [Azure Databricks access control](https://docs.azuredatabricks.net/administration-guide/admin-settings/table-acls/index.html#table-access-control)
* [HDInsight presentations](https://www.slideshare.net/AshishThapliyal5)
* [SQL Server Authentication](https://docs.microsoft.com/en-us/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-2017)


