---
title: LUIS vooraf gemaakte entiteiten keyphrase referentie - Azure | Microsoft Docs
titleSuffix: Azure
description: In dit artikel bevat keyphrase vooraf gedefinieerde entiteitgegevens in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/09/2018
ms.author: diberry
ms.openlocfilehash: 904f327dfe20e3d0864cbf355fd10237659879ee
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238597"
---
# <a name="keyphrase-entity"></a>keyPhrase-entiteit
keyPhrase haalt u een verscheidenheid aan sleuteltermen uit een utterance. U hoeft niet te voorbeeld utterances met keyPhrase naar de toepassing toevoegen. keyPhrase entiteit wordt ondersteund in [veel culturen](luis-supported-languages.md#languages-supported) als onderdeel van de [tekstanalyse](../text-analytics/overview.md) functies. 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Oplossing voor vooraf gedefinieerde keyPhrase entiteit
Het volgende voorbeeld ziet u de resolutie van de **builtin.keyPhrase** entiteit.

```JSON
{
  "query": "where is the educational requirements form for the development and engineering group",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.182757929
  },
  "entities": [
    {
      "entity": "development",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 61
    },
    {
      "entity": "educational requirements",
      "type": "builtin.keyPhrase",
      "startIndex": 13,
      "endIndex": 36
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [percentage](luis-reference-prebuilt-percentage.md), [getal](luis-reference-prebuilt-number.md), en [leeftijd](luis-reference-prebuilt-age.md) entiteiten. 