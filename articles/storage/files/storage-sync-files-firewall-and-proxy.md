---
title: Azure File Sync on-premises firewall en proxy-instellingen | Microsoft Docs
description: Azure File Sync on-premises netwerkconfiguratie
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: fauhse
ms.component: files
ms.openlocfilehash: 44bfdd192f846b710e378b1f00799eda304cec1e
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522761"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Proxy- en firewallinstellingen van Azure File Sync
Azure File Sync verbindt uw on-premises servers naar Azure Files, synchronisatie van meerdere locaties en cloud-opslaglagen functies inschakelen. Als zodanig moet een on-premises server worden verbonden met internet. IT-beheerder nodig heeft om te bepalen van het beste pad voor de server te bereiken in Azure cloudservices.

In dit artikel biedt inzicht in de specifieke vereisten en opties die beschikbaar zijn met succes en veilig verbinding maken als uw server met Azure File Sync.

> [!Important]
> Azure File Sync nog ondersteunt geen firewalls en virtuele netwerken voor een opslagaccount.

## <a name="overview"></a>Overzicht
Azure File Sync fungeert als een service orchestration tussen uw Windows-Server, uw Azure-bestandsshare en verschillende andere Azure-services om te synchroniseren van gegevens, zoals beschreven in de groep voor synchronisatie. Voor Azure File Sync correct te laten werken, moet u de servers om te communiceren met de volgende Azure-services configureren:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Verificatieservices

> [!Note]  
> De Azure File Sync-agent op Windows Server initieert alle aanvragen naar de cloud services, wat leidt tot alleen die rekening houden met het uitgaande verkeer vanuit het oogpunt van de firewall. <br /> Er is geen Azure-service maken van verbinding met de Azure File Sync-agent.

## <a name="ports"></a>Poorten
Azure File Sync verplaatst gegevens uit een bestand en metagegevens uitsluitend via HTTPS en vereist-poort 443 uitgaande worden geopend.
Als gevolg hiervan wordt al het verkeer versleuteld.

## <a name="networks-and-special-connections-to-azure"></a>Netwerken en speciale verbindingen naar Azure
De Azure File Sync-agent heeft geen vereisten met betrekking tot speciale kanalen, zoals [ExpressRoute](../../expressroute/expressroute-introduction.md), enzovoort naar Azure.

Azure File Sync werkt via middelen beschikbaar waarmee het bereik in Azure, automatisch aan te passen aan verschillende netwerkkenmerken, zoals bandbreedte, latentie, evenals aanbieding-beheer voor het aan te passen. Niet alle functies zijn beschikbaar op dit moment. Als u wilt om bepaald gedrag te configureren, laat het ons weten [Azure bestanden UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
Azure File Sync biedt ondersteuning voor app-specifieke en alle computers proxy-instellingen.

Machine-proxy-instellingen zijn transparant voor de Azure File Sync-agent omdat het volledige verkeer van de server wordt doorgestuurd via de proxy.

App-specifieke proxy-instellingen van de configuratie van een proxy specifiek voor Azure File Sync-verkeer toestaan. App-specifieke proxy-instellingen worden ondersteund op agentversie 3.0.12.0 of hoger en kunnen worden geconfigureerd tijdens de installatie van de agent of met behulp van de Set-StorageSyncProxyConfiguration PowerShell-cmdlet.

PowerShell-opdrachten voor het app-specifieke proxy-instellingen configureren:
```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```

## <a name="firewall"></a>Firewall
Zoals vermeld in een vorige sectie, open poort 443 moet uitgaande. Op basis van beleid in uw datacenter, de vertakking of de regio, verder beperken van verkeer via deze poort tot specifieke domeinen mogelijk gewenst of vereist.

De volgende tabel beschrijft de vereiste domeinen voor de communicatie:

| Service | Domein | Gebruik |
|---------|----------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | Elke aanroep van de gebruiker (zoals PowerShell) gaat via deze URL, met inbegrip van de eerste server registratie-aanroep. |
| **Azure Active Directory** | https://login.windows.net | Azure Resource Manager-aanroepen moeten worden gemaakt door een geverifieerde gebruiker. Als u wilt slagen, is deze URL wordt gebruikt voor verificatie van de gebruiker. |
| **Azure Active Directory** | https://graph.windows.net/ | Als onderdeel van de implementatie van Azure File Sync, wordt een service-principal in van het abonnement Azure Active Directory gemaakt. Deze URL wordt gebruikt voor die. Deze principal wordt gebruikt voor het overdragen van een minimale set rechten voor de Azure File Sync-service. De gebruiker die de eerste installatie van Azure File Sync uitvoert moet een geverifieerde gebruiker met bevoegdheden voor het abonnement eigenaren. |
| **Azure Storage** | &ast;.core.windows.net | Wanneer de server een bestand downloadt, klikt u vervolgens de server uitvoert die gegevensverplaatsing efficiënter bij het bespreken van rechtstreeks naar de Azure-bestandsshare in de Storage-Account. De server heeft een SAS-sleutel die alleen voor de betreffende bestandsshare-toegang toestaat. |
| **Azure File Sync** | &ast;.one.microsoft.com | Na de registratie van de eerste server ontvangt de server een regionale URL voor de Azure File Sync-service-exemplaar in deze regio. De server kan de URL gebruiken om te communiceren rechtstreeks en efficiënt met het verwerken van de synchronisatie-exemplaar. |

> [!Important]
> Wanneer het verkeer naar &ast;. one.microsoft.com, verkeer naar meer dan alleen de sync-service is mogelijk van de server. Er zijn veel meer Microsoft-services beschikbaar onder subdomeinen.

Als &ast;. one.microsoft.com te ruim is, kunt u de communicatie van de server beperken door toe te staan communicatie met alleen een expliciete regionale exemplaren van de Azure File Sync-service. Welke instantie (s) om te kiezen, is afhankelijk van de regio van de opslagsynchronisatieservice hebt u de server geregistreerd en geïmplementeerd. Deze regio heet 'primaire eindpunt-URL' in de onderstaande tabel.

Voor zakelijke continuïteit en noodherstel (BCDR) recovery redenen kan u hebt opgegeven dat uw Azure-bestandsshares in een wereldwijd redundant (GRS) storage-account. Als dit het geval is, wordt klikt u vervolgens uw Azure-bestandsshares failover naar de gekoppelde regio in geval van een langdurige regionale uitval. Azure File Sync maakt gebruik van de dezelfde regionale koppelingen als opslag. Dus als u GRS-opslagaccounts gebruiken, moet u om in te schakelen als u meer URL's voor het toestaan van uw server om te communiceren met de gekoppelde regio voor Azure File Sync. De onderstaande tabel roept deze 'gepaarde regio'. Daarnaast is er een traffic manager-profiel-URL die moet ook worden ingeschakeld. Dit zorgt ervoor netwerkverkeer kan worden naadloos opnieuw doorgestuurd naar de gekoppelde regio in het geval van een failover en met de naam 'Detectie-URL' in de onderstaande tabel.

| Regio | Primair eindpunt-URL | Gekoppelde regio | Detectie-URL | |---|---|| --------|| ---------------------------------------| | Australië-Oost | https://kailani-aue.one.microsoft.com | Australië Souteast | https://kailani-aue.one.microsoft.com | | Australië-Zuidoost | https://kailani-aus.one.microsoft.com | Australië-Oost | https://tm-kailani-aus.one.microsoft.com | | Canada centraal | https://kailani-cac.one.microsoft.com | Canada-Oost | https://tm-kailani-cac.one.microsoft.com | | Canada-Oost | https://kailani-cae.one.microsoft.com | Canada centraal | https://tm-kailani.cae.one.microsoft.com | | VS-midden | https://kailani-cus.one.microsoft.com | VS-Oost 2 | https://tm-kailani-cus.one.microsoft.com | | Oost-Azië | https://kailani11.one.microsoft.com | Zuidoost-Azië | https://tm-kailani11.one.microsoft.com | | VS-Oost | https://kailani1.one.microsoft.com | VS-West | https://tm-kailani1.one.microsoft.com | | VS-Oost 2 | https://kailani-ess.one.microsoft.com | VS-midden | https://tm-kailani-ess.one.microsoft.com | | Noord-Europa | https://kailani7.one.microsoft.com | West-Europa | https://tm-kailani7.one.microsoft.com | | Zuidoost-Azië | https://kailani10.one.microsoft.com | Oost-Azië | https://tm-kailani10.one.microsoft.com | | UK-Zuid | https://kailani-uks.one.microsoft.com | UK-West | https://tm-kailani-uks.one.microsoft.com | | UK-West | https://kailani-ukw.one.microsoft.com | UK-Zuid | https://tm-kailani-ukw.one.microsoft.com | | West-Europa | https://kailani6.one.microsoft.com | Noord-Europa | https://tm-kailani6.one.microsoft.com | | VS-West | https://kailani.one.microsoft.com | VS-Oost | https://tm-kailani.one.microsoft.com |

- Als u lokaal redundant (LRS) of zone redundant (ZRS) storage-accounts gebruiken, moet u alleen de URL die wordt vermeld onder 'primaire eindpunt-URL' inschakelen.

- Als u wereldwijd redundant (GRS) storage-accounts gebruiken, schakelt u drie URL's.

**Voorbeeld:** implementeren van een opslagsynchronisatieservice in `"West US"` en registreer de server met het. De URL's waarmee de server om te communiceren met in dit geval zijn:

> - https://kailani.one.microsoft.com (primaire eindpunt: VS-West)
> - https://kailani1.one.microsoft.com (gekoppelde failover regio: VS-Oost)
> - https://tm-kailani.one.microsoft.com (detectie-URL van de primaire regio)

## <a name="summary-and-risk-limitation"></a>Samenvatting en risico-beperking
De lijsten die u eerder in dit document bevat de URL's met Azure File Sync momenteel communiceert met. Firewalls moeten in staat om toe te staan verkeer uitgaand naar deze domeinen zijn. Microsoft streeft ernaar te houden deze lijst bijgewerkt.

Instellen van het domein te beperken van firewall-regels mag een meting om beveiliging te verbeteren. Als deze firewallconfiguraties worden gebruikt, moet een Houd er rekening mee dat URL's worden toegevoegd en kunnen zelfs na verloop van tijd worden gewijzigd. Controleer regelmatig in dit artikel.

## <a name="next-steps"></a>Volgende stappen
- [Planning voor de implementatie van een Azure File Sync](storage-sync-files-planning.md)
- [Azure Files Sync implementeren](storage-sync-files-deployment-guide.md)