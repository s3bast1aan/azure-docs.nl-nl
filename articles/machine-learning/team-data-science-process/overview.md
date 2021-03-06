---
title: Wat is Team Data Science Process? | Microsoft Docs
description: Een data science-methodologie voor het leveren van predictive analytics-oplossingen en intelligente toepassingen biedt.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: deguhath
ms.openlocfilehash: e44b21530cb3db8a2ba354ef19ebb3d2c557ecae
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125756"
---
# <a name="what-is-the-team-data-science-process"></a>Wat is Team Data Science Process?

Het Team Data Science Process (TDSP) is een flexibele, iteratieve data science-methodologie predictive analytics-oplossingen en intelligente toepassingen efficiënt leveren. TDSP helpt om de teamsamenwerking en het leerproces te verbeteren. Het bevat een distillatie van de best practices en structuren van Microsoft en andere bedrijven in deze branche die willen helpen om de implementatie van initiatieven op het gebied van data science te laten slagen. Het doel is bedrijven te helpen om de voordelen van hun analyseprogramma volledig te kunnen benutten.

Dit artikel bevat een overzicht van TDSP en de belangrijkste onderdelen. Wij bieden een algemene beschrijving van het proces dat kan worden geïmplementeerd met tal van hulpprogramma's. Een gedetailleerde beschrijving van de rollen die betrokken zijn bij de levenscyclus van het proces van de project-taken en is beschikbaar in extra gekoppelde onderwerpen. Richtlijnen voor het implementeren van de TDSP met behulp van een specifieke set Microsoft-programma's en infrastructuur die we gebruiken voor het implementeren van de TDSP in onze teams is ook beschikbaar.

## <a name="key-components-of-the-tdsp"></a>Belangrijke onderdelen van de TDSP

TDSP bestaat uit de volgende belangrijke onderdelen:

- Een **levenscyclus van wetenschappelijke gegevens** definitie
- Een **gestandaardiseerd projectstructuur**
- **Infrastructuur en resources** voor data science-projecten
- **Hulpprogramma's** voor de projectuitvoering van


## <a name="data-science-lifecycle"></a>Levenscyclus van wetenschappelijke gegevens

Het Team Data Science Process (TDSP) biedt een levenscyclus voor de ontwikkeling van uw data science-projecten structureren. De levenscyclus van geeft een overzicht van de stappen van begin tot eind, projecten gewoonlijk volgen wanneer ze worden uitgevoerd.

Als u een andere volledige wetenschappelijke levensduur, zoals gebruikt [heldere-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) of uw organisatie eigen aangepaste proces, u kunt nog steeds de TDSP op basis van een taak in de context van de levenscycli van de ontwikkeling. Op hoog niveau hebben deze verschillende methoden veel gemeen. 

Deze levenscyclus beheren, is ontworpen voor data science-projecten die worden geleverd als onderdeel van intelligente toepassingen. Deze toepassingen machine learning of kunstmatige intelligentie modellen voor voorspellende analyses te implementeren. Verkennende data science of ad-hoc analytics-projecten kunnen ook profiteren van dit proces. Maar in dergelijke gevallen enkele van de stappen mogelijk niet nodig.    

De levenscyclus van geeft een overzicht van de belangrijke fasen die projecten doorgaans worden uitgevoerd, vaak iteratief:

* **Inzicht in het bedrijf**
* **Gegevens verzamelen en begrijpen**
* **Modeling**
* **Implementatie**
* **Aanvaarding van de klant**

Hier volgt een visuele representatie van het **Team Data Science Process lifecycle**. 

![TDSP-Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

De doelen, taken en documentatie artefacten voor elke fase van de TDSP-levenscyclus worden beschreven in de [Team Data Science Process lifecycle](lifecycle.md) onderwerp. Deze taken en artefacten zijn gekoppeld aan het Projectrollen:

- Oplossingsarchitect
- Projectmanager
- Gegevenswetenschapper
- Projectleider 

Het volgende diagram biedt een rasterweergave van de taken (in het blauw) en de artefacten (in het groen) die zijn gekoppeld aan elke fase van de levenscyclus (op de horizontale as) voor deze rollen (op de verticale as). 

[ ![TDSP-rollen-en-taken](./media/overview/tdsp-tasks-by-roles.png) ](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Gestandaardiseerde projectstructuur

Met alle projecten delen van een mapstructuur en sjablonen gebruiken voor de project-documenten, kunt eenvoudig de teamleden voor informatie over hun projecten. Alle code en documenten worden opgeslagen in een versiebeheersysteem (Circuits), zoals Git, TFS of Subversion om in te schakelen samenwerking voor teams. Bijhouden van taken en -functies in een flexibel bijhouden van systeem zoals Jira projecten, Rally, Visual Studio Team Services van kunt is dichter bijhouden van de code voor afzonderlijke functies. Dergelijke bijhouden kan ook teams om op te halen beter kostenramingen. TDSP raadt aan om het maken van een aparte opslagplaats voor elk project in de Circuits voor versiebeheer, informatiebeveiliging en samenwerking. De gestandaardiseerde structuur voor alle projecten helpt met het bouwen van institutionele kennis in de hele organisatie.

We bieden sjablonen voor de mapstructuur en de vereiste documenten in standaardlocaties. Deze mapstructuur organiseert de bestanden die code voor gegevensverkenning en het ophalen van functies bevatten en dat model iteraties opnemen. Deze sjablonen wordt het eenvoudiger voor teamleden om te begrijpen werk van anderen en nieuwe leden toevoegen aan teams. Het is gemakkelijk om te bekijken en bijwerken van sjablonen in markdown-indeling. Gebruik sjablonen om controlelijsten voorzien van belangrijke vragen voor elk project om ervoor te zorgen dat het probleem duidelijk gedefinieerd is en producten te voldoen aan de kwaliteit verwacht. Voorbeelden zijn:

- een project Handvest vastleggen van de zakelijke probleem en het bereik van het project
- rapporten met gegevens naar het document de structuur en statistieken van de onbewerkte gegevens
- model-rapporten naar de afgeleide functies
- model maatstaven voor prestaties zoals ROC-curve of MSE


[ ![TDSP-mappen](./media/overview/tdsp-dir-structure.png) ](./media/overview/tdsp-dir-structure.png#lightbox)

De mapstructuur kan worden gekloond van [Github](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastructuur en resources voor data science-projecten  

TDSP bevat aanbevelingen voor het beheren van gedeelde analyse- en opslaginfrastructuur, zoals:

- cloud-bestandssystemen voor het opslaan van gegevenssets, 
- databases
- de clusters (Hadoop- of Apache Spark) big data 
- machine learning-services. 

De analyse- en opslaginfrastructuur kan zich in de cloud of on-premises. Dit is waar de onbewerkte gegevens en verwerkte gegevenssets worden opgeslagen. Deze infrastructuur kunt reproduceerbare analyse. Ook voorkomt u dubbele, wat tot inconsistenties en onnodige infrastructuurkosten leiden kan. Hulpprogramma's worden aan de gedeelde resources inrichten, traceren en zorgen dat elk teamlid veilig verbinding maken met deze resources verstrekt. Het is ook een goede gewoonte om het project leden maken van een consistente compute-omgeving. Andere teamleden kunnen vervolgens worden gerepliceerd en experimenten valideren.

Hier volgt een voorbeeld van een team werkt aan meerdere projecten en het delen van verschillende onderdelen van de cloud analytics infrastructuur.

[ ![TDSP-infrastructuur](./media/overview/tdsp-analytics-infra.png) ](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Hulpprogramma's voor de projectuitvoering van

Maak kennis met de processen in de meeste organisaties uitdagingen met zich mee. Hulpprogramma's te implementeren de data science-proces en de levenscyclus help lager de barrières voor vergroten van de consistentie van hun goedkeuring. TDSP biedt een eerste set met hulpprogramma's en scripts aan de acceptatie van TDSP binnen een team snel aan de slag. Ook helpt u bij automatiseren van de algemene taken in de levenscyclus van wetenschappelijke gegevens, zoals gegevens verkennen en modelleren van de basislijn. Er is een goed gedefinieerde structuur die is opgegeven voor personen om bij te dragen gedeelde functies en hulpprogramma's in gedeelde code-opslagplaats van hun team. Deze resources kunnen vervolgens worden gebruikt door andere projecten in het team of de organisatie. TDSP is ook van plan om in te schakelen van de bijdragen van de hulpprogramma's voor de gehele community. De TDSP-hulpprogramma's kunnen worden gekloond van [Github](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Volgende stappen

[Team Data Science Process: Rollen en taken](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) geeft een overzicht van de functies key personeel en de bijbehorende taken voor een team van gegevenswetenschappers die over dit proces standaardiseert. 
