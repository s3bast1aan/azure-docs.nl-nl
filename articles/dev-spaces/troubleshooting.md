---
title: Oplossen van problemen | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Snelle Kubernetes-ontwikkeling met containers en microservices in Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: b2ef450a429b26843cf770a6243c6f4de932de43
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247316"
---
# <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen

Deze handleiding bevat informatie over veelvoorkomende problemen die mogelijk hebt u bij het gebruik van Azure Dev spaties.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Fout 'Failed to Azure Dev spaties controller maken'

U kunt deze fout tegenkomen wanneer er iets met het maken van de controller misgaat. Als het een tijdelijke fout, wordt verwijderen en opnieuw maken van de controller eraan.

### <a name="try"></a>Probeer:

Als u wilt verwijderen van de controller, gebruikt u Azure Dev spaties CLI. Het is niet mogelijk om dat te doen in Visual Studio of Cloud Shell. AZDS CLI installeren, installeert eerst de Azure CLI en voer vervolgens deze opdracht uit:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

En voer vervolgens deze opdracht voor het verwijderen van de controller:

```cmd
azds remove -g <resource group name> -n <cluster name>
```

De controller opnieuw kan worden gedaan in de CLI of Visual Studio. Volg de instructies in de zelfstudies als voor de eerste keer starten.


## <a name="error-service-cannot-be-started"></a>Fout 'Service kan niet worden gestart.'

U kunt deze fout tegenkomen wanneer de servicecode van uw niet kan worden gestart. De oorzaak is vaak in de gebruikerscode. Als u meer diagnostische gegevens, moet u de volgende wijzigingen aanbrengen aan uw opdrachten en -instellingen:

### <a name="try"></a>Probeer:

Op de opdrachtregel:

Bij het gebruik van _azds.exe_, gebruikt u de--uitgebreide opdrachtregeloptie te gebruiken, en gebruik de--output opdrachtregeloptie te gebruiken om op te geven van de indeling van de uitvoer.
 
    ```cmd
    azds up --verbose --output json
    ```

In Visual Studio:

1. Open **Extra > opties** en klikt u onder **projecten en oplossingen**, kiezen en **bouwen en uitvoeren**.
2. Wijzig de instellingen voor **MSBuild-project build uitvoer uitgebreidheid** naar **gedetailleerd** of **diagnostische**.

    ![Schermafbeelding van de opties in menu Extra](media/common/VerbositySetting.PNG)

## <a name="error-required-tools-and-configurations-are-missing"></a>Fout 'Vereiste hulpprogramma's en configuraties ontbreken'

Deze fout kan optreden bij het starten van VS Code: "[Azure Dev spaties] vereiste hulpprogramma's en configuraties om te bouwen en fouten opsporen in '[naam project]' ontbreken."
De fout betekent dat azds.exe bevindt zich niet in de omgevingsvariabele PATH, zoals te zien is in VS Code.

### <a name="try"></a>Probeer:

Start VS Code vanaf een opdrachtprompt waarvan de omgevingsvariabele PATH correct is ingesteld.

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Fout 'azds' wordt niet herkend als een interne of externe opdracht, programma of batch-bestand
 
U ziet deze fout als azds.exe is niet geïnstalleerd of niet juist geconfigureerd.

### <a name="try"></a>Probeer:

1. Controleer de locatie-%ProgramFiles%/Microsoft SDKs\Azure\Azure Dev spaties CLI (Preview) voor azds.exe. Als deze aanwezig is, moet u die locatie toevoegen aan de omgevingsvariabele PATH.
2. Als azds.exe niet is geïnstalleerd, moet u de volgende opdracht uitvoeren:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Fout 'upstream verbindingsfout of verbreken/reset voordat headers'
Mogelijk ziet u deze fout bij het openen van uw service. Bijvoorbeeld, wanneer u gaat naar de URL van de service in een browser. 

### <a name="reason"></a>Reden 
De poort van de container is niet beschikbaar. Dit probleem kan optreden omdat: 
* De container wordt nog steeds worden gebouwd en geïmplementeerd. Dit probleem kan zich voordoen als u uitvoeren `azds up` of het foutopsporingsprogramma start en vervolgens probeert te krijgen tot de container voordat deze is geïmplementeerd.
* Poortconfiguratie van de is niet consistent zijn tussen uw _Dockerfile_, Helm-diagram en servercode die wordt een poort geopend.

### <a name="try"></a>Probeer:
1. Als de container gebouwd wordt/geïmplementeerd, kunt u wacht 2-3 seconden en probeer het opnieuw openen van de service. 
1. Controleer de poortconfiguratie. De opgegeven poortnummers moet **identieke** in de volgende elementen:
    * **Docker-bestand:** opgegeven door de `EXPOSE` instructie.
    * **[Helm-diagram](https://docs.helm.sh):** opgegeven door de `externalPort` en `internalPort` waarden voor een service (vaak zich in een `values.yml` bestand),
    * Eventuele poorten die worden geopend in de toepassingscode, bijvoorbeeld in Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Configuratiebestand niet gevonden
U hebt uitgevoerd `azds up` en het volgende foutbericht weergegeven: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Reden
U moet uitvoeren `azds up` vanuit de hoofdmap van de code die u wilt uitvoeren, en moet u de code-map om uit te voeren met Azure Dev spaties initialiseren.

### <a name="try"></a>Probeer:
1. Wijzig de huidige directory naar de hoofdmap met de servicecode van uw. 
1. Als u nog geen een _azds.yaml_ bestand in de map code uitvoeren `azds prep` voor het genereren van Docker, Kubernetes en Azure Dev spaties activa.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Fout: 'de pipe programma azds onverwacht afgesloten met code 126.'
Starten van het foutopsporingsprogramma VS Code kan soms leiden tot deze fout. Dit is een bekend probleem.

### <a name="try"></a>Probeer:
1. Sluiten en opnieuw openen van VS Code.
2. Druk nogmaals op F5.


## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Fout bij foutopsporing 'geconfigureerd foutopsporing type 'coreclr' wordt niet ondersteund'
Het foutopsporingsprogramma VS Code wordt uitgevoerd, rapporteert de fout: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Reden
U hebt niet de VS Code-extensie voor Azure Dev opslagruimten geïnstalleerd op uw ontwikkelcomputer.

### <a name="try"></a>Probeer:
Installeer de [VS Code-extensie voor Azure Dev spaties](get-started-netcore.md).

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Het type of de naam 'MijnBibliotheek' kan niet worden gevonden.

### <a name="reason"></a>Reden 
De build-context is op het niveau van het project/service standaard, wordt niet dus een bibliotheek-project dat u gevonden.

### <a name="try"></a>Probeer:
Wat er moet gebeuren:
1. Wijzig de _azds.yaml_ bestand in te stellen van de build-context op het niveau van de oplossing.
2. Wijzig de _Dockerfile_ en _Dockerfile.develop_ bestanden om te verwijzen naar het project (_.csproj_) bestanden correct ten opzichte van de nieuwe bouwen context.
3. Plaats een _.dockerignore_ bestand naast het SLN-bestand en wijzig zo nodig.

U vindt een voorbeeld op https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>'Microsoft.DevSpaces/register/action' Autorisatiefout
U kunt de volgende fout tegenkomen wanneer u een Dev-adresruimte van Azure beheert en u werkt in een Azure-abonnement waarvoor u geen hebt eigenaar of bijdrager toegang.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Reden
Het geselecteerde Azure-abonnement is niet geregistreerd. de `Microsoft.DevSpaces` naamruimte.

### <a name="try"></a>Probeer:
Iemand met de eigenaar of bijdrager toegang tot het Azure-abonnement kan worden uitgevoerd de volgende Azure CLI-opdracht om handmatig te registreren de `Microsoft.DevSpaces` naamruimte:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev spaties lijkt niet te gebruiken van mijn bestaande docker-bestand voor het bouwen van een container 

### <a name="reason"></a>Reden
Azure Dev opslagruimten kunnen worden geconfigureerd om te verwijzen naar een specifieke _Dockerfile_ in uw project. Als deze wordt weergegeven met het Azure Dev opslagruimten wordt niet met behulp van de _Dockerfile_ u verwacht te maken van uw containers, moet u mogelijk u vertelt Azure Dev ruimten waar ze zich expliciet. 

### <a name="try"></a>Probeer:
Open de _azds.yaml_ -bestand dat is gegenereerd door Azure Dev spaties in uw project. Gebruik de `configurations->develop->build->dockerfile` richtlijn om te verwijzen naar de Dockerfile die u wilt gebruiken:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```