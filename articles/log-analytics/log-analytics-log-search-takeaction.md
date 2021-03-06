---
title: Actie van Azure Automation-Runbook in logboekanalyse gebruiker gestart | Microsoft Docs
description: In dit artikel wordt beschreven hoe een Automation-runbook uitvoeren vanaf een Log Analytics zoeken resultaat op aanvraag.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 48e184206d9f1cf5b14c2e18b4ca1ed260d70161
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130213"
---
# <a name="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result"></a>Onderneem actie met een Automation-Runbook uit een zoekresultaat Log Analytics-logboek

In een logboek zoekresultaat in Azure Log Analytics, kunt u nu selecteren **maatregelen** een Automation-runbook uitvoeren.  Het runbook kan worden gebruikt voor het probleem te herstellen of een andere actie bijvoorbeeld als het verzamelen van informatie over probleemoplossing, e-mailbericht verzenden of een serviceaanvraag maken op te nemen. 

## <a name="components-and-features-used"></a>Gebruikte onderdelen en functies
* [Azure Automation-account](../automation/automation-offering-get-started.md)
* [Log Analytics-werkruimte](../log-analytics/log-analytics-overview.md)

## <a name="to-initiate-runbook-from-log-search"></a>Runbook van logboek zoekopdracht starten

Maatregelen nemen op een gebeurtenis en een runbook in de zoekresultaten logboek initiëren, u begint met het maken van een zoekopdracht logboek en uit de resultaten een runbook op aanvraag kunnen worden aangeroepen.  Dit kan worden bereikt vanaf de zoekfunctie logboek in de [Azure-portal](../log-analytics/log-analytics-log-search-new.md).  In dit voorbeeld uitvoeren we een zoekopdracht logboek vanuit de Azure-portal met een eenvoudige demonstratie van deze functie.

1. Klik in de Azure-portal op **alle services** en selecteer **logboekanalyse**.  
2. Selecteer de werkruimte voor logboekanalyse.
3. Selecteer op de werkruimte **logboek zoeken**.  
4. Op de pagina logboek zoeken te doorzoeken logboek.  
5. In de zoekresultaten logboek klikt u op de knop met weglatingstekens aan de linkerkant van een van de velden en van de pop-up Selecteer **maatregelen nemen op**.<br><br> ![Selecteer actie ondernemen in zoekresultaten](./media/log-analytics-log-search-takeaction/log-search-takeaction-menuoption.png) 
6. Selecteer **uitvoeren van een runbook** en selecteert u een runbook om uit te voeren.  U kunt een willekeurig runbook selecteren in het Automation-account dat is gekoppeld aan de werkruimte voor logboekanalyse.  Houd rekening met het volgende:

    * Runbooks zijn ingedeeld in labels
    * Runbook invoerparameter waarden kunnen rechtstreeks vanuit de velden van de zoekresultaten worden geselecteerd.  Een vervolgkeuzelijst wordt weergegeven om de beschikbare velden van het resultaat te selecteren in weer te geven.  
    * U kunt kiezen uit te voeren van het runbook op een [hybride runbook worker](../automation/automation-hybrid-runbook-worker.md) dat u hebt geïnstalleerd op de machine waarop het probleem is als er een bijbehorende Hybrid Runbook Worker-groep die alleen deze computer als een lid bevat.  Als de naam van de Hybrid Worker-groep overeenkomt met de naam van de computer die zich in de zoekresultaten logboek, wordt klikt u vervolgens de groep automatisch geselecteerd.    

6. Nadat u op **uitvoeren**, de pagina van de taak runbook geopend zodat u de status van de taak controleren.   

Als u een runbook dat is geconfigureerd om te worden [aangeroepen vanuit een waarschuwing voor logboekanalyse](../automation/automation-invoke-runbook-from-omsla-alert.md), heeft een invoerparameter aangeroepen **WebhookData** die **Object** type.  Als de invoerparameter verplicht is, moet u de zoekresultaten doorgeven aan het runbook zodat deze de tekenreeks JSON-indeling naar een objecttype dat u converteren kan kunt filteren op specifieke items waarnaar u in de runbook-activiteiten verwijst.  U dit doen door het selecteren van **zoekresultaat (Object)** uit de vervolgkeuzelijst.<br><br> ![Webhook-gegevensobject voor runbook-parameter selecteren](media/log-analytics-log-search-takeaction/select-runbook-and-properties.png)   
    
## <a name="next-steps"></a>Volgende stappen

* Controleer de [logboekanalyse Meld zoeken verwijzing](log-analytics-search-reference.md) om weer te geven van alle zoekvelden en facetten beschikbaar in Log Analytics.
* Als u wilt weten hoe u automatisch een Automation-runbook aanroepen, Bekijk [een Azure Automation-runbook aanroept vanuit een waarschuwing voor logboekanalyse](../automation/automation-invoke-runbook-from-omsla-alert.md).  