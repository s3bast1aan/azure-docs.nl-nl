---
title: Een Azure File Sync-implementatie plannen | Microsoft Docs
description: Meer informatie over wat u moet overwegen bij het plannen van een implementatie van Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: d00a6d3c476e10b13d00ff1738cb54c2eeea104c
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521819"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planning voor de implementatie van Azure Files Sync
Gebruik Azure File Sync te centraliseren bestandsshares van uw organisatie in Azure Files, terwijl de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver. Azure File Sync transformeert Windows Server naar een snelle cache van uw Azure-bestandsshare. U kunt elk protocol dat beschikbaar is op Windows Server voor toegang tot uw gegevens lokaal, met inbegrip van SMB, NFS en FTPS gebruiken. U kunt zoveel caches hebben als u nodig hebt over de hele wereld.

Dit artikel wordt beschreven belangrijke overwegingen voor de implementatie van Azure File Sync. Het is raadzaam dat u ook lezen [Planning voor de implementatie van Azure Files](storage-files-planning.md). 

## <a name="azure-file-sync-terminology"></a>Azure File Sync-terminologie
Voordat u de details van de planning voor de implementatie van een Azure File Sync, is het belangrijk dat u de terminologie begrijpt.

### <a name="storage-sync-service"></a>Opslagsynchronisatieservice
De Opslagsynchronisatieservice is de op het hoogste niveau Azure-resource voor Azure File Sync. De Opslagsynchronisatieservice-resource is een peer van de resource van het opslagaccount en kan op dezelfde manier worden geïmplementeerd op Azure-resourcegroepen. Een resource voor verschillende op het hoogste niveau van de resource van het opslagaccount is vereist omdat de Opslagsynchronisatieservice synchronisatierelaties met meerdere opslagaccounts via meerdere synchronisatiegroepen maken kunt. Een abonnement kan hebben meerdere Opslagsynchronisatieservice resources die zijn geïmplementeerd.

### <a name="sync-group"></a>Synchronisatiegroep
Een synchronisatiegroep definieert de synchronisatie-topologie voor een set bestanden. Eindpunten in een synchronisatiegroep worden synchroon met elkaar. Als u bijvoorbeeld twee verschillende sets van bestanden die u wilt beheren met Azure File Sync hebt, zou u twee synchronisatiegroepen maken en verschillende eindpunten toevoegen aan elke groep voor synchronisatie. Een Opslagsynchronisatieservice kunt zoveel synchronisatiegroepen als u wilt hosten.  

### <a name="registered-server"></a>Geregistreerde server
De geregistreerde server-object vertegenwoordigt een vertrouwensrelatie tussen uw server (of cluster) en de Opslagsynchronisatieservice. U kunt zo veel servers naar een exemplaar van de Opslagsynchronisatieservice als u wilt registreren. Echter, een server (of cluster) kan worden geregistreerd met slechts één Opslagsynchronisatieservice tegelijk.

### <a name="azure-file-sync-agent"></a>Azure File Sync-agent
De Azure File Sync-agent is een downloadbare pakket waarmee Windows-Server kan worden gesynchroniseerd met een Azure-bestandsshare. De Azure File Sync-agent heeft drie belangrijke onderdelen: 
- **FileSyncSvc.exe**: de achtergrond Windows-service die verantwoordelijk is voor het bewaken van wijzigingen op de servereindpunten, en voor het starten van synchronisatiesessies voor Azure.
- **StorageSync.sys**: de Azure File Sync bestandssysteemfilter, die verantwoordelijk is voor cloudlagen bestanden naar Azure Files (wanneer cloud tiering is ingeschakeld).
- **PowerShell-cmdlets voor beheer**: PowerShell-cmdlets die u gebruiken om te communiceren met de Microsoft.StorageSync Azure resourceprovider. U vindt deze op de volgende (standaard)-locaties:
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Servereindpunt
Een servereindpunt vertegenwoordigt een specifieke locatie op een geregistreerde server, bijvoorbeeld een map op een volume van de server. Meerdere servereindpunten kunnen zich op hetzelfde volume als de naamruimten niet overlappen (bijvoorbeeld `F:\sync1` en `F:\sync2`). U kunt cloud cloudlagen beleidsregels voor het eindpunt voor elke server afzonderlijk configureren. 

U kunt een servereindpunt via een koppelpunt maken. Opmerking: Stel de volgende parameter binnen het servereindpunt worden overgeslagen.  

U kunt een servereindpunt maken op het systeemvolume, maar er zijn twee beperkingen als u dit doet:
* Cloud tiering kan niet worden ingeschakeld.
* Snelle naamruimte terugzetten (waarbij het systeem snel brengt u de gehele naamruimte en start vervolgens aan de hand van inhoud) wordt niet uitgevoerd.


> [!Note]  
> Alleen niet-verwisselbaar volumes worden ondersteund.  Vanuit een externe share toegewezen stations worden niet ondersteund voor een server eindpunt-pad.  Bovendien een servereindpunt bevindt zich mogelijk op de Windows systeemvolume echter cloud tiering wordt niet ondersteund op het systeemvolume.

Als u de locatie van een server met een bestaande set bestanden als een servereindpunt met een synchronisatiegroep toevoegt, worden deze bestanden worden samengevoegd met andere bestanden die zich al op andere eindpunten in de groep voor synchronisatie.

### <a name="cloud-endpoint"></a>Cloud-eindpunt
Een cloudeindpunt is een Azure-bestandsshare die deel uitmaakt van een synchronisatiegroep. De volledige Azure file share wordt gesynchroniseerd en een Azure-bestandsshare kan een lid van slechts één cloudeindpunt zijn. Een Azure-bestandsshare mag daarom een lid van slechts één synchronisatiegroep. Als u een Azure-bestandsshare met een bestaande set bestanden als een cloudeindpunt met een synchronisatiegroep toevoegt, wordt de bestaande bestanden worden samengevoegd met andere bestanden die zich al op andere eindpunten in de groep voor synchronisatie.

> [!Important]  
> Azure File Sync ondersteunt rechtstreeks aanbrengen van wijzigingen in de Azure-bestandsshare. Alle wijzigingen in de Azure-bestandsshare moeten echter eerst moeten worden gedetecteerd door een Azure File Sync-taak wijzigen detectie. Een taak wijzigen-detectie wordt slechts één keer voor elke 24 uur voor een cloudeindpunt gestart. Bovendien wijzigingen aangebracht in een Azure-bestandsshare via de REST-protocol wordt niet bijgewerkt door de SMB-tijdstip laatst gewijzigd en zal niet worden gezien als een wijziging door synchronisatie. Zie voor meer informatie, [Veelgestelde vragen over Azure-bestanden](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Cloudopslaglagen 
Cloud-opslaglagen is een optionele functie van Azure File Sync in die niet vaak worden gebruikt of de bestanden die groter is dan 64 KiB in grootte kan worden gelaagde in Azure-bestanden zijn geopend. Wanneer een bestand is gelaagd, vervangen het bestandssysteemfilter van Azure File Sync (StorageSync.sys) het bestand lokaal door een wijzer, of een reparsepunt. Het reparsepunt vertegenwoordigt een URL naar het bestand in Azure Files. Een gelaagd bestand heeft de 'offline' kenmerk NTFS, zodat toepassingen van derden gelaagde bestanden kunnen identificeren. Wanneer een gebruiker een gelaagd bestand opent, roept Azure File Sync naadloos de bestandsgegevens van Azure Files zonder de gebruiker hoeft te weten dat het bestand niet lokaal is opgeslagen op het systeem. Deze functionaliteit is ook wel bekend als hiërarchische opslag Management (HSM).

> [!Important]  
> Cloud tiering wordt niet ondersteund voor servereindpunten op de volumes van Windows-systeem.

## <a name="azure-file-sync-interoperability"></a>Azure File Sync-interoperabiliteit 
In deze sectie bevat informatie over Azure File Sync-interoperabiliteit met Windows Server-functies en rollen en -oplossingen van derden.

### <a name="supported-versions-of-windows-server"></a>Ondersteunde versies van Windows Server
De ondersteunde versies van Windows Server door Azure File Sync zijn momenteel:

| Versie | Ondersteunde SKU 's | Ondersteunde implementatieopties |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter en Standard | Volledig (server met een gebruikersinterface) |
| Windows Server 2012 R2 | Datacenter en Standard | Volledig (server met een gebruikersinterface) |

Toekomstige versies van Windows Server wordt toegevoegd zodra ze worden vrijgegeven. Eerdere versies van Windows kunnen worden toegevoegd op basis van feedback van gebruikers.

> [!Important]  
> We raden aan om alle servers die u met Azure File Sync up-to-date met de meest recente updates van Windows Update gebruikt. 

### <a name="file-system-features"></a>Functies
| Functie | Ondersteuning voor status | Opmerkingen |
|---------|----------------|-------|
| Toegangsbeheerlijsten (ACL's) | Volledig ondersteund | Windows ACL's door Azure File Sync worden bewaard en worden afgedwongen door Windows Server op de servereindpunten. Windows ACL's nog niet zijn () ondersteund door Azure Files als bestanden zijn geopend rechtstreeks in de cloud. |
| Vaste koppelingen | Overgeslagen | |
| Symbolische koppelingen | Overgeslagen | |
| Koppelpunten | Gedeeltelijk ondersteund | Koppelpunten bevatten de hoofdmap van een servereindpunt kunnen zijn, maar ze worden overgeslagen als ze zijn opgenomen in de naamruimte van een servereindpunt. |
| Koppelingen | Overgeslagen | Bijvoorbeeld, Distributed File System DfrsrPrivate en DFSRoots mappen. |
| Reparsepunten | Overgeslagen | |
| NTFS-compressie | Volledig ondersteund | |
| Sparse bestanden | Volledig ondersteund | Sparse bestanden synchroniseren (niet worden geblokkeerd), maar ze synchroniseren met de cloud als een volledige-bestand. Als de inhoud van het bestand wijzigen in de cloud (of op een andere server), is het bestand niet langer sparse wanneer de wijziging wordt gedownload. |
| Alternatieve gegevensstreams (AD) | Behouden, maar niet gesynchroniseerd | Labels voor gegevensclassificatie die zijn gemaakt door de infrastructuur voor Bestandsclassificatie zijn bijvoorbeeld niet is gesynchroniseerd. Bestaande labels voor gegevensclassificatie met bestanden op elk van de servereindpunten zijn ongewijzigd. |

> [!Note]  
> Alleen NTFS-volumes worden ondersteund. ReFS, FAT, FAT32 en andere bestandssystemen worden niet ondersteund.

### <a name="files-skipped"></a>Bestanden die zijn overgeslagen
| Bestand/map | Opmerking |
|-|-|
| Desktop.ini | Bestand die specifiek zijn voor systeem |
| ethumbs.DB$ | Tijdelijk bestand voor miniaturen |
| ~$\*.\* | Tijdelijke Office-bestand |
| \*.tmp | Tijdelijk bestand |
| \*.laccdb | Vergrendeling Access DB-bestand|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Interne Sync-bestand|
| \\System Volume Information | Map die specifiek zijn voor volume |
| $RECYCLE. OPSLAGLOCATIE| Map |
| \\SyncShareState | Map voor synchronisatie |

### <a name="failover-clustering"></a>Failover Clustering
Windows Server Failover Clustering wordt ondersteund door Azure File Sync voor de Implementatieoptie 'Bestandsserver voor algemeen gebruik'. Failoverclustering wordt niet ondersteund op 'Scale-Out File Server for application data' (SOFS) of op geclusterde gedeelde Volumes (CSV's).

> [!Note]  
> De Azure File Sync-agent moet worden geïnstalleerd op elk knooppunt in een failovercluster voor synchronisatie correct te laten werken.

### <a name="data-deduplication"></a>De functie voor Gegevensontdubbeling
Voor volumes waarvoor geen cloud-opslaglagen ingeschakeld, ondersteunt Azure File Sync Windows Server-Gegevensontdubbeling wordt ingeschakeld op het volume. Op dit moment bieden we geen interoperabiliteit tussen Azure File Sync met cloud-opslaglagen ingeschakeld en de functie voor Gegevensontdubbeling ondersteuning.

### <a name="distributed-file-system-dfs"></a>Distributed File System (DFS)
Azure File Sync biedt ondersteuning voor samenwerking met DFS-naamruimten (DFS-N) en DFS-replicatie (DFS-R) vanaf [Azure File Sync-agent 1.2](https://go.microsoft.com/fwlink/?linkid=864522).

**DFS-naamruimten (DFS-N)**: Azure File Sync wordt volledig ondersteund voor DFS-N-servers. U kunt de Azure File Sync-agent installeren op een of meer leden DFS-N gegevens synchroniseren tussen de servereindpunten en de cloudeindpunt. Zie voor meer informatie, [DFS-naamruimten overzicht](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**DFS-replicatie (DFS-R)**: omdat DFS-R- en Azure File Sync zijn beide replicatieoplossingen in de meeste gevallen, het is raadzaam DFS-R vervangen door Azure File Sync. Er zijn echter enkele scenario's waarbij u zou willen DFS-R- en Azure File Sync samen gebruiken:

- U migreert vanaf een DFS-R-implementatie naar een Azure File Sync-implementatie. Zie voor meer informatie, [migreren van een DFS-replicatie (DFS-R)-implementatie naar Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Niet elke on-premises server die een kopie van uw gegevens uit een bestand moet kan rechtstreeks met internet worden verbonden.
- Vertakking servers samenvoegen van gegevens op één hubserver, waarvoor u wilt gebruiken van Azure File Sync.

Voor Azure File Sync en DFS-R om te werken side-by-side:

1. Azure File Sync cloud cloudlagen moet worden uitgeschakeld op volumes met DFS-R gerepliceerde mappen.
2. Servereindpunten moeten niet worden geconfigureerd op de mappen voor DFS-R-replicatie voor alleen-lezen.

Zie voor meer informatie, [DFS-replicatie-overzicht](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
Met behulp van sysprep op een server die de Azure File Sync-agent geïnstalleerd is, wordt niet ondersteund en kan leiden tot onverwachte resultaten. De registratie van de installatie en server van de agent moet worden uitgevoerd na het implementeren van de server-installatiekopie en sysprep mini-installatie te voltooien.

### <a name="windows-search"></a>Windows Search
Als cloud tiering is ingeschakeld op een servereindpunt, bestanden die zich gelaagd worden overgeslagen en niet zijn geïndexeerd met Windows Search. Niet-gelaagde bestanden worden juist geïndexeerd.

### <a name="antivirus-solutions"></a>Anti-virussoftware
Omdat antivirus werkt door te scannen van bestanden voor bekende schadelijke code, kan het intrekken van gelaagde bestanden leiden tot een antivirusproduct. Aangezien de gelaagde bestanden hebben het kenmerk 'offline' is ingesteld, wordt u aangeraden overleg met de softwareleverancier voor meer informatie over het configureren van de oplossing voor het lezen van offlinebestanden overslaan. 

De volgende oplossingen zijn bekende ter ondersteuning van offlinebestanden overslaan:

- [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus)
    - Windows Defender slaat automatisch over het lezen van deze bestanden. We hebben getest Defender en een klein probleem geïdentificeerd: wanneer u een server aan een bestaande synchronisatiegroep toevoegen, bestanden kleiner is dan 800 bytes (gedownload) op de nieuwe server worden ingetrokken. Deze bestanden blijven aanwezig op de nieuwe server en gelaagd omdat ze niet voldoen aan de vereiste cloudlagen (> 64kb).
- [System Center Endpoint Protection (SCEP)](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus)
    - SCEP werkt op dezelfde manier als Defender; Zie hierboven
- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee EndPoint Security](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (Zie "scannen alleen wat u moet' op de pagina 90 van het PDF-bestand)
- [Kaspersky Anti-Virus](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Back-upoplossingen
Back-upoplossingen kunnen leiden tot het intrekken van gelaagde bestanden, zoals antivirus-oplossingen. U wordt aangeraden met behulp van een cloudoplossing voor back-up naar back-up van de Azure-bestandsshare in plaats van een on-premises back-product.

Als u een back-up on-premises-oplossing gebruikt, moeten de back-ups worden uitgevoerd op een server in de groep voor synchronisatie met cloud-opslaglagen uitgeschakeld. Bij het herstellen van bestanden in de locatie van de server-eindpunt, gebruikt u de optie bestand herstelbewerkingen op. Bestanden hersteld zal worden gesynchroniseerd met alle eindpunten in de groep voor synchronisatie en bestaande bestanden wordt vervangen door de versie van back-up hersteld.

> [!Note]  
> App-gerichte volumeniveau en bare metal (BMR) terugzetten opties kunnen leiden tot onverwachte resultaten en worden momenteel niet ondersteund. Deze opties worden ondersteund in een toekomstige release terugzetten.

### <a name="encryption-solutions"></a>Versleutelingsoplossingen voor
Ondersteuning voor versleutelingsoplossingen voor, is afhankelijk van hoe ze worden geïmplementeerd. Azure File Sync is bekend bij het werken met:

- BitLocker-versleuteling
- Azure Information Protection, Azure Rights Management Services (Azure RMS), en Active Directory RMS

Azure File Sync bekend is niet werken met:

- NTFS versleuteld File System (EFS)

Azure File Sync moet in het algemeen interoperabiliteit met versleutelingsoplossingen voor onderliggende het bestandssysteem, zoals BitLocker, en met oplossingen die zijn geïmplementeerd in de bestandsindeling, zoals Azure Information Protection ondersteunen. Er zijn geen speciale interoperabiliteit zijn gemaakt in oplossingen die zich boven het bestandssysteem (zoals NTFS EFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Andere oplossingen hiërarchische opslag Management (HSM)
Er zijn geen andere HSM-oplossingen moeten worden gebruikt met Azure File Sync.

## <a name="region-availability"></a>Beschikbaarheid in regio’s
Azure File Sync is alleen beschikbaar in de volgende regio's:

| Regio | Datacenter-locatie |
|--------|---------------------|
| Australië - oost | New South Wales |
| Australië - zuidoost | Victoria |
| Canada - midden | Toronto |
| Canada - oost | Quebec (stad) |
| US - centraal | Iowa |
| Azië - oost | Hongkong |
| US - oost | Virginia |
| US - oost 2 | Virginia |
| Europa - noord | Ierland |
| Azië - zuidoost | Singapore |
| Verenigd Koninkrijk Zuid | Londen |
| Verenigd Koninkrijk West | Cardiff |
| Europa -west | Nederland |
| US - west | Californië |

Azure File Sync ondersteunt alleen met een Azure-bestandsshare die zich in dezelfde regio als de Opslagsynchronisatieservice worden gesynchroniseerd.

### <a name="azure-disaster-recovery"></a>Azure-noodherstel
Als u wilt beveiligen tegen het verlies van een Azure-regio, Azure File Sync kan worden geïntegreerd met de [geografisch redundante opslag met redundantie](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS) optie. GRS-opslag werkt met behulp van asynchrone blokreplicatie tussen opslag in de primaire regio, waarmee u normaal gesproken werken, en opslag in de gekoppelde secundaire regio. In het geval van een ramp die ervoor zorgt dat een Azure-regio offline te gaan tijdelijk of permanent, mislukken Microsoft over opslag voor de gekoppelde regio. 

Ter ondersteuning van de failover-integratie tussen geografisch redundante opslag en Azure File Sync, worden alle regio's voor Azure File Sync gekoppeld aan een secundaire regio die overeenkomt met de secundaire regio die wordt gebruikt door opslag. Deze paren zijn als volgt:

| Primaire regio      | Gekoppelde regio      |
|---------------------|--------------------|
| Australië - oost      | Australië Southest |
| Australië - zuidoost | Australië - oost     |
| Canada - midden      | Canada - oost        |
| Canada - oost         | Canada - midden     |
| US - centraal          | US - oost 2          |
| Azië - oost           | Azië - zuidoost     |
| US - oost             | US - west            |
| US - oost 2           | US - centraal         |
| Europa - noord        | Europa -west        |
| Azië - zuidoost      | Azië - oost          |
| Verenigd Koninkrijk Zuid            | Verenigd Koninkrijk West            |
| Verenigd Koninkrijk West             | Verenigd Koninkrijk Zuid           |
| Europa -west         | Europa - noord       |
| US - west             | US - oost            |

## <a name="azure-file-sync-agent-update-policy"></a>Updatebeleid Azure File Sync-agent
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Volgende stappen
* [Houd rekening met firewall en proxy-instellingen](storage-sync-files-firewall-and-proxy.md)
* [Implementatie van Azure Files plannen](storage-files-planning.md)
* [Azure Files implementeren](storage-files-deployment-guide.md)
* [Azure Files Sync implementeren](storage-sync-files-deployment-guide.md)
