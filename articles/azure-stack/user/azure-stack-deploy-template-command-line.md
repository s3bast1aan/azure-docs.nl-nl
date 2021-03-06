---
title: Sjablonen met de opdrachtregel in Azure-Stack implementeren | Microsoft Docs
description: Informatie over het gebruik van de platformoverschrijdende opdrachtregelinterface (CLI) om sjablonen te implementeren naar Azure-Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 761e09889a230642c42697b6bc4f96dc32fe03a0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316192"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Sjablonen in Azure-Stack via de opdrachtregel implementeren

*Van toepassing op: Azure Stack geïntegreerde systemen en Azure Stack Development Kit*

Gebruik de opdracht voor het implementeren van Azure Resource Manager-sjablonen op de Azure-Stack Development Kit. Azure Resource Manager-sjablonen implementeren en inrichten van alle resources voor uw toepassing in een enkele, gecoördineerde bewerking.

## <a name="before-you-begin"></a>Voordat u begint
 - [Installeren en verbinding maken met](azure-stack-version-profiles-azurecli2.md) naar de Stack van Azure met Azure CLI
 - Download de bestanden *azuredeploy.json* en *azuredeploy.parameters.json* van de [voorbeeldsjabloon storage-account maken](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Sjabloon implementeren
Navigeer naar de map waar deze bestanden zijn gedownload en voer de volgende opdracht om de sjabloon te implementeren:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Met deze opdracht wordt de sjabloon geïmplementeerd naar de resourcegroep **cliRG** in de standaardlocatie voor de Azure-Stack-Implementatiemodel.

## <a name="validate-template-deployment"></a>Sjabloonimplementatie valideren
Als deze resource groep en storage-account weergeven, gebruikt u de volgende opdrachten:

    azure group list

    azure storage account list




