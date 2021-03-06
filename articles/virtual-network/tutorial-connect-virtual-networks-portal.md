---
title: Virtuele netwerken verbinden met virtueel-netwerkpeering - zelfstudie - Azure Portal | Microsoft Docs
description: In deze zelfstudie leert u hoe u virtuele netwerken kunt verbinden met virtueel-netwerkpeering met behulp van Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: d702253c7b58b0a29c03e6563238b56ae75fa0d1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841785"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Zelfstudie: virtuele netwerken verbinden met virtueel-netwerkpeering met behulp van Azure Portal

U kunt virtuele netwerken met elkaar verbinden met virtueel-netwerk peering. Wanneer virtuele netwerken als peers zijn gekoppeld, kunnen resources in beide virtuele netwerken met elkaar communiceren met dezelfde latentie en bandbreedte als wanneer de resources zich in hetzelfde virtuele netwerk zouden bevinden. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Twee virtuele netwerken maken
> * Twee virtuele netwerken koppelen met virtueel-netwerkpeering
> * Een virtuele machine (VM) implementeren op elk van de virtuele netwerken
> * Communicatie tussen virtuele machines

U kunt deze zelfstudie desgewenst volgen met behulp van de [Azure CLI](tutorial-connect-virtual-networks-cli.md) of [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure. 

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-virtual-networks"></a>Virtuele netwerken maken

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Selecteer **Netwerken** en selecteer vervolgens **Virtueel netwerk**.
3. Voer de volgende informatie in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **Maken**:

    |Instelling|Waarde|
    |---|---|
    |Naam|myVirtualNetwork1|
    |Adresruimte|10.0.0.0/16|
    |Abonnement| Selecteer uw abonnement.|
    |Resourcegroep| Selecteer **Nieuwe maken** en voer *myResourceGroup* in.|
    |Locatie| Selecteer **VS Oost**.|
    |Subnetnaam|Subnet1|
    |Subnetadresbereik|10.0.0.0/24|

      ![Een virtueel netwerk maken](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Volg de stappen 1-3 nogmaals, met de volgende wijzigingen:

    |Instelling|Waarde|
    |---|---|
    |Naam|myVirtualNetwork2|
    |Adresruimte|10.1.0.0/16|
    |Resourcegroep| Selecteer **Bestaande gebruiken** en selecteer **myResourceGroup**.|
    |Subnetadresbereik|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Peering van virtuele netwerken

1. Begin in het zoekvak bovenaan Azure Portal *MyVirtualNetwork1* te typen. Wanneer **myVirtualNetwork1** wordt weergegeven in de zoekresultaten, selecteert u dit.
2. Selecteer **Peerings**onder **INSTELLINGEN** en selecteer **+ Toevoegen**, zoals in de volgende afbeelding:

    ![Peering maken](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Voer de volgende informatie in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **OK**.

    |Instelling|Waarde|
    |---|---|
    |Naam|myVirtualNetwork1-myVirtualNetwork2|
    |Abonnement| Selecteer uw abonnement.|
    |Virtueel netwerk|myVirtualNetwork2 - selecteer het virtuele netwerk *myVirtualNetwork2* door **Virtueel netwerk** en vervolgens **myVirtualNetwork2** te selecteren.|

    ![Peering-instellingen](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    De **PEERINGSTATUS** is *Gestart*, zoals wordt weergegeven in de volgende afbeelding:

    ![Peeringstatus](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Als u de status niet ziet, vernieuwt u de browser.

4. Begin in het vak **Zoeken** bovenaan Azure Portal *MyVirtualNetwork2* te typen. Wanneer **myVirtualNetwork2** wordt weergegeven in de zoekresultaten, selecteert u dit.
5. Volg de stappen 2-3 nogmaals, met de volgende wijzigingen, en selecteer daarna **OK**:

    |Instelling|Waarde|
    |---|---|
    |Naam|myVirtualNetwork2-myVirtualNetwork1|
    |Virtueel netwerk|myVirtualNetwork1|

    De **PEERINGSTATUS** is *Verbonden*. Azure heeft ook de peeringstatus voor de peering *myVirtualNetwork2-myVirtualNetwork1*  gewijzigd van *Gestart* naar *Verbonden*. De virtueel-netwerkpeering is pas volledig tot stand gebracht als de peeringstatus van beide virtuele netwerken *Verbonden* is. 

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak een VM in elk virtueel netwerk, zodat u er in een latere stap tussen kunt communiceren.

### <a name="create-the-first-vm"></a>De eerste VM maken

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Selecteer **Compute** en vervolgens **Windows Server 2016 Datacenter**. U kunt een ander besturingssysteem selecteren, maar in de overige stappen wordt ervan uitgegaan dat u **Windows Server 2016 Datacenter** hebt geselecteerd. 
3. Voer de volgende informatie voor **Basisinformatie** in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer vervolgens **Maken**:

    |Instelling|Waarde|
    |---|---|
    |Naam|myVm1|
    |Gebruikersnaam| Voer een gebruikersnaam naar keuze in.|
    |Wachtwoord| Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Resourcegroep| Selecteer **Bestaande gebruiken** en selecteer **myResourceGroup**.|
    |Locatie| Selecteer **VS Oost**.|
4. Selecteer een VM-grootte onder **Kies een grootte**.
5. Selecteer de volgende waarden voor **Instellingen** en selecteer **OK**:
    |Instelling|Waarde|
    |---|---|
    |Virtueel netwerk| myVirtualNetwork1 - als dit nog niet is geselecteerd, selecteert u **Virtueel netwerk** en vervolgens **myVirtualNetwork1** onder **Virtueel netwerk kiezen**.|
    |Subnet| Subnet1 - als dit nog niet is geselecteerd, selecteert u **Subnet** en vervolgens **Subnet1** onder **Subnet kiezen**.|
    
    ![Instellingen voor virtuele machines](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. Selecteer onder **Maken** in het **Overzicht** **Maken** om de implementatie van de VM te starten.

### <a name="create-the-second-vm"></a>De tweede VM maken

Volg de stappen 1-6 nogmaals, met de volgende wijzigingen:

|Instelling|Waarde|
|---|---|
|Naam | myVm2|
|Virtueel netwerk | myVirtualNetwork2|

Het maken van de VM's duurt enkele minuten. Ga niet verder met de overige stappen voordat beide VM's zijn gemaakt.

## <a name="communicate-between-vms"></a>Communicatie tussen virtuele machines

1. Begin in het vak *Zoeken* bovenaan de portal *myVm1* te typen. Wanneer **myVm1** wordt weergegeven in de zoekresultaten, selecteert u deze.
2. Maak een externe bureaubladverbinding met *myVm1*  door **Verbinding maken** te kiezen, zoals in de volgende afbeelding:

    ![Verbinding maken met de virtuele machine](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Open het gedownloade RDP-bestand om verbinding te maken met de VM. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.
4. Voer de gebruikersnaam en het wachtwoord in die u bij het maken van de VM hebt opgegeven (mogelijk moet u **Meer opties** en **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de virtuele machine) en selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Selecteer **Ja** om door te gaan met de verbinding.
6. In een latere stap wordt ping gebruikt om te communiceren met *myVm2* vanaf *myVm1*. Ping maakt gebruik van ICMP (Internet Control Message Protocol), wat standaard door Windows Firewall wordt geweigerd. Schakel op *myVm1* ICMP via Windows Firewall in, zodat u deze VM in een latere stap kunt pingen vanaf *myVm2* met behulp van PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```
    
    Hoewel ping wordt gebruikt voor communicatie tussen VM's in deze zelfstudie, wordt niet aanbevolen ICMP via Windows Firewall toe te staan voor productie-implementaties.

7. Maak verbinding met *myVm2* door op *myVm1* de volgende opdracht in te voeren vanaf een opdrachtprompt:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Aangezien u ping hebt ingeschakeld op *myVm1*, kunt u deze nu pingen met het IP-adres:

    ```
    ping 10.0.0.4
    ```
    
9. Verbreek de RDP-sessies met zowel *myVm1* als *myVm2*.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet langer nodig hebt, verwijdert u de resourcegroep en alle resources die deze bevat: 

1. Voer *myResourceGroup* in het vak **Zoeken** bovenaan de portal in. Wanneer u **myResourceGroup** ziet in de zoekresultaten, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Voer *myResourceGroup* in voor **TYP DE RESOURCEGROEPNAAM:** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u twee netwerken in dezelfde Azure-regio kunt verbinden met virtueel-netwerkpeering. U kunt ook virtuele netwerken in verschillende [ondersteunde regio's](virtual-network-manage-peering.md#cross-region) en in [ verschillende Azure-abonnementen ](create-peering-different-subscriptions.md#portal) 'peeren', en peering gebruiken om [netwerkontwerpen met een stertopologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) te maken. Zie voor meer informatie over virtueel-netwerkpeering [Peering op virtueel netwerk](virtual-network-peering-overview.md) en [Virtueel-netwerkpeerings beheren](virtual-network-manage-peering.md).

Als u uw eigen computer met een virtueel netwerk wilt verbinden via een VPN-verbinding, en communiceren met resources in een virtueel netwerk of in virtuele netwerken met peering, raadpleegt u [Uw computer verbinden met een virtueel netwerk](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
