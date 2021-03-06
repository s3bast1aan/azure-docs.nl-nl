---
title: Back-up van een SharePoint-farm op Azure-Stack
description: Azure Backup Server gebruiken om back-up en herstellen van uw SharePoint-gegevens op Azure-Stack. In dit artikel bevat de informatie voor het configureren van uw SharePoint-farm zodat de gewenste gegevens kunnen worden opgeslagen in Azure. U kunt beveiligde SharePoint-gegevens terugzetten vanaf schijf of Azure.
services: backup
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: pullabhk
ms.openlocfilehash: 309e817426fff1eb877ab02ae9aa16ddc8f5cf16
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751894"
---
# <a name="back-up-a-sharepoint-farm-on-azure-stack"></a>Back-up van een SharePoint-farm op Azure-Stack
U back-up een SharePoint-farm op Azure-Stack naar Microsoft Azure met behulp van Microsoft Azure Backup-Server (MABS) in ongeveer dezelfde manier als dat u back-up van andere gegevensbronnen. Azure Backup biedt flexibiliteit in het back-upschema maken het dagelijkse, wekelijkse, maandelijkse of jaarlijkse back-up verwijst en biedt u de bewaarperiode beleidsopties voor verschillende back-uppunten. Het biedt ook de mogelijkheid voor het opslaan van kopieën van de lokale schijf voor snelle doelstellingen voor hersteltijd (RTO) en voor het opslaan van kopieën naar Azure voor het bewaren van voordelige, op lange termijn.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint ondersteunde versies en gerelateerde beveiligingsscenario 's
Azure Backup voor MABS ondersteunt de volgende scenario's:

| Workload | Versie | SharePoint-implementatie | Beveiliging en herstel |
| --- | --- | --- | --- | --- | --- |
| SharePoint |SharePoint 2013, SharePoint 2010, SharePoint 2007 SharePoint 3.0 |SharePoint geïmplementeerd als een fysieke server of Hyper-V/VMware virtuele machine <br> -------------- <br> De Sqlalwayson | Opties voor herstel van SharePoint-Farm beveiligen: herstelfarm, database en bestand of lijstitem vanaf schijfherstelpunten.  Herstel van Azure herstelpunten farm en de database. |

## <a name="before-you-start"></a>Voordat u begint
Er zijn enkele dingen die u nodig om te bevestigen voordat u een SharePoint-farm naar Azure back-up.

### <a name="prerequisites"></a>Vereisten
Voordat u doorgaat, zorg ervoor dat u hebt [geïnstalleerd en de Azure Backup-Server voorbereid](backup-mabs-install-azure-stack.md) om workloads te beschermen.

### <a name="protection-agent"></a>Beveiligingsagent
De Azure Backup agent moet worden geïnstalleerd op de server waarop SharePoint, de servers waarop SQL Server en alle andere servers die deel van de SharePoint-farm uitmaken. Zie voor meer informatie over het instellen van de beveiligingsagent [Setup beveiligingsagent](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  De enige uitzondering hierop is dat u de agent op een enkel web-front-end (WFE)-server installeren. Azure Backup-Server moet de agent op één WFE-server alleen om te fungeren als het toegangspunt voor beveiliging.

### <a name="sharepoint-farm"></a>SharePoint-farm
Voor elke 10 miljoen items in de farm, moet er minstens 2 GB aan ruimte op het volume waar de map MABS zich bevindt. Deze ruimte is vereist voor het genereren van de catalogus. Genereren van de catalogus maakt voor MABS om specifieke items (siteverzamelingen, sites, lijsten, documentbibliotheken, mappen, individuele documenten en lijstitems) te herstellen, een lijst van de URL's die zijn opgenomen in elke inhoudsdatabase. U kunt de lijst met URL's bekijken in het deelvenster herstelbaar item in de **herstel** taakgebied van MABS Administrator-Console.

### <a name="sql-server"></a>SQL Server
Azure Backup-Server wordt uitgevoerd als lokale systeemaccount. Als u wilt back-up van SQL Server-databases, moet MABS sysadmin-machtigingen op dat account voor de server die SQL Server wordt uitgevoerd. NT AUTHORITY\SYSTEM ingesteld op *sysadmin* op de server waarop SQL Server wordt uitgevoerd voordat u een back-up.

Als de SharePoint-farm SQL Server-databases die zijn geconfigureerd met SQL Server-aliassen, installeert u de SQL Server-clientonderdelen op de front-endwebserver die MABS beveiligt.

### <a name="whats-not-supported"></a>Wat wordt er niet ondersteund
* MABS die een SharePoint-farm beveiligt biedt geen bescherming zoekindexen of toepassing-servicedatabases. U moet de beveiliging van deze databases afzonderlijk te configureren.
* MABS biedt geen back-up van de SharePoint SQL Server-databases die worden gehost op Servershares van scale-out bestandsserver (SOFS).

## <a name="configure-sharepoint-protection"></a>SharePoint-beveiliging configureren
Voordat u MABS gebruiken kunt om SharePoint te beveiligen, moet u de SharePoint VSS Writer-service (WSS Writer-service) configureren met behulp van **ConfigureSharePoint.exe**.

U vindt **ConfigureSharePoint.exe** in de map [MABS Installation Path] \bin op de front-endwebserver. Dit hulpprogramma bevat de beveiligingsagent met de referenties voor de SharePoint-farm. U uitvoeren deze op één WFE-server. Als u meerdere WFE-servers hebt, selecteert u slechts één wanneer u een beveiligingsgroep configureert.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>De SharePoint VSS Writer-service configureren
1. Op de WFE-server bij een opdrachtprompt, gaat u naar \bin\ [installatielocatie MABS]
2. Voer ConfigureSharePoint - EnableSharePointProtection.
3. Voer de referenties van de farmbeheerder. Deze account moet lid zijn van de lokale groep Administrators op de WFE-server. Als de farmbeheerder geen lokale beheerder de volgende machtigingen op de WFE-server toewijzen:
   * Verleen de WSS_Admin_WPG-groep volledige controle naar de DPM-map (% Program Files%\Microsoft Azure Backup\DPM).
   * De WSS_Admin_WPG-groep lezen toegang verlenen tot de DPM-registersleutel (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> U moet ConfigureSharePoint.exe opnieuw uitgevoerd wanneer er een wijziging in de beheerdersreferenties van de SharePoint-farm.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Back-up van een SharePoint-farm met behulp van MABS
Nadat u hebt MABS en de SharePoint-farm zoals hierboven is geconfigureerd, kan SharePoint worden beveiligd door MABS.

### <a name="to-protect-a-sharepoint-farm"></a>Een SharePoint-farm beveiligen
1. Van de **beveiliging** tabblad van de MABS Administrator-Console klikt u op **nieuw**.
    ![Tabblad voor nieuwe beveiliging](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Op de **Type beveiligingsgroep selecteren** pagina van de **nieuwe beveiligingsgroep maken** wizard, selecteer **Servers**, en klik vervolgens op **volgende**.

    ![Type beveiligingsgroep selecteren](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Op de **groepsleden selecteren** scherm, selecteert u het selectievakje in voor de SharePoint-server die u wilt beveiligen en klikt u op **volgende**.

    ![Groepsleden selecteren](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Met de beveiligingsagent is geïnstalleerd, ziet u de server in de wizard. MABS toont ook de structuur. Omdat u ConfigureSharePoint.exe hebt uitgevoerd, wordt MABS communiceert met de SharePoint VSS Writer-service en de bijbehorende SQL Server-databases en de structuur van SharePoint-farm, de bijbehorende inhoudsdatabases, en alle bijbehorende items herkent.
   >
   >
4. Op de **methode voor gegevensbeveiliging selecteren** pagina, voer de naam van de **beveiligingsgroep**, en selecteer de gewenste *beveiligingsmethode*. Klik op **Volgende**.

    ![Methode voor gegevensbeveiliging selecteren](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > De schijf-methode voor gegevensbeveiliging helpt te voldoen aan korte doelstellingen voor hersteltijd.
   >
   >
5. Op de **Kortetermijndoelen opgeven** pagina, selecteer de gewenste **bewaartermijn** en als u wilt dat back-ups te identificeren.

    ![Korte-termijndoelstellingen opgeven](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Omdat het herstel meestal nodig is voor gegevens die minder dan vijf dagen oud, we een bewaartermijn van vijf dagen op schijf geselecteerd en ervoor gezorgd dat de back-up moet worden uitgevoerd tijdens de niet-productieve uren, in dit voorbeeld.
   >
   >
6. De opslaggroep schijfruimte die voor de beveiligingsgroep is toegewezen en klik vervolgens op **volgende**.
7. Voor elke beveiligingsgroep wordt MABS toegewezen schijfruimte voor het opslaan en beheren van replica's. MABS moet op dit punt wordt een kopie van de geselecteerde gegevens maken. Selecteer hoe en wanneer u wilt dat de replica is gemaakt en klik op **volgende**.

    ![Methode voor het maken van replica selecteren](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Om ervoor te zorgen dat verkeer niet wordt gedaan, selecteer een tijdstip buiten productie-uren.
   >
   >
8. MABS wordt de gegevensintegriteit gewaarborgd door het uitvoeren van consistentiecontroles uit op de replica. Er zijn twee opties beschikbaar. Kunt u een planning wordt uitgevoerd, consistentiecontroles of DPM consistentiecontroles automatisch op de replica wordt uitgevoerd wanneer het inconsistent wordt. Selecteer de gewenste optie en klik vervolgens op **volgende**.

    ![Consistentiecontrole](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Op de **gegevens voor Online beveiliging opgeven** pagina, selecteert u de SharePoint-farm die u wilt beveiligen en klik vervolgens op **volgende**.

    ![DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Op de **Online back-upschema opgeven** pagina, selecteer uw voorkeursplanning en klik vervolgens op **volgende**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > MABS biedt een maximum van twee dagelijkse back-ups naar Azure vanaf het vervolgens beschikbaar nieuwste schijf back-punt. Azure Backup kan ook bepalen welke WAN-bandbreedte die kan worden gebruikt voor back-ups in piek- en daluren met behulp van [Azure back-up netwerkbeperking](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. Afhankelijk van het back-upschema die u hebt geselecteerd op de **Online bewaarbeleid opgeven** pagina, selecteert u het bewaarbeleid voor dagelijkse, wekelijkse, maandelijkse en jaarlijkse back-uppunten.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > Een opa-vader-zoon bewaarschema waarin een andere bewaarbeleid kan worden gekozen MABS gebruikt voor andere back-uppunten.
    >
    >
12. Vergelijkbaar met schijf, een eerste verwijzing punt replica moet worden gemaakt in Azure. Selecteer uw voorkeursoptie een eerste back-up naar Azure maken en klik vervolgens op **volgende**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Controleer de geselecteerde instellingen op de **samenvatting** pagina en klik vervolgens op **groep maken**. U ziet een bericht nadat de beveiligingsgroep is gemaakt.

    ![Samenvatting](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Een SharePoint-item terugzetten vanaf schijf met MABS
In het volgende voorbeeld wordt de *herstellen van SharePoint-item* per ongeluk is verwijderd en moet worden hersteld.
![MABS SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Open de **DPM Administrator-Console**. Alle SharePoint-farms die zijn beveiligd door DPM worden weergegeven in de **beveiliging** tabblad.

    ![MABS SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Om te beginnen met het item herstellen, selecteert u de **herstel** tabblad.

    ![MABS SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. U kunt zoeken in SharePoint voor *herstellen van SharePoint-item* verwijzen met behulp van een zoekopdracht op basis van een jokerteken binnen een herstelbewerking bereik.

    ![MABS SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Selecteer het juiste herstelpunt in de zoekresultaten, met de rechtermuisknop op het item en selecteer vervolgens **herstellen**.
5. U kunt ook bladeren door verschillende herstelpunten en selecteer een database of het item dat u wilt herstellen. Selecteer **datum > hersteltijd**, en selecteer vervolgens de juiste **Database > SharePoint-farm > herstelpunt > Item**.

    ![MABS SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Met de rechtermuisknop op het item en selecteer vervolgens **herstellen** openen de **Herstelwizard**. Klik op **Volgende**.

    ![Selectie voor herstel controleren](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Selecteer het type herstel dat u wilt uitvoeren en klik vervolgens op **volgende**.

    ![Hersteltype](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > De selectie van **herstellen naar oorspronkelijke** Hiermee herstelt u het item naar de oorspronkelijke SharePoint-site in het voorbeeld.
   >
   >
8. Selecteer de **herstelproces** die u wilt gebruiken.

   * Selecteer **herstellen zonder herstelfarm** als de SharePoint-farm is niet gewijzigd en is hetzelfde als het herstelpunt dat wordt hersteld.
   * Selecteer **herstellen met behulp van een herstelfarm** als de SharePoint-farm is gewijzigd sinds het herstelpunt is gemaakt.

     ![Herstelproces](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Een gefaseerde installatie locatie voor het exemplaar van SQL Server tijdelijk voor de database, en bieden een gefaseerde installatie bestandsshare op MABS en de server waarop SharePoint voor het herstellen van het item wordt uitgevoerd.

    ![Fasering Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS koppelt de inhoudsdatabase die als host voor de SharePoint-item naar de tijdelijke SQL Server-exemplaar fungeert. Van de inhoud van de database, het Hiermee herstelt u het item en plaatst deze op de tijdelijke bestandslocatie op MABS. Het herstelde item dat is nu op de faseringslocatie moet worden geëxporteerd naar de faseringslocatie op de SharePoint-farm.

    ![Fasering Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Selecteer **herstelopties opgeven**, en beveiligingsinstellingen toepassen op de SharePoint-farm of toepassen van de beveiligingsinstellingen van het herstelpunt. Klik op **Volgende**.

    ![Opties voor herstel](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > U kunt het gebruik van netwerkbandbreedte te beperken. Dit minimaliseert van invloed op de productieserver tijdens de productie-uren.
    >
    >
11. Controleer de overzichtsgegevens en klik vervolgens op **herstellen** om te beginnen met herstel van het bestand.

    ![Samenvatting van herstel](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Nu selecteren de **bewaking** tabblad de **MABS Administrator-Console** om weer te geven de **Status** van het herstel.

    ![Herstelstatus](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Het bestand is nu hersteld. U kunt de SharePoint-site om te controleren van het bestand moet worden teruggezet vernieuwen.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Een SharePoint-database herstellen van Azure met DPM
1. Als u wilt herstellen op een SharePoint-inhoudsdatabase, bladeren door verschillende herstelpunten (zoals eerder is weergegeven) en selecteer het herstelpunt dat u wilt herstellen.

    ![MABS SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dubbelklik op de SharePoint-herstelpunt om weer te geven van de beschikbare informatie van de SharePoint-catalogus.

   > [!NOTE]
   > Omdat de SharePoint-farm is beveiligd voor lange bewaartermijn in Azure, is er is geen catalogusinformatie (metagegevens) beschikbaar op MABS. Als een SharePoint-inhoudsdatabase punt in tijd worden hersteld moet, moet u als gevolg hiervan de SharePoint-farm opnieuw catalogiseren.
   >
   >
3. Klik op **opnieuw catalogiseren**.

    ![MABS SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    De **Cloud opnieuw catalogiseren** statusvenster wordt geopend.

    ![MABS SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Nadat catalogiseren is voltooid, wordt de status gewijzigd in *geslaagd*. Klik op **Sluiten**.

    ![MABS SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Klik op de SharePoint-object dat wordt weergegeven in de MABS **herstel** tabblad om op te halen van de inhoud van de database-structuur. Met de rechtermuisknop op het item en klik vervolgens op **herstellen**.

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Op dit moment, volgt u de [herstel stappen eerder in dit artikel](#restore-a-sharepoint-item-from-disk-using-dpm) voor het herstellen van een SharePoint-inhoudsdatabase van de schijf.

## <a name="faqs"></a>Veelgestelde vragen
V: kan ik een SharePoint-item naar de oorspronkelijke locatie herstellen als SharePoint is geconfigureerd met behulp van SQL AlwaysOn (met beveiliging op schijf)?<br>
A: Ja, de items kan worden hersteld naar de oorspronkelijke SharePoint-site.

V: kan ik een SharePoint-database naar de oorspronkelijke locatie herstellen als SharePoint met behulp van SQL AlwaysOn is geconfigureerd?<br>
A: omdat SharePoint-databases zijn geconfigureerd in de SQL AlwaysOn, kan deze niet worden gewijzigd als de beschikbaarheidsgroep wordt verwijderd. Als gevolg hiervan terugzetten MABS een database niet naar de oorspronkelijke locatie. U kunt een SQL Server-database naar een ander exemplaar van SQL Server herstellen.

## <a name="next-steps"></a>Volgende stappen

Zie de [back-up van bestanden en toepassing](backup-mabs-files-applications-azure-stack.md) artikel.
Zie de [back-up SQL Server op Azure-Stack](backup-mabs-sql-azure-stack.md) artikel.
