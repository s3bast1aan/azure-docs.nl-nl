---
title: 'Zelfstudie: LUIS-app maken voor het extraheren van gegevens - Azure | Microsoft Docs'
description: In deze zelfstudie leert u hoe u een eenvoudige LUIS-app maakt met behulp van 'intenties' en de entiteit Simple om via machine learning verkregen gegevens te extraheren.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 87d97b078927800e4e90c39a70e2acc7163a4c84
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493044"
---
# <a name="tutorial-7-add-simple-entity-and-phrase-list"></a>Zelfstudie: 7. De entiteit Simple en een woordgroepenlijst toevoegen
In deze zelfstudie maakt u een app die laat zien hoe u met behulp van de entiteit **Simple** via machine learning verkregen gegevens extraheert uit een 'utterance'.

<!-- green checkmark -->
> [!div class="checklist"]
> * Algemene informatie over entiteiten van het type Simple 
> * Nieuwe LUIS-app maken voor het domein Human Resources (HR) 
> * Een entiteit Simple maken om taken te extraheren uit een app
> * App trainen en publiceren
> * Eindpunt van app opvragen om JSON-antwoord van LUIS te zien
> * Woordgroepenlijst toevoegen om signalering van taakwoorden te verbeteren
> * Een app trainen, publiceren en opnieuw een eindpunt opvragen

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Voordat u begint
Als u geen Human Resources-app uit de zelfstudie over de [samengestelde entiteit](luis-tutorial-composite-entity.md) hebt, [importeert](luis-how-to-start-new-app.md#import-new-app) u de JSON in een nieuwe app op de [LUIS](luis-reference-regions.md#luis-website)-website. De app die kan worden geïmporteerd bevindt zich in de GitHub-opslagplaats met [voorbeelden van LUIS](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-composite-HumanResources.json).

Als u de oorspronkelijke Human Resources-app wilt gebruiken, kloont u de versie op de pagina [Settings](luis-how-to-manage-versions.md#clone-a-version) en wijzigt u de naam in `simple`. Klonen is een uitstekende manier om te experimenteren met verschillende functies van LUIS zonder dat de oorspronkelijke versie wordt gewijzigd.  

## <a name="purpose-of-the-app"></a>Doel van de app
Deze app laat zien hoe u gegevens extraheert uit een utterance. Laten we de volgende utterances uit een chatbot als voorbeeld nemen:

|Utterance|Extraheerbare functienaam|
|:--|:--|
|Ik wil solliciteren op de nieuwe functie als boekhouder.|boekhouder|
|Verzend mijn CV voor de functie als technicus.|technicus|
|Sollicitatieformulier invullen voor vacature 123456|123456|

Tijdens deze zelfstudie wordt een nieuwe entiteit toegevoegd waarmee de functienaam kan worden geëxtraheerd. 

## <a name="purpose-of-the-simple-entity"></a>Doel van de entiteit Simple
Het doel van de entiteit Simple in deze LUIS-app is om LUIS te leren wat een functienaam is en waar dit kan worden gevonden in een utterance. Het deel van de utterance dat de functie vormt, kan per utterance verschillen afhankelijk van woordkeuze en lengte van de utterance. LUIS heeft voorbeelden nodig van functies in utterances voor alle intenties.  

Het is moeilijk om te bepalen wat de functienaam is omdat dit een zelfstandig naamwoord, werkwoord of een woordgroep van meerdere woorden kan zijn. Bijvoorbeeld:

|Taken|
|--|
|technicus|
|software-engineer|
|senior software-engineer|
|manager technisch team |
|luchtverkeersleider|
|automonteur|
|ambulancebestuurder|
|operateur|
|onderhoudsmonteur|
|monteur voor draaiende machineonderdelen|

In deze LUIS-app komen functienamen in verschillende intenties voor. Door deze woorden in alle utterances binnen intenties van een label te voorzien, leert LUIS meer over wat elke functie is en waar deze kan worden gevonden in utterances.

## <a name="create-job-simple-entity"></a>Entiteit Simple voor functie maken

1. Zorg ervoor dat uw Human Resources-app zich in de sectie **Build** van LUIS bevindt. U kunt naar deze sectie gaan door **Build** te selecteren in de menubalk rechtsboven. 

2. Selecteer op de pagina **Intent** de intentie **ApplyForJob**. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png "Schermopname van LUIS met de intentie ApplyForJob gemarkeerd")](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png#lightbox)

3. In de utterance `I want to apply for the new accounting job` selecteert u `accounting`, voert u `Job` in het bovenste veld van het snelmenu in en selecteert u **Create new entity** in het snelmenu. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Schermopname van LUIS met de intentie ApplyForJob en de stappen voor het creëren van een entiteit gemarkeerd")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. Controleer de naam en het type van de entiteit in het pop-upvenster en selecteer **Done**.

    ![Een modaal pop-updialoogvenster voor een entiteit Simple creëren met de naam van de functie en het type Simple](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. In de utterance `Submit resume for engineering position` labelt u het woord `engineering` als een entiteit Job. Selecteer het woord `engineering` en selecteer **Job** in het snelmenu. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Schermopname van LUIS waarin het labelen van de entiteit Job is gemarkeerd")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)

    Alle utterances zijn gelabeld maar vijf utterances zijn niet voldoende om LUIS te leren welke woorden en woordgroepen gerelateerd zijn aan de functie. De functies die gebruikmaken van de cijferwaarde hebben niet meer voorbeelden nodig omdat deze van een reguliere expressie gebruikmaken. Van functies die uit woorden of woordgroepen bestaan, moeten ten minste 15 meer voorbeelden bestaan. 

6. Voeg meer utterances toe en markeer de woorden en woordgroepen als entiteit **Job**. De functietypen zijn algemeen voor aanstellingen van een aanstellingsservice. Als u functies voor een specifieke bedrijfstak had willen hebben, moet dat duidelijk worden uit de woorden voor de functie. 

    |Utterance|De entiteit Job|
    |:--|:--|
    |Ik solliciteer voor de functie van programmamanager op de afdeling Onderzoek en Ontwikkeling|Programmamanager|
    |Hier is mijn sollicitatiebrief naar de functie van kok.|kok|
    |Mijn cv voor de functie van kampleider heb ik bijgesloten.|kampleider|
    |Dit is mijn cv voor de functie van administratief medewerker.|administratief medewerker|
    |Ik wil graag solliciteren op de managementfunctie op de verkoopafdeling.|management, verkoopafdeling|
    |Dit is mijn cv voor de nieuwe functie als boekhouder.|boekhouder|
    |Mijn sollicitatiebrief voor de functie van barmedewerker is bijgesloten.|barmedewerker|
    |Ik wil met deze brief solliciteren naar de functie van dakdekker en glaszetter.|dakdekker, glaszetter|
    |Mijn cv voor de functie van buschauffeur vindt u hier.|buschauffeur|
    |Ik ben een gediplomeerd verpleegkundige. Hier is mijn cv.|gediplomeerd verpleegkundige|
    |Ik wil u hierbij mijn sollicatieformulieren aanbieden voor de positie als docent die ik in de krant heb gelezen.|docent|
    |Dit is mijn cv voor de functie van vakkenvuller op de afdeling Fruit en groenten.|vakkenvuller|
    |Solliciteren als tegelzetter.|tegelzetter|
    |Bijgevoegd cv voor landschapsarchitect.|landschapsarchitect|
    |Mijn cv voor de functie als biologiedocent is bijgesloten.|biologiedocent|
    |Ik wil graag solliciteren naar een functie binnen het vakgebied fotografie.|fotografie|git 

## <a name="label-entity-in-example-utterances-for-getjobinformation-intent"></a>Entiteit labelen in voorbeelden van utterances voor de intentie GetJobInformation
1. Selecteer **Intents** in het linkermenu.

2. Selecteer **GetJobInformation** in de lijst met intenties. 

3. Label de functies in de voorbeelden van utterances:

    |Utterance|De entiteit Job|
    |:--|:--|
    |Zijn er banen beschikbaar in het werken met databases?|databases|
    |Op zoek naar een nieuwe werkomgeving met verantwoordelijkheden als boekhouder|boekhouder|
    |Welke vacatures bestaan er voor hoofdtechnici?|hoofdtechnici|

    Er zijn nog meer voorbeelden van utterances maar die bevatten geen woorden met betrekking tot functies.

## <a name="train-the-luis-app"></a>LUIS-app trainen

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>App publiceren om eindpunt-URL op te vragen

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Eindpunt opvragen met een andere utterance

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Ga naar het einde van de URL in het adres en voer `Here is my c.v. for the programmer job` in. De laatste parameter van de queryreeks is `q`, de utterance **query**. Deze utterance is niet hetzelfde als een van de gelabelde utterances en dit is dus een goede test die de utterances van `ApplyForJob` als resultaat moet geven.

```JSON
{
  "query": "Here is my c.v. for the programmer job",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.9826467
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.9826467
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0218927357
    },
    {
      "intent": "MoveEmployee",
      "score": 0.007849265
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00349470088
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.00348804821
    },
    {
      "intent": "None",
      "score": 0.00319909188
    },
    {
      "intent": "FindForm",
      "score": 0.00222647213
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00211193133
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00172086991
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.00138010911
    }
  ],
  "entities": [
    {
      "entity": "programmer",
      "type": "Job",
      "startIndex": 24,
      "endIndex": 33,
      "score": 0.5230502
    }
  ]
}
```

## <a name="names-are-tricky"></a>Namen zijn verraderlijk
De LUIS-app heeft weliswaar de juiste intentie gevonden met een hoge betrouwbaarheidsfactor en heeft de functienaam geëxtraheerd, maar namen zijn verraderlijk. Probeer het met utterance `This is the lead welder paperwork`.  

In de volgende JSON antwoordt LUIS met de juiste intentie, `ApplyForJob`, maar is de functienaam `lead welder` niet geëxtraheerd. 

```JSON
{
  "query": "This is the lead welder paperwork.",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.468558252
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.468558252
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0102701457
    },
    {
      "intent": "MoveEmployee",
      "score": 0.009442534
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00639619166
    },
    {
      "intent": "None",
      "score": 0.005859333
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.005087704
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00315379258
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00259344373
    },
    {
      "intent": "FindForm",
      "score": 0.00193389168
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.000420796918
    }
  ],
  "entities": []
}
```

Omdat alles een naam kan zijn, is LUIS beter in staat om entiteiten te herkennen als er een woordgroepenlijst bestaat die het signaleringsvermogen versterkt.

## <a name="to-boost-signal-add-jobs-phrase-list"></a>Om het signaleringsvermogen te versterken, een woordgroepenlijst toevoegen
Open [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) in de GitHub-opslagplaats met LUIS-voorbeelden. De lijst bevat meer dan duizend woorden en woordgroepen met betrekking tot functies. Zoek in de lijst naar woorden met betrekking tot functies die relevant voor uw zijn. Als de woorden of woordgroepen waar u naar op zoek bent niet in de lijst voorkomen, kunt u deze zelf toevoegen.

1. In de sectie **Build** van de LUIS-app, selecteert u **Phrase lists** onder het menu **Improve app performance**.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png "Schermopname van de linkernavigatieknop van de woordgroepenlijst gemarkeerd")](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png#lightbox)

2. Selecteer **Create new phrase list**. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png "Schermopname van de knop voor het maken van een nieuwe woordgroepenlijst gemarkeerd")](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png#lightbox)

3. Noem de nieuwe woordgroepenlijst `Jobs` en kopieer de lijst van jobs-phrase-list.csv naar het tekstvak voor het invoeren van **waarden**. Selecteer Enter. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Schermopname van het pop-updialoogvenster voor het maken van een nieuwe woordgroepenlijst")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Als u meer woorden aan de woordgroepenlijst wilt toevoegen, bekijkt u de **Gerelateerde waarden** en voegt u de woorden toe die relevant voor u zijn. 

4. Selecteer **Save** om de woordgroepenlijst te activeren.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Schermopname van het pop-updialoogvenster met woorden in het vak voor waarden")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. U moet de app opnieuw [trainen](#train-the-luis-app) en [publiceren](#publish-the-app-to-get-the-endpoint-URL) om de woordgroepenlijst te kunnen gebruiken.

6. Een eindpunt opnieuw opvragen met de dezelfde utterance: `This is the lead welder paperwork.`

    Het JSON-antwoord bevat de geëxtraheerde entiteit:

    ```JSON
    {
        "query": "This is the lead welder paperwork.",
        "topScoringIntent": {
            "intent": "ApplyForJob",
            "score": 0.920025647
        },
        "intents": [
            {
            "intent": "ApplyForJob",
            "score": 0.920025647
            },
            {
            "intent": "GetJobInformation",
            "score": 0.003800706
            },
            {
            "intent": "Utilities.StartOver",
            "score": 0.00299335527
            },
            {
            "intent": "MoveEmployee",
            "score": 0.0027167045
            },
            {
            "intent": "None",
            "score": 0.00259556063
            },
            {
            "intent": "FindForm",
            "score": 0.00224019377
            },
            {
            "intent": "Utilities.Stop",
            "score": 0.00200693542
            },
            {
            "intent": "Utilities.Cancel",
            "score": 0.00195913855
            },
            {
            "intent": "Utilities.Help",
            "score": 0.00162656687
            },
            {
            "intent": "Utilities.Confirm",
            "score": 0.0002851904
            }
        ],
        "entities": [
            {
            "entity": "lead welder",
            "type": "Job",
            "startIndex": 12,
            "endIndex": 22,
            "score": 0.8295959
            }
        ]
    }
    ```

## <a name="phrase-lists"></a>Woordgroepenlijsten
Door de woordgroepenlijst toe te voegen, wordt de signalering van woorden die in de lijst voorkomen versterkt, maar worden deze woorden **niet** als exacte overeenkomst gebruikt. De woordgroepenlijst bevat diverse functies met `lead` als eerste woord. Ze bevat ook de functie `welder` maar niet de functie `lead welder`. Deze woordgroepenlijst voor functies is mogelijk niet volledig. Omdat u regelmatig [utterances van eindpunten zult bekijken ](luis-how-to-review-endoint-utt.md) en daaronder andere woorden met betrekking tot functies zult tegenkomen, kunt u deze aan uw woordgroepenlijst toevoegen. Vervolgens moet u de app opnieuw trainen en publiceren.

## <a name="what-has-this-luis-app-accomplished"></a>Wat is er met deze LUIS-app bereikt?
Met deze app, een entiteit Simple en een woordgroepenlijst met woorden, kan nu een intentie van een query in natuurlijke taal worden geïdentificeerd en kunnen de functiegegevens worden geretourneerd. 

Uw chatbot beschikt nu over voldoende informatie om vast te stellen wat de primaire actie is die ondernomen moet worden om naar een functie te solliciteren en om een parameter toe te passen die naar deze functie verwijst. 

## <a name="where-is-this-luis-data-used"></a>Waar worden deze gegevens van LUIS gebruikt? 
LUIS hoeft niets meer te doen met deze aanvraag. De aanroepende toepassing, zoals een chatbot, kan het resultaat topScoringIntent nemen plus de gegevens van de entiteit om vervolgens met behulp van een API van een derde partij de functiegegevens naar een HR-medewerker te verzenden. Als er andere programmatische opties bestaan voor de bot of de aanroepende toepassing, wordt die taak niet uitgevoerd door LUIS. LUIS bepaalt alleen wat de bedoeling van de gebruiker is. 

## <a name="clean-up-resources"></a>Resources opschonen

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een vooraf gemaakte entiteit KeyPhrase toevoegen](luis-quickstart-intent-and-key-phrase.md)