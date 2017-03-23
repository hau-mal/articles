# Install NiFi on an Azure HDInsight Edge Node

With the following steps you can install Apache NiFi on an Azure HDInsight Edge Node:

1. Copy the [installation Script](https://raw.githubusercontent.com/hau-mal/BigData/master/sh/nifi-install.sh) on your Azure Data Lakes Store.
2. Add the script as an installScriptAction to your Arm Template for the Edge Node:

```
                "installScriptActions": [
                    {
                        "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                        "uri": "adl://*yourADLS*.azuredatalakestore.net/cluster/shell/nifi-install.sh",
                        "roles": [
                            "edgenode"
                        ]
                    }
```
3. Deploy the Edge Node 
4. Setup an [SSH-Tunnel](https://github.com/Microsoft/azure-docs/blob/master/articles/hdinsight/hdinsight-linux-ambari-ssh-tunnel.md) to access the NiFi site.
5. You can access Apache NiFi on your edge Node: http://*edgenode*.internal.cloudapp.net:8080/nifi/
