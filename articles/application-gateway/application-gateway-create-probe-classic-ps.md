---
title: Een aangepaste sonde maken met PowerShell - Azure Application Gateway
description: Meer informatie over het maken van een aangepaste sonde voor Application Gateway met PowerShell in het klassieke implementatiemodel
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 0ba3e9ae7b5075d1f5457cb2960423ad1c737e94
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312556"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Een aangepaste sonde voor Azure Application Gateway (klassiek) maken met PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

In dit artikel voegt u een aangepaste sonde toe aan een bestaande toepassingsgateway met PowerShell. Aangepaste sondes zijn handig voor toepassingen met een specifieke statuscontrolepagina of voor toepassingen die geen succesvol antwoord bieden op de standaardwebtoepassing.

> [!IMPORTANT]
> Azure heeft twee verschillende implementatiemodellen voor het maken en werken met resources: [Resource Manager en Classic.](../azure-resource-manager/management/deployment-models.md) In dit artikel wordt het implementatiemodel Classic gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken. Lees [meer informatie over het uitvoeren van deze stappen met het Resource Manager-model](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

Ga als volgt te werk om een toepassingsgateway te maken:

1. Maak een toepassingsgatewayresource.
2. Maak een XML-configuratiebestand of een configuratieobject.
3. Voer de configuratie door voor de zojuist gemaakte toepassingsgatewayresource.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Een toepassingsgatewaybron maken met een aangepaste sonde

Gebruik de cmdlet `New-AzureApplicationGateway` en vervang de waarden door uw eigen waarden om een gateway te maken. Er worden op dat moment nog geen kosten in rekening gebracht voor gebruik van de gateway. De kosten zijn pas vanaf een latere stap van toepassing, wanneer de gateway wordt geopend.

In het volgende voorbeeld wordt een toepassingsgateway gemaakt met een virtueel netwerk met de naam testvnet1 en een subnet met de naam subnet-1.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Gebruik de cmdlet `Get-AzureApplicationGateway` om te controleren of de gateway is gemaakt.

```powershell
Get-AzureApplicationGateway AppGwTest
```

> [!NOTE]
> De standaardwaarde voor *InstanceCount* is 2 en de maximale waarde is 10. De standaardwaarde voor *GatewaySize* is Medium. U kiezen tussen klein, gemiddeld en groot.
> 
> 

*VirtualIPs* en *DnsName* zijn leeg, omdat de gateway nog niet is geopend. Deze waarden worden gemaakt zodra de gateway in de loopstatus is.

### <a name="configure-an-application-gateway-by-using-xml"></a>Een toepassingsgateway configureren met BEHULP van XML

In het volgende voorbeeld gebruikt u een XML-bestand om alle instellingen voor de toepassingsgateway te configureren en deze door te voeren voor de toepassingsgatewayresource.  

Kopieer de volgende tekst naar Kladblok.

```xml
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
<FrontendIPConfigurations>
    <FrontendIPConfiguration>
        <Name>fip1</Name>
        <Type>Private</Type>
    </FrontendIPConfiguration>
</FrontendIPConfigurations>
<FrontendPorts>
    <FrontendPort>
        <Name>port1</Name>
        <Port>80</Port>
    </FrontendPort>
</FrontendPorts>
<Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
    </Probes>
    <BackendAddressPools>
    <BackendAddressPool>
        <Name>pool1</Name>
        <IPAddresses>
            <IPAddress>1.1.1.1</IPAddress>
            <IPAddress>2.2.2.2</IPAddress>
        </IPAddresses>
    </BackendAddressPool>
</BackendAddressPools>
<BackendHttpSettingsList>
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
</BackendHttpSettingsList>
<HttpListeners>
    <HttpListener>
        <Name>listener1</Name>
        <FrontendIP>fip1</FrontendIP>
    <FrontendPort>port1</FrontendPort>
        <Protocol>Http</Protocol>
    </HttpListener>
</HttpListeners>
<HttpLoadBalancingRules>
    <HttpLoadBalancingRule>
        <Name>lbrule1</Name>
        <Type>basic</Type>
        <BackendHttpSettings>setting1</BackendHttpSettings>
        <Listener>listener1</Listener>
        <BackendAddressPool>pool1</BackendAddressPool>
    </HttpLoadBalancingRule>
</HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

Bewerk de waarden tussen de haakjes voor de configuratie-items. Sla het bestand op met de bestandsextensie .xml.

In het volgende voorbeeld ziet u hoe u een configuratiebestand gebruikt om de toepassingsgateway in te stellen om http-verkeer in balans op openbare poort 80 te laden en netwerkverkeer naar back-endpoort 80 tussen twee IP-adressen te verzenden met behulp van een aangepaste sonde.

> [!IMPORTANT]
> Het protocolitem Http of Https is hoofdlettergevoelig.

Er wordt \<een\> nieuwe configuratie-item Probe toegevoegd om aangepaste sondes te configureren.

De configuratieparameters zijn:

|Parameter|Beschrijving|
|---|---|
|**Naam** |Referentienaam voor aangepaste sonde. |
| **Protocol** | Protocol gebruikt (mogelijke waarden zijn HTTP of HTTPS).|
| **Gastheer** en **pad** | Voltooi het URL-pad dat wordt aangeroepen door de toepassingsgateway om de status van de instantie te bepalen. Als u bijvoorbeeld een website\/http: /contoso.com/ hebt, kan de aangepaste\/sonde worden geconfigureerd voor 'http: /contoso.com/path/custompath.htm' voor sondecontroles om een succesvol HTTP-antwoord te hebben.|
| **Interval** | Hiermee configureert u de intervalcontroles van de sonde in seconden.|
| **Timeout** | Hiermee definieert u de time-out van de sonde voor een HTTP-antwoordcontrole.|
| **Ongezonde Drempel** | Het aantal mislukte HTTP-antwoorden dat nodig is om de back-endinstantie als *niet-inord te markeren.*|

Naar de naam van \<de sonde wordt verwezen in de configuratie BackendHttpSettings\> om toe te wijzen welke back-endpool aangepaste sonde-instellingen gebruikt.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Een aangepaste sonde toevoegen aan een bestaande toepassingsgateway

Het wijzigen van de huidige configuratie van een toepassingsgateway vereist drie stappen: Het huidige XML-configuratiebestand downloaden, wijzigen om een aangepaste sonde te hebben en de toepassingsgateway configureren met de nieuwe XML-instellingen.

1. Download het XML-bestand met behulp van `Get-AzureApplicationGatewayConfig`. Met deze cmdlet wordt de configuratie-XML geëxporteerd die moet worden gewijzigd om een sondeinstelling toe te voegen.

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. Open het XML-bestand in een teksteditor. Voeg `<probe>` een `<frontendport>`sectie toe na .

   ```xml
   <Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
   </Probes>
   ```

   Voeg in de sectie backendHttpSettings van de XML de naam van de sonde toe zoals in het volgende voorbeeld:

   ```xml
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
   ```

   Sla het XML-bestand op.

1. Werk de configuratie van de toepassingsgateway `Set-AzureApplicationGatewayConfig`bij met het nieuwe XML-bestand met behulp van . Deze cmdlet werkt uw toepassingsgateway bij met de nieuwe configuratie.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Volgende stappen

Zie [Een toepassingsgateway configureren voor TLS-offload](application-gateway-ssl.md)als u TLS (Tls), voorheen bekend als Secure Sockets Layer (SSL).

Als u een toepassingsgateway wilt configureren voor gebruik met een interne load balancer, raadpleegt u [Create an application gateway with an internal load balancer (ILB)](application-gateway-ilb.md) (Een toepassingsgateway met een interne load balancer (ILB) maken).

