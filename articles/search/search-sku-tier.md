---
title: Kies een prijscategorie laag of SKU voor Azure Search-service | Microsoft Docs
description: "Azure Search kan worden ingericht op deze SKU's: gratis, Basic en Standard, waar Standard is beschikbaar in verschillende resourceconfiguraties en capaciteit niveaus."
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: heidist
ms.openlocfilehash: f7cf471a69395cef0aef7d5dd2e3c77218bf97a3
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715277"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Kies een prijscategorie voor Azure Search

In Azure Search, een [service is ingericht](search-create-service-portal.md) op een specifieke prijscategorie of SKU. Opties zijn onder andere **gratis**, **Basic**, of **Standard**, waarbij **Standard** is beschikbaar in meerdere configuraties en capaciteit. 

Het doel van dit artikel is kunt u een categorie kiezen. Vormt een aanvulling op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/search/) en [Servicelimieten](search-limits-quotas-capacity.md) pagina met een samenvatting van de facturering concepten en gebruiken op een manier die is gekoppeld aan verschillende lagen. Het is ook raadt aan om een herhaalbare aanpak om te begrijpen welke laag beste voldoet aan uw behoeften. 

Lagen bepalen capaciteit, niet functies. Als de capaciteit van een laag blijkt te laag zijn, moet u voor het inrichten van een nieuwe service van het hogere niveau en vervolgens [uw indexen opnieuw laden](search-howto-reindex.md). Er is geen in-place upgrade van de dezelfde service van een SKU naar een andere.

Beschikbaarheid van functies is geen primaire laag overweging. Alle lagen, met inbegrip van de **gratis** laag, functiepariteit, met uitzondering van de indexeerfunctie ondersteuning voor S3HD bieden. Echter kunnen beperkingen voor indexering en resource effectief beperken de omvang van het gebruik van functies. Bijvoorbeeld, [cognitief zoeken](cognitive-search-concept-intro.md) indexeren is langlopende vaardigheden die time-out voor een gratis service, tenzij de gegevensset te zijn uitgerust met zeer kleine gebeurt.

> [!TIP]
> De meeste klanten beginnen met de **gratis** -laag voor evaluatie en vervolgens omgezet naar **Standard** voor ontwikkeling. Nadat u een laag kiezen en [inrichten van een service voor zoeken](search-create-service-portal.md), kunt u [verhogen aantal replica en partitie](search-capacity-planning.md) voor het afstemmen van prestaties. Zie voor meer informatie over wanneer en waarom zou u capaciteit aanpassen, [aandachtspunten voor prestaties en optimalisatie](search-performance-optimization.md).
>

## <a name="billing-concepts"></a>Facturering-concepten

Concepten die u wilt meer informatie over voor de geselecteerde laag bevatten definities van de capaciteit, Servicelimieten en service-eenheden. 

### <a name="capacity"></a>Capaciteit

Capaciteit is gestructureerd als *replica's* en *partities*. 

+ Replica's zijn exemplaren van de search service, waarbij elke replica als host fungeert voor een gelijke kopie van een index. Een service met 6 replica's heeft bijvoorbeeld 6 kopieën van elke index geladen in de service. 

+ Partities indexen opslaan en doorzoekbare gegevens automatisch splitsen: twee partities uw index in de helft, drie partities splitsen in derde, enzovoort. Wat betreft de capaciteit, *partitie grootte* is van de primaire functie van de onderscheidende voor lagen.

> [!NOTE]
> Alle **Standard** lagen ondersteuning [flexibele combinaties replica en partities](search-capacity-planning.md#chart) zodat u kunt [gewicht van uw systeem voor snelheid of opslag](search-performance-optimization.md) door het veranderen van de balans. **Basic** biedt u drie replica's voor hoge beschikbaarheid, maar slechts één partitie heeft. **Gratis** lagen bieden geen toegewezen resources: computing resources worden gedeeld door meerdere gratis services.

### <a name="search-units"></a>Zoekeenheden

Het belangrijkste facturering concept om te begrijpen is een *zoekeenheid* (SU), is de Factureringseenheid voor Azure Search. Omdat Azure Search, hangt af van de replica's en partities van functie, verstandig niet het om aan te brengen door een of andere kosten in rekening. In plaats daarvan is facturering gebaseerd op een samenstelling van beide. Formulaically, is van het product van de replica en partities die worden gebruikt door een service in een SU: (X-P R = SU). Ten minste elke service wordt gestart met 1 SU (één replica wordt vermenigvuldigd met één partitie), maar een realistischer model mogelijk nog een 3-replica, 3-partitie-service in rekening gebracht als 9 su's. 

Hoewel elke laag geleidelijk hogere capaciteit biedt, kunt u een gedeelte van de totale capaciteit online brengen met de rest in reserve aan te geven. Wat betreft facturering is het aantal partities en replica's die u online, berekend met behulp van de formule SU, waarmee wordt bepaald wat u daadwerkelijk betaalt brengt.

Frequentie van de facturering is per uur per Streamingeenheid, waarbij elke laag met een ander tarief. Tarieven voor elke laag kan worden gevonden op [prijsinformatie](https://azure.microsoft.com/pricing/details/search/).

### <a name="limits"></a>Limieten

Hostresources, zoals indexen en indexeerfuncties-Services. Elke laag legt [Servicelimieten](search-limits-quotas-capacity.md) op de hoeveelheid resources die u kunt maken. Hierdoor is een bovengrens voor het aantal indexen (en andere objecten) de tweede onderscheidende functie voor lagen. Houd er rekening mee de beperkingen voor aantal indexen tijdens het bekijken van elke optie in de portal. Andere resources, zoals Indexeerfuncties en gegevensbronnen kennis en vaardigheden, wordt intensief gebruikt met index limieten.

## <a name="consumption-patterns"></a>Gebruikspatronen voor databasebronnen

De meeste klanten beginnen met de **gratis** service, die ze voor onbepaalde tijd houden, en kies vervolgens een van de **Standard** lagen voor ernstige ontwikkeling of productie workloads. 

![Azure search-lagen](./media/search-sku-tier/tiers.png "Azure search Prijscategorieën")

Op beide uiteinden **Basic** en **S3 HD** bestaan voor belangrijke, maar ongewone verbruik patronen. **Basic** is bedoeld voor kleine productieworkloads: SLA, biedt bronnen, hoge beschikbaarheid, maar matig opslag, toegenomen van 2 GB totaal toegewezen. Deze laag is ontworpen voor klanten die consistent onder gebruikte beschikbare capaciteit. Aan het einde, **S3 HD** is voor werkbelastingen karakteristiek voor ISV's, partners, [multitenant oplossingen](search-modeling-multitenant-saas-applications.md), of geen configuratie voor een groot aantal kleine indexen. Het is vaak voor de hand liggende wanneer **Basic** of **S3 HD** laag is geschikt, maar als u wilt bevestigen dat u kunt boeken [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) of [Neem contact op met Azure Ondersteuning voor](https://azure.microsoft.com/support/options/) voor verdere richtlijnen.

Focus verplaatsen naar de meest gangbare standaardlagen, **S1-S3** voortgang met het toenemende aantal niveaus van capaciteit, met belangrijk punten op de grootte van partitie- en maximumwaarden voor aantallen indexen, Indexeerfuncties en corollary resources zijn:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partitiegrootte|  25 GB | 100 GB | 250 GB |  |  |  |  |
| limieten voor index en indexeerfunctie| 50 | 200 | 200 |  |  |  |  |

**S1** is een algemene keuze wanneer toegewezen resources en meerdere partities noodzakelijk worden. Met partities van 25 GB voor maximaal 12 partities, de per-service beperken op **S1** is 300 GB totaal als u partities via de replica's maximaliseren (Zie [partities en replica's toewijzen](search-capacity-planning.md#chart) voor meer met gelijke taakverdeling samenstelling.)

Portal- en prijsmodel pagina's plaatsen de focus op de grootte van partitie en opslag, maar voor elke laag alle mogelijkheden (schijfcapaciteit, de snelheid van CPU's) compute lineair groeien met de prijs. Een **S2** replica is sneller dan **S1**, en **S3** sneller dan **S2**. **S3** lagen opsplitsen in het algemeen lineaire compute-prijzen-patroon met onevenredig sneller i/o's. Als u verwacht de i/o knelpunt dat, een **S3** biedt u veel meer IOPS dan lagere niveaus.

**S3** en **S3 HD** worden ondersteund door identieke hoge capaciteit infrastructuur, maar elk een de maximumlimiet is bereikt op verschillende manieren. **S3** is gericht op een kleiner aantal zeer grote indexen. Hierdoor de maximumlimiet is afhankelijk van de resource (2,4 TB voor elke service). **S3 HD** is gericht op een groot aantal kleine indexen. 1000 indexen **S3 HD** uiterste te belasten in de vorm van de indexbeperkingen is bereikt. Als u een **S3 HD** klant die is vereist meer dan 1000 indexen, neem contact op met Microsoft Support voor meer informatie over om door te gaan.

> [!NOTE]
> Voorheen, document limieten overweging zijn, maar zijn niet langer van toepassing voor de meeste Azure Search-services ingericht na januari 2018. Zie voor meer informatie over de voorwaarden die nog steeds document limieten van toepassing, [Servicelimieten: document limieten](search-limits-quotas-capacity.md#document-limits).
>

## <a name="evaluate-capacity"></a>Capaciteit evalueren

Capaciteit en kosten van het uitvoeren van de service gaan hand in hand. Lagen leggen beperkingen met betrekking tot twee niveaus (opslag en bronnen), dus u met beide doen moet omdat het een eerst is bereikt de effectieve limiet is. 

Bedrijfsvereisten voorschrijven doorgaans het aantal indexen, u moet. Bijvoorbeeld, globale index voor een grote opslagruimte voor documenten, of misschien meerdere indexen op basis van regio, toepassing of nichemarkten business.

Om te bepalen van de grootte van een index, die u hebt tot [bouwen een](search-create-index-portal.md). De gegevensstructuur van de in Azure Search is voornamelijk een [omgekeerde index](https://en.wikipedia.org/wiki/Inverted_index), heeft andere eigenschappen dan de brongegevens. Grootte en complexiteit voor een omgekeerde index worden bepaald door de inhoud, niet per se de hoeveelheid gegevens die u in het kanaal. Een bron van grote hoeveelheden gegevens met zeer grote redundantie kan leiden tot een kleinere-index dan een kleinere gegevensset die zeer variabel inhoud bevat.  Het is daarom zelden mogelijk afleiden van de indexgrootte van de is gebaseerd op de grootte van de oorspronkelijke gegevensset.

### <a name="step-1-develop-rough-estimates-using-the-free-tier"></a>Stap 1: Maakt een ruwe schatting met behulp van de gratis laag ontwikkelen

Een mogelijke aanpak voor het schatten van de capaciteit is te beginnen met de **gratis** laag. Intrekken die de **gratis** service biedt maximaal 3 indexen, 50 MB opslag, en 2 minuten tijd worden geïndexeerd. Het kan lastig zijn om in te schatten van de indexgrootte van een verwachte met deze beperkingen, maar het volgende voorbeeld wordt een benadering:

+ [Maak een gratis service](search-create-service-portal.md)
+ Een kleine, representatieve gegevensset voorbereiden (uitgaan van 5000 documenten en de grootte van de steekproef tien procent)
+ [Bouw een eerste index](search-create-index-portal.md) en noteer de grootte in de portal (wordt ervan uitgegaan dat 30 MB)

Ervan uitgaande dat het voorbeeld is zowel vertegenwoordiger en tien procent van de gehele gegevensbron, wordt een index 30 MB ongeveer 300 MB als alle documenten die zijn geïndexeerd. Met dit voorlopige nummer hebt, u mogelijk dubbele bedrag voor het budget voor twee indexen (ontwikkeling en productie), voor een totaal van 600 MB in de opslagvereisten voor. Dit gemakkelijk door wordt voldaan aan de **Basic** tier, zodat u er rekening gebracht.

### <a name="step-2-develop-refined-estimates-using-a-billable-tier"></a>Stap 2: Ontwikkelen van verfijnde schattingen met behulp van een factureerbare laag

Sommige klanten de voorkeur geeft aan om te beginnen met toegewezen resources die kunnen grotere steekproeven en verwerkingstijden en realistische maakt een schatting van de index aantal, de grootte en de query volumes tijdens het ontwikkelen van ontwikkelen. In eerste instantie een service is ingericht op basis van een schatting inschatting en vervolgens als het project voor de ontwikkeling zich verder ontwikkelt, teams meestal weten of de bestaande service boven of onder de capaciteit voor geplande productieworkloads. 

1. [Bekijk Servicelimieten op elke laag](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity#index-limits) om te bepalen of het aantal indexen, u moet kunnen ondersteuning voor lagere niveaus. In de **Basic**-**S1**- **S2** index limieten voor de lagen zijn 15-50-200, respectievelijk.

1. [Een service maken op een factureerbare laag](search-create-service-portal.md):

    + Start op laag **Basic** of **S1** als u aan het begin van de leercurve.
    + Start op hoog **S2** of zelfs **S3**, als voor de hand liggende grootschalige indexeren en query-belastingen.

1. [Bouw een eerste index](search-create-index-portal.md) om te bepalen hoe gegevens worden omgezet naar een index. Dit is de enige manier om te schatten van de indexgrootte van de.

1. [Opslag, Servicelimieten, queryvolume en latentie controleren](search-monitor-usage.md) in de portal. In de portal ziet u query's per seconde, beperkte query's en zoeklatentie; die allemaal kunt u bepalen of u op de juiste laag zijn. Afgezien van portal metrische gegevens, kunt u configureren diepe bewaking, zoals clickthrough-analyses door in te schakelen [zoekverkeer](search-traffic-analytics.md). 

Index aantal en de grootte zijn net zo relevant voor uw analyse, omdat de maximale limieten zijn bereikt via volledig benut opslag (partities) of door de maximale limieten voor resources (indexen, indexeerfuncties, enzovoort), afhankelijk van wat het eerste komt. De portal kunt u bijhouden van beide, van huidige gebruiks- en maximumlimieten naast elkaar op de pagina overzicht.

> [!NOTE]
> Vereisten voor opslag kunnen worden te veel hoge als documenten overbodige gegevens bevatten. In het ideale geval bevatten documenten alleen gegevens die u nodig hebt voor de zoekfunctie. Binaire gegevens is niet doorzoekbaar en moet worden opgeslagen afzonderlijk (bijvoorbeeld in een Azure-tabel of blob-opslag) met een veld in de index voor het opslaan van een URL-verwijzing naar de externe gegevens. De maximale grootte van een afzonderlijke document is 16 MB (of minder als u bulksgewijs uploaden van meerdere documenten in één aanvraag zijn). [Servicelimieten in Azure Search](search-limits-quotas-capacity.md) bevat meer informatie.
>

**Overwegingen voor het volume van query**

Query's-per-seconde (QPS) is een meetwaarde die verf laat komen krijgt bij het afstemmen van prestaties, maar is doorgaans niet aanmerking laag, tenzij u zeer hoge queryvolume in het begin verwacht.

Alle van de standaardlagen kan een saldo van replica's leveren aan partities, sneller query kunt worden geholpen door extra replica's ondersteunen voor het laden van netwerktaakverdeling en extra partities voor parallelle verwerking. U kunt afstemmen van prestaties nadat de service is ingericht.

Klanten die verwachten dat strong aanhoudend query volumes vanaf het begin moeten rekening houden met hogere lagen, ondersteund door krachtigere hardware. U kunt vervolgens partities en replica's offline te halen, of zelfs overschakelen naar een lagere laag-service als de query-volumes niet om te realiseren. Zie voor meer informatie over het berekenen van de query-doorvoer [Azure Search-prestaties en optimalisatie](search-performance-optimization.md).


**Serviceovereenkomsten**

De **gratis** laag en preview-functies worden niet geleverd met [service level agreements (Sla's)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Voor alle factureerbare lagen, sla's van kracht wanneer u voldoende redundantie voor uw service inricht. Twee of meer replica's zijn vereist voor de SLA van de query (lezen). Drie of meer replica's zijn vereist voor query's en indexering SLA (lezen-schrijven). Het aantal partities is niet een aandachtspunt SLA. 

## <a name="tips-for-tier-evaluation"></a>Tips voor evaluatie van de laag

+ Meer informatie over het bouwen van efficiënte indexen en welke methoden vernieuwing zijn de minimale impactful. Het is raadzaam [zoekverkeer](search-traffic-analytics.md) voor de inzichten die op query-activiteiten.

+ Metrische gegevens om te bouwen om query's en verzamelen van gegevens over gebruikspatronen (query's tijdens kantooruren, indexering tijdens daluren) toestaan, en deze gegevens gebruiken om te informeren over toekomstige beslissingen voor de provisioning-service. Tijdens het niet praktisch op het niveau van een uur of per dag, kunt u partities en resources voor geplande wijzigingen in query-volumes, of niet-geplande maar langdurige wijzigingen als niveaus lang genoeg is bevatten voor het ondernemen van actie garandeert dynamisch aanpassen.

+ Houd er rekening mee dat het enige nadeel van onder inrichting is dat u wellicht te moeten verbreken van een service als de daadwerkelijke vereisten groter dan u geschat zijn. Als u wilt voorkomen dat de service wordt onderbroken, zou u een nieuwe service maken in hetzelfde abonnement op een hogere laag en deze naast elkaar uitvoeren totdat alle apps en aanvragen gericht op het nieuwe eindpunt.

## <a name="next-steps"></a>Volgende stappen

Beginnen met een **gratis** -laag en het bouwen van een eerste index met behulp van een subset van uw gegevens om te begrijpen van de kenmerken ervan. De gegevensstructuur in Azure Search is een omgekeerde index, waarbij de grootte en complexiteit van een omgekeerde index wordt bepaald door de inhoud. Houd er rekening mee dat maximaal redundante inhoud leidt doorgaans tot een kleinere-index dan maximaal onregelmatige inhoud. Het is daarom inhoud kenmerken in plaats van de grootte van de gegevensset waarmee wordt bepaald index opslagvereisten.

Zodra u een eerste idee van de indexgrootte hebt, [een factureerbare service inrichten](search-create-service-portal.md) op een van de lagen die in dit artikel worden besproken, ofwel **Basic** of een **Standard** laag. Versoepelen kunstmatig opgelegde beperkingen op gegevenssubsets en [opnieuw opbouwen van uw index](search-howto-reindex.md) om op te nemen alle gegevens die u daadwerkelijk wilt worden doorzocht.

[Partities en replica's toewijzen](search-capacity-planning.md) indien nodig om op te halen van de prestaties en schaal die u nodig hebt.

Als prestaties en capaciteit fijn, bent u klaar. Anders, een search-service op een andere laag die meer nauw aansluit bij de behoeften van uw opnieuw maken.

> [!NOTE]
> Voor meer hulp bij uw vragen naar boeken [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) of [Neem contact op met ondersteuning voor Azure](https://azure.microsoft.com/support/options/).