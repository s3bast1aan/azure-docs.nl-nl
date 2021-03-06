---
title: Onveranderbare opslag voor Azure Blob storage (preview) | Microsoft Docs
description: Azure Storage biedt ondersteuning voor blobopslag (object) waarmee gebruikers gegevens opslaan in een status bewaarinterval, niet kan worden gewijzigd voor een opgegeven interval WORM (één keer schrijven, lezen veel).
services: storage
author: sangsinh
ms.service: storage
ms.topic: article
ms.date: 05/29/2018
ms.author: sangsinh
ms.component: blobs
ms.openlocfilehash: cfc25906e926e8dd6687eeccd311a38653772c4d
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398995"
---
# <a name="store-business-critical-data-in-azure-blob-storage-preview"></a>Store kritieke zakelijke gegevens in Azure Blob-opslag (preview)

Onveranderbare opslag voor Azure-blobopslag (object) kan gebruikers bedrijfskritieke gegevens opslaan in een status WORM (één keer schrijven, lezen veel). Deze status maakt de gegevens niet kan worden gewist en niet kan worden gewijzigd voor een gebruiker opgegeven interval. BLOBs kunnen worden gemaakt en lezen, maar niet gewijzigd of verwijderd voor de duur van de retentie-interval.

## <a name="overview"></a>Overzicht

Onveranderbare storage kunt financiële instellingen en verwante bedrijven--name handelaar organisaties--voor het veilig opslaan van gegevens.

Typische toepassingen zijn onder andere:

- **Naleving van regelgeving**: onveranderbare opslag voor Azure Blob-opslag helpt organisaties adres SEC 17a-4(f), CFTC 1.31(d) FINRA en andere voorschriften.

- **Beveiligen van documenten vasthouden**: Blob-opslag zorgt ervoor dat gegevens kan niet worden gewijzigd of verwijderd door een gebruiker, met inbegrip van gebruikers met beheerdersrechten account.

- **Juridische bewaring**: onveranderbare opslag voor Azure Blob-opslag kan gebruikers voor het opslaan van gevoelige informatie die essentieel is voor een geschil of een juridisch onderzoek in een fraudebestendig staat voor de gewenste duur.

Onveranderbare opslag maakt:

- **Ondersteuning voor op tijd gebaseerd bewaren groepsbeleid**: gebruikers een beleid voor het opslaan van gegevens voor een opgegeven interval instellen.

- **Ondersteuning van Groepsbeleid juridisch**: wanneer het bewaarinterval is niet bekend, gebruikers juridische bewaring voor het opslaan van gegevens immutably totdat de juridische bewaring is uitgeschakeld kunnen instellen.  Als een juridische bewaring is ingesteld, kunnen blobs worden gemaakt en gelezen, maar niet worden gewijzigd of verwijderd. Elke juridische bewaring is gekoppeld aan een gebruiker gedefinieerde alfanumerieke label, dat wordt gebruikt als een tekenreeks-id (zoals een case-ID).

- **Ondersteuning voor alle lagen blob**: WORM beleidsregels zijn onafhankelijk van de Azure Blob storage-laag en zijn van toepassing op alle lagen: hot, cool en archive Storage. Gebruikers kunnen de gegevens opslaan in de meeste kosten geoptimaliseerd laag voor hun workloads behoud van gegevens onveranderbaarheid.

- **Configuratie van de container op serverniveau**: gebruikers kunnen configureren voor op tijd gebaseerd bewaarbeleid en juridisch tags op het niveau van de container. Met behulp van eenvoudige container-niveau instellingen, kunnen gebruikers maken en op tijd gebaseerd bewaarbeleid; vergrendelen retentie-intervallen; uitbreiden Stel en juridische bewaring; en nog veel meer. Dit beleid van toepassing op alle blobs in de container, bestaande en nieuwe.

- **Ondersteuning voor logboekregistratie controleren**: elke container bevat een logboek. Het bevat maximaal vijf tijd gebaseerd bewaren-opdrachten voor het beleid voor het vergrendelde op tijd gebaseerd bewaren, met een maximum van drie logboeken voor extensies van retentie-interval. Het logboek bevat voor het bewaren op basis van tijd, de gebruikers-ID, het opdrachttype, de tijdstempels en de retentie-interval. Het logboek bevat de gebruikers-ID, het opdrachttype, de tijdstempels voor juridische bewaring en juridisch tags. Dit logboek worden bewaard gedurende de levensduur van de container, in overeenstemming met de seconde 17a-4(f)-regelgeving. De [Azure Activity Log](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ziet u een meer uitgebreide logboek van alle het besturingselement vlak activiteiten. Het is de verantwoordelijkheid van de gebruiker voor het opslaan van deze logboeken permanent mogelijk zijn vereist voor wettelijke of andere doeleinden.

Onveranderbare opslag is ingeschakeld in alle Azure openbare regio's.

## <a name="how-it-works"></a>Hoe werkt het?

Onveranderbare opslag voor Azure Blob storage ondersteunt twee typen onveranderbare beleidsregels of WORM: tijd gebaseerd bewaren en juridische bewaring. Zie voor meer informatie over het maken van deze onveranderbare beleidsregels de [aan de slag](#Getting-started) sectie.

Wanneer een op tijd gebaseerd bewaarbeleid of een juridische bewaring wordt toegepast op een container, alle bestaande blobs verplaatsen naar de onveranderbare (schrijven en verwijderen die zijn beveiligd) staat. Alle nieuwe blobs die zijn geüpload naar de container wordt ook verplaatsen naar de status van de onveranderbare.

> [!IMPORTANT]
> Een op tijd gebaseerd bewaarbeleid moet *vergrendeld* voor de blob in een onveranderbare (schrijven en verwijderen die zijn beveiligd) staat voor seconde 17a-4(f) en andere voorschriften. Het is raadzaam dat u het beleid in een redelijk tijdsbestek, meestal binnen 24 uur vergrendelen. U kunt beter geen de *ontgrendeld* staat voor enig doel dan op korte termijn functie proefversies.

Wanneer een op tijd gebaseerd bewaarbeleid wordt toegepast op een container, alle blobs in de container blijft in de onveranderbare staat voor de duur van de *effectieve* bewaarperiode. De effectieve retentieperiode voor bestaande blobs is gelijk aan het verschil tussen het tijdstip waarop de blob is gemaakt en de door de gebruiker opgegeven retentieperiode. 

Voor nieuwe blobs is de effectieve retentieperiode gelijk aan de door de gebruiker opgegeven retentieperiode. Omdat gebruikers de retentie-interval wijzigen kunnen, onveranderbare storage maakt gebruik van de meest recente waarde van de gebruiker opgegeven bewaarinterval voor het berekenen van de daadwerkelijke bewaarperiode.

> [!TIP]
> Voorbeeld:
> 
> Een gebruiker maakt een op tijd gebaseerd bewaarbeleid met een retentie-interval van vijf jaar.
>
> De bestaande blob in die container, testblob1, is één jaar geleden gemaakt. De daadwerkelijke bewaarperiode voor testblob1 is vier jaar.
>
> Nu wordt een nieuwe blob, testblob2, geüpload naar de container. De daadwerkelijke bewaarperiode voor deze nieuwe blob is vijf jaar.

### <a name="legal-holds"></a>Juridische bewaring

Als u een juridische bewaring instelt, worden alle bestaande en nieuwe blobs in de status van de onveranderbare blijven totdat de juridische bewaring is uitgeschakeld. Zie voor meer informatie over het instellen en schakel juridische bewaring de [aan de slag](#Getting-started) sectie.

Een container kan zowel een juridische bewaring en een op tijd gebaseerd bewaarbeleid hebben op hetzelfde moment. Alle blobs in die container blijven in de status van de onveranderbare, totdat alle juridische bewaring zijn uitgeschakeld, zelfs als de daadwerkelijke bewaarperiode is verlopen. Daarentegen blijft een blob in een onveranderbare status totdat de daadwerkelijke bewaarperiode is verlopen, zelfs als alle juridische bewaring zijn gewist.

De volgende tabel bevat de typen van blob-bewerkingen die zijn uitgeschakeld voor de verschillende onveranderbare scenario's. Zie voor meer informatie de [Azure Blob Service-API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) documentatie.

|Scenario  |BLOB-status  |BLOB-bewerkingen niet toegestaan  |
|---------|---------|---------|
|Effectieve retentieperiode voor de blob is nog niet verlopen en/of wettelijke bewaring is ingesteld     |Onveranderbaar: beveiligd tegen verwijderen en schrijven         |Delete Container, Delete Blob, Put Blob1, Put Block, Put Block List, Set Blob Metadata, Put Page, Set Blob Properties, Snapshot Blob, Incremental Copy Blob, Append Block         |
|Effectieve retentieperiode voor blob is verlopen     |Alleen beveiligd tegen schrijven (verwijderen is toegestaan)         |Put Blob, Put Block, Put Block List, Set Blob Metadata, Put Page, Set Blob Properties, Snapshot Blob, Incremental Copy Blob, Append Block         |
|Alle juridische bevat uitgeschakeld en niet op tijd gebaseerd bewaarbeleid is ingesteld op de container     |Veranderlijk         |Geen         |
|Er is geen WORM-beleid is gemaakt (op basis van tijd bewaren of juridische bewaring)     |Veranderlijk         |Geen         |

> [!NOTE]
> De eerste plaats Blob en de blokkeringslijst plaatsen en Put-blokken bewerkingen die nodig zijn voor een blob maken zijn in de eerste twee scenario's uit de voorgaande tabel toegestaan. Alle volgende bewerkingen zijn niet toegestaan.
>
> Onveranderbare storage is alleen beschikbaar in GPv2- en Blob storage-accounts. Deze moet worden gemaakt via [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="pricing"></a>Prijzen

Er is geen extra kosten voor het gebruik van deze functie. De prijs is onveranderbaar gegevens op dezelfde manier als normale, veranderlijke gegevens. Zie voor details over de prijzen, de [Azure Storage-pagina met prijzen](https://azure.microsoft.com/pricing/details/storage/blobs/).

### <a name="restrictions"></a>Beperkingen

Tijdens de openbare preview gelden de volgende beperkingen:

- *Sla geen productie- of business-kritische gegevens.*
- Alle preview en NDA beperkingen zijn van toepassing.

## <a name="getting-started"></a>Aan de slag

De meest recente versies van de [Azure-portal](http://portal.azure.com), [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), en [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May2018) onveranderbare storage ondersteuning voor Azure Blob-opslag.

### <a name="azure-portal"></a>Azure Portal

1. Maak een nieuwe container of selecteer een bestaande container om de blobs op te slaan die u in onveranderbare toestand wilt bewaren.
 De container moet zich in een GPv2-opslagaccount bevinden.
2. Selecteer **toegangsbeleid** in de containerinstellingen van de. Selecteer vervolgens **+ beleid toevoegen** onder **onveranderbare blob storage**.

    ![Containerinstellingen in de portal](media/storage-blob-immutable-storage/portal-image-1.png)

3. Als u tijd gebaseerd bewaren, schakelt **tijd gebaseerd bewaren** uit de vervolgkeuzelijst.

    !["Tijd gebaseerd bewaren' onder 'Beleidstype' geselecteerd](media/storage-blob-immutable-storage/portal-image-2.png)

4. Voer het retentie-interval in dagen (minimaal is één dag).

    ![Vak "Bewaarperiode update naar"](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    Zoals u in de schermafbeelding ziet, is de beginstatus van het beleid is ontgrendeld. U kunt de functie met een kleinere bewaarinterval testen en wijzigingen aanbrengen aan het beleid voordat u het vergrendelen. Vergrendelen is van essentieel belang voor u voldoet aan regelgeving zoals SEC 17 bis-4.

5. Vergrendelen van het beleid. Met de rechtermuisknop op het weglatingsteken (**...** ), en de volgende menu wordt weergegeven:

    !['Vergrendelen beleid' in het menu](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

    Selecteer **beleid vergrendelen**, en de beleidsstatus wordt nu weergegeven als vergrendeld. Nadat het beleid is vergrendeld, kan niet worden verwijderd en alleen-extensies van de retentie-interval kunnen worden.

6. Als u juridische bewaring, schakelt **+ beleid toevoegen**. Selecteer **juridische bewaring** uit de vervolgkeuzelijst.

    !["Juridisch" in het menu onder "Beleidstype"](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

7. Maak een juridische bewaring met een of meer labels.

    !['Naam van de tag' vak onder het beleidstype](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

### <a name="azure-cli-20"></a>Azure CLI 2.0

Installeer de [Azure CLI-extensie](http://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) met behulp van `az extension add -n storage-preview`.

Als u al de extensie geïnstalleerd hebt, gebruik de volgende opdracht om in te schakelen onveranderbare opslag: `az extension update -n storage-preview`.

De functie is opgenomen in de volgende opdrachtgroepen: `az storage container immutability-policy` en `az storage container legal-hold`. Voer `-h` op deze om te zien van de opdrachten.

### <a name="powershell"></a>PowerShell

[PowerShell-versie 4.4.0-preview](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May20180) onveranderbare storage ondersteunt.
Schakel de functie door de volgende stappen uit:

1. Zorg ervoor dat u de nieuwste versie van PowerShellGet geïnstalleerd hebt: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Verwijder eventuele vorige installatie van Azure PowerShell.
3. Installatie-AzureRM: `Install-Module AzureRM –Repository PSGallery –AllowClobber`. Azure kan op dezelfde manier worden geïnstalleerd vanuit deze opslagplaats.
4. De preview-versie van de Storage management vlak-cmdlets installeren: `Install-Module -Name AzureRM.Storage -AllowPrerelease -Repository PSGallery -AllowClobber`.

De [voorbeeld PowerShell-code](#sample-powershell-code) verderop in dit artikel ziet u het gebruik van functies.

## <a name="client-libraries"></a>Clientbibliotheken

De volgende clientbibliotheken bieden ondersteuning voor onveranderbare opslag voor Azure Blob-opslag:

- [.NET-clientbibliotheek versie 7.2.0-preview en hoger](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Node.js-clientbibliotheek versie 4.0.0 en hoger](https://www.npmjs.com/package/azure-arm-storage)
- [Python-clientbibliotheek versie 2.0.0 Release Candidate 2 en hoger](https://pypi.org/project/azure-mgmt-storage/2.0.0rc1/)

## <a name="supported-values"></a>Ondersteunde waarden

- De minimale bewaarinterval is één dag. Het maximum is 400 jaar.
- Voor een opslagaccount is het maximum aantal containers met vergrendeld beleid voor onveranderbare 1000.
- Voor een opslagaccount is het maximum aantal containers met een instelling voor juridische bewaring 1000.
- Voor een container is het maximum aantal tags voor juridische bewaring 10.
- De maximale lengte van een juridische bewaring-tag is 23 alfanumerieke tekens. De minimale lengte is drie tekens.
- Voor een container is het maximum aantal toegestane retentie-interval uitbreidingen voor vergrendelde onveranderbare beleid drie.
- Voor een container met een vergrendelde onveranderbare beleid houdt een maximum van vijf logboeken van het beleid op basis van tijd bewaren en maximaal 10 juridische beleid logboeken worden bewaard voor de duur van de container.

## <a name="faq"></a>Veelgestelde vragen

**Is de functie voor alleen blok-blobs, of voor pagina- en toevoeg-blobs ook toepassing?**

Onveranderbare opslag kan worden gebruikt met elk blobtype.  Maar we raden aan voornamelijk voor blok-blobs te gebruiken. In tegenstelling tot blok-blobs, pagina-blobs en toevoeg-blobs moeten worden gemaakt buiten de container van een WORM, en vervolgens gekopieerd. Nadat u deze blobs naar een container WORM niet verder kopiëren *voegt* aan een toevoeg-blob of wijzigingen aan een pagina-blob zijn toegestaan.

**Moet ik altijd een nieuw opslagaccount maken om deze functie te gebruiken?**

U kunt onveranderbare storage gebruiken met een bestaande GPv2-accounts of voor nieuwe opslagaccounts als het accounttype GPv2. Deze functie is alleen beschikbaar voor Blob-opslag.

**Wat gebeurt er als ik een container probeer te verwijderen die een *vergrendeld* retentiebeleid op basis van tijd of een juridische bewaring heeft?**

De Container verwijderen-bewerking mislukt als deze ten minste één blob aan een vergrendelde op tijd gebaseerd bewaarbeleid of een juridische bewaring. Dit is waar, zelfs als de gegevens [voorlopig verwijderde](storage-blob-soft-delete.md). De bewerking Container verwijderen slaagt als deze geen blob bevat die een actieve retentieperiode of een juridische bewaring heeft. Voordat u de container kunt verwijderen, moet u de blobs verwijderen. 

**Wat gebeurt er als ik een opslagaccount probeer te verwijderen met een WORM-container die een *vergrendeld* retentiebeleid op basis van tijd of een juridische bewaring heeft?**

De verwijdering van het opslagaccount mislukt als er minimaal één WORM-container is die een juridische bewaring of een blob met een actieve retentieperiode bevat.  Voordat u de storage-account kunt verwijderen, moet u alle WORM containers verwijderen. Zie voor informatie over het verwijderen van de beveiligingscontainer, de voorgaande vraag.

**Kan ik de gegevens verplaatsen tussen verschillende blob-lagen (dynamische toegang, statische toegang, archieftoegang) als de blob de status onveranderbaar heeft?**

Ja, met de opdracht Set Blob Tier kunt u gegevens tussen de blob-lagen verplaatsen met behoud met de onveranderbare status van de gegevens. Onveranderbare opslag wordt ondersteund op de lagen hot, cool en koude blob.

**Wat gebeurt er als ik niet meer betaal en mijn retentieperiode niet is verlopen?**

In het geval van niet-betaling geldt voor het bewaren van normale gegevens zoals bepaald in de voorwaarden en bepalingen van uw contract met Microsoft.

**Biedt u ook een evaluatie- of respijtperiode voor de zojuist beschreven functie?**

Ja. Wanneer een op tijd gebaseerd bewaarbeleid wordt gemaakt, is in een *ontgrendeld* staat. In deze status kunt u gewenste wijzigingen aanbrengen in de retentie-interval, zoals vergroten of verkleinen en zelfs verwijderen van het beleid. Nadat het beleid is vergrendeld, blijft deze vergrendelde altijd voorkomen verwijderen. Als het beleid is vergrendeld, kan de retentieperiode ook niet meer worden verkort. Wordt aangeraden dat u de *ontgrendeld* staat alleen ter evaluatie en een vergrendeling van het beleid binnen een periode van 24 uur. Deze procedures kunnen u voldoen aan de seconde 17a-4(f) en andere voorschriften.

**Is de functie beschikbaar in landelijke en overheidsclouds?**

Onveranderbare opslag is momenteel alleen beschikbaar in Azure openbare regio's. Als u geïnteresseerd in een specifieke nationale cloud bent, e- azurestoragefeedback@microsoft.com.

## <a name="sample-powershell-code"></a>PowerShell-voorbeeldcode

De volgende PowerShell-voorbeeldscript wordt ter referentie. Dit script maakt een nieuw opslagaccount en -container. Deze vervolgens ziet u hoe in te stellen en wissen juridische bewaring, maken en vergrendelen van een op tijd gebaseerd bewaarbeleid (ook wel bekend als een beleid voor onveranderbaarheid), het bewaarinterval uitbreiden.

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to the Azure Resource Manager account
Login-AzureRMAccount
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzureRmResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind Storage

# Create a new container
New-AzureRmStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Create Container 2 with a storage account object
$accountObject = Get-AzureRmStorageAccount -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount
New-AzureRmStorageContainer -StorageAccount $accountObject -Name $container2

# Get a container
Get-AzureRmStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Get a container with an account object
$containerObject = Get-AzureRmStorageContainer -StorageAccount $accountObject -Name $container

# List containers
Get-AzureRmStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount

# Remove a container (add -Force to dismiss the prompt)
Remove-AzureRmStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container2

# Remove a container with an account object
Remove-AzureRmStorageContainer -StorageAccount $accountObject -Name $container2

# Remove a container with a container object
$containerObject2 = Get-AzureRmStorageContainer -StorageAccount $accountObject -Name $container2
Remove-AzureRmStorageContainer -InputObject $containerObject2

# Set a legal hold
Add-AzureRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag tag1,tag2

# Set a legal hold with an account object
Add-AzureRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag tag3

# Set a legal hold with a container object
Add-AzureRmStorageContainerLegalHold -Container $containerObject -Tag tag4,tag5

# Clear a legal hold
Remove-AzureRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag tag2

# Clear a legal hold with an account object
Remove-AzureRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag tag3,tag5

# Clear a legal hold with a container object
Remove-AzureRmStorageContainerLegalHold -Container $containerObject -Tag tag4

# Create or update an immutability policy
## with an account name or container name

Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

## with an account object
Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

## with a container object
$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

## with an immutability policy object
Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5

# Get an immutability policy
Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# Get an immutability policy with an account object
Get-AzureRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# Get an immutability policy with a container object
Get-AzureRmStorageContainerImmutabilityPolicy -Container $containerObject

# Lock an immutability policy (add -Force to dismiss the prompt)
## with an immutability policy object

$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

## with an account name or container name
$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

## with an account object
$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

## with a container object
$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force

# Extend an immutability policy
## with an immutability policy object

$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

## with an account name or container name
$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

## with an account object
$policy = Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

## with a container object
$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy

# Remove an immutability policy (add -Force to dismiss the prompt)
## with an immutability policy object
$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

## with an account name or container name
Remove-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

## with an account object
Remove-AzureRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

## with a container object
Remove-AzureRmStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag
    
```
