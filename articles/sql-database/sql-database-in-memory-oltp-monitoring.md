---
title: XTP geheugenopslag bewaken | Microsoft Docs
description: Schatting en monitor XTP geheugenopslag gebruiken, capaciteit; capaciteit fout 41823 oplossen
services: sql-database
author: jodebrui
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: jodebrui
ms.openlocfilehash: f74c9bf06cad8b84d08baf7a0a0504b9cb729bf4
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308676"
---
# <a name="monitor-in-memory-oltp-storage"></a>Monitor In-geheugen OLTP-opslag
Wanneer u [In het geheugen OLTP](sql-database-in-memory.md), gegevens in tabellen geoptimaliseerd voor geheugen en tabelvariabelen bevindt zich in In het geheugen OLTP-opslag. Elke servicelaag Premium en kritieke zakelijke heeft een maximale grootte van de In-geheugen OLTP-opslag. Zie [resource op basis van het DTU - limieten voor één database](sql-database-dtu-resource-limits-single-databases.md), [resource op basis van het DTU - limieten voor elastische pools](sql-database-dtu-resource-limits-elastic-pools.md),[vCore gebaseerde limieten - individuele databases](sql-database-vcore-resource-limits-single-databases.md) en [vCore gebaseerde limieten - elastische pools](sql-database-vcore-resource-limits-elastic-pools.md).

Wanneer deze limiet wordt overschreden, invoegen en bijwerken operations kunnen mislukken met fout 41823 voor zelfstandige databases en de fout 41840 voor elastische pools. Op dat moment moet aan gegevens om geheugen vrij te verwijderen, of upgrade van de prestatielaag van uw database.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Bepalen of gegevens binnen het kapje In-Memory OLTP-opslag passen
Bepaal de caps opslag van de verschillende Servicelagen. Zie [resource op basis van het DTU - limieten voor één database](sql-database-dtu-resource-limits-single-databases.md), [resource op basis van het DTU - limieten voor elastische pools](sql-database-dtu-resource-limits-elastic-pools.md),[vCore gebaseerde limieten - individuele databases](sql-database-vcore-resource-limits-single-databases.md) en [vCore gebaseerde limieten - elastische pools](sql-database-vcore-resource-limits-elastic-pools.md).

Het schatten van geheugenvereisten voor een tabel geoptimaliseerd voor geheugen works voor SQL Server dezelfde manier als het komt in Azure SQL Database. Enkele minuten duren om te controleren dat artikel op [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Tabel en variabele tabelrijen, evenals indexen, meetellen voor de gegevensgrootte max gebruiker. ALTER TABLE moet bovendien voldoende ruimte voor het maken van een nieuwe versie van de gehele tabel en de indexen ervan.

## <a name="monitoring-and-alerting"></a>Bewaking en waarschuwingen
U kunt het opslaggebruik In het geheugen als percentage van het kapje opslag controleren voor de prestatielaag in de [Azure-portal](https://portal.azure.com/): 

1. Zoek het gebruik van Resource op de blade Database en klik op bewerken.
2. Selecteer de metriek `In-Memory OLTP Storage percentage`.
3. Als u wilt een waarschuwing toevoegen, klikt u op in het vak Resourcegebruik om de metriek blade te openen en klik vervolgens op waarschuwing toevoegen.

Of gebruik de volgende query om het gebruik van de opslag In-memory weer te geven:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Out-In-geheugen OLTP opslagsituaties - fouten 41823 en 41840 corrigeren
Roept het kapje In-Memory OLTP-opslag in de databaseresultaten van uw in invoegen, bijwerken, ALTER en bewerkingen is mislukt met foutbericht 41823 (voor zelfstandige databases) of fout 41840 (voor elastische pools) maakt. Beide fouten ervoor zorgen dat de actieve transactie af te breken.

Foutberichten 41823 en 41840 aangeven dat de tabellen geoptimaliseerd voor geheugen en tabelvariabelen in de database of de groep van toepassingen de maximale grootte van de In-geheugen OLTP-opslag hebt bereikt.

Deze fout, ofwel oplossen:

* Gegevens verwijderen uit de tabellen geoptimaliseerd voor geheugen, mogelijk offloading van de gegevens naar de traditionele, schijfgebaseerde tabellen. of,
* De servicetier upgraden naar een met voldoende opslagruimte in het geheugen voor de gegevens die u wilt bewaren in tabellen geoptimaliseerd voor geheugen.

> [!NOTE] 
> In zeldzame gevallen kunnen fouten 41823 en 41840 zijn tijdelijke, wat betekent dat er voldoende opslagruimte beschikbaar In het geheugen OLTP, en u de bewerking is geslaagd. Daarom wordt aangeraden beide monitor de totale beschikbare In het geheugen OLTP-opslag en probeer het opnieuw wanneer eerst zich voordoen tijdens fout 41823 of 41840. Zie voor meer informatie over Pogingslogica [conflictdetectie en probeer het opnieuw logica met In-geheugen OLTP](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Volgende stappen
Zie voor richtlijnen bewaking, [bewaking Azure SQL Database met dynamische beheerweergaven](sql-database-monitoring-with-dmvs.md).
