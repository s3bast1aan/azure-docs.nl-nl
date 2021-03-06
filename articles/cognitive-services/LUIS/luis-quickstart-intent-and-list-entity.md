---
title: 'Zelfstudie: Een LUIS-app maken voor het ophalen van tekstgegevens met exacte overeenkomst - Azure | Microsoft Docs'
description: In deze zelfstudie leert u hoe u een eenvoudige LUIS-app maakt met behulp van 'intenties' en een lijstentiteit om gegevens te extraheren.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: afad3fe725fddd0748cc206517a7274815cf1653
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495261"
---
# <a name="tutorial-4-add-list-entity"></a>Zelfstudie: 4. Een entiteit List toevoegen
In deze zelfstudie gaat u een app maken die laat zien hoe u gegevens ophaalt die overeenkomen met een vooraf gedefinieerde lijst. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Algemene informatie over lijstentiteiten 
> * Met de intentie MoveEmployee een nieuwe LUIS-app maken voor het domein Human Resources (HR)
> * Entiteit List toevoegen om Employee uit utterance te extraheren
> * App trainen en publiceren
> * Eindpunt van app opvragen om JSON-antwoord van LUIS te zien

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Voordat u begint
Als u geen Human Resources-app uit de zelfstudie over de [regex-entiteit](luis-quickstart-intents-regex-entity.md) hebt, [importeert](luis-how-to-start-new-app.md#import-new-app) u de JSON in een nieuwe app op de [LUIS](luis-reference-regions.md#luis-website)-website. De app die kan worden geïmporteerd bevindt zich in de GitHub-opslagplaats met [voorbeelden van LUIS](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-regex-HumanResources.json).

Als u de oorspronkelijke Human Resources-app wilt gebruiken, kloont u de versie op de pagina [Settings](luis-how-to-manage-versions.md#clone-a-version) en wijzigt u de naam in `list`. Klonen is een uitstekende manier om te experimenteren met verschillende functies van LUIS zonder dat de oorspronkelijke versie wordt gewijzigd. 

## <a name="purpose-of-the-list-entity"></a>Doel van de entiteit List
Deze app voorspelt utterances over het verplaatsen van een werknemer van het ene gebouw naar een ander. Deze app maakt gebruik van een entiteit List om een werknemer te extraheren. Naar de werknemer kan worden verwezen met de naam, het telefoonnummer, het e-mailadres of het Amerikaans sociaal-fiscaal nummer. 

Een entiteit List kan veel items bevatten met synoniemen voor elk item. Voor een klein tot middelgrote bedrijf wordt de entiteit List gebruikt voor het extraheren van werknemersgegevens. 

De canonieke naam voor elk item is het werknemersnummer. Voorbeelden van de synoniemen binnen dit domein zijn: 

|Doel synoniem|Waarde synoniem|
|--|--|
|Naam|John W. Smith|
|E-mailadres|john.w.smith@mycompany.com|
|Toestelnummer|x12345|
|Mobiel nummer (privé)|425-555-1212|
|Amerikaans sociaal-fiscaal nummer|123-45-6789|

Een entiteit List is een goede keuze voor dit type gegevens wanneer:

* De gegevenswaarden deel uitmaken van een bekende set.
* De set maximale [begrenzingen](luis-boundaries.md) van LUIS voor dit entiteitstype niet overschrijdt.
* De tekst in de utterance exact overeenkomt met een synoniem. 

LUIS extraheert de werknemer op een manier dat een standaardopdracht om de werknemer te verplaatsen door de clienttoepassing kan worden gemaakt.
<!--
## Example utterances
Simple example utterances for a `MoveEmployee` inent:

```
move John W. Smith from B-1234 to H-4452
mv john.w.smith@mycompany from office b-1234 to office h-4452

```
-->

## <a name="add-moveemployee-intent"></a>De intentie MoveEmployee toevoegen

1. Zorg ervoor dat uw Human Resources-app zich in de sectie **Build** van LUIS bevindt. U kunt naar deze sectie gaan door **Build** te selecteren in de menubalk rechtsboven. 

2. Selecteer **Create new intent**. 

3. Voer in het pop-updialoogvenster `MoveEmployee` in en selecteer vervolgens **Done**. 

    ![Schermopname van het pop-updialoogvenster voor het maken van een nieuwe intentie met](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Voeg voorbeelden van utterances toe aan de intentie.

    |Voorbeelden van utterances|
    |--|
    |John W. Smith van B-1234 naar H-4452 verplaatsen|
    |verplaats john.w.smith@mycompany.com van kantoor b-1234 naar kantoor h-4452|
    |verplaats x12345 naar h-1234 morgen|
    |plaats 425-555-1212 in HH 2345|
    |verplaats 123-45-6789 van A-4321 naar J-23456|
    |Verplaats Jill Jones van D-2345 naar J-23456|
    |verplaats jill-jones@mycompany.com naar M-12345|
    |x23456 naar M-12345|
    |425-555-0000 naar h-4452|
    |234 56 7891 naar hh 2345|

    [ ![Schermopname van de pagina Intent met de nieuwe utterances gemarkeerd](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png) ](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png#lightbox)

## <a name="create-an-employee-list-entity"></a>Een entiteit Employee List maken
Nu de intentie **MoveEmployee** utterances bevat, moet LUIS kunnen vaststellen wat een werknemer is. 

1. Selecteer **Entities** in het linkerpaneel.

2. Selecteer **Create new intent**.

3. Voer in het pop-updialoogvenster `Employee` in als naam voor de entiteit en **List** als het entiteitstype. Selecteer **Done**.  

    [![](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png "Schermopname van het pop-updialoogvenster voor het maken van een nieuwe entiteit")](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png#lightbox)

4. Voer op de pagina van de entiteit Employee `Employee-24612` als de nieuwe waarde in.

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "Schermafbeelding van het invoeren van de waarde")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

5. Voor synoniemen voegt u de volgende waarden toe:

    |Doel synoniem|Waarde synoniem|
    |--|--|
    |Naam|John W. Smith|
    |E-mailadres|john.w.smith@mycompany.com|
    |Toestelnummer|x12345|
    |Mobiel nummer (privé)|425-555-1212|
    |Amerikaans sociaal-fiscaal nummer|123-45-6789|

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "Schermafbeelding van het invoeren van synoniemen")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

6. Voer `Employee-45612` als een nieuwe waarde in.

7. Voor synoniemen voegt u de volgende waarden toe:

    |Doel synoniem|Waarde synoniem|
    |--|--|
    |Naam|Jill Jones|
    |E-mailadres|jill-jones@mycompany.com|
    |Toestelnummer|x23456|
    |Mobiel nummer (privé)|425-555-0000|
    |Amerikaans sociaal-fiscaal nummer|234-56-7891|

## <a name="train-the-luis-app"></a>LUIS-app trainen

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>App publiceren om eindpunt-URL op te vragen

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Eindpunt opvragen met een andere utterance

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Ga naar het einde van de URL in het adres en voer `shift 123-45-6789 from Z-1242 to T-54672` in. De laatste parameter van de queryreeks is `q`, de utterance **query**. Deze utterance is niet hetzelfde als een van de gelabelde utterances en dit is dus een goede test die de intentie `MoveEmployee` met `Employee` geëxtraheerd als resultaat moet geven.

  ```JSON
  {
    "query": "shift 123-45-6789 from Z-1242 to T-54672",
    "topScoringIntent": {
      "intent": "MoveEmployee",
      "score": 0.9882801
    },
    "intents": [
      {
        "intent": "MoveEmployee",
        "score": 0.9882801
      },
      {
        "intent": "FindForm",
        "score": 0.016044287
      },
      {
        "intent": "GetJobInformation",
        "score": 0.007611245
      },
      {
        "intent": "ApplyForJob",
        "score": 0.007063288
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00684710965
      },
      {
        "intent": "None",
        "score": 0.00304174074
      },
      {
        "intent": "Utilities.Help",
        "score": 0.002981
      },
      {
        "intent": "Utilities.Confirm",
        "score": 0.00212222221
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.00191026414
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0007461446
      }
    ],
    "entities": [
      {
        "entity": "123 - 45 - 6789",
        "type": "Employee",
        "startIndex": 6,
        "endIndex": 16,
        "resolution": {
          "values": [
            "Employee-24612"
          ]
        }
      },
      {
        "entity": "123",
        "type": "builtin.number",
        "startIndex": 6,
        "endIndex": 8,
        "resolution": {
          "value": "123"
        }
      },
      {
        "entity": "45",
        "type": "builtin.number",
        "startIndex": 10,
        "endIndex": 11,
        "resolution": {
          "value": "45"
        }
      },
      {
        "entity": "6789",
        "type": "builtin.number",
        "startIndex": 13,
        "endIndex": 16,
        "resolution": {
          "value": "6789"
        }
      },
      {
        "entity": "-1242",
        "type": "builtin.number",
        "startIndex": 24,
        "endIndex": 28,
        "resolution": {
          "value": "-1242"
        }
      },
      {
        "entity": "-54672",
        "type": "builtin.number",
        "startIndex": 34,
        "endIndex": 39,
        "resolution": {
          "value": "-54672"
        }
      }
    ]
  }
  ```

  De werknemer is gevonden en is geretourneerd als type `Employee` met de resolutiewaarde `Employee-24612`.

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>Waar vindt de verwerking van natuurlijke taal plaats in de entiteit List? 
Omdat de lijstentiteit een exacte tekstovereenkomst is, is er geen verwerking van natuurlijke taal (of machine learning) nodig. LUIS maakt gebruik van verwerking van natuurlijke taal (of machine learning) om de juiste best scorende intentie te selecteren. Daarnaast kan een utterance een combinatie zijn van meer dan één entiteit of zelfs meer dan één type entiteit. Elke utterance wordt verwerkt voor alle entiteiten in de app, inclusief de verwerking van entiteiten in natuurlijke taal (of verkregen via machine learning).

## <a name="what-has-this-luis-app-accomplished"></a>Wat is er met deze LUIS-app bereikt?
Deze app heeft met behulp van een entiteit List de juiste werknemer geëxtraheerd. 

Uw chatbot heeft nu voldoende gegevens om de primaire actie `MoveEmployee` te bepalen en welke medewerker moet worden verplaatst. 

## <a name="where-is-this-luis-data-used"></a>Waar worden deze gegevens van LUIS gebruikt? 
LUIS hoeft niets meer te doen met deze aanvraag. De aanroepende toepassing, zoals een chatbot, kan het resultaat topScoringIntent nemen plus de gegevens van de entiteit om de volgende stap uit te voeren. LUIS is niet verantwoordelijk voor die programmatische werken voor de bot of aanroepende toepassing. LUIS bepaalt alleen wat de bedoeling van de gebruiker is. 

## <a name="clean-up-resources"></a>Resources opschonen

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een hiërarchische entiteit aan de app toevoegen](luis-quickstart-intent-and-hier-entity.md)

