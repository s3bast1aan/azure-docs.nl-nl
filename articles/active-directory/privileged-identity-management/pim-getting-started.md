---
title: Aan de slag met Azure AD Privileged Identity Management | Microsoft Docs
description: Informatie over het beheren van bevoegde identiteiten met de Azure Active Directory Privileged Identity Management-toepassing in de Azure Portal.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 4eeb58e6e7377c2c0ec7db850a84bf1e296500d2
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39623048"
---
# <a name="start-using-azure-ad-privileged-identity-management"></a>Aan de slag met Azure AD Privileged Identity Management

Met Azure Active Directory (AD) Privileged Identity Management kunt u toegang binnen de organisatie beheren, controleren en bewaken. Dit bereik is inclusief toegang tot Azure-resources, Azure AD en andere Microsoft-onlineservices, zoals Office 365 en Microsoft Intune.

In dit artikel leest u hoe u de PIM-app van Azure AD toevoegt aan uw Azure Portal-dashboard.

## <a name="add-the-privileged-identity-management-application"></a>De Privileged Identity Management-toepassing toevoegen

Voordat u Azure AD Privileged Identity Management gebruikt, moet u de toepassing toevoegen aan uw Azure Portal-dashboard.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) als globale beheerder van uw directory.
2. Als uw organisatie meerdere directory's heeft, selecteert u uw gebruikersnaam rechtsboven in de Azure Portal. Selecteer de map waar u PIM gaat gebruiken.
3. Selecteer **Alle services** en gebruik het tekstvak Filteren om te zoeken naar **Azure AD Privileged Identity Management**.
4. Schakel **Vastmaken aan dashboard** in en klik op de knop **Maken**. De Privileged Identity Management-toepassing wordt geopend.

Als u de eerste persoon bent die het gebruik van Azure AD Privileged Identity Management in uw map gebruikt, krijgt u automatisch de wollen **Beveiligingsbeheerder** en **Beheerder met bevoegdheid** toegewezen in de map. Alleen beheerders met bevoegdheid kunnen directory-roltoewijzingen van gebruikers in Azure AD beheren. Bovendien kunt u de [beveiligingswizard](pim-security-wizard.md) uitvoeren. Deze helpt u bij de eerste ervaring met detectie en toewijzing.

## <a name="navigate-to-your-tasks"></a>Navigeer naar uw taken

Nadat Azure AD Privileged Identity Management is ingesteld, ziet u de navigatieblade telkens wanneer u de toepassing opent. Gebruik deze blade om de taken voor identiteitsbeheer te voltooien.

![Taken op het hoogste niveau voor PIM - schermopname](./media/pim-getting-started/PIM_Tasks_New.png)

- **Mijn rollen**: een lijst met rollen van in aanmerking komende en actieve rollen die aan u zijn toegewezen. Hier kunt u alle in aanmerking komende toegewezen rollen activeren.
- **Aanvragen goedkeuren (preview)**: een lijst met aanvragen voor het activeren van in aanmerking komende Azure AD-directory-rollen door gebruikers in uw directory, die u hebt aangewezen om goed te keuren. [Meer informatie.](./azure-ad-pim-approval-workflow.md)
- **Aanvragen in behandeling (preview)**: alle in behandeling genomen aanvragen voor het activeren van in aanmerking komende roltoewijzingen.
- **Toegang beoordelen**: actieve toegangsbeoordelingen die u hebt toegewezen om te voltooien, ongeacht of u de toegang beoordeelt voor uzelf of iemand anders.
- **Azure AD-directory-rollen**: bevindt zich onder de sectie Beheren van het linkernavigatiemenu en bevat het dashboard voor beheerders met bevoorrechte rollen. Hiermee kunnen ze roltoewijzingen beheren, instellingen voor rolactivering wijzigen, toegangsbeoordelingen starten en meer. Dit dashboard is uitgeschakeld voor iedereen die geen beheerder met een bevoorrechte rol is. Deze gebruikers hebben toegang tot een speciaal dashboard met de titel Mijn weergave. Het dashboard Mijn weergave bevat alleen informatie over de gebruiker die toegang heeft tot het dashboard, niet de gehele tenant.
- **Azure Resource-rollen (preview)**: bevindt zich onder de sectie Beheren van het linkernavigatiemenu en bevat een lijst van abonnementsresources waarvoor u roltoewijzingen kunt kiezen 

## <a name="next-steps"></a>Volgende stappen
Het [overzicht van Azure AD Privileged Identity Management](pim-configure.md) bevat meer informatie over het beheren van beheerderstoegang in uw organisatie.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
