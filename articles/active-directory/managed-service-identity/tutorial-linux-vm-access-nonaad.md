---
title: Toegang krijgen tot Azure Key Vault met Managed Service Identity voor een Linux-VM
description: Een zelfstudie die u helpt bij het doorlopen van het proces voor het krijgen van toegang tot Azure Resource Manager met Managed Service Identity voor een Linux-VM.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 54a763a768a57692cf0298c07f23fb4ed84f758f
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258147"
---
# <a name="tutorial-use-a-linux-vm-managed-service-identity-to-access-azure-key-vault"></a>Zelfstudie: Toegang krijgen tot Azure Key Vault met een Managed Service Identity voor een Linux-VM 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Deze zelfstudie laat zien hoe u Managed Service Identity kunt inschakelen voor een virtuele Linux-machine, en met die identiteit toegang kunt krijgen tot Azure Key Vault. Key Vault fungeert als een bootstrap en maakt het mogelijk voor uw clienttoepassing om met het geheim toegang te krijgen tot resources die niet zijn beveiligd met Azure Active Directory (AD). Managed Service Identity's worden automatisch beheerd in Azure en stellen u in staat om te verifiëren bij services die Azure AD-verificatie ondersteunen, zonder referenties in code te hoeven invoegen. 

In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Managed Service Identity op een virtuele Linux-machine inschakelen 
> * Uw virtuele machine toegang verlenen tot een geheim dat is opgeslagen in een sleutelkluis 
> * Een toegangstoken ophalen met behulp van de identiteit van de virtuele machine en daarmee het geheim uit de sleutelkluis ophalen 
 
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
6. Selecteer de grootte voor de virtuele machine. Kies om meer grootten weer te geven de optie **Alle weergeven** of wijzig het filter Ondersteund schijftype. Handhaaf de standaardinstellingen op de pagina Instellingen en klik op **OK**.

## <a name="enable-managed-service-identity-on-your-vm"></a>Managed Service Identity op uw VM inschakelen

Met Managed Service Identity op een virtuele machine kunt u toegangstokens uit Azure AD ophalen zonder dat u referenties in uw code hoeft op te nemen. Er gebeuren twee dingen als u Managed Service Identity inschakelt op een virtuele machine: de virtuele machine wordt bij Azure Active Directory geregistreerd om de beheerde identiteit te maken, en de identiteit wordt geconfigureerd op de virtuele machine.

1. Selecteer de **virtuele machine** waarop u Managed Service Identity wilt inschakelen.
2. Klik op de linkernavigatiebalk op **Configuratie**.
3. U ziet **Managed Service Identity**. Als u Managed Service Identity wilt registreren en inschakelen, selecteert u **Ja**. Als u het wilt uitschakelen, kiest u Nee.
4. Vergeet niet op **Opslaan** te klikken om de configuratie op te slaan.

    ![Alt-tekst voor afbeelding](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Uw virtuele machine toegang verlenen tot een geheim dat is opgeslagen in een sleutelkluis  

Met behulp van Managed Service Identity kan uw code toegangstokens ophalen voor verificatie bij resources die ondersteuning bieden voor Azure Active Directory-verificatie. Niet alle Azure-services bieden echter ondersteuning voor Azure AD-verificatie. Als u Managed Service Identity wilt gebruiken voor deze services, slaat u de referenties voor de service op in Azure Key Vault en haalt u de referenties op uit Key Vault met behulp van Managed Service Identity. 

Eerst moeten we een sleutelkluis maken en de identiteit van onze virtuele machine toegang tot de sleutelkluis verlenen.   

1. Selecteer boven aan de linkernavigatiebalk **Een resource maken** > **Beveiliging en identiteit** > **Key Vault**.  
2. Geef een **naam** op voor de nieuwe sleutelkluis. 
3. Zoek de sleutelkluis in hetzelfde abonnement en dezelfde resourcegroep als de virtuele machine die u eerder hebt gemaakt. 
4. Selecteer **Toegangsbeleid** en klik op de knop **Nieuwe toevoegen**. 
5. Selecteer **Geheimenbeheer** in Configureren met sjabloon. 
6. Kies **Principal selecteren**, en voer in het zoekveld de naam in van de virtuele machine die u eerder hebt gemaakt.  Selecteer de virtuele machine in de lijst met resultaten en klik op **Selecteren**. 
7. Klik op **OK** om het nieuwe toegangsbeleid toe te voegen en klik op **OK** om de selectie van het toegangsbeleid te voltooien. 
8. Klik op **Maken** om het maken van de sleutelkluis te voltooien. 

    ![Alt-tekst voor afbeelding](../managed-service-identity/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Voeg vervolgens een geheim toe aan de sleutelkluis, zodat u het geheim later kunt ophalen met behulp van code die wordt uitgevoerd op uw virtuele machine: 

1. Selecteer **Alle resources** en zoek en selecteer de sleutelkluis die u hebt gemaakt. 
2. Selecteer **Geheimen** en klik op **Toevoegen**. 
3. Selecteer **Handmatig** in **Opties uploaden**. 
4. Voer een naam en een waarde in voor de geheime sleutel.  De waarde mag elke gewenste waarde zijn. 
5. Laat de activeringsdatum en vervaldatum leeg en laat **Ingeschakeld** ingesteld staan op **Ja**. 
6. Klik op **Maken** om het geheim te maken. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Een toegangstoken ophalen met behulp van de identiteit van de virtuele machine en daarmee het geheim uit de sleutelkluis ophalen  

U hebt een SSH-client nodig om deze stappen uit te voeren.  Als u Windows gebruikt, kunt u de SSH-client in het [Windows-subsysteem voor Linux](https://msdn.microsoft.com/commandline/wsl/about) gebruiken. Zie [De sleutels van uw SSH-client gebruiken onder Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) of [Een sleutelpaar met een openbare SSH-sleutel en een privé-sleutel maken en gebruiken voor virtuele Linux-machines in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md) als u hulp nodig hebt bij het configureren van de sleutels van uw SSH-client.
 
1. Navigeer in de portal naar de virtuele Linux-machine en klik in het **overzicht** op **Verbinden**. 
2. **Maak verbinding** met de virtuele machine met de SSH-client van uw keuze. 
3. Dien in het terminalvenster met behulp van CURL een aanvraag in bij het eindpunt van de lokale instantie van Managed Service Identity om een toegangstoken voor Azure Key Vault op te halen.  
 
    Hieronder ziet u de CURL-aanvraag voor het toegangstoken.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    De reactie bevat het toegangstoken dat u nodig hebt voor toegang tot Resource Manager. 
    
    Reactie:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    U kunt dit toegangstoken gebruiken voor verificatie bij Azure Key Vault.  Met de volgende CURL-aanvraag wordt een geheim uit Key Vault gelezen met behulp van CURL en de REST-API van Key Vault.  U hebt de URL van uw sleutelkluis nodig. Deze vindt u in de sectie **Essentials** van de pagina **Overzicht** van de sleutelkluis.  U hebt ook het toegangstoken nodig dat u hebt verkregen in de vorige aanroep. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    De reactie ziet er ongeveer als volgt uit: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Zodra u het geheim hebt opgehaald uit de sleutelkluis, kunt u deze gebruiken om te verifiëren bij een service die een gebruikersnaam en wachtwoord vereist.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd een Managed Service Identity op een virtuele Linux-machine te maken om toegang tot Azure Key Vault te krijgen.  Zie voor meer informatie over Azure Key Vault:

> [!div class="nextstepaction"]
>[Azure Key Vault](/azure/key-vault/key-vault-whatis)




