---
title: 'Zelfstudie: Logboekregistratie van netwerkstroom naar en van een VM - Azure Portal | Microsoft Docs'
description: Informatie over de logboekregistratie van netwerkstromen naar en van een VM met behulp van de NSG-stroomlogboeken van Network Watcher.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to log the network traffic to and from a VM so I can analyze it for anomalies.
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: f010bebcf1130b3061c60987ffbd4e706a030773
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2018
ms.locfileid: "32776547"
---
# <a name="tutorial-log-network-traffic-to-and-from-a-virtual-machine-using-the-azure-portal"></a>Zelfstudie: Logboekregistratie van netwerkverkeer naar en van een virtuele machine met de Azure Portal

Met een NSG (netwerkbeveiligingsgroep) kunt u inkomend verkeer naar en uitgaand verkeer van een VM (virtuele machine) filteren. U kunt netwerkverkeer dat via een NSG stroomt, vastleggen in een NSG-stroomlogboek van Network Watcher. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een VM met een netwerkbeveiligingsgroep maken
> * Network Watcher inschakelen en de provider Microsoft.Insights registreren
> * Een verkeersstroomlogboek voor een NSG inschakelen met behulp van het stroomlogboek van Network Watcher
> * Logboekgegevens downloaden
> * Logboekgegevens weergeven

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-vm"></a>Een virtuele machine maken

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Selecteer **Compute** en selecteer vervolgens **Windows Server 2016 Datacenter** of **Ubuntu Server 17.10 VM**.
3. Voer de volgende informatie in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **OK**:

    |Instelling|Waarde|
    |---|---|
    |Naam|myVm|
    |Gebruikersnaam| Voer een gebruikersnaam naar keuze in.|
    |Wachtwoord| Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Abonnement| Selecteer uw abonnement.|
    |Resourcegroep| Selecteer **Nieuwe maken** en voer **myResourceGroup** in.|
    |Locatie| Selecteer **VS Oost**|

4. Selecteer een grootte voor de virtuele machine en selecteer **Selecteren**.
5. Accepteer onder **Instellingen** alle standaardwaarden en selecteer **OK**.
6. Selecteer onder **Maken** bij **Samenvatting** de optie **Maken** om de implementatie van de virtuele machine te starten. Het implementeren van de VM duurt een paar minuten. Wacht tot de VM is geïmplementeerd voordat u doorgaat met de resterende stappen.

Het maken van de virtuele machine duurt een paar minuten. Ga pas verder met de resterende stappen wanneer de virtuele machine is gemaakt. Terwijl de VM in de portal wordt gemaakt, wordt er ook een netwerkbeveiligingsgroep met de naam **myVm-nsg** gemaakt en aan de netwerkinterface van de VM gekoppeld.

## <a name="enable-network-watcher"></a>Network Watcher inschakelen

Als u al een netwerk-watcher hebt ingeschakeld in de regio VS - oost, ga dan verder met [Insights-provider registreren](#register-insights-provider).

1. Selecteer in de portal de optie **Alle services**. Typ *Network Watcher* in het vak **Filteren**. Selecteer **Network Watcher** in de resultaten.
2. Selecteer **Regio's** om dit item uit te vouwen en selecteer vervolgens **...** rechts van **VS - oost**, zoals wordt weergegeven in de volgende afbeelding:

    ![Network Watcher inschakelen](./media/network-watcher-nsg-flow-logging-portal/enable-network-watcher.png)

3. Selecteer **Network Watcher inschakelen**.

## <a name="register-insights-provider"></a>Insights-provider registreren

Voor NSG-stroomlogboekregistratie is de **Microsoft.Insights**-provider vereist. Voer de volgende stappen uit om de provider te registreren:

1. Selecteer in de linkerbovenhoek van de portal de optie **Alle services**. Typ *Abonnementen* in het vak Filter. Wanneer **Abonnementen** in de zoekresultaten wordt weergegeven, selecteert u deze optie.
2. Selecteer in de lijst met abonnementen het abonnement waarvoor u de provider wilt inschakelen.
3. Selecteer **Resourceproviders** onder **Instellingen**.
4. Controleer of de provider **microsoft.insights**de **STATUS** **Geregistreerd** heeft, zoals wordt weergegeven in de volgende afbeelding. Als de status **Niet geregistreerd** is, selecteer dan **Registreren**, rechts van de provider.

    ![Provider registreren](./media/network-watcher-nsg-flow-logging-portal/register-provider.png)

## <a name="enable-nsg-flow-log"></a>NSG-stroomlogboek inschakelen

1. NSG-stroomlogboekgegevens worden naar een Azure Storage-account geschreven. Om een Azure Storage-account te maken, selecteert u **+ Een resource maken** in de linkerbovenhoek van de portal.
2. Selecteer **Storage** en vervolgens **Storage-account - blob, file, table, queue**.
3. Typ of selecteer de volgende informatie, accepteer de overige standaardwaarden en selecteer **Maken**.

    | Instelling        | Waarde                                                        |
    | ---            | ---   |
    | Naam           | Mag 3 tot 24 tekens lang zijn, mag alleen kleine letters en cijfers bevatten en moet uniek zijn binnen alle Azure Storage-accounts.                                                               |
    | Locatie       | Selecteer **VS Oost**                                           |
    | Resourcegroep | Selecteer **Bestaande gebruiken** en vervolgens **myResourceGroup** |

    Het maken van het opslagaccount kan ongeveer een minuut duren. Ga pas verder met de resterende stappen wanneer het opslagaccount is gemaakt. Als u een bestaand opslagaccount wilt gebruiken in plaats van er een te maken, zorg er dan voor dat u een opslagaccount selecteert waarvoor **Alle netwerken** (standaard) is geselecteerd voor **Firewalls en virtuele netwerken**, onder de **Instellingen** voor het opslagaccount.
4. Selecteer in de linkerbovenhoek van de portal de optie **Alle services**. Typ *Network Watcher* in het vak **Filteren**. Selecteer **Network Watcher** in de zoekresultaten.
5. Selecteer onder **LOGBOEKEN** de optie **NSG-stroomlogboeken**, zoals wordt weergegeven in de volgende afbeelding:

    ![NSG's](./media/network-watcher-nsg-flow-logging-portal/nsgs.png)

6. Selecteer de NSG met de naam **myVm-nsg**.
7. Selecteer onder **Instellingen voor stroomlogboeken** de optie **Aan**.
8. Selecteer het opslagaccount dat u in stap 3 hebt gemaakt.
9. Stel **Bewaartermijn (dagen)** in op 5 en selecteer **Opslaan**.

## <a name="download-flow-log"></a>Stroomlogboek downloaden

1. Selecteer in de portal van Network Watcher de optie **NSG-stroomlogboeken** onder **LOGBOEKEN**.
2. Selecteer **U kunt u stroomlogboeken downloaden van geconfigureerde opslagaccounts**, zoals wordt weergegeven in de volgende afbeelding:

  ![Stroomlogboeken downloaden](./media/network-watcher-nsg-flow-logging-portal/download-flow-logs.png)

3. Selecteer het opslagaccount dat u hebt geconfigureerd in stap 2 van [NSG-stroomlogboek inschakelen](#enable-nsg-flow-log).
4. Selecteer **Containers** onder **BLOB-SERVICE** en selecteer vervolgens de container **insights-logs-networksecuritygroupflowevent**, zoals wordt weergegeven in de volgende afbeelding:

    ![Container selecteren](./media/network-watcher-nsg-flow-logging-portal/select-container.png)
5. Navigeer door de maphiërarchie totdat het bestand PT1H.json wordt weergegeven, zoals in de volgende afbeelding:

    ![Logboekbestand](./media/network-watcher-nsg-flow-logging-portal/log-file.png)

    Logboekbestanden worden geschreven naar een maphiërarchie die de volgende naamconventie volgt: https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

6. Selecteer **...**  rechts van het bestand PT1H.json en selecteer **Downloaden**.

## <a name="view-flow-log"></a>Stroomlogboek weergeven

De volgende json is een voorbeeld van wat u in het bestand PT1H.json ziet voor elke stroom waarvoor gegevens worden geregistreerd:

```json
{
    "time": "2018-05-01T15:00:02.1713710Z",
    "systemId": "<Id>",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/<Id>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MYVM-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 1,
        "flows": [{
            "rule": "UserRule_default-allow-rdp",
            "flows": [{
                "mac": "000D3A170C69",
                "flowTuples": ["1525186745,192.168.1.4,10.0.0.4,55960,3389,T,I,A"]
            }]
        }]
    }
}
```

De waarde voor **mac** in de vorige uitvoer is het MAC-adres van de netwerkinterface die is gemaakt toen de VM werd gemaakt. De door komma's gescheiden informatie voor **flowTuples** bevat het volgende:

| Voorbeeldgegevens | Wat de gegevens voorstellen   | Uitleg                                                                              |
| ---          | ---                    | ---                                                                                      |
| 1525186745   | Tijdstempel             | Het tijdstempel van wanneer de stroom heeft plaatsgevonden, in de indeling UNIX-EPOCHE. In het vorige voorbeeld is de datum omgezet in 1 mei 2018 op 2:59:05 PM GMT.                                                                                    |
| 192.168.1.4  | IP-adres van bron      | Het IP-adres van de bron waaruit de stroom afkomstig is.
| 10.0.0.4     | IP-adres van doel | Het IP-adres van het doel waarvoor de stroom is bestemd. 10.0.0.4 is het privé-IP-adres van de VM die u hebt gemaakt in [Een virtuele machine maken](#create-a-vm).                                                                                 |
| 55960        | Bronpoort            | De bronpoort waaruit de stroom afkomstig is.                                           |
| 3389         | Doelpoort       | De doelpoort waarvoor de stroom is bestemd. Aangezien het verkeer was bestemd voor poort 3389, is de stroom verwerkt door de regel met de naam **UserRule_default-allow-rdp** in het logboekbestand.                                                |
| T            | Protocol               | Hiermee wordt aangegeven of het protocol van de stroom TCP (T) of UDP (U).                                  |
| I            | Richting              | Hiermee wordt aangegeven of het verkeer inkomend (I) of uitgaand (O) was.                                     |
| A            | Bewerking                 | Hiermee wordt aangegeven of het verkeer was toegelaten (A) of geweigerd (D).                                           |

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u NSG-stroomlogboekregistratie inschakelt voor een NSG. U hebt ook geleerd hoe u gegevens die zijn geregistreerd in een bestand, downloadt en bekijkt. De onbewerkte gegevens in het json-bestand zijn soms lastig te interpreteren. Als u de gegevens wilt visualiseren, kunt u [verkeersanalyse](traffic-analytics.md) van Network Watcher, Microsoft [PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md) of een ander hulpprogramma gebruiken.