---
title: 'Snelstartgids: Telemetrie verzenden naar Azure IoT Hub (Java) | Microsoft Docs'
description: In deze snelstart voert u twee Java-voorbeeldtoepassingen uit om gesimuleerde telemetrie te verzenden naar een IoT-hub en telemetrie van de IoT-hub te lezen voor verwerking in de cloud.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/22/2018
ms.author: dobett
ms.openlocfilehash: 903c5ce4b914575dbd0a807efeec30c8b32fa9dc
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216120"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-java"></a>Snelstartgids: Telemetrie vanaf een apparaat verzenden naar een IoT-hub en de telemetrie lezen van de hub met een back-endtoepassing (Java)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub is een Azure-service waarmee grote hoeveelheden telemetrie van uw IoT-apparaten naar de cloud kunt opnemen voor opslag of verwerking. In deze snelstartgids verzendt u telemetrie vanuit een toepassing voor een gesimuleerd apparaat via IoT Hub naar een back-endtoepassing voor verwerking.

In de snelstartgids worden twee vooraf geschreven Java-toepassingen gebruikt, één voor het verzenden van de telemetrie en één voor het lezen van de telemetrie van de hub. Voordat u deze twee toepassingen kunt uitvoeren, moet u een IoT-hub maken en een apparaat registreren bij de hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

De twee voorbeeldtoepassingen die u uitvoert in deze snelstartgids zijn geschreven in Java. Java SE 8 of hoger moet zijn geïnstalleerd op uw ontwikkelcomputer.

U kunt Java voor meerdere platforms downloaden van [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

Gebruik de volgende opdracht om de huidige versie van Java op uw ontwikkelcomputer te controleren:

```cmd/sh
java --version
```

U moet Maven 3 installeren om de voorbeelden te kunnen compileren. U kunt Maven voor meerdere platforms downloaden van [Apache Maven](https://maven.apache.org/download.cgi).

Gebruik de volgende opdracht om de huidige versie van Maven op uw ontwikkelcomputer te controleren:

```cmd/sh
mvn --version
```

Download het Java-voorbeeldproject van https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip en pak het ZIP-archief uit.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze snelstart gebruikt u de Azure-CLI om een gesimuleerd apparaat te registreren.

1. Voeg de IoT Hub CLI-extensie toe en maak de apparaatidentiteit. Vervang `{YourIoTHubName}` door de naam die u hebt gekozen voor uw IoT-hub:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

    Als u een andere naam voor het apparaat kiest, werkt u de apparaatnaam bij in de voorbeeldtoepassingen voordat u ze uitvoert.

2. Voer de volgende opdracht uit om de _apparaatverbindingsreeks_ op te halen voor het apparaat dat u zojuist hebt geregistreerd:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Noteer de apparaatverbindingsreeks, die er ongeveer zo uitziet: `Hostname=...=`. U gebruikt deze waarde verderop in de snelstartgids.

3. U hebt ook het _Event Hubs-compatibele eindpunt_, het _Event Hubs-compatibele pad_ en de _primaire sleutel iothubowner_ van uw IoT-hub nodig om de back-endtoepassing in staat te stellen verbinding te maken met uw IoT-hub en de berichten op te halen. Met de volgende opdrachten worden deze waarden opgehaald voor uw IoT-hub:

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name iothubowner --query primaryKey --hub-name {your IoT Hub name}
    ```

    Noteer deze drie waarden want deze hebt u later in de snelstartgids nodig.

## <a name="send-simulated-telemetry"></a>Gesimuleerde telemetrie verzenden

De toepassing voor het gesimuleerde apparaat maakt verbinding met een apparaatspecifiek eindpunt op uw IoT-hub en verstuurt gesimuleerde telemetrie over temperatuur en luchtvochtigheid.

1. Navigeer in een terminalvenster naar de hoofdmap van het voorbeeldproject in Java. Navigeer vervolgens naar de map **iot-hub\Quickstarts\simulated-device**.

2. Open het bestand **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** in een teksteditor van uw keuze.

    Vervang de waarde van de variabele `connString` door de apparaatverbindingsreeks die u eerder hebt genoteerd. Sla daarna de wijzigingen in het bestand **SimulatedDevice.java** op.

3. Voer in het terminalvenster de volgende opdrachten uit om de vereiste bibliotheken te installeren en de toepassing voor het gesimuleerde apparaat te compileren:

    ```cmd/sh
    mvn clean package
    ```

4. Voer in het terminalvenster de volgende opdrachten uit om de toepassing voor het gesimuleerde apparaat uit te voeren:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    In de volgende schermafbeelding ziet u de uitvoer op het moment dat de toepassing voor het gesimuleerde apparaat telemetriegegevens naar uw IoT-hub verzendt:

    ![Het gesimuleerde apparaat uitvoeren](media/quickstart-send-telemetry-java/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>De telemetrie van uw hub lezen

De back-endtoepassing maakt verbinding met het eindpunt **Events** aan de servicezijde van uw IoT-hub. De toepassing ontvangt de berichten die van het gesimuleerde apparaat naar de cloud worden verzonden. Een back-endtoepassing van IoT Hub wordt meestal uitgevoerd in de cloud om berichten van apparaat naar cloud te ontvangen en verwerken.

1. Navigeer in een ander terminalvenster naar de hoofdmap van het voorbeeldproject in Java. Navigeer vervolgens naar de map **iot-hub\Quickstarts\read-d2c-messages**.

2. Open het bestand **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** in een teksteditor van uw keuze. Werk de volgende variabelen bij en sla de wijzigingen in het bestand op.

    | Variabele | Waarde |
    | -------- | ----------- |
    | `eventHubsCompatibleEndpoint` | Vervang de waarde van de variabele door het met Event Hubs compatibele eindpunt dat u eerder hebt genoteerd. |
    | `eventHubsCompatiblePath`     | Vervang de waarde van de variabele door het met Event Hubs compatibele pad dat u eerder hebt genoteerd. |
    | `iotHubSasKey`                | Vervang de waarde van de variabele door de primaire sleutel iothubowner die u eerder hebt genoteerd. |


3. Voer in het terminalvenster de volgende opdrachten uit om de vereiste bibliotheken te installeren en de back-endtoepassing te compileren:

    ```cmd/sh
    mvn clean package
    ```

4. Voer in het terminalvenster de volgende opdrachten uit om de back-endtoepassing uit te voeren:

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    In de volgende schermafbeelding ziet u de uitvoer op het moment dat de back-endtoepassing telemetriegegevens ontvangt die door het gesimuleerde apparaat naar de hub zijn verzonden:

    ![De back-endtoepassing uitvoeren](media/quickstart-send-telemetry-java/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u een IoT-hub geconfigureerd, een apparaat geregistreerd, gesimuleerde telemetrie verzonden naar de hub met behulp van een Java-toepassing en de telemetrie weer gelezen van de hub met behulp van een eenvoudige back-endtoepassing.

Ga verder met de volgende snelstartgids als u wilt weten hoe u een gesimuleerd apparaat beheert vanuit een back-endtoepassing.

> [!div class="nextstepaction"]
> [Snelstartgids: Een apparaat beheren dat is verbonden met een IoT-hub](quickstart-control-device-java.md)