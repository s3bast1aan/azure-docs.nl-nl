---
title: Azure Active Directory-implementatieplannen | Microsoft Docs
description: Deze bieden end-to-end-richtlijnen over het implementeren van de mogelijkheden van Azure Active Directory
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: overview
ms.date: 08/03/2018
ms.author: lizross
ms.openlocfilehash: 07d3915fd007c0827b885b0603eb176b9e408576
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39508359"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory-implementatieplannen
Bent u op zoek naar end-to-end-richtlijnen over het implementeren van sommige mogelijkheden van Azure Active Directory (Azure AD)? De volgende implementatieplannen geven richtlijnen bij de vereiste bedrijfswaarde, planningsoverwegingen, het ontwerp en de operationele procedures voor de implementatie van enkele algemene mogelijkheden van Azure AD. 

In de documenten vindt u e-mailsjablonen, diagrammen met systeemarchitectuur, algemene testscenario's en meer. 

Uw feedback over de documenten wordt op prijs gesteld. Vul deze korte [enquête](https://aka.ms/deploymentplanfeedback) in verband met de documenten in. 

|Scenario |Beschrijving |
|-|-|
|[Eenmalige aanmelding](https://aka.ms/SSODPDownload)|Met eenmalige aanmelding kunt u alle apps en resources openen die u voor uw bedrijf nodig hebt. U hoeft u slechts eenmaal aan te melden met één gebruikersaccount. Nadat u zich hebt aangemeld, gaat u vanuit Microsoft Office naar SalesForce en naar Box zonder dat u zich een tweede keer hoeft te verifiëren (bijvoorbeeld door een wachtwoord te typen).|
|[Inrichten van gebruikers](https://aka.ms/UserProvisioningDPDownload)|Met Azure AD kunt u het maken, onderhouden en verwijderen van gebruikers-id's in cloud(SaaS)-toepassingen als Dropbox, Salesforce, ServiceNow en andere, automatiseren.|
|[Multi-Factor Authentication](https://aka.ms/MFADPDownload)|Azure Multi-Factor Authentication (MFA) is een Microsoft-oplossing voor verificatie in twee stappen. Met behulp van door een beheerder goedgekeurde verificatiemethoden, helpt Azure MFA met het beveiligen van gegevens en toepassingen, terwijl tegelijkertijd aan de behoefte aan een eenvoudige aanmeldingsprocedure wordt voldaan.|
|[Voorwaardelijke toegang](https://aka.ms/CADPDownload)|Met voorwaardelijke toegang kunt u geautomatiseerde beslissingen voor toegangsbeheer implementeren voor wie toegang heeft tot uw cloud-apps op basis van voorwaarden.|
|[ADFS voor Pass Through-verificatie](https://aka.ms/ADFSTOPTADPDownload)|Met Azure AD Pass Through-verificatie kunnen gebruikers zich met hetzelfde wachtwoord aanmelden bij zowel on-premises als cloudtoepassingen. Deze functie biedt een betere gebruikerservaring (een wachtwoord minder te onthouden) en het vermindert de kosten voor de IT-helpdesk omdat gebruikers minder vaak een wachtwoord vergeten. Als iemand zich aanmeldt bij Azure AD, worden met deze functie de wachtwoorden rechtstreeks gecontroleerd tegen de on-premises Active Directory.|
|[ADFS voor wachtwoordhash-synchronisatie](https://aka.ms/ADFSTOPHSDPDownload)|Met wachtwoordhash-synchronisatie worden hashes van gebruikerswachtwoorden vanuit on-premises Active Directory gesynchroniseerd met Azure AD, waarbij Azure AD gebruikers verifieert zonder interactie met de on-premises Active Directory|
|[Naadloze eenmalige aanmelding](https://aka.ms/SeamlessSSODPDownload)|Met Naadloze eenmalige aanmelding van Azure Active Directory (Naadloze SSO van Azure AD) worden gebruikers aangemeld als hun bedrijfsapparaten zijn verbonden met net bedrijfsnetwerk. Als deze functie is ingeschakeld, hoeft de gebruiker geen wachtwoord in te typen om zich bij Azure AD aan te melden. Zelfs de gebruikersnaam hoeft niet te worden ingetypt. Deze functie biedt een gebruiker eenvoudig toegang tot cloudtoepassingen zonder dat er aanvullende on-premises onderdelen nodig zijn.|
|[Self-service voor wachtwoord opnieuw instellen](https://aka.ms/SSPRDPDownload)|De selfservice voor het opnieuw instellen van wachtwoorden helpt uw gebruikers overal en altijd hun wachtwoord opnieuw in te stellen zonder dat er een beheerder aan te pas komt.|
|[Azure AD-toepassingsproxy](https://aka.ms/AppProxyDPDownload)|De huidige werknemer wil overal, op elke plek en op elk apparaat productief kunnen zijn. Hij of zij wil op het eigen apparaat werken, of dat nu een tablet, telefoon of laptop is. En een werknemer verwacht dat hij toegang heeft tot al zijn toepassingen, zowel SaaS-apps in cloud als on-premises, zakelijke apps. Voorheen was bij het toegang bieden tot on-premises toepassingen een VPN (Virtual Private Network) of een DMZ (gedemilitariseerde zone) nodig. Dit is niet alleen ingewikkeld en moeilijk te beveiligen, maar het instellen en beheren ervan is duur. Er is een betere manier. - Azure AD-toepassingsproxy|
