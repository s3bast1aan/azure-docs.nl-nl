---
title: Toegang tot virtuele Machines van Azure in Server Explorer | Microsoft Docs
description: Krijg een overzicht van het weergeven van maken en beheren van Azure virtuele machines (VM's) in Server Explorer in Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: eb3afde6-ba90-4308-9ac1-3cc29da4ede0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 8/31/2017
ms.author: ghogen
ms.openlocfilehash: a19f33c4dd2654538c5718d2cd7dbe5d018e4de1
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792882"
---
# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>Toegang tot virtuele Machines van Azure in Server Explorer

Als u virtuele machines die worden gehost door Azure hebt, kunt u deze kunt openen in Server Explorer. U moet eerst aanmelden bij uw Azure-abonnement om uw mobiele services weer te geven. Als u wilt aanmelden, opent u het snelmenu voor het Azure-knooppunt in Server Explorer en kies **verbinding maken met Microsoft Azure**.

1. In de Cloud Explorer, kiest u een virtuele machine en kies vervolgens de sleutel F4 het eigenschappenvenster weer te geven.

    De volgende tabel ziet u welke eigenschappen beschikbaar zijn, maar ze zijn allemaal alleen-lezen. Om deze te wijzigen, gebruikt u de [Azure-portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

   | Eigenschap | Beschrijving |
   | --- | --- |
   | DNS-naam |De URL met het internetadres van de virtuele machine. |
   | Omgeving |De waarde van deze eigenschap is voor een virtuele machine altijd productie. |
   | Naam |De naam van de virtuele machine. |
   | Grootte |De grootte van de virtuele machine, dat overeenkomt met de hoeveelheid geheugen en schijfruimte ruimte die beschikbaar is. Zie voor meer informatie [grootten van virtuele machines](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs). |
   | Status |Waarden zijn starten, gestart, gestopt, gestopt en Status ophalen. Als bij het ophalen van de Status wordt weergegeven, is de huidige status is onbekend. De waarden voor deze eigenschap afwijken van de waarden die worden gebruikt op de [Azure-portal](http://go.microsoft.com/fwlink/p/?LinkID=525040). |
   | Abonnements-id |De abonnement-ID voor uw Azure-account. U kunt deze informatie weergeven op de [Azure-portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) door de eigenschappen voor een abonnement te bekijken. |
2. Kies een knooppunt van het eindpunt en bekijk vervolgens de **eigenschappen** venster.
3. De volgende tabel beschrijft de beschikbare eigenschappen van eindpunten, maar ze zijn alleen-lezen. Als u wilt toevoegen of bewerken van de eindpunten voor een virtuele machine, gebruikt u de [Azure-portal](http://go.microsoft.com/fwlink/p/?LinkID=525040). 

   | Eigenschap | Beschrijving |
   | --- | --- |
   | Naam |Een id voor het eindpunt. |
   | Particuliere poort |De poort voor intern gebruik binnen uw toepassing netwerktoegang. |
   | Protocol |Het protocol dat gebruikmaakt van de transportlaag is opgehaald voor dit eindpunt, TCP of UDP. |
   | Openbare poort |De poort die wordt gebruikt voor openbare toegang tot uw toepassing. |
