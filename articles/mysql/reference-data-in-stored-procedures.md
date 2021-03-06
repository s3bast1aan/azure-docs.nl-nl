---
title: Azure-Database voor de MySQL-gegevens in replicatie opgeslagen Procedures
description: Dit artikel bevat alle opgeslagen procedures gebruikt voor replicatie van gegevens in.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: 2d62cd693d7a67faf836c645f8bd33de9afca949
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266105"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Azure-Database voor de MySQL-gegevens in replicatie opgeslagen Procedures

Gegevens in replicatie kunt u gegevens van een MySQL-server on-premises uitgevoerd in de virtuele machines of databaseservices die worden gehost door andere cloudproviders aan de Azure-Database MySQL-service synchroniseren.

De volgende opgeslagen procedures worden kunt u instellen of verwijderen van gegevens in replicatie tussen een primaire server en de replicaserver gebruikt.

|**De naam van de opgeslagen Procedure**|**Invoerparameters**|**Output-Parameters**|**Gebruik Opmerking**|
|-----|-----|-----|-----|
|*MySQL.az_replication_change_primary*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/A|In de context van het CA-certificaat in de parameter master_ssl_ca doorgeven overbrengen van gegevens met SSL-modus. </br><br>In een lege tekenreeks in de parameter master_ssl_ca doorgeven overbrengen van gegevens zonder SSL.|
|*MySQL.az_replication _starten*|N/A|N/A|Replicatie start.|
|*MySQL.az_replication _stop*|N/A|N/A|Replicatie wordt gestopt.|
|*MySQL.az_replication _remove_primary*|N/A|N/A|Hiermee verwijdert u de replicatierelatie tussen de primaire en replica.|
|*MySQL.az_replication_skip_counter*|N/A|N/A|Slaat een replicatiefout.|

Als u gegevens in replicatie tussen een primaire en replica in een Azure-Database voor MySQL instelt, verwijzen naar [het configureren van replicatie van gegevens in](howto-data-in-replication.md).