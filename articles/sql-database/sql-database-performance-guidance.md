---
title: Azure SQL Database-prestaties afstemmen richtlijnen | Microsoft Docs
description: Meer informatie over het gebruik van aanbevelingen voor het verbeteren van de prestaties van Azure SQL Database-query's.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: carlrab
ms.openlocfilehash: 630ef13fbd64fac8c2a2a31e4174552e64aaa789
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092644"
---
# <a name="tuning-performance-in-azure-sql-database"></a>Afstemmen van prestaties in Azure SQL Database

Azure SQL Database biedt [aanbevelingen](sql-database-advisor.md) dat u gebruiken kunt om de prestaties van uw database te verbeteren, of u Azure SQL Database kunt [automatisch kan worden aangepast aan uw toepassing](sql-database-automatic-tuning.md) en wijzigingen toepassen die worden de prestaties van uw workload.

U geen toepasselijke aanbevelingen, en u hebt nog steeds problemen met prestaties, u kunt de volgende methoden gebruiken om prestaties te verbeteren:
- Vergroten van de service-lagen in uw [DTU gebaseerde aankoopmodel](sql-database-service-tiers-dtu.md) of uw [vCore gebaseerde aankoopmodel](sql-database-service-tiers-vcore.md) om meer resources met uw database te leveren.
- Uw toepassing af en enkele aanbevolen procedures die u kunnen de prestaties verbeteren van toepassing. 
- De database afstemmen door indexen en query's efficiënter werken met gegevens te wijzigen.

Dit zijn handmatige methodes omdat u nodig hebt om te bepalen van de hoeveelheid resources voldoen aan uw behoeften. Anders moet u voor het herschrijven van de toepassing of databasecode en de wijzigingen implementeren.

## <a name="increasing-performance-tier-of-your-database"></a>Toenemende prestatielaag van uw database

Azure SQL Database biedt twee modellen van aankopen, een [DTU gebaseerde aankoopmodel](sql-database-service-tiers-dtu.md) en een [vCore gebaseerde aankoopmodel](sql-database-service-tiers-vcore.md) die u kunt kiezen uit. Elke servicelaag isoleert alleen de resources die uw SQL-database gebruiken kunt en voorspelbare prestaties voor dat serviceniveau waarborgt. In dit artikel bieden wij richtlijnen waarmee u de servicelaag voor uw toepassing kiezen. We bespreken ook manieren dat u uw toepassing het meeste halen uit Azure SQL Database kunt afstemmen.

> [!NOTE]
> In dit artikel richt zich op informatie over de prestaties voor individuele databases in Azure SQL Database. Zie voor instructies prestaties met betrekking tot elastische pools [prijs- en Prestatieoverwegingen voor elastische pools](sql-database-elastic-pool-guidance.md). Houd er rekening mee, maar u kunt veel van de aanbevelingen voor afstemming in dit artikel zijn van toepassing op databases in een elastische pool en vergelijkbare prestatievoordelen ophalen.
> 

* **Basic**: de Basic-service-laag biedt goede prestaties voorspelbaarheid voor elke database, uur over uur. In een Basic-database ondersteunen voldoende bronnen zijn goede prestaties in een kleine database die geen meerdere gelijktijdige aanvragen. Er zijn typische gebruiksvoorbeelden wanneer u basis-servicelaag gebruiken:
  * **U net aan de slag met Azure SQL Database**. Toepassingen die in ontwikkeling vaak zijn hoeft geen hoge prestaties. Basic-databases zijn een ideale omgeving voor databaseontwikkeling en testen, tegen een lage prijs.
  * **U hebt een database met een enkele gebruiker**. Toepassingen die doorgaans een enkele gebruiker aan een database koppelt hebt geen hoge eisen voor gelijktijdigheid en prestaties. Deze toepassingen zijn kandidaten voor de basis-servicelaag.
* **Standard**: de Standard-servicelaag biedt verbeterde prestaties, voorspelbaarheid en biedt goede prestaties voor databases waarvoor meerdere gelijktijdige aanvragen, zoals werkgroep-en webtoepassingen. Als u een database van de laag Standard service kiest, u kunt het formaat van de databasetoepassing van uw op basis van de voorspelbare prestaties, minuut boven minuut.
  * **Uw database heeft meerdere gelijktijdige aanvragen**. Toepassingen die meer dan één gebruiker op een tijdstip meestal moeten hogere prestatieniveaus. Werkgroep- of web-toepassingen die hebben lage tot gemiddelde i/o-verkeersvereisten voor ondersteuning van meerdere gelijktijdige query's zijn bijvoorbeeld geschikt voor de servicelaag Standard.
* **Premium**: de Premium-servicelaag biedt voorspelbare prestaties, tweede via ten tweede voor elke database Premium en bedrijfskritiek. Als u de Premium-servicelaag kiest, kunt u het formaat van de databasetoepassing van uw op basis van de piekbelasting voor die database. Het plan wordt verwijderd gevallen in welke prestaties afwijking kleine query's langer duren dan verwacht in latentiegevoelige bewerkingen kan veroorzaken. Dit model kunt vereenvoudigen de ontwikkelings- en product validatie-cycli voor toepassingen die moeten sterke verklaringen over piek resource nodig heeft, afwijking van prestaties of latentie van query. De meeste Premium-laag service gebruiksvoorbeelden hebben een of meer van deze kenmerken:
  * **Hoge piekbelasting**. Een toepassing die moet ingrijpende CPU, geheugen of input/output (I/O) om de bewerkingen te voltooien is een speciale, hoge prestaties vereist. Een databasebewerking bekend is bij het gebruiken van verschillende CPU-kernen voor een langere periode is bijvoorbeeld een kandidaat voor de Premium-servicelaag.
  * **Veel gelijktijdige aanvragen**. Sommige databasetoepassingen service veel gelijktijdige aanvragen, bijvoorbeeld bij het ophalen van een website die een volume met veel verkeer heeft. Basic en Standard-Servicelagen Beperk het aantal gelijktijdige aanvragen per database. Toepassingen waarvoor meer verbindingen moet een juiste reserveringsgrootte voor het afhandelen van het maximum aantal aanvragen voor benodigde kiezen.
  * **Lage latentie**. Sommige toepassingen moeten garanderen dat een reactie van de database in een minimale tijd. Als een specifieke opgeslagen procedure wordt aangeroepen als onderdeel van een bredere klant-bewerking, wellicht u een vereiste om een retour-aanroep die in 99 procent van de tijd niet meer dan 20 milliseconden. Dit type toepassing profiteren van de Premium-servicelaag, om ervoor te zorgen dat de vereiste rekenkracht beschikbaar is.

Het serviceniveau dat u nodig hebt voor uw SQL-database, is afhankelijk van de vereisten voor het laden van piek voor elke resourcedimensie. Sommige toepassingen een trivial bedrag van één resource gebruiken, maar belangrijke vereisten voor andere resources.

### <a name="service-tier-capabilities-and-limits"></a>Service tier mogelijkheden en beperkingen

Op elke servicelaag instellen u het prestatieniveau, zodat u de flexibiliteit om te betalen alleen voor de capaciteit die u nodig hebt. U kunt [capaciteit aanpassen](sql-database-service-tiers-dtu.md), omhoog of omlaag, als de workload wordt gewijzigd. Als de werkbelasting van uw database tijdens de back-naar-school winkelwagen seizoen hoog is, kunt u het prestatieniveau voor de database verhogen voor een bepaalde tijd, juli tot en met September. U kunt deze beperken wanneer uw seizoen piek is beëindigd. U betaalt door het optimaliseren van uw cloudomgeving de periodieke variatie van uw bedrijf, kunt u minimaliseren. Dit model werkt ook goed voor introductiecycli voor software-product. Een testteam kan capaciteit toewijzen tijdens deze wordt uitgevoerd test en laat u deze capaciteit wanneer ze klaar bent met testen. In een model van de aanvraag capaciteit betaalt u voor capaciteit als u dit nodig hebt, en besparen op specifieke resources die u zelden gebruikt.

### <a name="why-service-tiers"></a>Waarom Servicelagen?
Hoewel elke databaseworkload van een verschillen kan, is het doel van service-lagen voor voorspelbare prestaties op verschillende prestatieniveaus. Klanten met grootschalige database resourcevereisten kunnen werken in een meer specifieke omgeving.

## <a name="tune-your-application"></a>Uw toepassing af te stemmen
In traditionele on-premises SQL Server, is het proces van het eerste capaciteitsplanning vaak gescheiden van het proces van het uitvoeren van een toepassing in productie. Hardware- en productinformatie licenties eerst worden aangeschaft en het afstemmen van prestaties later wordt uitgevoerd. Wanneer u Azure SQL Database gebruikt, is het een goed idee om het proces van het uitvoeren van een toepassing en het afstemmen interweave. Met het model van betalen voor capaciteit op aanvraag, kunt u uw toepassing voor het gebruik van de minimaal benodigde nu, in plaats van overmatige inrichting op hardware gebaseerd op goed van toekomstige groeiplannen voor een toepassing, die vaak onjuist zijn bronnen afstemmen. Sommige klanten kunnen ervoor kiezen niet om af te stemmen van een toepassing, maar in plaats daarvan hardware van resources. Deze aanpak is wellicht een goed idee als u niet wilt wijzigen van een sleutel toepassing gedurende een periode van bezet. Maar afstemmen van een toepassing kunt minimaliseren resourcevereisten en maandelijkse rekeningen te verlagen wanneer u de service-lagen in Azure SQL Database.

### <a name="application-characteristics"></a>Kenmerken van toepassingen
Hoewel Azure SQL Database-Servicelagen zijn ontworpen om de stabiliteit van de prestaties en voorspelbaarheid voor een toepassing te verbeteren, kunt enkele aanbevolen procedures u uw toepassing om beter te profiteren van de resources op een prestatieniveau af te stemmen. Hoewel veel toepassingen aanzienlijke prestatievoordelen hebben gewoon door over te schakelen naar een hoger prestatieniveau of servicelaag, sommige toepassingen nodig verdere afstemming om te profiteren van een hoger niveau van de service. Voor betere prestaties kunt u eventueel extra toepassing afstemmen voor toepassingen die deze kenmerken hebben:

* **Toepassingen met trage prestaties vanwege "intensieve" gedrag**. Intensieve toepassingen maken overmatige bewerkingen voor gegevenstoegang die gevoelig voor netwerklatentie. Mogelijk moet u dit soort toepassingen om te verminderen van het aantal bewerkingen voor gegevenstoegang met de SQL-database wijzigen. Bijvoorbeeld, u mogelijk de toepassingsprestaties verbeteren door met behulp van technieken zoals batchverwerking van ad-hocquery's of het verplaatsen van de query's opgeslagen procedures. Zie voor meer informatie, [Batch-query's](#batch-queries).
* **Databases met een intensieve workload die niet kan worden ondersteund door een volledige één machine**. Databases die groter zijn dan de resources van het hoogste niveau van de Premium-prestaties mogelijk profiteren van de workload uitschalen. Zie voor meer informatie, [Cross-database sharding](#cross-database-sharding) en [functionele partitionering](#functional-partitioning).
* **Toepassingen waarvoor suboptimale query's**. Toepassingen, met name degenen die in de laag voor gegevenstoegang, die zijn slecht afgestemd op query's mogelijk niet van profiteren van een hoger prestatieniveau. Dit geldt ook voor query's die niet over een WHERE-component, ontbrekende indexen of verouderde statistieken. Deze toepassingen profiteren van de standaardoperators voor query-prestaties optimaliseren technieken. Zie voor meer informatie, [ontbrekende indexen](#identifying-and-adding-missing-indexes) en [Query afstemmen en coderingstips](#query-tuning-and-hinting).
* **Toepassingen die suboptimale gegevens hebben toegang tot ontwerp**. Toepassingen die inherent gegevens toegang tot problemen met gelijktijdigheid, bijvoorbeeld deadlocking, hebben mogelijk niet profiteren van een hoger prestatieniveau. Verklein retouren op basis van de Azure SQL Database door opslaan in cache op de client met de service Azure Caching of een andere caching-technologie. Zie [toepassing laag caching](#application-tier-caching).

## <a name="tune-your-database"></a>Uw database af te stemmen
In deze sectie kijken we enkele technieken die u gebruiken kunt om af te stemmen, Azure SQL-Database voor de beste prestaties voor uw toepassing en voer deze uit op het laagste niveau van de mogelijke prestaties. Sommige van de volgende manieren overeenkomen met traditionele SQL Server best practices voor afstemmen, maar andere zijn specifiek voor Azure SQL Database. In sommige gevallen kunt u de verbruikte resources voor een database om te zoeken gebieden verder afstemmen en uitbreiden van traditionele SQL Server-technieken om te werken in Azure SQL Database controleren.

### <a name="identify-performance-issues-using-azure-portal"></a>Identificeren van prestatieproblemen met Azure portal
De volgende hulpprogramma's in Azure portal kunt u analyseren en oplossen van prestatieproblemen met uw SQL-database:

* [Inzicht in queryprestaties](sql-database-query-performance.md)
* [SQL Database Advisor](sql-database-advisor.md)

De Azure-portal bevat meer informatie over beide van deze hulpprogramma's en het gebruik ervan. Voor het efficiënt opsporen en corrigeren van problemen, is het raadzaam dat u de hulpprogramma's in Azure portal eerst. U wordt aangeraden dat u de handmatige benaderingen die we vervolgens bespreken om ontbrekende indexen en query afstemmen, in bijzondere gevallen afstemmen.

Meer informatie over het identificeren van problemen in Azure SQL Database op [prestatiebewaking](sql-database-single-database-monitor.md) artikel.

### <a name="identifying-and-adding-missing-indexes"></a>Identificeren en toe te voegen ontbrekende indexen
Er is een veelvoorkomend probleem in de prestaties van OLTP-database gekoppeld aan het ontwerp van de fysieke database. Databaseschema's zijn vaak ontworpen en zonder te testen op schaal (hetzij in de belasting of in gegevensvolume) verzonden. De prestaties van een queryplan kan helaas worden geaccepteerd op kleine schaal maar aanzienlijk verslechteren onder gegevensvolumes voor productie-niveau. De meest voorkomende oorzaak van dit probleem is het ontbreken van de juiste indexen om te voldoen aan de filters of andere beperkingen in een query. Ontbrekende indexen manifesten als een tabel worden vaak gescand als kan worden volstaan met een index zoeken.

In dit voorbeeld gebruikt het geselecteerde queryplan een scan bij een zoeken zou voldoende:

    DROP TABLE dbo.missingindex;
    CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
    SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;

![Een queryplan met de ontbrekende indexen](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database kunt u zoeken naar en fix algemene ontbrekende index-voorwaarden. DMV's die zijn ingebouwd in Azure SQL Database bekijken querycompilaties waarbij een index aanzienlijk sneller de geschatte kosten voor het uitvoeren van een query. SQL-Database tijdens de uitvoering van de query, houdt bij of hoe vaak elke queryplan wordt uitgevoerd en houdt de geschatte kloof tussen het queryplan wordt uitgevoerd en de imagined waar die index bestaat. Kunt u deze DMV's te snel bedenken welke wijzigingen in het ontwerp van de fysieke database totale kosten van de werkbelasting voor een database en de werkelijke werkbelasting kunnen verbeteren.

U kunt deze query gebruiken om te evalueren mogelijke ontbrekende indexen:

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

In dit voorbeeld wordt de query heeft geleid tot deze suggesties:

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Nadat deze gemaakt, kiest die dezelfde SELECT-instructie een ander abonnement, die gebruikmaakt van een zoeken in plaats van een scan, en vervolgens het plan efficiënter wordt uitgevoerd:

![Queryplan met de gecorrigeerde indexen](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

Het belangrijkste inzicht is dat de i/o-capaciteit van een gedeelde, basisservices systeem beperkter dan die van een speciale server-machine. Er is een premium op het minimaliseren van onnodige i/o om te profiteren maximale van het systeem in de DTU van elk prestatieniveau van de Azure SQL Database-Servicelagen. Ontwerp van de juiste fysieke database opties kunnen u de latentie voor afzonderlijke query's, aanzienlijk verbeteren verbetering van de doorvoer van gelijktijdige aanvragen per schaaleenheid verwerkt en Minimaliseer de kosten die zijn vereist om te voldoen aan de query. Zie voor meer informatie over de ontbrekende index DMV's [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Query afstemmen en coderingstips
Het queryoptimalisatieprogramma in Azure SQL Database is vergelijkbaar met de traditionele SQL Server-queryoptimalisatie. De meeste van de aanbevolen procedures voor het afstemmen van query's en informatie over de redenering model beperkingen voor het optimaliseren van de query ook van toepassing op Azure SQL Database. Als u query's in Azure SQL Database afstemmen, krijgt u mogelijk het bijkomende voordeel cumulatieve resourcebehoeften te beperken. Het is mogelijk dat uw toepassing kunnen worden uitgevoerd tegen een lagere kosten dan een ruiseffect equivalent, omdat het kan worden uitgevoerd op een lager prestatieniveau.

Een voorbeeld dat is gebruikelijk in SQL Server en dat geldt ook voor Azure SQL Database is hoe het queryoptimalisatieprogramma "bithandtekeningen' parameters. Tijdens de compilatie evalueert het queryoptimalisatieprogramma de huidige waarde van een parameter om te bepalen of er een meer optimale queryplan kunt genereren. Hoewel deze strategie vaak tot een queryplan die zijn aanzienlijk sneller dan een plan zonder bekende parameterwaarden gecompileerd leiden kan, momenteel werkt dat zowel in SQL Server en Azure SQL Database. Soms is de parameter niet sniff, en soms de parameter is sniff, maar de gegenereerde plan suboptimale voor de volledige set parameterwaarden in een werkbelasting. Microsoft levert queryhints (richtlijnen) zodat u kunt meer doelbewust doel opgeven en het standaardgedrag van de parameter bekijken negeren. Als u hints gebruikt, kunt u vaak gevallen waarin het standaardgedrag voor SQL Server of Azure SQL Database voor een specifieke klant werkbelasting gebrekkige is oplossen.

Het volgende voorbeeld ziet u hoe de queryprocessor een abonnement dat suboptimale voor prestaties en resourcevereisten kunt genereren. Dit voorbeeld laat ook zien als u een queryhint gebruikt, u vereisten voor uitvoering van de query veel tijd en middelen voor uw SQL-database verkleinen kunt:

    DROP TABLE psptest1;
    CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO psptest1(col2) values (1);
        INSERT INTO psptest1(col2) values (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION
    CREATE INDEX i1 on psptest1(col2);
    GO

    CREATE PROCEDURE psp1 (@param1 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1
        WHERE col2 = @param1
        ORDER BY col2;
    END
    GO

    CREATE PROCEDURE psp2 (@param2 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
        ORDER BY col2
        OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
    END
    GO

    CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
    GO

De setup-code maakt een tabel die Gegevensdistributie is vervormd. Het optimale queryplan verschilt afhankelijk van welke parameter is geselecteerd. Helaas compileren niet het plan cachegedrag altijd de query op basis van de waarde van de meest voorkomende. Het is dus mogelijk dat een suboptimale wilt worden in de cache opgeslagen en gebruikt voor veel waarden, zelfs als een ander abonnement mogelijk een betere keuze voor de planning op gemiddelde. Het queryplan maakt vervolgens twee opgeslagen procedures die identiek zijn, behalve dat een een speciale queryhint heeft.

**Bijvoorbeeld: deel 1**

    -- Prime Procedure Cache with scan plan
    EXEC psp1 @param1=1;
    TRUNCATE TABLE t1;

    -- Iterate multiple times to show the performance difference
    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp1 @param1=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

**Bijvoorbeeld: deel 2**

(Aangeraden dat u ten minste tien minuten voordat u begint met deel 2 van het voorbeeld wachten zodat de resultaten afzonderlijke in de resulterende telemetriegegevens zijn.)

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Elk onderdeel van dit voorbeeld probeert uit te voeren van een geparameteriseerde insert-instructie 1000 keer (voor het genereren van een voldoende laden om te gebruiken als een test-gegevensset). Bij het uitvoeren van opgeslagen procedures, onderzoekt de queryprocessor de waarde van de parameter die wordt doorgegeven aan de procedure tijdens de eerste compilatie (parameter ' bekijken'). De processor de resulterende plan in de cache opslaat en gebruikt deze voor later aanroepen, zelfs als de waarde van parameter verschilt. De optimale planning kan niet worden gebruikt in alle gevallen. Soms moet u voor de optimalisatie voor het kiezen van een plan dat is het beter voor het gemiddelde geval in plaats van het specifieke geval uit wanneer de query voor het eerst is gecompileerd. In dit voorbeeld wordt het eerste abonnement gegenereerd een 'scan'-plan dat alle rijen om elke waarde die overeenkomt met de parameter leest:

![Query afstemmen met behulp van een scan plannen](./media/sql-database-performance-guidance/query_tuning_1.png)

Omdat we de procedure hebt uitgevoerd met behulp van de waarde 1, wordt het resulterende plan optimaal is voor de waarde 1 is maar suboptimale voor alle andere waarden in de tabel is. Het resultaat waarschijnlijk niet wat u wilt als u elk abonnement willekeurig, kiezen omdat het abonnement wordt langzamer uitgevoerd en maakt gebruik van andere bronnen.

Als u de test met `SET STATISTICS IO` ingesteld op `ON`, de logische scanfragmentatie werk in het volgende voorbeeld wordt uitgevoerd op de achtergrond. U kunt zien dat er 1,148 leesbewerkingen gedaan door het plan (dit is inefficiënt als de gemiddelde case is slechts één rij geretourneerd):

![Query's uitvoeren met behulp van een logische scanfragmentatie afstemmen](./media/sql-database-performance-guidance/query_tuning_2.png)

Het tweede gedeelte van het voorbeeld maakt gebruik van een queryhint vertellen dat het optimalisatieprogramma gebruik van een specifieke waarde tijdens de compilatie. In dit geval het zorgt ervoor dat de queryprocessor de waarde die wordt doorgegeven als parameter, negeert en in plaats daarvan om aan te nemen `UNKNOWN`. Dit verwijst naar een waarde die de gemiddelde frequentie in de tabel is (negeert scheeftrekken). De resulterende plan is een plan voor zoeken op basis van die is sneller en gebruikt minder bronnen, Gemiddeld, dan het abonnement in deel 1 van dit voorbeeld:

![Query afstemmen met behulp van een queryhint](./media/sql-database-performance-guidance/query_tuning_3.png)

Ziet u het effect in de **sys.resource_stats** tabel (Er is een vertraging vanaf het moment dat u de test en wanneer de gegevens in de tabel gevuld uitvoeren). Voor dit voorbeeld, deel 1 uitgevoerd tijdens de periode 22:25:00 uur, en deel 2 om 22:35:00 uur uitgevoerd. De eerdere tijdvenster gebruikt meer resources in dat tijdvenster dan het nieuwere patch (vanwege plan efficiëntieverbeteringen).

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Van voorbeeldresultaten afstemmen van query 's](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Hoewel het volume in dit voorbeeld opzettelijk klein is, kan het effect van suboptimale parameters aanzienlijke, met name voor grotere databases worden. Het verschil in uitzonderlijke gevallen kan liggen tussen seconden voor snelle aanvragen en -uren voor trage aanvragen.
> 
> 

U kunt onderzoeken **sys.resource_stats** om te bepalen of de resource voor een test maakt gebruik van meer of minder resources dan een andere test. Als u gegevens, het tijdstip van de tests scheiden zodat ze zich niet in hetzelfde 5 minuten venster in de **sys.resource_stats** weergeven. Het doel van de oefening is om te beperken van de totale hoeveelheid resources die worden gebruikt en niet om de piek-resources. Over het algemeen minder een stukje code voor latentie optimaliseren ook gebruik van resources. Zorg ervoor dat de wijzigingen die u in een toepassing aanbrengt nodig zijn en de wijzigingen niet negatieve invloed op de gebruikerservaring voor iemand die mogelijk queryhints worden gebruikt in de toepassing.

Als een werkbelasting een set heeft van herhaalde query's, verstandig vaak het om te leggen en valideren van de optimalisatie van uw keuzes plan omdat deze de minimale resource grootte eenheid die is vereist voor het hosten van de database-stations. Nadat u het hebt gevalideerd, soms de abonnementen kunt u ervoor zorgen dat ze hebben niet is gedegradeerd te klikken. Vindt u meer informatie over [hints (Transact-SQL) query](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Cross-database sharding
Omdat Azure SQL Database wordt uitgevoerd op basishardware, zijn de capaciteitslimieten voor een individuele database lager is dan voor de installatie van een traditionele on-premises SQL Server. Sommige klanten gebruik sharding technieken databasebewerkingen verspreiden over meerdere databases wanneer de bewerkingen niet binnen de grenzen van een individuele database in Azure SQL Database passen. De meeste klanten die gebruikmaken van sharding-technieken in Azure SQL Database splitsen hun gegevens op één dimensie met meerdere databases. Voor deze benadering moet u begrijpen dat OLTP-toepassingen vaak transacties die van toepassing naar slechts één rij of rijen in het schema een kleine groep zijn uitvoeren.

> [!NOTE]
> SQL Database biedt nu een bibliotheek met sharding. Zie voor meer informatie, [overzicht client-bibliotheek elastische Database](sql-database-elastic-database-client-library.md).
> 
> 

Bijvoorbeeld als een database heeft de naam van de klant, volgorde en de details van de order (zoals de traditionele voorbeeld Northwind-database die wordt geleverd met SQL Server), u kunt deze gegevens splitsen in meerdere databases door te groeperen van een klant met de gerelateerde volgorde en de details van de volgorde informatie. U kunt garanderen dat de gegevens van de klant in een individuele database blijft. De toepassing zou verschillende klanten verdeeld over databases, effectief de belasting te spreiden over meerdere databases. Met sharding van klanten niet alleen de maximale limiet databasegrootte kunnen voorkomen, maar Azure SQL Database ook werkbelastingen die aanzienlijk groter dan de grenzen van de verschillende prestatieniveaus zijn, zolang elke afzonderlijke database de DTU past kan verwerken.

Database sharding niet de cumulatieve resourcecapaciteit voor een oplossing beperken, is het zeer effectief zijn bij het ondersteunen van zeer grote oplossingen die zijn verdeeld over meerdere databases. Elke database kunt uitvoeren op een ander prestatieniveau voor de ondersteuning van zeer grote "effectieve" databases met hoge benodigde middelen.

### <a name="functional-partitioning"></a>Functionele partitionering
SQL Server-gebruikers combineren vaak veel functies in een individuele database. Bijvoorbeeld, als een toepassing logica heeft voor het beheren van de inventaris voor een store, mogelijk dat de database logica die is gekoppeld aan de inventaris, bijhouden van inkooporders en opgeslagen procedures en geïndexeerde of gerealiseerde weergaven die het einde van maand rapportage beheren. Deze techniek wordt het gemakkelijker te beheren van de database voor bewerkingen zoals back-up, maar ook moet u om de grootte van de hardware voor het afhandelen van de piekbelasting over alle functies van een toepassing.

Als u een uitbreidbare architectuur in Azure SQL Database gebruikt, is het een goed idee om op te splitsen verschillende functies van een toepassing in verschillende databases. Met deze techniek kunnen schaalt elke toepassing onafhankelijk van elkaar. Als een toepassing drukker wordt (en de belasting van de database wordt verhoogd), kan de beheerder onafhankelijk van de prestatieniveaus voor elke functie kiezen in de toepassing. Op de limiet, met deze architectuur kan een toepassing worden groter is dan een machine één product kan worden verwerkt omdat de belasting is verdeeld over meerdere virtuele machines.

### <a name="batch-queries"></a>Batch-query 's
Voor toepassingen die toegang gegevens tot met behulp van grote hoeveelheden, vaak, ad hoc uitvoeren van query's, een aanzienlijke hoeveelheid reactietijd is besteed aan netwerkcommunicatie tussen de toepassingslaag en de Azure SQL Database-laag. Zelfs wanneer de toepassing en de Azure SQL Database zich in hetzelfde Datacenter, kan de netwerklatentie tussen de twee worden vergroot door een groot aantal bewerkingen voor gegevenstoegang. Om te beperken van het netwerk round trips voor de bewerkingen voor gegevenstoegang, kunt u met de optie een batch-ad-hocquery's, of als opgeslagen procedures worden gecompileerd. Als u de ad-hocquery's batch, kunt u meerdere query's verzenden als één grote batch in een enkele reis naar Azure SQL Database. Als u ad-hocquery's in een opgeslagen procedure compileren, kan u hetzelfde resultaat bereiken als u deze batch. Met behulp van een opgeslagen procedure biedt u ook het voordeel van de kans van de query-abonnementen in Azure SQL Database in het cachegeheugen, zodat u de opgeslagen procedure opnieuw kunt toeneemt.

Sommige toepassingen zijn schrijven-intensieve. U kunt de totale i/o-belasting van een database soms verminderen door rekening te houden hoe u batch schrijfbewerkingen. Vaak is dit net zo eenvoudig als met expliciete transacties in plaats van automatisch doorvoeren van transacties in opgeslagen procedures en ad-hoc batches. Zie voor een evaluatie van verschillende technieken die u kunt gebruiken, [batchverwerking technieken voor toepassingen in Azure SQL-Database](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Experimenteer met uw eigen werkbelasting op het juiste model voor batchverwerking vinden. Moet u om te begrijpen dat een model mogelijk enigszins transactionele consistentie gegarandeerd. Zoeken naar de juiste werkbelasting die wordt geminimaliseerd resource gebruiken, moet zoeken naar de juiste combinatie van consistentie en prestaties wisselwerking.

### <a name="application-tier-caching"></a>Toepassingslaag caching
Sommige databasetoepassingen hebt leesintensief workloads. Caching lagen mogelijk de belasting van de database te verminderen en het prestatieniveau dat is vereist ter ondersteuning van een database met behulp van Azure SQL Database kan mogelijk worden verlaagd. Met [Azure Redis Cache](https://azure.microsoft.com/services/cache/), als u een werkbelasting leesintensief hebt, kunt u de gegevens eenmaal lezen (of bijvoorbeeld één keer per toepassingslaag machine, afhankelijk van hoe deze is geconfigureerd), en dat gegevens buiten uw SQL-database op te slaan. Dit is een manier om te beperken van de belasting van de database (CPU en i/o-lezen), maar er is van invloed op transactionele consistentie, omdat de gegevens worden gelezen uit de cache mogelijk niet gesynchroniseerd met de gegevens in de database. Hoewel in veel toepassingen bepaalde mate van inconsistentie acceptabel is, is dat niet geldt voor alle werkbelastingen. U moet de vereisten van elke toepassing volledig begrijpen voordat u een cachingstrategie toepassingslaag implementeert.

## <a name="next-steps"></a>Volgende stappen
* Zie voor meer informatie over Servicelagen op basis van DTU [DTU gebaseerde aankoopmodel](sql-database-service-tiers-dtu.md).
* Zie voor meer informatie over Servicelagen op vCore gebaseerde [vCore gebaseerde aankoopmodel](sql-database-service-tiers-vcore.md).
* Zie voor meer informatie over elastische pools [wat is een elastische pool van Azure?](sql-database-elastic-pool.md)
* Zie voor meer informatie over prestaties en elastische pools [wanneer rekening houden met een elastische pool](sql-database-elastic-pool-guidance.md)

