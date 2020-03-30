---
title: Migreren van v1 naar v2 - Azure Application Gateway
description: In dit artikel ziet u hoe u Azure Application Gateway en Web Application Firewall migreert van v1 naar v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 9909c46015fffb3bea3eef094599312e28b935c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77046203"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Azure Application Gateway en Web Application Firewall migreren van v1 naar v2

[Azure Application Gateway en Web Application Firewall (WAF) v2](application-gateway-autoscaling-zone-redundant.md) is nu beschikbaar, met extra functies zoals automatisch schalen en redundantie van de beschikbaarheidszone. Bestaande V1-gateways worden echter niet automatisch bijgewerkt naar V2. Als u wilt migreren van v1 naar v2, volgt u de stappen in dit artikel.

Er zijn twee fasen in een migratie:

1. De configuratie migreren
2. Het clientverkeer migreren

Dit artikel behandelt configuratiemigratie. De migratie van het clientverkeer is afhankelijk van uw specifieke omgeving. Er worden echter enkele algemene aanbevelingen op hoog niveau [gedaan](#migrate-client-traffic).

## <a name="migration-overview"></a>Overzicht migratie

Er is een Azure PowerShell-script beschikbaar dat het volgende doet:

* Hiermee maakt u een nieuwe Standard_v2 of WAF_v2 gateway in een virtueel netwerksubnet dat u opgeeft.
* Kopieert naadloos de configuratie die is gekoppeld aan de v1-standaard- of WAF-gateway naar de nieuw gemaakte Standard_V2 of WAF_V2 gateway.

### <a name="caveatslimitations"></a>Kanttekeningen\Beperkingen

* De nieuwe v2-gateway heeft nieuwe openbare en private IP-adressen. Het is niet mogelijk om de IP-adressen die zijn gekoppeld aan de bestaande v1-gateway naadloos naar v2 te verplaatsen. U echter een bestaand (niet-toegewezen) openbaar of privé IP-adres toewijzen aan de nieuwe v2-gateway.
* U moet een IP-adresruimte opgeven voor een ander subnet binnen uw virtuele netwerk waar uw v1-gateway zich bevindt. Het script kan de v2-gateway niet maken in bestaande subnetten die al een v1-gateway hebben. Als het bestaande subnet echter al een v2-gateway heeft, kan dat nog steeds werken op voorwaarde dat er voldoende IP-adresruimte is.
* Als u een SSL-configuratie wilt migreren, moet u alle SSL-cert's opgeven die in uw v1-gateway worden gebruikt.
* Als u de FIPS-modus hebt ingeschakeld voor uw V1-gateway, wordt deze niet gemigreerd naar uw nieuwe v2-gateway. FIPS-modus wordt niet ondersteund in v2.
* v2 ondersteunt geen IPv6, dus V1-gateways met IPv6 zijn niet gemigreerd. Als u het script uitvoert, kan het niet worden voltooid.
* Als de v1-gateway alleen een privé-IP-adres heeft, wordt in het script een openbaar IP-adres en een privé-IP-adres voor de nieuwe v2-gateway gemaakt. v2-gateways ondersteunen momenteel niet alleen privé-IP-adressen.

## <a name="download-the-script"></a>Het script downloaden

Download het migratiescript uit de [PowerShell-galerie](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Het script gebruiken

Er zijn twee opties voor u, afhankelijk van de installatie en voorkeuren van uw lokale PowerShell-omgeving:

* Als u de Azure Az-modules niet hebt geïnstalleerd of het niet erg vindt om de `Install-Script` Azure Az-modules te verwijderen, u de optie het beste gebruiken om het script uit te voeren.
* Als u de Azure Az-modules moet behouden, u het beste het script downloaden en het rechtstreeks uitvoeren.

Voer de Azure Az-modules uit `Get-InstalledModule -Name az`om te bepalen of u de Azure Az-modules hebt geïnstalleerd. Als u geen geïnstalleerde Az-modules ziet, kunt `Install-Script` u de methode gebruiken.

### <a name="install-using-the-install-script-method"></a>Installeren met de methode Install-Script

Als u deze optie wilt gebruiken, mag u de Azure Az-modules niet op uw computer hebben geïnstalleerd. Als ze zijn geïnstalleerd, wordt in de volgende opdracht een fout weergegeven. U de Azure Az-modules verwijderen of de andere optie gebruiken om het script handmatig te downloaden en uit te voeren.
  
Voer het script uit met de volgende opdracht:

`Install-Script -Name AzureAppGWMigration`

Deze opdracht installeert ook de vereiste Az-modules.  

### <a name="install-using-the-script-directly"></a>Direct installeren met behulp van het script

Als u sommige Azure Az-modules hebt geïnstalleerd en deze niet verwijderen (of deze niet wilt verwijderen), u het script handmatig downloaden via het tabblad **Handmatig downloaden** in de downloadkoppeling voor scripts. Het script wordt gedownload als een ruw nupkg-bestand. Zie Handmatig pakket downloaden als u het script vanuit dit nupkg-bestand wilt [installeren.](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)

Het script uitvoeren:

1. Gebruiken `Connect-AzAccount` om verbinding te maken met Azure.

1. Gebruik `Import-Module Az` om de Az-modules te importeren.

1. Voer `Get-Help AzureAppGWMigration.ps1` uit om de vereiste parameters te onderzoeken:

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   Parameters voor het script:
   * **resourceId: [String]: Vereist** - Dit is de Azure Resource ID voor uw bestaande Standaard v1- of WAF v1-gateway. Als u deze tekenreekswaarde wilt vinden, navigeert u naar de Azure-portal, selecteert u de toepassingsgateway of De WAF-bron en klikt u op de koppeling **Eigenschappen** voor de gateway. De resource-id bevindt zich op die pagina.

     U ook de volgende Azure PowerShell-opdrachten uitvoeren om de Resource-id op te halen:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [Tekenreeks]: vereist** - Dit is de IP-adresruimte die u hebt toegewezen (of wilt toewijzen) voor een nieuw subnet dat uw nieuwe v2-gateway bevat. Dit moet worden gespecificeerd in de CIDR-notatie. Bijvoorbeeld: 10.0.0.0/24. U hoeft dit subnet niet van tevoren te maken. Het script maakt het voor u als het niet bestaat.
   * **appgwName: [Tekenreeks]: optioneel**. Dit is een tekenreeks die u wilt gebruiken als de naam voor de nieuwe Standard_v2 of WAF_v2 gateway. Als deze parameter niet wordt geleverd, wordt de naam van uw bestaande v1-gateway gebruikt met het achtervoegsel *_v2* toegevoegd.
   * **sslCertificaten: [PSApplicationGatewaySslCertificate]: optioneel**.  Een door komma's gescheiden lijst met PSApplicationGatewaySslCertificate-objecten die u maakt om de SSL-cert's van uw v1-gateway weer te geven, moet worden geüpload naar de nieuwe v2-gateway. Voor elk van uw SSL-certs die zijn geconfigureerd voor uw Standaard v1- of WAF `New-AzApplicationGatewaySslCertificate` v1-gateway, u een nieuw PSApplicationGatewaySslCertificate-object maken via de opdracht die hier wordt weergegeven. U hebt het pad naar uw SSL-cert-bestand en het wachtwoord nodig.

     Deze parameter is alleen optioneel als u geen HTTPS-listeners hebt geconfigureerd voor uw v1-gateway of WAF. Als u ten minste één HTTPS-listener hebt ingesteld, moet u deze parameter opgeven.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     U `$mySslCert1, $mySslCert2` in het vorige voorbeeld (komma-gescheiden) als waarden voor deze parameter in het script worden doorgegeven.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: optioneel**. Een door komma's gescheiden lijst met PSApplicationGatewayTrustedRootCertificate-objecten die u maakt om de [Trusted Root-certificaten](ssl-overview.md) weer te geven voor verificatie van uw backend-exemplaren van uw v2-gateway.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      Zie [Nieuw-AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0)als u een lijst met PSApplicationGatewayTrustedRootCertificate-objecten wilt maken.
   * **privateIpAddress: [Tekenreeks]: optioneel**. Een specifiek privé-IP-adres dat u wilt koppelen aan uw nieuwe v2-gateway.  Dit moet van dezelfde VNet zijn die u toewijst voor uw nieuwe v2-gateway. Als dit niet is opgegeven, wijst het script een privé-IP-adres toe voor uw v2-gateway.
   * **publicIpResourceId: [Tekenreeks]: optioneel**. De resourceId van bestaande openbare IP-adres (standaard SKU)-bron in uw abonnement die u wilt toewijzen aan de nieuwe v2-gateway. Als dit niet is opgegeven, wordt in het script een nieuw openbaar IP toegewezen in dezelfde resourcegroep. De naam is de naam van de v2-gateway met *-IP* toegevoegd.
   * **validateMigratie: [switch]: optioneel**. Gebruik deze parameter als u wilt dat het script een aantal basisconfiguratievergelijkingsvalidaties doet na het maken van de V2-gateway en de configuratiekopie. Standaard wordt er geen validatie uitgevoerd.
   * **inschakelenAutoScale: [switch]: optioneel**. Gebruik deze parameter als u wilt dat het script Automatisch schalen inschakelt op de nieuwe v2-gateway nadat deze is gemaakt. Autoscaling is standaard uitgeschakeld. U deze altijd handmatig inschakelen op de nieuw gemaakte v2-gateway.

1. Voer het script uit met de juiste parameters. Het kan vijf tot zeven minuten duren om te voltooien.

    **Voorbeeld**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Clientverkeer migreren

Controleer eerst of het script een nieuwe v2-gateway heeft gemaakt met de exacte configuratie die is gemigreerd van uw v1-gateway. U dit verifiëren via de Azure-portal.

Stuur ook een kleine hoeveelheid verkeer door de v2-gateway als een handmatige test.
  
Hier volgen een paar scenario's waarin uw huidige toepassingsgateway (Standaard) clientverkeer kan ontvangen en onze aanbevelingen voor elk:

* **Een aangepaste DNS-zone (bijvoorbeeld contoso.com) die verwijst naar het FRONTEnd IP-adres (met behulp van een A-record) die is gekoppeld aan uw Standaard v1- of WAF v1-gateway.**

    U uw DNS-record bijwerken om te wijzen op het ip- of DNS-label van de frontend dat is gekoppeld aan uw Standard_v2-toepassingsgateway. Afhankelijk van de TTL die is geconfigureerd op uw DNS-record, kan het even duren voordat al uw clientverkeer is gemigreerd naar uw nieuwe v2-gateway.
* **Een aangepaste DNS-zone (bijvoorbeeld contoso.com) die verwijst naar het DNS-label *(bijvoorbeeld: myappgw.eastus.cloudapp.azure.com* met behulp van een CNAME-record) die is gekoppeld aan uw v1-gateway.**

   Je hebt twee keuzes:

  * Als u openbare IP-adressen op uw toepassingsgateway gebruikt, u een gecontroleerde, gedetailleerde migratie doen met behulp van een Traffic Manager-profiel om het verkeer (gewogen verkeersrouteringsmethode) stapsgewijs naar de nieuwe v2-gateway te leiden.

    U dit doen door de DNS-labels van zowel de v1- als de V2-toepassingsgateways toe `www.contoso.com`te voegen aan het [Traffic Manager-profiel](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)en uw aangepaste DNS-record (bijvoorbeeld ) toe te voegen aan het domein Traffic Manager (bijvoorbeeld contoso.trafficmanager.net).
  * U ook uw aangepaste DNS-record voor domeinen bijwerken om naar het DNS-label van de nieuwe v2-toepassingsgateway te wijzen. Afhankelijk van de TTL die is geconfigureerd op uw DNS-record, kan het even duren voordat al uw clientverkeer is gemigreerd naar uw nieuwe v2-gateway.
* **Uw clients maken verbinding met het ip-adres van uw toepassinggateway.**

   Werk uw clients bij om het IP-adres(en) te gebruiken dat is gekoppeld aan de nieuw gemaakte v2-toepassingsgateway. We raden u aan ip-adressen niet direct te gebruiken. Overweeg het DNS-naamlabel (bijvoorbeeld yourgateway.eastus.cloudapp.azure.com) te gebruiken dat aan uw toepassingsgateway is gekoppeld, dat u CNAME gebruiken naar uw eigen aangepaste DNS-zone (bijvoorbeeld contoso.com).

## <a name="common-questions"></a>Veelgestelde vragen

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Zijn er beperkingen met het Azure PowerShell-script om de configuratie van v1 naar v2 te migreren?

Ja. Zie [kanttekeningen/beperkingen](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Is dit artikel en het Azure PowerShell-script ook van toepassing op Het WAF-product van Application Gateway? 

Ja.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Schakelt het Azure PowerShell-script ook het verkeer over van mijn v1-gateway naar de nieuw gemaakte v2-gateway?

Nee. Het Azure PowerShell-script migreert alleen de configuratie. Daadwerkelijke verkeersmigratie is uw verantwoordelijkheid en in uw controle.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Is de nieuwe v2-gateway die is gemaakt door het Azure PowerShell-script op de juiste manier geschikt om al het verkeer te verwerken dat momenteel wordt weergegeven door mijn v1-gateway?

Het Azure PowerShell-script maakt een nieuwe v2-gateway met een geschikte grootte om het verkeer op uw bestaande v1-gateway te verwerken. Automatisch schalen is standaard uitgeschakeld, maar u Automatisch schalen inschakelen wanneer u het script uitvoert.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Ik heb mijn v1-gateway geconfigureerd om logboeken naar Azure-opslag te verzenden. Is het script repliceren deze configuratie voor v2 ook?

Nee. Het script repliceert deze configuratie niet voor v2. U moet de logboekconfiguratie afzonderlijk toevoegen aan de gemigreerde v2-gateway.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>Ondersteunt dit script certificaten die zijn geüpload naar Azure KeyVault?

Nee. Momenteel ondersteunt het script geen certificaten in KeyVault. Echter, dit wordt overwogen voor een toekomstige versie.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Ik liep in een aantal problemen met het gebruik van dit script. Hoe kan ik hulp krijgen?
  
U een appgwmigrationsup@microsoft.come-mail verzenden naar, een ondersteuningsaanvraag openen met Azure Support of beide doen.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over Application Gateway v2](application-gateway-autoscaling-zone-redundant.md)
