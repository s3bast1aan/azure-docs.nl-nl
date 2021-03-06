---
title: Azure Policy-voorbeeld - Goedgekeurde VM-installatiekopieën
description: Dit beleid vereist dat alleen goedgekeurde aangepaste installatiekopieën in uw omgeving zijn geïmplementeerd.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 06/03/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 408968723ab9dc14a1db1321c4db3b3d8518d511
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319296"
---
# <a name="approved-vm-images"></a>Goedgekeurde VM-installatiekopieën

Dit beleid vereist dat alleen goedgekeurde aangepaste installatiekopieën in uw omgeving zijn geïmplementeerd. U geeft een matrix van goedgekeurde installatiekopie-id’s op.

U kunt dit voorbeeldbeleid implementeren met behulp van:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure-CLI](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Voorbeeldbeleid

### <a name="policy-definition"></a>Beleidsdefinitie

De volledige samengestelde JSON beleidsdefinitie, die wordt gebruikt door de REST-API, 'Deploy to Azure'-knoppen en handmatig in de portal.

[!code-json[full](../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.json "Complete policy definition (JSON)")]

> [!NOTE]
> Als u een beleid handmatig in de portal maakt, gebruikt u de gedeelten **properties.parameters** en **properties.policyRule** van de bovenstaande code. Voeg de twee secties samen met accolades `{}` om er geldige JSON van te maken.

### <a name="policy-rules"></a>Beleidsregels

De JSON definieert de regels van het beleid, zoals gebruikt door Azure CLI en Azure PowerShell.

[!code-json[rule](../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Beleidsparameters

De JSON definieert de beleidsparameters, zoals gebruikt door Azure CLI en Azure PowerShell.

[!code-json[parameters](../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.parameters.json "Policy parameters (JSON)")]

## <a name="parameters"></a>Parameters

|Naam |Type |Veld |Beschrijving |
|---|---|---|---|
|imageIds |Matrix |Microsoft.Compute/imageIds |De lijst met goedgekeurde VM-installatiekopieën|

Bij het maken van een toewijzing via PowerShell of Azure CLI kunnen de parameterwaarden worden doorgegeven als JSON in een tekenreeks of via een bestand met `-PolicyParameter` (PowerShell) of `--params` (Azure CLI).
PowerShell ondersteunt ook `-PolicyParameterObject`, waarvoor de cmdlet een hashtabel met naam/waardeparen moet ontvangen waarin **Name** de parameternaam is en **Value** is de enkelvoudige waarde of matrix met waarden die tijdens toewijzing wordt doorgegeven.

In deze voorbeeldparameter wordt alleen de _ContosoStdImage_ in de resourcegroep _YourResourceGroup_ of de versie van mei 2018 van de installatiekopie van Windows Server 2016 Datacenter in 'VS-midden' toegestaan.

```json
{
    "imageIds": {
        "value": [
            "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage",
            "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510"
        ]
    }
}
```

## <a name="azure-portal"></a>Azure Portal

[![Implementeren in Azure](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fallowed-custom-images%2Fazurepolicy.json)
[![Implementeren in Azure Gov](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fallowed-custom-images%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

### <a name="deploy-with-azure-powershell"></a>Implementeren met Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzureRmPolicyDefinition -Name 'allowed-custom-images' -DisplayName 'Approved VM images' -description 'This policy governs the approved VM images' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzureRmResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "imageIds": { "value": [ "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage", "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510" ] } }'

# Create the Policy Assignment
$assignment = New-AzureRmPolicyAssignment -Name 'allowed-custom-images-assignment' -DisplayName 'Approved VM images Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
```

### <a name="remove-with-azure-powershell"></a>Verwijderen met Azure PowerShell

Voer de volgende opdrachten uit om de vorige toewijzing en definitie te verwijderen:

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzureRmPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzureRmPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Toelichting van Azure PowerShell

De scripts voor implementeren en verwijderen gebruiken de volgende opdrachten. Elke opdracht in de volgende tabel is een koppeling naar opdrachtspecifieke documentatie:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) | Hiermee maakt u een nieuwe Azure Policy-definitie. |
| [Get-AzureRmResourceGroup](/powershell/module/azurerm.resources/get-azurermresourcegroup) | Hiermee vraagt u één resourcegroep op. |
| [New-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) | Hiermee maakt u een nieuwe Azure Policy-toewijzing. In dit voorbeeld bieden we een definitie aan, maar er kan ook een initiatief nodig zijn. |
| [Remove-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/remove-azurermpolicyassignment) | Hiermee verwijdert u een bestaande Azure Policy-toewijzing. |
| [Remove-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/remove-azurermpolicydefinition) | Hiermee verwijdert u een bestaande Azure Policy-definitie. |

## <a name="azure-cli"></a>Azure-CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Implementeren met Azure CLI

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'allowed-custom-images' --display-name 'Approved VM images' --description 'This policy governs the approved VM images' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "imageIds": { "value": [ "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage", "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510" ] } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'allowed-custom-images-assignment' --display-name 'Approved VM images Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
```

### <a name="remove-with-azure-cli"></a>Verwijderen met Azure CLI

Voer de volgende opdrachten uit om de vorige toewijzing en definitie te verwijderen:

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Toelichting van Azure CLI

| Opdracht | Opmerkingen |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Hiermee maakt u een nieuwe Azure Policy-definitie. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Hiermee vraagt u één resourcegroep op. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Hiermee maakt u een nieuwe Azure Policy-toewijzing. In dit voorbeeld bieden we een definitie aan, maar er kan ook een initiatief nodig zijn. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Hiermee verwijdert u een bestaande Azure Policy-toewijzing. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Hiermee verwijdert u een bestaande Azure Policy-definitie. |

## <a name="rest-api"></a>REST-API

Er zijn verschillende hulpprogramma's die u kunt gebruiken om te communiceren met de REST-API van Resource Manager, zoals [ARMClient](https://github.com/projectkudu/ARMClient) of PowerShell. Een voorbeeld van het aanroepen van een REST-API vanuit PowerShell vindt u in de sectie **Aliassen** van [Structuur van Azure-beleidsdefinities](../policy-definition.md#aliases).

### <a name="deploy-with-rest-api"></a>Implementeren met REST-API

- Maak de beleidsdefinitie (abonnementsbereik). Gebruik de [beleidsdefinitie](#policy-definition) geschreven in JSON voor de body van de aanvraag.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images?api-version=2016-12-01
  ```

- Maak de beleidstoewijzing (scope van resourcegroep).

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/allowed-custom-images-assignment?api-version=2017-06-01-preview
  ```

  Gebruik het volgende JSON-voorbeeld voor de body van de aanvraag:

  ```json
  {
      "properties": {
          "displayName": "Approved VM images Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images",
          "parameters": {
              "imageIds": {
                  "value": [
                      "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage",
                      "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510"
                  ]
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Verwijderen met REST-API

- Beleidstoewijzing verwijderen

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/allowed-custom-images-assignment?api-version=2017-06-01-preview
  ```

- Beleidsdefinitie verwijderen

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images?api-version=2016-12-01
  ```

### <a name="rest-api-explanation"></a>Toelichting van REST-API

| Service | Groep | Bewerking | Opmerkingen |
|---|---|---|---|
| Resourcebeheer | Definities voor beleid | [Maken](/rest/api/resources/policydefinitions/createorupdate) | Hiermee maakt u een nieuwe Azure Policy-definitie voor een abonnement. Alternatief: [Maken in beheergroep](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Resourcebeheer | Beleidstoewijzingen | [Maken](/rest/api/resources/policyassignments/create) | Hiermee maakt u een nieuwe Azure Policy-toewijzing. In dit voorbeeld bieden we een definitie aan, maar er kan ook een initiatief nodig zijn. |
| Resourcebeheer | Beleidstoewijzingen | [Verwijderen](/rest/api/resources/policyassignments/delete) | Hiermee verwijdert u een bestaande Azure Policy-toewijzing. |
| Resourcebeheer | Definities voor beleid | [Verwijderen](/rest/api/resources/policydefinitions/delete) | Hiermee verwijdert u een bestaande Azure Policy-definitie. Alternatief: [Verwijderen in beheergroep](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Volgende stappen

- Aanvullende [voorbeelden van Azure Policy](../json-samples.md) bekijken
- [Structuur van Azure Policy-definities](../policy-definition.md) bekijken
- Azure Policy-voorbeelden voor Virtual Machines in [Beleid toepassen op Windows-VM's](../../virtual-machines/windows/policy.md)