---
title: Met behulp van Azure CLI - Azure HDInsight Hadoop-clusters beheren
description: Informatie over het gebruik van de Azure-opdrachtregelinterface voor het beheren van Hadoop-clusters in Azure HDInsight. De Azure CLI werkt op Windows, Mac en Linux.
services: hdinsight
editor: jasonwhowell
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jasonh
ms.openlocfilehash: dea0f004c4283bf594e46097092a52dedabb9f4b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599017"
---
# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Beheer Hadoop-clusters in HDInsight met behulp van de Azure CLI
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Meer informatie over het gebruik van de [Azure Command-line Interface](../cli-install-nodejs.md) voor het beheren van Hadoop-clusters in Azure HDInsight. De Azure CLI is geïmplementeerd in Node.js en kan worden gebruikt op elk platform dat ondersteuning biedt voor Node.js, zoals Windows, Mac en Linux. Op dit moment biedt geen ondersteuning voor HDInsight [Azure CLI 2.0](https://docs.microsoft.com/cli/azure).

Dit artikel behandelt alleen de Azure-CLI gebruiken met HDInsight. Raadpleeg voor algemene richtlijnen over het gebruik van Azure CLI, [installeren en configureren van Azure CLI][azure-command-line-tools].

## <a name="prerequisites"></a>Vereisten
Voordat u dit artikel gaat lezen, moet u beschikken over het volgende:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Azure CLI**: zie [De Azure CLI installeren en configureren](../cli-install-nodejs.md) voor informatie over de installatie en configuratie.
* **Verbinding maken met Azure**, met de volgende opdracht:

    ```cli
    azure login
    ```
  
    Zie [Verbinding maken met een Azure-abonnement met Azure CLI](/cli/azure/authenticate-azure-cli) voor meer informatie over verificatie met een werk- of schoolaccount.
* **Overschakelen naar de modus Azure Resource Manager** met de volgende opdracht:
  
    ```cli
    azure config mode arm
    ```

Als u Help-informatie, gebruikt de **-h** overschakelen.  Bijvoorbeeld:

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>Clusters maken met de CLI
Zie [-clusters maken in HDInsight met behulp van de Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="list-and-show-cluster-details"></a>Clusterdetails tonen en vermelden
Gebruik de volgende opdrachten te kunnen aanbieden en clusterdetails weergeven:

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![Opdrachtregel weergave van de lijst met clusters][image-cli-clusterlisting]

## <a name="delete-clusters"></a>Clusters verwijderen
Gebruik de volgende opdracht om een cluster te verwijderen:

```cli
azure hdinsight cluster delete <Cluster Name>
```

U kunt ook een cluster verwijderen door de resourcegroep waarin het cluster te verwijderen. Let op: Hiermee worden alle resources in de groep met inbegrip van het standaardopslagaccount verwijderd.

```cli
azure group delete <Resource Group Name>
```

## <a name="scale-clusters"></a>Clusters schalen
De grootte van de Hadoop-cluster wijzigen:

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>HTTP-toegang voor een cluster in-of uitschakelen

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="enabledisable-rdp-access-for-a-cluster"></a>RDP-toegang voor een cluster in-of uitschakelen

```cli
azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
azure hdinsight cluster disable-rdp-access [options] <Cluster Name>
```

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u verschillende HDInsight-cluster administratieve taken uit te voeren. Zie de volgende artikelen voor meer informatie:

* [HDInsight beheren met behulp van de Azure-Portal][hdinsight-admin-portal]
* [HDInsight beheren met behulp van Azure PowerShell][hdinsight-admin-powershell]
* [Aan de slag met Azure HDInsight][hdinsight-get-started]
* [Het gebruik van de Azure CLI][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "Clusters tonen en vermelden"
