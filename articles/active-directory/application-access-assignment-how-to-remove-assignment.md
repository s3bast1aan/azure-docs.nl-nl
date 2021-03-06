---
title: Het verwijderen van een gebruiker toegang tot een toepassing | Microsoft Docs
description: Meer informatie over het verwijderen van een gebruiker toegang tot een toepassing
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 0deb5215c1379ac552a492f4b9e90df83201aebf
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364478"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Het verwijderen van een gebruiker toegang tot een toepassing

In dit artikel helpt u te begrijpen hoe u van een gebruiker toegang tot een toepassing te verwijderen.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Ik wil een specifieke gebruiker of groep van toewijzing aan een toepassing verwijderen

Als u wilt verwijderen van een gebruiker of groepstoewijzing aan een toepassing, volgt u de stappen in de [de toewijzing van een gebruiker of groep verwijderen uit een enterprise-app in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artikel.

. ## ik wil alle toegang tot een toepassing voor elke gebruiker uitschakelen

Als u wilt alle gebruikers zich aangemeld bij een toepassing uitschakelen, volgt u de stappen in de [gebruikersaanmeldingen voor een enterprise-app in Azure Active Directory uitschakelen](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) artikel.

## <a name="i-want-to-delete-an-application-entirely"></a>Ik wil een toepassing volledig verwijderen

Naar **verwijderen van een toepassing**, volgt u deze instructies:

1.  Open de [ **Azure-portal** ](https://portal.azure.com/) en meld u aan als een **hoofdbeheerder** of **Co-beheerder.**

2.  Open de **Azure Active Directory-extensie** door te klikken op **alle services** aan de bovenkant van het menu links hoofdgedeelte voor navigatie.

3.  Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4.  Klik op **bedrijfstoepassingen** in het navigatiemenu aan Azure Active Directory.

5.  Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weergeven die hier niet ziet, gebruikt u de **Filter** besturingselement aan de bovenkant van de **lijst met alle toepassingen** en stel de **weergeven** optie naar **alle Toepassingen.**

6.  Selecteer de toepassing die u wilt verwijderen.

7.  Nadat de toepassing wordt geladen, klikt u op **verwijderen** pictogram in de bovenste toepassing **overzicht** deelvenster.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Ik wil alle bewerkingen van toekomstige gebruikers toestemming voor elke toepassing uitschakelen

Toestemming van de gebruiker wordt uitgeschakeld voor uw hele directory te voorkomen dat eindgebruikers ermee akkoord dat voor elke toepassing. Beheerders kunnen nog steeds op van de gebruiker behalves instemmen. Voor meer informatie over de toestemming van de toepassing en waarom u niet wenst dat mogelijk dit wilt doen of lezen [inzicht in gebruikers- en toestemming van een beheerder](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

Naar **uitschakelen van alle toekomstige gebruikers toestemming bewerkingen in uw hele directory**, volgt u deze instructies:

1.  Open de [ **Azure-portal** ](https://portal.azure.com/) en meld u aan als een **globale beheerder.**

2.  Open de **Azure Active Directory-extensie** door te klikken op **alle services** aan de bovenkant van het menu links hoofdgedeelte voor navigatie.

3.  Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatiemenu.

5.  Klik op **gebruikersinstellingen**.

6.  Alle toekomstige gebruikers toestemming bewerkingen uitschakelen door in te stellen de **gebruikers kunnen toestaan dat apps toegang tot hun gegevens** overzet naar **Nee** en klikt u op de **opslaan** knop.


# <a name="next-steps"></a>Volgende stappen
[Toegang tot apps beheren](manage-apps/what-is-access-management.md)
