---
title: Azure Database Migration Service-module gebruiken in Microsoft Azure PowerShell voor het migreren van SQL Server on-premises naar Azure SQL-database | Microsoft Docs
description: Meer informatie over het migreren van on-premises SQL Server naar Azure SQL met behulp van Azure PowerShell.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 08/05/2018
ms.openlocfilehash: ebcd145689ea1d947ca8895b37b39543b1e097ed
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525906"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-db-using-azure-powershell"></a>On-premises SQL Server migreren naar Azure SQL-database met behulp van Azure PowerShell
In dit artikel, migreert u de **Adventureworks2012** database hersteld naar een on-premises exemplaar van SQL Server 2016 of hoger met een Azure SQL Database met behulp van Microsoft Azure PowerShell. U kunt databases uit een on-premises SQL Server-exemplaar migreren naar Azure SQL Database met behulp van de `AzureRM.DataMigration` module in Microsoft Azure PowerShell.

In dit artikel leert u het volgende:
> [!div class="checklist"]
> * Maak een resourcegroep.
> * Maak een instantie van de Azure Database Migration Service.
> * Een migratieproject maken in een Azure Database Migration Service-exemplaar.
> * De migratie uitvoert.

## <a name="prerequisites"></a>Vereisten
Als u wilt deze stappen hebt voltooid, hebt u het volgende nodig:

- [SQL Server 2016 of hoger](https://www.microsoft.com/sql-server/sql-server-downloads) (alle edities)
- Om in te schakelen het TCP/IP-protocol, dat standaard met SQL Server Express-installatie is uitgeschakeld. Het TCP/IP-protocol ingeschakeld door de [instructies in dit artikel](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Het configureren van uw [Windows Firewall voor toegang tot de database-engine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Een Azure SQL Database-exemplaar. U kunt een Azure SQL Database-exemplaar maken met de details in het artikel te volgen [maken van een Azure SQL database in Azure portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 of hoger.
- Azure Database Migration Service vereist een VNET gemaakt met behulp van het Azure Resource Manager-implementatiemodel, waarmee u site-naar-site-verbinding met uw on-premises bronservers met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Beoordeling van uw on-premises database en het schema een migratie met behulp van Data Migration Assistant zoals beschreven in het artikel voltooid [ uitvoeren van een SQL Server-migratie-evaluatie](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
- Download en installeer de module AzureRM.DataMigration vanuit de PowerShell Gallery met behulp van [Install-Module PowerShell-cmdlet](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)
- De referenties waarmee verbinding met SQL Server-bronexemplaar moeten hebben de [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) machtiging.
- De referenties waarmee verbinding maken met Azure SQL DB doelinstantie moeten de machtiging beheer DATABASE hebben op de doel-Azure SQL Database-databases.
- Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Meld u aan bij uw Microsoft Azure-abonnement
Gebruik de instructies in het artikel [Meld u aan met Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.4.1) aanmelden bij uw Azure-abonnement met behulp van PowerShell.

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Een resourcegroep maken voordat u een virtuele machine kunt maken.

Maak een resourcegroep met behulp van de [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) opdracht. 

Het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* in de *EastUS* regio.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service-exemplaar maken 
U kunt een nieuw exemplaar van Azure Database Migration Service maken met behulp van de `New-AzureRmDataMigrationService` cmdlet. Deze cmdlet wordt verwacht dat de volgende vereiste parameters:
- *De naam van de Azure-resourcegroep*. U kunt [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) opdracht voor het maken van Azure-resourcegroep als eerder weergegeven en geeft de naam op als een parameter.
- *Servicenaam*. Tekenreeks die overeenkomt met de naam van de gewenste unieke service voor Azure Database Migration Service 
- *Locatie*. Hiermee geeft u de locatie van de service. Geef een locatie van een Azure data center, zoals VS-West of Zuidoost-Azië
- *SKU*. Deze parameter komt overeen met de naam van de DMS-Sku. Namen van de momenteel ondersteunde Sku zijn *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*
- *Virtuele Subnet-id*. U kunt de cmdlet gebruiken [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-4.4.1) om een subnet te maken. 

Het volgende voorbeeld wordt een service met de naam *MyDMS* in de resourcegroep *MyDMSResourceGroup*, bevindt zich in *VS-Oost* regio met behulp van een virtueel netwerk met de naam *MyVNET* en subnet met de naam *MySubnet*.

```powershell
 $vNet = Get-AzureRmVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzureRmDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Een migratieproject maken
Na het maken van een Azure Database Migration Service-exemplaar, kunt u een migratieproject maken. Een Azure Database Migration Service-project vereist verbindingsgegevens voor zowel de bron en doel-instanties, evenals een lijst met databases die u wilt migreren als onderdeel van het project.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Een Database Connection-Info-object voor de bron en doel-verbindingen maken
U kunt een Database Connection-Info-object maken met behulp van de `New-AzureRmDmsConnInfo` cmdlet. Deze cmdlet wordt verwacht dat de volgende parameters:
- *ServerType*. Het type van de verbinding met database aangevraagd, bijvoorbeeld SQL, Oracle of MySQL. Gebruik SQL voor SQL Server en SQL Azure.
- *DataSource*. De naam of IP-adres van een SQL-exemplaar of een SQL Azure-server.
- *AuthType*. Het verificatietype voor verbinding, die SqlAuthentication of WindowsAuthentication kan zijn.
- *TrustServerCertificate* parameter stelt een waarde die aangeeft of het kanaal worden versleuteld terwijl het overslaan van de certificaatketen voor het valideren van vertrouwensrelatie lopen. Waarde kan true of false zijn.

Het volgende voorbeeld wordt een object van de verbindingsgegevens voor de bron-SQL-Server met de naam MySQLSourceServer met behulp van sql-verificatie 

```powershell
$sourceConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource MySQLSourceServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Volgende voorbeeld ziet u het maken van de verbindingsgegevens voor SQL Azure database-server met de naam MySQLAzureTarget met behulp van sql-verificatie

```powershell
$targetConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource "mysqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Databases bieden voor het migratieproject
Maak een lijst van `AzureRmDataMigrationDatabaseInfo` objecten die Hiermee geeft u de databases als onderdeel van de Azure Database Migration-project dat kunnen worden opgegeven als parameter voor het maken van het project. Cmdlet `New-AzureRmDataMigrationDatabaseInfo` kan worden gebruikt voor het maken van AzureRmDataMigrationDatabaseInfo. 

Het volgende voorbeeld wordt `AzureRmDataMigrationDatabaseInfo` project voor de database AdventureWorks2016 en toegevoegd aan de lijst om te worden opgegeven als parameter voor het maken van het project.

```powershell
$dbInfo1 = New-AzureRmDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Een project-object maken
Ten slotte kunt u Azure Database Migration-project met de naam *MyDMSProject* zich in *VS-Oost* met behulp van `New-AzureRmDataMigrationProject` en de eerder gemaakte bron en doel-verbindingen en de lijst toe te voegen te migreren databases.

```powershell
$project = New-AzureRmDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Maak en start u een migratietaak
Ten slotte maken en Azure Database Migration-taak te starten. Azure Database Migration-taak vereist verbinding referentie-informatie voor zowel bron- en doel en lijst met databasetabellen moeten worden gemigreerd naast de al voorzien van het project gemaakt als een vereiste informatie. 

### <a name="create-credential-parameters-for-source-and-target"></a>Referentie-parameters voor de bron en doel maken
Beveiligingsreferenties van de verbinding kunnen worden gemaakt als een [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) object. 

Het volgende voorbeeld toont het maken van *PSCredential* objecten voor zowel bron- en -verbindingen bieden wachtwoorden als tekenreeksvariabelen *$sourcePassword* en *$ doelwachtwoord*. 

```powershell
secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Maken van een tabel-kaart en selecteer parameters voor de bron en doel voor migratie
Een andere parameter die nodig zijn voor migratie is toewijzing van tabellen van bron naar doel moet worden gemigreerd. Maak woordenlijst van tabellen die een toewijzing tussen bron- en tabellen voor migratie bevat. Het volgende voorbeeld wordt de toewijzing tussen de bron- en doeltabellen Human Resources-schema voor de database AdventureWorks 2016.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

De volgende stap is het selecteren van de bron- en -databases en geef tabeltoewijzing als u wilt migreren als een parameter met behulp van de `New-AzureRmDmsSqlServerSqlDbSelectedDB` cmdlet, zoals weergegeven in het volgende voorbeeld:

```powershell
$selectedDbs = New-AzureRmDmsSqlServerSqlDbSelectedDB -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-and-start-a-migration-task"></a>Maak en start u een migratietaak

Gebruik de `New-AzureRmDataMigrationTask` cmdlet voor het maken en starten van een migratietaak. Deze cmdlet wordt verwacht dat de volgende parameters:
- *TaskType*. Type migratietaak maken voor SQL Server naar SQL Azure Migratietype *MigrateSqlServerSqlDb* wordt verwacht. 
- *Naam resourcegroep*. De naam van de Azure-resourcegroep waarin de taak te maken.
- *ServiceName*. Azure Database Migration Service-instantie in voor het maken van de taak.
- *ProjectName*. De naam van de Azure Database Migration-project waarin de taak te maken. 
- *Taaknaam*. De naam van de taak moet worden gemaakt. 
- *Gegevensbronverbinding*. AzureRmDmsConnInfo-object voor verbinding met de gegevensbron.
- *Doel verbinding*. AzureRmDmsConnInfo object doelverbinding vertegenwoordigt.
- *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) object voor het verbinden met de bronserver.
- *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) object voor het verbinden met de doelserver.
- *SelectedDatabase*. AzureRmDmsSqlServerSqlDbSelectedDB-object voor de bron- en database-toewijzing.

Het volgende voorbeeld maakt en een migratietaak met de naam myDMSTask begint:

```powershell
$migTask = New-AzureRmDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs`
```

## <a name="monitor-the-migration"></a>De migratie controleren
U kunt de migratietaak uitgevoerd door het opvragen van de eigenschap state van de taak, zoals wordt weergegeven in het volgende voorbeeld controleren:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="next-steps"></a>Volgende stappen
- Bekijk de hulp bij de migratie in de Microsoft [handleiding voor databasemigratie](https://datamigration.microsoft.com/).