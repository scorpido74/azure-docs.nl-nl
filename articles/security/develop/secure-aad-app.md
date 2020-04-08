---
title: Een beveiligde Azure AD-webtoepassing ontwikkelen | Microsoft Documenten
description: Deze eenvoudige voorbeeld-app implementeert best practices voor beveiliging die uw toepassing en de beveiligingshouding van uw organisatie verbeteren wanneer u zich ontwikkelt op Azure.
keywords: nb
services: security
documentationcenter: na
author: TerryLanfear
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: terrylan
ms.openlocfilehash: 599c4a31840b47294b43c4c4d1f0200b17f04540
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810546"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Veilige app ontwikkelen voor een Azure AD-app
## <a name="overview"></a>Overzicht

Dit voorbeeld is een eenvoudige Azure Active Directory met web-app die wordt gekoppeld aan beveiligingsbronnen voor het ontwikkelen van apps op Azure. De app implementeert best practices voor beveiliging die uw toepassing en de beveiligingshouding van uw organisatie kunnen verbeteren wanneer u apps op Azure ontwikkelt.

De implementatiescripts stellen de infrastructuur in. Nadat u de implementatiescripts hebt uitgevoerd, moet u een handmatige configuratie uitvoeren in de Azure-portal om de componenten en services aan elkaar te koppelen. Dit voorbeeld is gericht op ervaren ontwikkelaars op Azure die binnen de detailhandel werken en een beveiligde Azure Active Directory willen bouwen met beveiligde Azure-infrastructuur. 


Bij het ontwikkelen en implementeren van deze app leert u hoe u 
- Maak een Azure Key Vault-exemplaar, sla er geheimen uit op en haal deze op.
- Implementeer de Azure Web App, die is geïsoleerd met front-end firewalltoegang. 
- Maak en configureer een Azure Application Gateway-instantie met een firewall die Gebruikmaakt van OWASP Top 10 Ruleset. 
- Schakel versleuteling van gegevens in transit en in rust in met behulp van Azure-services. 
- Stel het Azure-beleids- en beveiligingscentrum in om de compliancies te evalueren. 

Nadat u deze app hebt ontwikkeld en geïmplementeerd, hebt u de volgende voorbeeldweb-app ingesteld, samen met de configuratie- en beveiligingsmaatregelen die worden beschreven.

## <a name="architecture"></a>Architectuur
De app is een typische n-tier applicatie met drie lagen. De front-end, back-end en database laag met monitoring en secret-management componenten geïntegreerd worden hier weergegeven:

![App-architectuur](./media/secure-aad-app/architecture.png)

Deze oplossing maakt gebruik van de volgende Azure-services. Details van de implementatiearchitectuur vindt u in de sectie Implementatiearchitectuur. 

De architectuur bestaat uit deze componenten

- [Azure Application Gateway](../../application-gateway/index.yml). Biedt de gateway en firewall voor onze applicatiearchitectuur.
- [Toepassingsinzichten](../../azure-monitor/app/app-insights-overview.md). Biedt een uitbreidbare APM-service (Application Performance Management) op meerdere platforms.
- [Azure Key Vault](../../key-vault/index.yml). Slaat de geheimen van onze app op en versleutelt en beheert het maken van toegangsbeleid eromheen.
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Biedt cloudgebaseerde identiteits- en toegangsbeheerservice, aanmelding sen en toegangsbronnen.
- [Azure Domain Name System](../../dns/dns-overview.md). Bied de service om het domein te hosten.
- [Azure Load Balancer](../../load-balancer/load-balancer-overview.md). Biedt om uw toepassingen te schalen en een hoge beschikbaarheid voor uw services te creëren.
- [Azure Web App](../../app-service/overview.md).  Biedt een OP HTTP-gebaseerde service voor het hosten van webtoepassingen.
- [Azure Security Center](../../security-center/index.yml). biedt geavanceerde bescherming tegen bedreigingen voor uw hybride workloads in de cloud.
- [Azure-beleid](../../governance/policy/overview.md). Biedt een evaluatie van uw resources voor niet-naleving van toegewezen beleidsregels.

## <a name="threat-model"></a>Bedreigingsmodel
Bedreigingsmodellering is het proces van het identificeren van potentiële beveiligingsbedreigingen voor uw bedrijf en toepassing en vervolgens ervoor te zorgen dat er een goed mitigatieplan is.

In dit voorbeeld wordt de [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) gebruikt om bedreigingsmodellering voor de beveiligde voorbeeld-app te implementeren. Door de componenten en de gegevensstromen in kaart te brengen, u problemen en bedreigingen al vroeg in het ontwikkelingsproces identificeren. Tijd en geld wordt later bespaard door dit te gebruiken.

Hier is het bedreigingsmodel voor de voorbeeld-app

![Bedreigingsmodel](./media/secure-aad-app/threat-model.png)

Sommige voorbeeldbedreigingen en potentiële kwetsbaarheden die de hulpprogramma voor bedreigingsmodellering genereert, worden weergegeven in de volgende schermafbeelding. Het dreigingsmodel geeft een overzicht van het blootgestelde aanvalsoppervlak en vraagt de ontwikkelaars na te denken over hoe de problemen kunnen worden beperkt.

![Uitvoer van bedreigingsmodellen](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>Vereisten
Als u de toepassing operationeel wilt krijgen, moet u de volgende hulpprogramma's installeren:

- Een codeeditor om de toepassingscode te wijzigen en te bekijken. [Visual Studio Code](https://code.visualstudio.com/) is een open-source optie.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) op uw ontwikkelcomputer.
- [Git](https://git-scm.com/) op uw systeem. Git wordt gebruikt om de broncode lokaal te klonen.
- [jq](https://stedolan.github.io/jq/), een UNIX-tool voor het op een gebruiksvriendelijke manier opvragen van JSON.

U hebt een Azure-abonnement nodig om de bronnen van de voorbeeld-app te implementeren. Als u geen Azure-abonnement hebt, u [een gratis account maken](https://azure.microsoft.com/free/) om de voorbeeld-app te testen.

Nadat u deze hulpprogramma's hebt geïnstalleerd, u de app op Azure implementeren.

### <a name="implementation-guidance"></a>Uitvoeringsrichtsnoeren
Het implementatiescript is een script dat in vier fasen kan worden opgesplitst. Elke fase implementeert en configureert een Azure-bron die zich in het [architectuurdiagram bevindt.](#architecture)

De vier fasen zijn

- Azure Key Vault implementeren.
- Azure Web Apps implementeren.
- Application Gateway implementeren met firewall voor webtoepassingen.
- Een Azure AD configureren met geïmplementeerde app.

Elke fase bouwt voort op de voorgaande fase met behulp van configuratie van de eerder geïmplementeerde resources.

Als u de implementatiestappen wilt voltooien, controleert u of u de hulpprogramma's hebt geïnstalleerd die worden vermeld onder [Vereisten](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Azure Key Vault implementeren
In deze sectie maakt en implementeert u een Azure Key Vault-exemplaar dat wordt gebruikt om geheimen en certificaten op te slaan.

Nadat u de implementatie hebt voltooid, wordt een Azure Key Vault-exemplaar geïmplementeerd op Azure.

Azure Key Vault implementeren met Powershell
 
1. Declareer de variabelen voor Azure Key Vault.
2. Registreer de Azure Key Vault-provider.
3. Maak de resourcegroep voor de instantie.
4. Maak de instantie Azure Key Vault in de resourcegroep die in stap 3 is gemaakt.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>De onderstaande Azure AD-gebruiker heeft beheerdersmachtigingen voor de Key Vault
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Registreer de Az Providers
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>De instantie Azure Key Vault maken
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>Het beheerdersbeleid toevoegen aan de sleutelkluis
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>Ga als u een toegangsbeleid maakt waarmee een gebruiker cryptografische sleutels, certificaten en geheimen kan ophalen en aanbieden als u de gebruikersnaam kent:
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

Het is een aanbevolen manier om beheerde identiteiten voor Azure-bronnen te gebruiken in apps die Key Vault gebruiken om toegang te krijgen tot bronnen. Uw beveiligingshouding neemt toe wanneer toegangssleutels tot Key Vault niet in code of in configuratie worden opgeslagen.

Een rootcertificaat is opgenomen in de container. De stappen die zijn genomen om het certificaat te verkrijgen, zijn

1. Download het certificaatbestand van de [Certificeringsinstantie](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. Decoderen van uw certificaatbestand:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
Met dit script wordt een toegewezen identiteit gemaakt voor de app-service-instantie die met MSI kan worden gebruikt om te communiceren met Azure Key Vault zonder harde coderingsgeheimen in code of configuratie.

Ga naar de instantie Azure Key Vault in de portal om de toegewezen identiteit te autoriseren op het tabblad toegangsbeleid. Selecteer **Nieuw toegangsbeleid toevoegen**. Zoek **onder Hoofd selecteren**de toepassingsnaam die vergelijkbaar is met de naam van de instantie App-service die is gemaakt.
Een serviceprincipal die aan de toepassing is gekoppeld, moet zichtbaar zijn. Selecteer deze en sla de pagina toegangsbeleid op, zoals in de volgende schermafbeelding wordt weergegeven.

Omdat de toepassing alleen sleutels hoeft op te halen, selecteert u de machtiging **Ophalen** in de geheimenopties, waarbij toegang wordt toegestaan terwijl de verleende bevoegdheden worden verminderd.

![Key Vault Access Policy](./media/secure-aad-app/kv-access-policy.png)

*Een toegangsbeleid voor key vault maken*

Sla het toegangsbeleid op en sla de nieuwe wijziging op op het tabblad **Toegangsbeleid** om het beleid bij te werken.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Application Gateway implementeren met firewall voor webtoepassingen ingeschakeld
In web apps, is het niet aan te raden dat u diensten rechtstreeks bloot aan de buitenwereld op het internet.
Load balancing en firewall regels bieden meer veiligheid en controle over het binnenkomende verkeer en helpen u het te beheren.

Een toepassingsgateway-instantie implementeren

1. Maak de resourcegroep om de toepassingsgateway te huisvesten.
2. Een virtueel netwerk inrichten om aan de gateway te koppelen.
3. Maak een subnet voor de gateway in het virtuele netwerk.
4. Een openbaar IP-adres opgeven.
5. Inrichten van de toepassingsgateway.
6. Firewall voor webtoepassingen inschakelen op de gateway.

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

#Create a virtual network with the subnets defined in the preceding steps.

$vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

#Retrieve the virtual network resource and subnet resources to be used in the steps that follow.

$vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet


#Create a public IP address for the front-end configuration

$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "East US" -AllocationMethod Dynamic

#Create an application gateway configuration object

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

#Create a front-end IP configuration

$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

#Configure the back-end IP address pool with the IP addresses of the back-end web servers

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.3.11

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 


#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the TLS/SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the TLS/SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Azure Web Apps implementeren
Met Azure App Service u web-apps bouwen en hosten met de talen zoals Python, Ruby, C#en Java. Azure ondersteunt ook aangepaste containers, waardoor vrijwel alle programmeertalen kunnen worden uitgevoerd op het Azure App Service-platform.

#### <a name="create-an-app-service-plan-in-free-tier"></a>Een App-serviceplan maken in de gratis laag
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>Een webtoepassing maken
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>Voordat u verdergaat, gaat u naar de configuratie-gebruikersinterface van https://aka.ms/appservicecustomdns uw Azure Domain Name System voor uw aangepaste domein en volgt u de instructies om een CNAME-record te configureren voor de hostnaam "www" en wijst u de standaarddomeinnaam van uw webapp aan

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>App-serviceabonnement upgraden naar gedeelde laag (minimaal vereist door aangepaste domeinen)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>Een aangepaste domeinnaam toevoegen aan de web-app
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>Richtlijnen en aanbevelingen

### <a name="network"></a>Netwerk
Nadat u de implementatie hebt voltooid, hebt u een toepassingsgateway met firewall voor webtoepassingen ingeschakeld.

De gateway-instantie stelt poort 443 voor HTTPS bloot. Deze configuratie zorgt ervoor dat onze app alleen toegankelijk is op poort 443 via HTTPS.

Het blokkeren van ongebruikte poorten en het beperken van de blootstelling aan het aanvalsoppervlak is een beveiligingstoepassing.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Netwerkbeveiligingsgroepen toevoegen aan het exemplaar app-service

App Service-exemplaren kunnen worden geïntegreerd met virtuele netwerken. Met deze integratie kunnen ze worden geconfigureerd met netwerkbeveiligingsgroepen die het inkomende en uitgaande verkeer van de app beheren.

1. Als u deze functie wilt inschakelen, selecteert u in het azure app-serviceinstantieblad onder **Instellingen** **Netwerkinstellingen**. Configureer in het rechterdeelvenster onder **VNet-integratie**.

   ![Nieuwe virtuele netwerkintegratie](./media/secure-web-app/app-vnet-menu.png)

    *Nieuwe virtuele netwerkintegratie voor App Service*
1. Selecteer **VNET toevoegen (voorbeeld)** op de volgende pagina.

1. Selecteer in het volgende menu het virtuele netwerk `aad-vnet`dat is gemaakt in de implementatie die begint met . U een nieuw subnet maken of een bestaand subnet selecteren.
   Maak in dit geval een nieuw subnet. Stel het **bereik van** het adres in op **10.0.3.0/24** en geef een naam aan het subnet **app-subnet**.

   ![Virtuele netwerkconfiguratie van App Service](./media/secure-web-app/app-vnet-config.png)

    *Virtuele netwerkconfiguratie voor App-service*

Nu u de virtuele netwerkintegratie hebt ingeschakeld, u netwerkbeveiligingsgroepen toevoegen aan onze app.

1. Gebruik het zoekvak, zoek naar **netwerkbeveiligingsgroepen.** Selecteer **Netwerkbeveiligingsgroepen** in de resultaten.

    ![Zoeken naar netwerkbeveiligingsgroepen](./media/secure-web-app/nsg-search-menu.png)

    *Zoeken naar netwerkbeveiligingsgroepen*

2. Selecteer In het volgende menu De optie **Toevoegen**. Voer de **naam** van de NSG en de **resourcegroep** in waarin deze zich moet bevinden. Deze NSG wordt toegepast op het subnet van de applicatiegateway.

    ![Een NSG maken](./media/secure-web-app/nsg-create-new.png)

    *Een NSG maken*

3. Nadat de NSG is gemaakt, selecteert u deze. Selecteer in het blad onder **Instellingen**de **inkomende beveiligingsregels**. Configureer deze instellingen zodat verbindingen in de toepassingsgateway via poort 443 kunnen worden opgenomen.

   ![De NSG configureren](./media/secure-web-app/nsg-gateway-config.png)

   *De NSG configureren*

4. Voeg in de uitgaande regels voor de gateway NSG een regel toe die uitgaande verbindingen met de app-service-instantie toestaat door een regel te maken die zich richt op de servicetag`AppService`

   ![Uitgaande regels voor de NSG toevoegen](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Uitgaande regels voor de NSG toevoegen*

    Voeg nog een uitgaande regel toe om de gateway uitgaande regels naar een virtueel netwerk te laten verzenden.

   ![Een andere uitgaande regel toevoegen](./media/secure-web-app/nsg-outbound-vnet.png)

    *Een andere uitgaande regel toevoegen*

5. Selecteer **Op**het subnet van het NSG Associate, selecteer je het virtuele netwerk dat in de implementatie is gemaakt en selecteer je het gatewaysubnet met de naam **gw-subnet**. De NSG wordt toegepast op het subnet.

6. Maak een andere NSG zoals in de eerdere stap, dit keer voor het app-service-exemplaar. Geef het een naam. Voeg de inkomende regel voor poort 443 toe, net als voor de toepassingsgateway NSG.

   Als u een App Service-instantie hebt geïmplementeerd op een app-serviceomgeving-instantie, wat niet het geval is voor deze app, u binnenkomende regels toevoegen om Azure Service Health-sondes toe te staan door poorten 454-455 te openen voor de binnenkomende beveiligingsgroepen van uw App Service NSG. Hier is de configuratie:

   ![Regels toevoegen voor Azure Service Health-sondes](./media/secure-web-app/nsg-create-healthprobes.png)

    *Regels toevoegen voor Azure Service Health-sondes (alleen App-serviceomgeving)*

Als u het aanvalsoppervlak wilt beperken, wijzigt u de netwerkinstellingen van App Service zodat alleen de toepassingsgateway toegang heeft tot de toepassing.
Als u de instellingen wilt toepassen, gaat u naar het tabblad App-netwerk, selecteert u het tabblad **IP-beperkingen** en maakt u een allow-regel waarmee alleen het IP-adres van de toepassingsgateway rechtstreeks toegang heeft tot de service. U het IP-adres van de gateway ophalen op de overzichtspagina. Voer op het tabblad **CIDR IP-adres** het `<GATEWAY_IP_ADDRESS>/32`IP-adres in deze indeling in: .

![Alleen de gateway toestaan](./media/secure-web-app/app-allow-gw-only.png)

*Alleen het gateway-IP toestaan om toegang te krijgen tot de App-service*

### <a name="azure-domain-name-system"></a>Azure-domeinnaamsysteem 
Het Azure Domain Name System, of Azure Domain Name System, is verantwoordelijk voor het vertalen (of oplossen) van een website of servicenaam naar het IP-adres. Azure Domain Namehttps://docs.microsoft.com/azure/dns/dns-overview) System is een hostingservice voor domeinen van het domain name system die naamomzetting biedt met behulp van Azure-infrastructuur. Door domeinen in Azure te hosten, kunnen gebruikers records van het domain name system beheren met dezelfde referenties, API's, hulpprogramma's en facturering als andere Azure-services. Azure Domain Name System ondersteunt ook private Domain Name System-domeinen.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption maakt gebruik van de BitLocker-functie van Windows om volumeversleuteling voor gegevensschijven te bieden. De oplossing integreert met Azure Key Vault om de schijfversleutelingssleutels te beheren en te beheren.

### <a name="identity-management"></a>Identiteitsbeheer
De volgende technologieën bieden mogelijkheden om toegang tot kaarthoudergegevens in de Azure-omgeving te beheren
- Azure Active Directory is de multi-tenant cloud- en identiteitsbeheerservice van Microsoft. Alle gebruikers voor deze oplossing worden gemaakt in Azure Active Directory, inclusief gebruikers die toegang hebben tot de Azure WebApp.
- Azure-toegangsbeheer op basis van azure-rollen stelt beheerders in staat om fijnmazige toegangsmachtigingen te definiëren om alleen de hoeveelheid toegang te verlenen die gebruikers nodig hebben om hun taken uit te voeren. In plaats van elke gebruiker onbeperkte toestemming te geven voor Azure-bronnen, kunnen beheerders alleen bepaalde acties toestaan voor toegang tot kaarthoudergegevens. Abonnementstoegang is beperkt tot de abonnementsbeheerder.
- Azure Active Directory Privileged Identity Management stelt klanten in staat om het aantal gebruikers dat toegang heeft tot bepaalde informatie, zoals kaarthoudergegevens, te minimaliseren. Beheerders kunnen Azure Active Directory Privileged Identity Management gebruiken om bevoorrechte identiteiten en hun toegang tot bronnen te ontdekken, te beperken en te controleren. Deze functionaliteit kan ook worden gebruikt om on-demand, just-in-time administratieve toegang af te dwingen wanneer dat nodig is.
- Azure Active Directory Identity Protection detecteert potentiële kwetsbaarheden die de identiteit van een organisatie beïnvloeden, configureert geautomatiseerde reacties op gedetecteerde verdachte acties met betrekking tot de identiteit van een organisatie en onderzoekt verdachte incidenten om passende maatregelen te nemen om deze op te lossen.
### <a name="secrets-management"></a>Geheimen beheer
De oplossing maakt gebruik van Azure Key Vault voor het beheer van sleutels en geheimen. Met Azure Key Vault kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Met de volgende Azure Key Vault-mogelijkheden kunnen klanten dergelijke gegevens beveiligen en openen
   - Geavanceerde toegangsbeleidsregels worden geconfigureerd op basis van behoefte.
   - Key Vault-toegangsbeleid wordt gedefinieerd met minimaal vereiste machtigingen voor sleutels en geheimen.
   - Alle sleutels en geheimen in Key Vault hebben vervaldatums.
   - Alle sleutels in Key Vault worden beschermd door gespecialiseerde hardwarebeveiligingsmodules. Het belangrijkste type is een hardware security module (HSM) Protected 2048-bit RSA Key.
   - Met Key Vault u sleutels en geheimen versleutelen (zoals verificatiesleutels, opslagaccountsleutels, gegevensversleutelingssleutels,. PFX-bestanden en wachtwoorden met behulp van sleutels die worden beschermd door hardwarebeveiligingsmodules (HSM's). 
   - Gebruik RBAC (Role-Based Access Control) om machtigingen toe te wijzen aan gebruikers, groepen en toepassingen op een bepaald bereik.     
   - Gebruik Key Vault om uw TLS-certificaten te beheren met automatische verlenging. 
   - Diagnostische logboeken voor Key Vault zijn ingeschakeld met een bewaartermijn van ten minste 365 dagen.
   - Toegestane cryptografische bewerkingen voor sleutels zijn beperkt tot de vereiste bewerkingen.
### <a name="azure-security-center"></a>Azure Security Center
Met Azure Security Center kunnen klanten beveiligingsbeleid centraal toepassen en beheren voor workloads, de blootstelling aan bedreigingen beperken en aanvallen detecteren en erop reageren. Bovendien 
   - Azure Security Center heeft toegang tot bestaande configuraties van Azure-services om configuratie- en serviceaanbevelingen te bieden om de beveiligingshouding te verbeteren en gegevens te beschermen.
   - Azure Security Center maakt gebruik van verschillende detectiemogelijkheden om klanten te waarschuwen voor mogelijke aanvallen op hun omgeving. Deze waarschuwingen bevatten waardevolle informatie over de trigger van de waarschuwing, de betrokken resources en de bron van de aanval. Azure Security Center heeft een reeks vooraf gedefinieerde beveiligingswaarschuwingen, die worden geactiveerd wanneer een bedreiging of verdachte activiteit plaatsvindt. Met aangepaste waarschuwingsregels in Azure Security Center kunnen klanten nieuwe beveiligingswaarschuwingen definiëren op basis van gegevens die al zijn verzameld uit hun omgeving.
   - Azure Security Center biedt geprioriteerd beveiligingswaarschuwingen en -incidenten, waardoor het voor klanten eenvoudiger wordt om potentiële beveiligingsproblemen te ontdekken en aan te pakken. Een threat intelligence rapport wordt gegenereerd voor elke gedetecteerde bedreiging om incident response teams te helpen bij het onderzoeken en herstellen van bedreigingen.
### <a name="azure-application-gateway"></a>Azure Application Gateway 
   De architectuur vermindert het risico op beveiligingsproblemen met behulp van een Azure Application Gateway met een firewall voor webtoepassingen geconfigureerd en de OWASP-regelset is ingeschakeld. Extra mogelijkheden zijn o.a.
   - End-to-end TLS.
   - Schakel TLS v1.0 en v1.1 uit.
   - Schakel TLSv1.2 in.
   - Firewall voor webtoepassingen (preventiemodus).
   - Preventiemodus met OWASP 3.0-regelset.
   - Diagnostische logboekregistratie inschakelen.
   - Aangepaste gezondheidssondes.
   - Azure Security Center en een Azure Advisor bieden extra bescherming en meldingen. Azure Security Center biedt ook een reputatiesysteem.
### <a name="logging-and-auditing"></a>Logboekregistratie en bewaking
Azure-services registreren op grote schaal systeem- en gebruikersactiviteit, evenals systeemstatus:
   - Activiteitslogboeken: [activiteitslogboeken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) geven inzicht in bewerkingen die worden uitgevoerd op resources in een abonnement. Activiteitslogboeken kunnen helpen bij het bepalen van de initiator, het tijdstip van het optreden en de status van een bewerking.
   - Diagnostische [logboeken: Diagnostische logboeken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) bevatten alle logboeken die door elke resource worden uitgestraald. Deze logboeken omvatten Windows-gebeurtenissysteemlogboeken, Azure Storage-logboeken, Key Vault-controlelogboeken en toepassingsgatewaytoegang en firewalllogboeken. Alle diagnostische logboeken schrijven naar een gecentraliseerd en versleuteld Azure-opslagaccount voor archivering. De retentie is tot 730 dagen gebruikersconfigureerbaar om te voldoen aan organisatiespecifieke bewaarvereisten.
### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken
   Deze logboeken worden geconsolideerd in [Azure Monitor-logboeken](https://azure.microsoft.com/services/log-analytics/) voor verwerking, opslag en dashboardrapportage. Eenmaal verzameld, worden de gegevens ingedeeld in afzonderlijke tabellen voor elk gegevenstype binnen Log Analytics-werkruimten, waarmee alle gegevens samen kunnen worden geanalyseerd, ongeacht de oorspronkelijke bron. Bovendien integreert Azure Security Center met Azure Monitor-logboeken waarmee klanten Kusto-query's kunnen gebruiken om toegang te krijgen tot hun beveiligingsgebeurtenisgegevens en deze te combineren met gegevens van andere services.

   De volgende [Azure-bewakingsoplossingen](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) zijn opgenomen als onderdeel van deze architectuur

   - [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): De Active Directory Health Check-oplossing beoordeelt het risico en de status van serveromgevingen op een regelmatig interval en biedt een geprioritteerde lijst met aanbevelingen die specifiek zijn voor de geïmplementeerde serverinfrastructuur.
   - [Agentgezondheid:](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)De oplossing agentstatus rapporteert hoeveel agents worden geïmplementeerd en hun geografische distributie, evenals hoeveel agents, die niet reageren en het aantal agents, die operationele gegevens indienen.
   - [Activiteitslogboekanalyse:](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)De oplossing Activity Log Analytics helpt bij het analyseren van de Azure-activiteitslogboeken voor alle Azure-abonnementen voor een klant.
### <a name="azure-monitor"></a>Azure Monitor
   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)helpt gebruikers prestaties bij te houden, de beveiliging te onderhouden en trends te identificeren door organisaties in staat te stellen te controleren, waarschuwingen te maken en gegevens te archiveren, waaronder het bijhouden van API-aanroepen in hun Azure-bronnen.
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) is een uitbreidbare Application Performance Management-service voor webontwikkelaars op meerdere platforms. Application Insights detecteert prestatieafwijkingen en klanten kunnen deze gebruiken om de live webapplicatie te monitoren. Het bevat krachtige analysetools om klanten te helpen problemen te diagnosticeren en te begrijpen wat gebruikers daadwerkelijk met hun app doen. Het is ontworpen om klanten te helpen de prestaties en bruikbaarheid voortdurend te verbeteren.

### <a name="azure-key-vault"></a>Azure Key Vault
   Maak een kluis voor de organisatie waarin sleutels kunnen worden opgeslagen en verantwoordelijkheid kunnen worden gehouden voor operationele taken zoals hieronder

   - Gegevens die zijn opgeslagen in Key Vault, omvatten   
   - Toepassingsinzichtsleutel
   - Toegangssleutel voor gegevensopslag
   - Verbindingsreeks
   - Naam van gegevenstabel
   - Gebruikersreferenties
   - Geavanceerd toegangsbeleid is geconfigureerd op basis van behoefte
   - Key Vault-toegangsbeleid wordt gedefinieerd met minimaal vereiste machtigingen voor sleutels en geheimen
   - Alle sleutels en geheimen in Key Vault hebben vervaldatums
   - Alle sleutels in Key Vault worden beschermd door hardwarebeveiligingsmodule (HSM) [Sleuteltype = beveiligde hardwarebeveiligingsmodule (HSM)       
     2048-bits RSA-sleutel]
   - Alle gebruikers/identiteiten krijgen minimaal vereiste machtigingen met behulp van RBAC (Role-Based Access Control)
   - Toepassingen delen geen Key Vault tenzij ze elkaar vertrouwen en ze toegang nodig hebben tot dezelfde geheimen tijdens runtime
   - Diagnostische logboeken voor Key Vault zijn ingeschakeld met een bewaartermijn van ten minste 365 dagen.
   - Toegestane cryptografische bewerkingen voor sleutels zijn beperkt tot de vereiste bewerkingen

### <a name="vpn-and-expressroute"></a>VPN en ExpressRoute
   Een beveiligde VPN-tunnel of [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) moest worden geconfigureerd door veilig een verbinding tot stand te brengen met de resources die worden geïmplementeerd als onderdeel van deze Referentiearchitectuur voor PaaS-webtoepassingen. Door een VPN of ExpressRoute op de juiste manier in te stellen, kunnen klanten een beveiligingslaag toevoegen voor gegevens die onderweg zijn.

   Door een beveiligde VPN-tunnel met Azure te implementeren, kan een virtuele privéverbinding worden gemaakt tussen een on-premises netwerk en een Azure Virtual Network. Deze verbinding vindt plaats via het internet en stelt klanten in staat om veilig "tunnel" informatie binnen een versleutelde verbinding tussen het netwerk van de klant en Azure. Site-to-Site VPN is een veilige, volwassen technologie die al tientallen jaren wordt geïmplementeerd door ondernemingen van elke omvang. De IPsec-tunnelmodus wordt in deze optie gebruikt als versleutelingsmechanisme.

   Omdat verkeer binnen de VPN-tunnel het internet doorkruist met een site-to-site VPN, biedt Microsoft een andere, nog veiligere verbindingsoptie. Azure ExpressRoute is een speciale WAN-koppeling tussen Azure en een on-premises locatie of een Exchange-hostingprovider. Aangezien ExpressRoute-verbindingen niet via internet verlopen, bieden deze verbindingen meer betrouwbaarheid, hogere snelheden, lagere latencies en hogere beveiliging dan typische verbindingen via internet. Aangezien dit een directe verbinding van de telecomprovider van de klant is, reizen de gegevens bovendien niet via internet en worden ze er dus niet aan blootgesteld.

   Aanbevolen procedures voor het implementeren van een beveiligd hybride netwerk dat een on-premises netwerk uitbreidt naar Azure zijn [beschikbaar.](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)

#### <a name="implement-azure-active-directory-oidc"></a>Azure Active Directory OIDC implementeren

1. Als u de broncoderepository wilt klonen, gebruikt u deze opdracht Git

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>De omleidings-URL's bijwerken
1.  Navigeer terug naar de Azure-portal. Selecteer in het navigatiedeelvenster aan de linkerkant de Azure Active Directory-service en selecteer app-registraties.
2.  Selecteer in het resulterende scherm de webapp-OpenIDConnect-DotNet-code-v2-toepassing.
3.  Selecteer web in het keuzekader voor verificatie in het tabblad Verificatie o In de sectie Uri's omleiden en voeg u de volgende omleidings-URI's toe.
    https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.nethttps://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc o Stel de URL van de aanmelding in de sectie Geavanceerde instellingen inhttps://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  Werk bijvoorbeeld https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.netop het tabblad Branding o Update de URL van de startpagina naar het adres van uw app-service .
        o Sla de configuratie op.
5.  Als uw toepassing een web-api aanroept, moet u de nodige wijzigingen toepassen op de projectappsettings.json, zodat de gepubliceerde API-URL wordt aangenomen in plaats van localhost.
Het voorbeeld publiceren
    1.  Download het publicatieprofiel op het tabblad Overzicht van de app-service door op de koppeling Publicatieprofiel ophalen te klikken en op te slaan. Andere implementatiemechanismen, zoals van bronbeheer, kunnen ook worden gebruikt.
    2.  Schakel over naar Visual Studio en ga naar het WebApp-OpenIDConnect-DotNet-code-v2-project. Klik met de rechtermuisknop op het project in de Solution Explorer en selecteer Publiceren. Klik op Profiel importeren op de onderste balk en importeer het publicatieprofiel dat u eerder hebt gedownload.
    3.  Klik op Configureren en werk op het tabblad Verbinding de URL van doel bij, zodat het bijvoorbeeld https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.neteen https is op de url van de startpagina. Klik op Volgende.
    4.  Controleer op het tabblad Instellingen of Organisatieverificatie inschakelen NIET is geselecteerd. Klik op Opslaan. Klik op Publiceren op het hoofdscherm.
    5.  Visual Studio publiceert het project en opent automatisch een browser naar de URL van het project. Als u de standaardwebpagina van het project ziet, is de publicatie geslaagd.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Multi-Factor-verificatie implementeren voor Azure Active Directory
   Beheerders moeten ervoor zorgen dat de abonnementsaccounts in de portal worden beveiligd. Het abonnement is kwetsbaar voor aanvallen omdat het de resources beheert die u hebt gemaakt. Als u het abonnement wilt beveiligen, schakelt u Multi-Factor Authentication in op het tabblad **Azure Active Directory** van het abonnement.

   Een Azure AD werkt op basis van beleidsregels die worden toegepast op een gebruiker of groepen gebruikers die aan een bepaalde criteria voldoen.
Azure maakt een standaardbeleid waarin wordt aangegeven dat beheerders tweestapsverificatie nodig hebben om zich aan te melden bij de portal.
Nadat u dit beleid hebt ingevonden, wordt u mogelijk gevraagd om u af te melden en u weer aan te melden bij de Azure-portal.

MFA inschakelen voor beheerdersaanmeldingen

   1. Ga naar het tabblad **Azure Active Directory** in de Azure-portal
   2. Selecteer onder de beveiligingscategorie voorwaardelijke toegang. U ziet dit scherm

       ![Voorwaardelijke toegang - Beleid](./media/secure-aad-app/ad-mfa-conditional-add.png)

Als u geen nieuw beleid maken

   1. Ga naar het **MFA-tabblad.**
   2. Selecteer een proefversiekoppeling van Azure AD Premium **Gratis** om u te abonneren op de gratis proefversie.

   ![Een gratis proefversie van Azure AD Premium](./media/secure-aad-app/ad-trial-premium.png)

Terug naar het scherm met voorwaardelijke toegang.

   1. Selecteer het nieuwe tabblad beleid.
   2. Voer de naam van het beleid in.
   3. Selecteer de gebruikers of groepen waarvoor u MFA wilt inschakelen.
   4. Selecteer **onder Toegangsbesturingselementen**het tabblad **Verlening** en selecteer Desgewenst **multifactorauthenticatie** (en andere instellingen vereisen).

   ![MFA vereisen](./media/secure-aad-app/ad-mfa-conditional-add.png)

   U het beleid inschakelen door het selectievakje boven aan het scherm in te schakelen of dit te doen op het tabblad **Voorwaardelijke toegang.** Wanneer het beleid is ingeschakeld, hebben gebruikers MFA nodig om zich aan te melden bij de portal.

   Er is een basislijnbeleid waarvoor MFA vereist is voor alle Azure-beheerders. U het direct inschakelen in de portal. Als u dit beleid inschakelt, kan de huidige sessie ongeldig worden gemaakt en u zich opnieuw aanmelden.

   Als het basislijnbeleid niet is ingeschakeld
   1.   Selecteer **MFA vereisen voor beheerders**.
   2.   Selecteer **Beleid gebruiken onmiddellijk**.

   ![Beleid gebruiken onmiddellijk selecteren](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Azure Sentinel gebruiken om apps en resources te controleren

   Naarmate een toepassing groeit, wordt het moeilijk om alle beveiligingssignalen en statistieken die van resources zijn ontvangen, samen te voegen en ze op een actiegerichte manier nuttig te maken.

   Azure Sentinel is ontworpen om gegevens te verzamelen, de soorten bedreigingen te detecteren die mogelijk zijn en inzicht te bieden in beveiligingsincidenten.
Terwijl het wacht op handmatige interventie, kan Azure Sentinel vertrouwen op vooraf geschreven playbooks om waarschuwingen en incidentbeheerprocessen te starten.

   De voorbeeld-app bestaat uit verschillende bronnen die Azure Sentinel kan controleren.
Als u Azure Sentinel wilt instellen, moet u eerst een Log Analytics-werkruimte maken die alle gegevens opslaat die zijn verzameld uit de verschillende bronnen.

Deze werkruimte maken

   1. Zoek in het zoekvak in de Azure-portal naar **Log Analytics**. Selecteer **Logboekanalysewerkruimten**.

   ![Zoeken naar Log Analytics-werkruimten](./media/secure-aad-app/sentinel-log-analytics.png)

   *Zoeken naar Log Analytics-werkruimten*

   2. Selecteer op de volgende pagina **Toevoegen** en geef vervolgens een naam, resourcegroep en locatie op voor de werkruimte.
   ![Een Log Analytics-werkruimte maken](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *Een Log Analytics-werkruimte maken*

   3. Gebruik het zoekvak om te zoeken naar **Azure Sentinel**.

   ![Zoeken naar Azure Sentinel](./media/secure-aad-app/sentinel-add.png)

   *Zoeken naar Azure Sentinel*

   4. Selecteer **Toevoegen** en selecteer vervolgens de werkruimte Log Analytics die u eerder hebt gemaakt.

   ![Een werkruimte Logboekanalyse toevoegen](./media/secure-aad-app/sentinel-workspace-add.png)

   *Een werkruimte Logboekanalyse toevoegen*

   5. Selecteer op de pagina **Azure Sentinel - Gegevensconnectoren** onder **Configuratie**de optie **Gegevensconnectoren**. U ziet een array met Azure-services die u koppelen aan de opslaginstantie Log Analytics voor analyse in Azure Sentinel.

   ![Gegevensconnectors voor Log Analytics](./media/secure-aad-app/sentinel-connectors.png)

      *Een gegevensconnector toevoegen aan Azure Sentinel*

   Als u bijvoorbeeld de toepassingsgateway wilt verbinden, neemt u de volgende stappen:

   1. Open het instantieblad azure application gateway.
   2. Selecteer **Diagnostische instellingen** onder **Controle**.
   3. Selecteer **Diagnostische instelling toevoegen**.

   ![Application Gateway-diagnose toevoegen](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *Application Gateway-diagnose toevoegen*

   4. Selecteer **op** de pagina Diagnostische instellingen de werkruimte Log Analytics die u hebt gemaakt en selecteer vervolgens alle statistieken die u wilt verzamelen en verzenden naar Azure Sentinel. Selecteer **Opslaan**.

   ![Azure Sentinel-connectorinstellingen](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>Kostenoverwegingen
   Als u nog geen Azure-account hebt, u een gratis account maken. Ga naar de [gratis accountpagina](https://azure.microsoft.com/free/) om aan de slag te gaan, zie wat u doen met een gratis Azure-account en ontdek welke producten 12 maanden gratis zijn.

   Als u de resources in de voorbeeld-app wilt implementeren met de beveiligingsfuncties, moet u betalen voor een aantal premiumfuncties. Aangezien de app schaalt en de gratis lagen en proefversies die door Azure worden aangeboden, moeten worden geüpgraded om te voldoen aan de vereisten voor toepassingen, kunnen uw kosten stijgen. Gebruik de [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/) om uw kosten te schatten.

## <a name="next-steps"></a>Volgende stappen
   De volgende artikelen kunnen u helpen bij het ontwerpen, ontwikkelen en implementeren van beveiligde toepassingen.

- [Ontwerp](secure-design.md)
- [Ontwikkelen](secure-develop.md)
- [Implementeren](secure-deploy.md)
