---
title: BGP configureren op Azure VPN Gateway:CLI
description: In dit artikel u BGP configureren met een Azure VPN-gateway met Azure Resource Manager en CLI.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/25/2018
ms.author: yushwang
ms.openlocfilehash: 42a07ac00fd8a26918164f6547bf57c2b021d14c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863611"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>BGP configureren op een Azure VPN-gateway met CLI

Met dit artikel u BGP inschakelen op een cross-premises Site-to-Site (S2S) VPN-verbinding en een VNet-naar-VNet-verbinding (dat wil zeggen een verbinding tussen virtuele netwerken) met behulp van het Azure Resource Manager-implementatiemodel en Azure CLI.

## <a name="about-bgp"></a>Over BGP

BGP is het standaard routeringsprotocol dat vaak op het internet wordt gebruikt om routerings- en bereikbaarheidsinformatie uit te wisselen tussen twee of meer netwerken. BGP stelt de VPN-gateways en uw on-premises VPN-apparaten, genaamd BGP-peers of buren, in staat om routes uit te wisselen. De routes informeren beide gateways over de beschikbaarheid en bereikbaarheid voor voorvoegsels om door de betrokken gateways of routers te gaan. BGP kan ook transitroutering tussen meerdere netwerken mogelijk maken door de routes die een BGP-gateway leert van één BGP-peer, te propageren naar alle andere BGP-peers.

Zie [Overzicht van BGP met Azure VPN-gateways](vpn-gateway-bgp-overview.md)voor meer informatie over de voordelen van BGP en om inzicht te krijgen in de technische vereisten en overwegingen van het gebruik van BGP.

In dit artikel u de volgende taken uitvoeren:

* [BGP inschakelen voor uw VPN-gateway](#enablebgp) (vereist)

  U vervolgens een van de volgende secties of beide secties voltooien:

* [Een cross-premises verbinding met BGP tot stand brengen](#crossprembgp)
* [Een VNet-naar-VNet-verbinding met BGP tot stand brengen](#v2vbgp)

Elk van deze drie secties vormt een basisbouwsteen voor het inschakelen van BGP in uw netwerkconnectiviteit. Als u alle drie de secties voltooit, bouwt u de topologie zoals weergegeven in het volgende diagram:

![BGP-topologie](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

U deze secties combineren om een complexer multihoptransitnetwerk op te bouwen dat aan uw behoeften voldoet.

## <a name="enable-bgp-for-your-vpn-gateway"></a><a name ="enablebgp"></a>BGP inschakelen voor uw VPN-gateway

Deze sectie is vereist voordat u een van de stappen in de andere twee configuratiesecties uitvoert. In de volgende configuratiestappen worden de BGP-parameters van de Azure VPN-gateway ingesteld, zoals in het volgende diagram wordt weergegeven:

![BGP-gateway](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Voordat u begint

Installeer de nieuwste versie van de CLI-opdrachten (2.0 of hoger). Zie [Azure CLI installeren](/cli/azure/install-azure-cli) en [Aan de slag met Azure CLI](/cli/azure/get-started-with-azure-cli) voor meer informatie over de CLI-opdrachten.

### <a name="step-1-create-and-configure-testvnet1"></a>Stap 1: TestVNet1 maken en configureren

#### <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. Verbinding maken met uw abonnement

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Een resourcegroep maken

In het volgende voorbeeld wordt een resourcegroep met de naam TestRG1 gemaakt op de locatie 'eastus'. Als u al een resourcegroep hebt in de regio waar u uw virtuele netwerk wilt maken, u die groep in plaats daarvan gebruiken.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. TestVNet1 maken

In het volgende voorbeeld wordt een virtueel netwerk met de naam TestVNet1 en drie subnetten gemaakt: GatewaySubnet, FrontEnd en BackEnd. Wanneer u waarden vervangt, is het belangrijk dat u altijd uw gatewaysubnet specifiek GatewaySubnet noemt. Als u een andere naam kiest, mislukt het maken van de gateway.

Met de eerste opdracht wordt de front-end adresruimte en het FrontEnd-subnet ge. Met de tweede opdracht wordt een extra adresruimte voor het subnet BackEnd. De derde en vierde opdrachten maken het BackEnd-subnet en GatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Stap 2: Maak de VPN-gateway voor TestVNet1 met BGP-parameters

#### <a name="1-create-the-public-ip-address"></a>1. Het openbare IP-adres maken

Vraag een openbaar IP-adres aan. Het openbare IP-adres wordt toegewezen aan de VPN-gateway die u maakt voor uw virtuele netwerk.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Maak de VPN-gateway met het AS-nummer

Maak de gateway van het virtuele netwerk voor TestVNet1. BGP vereist een Route-Based VPN-gateway. U hebt ook `-Asn` de extra parameter nodig om het autonome systeemnummer (ASN) in te stellen voor TestVNet1. Het maken van een gateway kan een tijdje duren (45 minuten of meer) te voltooien. 

Als u deze opdracht `--no-wait` uitvoert met behulp van de parameter, ziet u geen feedback of uitvoer. Met `--no-wait` de parameter kan de gateway op de achtergrond worden gemaakt. Het betekent niet dat de VPN-gateway onmiddellijk wordt gemaakt.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Het IP-adres van Azure BGP-peer verkrijgen

Nadat de gateway is gemaakt, moet u het IP-adres van de BGP-peer op de Azure VPN-gateway verkrijgen. Dit adres is nodig om de VPN-gateway te configureren als Een BGP-peer voor uw on-premises VPN-apparaten.

Voer de volgende opdracht `bgpSettings` uit en controleer de sectie boven aan de uitvoer:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Nadat de gateway is gemaakt, u deze gateway gebruiken om een cross-premises verbinding of een VNet-naar-VNet-verbinding met BGP tot stand te brengen.

## <a name="establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembgp"></a>Een cross-premises verbinding met BGP tot stand brengen

Als u een cross-premises verbinding wilt maken, moet u een lokale netwerkgateway maken om uw on-premises VPN-apparaat te vertegenwoordigen. Vervolgens verbindt u de Azure VPN-gateway met de lokale netwerkgateway. Hoewel deze stappen vergelijkbaar zijn met het maken van andere verbindingen, bevatten ze de extra eigenschappen die nodig zijn om de BGP-configuratieparameters op te geven.

![BGP voor cross-premises](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Stap 1: De lokale netwerkgateway maken en configureren

Deze oefening blijft de configuratie in het diagram bouwen. Zorg ervoor dat u de waarden vervangt door de waarden die u voor uw configuratie wilt gebruiken. Wanneer u met lokale netwerkgateways werkt, moet u rekening houden met de volgende dingen:

* De lokale netwerkgateway kan zich in dezelfde locatie- en resourcegroep bevinden als de VPN-gateway, of deze kan zich in een andere locatie- en resourcegroep bevinden. In dit voorbeeld worden de gateways in verschillende resourcegroepen op verschillende locaties weergegeven.
* Het minimale voorvoegsel dat u moet declareren voor de lokale netwerkgateway is het hostadres van uw BGP-peer IP-adres op uw VPN-apparaat. In dit geval is het een /32 voorvoegsel van 10.51.255.254/32.
* Ter herinnering: u moet verschillende BGP-ASN's gebruiken tussen uw on-premises netwerken en het virtuele Azure-netwerk. Als ze hetzelfde zijn, moet u uw VNet ASN wijzigen als uw on-premises VPN-apparaten de ASN al gebruiken om te peeren met andere BGP-buren.

Voordat u verdergaat, moet u ervoor zorgen dat u het [gedeelte BGP inschakelen voor uw VPN-gateway](#enablebgp) van deze oefening hebt voltooid en dat u nog steeds verbonden bent met Abonnement 1. In dit voorbeeld maakt u een nieuwe resourcegroep. Let ook op de twee extra parameters `Asn` `BgpPeerAddress`voor de lokale netwerkgateway: en .

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Stap 2: De VNet-gateway en de lokale netwerkgateway verbinden

In deze stap maakt u de verbinding van TestVNet1 naar Site5. U moet `--enable-bgp` de parameter opgeven om BGP voor deze verbinding in te schakelen. 

In dit voorbeeld bevinden de virtuele netwerkgateway en de lokale netwerkgateway zich in verschillende brongroepen. Wanneer de gateways zich in verschillende brongroepen bevinden, moet u de volledige bron-id van de twee gateways opgeven om een verbinding tussen de virtuele netwerken in te stellen.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Haal de resource-ID van VNet1GW

Gebruik de uitvoer van de volgende opdracht om de resource-id voor VNet1GW op te halen:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

Zoek in de `"id":` uitvoer de lijn. U hebt de waarden binnen de aanhalingstekens nodig om de verbinding in de volgende sectie te maken.

Voorbeelduitvoer:

```
{ 
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65010, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
  }, 
  "enableBgp": true, 
  "etag": "W/\"<your etag number>\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
```

Kopieer de `"id":` waarden daarna naar een teksteditor, zoals Kladblok, zodat u ze eenvoudig plakken bij het maken van uw verbinding. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Haal de bron-ID van Site5

Gebruik de volgende opdracht om de bron-id van Site5 uit de uitvoer te halen:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. De TestVNet1-naar-Site5-verbinding maken

In deze stap maakt u de verbinding van TestVNet1 naar Site5. Zoals eerder besproken, is het mogelijk om zowel BGP- als niet-BGP-verbindingen te hebben voor dezelfde Azure VPN-gateway. Tenzij BGP is ingeschakeld in de verbindingseigenschap, schakelt Azure Geen BGP in voor deze verbinding, ook al zijn BGP-parameters al geconfigureerd op beide gateways. Vervang de abonnements-id's door die van u.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

In het volgende voorbeeld worden in het volgende voorbeeld de parameters weergegeven die u moet invoeren in de bgp-configuratiesectie van uw on-premises VPN-apparaat:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

De verbinding moet na enkele minuten tot stand zijn gebracht. De BGP-peeringsessie begint nadat de IPsec-verbinding is ingesteld.

## <a name="establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Een VNet-naar-VNet-verbinding met BGP tot stand brengen

In deze sectie wordt een VNet-naar-VNet-verbinding met BGP toegevoegd, zoals in het volgende diagram wordt weergegeven: 

![BGP voor VNet-naar-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

De volgende instructies gaan verder uit de stappen in de voorgaande secties. Als u TestVNet1 en de VPN-gateway met BGP wilt maken en configureren, moet u de [BGP inschakelen voor uw VPN-gatewaysectie](#enablebgp) voltooien.

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Stap 1: Maak TestVNet2 en de VPN-gateway

Het is belangrijk om ervoor te zorgen dat de IP-adresruimte van het nieuwe virtuele netwerk, TestVNet2, niet overlapt met een van uw VNet-bereiken.

In dit voorbeeld behoren de virtuele netwerken tot hetzelfde abonnement. U VNet-naar-VNet-verbindingen tussen verschillende abonnementen instellen. Zie [Een VNet-naar-VNet-verbinding configureren](vpn-gateway-howto-vnet-vnet-cli.md)voor meer informatie. Zorg ervoor dat `-EnableBgp $True` u toevoegt bij het maken van de verbindingen om BGP in te schakelen.

#### <a name="1-create-a-new-resource-group"></a>1. Een nieuwe resourcegroep maken

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. TestVNet2 maken in de nieuwe resourcegroep

Met de eerste opdracht wordt de front-end adresruimte en het FrontEnd-subnet ge. Met de tweede opdracht wordt een extra adresruimte voor het subnet BackEnd. De derde en vierde opdrachten maken het BackEnd-subnet en GatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. Het openbare IP-adres maken

Vraag een openbaar IP-adres aan. Het openbare IP-adres wordt toegewezen aan de VPN-gateway die u maakt voor uw virtuele netwerk.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Maak de VPN-gateway met het AS-nummer

Maak de virtuele netwerkgateway voor TestVNet2. U moet de standaard ASN op uw Azure VPN-gateways overschrijven. De ASN's voor de verbonden virtuele netwerken moeten verschillend zijn om BGP- en transitroutering in te schakelen.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Stap 2: De TestVNet1- en TestVNet2-gateways aansluiten

In deze stap maakt u de verbinding van TestVNet1 naar Site5. Als u BGP voor deze verbinding `--enable-bgp` wilt inschakelen, moet u de parameter opgeven.

In het volgende voorbeeld bevinden de virtuele netwerkgateway en de lokale netwerkgateway zich in verschillende brongroepen. Wanneer de gateways zich in verschillende brongroepen bevinden, moet u de volledige bron-id van de twee gateways opgeven om een verbinding tussen de virtuele netwerken in te stellen. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Haal de resource-ID van VNet1GW 

Haal de resource-id van VNet1GW uit de uitvoer van de volgende opdracht:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Haal de resource-ID van VNet2GW

Haal de resource-id van VNet2GW uit de uitvoer van de volgende opdracht:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. De verbindingen maken

Maak de verbinding van TestVNet1 naar TestVNet2 en de verbinding van TestVNet2 naar TestVNet1. Vervang de abonnements-id's door die van u.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> BGP inschakelen voor *beide* verbindingen.
> 
> 

Nadat u deze stappen hebt voltooid, wordt de verbinding binnen enkele minuten tot stand gebracht. De BGP-peeringsessie is up nadat de VNet-to-VNet-verbinding is voltooid.

## <a name="next-steps"></a>Volgende stappen

Nadat uw verbinding is voltooid, u virtuele machines toevoegen aan uw virtuele netwerken. Zie Een [virtuele machine maken](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor stappen.
