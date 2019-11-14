---
title: SSL-offload met behulp van Power shell-Azure-toepassing gateway
description: Dit artikel bevat instructies voor het maken van een toepassings gateway met SSL-offload met behulp van het klassieke Azure-implementatie model
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: c456a0856adb0d36349b5f96ba0ab8bab3eec5c9
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74047921"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Een toepassings gateway configureren voor SSL-offload met behulp van het klassieke implementatie model

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Klassieke Azure Power shell](application-gateway-ssl.md)
> * [Azure CLI](application-gateway-ssl-cli.md)

Azure Application Gateway kan zodanig worden geconfigureerd dat de Secure Sockets Layer-sessie (SSL) wordt beëindigd bij de gateway om kostbare SSL-ontsleutelingstaken te voorkomen die worden uitgevoerd in de webfarm. Met SSL-offload worden ook het instellen van de front-endserver en het beheer van de webtoepassing eenvoudiger.

## <a name="before-you-begin"></a>Voordat u begint

1. Installeer de nieuwste versie van de Azure PowerShell-cmdlets via het webplatforminstallatieprogramma. U kunt de nieuwste versie downloaden en installeren via het gedeelte **Windows PowerShell** op de pagina [Downloads](https://azure.microsoft.com/downloads/).
2. Controleer of u een werkend virtueel netwerk hebt met een geldig subnet. Zorg ervoor dat er geen virtuele machines en cloudimplementaties zijn die gebruikmaken van het subnet. De toepassingsgateway moet afzonderlijk in een subnet van een virtueel netwerk staan.
3. De servers die u configureert voor het gebruik van de toepassings gateway moeten bestaan of hun eind punten hebben die zijn gemaakt in het virtuele netwerk of met een openbaar IP-adres of een virtueel IP-adres (VIP) toegewezen.

Als u SSL-offload wilt configureren op een toepassings gateway, voert u de volgende stappen uit in de aangegeven volg orde:

1. [Een toepassings gateway maken](#create-an-application-gateway)
2. [SSL-certificaten uploaden](#upload-ssl-certificates)
3. [De gateway configureren](#configure-the-gateway)
4. [De gateway configuratie instellen](#set-the-gateway-configuration)
5. [De gateway starten](#start-the-gateway)
6. [De gateway status controleren](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

Als u de gateway wilt maken, voert u de `New-AzureApplicationGateway` cmdlet in, waarbij u de waarden vervangt door uw eigen waarde. Er worden op dat moment nog geen kosten in rekening gebracht voor gebruik van de gateway. De kosten zijn pas vanaf een latere stap van toepassing, wanneer de gateway wordt geopend.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Als u wilt controleren of de gateway is gemaakt, kunt u de `Get-AzureApplicationGateway`-cmdlet opgeven.

In het voor beeld, **Description**, **InstanceCount**en **GatewaySize** zijn optionele para meters. De standaard waarde voor **InstanceCount** is **2**, met een maximale waarde van **10**. De standaard waarde voor **GatewaySize** is **gemiddeld**. Kleine en grote zijn andere beschik bare waarden. **VirtualIPs** en **DnsName** worden als leeg weer gegeven, omdat de gateway nog niet is gestart. Deze waarden worden gemaakt nadat de gateway de status actief heeft.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>SSL-certificaten uploaden

Voer `Add-AzureApplicationGatewaySslCertificate` in om het server certificaat in PFX-indeling te uploaden naar de Application Gateway. De naam van het certificaat is een door de gebruiker gekozen naam en moet uniek zijn binnen de Application Gateway. Naar dit certificaat wordt verwezen met deze naam in alle certificaat beheer bewerkingen in de toepassings gateway.

In het volgende voor beeld wordt de cmdlet weer gegeven. Vervang de waarden in de steek proef door uw eigen voor beeld.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Valideer vervolgens de upload van het certificaat. Voer de `Get-AzureApplicationGatewayCertificate`-cmdlet in.

In het volgende voor beeld wordt de cmdlet op de eerste regel weer gegeven, gevolgd door de uitvoer:

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
> Het certificaat wachtwoord moet tussen 4 en 12 tekens bestaan uit letters of cijfers. Speciale tekens worden niet geaccepteerd.

## <a name="configure-the-gateway"></a>De gateway configureren

Een configuratie van een toepassings gateway bestaat uit meerdere waarden. De waarden kunnen samen worden gekoppeld om de configuratie te maken.

De waarden zijn:

* **Back-end-server groep**: de lijst met IP-adressen van de back-endservers. De vermelde IP-adressen moeten deel uitmaken van het subnet van het virtuele netwerk of moeten een openbaar IP-of VIP-adres zijn.
* **Instellingen voor de back-end-server groep**: elke pool heeft instellingen als poort, protocol en affiniteit op basis van cookies. Deze instellingen zijn gekoppeld aan een pool en worden toegepast op alle servers in de pool.
* **Front-end poort**: deze poort is de open bare poort die wordt geopend op de toepassings gateway. Het verkeer komt binnen via deze poort en wordt vervolgens omgeleid naar een van de back-endservers.
* **Listener**: de listener heeft een front-end-poort, een protocol (http of https; deze waarden zijn hoofdletter gevoelig) en de naam van het SSL-certificaat (bij het configureren van een SSL-offload).
* **Regel**: de regel verbindt de listener en de back-end-server groep en definieert welke back-endserver Server groep moet worden doorgestuurd naar het verkeer wanneer het een bepaalde listener aantreft. Momenteel wordt alleen de regel *basic* ondersteund. De regel *basic* is een vorm van round-robinbelastingverdeling.

**Aanvullende configuratie opmerkingen**

Voor het configureren van SSL-certificaten moet het protocol in **HttpListener** worden gewijzigd in **Https** (hoofdlettergevoelig). Voeg het element **SslCert** toe aan **HttpListener** met de waarde ingesteld op dezelfde naam die wordt gebruikt in de sectie [SSL-certificaten uploaden](#upload-ssl-certificates) . De front-end-poort moet worden bijgewerkt naar **443**.

**Voor het inschakelen van de op cookies gebaseerde affiniteit**: u kunt een toepassings gateway configureren om ervoor te zorgen dat een aanvraag van een client sessie altijd wordt doorgestuurd naar dezelfde virtuele machine in de webfarm. Als u dit wilt doen, voegt u een sessie cookie in waarmee de gateway het verkeer correct kan omleiden. Als u op cookies gebaseerde affiniteit wilt inschakelen, stelt u **CookieBasedAffinity** in op **Enabled** in het element **BackendHttpSettings**.

U kunt uw configuratie samen stellen door een configuratie object te maken of door een XML-configuratie bestand te gebruiken.
Als u de configuratie wilt maken met behulp van een XML-configuratie bestand, voert u het volgende voor beeld in:


```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
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

## <a name="set-the-gateway-configuration"></a>De gateway configuratie instellen

Vervolgens stelt u de toepassingsgateway in. U kunt de cmdlet `Set-AzureApplicationGatewayConfig` invoeren met een configuratie object of een XML-configuratie bestand.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>De gateway openen

Nadat de gateway is geconfigureerd, voert u de `Start-AzureApplicationGateway`-cmdlet in om de gateway te starten. Voor een toepassingsgateway worden pas kosten doorberekend wanneer de gateway is geactiveerd.

> [!NOTE]
> Het kan 15-20 minuten duren voordat de cmdlet `Start-AzureApplicationGateway` is voltooid.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>De gatewaystatus controleren

Voer de `Get-AzureApplicationGateway`-cmdlet in om de status van de gateway te controleren. Als `Start-AzureApplicationGateway` in de vorige stap is geslaagd, moet de **status** worden **uitgevoerd**en moeten de **VirtualIPs** en **DnsName** geldige vermeldingen bevatten.

In dit voor beeld ziet u een toepassings gateway die actief, werkend en klaar is om verkeer te nemen:

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

Zie voor meer informatie over opties voor taak verdeling in het algemeen:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
