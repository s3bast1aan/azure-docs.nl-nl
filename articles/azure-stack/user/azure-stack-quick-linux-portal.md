---
title: Azure Stack Quick Start - een VM-Portal maken
description: Azure Stack-Quick Start - een Linux VM maken via de portal
services: azure-stack
cloud: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: c364a9ba5a9606967fa86262417ccd1ac3aeffc3
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866267"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-with-the-azure-stack-portal"></a>Snelstartgids: een virtuele Linux-server-machine maken met de Azure Stack-portal

*Is van toepassing op: geïntegreerde Azure Stack-systemen en Azure Stack Development Kit*

U kunt een Ubuntu Server 16.04 LTS virtuele machine maken met behulp van de Azure Stack-portal. Volg de stappen in dit artikel te maken en gebruiken van een virtuele machine. Dit artikel vindt u ook de stappen voor het:

* Verbinding maken met de virtuele machine met een externe client.
* Een NGINX-webserver installeren.
* Uw resources opschonen.

## <a name="prerequisites"></a>Vereisten

* **Een installatiekopie van Linux in Azure Stack marketplace**

   De Azure Stack marketplace bevat geen standaard een installatiekopie van Linux. Voordat u een virtuele Linux-server-machine maken kunt, moet u controleren dat de Azure Stack-operator levert de **Ubuntu Server 16.04 LTS** installatiekopie u nodig hebt. De operator kan gebruiken de stappen in de [marketplace-items van Azure naar Azure Stack downloaden](../azure-stack-download-azure-marketplace-item.md) artikel.

* **Toegang tot een SSH-client**

   Als u de Azure Stack Development Kit (ASDK) gebruikt, u mogelijk geen toegang tot een SSH-client. Als u een client moet, zijn er verschillende pakketten met een SSH-client. PuTTY bevat bijvoorbeeld een SSH-client en een SSH-sleutel generator (puttygen.exe). Lees voor meer informatie over beschikbare pakketten, het volgende artikel in Azure: [hoe u SSH-sleutels gebruiken met Windows op Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   In deze snelstartgids maakt gebruik van PuTTY voor het genereren van de SSH-sleutels en verbinding maken met de virtuele machine voor Linux-server. Als u wilt downloaden en installeren van PuTTY, gaat u naar [ http://www.putty.org/ ](http://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Een SSH-sleutelpaar maken

U moet een SSH-sleutelpaar voor het voltooien van de stappen in dit artikel. Als u een bestaand SSH-sleutelpaar hebt, kunt u deze stap overslaan.

1. Navigeer naar de installatiemap van PuTTY (de standaardlocatie is ```C:\Program Files\PuTTY```) en uit te voeren ```puttygen.exe```.
2. In het venster PuTTY-Sleutelgenerator, zorg ervoor dat de **Type sleutel voor het genereren van** is ingesteld op **RSA**, en de **aantal bits in een gegenereerde sleutel** is ingesteld op **2048**. Wanneer u klaar bent, klikt u op **genereren**.

   ![PuTTY-Sleutelgenerator configuratie](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Voor het genereren van een sleutel, verplaatst u de muisaanwijzer willekeurig in de PuTTY-Sleutelgenerator-venster.
4. Wanneer het genereren van sleutels is voltooid, klikt u op **openbare sleutel opslaan** en klik vervolgens op **persoonlijke sleutel opslaan** uw sleutels op bestanden opslaan.

   ![PuTTY-Sleutelgenerator resultaten](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>Aanmelden bij de Azure Stack-portal

Aanmelden bij de Azure Stack-portal. Het adres van de Azure Stack-portal, is afhankelijk van welk product Azure Stack u een verbinding maakt:

* Voor Azure Stack Development Kit (ASDK) gaat u naar: https://portal.local.azurestack.external.
* Ga naar de URL die uw Azure Stack-operator opgegeven voor een geïntegreerde Azure Stack-systeem.

## <a name="create-the-virtual-machine"></a>De virtuele machine maken

1. Klik op **een resource maken** in de linkerbovenhoek van de Azure Stack-portal.

2. Selecteer **Compute** en selecteer vervolgens **Ubuntu Server 16.04 LTS**.
3. Klik op **Create**.

4. Typ de informatie van de virtuele machine. Bij **Verificatietype** selecteert u **Openbare SSH-sleutel**. Plakken in de openbare SSH-sleutel die u opgeslagen en klik vervolgens op **OK**.

   >[!NOTE]
 Zorg ervoor dat u een willekeurig stuk witruimte voorloop- en volgspaties verwijderen voor deze sleutel.

   ![Basisbeginselen van het deelvenster - virtuele machine configureren](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Selecteer **D1_V2** voor de virtuele machine.

   ![Het formaat van deelvenster - virtuele machine een grootte kiezen](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. Op de **instellingen** pagina, laat de standaardwaarden staan en klik op **OK**.

7. Op de **samenvatting** pagina, klikt u op **OK** implementatie van virtuele machine te starten.

## <a name="connect-to-the-virtual-machine"></a>Verbinding maken met de virtuele machine

1. Klik op **Connect** op de pagina van de virtuele machine. U ziet nu een SSH-verbindingsreeks die u moet verbinding maken met de virtuele machine.

   ![Verbinding maken met virtuele machine](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. Open PuTTY.
3. Op de **PuTTY-configuratie** scherm u gebruikt de **categorie** venster omhoog of omlaag te schuiven. Schuif omlaag naar **SSH**, vouw **SSH**, en klik vervolgens op **Auth**. Klik op **Bladeren** en kies het bestand met persoonlijke sleutel die u hebt opgeslagen.

   ![PuTTY persoonlijke sleutel selecteren](media/azure-stack-quick-linux-portal/Putty03.PNG)

4. Blader omhoog de **categorie** venster en klik vervolgens op **sessie**.
5. In de **hostnaam (of IP-adres)** vak, plak de verbindingsreeks die wordt weergegeven in de Azure Stack-portal. In dit voorbeeld wordt de tekenreeks is ```asadmin@192.168.102.34```.

   ![PuTTY-configuratie-verbindingsreeks](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. Klik op **Open** om een sessie voor de virtuele machine te openen.

   ![Linux-sessie](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>De NGINX-webserver installeren

Gebruik de volgende bash-opdrachten pakketbronnen bijwerken en de meest recente NGINX-pakket installeren op de virtuele machine.

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Wanneer u klaar bent met het installeren van NGINX, sluit de SSH-sessie en open de overzichtspagina van de virtuele machine in de Azure Stack-portal.

## <a name="open-port-80-for-web-traffic"></a>Poort 80 openen voor webverkeer

Een netwerkbeveiligingsgroep (NSG) beveiligt binnenkomend en uitgaand verkeer. Wanneer een virtuele machine wordt gemaakt in de Azure Stack-portal, wordt een inkomende regel gemaakt op poort 22 voor SSH-verbindingen. Omdat deze virtuele machine als host fungeert voor een webserver, moet een NSG-regel worden gemaakt zodat beveiligd webverkeer op poort 80.

1. Op de virtuele machine **overzicht** pagina, klikt u op de naam van de **resourcegroep**.
2. Selecteer de **netwerkbeveiligingsgroep** voor de virtuele machine. De NSG kan worden geïdentificeerd met behulp van de kolom **Type**.
3. In het menu links onder **instellingen**, klikt u op **inkomende beveiligingsregels**.
4. Klik op **Add**.
5. Typ bij **Naam** **http**. Zorg ervoor dat het **poortbereik** is ingesteld op 80 en **Actie** is ingesteld op **Toestaan**.
6. Klik op **OK**

## <a name="view-the-nginx-welcome-page"></a>De welkomstpagina van NGINX weergeven

Met NGINX is geïnstalleerd en poort 80 is geopend op uw virtuele machine, kunt u toegang tot de webserver met behulp van het openbare IP-adres van de virtuele machine. (Het openbare IP-adres wordt weergegeven op de pagina overzicht van de virtuele machine).

Open een webbrowser en Ga naar ```http://<public IP address>```.

![Welkomstpagina van NGINX web server](media/azure-stack-quick-linux-portal/linux-04.PNG)

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de resources die u niet meer nodig. Als u wilt verwijderen van de virtuele machine en de daarbij behorende bronnen, selecteer de resourcegroep op de pagina van de virtuele machine en klik vervolgens op **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quick start hebt u een eenvoudige virtuele machine van een Linux-server met een webserver geïmplementeerd. Voor meer informatie over virtuele machines van Azure Stack, blijven [overwegingen voor virtuele Machines in Azure Stack](azure-stack-vm-considerations.md).
