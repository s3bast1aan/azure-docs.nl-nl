---
title: Bijwerken van de Azure Stack-MySQL-resourceprovider | Microsoft Docs
description: Meer informatie over hoe u de Azure Stack-MySQL-resourceprovider kunt bijwerken.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 4e894eaee6bb151b480204905d0a98324f5c353b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049592"
---
# <a name="update-the-mysql-resource-provider"></a>Bijwerken van de MySQL-resourceprovider 

*Is van toepassing op: Azure Stack-geïntegreerde systemen.*

Een nieuwe SQL-resource provider-adapter kan worden vrijgegeven wanneer Azure Stack-builds worden bijgewerkt. Hoewel de bestaande adapter werken blijft, kunt u het beste bijwerken naar de nieuwste build zo snel mogelijk. 

>[!IMPORTANT]
>U moet updates installeren in de volgorde waarin die ze zijn vrijgegeven. U kunt de versies niet overslaan. Raadpleeg de lijst met versies in [implementeert de vereisten van de provider resource](.\azure-stack-mysql-resource-provider-deploy.md#prerequisites).

## <a name="update-the-mysql-resource-provider-adapter-integrated-systems-only"></a>Bijwerken van de MySQL-resource provider-adapter (alleen geïntegreerde systemen)
Een nieuwe SQL-resource provider-adapter kan worden vrijgegeven wanneer Azure Stack-builds worden bijgewerkt. Hoewel de bestaande adapter werken blijft, kunt u het beste bijwerken naar de nieuwste build zo snel mogelijk.  
 
Het bijwerken van de resource-provider die u gebruikt de **UpdateMySQLProvider.ps1** script. Het proces is vergelijkbaar met het proces dat wordt gebruikt voor het installeren van een resourceprovider, zoals beschreven in de [implementeren van de resourceprovider](#deploy-the-resource-provider) sectie van dit artikel. Het script is inbegrepen bij het downloaden van de resourceprovider. 

De **UpdateMySQLProvider.ps1** script maakt u een nieuwe virtuele machine met de meest recente code van de resource-provider en de instellingen van de oude virtuele machine migreert naar de nieuwe virtuele machine. De instellingen die worden gemigreerd zijn database en die als host fungeert voor informatie over server en de vereiste DNS-record. 

>[!NOTE]
>Het is raadzaam dat u de meest recente installatiekopie van Windows Server 2016 Core uit het beheer van de Marketplace downloaden. Als u een update installeert wilt, kunt u plaatsen een **één** MSU-pakket in het afhankelijkheidspad van de lokale. Het script mislukken als er meer dan één MSU-bestand op deze locatie.

Het gebruik van dezelfde argumenten die worden beschreven door het script is vereist voor het script DeployMySqlProvider.ps1. Het certificaat hier ook opgeven.  

Hieronder volgt een voorbeeld van de *UpdateMySQLProvider.ps1* script dat u vanuit de PowerShell-prompt uitvoeren kunt. Zorg ervoor dat de accountgegevens en wachtwoorden zo nodig wijzigen:  

> [!NOTE] 
> Het updateproces is alleen van toepassing op geïntegreerde systemen. 

```powershell 
# Install the AzureRM.Bootstrapper module and set the profile. 
Install-Module -Name AzureRm.BootStrapper -Force 
Use-AzureRmProfile -Profile 2017-03-09-profile 

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS virtual machines 
$privilegedEndpoint = "AzS-ERCS01" 

# Point to the directory where the resource provider installation files were extracted. 
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services). 
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM. 
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access. 
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

# Change the following as appropriate. 
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
 
# Change directory to the folder where you extracted the installation files. 
# Then adjust the endpoints. 
$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
``` 
 
### <a name="updatemysqlproviderps1-parameters"></a>UpdateMySQLProvider.ps1 parameters 
U kunt deze parameters opgeven vanaf de opdrachtregel. Als u dit niet doet, of als een parametervalidatie mislukt, wordt u gevraagd de vereiste parameters opgeven. 

| Parameternaam | Beschrijving | Opmerking of de standaardwaarde | 
| --- | --- | --- | 
| **CloudAdminCredential** | De referentie voor de beheerder van de cloud, die nodig zijn voor toegang tot het eindpunt van de bevoegdheden. | _Vereist_ | 
| **AzCredential** | De referenties voor de beheerdersaccount van de Azure Stack-service. Gebruik de dezelfde referenties als u hebt gebruikt voor het implementeren van Azure Stack. | _Vereist_ | 
| **VMLocalCredential** |De referenties voor het lokale administrator-account van de SQL-resourceprovider VM. | _Vereist_ | 
| **PrivilegedEndpoint** | De IP-adres of de DNS-naam van het eindpunt van de bevoegdheden. |  _Vereist_ | 
| **DependencyFilesLocalPath** | Uw certificaat-pfx-bestand moet worden geplaatst in deze map ook. | _Optionele_ (_verplichte_ voor meerdere knooppunten) | 
| **DefaultSSLCertificatePassword** | Het wachtwoord voor het pfx-certificaat. | _Vereist_ | 
| **MaxRetryCount** | Het aantal keren dat die u wilt dat elke bewerking wordt uitgevoerd als er een fout is.| 2 | 
| **RetryDuration** | De time-outinterval tussen nieuwe pogingen in seconden. | 120 | 
| **Verwijderen** | Verwijder de resourceprovider en alle bijbehorende resources (Zie de opmerkingen bij de volgende). | Nee | 
| **Fouten opsporen-modus** | Hiermee voorkomt u dat bij fout automatisch op te schonen. | Nee | 
| **AcceptLicense** | Hiermee slaat u de prompt om de GPL-licentie te accepteren.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 
 

## <a name="next-steps"></a>Volgende stappen
[MySQL-resourceprovider onderhouden](azure-stack-mysql-resource-provider-maintain.md)
