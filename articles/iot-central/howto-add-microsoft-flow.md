---
title: Werkstromen bouwen met de IoT Central-connector in Microsoft Flow | Microsoft Docs
description: Gebruik van de IoT Central-connector in Microsoft Flow-trigger werkstromen en maken, bijwerken en verwijderen van apparaten in werkstromen.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/12/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: peterpr
ms.openlocfilehash: 2414fb0576448339b268dce92dafe6c70108ba5d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008949"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Werkstromen bouwen met de IoT Central-connector in Microsoft Flow

Microsoft Flow gebruiken voor het automatiseren van werkstromen in de vele toepassingen en services die afhankelijk zijn van zakelijke gebruikers. Met behulp van de IoT Central-connector in Microsoft Flow, kunt u werkstromen activeren wanneer een regel wordt geactiveerd in IoT Central. In een werkstroom geactiveerd door IoT Central of een andere toepassing, kunt u de acties in de IoT Central-connector te maken van een apparaat, het bijwerken van de eigenschappen en instellingen van een apparaat of het verwijderen van een apparaat. Bekijk [deze Microsoft Flow-sjablonen](https://aka.ms/iotcentralflowtemplates) die IoT Central verbinden met andere services, zoals mobiele meldingen en Microsoft Teams.

> [!NOTE] 
> U moet zich aanmelden bij Microsoft Flow met een persoonlijke of werk of school-account. Meer informatie over Microsoft Flow plannen [hier](https://aka.ms/microsoftflowplans).

## <a name="trigger-a-workflow-when-a-rule-is-fired"></a>Een werkstroom wordt geactiveerd wanneer een regel wordt geactiveerd

Deze sectie leest u hoe u kunt een mobiele melding ontvangen in de mobiele Flow-app activeren wanneer een regel wordt geactiveerd in IoT Central.

1. Begin met [maken van een regel in IoT Central](howto-create-telemetry-rules.md). Nadat u de voorwaarden van de regel hebt opgeslagen, kiest u de **Microsoft Flow-actie** als een nieuwe actie. Een nieuw tabblad of venster te openen in uw browser, die u in Microsoft Flow.

1. Meld u aan bij Microsoft Flow. Dit hoeft niet te worden van hetzelfde account zijn als het account waarmee u IoT-centraal-India. U komt op een overzichtspagina weergegeven van een verbinding te maken met een aangepaste actie IoT Central-connector.

1. Klik op **blijven**. U gaat naar de Microsoft Flow-ontwerpfunctie te maken van uw werkstroom. De werkstroom heeft een IoT Central activeringsopdracht met uw toepassing en de regel al ingevuld.

1. Kies **+ nieuwe stap** en **een actie toevoegen**. U kunt op dit moment geen actie die u uw werkstroom wilt toevoegen. Als voorbeeld gaan we sturen een mobiele melding ontvangen. Zoeken naar **melding**, en kies **meldingen - ik wil een mobiele melding ontvangen**.

1. Vul in de actie in het tekstveld met wat u uw aanmelding wilt te zeggen hadden. U kunt opnemen *dynamische inhoud* van uw IoT Central-regel, geven belangrijke informatie zoals de naam van apparaat- en tijdstempel op de melding.

    > [!NOTE]
    > Klik op de tekst 'Meer informatie' in het venster voor dynamische inhoud om op te halen van de meting en eigenschapswaarden die de regel geactiveerd.

    ![Flow actie met deelvenster met dynamische open bewerken](./media/howto-add-microsoft-flow/flowdynamicpane.PNG)

1. Wanneer u klaar bent uw actie bewerken, klikt u op **opslaan**. U wordt omgeleid naar de pagina overzicht van uw werkstroom. Hier ziet u de uitvoeringsgeschiedenis en delen met andere collega's.

    > [!NOTE]
    > Als u wilt dat andere gebruikers in uw IoT Central-app met deze regel bewerken, moet u deze met hen delen in Microsoft Flow. De Microsoft Flow-accounts toevoegen als eigenaren in uw werkstroom.

1. Als u terug naar uw IoT Central-app gaat, ziet u dat deze regel bevat een Microsoft Flow-actie in het gebied van acties.

U kunt altijd beginnen het bouwen van een werkstroom met behulp van de IoT Central-connector in Microsoft Flow. U kunt vervolgens kiezen welke IoT Central-app en welke regel verbinding maken met.

## <a name="create-a-device-in-a-workflow"></a>Een apparaat in een werkstroom maken

Deze sectie leest u hoe u een nieuw apparaat in IoT Central maken op de push-bewerking van een knop op een mobiel apparaat via de mobiele Microsoft Flow-app. U kunt deze actie in stroom gebruiken, zoals Dynamics ERP-systemen integreren met IoT Central door het maken van een nieuw apparaat wanneer een apparaat ergens anders wordt toegevoegd.

1. Beginnen met het maken van een lege workflow in Microsoft Flow.

1. Zoeken naar **knop stroom voor mobiel** als een trigger.

1. In deze trigger toevoegen met de naam tekstinvoer **apparaatnaam**. Wijzigen van de beschrijvende tekst om te worden **Voer de naam van het apparaat van het nieuwe apparaat**.

1. Voeg een nieuwe actie toe. Zoek de **Azure IoT Central - maken van een apparaat** actie.

1. Kies uw toepassing en kies een apparaat-sjabloon voor het maken van een apparaat uit in de vervolgkeuzelijsten. Hier ziet u de actie uitvouwen om alle eigenschappen en instellingen van het apparaat weer te geven.

1. Selecteer het veld met de naam van apparaat. Kies in het deelvenster met dynamische inhoud **apparaatnaam**. Deze waarde uit de invoer die de gebruiker invoert die worden doorgegeven via de mobiele app en de naam van het nieuwe apparaat in IoT Central. In dit voorbeeld is de enige vereiste veld de naam van het apparaat, aangegeven door de rode asterisk. Sjabloon voor een ander apparaat mogelijk meerdere vereiste velden die moeten worden ingevuld om een nieuwe apparaat te maken.

    ![Stroom dynamische actiedeelvenster apparaat maken](./media/howto-add-microsoft-flow/flowcreatedevice.PNG)
1. (Optioneel) Vul in de andere velden naar wens voor het maken van het nieuwe apparaten. Kies bijvoorbeeld, als het apparaat wordt gesimuleerd of niet.

1. Sla ten slotte uw werkstroom.

1. Probeer uw werkstroom in de mobiele Microsoft Flow-app. Ga naar de **knoppen** tabblad in de app. U ziet de knop maken een nieuwe werkstroom voor apparaten ->. Voer de naam van het nieuwe apparaat en bekijk wordt weergeven in IoT Central.

    ![Stroom maken mobiele apparaat-schermafbeelding](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Een apparaat in een werkstroom bijwerken

Deze sectie leest u hoe u instellingen voor apparaten en de eigenschappen in IoT Central voor de push-bewerking van een knop op een mobiel apparaat via de mobiele Microsoft Flow-app bij te werken.

1. Beginnen met het maken van een lege workflow in Microsoft Flow.

1. Zoeken naar **knop stroom voor mobiel** als een trigger.

1. In deze trigger toevoegen invoer, zoals een **locatie** tekstinvoer die overeenkomt met een instelling of de eigenschap u wilt wijzigen. De beschrijvende tekst wijzigen.

1. Voeg een nieuwe actie toe. Zoek de **Azure IoT Central - een-apparaat bijwerken** actie.

1. Kies uw toepassing in de vervolgkeuzelijst. Nu moet u de apparaat-ID van het bestaande apparaat dat u wilt bijwerken. U kunt de apparaat-ID ophalen uit IoT Central in de **Device Explorer**.

    ![Apparaat-ID van IoT Central device explorer](./media/howto-add-microsoft-flow/iotcdeviceid.png)

1. Op dit moment kunt u de naam van het apparaat kunt bijwerken en of deze een gesimuleerd apparaat is of niet. Voor het bijwerken van een van de eigenschappen en instellingen van het apparaat, moet u de sjabloon van het apparaat van het apparaat dat u bijwerken wilt de **apparaat sjabloon** vervolgkeuzelijst. De actie-tegel wordt uitgebreid om weer te geven van alle eigenschappen en instellingen die u kunt bijwerken.

1. Selecteer elk van de eigenschappen en instellingen die u wilt bijwerken. Kies in het deelvenster met dynamische inhoud, de bijbehorende invoer door de trigger. In dit voorbeeld wordt de locatiewaarde omlaag doorgegeven aan het bijwerken van de locatie-eigenschap van het apparaat.

    ![Dynamische actiedeelvenster van Flow update apparaat](./media/howto-add-microsoft-flow/flowupdatedevice.PNG)

1. Sla ten slotte uw werkstroom.

1. Probeer uw werkstroom in de mobiele Microsoft Flow-app. Ga naar de **knoppen** tabblad in de app. Hier ziet u de Update een werkstroom voor apparaten -> knop. Voer de invoer en zien van het apparaat in IoT Central bijgewerkt!

## <a name="delete-a-device-in-a-workflow"></a>Een apparaat in een werkstroom verwijderen

U kunt een apparaat door de apparaat-ID gebruikt verwijderen de **Azure IoT Central - een apparaat verwijderen** actie. Hier volgt een voorbeeldwerkstroom die Hiermee verwijdert u een apparaat op de push-bewerking van een knop in de mobiele Microsoft Flow-app.

   ![Werkstroom voor de apparaten van stroom verwijderen](./media/howto-add-microsoft-flow/flowdeletedevice.PNG)
    
## <a name="troubleshooting"></a>Problemen oplossen

Als u problemen bij het maken van een verbinding met de Azure IoT Central-connector ondervindt, volgen hier enkele tips om u te helpen.

1. Persoonlijke Microsoft-accounts (zoals @hotmail.com, @live.com, @outlook.com domeinen) worden niet ondersteund op dit moment. U moet een AAD-werk- of schoolaccount.

2. Als u een fout opgetreden tijdens het gebruik van een AAD-account ontvangt, probeert te openen van Windows PowerShell en voer de volgende commandlets als beheerder.
    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```
    
## <a name="next-steps"></a>Volgende stappen
U hebt geleerd hoe u kunt Microsoft Flow gebruiken om werkstromen te bouwen, de voorgestelde volgende stap is het [apparaten beheren](howto-manage-devices.md).
