---
title: 'Azure ExpressRoute: koppel een VNet aan een circuit: klassiek'
description: Dit document geeft een overzicht van hoe u virtuele netwerken (VNets) koppelen aan ExpressRoute-circuits met behulp van het klassieke implementatiemodel en PowerShell.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: cherylmc
ms.openlocfilehash: 53c200b01dfa6bce09cfc058dc24ab8e38d253a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930036"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Sluit een virtueel netwerk aan op een ExpressRoute-circuit met PowerShell (klassiek)
> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Powershell](expressroute-howto-linkvnet-arm.md)
> * [Azure-CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-linkvnet-classic.md)
>

In dit artikel u virtuele netwerken (VNets) koppelen aan Azure ExpressRoute-circuits met PowerShell. Een enkele VNet kan worden gekoppeld aan maximaal vier ExpressRoute-circuits. Gebruik de stappen in dit artikel om een nieuwe link te maken naar elk ExpressRoute-circuit waaru verbinding mee maakt. De ExpressRoute-circuits kunnen in hetzelfde abonnement, verschillende abonnementen of een mix van beide zijn. Dit artikel is van toepassing op virtuele netwerken die zijn gemaakt met behulp van het klassieke implementatiemodel.

U maximaal 10 virtuele netwerken koppelen aan een ExpressRoute-circuit. Alle virtuele netwerken moeten zich in dezelfde geopolitieke regio bevinden. U een groter aantal virtuele netwerken koppelen aan uw ExpressRoute-circuit of virtuele netwerken in andere geopolitieke regio's koppelen als u de ExpressRoute premium-add-on inschakelt. Bekijk de [FAQ](expressroute-faqs.md) voor meer informatie over de premium add-on.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Over Azure-implementatiemodellen**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Configuratievereisten

* Controleer de [vereisten,](expressroute-prerequisites.md) [routeringsvereisten](expressroute-routing.md)en [werkstromen](expressroute-workflows.md) voordat u met de configuratie begint.
* U moet een actief ExpressRoute-circuit hebben.
   * Volg de instructies om [een ExpressRoute-circuit](expressroute-howto-circuit-classic.md) te maken en laat uw connectiviteitsprovider het circuit inschakelen.
   * Zorg ervoor dat Azure private peering is geconfigureerd voor uw circuit. Zie het [routeringsartikel configureren](expressroute-howto-routing-classic.md) voor routeringsinstructies.
   * Controleer of Azure private peering is geconfigureerd en dat het BGP-peering tussen uw netwerk en Microsoft is ingeschakeld, zodat u end-to-end-connectiviteit inschakelen.
   * U moet beschikken over een virtueel netwerk en een virtuele netwerkgateway die zijn gemaakt en volledig zijn ingericht. Volg de instructies om [een virtueel netwerk voor ExpressRoute](expressroute-howto-vnet-portal-classic.md)te configureren.

### <a name="download-the-latest-powershell-cmdlets"></a>Download de nieuwste PowerShell-cmdlets

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Een virtueel netwerk in hetzelfde abonnement aansluiten op een circuit
U een virtueel netwerk koppelen aan een ExpressRoute-circuit met behulp van de volgende cmdlet. Zorg ervoor dat de virtuele netwerkgateway is gemaakt en klaar is voor het koppelen voordat u de cmdlet uitvoert.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Een virtuele netwerkkoppeling naar een circuit verwijderen
U een virtuele netwerkkoppeling naar een ExpressRoute-circuit verwijderen met behulp van de volgende cmdlet. Zorg ervoor dat het huidige abonnement is geselecteerd voor het opgegeven virtuele netwerk. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Een virtueel netwerk in een ander abonnement verbinden met een circuit
U een ExpressRoute-circuit delen via meerdere abonnementen. De volgende afbeelding toont een eenvoudig schema van hoe delen werkt voor ExpressRoute-circuits in meerdere abonnementen.

Elk van de kleinere clouds binnen de grote cloud wordt gebruikt om abonnementen weer te geven die behoren tot verschillende afdelingen binnen een organisatie. Elk van de afdelingen binnen de organisatie kan hun eigen abonnement gebruiken voor het implementeren van hun diensten - maar de afdelingen kunnen één ExpressRoute-circuit delen om verbinding te maken met uw on-premises netwerk. Eén afdeling (in dit voorbeeld: IT) kan eigenaar zijn van het ExpressRoute circuit. Andere abonnementen binnen de organisatie kunnen gebruik maken van het ExpressRoute circuit.

> [!NOTE]
> Connectiviteit en bandbreedtekosten voor het speciale circuit worden toegepast op de eigenaar van het ExpressRoute-circuit. Alle virtuele netwerken delen dezelfde bandbreedte.
> 
> 

![Connectiviteit met abonnementen voor abonnementen](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Beheer
De eigenaar van het *circuit* is de beheerder/medebeheerder van het abonnement waarin het ExpressRoute-circuit wordt aangemaakt. De eigenaar van het circuit kan beheerders/medebeheerders van andere abonnementen, *circuitgebruikers*genoemd, machtigen om het speciale circuit te gebruiken dat ze bezitten. Circuitgebruikers die gemachtigd zijn om het ExpressRoute-circuit van de organisatie te gebruiken, kunnen het virtuele netwerk in hun abonnement koppelen aan het ExpressRoute-circuit nadat ze zijn geautoriseerd.

De eigenaar van het circuit heeft de bevoegdheid om autorisaties op elk gewenst moment te wijzigen en in te trekken. Als u een autorisatie intrekt, worden alle koppelingen verwijderd uit het abonnement waarvan de toegang is ingetrokken.

### <a name="circuit-owner-operations"></a>Circuit eigenaar operaties

**Een autorisatie maken**

De eigenaar van het circuit geeft de beheerders van andere abonnementen toestemming om het opgegeven circuit te gebruiken. In het volgende voorbeeld stelt de beheerder van het circuit (Contoso IT) de beheerder van een ander abonnement (Dev-Test) in staat om maximaal twee virtuele netwerken aan het circuit te koppelen. De It-beheerder van Contoso maakt dit mogelijk door de Microsoft ID voor Dev-Test op te geven. De cmdlet stuurt geen e-mail naar de opgegeven Microsoft ID. De eigenaar van het circuit moet de eigenaar van het andere abonnement expliciet op de hoogte stellen dat de autorisatie is voltooid.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  retourneren:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Autorisaties controleren**

De eigenaar van het circuit kan alle autorisaties die op een bepaald circuit zijn afgegeven, bekijken door de volgende cmdlet uit te voeren:

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  retourneren:

  ```powershell
  Description         : EngineeringTeam
  Limit               : 3
  LinkAuthorizationId : ####################################
  MicrosoftIds        : engadmin@contoso.com
  Used                : 1

  Description         : MarketingTeam
  Limit               : 1
  LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
  MicrosoftIds        : marketingadmin@contoso.com
  Used                : 0

  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : salesadmin@contoso.com
  Used                : 2
  ```

**Autorisaties bijwerken**

De eigenaar van het circuit kan autorisaties wijzigen met behulp van de volgende cmdlet:

```powershell
Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
```

  retourneren:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Autorisaties verwijderen**

De eigenaar van het circuit kan autorisaties voor de gebruiker intrekken/verwijderen door de volgende cmdlet uit te voeren:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Gebruikersbewerkingen van het circuit

**Autorisaties controleren**

De gebruiker van het circuit kan autorisaties bekijken met behulp van de volgende cmdlet:

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  retourneren:

  ```powershell
  Bandwidth                        : 200
  CircuitName                      : ContosoIT
  Location                         : Washington DC
  MaximumAllowedLinks              : 2
  ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  UsedLinks                        : 0
  ```

**Koppelingsautorisaties inwisselen**

De gebruiker van het circuit kan de volgende cmdlet uitvoeren om een koppelingsautorisatie in te wisselen:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  retourneren:

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

Voer deze opdracht uit in het nieuw gekoppelde abonnement voor het virtuele netwerk:

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>Volgende stappen

Zie de Veelgestelde vragen over [ExpressRoute voor](expressroute-faqs.md)meer informatie over ExpressRoute.
