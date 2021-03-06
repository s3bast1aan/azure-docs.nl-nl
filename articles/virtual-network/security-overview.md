---
title: Overzicht van Azure-beveiligingsgroepen| Microsoft Docs
description: Meer informatie over netwerk- en toepassingsbeveiligingsgroepen. Beveiligingsgroepen helpen u bij het filteren van netwerkverkeer tussen Azure-resources.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2018
ms.author: jdial
ms.openlocfilehash: f01ff2dfff19d066fe3ecd8924a5b54cd6cc68a5
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2018
ms.locfileid: "39264161"
---
# <a name="security-groups"></a>Beveiligingsgroepen
<a name="network-security-groups"></a>

U kunt netwerkverkeer naar en van Azure-resources in een [virtueel Azure-netwerk](virtual-networks-overview.md) filteren met een netwerkbeveiligingsgroep. Een netwerkbeveiligingsgroep bevat [beveiligingsregels](#security-rules) waarmee binnenkomend netwerkverkeer naar, of uitgaand netwerkverkeer van diverse typen Azure-resources kan worden toegestaan of geweigerd. Zie [Integratie van virtuele netwerken voor Azure-services](virtual-network-for-azure-services.md) voor meer informatie over de Azure-resources die in een virtueel netwerk kunnen worden geïmplementeerd en waaraan netwerkbeveiligingsgroepen kunnen worden gekoppeld. Voor elke regel kunt u de bron en het doel, de poort en het protocol opgeven.

In dit artikel worden de concepten van netwerkbeveiligingsgroepen uitgelegd, zodat u ze effectief kunt gebruiken. Als u nog nooit een netwerkbeveiligingsgroep hebt gemaakt, kunt u een snelle [zelfstudie](tutorial-filter-network-traffic.md) uitvoeren om enige ervaring met het maken van een netwerkbeveiligingsgroep op te doen. Zie [Een netwerkbeveiligingsgroep maken, wijzigen of verwijderen](manage-network-security-group.md) als u bekend bent met het netwerkbeveiligingsgroepen en u ze wilt beheren. Zie [Een probleem met een netwerkverkeersfilter in een virtuele machine vaststellen](diagnose-network-traffic-filter-problem.md) als u communicatieproblemen hebt en problemen met netwerkbeveiligingsgroepen wilt oplossen. U kunt [logboekregistratie van stromen in netwerkbeveiligingsgroepen](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) inschakelen voor het [analyseren van netwerkverkeer](../network-watcher/traffic-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) naar en van resources waaraan een netwerkbeveiligingsgroep is gekoppeld.

## <a name="security-rules"></a>Beveiligingsregels

Een netwerkbeveiligingsgroep bevat nul regels of zoveel regels als u wilt binnen de [limieten](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) van uw Azure-abonnement. Elke regel geeft de volgende eigenschappen aan:

|Eigenschap  |Uitleg  |
|---------|---------|
|Naam|Een unieke naam binnen de netwerkbeveiligingsgroep.|
|Prioriteit | Een getal tussen 100 en 4096. Regels worden verwerkt in volgorde van prioriteit, waarbij lagere getallen worden verwerkt vóór hogere getallen omdat lagere getallen een hogere prioriteit hebben. Zodra het verkeer overeenkomt met een regel, wordt de verwerking beëindigd. Daardoor worden regels met een lagere prioriteit (een hoger getal) die dezelfde kenmerken hebben als regels met een hogere prioriteit, niet verwerkt.|
|Bron of doel| Een IP-adres, CIDR-blok (bijvoorbeeld 10.0.0.0/24), [servicetag](#service-tags) of [toepassingsbeveiligingsgroep](#application-security-groups). Als u een adres opgeeft voor een Azure-resource, geeft u het privé-IP-adres op dat aan de resource is toegewezen. Netwerkbeveiligingsgroepen worden verwerkt nadat Azure een openbaar IP-adres vertaalt naar een privé-IP-adres voor binnenkomend verkeer en voordat Azure een privé-IP-adres naar een openbaar IP-adres voor uitgaand verkeer vertaalt. Meer informatie over de [IP-adressen](virtual-network-ip-addresses-overview-arm.md) van Azure. Als u een bereik, servicetag of toepassingsbeveiligingsgroep opgeeft, hoeft u minder beveiligingsregels te maken. De mogelijkheid om in een regel meerdere afzonderlijke IP-adressen en -bereiken op te geven (u kunt niet meerdere servicetags of toepassingsgroepen opgeven) wordt aangeduid met de term [uitgebreide beveiligingsregels](#augmented-security-rules). Uitgebreide beveiligingsregels kunnen alleen worden gemaakt in netwerkbeveiligingsgroepen die zijn gemaakt via het Resource Manager-implementatiemodel. U kunt niet meerdere IP-adressen en IP-adresbereiken opgeven in netwerkbeveiligingsgroepen die zijn gemaakt via het klassieke implementatiemodel. Meer informatie over [implementatiemodellen van Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|Protocol     | TCP, UDP of Alle, dat TCP, UDP en ICMP omvat. U kunt niet alleen ICMP opgeven, dus als u ICMP vereist, gebruikt u Alle. |
|Richting| Hiermee wordt aangegeven of de regel van toepassing is op binnenkomend of uitgaand verkeer.|
|Poortbereik     |U kunt één poort of een poortbereik opgeven. U kunt bijvoorbeeld 80 of 10000-10005 opgeven. Als u bereiken opgeeft, hoeft u minder beveiligingsregels te maken. Uitgebreide beveiligingsregels kunnen alleen worden gemaakt in netwerkbeveiligingsgroepen die zijn gemaakt via het Resource Manager-implementatiemodel. U kunt niet meerdere poorten of poortbereiken opgeven in dezelfde beveiligingsregel in netwerkbeveiligingsgroepen die zijn gemaakt via het klassieke implementatiemodel.   |
|Bewerking     | Toestaan of weigeren        |

Beveiligingsregels voor netwerkbeveiligingsgroepen worden op prioriteit geëvalueerd op basis van de 5 tuple-gegevens (bron, bronpoort, doel, doelpoort en protocol) voor het toestaan of weigeren van het verkeer. Voor bestaande verbindingen wordt een stroomrecord gemaakt. Communicatie wordt toegestaan of geweigerd op basis van de verbindingsstatus van de stroomrecord. Met de stroomrecord wordt een netwerkbeveiligingsgroep toegestaan stateful te zijn. Als u bijvoorbeeld een beveiligingsregel voor uitgaand verkeer opgeeft voor elk adres via poort 80, hoeft u geen beveiligingsregel voor binnenkomend verkeer op te geven voor de reacties op het uitgaande verkeer. U hoeft alleen een beveiligingsregel voor binnenkomend verkeer op te geven als de communicatie extern is gestart. Het omgekeerde geldt ook. Als binnenkomend verkeer via een poort is toegestaan, is het niet nodig om een beveiligingsregel voor uitgaand verkeer op te geven om te reageren op verkeer via die poort.
Bestaande verbindingen kunnen niet worden onderbroken wanneer u een beveiligingsregel verwijdert die de stroom mogelijk heeft gemaakt. Verkeersstromen worden onderbroken wanneer er verbindingen worden gestopt en er gedurende minstens een paar minuten in beide richtingen geen verkeer stroomt.

Er gelden beperkingen voor het aantal beveiligingsregels dat u in een netwerkbeveiligingsgroep kunt maken. Zie [Netwerkenlimieten](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie.

## <a name="augmented-security-rules"></a>Uitgebreide beveiligingsregels

Uitgebreide beveiligingsregels vereenvoudigen de beveiligingsdefinitie voor virtuele netwerken, zodat u een uitgebreider en complexer netwerkbeveiligingsbeleid kunt definiëren met minder regels. U kunt meerdere poorten en meerdere expliciete IP-adressen en -bereiken combineren tot één gemakkelijk te begrijpen beveiligingsregel. U kunt uitgebreide regels gebruiken in de velden voor bron, doel en poort van een regel. Om het onderhoud van de definitie van uw beveiligingsregel te vereenvoudigen, kunt u uitgebreide beveiligingsregels combineren met [servicetags](#service-tags) of [toepassingsbeveiligingsgroepen](#application-security-groups). Er gelden beperkingen voor het aantal adressen, bereiken en poorten dat u in een regel kunt opgeven. Zie [Netwerkenlimieten](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie.

## <a name="service-tags"></a>Servicetags

 Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels die het maken van beveiligingsregel vereenvoudigt. U kunt niet uw eigen servicetag maken en ook niet opgeven welke IP-adressen zijn opgenomen in een tag. Microsoft beheert de adresvoorvoegsels die de servicetag omvat en werkt de servicetag automatisch bij wanneer adressen veranderen. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. De volgende servicetags zijn beschikbaar voor gebruik in de definitie van beveiligingsregels. De namen verschillen enigszins tussen [Azure-implementatiemodellen](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

* **VirtualNetwork** (Resource Manager) (**VIRTUAL_NETWORK** voor de klassieke versie): dit label omvat de adresruimte van het virtuele netwerk (alle CIDR-bereiken die zijn gedefinieerd voor het virtuele netwerk), alle verbonden on-premises adresruimten en [via peering gekoppelde](virtual-network-peering-overview.md) virtuele netwerken of virtuele netwerken die zijn verbonden via een [virtuele netwerkgateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** voor klassiek): met deze tag wordt de load balancer voor de infrastructuur van Azure aangeduid. De tag wordt omgezet in het [IP-adres van een Azure-datacenter](https://www.microsoft.com/download/details.aspx?id=41653) van waaruit statuscontroles van Azure worden uitgevoerd. Als u de load balancer van Azure niet gebruikt, kunt u deze regel onderdrukken.
* **Internet** (Resource Manager) (**INTERNET** voor klassiek): met deze tag wordt de IP-adresruimte aangeduid die zich buiten het virtuele netwerk bevindt en bereikbaar is via internet. Dit adresbereik omvat ook de [openbare IP-adresruimte van Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* **AzureTrafficManager** (Alleen Resource Manager): deze tag geeft de IP-adresruimte aan voor de Azure Traffic Manager test-IP-adressen. Meer informatie over de test-IP-adressen van Traffic Manager vindt u in de [Veelgestelde vragen over Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). U kunt de lijst met voorvoegsels downloaden die aan deze tag zijn toegewezen voor de Azure-clouds [Openbaar](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) en [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064).
* **Storage** (alleen Resource Manager): deze tag geeft de IP-adresruimte voor de service Azure Storage aan. Als u *Storage* opgeeft als waarde, wordt verkeer naar de opslag toegestaan of geweigerd. Als u toegang tot opslag alleen wilt toestaan in een specifieke [regio](https://azure.microsoft.com/regions), kunt u de regio opgeven. Als u toegang tot Azure Storage bijvoorbeeld alleen wilt toestaan in de regio US - oost, kunt u *Storage.EastUS* opgeven als servicetag. De tag vertegenwoordigt de service, maar geen specifieke exemplaren van de service. De tag vertegenwoordigt bijvoorbeeld de service Azure Storage, maar geen specifiek Azure Storage-account. Alle adresvoorvoegsels die worden vertegenwoordigd door deze tag worden ook vertegenwoordigd door de tag **Internet**. U kunt de lijst met voorvoegsels downloaden die aan deze tag zijn toegewezen voor de Azure-clouds [Openbaar](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) en [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064).
* **Sql** (alleen voor Resource Manager): deze tag geeft de adresvoorvoegsels aan van de services Azure SQL Database en Azure SQL Data Warehouse. Als u *Sql* opgeeft als waarde, wordt verkeer naar Sql toegestaan of geweigerd. Als u toegang tot Sql alleen wilt toestaan in een specifieke [regio](https://azure.microsoft.com/regions), kunt u de regio opgeven. Als u toegang tot Azure SQL Database bijvoorbeeld alleen wilt toestaan in de regio US - oost, kunt u *Sql.EastUS* opgeven als servicetag. De tag vertegenwoordigt de service, maar geen specifieke exemplaren van de service. De tag vertegenwoordigt bijvoorbeeld de service Azure SQL Database, maar geen specifieke SQL-database of -server. Alle adresvoorvoegsels die worden vertegenwoordigd door deze tag worden ook vertegenwoordigd door de tag **Internet**. U kunt de lijst met voorvoegsels downloaden die aan deze tag zijn toegewezen voor de Azure-clouds [Openbaar](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) en [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064).
* **AzureCosmosDB** (alleen voor Resource Manager): deze tag geeft de adresvoorvoegsels aan van de Azure Cosmos Database-service. Als u *AzureCosmosDB* opgeeft als waarde, wordt verkeer naar AzureCosmosDB toegestaan of geweigerd. Als u alleen toegang wilt toestaan tot AzureCosmosDB in een specifieke [regio](https://azure.microsoft.com/regions), kunt u de regio in de volgende indeling AzureCosmosDB.[regionaam] specificeren. U kunt de lijst met voorvoegsels downloaden die aan deze tag zijn toegewezen voor de Azure-clouds [Openbaar](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) en [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064).
* **AzureKeyVault** (alleen voor Resource Manager): deze tag geeft de adresvoorvoegsels van de Azure-KeyVault-service. Als u *AzureKeyVault* opgeeft als waarde, wordt verkeer naar AzureKeyVault toegestaan of geweigerd. Als u alleen toegang wilt toestaan tot AzureKeyVault in een specifieke [regio](https://azure.microsoft.com/regions), kunt u de regio in de volgende indeling AzureKeyVault.[regionaam] specificeren. U kunt de lijst met voorvoegsels downloaden die aan deze tag zijn toegewezen voor de Azure-clouds [Openbaar](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) en [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064).

> [!NOTE]
> Servicetags van Azure-services geven de adresvoorvoegsels van de specifieke cloud die wordt gebruikt. Regionale servicetags worden niet ondersteund op nationale clouds, alleen in de algemene indeling. Bijvoorbeeld *Storage* en *Sql*.

> [!NOTE]
> Als u een [service-eindpunt voor een virtueel netwerk](virtual-network-service-endpoints-overview.md) voor een service als Azure Storage of Azure SQL Database implementeert, wordt een [route](virtual-networks-udr-overview.md#optional-default-routes) naar een virtueel netwerksubnet voor de service toegevoegd. De adresvoorvoegsels in de route zijn dezelfde adresvoorvoegsels (of CIDR-bereiken) als de bijbehorende servicetag.

## <a name="default-security-rules"></a>Standaardbeveiligingsregels

Azure maakt de volgende standaardregels in elke netwerkbeveiligingsgroep die u maakt:

### <a name="inbound"></a>Inkomend

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Prioriteit|Bron|Bronpoorten|Doel|Doelpoorten|Protocol|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Alle|Toestaan|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Prioriteit|Bron|Bronpoorten|Doel|Doelpoorten|Protocol|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Alle|Toestaan|

#### <a name="denyallinbound"></a>DenyAllInbound

|Prioriteit|Bron|Bronpoorten|Doel|Doelpoorten|Protocol|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Alle|Weigeren|

### <a name="outbound"></a>Uitgaand

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Prioriteit|Bron|Bronpoorten| Doel | Doelpoorten | Protocol | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Alle | Toestaan |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Prioriteit|Bron|Bronpoorten| Doel | Doelpoorten | Protocol | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Alle | Toestaan |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Prioriteit|Bron|Bronpoorten| Doel | Doelpoorten | Protocol | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Alle | Weigeren |

In de kolommen **Bron** en **Doel** zijn *VirtualNetwork*, *AzureLoadBalancer* en *Internet* [servicetags](#service-tags) in plaats van IP-adressen. In de kolom protocol omvat **Alle** TCP, UDP en ICMP. Wanneer u een regel maakt, kunt u TCP, UDP of Alle opgeven, maar u kunt niet alleen ICMP opgeven. Als uw regel ICMP vereist, selecteert u daarom *Alle* voor protocol. *0.0.0.0/0* in de kolommen **Bron** en **Doel** vertegenwoordigt alle adressen.
 
U kunt de standaardregels niet verwijderen, maar u kunt ze negeren door regels te maken met een hogere prioriteit.

## <a name="application-security-groups"></a>Toepassingsbeveiligingsgroepen

Met behulp van toepassingsbeveiligingsgroepen kunt u netwerkbeveiliging configureren als een natuurlijk verlengstuk van de structuur van een toepassing, waarbij u virtuele machines kunt groeperen en netwerkbeveiligingsbeleid kunt definiëren op basis van die groepen. U kunt het beveiligingsbeleid op grote schaal opnieuw gebruiken zonder handmatig onderhoud van expliciete IP-adressen. Het platform verwerkt de complexiteit van expliciete IP-adressen en meerdere regelsets, zodat u zich kunt richten op uw bedrijfslogica. Kijk eens naar de volgende voorbeelden voor een beter begrip van toepassingsbeveiligingsgroepen:

![Toepassingsbeveiligingsgroepen](./media/security-groups/application-security-groups.png)

In de vorige afbeelding zijn *NIC1* en *NIC2* leden van de toepassingsbeveiligingsgroep *AsgWeb*. *NIC3* is een lid van de toepassingsbeveiligingsgroep *AsgLogic*. *NIC4* is een lid van de toepassingsbeveiligingsgroep *AsgDb*. Hoewel elke netwerkinterface in dit voorbeeld een lid is van slechts één toepassingsbeveiligingsgroep, kan een netwerkinterface lid zijn van meerdere toepassingsbeveiligingsgroepen, maar niet meer dan de opgegeven [Azure-limiet](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Aan geen van de netwerkinterfaces is een netwerkbeveiligingsgroep gekoppeld. *NSG1* is gekoppeld aan beide subnetten en bevat de volgende regels:

### <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Deze regel is vereist om verkeer van internet naar de webservers te laten lopen. Binnenkomend verkeer van internet wordt geweigerd door de standaardbeveiligingsregel [DenyAllInbound](#denyallinbound). Daarom is er geen extra regel nodig voor de toepassingsbeveiligingsgroepen *AsgLogic* of *AsgDb*.

|Prioriteit|Bron|Bronpoorten| Doel | Doelpoorten | Protocol | Access |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Toestaan |

### <a name="deny-database-all"></a>Deny-Database-All

De standaardbeveiligingsregel [AllowVNetInBound](#allowvnetinbound) staat communicatie toe tussen resources in hetzelfde virtuele netwerk. Daarom is deze regel vereist voor het weigeren van verkeer dat van een willekeurige resource afkomstig is.

|Prioriteit|Bron|Bronpoorten| Doel | Doelpoorten | Protocol | Access |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Alle | Weigeren |

### <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Deze regel staat verkeer toe van de toepassingsbeveiligingsgroep *AsgLogic* naar de toepassingsbeveiligingsgroep *AsgDb*. De prioriteit voor deze regel is hoger dan de prioriteit voor de regel *Deny-Database-All*. Als gevolg hiervan wordt deze regel verwerkt vóór de regel *Deny-Database-All*, zodat verkeer van de toepassingsbeveiligingsgroep *AsgLogic* wordt toegestaan, terwijl al het andere verkeer wordt geblokkeerd.

|Prioriteit|Bron|Bronpoorten| Doel | Doelpoorten | Protocol | Access |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Toestaan |

De regels die een toepassingsbeveiligingsgroep als bron of doel opgeven, worden alleen toegepast op netwerkinterfaces die lid van de toepassingsbeveiligingsgroep. Als de netwerkinterface geen lid is van een toepassingsbeveiligingsgroep, wordt de regel niet toegepast op de netwerkinterface, ook niet als de netwerkbeveiligingsgroep aan het subnet is gekoppeld.

Toepassingsbeveiligingsgroepen hebben de volgende beperkingen:

-   Er gelden beperkingen voor het aantal toepassingsbeveiligingsgroepen dat u met een abonnement kunt hebben. Er gelden ook andere beperkingen met betrekking tot toepassingsbeveiligingsgroepen. Zie [Netwerkenlimieten](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie.
- U kunt in een beveiligingsregel een en dezelfde toepassingsbeveiligingsgroep opgeven als de bron en het doel. Het is niet mogelijk om meerdere toepassingsbeveiligingsgroepen in de bron of het doel op te geven.
- Alle netwerkinterfaces die zijn toegewezen aan een toepassingsbeveiligingsgroep, moeten zich in hetzelfde virtuele netwerk bevinden als de eerste netwerkinterface die aan de toepassingsbeveiligingsgroep is toegewezen. Bijvoorbeeld, als de eerste netwerkinterface die aan een toepassingsbeveiligingsgroep met de naam *AsgWeb* is toegewezen, zich in het virtuele netwerk met de naam *VNet1* bevindt, moeten alle volgende netwerkinterfaces die zijn toegewezen aan *AsgWeb*, aanwezig zijn in *VNet1*. U kunt geen netwerkinterfaces van verschillende virtuele netwerken toevoegen aan dezelfde toepassingsbeveiligingsgroep.
- Als u in een beveiligingsregel een toepassingsbeveiligingsgroep als bron en doel opgeeft, moeten de netwerkinterfaces in beide toepassingsbeveiligingsgroepen zich in hetzelfde virtuele netwerk bevinden. Als *AsgLogic* bijvoorbeeld netwerkinterfaces van *VNet1* bevat en *AsgDb* netwerkinterfaces van *VNet2*, is het niet mogelijk om in een regel *AsgLogic* toe te wijzen als bron en *AsgDb* als doel. Alle netwerkinterfaces voor zowel de bron- als de doeltoepassingsbeveiligingsgroepen moeten aanwezig zijn in hetzelfde virtuele netwerk.

> [!TIP]
> Als u het gewenste aantal beveiligingsregels wilt verminderen en tevens het wijzigen van de regels wilt beperken, plant u de toepassingsbeveiligingsgroepen die u nodig hebt en maakt u, indien mogelijk, regels met behulp van servicetags of toepassingsbeveiligingsgroepen in plaats van afzonderlijke IP-adressen of -bereiken.

## <a name="how-traffic-is-evaluated"></a>Verkeer evalueren

U kunt resources van diverse Azure-services in een virtueel Azure-netwerk implementeren. Zie [Services die kunnen worden geïmplementeerd in een virtueel netwerk](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) voor de volledige lijst. U kunt nul of één netwerkbeveiligingsgroep aan elk [subnet](virtual-network-manage-subnet.md#change-subnet-settings) van een virtueel netwerk en [netwerkinterface](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) in een virtuele machine koppelen. Dezelfde netwerkbeveiligingsgroep kan aan zoveel subnetten en netwerkinterfaces worden gekoppeld als u wilt.

In de volgende afbeelding ziet u verschillende scenario's voor het implementeren van netwerkbeveiligingsgroepen om netwerkverkeer naar en van internet via TCP-poort 80 toe te staan:

![Verwerken van NSG's](./media/security-groups/nsg-interaction.png)

Raadpleeg de afbeelding hierboven in combinatie met de volgende tekst als u wilt weten hoe binnenkomende en uitgaande regels voor netwerkbeveiligingsgroepen worden verwerkt:

### <a name="inbound-traffic"></a>Binnenkomend verkeer

Voor binnenkomend verkeer worden eerst de regels verwerkt in een netwerkbeveiligingsgroep die aan een subnet is gekoppeld, indien aanwezig. Vervolgens worden de regels verwerkt die zijn gekoppeld aan een netwerkinterface, indien aanwezig.

- **VM1**: de beveiligingsregels in *NSG1* worden verwerkt, omdat deze is gekoppeld aan *Subnet1* en *VM1* zich in *Subnet1* bevindt. Tenzij u een regel hebt gemaakt waardoor poort 80 binnenkomend verkeer toestaat, wordt het verkeer geweigerd door de standaardbeveiligingsregel [DenyAllInbound](#denyallinbound) en nooit geëvalueerd door *NSG2*, omdat *NSG2* is gekoppeld aan de netwerkinterface. Als *NSG1* een beveiligingsregel bevat die poort 80 toestaat, wordt het verkeer door *NSG2* verwerkt. Als u poort 80 voor de virtuele machine wilt toestaan, moet zowel *NSG1* als *NSG2* een regel bevatten die verkeer vanaf internet via poort 80 toestaat.
- **VM2**: de regels in *NSG1* worden verwerkt omdat *VM2* zich ook in *Subnet1* bevindt. Omdat *VM2* geen netwerkinterface bevat die aan de bijbehorende netwerkinterface is gekoppeld, wordt hier alle verkeer ontvangen dat is toegestaan via *NSG1* of wordt alle verkeer geweigerd dat is geweigerd door *NSG1*. Verkeer wordt voor alle resources in hetzelfde subnet toegestaan of geweigerd als een netwerkbeveiligingsgroep aan een subnet is gekoppeld.
- **VM3**: omdat er geen netwerkbeveiligingsgroep is gekoppeld aan *Subnet2*, wordt verkeer naar het subnet toegestaan en verwerkt door *NSG2*, omdat *NSG2* is gekoppeld aan de netwerkinterface die met *VM3* is verbonden.
- **VM4**: verkeer naar *VM4* is toegestaan omdat er geen netwerkbeveiligingsgroep is gekoppeld aan *Subnet3* of de netwerkinterface in de virtuele machine. Alle netwerkverkeer wordt toegestaan via een subnet en netwerkinterface als er geen netwerkbeveiligingsgroep aan is gekoppeld.

### <a name="outbound-traffic"></a>Uitgaand verkeer

Voor uitgaand verkeer worden eerst de regels verwerkt in een netwerkbeveiligingsgroep die aan een netwerkinterface is gekoppeld, indien aanwezig. Vervolgens worden de regels in een netwerkbeveiligingsgroep verwerkt die zijn gekoppeld aan het subnet, indien aanwezig.

- **VM1**: de beveiligingsregels in *NSG2* worden verwerkt. Tenzij u een beveiligingsregel maakt die uitgaand verkeer naar internet via poort 80 weigert, wordt in zowel *NSG1* als *NSG2* verkeer toegestaan door de standaardbeveiligingsregel [AllowInternetOutbound](#allowinternetoutbound). Als *NSG2* een beveiligingsregel bevat die poort 80 weigert, wordt het verkeer geweigerd en nooit door *NSG1* geëvalueerd. Als u binnenkomend verkeer van de virtuele machine via poort 80 wilt weigeren, moet een van de netwerkbeveiligingsgroepen, of beide, een regel bevatten die uitgaand verkeer naar internet via poort 80 weigert.
- **VM2**: al het verkeer wordt via de netwerkinterface naar het subnet verzonden, omdat aan de netwerkinterface die met *VM2* is verbonden, geen netwerkbeveiligingsgroep is gekoppeld. De regels in *NSG1* worden verwerkt.
- **VM3**: als *NSG2* een beveiligingsregel bevat die poort 80 weigert, wordt het verkeer geweigerd. Als *NSG2* een beveiligingsregel bevat die poort 80 toestaat, wordt via poort 80 uitgaand verkeer naar internet toegestaan, omdat er geen netwerkbeveiligingsgroep aan *Subnet2* is gekoppeld.
- **VM4**: al het netwerkverkeer naar *VM4* is toegestaan omdat er geen netwerkbeveiligingsgroep is gekoppeld aan de netwerkinterface die met de virtuele machine is verbonden, of aan *Subnet3*.

U kunt eenvoudig de cumulatieve regels bekijken die op een netwerkinterface worden toegepast door de [effectieve beveiligingsregels](virtual-network-network-interface.md#view-effective-security-rules) voor een netwerkinterface te bekijken. U kunt ook de functie [IP-stroom controleren](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) in Azure Network Watcher gebruiken om te bepalen of de communicatie van of naar een netwerkinterface is toegestaan. IP-stroom controleren geeft aan of communicatie is toegestaan en welke netwerkbeveiligingsregel verkeer toestaat of weigert.

> [!NOTE]
> Netwerkbeveiligingsgroepen worden gekoppeld aan subnetten of aan virtuele machines en cloudservices die het klassieke implementatiemodel implementeren, in plaats van aan netwerkinterfaces in het Resource Manager-implementatiemodel. Zie [Over Azure-implementatiemodellen](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over Azure-implementatiemodellen.

> [!TIP]
> Het wordt aangeraden een netwerkbeveiligingsgroep aan hetzij een subnet, hetzij een netwerkinterface te koppelen, tenzij u een bepaalde reden hebt om ze aan beide te koppelen. Aangezien regels in een netwerkbeveiligingsgroep die aan een subnet is gekoppeld, kunnen conflicteren met regels in een netwerkbeveiligingsgroep die aan een netwerkinterface is gekoppeld, kunnen er onverwachte communicatieproblemen optreden waarvoor een probleemoplossing dient te worden uitgevoerd.

## <a name="azure-platform-considerations"></a>Overwegingen bij het Azure-platform

- **Virtueel IP-adres van het hostknooppunt:** basisinfrastructuurservices zoals DHCP, DNS en statuscontrole worden geleverd via de gevirtualiseerde host-IP-adressen 168.63.129.16 en 169.254.169.254. Deze openbare IP-adressen zijn van Microsoft en zijn de enige gevirtualiseerde IP-adressen die in alle regio's voor dit doel worden gebruikt. De adressen worden toegewezen aan het fysieke IP-adres van de servermachine (hostknooppunt) die fungeert als host voor de virtuele machine. Het hostknooppunt fungeert als de DHCP-relay, de recursieve DNS-omzetter en de bron voor de statuscontrole van de load balancer en de machine. Communicatie met deze IP-adressen is geen aanval. Als u verkeer naar of van deze IP-adressen blokkeert, werkt een virtuele machine mogelijk niet goed.
- **Licentieverlening (Key Management Service):** voor alle Windows installatiekopieën die op virtuele machines worden uitgevoerd, is een licentie vereist. Hiervoor wordt een licentieaanvraag verstuurd naar de Key Management Service-hostservers waarop dergelijke query's worden afgehandeld. De uitgaande aanvraag wordt gedaan via poort. 1688. Voor implementaties die gebruikmaken van een configuratie met de [standaardroute 0.0.0.0/0](virtual-networks-udr-overview.md#default-route), wordt deze platformregel uitgeschakeld.
- **Virtuele machines in groepen met gelijke taakverdeling**: de bronpoort en het bronadresbereik die worden toegepast, zijn die van de oorspronkelijke computer, niet van de load balancer. De doelpoort en het doeladresbereik zijn die van de doelcomputer, niet van de load balancer.
- **Azure-service-exemplaren**: exemplaren van verschillende Azure-services, zoals HDInsight, toepassingsserviceomgevingen en virtuele-machineschaalsets, worden geïmplementeerd in virtuele netwerksubnetten. Zie [Virtueel netwerk voor Azure-services](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) voor een volledige lijst met services die u in virtuele netwerken kunt implementeren. Zorg ervoor dat u vertrouwd raakt met de poortvereisten voor elke service voordat u een netwerkbeveiligingsgroep toepast op het subnet waarin de resource is geïmplementeerd. Als u poorten weigert die vereist zijn voor de service, werkt de service niet naar behoren.
- **Uitgaande e-mail verzenden**: Microsoft raadt u aan om geverifieerde SMTP-relayservices te gebruiken (doorgaans verbonden via TCP-poort 587, maar ook vaak andere) om e-mail vanaf Azure Virtual Machines te verzenden. SMTP-relayservices leggen zich toe op de reputatie van de afzender, om zo de kans dat e-mailproviders van derden berichten weigeren, tot het uiterste terug te dringen. Dergelijke SMTP-relayservices omvatten, maar beperken zich niet tot Exchange Online Protection en SendGrid. Het gebruik van de SMTP-relayservices wordt in Azure op geen enkele wijze beperkt, ongeacht welk type abonnement u hebt. 

  Als u uw Azure-abonnement vóór 15 november 2017 hebt gemaakt, kunt u naast de mogelijkheid om gebruik te maken van SMTP-relayservices ook rechtstreeks e-mail verzenden via TCP-poort 25. Als u uw abonnement na 15 november 2017 hebt gemaakt kunt u mogelijk geen e-mail rechtstreeks via poort 25 verzenden. Hoe uitgaande communicatie via poort 25 verloopt, hangt als volgt samen met het type abonnement dat u hebt:

     - **Enterprise Overeenkomst**: communicatie via poort 25 is toegestaan. U kunt uitgaande e-mail rechtstreeks vanaf virtuele machines naar externe e-mailproviders verzenden, zonder dat daar beperkingen voor gelden op grond van het Azure-platform. 
     - **Betalen naar gebruik:** communicatie via uitgaande poort 25 is voor alle resources geblokkeerd. Als u e-mail vanaf een virtuele machine rechtstreeks naar de externe e-mailproviders wilt verzenden (niet via een geverifieerde SMTP-relay), kunt u een aanvraag indienen om de beperking op te heffen. Het is aan Microsoft om te bepalen of aanvragen worden gecontroleerd en goedgekeurd, en aanvragen worden alleen toegekend nadat er controles ter voorkoming van fraude zijn uitgevoerd. Als u een aanvraag wilt indienen, opent u een ondersteuningsaanvraag met het probleemtype *Technisch*, *Virtuele netwerkverbinding*, *Kan geen e-mail verzenden (SMTP/poort 25)*. Vermeld in uw ondersteuningsaanvraag informatie zoals waarom uw abonnement e-mail rechtstreeks naar e-mailproviders moet kunnen verzenden in plaats van via een geverifieerde SMTP-relay. Als de aanvraag voor uw abonnement wordt toegekend, kunnen alleen de virtuele machines die na de toekenningsdatum zijn gemaakt poort 25 voor uitgaande communicatie gebruiken.
     - **MSDN, Azure Pass, Azure in Open, Education, BizSpark en gratis proefabonnementen**: uitgaande communicatie via poort 25 voor alle resources geblokkeerd. Er kunnen geen aanvragen worden ingediend voor het opheffen van de beperking, omdat zulke aanvragen niet worden toegekend. Als u e-mail moet verzenden vanaf uw virtuele machine, dient u een SMTP-relayservice te gebruiken.
     - **Cloudserviceprovider**: klanten die Azure-resources via een cloudserviceprovider gebruiken, kunnen een ondersteuningsaanvraag maken bij hun cloudserviceprovider. Zij kunnen vervolgens aanvragen dat de provider namens hen een blokkering opheft, als er geen beveiligde SMTP-relay kan worden gebruikt.

  Als u in Azure e-mail kunt verzenden via poort 25, garandeert Microsoft niet dat e-mailproviders inkomende e-mail vanaf uw virtuele machine accepteren. Als een specifieke provider e-mail van uw virtuele machine weigert, dient u samen met de provider alle eventuele problemen met de aflevering van berichten of met de spamfilter op te lossen, of u moet een geverifieerde SMTP-relayservice gebruiken.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Een netwerkbeveiligingsgroep maken](tutorial-filter-network-traffic.md).
