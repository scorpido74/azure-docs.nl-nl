---
title: 'Een virtueel netwerk koppelen aan een ExpressRoute-circuit: Power shell: klassiek: Azure | Microsoft Docs'
description: Dit document bevat een overzicht van hoe u virtuele netwerken (VNets) koppelt aan ExpressRoute-circuits met behulp van het klassieke implementatie model en Power shell.
services: expressroute
documentationcenter: na
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: cherylmc
ms.openlocfilehash: 9365e36cb2beff21e795adecaef5fa41e0d7583c
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748265"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Een virtueel netwerk verbinden met een ExpressRoute-circuit met behulp van Power shell (klassiek)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video-Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-linkvnet-classic.md)
>

In dit artikel vindt u informatie over het koppelen van virtuele netwerken (VNets) aan Azure ExpressRoute-circuits met behulp van Power shell. Eén VNet kan worden gekoppeld aan Maxi maal vier ExpressRoute-circuits. Volg de stappen in dit artikel om een nieuwe koppeling te maken naar elk ExpressRoute-circuit waarmee u verbinding maakt. De ExpressRoute-circuits kunnen zich in hetzelfde abonnement, verschillende abonnementen of een combi natie van beide bevindt. Dit artikel is van toepassing op virtuele netwerken die zijn gemaakt met het klassieke implementatie model.

U kunt Maxi maal 10 virtuele netwerken koppelen aan een ExpressRoute-circuit. Alle virtuele netwerken moeten zich in dezelfde geopolitieke regio bevinden. U kunt een groter aantal virtuele netwerken koppelen aan uw ExpressRoute-circuit of virtuele netwerken koppelen die zich in andere geopolitieke regio's bevinden als u de ExpressRoute Premium-invoeg toepassing inschakelt. Raadpleeg de [Veelgestelde vragen](expressroute-faqs.md) voor meer informatie over de Premium-invoeg toepassing.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Over Azure-implementatiemodellen**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Configuratievereisten

* Bekijk de [](expressroute-prerequisites.md) [vereisten, routerings behoeften](expressroute-routing.md)en [werk stromen](expressroute-workflows.md) voordat u begint met de configuratie.
* U moet een actief ExpressRoute-circuit hebben.
   * Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-classic.md) en laat uw connectiviteits provider het circuit inschakelen.
   * Zorg ervoor dat u persoonlijke Azure-peering voor uw circuit hebt geconfigureerd. Zie het artikel [route ring configureren](expressroute-howto-routing-classic.md) voor instructies voor route ring.
   * Zorg ervoor dat persoonlijke Azure-peering is geconfigureerd en dat de BGP-peering tussen uw netwerk en micro soft actief is zodat u end-to-end connectiviteit kunt inschakelen.
   * U moet een virtueel netwerk en een gateway voor het virtuele netwerk hebben gemaakt en volledig zijn ingericht. Volg de instructies voor het [configureren van een virtueel netwerk voor ExpressRoute](expressroute-howto-vnet-portal-classic.md).

### <a name="download-the-latest-powershell-cmdlets"></a>De meest recente Power shell-cmdlets downloaden

Installeer de nieuwste versies van de Azure Service Management (SM) Power shell-modules en de ExpressRoute-module. Wanneer u het volgende voor beeld gebruikt, ziet u dat het versie nummer (in dit voor beeld 5.1.1) verandert als nieuwere versies van de cmdlets worden vrijgegeven.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Als u meer informatie over Azure PowerShell nodig hebt, raadpleegt u aan de slag [met Azure PowerShell-cmdlets](/powershell/azure/overview) voor stapsgewijze instructies voor het configureren van uw computer om de Azure PowerShell modules te gebruiken.

### <a name="sign-in"></a>Aanmelden

Gebruik de volgende voor beelden om u aan te melden bij uw Azure-account:

1. Open de PowerShell-console met verhoogde rechten en maak verbinding met uw account.

   ```powershell
   Connect-AzAccount
   ```
2. Controleer de abonnementen voor het account.

   ```powershell
   Get-AzSubscription
   ```
3. Als u meerdere abonnementen hebt, selecteert u het abonnement dat u wilt gebruiken.

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

4. Gebruik vervolgens de volgende cmdlet om uw Azure-abonnement toe te voegen aan Power shell voor het klassieke implementatie model.

   ```powershell
   Add-AzureAccount
   ```

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Een virtueel netwerk in hetzelfde abonnement verbinden met een circuit
U kunt een virtueel netwerk koppelen aan een ExpressRoute-circuit met behulp van de volgende cmdlet. Zorg ervoor dat de virtuele netwerk gateway is gemaakt en gereed is voor koppeling voordat u de cmdlet uitvoert.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Een koppeling met een virtueel netwerk naar een circuit verwijderen
U kunt een koppeling van een virtueel netwerk met een ExpressRoute-circuit verwijderen met de volgende cmdlet. Zorg ervoor dat het huidige abonnement is geselecteerd voor het opgegeven virtuele netwerk. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Een virtueel netwerk in een ander abonnement verbinden met een circuit
U kunt een ExpressRoute-circuit delen tussen meerdere abonnementen. In de volgende afbeelding ziet u hoe delen werkt voor ExpressRoute-circuits voor meerdere abonnementen.

Elk van de kleinere Clouds in de grote Cloud wordt gebruikt om abonnementen weer te geven die deel uitmaken van verschillende afdelingen binnen een organisatie. Elk van de afdelingen in de organisatie kan hun eigen abonnement gebruiken voor het implementeren van hun services, maar de afdelingen kunnen een enkel ExpressRoute-circuit delen om verbinding te maken met uw on-premises netwerk. Eén afdeling (in dit voor beeld: IT) kan eigenaar zijn van het ExpressRoute-circuit. Andere abonnementen binnen de organisatie kunnen het ExpressRoute-circuit gebruiken.

> [!NOTE]
> Connectiviteits-en bandbreedte kosten voor het specifieke circuit worden toegepast op de eigenaar van het ExpressRoute-circuit. Alle virtuele netwerken delen dezelfde band breedte.
> 
> 

![Connectiviteit tussen abonnementen](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Beheer
De *eigenaar* van het circuit is de beheerder/beheerder van het abonnement waarin het ExpressRoute-circuit is gemaakt. De eigenaar van het circuit kan beheerders/mede beheerders van andere abonnementen, zoals *circuit gebruikers*genoemd, autoriseren voor het gebruik van het toegewezen circuit waarvan ze eigenaar zijn. Circuit gebruikers die gemachtigd zijn om het ExpressRoute-circuit van de organisatie te gebruiken, kunnen het virtuele netwerk in hun abonnement koppelen aan het ExpressRoute-circuit nadat ze zijn geautoriseerd.

De eigenaar van het circuit heeft de bevoegdheid om autorisaties op elk gewenst moment te wijzigen en in te trekken. Als u een autorisatie intrekt, worden alle koppelingen verwijderd uit het abonnement waarvan de toegang is ingetrokken.

### <a name="circuit-owner-operations"></a>Bewerkingen voor circuit eigenaars

**Een autorisatie maken**

De eigenaar van het circuit geeft de beheerders van andere abonnementen toestemming om het opgegeven circuit te gebruiken. In het volgende voor beeld is de beheerder van het circuit (Contoso IT) de beheerder van een ander abonnement (dev-test) in staat om Maxi maal twee virtuele netwerken aan het circuit te koppelen. De contoso IT-beheerder kan dit doen door de micro soft-ID voor dev-test op te geven. De cmdlet verzendt geen e-mail bericht naar de opgegeven micro soft-ID. De eigenaar van het circuit moet expliciet de andere eigenaar van het abonnement melden dat de autorisatie is voltooid.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  Opvragen

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Autorisaties controleren**

De eigenaar van het circuit kan alle autorisaties controleren die zijn uitgegeven op een bepaald circuit door de volgende cmdlet uit te voeren:

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  Opvragen

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

  Opvragen

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

### <a name="circuit-user-operations"></a>Gebruikers bewerkingen circuit

**Autorisaties controleren**

De circuit gebruiker kan autorisaties controleren met behulp van de volgende cmdlet:

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  Opvragen

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

**Koppelings autorisaties worden ingewisseld**

De circuit gebruiker kan de volgende cmdlet uitvoeren om een koppelings autorisatie te verwisselen:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  Opvragen

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

Voer deze opdracht uit in het zojuist gekoppelde abonnement voor het virtuele netwerk:

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over ExpressRoute raadpleegt u de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md).
