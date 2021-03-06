---
title: Het bladeren door de beschikbare webpagina's | Microsoft Docs
description: Laat zien hoe u de pagina door alle van de webpagina's met Bing kunt terugkeren.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: bf29783246c603270d59b20b63027fccdbd45b89
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35344456"
---
# <a name="paging-webpages"></a>Paginering webpagina 's 

Wanneer u de Web zoeken-API aanroept, retourneert Bing een lijst met resultaten. De lijst is een subset van het totale aantal resultaten die relevant voor de query zijn mogelijk. Als u het geschatte aantal beschikbare resultaten, toegang krijgen tot het antwoord-object [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#totalestimatedmatches) veld.  
  
Het volgende voorbeeld wordt de `totalEstimatedMatches` veld met een Web-antwoord.  
  
```  
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```  
  
Pagina via de webpagina's die beschikbaar is, gebruikt u de [aantal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#count) en [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#offset) queryparameters.  
  
De `count` parameter geeft u het aantal resultaten te retourneren in het antwoord. Het maximum aantal resultaten dat u in het antwoord aanvragen mogelijk is 50. De standaardwaarde is 10. Het werkelijke aantal geleverde mogelijk minder dan aangevraagd.

De `offset` parameter geeft u het aantal resultaten om over te slaan. De `offset` is gebaseerd op nul zijn en moet kleiner zijn dan (`totalEstimatedMatches` - `count`).  
  
Als u weergeven van 15 webpagina's per pagina wilt, stelt u `count` tot en met 15 en `offset` op 0 om op te halen van de eerste pagina van de resultaten. Voor elke volgende pagina opgehoogd `offset` 15 (bijvoorbeeld, 15, 30).  
  
Het volgende voorbeeld aanvragen 15 webpagina's die beginnen bij verschuiving 45.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Als de standaard `count` waarde werkt voor uw implementatie, moet u alleen om op te geven de `offset` queryparameter.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

De Web-API voor Search retourneert resultaten die eventueel afbeeldingen, video's en nieuws en webpagina's bevatten. Wanneer u de zoekresultaten zijn paging de [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) antwoord en de andere antwoorden zoals afbeeldingen of nieuws. Als u bijvoorbeeld `count` tot 50, krijgt u terug 50 webpagina resultaten, maar het antwoord resultaten voor het ook nog andere antwoorden kan bevatten. Bijvoorbeeld: de reactie omvat mogelijk 15 installatiekopieën en 4 nieuwsartikelen. Het is ook mogelijk dat nieuws over de eerste pagina, maar niet de tweede pagina, de resultaten omvatten kunnen of vice versa.   
    
Als u opgeeft de `responseFilter` queryparameter en webpagina's niet opnemen in de filterlijst, gebruik niet de `count` en `offset` parameters.  
