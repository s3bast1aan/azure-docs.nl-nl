---
title: Gegevens kopiëren van Netezza met behulp van Azure Data Factory | Microsoft Docs
description: Informatie over het kopiëren van gegevens van Netezza naar gegevensarchieven ondersteunde sink met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: jingwang
ms.openlocfilehash: f8c10e2200f830ea6e568e7b3fba1f0a6085cef2
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37058958"
---
# <a name="copy-data-from-netezza-using-azure-data-factory"></a>Gegevens kopiëren van Netezza met behulp van Azure Data Factory 

In dit artikel bevat een overzicht van het gebruik van de Kopieeractiviteit in Azure Data Factory om gegevens van Netezza kopiëren. Dit is gebaseerd op de [activiteit overzicht kopiëren](copy-activity-overview.md) artikel met daarin een algemeen overzicht van de kopieeractiviteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U kunt gegevens uit Netezza kopiëren naar een ondersteunde sink-gegevensarchief. Zie voor een lijst van opgeslagen gegevens die worden ondersteund als bronnen/put door met de kopieerbewerking de [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) tabel.

Azure Data Factory biedt een ingebouwde stuurprogramma's zodat connectiviteit, dus u hoeft niet te gebruik van deze connector stuurprogramma handmatig installeren.

## <a name="getting-started"></a>Aan de slag

U kunt een pijplijn maken met de kopieeractiviteit middels de .NET SDK, Python SDK, Azure PowerShell, REST-API of Azure Resource Manager-sjabloon. Zie [kopie activiteit zelfstudie](quickstart-create-data-factory-dot-net.md) voor stapsgewijze instructies voor een pijplijn maken met een kopieeractiviteit.

De volgende secties bevatten informatie over de eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten specifieke naar Netezza-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor Netezza gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **Netezza** | Ja |
| connectionString | Een ODBC-verbindingsreeks verbinding maken met Netezza. Dit veld markeren als een SecureString Bewaar deze zorgvuldig in Data Factory of [verwijzen naar een geheim dat is opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | De [integratie Runtime](concepts-integration-runtime.md) moeten worden gebruikt voor het verbinding maken met het gegevensarchief. U kunt Self-hosted integratie Runtime of Azure integratie Runtime gebruiken (als uw gegevensarchief openbaar toegankelijk). Als niet wordt opgegeven, wordt de standaardwaarde Azure integratie Runtime. |Nee |

Een typische verbindingsreeks is `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. Meer eigenschappen die u per uw aanvraag instellen kunt:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |:--- |
| SecurityLevel | Het niveau van beveiliging SSL/TLS () die gebruikmaakt van het stuurprogramma voor de verbinding met het gegevensarchief. Bijvoorbeeld `SecurityLevel=preferredSecured`. Ondersteunde waarden zijn:<br/>-Alleen niet-beveiligde (**onlyUnSecured**): het stuurprogramma maakt geen gebruik van SSL.<br/>- **Voorkeur onbeveiligd (preferredUnSecured) (standaard)**: als de server een keuze biedt, het stuurprogramma geen gebruik van SSL. <br/>- **Voorkeur beveiligd (preferredSecured)**: als de server een keuze biedt, SSL wordt gebruikt door het stuurprogramma. <br/>- **Alleen beveiligd (onlySecured)**: het stuurprogramma geen verbinding maken tenzij een SSL-verbinding beschikbaar is | Nee |
| CACertBestand | Het volledige pad naar het SSL-certificaat dat wordt gebruikt door de server. Bijvoorbeeld `CaCertFile=<cert path>;`| Ja, als SSL is ingeschakeld |

**Voorbeeld:**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie voor een volledige lijst met secties en de eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. Deze sectie bevat een lijst met eigenschappen die ondersteund worden door Netezza dataset.

Stel de eigenschap type van de gegevensset om gegevens te kopiëren van Netezza, **NetezzaTable**. Er is geen aanvullende typespecifieke-eigenschap in dit type dataset.

**Voorbeeld**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie voor een volledige lijst met secties en de eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. Deze sectie bevat een lijst met eigenschappen die ondersteund worden door Netezza bron.

### <a name="netezza-as-source"></a>Netezza als bron

Om gegevens te kopiëren van Netezza, stelt u het brontype in de kopieerbewerking naar **NetezzaSource**. De volgende eigenschappen worden ondersteund in de kopieerbewerking **bron** sectie:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron voor kopiëren-activiteit moet worden ingesteld op: **NetezzaSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Ja |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven als bronnen en put wordt ondersteund door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats).
