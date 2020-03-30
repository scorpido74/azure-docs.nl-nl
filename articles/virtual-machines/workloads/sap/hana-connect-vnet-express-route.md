---
title: Connectiviteit ingesteld van virtueel netwerk naar SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: Connectiviteit die vanuit virtueel netwerk is ingesteld om SAP HANA op Azure (grote exemplaren) te gebruiken.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f7ac8e69c4e149fdd0f365e19f7a0282a547af43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617190"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Een virtueel netwerk verbinden met grote exemplaren van HANA

Nadat u een virtueel Azure-netwerk hebt gemaakt, u dat netwerk verbinden met SAP HANA op grote exemplaren van Azure. Maak een Azure ExpressRoute-gateway op het virtuele netwerk. Met deze gateway u het virtuele netwerk koppelen aan het ExpressRoute-circuit dat verbinding maakt met de klanttenant op de HANA Large Instance-stempel.

> [!NOTE] 
> Deze stap kan tot 30 minuten duren. De nieuwe gateway wordt gemaakt in het aangewezen Azure-abonnement en vervolgens verbonden met het opgegeven virtuele Azure-netwerk.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Als er al een gateway bestaat, controleert u of het een ExpressRoute-gateway is of niet. Als het geen ExpressRoute-gateway is, verwijdert u de gateway en maakt u deze opnieuw als een ExpressRoute-gateway. Als er al een ExpressRoute-gateway is ingesteld, raadpleegt u het volgende gedeelte van dit artikel, 'Virtuele netwerken koppelen'. 

- Gebruik de [Azure-portal](https://portal.azure.com/) of PowerShell om een ExpressRoute VPN-gateway te maken die is verbonden met uw virtuele netwerk.
  - Als u de Azure-portal gebruikt, voegt u een nieuwe **virtuele netwerkgateway toe**en selecteert u **ExpressRoute** als gatewaytype.
  - Als u PowerShell gebruikt, downloadt en gebruikt u eerst de nieuwste [Azure PowerShell SDK.](https://azure.microsoft.com/downloads/) 
 
Met de volgende opdrachten wordt een ExpressRoute-gateway gemaakt. De teksten voorafgegaan _$_ door a zijn door de gebruiker gedefinieerde variabelen die moeten worden bijgewerkt met uw specifieke informatie.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

In dit voorbeeld is de HighPerformance-gateway SKU gebruikt. Uw opties zijn HighPerformance of UltraPerformance als de enige gateway SKU's die worden ondersteund voor SAP HANA op Azure (grote exemplaren).

> [!IMPORTANT]
> Voor HANA-grote exemplaren van de Klasse SKU van type II moet u de UltraPerformance Gateway SKU gebruiken.

## <a name="link-virtual-networks"></a>Virtuele netwerken koppelen

Het virtuele Azure-netwerk heeft nu een ExpressRoute-gateway. Gebruik de autorisatiegegevens van Microsoft om de ExpressRoute-gateway te verbinden met het SAP HANA Large Instances ExpressRoute-circuit. U verbinding maken via de Azure-portal of PowerShell. De PowerShell instructies zijn als volgt. 

Voer de volgende opdrachten uit voor elke ExpressRoute-gateway met behulp van een andere AuthGUID voor elke verbinding. De eerste twee vermeldingen in het volgende script zijn afkomstig van de door Microsoft verstrekte informatie. Ook is de AuthGUID specifiek voor elk virtueel netwerk en zijn gateway. Als u een ander Virtueel Azure-netwerk wilt toevoegen, moet u een andere AuthID voor uw ExpressRoute-circuit krijgen die HANA-grote exemplaren van Microsoft met Azure verbindt. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> De laatste parameter in de opdracht New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** is een nieuwe parameter die ExpressRoute Fast Path mogelijk maakt. Een functionaliteit die de netwerklatentie tussen uw HANA Large Instance-eenheden en Azure VM's vermindert. De functionaliteit is in mei 2019 toegevoegd. Raadpleeg voor meer informatie de netwerkarchitectuur van HET artikel [SAP HANA (Large Instances).](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture) Zorg ervoor dat u de nieuwste versie van PowerShell-cmdlets uitvoert voordat u de opdrachten uitvoert.

Als u de gateway wilt verbinden met meer dan één ExpressRoute-circuit dat aan uw abonnement is gekoppeld, moet u deze stap mogelijk meerdere stappen meerdere tijd uitvoeren. U gaat bijvoorbeeld waarschijnlijk dezelfde virtuele netwerkgateway aansluiten op het ExpressRoute-circuit dat het virtuele netwerk verbindt met uw on-premises netwerk.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>ExpressRoute Fast Path toepassen op bestaande HANA Large Instance ExpressRoute-circuits
In de documentatie tot nu toe is uitgelegd hoe u een nieuw ExpressRoute-circuit verbinden dat is gemaakt met een HANA Large Instance-implementatie op een Azure ExpressRoute-gateway van een van uw Azure-virtuele netwerken. Maar veel klanten hebben hun ExpressRoute circuits al setup en hebben hun virtuele netwerken al aangesloten op HANA Large Instances. Aangezien de nieuwe ExpressRoute Fast Path de netwerklatentie vermindert, is het raadzaam om de wijziging toe te passen om deze functionaliteit te gebruiken. De commando's om een nieuw ExpreesRoute circuit aan te sluiten en een bestaand ExpressRoute Circuit te veranderen zijn hetzelfde. Als gevolg hiervan moet u deze reeks PowerShell-opdrachten uitvoeren om een bestaand circuit te wijzigen 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

Het is belangrijk dat u de laatste parameter toevoegt zoals hierboven weergegeven om de ExpressRoute Fast Path-functionaliteit in te schakelen


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
Als u Globaal bereik wilt inschakelen voor een of beide van de twee scenario's:

 - HANA-systeemreplicatie zonder extra proxy's of firewalls
 - Back-ups kopiëren tussen HANA Large Instance-eenheden in twee verschillende regio's om systeemkopieën of systeemvernieuwingen uit te voeren

je moet overwegen dat:

- U moet een adresruimte van een adresruimte van /29 opgeven. Dat adresbereik overlapt mogelijk niet met een van de andere adresruimtebereiken die u tot nu toe hebt gebruikt en die HANA Large Instances met Azure verbindt en mag niet overlappen met een van uw IP-adresbereiken die u ergens anders in Azure of on-premises hebt gebruikt.
- Er is een beperking op de ASNs (Autonomous System Number) die kunnen worden gebruikt om uw on-premises routes naar HANA Large Instances te adverteren. Uw on-premises mag geen reclame maken voor routes met privé-ASN's in het bereik van 65000 – 65020 of 65515. 
- Voor het scenario van het maken van on-premises directe toegang tot HANA Grote exemplaren, moet u een vergoeding berekenen voor het circuit dat u verbindt met Azure. Voor prijzen, controleer de prijzen voor [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/).

Als u een of beide scenario's wilt openen die op uw implementatie worden toegepast, opent u een ondersteuningsbericht met Azure zoals beschreven in [Een ondersteuningsaanvraag openen voor grote HANA-exemplaren](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

Gegevens die nodig zijn en zoekwoorden die u moet gebruiken voor Microsoft om uw verzoek te routeren en uit te voeren, zien eruit als volgt:

- Service: SAP HANA Large Instance
- Probleemtype: configuratie en installatie
- Probleem subtype: Mijn probleem is niet hierboven vermeld
- Onderwerp 'Modify my Network - add Global Reach'
- Details: 'Voeg globaal bereik toe aan HANA Large Instance aan de HANA Large Instance-tenant of 'Voeg Globaal bereik toe aan on-premises aan de HANA Large Instance-tenant.
- Aanvullende details voor de HANA Large Instance voor HANA Large Instance tenant case: U moet de **twee Azure-regio's** definiëren waar de twee tenants zich bevinden **en** u moet het **IP-adresbereik van /29** indienen
- Aanvullende details voor de on-premises naar HANA Large Instance tenant case: U moet de **Azure-regio** definiëren waar de HANA Large Instance-tenant is geïmplementeerd waarmee u rechtstreeks verbinding wilt maken. Daarnaast moet u de **Auth GUID** en **Circuit Peer ID** leveren die u hebt ontvangen toen u uw ExpressRoute-circuit tussen on-premises en Azure hebt ingesteld. Daarnaast moet u uw **ASN**een naam geven. De laatste deliverable is een **/29 IP-adresbereik** voor ExpressRoute Global Reach.

> [!NOTE]
> Als u beide aanvragen wilt laten behandelen, moet u twee verschillende /29 IP-adresbereiken leveren die niet overlappen met een ander IP-adresbereik dat tot nu toe is gebruikt. 




## <a name="next-steps"></a>Volgende stappen

- [Aanvullende netwerkvereisten voor HLI](hana-additional-network-requirements.md)
