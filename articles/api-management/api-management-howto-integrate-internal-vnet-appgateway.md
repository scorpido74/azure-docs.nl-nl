---
title: API-beheer gebruiken in virtueel netwerk met toepassingsgateway
titleSuffix: Azure API Management
description: Meer informatie over het instellen en configureren van Azure API Management in intern virtueel netwerk met Application Gateway (WAF) als FrontEnd
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: sasolank
ms.openlocfilehash: 2b8cf66afa1d8aa592d5755ebab70cd6ad2e75fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298050"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>API-beheer integreren in een interne VNET met Application Gateway

## <a name="overview"></a><a name="overview"> </a> Overzicht

De API Management-service kan worden geconfigureerd in een virtueel netwerk in interne modus, waardoor deze alleen toegankelijk is vanuit het virtuele netwerk. Azure Application Gateway is een PAAS-service, die een Layer-7 load balancer biedt. Het fungeert als een reverse-proxy service en biedt onder haar het aanbieden van een Web Application Firewall (WAF).

Door API Management te combineren in een intern VNET met de Frontend van de Application Gateway, worden de volgende scenario's mogelijk:

* Gebruik dezelfde API Management-bron voor consumptie door zowel interne consumenten als externe consumenten.
* Gebruik één API Management-bron en een subset van API's die zijn gedefinieerd in API-beheer beschikbaar voor externe consumenten.
* Bied een turn-key manier om toegang tot API Management over te schakelen vanaf het openbare internet in en uit.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u de in dit artikel beschreven stappen wilt volgen, moet u het volgende hebben:

* Een actief Azure-abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certificaten - pfx en cer voor de API hostname en pfx voor de hostname van de ontwikkelaarsportal.

## <a name="scenario"></a><a name="scenario"> </a> Scenario

Dit artikel gaat over het gebruik van één API Management-service voor zowel interne als externe consumenten en ervoor zorgen dat deze fungeert als één frontend voor zowel on-premises als cloud-API's. U ziet ook hoe u alleen een subset van uw API's (in het voorbeeld waarin ze in het groen zijn gemarkeerd) blootstellen aan extern verbruik met behulp van routeringsfunctionaliteit die beschikbaar is in Application Gateway.

In het eerste installatievoorbeeld worden al uw API's alleen beheerd vanuit uw virtuele netwerk. Interne consumenten (oranje gemarkeerd) hebben toegang tot al uw interne en externe API's. Verkeer gaat nooit uit naar het internet. Krachtige connectiviteit wordt geleverd via Express Route-circuits.

![url-route](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a><a name="before-you-begin"> </a> Voordat u begint

* Zorg ervoor dat u de nieuwste versie van Azure PowerShell gebruikt. Zie de installatie-instructies bij [Installeer Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Wat is er nodig om een integratie te maken tussen API Management en Application Gateway?

* **Back-endservergroep:** Dit is het interne virtuele IP-adres van de API Management-service.
* **Instellingen voor back-endservergroep:** Elke groep heeft instellingen zoals poort, protocol en cookie-gebaseerde affiniteit. Deze instellingen worden toegepast op alle servers in de groep.
* **Front-end poort:** Dit is de openbare poort die wordt geopend op de toepassingsgateway. Verkeer raken wordt doorgestuurd naar een van de back-end servers.
* **Luisteraar:** De listener heeft een front-end poort, een protocol (Http of Https, deze waarden zijn hoofdlettergevoelig) en de naam van het SSL-certificaat (als ssl-offload wordt geconfigureerd).
* **Regel:** De regel bindt een listener aan een back-endservergroep.
* **Aangepaste statussonde:** Application Gateway gebruikt standaard op IP-adres gebaseerde sondes om erachter te komen welke servers in de BackendAddressPool actief zijn. De API Management-service reageert alleen op aanvragen met de juiste hostheader, vandaar dat de standaardsondes mislukken. Er moet een aangepaste statussonde worden gedefinieerd om de toepassingsgateway te helpen bepalen of de service in leven is en moet aanvragen doorsturen.
* **Aangepaste domeincertificaten:** Als u apibeheer vanaf internet wilt openen, moet u een CNAME-toewijzing van de hostnaam maken naar de front-end DNS-naam van de Application Gateway. Dit zorgt ervoor dat de hostname header en certificaat verzonden naar Application Gateway die wordt doorgestuurd naar API Management is een APIM kan herkennen als geldig. In dit voorbeeld gebruiken we twee certificaten - voor de backend en voor de ontwikkelaarsportal.  

## <a name="steps-required-for-integrating-api-management-and-application-gateway"></a><a name="overview-steps"> </a> Stappen vereist voor de integratie van API-beheer en application gateway

1. Maak een resourcegroep voor Resource Manager.
2. Maak een virtueel netwerk, subnet en openbaar IP voor de Toepassingsgateway. Maak een ander subnet voor API-beheer.
3. Maak een API Management-service in het bovenstaande VNET-subnet en zorg ervoor dat u de interne modus gebruikt.
4. Stel een aangepaste domeinnaam in de API-beheerservice in.
5. Maak een configuratieobject Application Gateway.
6. Maak een application gateway-bron.
7. Maak een CNAME van de openbare DNS-naam van de Toepassingsgateway naar de hostnaam API Management proxy.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>De ontwikkelaarsportal extern blootstellen via Application Gateway

In deze gids zullen we ook de **ontwikkelaarsportal** blootstellen aan externe doelgroepen via de Application Gateway. Het vereist extra stappen om de listener, sonde, instellingen en regels van de ontwikkelaarsportal te maken. Alle details worden verstrekt in de respectievelijke stappen.

> [!WARNING]
> Als u Azure AD of verificatie door derden gebruikt, schakelt u de functie [sessieaffiniteit op basis van cookies](../application-gateway/features.md#session-affinity) in Application Gateway in.

> [!WARNING]
> Om te voorkomen dat Application Gateway WAF de download van OpenAPI-specificatie `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"`in de ontwikkelaarsportal breekt, moet u de firewallregel uitschakelen.

## <a name="create-a-resource-group-for-resource-manager"></a>Een resourcegroep maken voor Resource Manager

### <a name="step-1"></a>Stap 1

Meld u aan bij Azure.

```powershell
Connect-AzAccount
```

Authenticeer met uw referenties.

### <a name="step-2"></a>Stap 2

Selecteer het gewenste abonnement.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>Stap 3

Maak een resourcegroep (u kunt deze stap overslaan als u een bestaande resourcegroep gebruikt).

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Azure Resource Manager vereist dat er voor alle resourcegroepen een locatie wordt opgegeven. Deze locatie wordt gebruikt als de standaardlocatie voor resources in die resourcegroep. Zorg ervoor dat alle opdrachten voor het maken van een toepassingsgateway dezelfde brongroep gebruiken.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Een virtueel netwerk en een subnet voor de toepassingsgateway maken

In het volgende voorbeeld ziet u hoe u een virtueel netwerk maakt met Resource Manager.

### <a name="step-1"></a>Stap 1

Wijs het adresbereik 10.0.0.0/24 toe aan de subnetvariabele die moet worden gebruikt voor Application Gateway terwijl u een virtueel netwerk maakt.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Stap 2

Wijs het adresbereik 10.0.1.0/24 toe aan de subnetvariabele die moet worden gebruikt voor API-beheer tijdens het maken van een virtueel netwerk.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Stap 3

Maak een virtueel netwerk met de naam **appgwvnet** in resourcegroep **apim-appGw-RG** voor de regio West-VS. Gebruik het voorvoegsel 10.0.0.0/16 met subnetten 10.0.0.0/24 en 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Stap 4

Een subnetvariabele toewijzen voor de volgende stappen

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Een API-beheerservice maken in een VNET-configuratie in de interne modus

In het volgende voorbeeld ziet u hoe u een API Management-service maakt in een VNET die alleen is geconfigureerd voor interne toegang.

### <a name="step-1"></a>Stap 1

Maak een object API Management Virtual Network met het subnet $apimsubnetdata hierboven gemaakt.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Stap 2

Maak een API Management-service binnen het virtuele netwerk.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Nadat de bovenstaande opdracht slaagt, verwijst u naar [DNS-configuratie die vereist is om toegang te krijgen tot de interne VNET API Management-service](api-management-using-with-internal-vnet.md#apim-dns-configuration) om toegang te krijgen tot deze service. Deze stap kan meer dan een half uur duren.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Een aangepaste domeinnaam instellen in API-beheer

> [!IMPORTANT]
> De [nieuwe ontwikkelaarsportal](api-management-howto-developer-portal.md) vereist naast de onderstaande stappen ook connectiviteit met het beheereindpunt van het API-beheer mogelijk maken.

### <a name="step-1"></a>Stap 1

Initialiseer de volgende variabelen met de details van de certificaten met privésleutels voor de domeinen. In dit voorbeeld zullen `api.contoso.net` `portal.contoso.net`we gebruiken en .  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>Stap 2

Maak en stel de configuratieobjecten hostnaam in voor de proxy en voor de portal.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Als u de verouderde ontwikkelaarsportalconnectiviteit `-HostnameType DeveloperPortal` wilt `-HostnameType Portal`configureren die u moet vervangen door .

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Een openbaar IP-adres maken voor de front-endconfiguratie

Maak een openbare IP-bron **publicIP01** in de resourcegroep.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

Er wordt een IP-adres toegewezen aan de toepassingsgateway wanneer de service wordt gestart.

## <a name="create-application-gateway-configuration"></a>Toepassingsgatewayconfiguratie maken

Alle configuratie-items moeten zijn ingesteld voordat u de toepassingsgateway maakt. Volg de onderstaande stappen om de configuratie-items te maken die nodig zijn voor een toepassingsgatewayresource.

### <a name="step-1"></a>Stap 1

Maak een IP-configuratie van de toepassingsgateway met de naam **gatewayIP01**. Wanneer de toepassingsgateway wordt geopend, wordt er een IP-adres opgehaald via het geconfigureerde subnet en wordt het netwerkverkeer omgeleid naar de IP-adressen in de back-end-IP-pool. Onthoud dat elk exemplaar één IP-adres gebruikt.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Stap 2

Configureer de front-end IP-poort voor het openbare IP-eindpunt. Deze poort is de poort waarmee eindgebruikers verbinding maken.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Stap 3

Configureer het front-end-IP-adres met openbaar IP-eindpunt.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Stap 4

Configureer de certificaten voor de Application Gateway, die worden gebruikt om het verkeer dat doorgaat te decoderen en opnieuw te versleutelen.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Stap 5

Maak de HTTP-listeners voor de toepassingsgateway. Wijs de front-end IP-configuratie-, poort- en ssl-certificaten aan hen toe.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Stap 6

Aangepaste sondes maken `ContosoApi` voor het eindpunt van het API Management-proxydomein. Het `/status-0123456789abcdef` pad is een standaardeindpunt voor de status dat wordt gehost op alle API-beheerservices. Stel `api.contoso.net` in als een aangepaste hostname van sondeomslag om deze te beveiligen met SSL-certificaat.

> [!NOTE]
> De hostnaam `contosoapi.azure-api.net` is de standaardnaam van de `contosoapi` proxyhost die is geconfigureerd wanneer een service met de naam wordt gemaakt in het openbare Azure.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Stap 7

Upload het certificaat dat moet worden gebruikt op de backend-bronnen met SSL-functie. Dit is hetzelfde certificaat dat u in stap 4 hierboven hebt opgegeven.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Stap 8

Configureer HTTP-backend-instellingen voor de toepassingsgateway. Dit omvat het instellen van een time-outlimiet voor backend-aanvragen, waarna deze worden geannuleerd. Deze waarde is anders dan de time-out van de sonde.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Stap 9

Configureer een back-end IP-adresgroep met de naam **apimbackend** met het interne virtuele IP-adres van de api-beheerservice die hierboven is gemaakt.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Stap 10

Maak regels voor de toepassingsgateway om basisroutering te gebruiken.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Wijzig het regeltype en de routering om de toegang tot bepaalde pagina's van de ontwikkelaarsportal te beperken.

### <a name="step-11"></a>Stap 11

Configureer het aantal exemplaren en de grootte van de toepassingsgateway. In dit voorbeeld gebruiken we de [WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) voor een betere beveiliging van de API Management-bron.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Stap 12

Configureer WAF om in de "Preventie"-modus te zijn.

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Toepassingsgateway maken

Maak een toepassingsgateway met alle configuratieobjecten uit de voorgaande stappen.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME de API Management proxy hostname voor de openbare DNS-naam van de Application Gateway-bron

Wanneer de gateway is gemaakt, gaat u in de volgende stap de front-end voor communicatie configureren. Bij het gebruik van een openbaar IP-adres vereist Application Gateway een dynamisch toegewezen DNS-naam, die mogelijk niet eenvoudig te gebruiken is.

De DNS-naam van de Application Gateway moet worden gebruikt om een CNAME-record `api.contoso.net` te maken die de naam van de APIM-proxyhost (bijvoorbeeld in de bovenstaande voorbeelden) naar deze DNS-naam verwijst. Als u de IP CNAME-record voor frontend wilt configureren, haalt u de details van de Toepassingsgateway en de bijbehorende IP/DNS-naam op met het element PublicIPAddress. Het gebruik van A-records wordt afgeraden omdat de VIP kan veranderen bij het opnieuw opstarten van gateway.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a><a name="summary"> </a> Overzicht
Azure API Management geconfigureerd in een VNET biedt één gateway-interface voor alle geconfigureerde API's, ongeacht of ze op locatie of in de cloud worden gehost. De integratie van Application Gateway met API Management biedt de flexibiliteit om bepaalde API's selectief toegankelijk te maken op het internet en een Web Application Firewall te bieden als een frontend voor uw API Management-instantie.

## <a name="next-steps"></a><a name="next-steps"> </a> Volgende stappen
* Meer informatie over Azure Application Gateway
  * [Overzicht van toepassingsgateway](../application-gateway/application-gateway-introduction.md)
  * [Application Gateway Web Application Firewall](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Toepassingsgateway met routeroutering op basis van paden](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Meer informatie over API-beheer en VNET's
  * [Api-beheer gebruiken dat alleen beschikbaar is binnen vnet](api-management-using-with-internal-vnet.md)
  * [API-beheer gebruiken in VNET](api-management-using-with-vnet.md)
