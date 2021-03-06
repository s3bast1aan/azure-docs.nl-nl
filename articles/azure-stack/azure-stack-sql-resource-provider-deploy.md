---
title: Met behulp van SQL-databases in Azure Stack | Microsoft Docs
description: Meer informatie over hoe u SQL-databases kunt implementeren als een service op Azure Stack en de snelle stappen voor het implementeren van de SQL Server-resource provider-adapter.
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
ms.openlocfilehash: f53b1e08da1cb2d0dc02381bf47c27e8f84cb1d0
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044829"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>De resourceprovider van SQL Server op Azure Stack implementeren
De resourceprovider van Azure Stack SQL Server gebruiken om SQL-databases als een Azure Stack-service zichtbaar te maken. De SQL-resourceprovider wordt uitgevoerd als een service op een Windows Server 2016 Server Core-machine (VM).

## <a name="prerequisites"></a>Vereisten

Er zijn verschillende vereisten die worden voldaan moet voordat u kunt de Azure Stack-SQL-resourceprovider implementeren. Om te voldoen aan deze vereisten, voert u de volgende stappen uit op een computer die toegang de beschermde VM-eindpunt tot:

- Als u dit nog niet hebt gedaan, [registreren Azure Stack](azure-stack-registration.md) met Azure, zodat u kunt Azure marketplace-items downloaden.
- U moet de Azure- en PowerShell voor Azure Stack-modules installeren op het systeem waar u deze installatie wordt uitgevoerd. Dat systeem moet een installatiekopie van Windows 10 of Windows Server 2016 met de meest recente versie van de .NET runtime. Zie [PowerShell voor Azure Stack installeren](.\azure-stack-powershell-install.md).
- De vereiste Windows Server-core VM toevoegen aan de Azure Stack marketplace door te downloaden de **Windows Server 2016 Datacenter - Server Core** installatiekopie. 
- Downloaden van de SQL-resourceprovider binaire en voer vervolgens de zelfstandige extractor om de inhoud uitpakken naar een tijdelijke map. De resourceprovider heeft een minimale bijbehorende Azure Stack bouwen. Zorg ervoor dat u het juiste binaire bestand voor de versie van Azure-Stack die u gebruikt downloaden:

    |Azure Stack-versie|SQL RP-versie|
    |-----|-----|
    |Versie 1804 (1.0.180513.1)|[SQL RP versie 1.1.24.0](https://aka.ms/azurestacksqlrp1804)
    |Versie 1802 (1.0.180302.1)|[SQL RP versie 1.1.18.0](https://aka.ms/azurestacksqlrp1802)|
    |     |     |

- Zorg ervoor dat de datacenter-integratie vereisten wordt voldaan:

    |Vereiste|Referentie|
    |-----|-----|
    |Voorwaardelijk doorsturen van DNS is correct ingesteld.|[Datacenter-integratie Azure Stack - DNS](azure-stack-integrate-dns.md)|
    |Poorten voor inkomend verkeer voor resourceproviders zijn geopend.|[Azure Stack-datacenter-integratie - eindpunten publiceren](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |PKI-certificaatonderwerp en SAN zijn juist ingesteld.|[Azure Stack verplichte PKI vereisten voor implementatie](azure-stack-pki-certs.md#mandatory-certificates)<br>[De vereisten PaaS-certificaat voor Azure Stack-implementatie](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>Certificaten

_Geïntegreerde systemen alleen voor installaties_. Moet u de SQL-PaaS-PKI-certificaat dat is beschreven in de sectie met optionele PaaS certificaten van [Azure Stack-implementatievereisten PKI](.\azure-stack-pki-certs.md#optional-paas-certificates). Plaats het pfx-bestand in de locatie die is opgegeven door de **DependencyFilesLocalPath** parameter. Bieden een certificaat voor ASDK systemen.

## <a name="deploy-the-sql-resource-provider"></a>De SQL-resourceprovider implementeren

Nadat u alle vereisten die zijn geïnstalleerd hebt, voert u de **DeploySqlProvider.ps1** script voor het implementeren van de SQL-resourceprovider. Het script DeploySqlProvider.ps1 wordt opgehaald als onderdeel van de SQL-resource provider binaire bestand dat u hebt gedownload voor uw versie van Azure Stack.

Voor het implementeren van de SQL-resourceprovider, opent u een **nieuwe** verhoogde PowerShell-venster (niet PowerShell ISE) en de wijzigingen in de map waar u de SQL-resource provider binaire bestanden hebt uitgepakt. We raden u aan met behulp van een nieuwe PowerShell-venster om te voorkomen van potentiële problemen veroorzaakt door een PowerShell-modules die al zijn geladen.

Voer het script DeploySqlProvider.ps1, die bestaat uit de volgende taken:

- De certificaten en andere artefacten worden geüpload naar een opslagaccount in Azure Stack.
- Publiceert galeriepakketten zodat u SQL-databases met behulp van de galerie kunt implementeren.
- Hiermee geeft u een galerijpakket voor het implementeren van hostingservers mogelijk.
- Een virtuele machine met behulp van de Windows Server 2016 core u hebt gedownload en vervolgens installeert hij de SQL-resourceprovider implementeert.
- Hiermee wordt een lokale DNS-record die wordt toegewezen aan uw VM-resourceprovider geregistreerd.
- Hiermee wordt de resourceprovider geregistreerd met de lokale Azure Resource Manager voor de operator-account.

> [!NOTE]
> Wanneer de implementatie van SQL-resource provider wordt gestart, wordt de **system.local.sqladapter** resourcegroep wordt gemaakt. Het duurt maximaal 75 minuten voor het voltooien van de vereiste implementaties aan deze resourcegroep.

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 parameters

U kunt de volgende parameters vanaf de opdrachtregel opgeven. Als u dit niet doet, of als een parametervalidatie mislukt, wordt u gevraagd de vereiste parameters opgeven.

| Parameternaam | Beschrijving | Opmerking of de standaardwaarde |
| --- | --- | --- |
| **CloudAdminCredential** | De referentie voor de beheerder van de cloud, die nodig zijn voor toegang tot het eindpunt van de bevoegdheden. | _Vereist_ |
| **AzCredential** | De referenties voor de beheerdersaccount van de Azure Stack-service. Gebruik de dezelfde referenties die u gebruikt voor het implementeren van Azure Stack. | _Vereist_ |
| **VMLocalCredential** | De referenties voor het lokale administrator-account van de SQL-resourceprovider VM. | _Vereist_ |
| **PrivilegedEndpoint** | De IP-adres of de DNS-naam van het eindpunt van de bevoegdheden. |  _Vereist_ |
| **DependencyFilesLocalPath** | Voor alleen geïntegreerde systemen, moet uw certificaat-pfx-bestand in deze map worden geplaatst. U kunt eventueel een pakket voor Windows Update MSU hier kopiëren. | _Optionele_ (_verplichte_ voor geïntegreerde systemen) |
| **DefaultSSLCertificatePassword** | Het wachtwoord voor het pfx-certificaat. | _Vereist_ |
| **MaxRetryCount** | Het aantal keren dat die u wilt dat elke bewerking wordt uitgevoerd als er een fout is.| 2 |
| **RetryDuration** | De time-outinterval tussen nieuwe pogingen in seconden. | 120 |
| **Verwijderen** | Hiermee verwijdert u de resourceprovider en alle bijbehorende resources (Zie de opmerkingen bij de volgende). | Nee |
| **Fouten opsporen-modus** | Hiermee voorkomt u dat bij fout automatisch op te schonen. | Nee |

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Met behulp van een aangepast script SQL-resourceprovider implementeren

Om te voorkomen handmatige configuratie bij het implementeren van de resourceprovider, kunt u het volgende script aanpassen. Wijzig de standaard-accountgegevens en -wachtwoorden voor uw Azure Stack-implementatie.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module  -Name AzureStack -RequiredVersion 1.3.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

Wanneer het script voor resource provider-installatie is voltooid, vernieuw uw browser om ervoor te zorgen dat u de meest recente updates kunt zien.

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Controleer of de implementatie met behulp van de Azure Stack-portal

U kunt de volgende stappen Controleer of de SQL-resourceprovider met succes is geïmplementeerd.

1. Meld u aan de admin-portal als de servicebeheerder.
2. Selecteer **resourcegroepen**.
3. Selecteer de **system.\< locatie\>.sqladapter** resourcegroep.
4. Op de overzichtspagina voor de resourcegroep overzicht, mogen er geen mislukte implementaties.

      ![Controleer of de implementatie van de SQL-resourceprovider](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

## <a name="next-steps"></a>Volgende stappen

[Hosting-servers toevoegen](azure-stack-sql-resource-provider-hosting-servers.md)
