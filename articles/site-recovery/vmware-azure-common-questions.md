---
title: Veelgestelde vragen - replicatie van VMware naar Azure met Azure Site Recovery | Microsoft Docs
description: In dit artikel bevat een overzicht van veelgestelde vragen wanneer u on-premises VMware-machines repliceren naar Azure met behulp van Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 07/19/2018
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: e8d30ae6cde7c787f1aa950506e0eb74bac0c12d
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238805"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Veelgestelde vragen - VMware naar Azure-replicatie

In dit artikel vindt u antwoorden op veelgestelde vragen, we zien bij het repliceren van on-premises VMware-machines naar Azure. Als u vragen hebt na het lezen van dit artikel, plaatst u deze op de [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Algemeen
### <a name="how-is-site-recovery-priced"></a>Hoe wordt de Site Recovery geprijsd?
Beoordeling [prijzen voor Azure Site Recovery](https://azure.microsoft.com/en-in/pricing/details/site-recovery/) details.

### <a name="how-do-i-pay-for-azure-vms"></a>Hoe moet ik betalen voor Azure VM's?
Tijdens de replicatie, gegevens worden gerepliceerd naar Azure storage en betaalt u geen wijzigingen in de virtuele machine. Wanneer u een failover naar Azure uitvoert, maakt Site Recovery automatisch virtuele machines van Azure IaaS. Hierna wordt u gefactureerd voor de rekenresources die u in Azure gebruikt.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>Wat kan ik doen met VMware naar Azure-replicatie?
- **Herstel na noodgevallen**: U kunt volledige noodherstel instellen. In dit scenario kunt u on-premises VMware-machines repliceren naar Azure storage. Klik, als uw on-premises infrastructuur niet beschikbaar is, kunt u een failover naar Azure. Wanneer u een failover uitvoert, kan Azure-VM's worden gemaakt met behulp van de gerepliceerde gegevens. U kunt toegang tot apps en workloads op de Azure VM's, totdat uw on-premises datacenter weer beschikbaar is. U kunt vervolgens failback van Azure naar uw on-premises site.
- **Migratie**: U kunt Site Recovery gebruiken voor het migreren van on-premises VMware-machines naar Azure. In dit scenario kunt u on-premises VMware-machines repliceren naar Azure storage. Vervolgens kunt u een failover van on-premises naar Azure. Na een failover zijn uw toepassingen en workloads beschikbaar en worden uitgevoerd op Azure Virtual machines.



## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>Wat moet ik in Azure?
U moet een Azure-abonnement, een Recovery Services-kluis, een storage-account en een virtueel netwerk. De kluis, de storage-account en het netwerk moeten zich in dezelfde regio bevinden.

### <a name="what-azure-storage-account-do-i-need"></a>Welke Azure-opslagaccount heb ik nodig?
U moet een LRS of GRS-opslagaccount. GRS wordt aanbevolen, omdat de gegevens dan flexibel zijn te gebruiken als er sprake is van regionale uitval of als de primaire regio niet kan worden hersteld. Premium storage wordt ondersteund.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Mijn Azure-account moet machtigingen voor het maken van virtuele machines?
Als u een abonnementsbeheerder bent, hebt u de replicatiemachtigingen die u nodig hebt. Als u niet bent, moet u machtigingen voor het maken van een Azure-VM in de resourcegroep en het virtuele netwerk dat u opgeeft bij het configureren van Site Recovery en machtigingen voor het schrijven naar het geselecteerde opslagaccount. [Meer informatie](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).



## <a name="on-premises"></a>On-premises 

### <a name="what-do-i-need-on-premises"></a>Wat kan ik on-premises nodig?
In on-premises moet u Site Recovery-onderdelen geïnstalleerd op een enkele VMware-VM. U moet ook een VMware-infrastructuur, met ten minste één ESXi-host, en het is raadzaam een vCenter-server. Bovendien moet u een of meer virtuele VMware-machines te repliceren. [Meer informatie](vmware-azure-architecture.md) over VMware naar Azure-architectuur.

De on-premises configuratieserver kan worden geïmplementeerd in een van de volgende twee manieren

1. Implementeren met behulp van een VM-sjabloon met de configuratieserver vooraf zijn geïnstalleerd. [Lees hier meer](vmware-azure-tutorial.md#download-the-vm-template).
2. Implementeren met behulp van de installatie op een computer met Windows Server 2016 van uw keuze. [Lees hier meer](physical-azure-disaster-recovery.md#set-up-the-source-environment).

Kies voor het detecteren van de aan de slag procedure voor het implementeren van de configuratieserver op uw eigen Windows Server-machines in het doel van de beveiliging van inschakelen van de beveiliging, **naar Azure > niet gevirtualiseerd/Overig**.

### <a name="where-do-on-premises-vms-replicate-to"></a>Waar repliceer on-premises machines naar?
Gegevens worden gerepliceerd naar Azure storage. Wanneer u een failover uitvoert, maakt Site Recovery automatisch virtuele Azure-machines uit de storage-account.

### <a name="what-apps-can-i-replicate"></a>Welke apps kan ik repliceren?
U kunt elke app of de werkbelasting wordt uitgevoerd op een VMware-VM die aan de voldoet repliceren [replicatievereisten](vmware-physical-azure-support-matrix.md##replicated-machines). Site Recovery biedt ondersteuning voor toepassingsgevoelige replicatie, zodat apps kunnen worden failover is uitgevoerd en kan niet naar een intelligente status. Site Recovery kan worden geïntegreerd met Microsoft-toepassingen zoals SharePoint, Exchange, Dynamics, SQL Server en Active Directory, en werkt nauw samen met toonaangevende leveranciers zoals Oracle, SAP, IBM en Red Hat. Lees [hier](site-recovery-workload.md) meer informatie over workloadbeveiliging.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Kan ik repliceren naar Azure met een site-naar-site-VPN?
Site Recovery repliceert gegevens van on-premises naar Azure storage via een openbaar eindpunt of met behulp van openbare ExpressRoute-peering. Replicatie via een site-naar-site VPN-netwerk wordt niet ondersteund.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Kan ik repliceren naar Azure met ExpressRoute?
Ja, ExpressRoute kan worden gebruikt voor het repliceren van virtuele machines naar Azure. Site Recovery worden gegevens gerepliceerd naar een Azure Storage-Account via een openbaar eindpunt en moet u voor het instellen van [openbare peering](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) voor Site Recovery-replicatie. Nadat de virtuele machines een failover uitvoeren naar een Azure-netwerk, kunt u ze openen met behulp van [privépeering](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).


### <a name="why-cant-i-replicate-over-vpn"></a>Waarom kan ik niet repliceren via VPN?

Wanneer u naar Azure repliceren, replicatieverkeer bereikt de openbare eindpunten van een Azure Storage-account, dus u kunt alleen repliceren via het openbare internet met ExpressRoute (openbare peering) en VPN werkt niet. 



## <a name="what-are-the-replicated-vm-requirements"></a>Wat zijn de vereisten van de gerepliceerde VM's?

Voor replicatie, moet een ondersteund besturingssysteem op een VMware-VM worden uitgevoerd. Bovendien hebben de virtuele machine moet voldoen aan de vereisten voor Azure-VM's. [Meer informatie](vmware-physical-azure-support-matrix.md##replicated-machines) in de ondersteuningsmatrix.

## <a name="how-often-can-i-replicate-to-azure"></a>Hoe vaak kan ik repliceren naar Azure?
Replicatie is continue bij het repliceren van virtuele VMware-machines naar Azure.

## <a name="can-i-extend-replication"></a>Kan ik replicatie verlengen?
Uitgebreide of gekoppelde replicatie wordt niet ondersteund. Deze functie in aanvragen [Feedbackforum](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

## <a name="can-i-do-an-offline-initial-replication"></a>Kan ik een offline initiële replicatie?
Nee, dit wordt niet ondersteund. Aanvragen van deze functie in de [Feedbackforum](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Kan ik schijven uitsluiten?
Ja, kunt u schijven uitsluiten van replicatie. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Kan ik virtuele machines met dynamische schijven repliceren?
Dynamische schijven kunnen worden gerepliceerd. De besturingssysteemschijf moet een standaardschijf.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Als ik gebruik replicatiegroepen voor multi-VM-consistentie, kan ik toevoegen een nieuwe virtuele machine aan een bestaande replicatiegroep?
Ja, kunt u nieuwe virtuele machines toevoegen aan een bestaande replicatiegroep wanneer u replicatie voor deze inschakelt. U kunt een virtuele machine niet toevoegen aan een bestaande replicatiegroep nadat replicatie is gestart, en u een replicatiegroep voor bestaande VM's maken kunt.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Kan ik virtuele machines die worden gerepliceerd door toe te voegen of het formaat van schijven wijzigen?

U kunt de schijfgrootte wijzigen voor VMware-replicatie naar Azure. Als u wilt toevoegen van nieuwe schijven moet u de schijf toevoegen en weer inschakelen van beveiliging voor de virtuele machine.

## <a name="configuration-server"></a>Configuratieserver

### <a name="what-does-the-configuration-server-do"></a>Wat doet de configuratieserver?
De configuratieserver wordt uitgevoerd de on-premises Site Recovery-onderdelen, met inbegrip van: 
- De configuratieserver coördineert de communicatie tussen on-premises en Azure en beheert de gegevensreplicatie.
- De processerver die als replicatiegateway fungeert. Deze ontvangt replicatiegegevens; Met caching, compressie en versleuteling, optimaliseert en verzendt die deze naar Azure storage-., de processerver installeert ook Mobility-Service op VM's die u wilt repliceren en detecteert automatisch on-premises virtuele VMware-machines.
- De hoofddoelserver die verantwoordelijk is voor replicatiegegevens tijdens de failback vanuit Azure.

### <a name="where-do-i-set-up-the-configuration-server"></a>Waar kan ik de configuratieserver instellen?
In dat geval moet u een één maximaal beschikbare on-premises virtuele VMware-machine in voor de configuratieserver.

### <a name="what-are-the-requirements-for-the-configuration-server"></a>Wat zijn de vereisten voor de configuratieserver?

Controleer de [vereisten](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Kan ik handmatig instellen de configuratieserver in plaats van een sjabloon?
Het is raadzaam dat u de meest recente versie van de OVF-sjabloon [maken van de configuratieserver VM](vmware-azure-deploy-configuration-server.md). Als voor een of andere reden is niet mogelijk, zoals u geen toegang tot de VMware-server, kunt u [de geïntegreerde Setup-bestand downloaden](physical-azure-set-up-source.md) vanuit de portal en uitvoeren op een virtuele machine. 

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Kan een configuratieserver in meer dan één regio repliceren?
Nee. Om dit te doen, moet u voor het instellen van een configuratieserver in elke regio.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Kan ik een configuratieserver in Azure hosten?
Tijdens het mogelijk is moet de virtuele Azure-machine waarop de configuratieserver wordt uitgevoerd om te communiceren met uw on-premises VMware-infrastructuur en virtuele machines. De overhead waarschijnlijk is niet mogelijk.


### <a name="where-can-i-get-the-latest-version-of-the-configuration-server-template"></a>Waar vind ik de meest recente versie van de configuratieserversjabloon?
Download de nieuwste versie van de [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

### <a name="how-do-i-update-the-configuration-server"></a>Hoe kan ik de configuratieserver bijwerken?
U installeren updatepakketten. U vindt de meest recente update-informatie in de [wiki updates pagina](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

## <a name="mobility-service"></a>Mobility-service

### <a name="where-can-i-find-the-mobility-service-installers"></a>Waar vind ik de Mobility-service-installatieprogramma's?
Het installatieprogramma's zijn ondergebracht in de **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** map op de configuratieserver.

## <a name="how-do-i-install-the-mobility-service"></a>Hoe installeer ik de Mobility-service?
U installeert op elke virtuele machine die u repliceren wilt, met behulp van een [push-installatie](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery), of het handmatig installeren van [de gebruikersinterface](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui), of [met behulp van PowerShell](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt). U kunt ook implementeren met behulp van een implementatieprogramma zoals [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md), of [Azure Automation en DSC](vmware-azure-mobility-deploy-automation-dsc.md).



## <a name="security"></a>Beveiliging

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Welke toegang Site Recovery moet VMware-servers
Site Recovery moet toegang hebben tot de VMware-servers om het volgende te kunnen doen:

- Instellen van een VMware-VM met de configuratieserver en andere on-premises Site Recovery-onderdelen. [Meer informatie](vmware-azure-deploy-configuration-server.md)
- Virtuele machines automatisch detecteren voor replicatie. Meer informatie over een account voorbereiden voor automatische detectie. [Meer informatie](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Welke toegang Site Recovery moet VMware-VM's?

- Als u wilt repliceren, moet een VMware-VM hebben de Site Recovery Mobility service geïnstalleerd en wordt uitgevoerd. U kunt het hulpprogramma handmatig implementeren of u kunt opgeven dat Site Recovery een push-installatie van de dienst doen moet wanneer u replicatie voor een virtuele machine inschakelt. Site Recovery moet voor de push-installatie een account die kan worden gebruikt voor het installeren van de service zijn. [Meer informatie](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation). De processerver (standaard uitgevoerd op de configuratieserver) uitvoert deze installatie. [Meer informatie](vmware-azure-install-mobility-service.md) over de installatie van de Mobility-service.
- Tijdens de replicatie communiceren de virtuele machines als volgt met Site Recovery:
    - Virtuele machines communiceren met de configuratieserver op poort 443 voor HTTPS voor het replicatiebeheer van.
    - Virtuele machines verzenden replicatiegegevens naar de processerver op HTTPS-poort 9443 (kan worden gewijzigd).
    - Als u multi-VM-consistentie inschakelt, wordt de status van virtuele machines communiceren met elkaar via poort 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>Worden er replicatiegegevens verzonden met Site Recovery?
Nee, Site Recovery gerepliceerde gegevens niet worden onderschept, en heeft geen informatie over wat wordt uitgevoerd op uw virtuele machines. Replicatiegegevens worden uitgewisseld tussen VMware-hypervisors en Azure storage. Met Site Recovery kunnen deze gegevens niet worden onderschept. Alleen de metagegevens die nodig zijn om replicatie en failover te organiseren, worden naar de Site Recovery-service verzonden.  

Site Recovery is ISO 27001: 2013, 27018, HIPAA, DPA gecertificeerd en wordt momenteel SOC2 en FedRAMP JAB-beoordelingen.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>Kunnen we de metagegevens van on-premises bewaren binnen een geografische regio's?
Ja. Wanneer u een kluis in een regio maken, zorgen we dat alle metagegevens die wordt gebruikt door Site Recovery blijft binnen een geografische grens van die regio.

### <a name="does-site-recovery-encrypt-replication"></a>Wordt replicatie met Site Recovery versleuteld?
Ja, zowel versleuteling-in-transit en [versleuteling in Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) worden ondersteund.


## <a name="failover-and-failback"></a>Failover en failback
### <a name="how-far-back-can-i-recover"></a>Hoe ver terug kan ik gegevens herstellen?
Voor VMware naar Azure is het oudste herstelpunt dat u kunt gebruiken dit 72 uur.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hoe krijg ik toegang tot Azure-VM's na een failover?
Na een failover, kunt u Azure-VM's openen via een beveiligde internetverbinding, via een site-naar-site-VPN of via Azure ExpressRoute. U moet een aantal dingen om verbinding te kunnen voorbereiden. [Meer informatie](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Failover wordt uitgevoerd gegevens tegen?
Azure is ontworpen voor herstelbaarheid. Site Recovery is ontworpen voor failover naar een secundaire Azure-datacenter, in overeenstemming met de Azure SLA. Wanneer een failover optreedt, zorgen wij ervoor dat uw metagegevens en kluizen binnen dezelfde geografische regio die u hebt gekozen voor uw kluis blijven.

### <a name="is-failover-automatic"></a>Vindt failover automatisch plaats?
[Failover](site-recovery-failover.md) wordt niet automatisch uitgevoerd. Initiëren van failover met één klik in de portal of kunt u [ PowerShell](/powershell/module/azurerm.siterecovery) dat een failover wordt geactiveerd.

### <a name="can-i-fail-back-to-a-different-location"></a>Kan ik een failback uitvoeren naar een andere locatie?
Ja, als u een failover naar Azure, kunt u failover naar een andere locatie als de oorspronkelijke map is niet beschikbaar. [Meer informatie](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Waarom heb ik een VPN of ExpressRoute voor failback nodig?

Wanneer u een failback van Azure, gegevens van Azure is gekopieerd naar uw on-premises virtuele machine en persoonlijke toegang is vereist. 



## <a name="automation-and-scripting"></a>Automation en scripting

### <a name="can-i-set-up-replication-with-scripting"></a>Kan ik replicatie met het uitvoeren van scripts instellen?
Ja. U kunt met behulp van de Rest-API, PowerShell of de SDK van Azure Site Recovery-werkstromen automatiseren. [Meer](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Prestaties en capaciteit
### <a name="can-i-throttle-replication-bandwidth"></a>Kan ik de replicatiebandbreedte beperken?
Ja. [Meer informatie](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>Volgende stappen
* [Beoordeling](vmware-physical-azure-support-matrix.md) vereisten ondersteunen.
* [Instellen van](vmware-azure-tutorial.md) VMware naar Azure-replicatie.
