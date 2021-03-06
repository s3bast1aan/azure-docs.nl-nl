---
title: Toegang krijgen tot Azure Resource Manager met Managed Service Identity voor een Linux-VM
description: Een zelfstudie die u helpt bij het doorlopen van het proces voor het krijgen van toegang tot Azure Resource Manager met Managed Service Identity voor een Linux-VM.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 643d4814dd30926a9a4294494e768cadc60ee428
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247976"
---
# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-resource-manager"></a>Toegang krijgen tot Azure Resource Manager met Managed Service Identity voor een Linux-VM

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Deze zelfstudie laat zien hoe u Managed Service Identity kunt inschakelen voor een virtuele Linux-machine, en daarmee toegang tot de Azure Resource Manager-API kunt krijgen. Managed Service Identity's worden automatisch beheerd in Azure en stellen u in staat om verificaties uit te voeren bij services die Azure AD-verificatie ondersteunen, zonder referenties in code te hoeven invoegen. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Managed Service Identity op een virtuele Linux-machine inschakelen 
> * Uw virtuele machine toegang verlenen tot een resourcegroep in Azure Resource Manager 
> * Een toegangstoken ophalen met behulp van de identiteit van de virtuele machine en daarmee Azure Resource Manager aanroepen 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Een virtuele Linux-machine maken in een nieuwe resourcegroep

Voor deze zelfstudie maken we een nieuwe virtuele Linux-machine. U kunt Managed Service Identity ook op een bestaande VM inschakelen.

1. Klik op de knop **Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Selecteer **Compute** en selecteer vervolgens **Ubuntu Server 16.04 LTS**.
3. Geef de informatie van de virtuele machine op. Bij **Verificatietype** selecteert u **Openbare SSH-sleutel** of **Wachtwoord**. Met de gemaakte referenties kunt u zich aanmelden bij de virtuele machine.

    ![Alt-tekst voor afbeelding](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Kies een **abonnement** voor de virtuele machine in de vervolgkeuzelijst.
5. Om een nieuwe **resourcegroep** te selecteren waarin u de virtuele machine wilt maken, kiest u **Nieuwe maken**. Na het voltooien klikt u op **OK**.
6. Selecteer de grootte voor de virtuele machine. Kies om meer grootten weer te geven de optie **Alle weergeven** of wijzig het filter Ondersteund schijftype. Handhaaf op de blade Instellingen de standaardwaarden en klik op **OK**.

## <a name="enable-managed-service-identity-on-your-vm"></a>Managed Service Identity op uw VM inschakelen

Met Managed Service Identity op een virtuele machine kunt u toegangstokens uit Azure AD ophalen zonder dat u referenties in uw code hoeft op te nemen. Er gebeuren twee dingen als u Managed Service Identity inschakelt op een virtuele machine: de virtuele machine wordt bij Azure Active Directory geregistreerd om de beheerde identiteit te maken, en de identiteit wordt geconfigureerd op de virtuele machine.

1. Selecteer de **virtuele machine** waarop u Managed Service Identity wilt inschakelen.
2. Klik op de linkernavigatiebalk op **Configuratie**.
3. U ziet **Managed Service Identity**. Als u Managed Service Identity wilt registreren en inschakelen, selecteert u **Ja**. Als u het wilt uitschakelen, kiest u Nee.
4. Vergeet niet op **Opslaan** te klikken om de configuratie op te slaan.

    ![Alt-tekst voor afbeelding](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-azure-resource-manager"></a>Uw virtuele machine toegang verlenen tot een resourcegroep in Azure Resource Manager 

Met behulp van Managed Service Identity kan uw code toegangstokens ophalen voor verificatie bij resources die ondersteuning bieden voor Azure AD-verificatie. De Azure Resource Manager-API biedt ondersteuning voor Azure AD-verificatie. Eerst moeten we de identiteit van deze virtuele machine toegang verlenen tot een resource in Azure Resource Manager, in dit geval de resourcegroep waarin de virtuele machine is opgenomen.  

1. Navigeer naar het tabblad **Resourcegroepen**.
2. Selecteer de specifieke **resourcegroep** die u eerder hebt gemaakt.
3. Ga naar **Toegangsbeheer (IAM)** in het linkerpaneel.
4. Klik op **Toevoegen** om een nieuwe roltoewijzing voor de virtuele machine toe te voegen. Kies **Rol** als **lezer**.
5. Stel in de volgende vervolgkeuzelijst **Toegang toewijzen aan** de resource in op **Virtuele machine**.
6. Controleer vervolgens of het juiste abonnement wordt weergegeven in de vervolgkeuzelijst **Abonnement**. Bij **Resourcegroep** selecteert u **Alle resourcegroepen**.
7. Kies ten slotte bij **Selecteren** uw virtuele Linux-machine in de vervolgkeuzelijst en klik op **Opslaan**.

    ![Alt-tekst voor afbeelding](media/msi-tutorial-linux-vm-access-arm/msi-permission-linux.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Een toegangstoken ophalen met behulp van de identiteit van de virtuele machine en daarmee Resource Manager aanroepen 

U hebt een SSH-client nodig om deze stappen uit te voeren. Als u Windows gebruikt, kunt u de SSH-client in het [Windows-subsysteem voor Linux](https://msdn.microsoft.com/commandline/wsl/about) gebruiken. Zie [De sleutels van uw SSH-client gebruiken onder Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) of [Een sleutelpaar met een openbare SSH-sleutel en een privé-sleutel maken en gebruiken voor virtuele Linux-machines in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md) als u hulp nodig hebt bij het configureren van de sleutels van uw SSH-client.

1. Navigeer in de portal naar de virtuele Linux-machine en klik in het **overzicht** op **Verbinden**.  
2. **Maak verbinding** met de virtuele machine met de SSH-client van uw keuze. 
3. Dien in het terminalvenster met behulp van CURL een aanvraag in bij het eindpunt van de lokale instantie van Managed Service Identity om een toegangstoken voor Azure Resource Manager op te halen.  
 
    Hieronder ziet u de CURL-aanvraag voor het toegangstoken.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
    
    > [!NOTE]
    > De waarde van de parameter 'resource' moet exact overeenkomen met wat er in Azure AD wordt verwacht.  Wanneer u de resource-id van Resource Manager gebruikt, moet de URI opgeven met een slash op het einde. 
    
    Het antwoord bevat het toegangstoken dat u nodig hebt voor toegang tot Azure Resource Manager. 
    
    Reactie:  

    ```bash
    {"access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
    ```
    
    U kunt dit toegangstoken gebruiken voor toegang tot Azure Resource Manager, bijvoorbeeld om de details te bekijken van de resourcegroep waaraan u deze virtuele machine eerder toegang hebt verleend. Vervang de waarden van \<SUBSCRIPTION ID\> (abonnements-id), \<RESOURCE GROUP\> (resourcegroep) en \<ACCESS TOKEN\> (toegangstoken) door de waarden die u eerder hebt gemaakt. 
    
    > [!NOTE]
    > De URL is hoofdlettergevoelig, dus gebruik precies dezelfde naam van de resourcegroep als hiervoor, met inbegrip van de hoofdletter 'G' in 'resourceGroup'.  
    
    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    De reactie met de specifieke resourcegroepsinformatie: 
     
    ```bash
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}} 
    ```     

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd een door de gebruiker toegewezen identiteit te maken en deze te koppelen aan een virtuele machine in Azure voor toegang tot de Azure Resource Manager-API.  Zie voor meer informatie over Azure Resource Manager:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)