---
title: Use custom activities in an Azure Data Factory pipeline (Aangepaste activiteiten gebruiken in een Azure Data Factory-pijplijn)
description: Informatie over het maken van aangepaste activiteiten en deze gebruiken in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 2dab0adb0728a1fb5e8ac9bebe01f861ed8c7c3a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37058883"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Use custom activities in an Azure Data Factory pipeline (Aangepaste activiteiten gebruiken in een Azure Data Factory-pijplijn)
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versie 1](v1/data-factory-use-custom-activities.md)
> * [Huidige versie](transform-data-using-dotnet-custom-activity.md)

Er zijn twee soorten activiteiten die u in een Azure Data Factory-pijplijn gebruiken kunt.

- [Activiteiten voor gegevensverplaatsing](copy-activity-overview.md) verplaatsen van gegevens tussen [ondersteunde bron- en sink gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats).
- [Activiteiten voor gegevenstransformatie](transform-data.md) om gegevens te transformeren met behulp van compute-services zoals Azure HDInsight Azure Batch en Azure Machine Learning. 

Verplaatsen Sla gegevens van/naar een data dat Data Factory biedt geen ondersteuning of om gegevens op een manier die niet wordt ondersteund door Data Factory-transformatie/proces, kunt u een **aangepaste activiteit** met uw eigen verplaatsing van gegevens of transformatie logica en gebruik de activiteit in een pijplijn. De aangepaste activiteit uw aangepaste code-logica wordt uitgevoerd op een **Azure Batch** pool van virtuele machines.

Zie de volgende artikelen op als u niet bekend met Azure Batch-service bent:

* [Basisbeginselen van Azure Batch](../batch/batch-technical-overview.md) voor een overzicht van de Azure Batch-service.
* [Nieuwe AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) cmdlet voor het maken van een Azure Batch-account (of) [Azure-portal](../batch/batch-account-create-portal.md) naar de Azure Batch-account maken met Azure-portal. Zie [met behulp van PowerShell voor het beheren van Azure Batch-Account](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) artikel voor gedetailleerde instructies over het gebruik van de cmdlet.
* [Nieuwe-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) cmdlet om een Azure Batch-pool te maken.

## <a name="azure-batch-linked-service"></a>Azure Batch gekoppeld-service 
De volgende JSON definieert een voorbeeld van een gekoppelde Azure-Batch-service. Zie voor meer informatie [Compute omgevingen wordt ondersteund door Azure Data Factory](compute-linked-services.md)

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
}
```

 Zie voor meer informatie over Azure Batch gekoppelde service, [gekoppelde services berekenen](compute-linked-services.md) artikel. 

## <a name="custom-activity"></a>Aangepaste activiteit

De volgende JSON-fragment definieert een pijplijn met een eenvoudige aangepaste activiteit. De definitie van de activiteit bevat een verwijzing naar de gekoppelde Azure-Batch-service. 

```json
{
    "name": "MyCustomActivityPipeline",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "helloworld.exe",
          "folderPath": "customactv2/helloworld",
          "resourceLinkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }]
    }
  }
```

In dit voorbeeld is de helloworld.exe een aangepaste toepassing opgeslagen in de map customactv2/helloworld van het Azure Storage-account in de resourceLinkedService gebruikt. De aangepaste activiteit verzendt deze aangepaste toepassing om te worden uitgevoerd op Azure Batch. U kunt de opdracht om elke gewenste toepassing die kan worden uitgevoerd op de doel-besturingssysteem van de Azure Batch-Pool-knooppunten te vervangen. 

De volgende tabel beschrijft de namen en beschrijvingen van eigenschappen die specifiek voor deze activiteit zijn. 

| Eigenschap              | Beschrijving                              | Vereist |
| :-------------------- | :--------------------------------------- | :------- |
| naam                  | Naam van de activiteit in de pijplijn     | Ja      |
| description           | Tekst die beschrijft wat de activiteit doet.  | Nee       |
| type                  | Voor aangepaste activiteit, het type hoofdactiviteit is **aangepaste**. | Ja      |
| linkedServiceName     | Gekoppelde Azure Batch-Service. Zie voor meer informatie over deze gekoppelde service, [gekoppelde services berekenen](compute-linked-services.md) artikel.  | Ja      |
| opdracht               | De opdracht van de aangepaste toepassing moet worden uitgevoerd. Als de toepassing al beschikbaar op het knooppunt in Azure Batch Pool, de resourceLinkedService is en folderPath worden overgeslagen. Bijvoorbeeld, kunt u de opdracht om te worden `cmd /c dir`, die standaard wordt ondersteund door de Batch-Pool van Windows-knooppunt. | Ja      |
| resourceLinkedService | Azure Storage Linked Service naar het opslagaccount waarin de aangepaste toepassing is opgeslagen | Nee       |
| folderPath            | Pad naar de map van de aangepaste toepassing en alle afhankelijkheden ervan | Nee       |
| referenceObjects      | Een matrix van bestaande gekoppelde Services en gegevenssets. De waarnaar wordt verwezen gekoppelde Services en gegevenssets worden doorgegeven aan de aangepaste toepassing in JSON-indeling zodat uw aangepaste code kunt verwijzen naar resources van de Gegevensfactory | Nee       |
| extendedProperties    | Gebruiker gedefinieerde eigenschappen die kunnen worden doorgegeven aan de aangepaste toepassing in JSON-indeling, zodat uw aangepaste code kunt verwijzen naar aanvullende eigenschappen | Nee       |

## <a name="executing-commands"></a>Uitvoeren van opdrachten

U kunt een opdracht met behulp van aangepaste activiteit rechtstreeks uitvoeren. Het volgende voorbeeld de opdracht 'echo hello world' wordt uitgevoerd op de doelknooppunten Azure Batch-Pool en de uitvoer naar stdout afgedrukt. 

  ```json
  {
    "name": "MyCustomActivity",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "cmd /c echo hello world"
        }
      }]
    }
  } 
  ```

## <a name="passing-objects-and-properties"></a>Objecten en Eigenschappen doorgeven

Dit voorbeeld toont hoe u de referenceObjects en extendedProperties Data Factory-objecten en de gebruiker gedefinieerde eigenschappen doorgeven aan uw aangepaste toepassing kunt gebruiken. 


```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "aSampleSecureString"
            },
            "PropertyBagPropertyName1": "PropertyBagValue1",
            "propertyBagPropertyName2": "PropertyBagValue2",
            "dateTime1": "2015-04-12T12:13:14Z"              
        }
      }
    }]
  }
}
```

Wanneer de activiteit wordt uitgevoerd, worden referenceObjects en extendedProperties opgeslagen in de volgende bestanden die zijn geïmplementeerd naar dezelfde map uitvoering van de SampleApp.exe: 

- Activity.JSON

  ExtendedProperties en eigenschappen van de aangepaste activiteit worden opgeslagen. 

- linkedServices.json

  Slaat een matrix met gekoppelde Services gedefinieerd in de eigenschap referenceObjects. 

- datasets.json

  Slaat een matrix van gegevenssets die worden gedefinieerd in de eigenschap referenceObjects. 

Volgende voorbeeldcode laten zien hoe de SampleApp.exe kunnen toegang krijgen tot de vereiste gegevens uit JSON-bestanden: 

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Uitvoering uitvoer ophalen

  U kunt een pijplijn uitvoeren met de volgende PowerShell-opdracht op te starten: 

  ```.powershell
  $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
  ```
  Wanneer de pijplijn wordt uitgevoerd, kunt u de uitvoer van de uitvoering van de volgende opdrachten kunt controleren: 

  ```.powershell
  while ($True) {
      $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

      if(!$result) {
          Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
      }
      elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
          Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
      }
      else {
          Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
          $result
          break
      }
      ($result | Format-List | Out-String)
      Start-Sleep -Seconds 15
  }

  Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
  $result.Output -join "`r`n"

  Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
  $result.Error -join "`r`n"
  ```

  De **stdout** en **stderr** van een aangepaste toepassing worden opgeslagen in de **adfjobs** container in de Azure Storage Linked Service u bij het maken van Azure Batch gekoppelde gedefinieerd De service met een GUID van de taak. U kunt het gedetailleerde pad krijgen van uitvoer activiteit uitgevoerd zoals weergegeven in het volgende fragment: 

  ```shell
  Pipeline ' MyCustomActivity' run finished. Result:

  ResourceGroupName : resourcegroupname
  DataFactoryName   : datafactoryname
  ActivityName      : MyCustomActivity
  PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  PipelineName      : MyCustomActivity
  Input             : {command}
  Output            : {exitcode, outputs, effectiveIntegrationRuntime}
  LinkedServiceName : 
  ActivityRunStart  : 10/5/2017 3:33:06 PM
  ActivityRunEnd    : 10/5/2017 3:33:28 PM
  DurationInMs      : 21203
  Status            : Succeeded
  Error             : {errorCode, message, failureType, target}

  Activity Output section:
  "exitcode": 0
  "outputs": [
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
  ]
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
  Activity Error section:
  "errorCode": ""
  "message": ""
  "failureType": ""
  "target": "MyCustomActivity"
  ```
Als u wilt gebruiken voor de inhoud van stdout.txt in downstream-activiteiten, krijgt u het pad naar het bestand stdout.txt in de expressie '\@activity('MyCustomActivity').output.outputs [0] '. 

  > [!IMPORTANT]
  > - De activity.json, linkedServices.json en datasets.json worden opgeslagen in de runtime-map van de Batch-taak. In dit voorbeeld de activity.json, linkedServices.json en datasets.json worden opgeslagen in "https://adfv2storage.blob.core.windows.net/adfjobs/<GUID>/runtime/ ' pad. Indien nodig, moet u deze afzonderlijk opschonen. 
  > - Blijft in klant gedefinieerd voor de gekoppelde Services maakt gebruik van Self-Hosted integratie Runtime, de gevoelige gegevens, zoals sleutels of wachtwoorden zijn versleuteld door de Runtime Self-Hosted integratie om ervoor te zorgen referentie persoonlijke netwerkomgeving. Sommige velden gevoelige mogelijk ontbreekt wanneer waarnaar wordt verwezen door de aangepaste toepassingscode die op deze manier. Gebruik SecureString in extendedProperties in plaats van verwijzing naar de gekoppelde Service, indien nodig. 

## <a name="compare-v2-v1"></a> Vergelijk v2 aangepaste activiteit en versie 1 (aangepast) DotNet activiteit

  In Azure Data Factory versie 1, u een activiteit van de DotNet (aangepast) implementeren met het maken van een .net Class Library-project met een klasse die implementeert de `Execute` methode van de `IDotNetActivity` interface. De gekoppelde Services, gegevenssets en uitgebreide eigenschappen in de JSON-nettolading van een activiteit van de DotNet (aangepast) zijn doorgegeven aan de methode kan worden uitgevoerd als objecten sterk getypeerd. Zie voor meer informatie over het gedrag van versie 1 [DotNet in versie 1 (aangepast)](v1/data-factory-use-custom-activities.md). Vanwege deze implementatie uw code van versie 1 DotNet activiteit heeft als doel in .net Framework 4.5.2. De versie 1 DotNet activiteit heeft ook worden uitgevoerd op de knooppunten op basis van Windows Azure Batch-Pool. 

  In de Azure Data Factory V2 aangepaste activiteit, u niet vereist voor het implementeren van een .net-interface. U kunt nu rechtstreeks uitvoeren opdrachten, scripts en uw eigen aangepaste code, die is gecompileerd als een uitvoerbaar bestand. Voor het configureren van deze implementatie, die u opgeeft de `Command` eigenschap in combinatie met de `folderPath` eigenschap. De aangepaste activiteit uploadt het uitvoerbare bestand en de bijbehorende afhankelijkheden te `folderpath` en voert u de opdracht voor u. 

  De gekoppelde Services, gegevenssets (gedefinieerd in referenceObjects) en uitgebreide eigenschappen die zijn gedefinieerd in de JSON-nettolading van een Data Factory-v2 die aangepaste activiteit kan worden geopend door uw uitvoerbaar bestand als JSON-bestanden. U kunt toegang tot de vereiste eigenschappen met behulp van een JSON-serialisatiefunctie zoals weergegeven in de voorbeeldcode van de voorgaande SampleApp.exe. 

  U kunt met de wijzigingen in de Data Factory V2 aangepaste activiteit, de logica van uw aangepaste code schrijven in uw voorkeurstaal en uitvoeren voor Windows en Linux-besturingssystemen wordt ondersteund door Azure Batch. 

  De volgende tabel beschrijft de verschillen tussen de Data Factory V2 aangepaste activiteit en de Data Factory versie 1 (aangepast) DotNet activiteit: 


|Verschillen      | Aangepaste activiteit      | versie 1 (aangepast) DotNet activiteit      |
| ---- | ---- | ---- |
|Hoe aangepaste regels is gedefinieerd      |Door op te geven van een uitvoerbaar bestand      |Door het implementeren van een .net-DLL-bestand      |
|Uitvoeringsomgeving van het aangepaste regels      |Windows- of Linux      |Windows (.Net Framework 4.5.2)      |
|Scripts uitvoeren      |Ondersteunt het uitvoeren van scripts rechtstreeks (bijvoorbeeld "cmd /c echo Hallo wereld' op de virtuele machine van Windows)      |Implementatie in de .net-DLL-bestand is vereist      |
|DataSet vereist      |Optioneel      |Vereist voor het koppelen van activiteiten en informatie doorgeven      |
|Informatie van de activiteit aan aangepaste regels doorgeven      |Via ReferenceObjects (LinkedServices en gegevenssets) en ExtendedProperties (aangepaste eigenschappen)      |Via ExtendedProperties (aangepaste eigenschappen), invoer en Uitvoergegevenssets      |
|Ophalen van informatie aangepaste regels      |Parseert activity.json linkedServices.json en opgeslagen in dezelfde map van het uitvoerbare bestand datasets.json      |Via .net SDK (.Net Frame 4.5.2)      |
|Logboekregistratie      |Schrijft rechtstreeks naar STDOUT      |Implementatie van berichtenlogboek in .net DLL-bestand      |


  Als u bestaande geschreven voor een versie 1 (aangepast) DotNet activiteit .net-code hebt, moet u uw code te werken met de huidige versie van de aangepaste activiteit wijzigen. Werk uw code door deze op hoog niveau richtlijnen te volgen:  

   - Het project wijzigen vanuit een .net-klassenbibliotheek naar een Console-App. 
   - Start uw toepassing met de `Main` methode. De `Execute` methode van de `IDotNetActivity` interface is niet langer vereist. 
   - Lezen en parseren van de gekoppelde Services, gegevenssets en activiteit met een JSON-serialisatiefunctie en niet als objecten sterk getypeerd. De waarden van de vereiste eigenschappen doorgeven aan de logica van uw belangrijkste aangepaste code. Raadpleeg de voorafgaande SampleApp.exe code als voorbeeld. 
   - Het logboek-object is niet meer ondersteund. De uitvoer van uw uitvoerbare bestand kan worden afgedrukt aan de console en wordt opgeslagen in stdout.txt. 
   - Het Microsoft.Azure.Management.DataFactories NuGet-pakket is niet langer vereist. 
   - De code compileren, het uitvoerbare bestand en de bijbehorende afhankelijkheden uploaden naar Azure Storage en definiëren van het pad in de `folderPath` eigenschap. 

Voor een compleet voorbeeld van hoe de end-to-end-DLL en pijplijn voorbeeld in de Data Factory-versie 1 artikel beschreven [aangepaste activiteiten gebruiken in een Azure Data Factory-pijplijn](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) opnieuw kan worden geschreven als een aangepaste activiteit van Data Factory, Zie [ Data Factory aangepaste activiteit voorbeeld](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFv2CustomActivitySample). 

## <a name="auto-scaling-of-azure-batch"></a>Automatische schaling van Azure Batch
U kunt ook maken met een Azure Batch-pool met **automatisch schalen** functie. U kunt bijvoorbeeld een azure batch-pool maken met 0 toegewezen virtuele machines en een formule voor automatisch schalen is op basis van het aantal in behandeling zijnde taken. 

De voorbeeldformule bereikt het volgende gedrag: wanneer de groep in eerste instantie is gemaakt, wordt 1 VM gestart. $PendingTasks metriek definieert het aantal taken in uitvoering + actief (in de wachtrij) staat.  De formule wordt het gemiddelde aantal in behandeling zijnde taken gevonden in de afgelopen 180 seconden en dienovereenkomstig TargetDedicated ingesteld. Hiermee zorgt u ervoor dat TargetDedicated nooit zich verder uitstrekt dan 25 virtuele machines. Dus als nieuwe taken worden verzonden, pool automatisch groeit en als de taken zijn voltooid, virtuele machines worden gratis één voor één en verkleint u het automatisch schalen die virtuele machines. startingNumberOfVMs en maxNumberofVMs kan worden aangepast aan uw behoeften.

De formule voor automatisch schalen:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Zie [automatisch schalen rekenknooppunten in een Azure Batch-pool](../batch/batch-automatic-scaling.md) voor meer informatie.

Als de groep met behulp van de standaard [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), de Batch-service kan enige tijd duren 15-30 minuten voor het voorbereiden van de virtuele machine voordat u de aangepaste activiteit.  Als de groep een andere autoScaleEvaluationInterval gebruikt is, kan de Batch-service autoScaleEvaluationInterval + 10 minuten duren.


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen waarin wordt uitgelegd hoe voor het transformeren van gegevens op andere manieren: 

* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive-activiteit](transform-data-using-hadoop-hive.md)
* [Pig-activiteit](transform-data-using-hadoop-pig.md)
* [MapReduce-activiteit](transform-data-using-hadoop-map-reduce.md)
* [Hadoop-streamingactiviteit](transform-data-using-hadoop-streaming.md)
* [Spark-activiteit](transform-data-using-spark.md)
* [Machine Learning-Batchuitvoering activiteit](transform-data-using-machine-learning.md)
* [De activiteit opgeslagen procedure](transform-data-using-stored-procedure.md)
