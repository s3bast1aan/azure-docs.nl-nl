---
title: Koppel Excel aan Hadoop met het Hive ODBC-stuurprogramma - Azure HDInsight
description: Informatie over het instellen en het Microsoft Hive ODBC-stuurprogramma voor Excel gebruiken om gegevens te doorzoeken in HDInsight-clusters uit Microsoft Excel.
keywords: hadoop excel-, hive-excel-, hive odbc
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: 4153504e7d0fb6dff4b8a675b301f54fb3588e46
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590864"
---
# <a name="connect-excel-to-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Excel verbinden met Hadoop in Azure HDInsight met het Microsoft Hive ODBC-stuurprogramma

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsoft Business Intelligence (BI) onderdelen van Microsoft Big Data-oplossing geïntegreerd met Apache Hadoop-clusters die zijn geïmplementeerd door de Azure HDInsight. Een voorbeeld van deze integratie is de mogelijkheid Excel verbindt met het Hive-datawarehouse van een Hadoop-cluster in HDInsight met behulp van het stuurprogramma Microsoft Hive Open Database Connectivity (ODBC).

Het is ook mogelijk om de gegevens die zijn gekoppeld aan een HDInsight-cluster en andere gegevensbronnen, met inbegrip van andere (niet-HDInsight) Hadoop-clusters uit Excel met de Microsoft Power Query-invoegtoepassing voor Excel te verbinden. Zie voor meer informatie over het installeren en met behulp van Power Query [Excel verbinding maken met HDInsight met Power Query][hdinsight-power-query].



**Vereisten**:

Voordat u dit artikel, hebt u de volgende items:

* **Een HDInsight-cluster**. Zie voor het maken van een [aan de slag met Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **Een werkstation** met Office 2013 Professional Plus, Office 365 Pro Plus, zelfstandige versie van Excel 2013 of Office 2010 Professional Plus.

## <a name="install-microsoft-hive-odbc-driver"></a>Microsoft Hive ODBC-stuurprogramma installeren
Download en installeer Microsoft Hive ODBC-stuurprogramma van de [Downloadcentrum][hive-odbc-driver-download].

Dit stuurprogramma kan worden geïnstalleerd op 32-bits of 64-bits versies van Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 en Windows Server 2012. Het stuurprogramma kunt verbinding maken met Azure HDInsight. U moet de versie die overeenkomt met de versie van de toepassing waar u het ODBC-stuurprogramma installeren. Voor deze zelfstudie wordt het stuurprogramma van Office Excel gebruikt.

## <a name="create-hive-odbc-data-source"></a>Hive ODBC-gegevensbron maken
De volgende stappen laten zien hoe u een Hive ODBC-gegevensbron maken.

1. Druk op de Windows-sleutel voor het openen van het startscherm van Windows 8 of Windows 10, en typ vervolgens **gegevensbronnen**.
2. Klik op **instellen van ODBC-gegevensbronnen (32-bits)** of **instellen van ODBC-gegevensbronnen (64-bits)** , afhankelijk van uw Office-versie. Als u van Windows 7 gebruikmaakt, kiest u **ODBC-gegevensbronnen (32 bits)** of **ODBC-gegevensbronnen (64 bits)** van **Systeembeheer**. U ziet de **ODBC-gegevensbronbeheer** dialoogvenster.
   
    ![OBDC gegevensbronbeheer](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "configureren met behulp van ODBC-gegevensbronbeheer DSN")

3. Via DNS van de gebruiker, klikt u op **toevoegen** openen de **nieuwe gegevensbron maken** wizard.
4. Selecteer **stuurprogramma Microsoft Hive ODBC**, en klik vervolgens op **voltooien**. U ziet de **Microsoft Hive ODBC-stuurprogramma voor DNS-instellingen** dialoogvenster.
5. Typ of selecteer de volgende waarden:
   
   | Eigenschap | Beschrijving |
   | --- | --- |
   |  Naam van de gegevensbron |Geef uw gegevensbron een naam |
   |  Host |Voer &lt;HDInsightClusterName>.azurehdinsight.net in. Bijvoorbeeld: myHDICluster.azurehdinsight.net |
   |  Poort |Gebruik <strong>443</strong>. (Deze poort is gewijzigd van 563 in 443.) |
   |  Database |Gebruik <strong>Standaard</strong>. |
   |  Mechanisme |Selecteer <strong>Azure HDInsight Service</strong> |
   |  Gebruikersnaam |HDInsight-cluster HTTP-gebruikersnaam invoeren. De standaardgebruikersnaam <strong>admin</strong>. |
   |  Wachtwoord |Voer gebruikerswachtwoord van HDInsight-cluster. |
   
    </table>
   
    Er zijn enkele belangrijke parameters rekening mee moet houden wanneer u klikt op **geavanceerde opties**:
   
   | Parameter | Beschrijving |
   | --- | --- |
   |  Systeemeigen Query gebruiken |Wanneer deze optie is geselecteerd, probeert het ODBC-stuurprogramma niet TSQL converteren naar HiveQL. U dient deze alleen als u 100% zeker dat u verzendt pure HiveQL-instructies gebruiken. Bij het verbinden met SQL Server of Azure SQL Database, laat u het vakje uitgeschakeld. |
   |  Rijen per blok wordt opgehaald |Tijdens het ophalen van een groot aantal records, kan afstemmen van deze parameter worden vereist om ervoor te zorgen van optimale prestaties. |
   |  Standaard kolomlengte tekenreeks, lengte van de binaire kolom, decimaal kolomschaal |De lengte van het gegevenstype en Precision-systemen kunnen beïnvloeden hoe gegevens worden geretourneerd. Ze ervoor zorgen dat de onjuiste gegevens worden geretourneerd vanwege verlies van precisie-en/of moet worden afgekapt. |

    ![Geavanceerde opties](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "DSN geavanceerde configuratieopties")

1. Klik op **testen** voor het testen van de gegevensbron. Wanneer de gegevensbron correct is geconfigureerd, worden *TESTS voltooid!*.
2. Klik op **OK** om de Test-dialoogvenster te sluiten. De nieuwe gegevensbron worden vermeld op de **ODBC-gegevensbronbeheer**.
3. Klik op **OK** om de wizard af te sluiten.

## <a name="import-data-into-excel-from-hdinsight"></a>Gegevens in Excel importeren vanuit HDInsight
De volgende stappen beschrijven de manier waarop gegevens importeren uit een Hive-tabel in een Excel-werkmap met behulp van de ODBC-gegevensbron die u in de vorige sectie hebt gemaakt.

1. Open een nieuwe of bestaande werkmap in Excel.
2. Uit de **gegevens** tabblad **gegevens ophalen**, klikt u op **van andere bronnen**, en klik vervolgens op **uit ODBC** starten de **gegevens De Verbindingswizard**.
   
    ![Wizard Gegevensverbinding Open](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "Open wizard Gegevensverbinding")
4. Selecteer de naam van de gegevensbron die u in de laatste sectie hebt gemaakt en klik vervolgens op **OK**.
5. Voer de naam van de gebruiker Hadoop (de standaardnaam is beheerder) en het wachtwoord en klik vervolgens op **Connect**.
6. Vouw in Navigator, **HIVE**, vouw **standaard**, klikt u op **hivesampletable**, en klik vervolgens op **Load**. Het duurt een paar seconden voordat de gegevens naar Excel worden geïmporteerd.

    ![HDInsight Hive ODBC-navigator](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "Open wizard Gegevensverbinding")


## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u gegevens ophaalt uit de HDInsight-Service in Excel met het Microsoft Hive ODBC-stuurprogramma. Op deze manier kunt u gegevens ophalen uit de HDInsight-Service in SQL-Database. Het is ook mogelijk om gegevens te uploaden naar een HDInsight-Service. Voor meer informatie zie:

* [Hive-gegevens visualiseren met Microsoft Power BI in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Interactive Query Hive-gegevens visualiseren met Power BI in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Zeppelin gebruiken voor het uitvoeren van Hive-query's in Azure HDInsight ](./../hdinsight-connect-hive-zeppelin.md).
* [Koppel Excel aan Hadoop met Power Query](apache-hadoop-connect-excel-power-query.md).
* [Verbinding maken met Azure HDInsight en het uitvoeren van Hive-query's met Data Lake Tools voor Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Azure HDInsight-hulpprogramma gebruiken voor Visual Studio Code](../hdinsight-for-vscode.md).
* [Gegevens uploaden naar HDInsight](./../hdinsight-upload-data.md).

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698


