---
title: Apparaatbeheer met Azure IoT Hub | Microsoft Docs
description: "Overzicht van apparaatbeheer in Azure IoT Hub: levenscyclus van bedrijfsapparaten en apparaatbeheerpatronen, zoals opnieuw opstarten, de fabrieksinstellingen herstellen, firmware bijwerken, configuratie, apparaatdubbels, query's en taken."
author: bzurcher
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: briz
ms.openlocfilehash: 55c28b9ec39ffd617c816d76f67ff6f9853d7012
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185911"
---
# <a name="overview-of-device-management-with-iot-hub"></a>Overzicht van apparaatbeheer met IoT Hub

Azure IoT Hub biedt de functies en het uitbreidingsmodel waarmee apparaat- en back-end-ontwikkelaars krachtige beheeroplossingen voor apparaten kunnen bouwen. Apparaten variëren van beperkte sensoren en microcontrollers met één doel tot krachtige gateways die communicatie routeren voor groepen apparaten.  Bovendien verschillen de gebruiksvoorbeelden en de vereisten voor IoT-operators aanzienlijk in de verschillende sectoren.  Ondanks deze variatie biedt apparaatbeheer met IoT Hub mogelijkheden, patronen en codebibliotheken die geschikt zijn voor een groot aantal apparaten en eindgebruikers.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Een essentieel onderdeel van het maken van een geslaagde enterprise-IoT-oplossing is het bieden van een strategie voor hoe operators omgaan met het continue beheer van de apparaten in hun bedrijf. IoT-operators hebben eenvoudige en betrouwbare hulpprogramma's en toepassingen nodig waarmee ze zich kunnen concentreren op de strategische aspecten van hun werk. Dit artikel bevat:

* Een kort overzicht van de Azure IoT Hub-aanpak van apparaatbeheer.
* Een beschrijving van de algemene principes van apparaatbeheer.
* Een beschrijving van de levenscyclus van het apparaat.
* Een overzicht van de algemene patronen van apparaatbeheer.

## <a name="device-management-principles"></a>Principes van apparaatbeheer
IoT zorgt voor unieke apparaatbeheeruitdagingen en elke oplossing van enterprise-klasse moet rekening houden met de volgende principes:

![Afbeelding Principes van apparaatbeheer][img-dm_principles]

* **Schaal en automatisering**: voor IoT-oplossingen zijn eenvoudige hulpprogramma's nodig waarmee routinetaken kunnen worden geautomatiseerd en waarmee een relatief klein aantal operationele medewerkers miljoenen apparaten kan beheren. Operators verwachten dagelijks apparaatbewerkingen extern en bulksgewijs uit te voeren en alleen te worden gewaarschuwd wanneer er problemen opduiken waarvoor hun directe aandacht is vereist.
* **Openheid en compatibiliteit**: het ecosysteem van apparaten is buitengewoon divers. Beheerhulpprogramma's moeten zo worden aangepast dat ze kunnen omgaan met een groot aantal apparaatklassen, platforms en protocollen. Operators moeten vele typen apparaten kunnen ondersteunen, van de meest beperkte ingesloten chips voor enkelvoudige processen tot krachtige en volledig functionele computers.
* **Contextbewustzijn**: IoT-omgevingen zijn dynamisch en veranderen voortdurend. Betrouwbaarheid van de service is cruciaal. Tijdens bewerkingen voor apparaatbeheer moet er rekening worden gehouden met de volgende factoren, om ervoor te zorgen dat uitvaltijd wegens onderhoud de kritieke bedrijfsbewerkingen niet beïnvloedt en geen gevaarlijke situaties creëert:
    * SLA-onderhoudsvensters
    * Netwerk- en voedingsstatussen
    * Voorwaarden wanneer in gebruik
    * Geolocatie van apparaat
* **Vele rollen bedienen**: ondersteuning voor de unieke werkstromen en processen van IoT-bewerkingsrollen is cruciaal. Beheerders moeten in harmonie met de gegeven beperkingen van interne IT-afdelingen werken.  Ze moeten ook duurzame manieren vinden om in realtime gegevens over apparaatbewerkingen door te sturen naar toezichthouders en andere zakelijke leidinggevende rollen.

## <a name="device-lifecycle"></a>Levenscyclus van apparaat
Er is een set algemene fasen voor apparaatbeheer die hetzelfde zijn voor alle enterprise-IoT-projecten. In Azure IoT zijn er vijf fasen binnen de levenscyclus van een apparaat:

![De vijf fasen van de levenscyclus van een Azure IoT-apparaat: plannen, inrichten, configureren, bewaken, buiten gebruik stellen][img-device_lifecycle]

Binnen elk van deze vijf fasen zijn er verschillende vereisten voor de apparaatoperator waaraan moet worden voldaan voor een volledige oplossing:

* **Plannen**: operators in staat stellen een schema voor de metagegevens van een apparaat te maken waarmee ze eenvoudig en nauwkeurig kunnen zoeken naar en zich richten op een groep apparaten voor bulksgewijze beheerbewerkingen. U kunt de apparaatdubbel gebruiken om de metagegevens van dit apparaat op te slaan in de vorm van tags en eigenschappen.
  
    *Meer weten*: [aan de slag met apparaatdubbels][lnk-twins-getstarted], [meer informatie over apparaatdubbels][lnk-twins-devguide], [hoe apparaatdubbeleigenschappen gebruiken][lnk-twin-properties], [aanbevolen procedures voor de configuratie van het apparaat in een IoT-oplossing][lnk-adm-best-practices].
* **Inrichten**: nieuwe apparaten veilig inrichten voor IoT Hub en operators in staat stellen apparaatmogelijkheden onmiddellijk te detecteren.  Gebruik het id-register van IoT Hub om flexibele apparaat-id's en -referenties te maken. Voer dit uit in één bulkbewerking met behulp van een taak. Bouw apparaten om hun mogelijkheden en voorwaarden via apparaateigenschappen te rapporteren in de apparaatdubbel.
  
    *Meer weten*: [apparaatidentiteiten beheren][lnk-identity-registry], [bulksgewijs beheren van apparaat-id's][lnk-bulk-identity], [Apparaatdubbeleigenschappen gebruiken][lnk-twin-properties], [aanbevolen procedures voor de configuratie van het apparaat in een IoT-oplossing][lnk-adm-best-practices], [Azure IoT Hub Device Provisioning Service][lnk-dps].
* **Configureren**: bulksgewijze configuratiewijzigingen en firmware-updates op apparaten ondersteunen terwijl de status en beveiliging behouden blijven. Voer deze apparaatbeheerbewerkingen bulksgewijs uit met behulp van de gewenste eigenschappen of met rechtstreekse methoden en broadcast-taken.
  
    *Meer weten*: [apparaatdubbeleigenschappen gebruiken][lnk-twin-properties], [configureren en controleren van IoT-apparaten op schaal][lnk-adm-how-to], [ Aanbevolen procedures voor de configuratie van het apparaat in een IoT-oplossing][lnk-adm-best-practices].
* **Bewaken**: de verzamelde status van alle apparaten bewaken, de status van lopende bewerkingen bewaken en operators attenderen op problemen die mogelijk hun aandacht vereisen.  Pas de apparaatdubbel toe, zodat apparaten in realtime bewerkingsvoorwaarden en de status van de update-bewerkingen kunnen rapporteren. Bouw krachtige dashboardrapporten die de meeste directe problemen melden via apparaatdubbel-query's.
  
    *Meer weten*: [apparaatdubbeleigenschappen gebruiken][lnk-twin-properties], [IoT Hub-querytaal voor apparaatdubbels, taken en berichtroutering] [ lnk-query-language], [Configureren en controleren van IoT-apparaten op schaal][lnk-adm-how-to], [aanbevolen procedures voor de configuratie van het apparaat in een IoT-oplossing] [lnk-adm-best-practices].
* **Buiten gebruik stellen**: apparaten vervangen of uit bedrijf nemen na een storing, upgradecyclus of aan het einde van de levensduur.  Gebruik de apparaatdubbel om apparaatgegevens te onderhouden als het fysieke apparaat wordt vervangen, of te archiveren als het apparaat buiten gebruik wordt gesteld. Gebruik het id-register van IoT Hub voor het veilig intrekken van apparaat-id's en -referenties.
  
    *Meer weten*: [Dubbeleigenschappen apparaat gebruiken][lnk-twin-properties], [Apparaatidentiteiten beheren][lnk-identity-registry].

## <a name="device-management-patterns"></a>Patronen voor apparaatbeheer
Met IoT Hub wordt de volgende set apparaatbeheerpatronen mogelijk gemaakt.  In de [Zelfstudies apparaatbeheer][lnk-get-started] wordt uitgebreid beschreven hoe u deze patronen zo kunt uitbreiden dat ze exact aansluiten bij uw scenario en hoe u nieuwe patronen ontwerpt op basis van deze kernsjablonen.

* **Opnieuw opstarten**: via de back-endtoepassing wordt het apparaat met een rechtstreekse methode geïnformeerd dat er opnieuw wordt opgestart.  Het apparaat maakt gebruik van de gerapporteerde eigenschappen om de opstartstatus van het apparaat bij te werken.
  
    ![Afbeelding van het opstartpatroon van apparaatbeheer][img-reboot_pattern]
* **Fabrieksinstellingen terugzetten**: via de back-endtoepassing wordt het apparaat via een rechtstreekse methode geïnformeerd dat het terugzetten van de fabrieksinstellingen is gestart.  Het apparaat maakt gebruik van de gerapporteerde eigenschappen om de status van het terugzetten van de fabrieksinstellingen van het apparaat bij te werken.
  
    ![Afbeelding van het patroon van herstel naar fabrieksinstellingen voor apparaatbeheer][img-facreset_pattern]
* **Configuratie**: voor de back-endtoepassing worden de gewenste eigenschappen gebruikt voor het configureren van software die op het apparaat wordt uitgevoerd.  Het apparaat maakt gebruik van de gerapporteerde eigenschappen om de configuratiestatus van het apparaat bij te werken.
  
    ![Afbeelding van het configuratiepatroon van apparaatbeheer][img-config_pattern]
* **Firmware-Update** -de back-end-app maakt gebruik van een configuratie van automatische device management om te selecteren van de apparaten voor het ontvangen van de update, om te zien van de apparaten waar u op zoek naar de update en het controleren van het updateproces. Het apparaat wordt een proces voor het downloaden, controleren, en de firmware-installatiekopie toe te passen en vervolgens het apparaat opnieuw opstarten voordat u opnieuw verbinding maken met de IoT Hub-service.  Gedurende het proces met meerdere stappen wordt gebruikgemaakt van de gerapporteerde eigenschappen om de voortgang en status van het apparaat bij te werken.
  
    ![Afbeelding van het firmware-updatepatroon van apparaatbeheer][img-fwupdate_pattern]
* **Voortgang en status rapporteren**: met de back-end van de oplossing worden query's voor apparaatdubbels uitgevoerd op een set apparaten om de status en voortgang te rapporteren van acties die op de apparaten worden uitgevoerd.
  
    ![Afbeelding van het proces- en statuspatroon van apparaatbeheerrapportage][img-report_progress_pattern]

## <a name="next-steps"></a>Volgende stappen
U kunt de mogelijkheden, patronen en codebibliotheken die IoT Hub biedt voor apparaatbeheer, gebruiken voor het maken van IoT-toepassingen die voldoen aan de enterprise-IoT-operatorvereisten in elke fase van de levenscyclus van een apparaat.

Zie de zelfstudie [Aan de slag met apparaatbeheer][lnk-get-started] voor meer informatie over de functies voor apparaatbeheer in IoT Hub.

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-twins-devguide]: iot-hub-devguide-device-twins.md
[lnk-get-started]: iot-hub-node-node-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: tutorial-device-twins.md
[lnk-hub-getstarted]: quickstart-send-telemetry-dotnet.md
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-query-language]: iot-hub-devguide-query-language.md
[lnk-c2d-methods]: quickstart-control-device-node.md
[lnk-methods-devguide]: iot-hub-devguide-direct-methods.md
[lnk-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-jobs-devguide]: iot-hub-devguide-jobs.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
[lnk-adm-best-practices]: iot-hub-configuration-best-practices.md
[lnk-adm-how-to]: iot-hub-auto-device-config.md