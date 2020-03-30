---
title: SSL-offload met PowerShell - Azure Application Gateway
description: In dit artikel vindt u instructies voor het maken van een toepassingsgateway met SSL-offload met behulp van het klassieke Azure-implementatiemodel
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: c456a0856adb0d36349b5f96ba0ab8bab3eec5c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74047921"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Een toepassingsgateway configureren voor SSL-offload met behulp van het klassieke implementatiemodel

> [!div class="op_single_selector"]
> * [Azure-portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Azure-klassieker PowerShell](application-gateway-ssl.md)
> * [Azure-CLI](application-gateway-ssl-cli.md)

Azure Application Gateway kan zodanig worden geconfigureerd dat de Secure Sockets Layer-sessie (SSL) wordt beëindigd bij de gateway om kostbare SSL-ontsleutelingstaken te voorkomen die worden uitgevoerd in de webfarm. Met SSL-offload worden ook het instellen van de front-endserver en het beheer van de webtoepassing eenvoudiger.

## <a name="before-you-begin"></a>Voordat u begint

1. Installeer de nieuwste versie van de Azure PowerShell-cmdlets via het webplatforminstallatieprogramma. U kunt de nieuwste versie downloaden en installeren via het gedeelte **Windows PowerShell** op de pagina [Downloads](https://azure.microsoft.com/downloads/).
2. Controleer of u een werkend virtueel netwerk hebt met een geldig subnet. Zorg ervoor dat er geen virtuele machines en cloudimplementaties zijn die gebruikmaken van het subnet. De toepassingsgateway moet afzonderlijk in een subnet van een virtueel netwerk staan.
3. De servers die u configureert om de toepassingsgateway te gebruiken, moeten bestaan of hun eindpunten hebben die zijn gemaakt in het virtuele netwerk of met een openbaar IP-adres of een vip-adres (Virtual IP-adres).

Voer de volgende stappen in de vermelde volgorde uit om SSL-offload op een toepassingsgateway te configureren:

1. [Een toepassingsgateway maken](#create-an-application-gateway)
2. [SSL-certificaten uploaden](#upload-ssl-certificates)
3. [De gateway configureren](#configure-the-gateway)
4. [De gatewayconfiguratie instellen](#set-the-gateway-configuration)
5. [De gateway openen](#start-the-gateway)
6. [De gatewaystatus controleren](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

Als u de gateway `New-AzureApplicationGateway` wilt maken, voert u de cmdlet in en vervangt u de waarden door die van u. Er worden op dat moment nog geen kosten in rekening gebracht voor gebruik van de gateway. De kosten zijn pas vanaf een latere stap van toepassing, wanneer de gateway wordt geopend.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Als u wilt valideren of de `Get-AzureApplicationGateway` gateway is gemaakt, u de cmdlet invoeren.

In het voorbeeld zijn **Beschrijving**, **InstanceCount**en **GatewaySize** optionele parameters. De standaardwaarde voor **InstanceCount** is **2**, met een maximale waarde van **10**. De standaardwaarde voor **GatewaySize** is **Gemiddeld**. Klein en groot zijn andere beschikbare waarden. **VirtualIP's** en **DnsName** worden weergegeven als leeg, omdat de gateway nog niet is gestart. Deze waarden worden gemaakt nadat de gateway in de loopstatus is.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>SSL-certificaten uploaden

Voer `Add-AzureApplicationGatewaySslCertificate` deze in om het servercertificaat in PFX-indeling te uploaden naar de toepassingsgateway. De certificaatnaam is een door de gebruiker gekozen naam en moet uniek zijn binnen de toepassingsgateway. Dit certificaat wordt met deze naam aangeduid in alle certificaatbeheerbewerkingen op de toepassingsgateway.

Het volgende monster toont de cmdlet. Vervang de waarden in het voorbeeld door die van u.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Valideer vervolgens het uploaden van het certificaat. Voer `Get-AzureApplicationGatewayCertificate` de cmdlet in.

In het volgende monster wordt de cmdlet op de eerste regel weergegeven, gevolgd door de uitvoer:

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
> Het certificaatwachtwoord moet tussen de 4 en 12 tekens zijn die uit letters of cijfers bestaan. Speciale tekens worden niet geaccepteerd.

## <a name="configure-the-gateway"></a>De gateway configureren

Een configuratie van de toepassingsgateway bestaat uit meerdere waarden. De waarden kunnen aan elkaar worden gekoppeld om de configuratie te construeren.

De waarden zijn:

* **Back-end serverpool:** de lijst met IP-adressen van de back-endservers. De vermelde IP-adressen moeten deel uitmaken van het virtuele netwerksubnet of een openbaar IP- of VIP-adres zijn.
* **Back-end serverpoolinstellingen:** elke groep heeft instellingen zoals poort-, protocol- en cookiegebaseerde affiniteit. Deze instellingen zijn gekoppeld aan een pool en worden toegepast op alle servers in de pool.
* **Front-end poort**: Deze poort is de openbare poort die wordt geopend op de toepassingsgateway. Het verkeer komt binnen via deze poort en wordt vervolgens omgeleid naar een van de back-endservers.
* **Listener**: De listener heeft een front-endpoort, een protocol (Http of Https; deze waarden zijn hoofdlettergevoelig) en de naam van het SSL-certificaat (als u een SSL-offload configureert).
* **Regel:** De regel bindt de listener en de back-end servergroep en definieert naar welke back-endservergroep het verkeer moet worden gericht wanneer het een bepaalde listener raakt. Momenteel wordt alleen de regel *basic* ondersteund. De regel *basic* is een vorm van round-robinbelastingverdeling.

**Aanvullende configuratieopmerkingen**

Voor het configureren van SSL-certificaten moet het protocol in **HttpListener** worden gewijzigd in **Https** (hoofdlettergevoelig). Voeg het element **SslCert** toe aan **HttpListener** met de waarde die is ingesteld op dezelfde naam die wordt gebruikt in de sectie [SSL-certificaten uploaden.](#upload-ssl-certificates) De front-end poort moet worden bijgewerkt tot **443**.

**Affiniteit op basis van cookies inschakelen:** U een toepassingsgateway configureren om ervoor te zorgen dat een aanvraag van een clientsessie altijd naar dezelfde VM in de webfarm wordt geleid. Om dit te bereiken, voegt u een sessiecookie in waarmee de gateway verkeer op de juiste manier kan leiden. Als u op cookies gebaseerde affiniteit wilt inschakelen, stelt u **CookieBasedAffinity** in op **Enabled** in het element **BackendHttpSettings**.

U uw configuratie samenstellen door een configuratieobject te maken of door een XML-configuratiebestand te gebruiken.
Voer het volgende voorbeeld in om uw configuratie te construeren met behulp van een XML-configuratiebestand:


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

## <a name="set-the-gateway-configuration"></a>De gatewayconfiguratie instellen

Vervolgens stelt u de toepassingsgateway in. U `Set-AzureApplicationGatewayConfig` de cmdlet invoeren met een configuratieobject of een XML-configuratiebestand.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>De gateway openen

Nadat de gateway is geconfigureerd, `Start-AzureApplicationGateway` voert u de cmdlet in om de gateway te starten. Voor een toepassingsgateway worden pas kosten doorberekend wanneer de gateway is geactiveerd.

> [!NOTE]
> De `Start-AzureApplicationGateway` cmdlet kan 15-20 minuten duren om te voltooien.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>De gatewaystatus controleren

Voer `Get-AzureApplicationGateway` de cmdlet in om de status van de gateway te controleren. Als `Start-AzureApplicationGateway` dit in de vorige stap is gelukt, moet de **status** **worden uitgevoerd**en moeten de **VirtualIP's** en **DnsName** geldige vermeldingen hebben.

In dit voorbeeld wordt een toepassingsgateway weergegeven die up, running en klaar is om verkeer te verwerken:

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

Zie voor meer informatie over opties voor het balanceren van de lasten in het algemeen:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure-beheer](https://azure.microsoft.com/documentation/services/traffic-manager/)
