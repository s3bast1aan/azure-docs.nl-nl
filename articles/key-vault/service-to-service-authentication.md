---
title: Service-naar-serviceverificatie naar Azure Key Vault met behulp van .NET
description: De bibliotheek Microsoft.Azure.Services.AppAuthentication gebruiken om te verifiëren bij Azure Key Vault met behulp van .NET.
keywords: verificatie van Azure sleutelkluis lokale referenties
author: lleonard-msft
manager: mbaldwin
services: key-vault
ms.author: alleonar
ms.date: 11/15/2017
ms.topic: article
ms.prod: ''
ms.service: key-vault
ms.technology: ''
ms.assetid: 4be434c4-0c99-4800-b775-c9713c973ee9
ms.openlocfilehash: 78baef69d0d411b7611575300580c1c719c28bf8
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988809"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Service-naar-serviceverificatie naar Azure Key Vault met behulp van .NET

Als u wilt verifiëren met Azure Key Vault, moet u een referentie voor de Azure Active Directory (AD), een gedeeld geheim of een certificaat. Beheren van deze referenties kan lastig en het is verleidelijk om referenties in een app door ze in de bron-of configuratiebestanden te bundelen.

De `Microsoft.Azure.Services.AppAuthentication` voor .NET-bibliotheek vereenvoudigt dit probleem. De referenties van de ontwikkelaar wordt gebruikt om te verifiëren tijdens de lokale ontwikkeling. Wanneer de oplossing wordt later geïmplementeerd op Azure, wordt de bibliotheek automatisch overgeschakeld naar de referenties van de toepassing.  

Met de ontwikkelaarsreferenties van tijdens het ontwikkelen van lokale is veiliger omdat u niet hoeft te maken van Azure AD-referenties of referenties tussen ontwikkelaars delen.

De `Microsoft.Azure.Services.AppAuthentication` bibliotheek beheert verificatie automatisch, op zijn beurt waarmee u zich richten op uw oplossing in plaats van uw referenties.

De `Microsoft.Azure.Services.AppAuthentication` bibliotheek biedt ondersteuning voor lokale ontwikkeling met Microsoft Visual Studio, Azure CLI of Azure AD-geïntegreerde verificatie. Wanneer geïmplementeerd in Azure App Services of een Azure-virtuele Machine (VM), gebruikt de bibliotheek automatisch [beheerde Service-identiteit](/azure/active-directory/msi-overview) (MSI). Er zijn geen wijzigingen code of configuratie nodig. De bibliotheek ondersteunt ook direct gebruik van Azure AD [clientreferenties](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) wanneer MSI-bestand is niet beschikbaar of wanneer de beveiligingscontext van de ontwikkelaar kan niet worden bepaald tijdens de lokale ontwikkeling.

<a name="asal"></a>
## <a name="using-the-library"></a>Met behulp van de bibliotheek

Voor .NET-toepassingen, de eenvoudigste manier om te werken met een Managed Service Identity (MSI) is via de `Microsoft.Azure.Services.AppAuthentication` pakket. Hier wordt aan de slag:

1. Voeg een verwijzing naar de [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) NuGet-pakket aan uw toepassing.

2. Voeg de volgende code toe:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // ...

    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(
    azureServiceTokenProvider.KeyVaultTokenCallback));

    // or

    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync(
       "https://management.azure.com/").ConfigureAwait(false);
    ```

De `AzureServiceTokenProvider` klasse in de cache van het token in het geheugen opgeslagen en opgehaald uit Azure AD net voordat het verloopt. Als gevolg hiervan, u hoeft niet langer om te controleren is verlopen voordat de `GetAccessTokenAsync` methode. Roept de methode als u wilt dat het token wordt gebruikt. 

De `GetAccessTokenAsync` methode vereist een resource-id. Zie voor meer informatie, [die Azure-services ondersteunen beheerde Service-identiteit](https://docs.microsoft.com/azure/active-directory/msi-overview#which-azure-services-support-managed-service-identity).


<a name="samples"></a>
## <a name="samples"></a>Voorbeelden

De volgende voorbeelden tonen de `Microsoft.Azure.Services.AppAuthentication` bibliotheek in actie:

1. [Gebruik een Managed Service Identity (MSI) voor het ophalen van een geheim uit Azure Key Vault tijdens runtime](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Een Azure Resource Manager-sjabloon van een Azure-VM met een MSI-bestand via een programma implementeren](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Voorbeeld van .NET Core- en MSI gebruiken voor het aanroepen van Azure-services van een Azure Linux VM](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).


<a name="local"></a>
## <a name="local-development-authentication"></a>Lokale ontwikkeling-verificatie

Er zijn twee scenario's voor primaire verificatie voor lokale ontwikkeling:

- [Verificatie bij Azure-services](#authenticating-to-azure-services)
- [Verificatie bij aangepaste services](#authenticating-to-custom-services)

Hier kunt u meer informatie over de vereisten voor elk scenario en de ondersteunde hulpprogramma's.


### <a name="authenticating-to-azure-services"></a>Verificatie bij Azure-Services

Lokale machines bieden geen ondersteuning voor Managed Service Identity (MSI).  Als gevolg hiervan de `Microsoft.Azure.Services.AppAuthentication` bibliotheek maakt gebruik van uw ontwikkelaarsreferenties om uit te voeren in uw lokale ontwikkelomgeving. Wanneer de oplossing wordt geïmplementeerd naar Azure, wordt in de bibliotheek MSI overschakelen naar een stroom voor de referentie verlenen van OAuth 2.0-client gebruikt.  Dit betekent dat u kunt dezelfde code testen lokaal en extern zonder zorgen.

Voor lokale ontwikkeling `AzureServiceTokenProvider` worden opgehaald met behulp van **Visual Studio**, **Azure-opdrachtregelinterface** (CLI), of **Azure AD-geïntegreerde verificatie**. Elke optie geprobeerd sequentieel worden verwerkt en de bibliotheek maakt gebruik van de eerste optie die is geslaagd. Als er geen optie werkt, een `AzureServiceTokenProviderException` uitzondering is opgetreden met gedetailleerde informatie.

### <a name="authenticating-with-visual-studio"></a>Verificatie met Visual Studio

Voor het gebruik van Visual Studio, controleert u of:

1. U hebt geïnstalleerd [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) of hoger.

2. De [App verificatie-extensie voor Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354) is geïnstalleerd.
 
3. U aangemeld bij Visual Studio en een account kunt gebruiken voor lokale ontwikkeling hebt geselecteerd. Gebruik **extra**&nbsp;>&nbsp;**opties**&nbsp;>&nbsp;**Azure Service-verificatie**kiezen van een account met lokale ontwikkeling. 

Als u problemen bij het gebruik van Visual Studio, zoals fouten met betrekking tot het bestand tokenprovider zorgvuldig door deze stappen. 

Het is mogelijk ook die nodig zijn voor het verifiëren van uw developer-token.  Om dit te doen, gaat u naar **extra**&nbsp;>&nbsp;**opties**>**Azure&nbsp;Service&nbsp;verificatie**  en zoek naar een **zich opnieuw verifiëren** koppeling onder het geselecteerde account.  Selecteer deze om te verifiëren. 

### <a name="authenticating-with-azure-cli"></a>Verificatie met Azure CLI

Azure CLI gebruiken voor lokale ontwikkeling:

1. Installeer [Azure CLI v2.0.12](/cli/azure/install-azure-cli) of hoger. Upgrade van eerdere versies. 

2. Gebruik **az login** zich aanmeldt bij Azure.

Gebruik `az account get-access-token` om te controleren of de toegang.  Als u een foutbericht ontvangt, controleert u stap 1 is voltooid. 

Als Azure-CLI is niet geïnstalleerd in de standaardmap, ontvangt u mogelijk een die fouten rapporteren die `AzureServiceTokenProvider` het pad niet vinden voor de Azure CLI.  Gebruik de **AzureCLIPath**omgevingsvariabele voor het definiëren van de Azure CLI-installatiemap. `AzureServiceTokenProvider` voegt de map die is opgegeven de **AzureCLIPath** omgevingsvariabele de **pad** omgevingsvariabele indien nodig.

Als u bent aangemeld bij Azure CLI met behulp van meerdere accounts of uw account toegang tot meerdere abonnementen heeft, moet u om op te geven van het specifieke abonnement moet worden gebruikt.  Gebruik het volgende om dit te doen:

```
az account set --subscription <subscription-id>
```

Met deze opdracht genereert uitvoer alleen op fouten.  Als u wilt controleren of de instellingen van het huidige, gebruikt u:

```
az account list
```

### <a name="authenticating-with-azure-ad-integrate-authentication"></a>Verificatie met Azure AD-integratie-verificatie

Voor het gebruik van Azure AD-verificatie, moet u controleren of:

- Uw on-premises active directory [wordt gesynchroniseerd met Azure AD](/azure/active-directory/connect/active-directory-aadconnect).

- Uw code wordt uitgevoerd op een computer voor het domein.


### <a name="authenticating-to-custom-services"></a>Verificatie bij aangepaste services

Als de serviceaanroepen van een van Azure-services, wordt de vorige stappen werken omdat de toegang tot gebruikers en toepassingen voor Azure-services toestaan.  

Bij het maken van een service die een aangepaste service aanroept, gebruikt u de referenties van de Azure AD-client voor lokale ontwikkeling-verificatie.  Er zijn twee opties: 

1.  Een service-principal gebruiken om aan te melden bij Azure:

    1.  [Maken van een service-principal](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Azure CLI gebruiken voor het aanmelden:

        ```
        az login --service-principal -u <principal-id> --password <password>
           --tenant <tenant-id> --allow-no-subscriptions
        ```

        Omdat de service-principal geen toegang tot een abonnement hebt kan, gebruikt u de `--allow-no-subscriptions` argument.

2.  Omgevingsvariabelen gebruiken om op te geven van de details van de service-principal.  Zie voor meer informatie, [uitvoeren van de toepassing met behulp van een service-principal](#running-the-application-using-a-service-principal).

Zodra u zich hebt aangemeld bij Azure, `AzureServiceTokenProvider` de service-principal gebruikt voor het ophalen van een token voor lokale ontwikkeling.

Dit geldt alleen voor lokale ontwikkeling. Als uw oplossing is geïmplementeerd in Azure, wordt de bibliotheek overbrengen naar MSI-verificatie.

<a name="msi"></a>
## <a name="running-the-application-using-a-managed-service-identity"></a>Uitvoeren van de toepassing met behulp van een beheerde Service-identiteit 

Wanneer u uw code op een Azure App Service of een virtuele Azure-machine met ingeschakelde MSI uitvoert, maakt gebruik van de bibliotheek automatisch beheerde Service-identiteit. Er zijn geen codewijzigingen vereist. 


<a name="sp"></a>
## <a name="running-the-application-using-a-service-principal"></a>Uitvoeren van de toepassing met behulp van een Service-Principal 

Het is mogelijk nodig om u te maken van een Azure AD-Client-referentie om te verifiëren. Algemene voorbeelden zijn onder meer:

1. Uw code wordt uitgevoerd op een lokale ontwikkelingsomgeving, maar niet onder de identiteit van de ontwikkelaar.  Service Fabric gebruikt bijvoorbeeld de [NetworkService-account](/azure/service-fabric/service-fabric-application-secret-management) voor lokale ontwikkeling.
 
2. Uw code wordt uitgevoerd op een lokale ontwikkelingsomgeving en u aanmelden bij een aangepaste service, zodat u uw developer-identiteit niet gebruiken. 
 
3. Uw code wordt uitgevoerd op een Azure compute-resource die nog geen beheerde Service-identiteit, zoals Azure Batch ondersteunt.

Een certificaat aan te melden bij Azure AD gebruiken:

1. Maak een [principal servicecertificaat](/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

2. Implementeren van het certificaat aan de _LocalMachine /_ of _CurrentUser_ opslaan. 

3. Stel een omgevingsvariabele met de naam **AzureServicesAuthConnectionString** aan:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={LocalMachine or CurrentUser}
    ```
 
    Vervang _{AppId}_, _{tenant-id}_, en _{Thumbprint}_ met waarden die in stap 1 is gegenereerd.

    **CertificateStoreLocation** moet een _CurrentUser_ of _LocalMachine /_, op basis van uw implementatieplan.

4. Voer de toepassing uit. 

Om aan te melden met behulp van een Azure gedeelde AD geheime referentie:

1. Maak een [service-principal met een wachtwoord](/azure/azure-resource-manager/resource-group-authenticate-service-principal) en het toegang geven tot de Key Vault. 

2. Stel een omgevingsvariabele met de naam **AzureServicesAuthConnectionString** aan:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    Vervang _{AppId}_, _{tenant-id}_, en _{ClientSecret}_ met waarden die in stap 1 is gegenereerd.

3. Voer de toepassing uit. 

Als alles goed, geen verdere wijzigingen in de code ingesteld zijn nodig.  `AzureServiceTokenProvider` maakt gebruik van de omgevingsvariabele en het certificaat te verifiëren met Azure AD. 

<a name="connectionstrings"></a>
## <a name="connection-string-support"></a>Ondersteuning voor tekenreeks-verbindingen

Standaard `AzureServiceTokenProvider` meerdere methoden gebruikt voor het ophalen van een token. 

Voor het beheren van het proces, gebruik een verbindingsreeks die is doorgegeven aan de `AzureServiceTokenProvider` constructor of opgegeven in de *AzureServicesAuthConnectionString* omgevingsvariabele. 

De volgende opties worden ondersteund:

| Verbinding&nbsp;tekenreeks&nbsp;optie | Scenario | Opmerkingen|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Lokale ontwikkeling | AzureServiceTokenProvider maakt gebruik van Azure CLI-token ophalen. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Lokale ontwikkeling | AzureServiceTokenProvider maakt gebruik van Visual Studio-token ophalen. |
| `RunAs=CurrentUser;` | Lokale ontwikkeling | AzureServiceTokenProvider maakt gebruik van geïntegreerde verificatie van Azure AD-token ophalen. |
| `RunAs=App;` | Beheerde service-identiteit | Beheerde Service-identiteit AzureServiceTokenProvider gebruikt voor het token ophalen. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint`<br>`   ={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`  | Service-principal | `AzureServiceTokenProvider` maakt gebruik van certificaten aan van Azure AD-token ophalen. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};`<br>`   CertificateSubjectName={Subject};CertificateStoreLocation=`<br>`   {LocalMachine or CurrentUser}` | Service-principal | `AzureServiceTokenProvider` certificaat wordt gebruikt om op te halen token uit Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Service-principal |`AzureServiceTokenProvider` maakt gebruik van geheim aan van Azure AD-token ophalen. |


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beheerde Service-identiteit](/azure/app-service/app-service-managed-service-identity).

- Verschillende manieren om te leren [verifiëren en autoriseren van apps](/azure/app-service/app-service-authentication-overview).

- Meer informatie over Azure AD [verificatiescenario's](/azure/active-directory/develop/active-directory-authentication-scenarios#web-browser-to-web-application).
