---
title: Wat is Azure DNS?
description: Overzicht van DNS-hostingservice op Microsoft Azure. Host uw domein op Microsoft Azure.
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: overview
ms.date: 6/7/2018
ms.author: victorh
ms.openlocfilehash: e95617664ee30f1b9253f1892176fd39649ee2c2
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39174629"
---
# <a name="what-is-azure-dns"></a>Wat is Azure DNS?

Azure DNS is een service voor het hosten van DNS-domeinen die naamomzetting verzorgt met behulp van de Microsoft Azure-infrastructuur. Door uw domeinen in Azure te hosten, kunt u uw DNS-records met dezelfde referenties, API's, hulpprogramma's en facturering beheren als voor uw andere Azure-services.

U kunt Azure DNS niet gebruiken om een ​​domeinnaam te kopen. Tegen een jaarlijkse vergoeding kunt u een domeinnaam kopen met behulp van [Azure Web Apps](https://docs.microsoft.com/en-us/azure/app-service/custom-dns-web-site-buydomains-web-app#buy-the-domain) of een externe domeinnaamregistrar. Uw domeinen kunnen vervolgens worden gehost in Azure DNS voor recordbeheer. Zie [Een domein aan Azure DNS overdragen](dns-domain-delegation.md) voor meer informatie.

De volgende functies zijn opgenomen in Azure DNS:

## <a name="reliability-and-performance"></a>Betrouwbaarheid en prestaties

DNS-domeinen in Azure DNS worden gehost op het wereldwijde netwerk van Azure DNS-naamservers. Azure DNS maakt gebruik van anycast-netwerken, zodat elke DNS-query wordt beantwoord door de dichtstbijzijnde beschikbare DNS-server. Dit biedt zowel snelle prestaties als hoge beschikbaarheid voor uw domein.

## <a name="security"></a>Beveiliging

De Azure DNS-service is gebaseerd op Azure Resource Manager. Dus krijgt u Resource Manager-functies zoals:

* [op rollen gebaseerd toegangsbeheer](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#access-control) om te bepalen wie er toegang heeft tot bepaalde acties voor uw organisatie.

* [activiteitenlogboeken](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#activity-logs) om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd, of te gebruiken bij het opsporen van fouten.

* [resourcevergrendeling](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-lock-resources) om een ​​abonnement, resourcegroep of bron te vergrendelen om te voorkomen dat andere gebruikers in uw organisatie per ongeluk kritieke resources verwijderen of wijzigen.

Zie voor meer informatie [DNS-zones en -records beschermen](dns-protect-zones-recordsets.md). 


## <a name="ease-of-use"></a>Gebruiksgemak

De Azure DNS-service kan DNS-records voor uw Azure-services beheren en kan ook DNS verzorgen voor uw externe resources. Azure DNS is geïntegreerd in Azure Portal en gebruikt dezelfde referenties, hetzelfde ondersteuningscontract en dezelfde facturering als uw andere Azure-services. 

De facturering van DNS vindt plaats op basis van het aantal DNS-zones dat in Azure wordt gehost en het aantal DNS-query's. Zie [Prijzen voor Azure DNS](https://azure.microsoft.com/pricing/details/dns/) voor meer informatie over prijzen.

Uw domeinen en records kunnen worden beheerd met behulp van Azure Portal, Azure PowerShell-cmdlets en de platformoverschrijdende Azure CLI. Toepassingen waarvoor automatisch DNS-beheer vereist is, kunnen met de service worden geïntegreerd met behulp van de REST-API en SDK's.

## <a name="customizable-virtual-networks-with-private-domains"></a>Aanpasbare virtuele netwerken met privédomeinen

Azure DNS ondersteunt ook privé-DNS-domeinen, die nu in openbare preview zijn. Hiermee kunt u uw eigen aangepaste domeinnamen gebruiken in uw privé virtuele netwerken in plaats van de door Azure geleverde namen die vandaag beschikbaar zijn.

Zie voor meer informatie [Azure DNS gebruiken voor privédomeinen](private-dns-overview.md).


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over DNS-zones en records: [Overzicht van DNS-zones en -records](dns-zones-records.md).

* Meer informatie over het maken van een zone in Azure DNS: [Een DNS-zone maken](./dns-getstarted-create-dnszone-portal.md).

* Zie voor veelgestelde vragen over Azure DNS de [Veelgestelde vragen over Azure DNS](dns-faq.md).

