---
title: Een veilige Azure AD-webtoepassing ontwikkelen | Microsoft Docs
description: Deze eenvoudige voor beeld-app implementeert aanbevolen beveiligings procedures voor het verbeteren van uw toepassing en de beveiligings postuur van uw organisatie bij het ontwikkelen op Azure.
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
ms.openlocfilehash: 58deae64bdde27580b85b00a1c87ba115e24478a
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "82159895"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Een beveiligde App ontwikkelen voor een Azure AD-app
## <a name="overview"></a>Overzicht

Dit voor beeld is een eenvoudige Azure Active Directory met een web-app die is gekoppeld aan beveiligings resources voor het ontwikkelen van apps in Azure. De app implementeert aanbevolen beveiligings procedures waarmee u uw toepassing en de beveiligings postuur van uw organisatie kunt verbeteren wanneer u apps op Azure ontwikkelt.

De implementatie scripts instellen de infra structuur. Nadat u de implementatie scripts hebt uitgevoerd, moet u een aantal hand matige configuratie in het Azure Portal uitvoeren om de onderdelen en services samen te koppelen. Dit voor beeld is gericht op ervaren ontwikkel aars van Azure die in de zakelijke branche werken en willen een beveiligd Azure Active Directory bouwen met een beveiligde Azure-infra structuur. 


Bij het ontwikkelen en implementeren van deze app leert u hoe u 
- Maak een Azure Key Vault-exemplaar, sla het op en haal er geheimen van op.
- Implementeer de Azure-web-app, die speciaal is geïsoleerd met toegang tot de front-end-firewall. 
- Een Azure-toepassing gateway-exemplaar maken en configureren met een firewall die gebruikmaakt van de Rule OWASP Top 10. 
- Versleuteling van gegevens in door Voer en op rest inschakelen met behulp van Azure-Services. 
- Stel het Azure-beleid en Security Center in om de compliancies te evalueren. 

Nadat u deze app hebt ontwikkeld en geïmplementeerd, hebt u de volgende voor beeld-web-app ingesteld, samen met de configuratie-en beveiligings maatregelen die worden beschreven.

## <a name="architecture"></a>Architectuur
De app is een typische toepassing met n-tier met drie lagen. De front-end, de back-end en de data base-laag met de bewakings-en geheim beheer onderdelen die zijn geïntegreerd, worden hier weer gegeven:

![App-architectuur](./media/secure-aad-app/architecture.png)

Deze oplossing maakt gebruik van de volgende Azure-Services. Details van de implementatie architectuur vindt u in de sectie implementatie architectuur. 

De architectuur bestaat uit deze onderdelen

- [Azure-toepassing gateway](../../application-gateway/index.yml). Biedt de gateway en Firewall voor onze toepassings architectuur.
- [Application Insights](../../azure-monitor/app/app-insights-overview.md). Biedt een uitbreid bare service voor het beheer van toepassings prestaties op meerdere platforms.
- [Azure Key Vault](../../key-vault/index.yml). De geheimen van onze app worden opgeslagen en versleuteld en het maken van toegangs beleid wordt beheerd.
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Biedt service voor identiteits-en toegangs beheer op basis van de Cloud, aanmelden en toegang tot resources.
- [Azure Domain Name System](../../dns/dns-overview.md). Geef de service op om het domein te hosten.
- [Azure Load Balancer](../../load-balancer/load-balancer-overview.md). Voorziet in het schalen van uw toepassingen en het maken van hoge Beschik baarheid voor uw services.
- [Azure-web-app](../../app-service/overview.md).  Voorziet in een HTTP-gebaseerde service voor het hosten van webtoepassingen.
- [Azure Security Center](../../security-center/index.yml). biedt geavanceerde beveiliging tegen bedreigingen in uw hybride werk belastingen in de Cloud.
- [Azure Policy](../../governance/policy/overview.md). Voorziet in het evalueren van uw resources voor niet-naleving van toegewezen beleid.

## <a name="threat-model"></a>Bedreigings model
Bedreigings modellering is het proces van het identificeren van mogelijke beveiligings Risico's voor uw bedrijf en toepassing en zorgt ervoor dat er een goed risico op de juiste wijze wordt uitgevoerd.

In dit voor beeld wordt de [Microsoft Threat Modeling tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) gebruikt voor het implementeren van bedreigings modellen voor de beveiligde voor beeld-app. Door de componenten en de gegevens stromen te ontwikkelen, kunt u problemen en bedreigingen vroegtijdig in het ontwikkelings proces identificeren. Tijd en geld worden later opgeslagen met behulp van deze.

Dit is het bedreigings model voor de voor beeld-app

![Bedreigings model](./media/secure-aad-app/threat-model.png)

Enkele voor beelden van bedreigingen en mogelijke beveiligings problemen die het hulp programma voor het maken van dreigingen worden gegenereerd, worden weer gegeven in de volgende scherm afbeelding. Het bedreigings model biedt een overzicht van de beschik bare kwets baarheid en vraagt de ontwikkel aars om te zien hoe ze de problemen kunnen oplossen.

![Bedreigings model uitvoer](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>Vereisten
Als u de toepassing wilt uitvoeren, moet u deze hulpprogram ma's installeren:

- Een code-editor om de toepassings code te wijzigen en weer te geven. [Visual Studio code](https://code.visualstudio.com/) is een open-source optie.
- [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) op uw ontwikkel computer.
- [Git](https://git-scm.com/) op uw systeem. Git wordt gebruikt om de bron code lokaal te klonen.
- [JQ](https://stedolan.github.io/jq/), een UNIX-hulp programma voor het uitvoeren van QUERY'S in JSON op een gebruiks vriendelijke manier.

U hebt een Azure-abonnement nodig om de resources van de voor beeld-app te implementeren. Als u geen Azure-abonnement hebt, kunt u [een gratis account maken](https://azure.microsoft.com/free/) om de voor beeld-app te testen.

Na de installatie van deze hulpprogram ma's bent u klaar om de app te implementeren in Azure.

### <a name="implementation-guidance"></a>Implementatie richtlijnen
Het implementatie script is een script dat kan worden onderverdeeld in vier fasen. Elke fase implementeert en configureert een Azure-resource in het [architectuur diagram](#architecture).

De vier fasen zijn

- Azure Key Vault implementeren.
- Azure Web Apps implementeren.
- Implementeer Application Gateway met Web Application Firewall.
- Een Azure AD configureren met geïmplementeerde app.

Elke fase bouwt voort op het bovenstaande door gebruik te maken van de configuratie van de eerder geïmplementeerde resources.

Als u de implementaties tappen wilt volt ooien, moet u ervoor zorgen dat u de hulpprogram ma's hebt geïnstalleerd die worden vermeld onder [vereisten](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Azure Key Vault implementeren
In deze sectie maakt en implementeert u een Azure Key Vault-exemplaar dat wordt gebruikt voor het opslaan van geheimen en certificaten.

Nadat u de implementatie hebt voltooid, hebt u een Azure Key Vault-exemplaar geïmplementeerd in Azure.

Azure Key Vault implementeren met behulp van Power shell
 
1. Declareer de variabelen voor Azure Key Vault.
2. Registreer de Azure Key Vault provider.
3. Maak de resource groep voor het exemplaar.
4. Maak het Azure Key Vault-exemplaar in de resource groep die u in stap 3 hebt gemaakt.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>De onderstaande gebruiker van Azure AD heeft beheerders machtigingen voor de Key Vault
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>De AZ-providers registreren
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>Het Azure Key Vault-exemplaar maken
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>De beleids regels voor beheerders toevoegen aan de Key Vault
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>Om een toegangs beleid te maken waarmee een gebruiker cryptografische sleutels, certificaten en geheimen kan ophalen en weer geven als u de principal-naam van de gebruiker kent:
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

Het is een best practice voor het gebruik van beheerde identiteiten voor Azure-resources in apps die gebruikmaken van Key Vault om toegang te krijgen tot bronnen. Uw beveiligings postuur neemt toe wanneer toegangs sleutels voor Key Vault niet worden opgeslagen in code of in configuratie.

Een basis certificaat is opgenomen in de container. De stappen voor het verkrijgen van het certificaat zijn

1. Down load het certificaat bestand van de [certificerings instantie](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. Uw certificaat bestand decoderen:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
Met dit script maakt u een toegewezen identiteit voor het App Service-exemplaar dat kan worden gebruikt met MSI om te communiceren met Azure Key Vault zonder geheimen voor vaste code ring in code of configuratie.

Ga naar het Azure Key Vault-exemplaar in de portal om de toegewezen identiteit te autoriseren op het tabblad toegangs beleid. Selecteer **nieuw toegangs beleid toevoegen**. Zoek onder **Principal selecteren**naar de toepassings naam die lijkt op de naam van het app service exemplaar dat is gemaakt.
Een service-principal die aan de toepassing is gekoppeld, moet zichtbaar zijn. Selecteer de pagina en sla het toegangs beleid op, zoals wordt weer gegeven in de volgende scherm afbeelding.

Omdat de toepassing alleen sleutels hoeft op te halen, selecteert u de machtiging **Get** in de geheimen opties, waardoor toegang is toegestaan terwijl de verleende bevoegdheden worden verminderd.

![Toegangs beleid Key Vault](./media/secure-aad-app/kv-access-policy.png)

*Een Key Vault toegangs beleid maken*

Sla het toegangs beleid op en sla de nieuwe wijziging op het tabblad **toegangs beleid** op om de beleids regels bij te werken.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Application Gateway implementeren met Web Application Firewall ingeschakeld
In web apps is het niet raadzaam om services rechtstreeks aan de buiten wereld op internet beschikbaar te stellen.
Taak verdeling en firewall regels bieden meer beveiliging en controle over het binnenkomende verkeer en helpen u het te beheren.

Een Application Gateway-exemplaar implementeren

1. Maak de resource groep om de toepassings gateway te maken.
2. Richt een virtueel netwerk in om aan de gateway te koppelen.
3. Maak een subnet voor de gateway in het virtuele netwerk.
4. Richt een openbaar IP-adres in.
5. Richt de toepassings gateway in.
6. Schakel Web Application Firewall in op de gateway.

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
Met Azure App Service kunt u web-apps bouwen en hosten met behulp van de talen zoals python, Ruby, C# en Java. Azure biedt ook ondersteuning voor aangepaste containers, waardoor vrijwel alle programmeer talen kunnen worden uitgevoerd op het Azure App Service platform.

#### <a name="create-an-app-service-plan-in-free-tier"></a>Een App Service plan maken in een gratis laag
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>Een webtoepassing maken
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>Voordat u doorgaat, gaat u naar de gebruikers interface van Azure Domain Name System-configuratie voor uw aangepaste domein https://aka.ms/appservicecustomdns en volgt u de instructies in om een CNAME-record voor de hostnaam ' www ' te configureren en de standaard domein naam van de web-app te laten wijzen

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>Upgrade App Service plan naar gedeelde laag (mini maal vereist voor aangepaste domeinen)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>Een aangepaste domein naam toevoegen aan de web-app
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>Richt lijnen en aanbevelingen

### <a name="network"></a>Netwerk
Nadat u de implementatie hebt voltooid, hebt u een toepassings gateway met Web Application Firewall ingeschakeld.

Het gateway-exemplaar geeft poort 443 voor HTTPS weer. Deze configuratie zorgt ervoor dat onze app alleen toegankelijk is via poort 443 via HTTPS.

Het blok keren van ongebruikte poorten is een beveiligings best practice.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Netwerk beveiligings groepen toevoegen aan het App Service-exemplaar

App Service-exemplaren kunnen worden geïntegreerd met virtuele netwerken. Met deze integratie kunnen ze worden geconfigureerd met beleids regels voor netwerk beveiligings groepen die het binnenkomende en uitgaande verkeer van de app beheren.

1. Als u deze functie wilt inschakelen, selecteert u op de Blade Azure-app service-exemplaar onder **instellingen**de optie **netwerken**. Configureer in het rechterdeel venster onder **VNet-integratie**.

   ![Nieuwe integratie van virtueel netwerk](./media/secure-web-app/app-vnet-menu.png)

    *Nieuwe integratie van virtuele netwerken voor App Service*
1. Selecteer op de volgende pagina **VNET toevoegen (preview)**.

1. Selecteer in het volgende menu het virtuele netwerk dat u hebt gemaakt in de implementatie die `aad-vnet`begint met. U kunt een nieuw subnet maken of een bestaande selecteren.
   In dit geval maakt u een nieuw subnet. Stel het **adres bereik** in op **10.0.3.0/24** en noem het subnet **app-subnet**.

   ![Configuratie van virtuele netwerk App Service](./media/secure-web-app/app-vnet-config.png)

    *Configuratie van het virtuele netwerk voor App Service*

Nu u de integratie van het virtuele netwerk hebt ingeschakeld, kunt u netwerk beveiligings groepen toevoegen aan de app.

1. Zoek in het zoekvak naar **netwerk beveiligings groepen**. Selecteer **netwerk beveiligings groepen** in de resultaten.

    ![Zoeken naar netwerk beveiligings groepen](./media/secure-web-app/nsg-search-menu.png)

    *Zoeken naar netwerk beveiligings groepen*

2. Selecteer in het volgende menu de optie **toevoegen**. Voer de **naam** in van de NSG en de **resource groep** waarin deze zich bevindt. Deze NSG wordt toegepast op het subnet van de toepassings gateway.

    ![Een NSG maken](./media/secure-web-app/nsg-create-new.png)

    *Een NSG maken*

3. Nadat de NSG is gemaakt, selecteert u deze. Selecteer in de Blade onder **instellingen**de optie **regels voor inkomende beveiliging**. Configureer deze instellingen om verbindingen toe te staan in de toepassings gateway via poort 443.

   ![De NSG configureren](./media/secure-web-app/nsg-gateway-config.png)

   *De NSG configureren*

4. Voeg in de uitgaande regels voor de gateway NSG een regel toe waarmee uitgaande verbindingen met het App Service-exemplaar worden toegestaan door een regel te maken die de servicetag van de service bedoelt`AppService`

   ![Uitgaande regels voor de NSG toevoegen](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Uitgaande regels voor de NSG toevoegen*

    Voeg nog een regel voor uitgaande verbindingen toe zodat de gateway uitgaande regels kan verzenden naar een virtueel netwerk.

   ![Een andere uitgaande regel toevoegen](./media/secure-web-app/nsg-outbound-vnet.png)

    *Een andere uitgaande regel toevoegen*

5. Selecteer **koppelen**op de Blade subnetten van de NSG, selecteer het virtuele netwerk dat u in de implementatie hebt gemaakt en selecteer het gateway-subnet met de naam **gw-subnet**. De NSG wordt toegepast op het subnet.

6. Maak een andere NSG, zoals in de vorige stap, de tijd voor de App Service-instantie. Geef het een naam. Voeg de regel voor binnenkomende verbindingen toe voor poort 443, zoals u dat voor de Application Gateway NSG hebt gedaan.

   Als er een App Service-exemplaar is geïmplementeerd op een App Service Environment exemplaar, wat niet het geval is voor deze app, kunt u regels voor binnenkomend verkeer toevoegen om Azure Service Health tests toe te staan door poorten 454-455 te openen in de binnenkomende beveiligings groepen van uw App Service NSG. Hier is de configuratie:

   ![Regels voor Azure Service Health tests toevoegen](./media/secure-web-app/nsg-create-healthprobes.png)

    *Regels voor Azure Service Health tests toevoegen (alleen App Service Environment)*

Als u de kwets baarheid wilt beperken, wijzigt u de App Service netwerk instellingen zodanig dat alleen de toepassings gateway toegang heeft tot de toepassing.
Als u de instellingen wilt Toep assen, gaat u naar App Service tabblad netwerk, selecteert u het tabblad **IP-beperkingen** en maakt u een regel voor toestaan waarmee alleen het IP-adres van de toepassings gateway rechtstreeks toegang heeft tot de service. U kunt het IP-adres van de gateway ophalen van de pagina overzicht. Geef op het tabblad **IP-adres CIDR** het IP-adres op in de `<GATEWAY_IP_ADDRESS>/32`volgende indeling:.

![Alleen de gateway toestaan](./media/secure-web-app/app-allow-gw-only.png)

*Alleen het gateway-IP-adres toegang geven tot de App Service*

### <a name="azure-domain-name-system"></a>Azure Domain Name System 
De Azure Domain Name System, of Azure Domain Name System, is verantwoordelijk voor het vertalen van een website-of service naam naar het IP-adres. Azure Domain Name System (https://docs.microsoft.com/azure/dns/dns-overview) is een hosting service voor Domain Name System domeinen die naam omzetting met behulp van Azure-infra structuur bieden. Door domeinen in azure te hosten, kunnen gebruikers Domain Name System records beheren met dezelfde referenties, Api's, hulpprogram ma's en facturering als andere Azure-Services. Azure Domain Name System biedt ook ondersteuning voor persoonlijke Domain Name System domeinen.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption maakt gebruik van de BitLocker-functie van Windows om volume versleuteling voor gegevens schijven te bieden. De oplossing kan worden geïntegreerd met Azure Key Vault om de versleutelings sleutels voor de schijf te controleren en te beheren.

### <a name="identity-management"></a>Identiteitsbeheer
De volgende technologieën bieden mogelijkheden voor het beheren van toegang tot gegevens van de kaart houder in de Azure-omgeving
- Azure Active Directory is de multi tenant-Cloud Directory en identiteits beheer service van micro soft. Alle gebruikers voor deze oplossing worden gemaakt in Azure Active Directory, met inbegrip van gebruikers die toegang hebben tot de Azure-WebApp.
- Met op rollen gebaseerd toegangs beheer van Azure kunnen beheerders verfijnde toegangs machtigingen definiëren om alleen de hoeveelheid toegang te verlenen die gebruikers nodig hebben om hun taken uit te voeren. In plaats van elke gebruiker onbeperkte machtiging voor Azure-resources geven, kunnen beheerders alleen bepaalde acties toestaan om toegang te krijgen tot gegevens van de kaart houder. Abonnements toegang is beperkt tot de abonnements beheerder.
- Met Azure Active Directory Privileged Identity Management kunnen klanten het aantal gebruikers dat toegang heeft tot bepaalde gegevens, zoals gegevens van de kaart houder, minimaliseren. Beheerders kunnen Azure Active Directory Privileged Identity Management gebruiken om bevoegde identiteiten en hun toegang tot bronnen te detecteren, beperken en controleren. Deze functie kan ook worden gebruikt voor het afdwingen van alleen-in-time-beheer toegang op aanvraag als dat nodig is.
- Azure Active Directory Identity Protection detecteert potentiële beveiligings problemen die van invloed zijn op de identiteit van een organisatie, configureert automatische antwoorden op gedetecteerde verdachte acties die betrekking hebben op de identiteit van een organisatie en onderzoekt verdachte incidenten om deze te verhelpen.
### <a name="secrets-management"></a>Geheimen beheren
De oplossing maakt gebruik van Azure Key Vault voor het beheer van sleutels en geheimen. Met Azure Key Vault kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Met de volgende Azure Key Vault mogelijkheden kunnen klanten dergelijke gegevens beveiligen en gebruiken
   - Geavanceerd toegangs beleid wordt geconfigureerd op basis van behoefte.
   - Key Vault toegangs beleid wordt gedefinieerd met mini maal vereiste machtigingen voor sleutels en geheimen.
   - Alle sleutels en geheimen in Key Vault hebben verloop datums.
   - Alle sleutels in Key Vault worden beveiligd door gespecialiseerde hardware security modules. Het sleutel type is een met HSM beveiligde 2048-bits RSA-sleutel (Hardware Security module).
   - Met Key Vault kunt u sleutels en geheimen versleutelen (zoals verificatie sleutels, sleutels voor opslag accounts, sleutels voor gegevens versleuteling,. PFX-bestanden en wacht woorden) met behulp van sleutels die worden beveiligd door Hardware Security modules (Hsm's). 
   - Gebruik op rollen gebaseerde Access Control (RBAC) om machtigingen toe te wijzen aan gebruikers, groepen en toepassingen bij een bepaald bereik.     
   - Gebruik Key Vault om uw TLS-certificaten te beheren met autoverlengen. 
   - Diagnostische logboeken voor Key Vault zijn ingeschakeld met een Bewaar periode van ten minste 365 dagen.
   - Toegestane cryptografische bewerkingen voor sleutels zijn beperkt tot de vereisten die nodig zijn.
### <a name="azure-security-center"></a>Azure Security Center
Met Azure Security Center kunnen klanten centraal beveiligings beleid Toep assen en beheren voor werk belastingen, bloot stelling aan bedreigingen beperken en aanvallen detecteren en erop reageren. Let 
   - Azure Security Center opent bestaande configuraties van Azure-Services om aanbevelingen voor de configuratie en service te bieden voor het verbeteren van de beveiliging postuur en het beschermen van gegevens.
   - Azure Security Center maakt gebruik van diverse detectie mogelijkheden om klanten te waarschuwen over mogelijke aanvallen die zijn gericht op hun omgeving. Deze waarschuwingen bevatten waardevolle informatie over de trigger van de waarschuwing, de betrokken resources en de bron van de aanval. Azure Security Center heeft een reeks vooraf gedefinieerde beveiligings waarschuwingen, die worden geactiveerd wanneer een bedreiging of verdachte activiteit plaatsvindt. Met aangepaste waarschuwings regels in Azure Security Center kunnen klanten nieuwe beveiligings waarschuwingen definiëren op basis van gegevens die al zijn verzameld uit hun omgeving.
   - Azure Security Center biedt beveiligings waarschuwingen en-incidenten met prioriteit, waardoor klanten eenvoudiger mogelijke beveiligings problemen kunnen detecteren en oplossen. Er wordt een Threat Intelligence-rapport voor elke gedetecteerde bedreiging gegenereerd om de incidenten te helpen bij het onderzoeken en oplossen van bedreigingen.
### <a name="azure-application-gateway"></a>Azure Application Gateway 
   De architectuur vermindert het risico op beveiligings problemen met behulp van een Azure-toepassing gateway waarvoor een Web Application Firewall is geconfigureerd en de ruleset OWASP ingeschakeld. Aanvullende mogelijkheden zijn onder andere
   - End-to-end TLS.
   - Schakel TLS v 1.0 en v 1.1 uit.
   - Schakel TLSv 1.2 in.
   - Web Application firewall (preventie modus).
   - Preventie modus met OWASP 3,0 ruleset.
   - Schakel logboek registratie van diagnostische gegevens in.
   - Aangepaste status controles.
   - Azure Security Center en een Azure Advisor bieden extra beveiliging en meldingen. Azure Security Center biedt ook een reputatie systeem.
### <a name="logging-and-auditing"></a>Logboekregistratie en bewaking
Azure-Services registreren systeem-en gebruikers activiteiten uitvoerig, evenals systeem status:
   - Activiteiten logboeken: [activiteiten logboeken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) bieden inzicht in bewerkingen die worden uitgevoerd op resources in een abonnement. Activiteiten logboeken kunnen helpen bij het bepalen van de initiator, het tijdstip van de gebeurtenis en de status van een bewerking.
   - Diagnostische logboeken: [Diagnostische](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) logboeken bevatten alle logboeken die worden verzonden door elke resource. Deze logboeken bevatten Windows-gebeurtenis systeem logboeken, Azure Storage logboeken, Key Vault controle logboeken en Application Gateway toegang en firewall Logboeken. Alle Diagnostische logboeken schrijven naar een gecentraliseerd en versleuteld Azure Storage-account voor archivering. De retentie kan door de gebruiker worden geconfigureerd, tot 730 dagen, om te voldoen aan de specifieke vereisten voor het bewaren van een organisatie.
### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken
   Deze logboeken worden samengevoegd in [Azure monitor logboeken](https://azure.microsoft.com/services/log-analytics/) voor verwerking, opslag en dashboard rapportage. Zodra de gegevens zijn verzameld, worden deze in verschillende tabellen ingedeeld voor elk gegevens type binnen Log Analytics werk ruimten, waardoor alle gegevens kunnen worden geanalyseerd, ongeacht de oorspronkelijke bron. Bovendien kan Azure Security Center worden geïntegreerd met Azure Monitor-logboeken, zodat klanten Kusto-query's kunnen gebruiken om toegang te krijgen tot de gegevens van de beveiligings gebeurtenis en deze te combi neren met gegevens uit andere services.

   De volgende Azure- [bewakings oplossingen](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) zijn opgenomen als onderdeel van deze architectuur

   - [Active Directory-evaluatie](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): de Active Directory Health Check-oplossing evalueert het risico en de status van de server omgevingen volgens een regel matig interval en biedt een lijst met aanbevelingen die specifiek zijn voor de geïmplementeerde server infrastructuur.
   - [Status van agent](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): de status van agent oplossing rapporteert hoeveel agents zijn geïmplementeerd en wat hun geografische distributie zijn, evenals hoeveel agents niet reageren en het aantal agents dat operationele gegevens verzendt.
   - [Analyse van activiteitenlogboek](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): de analyse van activiteitenlogboek oplossing helpt bij het analyseren van de activiteiten logboeken van Azure in alle Azure-abonnementen voor een klant.
### <a name="azure-monitor"></a>Azure Monitor
   [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)helpt gebruikers bij het volgen van prestaties, het onderhouden van de beveiliging en het identificeren van trends door organisaties in te scha kelen, waarschuwingen te maken en gegevens te archiveren, met inbegrip van tracking-API-aanroepen in hun Azure-resources.
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) is een uitbreid bare service voor het beheer van toepassings prestaties voor webontwikkelaars op meerdere platforms. Application Insights detecteert prestatie afwijkingen en klanten kunnen deze gebruiken om de Live webtoepassing te bewaken. Het bevat krachtige analyse tools om klanten te helpen bij het onderzoeken van problemen en om te begrijpen wat gebruikers daad werkelijk doen met hun app. Het is ontworpen om klanten te helpen de prestaties en bruikbaarheid continu te verbeteren.

### <a name="azure-key-vault"></a>Azure Key Vault
   Maak een kluis voor de organisatie waarin sleutels moeten worden opgeslagen en behoud de verantwoordelijkheid voor operationele taken zoals hieronder

   - Gegevens die zijn opgeslagen in Key Vault bevatten   
   - Toepassings Insight-sleutel
   - Toegangs sleutel voor gegevens opslag
   - Verbindingsreeks
   - Naam gegevens tabel
   - Gebruikers referenties
   - Geavanceerd toegangs beleid wordt op basis van behoefte geconfigureerd
   - Key Vault toegangs beleid is gedefinieerd met mini maal vereiste machtigingen voor sleutels en geheimen
   - Alle sleutels en geheimen in Key Vault verloop datums hebben
   - Alle sleutels in Key Vault worden beveiligd door HSM (Hardware Security module) [key type = hardware Security module (HSM) beveiligd       
     2048-bits RSA-sleutel]
   - Aan alle gebruikers/identiteiten worden mini maal vereiste machtigingen verleend met behulp van op rollen gebaseerde Access Control (RBAC)
   - Toepassingen delen een Key Vault alleen als ze elkaar vertrouwen en toegang hebben tot dezelfde geheimen tijdens runtime
   - Diagnostische logboeken voor Key Vault zijn ingeschakeld met een Bewaar periode van ten minste 365 dagen.
   - Toegestane cryptografische bewerkingen voor sleutels zijn beperkt tot de vereisten die nodig zijn

### <a name="vpn-and-expressroute"></a>VPN-en ExpressRoute
   Een beveiligde VPN-tunnel of [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) moet worden geconfigureerd door een beveiligde verbinding te maken met de resources die zijn geïmplementeerd als onderdeel van deze PaaS-referentie architectuur voor webtoepassingen. Door een VPN-of ExpressRoute in te stellen, kunnen klanten een beveiligingslaag toevoegen voor gegevens die onderweg zijn.

   Door een beveiligde VPN-tunnel te implementeren met Azure, kan een virtuele particuliere verbinding tussen een on-premises netwerk en een Azure-Virtual Network worden gemaakt. Deze verbinding vindt plaats via internet en stelt klanten in staat om ' tunnel-informatie veilig ' te maken binnen een versleutelde koppeling tussen het netwerk van de klant en Azure. Site-naar-site-VPN is een veilige, rijpere technologie die is geïmplementeerd door ondernemingen van elke omvang voor tien tallen. De IPsec-tunnel modus wordt in deze optie als een versleutelings mechanisme gebruikt.

   Omdat verkeer binnen de VPN-tunnel via internet met een site-naar-site-VPN gaat, biedt micro soft een andere, nog veiliger verbindings optie. Azure ExpressRoute is een specifieke WAN-verbinding tussen Azure en een on-premises locatie of een Exchange-hosting provider. Omdat ExpressRoute-verbindingen niet via internet werken, bieden deze verbindingen meer betrouw baarheid, hogere snelheden, lagere latenties en een betere beveiliging dan typische verbindingen via internet. Omdat dit een directe verbinding is tussen de telecom municatie-provider van de klant, worden de gegevens niet via internet verzonden en worden ze daarom niet blootgesteld aan IT.

   Aanbevolen procedures voor het implementeren van een veilig hybride netwerk dat een on-premises netwerk uitbreidt naar Azure, zijn [beschikbaar](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

#### <a name="implement-azure-active-directory-oidc"></a>Azure Active Directory OIDC implementeren

1. Gebruik deze git-opdracht om de opslag plaats van de bron code te klonen

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>De omleidings-Url's bijwerken
1.  Ga terug naar de Azure Portal. Selecteer in het navigatie deel venster aan de linkerkant de Azure Active Directory-service en selecteer vervolgens App-registraties.
2.  In het scherm resultd, selecteert u de toepassing WebApp-OpenIDConnect-DotNet-code-v2.
3.  Selecteer in het tabblad Verificatie van de sectie omleidings-Uri's de optie Web in de keuze lijst met invoervak en voeg de volgende omleidings-Uri's toe.
    `https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net`
    `https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc`
    o in de sectie Geavanceerde instellingen stel de URL voor afmelden in op`https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc`
4.  Werk op het tabblad branding de URL van de start pagina bij, bijvoorbeeld `https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net`op het adres van uw app service.
        o de configuratie opslaan.
5.  Als uw toepassing een web-API aanroept, moet u ervoor zorgen dat u de benodigde wijzigingen toepast op het project appSettings. json, zodat het de URL van de gepubliceerde API aanroept in plaats van localhost.
Het voor beeld publiceren
    1.  Op het tabblad Overzicht van de App Service downloadt u het publicatie profiel door te klikken op de koppeling publicatie profiel ophalen en op te slaan. Andere implementatie mechanismen, zoals van broncode beheer, kunnen ook worden gebruikt.
    2.  Schakel over naar Visual Studio en ga naar het WebApp-OpenIDConnect-DotNet-code-v2-project. Klik met de rechter muisknop op het project in de Solution Explorer en selecteer publiceren. Klik op profiel importeren op de onderste balk en importeer het publicatie profiel dat u eerder hebt gedownload.
    3.  Klik op configureren en werk op het tabblad verbinding de doel-URL bij, zodat deze een HTTPS is in de URL van de start pagina `https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net`, bijvoorbeeld. Klik op Volgende.
    4.  Controleer op het tabblad instellingen of organisatie verificatie inschakelen niet is geselecteerd. Klik op Opslaan. Klik op het hoofd scherm op publiceren.
    5.  Visual Studio publiceert het project en opent automatisch een browser naar de URL van het project. Als u de standaard webpagina van het project ziet, is de publicatie geslaagd.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Multi-Factor Authentication implementeren voor Azure Active Directory
   Beheerders moeten ervoor zorgen dat de abonnements accounts in de portal zijn beveiligd. Het abonnement is kwetsbaar voor aanvallen omdat het de resources beheert die u hebt gemaakt. Als u het abonnement wilt beveiligen, schakelt u Multi-Factor Authentication in op het tabblad **Azure Active Directory** van het abonnement.

   Een Azure AD werkt op basis van beleids regels die worden toegepast op een gebruiker of groepen gebruikers die aan een bepaald criterium voldoen.
Er wordt door Azure een standaard beleid gemaakt om aan te geven dat beheerders twee ledige verificatie nodig hebben om zich aan te melden bij de portal.
Nadat u dit beleid hebt ingeschakeld, wordt u mogelijk gevraagd om u af te melden en weer aan te melden bij de Azure Portal.

MFA inschakelen voor beheerders aanmeldingen

   1. Ga naar het tabblad **Azure Active Directory** in het Azure Portal
   2. Onder de categorie beveiliging selecteert u voorwaardelijke toegang. Dit scherm wordt weer gegeven

       ![Voorwaardelijke toegang-beleids regels](./media/secure-aad-app/ad-mfa-conditional-add.png)

Als u geen nieuw beleid kunt maken

   1. Ga naar het tabblad **MFA** .
   2. Selecteer een Azure AD Premium **gratis proef versie** om u te abonneren op de gratis proef versie.

   ![Een gratis proef versie van Azure AD Premium](./media/secure-aad-app/ad-trial-premium.png)

Ga terug naar het scherm voorwaardelijke toegang.

   1. Selecteer het tabblad Nieuw beleid.
   2. Voer de naam van het beleid in.
   3. Selecteer de gebruikers of groepen waarvoor u MFA wilt inschakelen.
   4. Selecteer onder **besturings elementen voor toegang**het tabblad **Grant** en selecteer vervolgens **multi-factor Authentication vereisen** (en andere instellingen indien gewenst).

   ![MFA vereisen](./media/secure-aad-app/ad-mfa-conditional-add.png)

   U kunt het beleid inschakelen door het selectie vakje boven aan het scherm te selecteren of op het tabblad **voorwaardelijke toegang** . Wanneer het beleid is ingeschakeld, hebben gebruikers MFA nodig om zich aan te melden bij de portal.

   Er is een basislijn beleid waarvoor MFA is vereist voor alle Azure-beheerders. U kunt deze direct inschakelen in de portal. Als u dit beleid inschakelt, wordt de huidige sessie mogelijk ongeldig en wordt u afgedwongen om u opnieuw aan te melden.

   Als het basislijn beleid niet is ingeschakeld
   1.   Selecteer **MFA vereisen voor beheerders**.
   2.   Selecteer **beleid direct gebruiken**.

   ![Selecteer beleid direct gebruiken](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Azure Sentinel gebruiken om apps en resources te bewaken

   Naarmate een toepassing groeit, is het lastig om alle beveiligings signalen en metrische gegevens die van resources zijn ontvangen samen te voegen en ze nuttig te maken op een op actie gerichte manier.

   Azure Sentinel is ontworpen voor het verzamelen van gegevens, het detecteren van mogelijke soorten bedreigingen en het geven van inzicht in beveiligings incidenten.
Hoewel er wordt gewacht op hand matige interventie, kan Azure Sentinel afhankelijk zijn van vooraf geschreven playbooks om waarschuwingen en incident beheer processen te starten.

   De voor beeld-app bestaat uit verschillende bronnen die door Azure Sentinel kunnen worden bewaakt.
Als u Azure Sentinel wilt instellen, moet u eerst een Log Analytics-werk ruimte maken waarin alle verzamelde gegevens van de verschillende bronnen worden opgeslagen.

Deze werk ruimte maken

   1. Zoek in het zoekvak in het Azure Portal naar **log Analytics**. Selecteer **log Analytics-werk ruimten**.

   ![Zoeken naar Log Analytics-werk ruimten](./media/secure-aad-app/sentinel-log-analytics.png)

   *Zoeken naar Log Analytics-werk ruimten*

   2. Selecteer op de volgende pagina **toevoegen** en geef vervolgens een naam, resource groep en locatie op voor de werk ruimte.
   ![Een Log Analytics-werkruimte maken](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *Een Log Analytics-werkruimte maken*

   3. Gebruik het zoekvak om te zoeken naar **Azure Sentinel**.

   ![Zoeken naar Azure Sentinel](./media/secure-aad-app/sentinel-add.png)

   *Zoeken naar Azure Sentinel*

   4. Selecteer **toevoegen** en selecteer vervolgens de log Analytics werk ruimte die u eerder hebt gemaakt.

   ![Een Log Analytics-werk ruimte toevoegen](./media/secure-aad-app/sentinel-workspace-add.png)

   *Een Log Analytics-werk ruimte toevoegen*

   5. Op de **Azure Sentinel-data connectors-** pagina onder **configuratie**selecteert u **gegevens connectors**. U ziet een matrix met Azure-Services die u kunt koppelen aan het Log Analytics Storage-exemplaar voor analyse in azure Sentinel.

   ![Log Analytics gegevens connectors](./media/secure-aad-app/sentinel-connectors.png)

      *Een gegevens connector toevoegen aan Azure Sentinel*

   Als u bijvoorbeeld verbinding wilt maken met de toepassings gateway, voert u de volgende stappen uit:

   1. Open de Blade Azure-toepassing gateway-exemplaar.
   2. Selecteer **Diagnostische instellingen** onder **Controle**.
   3. Selecteer **Diagnostische instelling toevoegen**.

   ![Application Gateway diagnostische gegevens toevoegen](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *Application Gateway diagnostische gegevens toevoegen*

   4. Selecteer op de pagina **Diagnostische instellingen** de log Analytics werk ruimte die u hebt gemaakt en selecteer vervolgens alle metrische gegevens die u wilt verzamelen en verzenden naar Azure Sentinel. Selecteer **Opslaan**.

   ![Instellingen voor de Azure Sentinel connector](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>Kostenoverwegingen
   Als u nog geen Azure-account hebt, kunt u een gratis versie maken. Ga naar de [pagina gratis account](https://azure.microsoft.com/free/) om aan de slag te gaan. Zie wat u kunt doen met een gratis Azure-account en ontdek welke producten gedurende 12 maanden gratis zijn.

   Als u de resources in de voor beeld-app wilt implementeren met behulp van de beveiligings functies, moet u betalen voor een aantal Premium-functies. Wanneer de app wordt geschaald en de gratis lagen en tests die door Azure worden aangeboden, moeten worden bijgewerkt om te voldoen aan de toepassings vereisten, kunnen uw kosten toenemen. Gebruik de [prijs calculator](https://azure.microsoft.com/pricing/calculator/) van Azure om uw kosten te schatten.

## <a name="next-steps"></a>Volgende stappen
   De volgende artikelen kunnen u helpen bij het ontwerpen, ontwikkelen en implementeren van beveiligde toepassingen.

- [Ontwerpen](secure-design.md)
- [Ontwikkelen](secure-develop.md)
- [Implementeren](secure-deploy.md)
