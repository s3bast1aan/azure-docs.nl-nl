---
title: Een toegangscontrole uitvoeren in Privileged Identity Management voor Azure-resources | Microsoft Docs
description: Dit document wordt beschreven hoe u een toegangscontrole uitvoeren in PIM voor Azure-resources op basis van de resourcerol van de.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 336ad1d7eff8eee0f2370e206eb98f20b4efb45b
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617221"
---
# <a name="perform-an-access-review-in-pim-according-to-resource-role"></a>Een toegangscontrole uitvoeren in PIM, op basis van de resourcerol van de
Privileged Identity Management (PIM) voor Azure-resources vereenvoudigt de manier waarop ondernemingen bevoegde toegang tot resources in Azure beheren. 

Als u aan een beheerdersrol toegewezen bent, kan beheerder met bevoorrechte rol van uw organisatie u vragen om regelmatig bevestigen dat u die rol nog voor de taak moet. U kunt een e-mailbericht een koppeling bevat krijgen of gaat u rechtstreeks naar de [Azure-portal](https://portal.azure.com). Volg de stappen in dit artikel om uit te voeren met een zelf bekijken van de toegewezen rollen.

Als u een beheerder met bevoorrechte rol toegangsbeoordelingen geïnteresseerd bent, meer details op ophalen [een toegangsbeoordeling starten](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>De Privileged Identity Management-toepassing toevoegen
U kunt de Azure Active Directory (Azure AD) PIM-toepassing in de [Azure-portal](https://portal.azure.com/) om uit te voeren van uw beoordeling. Als u de toepassing in de portal hebt, volgt u deze stappen om te beginnen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer uw gebruiker in de rechterbovenhoek van de Azure-portal de naam en selecteer de map waar u u wilt worden uitgevoerd.
3. Selecteer **alle services**, en gebruik de **Filter** om te zoeken naar *Azure AD Privileged Identity Management*.
4. Controleer **vastmaken aan dashboard**, en selecteer vervolgens **maken**. De PIM-toepassing wordt geopend.

## <a name="approve-or-deny-access"></a>Goedkeuren of weigeren van toegang
Wanneer u goedkeuren of weigeren van toegang, u bent slechts zorgt ervoor dat de revisor of u nog steeds gebruik van deze rol of niet. Kies **goedkeuren** als u wilt blijven in de rol of **weigeren** als u de toegang niet meer nodig. De status verandert alleen wanneer de revisor van de toepassing de resultaten.

Volg deze stappen om te zoeken en de toegangsbeoordeling voltooien:
1. Blader naar de Azure AD PIM-toepassing.
2. Selecteer de **toegang beoordelen** blade.

   ![Schermafbeelding van de PIM-toepassing, met controle toegang blade geselecteerd](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Selecteer de beoordeling die u wilt uitvoeren. 
4. Kies een **goedkeuren** of **weigeren**. In de **een vak reden**, moet u mogelijk een reden op voor uw beslissing nemen.

   ![Pagina met details van schermafbeelding van de beoordeling](media/azure-pim-resource-rbac/rbac-access-review-choice.png)
