---
title: Basisbeginselen van Azure Stack Development Kit | Microsoft Docs
description: Beschrijft hoe algemene beheertaken uitvoeren voor Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: fa2ee4df9a0a28c08237f30fcf6a4bac50c21100
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849505"
---
# <a name="asdk-administration-basics"></a>Basisprincipes van beheer ASDK 
Er zijn verschillende dingen die u weten moet als u geen ervaring met beheer van Azure Stack Development Kit (ASDK). In deze richtlijnen biedt een overzicht van uw rol als een Azure-Stack-operator in de evaluatieomgeving en het waarborgen van uw testgebruikers snel productief kunt worden.

Eerst moet u nagaan de [wat is Azure Stack Development Kit?](asdk-what-is.md) artikel om ervoor te zorgen dat u het doel van de ASDK en de beperkingen begrijpt. U moet de development kit gebruiken als een 'sandbox', waarin u Azure-Stack te ontwikkelen en testen van uw apps in een niet-productieomgeving kunt evalueren. 

Zoals Azure innovates Azure Stack snel zodat we je regelmatig nieuwe versies van de ASDK release. U kunt de ASDK echter niet upgraden zoals u Azure-Stack geïntegreerd systemen implementaties kunt. Dus als u verplaatsen naar de laatste build wilt, moet u volledig [opnieuw implementeren van de ASDK](asdk-redeploy.md). U kunt geen updatepakketten toepassen. Dit proces duurt, maar het voordeel is kunt u de nieuwste functies uitproberen zodra deze beschikbaar komen. 

## <a name="what-account-should-i-use"></a>Welk account moet ik gebruiken?
Er zijn enkele aandachtspunten voor gebruikersaccounts die u houden moet rekening bij het beheren van Azure-Stack. Met name in implementaties met behulp van Windows Server Active Directory Federation Services (AD FS) als de id-provider in plaats van Azure Active Directory (Azure AD). De volgende aandachtspunten voor gebruikersaccounts van toepassing op Azure-Stack geïntegreerd systemen en ASDK implementaties:

|Account|Azure AD|AD FS|
|-----|-----|-----|
|Lokale beheerder (. \Administrator)|ASDK host beheerder|ASDK host beheerder|
|AzureStack\AzureStackAdmin|ASDK host beheerder<br><br>Kan worden gebruikt voor aanmelding bij de Azure-Stack-beheerportal<br><br>Toegang tot bekijken en beheren van Service Fabric-ringen|ASDK host beheerder<br><br>Geen toegang tot de Stack van Azure-beheerportal<br><br>Toegang tot bekijken en beheren van Service Fabric-ringen<br><br>Niet langer eigenaar van de standaard Provider abonnement (DP's)|
|AzureStack\CloudAdmin|Toegang tot en toegestane opdrachten binnen het bevoegde eindpunt uitvoeren|Toegang tot en toegestane opdrachten binnen het bevoegde eindpunt uitvoeren<br><br>Kan niet aanmelden bij de host ASDK<br><br>Eigenaar van de Provider standaardabonnement (DP's)|
|Globale beheerder van Azure AD|Tijdens de installatie gebruikt<br><br>Eigenaar van de Provider standaardabonnement (DP's)|Niet van toepassing|
|

## <a name="what-tools-do-i-use-to-manage"></a>Welke hulpprogramma's kan ik gebruiken om te beheren?
U kunt de [Azure Stack-Beheerdersportal](https://adminportal.local.azurestack.external) of PowerShell voor het beheren van Azure-Stack. De eenvoudigste manier om meer informatie over de basisconcepten is via de portal. Als u PowerShell gebruiken wilt, moet u voor het installeren van [PowerShell voor Azure-Stack](asdk-post-deploy.md#install-azure-stack-powershell) en [downloaden van de Azure-Stack-hulpprogramma's van GitHub](asdk-post-deploy.md#download-the-azure-stack-tools).

Azure Stack maakt gebruik van Azure Resource Manager als de onderliggende implementatie, beheer en organisatie-mechanisme. Als u Azure-Stack beheren en ondersteuning aan gebruikers, moet u meer informatie over Azure Resource Manager. U kunt meer informatie de [aan de slag met Azure Resource Manager technisch document](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Uw gebruikelijke verantwoordelijkheden
Gebruikers willen services gebruiken. Uw belangrijkste functie is deze services hen ter beschikking vanuit het perspectief. Met behulp van de ASDK, Ontdek welke services te bieden, en hoe moet u deze services beschikbaar door [plannen, aanbiedingen en quota's maken](asdk-offer-services.md). U moet ook items toevoegen aan de marketplace, zoals installatiekopieën van virtuele machines. De eenvoudigste manier is het [marketplace-items downloaden](asdk-marketplace-item.md) van Azure naar de Azure-Stack.

> [!NOTE]
> Als u testen van uw abonnementen, aanbiedingen en services wilt, moet u de [gebruikersportal](https://portal.local.azurestack.external); niet de [beheerdersportal](https://adminportal.local.azurestack.external).

Naast het leveren van services, moet u alle normale taken van een Azure-Stack-Operator te houden van de ASDK actief en werkend uitvoeren. Deze rechten zijn bijvoorbeeld het volgende:
- Gebruikersaccounts toevoegen voor Azure Active Directory (Azure AD) of Active Directory Federation Services (AD FS) implementaties.
- Toegangsbeheer (RBAC)-rollen (dit is niet beperkt tot alleen beheerders) voor toegang op basis van rollen toewijzen
- Status van de groepsbeleidstructuur controleren
- Netwerk-en opslagbronnen te beheren
- Mislukte development kit host computerhardware vervangen 

## <a name="where-to-get-support"></a>Waar u ondersteuning krijgen
Voor de development kit is het de enige ondersteuningsoptie te vragen met betrekking hebben op ondersteuning in de [Microsoft Azure-Stack-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Als u klikt u op het pictogram Help en ondersteuning (vraagteken) in de rechterbovenhoek van de beheerdersportal en klik vervolgens op **nieuw ondersteuningsverzoek**, Hiermee opent u de site forums rechtstreeks. Deze forums worden regelmatig bewaakt. 

> [!IMPORTANT]
> Omdat de ASDK een evaluatieomgeving is, is geen officiële ondersteuning wordt aangeboden via Microsoft Customer ondersteunen Services (CSS).

## <a name="next-steps"></a>Volgende stappen
[De ASDK implementeren](asdk-install.md)

