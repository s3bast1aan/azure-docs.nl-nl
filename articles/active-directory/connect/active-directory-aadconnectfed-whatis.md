---
title: Azure AD Connect en Federatie | Microsoft Docs
description: Deze pagina is de centrale locatie voor alle documentatie met betrekking tot AD FS-bewerkingen die gebruikmaken van Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ce6b889d23959e9d04b95ec2bc5847340b596448
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915833"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect en federatie
Met Azure Active Directory (Azure AD) Connect kunt u bij het configureren van Federatie met on-premises Active Directory Federation Services (AD FS) en Azure AD. Met de aanmelding federation kunt u gebruikers zich aanmelden voor Azure op basis van een AD-services met hun on-premises wachtwoorden-- en, terwijl op het bedrijfsnetwerk bevinden, zonder te hoeven hun wachtwoord opnieuw invoeren. U kunt een nieuwe installatie van AD FS implementeren met de optie Federatie met AD FS, of kunt u een bestaande installatie in een farm met Windows Server 2012 R2.

In dit onderwerp is de thuisbasis voor informatie over de federation-gerelateerde functies voor Azure AD Connect. Het artikel bevat koppelingen naar alle verwante onderwerpen. Zie voor koppelingen naar Azure AD Connect [uw on-premises identiteiten integreren met Azure Active Directory](active-directory-aadconnect.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: federation-onderwerpen
| Onderwerp | Er wordt aangegeven en bij het lezen |
|:--- |:--- |
| **Azure AD Connect-aanmelden-gebruikersopties** | |
| [Aanmeldingsopties voor gebruiker begrijpen](active-directory-aadconnect-user-signin.md) |Meer informatie over de verschillende gebruiker aanmelden opties en hoe ze invloed hebben op de gebruikerservaring voor Azure aanmelden. |
| **AD FS installeren met behulp van Azure AD Connect** | |
| [Vereisten](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Zie de vereisten voor een geslaagde installatie van AD FS via Azure AD Connect. |
| [AD FS-farm configureren](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Installeer een nieuwe AD FS-farm met behulp van Azure AD Connect. |
| [Federeren met Azure AD met behulp van alternatieve aanmeldings-ID ](active-directory-aadconnect-federation-management.md#alternateid) | Federatie met behulp van alternatieve aanmeldings-ID configureren  |
| **De AD FS-configuratie wijzigen** | |
| [Herstel de vertrouwensrelatie](active-directory-aadconnect-federation-management.md#repairthetrust) |Herstel de vertrouwensrelatie van de huidige tussen on-premises AD FS- en Office 365/Azure. |
| [Een nieuwe AD FS-server toevoegen](active-directory-aadconnect-federation-management.md#addadfsserver) |Vouw een AD FS-farm met een aanvullende AD FS-server na de eerste installatie. |
| [Een nieuwe AD FS WAP-server toevoegen](active-directory-aadconnect-federation-management.md#addwapserver) |Vouw een AD FS-farm met een extra Webtoepassingsproxy (WAP)-server na de eerste installatie. |
| [Een nieuwe federatieve domein toevoegen](active-directory-aadconnect-federation-management.md#addfeddomain) |Voeg een ander domein dat gefedereerd met Azure AD. |
| [Het SSL-certificaat bijwerken](active-directory-aadconnectfed-ssl-update.md)| De SSL-certificaat voor AD FS-farm bijwerken. |
| [Federatiecertificaten vernieuwen voor Office 365 en Azure AD](active-directory-aadconnect-o365-certs.md)|Verleng uw O365-certificaat met Azure AD.|
| **Andere federation-configuratie** | |
| [Meerdere exemplaren van Azure AD federeren met één exemplaar van AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md) | Meerdere Azure AD met één AD FS-farm federeren| 
| [Een aangepaste bedrijfsportal logo/afbeelding toevoegen](active-directory-aadconnect-federation-management.md#customlogo) |De aanmeldingsprocedure wijzigen door het aangepaste logo dat wordt weergegeven op de AD FS-aanmeldingspagina op te geven. |
| [Een beschrijving van de aanmelding toevoegen](active-directory-aadconnect-federation-management.md#addsignindescription) |De beschrijving aanmelden op de aanmeldingspagina van AD FS wijzigen. |
| [AD FS claimregels wijzigen](active-directory-aadconnect-federation-management.md#modclaims) |Wijzigen of claimregels in AD FS die overeenkomen met aan Azure AD Connect sync-configuratie toevoegen. |


## <a name="additional-resources"></a>Aanvullende resources
* [Federeren twee Azure AD met één AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)
* [AD FS-implementatie in Azure](active-directory-aadconnect-azure-adfs.md)
* [Hoge beschikbaarheid meerdere regio's AD FS-implementatie in Azure met Azure Traffic Manager](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
