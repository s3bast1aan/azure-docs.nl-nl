---
title: SaaS-apps voor B2B-samenwerking configureren in Azure Active Directory | Microsoft Docs
description: Voorbeelden van code en PowerShell voor Azure Active Directory B2B-samenwerking
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: f0f291216a3031d50d304c02b97786f23d1a6267
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267414"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>SaaS-apps voor B2B-samenwerking configureren

Azure Active Directory (Azure AD) B2B-samenwerking werkt met de meeste apps die zijn geïntegreerd met Azure AD. In deze sectie doorlopen we instructies voor het configureren van een aantal populaire SaaS-apps voor gebruik met Azure AD B2B.

Voordat u de instructies van de app-specifiek bekijkt, hier een aantal regels van miniatuur te volgen:

* Voor de meeste van de apps moet gebruikersinstellingen handmatig gebeuren. Dat wil zeggen, moeten gebruikers handmatig in de app ook worden gemaakt.

* Voor apps die ondersteuning bieden voor automatische installatie, zoals Dropbox, worden afzonderlijke uitnodigingen gemaakt vanuit apps. Gebruikers moeten ervoor dat elke uitnodiging te accepteren.

* In de kenmerken van de gebruiker om te beperken eventuele problemen met vervormde gebruikersprofielschijf (UDP) in gastgebruikers, altijd ingesteld **gebruikers-id** naar **user.mail**.


## <a name="dropbox-business"></a>Dropbox Business

Om gebruikers aan te melden met hun organisatie, moet u handmatig Business Dropbox voor het gebruik van Azure AD als een id-provider Security Assertion Markup Language (SAML) configureren. Als Dropbox bedrijven is niet geconfigureerd om dit te doen, kan deze vragen of anders kunnen gebruikers zich aanmelden bij het gebruik van Azure AD.

1. Selecteer om de Dropbox-Business-app met Azure AD toe **bedrijfstoepassingen** in het linkerdeelvenster en klik vervolgens op **toevoegen**.

  ![De knop "Toevoegen" op de pagina van de Enterprise-toepassingen](media/configure-saas-apps/add-dropbox.png)

2. In de **een toepassing toevoegen** venster invoeren **dropbox** in het zoekvak en selecteer vervolgens **Dropbox voor bedrijven** in de lijst met resultaten.

  ![Zoeken naar 'dropbox' op het toevoegen van een toepassingspagina](media/configure-saas-apps/add-app-dialog.png)

3. Op de **eenmalige aanmelding** pagina **eenmalige aanmelding** in het linkerdeelvenster en voer vervolgens **user.mail** in de **gebruikers-id** vak. (Het ingesteld als de UPN standaard.)

  ![Eenmalige aanmelding voor de app configureren](media/configure-saas-apps/configure-app-sso.png)

4. Selecteer voor het downloaden van het certificaat moet worden gebruikt voor de configuratie van Dropbox, **DropBox configureren**, en selecteer vervolgens **SAML aanmelding op Service-URL met eenmalige** in de lijst.

  ![Downloaden van het certificaat voor Dropbox-configuratie](media/configure-saas-apps/download-certificate.png)

5. Aanmelden bij Dropbox met de aanmeldings-URL van de **eenmalige aanmelding** pagina.

  ![De aanmeldingspagina Dropbox](media/configure-saas-apps/sign-in-to-dropbox.png)

6. Selecteer in het menu **beheerconsole**.

  ![De koppeling 'Admin Console' in het menu Dropbox](media/configure-saas-apps/dropbox-menu.png)

7. In de **verificatie** dialoogvenster, **meer**, upload het certificaat en klik vervolgens op de **aanmelden URL** Voer het SAML één aanmeldings-URL.

  ![De koppeling 'Meer' in het dialoogvenster voor samengevouwen verificatie](media/configure-saas-apps/dropbox-auth-01.png)

  ![De 'aanmelden URL' in het dialoogvenster voor uitgebreide verificatie](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Voor het configureren van automatische gebruikersinstellingen in de Azure portal, selecteer **inrichten** selecteren in het linkerdeelvenster **automatische** in de **modus inrichting** vak en selecteer vervolgens **autoriseren**.

  ![Automatisch gebruikers inrichten in de Azure portal configureren](media/configure-saas-apps/set-up-automatic-provisioning.png)

Nadat de gast of lid gebruikers zijn ingesteld in de app Dropbox, ontvangen ze een uitnodiging voor een afzonderlijke van Dropbox. Voor het gebruik van eenmalige aanmelding Dropbox, moeten de genodigden de uitnodiging accepteren door te klikken op een koppeling in het.

## <a name="box"></a>Box
U kunt gebruikers vak Gast om gebruikers te verifiëren met hun Azure AD-account met behulp van de federatieserver die gebaseerd op het SAML-protocol. In deze procedure kunt u metagegevens uploaden naar Box.com.

1. De Box-app uit de zakelijke apps toevoegen.

2. Eenmalige aanmelding configureren in de volgende volgorde:

  ![Eenmalige aanmelding vak configureren](media/configure-saas-apps/configure-box-sso.png)

 a. In de **aanmelden URL** Zorg dat de aanmeldings-URL juist is ingesteld voor het selectievakje in de Azure portal. Deze URL is de URL van uw tenant Box.com. Deze naam moet voldoen aan de naamgevingsconventie *https://.box.com*.  
 De **id** geldt niet voor deze app maar nog steeds wordt weergegeven als een verplicht veld.

 b. In de **gebruikers-id** Voer **user.mail** (voor eenmalige aanmelding voor gastaccounts).

 c. Onder **SAML-certificaat voor ondertekening van**, klikt u op **nieuw certificaat maken**.

 d. Om te beginnen met het configureren van uw tenant Box.com voor het gebruik van Azure AD als id-provider, downloadt u het metagegevensbestand en vervolgens opslaan in de lokale schijf.

 e. Het metagegevensbestand aan het vak ondersteuning doorsturen team eenmalige aanmelding voor u te configureren.

3. Selecteer voor Azure AD automatische gebruikersinstellingen, in het linkerdeelvenster **inrichten**, en selecteer vervolgens **autoriseren**.

  ![Azure AD verbinding maken met het selectievakje toestaan](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Zoals Dropbox genodigden, moeten het selectievakje genodigden hun uitnodiging van de app vak inwisselen.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen over Azure AD B2B-samenwerking:

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [Dynamische groepen en B2B-samenwerking](use-dynamic-groups.md)
- [Gebruikersclaims voor B2B-samenwerking toewijzing](claims-mapping.md)
- [Office 365 extern delen](o365-external-user.md)

