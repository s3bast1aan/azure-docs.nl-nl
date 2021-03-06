---
title: naslaginformatie over host.JSON voor Azure Functions
description: Referentiedocumentatie voor de Azure Functions host.json-bestand.
services: functions
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/12/2018
ms.author: glenga
ms.openlocfilehash: 9043add91022c2829c305425dba9c8f11b224fcf
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345511"
---
# <a name="hostjson-reference-for-azure-functions"></a>naslaginformatie over host.JSON voor Azure Functions

De *host.json* metagegevensbestand globale configuratie-opties die invloed hebben op alle functies die voor een functie-app bevat. In dit artikel bevat de instellingen die beschikbaar zijn. Het JSON-schema loopt http://json.schemastore.org/host.

Er zijn andere globale configuratie-opties in [app-instellingen](functions-app-settings.md) en in de [local.settings.json](functions-run-local.md#local-settings-file) bestand.

## <a name="sample-hostjson-file"></a>Voorbeeldbestand voor host.json

Het volgende voorbeeld *host.json* bestand heeft alle mogelijke opties opgegeven.

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

De volgende secties van dit artikel wordt uitgelegd dat elke eigenschap op het hoogste niveau. Alle zijn optioneel tenzij anders aangegeven.

## <a name="aggregator"></a>aggregator

Hiermee geeft u het aantal functieaanroepen zijn samengevoegd wanneer [berekenen van de metrische gegevens voor Application Insights](functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Eigenschap |Standaard  | Beschrijving |
|---------|---------|---------| 
|batchSize|1000|Maximum aantal aanvragen om samen te voegen.| 
|flushTimeout|00:00:30|Maximale tijd periode om samen te voegen.| 

Functieaanroepen worden geaggregeerd als de eerste dag van de twee limieten zijn bereikt.

## <a name="applicationinsights"></a>Application Insights

Besturingselementen voor de [functie steekproeven in Application Insights](functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|isEnabled|true|Hiermee schakelt lijnen of.| 
|maxTelemetryItemsPerSecond|5|De drempelwaarde op welke steekproeven wordt gestart.| 

## <a name="durabletask"></a>durableTask

Configuratie-instellingen voor [duurzame functies](durable-functions-overview.md).

```json
{
  "durableTask": {
    "HubName": "MyTaskHub",
    "ControlQueueBatchSize": 32,
    "PartitionCount": 4,
    "ControlQueueVisibilityTimeout": "00:05:00",
    "WorkItemQueueVisibilityTimeout": "00:05:00",
    "MaxConcurrentActivityFunctions": 10,
    "MaxConcurrentOrchestratorFunctions": 10,
    "AzureStorageConnectionStringName": "AzureWebJobsStorage",
    "TraceInputsAndOutputs": false,
    "LogReplayEvents": false,
    "EventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "EventGridKeySettingName":  "EventGridKey",
    "EventGridPublishRetryCount": 3,
    "EventGridPublishRetryInterval": "00:00:30"
  }
}
```

Namen van taken hub moeten beginnen met een letter en bestaan uit alleen letters en cijfers. Indien niet opgegeven, wordt de standaardnaam voor het hub van taak voor een functie-app is **DurableFunctionsHub**. Zie voor meer informatie, [taak hubs](durable-functions-task-hubs.md).

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|HubName|DurableFunctionsHub|Alternatieve [taak hub](durable-functions-task-hubs.md) namen kunnen worden gebruikt voor het isoleren van meerdere duurzame functies toepassingen van elkaar worden verbonden, zelfs als ze de dezelfde opslag back-end gebruiken.|
|ControlQueueBatchSize|32|Het aantal berichten om op te halen vanuit de besturingselement-wachtrij op een tijdstip.|
|PartitionCount |4|Het aantal partities voor de wachtrij van het besturingselement. Een positief geheel getal tussen 1 en 16 mogelijk.|
|ControlQueueVisibilityTimeout |5 minuten|De time-out voor zichtbaarheid van besturingselement voor uit wachtrij geplaatste berichten in wachtrij plaatsen.|
|WorkItemQueueVisibilityTimeout |5 minuten|De time-out voor zichtbaarheid van berichten in de wachtrij-item uit de wachtrij genomen werk.|
|MaxConcurrentActivityFunctions |10 x het aantal processors op de huidige computer|Het maximale aantal activiteitsfuncties mediataken tegelijk kunnen worden verwerkt op een afzonderlijke host-instantie.|
|MaxConcurrentOrchestratorFunctions |10 x het aantal processors op de huidige computer|Het maximale aantal activiteitsfuncties mediataken tegelijk kunnen worden verwerkt op een afzonderlijke host-instantie.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|De naam van de appinstelling met de Azure Storage-verbindingsreeks die wordt gebruikt om de onderliggende Azure Storage-resources te beheren.|
|TraceInputsAndOutputs |false|Een waarde die aangeeft of de invoer en uitvoer van functieaanroepen traceren. Het standaardgedrag voor het traceren van gebeurtenissen voor de functie kan worden uitgevoerd, moet u het aantal bytes in de geserialiseerde invoer en uitvoer voor de functieaanroepen. Dit biedt minimale informatie over hoe de invoer en uitvoer eruit zien zonder aanzienlijk groter worden de logboeken of per ongeluk gevoelige informatie naar de logboeken om vrij te geven. Deze eigenschap instelt op true, zal de functie standaard logboekregistratie om aan te melden van de volledige inhoud van de functie-invoer en uitvoer.|
|LogReplayEvents|false|Een waarde die aangeeft of orchestration opnieuw afspelen gebeurtenissen schrijven naar Application Insights.|
|EventGridTopicEndpoint ||De URL van een Azure Event Grid-aangepast onderwerp-eindpunt. Als deze eigenschap is ingesteld, worden orchestration levenscyclus van de meldingsgebeurtenissen worden gepubliceerd naar dit eindpunt. Deze eigenschap ondersteunt resolutie van App-instellingen.|
|EventGridKeySettingName ||De naam van de app-instelling met de sleutel die wordt gebruikt voor verificatie met de aangepaste Azure Event Grid-onderwerp op `EventGridTopicEndpoint`.|
|EventGridPublishRetryCount|0|Het aantal nieuwe pogingen als publiceren naar de Event Grid-onderwerp is mislukt.|
|EventGridPublishRetryInterval|5 minuten|De Event Grid publiceren interval voor opnieuw proberen in de *uu: mm:* indeling.|

Veel van deze zijn voor het optimaliseren van prestaties. Zie voor meer informatie, [prestaties en schaal](durable-functions-perf-and-scale.md).

## <a name="eventhub"></a>eventHub

Configuratie-instellingen voor [Event Hub-triggers en bindingen](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

Een lijst met functies die de host van de taak wordt uitgevoerd. Een lege matrix betekent dat alle functies uitvoeren. Bedoeld voor gebruik alleen wanneer [lokaal uitgevoerde](functions-run-local.md). In de functie-apps, gebruikt u de *function.json* `disabled` eigenschap in plaats van deze eigenschap in *host.json*.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Geeft aan dat de duur van de time-out voor alle functies. Het geldige bereik is 1 seconde tot 10 minuten in verbruiksabonnementen, en de standaardwaarde is 5 minuten. Er is geen limiet in App Service-plan en de standaardwaarde is null, wat aangeeft dat het geen time-out.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Configuratie-instellingen voor [health monitor voor de Host](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|ingeschakeld|true|Of de functie is ingeschakeld. | 
|healthCheckInterval|10 seconden|Het tijdsinterval tussen de periodieke achtergrond-status wordt gecontroleerd. | 
|healthCheckWindow|2 minuten|Een verschuivend tijdvenster gebruikt in combinatie met de `healthCheckThreshold` instelling.| 
|healthCheckThreshold|6|Maximum aantal keren dat de statuscontrole kan mislukken voordat het recyclen van een host wordt gestart.| 
|counterThreshold|0,80|De drempelwaarde waarmee een prestatiemeteritem wordt beschouwd als niet in orde.| 

## <a name="http"></a>http

Configuratie-instellingen voor [http-triggers en bindingen](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

De unieke ID voor de host van een taak. Een kleine letter GUID met streepjes u kunt verwijderen. Vereist wanneer die lokaal wordt uitgevoerd. Bij het uitvoeren in Azure Functions, een ID automatisch wordt gegenereerd als `id` wordt weggelaten.

Als u een opslagaccount voor meerdere functie-apps deelt, zorg ervoor dat elke functie-app een andere heeft `id`. U kunt weglaten de `id` eigenschap of elke functie-app handmatig in te stellen `id` op een andere waarde. De timertrigger gebruikt een opslag-vergrendeling om ervoor te zorgen dat er slechts één instantie van de timer als een functie-app wordt geschaald naar meerdere exemplaren. Als twee functie-apps dezelfde delen `id` en elk een timertrigger gebruikt, slechts één timer wordt uitgevoerd.


```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Logger

Besturingselementen voor filteren voor logboeken die is geschreven door een [ILogger object](functions-monitoring.md#write-logs-in-c-functions) of door [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|categoryFilter|N.v.t.|Hiermee geeft u filteren op categorie| 
|defaultLevel|Informatie|Voor de categorieën die niet is opgegeven in de `categoryLevels` matrix kunnen logboeken op dit niveau en hoger verzenden naar Application Insights.| 
|categoryLevels|N.v.t.|Een matrix van categorieën die Hiermee geeft u het minimale logboek-niveau te verzenden naar Application Insights voor elke categorie. Alle categorieën die met dezelfde waarde beginnen Hiermee beheert u de categorie die u hier opgeeft, en meer waarden hebben voorrang. In het voorgaande voorbeeld *host.json* -bestand, alle categorieën die beginnen met "Host.Aggregator" log op `Information` niveau. Alle andere categorieën die beginnen met 'Host', zoals "Host.Executor", zich aanmelden `Error` niveau.| 

## <a name="queues"></a>wachtrijen

Configuratie-instellingen voor [Storage queue-triggers en bindingen](functions-bindings-storage-queue.md).

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="servicebus"></a>serviceBus

Configuratie-instelling voor [Service Bus-triggers en bindingen](functions-bindings-service-bus.md).

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

## <a name="singleton"></a>Singleton

Configuratie-instellingen voor het gedrag van Singleton vergrendelen. Zie voor meer informatie, [GitHub-probleem over de ondersteuning van singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|lockPeriod|00:00:15|De periode die functie niveau vergrendelingen voor worden uitgevoerd. De vergrendelingen voor automatisch verlengen.| 
|listenerLockPeriod|00:01:00|De periode die listener vergrendelingen voor worden uitgevoerd.| 
|listenerLockRecoveryPollingInterval|00:01:00|Het tijdsinterval voor listener-lock herstel moet worden gebruikt als een listener-vergrendeling kan niet worden verkregen bij het opstarten.| 
|lockAcquisitionTimeout|00:01:00|De maximale hoeveelheid tijd die de runtime wordt geprobeerd om een vergrendeling te verkrijgen.| 
|lockAcquisitionPollingInterval|N.v.t.|Het interval tussen pogingen van vergrendeling ophalen.| 

## <a name="tracing"></a>tracering

Configuratie-instellingen voor logboeken die u met behulp van maakt een `TraceWriter` object. Zie [C# logboekregistratie](functions-reference-csharp.md#logging) en [Node.js logboekregistratie](functions-reference-node.md#writing-trace-output-to-the-console). 

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|consoleLevel|informatie|Het traceringsniveau bepaald voor console-aanmelding. Opties zijn: `off`, `error`, `warning`, `info`, en `verbose`.|
|fileLoggingMode|debugOnly|Het traceringsniveau bepaald voor logboekregistratie. Opties zijn `never`, `always`, `debugOnly`.| 

## <a name="watchdirectories"></a>watchDirectories

Een set [gedeelde code mappen](functions-reference-csharp.md#watched-directories) die moet worden gecontroleerd op wijzigingen.  Zorgt ervoor dat wanneer de code in deze mappen wordt gewijzigd, de wijzigingen worden doorgevoerd door uw functies.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het bijwerken van het bestand host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Zie de globale instellingen in omgevingsvariabelen](functions-app-settings.md)
