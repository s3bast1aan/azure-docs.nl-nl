---
title: Overzicht van Azure Data Lake Storage Gen1 | Microsoft Docs
description: Begrijpen wat Data Lake Storage Gen1 is (voorheen bekend als Azure Data Lake Store) en de meerwaarde is ten opzicht van andere gegevensarchieven
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: 4dff8f4ff9fc324d48391c0399677b64824493c6
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035932"
---
# <a name="overview-of-azure-data-lake-storage-gen1"></a>Overzicht van Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Store is een ondernemingsbrede opslagplaats op hyperschaal voor analytische workloads van big data. Met Azure Data Lake kunt u gegevens van elke grootte, type en opnamesnelheid vastleggen op één enkele locatie voor operationele en experimentele analyses.

> [!TIP]
> Gebruik het [leertraject van Data Lake Store](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) om de service Azure Data Lake Store te verkennen.
> 
> 

Azure Data Lake Store kan worden geopend via Hadoop (beschikbaar met HDInsight-cluster) met behulp van de met WebHDFS compatibele REST-API's. Het is speciaal ontworpen om de opgeslagen gegevens te kunnen analyseren en het is afgestemd op prestaties voor scenario's met gegevensanalyses. Het bevat out-of-the-box alle mogelijkheden op bedrijfsniveau: beveiliging, beheerbaarheid, schaalbaarheid, betrouwbaarheid en beschikbaarheid. Dit zijn allemaal essentiële factoren voor zakelijke gebruiksvoorbeelden.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

De belangrijkste mogelijkheden van Azure Data Lake zijn onder andere de volgende.

### <a name="built-for-hadoop"></a>Gebouwd voor Hadoop
Het Azure Data Lake-archief is een Apache Hadoop-bestandssysteem dat compatibel is met Hadoop Distributed File System (HDFS) en samenwerkt met het Hadoop-ecosysteem.  Uw bestaande toepassingen of services van HDInsight die gebruikmaken van de API WebHDFS kunnen eenvoudig worden geïntegreerd met Data Lake Store. Data Lake Store bevat ook een met WebHDFS compatibele REST-interface voor toepassingen

Gegevens die zijn opgeslagen in Data Lake Store kunnen eenvoudig worden geanalyseerd met analytische frameworks van Hadoop zoals MapReduce of Hive. Microsoft Azure HDInsight-clusters kunnen worden ingericht en geconfigureerd voor directe toegang tot gegevens die zijn opgeslagen in Data Lake Store.

### <a name="unlimited-storage-petabyte-files"></a>Onbeperkte opslag, bestanden ter grootte van petabytes
Azure Data Lake Store biedt onbeperkte opslag en is geschikt voor het opslaan van een verscheidenheid aan gegevens voor analyses. Het legt geen limieten op voor de grootte van accounts of bestanden, of de hoeveelheid gegevens die kunnen worden opgeslagen in een Data Lake. De grootte van afzonderlijke bestanden kan uiteenlopen van een kilobyte tot petabytes, waardoor het een uitstekende keuze is voor het opslaan van elk type gegevens. Gegevens worden blijvend opgeslagen door meerdere kopieën te maken en er is geen limiet voor de tijdsduur waarin de gegevens kunnen worden opgeslagen in de Data Lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Prestaties zijn afgestemd op big data-analyses
Azure Data Lake Store is gebouwd voor het uitvoeren van grootschalige analytische systemen waarvoor grote doorvoer vereist is om grote hoeveelheden gegevens op te vragen en te analyseren. De Data Lake verspreidt delen van een bestand over een aantal afzonderlijke opslagservers. Hiermee verbetert u de doorvoer wanneer het bestand in parallel wordt gelezen voor het uitvoeren van gegevensanalyse.

### <a name="enterprise-ready-highly-available-and-secure"></a>Enterprise-ready: hoge beschikbaarheid en veiligheid
Azure Data Lake Store biedt industriestandaard beschikbaarheid en betrouwbaarheid. Uw gegevensassets worden blijvend opgeslagen door het maken van redundante exemplaren ter bescherming tegen onverwachte fouten. Ondernemingen kunnen Azure Data Lake bij hun oplossingen gebruiken als een belangrijk onderdeel van hun bestaande gegevensplatform.

Data Lake Store biedt ook beveiliging op bedrijfsniveau voor de opgeslagen gegevens. Zie voor meer informatie [Gegevens beveiligen in Azure Data Lake Store](#DataLakeStoreSecurity).

### <a name="all-data"></a>Alle gegevens
In Azure Data Lake Store kunnen alle gegevens worden opgeslagen in de oorspronkelijke indeling, zoals ze gemaakt zijn, en het is niet nodig om de gegevens eerst om te zetten. In Data Lake Store hoeft geen schema te worden gedefinieerd voordat de gegevens worden geladen. Daardoor kan het afzonderlijke analytische framework de gegevens interpreteren en een schema definiëren op het moment van de analyse. Doordat bestanden van verschillende groottes en indelingen kunnen worden opgeslagen, kan Data Lake Store gestructureerde, semi-gestructureerde en ongestructureerde gegevens verwerken.

Containers voor gegevens van Azure Data Lake Store zijn eigenlijk mappen en bestanden. U werkt met de opgeslagen gegevens met behulp van SDK's, Azure Portal en Azure Powershell. Als u uw gegevens in het archief plaats via deze interfaces en de juiste containers gebruikt, kunt u allerlei soorten gegevens opslaan. Data Lake Store voert geen speciale verwerking van gegevens uit op basis van het type gegevens dat wordt opgeslagen.

## <a name="DataLakeStoreSecurity"></a>Gegevens beveiligen in Azure Data Lake Store
Azure Data Lake Store maakt gebruik van Azure Active Directory voor verificatie en toegangsbeheerlijsten (ACL’s) om toegang tot uw gegevens te beheren.

| Functie | Beschrijving |
| --- | --- |
| Verificatie |Azure Data Lake Store integreert met Azure Active Directory (AAD) voor identiteits- en toegangsbeheer voor alle gegevens die zijn opgeslagen in Azure Data Lake Store. Als gevolg van de integratie profiteert Azure Data Lake van alle AAD-functies, zoals Multi-Factor Authentication, voorwaardelijke toegang, op rollen gebaseerd toegangsbeheer, bewaking van het gebruik van toepassingen, beveiligingsbewaking en waarschuwingen, enzovoort. Azure Data Lake Store ondersteunt het OAuth 2.0-protocol voor verificatie in de REST-interface. Zie [Data Lake Store-verificatie](data-lakes-store-authentication-using-azure-active-directory.md)|
| Toegangsbeheer |Azure Data Lake Store biedt toegangsbeheer door ondersteuning te bieden voor POSIX-machtigingen die beschikbaar worden gemaakt door het protocol WebHDFS. ACL's kunnen worden ingeschakeld voor de hoofdmap, submappen en afzonderlijke bestanden. Zie voor meer informatie over de werking van ACL's in de context van Data Lake Store [Toegangsbeheer in Data Lake Store](data-lake-store-access-control.md). |
| Versleuteling |Data Lake Store biedt ook versleuteling voor gegevens die zijn opgeslagen in het account. U geeft de versleutelingsinstellingen op tijdens het maken van een Data Lake Store-account. U kunt ervoor kiezen de gegevens te versleutelen of niet te versleutelen. Zie [Versleuteling in Data Lake Store](data-lake-store-encryption.md) voor meer informatie. Voor instructies voor het bieden van versleuteling-gerelateerde configuratie raadpleegt u [Aan de slag met Azure Data Lake Store met Azure Portal](data-lake-store-get-started-portal.md). |

Wilt u meer informatie over het beveiligen van gegevens in Data Lake Store? Volg dan de onderstaande links.

* Zie voor instructies over het beveiligen van gegevens in Data Lake Store [Gegevens beveiligen in Azure Data Lake Store](data-lake-store-secure-data.md).
* Kijkt u liever naar video’s? [Bekijk deze video](https://mix.office.com/watch/1q2mgzh9nn5lx) over het beveiligen van gegevens die zijn opgeslagen in Data Lake Store.

## <a name="applications-compatible-with-azure-data-lake-store"></a>Toepassingen die compatibel zijn met Azure Data Lake Store
Azure Data Lake Store is compatibel met de meeste open source-onderdelen van het Hadoop-ecosysteem. Het kan ook goed worden geïntegreerd in andere Azure-services. Hierdoor is Data Lake Store een perfecte optie voor uw opslagbehoeften. Volg de onderstaande links voor meer informatie over hoe Data Lake Store kan worden gebruikt met open source-onderdelen en met andere Azure-services.

* Zie [Toepassingen en services die compatibel zijn met Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md) voor een lijst met open-sourcetoepassingen die compatibel zijn met Data Lake Store.
* Zie [Integreren met andere Azure-services](data-lake-store-integrate-with-other-services.md) om te begrijpen hoe Data Lake Store samen met andere Azure-services kan worden gebruikt in een breed scala aan scenario's.
* Zie [Scenario's voor het gebruik van Data Lake Store](data-lake-store-data-scenarios.md) voor informatie over het gebruik van Data Lake Store in scenario's zoals het opnemen, verwerken, downloaden en visualiseren van gegevens.

## <a name="what-is-azure-data-lake-store-file-system-adl"></a>Wat is het bestandssysteem van Azure Data Lake Store (adl://)?
Data Lake Store is toegankelijk via het nieuwe bestandssysteem, het AzureDataLakeFilesystem (adl://), in Hadoop-omgevingen (beschikbaar met HDInsight-cluster). Toepassingen en services die gebruikmaken van adl:// kunnen profiteren van verdere optimalisatie van prestaties die op dit moment niet beschikbaar is in WebHDFS. Als gevolg hiervan geeft Data Lake Store u de flexibiliteit om de beste prestaties te behalen met de aanbevolen optie adl:// of bestaande code te beheren door de API van WebHDFS rechtstreeks te blijven gebruiken. Azure HDInsight maakt volledig gebruik van AzureDataLakeFilesystem om de beste prestaties te leveren op Data Lake Store.

U kunt toegang krijgen tot uw gegevens in Data Lake Store met behulp van `adl://<data_lake_store_name>.azuredatalakestore.net`. Zie voor meer informatie over toegang krijgen tot de gegevens in Data Lake Store [Eigenschappen van de opgeslagen gegevens weergeven](data-lake-store-get-started-portal.md#properties)

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Data Lake Store via de Azure Portal](data-lake-store-get-started-portal.md)
* [Aan de slag met Azure Data Lake Store met .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure HDInsight gebruiken met Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)