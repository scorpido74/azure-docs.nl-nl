---
title: 'Een computer verbinden met een virtueel netwerk met point-to-site- en RADIUS-verificatie: PowerShell | Azure'
description: Sluit Windows- en Mac OS X-clients veilig aan op een virtueel netwerk met Behulp van P2S- en RADIUS-verificatie.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: cb9a02532c3651aca544ed946f40bdcff9e9be83
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411777"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Een Point-to-Site-verbinding met een VNet configureren met RADIUS-verificatie: PowerShell

In dit artikel ziet u hoe u een VNet maakt met een Point-to-Site-verbinding die RADIUS-verificatie gebruikt. Deze configuratie is alleen beschikbaar voor het implementatiemodel van Resource Manager.

Met een punt-naar-site-VPN-gateway (P2S) kunt u vanaf een afzonderlijke clientcomputer een beveiligde verbinding maken met uw virtuele netwerk. Point-to-Site VPN-verbindingen zijn handig wanneer u verbinding wilt maken met uw VNet vanaf een externe locatie, bijvoorbeeld wanneer u thuis of een conferentie aan het telewerken bent. Een P2S-VPN is ook een uitstekende oplossing in plaats van een site-naar-site-VPN wanneer u maar een paar clients hebt die verbinding moeten maken met een VNet.

Er wordt een P2S-VPN-verbinding gestart vanaf Windows en Mac-apparaten. Clients die verbinding maken, kunnen de volgende verificatiemethoden gebruiken: 

* RADIUS-server
* VPN Gateway native certificaatverificatie

Met dit artikel u een P2S-configuratie configureren met verificatie met behulp van DE RADIUS-server. Zie [Een Point-to-Site-verbinding configureren met een VNet met behulp van VPN-gateway native certificate authentication](vpn-gateway-howto-point-to-site-rm-ps.md)als u wilt verifiëren met behulp van gegenereerde certificaten en vpn-gateway native certificaatverificatie.

![Verbindingsdiagram - RADIUS](./media/point-to-site-how-to-radius-ps/p2sradius.png)

Punt-naar-site-verbindingen hebben geen VPN-apparaat of openbaar IP-adres nodig. P2S maakt de VPN-verbinding via SSTP (Secure Socket Tunneling Protocol), OpenVPN of IKEv2.

* SSTP is een TLS-gebaseerde VPN-tunnel die alleen wordt ondersteund op Windows-clientplatforms. Omdat met SSTP firewalls kunnen worden gepasseerd, is dit een ideale optie om vanaf elke locatie verbinding te maken met Azure. Wij ondersteunen SSTP versies 1.0, 1.1 en 1.2 aan de serverzijde. De client besluit welke versie moet worden gebruikt. Voor Windows 8.1 en hoger, gebruikt SSTP standaard 1.2.

* OpenVPN® Protocol, een SSL/TLS gebaseerd VPN-protocol. Een TLS VPN-oplossing kan firewalls binnendringen, omdat de meeste firewalls TCP-poort 443 uitgaand openen, die TLS gebruikt. OpenVPN kan worden gebruikt om verbinding te maken vanaf Android, iOS (versies 11.0 en hoger), Windows, Linux en Mac-apparaten (OSX-versies 10.13 en hoger).

* IKEv2 VPN, een op standaarden gebaseerde IPsec VPN-oplossing. IKEv2 VPN kan worden gebruikt om verbinding te maken vanaf Mac-apparaten (OSX-versie 10.11 en hoger).

Voor P2S-verbindingen is het volgende vereist:

* Een RouteBased VPN-gateway. 
* Een RADIUS-server voor het verwerken van gebruikersverificatie. De RADIUS-server kan on-premises of in het Azure VNet worden geïmplementeerd.
* Een VPN-clientconfiguratiepakket voor de Windows-apparaten die verbinding maken met het VNet. Een VPN-clientconfiguratiepakket biedt de instellingen die nodig zijn voor een VPN-client om verbinding te maken via P2S.

## <a name="about-active-directory-ad-domain-authentication-for-p2s-vpns"></a><a name="aboutad"></a>Over Ad-domeinverificatie (Active Directory) voor P2S-VPN's

Met verificatie van AD-domein kunnen gebruikers zich aanmelden bij Azure met behulp van hun domeinreferenties voor de organisatie. Hiervoor is een RADIUS-server nodig die integreert met de AD-server. Organisaties kunnen ook gebruikmaken van hun bestaande RADIUS-implementatie.
 
De RADIUS-server kan zich on-premises of in uw Azure VNet bevinden. Tijdens de verificatie fungeert de VPN-gateway als een doorgeef- en doorsturen van verificatieberichten heen en weer tussen de RADIUS-server en het verbindingsapparaat. Het is belangrijk dat de VPN-gateway de RADIUS-server kan bereiken. Als de RADIUS-server zich on-premises bevindt, is een VPN-site-naar-siteverbinding van Azure naar de on-premises site vereist.

Naast Active Directory kan een RADIUS-server ook integreren met andere externe identiteitssystemen. Dit opent tal van verificatieopties voor Point-to-Site VPN's, waaronder MFA-opties. Controleer de documentatie van uw RADIUS-serverleverancier om de lijst met identiteitssystemen te krijgen waarmee deze is geïntegreerd.

![Verbindingsdiagram - RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Alleen een VPN Site-to-Site-verbinding kan worden gebruikt voor on-premises verbinding maken met een RADIUS-server. Een ExpressRoute-verbinding kan niet worden gebruikt.
>
>

## <a name="before-beginning"></a><a name="before"></a>Voordat u begint

Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial).

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>Voorbeeldwaarden

U kunt de volgende voorbeeldwaarden gebruiken om een testomgeving te maken of ze raadplegen om meer inzicht te krijgen in de voorbeelden in dit artikel. U kunt de stappen gebruiken als een overzicht en de waarden ongewijzigd gebruiken, of u kunt ze wijzigen zodat ze overeenkomen met uw omgeving.

* **Naam: VNet1**
* **Adresruimte: 192.168.0.0/16** en **10.254.0.0/16**<br>Voor dit voorbeeld gebruiken we meer dan één adresruimte om te verduidelijken dat deze configuratie met meerdere adresruimten werkt. Meerdere adresruimten zijn echter niet vereist voor deze configuratie.
* **Subnetnaam: FrontEnd**
  * **Subnetadresbereik: 192.168.1.0/24**
* **Subnetnaam: BackEnd**
  * **Subnetadresbereik: 10.254.1.0/24**
* **Subnetnaam: GatewaySubnet**<br>De naam van het subnet *GatewaySubnet* is verplicht voor een goede werking van de VPN-gateway.
  * **Adresbereik GatewaySubnet: 192.168.200.0/24** 
* **VPN-clientadrespool: 172.16.201.0/24**<br>VPN-clients die verbinding maken met het VNet via deze punt-naar-site-verbinding, ontvangen een IP-adres van de VPN-clientadresgroep.
* **Abonnement:** controleer of u het juiste abonnement hebt in het geval u er meer dan één hebt.
* **Resourcegroep: TestRG**
* **Locatie: Oost-VS**
* **DNS Server: IP-adres** van de DNS-server die u wilt gebruiken voor naamomzetting voor uw VNet. (optioneel)
* **Gatewaynaam: Vnet1GW**
* **Openbare IP-naam: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="1-set-the-variables"></a><a name="signin"></a>1. De variabelen instellen

Declareer de waarden die u wilt gebruiken. Gebruik het volgende voorbeeld, en vervang zo nodig de waarden door uw eigen waarden. Als u uw PowerShell/Cloud Shell-sessie op enig moment tijdens de oefening sluit, kopieert en plakt u de waarden opnieuw om de variabelen opnieuw te declareren.

  ```azurepowershell-interactive
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="2-create-the-resource-group-vnet-and-public-ip-address"></a>2. <a name="vnet"> </a>De resourcegroep, VNet en openbaar IP-adres maken

Met de volgende stappen wordt een resourcegroep en een virtueel netwerk in de resourcegroep met drie subnetten gemaakt. Bij het vervangen van waarden is het belangrijk dat u altijd uw gateway subnet specifiek 'GatewaySubnet' noemt. Als u het iets anders noemt, mislukt het maken van uw gateway;

1. Maak een resourcegroep.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name "TestRG" -Location "East US"
   ```
2. Maak de subnetconfiguraties voor het virtuele netwerk, noem deze *FrontEnd*, *BackEnd* en *GatewaySubnet*. Deze voorvoegsels moeten deel uitmaken van de VNet-adresruimte die u hebt opgegeven.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
   $besub = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
   ```
3. Maak het virtuele netwerk.

   In dit voorbeeld is de serverparameter-DnsServer optioneel. Het opgeven van een waarde betekent niet dat er een nieuwe DNS-server wordt gemaakt. Het IP-adres van de DNS-server dat u opgeeft, moet het adres zijn van een DNS-server die de namen kan omzetten voor de resources waarmee u verbinding maakt vanuit uw VNet. Voor dit voorbeeld hebben we een privé-IP-adres gebruikt, maar het is zeer onwaarschijnlijk dat dit het IP-adres van uw DNS-server is. Zorg ervoor dat u uw eigen waarden gebruikt. De opgegeven waarde wordt gebruikt door de resources die u implementeert naar het VNet, niet door de P2S-verbinding.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. Een VPN Gateway moet een openbaar IP-adres hebben. U vraagt eerst de resource van het IP-adres aan en verwijst hier vervolgens naar bij het maken van uw virtuele netwerkgateway. Het IP-adres wordt dynamisch aan de resource toegewezen wanneer de VPN Gateway wordt gemaakt. VPN Gateway ondersteunt momenteel alleen *dynamische* toewijzing van openbare IP-adressen. U kunt geen toewijzing van een statisch openbaar IP-adres aanvragen. Dit betekent echter niet dat het IP-adres wordt gewijzigd nadat het aan uw VPN Gateway is toegewezen. Het openbare IP-adres verandert alleen wanneer de gateway wordt verwijderd en opnieuw wordt gemaakt. Het verandert niet wanneer de grootte van uw VPN Gateway verandert, wanneer deze gateway opnieuw wordt ingesteld of wanneer andere interne onderhoudswerkzaamheden of upgrades worden uitgevoerd.

   Geef de variabelen op om een dynamisch toegewezen openbaar IP-adres op te vragen.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
   $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="3-set-up-your-radius-server"></a>3. <a name="radius"> </a>Uw RADIUS-server instellen

Voordat u de virtuele netwerkgateway maakt en configureert, moet uw RADIUS-server correct zijn geconfigureerd voor verificatie.

1. Als u geen RADIUS-server hebt geïmplementeerd, implementeert u deze. Raadpleeg voor implementatiestappen de installatiehandleiding van uw RADIUS-leverancier.  
2. Configureer de VPN-gateway als een RADIUS-client op de RADIUS. Wanneer u deze RADIUS-client toevoegt, geeft u het virtuele netwerk GatewaySubnet op dat u hebt gemaakt. 
3. Zodra de RADIUS-server is ingesteld, krijgt u het IP-adres van de RADIUS-server en het gedeelde geheim dat RADIUS-clients moeten gebruiken om met de RADIUS-server te praten. Als de RADIUS-server zich in azure VNet bevindt, gebruikt u het CA-IP van de VM RADIUS-server.

Het [NPS-artikel (Network Policy Server)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) biedt richtlijnen voor het configureren van een Windows RADIUS-server (NPS) voor VERIFICATIE van AD-domein.

## <a name="4-create-the-vpn-gateway"></a>4. <a name="creategw"> </a>Maak de VPN-gateway

Configureer en maak de VPN-gateway voor uw VNet.

* De -GatewayType moet 'Vpn' zijn en het VpnType moet 'RouteBased' zijn.
* Een VPN-gateway kan tot 45 minuten in beslag nemen, afhankelijk van de [gateway SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku) die u selecteert.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## <a name="5-add-the-radius-server-and-client-address-pool"></a>5. <a name="addradius"> </a>De GROEP RADIUS-server en clientadres toevoegen
 
* De -RadiusServer kan worden opgegeven op naam of op IP-adres. Als u de naam opgeeft en de server on-premises is, kan de VPN-gateway de naam mogelijk niet oplossen. Als dat het geval is, is het beter om het IP-adres van de server op te geven. 
* De -RadiusSecret moet overeenkomen met wat is geconfigureerd op uw RADIUS-server.
* De VpnClientAddressPool is het bereik van waaruit de verbindende VPN-clients een IP-adres ontvangen.Gebruik een privé-IP-adresbereik dat niet overlapt met de on-premises locatie waarvanaf u verbinding maakt of met het VNet waarmee u verbinding wilt maken. Zorg ervoor dat u een voldoende grote adresgroep hebt geconfigureerd.  

1. Maak een veilige tekenreeks voor het RADIUS-geheim.

   ```azurepowershell-interactive
   $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
   ```

2. U wordt gevraagd om het RADIUS-geheim in te voeren. De tekens die u invoert, worden niet weergegeven en worden in plaats daarvan vervangen door het teken "*".

   ```azurepowershell-interactive
   RadiusSecret:***
   ```
3. Voeg de VPN-clientadresgroep en de RADIUS-servergegevens toe.

   Voor SSTP-configuraties:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Voor OpenVPN® configuraties:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "OpenVPN" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```


   Voor IKEv2-configuraties:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Voor SSTP + IKEv2

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

## <a name="6-download-the-vpn-client-configuration-package-and-set-up-the-vpn-client"></a>6. <a name="vpnclient"> </a>Download het VPN-clientconfiguratiepakket en stel de VPN-client in

Met de VPN-clientconfiguratie kunnen apparaten verbinding maken met een VNet via een P2S-verbinding.Zie [Een VPN-clientconfiguratievoor RADIUS-verificatie](point-to-site-vpn-client-configuration-radius.md)maken als u een VPN-clientconfiguratie wilt genereren en de VPN-client wilt instellen.

## <a name="7-connect-to-azure"></a><a name="connect"></a>7. Verbinding maken met Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Verbinding maken vanaf een Windows-VPN-client

1. Als u met uw VNet wilt verbinden, gaat u op de clientcomputer naar de VPN-verbindingen en zoekt u de VPN-verbinding die u hebt gemaakt. Deze heeft dezelfde naam als het virtuele netwerk. Voer uw domeinreferenties in en klik op 'Verbinden'. Er verschijnt een pop-upbericht waarin verhoogde rechten worden aangevraagd. Accepteer het en voer de referenties in.

   ![VPN-client maakt verbinding met Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. De verbinding is tot stand gebracht.

   ![Verbinding tot stand gebracht](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Verbinding maken vanaf een Mac VPN-client

Zoek in het dialoogvenster Netwerk het clientprofiel dat u wilt gebruiken en klik op **Verbinding maken**.

  ![Mac-verbinding](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-your-connection"></a><a name="verify"></a>De verbinding verifiëren

1. Als u wilt controleren of uw VPN-verbinding actief is, opent u een opdrachtprompt met verhoogde bevoegdheid en voert u *ipconfig/all* in.
2. Bekijk de resultaten. Het IP-adres dat u hebt ontvangen is een van de adressen binnen het adresbereik van de punt-naar-site-VPN-clientadresgroep die u hebt opgegeven in uw configuratie. De resultaten zijn vergelijkbaar met het volgende voorbeeld:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

Zie [Azure point-to-site-verbindingen oplossen](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)als u een P2S-verbinding wilt oplossen.

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Verbinding maken met een virtuele machine

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a><a name="faq"></a>Veelgestelde vragen

Deze veelgestelde vragen is van toepassing op P2S met RADIUS-verificatie

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Volgende stappen

Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Virtuele machines](https://docs.microsoft.com/azure/) voor meer informatie. Zie [Azure and Linux VM Network Overview](../virtual-machines/linux/azure-vm-network-overview.md) (Overzicht van Azure- en Linux-VM-netwerken) voor meer informatie over netwerken en virtuele machines.
