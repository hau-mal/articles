
# Init script to install pyodbc on Azure Databricks


    script = """
      cd /tmp
      sudo apt-get -q -y install unixodbc unixodbc-dev
      sudo apt-get -q -y install python3-dev
      pip install pyodbc
      curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
      curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-release.list
      sudo apt-get update
      sudo ACCEPT_EULA=Y apt-get -q -y install msodbcsql
    """

    dbutils.fs.put("/databricks/init/<clustername>/pyodbc.sh", script, True)
