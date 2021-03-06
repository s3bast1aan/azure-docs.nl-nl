---
title: 'Zelfstudie: Een Azure Stream Analytics-taak met CI/CD met behulp van VSTS'
description: In dit artikel wordt beschreven hoe u een Stream Analytics-taak met CI/CD implementeert met behulp van VSTS.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 7/10/2018
ms.openlocfilehash: 303c1cfaf2b91712f706c5b78e027bb02739c770
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39074211"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-vsts"></a>Zelfstudie: Een Azure Stream Analytics-taak met CI/CD met behulp van VSTS
In deze zelfstudie wordt beschreven hoe u continue integratie en implementatie instelt voor een Azure Stream Analytics-taak met behulp van Visual Studio-teamservices. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Broncodebeheer aan uw project toevoegen
> * Een build-definitie in Team Services maken
> * Een release-definitie in Team Services maken
> * Automatisch een upgrade en een toepassing implementeren

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat u het volgende hebt voordat u begint:

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.
* Installeer [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) en de workload **Azure-ontwikkeling** of **Gegevensopslag en verwerking**.
* Maak een [Stream Analytics-project in Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-quick-create-vs).
* Maak een [Visual Studio Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)-account.

## <a name="configure-nuget-package-dependency"></a>Afhankelijkheid van NuGet-pakket configureren
Om automatisch een build te kunnen maken en automatisch te implementeren op een willekeurige computer, moet u het NuGet-pakket `Microsoft.Azure.StreamAnalytics.CICD` gebruiken. Dit bevat de tools voor MSBuild, lokaal uitvoeren en implementatie die ondersteuning bieden voor het continue integratie- en implementatieproces van Stream Analytics Visual Studio-projecten. Zie [Stream Analytics CI/CD-hulpprogramma's](stream-analytics-tools-for-visual-studio-cicd.md) voor meer informatie.

Voeg **packages.config** toe aan de projectmap.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>Uw Visual Studio-oplossing delen met een nieuwe Git-opslagplaats van Team Services
Deel de bronbestanden van uw toepassing met een teamproject in Team Services zodat u builds kunt genereren.  

1. Maak een nieuwe Git-opslagplaats voor uw project door op de statusbalk in de linkeronderhoek van Visual Studio **Add to Source Control** en vervolgens **Git** te selecteren. 

2. Ga naar de **Synchronization**-weergave in **Team Explorer** en selecteer onder **Push to Visual Studio Team Services** de knop **Publish Git Repo**.

   ![Git-opslagplaats pushen](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishgitrepo.png)

3. Controleer uw e-mail en selecteer uw account in de vervolgkeuzelijst **Team Services Domain**. Voer de naam van de opslagplaats in en selecteer **Publish repository**.

   ![Git-opslagplaats pushen](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishcode.png)

    Als u de opslagplaats pusht, wordt er een nieuw teamproject voor uw account gemaakt met dezelfde naam als de lokale opslagplaats. Als u de repro in een bestaand teamproject wilt maken, klikt u naast de naam van de **opslagplaats** op **Advanced** en selecteert u een teamproject. U kunt uw code in de browser weergeven door **See it on the web** te selecteren.
 
## <a name="configure-continuous-delivery-with-vsts"></a>Continue levering configureren met VSTS
Een definitie van een Team Services-build beschrijft een werkstroom die bestaat uit build-stappen die achtereenvolgens worden uitgevoerd. Meer informatie over [Definities van Team Services-builds](https://www.visualstudio.com/docs/build/define/create). 

Een definitie van een Team Services-release beschrijft een werkstroom waarmee een toepassingspakket in een cluster wordt geïmplementeerd. Als de definities van de build en de release samen worden gebruikt, wordt hiermee de hele werkstroom uitgevoerd, te beginnen met bronbestanden en te eindigen met het uitvoeren van een toepassing in uw cluster. Meer informatie over [release-definities ](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition) van Team Services.

### <a name="create-a-build-definition"></a>Een build-definitie maken
Open een webbrowser en navigeer naar het teamproject dat u zojuist hebt gemaakt in [Visual Studio Team Services](https://app.vsaex.visualstudio.com/). 

1. Selecteer op het tabblad **Build & Release** de optie **Builds** en vervolgens **+New**.  Selecteer **VSTS Git** en **Continue**.
    
    ![Bron selecteren](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source.png)

2. Klik in **Select a template** op **Empty Process** om met een lege definitie te beginnen.
    
    ![Build-sjabloon kiezen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template.png)

3. Schakel onder **Triggers** continue integratie in door de triggerstatus **Enable continuous integration** in te stellen.  Selecteer **Save and queue** om handmatig een build te starten. 
    
    ![Triggerstatus](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger.png)

4. Builds worden ook getriggerd na pushen of inchecken. Als u de voortgang van de build wilt controleren, schakelt u over naar het tabblad **Builds**.  Als u hebt gecontroleerd dat de build correct wordt uitgevoerd, moet u een releasedefinitie definiëren waarmee uw toepassing in een cluster wordt geïmplementeerd. Klik met de rechtermuisknop op het beletselteken naast uw builddefinitie en selecteer **Edit**.

5.  Voer in **Tasks** "Hosted" in als de **Agent queue**.
    
    ![Agentwachtrij selecteren](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue.png) 

6. Klik in **Phase 1** op **+** en voeg een **NuGet**-taak toe.
    
    ![Een Nuget-taak toevoegen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget.png)

7. Vouw **Advanced** uit en voeg `$(Build.SourcesDirectory)\packages` toe aan **Destination directory**. Behoud de overige NuGet-standaardconfiguratiewaarden.

   ![NuGet-taak configureren](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-config.png)

8. Klik in **Phase 1** op **+** en voeg een **MSBuild**-taak toe.

   ![MSBuild-taak toevoegen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild-task.png)

9. Wijzig de **MSBuild Arguments** in het volgende:

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![MSBuild-taak configureren](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild.png)

10. Klik in **Phase 1** op **+** en voeg een **Azure Resource Group Deployment**-taak toe. 
    
    ![Een Azure Resource Group Deployment-taak toevoegen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy.png)

11. Vouw **Azure Details** uit en vul de configuratie met het volgende:
    
    |**Instelling**  |**Voorgestelde waarde**  |
    |---------|---------|
    |Abonnement  |  Kies uw abonnement.   |
    |Bewerking  |  Resourcegroep maken of bijwerken   |
    |Resourcegroep  |  Voer een resourcegroepnaam in.   |
    |Template  | [Uw oplossingspad] \bin\Debug\Deploy\\[Naam van uw project]. JobTemplate.json   |
    |Sjabloonparameters  | [Uw oplossingspad] \bin\Debug\Deploy\\[Naam van uw project].JobTemplate.parameters.json   |
    |Sjabloonparameters overschrijven  | Typ de sjabloonparameters die u wilt overschrijven in het tekstvak. Bijvoorbeeld: –storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre). Deze eigenschap is optioneel, maar er treden fouten op in uw build als belangrijke parameters worden niet overschreven.    |
    
    ![Eigenschappen instellen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy-2.png)

12. Klik op **Save & Queue** om de builddefinitie te testen.
    
    ![Te overschrijven parameters instellen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-queue.png)

### <a name="failed-build-process"></a>Mislukt buildproces
Mogelijk ontvangt u fouten wegens implementatieparameters van null als u geen sjabloonparameters hebt overschreven in de **Azure Resource Group Deployment**-taak van uw builddefinitie. Ga terug naar de builddefinitie en overschrijf de parameters van null om de fout op te lossen.

   ![Buildproces mislukt](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>Wijzigingen doorvoeren en pushen om een release te activeren
Controleer of de pijplijn voor continue integratie functioneert door enkele codewijzigingen aan Team Services na te gaan.    

Terwijl u de code schrijft, worden de wijzigingen automatisch in Visual Studio bijgehouden. Voer wijzigingen door in de lokale Git-opslagplaats door het pictogram voor wijzigingen in behandeling te selecteren in de statusbalk rechtsonder.

1. Voeg aan de weergave **Changes** in Team Explorer een bericht toe waarin u de update beschrijft en voer de wijzigingen door.

    ![Wijzigingen doorvoeren en pushen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes.png)

2. Selecteer het pictogram voor de statusbalk met niet-gepubliceerde wijzigingen of de synchronisatieweergave in Team Explorer. Selecteer **Push** om de code bij te werken in Team Services/TFS.

    ![Wijzigingen doorvoeren en pushen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-2.png)

Als u de wijzigingen naar Team Services pusht, wordt er automatisch een build geactiveerd.  Als de builddefinitie is voltooid, wordt er automatisch een release gemaakt en wordt gestart met het bijwerken van de toepassing in het cluster.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een resourcegroep niet meer nodig hebt, verwijdert u de resourcegroep, de streamingtaak en alle gerelateerde resources. Door de taak te verwijderen, voorkomt u dat de streaming-eenheden die door de taak worden verbruikt, in rekening worden gebracht. Als u denkt dat u de taak in de toekomst nog gaat gebruiken, kunt u deze stoppen en later opnieuw starten wanneer dat nodig is. Als u deze taak niet meer gaat gebruiken, verwijdert u alle resources die in deze zelfstudie zijn gemaakt. Daarvoor voert u de volgende stappen uit:

1. Klik in het menu aan de linkerkant in Azure Portal op **Resourcegroepen** en klik vervolgens op de resource die u hebt gemaakt.  
2. Klik op de pagina van uw resourcegroep op **Verwijderen**, typ de naam van de resource die u wilt verwijderen in het tekstvak en klik vervolgens op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het gebruik van Azure Stream Analytics-hulpprogramma's voor Visual Studio voor het instellen van een continue integratie en implementatie, gaat u verder met artikelen over het instellen van de CI/CD-pijplijn:

> [!div class="nextstepaction"]
> [Continue integratie en ontwikkeling met Stream Analytics-hulpprogramma’s](stream-analytics-tools-for-visual-studio-cicd.md)