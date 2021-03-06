---
title: Azure Active Directory Application Proxy en Tableau | Microsoft Docs
description: Informatie over het gebruik van Azure Active Directory (Azure AD) Application Proxy om externe toegang voor uw implementatie Tableau te bieden.  .
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 7cace1af527c1c7c80bf0e23f7a88aa9ac9f9d03
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365019"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory Application Proxy en Tableau 

Azure Active Directory Application Proxy en Tableau werken samen om te controleren of dat u zich eenvoudig kunt toepassingsproxy gebruiken voor externe toegang voor uw implementatie Tableau. In dit artikel wordt uitgelegd hoe u dit scenario configureert.  

## <a name="prerequisites"></a>Vereisten 

Het scenario in dit artikel wordt ervan uitgegaan dat u hebt:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) geconfigureerd. 

- Een [Application Proxy-connector](application-proxy-enable.md) geïnstalleerd. 

 

## <a name="enabling-application-proxy-for-tableau"></a>Toepassingsproxy voor Tableau wordt ingeschakeld 

Als u gebruiken van de toepassingsproxy voor Tableau wilt, moet u een e-mail verzenden naar [ aadapfeedback@microsoft.com ](mailto:aadapfeedback@microsoft.com) om op te halen van dit scenario is ingeschakeld.
In uw e-mailadres:

-   Toepassingsproxy inschakelen voor Tableau gebruiken als onderwerp
-   Uw tenant-ID opnemen in de hoofdtekst    

U krijgt een bevestiging wanneer u klaar bent voor het gebruik van de toepassing. U kunt de configuraties voltooien tijdens het wachten op voor de bevestiging.


 

## <a name="publish-your-applications-in-azure"></a>Uw toepassingen publiceren in Azure 

Als u wilt publiceren Tableau, moet u een toepassing publiceren in Azure Portal.

Voor:

- Gedetailleerde instructies van de stappen 1-8, Zie [toepassingen publiceren die gebruikmaken van Azure AD-toepassingsproxy](application-proxy-publish-azure-portal.md). 
- Informatie over hoe u Tableau om waarden te vinden voor de velden van de App-Proxy, Zie de documentatie Tableau.  

**Uw app publiceren**: 


1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder. 

2. Selecteer **Azure Active Directory > bedrijfstoepassingen**. 

3. Selecteer **toevoegen** aan de bovenkant van de blade. 

4. Selecteer **On-premises toepassing**. 

5. Vul de vereiste velden met informatie over de nieuwe app. Gebruik de volgende richtlijnen voor de instellingen: 

    - **Interne URL**: deze toepassing moet een interne URL de URL is Tableau zelf. Bijvoorbeeld `https://adventure-works.tableau.com`. 

    - **Methode voor verificatie vooraf**: Azure Active Directory (aanbevolen maar niet vereist). 

6. Selecteer **toevoegen** aan de bovenkant van de blade. Uw toepassing wordt toegevoegd en het menu Snel starten wordt geopend. 

7. Selecteer in het menu snelle start **een gebruiker toewijzen voor het testen van**, en ten minste één gebruiker toevoegen aan de toepassing. Zorg ervoor dat deze testaccount toegang heeft tot de on-premises toepassing. 

8. Selecteer **toewijzen** om op te slaan van de toewijzing van de gebruiker testen. 

9. (Optioneel) Selecteer op de pagina app-beheer **eenmalige aanmelding**. Kies **geïntegreerde Windows-verificatie** uit de vervolgkeuzelijst en vul de vereiste velden in op basis van uw configuratie Tableau. Selecteer **Opslaan**. 

 

## <a name="testing"></a>Testen 

Uw toepassing is nu gereed om te testen. Toegang tot de externe URL die u gebruikt voor het Tableau, publiceren en meld u aan als een gebruiker die is toegewezen aan beide toepassingen.



## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure AD-toepassingsproxy [het bieden van veilige externe toegang tot on-premises toepassingen](application-proxy.md).

