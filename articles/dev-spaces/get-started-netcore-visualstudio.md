---
title: Een Kubernetes-ontwikkelomgeving in de cloud maken met .NET Core en Visual Studio | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: tutorial
description: Snelle Kubernetes-ontwikkeling met containers en microservices in Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 452746e897f95f12a25522ea9f37ca0254a12d6d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38612866"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core-and-visual-studio"></a>Aan de slag in Azure Dev Spaces met behulp van .NET Core en Visual Studio

In deze handleiding leert u het volgende:

- Azure Dev Spaces instellen met een beheerd Kubernetes-cluster in Azure.
- Iteratief code ontwikkelen in containers met Visual Studio.
- Twee afzonderlijke services ontwikkelen en de DNS-servicedetectie van Kubernetes gebruiken om een andere service aan te roepen.
- Uw code op een productieve manier ontwikkelen en testen in een teamomgeving.

[!INCLUDE[](includes/see-troubleshooting.md)]

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="get-the-visual-studio-tools"></a>Visual Studio-hulpprogramma's downloaden
1. Installeer de nieuwste versie van [Visual Studio 2017](https://www.visualstudio.com/vs/)
1. Zorg ervoor dat de volgende werkbelasting is geselecteerd in het installatieprogramma van Visual Studio:
    * ASP.NET-ontwikkeling en webontwikkeling
1. [Visual Studio Tools voor Kubernetes](https://aka.ms/get-azds-visualstudio) installeren

## <a name="create-a-web-app-running-in-a-container"></a>Een web-app maken die wordt uitgevoerd in een container

In deze sectie maakt u een ASP.NET Core-web-app en voert u deze uit in een container in Kubernetes.

### <a name="create-an-aspnet-web-app"></a>Een ASP.NET-web-app maken

Maak een nieuw project in Visual Studio 2017. Op dit moment moet het project een **ASP.NET Core-webtoepassing** zijn. Geef het project de naam **webfrontend**.

![](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

Selecteer de sjabloon **Web Application (Model-View-Controller)** en selecteer **.NET Core** en **ASP.NET Core 2.0** in de twee vervolgkeuzelijsten boven in het dialoogvenster. Klik op **OK** om het project aan te maken.

![](media/get-started-netcore-visualstudio/NewProjectDialog2.png)


### <a name="enable-dev-spaces-for-an-aks-cluster"></a>Dev Spaces inschakelen voor een AKS-cluster

Met het project dat u zojuist hebt gemaakt, selecteert u **Azure Dev Spaces** in de vervolgkeuzelijst met opstartinstellingen, zoals hieronder wordt weergegeven.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Controleer in het dialoogvenster dat vervolgens wordt weergegeven, of u bent aangemeld bij het juiste account, en selecteer vervolgens een bestaand Kubernetes-cluster.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.PNG)

Laat de waarde in de vervolgkeuzelijst **Space** voorlopig op `default` staan. Later besteden we meer aandacht aan deze optie. Schakel het selectievakje **Publicly Accessible** in zodat de web-app toegankelijk is via een openbaar eindpunt. Deze instelling is niet vereist, maar is wel handig om verderop in dit scenario enkele dingen te demonstreren. Maar u hoeft u geen zorgen te maken, want in beide gevallen kunt u fouten op de website opsporen met Visual Studio.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Klik op **OK** om het cluster te selecteren of een cluster te maken.

Als u een cluster kiest dat niet is ingeschakeld om te werken met Azure Dev Spaces, ziet u een bericht waarin u wordt gevraagd of u dit cluster wilt configureren.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Kies **OK**.

 Er wordt een achtergrondtaak gestart om dit te doen. Het uitvoeren van deze taak duurt enkele minuten. Als u wilt zien of de taak nog steeds actief is, plaatst u de muisaanwijzer op het pictogram **Background tasks** in de linkerhoek onder in de statusbalk, zoals wordt weergegeven in de volgende afbeelding.

![](media/get-started-netcore-visualstudio/BackgroundTasks.PNG)

> [!Note]
> U kunt pas fouten in een toepassing oplossen wanneer de ontwikkelomgeving is gemaakt.

### <a name="look-at-the-files-added-to-project"></a>Bestanden bekijken die zijn toegevoegd aan het project
Terwijl u wacht totdat de ontwikkelomgeving is gemaakt, kunt u bekijken welke bestanden aan het project zijn toegevoegd toen u ervoor koos om een ontwikkelomgeving te gebruiken.

Allereerst ziet u dat een map met de naam `charts` is toegevoegd, en dat er binnen deze map een [Helm-grafiek](https://docs.helm.sh) voor de toepassing is klaargezet. Deze bestanden worden gebruikt om de toepassing in de ontwikkelomgeving te implementeren.

U ziet dat er een bestand met de naam `Dockerfile` is toegevoegd. Dit bestand bevat gegevens die nodig zijn om van de toepassing een pakket te maken met de standaard-Docker-indeling.

Tot slot ziet u een bestand met de naam `azds.yaml`. Dit bestand bevat configuratie over de ontwikkeltijd die de ontwikkelomgeving nodig heeft.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Fouten opsporen in een Kubernetes-container
Zodra de ontwikkelomgeving is gemaakt, kunt u fouten oplossen voor de toepassing. Stel een onderbrekingspunt in de code in, bijvoorbeeld op regel 20 in het bestand `HomeController.cs`, waarbij de variabele `Message` wordt ingesteld. Druk op **F5** om de foutopsporing te starten. 

Visual Studio communiceert met de ontwikkelomgeving om de toepassing te compileren en te implementeren. Vervolgens wordt een browser geopend met de web-app. Het lijkt misschien alsof de container lokaal wordt uitgevoerd, maar dat is niet zo. De container wordt uitgevoerd in de ontwikkelomgeving in Azure. De reden voor het localhost-adres is dat Azure Dev Spaces een tijdelijke SSH-tunnel maakt naar de container die wordt uitgevoerd in AKS.

Klik boven aan de pagina op de koppeling **About** om het onderbrekingspunt te activeren. U hebt volledige toegang tot foutopsporingsgegevens (net als wanneer de code lokaal wordt uitgevoerd), zoals als de aanroep-stack, lokale variabelen, informatie over uitzonderingen, enzovoort.

## <a name="iteratively-develop-code"></a>Code iteratief ontwikkelen

Azure Dev Spaces draait niet alleen om het ophalen van code die wordt uitgevoerd in Kubernetes. Het gaat er om dat u de codewijzigingen snel en iteratief toegepast kunt zien in een Kubernetes-omgeving in de cloud.

### <a name="update-a-content-file"></a>Een inhoudsbestand bijwerken
1. Zoek het bestand `./Views/Home/Index.cshtml` en bewerk de HTML-code. Wijzig bijvoorbeeld regel 70 waar `<h2>Application uses</h2>` staat in iets als: `<h2>Hello k8s in Azure!</h2>`
1. Sla het bestand op.
1. Ga naar de browser en vernieuw de pagina. De bijgewerkte HTML-code wordt op de webpagina weergegeven.

Wat is er gebeurd? Voor bewerkingen van inhoudsbestanden, zoals HTML en CSS, hoeft een .NET Core-web-app niet opnieuw te worden gecompileerd. Tijdens een actieve F5-sessie worden alle gewijzigde inhoudsbestanden dus automatisch gesynchroniseerd in de actieve container in AKS, zodat u de bewerkingen van de inhoud direct kunt zien.

### <a name="update-a-code-file"></a>Een codebestand bijwerken
Het bijwerken van codebestanden vereist iets meer werk, omdat een .NET Core-app bijgewerkte binaire toepassingsbestanden moet bouwen en produceren.

1. Stop de foutopsporing in Visual Studio.
1. Open het codebestand met de naam `Controllers/HomeController.cs` en bewerk het bericht dat op de pagina Info wordt weergegeven: `ViewData["Message"] = "Your application description page.";`
1. Sla het bestand op.
1. Druk op **F5** als u de foutopsporing weer wilt starten. 

In plaats van telkens als codewijzigingen zijn aangebracht een nieuwe containerinstallatiekopie opnieuw te bouwen en opnieuw te implementeren, wat vaak behoorlijk wat tijd kost, hercompileert Azure Dev Spaces incrementeel code binnen de bestaande container voor een snellere bewerkings-/foutopsporingslus.

Vernieuw de web-app in de browser en ga naar de pagina Info. U ziet dat uw aangepaste bericht wordt weergegeven in de gebruikersinterface.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over teamontwikkeling](team-development-netcore-visualstudio.md)
