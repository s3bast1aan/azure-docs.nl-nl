---
title: Metrische gegevens, waarschuwingen en diagnostische logboeken voor Azure Batch | Microsoft Docs
description: Registreren en analyseren van diagnostische gebeurtenissen voor Azure Batch-accountresources zoals pools en taken.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 04/05/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 54034b9a851fc6f06f97be9cfd5f261465bad455
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248259"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch metrische gegevens, waarschuwingen en logboeken voor diagnostische evaluatie en bewaking

In dit artikel wordt uitgelegd hoe u voor het bewaken van een Batch-account met behulp van functies van [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Azure Monitor verzamelt [metrische gegevens](../monitoring-and-diagnostics/monitoring-overview-metrics.md) en [diagnostische logboeken](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) voor resources in uw Batch-account. Verzamelen en gebruiken deze gegevens op verschillende manieren voor het bewaken van uw Batch-account en problemen diagnosticeren. U kunt ook configureren [metrische waarschuwingen](../monitoring-and-diagnostics/monitoring-overview-alerts.md#alerts-on-azure-monitor-data) , zodat u meldingen ontvangen wanneer een metrische waarde een bepaalde waarde heeft bereikt. 

## <a name="batch-metrics"></a>Metrische gegevens voor batch

Metrische gegevens zijn Azure telemetriegegevens (ook wel prestatiemeteritems) die door uw Azure-resources die worden verbruikt door de service Azure Monitor. Voorbeeld van de metrische gegevens in een Batch-account zijn: Pool maken gebeurtenissen, aantal van de knooppunten met lage prioriteit en taak voltooid. 

Zie de [lijst van ondersteunde metrische gegevens voor Batch](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftbatchbatchaccounts).

Metrische gegevens zijn:

* Standaard ingeschakeld in elke Batch-account zonder aanvullende configuratie
* Elke minuut gegenereerd
* Niet permanent automatisch, maar beschikken over een 30-daagse rolling overzicht. U kunt metrische gegevens van activiteiten behouden als onderdeel van [registratie in diagnoselogboek](#work-with-diagnostic-logs).

### <a name="view-metrics"></a>Metrische gegevens weergeven

Bekijk metrische gegevens voor uw Batch-account in Azure portal. De **overzicht** pagina voor het account standaard ziet u het knooppunt, core en taak metrische gegevens. 

Alle Batch-account metrische gegevens weergeven: 

1. Klik in de portal op **alle services** > **Batch-accounts**, en klik vervolgens op de naam van uw Batch-account.
2. Onder **bewaking**, klikt u op **metrische gegevens**.
3. Selecteer een of meer van de metrische gegevens. Als u wilt, selecteert u metrische gegevens voor aanvullende resources met behulp van de **abonnementen**, **resourcegroep**, **resourcetype**, en **Resource** vervolgkeuzelijsten.

    ![Metrische gegevens voor batch](media/batch-diagnostics/metrics-portal.png)

Om op te halen metrische gegevens via een programma, gebruikt u de Azure Monitor-API's. Zie bijvoorbeeld [ophalen van Azure Monitor metrics met .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-alerts"></a>Batch metrische waarschuwingen

Configureer desgewenst bijna-realtime *metrische waarschuwingen* die wordt geactiveerd wanneer de waarde van een opgegeven metrische gegevens van een drempel die u toewijst overschrijden. De waarschuwing genereert een [melding](../monitoring-and-diagnostics/insights-alerts-portal.md) u kiest u wanneer de waarschuwing is 'geactiveerd' (als de drempelwaarde is overschreden en de waarschuwing voorwaarde wordt voldaan) evenals bij is 'opgelost' (wanneer de drempel voor het opnieuw is overschreden en de voorwaarde is geen meer voldaan aan). 

Bijvoorbeeld, als u wilt een waarschuwing voor metrische gegevens configureren wanneer uw kerngeheugens met lage prioriteit op een bepaald niveau, valt zodat u de samenstelling van uw toepassingen kunt aanpassen.

Het configureren van een waarschuwing voor metrische gegevens in de portal:

1. Klik op **Alle services** > **Batch-accounts** en klik vervolgens op de naam van uw Batch-account.
2. Onder **bewaking**, klikt u op **waarschuwingsregels** > **metrische waarschuwing toevoegen**.
3. Selecteer een metrische waarde, de voorwaarde voor een waarschuwing (zoals wanneer een metrische waarde een bepaalde waarde gedurende een periode overschrijdt) en een of meer meldingen.

U kunt ook een bijna realtime waarschuwingen met de [REST-API](). Zie voor meer informatie, [de nieuwere metrische waarschuwingen voor Azure-services in Azure portal gebruiken](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md)
## <a name="batch-diagnostics"></a>Diagnostische gegevens van batch

Diagnostische logboeken bevatten gegevens die zijn gegenereerd door Azure-resources die de werking van elke resource beschrijven. Voor Batch, kunt u de volgende logboeken te verzamelen:

* **Servicelogboeken** gebeurtenissen die zijn gegenereerd door de Azure Batch-service tijdens de levensduur van een afzonderlijke Batch-resource, zoals een groep of -taak. 

* **Metrische gegevens** logboeken op het accountniveau. 

Instellingen om te verzamelen van logboeken met diagnostische gegevens worden niet standaard ingeschakeld. Diagnostische instellingen voor elke Batch-account dat u wilt bewaken expliciet inschakelen.

### <a name="log-destinations"></a>Logboek bestemmingen

Een veelvoorkomend scenario is een Azure Storage-account selecteren als het doel van het logboek. Voor het opslaan van Logboeken in Azure Storage, moet u het account maken voordat u het verzamelen van logboeken inschakelt. Als u een storage-account gekoppeld aan uw Batch-account, kunt u dat account als de logboekbestemming. 

Andere optionele bestemmingen voor diagnostische logboeken:

* Stream gebeurtenissen uit batches met diagnostische logboeken naar een [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Eventhubs kunnen miljoenen gebeurtenissen per seconde, die u kunt vervolgens transformeren en opslaan met elke gewenste realtime analyseprovider opnemen. 

* Verzenden van diagnostische logboeken naar [Azure Log Analytics](../log-analytics/log-analytics-overview.md), kunt u ze in de portal voor Operations Management Suite (OMS) te analyseren, of ze voor analyse in Power BI of Excel exporteren.

> [!NOTE]
> Er worden mogelijk extra kosten als u wilt opslaan of verwerken van diagnostische gegevens van een met Azure-services. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Verzamelen van diagnostische logboeken voor Batch

1. Klik in de portal op **alle services** > **Batch-accounts**, en klik vervolgens op de naam van uw Batch-account.
2. Onder **bewaking**, klikt u op **diagnostische logboeken** > **diagnostische gegevens inschakelen**.
3. In **diagnostische instellingen**, voer een naam voor de instelling en kies een doel van het logboek (bestaande Storage-account, Event Hub of Log Analytics). Selecteer een of beide **ServiceLog** en **AllMetrics**.

    Wanneer u een storage-account selecteert, moet u eventueel een bewaarbeleid instellen. Als u een aantal dagen voor bewaarperiode niet opgeeft, worden gegevens worden bewaard gedurende de levensduur van het storage-account.

4. Klik op **Opslaan**.

    ![Diagnostische gegevens van batch](media/batch-diagnostics/diagnostics-portal.png)

Andere opties om logboekbestanden te verzamelen zijn onder andere: Azure Monitor gebruiken in de portal voor diagnostische instellingen configureren, gebruikt u een [Resource Manager-sjabloon](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md), of gebruikt u Azure PowerShell of Azure CLI. Zie [verzamelen en gebruiken van logboekgegevens van uw Azure-resources](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs).


### <a name="access-diagnostics-logs-in-storage"></a>Toegang tot diagnostische logboeken in de opslag

Als u diagnostische logboeken voor Batch in een opslagaccount archiveren, wordt een opslagcontainer wordt gemaakt in de storage-account als een gerelateerde gebeurtenis zich voordoet. BLOBs zijn gemaakt op basis van een naamgevingspatroon uit het volgende:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Voorbeeld:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Elke PT1H.json-blob-bestand bevat JSON-indeling gebeurtenissen die hebben plaatsgevonden binnen het uur dat is opgegeven in de blob-URL (bijvoorbeeld, h = 12). Tijdens het huidige uur worden gebeurtenissen toegevoegd aan het bestand PT1H.json wanneer deze zich voordoen. De minuutwaarde (m = 00) is altijd 00 omdat logboekgebeurtenissen voor diagnostische zijn onderverdeeld in afzonderlijke blobs per uur. (Alle tijden zijn in UTC).


Zie voor meer informatie over het schema van logboeken met diagnostische gegevens in de storage-account, [diagnostische logboeken archiveren Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

Als u wilt toegang krijgen tot de logboeken in uw storage-account via een programma, de opslag-API's te gebruiken. 

### <a name="service-log-events"></a>Gebeurtenissen van de service-logboek
Azure Batch-Service zich aanmeldt, als die worden verzameld, bevatten gebeurtenissen die zijn gegenereerd door de Azure Batch-service tijdens de levensduur van een afzonderlijke Batch-bron, zoals een groep of -taak. Elke gebeurtenis verzonden door de Batch wordt geregistreerd in JSON-indeling. Dit is bijvoorbeeld de hoofdtekst van een voorbeeld van een **gebeurtenis pool maken**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

De Batch-service verzendt momenteel de volgende Service-logboek-gebeurtenissen. Deze lijst zijn mogelijk niet volledig, omdat er aanvullende gebeurtenissen zijn toegevoegd sinds dit artikel voor het laatst is bijgewerkt.

| **Gebeurtenissen van de service-logboek** |
| --- |
| [Groep maken](batch-pool-create-event.md) |
| [Pool verwijderen starten](batch-pool-delete-start-event.md) |
| [Pool verwijderen voltooid](batch-pool-delete-complete-event.md) |
| [Pool resize start](batch-pool-resize-start-event.md) |
| [Formaat van pool wijzigen voltooid](batch-pool-resize-complete-event.md) |
| [Taak starten](batch-task-start-event.md) |
| [De taak is voltooid](batch-task-complete-event.md) |
| [Taak is mislukt](batch-task-fail-event.md) |



## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Batch-API's en -hulpprogramma's](batch-apis-tools.md) die beschikbaar zijn voor het bouwen van Batch-oplossingen.
* Meer informatie over [Batch bewakingsoplossingen](monitoring-overview.md).
