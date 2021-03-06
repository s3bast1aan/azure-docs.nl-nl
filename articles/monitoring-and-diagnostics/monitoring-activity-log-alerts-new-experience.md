---
title: Gebruik van de activiteit logboek waarschuwingen in de nieuwe ervaring van de Monitor voor Azure-waarschuwingen
description: Het maken van de activiteit logboek waarschuwingen van het tabblad waarschuwingen (Preview) onder Azure Monitor. In dit artikel wordt de nieuwe gebruikerservaring voor deze functie.
author: jyothirmaisuri
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: v-jysur
ms.component: alerts
ms.openlocfilehash: 10cd4e2ea14ab66a44ba2123e025b3d1b91f385c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263557"
---
# <a name="create-activity-log-alerts-using-the-new-alerts-experience"></a>Waarschuwingen met behulp van de nieuwe waarschuwingen optreden activiteitenlogboek maken

Activiteit logboek waarschuwingen zijn de waarschuwingen die ophalen geactiveerd wanneer een nieuwe activiteit logboekgebeurtenis optreedt die overeenkomt met de voorwaarden die is opgegeven in de waarschuwing.

Deze waarschuwingen zijn voor Azure-resources, kunnen worden gemaakt met behulp van een Azure Resource Manager-sjabloon. Ze kunnen ook worden gemaakt, bijgewerkt of verwijderd in de Azure portal. Dit artikel bevat de concepten achter activiteit logboek waarschuwingen. Deze vervolgens ziet u hoe de Azure portal gebruiken om een waarschuwing op activiteit logboekgebeurtenissen met behulp van de nieuwe ervaring in te stellen [Azure waarschuwingen](monitoring-overview-unified-alerts.md).

Meestal maakt u activiteit logboek waarschuwingen meldingen wilt ontvangen wanneer bepaalde wijzigingen voor bronnen in uw Azure-abonnement optreden, vaak binnen het bereik van bepaalde resourcegroepen of bron. Bijvoorbeeld, u wilt mogelijk een melding krijgen wanneer een virtuele machine in (voorbeeld resourcegroep) **myProductionResourceGroup** wordt verwijderd, of u kunt de hoogte worden gebracht als er geen nieuwe rollen zijn toegewezen aan een gebruiker in uw abonnement.

U kunt een activiteit logboek waarschuwing op basis van een eigenschap op het hoogste niveau in de JSON-object voor een activiteit van het gebeurtenislogboek kunt configureren. De portal ziet u echter de meest voorkomende opties zoals beschreven in de volgende secties:

>[!NOTE]

> Wanneer de categorie 'administratieve' is, moet u ten minste één van de bovenstaande criteria opgeven in de waarschuwing. U kunt een waarschuwing die wordt geactiveerd telkens wanneer een gebeurtenis wordt gemaakt in de activiteitenlogboeken van de niet maken.
>

Wanneer een activiteit logboek waarschuwing is geactiveerd, wordt een actiegroep gebruikt om acties of meldingen te genereren. Een actiegroep is een herbruikbare reeks melding ontvangers, zoals e-mailadressen, telefoonnummers webhook-URL's of SMS-bericht. De ontvangers kunnen worden verwezen vanuit meerdere waarschuwingen en de meldingskanalen groep centraliseren. Wanneer u uw logboek activiteit waarschuwing definieert, hebt u twee opties. U kunt:

* Gebruik een bestaande actiegroep in de activiteit logboek-waarschuwing.
* Maak een nieuwe actiegroep.

Zie voor meer informatie over actiegroepen, [maken en beheren van actiegroepen in de Azure portal](monitoring-action-groups.md).

Zie voor meer informatie over servicestatusmeldingen, [activiteit logboek meldingen ontvangen op servicestatusmeldingen](monitoring-activity-log-alerts-on-service-notifications.md).


## <a name="whats-new-in-alerts-for-activity-logs"></a>Wat is er nieuw in waarschuwingen om activiteitenlogboeken?

[Waarschuwingen van Azure](monitoring-overview-unified-alerts.md) biedt nu verbeterde gebruikerservaring voor activiteit logboek waarschuwingen. Met de [verbeterde gebruikerservaring voor waarschuwingen](monitoring-overview-unified-alerts.md), kunt u nu:

- [Maak](#create-an-alert-rule-for-an-activity-log) en [beheren](#view-and-manage-activity-log-alert-rules) de activiteit waarschuwingsregels, meld u bij **Monitor** > **waarschuwingen** blade. Meer informatie over [activiteitenlogboeken](monitoring-overview-activity-logs.md).

- **Nieuwe opties voor waarschuwingen doel**: tijdens het maken van een nieuwe activiteit logboek waarschuwingsregel, kunt u nu kiezen een doelbron of een resourcegroep of een abonnement.


## <a name="create-an-alert-rule-for-an-activity-log"></a>Een waarschuwingsregel voor een logboek maken

> [!NOTE]

>  Tijdens het maken van de regels voor waarschuwingen, Controleer het volgende:

> - Abonnement in het bereik is niet verschilt van het abonnement waarvoor de waarschuwing wordt gemaakt.
- Criteria moet niveau/status/aanroeper / resourcegroep / resource-id / brontype / gebeurteniscategorie waarop de waarschuwing is geconfigureerd.
- Er is geen voorwaarde 'dragen' of geneste voorwaarden in de configuratie van de waarschuwing JSON (in principe slechts één zet met niets verdere zet/dragen is toegestaan).


Gebruik de volgende procedure:

1. Selecteer in Azure portal **Monitor** > **waarschuwingen**
2. Klik op **nieuwe waarschuwingsregel** boven aan de **waarschuwingen** venster.

     ![nieuwe waarschuwingsregel](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule.png)

     De **maken regel** venster wordt weergegeven.

      ![nieuwe waarschuwingsregel opties](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule-options.png)

3. **Onder definiëren meldingsvoorwaarde,** bieden de volgende informatie en klik op **gedaan**.

    - **Waarschuwing doel:** gebruiken om te bekijken en selecteer het doel voor de nieuwe waarschuwing, **filteren op abonnement** / **filteren op resourcetype** en selecteer de bron of de resourcegroep van de een lijst weergegeven.

    > [!NOTE]

    > u kunt een resource, resourcegroep of een hele abonnement selecteren.

    **Waarschuwing doel voorbeeld weergeven**

     ![Doel selecteren](./media/monitoring-activity-log-alerts-new-experience/select-target.png)

    - Onder **doel Criteria**, klikt u op **criteria toevoegen** Selecteer het signaaltype als **activiteitenlogboek**.

    - Selecteer het signaal in de lijst weergegeven.

    U kunt de geschiedenis logboek tijdlijn en de bijbehorende waarschuwing logica voor dit doel-signaal selecteren:

    **Scherm criteria toevoegen**

    ![criteria toevoegen](./media/monitoring-activity-log-alerts-new-experience/add-criteria.png)

    **Geschiedenis van tijd**: in de afgelopen 12/6/24 uur gedurende de afgelopen Week.

    **Waarschuwing logica**:

     - **Gebeurtenisniveau**-de ernst van de gebeurtenis. **Uitgebreide, informatief, waarschuwing, fout**, of **kritieke**.
     - **Status**: de status van de gebeurtenis. **Gestart, kan niet**, of **geslaagd**.
     - **De gebeurtenis wordt gestart door**: ook wel bekend als de aanroepfunctie De e-mailadres of Azure Active Directory-id van de gebruiker die de bewerking uitgevoerd.

        **Voorbeeld signaal grafiek met waarschuwing logica toegepast** :

        ![ geselecteerde criteria](./media/monitoring-activity-log-alerts-new-experience/criteria-selected.png)

4. Onder **waarschuwingsregels details definiëren**, bieden de volgende details:

    - **De naam van de waarschuwingsregel** – naam voor de nieuwe regel voor waarschuwing
    - **Beschrijving** – beschrijving voor de nieuwe regel voor waarschuwing
    - **Waarschuwing opslaan naar resourcegroep** – Selecteer de resourcegroep waar u deze nieuwe regel.

5. Onder **actiegroep**, geef de actiegroep die u wilt toewijzen aan deze nieuwe waarschuwingsregel uit de vervolgkeuzelijst. U kunt ook [Maak een nieuwe actiegroep](monitoring-action-groups.md) en toewijzen aan de nieuwe regel. Klik op om een nieuwe groep **+ een nieuwe groep**.

6. Als de regels nadat u deze hebt gemaakt, klikt u op **Ja** voor **regel inschakelen tijdens het maken van** optie.
7. Klik op **waarschuwingsregel maken**.

    De nieuwe waarschuwingsregel voor het logboek wordt gemaakt en verschijnt een bevestigingsbericht aan de bovenkant van het venster Direct.

    ![ waarschuwingsregel gemaakt](./media/monitoring-activity-log-alerts-new-experience/alert-created.png)

    U kunt inschakelen, uitschakelen, bewerken of verwijderen van een regel. [Meer informatie](#view-and-manage-activity-log-alert-rules) over het beheren van de activiteit logboek regels.

## <a name="view-and-manage-activity-log-alert-rules"></a>Weergeven en beheren van de activiteit logboek waarschuwingsregels

1. Klik vanuit Azure-portal op **Monitor** > **waarschuwingen** en klik op **regels beheren** op linksboven in het venster.

    ![ regels voor waarschuwingen beheren](./media/monitoring-activity-log-alerts-new-experience/manage-alert-rules.png)

    De lijst met beschikbare regels wordt weergegeven.

2. Zoek de regel voor het logboek van activiteit wilt wijzigen.

    ![ activiteit logboek waarschuwingsregels zoeken](./media/monitoring-activity-log-alerts-new-experience/searth-activity-log-rule-to-edit.png)

    U kunt de beschikbare filters - **abonnement**, **resourcegroep**, of **Resource** vinden van de activiteit regel die u wilt bewerken.

    > [!NOTE]

    > U kunt alleen bewerken **beschrijving** , **criteria als doel** en **actiegroepen**.

3.  Selecteer de regel en dubbelklik om te bewerken van de opties voor de regel. De vereiste wijzigingen aanbrengen en klik vervolgens op **opslaan**.

    ![ regels voor waarschuwingen beheren](./media/monitoring-activity-log-alerts-new-experience/activity-log-rule-edit-page.png)

4.  U kunt uitschakelen, inschakelen of verwijderen van een regel. Selecteer de relevante optie boven aan het venster nadat u de regel zoals beschreven in stap 2 hebt geselecteerd.


## <a name="next-steps"></a>Volgende stappen

- [Activiteit logboek waarschuwingen archiveren](monitoring-archive-activity-log.md)
- [Stroom activiteitenlogboeken naar Event hubs](monitoring-stream-activity-logs-event-hubs.md)
- [Migreren naar activiteitenlogboeken](monitoring-migrate-management-alerts.md)
