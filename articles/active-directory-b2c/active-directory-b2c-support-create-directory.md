---
title: Oplossen van het maken van het tenants in Azure Active Directory B2C | Microsoft Docs
description: Problemen en oplossingen voor het maken van een Azure Active Directory of Azure Active Directory B2C-tenant.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 009f7ac2f7e614b7e07623e41888973f1a2b254d
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440973"
---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>Oplossen van het maken van een Azure Active Directory of Azure Active Directory B2C-tenant 

## <a name="create-an-azure-ad-tenant"></a>Een Azure AD-tenant maken
Als u een tenant Azure Active Directory (Azure AD) bij de eerste poging maken kunt, probeer het opnieuw. Als het probleem zich blijft voordoen, neem dan contact op met ondersteuning voor Azure.

## <a name="create-an-azure-ad-b2c-tenant"></a>Een Azure AD B2C-tenant maken
Als u problemen ondervindt wanneer u [maken van een Azure Active Directory B2C-tenant (Azure AD B2C)](active-directory-b2c-get-started.md), probeer de volgende opties:

* Als de Azure AD B2C-tenant niet wordt weergegeven in de lijst met tenants, probeer het opnieuw te maken van de tenant.
* Als de Azure AD B2C-tenant wordt weergegeven in de lijst met tenants en u het volgende foutbericht ziet, de tenant verwijderen en opnieuw maken:

    'Kan niet voltooien voor het maken van de B2C-tenant 'contosob2c'. Lees deze [koppeling](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) voor meer informatie. "
* Er zijn bekende problemen wanneer u een bestaande Azure AD B2C-tenant niet verwijderen en opnieuw maken met behulp van dezelfde domeinnaam. Wanneer u een nieuwe Azure AD B2C-tenant maakt, moet u de naam van een ander domein.
* Als deze oplossingen niet werkt, neem dan contact op met ondersteuning voor Azure. Zie voor meer informatie, [bestand ondersteuningsaanvragen voor Azure AD B2C](active-directory-b2c-support.md).

