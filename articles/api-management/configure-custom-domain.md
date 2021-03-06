---
title: Een aangepaste domeinnaam configureren voor uw exemplaar van Azure API Management | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe een aangepaste domeinnaam configureren voor uw Azure API Management-exemplaar.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 12/14/2017
ms.author: apimpm
ms.openlocfilehash: 96e233a26af95d4373323867046ca01fe1304608
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2018
ms.locfileid: "29345367"
---
# <a name="configure-a-custom-domain-name"></a>Een aangepaste domeinnaam configureren 

Wanneer u een exemplaar van API Management (APIM) maakt, Azure toegewezen aan een subdomein van azure api.net (bijvoorbeeld `apim-service-name.azure-api.net`). Echter uw APIM eindpunten met uw eigen domeinnaam als kan worden blootgesteld **contoso.com**. Deze zelfstudie laat zien hoe u een bestaande aangepaste DNS-naam toewijzen aan eindpunten die worden weergegeven door een exemplaar van Azure API Management.

> [!WARNING]
> Klanten die gebruikt vastmaken certificaat willen voor het verbeteren van de beveiliging van hun toepassingen moeten gebruiken een aangepaste domeinnaam > en het certificaat die ze beheren, niet de standaardcertificaat. Klanten die het standaardcertificaat vastmaken in plaats daarvan worden > duurt een harde afhankelijkheid van de eigenschappen van het certificaat dat ze niet worden beheerd, dit is geen aanbevolen.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel, moet u het volgende hebben:

+ Een actief Azure-abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Een exemplaar APIM. Zie voor meer informatie [Azure API Management-exemplaar maken](get-started-create-service-instance.md).
+ Een aangepaste domeinnaam dat eigendom is van u. De aangepaste domeinnaam die u wilt gebruiken, moet worden afzonderlijk aangeschaft en gehost op een DNS-server. In dit onderwerp geeft geen instructies voor het hosten van een aangepaste domeinnaam.
+ U moet een geldig certificaat met een openbare en persoonlijke sleutel hebben (. (PFX). Onderwerpnaam of alternatieve onderwerpnaam (SAN) moet overeenkomen met de naam van het domein (Hiermee kunt u APIM veilig kunt blootstellen URL's via SSL).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>De Azure portal gebruiken om in te stellen van een aangepaste domeinnaam

1. Navigeer naar uw APIM exemplaar in de [Azure-portal](https://portal.azure.com/).
2. Selecteer **aangepaste domeinen en SSL**.
    
    Er is een aantal eindpunten waaraan u een aangepaste domeinnaam kunt toewijzen. De volgende eindpunten zijn momenteel beschikbaar: 
    + **Proxy** (standaard: `<apim-service-name>.azure-api.net`), 
    + **Portal** (standaard: `<apim-service-name>.portal.azure-api.net`),     
    + **Management** (standaard: `<apim-service-name>.management.azure-api.net`), 
    + **SCM** (standaard: `<apim-service-name>.scm.azure-api.net`).

    >[!NOTE]
    > U kunt alle van de eindpunten of een deel van deze bijwerken. Normaal gesproken klanten bijwerken **Proxy** (deze URL wordt gebruikt om aan te roepen de beschikbaar via het API Management-API) en **Portal** (de portal voor ontwikkelaars URL). **Management** en **SCM** eindpunten wordt intern gebruikt door klanten APIM en dus een aangepaste domeinnaam minder vaak toegewezen.
3. Selecteer het eindpunt dat u wilt bijwerken. 
4. Klik in het venster aan de rechterkant **aangepaste**.

    + In de **aangepaste domeinnaam**, geef de naam die u wilt gebruiken. Bijvoorbeeld `api.contoso.com`. <br/>Jokertekens domeinnamen (bijvoorbeeld *. domein.com) worden ook ondersteund.
    + In de **certificaat**, Geef een geldige. PFX-bestand dat u wilt uploaden. 
    + Als u het certificaat heeft een wachtwoord, voert u in de **wachtwoord** veld.
1. Klik op toepassen.

    >[!NOTE]
    >Het toewijzingsproces van het certificaat kan duren 15 minuten of langer, afhankelijk van de grootte van de implementatie. Ontwikkelaars SKU heeft uitvaltijd, basis en hogere SKU's hebben geen uitvaltijd.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Volgende stappen

[Upgraden en schalen van uw service](upgrade-and-scale.md)
