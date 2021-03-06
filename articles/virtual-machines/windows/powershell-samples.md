---
title: Voorbeelden van de virtuele Machine van Azure PowerShell | Microsoft Docs
description: Voorbeelden van de virtuele Machine van Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 27f926a9a5d30d28e2721ffa52695292f794a501
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31522428"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure virtuele Machine PowerShell-voorbeelden

De volgende tabel bevat koppelingen naar voorbeelden van de PowerShell-scripts die maken en beheren van virtuele machines van Windows.

| | |
|---|---|
|**Virtuele machines maken**||
| [Snel een virtuele machine maken](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Een resourcegroep, virtuele machine en alle gerelateerde resources maakt met het minimum van prompts.|
| [Een volledig geconfigureerde virtuele machine maken](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Een resourcegroep, virtuele machine en alle gerelateerde resources gemaakt.|
| [Maximaal beschikbare virtuele machines maken](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Maakt verschillende virtuele machines in een maximaal beschikbare en configuratie van taakverdeling.|
| [Een virtuele machine maken en voer het script voor configuratie](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Een virtuele machine maakt en wordt de extensie Azure aangepast Script gebruikt om IIS te installeren. |
| [Een virtuele machine maken en uitvoeren van de DSC-configuratie](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Een virtuele machine maakt en wordt de extensie Azure Desired State Configuration (DSC) gebruikt om IIS te installeren. |
| [Een VHD uploaden en virtuele machines maken](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Een lokale VHD-bestand wordt geüpload naar Azure, maakt en een installatiekopie van de VHD en maakt u een virtuele machine vervolgens vanuit die installatiekopie. |
| [Een virtuele machine maken van een beheerde OS-schijf](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Maakt een virtuele machine door het koppelen van een bestaande schijf beheerd als besturingssysteemschijf. |
| [Een virtuele machine maken vanuit een momentopname](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Een virtuele machine maakt vanuit een momentopname eerst een beheerde schijven maken vanuit een momentopname en vervolgens de nieuwe beheerde schijf als besturingssysteemschijf te koppelen. |
|**Opslag beheren**||
| [Beheerde schijf maken vanaf een VHD in hetzelfde of een ander abonnement](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hiermee maakt een beheerde schijf vanaf een speciale VHD als een besturingssysteemschijf of uit een VHD als gegevensschijf in hetzelfde of een ander abonnement.  |
| [Een beheerde schijf maken vanuit een momentopname](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Maakt een beheerde schijf van een momentopname. |
| [Beheerde schijf kopiëren naar hetzelfde of een ander abonnement](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Exemplaren beheerd van schijf naar hetzelfde of een ander abonnement maar beheerd in dezelfde regio bevinden als de bovenliggende schijf. 
| [Een momentopname exporteren als VHD naar een opslagaccount](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Exporteert een momentopname van een beheerde als VHD naar een opslagaccount in andere regio. |
| [Een momentopname maken van een VHD](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Maakt vanaf een VHD te maken van meerdere identieke beheerde schijven vanuit een momentopname in korte tijd momentopname.  |
| [Momentopname naar hetzelfde of een ander abonnement kopiëren](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Kopieën momentopname aan hetzelfde of een ander abonnement, maar in dezelfde regio bevinden als de momentopname van de bovenliggende. |
|**Beveiligde virtuele machines**||
| [Versleutelen van een VM- en gegevensschijven](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Een Azure Sleutelkluis, de versleutelingssleutel en de service-principal gemaakt en vervolgens een virtuele machine versleutelt. |
|**Virtuele machines bewaken**||
| [Monitor voor een virtuele machine bij Operations Management Suite](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Een virtuele machine maakt, installeert de Operations Management Suite-agent en de virtuele machine in een OMS-werkruimte inschrijft.  |
| | |

