---
title: Azure Policy gebruiken voor het beperken van de installatie van de VM-extensie | Microsoft Docs
description: Azure Policy gebruiken voor het beperken van extensie-implementaties.
services: virtual-machines-linux
documentationcenter: ''
author: zroiy
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz;cynthn
ms.openlocfilehash: b63bfba4c1d84480724c4b03891f068145109626
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411723"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Azure Policy gebruiken voor het beperken van extensies installatie op Windows-VM 's

Als u voorkomen dat het gebruik of de installatie van bepaalde uitbreidingen op uw Windows-VM's wilt, kunt u een Azure-beleid met behulp van PowerShell om te beperken van extensies voor virtuele machines binnen een resourcegroep maken. 

In deze zelfstudie wordt Azure PowerShell in Cloud Shell, die voortdurend wordt bijgewerkt naar de nieuwste versie. Als u PowerShell lokaal wilt installeren en gebruiken, is voor deze zelfstudie versie 3.6 of hoger van de Azure PowerShell-module vereist. Voer ` Get-Module -ListAvailable AzureRM` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-azurerm-ps). 

## <a name="create-a-rules-file"></a>Maak een regelbestand

Als u wilt beperken welke extensies kunnen worden geïnstalleerd, moet u beschikken over een [regel](/azure/azure-policy/policy-definition#policy-rule) voor de logica voor het identificeren van de extensie.

Dit voorbeeld ziet u hoe u voor het weigeren van de uitbreidingen die zijn gepubliceerd door 'Microsoft.Compute' door het maken van een bestand in Azure Cloud Shell, maar als u in PowerShell lokaal werkt, kunt u ook een lokaal bestand gemaakt en vervangen door het pad ($home/clouddrive) met het pad naar de lokaal bestand op uw computer.

In een [Cloud Shell](https://shell.azure.com/powershell), type:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Kopieer en plak de volgende .json in het bestand.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Wanneer u klaar bent, bereikt de **Ctrl + O** en vervolgens **Enter** op te slaan. Raak **Ctrl + X** te sluiten van het bestand en afsluiten.

## <a name="create-a-parameters-file"></a>Maak een parameterbestand

U moet ook een [parameters](/azure/azure-policy/policy-definition#parameters) -bestand dat wordt gemaakt van een structuur moet worden gebruikt voor het doorgeven in een lijst van de extensies blokkeren. 

In dit voorbeeld laat zien hoe een parameterbestand maken voor virtuele machines in de Cloud Shell, maar als u in PowerShell lokaal werkt, kunt u ook een lokaal bestand gemaakt en vervangen door het pad ($home/clouddrive) met het pad naar het lokale bestand op uw computer.

In [Cloud Shell](https://shell.azure.com/powershell), type:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Kopieer en plak de volgende .json in het bestand.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

Wanneer u klaar bent, bereikt de **Ctrl + O** en vervolgens **Enter** op te slaan. Raak **Ctrl + X** te sluiten van het bestand en afsluiten.

## <a name="create-the-policy"></a>Het beleid maken

Een beleidsdefinitie is een object dat wordt gebruikt voor het opslaan van de configuratie die u wilt gebruiken. De beleidsdefinitie maakt gebruik van de regels en de parameters-bestanden voor het definiëren van het beleid. Maak een beleid definitie met de [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) cmdlet.

 De beleidsregels en parameters zijn bestanden die u hebt gemaakt en opgeslagen als JSON-bestanden in uw cloudshell.


```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Het beleid toewijzen

In dit voorbeeld wordt het beleid voor toegewezen aan een resource-groep met [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment). Een virtuele machine hebt gemaakt in de **myResourceGroup** resourcegroep niet mogelijk voor het installeren van de Agent voor VM-toegang of de Custom Script-extensies. 

Gebruik de [Get-AzureRMSubscription | Format-Table](/powershell/module/azurerm.profile/get-azurermsubscription) cmdlet om op te halen van uw abonnements-ID in plaats van het certificaat in het voorbeeld.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzureRMPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>Het beleid testen

Als u wilt testen van het beleid, probeert u de extensie voor VM-toegang. De volgende zou moeten mislukken met het bericht ' Set-AzureRmVMAccessExtension: Resource 'myVMAccess' is niet toegestaan door het beleid. "

```azurepowershell-interactive
Set-AzureRmVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

In de portal voor moet wijzigen van het wachtwoord mislukken met de 'de sjabloonimplementatie is mislukt vanwege schending van het beleid." Bericht.

## <a name="remove-the-assignment"></a>De toewijzing verwijderen

```azurepowershell-interactive
Remove-AzureRMPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Verwijder het beleid

```azurepowershell-interactive
Remove-AzureRmPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie, [Azure Policy](../../azure-policy/azure-policy-introduction.md).
