---
title: bestand opnemen
description: bestand opnemen
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: f70e0bcb68f059618f9b398a00e23498a10df23e
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39582960"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>De Microsoft Graph-API aanroepen vanuit een toepassing met JavaScript één pagina (SPA)

Deze handleiding laat zien hoe een JavaScript één pagina toepassingen (SPA) kunnen zich in een persoonlijke, werk en schoolaccounts, een toegangstoken en aanroepen van de Microsoft Graph API of andere API's die tokens voor toegang vanuit de Azure Active Directory v2-eindpunt vereist.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>De werking van de voorbeeld-app die is gegenereerd door deze handleiding

![De werking van de voorbeeld-app die is gegenereerd door deze handleiding](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>Meer informatie

De voorbeeldtoepassing die is gemaakt in deze handleiding kunt een JavaScript-SPA query uitvoeren op de Microsoft Graph API of een Web-API die tokens van Azure Active Directory v2-eindpunt accepteert. Voor dit scenario, nadat een gebruiker zich aanmeldt, is een toegangstoken aangevraagd en toegevoegd aan de HTTP-aanvragen via de autorisatie-header. Ophalen van tokens en vernieuwing worden afgehandeld door de Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotheken

Deze handleiding worden de volgende bibliotheek gebruikt:

|Bibliotheek|Beschrijving|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library voor JavaScript-Preview|

> [!NOTE]
> *msal.js* doelen de *Azure Active Directory v2-eindpunt* -waarmee privé, school- als accounts aanmelden en tokens verkrijgen. De *Azure Active Directory v2-eindpunt* heeft [enkele beperkingen](..\articles\active-directory\develop\active-directory-v2-limitations.md). Als u alleen geïnteresseerd in school als accounts, gebruikt u *adal.js* en de *V1-eindpunt*. Om te begrijpen van de verschillen tussen de v1 en v2-eindpunten lezen de [vergelijking van v1-v2](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->
