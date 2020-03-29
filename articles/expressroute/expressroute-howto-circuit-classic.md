---
title: 'Azure ExpressRoute: Een circuit wijzigen: PowerShell:classic'
description: In dit artikel u de stappen doorlopen om de status te controleren, bij te werken of uw ExpressRoute-modelcircuit voor klassieke implementatie te verwijderen en te deprovisioneren.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cherylmc
ms.openlocfilehash: e421a534b04f74d2a2eb0bc06aeffa52684ae17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931965"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Een ExpressRoute-circuit wijzigen met PowerShell (klassiek)

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Powershell](expressroute-howto-circuit-arm.md)
> * [Azure-CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sjabloon](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-circuit-classic.md)
>

In dit artikel u de stappen doorlopen om de status te controleren, bij te werken of uw ExpressRoute-modelcircuit voor klassieke implementatie te verwijderen en te deprovisioneren. Dit artikel is van toepassing op het klassieke implementatiemodel.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Over Azure-implementatiemodellen**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="get-the-status-of-a-circuit"></a>De status van een circuit krijgen

U deze informatie op elk `Get-AzureCircuit` gewenst moment ophalen met behulp van de cmdlet. Het maken van de oproep zonder enige parameters lijsten alle circuits.

```powershell
Get-AzureDedicatedCircuit

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled

Bandwidth                        : 1000
CircuitName                      : MyAsiaCircuit
Location                         : Singapore
ServiceKey                       : #################################
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

U informatie krijgen over een specifiek ExpressRoute-circuit door de servicesleutel als parameter door te geven aan de aanroep.

```powershell
Get-AzureDedicatedCircuit -ServiceKey "*********************************"

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

U gedetailleerde beschrijvingen van alle parameters krijgen door het volgende voorbeeld uit te voeren:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Een circuit wijzigen

U bepaalde eigenschappen van een ExpressRoute-circuit wijzigen zonder dat dit gevolgen heeft voor de connectiviteit.

U de volgende taken uitvoeren zonder downtime:

* Een ExpressRoute premium add-on voor uw ExpressRoute-circuit in- of uitschakelen.
* Verhoog de bandbreedte van uw ExpressRoute-circuit, mits er capaciteit beschikbaar is op de poort. Het verlagen van de bandbreedte van een circuit wordt niet ondersteund.
* Wijzig het meetplan van Gemeten Data in Onbeperkte gegevens. Het wijzigen van het meetplan van Unlimited Data naar Gemeten Data wordt niet ondersteund.
* U *Klassieke bewerkingen*toestaan en uitschakelen.

Raadpleeg de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor meer informatie over limieten en beperkingen.

### <a name="enable-the-expressroute-premium-add-on"></a>De ExpressRoute premium add-on inschakelen

U de ExpressRoute premium add-on inschakelen voor uw bestaande circuit met behulp van de volgende PowerShell-cmdlet:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Premium
Status                           : Enabled
```

Uw circuit heeft nu de ExpressRoute premium add-on functies ingeschakeld. Zodra de opdracht is uitgevoerd, begint de facturering voor de premium add-on-mogelijkheid.

### <a name="disable-the-expressroute-premium-add-on"></a>De ExpressRoute premium add-on uitschakelen

> [!IMPORTANT]
> Deze bewerking kan mislukken als u resources gebruikt die groter zijn dan wat is toegestaan voor het standaardcircuit.
>
>

#### <a name="considerations"></a>Overwegingen

* Zorg ervoor dat het aantal virtuele netwerken gekoppeld aan het circuit is minder dan 10 voordat u downgrade van premium naar standaard. Als u dit niet doet, mislukt uw updateverzoek en worden de premietarieven in rekening gebracht.
* U moet alle virtuele netwerken in andere geopolitieke regio's ontkoppelen. Als u dit niet doet, mislukt uw updateaanvraag en worden de premietarieven in rekening gebracht.
* Uw routetabel moet minder dan 4.000 routes hebben voor privé-peering. Als de grootte van uw routetabel groter is dan 4.000 routes, daalt de BGP-sessie en wordt deze pas opnieuw ingeschakeld als het aantal geadverteerde voorvoegsels onder de 4.000 komt.

#### <a name="to-disable-the-premium-add-on"></a>De premium-invoegtoepassing uitschakelen

U de ExpressRoute premium add-on voor uw bestaande circuit uitschakelen met behulp van de volgende PowerShell-cmdlet:

```powershell

Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

### <a name="update-the-expressroute-circuit-bandwidth"></a>De bandbreedte van het ExpressRoute-circuit bijwerken

Raadpleeg de [veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor ondersteunde bandbreedteopties voor uw provider. U elke grootte kiezen die groter is dan de grootte van uw bestaande circuit, zolang de fysieke poort (waarop uw circuit is gemaakt) dit toelaat.

> [!IMPORTANT]
> Het kan zijn dat u het ExpressRoute-circuit opnieuw moet maken als er onvoldoende capaciteit is op de bestaande poort. U het circuit niet upgraden als er op die locatie geen extra capaciteit beschikbaar is.
>
> U de bandbreedte van een ExpressRoute-circuit niet zonder onderbreking verminderen. Voor het verlagen van de bandbreedte moet u het ExpressRoute-circuit deprovisioneren en vervolgens een nieuw ExpressRoute-circuit opnieuw inrichten.
>
>

#### <a name="resize-a-circuit"></a>Het formaat van een circuit wijzigen

Nadat u hebt bepaald welke maat u nodig hebt, u de volgende opdracht gebruiken om het formaat van uw circuit te wijzigen:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Zodra uw circuit is uitgebreid aan de Microsoft-kant, moet u contact opnemen met uw connectiviteitsprovider om configuraties aan hun kant bij te werken om aan deze wijziging te voldoen. Facturering begint vanaf dit punt voor de bijgewerkte bandbreedteoptie.

Als u de volgende fout ziet bij het verhogen van de bandbreedte van het circuit, betekent dit dat er onvoldoende bandbreedte over is op de fysieke poort waar uw bestaande circuit wordt gemaakt. U moet dit circuit verwijderen en een nieuw circuit maken van de grootte die u nodig hebt.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Een circuit deprovisionen en verwijderen

### <a name="considerations"></a>Overwegingen

* U moet alle virtuele netwerken loskoppelen van het ExpressRoute-circuit om deze bewerking te laten slagen. Controleer of u virtuele netwerken hebt die zijn gekoppeld aan het circuit als deze bewerking mislukt.
* Als de ExpressRoute-dienstverlener provisioning state is **Provisioning** or **Provisioned,** moet u samenwerken met uw serviceprovider om het circuit aan hun kant te deprovisioneren. We blijven resources reserveren en u factureren totdat de serviceprovider het circuit heeft voltooid en ons hiervan op de hoogte stelt.
* Als de serviceprovider het circuit heeft gedeprovisioneerd (de status van de serviceprovider is ingesteld op **Niet-ingericht),** u het circuit verwijderen. Hiermee wordt ook de facturering voor het circuit gestopt.

#### <a name="delete-a-circuit"></a>Een circuit verwijderen

U uw ExpressRoute-circuit verwijderen door de volgende opdracht uit te voeren:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
