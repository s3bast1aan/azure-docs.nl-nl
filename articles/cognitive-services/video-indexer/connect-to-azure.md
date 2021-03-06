---
title: Een Video Indexer-account dat is verbonden met Azure maken | Microsoft Docs
description: Dit artikel leest over het maken van een Video Indexer-account met Azure bent verbonden.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 08/05/2018
ms.author: juliako
ms.openlocfilehash: 2c7c74d81f6d6634312b9af3f8657c520449787a
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577322"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Maken van een Video Indexer-account dat is verbonden met Azure

Als u een Video Indexer-account maakt, kunt u een gratis proefaccount (waar u aan een bepaald aantal gratis indexering minuten) of een betaalde optie (waarbij u bent niet beperkt door het quotum). Met gratis proefversie Video Indexer biedt maximaal 600 minuten gratis worden geïndexeerd voor gebruikers van de website en maximaal 2400 minuten gratis indexing voor een API-gebruikers. Met de betaalde optie maakt u een Video Indexer-account dat is verbonden met uw Azure-abonnement en een Azure Media Services-account. U betaalt voor minuten geïndexeerd, evenals de Media-Account gekoppeld kosten in rekening gebracht. 

In dit artikel laat zien hoe een Video Indexer-account dat gekoppeld aan een Azure-abonnement en een Azure Media Services-account maken. 

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. 

    Als u een Azure-abonnement nog geen hebt, kunt u zich aanmelden voor [gratis proefversie van Azure](https://azure.microsoft.com/free/).

* Een Azure Active Directory (AD)-domein. 

    Als u geen Azure AD-domein, moet u dit domein maken met uw Azure-abonnement. Zie voor meer informatie, [aangepaste domeinnamen in uw Azure Active Directory beheren](../../active-directory/users-groups-roles/domains-manage.md)

* Een gebruiker en het lid in uw Azure AD-domein. U gebruikt dit lid bij het verbinden van uw Video Indexer-account met Azure.

    Deze gebruiker moet voldoen aan deze criteria:

    * Een Azure AD-gebruiker met een werk- of schoolaccount, niet een persoonlijk account, zoals outlook.com, live.com of hotmail.com worden.
        
        ![alle AAD-gebruikers](./media/create-account/all-aad-users.png)

    *  Een lid in uw Azure-abonnement met de rol van een eigenaar of Inzender-en beheerder van gebruikerstoegang worden. Een gebruiker kan twee keer worden toegevoegd met behulp van 2. Eenmaal met Inzender en anderzijds met Access-beheerder.

        ![Toegangsbeheer](./media/create-account/access-control-iam.png)

    * Registreer de resourceprovider voor EventGrid met behulp van de Azure portal.

        In de Azure-portal, gaat u naar **abonnementen** > [abonnement] > **resourceproviders en** > **Microsoft.EventGrid**. Als u niet in de status 'Registered', klikt u op **registreren**. Het duurt enkele minuten om te registreren. 

        ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Verbinding maken met Azure

1. Meld u aan met die gebruiker en klik op de **verbinding maken met Azure** knop:

    ![Verbinding maken met Azure](./media/create-account/connect-to-azure.png)

2. Wanneer de abonnementenlijst met wordt weergegeven, selecteert u het abonnement dat u wilt gebruiken. 

    ![Video Indexer verbinden met Azure](./media/create-account/connect-vi-to-azure-subscription.png)

3. Selecteer een Azure-regio van de ondersteunde locaties: VS-West 2, Noord-Europa of Oost-Azië.
4. Onder **Azure Media Services-account**, kies een van de volgende opties:

    * Voor het maken van een nieuwe Media Services-account selecteert **nieuwe resourcegroep maken**. Geef een naam voor de resourcegroep.

        Azure maakt uw nieuwe account in uw abonnement, met inbegrip van een nieuw Azure Storage-account. Nieuwe Media Services-account heeft een standaard initiële configuratie met een Streaming-eindpunt en 10 gereserveerde S3-eenheden.
    * Voor het gebruik van een Media Services-account, selecteert u **bestaande resource gebruiken**. Selecteer uw account in de lijst met accounts.

        Media Services-account moet dezelfde regio als uw Video Indexer-account hebben. Om te beperken de duur van indexering en lage doorvoer, het aanpassen van het type en aantal gereserveerde eenheden voor **10 gereserveerde S3-eenheden** in Media Services-account.
    * Voor het handmatig configureren van de verbinding, klikt u op de **overschakelen naar de handmatige configuratie** koppelen en geef de benodigde informatie:

    ![Video Indexer verbinden met Azure](./media/create-account/connect-vi-to-azure-subscription-2.png)

5. Wanneer u klaar bent, kiest u **Connect**. Met deze bewerking kan enkele minuten duren. 

    Nadat u met Azure verbonden bent, wordt uw nieuwe Video Indexer-account wordt weergegeven in de lijst met accounts:

    ![nieuw account](./media/create-account/new-account.png)

6. Blader naar uw nieuwe account: 

    ![Video Indexer-account](./media/create-account/vi-account.png)

## <a name="considerations"></a>Overwegingen

De volgende Azure Media Services gerelateerde overwegingen zijn van toepassing:

* Als u verbinding met een nieuwe Media Services-account, ziet u een nieuwe resourcegroep, Media Services-account en een opslagaccount in uw Azure-abonnement.
* Als u met een nieuwe Media Services-account verbonden, het medium wordt ingesteld door Video Indexer **gereserveerde eenheden** op 10 S3-eenheden:

    ![Gereserveerde Media Services-eenheden](./media/create-account/ams-reserved-units.png)

* Als u met een Media Services-account verbonden, wordt niet de bestaande mediaset is gewijzigd voor Video Indexer **gereserveerde eenheden** configuratie.

    Mogelijk moet u het type en aantal media aanpassen **gereserveerde eenheden**, op basis van het geplande laden. Houd er rekening mee dat als het laden hoog is en u geen voldoende eenheden of snelheid, video's verwerken in time-fouten resulteren kunnen.

* Als u verbinding met een nieuwe Media Services-account, start Video Indexer automatisch een **Streaming-eindpunt** erin:

    ![Media Services streaming-eindpunt](./media/create-account/ams-streaming-endpoint.png)

* Als u met een Media Services-account verbonden, wordt de configuratie van de streaming endpoints niet gewijzigd door Video Indexer. Als er geen wordt uitgevoerd **Streaming-eindpunt**, kunt u zich niet Bekijk video's vanaf deze Media Services-account of in Video Indexer.

## <a name="use-video-indexer-apis-v2"></a>Gebruik Video Indexer API v2

U kunt via programmacode communiceren met uw proefaccount en/of met uw Video Indexer-accounts die zijn verbonden met azure door de instructies in: [gebruik API's](video-indexer-use-apis.md).

U moet dezelfde Azure AD-gebruiker die u hebt gebruikt bij het verbinden met Azure.

## <a name="next-steps"></a>Volgende stappen

[Details van de JSON-uitvoer onderzoeken](video-indexer-output-json-v2.md).

