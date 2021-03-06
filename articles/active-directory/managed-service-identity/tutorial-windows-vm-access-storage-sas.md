---
title: Een Windows VM beheerde identiteit gebruiken voor toegang tot Azure Storage met behulp van een SAS-referenties
description: Een zelfstudie waarin wordt uitgelegd hoe u een Windows VM beheerde Service-identiteit gebruiken voor toegang tot Azure Storage, met behulp van een SAS-referenties in plaats van een toegangssleutel voor opslag.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 983cecdcdb95dca398f728dbdbe5feac69075d6a
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248367"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Zelfstudie: Een Windows VM beheerde Service-identiteit gebruiken voor toegang tot Azure Storage via SAS-referenties

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Deze zelfstudie leert u hoe u beheerde Service-identiteit inschakelen voor een Windows-Machine en vervolgens de beheerde Service-identiteit gebruiken om op te halen van een Shared Access Signature (SAS) storage referentie. Een [service-SAS-referentie](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures), om precies te zijn. 

Een Service-SAS biedt de mogelijkheid beperkte toegang verlenen tot objecten in een storage-account gedurende beperkte tijd en een bepaalde service (in ons geval de blob-service), zonder dat een toegangssleutel. U kunt een SAS-referentie gebruiken zoals u gewend bent bij opslagbewerkingen, bijvoorbeeld bij het gebruik van de Storage-SDK. Voor deze zelfstudie ziet uploaden en downloaden van een blob met behulp van PowerShell voor Azure-opslag. U leert het volgende:


> [!div class="checklist"]
> * Managed Service Identity op een virtuele Windows-machine inschakelen 
> * Uw virtuele machine toegang verlenen tot een SAS voor een opslagaccount in Resource Manager 
> * Een toegangstoken ophalen met de identiteit van de virtuele machine, en daarmee de SAS ophalen uit Resource Manager 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Een virtuele Windows-machine maken in een nieuwe resourcegroep

Voor deze zelfstudie maken we een nieuwe virtuele Windows-machine. U kunt ook beheerde Service-identiteit van een bestaande VM inschakelen.

1.  Klik op de knop **+/Nieuwe service maken** in de linkerbovenhoek van Azure Portal.
2.  Selecteer **Compute** en vervolgens **Windows Server 2016 Datacenter**. 
3.  Geef de informatie van de virtuele machine op. De referenties (combinatie van **Gebruikersnaam** en **Wachtwoord**) die u hier opgeeft, zijn de referenties waarmee u zich aanmeldt bij de virtuele machine.
4.  Kies het juiste **abonnement** voor de virtuele machine in de vervolgkeuzelijst.
5.  Om een nieuwe **resourcegroep** te selecteren waarin de virtuele machine moet worden gemaakt, kiest u **Nieuwe maken**. Na het voltooien klikt u op **OK**.
6.  Selecteer de grootte voor de virtuele machine. Kies om meer groottes weer te geven de optie **Alle weergeven** of wijzig het filter **Ondersteund schijftype**. Handhaaf op de blade Instellingen de standaardwaarden en klik op **OK**.

    ![Alt-tekst voor afbeelding](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-managed-service-identity-on-your-vm"></a>Beheerde Service-identiteit op de virtuele machine inschakelen

Een virtuele Machine beheerde Service-identiteit kunt u tokens voor toegang van Azure AD ophalen zonder dat u om referenties in uw code. Op de achtergrond, beheerde Service-identiteit inschakelen doet twee dingen: registreert uw virtuele machine met Azure Active Directory voor het maken van de beheerde identiteit, en configureert u de identiteit op de virtuele machine.

1. Navigeer naar de resourcegroep van de nieuwe virtuele machine en selecteer de virtuele machine die u in de vorige stap hebt gemaakt.
2. Klik onder de virtuele machine 'Instellingen' in het linkerdeelvenster op **configuratie**.
3. Als u wilt registreren en de beheerde Service-identiteit inschakelen, selecteert u **Ja**, als u wilt uitschakelen, kiest u Nee.
4. Vergeet niet op **Opslaan** te klikken om de configuratie op te slaan.

    ![Alt-tekst voor afbeelding](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-storage-account"></a>Create a storage account 

Als u nog geen opslagaccount hebt, maakt u er nu een. Ook kunt u deze stap overslaan en uw virtuele machine beheerde Service-identiteit toegang verlenen tot de SAS-referenties van een bestaand opslagaccount. 

1. Klik op de knop **+/Nieuwe service maken** in de linkerbovenhoek van Azure Portal.
2. Klik op **Opslag** en vervolgens op **Opslagaccount**. Het paneel Opslagaccount maken wordt weergegeven.
3. Voer een naam voor het opslagaccount in. U gaat deze gegevens later gebruiken.  
4. **Implementatiemodel** en **Soort account** moeten respectievelijk worden ingesteld op Resource Manager en Algemeen gebruik. 
5. Zorg ervoor dat de waarden van **Abonnement** en **Resourcegroep** overeenkomen met de waarden die u hebt opgegeven bij het maken van de virtuele machine in de vorige stap.
6. Klik op **Create**.

    ![Nieuw opslagaccount maken](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Een blobcontainer in het opslagaccount maken

Later zullen we een bestand uploaden en downloaden naar het nieuwe opslagaccount. Omdat voor bestanden blobopslag nodig is, moeten we een blobcontainer maken waarin het bestand kan worden opgeslagen.

1. Navigeer terug naar het zojuist gemaakte opslagaccount.
2. Klik op de koppeling **Containers** in het linkerpaneel, onder Blob service.
3. Klik op **+ Container** boven aan de pagina om het paneel Nieuwe container deelvenster uit te schuiven.
4. Geef een naam voor de container op, selecteer een toegangsniveau en klik op **OK**. De naam die u hebt opgegeven, wordt verderop in de zelfstudie gebruikt. 

    ![Opslagcontainer maken](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-managed-service-identity-access-to-use-a-storage-sas"></a>Toegang van de virtuele machine beheerde Service-identiteit voor het gebruik van een SAS-opslag 

Azure Storage biedt geen systeemeigen ondersteuning voor Azure AD-verificatie.  U kunt echter het gebruik van een beheerde Service-identiteit voor het ophalen van een SAS-opslag van Resource Manager en vervolgens de SAS gebruiken voor toegang tot opslag.  In deze stap maakt verlenen u uw virtuele machine beheerde Service-identiteit-toegang tot uw storage-account-SAS.   

1. Navigeer terug naar het zojuist gemaakte opslagaccount.   
2. Klik op de koppeling **Toegangsbeheer (IAM)** in het linkerpaneel.  
3. Klik op **+ Toevoegen** boven aan de pagina om een nieuwe roltoewijzing voor de virtuele machine toe te voegen
4. Stel **Rol** in op 'Inzender voor opslagaccounts', aan de rechterkant van de pagina.  
5. Stel in de volgende vervolgkeuzelijst **Toegang toewijzen aan** de resource in op Virtuele machine.  
6. Controleer vervolgens of het juiste abonnement wordt weergegeven in de vervolgkeuzelijst **Abonnement**, en stel **Resourcegroep** in op Alle resourcegroepen.  
7. Kies ten slotte onder **Selecteren** uw virtuele Windows-machine in de vervolgkeuzelijst en klik op **Opslaan**. 

    ![Alt-tekst voor afbeelding](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Een toegangstoken ophalen met behulp van de identiteit van de virtuele machine en daarmee Azure Resource Manager aanroepen 

Voor de rest van de zelfstudie werken we op de virtuele machine die we eerder hebben gemaakt.

In dit gedeelte moet u de PowerShell-cmdlets voor Azure Resource Manager gebruiken.  Als u deze nog niet hebt geïnstalleerd, moet u [de nieuwste versie downloaden](https://docs.microsoft.com/powershell/azure/overview) voordat u doorgaat.

1. In Azure Portal navigeert u naar **Virtuele machines**, gaat u naar uw virtuele Windows-machine, klikt u vervolgens boven aan de pagina **Overzicht** op **Verbinden**.
2. Voer uw referenties (**gebruikersnaam** en **wachtwoord**) in die u hebt toegevoegd bij het maken van de virtuele Windows-machine. 
3. Nu u een **Verbinding met extern bureaublad** met de virtuele machine hebt gemaakt, opent u PowerShell in de externe sessie. 
4. Met behulp van Powershell Invoke-WebRequest, een aanvraag indienen met het lokale eindpunt van de beheerde Service-identiteit voor een toegangstoken ophalen voor Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > De waarde van de parameter 'resource' moet exact overeenkomen met wat er in Azure AD wordt verwacht. Wanneer u de resource-id van Azure Resource Manager gebruikt, moet u de URI opgeven met een slash op het einde.
    
    Extraheer vervolgens het element 'Content' (inhoud), dat is opgeslagen als een tekenreeks in JSON-indeling (JavaScript Object Notation) in het object $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Extraheer vervolgens het toegangstoken uit het antwoord.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Een SAS-referentie ophalen uit Azure Resource Manager om opslagaanroepen te maken 

Nu PowerShell gebruiken voor het aanroepen van Resource Manager met behulp van het toegangstoken dat we opgehaald in de vorige sectie, een opslag-SAS-referentie maken. Zodra we de SAS-referenties hebt, kunnen we opslagbewerkingen aanroepen.

Voor deze aanvraag gebruiken we de volgende HTTP-aanvraagparameters voor het maken van de SAS-referentie:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Deze parameters worden opgenomen in de tekst van de POST-aanvraag voor de SAS-referentie. Zie de [naslaginformatie over REST voor het weergeven van service-SAS](/rest/api/storagerp/storageaccounts/listservicesas) voor meer informatie over de parameters voor het maken van een SAS-referentie.

Eerst de parameters niet converteren naar JSON, vervolgens de storage aanroepen `listServiceSas` referentie-eindpunt om de SAS te maken:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> De URL is hoofdlettergevoelig, dus gebruik precies dezelfde naam van de resourcegroep als hiervoor, met inbegrip van de hoofdletter 'G' in 'resourceGroups'. 

Nu kunnen we de SAS-referenties ophalen uit het antwoord:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Als u de SAS-referenties downloaden inspecteren ziet er ongeveer als volgt op:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Vervolgens maken we een bestand met de naam test.txt. Gebruik vervolgens de SAS-referenties om te verifiëren met de `New-AzureStorageContent` cmdlet, upload het bestand naar de blob-container en vervolgens het bestand te downloaden.

```bash
echo "This is a test text file." > test.txt
```

Zorg ervoor dat u eerst de Azure Storage-cmdlets installeert met `Install-Module Azure.Storage`. Upload de blob die u zojuist hebt gemaakt, met behulp van de `Set-AzureStorageBlobContent` PowerShell-cmdlet:

```powershell
$ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Reactie:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

U kunt de blob die u zojuist hebt geüpload, ook downloaden met de `Get-AzureStorageBlobContent` PowerShell-cmdlet:

```powershell
Get-AzureStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Reactie:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd over het maken van een Service-identiteit beheerd voor toegang tot Azure Storage met behulp van een SAS-referenties.  Zie voor meer informatie over Azure Storage SAS:

> [!div class="nextstepaction"]
>[Shared Access Signatures (SAS) gebruiken](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)


