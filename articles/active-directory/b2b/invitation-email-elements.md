---
title: De elementen van de uitnodigingsmail B2B-samenwerking - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B-samenwerking uitnodiging voor een e-mailsjabloon
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: e8285779154914bd09513c057d8e5ae0b6388831
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260028"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>De elementen van Azure Active Directory een uitnodiging voor e-mail in B2B - samenwerking

Een uitnodiging voor e-mailberichten zijn een essentieel onderdeel op te zetten van partners aan boord als B2B-samenwerking gebruikers in Azure AD. U kunt deze gebruiken om te verhogen van de geadresseerde vertrouwensrelatie. u kunt legitimiteit toevoegen en sociale bewijs in de e-mail om te controleren of de ontvanger werkt bekendheid met het selecteren van de **aan de slag** knop de uitnodiging te accepteren. Deze vertrouwensrelatie is dat een sleutel betekent dat u wilt delen wrijving verminderen. En u wilt er ook voor het e-mailadres er fantastisch uitzien!

![Azure AD B2b-uitnodigingsmail](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Het e-mailbericht waarin wordt uitgelegd
Bekijk enkele elementen van het e-mailadres zodat u hoe het beste weet hun mogelijkheden gebruiken.

### <a name="subject"></a>Onderwerp
Het onderwerp van het e-mailbericht volgt u de volgende notatie: U bent uitgenodigd voor de &lt;tenantname&gt; organisatie

### <a name="from-address"></a>Van adres
We gebruiken een LinkedIn patroon voor het adres van de afzender.  U moet wissen die afzender van de uitnodiging is en waaruit de bedrijfsportal en ook verduidelijken dat het e-mailbericht afkomstig is van een Microsoft e-mailadres. De indeling is: &lt;weergavenaam van de uitnodiging antwoorden&gt; van &lt;tenantname&gt; (via Microsoft) <invites@microsoft.com>

### <a name="reply-to"></a>Antwoorden op
Het antwoord aan e-mailbericht is ingesteld op van de afzender van de uitnodiging e indien beschikbaar, zodat u het e-mailbericht beantwoordt een e-mailbericht teruggestuurd naar de uitnodiging antwoorden wordt.

### <a name="branding"></a>De huisstijl
De uitnodiging voor een e-mailberichten van uw gebruik van de tenant de huisstijl die u mogelijk hebt ingesteld voor uw tenant. Als u wilt profiteren van deze mogelijkheid [hier](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) vindt u de details over het configureren van deze. Het logo in banner wordt weergegeven in het e-mailbericht. Voer de grootte van de installatiekopie en kwaliteit instructies [hier](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) voor de beste resultaten. Bovendien de bedrijfsnaam wordt ook weergegeven in de aanroep aan de actie.

### <a name="call-to-action"></a>Aanroep van bewerking
De aanroep van bewerking bestaat uit twee delen: waarin wordt uitgelegd waarom de ontvanger het e-mailbericht heeft ontvangen en wat de ontvanger is wordt gevraagd om u te doen.
- De sectie 'Waarom' kan worden aangepakt met behulp van het volgende patroon volgen: U bent uitgenodigd voor toegang tot toepassingen in de &lt;tenantname&gt; organisatie

- En de 'wat u wordt gevraagd om te doen' sectie wordt aangegeven door de aanwezigheid van de **aan de slag** knop. Als de ontvanger zonder de noodzaak van uitnodigingen is toegevoegd, weergegeven deze knop niet.

### <a name="inviters-information"></a>De uitnodiging antwoorden informatie
Weergavenaam van de afzender van de uitnodiging is opgenomen in het e-mailbericht. En daarnaast als u een profielfoto voor uw Azure AD-account hebt ingesteld, het uitnodigen e-mailbericht omvatten ook afbeelding. Beide zijn bedoeld om meer vertrouwen in de e-mail van de geadresseerde.

Als u nog uw profielfoto niet hebt ingesteld, wordt een pictogram met de initialen van de afzender van de uitnodiging in plaats van de afbeelding wordt weergegeven:

  ![Initialen van de afzender van de uitnodiging weergeven](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>Hoofdtekst
De hoofdtekst van bevat het bericht dat de afzender van de uitnodiging stelt het bericht of wordt doorgegeven aan de uitnodiging API. Het is een tekstgebied, zodat deze geen HTML-tags uit veiligheidsoverwegingen verwerkt.

### <a name="footer-section"></a>Voettekstsectie
De voettekst bevat van de Microsoft-bedrijfsmerk en stelt de ontvanger weten of het e-mailbericht is verzonden vanaf een niet-bewaakte alias. Speciale gevallen:

- De uitnodiging antwoorden geen een e-mailadres in de uitnodiging tenancymodus

  ![afbeelding van uitnodiging antwoorden geen een e-mailadres in de uitnodiging tenancymodus](media/invitation-email-elements/inviter-no-email.png)


- De ontvanger hoeft niet te nemen van de uitnodiging

  ![Wanneer de ontvanger niet hoeft te uitnodiging inwisselen](media/invitation-email-elements/when-recipient-doesnt-redeem.png)


## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen over Azure AD B2B-samenwerking:

- [Wat is Azure AD B2B-samenwerking](what-is-b2b.md)
- [Hoe voeg beheerders van Azure Active Directory B2B-samenwerking gebruikers?](add-users-administrator.md)
- [Hoe kunnen IT-medewerkers B2B-samenwerking gebruikers toevoegen](add-users-information-worker.md)
- [B2B-samenwerking uitnodiging inwisseling](redemption-experience.md)
- [B2B-samenwerking gebruikers zonder een uitnodiging toevoegen](add-user-without-invite.md)
