---
title: Hadoop-Pig gebruiken met PowerShell in HDInsight - Azure
description: Informatie over het verzenden van Pig-taken naar een Hadoop-cluster in HDInsight met behulp van Azure PowerShell.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: jasonh
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: d8a729177328b2f6f4e7e75f133b91ddb4db5a61
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597589"
---
# <a name="use-azure-powershell-to-run-pig-jobs-with-hdinsight"></a>Azure PowerShell gebruiken voor het uitvoeren van Pig-taken met HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Dit document bevat een voorbeeld van het gebruik van Azure PowerShell voor het verzenden van Pig-taken met een Hadoop op HDInsight-cluster. Pig kunt u schrijven die gegevenstransformaties modellen MapReduce-taken met behulp van een taal (Pig Latin) in plaats van toewijzen en functies te beperken.

> [!NOTE]
> Dit document biedt geen een gedetailleerde beschrijving van wat de Pig Latin-instructies gebruikt in de voorbeelden doen. Zie voor meer informatie over de Pig Latin gebruikt in dit voorbeeld [Pig gebruiken met Hadoop op HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Vereisten

* **Een Azure HDInsight-cluster**

  > [!IMPORTANT]
  > Linux is het enige besturingssysteem dat wordt gebruikt in HDInsight-versie 3.4 of hoger. Zie [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (HDInsight buiten gebruik gestel voor Windows) voor meer informatie.

* **Een werkstation met Azure PowerShell**.

## <a id="powershell"></a>Een Pig-taak uitvoeren

Azure PowerShell biedt *cmdlets* waarmee u op afstand Pig-taken uitvoeren op HDInsight. Intern, PowerShell maakt gebruik van REST-aanroepen naar [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) uitgevoerd op het HDInsight-cluster.

De volgende cmdlets worden gebruikt bij het uitvoeren van Pig-taken op een extern HDInsight-cluster:

* **Connect-AzureRmAccount**: Azure PowerShell gebruiken om uw Azure-abonnement te worden geverifieerd.
* **Nieuwe AzureRmHDInsightPigJobDefinition**: Hiermee maakt u een *taak definitie* met behulp van de opgegeven Pig Latin-instructies.
* **Start-AzureRmHDInsightJob**: de taakdefinitie verzendt naar HDInsight en wordt de taak wordt gestart. Een *taak* object wordt geretourneerd.
* **Wacht AzureRmHDInsightJob**: maakt gebruik van het taakobject om de status van de taak te controleren. Wacht totdat de taak is voltooid of de wachttijd is overschreden.
* **Get-AzureRmHDInsightJobOutput**: gebruikt voor het ophalen van de uitvoer van de taak.

De volgende stappen laten zien hoe u deze cmdlets gebruiken om een taak uitvoeren op uw HDInsight-cluster.

1. Met behulp van een editor, slaat u de volgende code als **pigjob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Open een nieuwe Windows PowerShell-opdrachtprompt. Wijzig de mappen in de locatie van de **pigjob.ps1** bestand en gebruik vervolgens de volgende opdracht uit het script uit te voeren:

        .\pigjob.ps1

    U wordt gevraagd om aan te melden bij uw Azure-abonnement. Vervolgens wordt u gevraagd voor de HTTPs/Admin-accountnaam en het wachtwoord voor het HDInsight-cluster.

2. Wanneer de taak is voltooid, moet deze informatie die vergelijkbaar is met de volgende tekst retourneren:

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="troubleshooting"></a>Problemen oplossen

Als er geen gegevens worden geretourneerd als de taak is voltooid, moet u de foutenlogboeken weergeven. Toevoegen als foutinformatie voor deze taak, de volgende opdracht aan het einde van de **pigjob.ps1** bestand, opslaan en voer het vervolgens opnieuw uit.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Deze cmdlet retourneert de informatie die is geschreven naar STDERR tijdens de taakverwerking van de.

## <a id="summary"></a>Samenvatting
Zoals u ziet, biedt Azure PowerShell een eenvoudige manier om Pig-taken uitvoeren op een HDInsight-cluster, de taakstatus controleren en ophalen van de uitvoer.

## <a id="nextsteps"></a>Volgende stappen
Voor algemene informatie over Pig in HDInsight:

* [Pig gebruiken met Hadoop op HDInsight](hdinsight-use-pig.md)

Voor meer informatie over andere manieren kunt u werken met Hadoop op HDInsight:

* [Hive gebruiken met Hadoop op HDInsight](hdinsight-use-hive.md)
* [MapReduce gebruiken met Hadoop op HDInsight](hdinsight-use-mapreduce.md)
