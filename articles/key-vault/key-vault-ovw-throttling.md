---
title: Azure Key Vault-beperkingsrichtlijnen
description: Beperking van de Sleutelkluis beperkt het aantal gelijktijdige aanroepen om te voorkomen dat overmatig gebruik van bronnen.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 05/10/2018
ms.author: alleonar
ms.openlocfilehash: 59968f2bccbe2828ebe5fb33c57ed28d4f8509b6
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34067687"
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault-beperkingsrichtlijnen

Beperking is een proces dat u starten die het aantal gelijktijdige aanroepen naar de Azure-service beperkt om te voorkomen dat overmatig gebruik van bronnen. Azure-kluis (Azure sleutel Sleutelkluis) is ontworpen voor het verwerken van een groot aantal aanvragen. Als er een groot aantal aanvragen optreedt, kunt beperking van de client aanvragen onderhouden voor optimale prestaties en betrouwbaarheid van de Azure Sleutelkluis-service.

Limieten voor bandbreedteregeling variëren, afhankelijk van het scenario. Bijvoorbeeld, als u een groot aantal schrijfbewerkingen uitvoert, is de mogelijkheid voor beperking hoger dan als u alleen leesbewerkingen uitvoert.

## <a name="how-does-key-vault-handle-its-limits"></a>Hoe wordt de grenzen in Sleutelkluis verwerkt?

Er zijn er Servicelimieten in Sleutelkluis te voorkomen dat misbruik van resources en ervoor zorgen dat quality of service voor alle clients van de Sleutelkluis. Wanneer een service-drempelwaarde wordt overschreden, beperkt Sleutelkluis alle verdere aanvragen van die client voor een bepaalde periode. Als dit gebeurt, Sleutelkluis retourneert HTTP-statuscode 429 (te veel aanvragen), en de aanvragen mislukken. Ook mislukte aanvragen die resulteren in een 429 telling voor de versnelling limieten bijgehouden door de Sleutelkluis. 

Als u een geldige business case voor hogere versnelling limieten hebt, neem dan contact met ons.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Hoe uw app in reactie op Servicelimieten beperken

Hieronder vindt u **aanbevolen procedures** voor beperking van uw app:
- Verminder het aantal bewerkingen per aanvraag.
- Verlaag de frequentie van aanvragen.
- Vermijd directe nieuwe pogingen. 
    - Alle aanvragen samenvoegen op basis van uw gebruikslimieten.

Wanneer u uw app foutafhandeling implementeert, moet u de HTTP-foutcode 429 gebruiken voor het detecteren van de client-side '-beperking. Als de aanvraag opnieuw met een foutcode HTTP 429 mislukt, doe u nog steeds een limiet voor de Azure-service. Blijven gebruiken van de aanbevolen clientzijde methode beperken, de aanvraag opnieuw proberen totdat dit is gelukt.

### <a name="recommended-client-side-throttling-method"></a>Aanbevolen bandbreedteregeling client-side '-methode

In de HTTP-foutcode 429 beginnen met beperking van de client met een benadering exponentieel uitstel:

1. Wacht 1 seconde, de aanvraag opnieuw proberen
2. Als u nog steeds beperkt 2 seconden wachten, aanvraag opnieuw proberen
3. Als u nog steeds beperkt wacht 4 seconden, aanvraag opnieuw proberen
4. Als u nog steeds beperkt 8 seconden wachten, aanvraag opnieuw proberen
5. Als u nog steeds beperkt 16 seconden wachten, aanvraag opnieuw proberen

Op dit moment moet u niet worden opgehaald HTTP 429 responscodes.

## <a name="see-also"></a>Zie ook

Zie voor een beter afdrukstand van beperking op de Microsoft Cloud [patroon beperking](https://docs.microsoft.com/azure/architecture/patterns/throttling).

