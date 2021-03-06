---
title: bestand opnemen
description: bestand opnemen
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 07/03/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 6fb9b5cd1379f39796f984f0436900f629c66436
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/17/2018
ms.locfileid: "40209471"
---
Als u Service Bus-berichtenentiteiten wilt gebruiken in Azure, moet u eerst een naamruimte maken met een naam die uniek is binnen Azure. Een naamruimte biedt een scoping container voor het verwerken van Service Bus-resources in uw toepassing.

Ga als volgt te werk om een naamruimte te maken:

1. Meld u aan bij [Azure Portal][Azure portal].
2. Klik in het linkernavigatievenster van de portal achtereenvolgens op **+ Een resource maken**, **Integratie** en **Service Bus**.
3. Voer in het dialoogvenster **Naamruimte maken** een naam in voor de naamruimte. In het systeem wordt onmiddellijk gecontroleerd of de naam beschikbaar is.
4. Nadat u hebt gecontroleerd of de naam van de naamruimte beschikbaar is, kiest u de prijscategorie (Basic, Standard of Premium). Kies Standard of Premium indien u gebruik wilt maken van [onderwerpen en/of abonnementen](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions). Onderwerpen/abonnementen worden niet ondersteund in de prijscategorie Basic.
5. Kies in het veld **Abonnement** een Azure-abonnement waarin u de naamruimte maakt.
6. Kies in het veld **Resourcegroep** een bestaande resourcegroep waarin de naamruimte zal zijn opgenomen of maak een nieuwe resourcegroep.      
7. Kies in **Locatie** het land of regio waarin uw naamruimte moet worden gehost.
   
    ![Een naamruimte maken][create-namespace]
8. Klik op **Create**. Uw naamruimte wordt nu gemaakt en ingeschakeld. U moet wellicht enkele minuten wachten terwijl de resources voor uw account worden ingericht.

### <a name="obtain-the-management-credentials"></a>De beheerreferenties ophalen

Bij het maken van een nieuwe naamruimte wordt automatisch een eerste SAS-regel (Shared Access Signature) gegenereerd met een bijbehorende primaire en secundaire sleutel die elk volledige controle over alle aspecten van de naamruimte bieden. Raadpleeg [Vereenvoudigde Service Bus-verificatie en -autorisatie](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) voor meer informatie over het maken van meer regels met beperktere rechten voor reguliere afzenders en ontvangers. Volg deze stappen om de eerste regel te kopiëren: 

1. Klik op **Alle resources** en klik vervolgens op de zojuist gemaakte naam voor de naamruimte.
2. Klik in het venster van de naamruimte op **Beleid voor gedeelde toegang**.
3. Klik in het scherm **Beleid voor gedeelde toegang** op **RootManageSharedAccessKey**.
   
    ![verbinding-gegevens][connection-info]
4. Klik in het venster **Beleid: RootManageSharedAccessKey** op de knop Kopiëren naast **Primaire verbindingsreeks** om de verbindingsreeks naar het klembord te kopiëren voor later gebruik. Plak deze waarde in Kladblok of een andere tijdelijke locatie.
   
    ![connection-string][connection-string]

5. Herhaal de vorige stap: het kopiëren en plakken van de waarde voor de **Primaire sleutel** voor een tijdelijke locatie zodat u deze later kunt gebruiken.

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
