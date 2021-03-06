---
title: Aggregaties in Azure Log Analytics-query's | Microsoft Docs
description: Hierin wordt beschreven aggregatiefuncties in Log Analytics-query's die nuttige manieren om uw gegevens te analyseren.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 562fdc82e0b814fc759bda7b853492b47d073925
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190126"
---
# <a name="aggregations-in-log-analytics-queries"></a>Aggregaties in Log Analytics-query 's

> [!NOTE]
> U moet voltooien [aan de slag met de Analytics-portal](get-started-analytics-portal.md) en [aan de slag met query's](get-started-queries.md) voordat het voltooien van deze les gaat uitvoeren.

Dit artikel wordt beschreven aggregatiefuncties in Log Analytics-query's die nuttige manieren om uw gegevens te analyseren. Deze alle functies werken met de `summarize` operator die hiermee een tabel met samengevoegde resultaten van de invoertabel wordt.

## <a name="counts"></a>Aantallen

### <a name="count"></a>count
Telt het aantal rijen in de resultatenset nadat alle filters zijn toegepast. Het volgende voorbeeld wordt het totale aantal rijen in de _Perf_ tabel uit de laatste 30 minuten. Het resultaat wordt geretourneerd in een kolom met de naam *count_* , tenzij u deze een specifieke naam toewijzen:


```OQL
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```OQL
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Een visualisatie tijdgrafiek kan nuttig zijn voor een trend Zie na verloop van tijd:

```OQL
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

De uitvoer van dit voorbeeld toont het aantal records voor prestaties trendlijn in intervallen van vijf minuten:

![Aantal trend](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>DCount, dcountif
Gebruik `dcount` en `dcountif` aan het aantal distinctieve waarden in een specifieke kolom. De volgende query wordt geëvalueerd als het aantal afzonderlijke computers in het afgelopen uur heartbeats worden verzonden:

```OQL
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Als u wilt tellen alleen de Linux-computers die heartbeats worden verzonden, gebruik `dcountif`:

```OQL
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Subgroepen evalueren
Om uit te voeren een aantal of andere aggregaties op subgroepen in uw gegevens, gebruikt u de `by` trefwoord. Als u bijvoorbeeld voor het tellen van het aantal verschillende Linux-computers die in elk land heartbeats worden verzonden:

```OQL
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|Verenigde Staten    | 19                  |
|Canada           | 3                   |
|Ierland          | 0                   |
|Verenigd Koninkrijk   | 0                   |
|Nederland      | 2                   |


Voor het analyseren van kleinere subgroepen van uw gegevens, extra kolomnamen toevoegen aan de `by` sectie. U wilt bijvoorbeeld de afzonderlijke computers uit elk land per OSType tellen:

```OQL
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Percentielen en variantie
Bij het evalueren van numerieke waarden, een normaal worden bij deze gemiddelde met behulp van `summarize avg(expression)`. Gemiddelden worden beïnvloed door extreme waarden die kenmerkend zijn voor slechts enkele gevallen. Om dit probleem op te lossen, kunt u minder gevoelig zijn functies zoals `median` of `variance`.

### <a name="percentile"></a>Percentiel
Als u de mediaanwaarde zoekt, gebruikt u de `percentile` functie met een waarde op te geven van de percentiel:

```OQL
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

U kunt ook verschillende percentielen om een samengevoegd resultaat voor elke opgeven:

```OQL
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Dit kan laten zien dat een computer CPU's vergelijkbare mediaan waarden hebben, maar sommige zijn onveranderlijk rond de mediaan, andere computers veel hogere en lagere CPU waarden wat betekent dat zij ervaren pieken hebben gerapporteerd.

### <a name="variance"></a>Afwijking
Om te evalueren rechtstreeks de variantie van een waarde, gebruikt u de standaarddeviatie en variantie methoden:

```OQL
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Er is een goede manier om de stabiliteit van het CPU-gebruik analyseren stdev combineren met de berekening van de mediaan:

```OQL
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Zie andere lessen voor het gebruik van de querytaal van Log Analytics:

- [Bewerkingen op tekenreeksen uitvoeren](string-operations.md)
- [Datum- en tijdbewerkingen](datetime-operations.md)
- [Geavanceerde aggregaties](advanced-aggregations.md)
- [JSON en gegevensstructuren](json-data-structures.md)
- [Geavanceerde query schrijven](advanced-query-writing.md)
- [Joins](joins.md)
- [Grafieken](charts.md)
