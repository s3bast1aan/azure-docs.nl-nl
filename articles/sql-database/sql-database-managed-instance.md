---
title: Azure SQL Database Managed Instance-overzicht | Microsoft Docs
description: Dit onderwerp wordt een Azure SQL Database Managed Instance beschreven en wordt uitgelegd hoe het werkt en wat is het verschil met een individuele database in Azure SQL Database.
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: bonova
ms.openlocfilehash: edacb9fe1d09a4e775f8f7107dfa4d9810f53f07
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006041"
---
# <a name="what-is-a-managed-instance-preview"></a>Wat is een Managed Instance (preview)?

Azure SQL Database Managed Instance (preview) is een nieuwe mogelijkheid van Azure SQL-Database, met bijna 100% compatibiliteit met SQL Server on-premises (Enterprise Edition), biedt een systeemeigen [virtueel netwerk (VNet)](../virtual-network/virtual-networks-overview.md) implementatie die algemene beveiligingsproblemen, en een [bedrijfsmodel](https://azure.microsoft.com/pricing/details/sql-database/) gunstig voor on-premises SQL Server-klanten. Met Managed Instance kunnen bestaande klanten met SQL Server voor lift- and -shift van hun on-premises toepassingen naar de cloud met minimale wijzigingen van toepassing en -database. Managed Instance bewaart op hetzelfde moment, alle PaaS-mogelijkheden (automatische updates voor toepassen van patches en versie, back-up, hoge beschikbaarheid), die aanzienlijk minder beheer-overhead en totale Eigendomskosten.

> [!IMPORTANT]
> Zie [Uw databases migreren naar een volledig beheerde service met Azure SQL Database Managed Instance](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/) voor een lijst met regio's waarin MI momenteel beschikbaar is.
 
Het volgende diagram geeft een overzicht van de belangrijkste functies van het beheerde exemplaar:

![belangrijke functies](./media/sql-database-managed-instance/key-features.png)

Beheerd exemplaar is overwogen als beste platform voor de volgende scenario's: 

- On-premises SQL Server- / IaaS-klanten die willen migreren van hun toepassingen naar een volledig beheerde service met minimale wijzigingen ontwerpen.
- ISV's vertrouwen op SQL-databases, die willen hun klanten te migreren naar de cloud en aanzienlijke concurrentievoordeel dus maar liefst in- of bereiken van de wereldwijde markt. 

Door de algemene beschikbaarheid, Managed Instance is erop gericht om te leveren dicht bij surface area van 100% compatibiliteit met de nieuwste versie van on-premises SQL Server via een gefaseerde release-plan. 

De volgende tabel bevat belangrijke verschillen en ontwikkelaars van gebruiksscenario's tussen SQL IaaS, Azure SQL Database en SQL Database Managed Instance:

| | Gebruiksscenario | 
| --- | --- | 
|SQL Database Managed Instance |Voor klanten die willen migreren van een groot aantal apps van on-premises of IaaS, zelf is ingebouwd, of ISV hebt opgegeven, met voorstellen als lage migratie inspanning mogelijk, beheerd exemplaar. Met behulp van de volledig geautomatiseerde [Data Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) in Azure, klanten kunnen lift- en shift van hun on-premises SQL Server naar een beheerd exemplaar biedt compatibiliteit met SQL Server on-premises en volledig isoleren de exemplaren van de klant met ondersteuning voor systeemeigen VNET.  Met Software Assurance, kunt u exchange-hun bestaande licenties voor kortingstarieven op een SQL Database Managed Instance met de [Azure Hybrid Use Benefit voor SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  SQL Database Managed Instance is de beste Migratiebestemming in de cloud voor SQL Server-exemplaren die hoge beveiliging en een uiterst programmeerbare oppervlak vereisen. |
|Azure SQL-Database (enkel of -groep) |**Elastische pools**: voor klanten nieuwe SaaS-toepassingen met meerdere tenants ontwikkelt of opzettelijk transformeren van hun bestaande on-premises apps in een multitenant SaaS-app, voorstellen elastische pools. Voordelen van dit model zijn: <br><ul><li>Conversie van het bedrijfsmodel van het verkopen van licenties voor het verkopen van serviceabonnementen (voor ISV's)</li></ul><ul><li>Isolatie van tenants eenvoudig en opsommingsteken bewijs</li></ul><ul><li>Een vereenvoudigde database gerichte programmeermodel</li></ul><ul><li>De mogelijkheid om uit te schalen zonder een vaste maximum hebt bereikt</li></ul>**Enkelvoudige databases**: voor het ontwikkelen van nieuwe apps dan SaaS met meerdere tenants, waarvan de werklast stabiel en voorspelbaar is, klanten individuele databases voorstellen. Voordelen van dit model zijn:<ul><li>Een vereenvoudigde database gerichte programmeermodel</li></ul>  <ul><li>Voorspelbare prestaties voor elke database</li></ul>|
|SQL IaaS-virtuele machine|Voor klanten die voor het aanpassen van het besturingssysteem of de database-server, evenals klanten specifieke vereisten voor het uitvoeren van apps van derden elkaar met SQL Server (op dezelfde VM), die SQL-VM's stellen / IaaS als de optimale oplossing|
|||

## <a name="how-to-programmatically-identify-a-managed-instance"></a>Een beheerd exemplaar via een programma te identificeren

De volgende tabel toont enkele eigenschappen, toegankelijk zijn via Transact-SQL, dat u kunt gebruiken voor het detecteren van uw toepassing werkt met Managed Instance en belangrijke eigenschappen ophalen.

|Eigenschap|Waarde|Opmerking|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Deze waarde is gelijk aan die in SQL-Database.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Deze waarde is gelijk aan die in SQL-Database.|
|`SERVERPROPERTY('EngineEdition')`|8|Deze waarde wordt aangeduid Managed Instance.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Volledige DNS-exemplaarnaam in de volgende indeling:<instanceName>.<dnsPrefix>. database.Windows.NET, waar <instanceName> is geleverd door de klant, terwijl <dnsPrefix> automatisch gegenereerde deel uitmaakt van de naam van de globale DNS-naam uniekheid garanderen ('wcus17662feb9ce98', bijvoorbeeld)|Voorbeeld: Mijn-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="key-features-and-capabilities-of-a-managed-instance"></a>Belangrijke functies en mogelijkheden van een beheerd exemplaar 

> [!IMPORTANT]
> Een beheerd exemplaar wordt uitgevoerd met alle van de functies van de meest recente versie van SQL Server, met inbegrip van online bewerkingen, automatische plan correcties en andere prestatieverbeteringen enterprise. 

| **Voordelen van PaaS** | **Bedrijfscontinuïteit** |
| --- | --- |
|Er is geen aanschaffen van hardware en het beheer <br>Er is geen management overhead voor het beheren van de onderliggende infrastructuur <br>Snel inrichten en schalen van service <br>Automatische patching en versie-upgrade <br>Integratie met andere PaaS-services voor gegevens |uptime van 99,99% SLA  <br>Ingebouwde hoge beschikbaarheid <br>Gegevens die worden beveiligd met geautomatiseerde back-ups <br>Klanten kunnen worden geconfigureerd back-up bewaarperiode (vast tot zeven dagen duren in openbare preview-versie) <br>De gebruiker geïnitieerde back-ups <br>Punt in tijd database terugzetten mogelijkheid |
|**Beveiliging en naleving** | **Management**|
|Geïsoleerde omgeving (VNet-integratie, service met één tenant, toegewezen berekeningen en opslag) <br>Transparent Data Encryption<br>Azure AD-verificatie, ondersteuning voor eenmalige aanmelding <br>Voldoet aan de standaarden voor compliance hetzelfde als Azure SQL-database <br>Controleren voor SQL <br>Detectie van bedreigingen |Azure Resource Manager-API voor het automatiseren van service inrichten en schalen <br>Functionaliteit voor handmatige service inrichten en schalen van Azure portal <br>Data migratieservice 

![Eenmalige aanmelding](./media/sql-database-managed-instance/sso.png) 

## <a name="vcore-based-purchasing-model"></a>op vCore gebaseerde aankoopmodel

Het vCore-aanschafmodel biedt u flexibiliteit, controle, transparantie, en een eenvoudige manier te vertalen on-premises vereisten workloads naar de cloud. Dit model kunt u Computing kunt schalen, het geheugen en opslag op basis van hun werkbelasting. Het vCore-model is ook in aanmerking komen voor van 30 procent besparen met de [Azure Hybrid Use Benefit voor SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

Een virtuele kern staat voor de logische CPU met een optie te kiezen tussen verschillende hardwaregeneraties.
- Logische CPU's van de vierde generatie zijn gebaseerd op Intel E5 2673 v3 (Haswell)-processors van 2,4 GHz.
- Gen 5 logische CPU's zijn gebaseerd op Intel E5-2673 v4 (Broadwell) processors van 2,3 GHz.

De volgende tabel vindt u informatie over de optimale configuratie van uw Reken-, geheugen-, opslag- en i/o-resources te selecteren.

||Gen 4|Gen 5|
|----|------|-----|
|Hardware|Intel E5-2673 v3 (Haswell) processors van 2,4 GHz, gekoppeld SSD vCore = 1 PP (fysieke kernen)|Intel E5-2673 v4 (Broadwell) processors van 2,3 GHz, snelle eNVM SSD, vCore = 1 LP (hyper-thread)|
|Prestatieniveaus|8, 16, 24 vCores|8, 16, 24 uur per dag, 32, 40, 64, 80 vCores|
|Geheugen|7 GB per vCore|5.5 GB per vCore|
||||

## <a name="managed-instance-service-tiers"></a>Beheerd exemplaar van service-lagen

Managed Instance is beschikbaar in twee Servicelagen:
- **Algemeen gebruik**: ontworpen voor toepassingen met normale beschikbaarheid en algemene vereisten voor i/o-latentie.
- **Bedrijfskritiek**: ontworpen voor toepassingen met hoge beschikbaarheid en lage i/o-latentie is vereist.
 
> [!IMPORTANT]
> Het wijzigen van de servicelaag van algemeen naar bedrijfskritiek of vice versa wordt niet ondersteund in openbare Preview. Als u uw databases migreren naar een exemplaar in verschillende servicelaag wilt, kunt u maken van nieuwe instantie, databases met een punt in tijd herstel herstellen uit het oorspronkelijke exemplaar en zet vervolgens neer oorspronkelijk exemplaar als het is niet meer nodig. 

### <a name="general-purpose-service-tier"></a>Categorie van de service Algemeen gebruik

De volgende lijst beschrijft de belangrijkste kenmerken van de categorie Algemeen gebruik-service: 

- Ontwerpen voor de meeste zakelijke toepassingen met normale prestaties en HA-vereisten 
- Azure Premium storage van hoge kwaliteit (8 TB) 
- 100 databases / exemplaar 

In deze laag kunt u onafhankelijk opslag selecteren en de rekencapaciteit. 

Het volgende diagram illustreert de actieve reken- en de redundante knooppunten in deze servicelaag.
 
![Categorie van de service Algemeen gebruik](./media/sql-database-managed-instance/general-purpose-service-tier.png) 

De volgende lijst geeft een overzicht van de belangrijkste kenmerken van de categorie Algemeen gebruik-service:

|Functie | Beschrijving|
|---|---|
| Aantal vCores * | 8, 16, 24 uur per dag (gen 4)<br>8, 16, 24 uur per dag, 32, 40, 64, 80 (gen 5)|
| SQL Server-versie / build | SQL Server nieuwste (beschikbaar) |
| De opslaggrootte min | 32 GB |
| Maximumgrootte van opslag | 8 TB |
| Maximale opslagruimte per database | Bepaald door de maximale opslagruimte per exemplaar |
| Verwachte opslag IOPS | 500-7500 IOP's per bestand (afhankelijk bestand). Zie [Premium-opslag](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes) |
| Het aantal gegevensbestanden (rijen) per de database | Meerdere | 
| Aantal logboekbestanden (logboek) per database | 1 | 
| Automatische back-ups beheren | Ja |
| HOGE BESCHIKBAARHEID | Op basis van externe opslag en [Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| Ingebouwd exemplaar en database controleren en metrische gegevens | Ja |
| Automatische softwarepatches | Ja |
| VNet - implementatie van Azure Resource Manager | Ja |
| VNet - klassieke implementatiemodel | Nee |
| Portal-ondersteuning | Ja|
|||

\* Een virtuele kern staat voor de logische CPU met een optie te kiezen tussen verschillende hardwaregeneraties. Gen 4 logische CPU's zijn gebaseerd op Intel E5-2673 v3 (Haswell) processors van 2,4 GHz en Gen 5 logische CPU's zijn gebaseerd op Intel E5-2673 v4 (Broadwell) processors van 2,3 GHz. 

### <a name="business-critical-service-tier"></a>Kritieke-bedrijfslaag

Kritieke-bedrijfslaag is gebouwd voor toepassingen met hoge i/o-vereisten. Het biedt de hoogste herstelmogelijkheden bij fouten met behulp van verschillende geïsoleerde altijd op replica's. Het volgende diagram illustreert de onderliggende architectuur van deze servicelaag:

![Kritieke-bedrijfslaag](./media/sql-database-managed-instance/business-critical-service-tier.png)  

De volgende lijst geeft een overzicht van de belangrijkste kenmerken van de laag bedrijfskritiek service: 
-   Ontworpen voor zakelijke toepassingen met de hoogste prestaties en HA-vereisten 
-   Wordt geleverd met zeer snelle SSD-opslag (maximaal 1 TB op Gen 4 en maximaal 4 TB voor Gen 5)
-   Biedt ondersteuning voor maximaal 100 databases per exemplaar 

|Functie | Beschrijving|
|---|---|
| Aantal vCores * | 8, 16, 24 uur per dag, 32 (gen 4)<br>8, 16, 24 uur per dag, 32, 40, 64, 80 (gen 5)|
| SQL Server-versie / build | SQL Server nieuwste (beschikbaar) |
| Extra functies | [In-Memory OLTP](sql-database-in-memory.md)<br> 1 extra alleen-lezen replica ([Read Scale-Out](sql-database-read-scale-out.md))
| De opslaggrootte min | 32 GB |
| Maximumgrootte van opslag | Gen 4: 1 TB (alle vCore-grootten<br> Gen 5:<ul><li>1 TB voor 8, 16 vcores uitvoert</li><li>2 TB voor 24 vCores</li><li>4 TB voor 32, 40, 64, 80 vCores</ul>|
| Maximale opslagruimte per database | Bepaald door de maximale opslagruimte per exemplaar |
| Het aantal gegevensbestanden (rijen) per de database | Meerdere | 
| Aantal logboekbestanden (logboek) per database | 1 | 
| Automatische back-ups beheren | Ja |
| HOGE BESCHIKBAARHEID | Op basis van [Always On Availability Groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) en [Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| Ingebouwd exemplaar en database controleren en metrische gegevens | Ja |
| Automatische softwarepatches | Ja |
| VNet - implementatie van Azure Resource Manager | Ja |
| VNet - klassieke implementatiemodel | Nee |
| Portal-ondersteuning | Ja|
|||

## <a name="advanced-security-and-compliance"></a>Geavanceerde beveiliging en naleving van voorschriften 

### <a name="managed-instance-security-isolation"></a>Isolatie beheerde exemplaar 

Beheerd exemplaar bieden extra beveiligingsisolatie van andere tenants in de Azure-cloud. Isolatie bevat: 

- [Implementatie van systeemeigen virtuele netwerk](sql-database-managed-instance-vnet-configuration.md) en verbinding met uw on-premises-omgeving met behulp van Azure Express Route- of VPN-Gateway 
- SQL-eindpunt wordt alleen weergegeven via een privé IP-adres, zodat u veilige connectiviteit van Azure privé of hybride netwerken
- Één tenant met toegewezen onderliggende infrastructuur (compute, storage)

Het volgende diagram geeft een overzicht van de verschillende connectiviteitsopties voor uw toepassingen: 

![hoge beschikbaarheid](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Zie voor meer informatie over VNet-integratie en netwerken enforcements beleid op het subnetniveau meer [een VNet configureren voor Azure SQL Database Managed Instance](sql-database-managed-instance-vnet-configuration.md) en [verbinding maken met uw toepassing naar Azure SQL Database Beheerd exemplaar](sql-database-managed-instance-connect-app.md). 

> [!IMPORTANT]
> Plaats meerdere beheerd exemplaar in hetzelfde subnet bevinden, waar die is toegestaan door uw beveiligingsvereisten, omdat u extra voordelen opent Hiermee. Collocating-exemplaren in hetzelfde subnet aanzienlijk netwerken onderhoud aan de infrastructuur vereenvoudigen en exemplaar inrichtingstijd, omdat de lange duur van de inrichting is gekoppeld aan de kosten van de implementatie eerst beheerd exemplaar in een subnet beperken.


### <a name="auditing-for-compliance-and-security"></a>Controles voor naleving en beveiliging 

[Beheerd exemplaar controle](sql-database-managed-instance-auditing.md) databasegebeurtenissen bijgehouden en geschreven naar een auditlogboek in uw Azure storage-account. Controles kunt naleving van regelgeving, begrijpen databaseactiviteiten en inzicht krijgen in discrepanties en afwijkingen die kunnen wijzen op problemen voor het bedrijf of vermoedelijke beveiligingsschendingen. 

### <a name="data-encryption-in-motion"></a>Versleuteling van gegevens in beweging 

Beheerd exemplaar voor beveiliging van uw gegevens door te geven van de versleuteling van data-in-motion met behulp van Transport Layer Security.

Naast transport layer security, SQL Database Managed Instance biedt bescherming van gevoelige gegevens in beweging, inactieve en tijdens queryverwerking met [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Het unieke Always Encrypted biedt ongeëvenaarde beveiliging tegen diefstal van kritieke gegevens. Bijvoorbeeld, met Always Encrypted, creditcardnummers, worden versleuteld opgeslagen in de database altijd, zelfs tijdens query verwerken, waardoor ontsleuteling bij gebruik door gemachtigde medewerkers of toepassingen die deze gegevens moeten verwerken. 

### <a name="data-encryption-at-rest"></a>Versleuteling van inactieve gegevens 
[Transparante gegevensversleuteling (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) beheerd exemplaar voor Azure SQL-gegevensbestanden, bekend als het versleutelen van gegevens in rust worden versleuteld. TDE voert realtime i/o-versleuteling en ontsleuteling van de gegevens en logboekbestanden. De versleuteling gebruikt een databaseversleutelingssleutel (DEK), dat is opgeslagen in de database-bootrecord voor beschikbaarheid tijdens het herstel. U kunt alle databases in het beheerde exemplaar met transparante gegevensversleuteling beveiligen. TDE is een beproefde technologie voor versleuteling van inactieve gegevens, die verplicht is volgens veel nalevingsstandaarden voor de bescherming tegen diefstal van opslagmedia. Tijdens de openbare preview, wordt de automatische Sleutelbeheer-model ondersteund (uitgevoerd door de PaaS-platform). 

Migratie van een versleutelde database naar SQL Managed Instance wordt via de Azure Database Migration Service (DMS) of systeemeigen terugzetten ondersteund. Als u van plan bent voor het migreren van versleutelde database met behulp van systeemeigen restore, is de migratie van het bestaande TDE-certificaat van de SQL Server on-premises of SQL Server-VM aan het beheerde exemplaar is een vereiste stap. Zie voor meer informatie over opties voor de migratie, [SQL Server-exemplaar migratie naar Azure SQL Database Managed Instance](sql-database-managed-instance-migrate.md).

### <a name="dynamic-data-masking"></a>Dynamische gegevensmaskering 

SQL-Database [dynamische gegevensmaskering](/sql/relational-databases/security/dynamic-data-masking) blootstelling van gevoelige gegevens door deze te maskeren voor replicagegevens gebruikers. Dynamische gegevensmaskering helpt onbevoegde toegang tot gevoelige gegevens voorkomen doordat u aangeven hoeveel van de gevoelige gegevens worden vrijgegeven, met minimale impact op de toepassingslaag. Dit is een beveiligingsfunctie op basis van beleid. De gevoelige gegevens in de resultatenset van een query die is uitgevoerd op toegewezen databasevelden worden verborgen, terwijl de gegevens in de database niet worden gewijzigd. 

### <a name="row-level-security"></a>Beveiliging op rijniveau 

[Beveiliging op rijniveau](/sql/relational-databases/security/row-level-security) kunt u om te bepalen de toegang tot rijen in een database-tabel op basis van de kenmerken van de gebruiker die een query uitvoert (bijvoorbeeld door het groepslidmaatschap of uitvoeringscontext context groep). Beveiliging op rijniveau (RLS) vereenvoudigt het ontwerp en de code van de beveiliging in uw toepassing. Met RLS kunt u beperkingen instellen voor de toegang tot gegevens in rijen. Bijvoorbeeld, ervoor te zorgen dat werknemers alleen de rijen met gegevens die relevant voor hun afdeling zijn, of een gegevenstoegang beperken tot alleen de relevante gegevens. 

### <a name="threat-detection"></a>Detectie van bedreigingen 

[Beheerd exemplaar Threat Detection](sql-database-managed-instance-threat-detection.md) vormt een aanvulling op [Managed Instance controle](sql-database-managed-instance-auditing.md) door te geven van een extra laag met beveiligingsinformatie die is ingebouwd in de service die ongebruikelijke en potentieel schadelijke pogingen tot gedetecteerd toegang tot of misbruik te maken van databases. U wordt gewaarschuwd bij verdachte activiteiten, potentiële kwetsbaarheden, en SQL-injectie aanvallen, en afwijkende patronen voor databasetoegang. Waarschuwingen van Threat Detection kunnen worden bekeken via [Azure Security Center](https://azure.microsoft.com/services/security-center/) en Geef details op van verdachte activiteiten en geven aanbevelingen voor het onderzoeken en tegenhouden.  

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integratie en meervoudige verificatie 

Dankzij [Azure Active Directory-integratie](sql-database-aad-authentication.md) kunt u in SQL Database de identiteit van databasegebruikers en andere Microsoft-services centraal beheren. Deze mogelijkheid vereenvoudigt het beheer van machtigingen en verbetert de beveiliging. Azure Active Directory ondersteunt [Multi-Factor Authentication](sql-database-ssms-mfa-authentication-configure.md) (MFA) voor betere beveiliging van gegevens en toepassingen, en ondersteunt ook een proces voor eenmalige aanmelding (SSO). 

### <a name="authentication"></a>Verificatie 
SQL database-verificatie verwijst naar hoe gebruikers hun identiteit bewijst bij het verbinden met de database. SQL Database ondersteunt twee typen verificatie:  

- SQL-verificatie, waarbij een gebruikersnaam en wachtwoord.
- Azure Active Directory-verificatie, die gebruikmaakt van identiteiten die worden beheerd door Azure Active Directory wordt ondersteund voor beheerde en geïntegreerde domeinen. 

### <a name="authorization"></a>Autorisatie

Autorisatie verwijst naar wat een gebruiker kan doen binnen een Azure SQL Database en wordt beheerd door de databaserollidmaatschappen en objectmachtigingen van uw gebruikersaccount. Beheerd exemplaar heeft dezelfde autorisatiemogelijkheden bedoeld als SQL Server 2017. 

## <a name="database-migration"></a>Databasemigratie 

Beheerd exemplaar doelen gebruikersscenario's met grote databasemigratie vanuit on-premises of IaaS-database-implementaties. Beheerd exemplaar ondersteunt verschillende opties voor de migratie van database: 

### <a name="data-migration-service"></a>Data migratieservice

Azure Database Migration Service is een volledig beheerde service die is ontworpen om in te schakelen naadloze migratie van meerdere databasebronnen naar Azure Data platforms met minimale downtime. Deze service stroomlijnt de vereiste taken voor het verplaatsen van bestaande van derden en SQL Server-databases naar Azure. Implementatieopties zijn onder andere Azure SQL Database Managed Instance en SQL Server in virtuele Azure-machine in openbare Preview. Zie [uw on-premises database migreren naar Managed Instance met behulp van DMS](https://aka.ms/migratetoMIusingDMS). 

### <a name="backup-and-restore"></a>Back-ups en herstellen  

De migratie maakt gebruik van SQL-back-ups naar Azure blob-opslag. Back-ups die zijn opgeslagen in Azure storage-blob kunnen rechtstreeks worden hersteld naar beheerd exemplaar. Als u wilt een bestaande SQL-database herstellen naar een beheerd exemplaar, kunt u het volgende doen:

- Gebruik [Data migratieservice (DMS)](../dms/dms-overview.md). Zie voor een zelfstudie [migreren naar een beheerd exemplaar met behulp van de Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) om terug te zetten vanuit een back-upbestand van de database
- Gebruik de [T-SQL terugzetten opdracht](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql). 
  - Zie voor een zelfstudie waarin wordt getoond hoe om te herstellen van de Wide World Importers - back-upbestand van Standard-database, [herstellen van een back-upbestand naar een beheerd exemplaar](sql-database-managed-instance-restore-from-backup-tutorial.md). In deze zelfstudie leert dat u moet een back-upbestand uploaden naar Azure BLOB-opslag en beveiligd met behulp van een Shared access signature (SAS)-sleutel.
  - Zie voor meer informatie over het herstellen van URL [systeemeigen terugzetten vanuit URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

## <a name="sql-features-supported"></a>SQL-functies die worden ondersteund 

Beheerd exemplaar is erop gericht om te leveren dicht bij surface area van 100% compatibiliteit met on-premises SQL Server die afkomstig zijn in verschillende fasen totdat de algemene beschikbaarheid van de service. Voor een functies en van de vergelijkingslijst, Zie [algemene SQL-functies](sql-database-features.md).
 
Beheerd exemplaar biedt ondersteuning voor achterwaartse compatibiliteit bij SQL 2008-databases. Directe migratie van SQL 2005-database-servers wordt ondersteund, het compatibiliteitsniveau voor gemigreerde SQL 2005-databases worden bijgewerkt naar SQL 2008. 
 
Het volgende diagram geeft een overzicht van de compatibiliteit van gebied in het beheerde exemplaar:  

![Migratie](./media/sql-database-managed-instance/migration.png) 

### <a name="key-differences-between-sql-server-on-premises-and-managed-instance"></a>Belangrijke verschillen tussen on-premises SQL Server en de Managed Instance 

Beheerd exemplaar voordelen wordt altijd-up-to-date in de cloud, betekent dat sommige functies in on-premises SQL Server kunnen een verouderd, buiten gebruik gesteld of alternatieven zijn. Er zijn specifieke gevallen wanneer hulpprogramma's nodig hebt voor het herkennen van dat een bepaalde functie in een iets andere manier werkt of service wordt niet uitgevoerd in een omgeving die u volledig geen zeggenschap: 

- Hoge beschikbaarheid is ingebouwd en vooraf is geconfigureerd. Always On hoge beschikbaarheid-functies worden niet weergegeven in een dezelfde manier als bij SQL IaaS-implementaties 
- Automatische back-ups en punt in tijd herstel. Klanten kan initiëren `copy-only` back-ups die niet leiden tot met automatische back-keten problemen. 
- Beheerd exemplaar is niet toegestaan voor het volledige fysieke paden op te geven, zodat alle bijbehorende scenario's moeten anders worden ondersteund: DB herstellen biedt geen ondersteuning voor het verplaatsen met, DB maken kunnen geen fysieke paden, BULK INSERT werkt met Azure-Blobs alleen, enzovoort. 
- Beheerd exemplaar ondersteunt [Azure AD-verificatie](sql-database-aad-authentication.md) als cloud-alternatief voor het Windows-verificatie. 
- Beheerd exemplaar beheert automatisch de XTP-bestandsgroep en bestanden voor databases met In-Memory OLTP-objecten
- Beheerd exemplaar biedt ondersteuning voor SQL Server Integration Services (SSIS) en host SSIS-catalogus (SSISDB) waarin de SSIS-pakketten kunt, maar ze worden uitgevoerd op een beheerde Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF), Zie [maken Azure-SSIS IR in ADF](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). Als u wilt vergelijken van de SSIS-functies in SQL-Database en de Managed Instance, Zie [SQL-Database vergelijken en Managed Instance (Preview)](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview).

### <a name="managed-instance-administration-features"></a>Beheerfuncties voor beheerd exemplaar  

Beheerd exemplaar inschakelen systeembeheerder om zich te richten op wat belangrijk het meest voor bedrijven is. Veel beheerder/DBA systeemactiviteiten zijn niet vereist, of ze zijn eenvoudig. Bijvoorbeeld, OS / RDBMS-installatie en patching uit handen, dynamische exemplaar vergroten of verkleinen en configuratie, back-ups, database-replicatie (met inbegrip van systeemdatabases), configuratie voor hoge beschikbaarheid en configuratie van de status en prestaties bewaken van gegevens streams. 

> [!IMPORTANT]
> Zie voor een lijst met ondersteunde, gedeeltelijk ondersteunde en niet-ondersteunde functies [SQL Database-functies](sql-database-features.md). Zie voor een lijst van T-SQL-verschillen in beheerde instanties ten opzichte van SQL Server, [exemplaar T-SQL-verschillen beheerd met SQL Server](sql-database-managed-instance-transact-sql-information.md)
 
## <a name="next-steps"></a>Volgende stappen

- Voor een functies en van de vergelijkingslijst, Zie [algemene SQL-functies](sql-database-features.md).
- Lees [Managed Instance VNet Configuration](sql-database-managed-instance-vnet-configuration.md) (VNet-configuratie voor beheerd exemplaar) voor meer informatie over VNet-configuratie.
- Zie voor een zelfstudie die een beheerd exemplaar maakt en wordt een database teruggezet vanuit een back-upbestand [maken van een beheerd exemplaar](sql-database-managed-instance-create-tutorial-portal.md).
- Lees het artikel [Managed Instance migration using DMS](../dms/tutorial-sql-server-to-managed-instance.md) (Migratie van een beheerd exemplaar via DMS) voor een zelfstudie over gebruik van de Azure Database Migration Service (DMS).
- Zie voor informatie over de prijzen [prijzen van SQL Database Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).
