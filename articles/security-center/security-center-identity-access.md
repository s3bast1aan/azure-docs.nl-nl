---
title: Identiteit en toegang bewaken in Azure Security Center | Microsoft Docs
description: Lees hoe u de functies voor identiteit en toegang in Azure Security Center gebruikt om problemen met de toegang en identiteit van uw gebruikers te bewaken.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: rkarlin
ms.openlocfilehash: 4a934bd69e63605fd624d06533c4e411bc94b531
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630915"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Identiteit en toegang bewaken in Azure Security Center (Preview)
In dit artikel leest u hoe u Azure Security Center kunt gebruiken om de identiteit en toegangsactiviteiten van gebruikers te bewaken.

> [!NOTE]
> Identiteit en toegang bewaken is in preview en alleen beschikbaar op de prijscategorie Standard van Security Center. Bekijk de pagina [Prijzen](security-center-pricing.md) voor meer informatie over de tariefopties van Security Center.
>
>

Identiteit moet de controlelaag voor uw onderneming zijn. Het beveiligen van uw identiteit moet daarom de hoogste prioriteit hebben. De beveiligingsperimeter heeft zich ontwikkeld van een perimeter van het netwerk een perimeter-identiteit. Beveiliging wordt minder over het verdedigen van uw netwerk en meer informatie over uw gegevens beschermen, evenals de beveiliging van uw apps en gebruikers beheren. Tegenwoordig bevinden zich steeds meer gegevens en apps in de cloud en is identiteit het nieuwe perimeternetwerk.

Door identiteitsactiviteiten te bewaken, kunt u proactieve maatregelen treffen voordat een incident plaatsvindt of reactieve maatregelen nemen om een aanvalspoging te stoppen. Het dashboard Identity & Access biedt aanbevelingen, zoals:

- MFA inschakelen voor bevoegde accounts voor uw abonnement
- Externe accounts met schrijfmachtigingen verwijderen van uw abonnement
- Bevoegde externe accounts verwijderen van uw abonnement

> [!NOTE]
> Als uw abonnement meer dan 600 accounts heeft, kan Security Center om uit te voeren van de identiteit aanbevelingen op basis van uw abonnement. Aanbevelingen die niet worden uitgevoerd, worden vermeld onder 'niet-beschikbare evaluaties' Dit wordt hieronder beschreven.
Security Center is niet de identiteit aanbevelingen uitvoeren in een Cloud Solution Provider (CSP)-partner admin agents.
>
>

Zie [aanbevelingen](security-center-identity-access.md#recommendations) voor een lijst van de aanbevelingen voor identiteits- en toegangsbeheer die door Security Center.

## <a name="monitoring-security-health"></a>Beveiligingsstatus bewaken
U kunt de beveiligingsstatus van uw resources bewaken op de **Security Center-overzicht** dashboard. De **Resources** sectie is geen integriteitsindicator waarin de ernstcategorieën voor elk resourcetype.

U kunt een lijst van alle problemen weergeven door te selecteren **aanbevelingen**. Onder **Resources**, vindt u een lijst met problemen die specifiek zijn voor reken- en apps, gegevensbeveiliging, netwerken, of identiteit en toegang. Zie voor meer informatie over het toepassen van aanbevelingen [beveiligingsaanbevelingen implementeren in Azure Security Center](security-center-recommendations.md).

Zie voor een volledige lijst met aanbevelingen voor identiteits- en toegangsbeheer, [aanbevelingen](security-center-identity-access.md#recommendations).

Als u wilt doorgaan, selecteer **Identity & access** onder **Resources** of het hoofdmenu van Security Center.

![Dashboard van Security Center][1]

## <a name="monitor-identity-and-access"></a>Monitor voor identiteits- en toegangsbeheer
Onder **Identity & Access**, er zijn twee tabbladen:

- **Overzicht**: aanbevelingen geïdentificeerd door Security Center.
- **Abonnementen**: lijst met uw abonnementen en de huidige beveiligingsstatus van elk.

![Identity & Access][2]

### <a name="overview-section"></a>Overzichtssectie
Onder **overzicht**, er is een lijst met aanbevelingen. De eerste kolom bevat de aanbeveling. De tweede kolom geeft het totale aantal abonnementen dat is beïnvloed door deze aanbeveling. De derde kolom geeft de ernst van het probleem.

1. Selecteer een aanbeveling. Venster van de aanbeveling wordt geopend en wordt weergegeven:

  - Beschrijving van de aanbeveling
  - Lijst met abonnementen niet in orde en in orde is
  - Lijst met resources die niet-gescande vanwege een mislukte evaluatie zijn of de resource is in een abonnement op de gratis laag en wordt niet beoordeeld

  ![De aanbeveling venster][3]

1. Selecteer een abonnement in de lijst voor meer informatie.

### <a name="subscriptions-section"></a>Sectie abonnementen
Onder **abonnementen**, er is een lijst met abonnementen. De eerste kolom bevat de abonnementen. De tweede kolom geeft het totale aantal aanbevelingen voor elk abonnement. De derde kolom geeft de ernst van de problemen.

![Tabblad van het abonnement][4]

1.  Selecteer een abonnement. Een overzicht wordt geopend met drie tabbladen:

  - **Aanbevelingen**: op basis van beoordelingen die door Security Center die niet zijn uitgevoerd.
  - **Evaluaties doorgegeven**: lijst met beoordelingen uitgevoerd door Security Center die doorgegeven.
  - **Niet-beschikbare evaluaties**: lijst met beoordelingen die kan niet worden uitgevoerd vanwege een fout of omdat het abonnement meer dan 600 accounts heeft.

  Onder **aanbevelingen** is een lijst met aanbevelingen voor het geselecteerde abonnement en de ernst van elke aanbeveling.

  ![Aanbevelingen voor het abonnement selecteren][5]

1. Selecteer een aanbeveling voor een beschrijving van de aanbeveling, een lijst met abonnementen niet in orde en in orde is en een lijst met niet-gescande resources.

  ![Beschrijving van de aanbeveling][6]

  Onder **evaluaties doorgegeven** is een lijst van geslaagde evaluaties.  Ernst van deze beoordeling is altijd groen.

  ![Geslaagde evaluaties][7]

1. Selecteer een evaluatie van de doorgegeven in de lijst voor een beschrijving van de evaluatie en een lijst met abonnementen in orde. Er is een tabblad voor niet in orde abonnementen met een lijst met alle abonnementen die niet zijn geslaagd.

  ![Geslaagde evaluaties][8]

## <a name="recommendations"></a>Aanbevelingen
Gebruik de onderstaande tabel als referentie om te begrijpen van de beschikbare Identity & Access aanbevelingen en wat elke doet als u deze toepast.

| Aanbeveling | Beschrijving |
| --- | --- |
| Meer dan één eigenaar van uw abonnement opgeven | Hiermee wordt aanbevolen dat u meer dan één abonnementseigenaar aanwijzen om de beheerder toegang redundantie. |
| Maximaal 3 eigenaren voor uw abonnement opgeven | Hiermee wordt aanbevolen dat u minder dan 3 abonnementseigenaren aanwijzen om te reduceren van de mogelijkheden voor inbreuk op een gecomprimeerde eigenaar. |
| MFA inschakelen voor accounts met eigenaarsmachtigingen voor uw abonnement | Hiermee wordt aanbevolen dat u multi-factor Authentication (MFA) voor alle abonnementsaccounts met administrator-bevoegdheden inschakelen om te voorkomen dat er inbreuk wordt gepleegd accounts of resources. |
| MFA inschakelen voor accounts met schrijfmachtigingen voor uw abonnement | Hiermee wordt aanbevolen dat u multi-factor Authentication (MFA) voor alle abonnementsaccounts met schrijfmachtigingen inschakelen om te voorkomen dat er inbreuk wordt gepleegd accounts of resources. |
| MFA inschakelen voor accounts met leesmachtigingen voor uw abonnement | Hiermee wordt aanbevolen dat u multi-factor Authentication (MFA) voor alle abonnementsaccounts met leesmachtigingen inschakelen om te voorkomen dat er inbreuk wordt gepleegd accounts of resources. |
| Externe accounts met leesmachtigingen verwijderen van uw abonnement | Hiermee wordt aanbevolen dat u externe accounts met leesmachtigingen van uw abonnement verwijderen om onbewaakte toegang te voorkomen. |
| Externe accounts met schrijfmachtigingen verwijderen van uw abonnement | Hiermee wordt aanbevolen dat u externe accounts met schrijfmachtigingen van uw abonnement verwijderen om onbewaakte toegang te voorkomen. |
| Externe accounts met eigenaarsmachtigingen van uw abonnement verwijderen | Hiermee wordt aanbevolen dat u externe accounts met eigenaarsmachtigingen uit uw abonnement verwijderen om onbewaakte toegang te voorkomen. |
| Afgeschafte accounts verwijderen van abonnement | Raadt u afgeschafte accounts van uw abonnementen. |
| Afgeschafte accounts met eigenaarsmachtigingen verwijderen uit abonnement | Raadt u afgeschafte accounts met eigenaarsmachtigingen van uw abonnementen. |

## <a name="next-steps"></a>Volgende stappen
Zie de volgende onderwerpen voor meer informatie over aanbevelingen die betrekking hebben op andere typen Azure-resources:

- [Beveiligen van uw computers en toepassingen in Azure Security Center](security-center-virtual-machine-recommendations.md)
- [Protecting your network in Azure Security Center](security-center-network-recommendations.md) (Uw netwerk beveiligen in Azure Security Center)
- [Bescherming van uw Azure SQL-service en de gegevens in Azure Security Center](security-center-sql-service-recommendations.md)

Zie de volgende onderwerpen voor meer informatie over het Beveiligingscentrum:
* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informatie over het beheren van waarschuwingen en het reageren op beveiligingsincidenten in Security Center.
* [Beveiligingswaarschuwingen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Meer informatie over de verschillende typen beveiligingswaarschuwingen.
* [Veelgestelde vragen over Azure Security Center](security-center-faq.md). Vind antwoorden op veelgestelde vragen over het gebruik van Security Center.


<!--Image references-->
[1]: ./media/security-center-identity-access/overview.png
[2]: ./media/security-center-identity-access/identity-dashboard.png
[3]: ./media/security-center-identity-access/select-subscription.png
[4]: ./media/security-center-identity-access/subscriptions.png
[5]: ./media/security-center-identity-access/recommendations.png
[6]: ./media/security-center-identity-access/designate.png
[7]: ./media/security-center-identity-access/passed-assessments.png
[8]: ./media/security-center-identity-access/remove.png
