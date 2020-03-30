---
title: 'Azure ExpressRoute: ExpressRoute direct configureren'
description: Op deze pagina u ExpressRoute Direct configureren.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: jaredro
ms.openlocfilehash: 2722a852b1119ef619bc414bce5cb3a8ff6f8f00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031609"
---
# <a name="how-to-configure-expressroute-direct"></a>ExpressRoute Direct configureren

ExpressRoute Direct biedt u de mogelijkheid om rechtstreeks verbinding te maken met het wereldwijde netwerk van Microsoft op peeringlocaties die strategisch over de hele wereld zijn verspreid. Zie [About ExpressRoute Direct](expressroute-erdirect-about.md) (Over ExpressRoute Direct) voor meer informatie.

## <a name="create-the-resource"></a><a name="resources"></a>De resource maken

1. Meld u aan bij Azure en selecteer het abonnement. De expressroute direct resource- en ExpressRoute-circuits moeten in hetzelfde abonnement zitten.

   ```powershell
   Connect-AzAccount 

   Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
   ```
   
2. Registreer uw abonnement opnieuw op Microsoft.Network om toegang te krijgen tot de expressrouteportslocatie en expressrouteport API's.

   ```powershell
   Register-AzResourceProvider -ProviderNameSpace "Microsoft.Network"
   ```   
3. Vermeld alle locaties waar ExpressRoute Direct wordt ondersteund.
  
   ```powershell
   Get-AzExpressRoutePortsLocation
   ```

   **Voorbeelduitvoer**
  
   ```powershell
   Name                : Equinix-Ashburn-DC2
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-D
                        C2
   ProvisioningState   : Succeeded
   Address             : 21715 Filigree Court, DC2, Building F, Ashburn, VA 20147
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-Dallas-DA3
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA
                        3
   ProvisioningState   : Succeeded
   Address             : 1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : []
   ```
4. Bepalen of een bovenstaande locatie beschikbare bandbreedte heeft

   ```powershell
   Get-AzExpressRoutePortsLocation -LocationName "Equinix-San-Jose-SV1"
   ```

   **Voorbeelduitvoer**

   ```powershell
   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : [
                          {
                            "OfferName": "100 Gbps",
                            "ValueInGbps": 100
                          }
                        ]
   ```
5. Maak een ExpressRoute Direct-bron op basis van de hierboven gekozen locatie

   ExpressRoute Direct ondersteunt zowel QinQ als Dot1Q inkapseling. Als QinQ is geselecteerd, krijgt elk ExpressRoute-circuit dynamisch een S-Tag toegewezen en is het uniek in de ExpressRoute Direct-bron. Elke C-Tag op het circuit moet uniek zijn op het circuit, maar niet over de ExpressRoute Direct.  

   Als Dot1Q-inkapseling is geselecteerd, moet u de uniciteit van de C-Tag (VLAN) beheren voor de gehele ExpressRoute Direct-bron.  

   > [!IMPORTANT]
   > ExpressRoute Direct kan slechts één inkapselingstype zijn. Inkapseling kan niet worden gewijzigd na expressroute direct creatie.
   > 
 
   ```powershell 
   $ERDirect = New-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
   ```

   > [!NOTE]
   > Het kenmerk Encapsulation kan ook worden ingesteld op Dot1Q. 
   >

   **Voorbeelduitvoer:**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                               ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                                 {
                                   "Name": "link1",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link1",
                                   "RouterName": "tst-09xgmr-cis-1",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "link2",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link2",
                                   "RouterName": "tst-09xgmr-cis-2",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
   Circuits                   : []
   ```

## <a name="change-admin-state-of-links"></a><a name="state"></a>Beheerdersstatus van koppelingen wijzigen

  Dit proces moet worden gebruikt om een Layer 1-test uit te voeren, zodat elke kruisverbinding correct in elke router wordt gepatcht voor primaire en secundaire.
1. Ontvang Direct informatie over ExpressRoute.

   ```powershell
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   ```
2. Koppeling instellen op Ingeschakeld. Herhaal deze stap om elke koppeling in te stellen op ingeschakeld.

   Links[0] is de primaire poort en Links[1] is de secundaire poort.

   ```powershell
   $ERDirect.Links[0].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   $ERDirect.Links[1].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   ```
   **Voorbeelduitvoer:**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<number>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                             ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                               {
                                 "Name": "link1",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link1",
                                 "RouterName": "tst-09xgmr-cis-1",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               },
                               {
                                 "Name": "link2",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link2",
                                 "RouterName": "tst-09xgmr-cis-2",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
   Circuits                   : []
   ```

   Gebruik dezelfde procedure `AdminState = "Disabled"` om de poorten uit te schakelen.

## <a name="create-a-circuit"></a><a name="circuit"></a>Een circuit maken

Standaard u 10 circuits maken in het abonnement waar de ExpressRoute Direct-bron zich bevindt. Dit kan worden verhoogd door ondersteuning. U bent verantwoordelijk voor het bijhouden van zowel de ingerichte als de gebruikte bandbreedte. Ingerichte bandbreedte is de som van de bandbreedte van alle circuits op de ExpressRoute Direct-bron en gebruikte bandbreedte is het fysieke gebruik van de onderliggende fysieke interfaces.

Er zijn extra circuitbandbreedtes die op ExpressRoute Direct alleen kunnen worden gebruikt om de hierboven beschreven scenario's te ondersteunen. Dit zijn: 40Gbps en 100Gbps.

**SkuTier** kan lokaal, standaard of premium zijn.

**SkuFamily** mag alleen GemetenData hebben, omdat onbeperkt niet wordt ondersteund op ExpressRoute Direct.

Maak een circuit op de ExpressRoute Direct-bron.

  ```powershell
  New-AzExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
  ```

  Andere bandbreedtes zijn: 5.0, 10.0 en 40.0

  **Voorbeelduitvoer:**

  ```powershell
  Name                             : ExpressRoute-Direct-ckt
  ResourceGroupName                : Contoso-Direct-rg
  Location                         : westcentralus
  Id                               : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Netwo
                                   rk/expressRouteCircuits/ExpressRoute-Direct-ckt
  Etag                             : W/"<etagnumber>"
  ProvisioningState                : Succeeded
  Sku                              : {
                                     "Name": "Premium_MeteredData",
                                     "Tier": "Premium",
                                     "Family": "MeteredData"
                                   }
  CircuitProvisioningState         : Enabled
  ServiceProviderProvisioningState : Provisioned
  ServiceProviderNotes             : 
    ServiceProviderProperties        : null
  ExpressRoutePort                 : {
                                     "Id": "/subscriptions/<subscriptionID>n/resourceGroups/Contoso-Direct-rg/providers/Micros
                                   oft.Network/expressRoutePorts/Contoso-Direct"
                                   }
  BandwidthInGbps                  : 10
  Stag                             : 2
  ServiceKey                       : <number>
  Peerings                         : []
  Authorizations                   : []
  AllowClassicOperations           : False
  GatewayManagerEtag     
  ```

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht](expressroute-erdirect-about.md)voor meer informatie over ExpressRoute Direct.
