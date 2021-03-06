---
title: Prijscategorieën voor Azure Database for MySQL
description: Dit artikel beschrijft de Prijscategorieën voor Azure Database voor MySQL.
services: mysql
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 07/11/2018
ms.openlocfilehash: 70891e4c1425badb43dac66ada9c0b3a43b8fb0d
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173670"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Azure Database voor MySQL Prijscategorieën

U kunt een Azure Database for MySQL-server maken in een van drie verschillende Prijscategorieën: Basic, algemeen gebruik en geoptimaliseerd voor geheugen. De Prijscategorieën worden onderscheiden op basis van de hoeveelheid Computing in vCores die kunnen worden ingericht, het geheugen per vCore en de technologie voor gegevensopslag gebruikt voor het opslaan van de gegevens. Alle resources zijn ingericht op het niveau van de MySQL-server. Een server kan een of meer databases bevatten.

|    | **Basic** | **Algemeen gebruik** | **Geoptimaliseerd voor geheugen** |
|:---|:----------|:--------------------|:---------------------|
| Bewerking voor COMPUTE | Gen 4, Gen 5 | Gen 4, Gen 5 | Gen 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32 |2, 4, 8, 16 |
| Geheugen per vCore | 2 GB | 5 GB | 10 GB |
| Opslaggrootte | 5 GB tot 1 TB | 5 GB tot 4 TB | 5 GB tot 4 TB |
| Opslagtype | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Database-back-up bewaarperiode | 7 tot 35 dagen | 7 tot 35 dagen | 7 tot 35 dagen |

Kies een prijscategorie, gebruik de volgende tabel als uitgangspunt.

| Prijscategorie | Beoogde workloads |
|:-------------|:-----------------|
| Basic | Workloads waarvoor lichte reken- en i/o-prestaties. Voorbeelden zijn onder meer servers die worden gebruikt voor ontwikkeling of tests of kleinschalige, onregelmatig gebruikte toepassingen. |
| Algemeen doel | Meeste zakelijke workloads die goede balans tussen Computing en geheugen met schaalbare i/o-doorvoer vereisen. Voorbeelden zijn servers voor het hosten van web en mobiele apps en andere zakelijke toepassingen.|
| Geoptimaliseerd geheugen | High-performance workloads van databases waarvoor de prestaties van het geheugen voor snellere transactieverwerking en hogere gelijktijdigheid. Voorbeelden zijn servers voor het verwerken van realtimegegevens en transactie- of analyseapps apps van hoge prestaties.|

Nadat u een server maakt, het aantal vCores, hardware genereren en prijzen laag (met uitzondering van en naar Basic) kan omhoog of omlaag worden gewijzigd binnen enkele seconden. U kunt ook afzonderlijk de hoeveelheid opslag van en de back-up bewaarperiode omhoog of omlaag met zonder downtime van toepassingen aanpassen. U kunt de back-upopslag-type niet wijzigen nadat een server is gemaakt. Zie voor meer informatie de [resources schalen](#scale-resources) sectie.

## <a name="compute-generations-and-vcores"></a>Bewerkingen voor COMPUTE en vCores

COMPUTE-resources worden verstrekt als vCores, die staan voor de logische CPU van de onderliggende hardware. Op dit moment, kunt u kiezen uit twee bewerkingen voor compute, Gen 4 en Gen 5. Gen 4 logische CPU's zijn gebaseerd op Intel E5-2673 v3 (Haswell)-processors voor 2,4 GHz. Gen 5 logische CPU's zijn gebaseerd op Intel E5-2673 v4 (Broadwell) 2,3 GHz-processors. Gen 4 en Gen 5 zijn beschikbaar in de volgende regio's ('X' geeft aan beschikbaar).

| **Azure-regio** | **Gen 4** | **Gen 5** |
|:---|:----------:|:--------------------:|
| VS - midden | X |  |
| VS - oost | X | X |
| VS - oost 2 | X | X |
| Noord-centraal VS | X | X |
| Zuid-centraal VS | X | X |
| VS - west | X | X |
| VS - west 2 |  | X |
| Canada - midden | X | X |
| Canada - oost | X | X |
| Brazilië - zuid | X | X |
| Noord-Europa | X | X |
| West-Europa |  | X |
| Frankrijk - centraal |  | X |
| Verenigd Koninkrijk West |  | X |
| Verenigd Koninkrijk Zuid |  | X |
| Oost-Azië | X | X |
| Zuidoost-Azië | X | X |
| Australië - oost |  | X |
| Australië - centraal |  | X |
| Australië - centraal 2 |  | X |
| Australië - zuidoost |  | X |
| Centraal-India | X | X |
| West-India | X | X |
| Zuid-India |  | X |
| Japan - oost | X | X |
| Japan - west | X | X |
| Korea - centraal |  | X |
| Korea - zuid |  | X |

## <a name="storage"></a>Storage

De opslag die u inricht is de hoeveelheid opslagcapaciteit beschikbaar zijn voor uw Azure Database voor MySQL-server. De opslag wordt gebruikt voor de databasebestanden, tijdelijke bestanden transactielogboeken en de MySQL-server zich aanmeldt. De totale hoeveelheid opslag die u inricht definieert ook de i/o-capaciteit beschikbaar voor uw server.

|    | **Basic** | **Algemeen gebruik** | **Geoptimaliseerd voor geheugen** |
|:---|:----------|:--------------------|:---------------------|
| Opslagtype | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Opslaggrootte | 5 GB tot 1 TB | 5 GB tot 4 TB | 5 GB tot 4 TB |
| Maximale grootte van de verhoging | 1 GB | 1 GB | 1 GB |
| IOPS | Variabele |3 IOPS/GB<br/>Min 100 IOPS<br/>Max. aantal 6000 IOP 's | 3 IOPS/GB<br/>Min 100 IOPS<br/>Max. aantal 6000 IOP 's |

U kunt extra opslagcapaciteit toevoegen tijdens en na het maken van de server. De Basic-laag biedt geen een garantie IOPS. In het algemeen gebruik en geoptimaliseerd voor geheugen Prijscategorieën, schalen de IOPS-waarde met de grootte van de ingerichte opslag in een verhouding van 3:1.

U kunt uw i/o-gebruik in Azure portal of met behulp van Azure CLI-opdrachten kunt bewaken. De relevante metrische gegevens voor het bewaken van zijn [-limiet voor opslag, opslagpercentage, opslag die wordt gebruikt en i/o-percentage](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>De opslaglimiet bereikt

De server wordt gemarkeerd als alleen-lezen wanneer de hoeveelheid vrije opslagruimte kleiner is dan 5 GB of 5% van de ingerichte opslag bereikt, afhankelijk van wat kleiner is. Bijvoorbeeld, als u 100 GB aan opslag hebt ingericht, en het gebruik van de werkelijke gaat via 95 GB, de server is gemarkeerd als alleen-lezen. U kunt ook als u 5 GB aan opslag hebt ingericht, is de server gemarkeerd alleen-lezen wanneer de vrije opslagruimte is minder dan 250 MB bereikt.  

Terwijl de service probeert te maken van de server alleen-lezen, alle nieuwe schrijftransactieaanvragen geblokkeerd en kunnen bestaande actieve transacties blijft om uit te voeren. Wanneer de server is ingesteld op alleen-lezen, voert alle daaropvolgende schrijfbewerkingen en transactie is mislukt. Lezen query's blijven doorwerken. Nadat u de ingerichte opslag verhoogt, wordt de server zijn klaar om te accepteren schrijftransacties opnieuw.

U wordt aangeraden dat u een waarschuwing instellen om u te waarschuwen als de opslag van uw server bijna de drempelwaarde is bereikt, zodat u kunt te voorkomen dat de status alleen-lezen. Zie voor meer informatie de documentatie over [over het instellen van een waarschuwing](howto-alert-on-metric.md).

## <a name="backup"></a>Backup

Voor de service worden automatisch back-ups van uw server. De minimale bewaarperiode voor back-ups is zeven dagen. U kunt een bewaarperiode van maximaal 35 dagen instellen. De bewaarperiode kan worden aangepast op elk gewenst moment tijdens de levensduur van de server. U kunt kiezen tussen lokaal redundant en geografisch redundante back-ups. Geografisch redundante back-ups worden ook opgeslagen in de [geokoppeling regio](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) van de regio waarin uw server is gemaakt. Deze redundantie biedt een niveau van beveiliging in het geval van een noodgeval. Ook krijgt u de mogelijkheid om uw server te herstellen naar een andere Azure-regio waarin de service beschikbaar met geografisch redundante back-ups is. Het is niet mogelijk om te wisselen tussen de twee opties voor back-upopslag nadat de server is gemaakt.

## <a name="scale-resources"></a>Resources omhoog/omlaag schalen

Nadat u uw server maakt, kunt u onafhankelijk van elkaar wijzigen de vCores, de hardware-generatie en de prijscategorie (met uitzondering van en naar Basic), de hoeveelheid opslag en de bewaarperiode voor back-up. U kunt de back-upopslag-type niet wijzigen nadat een server is gemaakt. Het aantal vCores kunt omhoog of omlaag worden geschaald. De bewaarperiode voor back-up kan worden geschaald omhoog of omlaag van 7 tot 35 dagen. De grootte van de kan alleen worden verhoogd. Schalen van de resources die kan worden gedaan via de portal of Azure CLI. Zie voor een voorbeeld van schalen met behulp van Azure CLI, [bewaken en schalen van een Azure Database for MySQL-server met behulp van Azure CLI](scripts/sample-scale-server.md).

Wanneer u het aantal vCores wijzigt, worden de hardware-generatie en de prijscategorie, een kopie van de oorspronkelijke server wordt gemaakt met de nieuwe compute-toewijzing. Nadat de nieuwe server actief en werkend is, worden verbindingen via overgeschakeld naar de nieuwe server. Tijdens het moment dat wanneer het systeem overgeschakeld naar de nieuwe server, kunnen er geen nieuwe verbindingen tot stand worden gebracht en alle niet-doorgevoerde transacties worden teruggedraaid. In dit venster varieert, maar in de meeste gevallen is minder dan een minuut.

Schalen van opslag en het wijzigen van de bewaarperiode voor back-up zijn ' True ' online bewerkingen. Er is geen downtime en uw toepassing wordt niet beïnvloed. IOP's met de grootte van de ingerichte opslag te schalen, kunt u de beschikbare IOPS verhogen met uw server met omhoog schalen van opslag.

## <a name="pricing"></a>Prijzen

Zie de service voor de meest recente prijsinformatie [pagina met prijzen](https://azure.microsoft.com/pricing/details/mysql/). Om te zien van de kosten voor de configuratie die u wilt, de [Azure-portal](https://portal.azure.com/#create/Microsoft.MySQLServer) ziet u de maandelijkse kosten op de **prijscategorie** tabblad op basis van de opties die u selecteert. Als u geen Azure-abonnement hebt, kunt u de prijscalculator van Azure gebruiken om op te halen van een geschatte prijs. Op de [prijscalculator van Azure](https://azure.microsoft.com/pricing/calculator/) website, selecteer **items toevoegen**, vouw de **Databases** categorie, en kies **Azure Database for MySQL**om aan te passen van de opties.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [een MySQL-server maken in de portal](howto-create-manage-server-portal.md).
- Meer informatie over het [controleren en schalen van een Azure Database for MySQL-server met behulp van Azure CLI](scripts/sample-scale-server.md).
- Meer informatie over de [service beperkingen](concepts-limits.md).
