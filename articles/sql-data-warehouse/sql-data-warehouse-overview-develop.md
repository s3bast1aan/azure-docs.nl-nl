---
title: Resources voor het ontwikkelen van een datawarehouse in Azure | Microsoft Docs
description: Concepten voor ontwikkeling, ontwerpbeslissingen, aanbevelingen en codering technieken voor SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: d9a272b2f43e080cd44b7179fe6f9dc55507142b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31601801"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Ontwerpbeslissingen en codering technieken voor SQL Data Warehouse
Bekijk via deze artikelen ontwikkeling voor een beter begrip van belangrijke ontwerpbeslissingen, aanbevelingen en codering technieken voor SQL Data Warehouse.

## <a name="key-design-decisions"></a>Belangrijke ontwerpbeslissingen
De volgende artikelen markeren concepten en ontwerpbeslissingen voor het ontwikkelen van een gedistribueerde datawarehouse met behulp van SQL Data Warehouse:

* [Verbindingen][connections]
* [Gelijktijdigheid van taken][concurrency]
* [Transacties][transactions]
* [Gebruiker gedefinieerde schema 's][user-defined schemas]
* [tabeldistributie][table distribution]
* [tabelindexen][table indexes]
* [Tabelpartities][table partitions]
* [CTAS][CTAS]
* [Statistieken][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Aanbevelingen voor ontwikkeling en codering technieken
Deze artikelen markeren specifieke codering technieken, tips en aanbevelingen voor het ontwikkelen van uw SQL Data Warehouse:

* [Opgeslagen procedures][stored procedures]
* [labels][labels]
* [Weergaven][views]
* [Tijdelijke tabellen][temporary tables]
* [Dynamische SQL][dynamic SQL]
* [lussen][looping]
* [Groeperen op Opties][group by options]
* [Toewijzing van variabele][variable assignment]

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie, [SQL Data Warehouse T-SQL-instructies](sql-data-warehouse-reference-tsql-statements.md).

<!--Image references-->

<!--Article references-->
[concurrency]: ./resource-classes-for-workload-management.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md


<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
