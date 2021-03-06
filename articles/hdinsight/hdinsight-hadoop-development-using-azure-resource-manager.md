---
title: Migreren naar Azure Resource Manager-hulpprogramma's voor HDInsight
description: Migreren naar ontwikkelingsprogramma's van Azure Resource Manager voor HDInsight-clusters
services: hdinsight
editor: jasonwhowell
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: jasonh
ms.openlocfilehash: 3b451973cc867af41399a99f680e000e8e283f51
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39598446"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migreren naar Azure Resource Manager gebaseerde ontwikkelingsprogramma's voor HDInsight-clusters

HDInsight is niet meer ondersteund op basis van een Azure Service Manager ASM-hulpprogramma's voor HDInsight. Als u hebt gebruikt Azure PowerShell, Azure CLI of de HDInsight .NET SDK voor het werken met HDInsight-clusters, wordt u aangeraden de Azure Resource Manager-versies van PowerShell, CLI en .NET-SDK gaan gebruiken. In dit artikel bevat verwijzingen over het migreren naar de nieuwe Resource Manager gebaseerde aanpak. Waar van toepassing, worden de verschillen tussen de benaderingen ASM en Resource Manager voor HDInsight in dit document gemarkeerd.

> [!IMPORTANT]
> De ondersteuning voor ASM op basis van PowerShell, CLI, en .NET-SDK wordt stopgezet op **per 1 januari 2017**.
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>Migreren naar Azure Resource Manager in de Azure CLI

> [!IMPORTANT]
> Azure CLI 2.0 biedt geen ondersteuning voor het werken met HDInsight-clusters. U kunt nog steeds Azure CLI 1.0 gebruiken met HDInsight, maar Azure CLI 1.0 is afgeschaft.

Hier volgen de basisopdrachten voor het werken met HDInsight via Azure CLI 1.0:

* `azure hdinsight cluster create` -maakt een nieuw HDInsight-cluster
* `azure hdinsight cluster delete` -Hiermee verwijdert u een bestaand HDInsight-cluster
* `azure hdinsight cluster show` -informatie weergeven over een bestaand cluster
* `azure hdinsight cluster list` -een lijst met HDInsight-clusters voor uw Azure-abonnement

Gebruik de `-h` overschakelen naar het inspecteren van de parameters en switches die beschikbaar zijn voor elke opdracht.

### <a name="new-commands"></a>Nieuwe CLI-opdrachten
Nieuwe opdrachten die beschikbaar zijn met Azure Resource Manager zijn:

* `azure hdinsight cluster resize` -het aantal worker-knooppunten in het cluster dynamisch wordt gewijzigd
* `azure hdinsight cluster enable-http-access` -Hiermee kunt u HTTPs-toegang tot het cluster (op standaard)
* `azure hdinsight cluster disable-http-access` -schakelt u de HTTPs-toegang tot het cluster
* `azure hdinsight script-action` -bevat opdrachten voor het maken/beheren van scriptacties op een cluster
* `azure hdinsight config` -bevat opdrachten voor het maken van een configuratiebestand die kan worden gebruikt met de `hdinsight cluster create` opdracht om configuratie-informatie te geven.

### <a name="deprecated-commands"></a>Afgeschafte opdrachten
Als u de `azure hdinsight job` opdrachten voor het verzenden van taken naar uw HDInsight-cluster, deze opdrachten zijn niet beschikbaar is via het Resource Manager-opdrachten. Als u nodig hebt voor het programmatisch verzenden van taken naar HDInsight van scripts, moet u in plaats daarvan de geleverd door HDInsight REST-API's. Zie de volgende documenten voor meer informatie over het verzenden van taken met behulp van REST-API's.

* [MapReduce-taken uitvoeren met Hadoop op HDInsight met cURL](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Hive-query's uitvoeren met Hadoop op HDInsight met cURL](hadoop/apache-hadoop-use-hive-curl.md)
* [Pig-taken uitvoeren met Hadoop op HDInsight met cURL](hadoop/apache-hadoop-use-pig-curl.md)

Voor informatie over andere manieren om uit te voeren van MapReduce, Hive, en Pig-interactief, Zie [MapReduce gebruiken met Hadoop op HDInsight](hadoop/hdinsight-use-mapreduce.md), [Hive gebruiken met Hadoop op HDInsight](hadoop/hdinsight-use-hive.md), en [Pig gebruiken met Hadoop op HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Voorbeelden
**Het maken van een cluster**

* Oude opdracht (ASM)- `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nieuwe opdracht: `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Verwijderen van een cluster**

* Oude opdracht (ASM)- `azure hdinsight cluster delete myhdicluster`
* Nieuwe opdracht: `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Clusters groeperen**

* Oude opdracht (ASM)- `azure hdinsight cluster list`
* Nieuwe opdracht: `azure hdinsight cluster list`

> [!NOTE]
> Voor de opdracht de lijst op te geven de resource-groep met `-g` retourneert alleen de clusters in de opgegeven resourcegroep.
> 
> 

**Cluster-informatie weergeven**

* Oude opdracht (ASM)- `azure hdinsight cluster show myhdicluster`
* Nieuwe opdracht: `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Azure PowerShell migreren naar Azure Resource Manager
De algemene informatie over Azure PowerShell in de modus Azure Resource Manager kan worden gevonden op [met behulp van Azure PowerShell met Azure Resource Manager](../powershell-azure-resource-manager.md).

De Azure PowerShell Resource Manager-cmdlets kunnen worden geïnstalleerd naast de ASM-cmdlets. De cmdlets van de twee modi kunnen worden onderscheiden door hun namen.  De modus Resource Manager heeft *AzureRmHDInsight* in vergelijking met namen van de cmdlets *AzureHDInsight* in de ASM-modus.  Bijvoorbeeld, *New-AzureRmHDInsightCluster* vs. *New-AzureHDInsightCluster*. Parameters en switches mogelijk nieuws namen en er zijn veel nieuwe parameters beschikbaar bij het gebruik van Resource Manager.  Bijvoorbeeld verschillende cmdlets vereisen dat een nieuwe switch met de naam *- ResourceGroupName*. 

Voordat u de HDInsight-cmdlets gebruiken kunt, moet u verbinding maken met uw Azure-account en een nieuwe resourcegroep maken:

* Connect-AzureRmAccount of [Select-AzureRmProfile](https://msdn.microsoft.com/library/mt619310.aspx). Zie [verifiëren van een service-principal met Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Hernoemd cmdlets
Overzicht van de HDInsight ASM-cmdlets in Windows PowerShell-console:

    help *azurermhdinsight*

De volgende tabel bevat de ASM-cmdlets en hun namen in Resource Manager-modus:

| ASM-cmdlets | Resource Manager-cmdlets |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzureRmHDInsightConfigValues](https://msdn.microsoft.com/library/mt603530.aspx) |
| Add-AzureHDInsightMetastore |[Add-AzureRmHDInsightMetastore](https://msdn.microsoft.com/library/mt603670.aspx) |
| Add-AzureHDInsightScriptAction |[Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) |
| Add-AzureHDInsightStorage |[Add-AzureRmHDInsightStorage](https://msdn.microsoft.com/library/mt619445.aspx) |
| Get-AzureHDInsightCluster |[Get-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619371.aspx) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603590.aspx) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://msdn.microsoft.com/library/mt603793.aspx) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://msdn.microsoft.com/library/mt603546.aspx) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619407.aspx) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603717.aspx) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzureRmHDInsightHiveJob](https://msdn.microsoft.com/library/mt603593.aspx) |
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzureRmHDInsightClusterConfig](https://msdn.microsoft.com/library/mt603700.aspx) |
| New-AzureHDInsightHiveJobDefinition |[New-AzureRmHDInsightHiveJobDefinition](https://msdn.microsoft.com/library/mt619448.aspx) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzureRmHDInsightMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| New-AzureHDInsightPigJobDefinition |[New-AzureRmHDInsightPigJobDefinition](https://msdn.microsoft.com/library/mt603671.aspx) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzureRmHDInsightSqoopJobDefinition](https://msdn.microsoft.com/library/mt608551.aspx) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzureRmHDInsightStreamingMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| Remove-AzureHDInsightCluster |[Remove-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619431.aspx) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619375.aspx) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603523.aspx) |
| Set-AzureHDInsightClusterSize |[Set-AzureRmHDInsightClusterSize](https://msdn.microsoft.com/library/mt603513.aspx) |
| Set-AzureHDInsightDefaultStorage |[Set-AzureRmHDInsightDefaultStorage](https://msdn.microsoft.com/library/mt603486.aspx) |
| Start-AzureHDInsightJob |[Start-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603798.aspx) |
| Stop-AzureHDInsightJob |[Stop-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt619424.aspx) |
| Use-AzureHDInsightCluster |[Use-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619442.aspx) |
| Wait-AzureHDInsightJob |[Wait-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603834.aspx) |

### <a name="new-cmdlets"></a>Nieuwe cmdLets
Hieronder vindt u de nieuwe cmdlets die alleen beschikbaar in Resource Manager-modus zijn. 

**Script actie-gerelateerde cmdlets:**

* **Get-AzureRmHDInsightPersistedScriptAction**: haalt het persistente scriptacties voor een cluster en worden ze weergegeven in chronologische volgorde of Hiermee haalt u details voor een opgegeven persistente scriptactie. 
* **Get-AzureRmHDInsightScriptActionHistory**: Hiermee haalt u de geschiedenis van de scriptactie voor een cluster en geeft dit weer in omgekeerde volgorde of details van een eerder uitgevoerde scriptactie opgehaald. 
* **Remove-AzureRmHDInsightPersistedScriptAction**: Hiermee verwijdert u een persistente scriptactie van een HDInsight-cluster.
* **Set-AzureRmHDInsightPersistedScriptAction**: Hiermee stelt u een eerder uitgevoerde scriptactie moet een persistente scriptactie.
* **Indienen AzureRmHDInsightScriptAction**: een nieuwe scriptactie naar een Azure HDInsight-cluster worden verzonden. 

Zie voor informatie over het extra gebruik, [aanpassen Linux gebaseerde HDInsight-clusters met Script Action](hdinsight-hadoop-customize-cluster-linux.md).

**Cluster-id-gerelateerde cmdlets:**

* **Voeg AzureRmHDInsightClusterIdentity**: de identiteit van een cluster toevoegt aan een configuratieobject voor de cluster, zodat het HDInsight-cluster toegang heeft tot Azure Data Lake Stores. Zie [een HDInsight-cluster maken met Data Lake Store met behulp van Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Voorbeelden
**Cluster maken**

Oude opdracht (ASM): 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

Nieuwe opdracht:

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**Cluster verwijderen**

Oude opdracht (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Nieuwe opdracht:

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Lijst met cluster**

Oude opdracht (ASM):

    Get-AzureHDInsightCluster

Nieuwe opdracht:

    Get-AzureRmHDInsightCluster 

**Cluster weergeven**

Oude opdracht (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Nieuwe opdracht:

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Andere voorbeelden
* [HDInsight-clusters maken](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Hive-taken indienen](hadoop/apache-hadoop-use-hive-powershell.md)
* [Verzenden van Pig-taken](hadoop/apache-hadoop-use-pig-powershell.md)
* [Sqoop taken verzenden](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migreren naar de nieuwe HDInsight .NET SDK
De Azure Service Management-gebaseerde [(ASM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) is nu verouderd. U wordt aangeraden gebruik van de Azure Resource Management-gebaseerde [Resource Manager gebaseerde HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight). De volgende HDInsight op basis van een ASM-pakketten worden afgeschaft.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Deze sectie bevat verwijzingen naar meer informatie over het uitvoeren van bepaalde taken met behulp van de SDK op basis van Resource Manager.

| Hoe u... met de Resource Manager gebaseerde HDInsight-SDK | Koppelingen |
| --- | --- |
| Maken van HDInsight-clusters met behulp van .NET SDK |Zie [maken van HDInsight-clusters met behulp van .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| Aanpassen van een cluster met Script Action met .NET SDK |Zie [aanpassen HDInsight Linux-clusters met Script Action](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Toepassingen die gebruikmaken van Azure Active Directory interactief met .NET SDK verifiëren |Zie [uitvoeren Hive-query's met .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Het codefragment in dit artikel maakt gebruik van de aanpak van interactieve verificatie. |
| Toepassingen die gebruikmaken van Azure Active Directory niet-interactief met .NET SDK verifiëren |Zie [maken van niet-interactieve toepassingen voor HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Indienen van een Hive-taak met behulp van .NET SDK |Zie [indienen Hive-taken](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Verzenden van een Pig-taak met behulp van .NET SDK |Zie [verzenden van Pig-taken](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| Verzenden van een Sqoop-taak met behulp van .NET SDK |Zie [indienen Sqoop taken](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Lijst met HDInsight-clusters met behulp van .NET SDK |Zie [lijst met HDInsight-clusters](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Schalen van HDInsight-clusters met behulp van .NET SDK |Zie [schaal HDInsight-clusters](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Toegang verlenen of in te trekken met HDInsight-clusters met behulp van .NET SDK |Zie [Grant/revoke toegang tot HDInsight-clusters](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| HTTP-gebruikersreferenties voor HDInsight-clusters met behulp van .NET SDK bijwerken |Zie [Update HTTP-gebruikersreferenties voor HDInsight-clusters](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Het standaardopslagaccount vinden voor HDInsight-clusters met behulp van .NET SDK |Zie [vinden van het standaardaccount voor opslag voor HDInsight-clusters](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| HDInsight-clusters met behulp van .NET SDK verwijderen |Zie [verwijderen HDInsight-clusters](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Voorbeelden
Hier volgen enkele voorbeelden van hoe een bewerking is uitgevoerd met behulp van de SDK op basis van ASM en de equivalente codefragment voor de SDK op basis van Resource Manager.

**Het maken van een cluster CRUD-client**

* Oude opdracht (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Nieuwe opdracht (Service principal autorisatie)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Nieuwe opdracht (gebruikersverificatie)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**Het maken van een cluster**

* Oude opdracht (ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* Nieuwe opdracht
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**HTTP-toegang inschakelen**

* Oude opdracht (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Nieuwe opdracht
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**Verwijderen van een cluster**

* Oude opdracht (ASM)
  
        client.DeleteCluster(dnsName);
* Nieuwe opdracht
  
        client.Clusters.Delete(resourceGroup, dnsname);

