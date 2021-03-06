---
title: Beveiligen van een database in SQL Data Warehouse | Microsoft Docs
description: Tips voor het beveiligen van een database in Azure SQL Data Warehouse om oplossingen te ontwikkelen.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: c42b065a307d5e10882c621191318a667e78795c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31524890"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Een database in SQL Data Warehouse beveiligen
> [!div class="op_single_selector"]
> * [Overzicht van beveiliging](sql-data-warehouse-overview-manage-security.md)
> * [Verificatie](sql-data-warehouse-authentication.md)
> * [Versleuteling (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Versleuteling (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Dit artikel helpt bij de basisprincipes van beveiliging van uw Azure SQL Data Warehouse-database. Beveiligen van gegevens in het bijzonder in dit artikel krijgt u bronnen voor het beperken van toegang, gestart en het controleren van activiteiten in een database.

## <a name="connection-security"></a>Verbindingsbeveiliging
Verbindingsbeveiliging verwijst naar de manier waarop u verbindingen met uw database beperkt en beveiligt met behulp van firewallregels en verbindingsversleuteling.

Firewallregels worden zowel door de server als de database gebruikt om verbindingspogingen van IP-adressen die niet expliciet zijn goedgekeurd te weigeren. Als u wilt toestaan verbindingen van uw toepassing of het openbare IP-adres client-computer, moet u eerst een firewallregel op serverniveau met behulp van de Azure-portal, REST-API of PowerShell maken. Als best practice moet u het IP-adresbereik dat is toegestaan door uw serverfirewall zoveel mogelijk beperken.  Voor toegang tot Azure SQL Data Warehouse vanaf uw lokale computer, zorg ervoor dat de firewall op uw netwerk en de lokale computer staat uitgaande communicatie op TCP-poort 1433.  

SQL Data Warehouse maakt gebruik van firewallregels op serverniveau. Firewallregel op databaseniveau regels worden niet ondersteund. Zie voor meer informatie [Azure SQL Database-firewall][Azure SQL Database firewall], [sp_set_firewall_rule][sp_set_firewall_rule].

Verbindingen met uw SQL Data Warehouse zijn standaard versleuteld.  Verbindingsinstellingen aanpassen om uit te schakelen van versleuteling worden genegeerd.

## <a name="authentication"></a>Verificatie
Verificatie verwijst naar hoe u uw identiteit bewijst bij het maken van verbinding met de database. SQL Data Warehouse biedt momenteel ondersteuning voor SQL Server-verificatie met een gebruikersnaam en wachtwoord, en met Azure Active Directory. 

Wanneer u de logische server voor uw database hebt gemaakt, hebt u een aanmelding 'serverbeheerder' opgegeven met een gebruikersnaam en wachtwoord. Deze referenties gebruikt, kunt u verifiëren met een database op die server als de database-eigenaar of 'dbo' via SQL Server-verificatie.

Echter moeten, als een best practice gebruikers van uw organisatie gebruiken een ander account te verifiëren. Deze manier kunt u beperkt de machtigingen te krijgen tot de toepassing en de risico's van schadelijke activiteiten te verminderen voor het geval de toepassingscode kwetsbaar voor een SQL-injectieaanvallen is. 

Voor het maken van een SQL-Server geverifieerde gebruiker verbinding maken met de **master** database op de server met uw aanmeldgegevens van serverbeheerder en maak een nieuwe server-aanmelding.  Bovendien is het een goed idee om een gebruiker maken in de hoofddatabase voor gebruikers van Azure SQL Data Warehouse. Maken van een gebruiker in master kan een gebruiker aan te melden met hulpmiddelen zoals SSMS zonder op te geven van een databasenaam op.  Ook kunnen ze de Objectverkenner gebruiken om weer te geven van alle databases op een SQL server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Vervolgens verbinding maken met uw **SQL Data Warehouse-database** met uw aanmeldgegevens van serverbeheerder en maak een databasegebruiker op basis van de aanmelding van de server die u hebt gemaakt.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Als een gebruiker machtigingen voor het uitvoeren van extra bewerkingen zoals het maken van aanmeldingen of maken van nieuwe databases geven, wijst u de gebruiker de `Loginmanager` en `dbmanager` rollen in de database master. Zie voor meer informatie over deze aanvullende functies en verificatie bij een SQL-Database [databases en aanmeldingen in Azure SQL Database beheren][Managing databases and logins in Azure SQL Database].  Zie voor meer informatie [verbinding te maken met SQL Data Warehouse door met behulp van Azure Active Directory-verificatie][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Autorisatie
Autorisatie verwijst naar wat u in een Azure SQL Data Warehouse-database doen kunt. Autorisatie-bevoegdheden worden bepaald door de lidmaatschappen van rollen en machtigingen. Het wordt aanbevolen om gebruikers de minimaal benodigde bevoegdheden te verlenen. Voor het beheren van rollen kunt u de volgende opgeslagen procedures:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Het serverbeheerdersaccount waarmee u verbinding maakt is lid van db_owner, die geautoriseerd is om alle bewerkingen binnen de database uit te voeren. Gebruik dit account voor het implementeren van schema-updates en andere beheerbewerkingen. Gebruik het 'ApplicationUser'-account met beperktere machtigingen om vanuit van uw toepassing verbinding te maken met de database met de minste bevoegdheden die nodig zijn voor uw toepassing.

Er zijn manieren verder te beperken wat een gebruiker kan doen met Azure SQL Data Warehouse:

* Gedetailleerde [machtigingen] [ Permissions] kunt u beheren welke bewerkingen kunt u op afzonderlijke kolommen, tabellen, weergaven, schema's, procedures en andere objecten in de database. Gebruik gedetailleerde machtigingen in de meeste controle hebt en de minimaal benodigde machtiging verlenen. 
* [Databaserollen] [ Database roles] andere dan db_datareader en db_datawriter kunnen worden gebruikt om krachtigere gebruikersaccounts van de toepassing of minder krachtige de accounts te maken. De ingebouwde vaste databaserollen bieden een eenvoudige manier om machtigingen te verlenen, maar kunnen ertoe leiden dat meer machtigingen worden dan nodig zijn.
* [Opgeslagen procedures] [ Stored procedures] kan worden gebruikt om de acties die kunnen worden uitgevoerd op de database te beperken.

Het volgende voorbeeld verleent leestoegang tot een door de gebruiker gedefinieerde schema.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Databases en logische servers beheren vanaf de Azure-portal of met behulp van de Azure Resource Manager-API wordt bepaald door uw portal gebruikersaccount roltoewijzingen. Zie voor meer informatie [toegangsbeheer op basis van rollen in Azure-portal][Role-based access control in Azure portal].

## <a name="encryption"></a>Versleuteling
Azure SQL Data Warehouse transparante gegevens codering (TDE) beschermt tegen de dreiging van schadelijke activiteiten door uw gegevens in rust coderen en decoderen.  Bij het versleutelen van uw database, worden gekoppelde back-ups en transactielogboekbestanden versleuteld zonder wijzigingen aan uw toepassingen. TDE versleutelt de opslag van een volledige database met behulp van een symmetrische sleutel, naam van de databaseversleutelingssleutel. 

In SQL-Database, wordt de databaseversleutelingssleutel beveiligd door een ingebouwde servercertificaat. Het ingebouwde servercertificaat is uniek voor elke SQL-Database-server. Microsoft draait automatisch deze certificaten ten minste om de 90 dagen. De versleutelingsalgoritme die wordt gebruikt door SQL Data Warehouse is AES-256. Zie voor een algemene beschrijving van TDE [Transparent Data Encryption][Transparent Data Encryption].

U kunt versleutelen uw database met de [Azure-portal] [ Encryption with Portal] of [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie en voorbeelden voor het verbinden met uw SQL Data Warehouse met verschillende protocollen, [verbinding maken met SQL Data Warehouse][Connect to SQL Data Warehouse].

<!--Image references-->

<!--Article references-->
[Connect to SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Azure SQL Database firewall]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Database roles]: https://msdn.microsoft.com/library/ms189121.aspx
[Managing databases and logins in Azure SQL Database]: https://msdn.microsoft.com/library/ee336235.aspx
[Permissions]: https://msdn.microsoft.com/library/ms191291.aspx
[Stored procedures]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Role-based access control in Azure portal]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
