---
title: Configureren van SSL-offload - Azure Application Gateway - PowerShell-klassiek | Microsoft Docs
description: Dit artikel bevat instructies voor het maken van een toepassingsgateway met SSL-offload met behulp van het klassieke implementatiemodel Azure
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 63f28d96-9c47-410e-97dd-f5ca1ad1b8a4
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: victorh
ms.openlocfilehash: e620730b86d648c1ac9db7a9e6faa7a2d206b46e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2018
ms.locfileid: "33205377"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Een toepassingsgateway voor SSL-offload configureren met behulp van het klassieke implementatiemodel

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Azure classic PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Azure Application Gateway kan zodanig worden geconfigureerd dat de Secure Sockets Layer-sessie (SSL) wordt beëindigd bij de gateway om kostbare SSL-ontsleutelingstaken te voorkomen die worden uitgevoerd in de webfarm. Met SSL-offload worden ook het instellen van de front-endserver en het beheer van de webtoepassing eenvoudiger.

## <a name="before-you-begin"></a>Voordat u begint

1. Installeer de nieuwste versie van de Azure PowerShell-cmdlets via het webplatforminstallatieprogramma. U kunt de nieuwste versie downloaden en installeren via het gedeelte **Windows PowerShell** op de pagina [Downloads](https://azure.microsoft.com/downloads/).
2. Controleer of u een werkend virtueel netwerk hebt met een geldig subnet. Zorg ervoor dat er geen virtuele machines en cloudimplementaties zijn die gebruikmaken van het subnet. De toepassingsgateway moet afzonderlijk in een subnet van een virtueel netwerk staan.
3. De servers die u configureert voor het gebruik van de toepassingsgateway moeten bestaan of hebben hun eindpunten die zijn gemaakt in het virtuele netwerk of met een openbaar IP-adres of een virtueel IP-adres (VIP) toegewezen.

SSL-offload configureren op een application gateway, moet u de volgende stappen in de volgorde uitvoeren:

1. [Een toepassingsgateway maken](#create-an-application-gateway)
2. [SSL-certificaten uploaden](#upload-ssl-certificates)
3. [De gateway configureren](#configure-the-gateway)
4. [Configuratie van de gateway instellen](#set-the-gateway-configuration)
5. [Start de gateway](#start-the-gateway)
6. [De gatewaystatus controleren](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

Voer voor het maken van de gateway, de `New-AzureApplicationGateway` cmdlet, waarbij de waarden vervangt door uw eigen. Er worden op dat moment nog geen kosten in rekening gebracht voor gebruik van de gateway. De kosten zijn pas vanaf een latere stap van toepassing, wanneer de gateway wordt geopend.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Valideren dat de gateway is gemaakt, kunt u de `Get-AzureApplicationGateway` cmdlet.

In het voorbeeld **beschrijving**, **InstanceCount**, en **GatewaySize** zijn optionele parameters. De standaardwaarde voor **InstanceCount** is **2**, met een maximale waarde van **10**. De standaardwaarde voor **GatewaySize** is **gemiddeld**. Kleine en grote andere beschikbare waarden zijn. **VirtualIPs** en **DnsName** als niets wordt opgegeven, worden weergegeven omdat de gateway is nog niet gestart. Deze waarden worden gemaakt nadat de gateway uitgevoerd wordt.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>SSL-certificaten uploaden

Voer `Add-AzureApplicationGatewaySslCertificate` voor het uploaden van het servercertificaat in PFX-indeling voor de toepassingsgateway. Naam van het certificaat is de naam van een gebruiker zijn gekozen en moet uniek zijn binnen de toepassingsgateway. Dit certificaat wordt verwezen door deze naam in alle beheerbewerkingen van certificaat in de toepassingsgateway.

Het volgende voorbeeld ziet u de cmdlet. Vervang de waarden in de steekproef die door uw eigen.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Het uploaden van het certificaat vervolgens valideren. Voer de `Get-AzureApplicationGatewayCertificate` cmdlet.

Het volgende voorbeeld ziet u de cmdlet op de eerste regel weergegeven, gevolgd door de uitvoer:

```powershell
Get-AzureApplicationGatewaySslCertificate AppGwTest
```

```
VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
Name           : SslCert
SubjectName    : CN=gwcert.app.test.contoso.com
Thumbprint     : AF5ADD77E160A01A6......EE48D1A
ThumbprintAlgo : sha1RSA
State..........: Provisioned
```

> [!NOTE]
> Wachtwoord voor het certificaat moet tussen 4-12 tekens bestaan uit letters of cijfers zijn. Speciale tekens worden niet geaccepteerd.

## <a name="configure-the-gateway"></a>De gateway configureren

Een toepassingsgateway configuratie bestaat uit meerdere waarden. De waarden kunnen worden gekoppeld samen te stellen de configuratie.

De waarden zijn:

* **Back-endserverpool**: de lijst met IP-adressen van de back-endservers. De IP-adressen die worden vermeld moeten deel uitmaken van het subnet van het virtuele netwerk of een openbaar IP- of VIP-adres moeten worden.
* **Back-endserverpoolinstellingen**: elke pool heeft instellingen, zoals poort, het protocol en cookies gebaseerde affiniteit. Deze instellingen zijn gekoppeld aan een pool en worden toegepast op alle servers in de pool.
* **Front-endpoort**: dit is de openbare poort die in de toepassingsgateway wordt geopend. Het verkeer komt binnen via deze poort en wordt vervolgens omgeleid naar een van de back-endservers.
* **Listener**: de listener beschikt over een front-endpoort, een protocol (Http of Https; deze waarden zijn hoofdlettergevoelig), en de SSL-certificaatnaam (als een SSL-offload configureren).
* **Regel**: de regel verbindt de listener met de back-endserverpool en definieert naar welke back-endserverpool om het verkeer naar wanneer dit bij een bepaalde listener aankomt. Momenteel wordt alleen de regel *basic* ondersteund. De regel *basic* is een vorm van round-robinbelastingverdeling.

**Aanvullende configuratieopmerkingen**

Voor het configureren van SSL-certificaten moet het protocol in **HttpListener** worden gewijzigd in **Https** (hoofdlettergevoelig). Voeg de **SslCert** element op de **HttpListener** met de waarde die is ingesteld op dezelfde naam gebruikt in de [uploaden SSL-certificaten](#upload-ssl-certificates) sectie. De front-endpoort moet worden bijgewerkt naar **443**.

**Inschakelen van cookies gebaseerde affiniteit**: U kunt een toepassingsgateway om ervoor te zorgen dat een aanvraag van een clientsessie altijd wordt omgeleid naar dezelfde virtuele machine in de webfarm configureren. Voeg hiervoor een sessiecookie waarmee de gateway om verkeer te regelen op de juiste wijze. Als u op cookies gebaseerde affiniteit wilt inschakelen, stelt u **CookieBasedAffinity** in op **Enabled** in het element **BackendHttpSettings**.

U kunt de configuratie maken door het maken van een configuratieobject of met behulp van een XML-configuratiebestand.
Kan de configuratie met behulp van een XML-configuratiebestand, voer het volgende voorbeeld:


```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations />
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>443</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>BackendPool1</Name>
            <IPAddresses>
                <IPAddress>10.0.0.1</IPAddress>
                <IPAddress>10.0.0.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>BackendSetting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>HTTPListener1</Name>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Https</Protocol>
            <SslCert>GWCert</SslCert>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>HttpLBRule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
            <Listener>HTTPListener1</Listener>
            <BackendAddressPool>BackendPool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

## <a name="set-the-gateway-configuration"></a>Configuratie van de gateway instellen

Vervolgens stelt u de toepassingsgateway in. Kunt u de `Set-AzureApplicationGatewayConfig` cmdlet met een configuration-object of een XML-configuratiebestand.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>De gateway openen

Nadat de gateway is geconfigureerd, voert u de `Start-AzureApplicationGateway` cmdlet starten van de gateway. Voor een toepassingsgateway worden pas kosten doorberekend wanneer de gateway is geactiveerd.

> [!NOTE]
> De `Start-AzureApplicationGateway` cmdlet 15-20 minuten in beslag kan nemen.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>De gatewaystatus controleren

Voer de `Get-AzureApplicationGateway` cmdlet om de status van de gateway te controleren. Als `Start-AzureApplicationGateway` is voltooid in de vorige stap, de **status** moet **met**, en de **VirtualIPs** en **DnsName** moet geldige waarden hebben.

Dit voorbeeld wordt een application gateway die wordt weergegeven, uitgevoerd en gereed voor verkeer:

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
Name          : AppGwTest2
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
VirtualIPs    : {23.96.22.241}
DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net
```

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over taakverdeling opties in het algemeen, Zie:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
