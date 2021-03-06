---
title: Een Azure Resource Manager-sjabloon exporteren | Microsoft Docs
description: Gebruik Azure Resource Manager om een sjabloon uit een bestaande resourcegroep te exporteren.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: tomfitz
ms.openlocfilehash: 3e1dd8ad49ceb126a14070ed641146d91419640a
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025740"
---
# <a name="export-an-azure-resource-manager-template-from-existing-resources"></a>Een Azure Resource Manager-sjabloon uit bestaande resources exporteren
In dit artikel wordt uitgelegd hoe u een Resource Manager-sjabloon exporteert uit bestaande resources in uw abonnement. Aan de hand van de gegenereerde sjabloon kunt u een beter begrip krijgen van de syntaxis van sjablonen.

Er zijn twee manieren om een sjabloon te exporteren:

* U kunt de **daadwerkelijke sjabloon die u voor implementatie hebt gebruikt**, exporteren. De geëxporteerde sjabloon bevat alle parameters en variabelen precies zoals ze worden weergegeven in de oorspronkelijke sjabloon. Deze methode is handig als u resources via de portal hebt geïmplementeerd en u de sjabloon voor het maken van deze resources wilt zien. Deze sjabloon kan ongewijzigd worden gebruikt. 
* U kunt een **gegenereerde sjabloon exporteren met de huidige status van de resourcegroep**. De geëxporteerde sjabloon is niet op basis van een sjabloon die u voor implementatie gebruikt. In plaats daarvan wordt een sjabloon die een 'snapshot' of 'back-up' van de resourcegroep wordt gemaakt. De geëxporteerde sjabloon heeft veel vastgelegde waarden en waarschijnlijk niet zoveel parameters als u doorgaans zou definiëren. Gebruik deze optie om het implementeren van resources op de dezelfde resourcegroep. Als u wilt deze sjabloon voor een andere resourcegroep gebruikt, moet u wellicht aanzienlijk wijzigen.

Dit artikel ziet beide benaderingen via de portal.

## <a name="deploy-resources"></a>Resources implementeren
Laten we beginnen door resources te implementeren in Azure, zodat u die kunt exporteren als een sjabloon. Als u al een resourcegroep in uw abonnement hebt die u wilt exporteren naar een sjabloon hebt, kunt u deze sectie overslaan. De rest van dit artikel wordt ervan uitgegaan dat u de SQL database-oplossing in deze sectie wordt weergegeven en web-app hebt geïmplementeerd. Als u een andere oplossing gebruikt, is de ervaring mogelijk iets anders, maar de stappen voor het exporteren van een sjabloon blijven hetzelfde. 

1. In de [Azure-portal](https://portal.azure.com), selecteer **maken van een resource**.
   
      ![Selecteer Nieuw](./media/resource-manager-export-template/new.png)
2. Zoek naar **web-app + SQL** en selecteer dit resultaat uit de beschikbare opties.
   
      ![Web-app zoeken en SQL](./media/resource-manager-export-template/webapp-sql.png)

3. Selecteer **Maken**.

      ![Selecteer Maken](./media/resource-manager-export-template/create.png)

4. Geef de vereiste waarden op voor de web-app en SQL-database. Selecteer **Maken**.

      ![Web- en SQL-waarde opgeven](./media/resource-manager-export-template/provide-web-values.png)

De implementatie kan een minuut duren. Als de implementatie is voltooid, ziet u de oplossing in uw abonnement.

## <a name="view-template-from-deployment-history"></a>Sjabloon weergeven vanuit implementatiegeschiedenis
1. Ga naar de resourcegroep voor uw nieuwe resourcegroep. U ziet dat de portal het resultaat van de laatste implementatie geeft. Selecteer deze koppeling.
   
      ![Resourcegroep](./media/resource-manager-export-template/select-deployment.png)
2. U ziet nu de geschiedenis van de implementaties voor de groep. In uw geval worden de portal waarschijnlijk slechts een implementatie. Selecteer deze implementatie.
   
     ![Laatste implementatie](./media/resource-manager-export-template/select-history.png)
3. De portal geeft een samenvatting van de implementatie. Deze samenvatting bevat de status van de implementatie en bewerkingen, en de waarden die u hebt opgegeven voor de parameters. Selecteer **Sjabloon weergeven** om de sjabloon te bekijken die u voor de implementatie hebt gebruikt.
   
     ![Implementatiesamenvatting bekijken](./media/resource-manager-export-template/view-template.png)
4. Resource Manager haalt de volgende zeven bestanden voor u op:
   
   1. **Sjabloon**: de sjabloon die de infrastructuur voor uw oplossing definieert. Toen u het opslagaccount via de portal maakte, heeft Resource Manager een sjabloon gebruikt om het te implementeren. De sjabloon is opgeslagen voor toekomstig gebruik.
   2. **Parameters**: een parameterbestand dat u kunt gebruiken om tijdens de implementatie waarden door te geven. Het bevat de waarden die u tijdens de eerste implementatie hebt opgegeven. U kunt deze waarden wijzigen wanneer u de sjabloon opnieuw gaat implementeren.
   3. **CLI** -An Azure CLI-scriptbestand dat u gebruiken kunt om de sjabloon te implementeren.
   4. **PowerShell**: een Azure PowerShell-scriptbestand dat u kunt gebruiken om de sjabloon te implementeren.
   5. **.NET**: een .NET-klasse die u kunt gebruiken om de sjabloon te implementeren.
   6. **Ruby**: een Ruby-klasse die u kunt gebruiken om de sjabloon te implementeren.
      
      De portal wordt standaard weergegeven van de sjabloon.
      
       ![Sjabloon weergeven](./media/resource-manager-export-template/see-template.png)
      
Deze sjabloon is de daadwerkelijke sjabloon die wordt gebruikt voor het maken van uw web-app en SQL-database. De sjabloon bevat parameters waarmee u tijdens de implementatie verschillende waarden kunt opgeven. Zie [Azure Resource Manager-sjablonen samenstellen](resource-group-authoring-templates.md) voor meer informatie over de structuur van een sjabloon.

## <a name="export-the-template-from-resource-group"></a>De sjabloon vanuit de resourcegroep exporteren
Als u hebt handmatig gewijzigd van uw resources of resources toegevoegd in meerdere implementaties, bij het ophalen van een sjabloon van de implementatiegeschiedenis van de niet worden opgenomen in de huidige status van de resourcegroep. In deze sectie ziet u hoe u een sjabloon kunt exporteren die de huidige status van de resourcegroep voorstelt. Het is bedoeld als een momentopname van de resourcegroep waarin u kunt implementeren naar dezelfde resourcegroep. Voor het gebruik van de geëxporteerde sjabloon voor andere oplossingen, moet u deze aanzienlijk wijzigen.

> [!NOTE]
> U kunt een sjabloon voor een resourcegroep die meer dan 200 bronnen heeft, niet exporteren.
> 
> 

1. Selecteer **Automatiseringsscript** om de sjabloon voor een resourcegroep te bekijken.
   
      ![Resourcegroep exporteren](./media/resource-manager-export-template/select-automation.png)
   
     Resource Manager evalueert de resources in de resourcegroep en genereert een sjabloon voor deze resources. Niet alle resourcetypen ondersteunen de functie voor het exporteren van sjablonen. Mogelijk ziet u een foutmelding dat er een probleem is met het exporteren. U krijgt meer informatie over het afhandelen van die problemen in de sectie [Problemen met exports oplossen](#fix-export-issues).
2. U ziet weer de zes bestanden die u kunt gebruiken om de oplossing opnieuw te implementeren. Deze keer ziet de sjabloon er echter iets anders uit. U ziet dat de gegenereerde sjabloon minder parameters bevat dan de sjabloon in de vorige sectie. Bovendien zijn veel van de waarden (zoals de locatie en de SKU-waarden) hardgecodeerd in deze sjabloon en kunnen deze waarden niet worden opgegeven via een parameter. Het is raadzaam om deze sjabloon eerst te bewerken om efficiënter gebruik te maken van parameters. 
   
3. U hebt een aantal opties om verder te werken met deze sjabloon. U kunt de sjabloon downloaden en er lokaal mee werken met een JSON-editor. Of u kunt de sjabloon opslaan in uw bibliotheek en er mee werken via de portal.
   
     Als u vertrouwd met een JSON-editor, zoals [tegenover Code](https://code.visualstudio.com/) of [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md), werkt u liever lokaal in de sjabloon downloaden en gebruiken van deze editor. Selecteer **Downloaden** als u lokaal wilt werken.
   
      ![Een sjabloon downloaden](./media/resource-manager-export-template/download-template.png)
   
     Als u niet worden ingesteld met een JSON-editor, kunt u liever bewerken van het sjabloon via de portal. De rest van dit artikel wordt ervan uitgegaan dat u de sjabloon hebt opgeslagen naar de bibliotheek in de portal. U brengt echter dezelfde syntaxiswijzigingen aan in de sjabloon, ongeacht of u lokaal met een JSON-editor werkt of via de portal. Selecteer **Toevoegen aan bibliotheek** als u via de portal wilt werken.
   
      ![Toevoegen aan bibliotheek](./media/resource-manager-export-template/add-to-library.png)
   
     Wanneer een sjabloon toe te voegen aan de bibliotheek, geeft u de sjabloon een naam en beschrijving. Selecteer vervolgens **Opslaan**.
   
     ![Sjabloonwaarden](./media/resource-manager-export-template/save-library-template.png)
4. Als u een sjabloon wilt weergeven die is opgeslagen in uw bibliotheek, selecteert u **Meer services**, typt u **sjablonen** om de resultaten te filteren, en selecteert u **Sjablonen**.
   
      ![Sjablonen zoeken](./media/resource-manager-export-template/find-templates.png)
5. Selecteer de sjabloon met de naam die u hebt opgeslagen.
   
      ![Sjabloon selecteren](./media/resource-manager-export-template/select-saved-template.png)

## <a name="customize-the-template"></a>De sjabloon aanpassen
De geëxporteerde sjabloon werkt prima als u voor elke implementatie dezelfde web-app en SQL-database wilt maken. Resource Manager biedt echter opties die u in staat stellen sjablonen met veel meer flexibiliteit te implementeren. In dit artikel ziet u hoe u parameters toevoegt voor de naam van de databasebeheerder en het wachtwoord. U kunt deze benadering ook gebruiken om meer flexibiliteit toe te voegen voor andere waarden in de sjabloon.

1. Selecteer **Bewerken** om de sjabloon aan te passen.
   
     ![Sjabloon weergeven](./media/resource-manager-export-template/select-edit.png)
2. Selecteer de sjabloon.
   
     ![Sjabloon bewerken](./media/resource-manager-export-template/select-added-template.png)
3. Toevoegen als u wilt doorgeven waarden die u misschien wilt opgeven tijdens de implementatie, de volgende twee parameters voor de **parameters** sectie in de sjabloon:

   ```json
   "administratorLogin": {
       "type": "String"
   },
   "administratorLoginPassword": {
       "type": "SecureString"
   },
   ```

4. U kunt de nieuwe parameters gebruiken door de definitie van de SQL-server in de sectie **resources** te vervangen. U ziet dat **administratorLogin** en **administratorLoginPassword** nu parameterwaarden gebruiken.

   ```json
   {
       "comments": "Generalized from resource: '/subscriptions/{subscription-id}/resourceGroups/exportsite/providers/Microsoft.Sql/servers/tfserverexport'.",
       "type": "Microsoft.Sql/servers",
       "kind": "v12.0",
       "name": "[parameters('servers_tfserverexport_name')]",
       "apiVersion": "2014-04-01-preview",
       "location": "South Central US",
       "scale": null,
       "properties": {
           "administratorLogin": "[parameters('administratorLogin')]",
           "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
           "version": "12.0"
       },
       "dependsOn": []
   },
   ```

6. Selecteer **OK** wanneer u klaar bent voor het bewerken van de sjabloon.
7. Klik op **Opslaan** om de wijzigingen in de sjabloon op te slaan.
   
     ![Sjabloon opslaan](./media/resource-manager-export-template/save-template.png)
8. Selecteer **Implementeren** om de bijgewerkte sjabloon opnieuw te implementeren.
   
     ![Sjabloon implementeren](./media/resource-manager-export-template/redeploy-template.png)
9. Geef parameterwaarden op en selecteer een resourcegroep waarin u de resources wilt implementeren.


## <a name="fix-export-issues"></a>Problemen met exports oplossen
Niet alle resourcetypen ondersteunen de functie voor het exporteren van sjablonen. Wordt alleen weergegeven exporteren problemen bij het exporteren van een resourcegroep in plaats van vanaf de geschiedenis van uw implementatie. Als uw laatste implementatie een nauwkeurige weergave is van de huidige status van de resourcegroep, kunt u het beste de sjabloon exporteren uit de implementatiegeschiedenis in plaats van uit de resourcegroep. Alleen exporteren uit een resourcegroep wanneer u hebt wijzigingen aangebracht aan de resourcegroep die niet zijn gedefinieerd in één sjabloon.

Export-problemen oplossen door handmatig de ontbrekende resources weer in de sjabloon toevoegen. Het foutbericht bevat de brontypen die niet kunnen worden geëxporteerd. Zoek dat resourcetype in de Engelstalige [naslaghandleiding over sjablonen](/azure/templates/). Als u bijvoorbeeld handmatig een virtuele netwerkgateway wilt toevoegen, raadpleegt u [Microsoft.Network/virtualNetworkGateways](/azure/templates/microsoft.network/virtualnetworkgateways) in de handleiding. De verwijzing naar de sjabloon biedt u de JSON de resource toevoegen aan uw sjabloon.

Nadat u de JSON-indeling voor de bron, moet u de waarden van de resource. U ziet de waarden voor de bron via de GET-bewerking in de REST-API voor het brontype. Bijvoorbeeld, als u de waarden voor uw virtuele netwerkgateway, Zie [ophalen van de virtuele netwerkgateways -](/rest/api/network-gateway/virtualnetworkgateways/get).

## <a name="next-steps"></a>Volgende stappen

* U kunt een sjabloon implementeren via [PowerShell](resource-group-template-deploy.md), [Azure CLI](resource-group-template-deploy-cli.md) of [REST API](resource-group-template-deploy-rest.md).
* Zie voor het exporteren van een sjabloon via PowerShell [exporteren Azure Resource Manager-sjablonen met PowerShell](resource-manager-export-template-powershell.md).
* Zie voor het exporteren van een sjabloon via Azure CLI [exporteren Azure Resource Manager-sjablonen met Azure CLI](resource-manager-export-template-cli.md).

