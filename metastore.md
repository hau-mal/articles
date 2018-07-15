# Using a Hive shared metastore on Azure

## Best Practices
* Use a custom Metastore in an Azure SQL DB whenever possible, this will help you separate Compute and Metadata
* Start with S2 tier which will give you 50 DTU and 250 GB of storage, you can always scale the database up in case you see bottlenecks
* Ensure that the Metastore created for one HDInsight cluster version is not shared across different HDInsight cluster versions. This is due to different Hive versions has different schemas. Example - Hive 1.2 and Hive 2.1 clusters trying to use same Metastore.
* Back-up your custom Metastore periodically for OOPS recovery and DR needs
* Keep Metastore and HDInsight cluster in same region
* Monitor your Metastore for performance and availability with Azure SQL DB Monitoring tools [Azure Portal , Azure Log Analytics]

## HDInsight and databricks configurations

Set hive-metastore.schema.verification to true, also consider to set the hive warehouse dir explicitly. Hive-site configuration for HDInsight: 

    "hive-site": {
                  	"javax.jdo.option.ConnectionDriverName": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                            "javax.jdo.option.ConnectionURL": "jdbc:sqlserver://hmanemeta.database.windows.net;database=hive1;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                            "javax.jdo.option.ConnectionUserName": "[parameters('hiveUserName')]",
                            "javax.jdo.option.ConnectionPassword": "[parameters('hivePassword')]",
                            "hive.metastore.warehouse.dir": "adl://<your-adls>.azuredatalakestore.net/<your-hive-warehouse-dir>",
                            "hive.metastore.schema.verification": true
                            
 Azure Databricks init-script modification:
 
     "hive.metastore.schema.verification" = "true"


## How to prevent metadata loss?
Consider some best practices to prevent metadata loss:
* Metastore object definitions (Hive or Spark SQL Tables, Views, …) are part of the CI/CD pipeline
* SQL DB offers point in time restore capabilities
* Only grant necessary permissions to the hive user -setup a reasonable user concept (see the following tables). For HDInsight the Azure SQL DB user needs a CREATE TABLE authorization.

For Azure SQL DB you should consider minimum three users:
* An Azure SQL DB admin
* a metastore admin
* the normal metastore user

User||permissions|comment|
--- | --- | --- | --- 
|Database admin|||do not use the DB admin as hive metastore admin|
|Hive Metastore admin|CREATE USER msadmin WITH PASSWORD = 'pwd';|GRANT ALTER ANY USER TO msadmin; ALTER ROLE db_owner ADD MEMBER msadmin;|Metastore admin, e. g. used for Metastore administration|
|Metastore User 1 |CREATE USER projectuser1rw WITH PASSWORD = 'pwd';|ALTER ROLE db_datawriter ADD MEMBER projectuser1rw; ALTER ROLE db_datareader ADD MEMBER projectuser1rw; GRANT CREATE TABLE TO projectuser1rw;|Normal metastore user used for HDInsight cluster & Databricks|
|Metastore User 2 |CREATE USER projectuser1ro WITH PASSWORD = 'pwd';|ALTER ROLE db_datareader ADD MEMBER projectuser1ro; GRANT CREATE TABLE TO projectuser1ro;|limited user, not able to update metastore tables|


## Data loss prevention
* Core layer file access should be restricted to ETL operations (e. g. orchestrated via Data Factory), so DROP TABLE statements should fail for Hive or Spark users (SP or User Identities have only read access to Core folders)	 


## References
[Hive Metastore in HDInsight tips & tricks](https://blogs.msdn.microsoft.com/azuredatalake/2017/03/24/hive-metastore-in-hdinsight-tips-tricks-best-practices/)
[Hive access control] https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+Based+Hive+Authorization
[Databricks access control] https://docs.azuredatabricks.net/administration-guide/admin-settings/table-acls/index.html#table-access-control 
[HDInsight presentations]https://www.slideshare.net/AshishThapliyal5
[SQL Server Authentication]https://docs.microsoft.com/en-us/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-2017


