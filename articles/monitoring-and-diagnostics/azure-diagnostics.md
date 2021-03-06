---
title: Overzicht van de Azure Diagnostics-extensie
description: Azure diagnostics gebruiken voor foutopsporing, meten van prestaties, bewaking, analyse van het netwerkverkeer in cloudservices, virtuele machines en service fabric
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/13/2018
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: b00d774ec59755288b8660d238c7b8dfc9a89eab
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089890"
---
# <a name="what-is-azure-diagnostics-extension"></a>Wat is Azure Diagnostics-extensie
De Azure Diagnostics-extensie is een agent in Azure waarmee u het verzamelen van diagnostische gegevens op een geïmplementeerde toepassing. U kunt de extensie voor diagnostische gegevens gebruiken uit een aantal verschillende bronnen. Op dit moment ondersteund zijn Azure-Cloudservice (klassiek) Web- en werkrollen, virtuele Machines, Virtual Machine Scale sets en Service Fabric. Andere Azure-services hebben diagnostische gegevens van andere methoden. Zie [overzicht van de bewaking in Azure](monitoring-overview.md). 

## <a name="linux-agent"></a>Linux-Agent
Een [Linux-versie van de extensie](../virtual-machines/linux/diagnostic-extension.md) is beschikbaar voor virtuele Machines waarop Linux wordt uitgevoerd. De statistieken die worden verzameld en het gedrag verschillen van de Windows-versie. 

## <a name="data-you-can-collect"></a>Gegevens die u kunt verzamelen
De Azure Diagnostics-extensie kan de volgende typen gegevens verzamelen:

| Gegevensbron | Beschrijving |
| --- | --- |
| Prestatiemeteritems |Besturingssysteem- en aangepaste prestatiemeteritems |
| Toepassingslogboeken |Traceringsberichten die is geschreven door uw toepassing |
| Windows-gebeurtenislogboeken |Informatie verzonden naar het aanmeldingssysteem voor Windows-gebeurtenis |
| .NET-gebeurtenisbron |Code schrijven met behulp van de .NET gebeurtenissen [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) klasse |
| IIS-logboeken |Informatie over IIS-websites |
| ETW op basis van manifest |Event Tracing voor Windows-gebeurtenissen die worden gegenereerd door een proces. (1) |
| Crashdumps |Informatie over de status van het proces in het geval van een crash van een toepassing |
| Aangepaste foutenlogboeken |Logboeken die zijn gemaakt door uw toepassing of service |
| Logboeken met diagnostische Azure-infrastructuur |Informatie over diagnostische gegevens zelf |

(1) uitvoeren als u een lijst van ETW-providers, `c:\Windows\System32\logman.exe query providers` in een consolevenster weergegeven op de computer die u wilt verzamelen van gegevens uit. 

## <a name="data-storage"></a>Gegevensopslag
De extensie slaat de gegevens op in een [Azure Storage-account](azure-diagnostics-storage.md) die u opgeeft. 

U kunt ook verzenden naar [Application Insights](../application-insights/app-insights-cloudservices.md). Een andere optie is om te streamen naar [Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md), die vervolgens kunt u deze verzenden naar het controleren van niet-Azure-services. 


## <a name="versioning-and-configuration-schema"></a>Versiebeheer en configuratie-schema
Zie [versiegeschiedenis van Azure Diagnostics en het Schema](azure-diagnostics-versioning-history.md).


## <a name="next-steps"></a>Volgende stappen
Kies welke service u wilt verzamelen van diagnostische gegevens op en gebruik de volgende artikelen aan de slag. Gebruik de algemene Azure diagnostics-koppelingen ter referentie voor specifieke taken.

## <a name="cloud-services-using-azure-diagnostics"></a>Cloudservices met Azure Diagnostics
* Als u Visual Studio, Zie [Visual Studio gebruiken om een Cloud Services-toepassing](../vs-azure-tools-debug-cloud-services-virtual-machines.md) aan de slag. Raadpleegt u
* [Het bewaken van cloudservices met Azure Diagnostics](../cloud-services/cloud-services-how-to-monitor.md)
* [Azure Diagnostics in een Cloud Services-toepassing instellen](../cloud-services/cloud-services-dotnet-diagnostics.md)

Zie voor meer geavanceerde onderwerpen

* [Met behulp van Azure Diagnostics voor Cloudservices met Application Insights](../application-insights/app-insights-cloudservices.md)
* [De stroom van een Cloud Services-toepassing met Azure Diagnostics traceren](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [PowerShell gebruiken voor het instellen van diagnostische gegevens op Cloud Services](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtuele machines
* Als u Visual Studio, Zie [Gebruik Visual Studio te traceren Azure Virtual Machines](../vs-azure-tools-debug-cloud-services-virtual-machines.md) aan de slag. Raadpleegt u
* [Azure Diagnostics op een Azure-Machine instellen](../virtual-machines-dotnet-diagnostics.md)

Zie voor meer geavanceerde onderwerpen

* [PowerShell gebruiken voor het instellen van diagnostische gegevens op Azure Virtual Machines](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Een virtuele Windows-machine met controle en diagnostische gegevens met behulp van Azure Resource Manager-sjabloon maken](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Aan de slag op [bewaken van een Service Fabric-toepassing](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Wanneer u in dit artikel, zijn er veel artikelen van andere Service Fabric diagnostische gegevens beschikbaar zijn in de navigatiestructuur aan de linkerkant.

## <a name="general-articles"></a>Algemene artikelen
* Meer informatie over het [prestatiemeteritems gebruiken in Azure Diagnostics](../cloud-services/diagnostics-performance-counters.md).
* Als u problemen bij het starten van diagnostische gegevens ondervindt of raadpleegt u uw gegevens zoeken in Azure storage-tabellen, [het oplossen van Azure Diagnostics](azure-diagnostics-troubleshooting.md)
