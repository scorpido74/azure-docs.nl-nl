---
title: 'Azure ExpressRoute: S2S VPN configureren via Microsoft-peering'
description: Configureer IPsec/IKE-connectiviteit met Azure via een ExpressRoute Microsoft-peeringcircuit met behulp van een site-to-site VPN-gateway.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: f3044a2701b0f1cd0e5f9ab3ab60c1d60cfb8f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436816"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Een site-to-site VPN configureren via ExpressRoute Microsoft-peering

Met dit artikel u beveiligde versleutelde connectiviteit tussen uw on-premises netwerk en uw Virtual Networks (Azure) configureren via een privéverbinding met ExpressRoute. U Microsoft-peering gebruiken om een site-to-site IPsec/IKE VPN-tunnel op te zetten tussen uw geselecteerde on-premises netwerken en Azure VNets. Het configureren van een beveiligde tunnel via ExpressRoute zorgt voor gegevensuitwisseling met vertrouwelijkheid, anti-replay, authenticiteit en integriteit.

>[!NOTE]
>Wanneer u site-to-site VPN instelt via Microsoft-peering, worden er kosten in rekening gebracht voor de VPN-gateway en vpn-uitgang. Zie [VPN Gateway-prijzen](https://azure.microsoft.com/pricing/details/vpn-gateway)voor meer informatie.
>
>

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="architecture"></a><a name="architecture"></a>Architectuur


  ![connectiviteitsoverzicht](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


Voor hoge beschikbaarheid en redundantie u meerdere tunnels configureren over de twee MSEE-PE-paren van een ExpressRoute-circuit en taakverdeling tussen de tunnels mogelijk maken.

  ![opties voor hoge beschikbaarheid](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

VPN-tunnels via Microsoft-peering kunnen worden beëindigd via vpn-gateway of met behulp van een geschikt Network Virtual Appliance (NVA) dat beschikbaar is via Azure Marketplace. U routes statisch of dynamisch uitwisselen over de versleutelde tunnels zonder de route-uitwisseling bloot te stellen aan de onderliggende Microsoft-peering. In de voorbeelden in dit artikel wordt BGP (anders dan de BGP-sessie die wordt gebruikt om de Microsoft-peering te maken) gebruikt om dynamisch voorvoegsels uit te wisselen over de versleutelde tunnels.

>[!IMPORTANT]
>Voor de on-premises kant wordt microsoft-peering meestal beëindigd op de DMZ en wordt private peering beëindigd in de kernnetwerkzone. De twee zones zouden worden gescheiden met behulp van firewalls. Als u Microsoft peering uitsluitend configureren voor het inschakelen van beveiligde tunneling via ExpressRoute, vergeet niet om te filteren door alleen de openbare IP's van belang die worden steeds geadverteerd via Microsoft peering.
>
>

## <a name="workflow"></a><a name="workflow"></a>Werkstroom

1. Microsoft-peering configureren voor uw ExpressRoute-circuit.
2. Adverteer geselecteerde azure-regionale voorvoegsels voor uw on-premises netwerk via Microsoft-peering.
3. Een VPN-gateway configureren en IPsec-tunnels instellen
4. Configureer het on-premises VPN-apparaat.
5. Maak de site-to-site IPsec/IKE-verbinding.
6. (Optioneel) Firewalls/filtering configureren op het on-premises VPN-apparaat.
7. Test en valideer de IPsec-communicatie via het ExpressRoute-circuit.

## <a name="1-configure-microsoft-peering"></a><a name="peering"></a>1. Microsoft-peering configureren

Als u een vpn-verbinding van site-to-site wilt configureren via ExpressRoute, moet u gebruikmaken van ExpressRoute Microsoft-peering.

* Als u een nieuw ExpressRoute-circuit wilt configureren, begint u met het artikel [expressroutevereisten](expressroute-prerequisites.md) en [maakt en wijzigt u vervolgens een ExpressRoute-circuit.](expressroute-howto-circuit-arm.md)

* Als u al een ExpressRoute-circuit hebt, maar microsoft-peering niet hebt geconfigureerd, configureert u Microsoft-peering met behulp van het artikel [Peering maken en wijzigen voor een expressroute-circuitartikel.](expressroute-howto-routing-arm.md#msft)

Zodra u uw circuit en Microsoft-peering hebt geconfigureerd, u het eenvoudig bekijken via de **pagina Overzicht** in de Azure-portal.

![Circuit](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="2-configure-route-filters"></a><a name="routefilter"></a>2. Routefilters configureren

Via een routefilter kunt u services identificeren die u wilt gebruiken via Microsoft-peering op uw ExpressRoute-circuit. Het is in wezen een lijst van alle BGP-communitywaarden. 

![routefilter](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

In dit voorbeeld bevindt de implementatie zich alleen in de regio *Azure West US 2.* Er wordt een routefilterregel toegevoegd om alleen de advertentie van regionale voorvoegsels van Azure West US 2 toe te staan, die de BGP-communitywaarde *12076:51026*heeft. U geeft de regionale voorvoegsels op die u wilt toestaan door **regel beheren**te selecteren.

Binnen het routefilter moet u ook de ExpressRoute-circuits kiezen waarvoor het routefilter van toepassing is. U de ExpressRoute-circuits kiezen door **circuit toevoegen te**selecteren. In de vorige afbeelding is het routefilter gekoppeld aan het voorbeeld ExpressRoute-circuit.

### <a name="21-configure-the-route-filter"></a><a name="configfilter"></a>2.1 Het routefilter configureren

Een routefilter configureren. Zie [Routefilters configureren voor Microsoft-peering voor](how-to-routefilter-portal.md)stappen.

### <a name="22-verify-bgp-routes"></a><a name="verifybgp"></a>2.2 BGP-routes verifiëren

Zodra u Microsoft-peering over uw ExpressRoute-circuit hebt gemaakt en een routefilter aan het circuit hebt gekoppeld, u de BGP-routes verifiëren die u van MTE's hebt ontvangen op de PE-apparaten die peeren met de MEE's. De verificatieopdracht is afhankelijk van het besturingssysteem van uw PE-apparaten.

#### <a name="cisco-examples"></a>Cisco voorbeelden

In dit voorbeeld wordt een cisco IOS-XE-opdracht gebruikt. In het voorbeeld wordt een instantie voor virtuele routering en doorsturen (VRF) gebruikt om het peeringverkeer te isoleren.

```
show ip bgp vpnv4 vrf 10 summary
```

Uit de volgende gedeeltelijke uitvoer blijkt dat er \*68 voorvoegsels zijn ontvangen van de buurman .243.229.34 met de ASN 12076 (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Als u de lijst met voorvoegsels wilt bekijken die van de buurman zijn ontvangen, gebruikt u het volgende voorbeeld:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Als u wilt bevestigen dat u de juiste set voorvoegsels ontvangt, u deze kruisverificatie controleren. In de volgende Azure PowerShell-opdrachtuitvoer worden de voorvoegsels weergegeven die via Microsoft-peering worden geadverteerd voor elk van de services en voor elk van de Azure-regio:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```

## <a name="3-configure-the-vpn-gateway-and-ipsec-tunnels"></a><a name="vpngateway"></a>3. Configureer de VPN-gateway- en IPsec-tunnels

In deze sectie worden VPN-tunnels van IPsec gemaakt tussen de Azure VPN-gateway en het on-premises VPN-apparaat. De voorbeelden maken gebruik van Cisco Cloud Service Router (CSR1000) VPN-apparaten.

Het volgende diagram toont de VPN-tunnels van IPsec die zijn ingesteld tussen on-premises VPN-apparaat 1 en het azure VPN-gateway-instantiepaar. De twee IPsec VPN-tunnels die zijn ingesteld tussen het on-premises VPN-apparaat 2 en het Azure VPN-gateway-instantiepaar, worden niet geïllustreerd in het diagram en de configuratiedetails worden niet vermeld. Het hebben van extra VPN-tunnels verbetert echter de hoge beschikbaarheid.

  ![VPN-tunnels](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Via het IPsec-tunnelpaar wordt een eBGP-sessie opgezet om privénetwerkroutes uit te wisselen. In het volgende diagram ziet u de eBGP-sessie die is ingesteld over het IPsec-tunnelpaar:

  ![eBGP-sessies over tunnelpaar](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

In het volgende diagram ziet u het geabstraheerde overzicht van het voorbeeldnetwerk:

  ![voorbeeld netwerk](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Voorbeelden van de sjabloon Azure Resource Manager

In de voorbeelden worden de VPN-gateway en de IPsec-tunnelbeëindigingen geconfigureerd met behulp van een Azure Resource Manager-sjabloon. Zie [De structuur en de syntaxis van Azure Resource Manager-sjablonen begrijpen](../azure-resource-manager/templates/template-syntax.md)als u nieuw bent in het gebruik van Resource Manager-sjablonen of om de basisbeginselen van de resourcebeheersjabloon te begrijpen. De sjabloon in deze sectie maakt een greenfield Azure-omgeving (VNet). Als u echter een bestaand VNet hebt, u ernaar verwijzen in de sjabloon. Zie [Een site-to-site-verbinding maken](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)als u niet bekend bent met vpn-gateway IPsec/IKE-site-to-site-configuraties.

>[!NOTE]
>U hoeft azure resourcebeheersjablonen niet te gebruiken om deze configuratie te maken. U deze configuratie maken met de Azure-portal of PowerShell.
>
>

### <a name="31-declare-the-variables"></a><a name="variables3"></a>3.1 De variabelen declareren

In dit voorbeeld komen de variabele declaraties overeen met het voorbeeldnetwerk. Wijzig deze sectie bij het declareren van variabelen om uw omgeving weer te geven.

* De variabele **localAddressPrefix** is een array van on-premises IP-adressen om de IP-tunnels te beëindigen.
* De **gatewaySku** bepaalt de VPN-doorvoer. Zie [VPN Gateway-configuratie-instellingen](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku)voor meer informatie over gatewaySku en vpnType. Zie VPN [Gateway-prijzen](https://azure.microsoft.com/pricing/details/vpn-gateway)voor prijzen.
* Stel de **vpnType** in **op RouteBased**.

```json
"variables": {
  "virtualNetworkName": "SecureVNet",       // Name of the Azure VNet
  "azureVNetAddressPrefix": "10.2.0.0/24",  // Address space assigned to the VNet
  "subnetName": "Tenant",                   // subnet name in which tenants exists
  "subnetPrefix": "10.2.0.0/25",            // address space of the tenant subnet
  "gatewaySubnetPrefix": "10.2.0.224/27",   // address space of the gateway subnet
  "localGatewayName": "localGW1",           // name of remote gateway (on-premises)
  "localGatewayIpAddress": "X.243.229.110", // public IP address of the on-premises VPN device
  "localAddressPrefix": [
    "172.16.0.1/32",                        // termination of IPsec tunnel-1 on-premises 
    "172.16.0.2/32"                         // termination of IPsec tunnel-2 on-premises 
  ],
  "gatewayPublicIPName1": "vpnGwVIP1",    // Public address name of the first VPN gateway instance
  "gatewayPublicIPName2": "vpnGwVIP2",    // Public address name of the second VPN gateway instance 
  "gatewayName": "vpnGw",                 // Name of the Azure VPN gateway
  "gatewaySku": "VpnGw1",                 // Azure VPN gateway SKU
  "vpnType": "RouteBased",                // type of VPN gateway
  "sharedKey": "string",                  // shared secret needs to match with on-premises configuration
  "asnVpnGateway": 65000,                 // BGP Autonomous System number assigned to the VPN Gateway 
  "asnRemote": 65010,                     // BGP Autonmous Syste number assigned to the on-premises device
  "bgpPeeringAddress": "172.16.0.3",      // IP address of the remote BGP peer on-premises
  "connectionName": "vpn2local1",
  "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]",
  "gatewaySubnetRef": "[concat(variables('vnetID'),'/subnets/','GatewaySubnet')]",
  "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
  "api-version": "2017-06-01"
},
```

### <a name="32-create-virtual-network-vnet"></a><a name="vnet"></a>3.2 Virtueel netwerk maken (VNet)

Als u een bestaand VNet associeert met de VPN-tunnels, u deze stap overslaan.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[variables('azureVNetAddressPrefix')]"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "[variables('subnetPrefix')]"
        }
      },
      {
        "name": "GatewaySubnet",
        "properties": {
          "addressPrefix": "[variables('gatewaySubnetPrefix')]"
        }
      }
    ]
  },
  "comments": "Create a Virtual Network with Subnet1 and Gatewaysubnet"
},
```

### <a name="33-assign-public-ip-addresses-to-vpn-gateway-instances"></a><a name="ip"></a>3.3 Openbare IP-adressen toewijzen aan VPN-gateway-exemplaren
 
Wijs een openbaar IP-adres toe voor elk exemplaar van een VPN-gateway.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName1')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the first instance of the VPN gateway"
  },
  {
    "apiVersion": "[variables('api-version')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName2')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the second instance of the VPN gateway"
  },
```

### <a name="34-specify-the-on-premises-vpn-tunnel-termination-local-network-gateway"></a><a name="termination"></a>3.4 Geef de on-premises VPN-tunnelbeëindiging op (lokale netwerkgateway)

De on-premises VPN-apparaten worden aangeduid als de **lokale netwerkgateway.** In het volgende jsonfragment worden ook externe BGP-peerdetails opgegeven:

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/localNetworkGateways",
  "name": "[variables('localGatewayName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "localNetworkAddressSpace": {
      "addressPrefixes": "[variables('localAddressPrefix')]"
    },
    "gatewayIpAddress": "[variables('localGatewayIpAddress')]",
    "bgpSettings": {
      "asn": "[variables('asnRemote')]",
      "bgpPeeringAddress": "[variables('bgpPeeringAddress')]",
      "peerWeight": 0
    }
  },
  "comments": "Local Network Gateway (referred to your on-premises location) with IP address of remote tunnel peering and IP address of remote BGP peer"
},
```

### <a name="35-create-the-vpn-gateway"></a><a name="creategw"></a>3.5 De VPN-gateway maken

Dit gedeelte van de sjabloon configureert de VPN-gateway met de vereiste instellingen voor een actief actieve configuratie. Houd rekening met de volgende vereisten:

* Maak de VPN-gateway met een **"RouteBased"** VpnType. Deze instelling is verplicht als u de BGP-routering tussen de VPN-gateway en de VPN-on-premises wilt inschakelen.
* Als u VPN-tunnels wilt instellen tussen de twee exemplaren van de VPN-gateway en een bepaald on-premises apparaat in actieve actieve modus, wordt de parameter **"activeActive"** ingesteld op **true** in de sjabloon Resource Manager. Zie [Zeer beschikbare VPN-gatewayconnectiviteit](../vpn-gateway/vpn-gateway-highlyavailable.md)voor meer informatie over zeer beschikbare VPN-gateways.
* Als u eBGP-sessies wilt configureren tussen de VPN-tunnels, moet u aan weerszijden twee verschillende ASN's opgeven. Het verdient de voorkeur om privé ASN-nummers op te geven. Zie [Overzicht van BGP- en Azure VPN-gateways](../vpn-gateway/vpn-gateway-bgp-overview.md)voor meer informatie.

```json
{
"apiVersion": "[variables('api-version')]",
"type": "Microsoft.Network/virtualNetworkGateways",
"name": "[variables('gatewayName')]",
"location": "[resourceGroup().location]",
"dependsOn": [
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName1'))]",
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName2'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
],
"properties": {
  "ipConfigurations": [
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName1'))]"
        }
      },
      "name": "vnetGtwConfig1"
    },
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName2'))]"
        }
      },
          "name": "vnetGtwConfig2"
        }
      ],
      "sku": {
        "name": "[variables('gatewaySku')]",
        "tier": "[variables('gatewaySku')]"
      },
      "gatewayType": "Vpn",
      "vpnType": "[variables('vpnType')]",
      "enableBgp": true,
      "activeActive": true,
      "bgpSettings": {
        "asn": "[variables('asnVpnGateway')]"
      }
    },
    "comments": "VPN Gateway in active-active configuration with BGP support"
  },
```

### <a name="36-establish-the-ipsec-tunnels"></a><a name="ipsectunnel"></a>3.6 De IPsec-tunnels instellen

De laatste actie van het script maakt IPsec-tunnels tussen de Azure VPN-gateway en het on-premises VPN-apparaat.

```json
{
  "apiVersion": "[variables('api-version')]",
  "name": "[variables('connectionName')]",
  "type": "Microsoft.Network/connections",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworkGateways/', variables('gatewayName'))]",
    "[concat('Microsoft.Network/localNetworkGateways/', variables('localGatewayName'))]"
  ],
  "properties": {
    "virtualNetworkGateway1": {
      "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', variables('gatewayName'))]"
    },
    "localNetworkGateway2": {
      "id": "[resourceId('Microsoft.Network/localNetworkGateways', variables('localGatewayName'))]"
    },
    "connectionType": "IPsec",
    "routingWeight": 0,
    "sharedKey": "[variables('sharedKey')]",
    "enableBGP": "true"
  },
  "comments": "Create a Connection type site-to-site (IPsec) between the Azure VPN Gateway and the VPN device on-premises"
  }
```

## <a name="4-configure-the-on-premises-vpn-device"></a><a name="device"></a>4. Het on-premises VPN-apparaat configureren

De Azure VPN-gateway is compatibel met veel VPN-apparaten van verschillende leveranciers. Zie [Over VPN-apparaten](../vpn-gateway/vpn-gateway-about-vpn-devices.md)voor configuratie-informatie en apparaten die zijn gevalideerd om met vpn-gateway te werken.

Bij het configureren van uw VPN-apparaat hebt u de volgende items nodig:

* Een gedeelde sleutel. Dit is dezelfde gedeelde sleutel die u opgeeft bij het maken van uw vpn-verbinding tussen site en site. De voorbeelden maken gebruik van een basisgedeelde sleutel. We raden u aan een complexere sleutel te genereren.
* Het openbare IP-adres van uw VPN-gateway. U kunt het openbare IP-adres weergeven met behulp van Azure Portal, PowerShell of de CLI. Navigeer naar Virtuele netwerkgateways en klik op de naam van uw VPN-gateway om het openbare IP-adres dat gebruikmaakt van Azure Portal te achterhalen.

Doorgaans zijn eBGP-peers rechtstreeks verbonden (vaak via een WAN-verbinding). Wanneer u echter eBGP configureert via IPsec VPN-tunnels via ExpressRoute Microsoft-peering, zijn er meerdere routeringsdomeinen tussen de eBGP-peers. Gebruik de **opdracht ebgp-multihop** om de eBGP-relatie tussen de twee niet-direct verbonden peers vast te stellen. Het gehele getal dat de opdracht ebgp-multihop volgt, geeft de TTL-waarde in de BGP-pakketten aan. De opdracht **maximale paden eibgp 2** maakt het mogelijk load balancing van het verkeer tussen de twee BGP paden.

### <a name="cisco-csr1000-example"></a><a name="cisco1"></a>Cisco CSR1000 voorbeeld

In het volgende voorbeeld wordt de configuratie voor Cisco CSR1000 in een virtuele Hyper-V-machine weergegeven als het on-premises VPN-apparaat:

```
!
crypto ikev2 proposal az-PROPOSAL
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy az-POLICY
 proposal az-PROPOSAL
!
crypto ikev2 keyring key-peer1
 peer azvpn1
  address 52.175.253.112
  pre-shared-key secret*1234
 !
!
crypto ikev2 keyring key-peer2
 peer azvpn2
  address 52.175.250.191
  pre-shared-key secret*1234
 !
!
!
crypto ikev2 profile az-PROFILE1
 match address local interface GigabitEthernet1
 match identity remote address 52.175.253.112 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer1
!
crypto ikev2 profile az-PROFILE2
 match address local interface GigabitEthernet1
 match identity remote address 52.175.250.191 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer2
!
crypto ikev2 dpd 10 2 on-demand
!
!
crypto ipsec transform-set az-IPSEC-PROPOSAL-SET esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile az-VTI1
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE1
!
crypto ipsec profile az-VTI2
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE2
!
!
interface Loopback0
 ip address 172.16.0.3 255.255.255.255
!
interface Tunnel0
 ip address 172.16.0.1 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.253.112
 tunnel protection ipsec profile az-VTI1
!
interface Tunnel1
 ip address 172.16.0.2 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.250.191
 tunnel protection ipsec profile az-VTI2
!
interface GigabitEthernet1
 description External interface
 ip address x.243.229.110 255.255.255.252
 negotiation auto
 no mop enabled
 no mop sysid
!
interface GigabitEthernet2
 ip address 10.0.0.1 255.255.255.0
 negotiation auto
 no mop enabled
 no mop sysid
!
router bgp 65010
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 network 10.0.0.0 mask 255.255.255.0
 network 10.1.10.0 mask 255.255.255.128
 neighbor 10.2.0.228 remote-as 65000
 neighbor 10.2.0.228 ebgp-multihop 5
 neighbor 10.2.0.228 update-source Loopback0
 neighbor 10.2.0.228 soft-reconfiguration inbound
 neighbor 10.2.0.228 filter-list 10 out
 neighbor 10.2.0.229 remote-as 65000    
 neighbor 10.2.0.229 ebgp-multihop 5
 neighbor 10.2.0.229 update-source Loopback0
 neighbor 10.2.0.229 soft-reconfiguration inbound
 maximum-paths eibgp 2
!
ip route 0.0.0.0 0.0.0.0 10.1.10.1
ip route 10.2.0.228 255.255.255.255 Tunnel0
ip route 10.2.0.229 255.255.255.255 Tunnel1
!
```

## <a name="5-configure-vpn-device-filtering-and-firewalls-optional"></a><a name="firewalls"></a>5. Filtering en firewalls van VPN-apparaten configureren (optioneel)

Configureer uw firewall en filtering volgens uw vereisten.

## <a name="6-test-and-validate-the-ipsec-tunnel"></a><a name="testipsec"></a>6. Test en valideer de IPsec-tunnel

De status van IPsec-tunnels kan worden geverifieerd op de Azure VPN-gateway door Powershell-opdrachten:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Voorbeelduitvoer:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Als u de status van de tunnels op de Azure VPN-gateway-exemplaren onafhankelijk wilt controleren, gebruikt u het volgende voorbeeld:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

Voorbeelduitvoer:

```azurepowershell
Tunnel                           : vpn2local1_52.175.250.191
ConnectionStatus                 : Connected
IngressBytesTransferred          : 4877438
EgressBytesTransferred           : 8754071
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:30

Tunnel                           : vpn2local1_52.175.253.112
ConnectionStatus                 : Connected
IngressBytesTransferred          : 5660773
EgressBytesTransferred           : 8980589
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:13
```

U ook de tunnelstatus controleren op uw on-premises VPN-apparaat.

Cisco CSR1000 voorbeeld:

```
show crypto session detail
show crypto ikev2 sa
show crypto ikev2 session detail
show crypto ipsec sa
```

Voorbeelduitvoer:

```
csr1#show crypto session detail

Crypto session current status

Code: C - IKE Configuration mode, D - Dead Peer Detection
K - Keepalives, N - NAT-traversal, T - cTCP encapsulation
X - IKE Extended Authentication, F - IKE Fragmentation
R - IKE Auto Reconnect

Interface: Tunnel1
Profile: az-PROFILE2
Uptime: 00:52:46
Session status: UP-ACTIVE
Peer: 52.175.250.191 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.250.191
      Desc: (none)
  Session ID: 3
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.250.191/4500 Active
          Capabilities:DN connid:3 lifetime:23:07:14
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 279 drop 0 life (KB/Sec) 4607976/433
        Outbound: #pkts enc'ed 164 drop 0 life (KB/Sec) 4607992/433

Interface: Tunnel0
Profile: az-PROFILE1
Uptime: 00:52:43
Session status: UP-ACTIVE
Peer: 52.175.253.112 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.253.112
      Desc: (none)
  Session ID: 2
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.253.112/4500 Active
          Capabilities:DN connid:2 lifetime:23:07:17
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 668 drop 0 life (KB/Sec) 4607926/437
        Outbound: #pkts enc'ed 477 drop 0 life (KB/Sec) 4607953/437
```

Het lijnprotocol op de Virtual Tunnel Interface (VTI) verandert niet in "up" totdat IKE fase 2 is voltooid. Met de volgende opdracht wordt de beveiligingskoppeling geverifieerd:

```
csr1#show crypto ikev2 sa

IPv4 Crypto IKEv2  SA

Tunnel-id Local                 Remote                fvrf/ivrf            Status
2         10.1.10.50/4500       52.175.253.112/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3277 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status
3         10.1.10.50/4500       52.175.250.191/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3280 sec

IPv6 Crypto IKEv2  SA

csr1#show crypto ipsec sa | inc encaps|decaps
    #pkts encaps: 177, #pkts encrypt: 177, #pkts digest: 177
    #pkts decaps: 296, #pkts decrypt: 296, #pkts verify: 296
    #pkts encaps: 554, #pkts encrypt: 554, #pkts digest: 554
    #pkts decaps: 746, #pkts decrypt: 746, #pkts verify: 746
```

### <a name="verify-end-to-end-connectivity-between-the-inside-network-on-premises-and-the-azure-vnet"></a><a name="verifye2e"></a>Controleer de end-to-end-verbinding tussen het on-premises binnennetwerk en het Azure VNet

Als de IPsec-tunnels zijn ingesteld en de statische routes correct zijn ingesteld, moet u het IP-adres van de externe BGP-peer kunnen pingen:

```
csr1#ping 10.2.0.228
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.228, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 5/5/5 ms

#ping 10.2.0.229
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.229, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/5/6 ms
```

### <a name="verify-the-bgp-sessions-over-ipsec"></a><a name="verifybgp"></a>Controleer de BGP-sessies via IPsec

Controleer op de Azure VPN-gateway de status van BGP-peer:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Voorbeelduitvoer:

```azurepowershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

Als u de lijst met netwerkvoorvoegsels die via eBGP van de VPN-concentrator on-premises zijn ontvangen, wilt verifiëren, u filteren op kenmerk Origin:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

In de voorbeelduitvoer is de ASN 65010 het BGP-autonome systeemnummer in de VPN on-premises.

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

Ga als volgt te werk om de lijst met geadverteerde routes te bekijken:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

Voorbeelduitvoer:

```azurepowershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

Voorbeeld voor de on-premises Cisco CSR1000:

```
csr1#show ip bgp neighbors 10.2.0.228 routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.2.0.0/24      10.2.0.228                             0 65000 i
 r>   172.16.0.1/32    10.2.0.228                             0 65000 i
 r>   172.16.0.2/32    10.2.0.228                             0 65000 i
 r>   172.16.0.3/32   10.2.0.228                             0 65000 i

Total number of prefixes 4
```

De lijst met netwerken die worden geadverteerd vanaf de on-premises Cisco CSR1000 tot de Azure VPN-gateway, kan worden weergegeven met de volgende opdracht:

```
csr1#show ip bgp neighbors 10.2.0.228 advertised-routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.0.0.0/24      0.0.0.0                  0         32768 i
 *>   10.1.10.0/25     0.0.0.0                  0         32768 i

Total number of prefixes 2
```

## <a name="next-steps"></a>Volgende stappen

* [Netwerkprestatiemeter configureren voor ExpressRoute](how-to-npm.md)

* [Een site-to-site-verbinding toevoegen aan een VNet met een bestaande VPN-gatewayverbinding](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
