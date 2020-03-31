---
title: End-to-end SSL configureren met Azure Application Gateway
description: In dit artikel wordt beschreven hoe u end-to-end SSL configureert met Azure Application Gateway met PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/8/2019
ms.author: victorh
ms.openlocfilehash: 7ba273cddb6cf41872c4db1c34560c104b992787
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72286465"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>End-to-end-SSL configureren door Application Gateway met PowerShell te gebruiken

## <a name="overview"></a>Overzicht

Azure Application Gateway ondersteunt end-to-end versleuteling van verkeer. Application Gateway beëindigt de SSL-verbinding op de toepassingsgateway. De gateway past vervolgens de routeringsregels toe op het verkeer, versleutelt het pakket opnieuw en stuurt het pakket door naar de juiste back-endserver op basis van de gedefinieerde routeringsregels. Reacties van de webserver ondergaan hetzelfde proces terug naar de eindgebruiker.

Application Gateway ondersteunt het definiëren van aangepaste SSL-opties. Het ondersteunt ook het uitschakelen van de volgende protocolversies: **TLSv1.0**, **TLSv1.1**en **TLSv1.2**, evenals het definiëren van welke cipher suites te gebruiken en de volgorde van voorkeur. Zie het overzicht van het [SSL-beleid](application-gateway-SSL-policy-overview.md)voor meer informatie over configureerbare SSL-opties.

> [!NOTE]
> SSL 2.0 en SSL 3.0 zijn standaard uitgeschakeld en kunnen niet worden ingeschakeld. Ze worden als onveilig beschouwd en kunnen niet worden gebruikt met Application Gateway.

![scenarioafbeelding][scenario]

## <a name="scenario"></a>Scenario

In dit scenario leert u hoe u een toepassingsgateway maakt met end-to-end SSL met PowerShell.

Dit scenario zal:

* Maak een resourcegroep met de naam **appgw-rg**.
* Maak een virtueel netwerk met de naam **appgwvnet** met een adresruimte van **10.0.0.0/16**.
* Maak twee subnetten genaamd **appgwsubnet** en **appsubnet**.
* Maak een kleine toepassingsgateway die end-to-end SSL-versleuteling ondersteunt die ssl-protocolversies en ciphersuites beperkt.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om end-to-end SSL te configureren met een toepassingsgateway, is een certificaat vereist voor de gateway en zijn certificaten vereist voor de back-endservers. Het gatewaycertificaat wordt gebruikt om een symmetrische sleutel af te leiden volgens ssl-protocolspecificatie. De symmetrische sleutel wordt vervolgens gebruikt versleutelen en decoderen van het verkeer naar de gateway. Het gatewaycertificaat moet in PFX-formaat (Personal Information Exchange) zijn. Met deze bestandsindeling u de privésleutel exporteren die vereist is door de toepassingsgateway om de versleuteling en decryptie van het verkeer uit te voeren.

Voor end-to-end SSL-versleuteling moet de back-end expliciet worden toegestaan door de toepassingsgateway. Upload het openbare certificaat van de back-endservers naar de toepassingsgateway. Het toevoegen van het certificaat zorgt ervoor dat de toepassingsgateway alleen communiceert met bekende back-end-exemplaren. Dit zorgt verder voor de end-to-end communicatie.

Het configuratieproces wordt beschreven in de volgende secties.

## <a name="create-the-resource-group"></a>De resourcegroep maken

In deze sectie u een resourcegroep maken die de toepassingsgateway bevat.

1. Meld u aan bij uw Azure-account.

   ```powershell
   Connect-AzAccount
   ```

2. Selecteer het abonnement dat u voor dit scenario wilt gebruiken.

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```

3. Maak een resourcegroep. (Sla deze stap over als u een bestaande resourcegroep gebruikt.)

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Een virtueel netwerk en een subnet maken voor de toepassingsgateway

In het volgende voorbeeld wordt een virtueel netwerk en twee subnetten gemaakt. Eén subnet wordt gebruikt om de toepassingsgateway vast te houden. Het andere subnet wordt gebruikt voor de back-ends die de webtoepassing hosten.

1. Wijs een adresbereik toe voor het subnet dat moet worden gebruikt voor de toepassingsgateway.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Subnetten die zijn geconfigureerd voor een toepassingsgateway, moeten op de juiste grootte zijn. Een toepassingsgateway kan worden geconfigureerd voor maximaal 10 exemplaren. Elke instantie neemt één IP-adres van het subnet. Te klein van een subnet kan een negatieve invloed hebben op het uitschalen van een toepassingsgateway.
   >

2. Wijs een adresbereik toe dat moet worden gebruikt voor de groep back-endadres.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. Maak een virtueel netwerk met de subnetten die in de voorgaande stappen zijn gedefinieerd.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. Haal de virtuele netwerkbron- en subnetbronnen op die moeten worden gebruikt in de volgende stappen.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Een openbaar IP-adres maken voor de front-endconfiguratie

Maak een openbare IP-bron die moet worden gebruikt voor de toepassingsgateway. Dit openbare IP-adres wordt gebruikt in een van de volgende stappen.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Application Gateway biedt geen ondersteuning voor het gebruik van een openbaar IP-adres dat is gemaakt met een gedefinieerd domeinlabel. Alleen een openbaar IP-adres met een dynamisch gemaakt domeinlabel wordt ondersteund. Als u een vriendelijke DNS-naam voor de toepassingsgateway nodig hebt, raden we u aan een CNAME-record als alias te gebruiken.

## <a name="create-an-application-gateway-configuration-object"></a>Een configuratieobject voor de toepassingsgateway maken

Alle configuratie-items zijn ingesteld voordat u de toepassingsgateway maakt. Volg de onderstaande stappen om de configuratie-items te maken die nodig zijn voor een toepassingsgatewayresource.

1. Maak een IP-configuratie van de toepassingsgateway. Met deze instelling configureert u welke van de subnetten de toepassingsgateway wordt gebruikt. Wanneer de toepassingsgateway wordt gestart, wordt een IP-adres opgehaald van het geconfigureerde subnet en wordt het netwerkverkeer naar de IP-adressen in de back-end IP-groep getoverd. Onthoud dat elk exemplaar één IP-adres gebruikt.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. Maak een front-end IP-configuratie. Met deze instelling wordt een privé- of openbaar IP-adres toegewezen aan de voorkant van de toepassingsgateway. Met de volgende stap wordt het openbare IP-adres in de vorige stap gekoppeld aan de front-end IP-configuratie.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Configureer de back-end IP-adresgroep met de IP-adressen van de back-endwebservers. Deze IP-adressen zijn de IP-adressen die het netwerkverkeer ontvangen dat afkomstig is van het front-end-IP-eindpunt. Vervang de IP-adressen in het voorbeeld door de IP-adrespunten van uw eigen toepassing.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Een volledig gekwalificeerde domeinnaam (FQDN) is ook een geldige waarde om te gebruiken in plaats van een IP-adres voor de back-end servers. U schakelt het in met de **-BackendFqdns-schakelaar.** 

4. Configureer de front-end IP-poort voor het openbare IP-eindpunt. Deze poort is de poort waarmee eindgebruikers verbinding maken.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Configureer het certificaat voor de toepassingsgateway. Dit certificaat wordt gebruikt om het verkeer op de toepassingsgateway te decoderen en opnieuw te versleutelen.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > In dit voorbeeld wordt het certificaat geconfigureerd dat wordt gebruikt voor de SSL-verbinding. Het certificaat moet in .pfx-formaat zijn en het wachtwoord moet 4 tot 12 tekens zijn.

6. Maak de HTTP-listener voor de toepassingsgateway. Wijs de front-end IP-configuratie, poort en SSL-certificaat toe om te gebruiken.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Upload het certificaat dat moet worden gebruikt op de back-endpoolbronnen met SSL.Upload the certificate to be used on the SSL-enabled back-end pool resources.

   > [!NOTE]
   > De standaardsonde krijgt de openbare sleutel van de *standaard* SSL-binding op het IP-adres van de back-end en vergelijkt de waarde van de openbare sleutel die deze ontvangt met de openbare sleutelwaarde die u hier verstrekt. 
   > 
   > Als u hostheaders en Server name Indication (SNI) op de back-end gebruikt, is de opgehaalde openbare sleutel mogelijk niet de beoogde site waarnaar verkeer stroomt. Als u twijfelt, gaat https://127.0.0.1/ u naar de back-endservers om te bevestigen welk certificaat wordt gebruikt voor de *standaard* SSL-binding. Gebruik de openbare sleutel van dat verzoek in deze sectie. Als u host-headers en SNI op HTTPS-bindingen gebruikt en u geen antwoord https://127.0.0.1/ en certificaat ontvangt van een handmatig browserverzoek tot op de back-endservers, moet u een standaard SSL-binding instellen op de servers. Als u dit niet doet, mislukken sondes en wordt de back-end niet op de witte lijst gewhitelist.

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > Het certificaat in de vorige stap moet de openbare sleutel zijn van het .pfx-certificaat dat op de back-end aanwezig is. Exporteer het certificaat (niet het rootcertificaat) dat is geïnstalleerd op de back-endserver in de CER-indeling claim, bewijs en redeneren en gebruik het in deze stap. Met deze stap wordt de back-end met de toepassingsgateway op de witte lijst gezet.

   Als u de Application Gateway v2 SKU gebruikt, maakt u een vertrouwd rootcertificaat in plaats van een verificatiecertificaat. Zie [Overzicht van ssl met toepassinggateway:](ssl-overview.md#end-to-end-ssl-with-the-v2-sku)

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Configureer de HTTP-instellingen voor de back-end van de toepassingsgateway. Wijs het certificaat dat in de vorige stap is geüpload toe aan de HTTP-instellingen.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   Voer voor de Application Gateway v2 SKU de volgende opdracht in:

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Maak een routeringsregel voor load-balancer die het gedrag van de load balancer configureert. In dit voorbeeld wordt een basisregel voor rondborstjes gemaakt.

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Configureer de exemplaargrootte van de toepassingsgateway. De beschikbare maten zijn **Standard\_Small,** **Standard\_Medium**en Standard **\_Large**.  Voor capaciteit zijn de beschikbare waarden **1** tot **en met 10**.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Een instantietelling van 1 kan worden gekozen voor testdoeleinden. Het is belangrijk om te weten dat het aantal exemplaren onder twee instanties niet onder de SLA valt en daarom niet wordt aanbevolen. Kleine gateways moeten worden gebruikt voor dev-test en niet voor productiedoeleinden.

11. Configureer het SSL-beleid dat moet worden gebruikt op de toepassingsgateway. Application Gateway ondersteunt de mogelijkheid om een minimale versie in te stellen voor SSL-protocolversies.

    De volgende waarden zijn een lijst met protocolversies die kunnen worden gedefinieerd:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    In het volgende voorbeeld wordt de minimale protocolversie **ingesteld** op TLSv1_2 en wordt **TLS\_ECDHE\_ECDSA\_met\_AES\_128\_GCM\_SHA256**, TLS **\_ECDHE\_ECDSA\_met\_AES\_256\_GCM\_SHA384**en TLS **RSA met alleen AES 128 GCM SHA256 en TLS\_RSA\_met\_AES\_128\_GCM\_SHA256** ingesteld.

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>De toepassingsgateway maken

Maak met alle voorgaande stappen de toepassingsgateway. Het maken van de gateway is een proces dat lang duurt om uit te voeren.

Voor V1 SKU gebruik de onderstaande opdracht
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

Voor V2 SKU gebruik de onderstaande opdracht
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>Een nieuw certificaat toepassen als het back-endcertificaat is verlopen

Gebruik deze procedure om een nieuw certificaat toe te passen als het back-endcertificaat is verlopen.

1. Haal de toepassingsgateway op om bij te werken.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Voeg de nieuwe certificaatbron toe uit het .cer-bestand, dat de openbare sleutel van het certificaat bevat en kan ook hetzelfde certificaat zijn dat aan de listener is toegevoegd voor SSL-beëindiging bij de toepassingsgateway.

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. Het nieuwe verificatiecertificaatobject in een variabele verwerken (TypeName: Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayAuthenticationCertificate).

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. Wijs het nieuwe certificaat toe aan de **instelling BackendHttp** en verwijs het door met de variabele $AuthCert. (Geef de HTTP-instellingsnaam op die u wilt wijzigen.)
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. Commit the change into the application gateway and pass the new configuration contained into the $out variable.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>Een ongebruikt verlopen certificaat verwijderen uit HTTP-instellingen

Gebruik deze procedure om een ongebruikt verlopen certificaat uit HTTP-instellingen te verwijderen.

1. Haal de toepassingsgateway op om bij te werken.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Vermeld de naam van het verificatiecertificaat dat u wilt verwijderen.

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. Verwijder het verificatiecertificaat uit een toepassingsgateway.

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. Zet de verandering in.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>SSL-protocolversies beperken op een bestaande toepassingsgateway

De voorgaande stappen hebben u ertoe verbonden een toepassing te maken met end-to-end SSL en bepaalde SSL-protocolversies uit te schakelen. In het volgende voorbeeld wordt bepaalde SSL-beleidsregels op een bestaande toepassingsgateway uitgeschakeld.

1. Haal de toepassingsgateway op om bij te werken.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. Definieer een SSL-beleid. In het volgende voorbeeld zijn **TLSv1.0** en **TLSv1.1** uitgeschakeld en zijn de ciphersuites **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, TLS **\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, en **\_TLSA\_WITH\_AES\_128\_GCM\_SHA256** de enige toegestaan.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. Werk ten slotte de gateway bij. Deze laatste stap is een langlopende taak. Wanneer dit is gedaan, wordt end-to-end SSL geconfigureerd op de toepassingsgateway.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>DNS-naam van een toepassingsgateway opmaken

Nadat de gateway is gemaakt, is de volgende stap het configureren van de front-end voor communicatie. Application Gateway vereist een dynamisch toegewezen DNS-naam bij het gebruik van een openbaar IP-adres, wat niet vriendelijk is. Om ervoor te zorgen dat eindgebruikers de toepassingsgateway kunnen raken, u een CNAME-record gebruiken om naar het openbare eindpunt van de toepassingsgateway te wijzen. Zie [Een aangepaste domeinnaam configureren voor in Azure voor](../cloud-services/cloud-services-custom-domain-name-portal.md)meer informatie. 

Als u een alias wilt configureren, haalt u details van de toepassingsgateway en de bijbehorende IP/DNS-naam op met het **element PublicIPAddress** dat aan de toepassingsgateway is gekoppeld. Gebruik de DNS-naam van de toepassingsgateway om een CNAME-record te maken die de twee webtoepassingen naar deze DNS-naam wijst. We raden het gebruik van A-records niet aan, omdat de VIP kan veranderen bij het opnieuw opstarten van de toepassingsgateway.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Volgende stappen

Zie het overzicht van de firewall van de [webtoepassing](application-gateway-webapplicationfirewall-overview.md)voor meer informatie over het verharden van de beveiliging van uw webtoepassingen met Web Application Firewall via Application Gateway.

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
