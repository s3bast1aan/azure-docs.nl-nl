---
title: Aandachtspunten voor de integratie van connectiviteit netwerk voor Azure Stack-geïntegreerde systemen rand | Microsoft Docs
description: Meer informatie over wat u kunt doen om te plannen voor datacenter rand verbinding met het netwerk met meerdere knooppunten Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 260c58ad9099a4532c8a6558cfcf5c13f0fc8d52
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282005"
---
# <a name="border-connectivity"></a>Rand-connectiviteit 
Planning van integratie van het netwerk is een belangrijk vereist item voor geslaagde implementatie van de geïntegreerde Azure Stack-systemen, bewerking en beheren. Rand connectiviteit planning begint met het wel of niet gebruik van dynamische routering met border gateway protocol (BGP) te kiezen. Dit is vereist voor het toewijzen van een 16-bits autonoom systeemnummer BGP (openbaar of privé) of met behulp van statische routering, waarbij een standaard statische route is toegewezen aan de rand-apparaten.

> [!IMPORTANT]
> De top van rack (TOR) switches vereisen Layer 3-uplinks met Point-to-Point IP-adressen (/ 30 netwerken) geconfigureerd op de fysieke interfaces. Het wordt niet ondersteund voor het gebruik van laag 2 uplinks met TOR-switches ondersteunen Azure Stack-bewerkingen. 

## <a name="bgp-routing"></a>BGP-routering
Met behulp van een dynamisch routeringsprotocol zoals BGP zorgt ervoor dat uw systeem altijd op de hoogte van wijzigingen in het netwerk is en vereenvoudigt het beheer. 

Zoals u in het volgende diagram, is adverteren van de privé IP-adres ruimte op de TOR-switch beperkt met behulp van een voorvoegsel-lijst. De lijsten voorvoegsel weigert het particuliere IP-subnetten en toe te passen als een route-kaart op de verbinding tussen de TOR- en de rand.

De Software Load Balancer (SLB) die worden uitgevoerd binnen de Azure Stack-oplossing van collega's op de TOR-apparaten, zodat deze de VIP-adressen dynamisch kunt adverteren.

Aggregatie op de hosts en HSRP of VRRP waarmee het netwerk om ervoor te zorgen dat gebruikersverkeer onmiddellijk en transparant van fout herstelt, de VPC of MLAG geconfigureerd tussen de TOR-apparaten kunt het gebruik van meerdere chassis koppeling redundantie voor de IP-netwerken.

![BGP-routering](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>Statische routering
Statische routering vereist aanvullende configuratie aan de rand-apparaten. Het meer handmatige interventie nodig is en beheer, evenals een grondige analyse voor wijzigings- en problemen veroorzaakt door een configuratiefout meer tijd kan duren terug te draaien, afhankelijk van de wijzigingen. Het is niet de aanbevolen methode om routering, maar dit wordt ondersteund.

Als u wilt integreren Azure Stack in uw netwerkomgeving met statische routering, alle vier fysieke koppelingen tussen de rand en de TOR-apparaat moet zijn verbonden en hoge beschikbaarheid vanwege hoe statische routering werkt niet worden gegarandeerd.

Het apparaat van rand moet worden geconfigureerd met statische routes die verwijst naar de TOR-apparaten P2P voor verkeer dat bestemd is voor het externe netwerk of openbare VIP's en het infrastructuurnetwerk. Deze vereist statische routes met het BMC-netwerk voor de implementatie. Klanten kunnen kiezen om te laten statische routes in de rand voor toegang tot bepaalde resources die zich in de BMC-netwerk bevinden.  Statische routes toe te voegen *switch infrastructuur* en *overschakelen management* netwerken is optioneel.

De TOR-apparaten worden geconfigureerd geleverd met een standaard statische route verzendt al het verkeer naar de rand-apparaten. De enige uitzondering verkeer naar de standaardregel is voor de persoonlijke ruimte, dat is geblokkeerd met behulp van een toegangsbeheerlijst toegepast op de TOR verbinding van de rand.

Statische routering geldt alleen voor de uplinks tussen de TOR- en randeigenschappen switches. Dynamische BGP-routering wordt gebruikt binnen het rack omdat het is een essentieel hulpprogramma voor de SLB en andere onderdelen en kan niet worden uitgeschakeld of verwijderd.

![Statische routering](media/azure-stack-border-connectivity/static-routing.png)

## <a name="transparent-proxy"></a>Transparante proxy
Als uw datacenter al het verkeer vereist naar een proxy gebruikt, moet u een *transparante proxy* voor het verwerken van al het verkeer van het rack verwerkt; het op basis van beleid voor het scheiden van verkeer tussen de zones op uw netwerk.

> [!IMPORTANT]
> De Azure Stack-oplossing biedt geen ondersteuning voor normale web proxy's.  

Normale communicatie in de netwerklaag onderschept een transparante proxy (ook wel bekend als een onderschept, inline of geforceerde proxy) zonder speciale clientconfiguratie. Clients moeten niet worden op de hoogte van de aanwezigheid van de proxy.

![Transparante proxy](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>Volgende stappen
[DNS-integratie](azure-stack-integrate-dns.md)