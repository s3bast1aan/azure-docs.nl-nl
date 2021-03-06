---
title: Azure Kubernetes-cluster bewaken met Datadog
description: Bewaking van Kubernetes-cluster in Azure Container Service met Datadog
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 0a3f0baa4998dbc594023935575d659f7d45bbb9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38629066"
---
# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>Een Azure Container Service-cluster bewaken met DataDog

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Vereisten
In dit scenario wordt ervan uitgegaan dat u hebt [gemaakt van een Kubernetes-cluster met behulp van Azure Container Service](container-service-kubernetes-walkthrough.md).

Ook wordt ervan uitgegaan dat u hebt de `az` Azure cli en `kubectl` hulpprogramma's zijn geïnstalleerd.

U kunt testen als u hebt de `az` hulpprogramma is geïnstalleerd door uit te voeren:

```console
$ az --version
```

Als u geen de `az` hulpprogramma is geïnstalleerd, worden er instructies [hier](https://github.com/azure/azure-cli#installation).

U kunt testen als u hebt de `kubectl` hulpprogramma is geïnstalleerd door uit te voeren:

```console
$ kubectl version
```

Als u geen `kubectl` geïnstalleerd, u kunt uitvoeren:

```console
$ az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog
Datadog is een bewakingsservice waarmee gegevens uit uw containers in uw Azure Container Service-cluster verzamelt. Datadog heeft een Docker-integratie-Dashboard waar u specifieke metrische gegevens in uw containers kunt zien. Metrische gegevens die zijn verzameld uit uw containers worden georganiseerd door CPU, geheugen, netwerk- en i/o. Datadog splitst metrische gegevens in containers en afbeeldingen.

U moet eerst [een account maken](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>De Datadog-Agent installeren met een DaemonSet
DaemonSets worden gebruikt door Kubernetes om uit te voeren van één exemplaar van een container op elke host in het cluster.
Ze zijn ideaal voor het uitvoeren van bewakingsagents.

Nadat u Datadog hebt aangemeld, kunt u volgen de [Datadog instructies](https://app.datadoghq.com/account/settings#agent/kubernetes) Datadog om agents te installeren op uw cluster met behulp van een DaemonSet.

## <a name="conclusion"></a>Conclusie
Dat is alles. Wanneer de agents actief en werkend zijn ziet u gegevens in de console in een paar minuten. Ga naar de geïntegreerde [kubernetes-dashboard](https://app.datadoghq.com/screen/integration/kubernetes) voor een overzicht van het cluster.
