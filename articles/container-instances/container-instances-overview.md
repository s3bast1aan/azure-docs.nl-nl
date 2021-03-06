---
title: Overzicht van Azure Container Instances
description: Inzicht krijgen in Azure Container Instances
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 07/19/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 953d1dfd633f2fee52a2e6d197c6f32e7ab053f7
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160447"
---
# <a name="azure-container-instances"></a>Azure Container Instances

Containers worden de voorkeursmethode voor het verpakken, implementeren en beheren van cloudtoepassingen. Azure Container Instances is de snelste en meest eenvoudige methode voor het uitvoeren van een container in Azure, waarbij het niet nodig is om virtuele machines te beheren of het serviceniveau te verhogen.

Azure Container Instances is een ideale oplossing voor elk scenario dat kan werken in geïsoleerde containers, met inbegrip van eenvoudige toepassingen, taakautomatisering en het bouwen van taken. Voor scenario's waarin u een volledige indeling met containers nodig hebt, waaronder servicedetectie in meerdere containers, automatisch schalen en gecoördineerde toepassingsupgrades, raden wij [Azure Kubernetes Service (AKS)](../aks/index.yml) aan.

## <a name="fast-startup-times"></a>Snel opstarten

Containers bieden aanzienlijke opstartvoordelen ten opzichte van virtuele machines. Met Azure Container Instances kunnen containers in Azure in enkele seconden worden gestart, zonder dat u VM’s hoeft in te richten en te beheren.

## <a name="public-ip-connectivity-and-dns-name"></a>Openbare IP-verbinding en DNS-naam

Met Azure Container Instances kunt u uw containers direct weergeven op internet met een IP-adres en een volledig gekwalificeerde domeinnaam. Wanneer u een containerexemplaar maakt, kunt u een aangepast DNS-naamlabel opgeven, zodat uw toepassing bereikbaar is via *aangepastlabel*.*azureregio*.azurecontainer.io.

## <a name="hypervisor-level-security"></a>Beveiliging op hypervisorniveau

In het verleden boden containers toepassingsafhankelijke isolatie en resourcebesturing, maar werden ze niet voldoende veilig beschouwd voor onveilig multitenant gebruik. Met Azure Container Instances is uw toepassing gegarandeerd even geïsoleerd in een container als deze op een VM zou zijn.

## <a name="custom-sizes"></a>Aangepaste grootten

Containers zijn meestal geoptimaliseerd voor het uitvoeren van één toepassing, maar de exacte behoeften van deze toepassingen kunnen aanzienlijk verschillen. Azure Container Instances biedt een optimaal gebruik door exacte specificaties van CPU-kernen en -geheugen toe te staan. U betaalt op basis van wat u nodig hebt en wordt per seconde gefactureerd, zodat u uw uitgaven kunt verfijnen op basis van werkelijke behoefte.

## <a name="persistent-storage"></a>Permanente opslag

Voor het ophalen en persisteren van statuswaarden met exemplaren van Azure Container Instances, bieden we direct [koppelen van Azure Files-shares](container-instances-mounting-azure-files-volume.md).

## <a name="linux-and-windows-containers"></a>Linux- en Windows-containers

Met Azure Container Instances kunt u Windows- en Linux-containers met dezelfde API plannen. Geef gewoon het type besturingssysteem op wanneer u de [containergroepen](container-instances-container-groups.md) maakt.

Sommige functies zijn momenteel beperkt tot Linux-containers. Terwijl er hard wordt gewerkt om functiepariteit beschikbaar te maken voor Windows-containers, kunt u [Quota en beschikbaarheid in regio's voor Azure Container Instances](container-instances-quotas.md) raadplegen voor informatie over de huidige verschillen tussen platforms.

Azure Container Instances biedt ondersteuning voor Windows-installatiekopieën op basis van LTSC-versies (Long-Term Servicing-kanaal). SAC-releases (Semi-Annual-kanaal) van Windows, zoals 1709 en 1803 worden niet ondersteund.

## <a name="co-scheduled-groups"></a>Samen geplande groepen

Azure Container Instances biedt ondersteuning voor planning van [meerdere containergroepen](container-instances-container-groups.md) die een hostmachine, lokaal netwerk, opslag en levenscyclus delen. Hierdoor kunt u uw hoofdtoepassingscontainer combineren met andere ondersteunende rolcontainers, zoals het in een logboek vastleggen van sidecars.

## <a name="next-steps"></a>Volgende stappen

Probeer met behulp van onze snelstart een container te implementeren in Azure met een enkele opdracht:

> [!div class="nextstepaction"]
> [Snelstart voor Azure Container Instances](container-instances-quickstart.md)
