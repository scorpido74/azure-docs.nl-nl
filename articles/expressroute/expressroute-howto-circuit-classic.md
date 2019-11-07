---
title: 'Een ExpressRoute-circuit wijzigen: Power shell: klassiek Azure | Microsoft Docs'
description: Dit artikel begeleidt u stapsgewijs door de stappen voor het controleren van de status, het bijwerken of verwijderen van uw ExpressRoute klassieke implementatie model circuit.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cherylmc
ms.openlocfilehash: 9f1c05b85fac6dd0168d9c2b2944326800e90493
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643675"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Aanpassen van een ExpressRoute-circuit met behulp van PowerShell (klassiek)

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sjabloon](expressroute-howto-circuit-resource-manager-template.md)
> * [Video-Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-circuit-classic.md)
>

Dit artikel begeleidt u stapsgewijs door de stappen voor het controleren van de status, het bijwerken of verwijderen van uw ExpressRoute klassieke implementatie model circuit. Dit artikel is van toepassing op het klassieke implementatiemodel.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Over Azure-implementatiemodellen**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Voordat u begint

Installeer de nieuwste versies van de Azure Service Management (SM) Power shell-modules en de ExpressRoute-module. U kunt de Azure Cloud shell-omgeving niet gebruiken om SM-modules uit te voeren.

1. Volg de instructies in het artikel [service management-module installeren](/powershell/azure/servicemanagement/install-azure-ps) om de Azure service management-module te installeren. Als u de module AZ of RM al hebt geïnstalleerd, zorg er dan voor dat u '-AllowClobber ' gebruikt.
2. Importeer de geïnstalleerde modules. Wanneer u het volgende voor beeld gebruikt, past u het pad aan om de locatie van de geïnstalleerde Power shell-modules weer te geven.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Open uw Power shell-console met verhoogde rechten en maak verbinding met uw account om u aan te melden bij uw Azure-account. Gebruik het volgende voor beeld om verbinding te maken met behulp van de module Service Management:

   ```powershell
   Add-AzureAccount
   ```

## <a name="get-the-status-of-a-circuit"></a>De status van een circuit ophalen

U kunt deze informatie op elk gewenst moment ophalen met behulp van de cmdlet `Get-AzureCircuit`. Als u de aanroep zonder para meters, worden alle circuits vermeld.

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

U kunt informatie over een specifiek ExpressRoute-circuit verkrijgen door de service sleutel als een para meter voor de aanroep door te geven.

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

U kunt gedetailleerde beschrijvingen van alle para meters verkrijgen door het volgende voor beeld uit te voeren:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Een circuit wijzigen

U kunt bepaalde eigenschappen van een ExpressRoute-circuit wijzigen zonder dat dit van invloed is op de verbinding.

U kunt de volgende taken zonder uitval tijd doen:

* Een ExpressRoute Premium-invoeg toepassing voor uw ExpressRoute-circuit in-of uitschakelen.
* Verg root de band breedte van uw ExpressRoute-circuit, op voor waarde dat er capaciteit beschikbaar is op de poort. Het downgraden van de bandbreedte van een circuit wordt niet ondersteund.
* Wijzig het licentie controle plan van gegevens met data limiet naar onbeperkte gegevens. Het is niet mogelijk om het meet plan van onbeperkte gegevens te wijzigen in gegevens met data limiet.
* U kunt *klassieke bewerkingen toestaan*in-en uitschakelen.

Raadpleeg de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor meer informatie over limieten en beperkingen.

### <a name="enable-the-expressroute-premium-add-on"></a>De Premium-invoeg toepassing voor ExpressRoute inschakelen

U kunt de ExpressRoute Premium-invoeg toepassing voor uw bestaande circuit inschakelen met behulp van de volgende Power shell-cmdlet:

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

Uw circuit heeft nu de ExpressRoute Premium-invoeg toepassingen ingeschakeld. Zodra de opdracht is uitgevoerd, wordt de facturering voor de Premium-invoeg functie gestart.

### <a name="disable-the-expressroute-premium-add-on"></a>De Premium-invoeg toepassing voor ExpressRoute uitschakelen

> [!IMPORTANT]
> Deze bewerking kan mislukken als u resources gebruikt die groter zijn dan is toegestaan voor het standaard circuit.
>
>

#### <a name="considerations"></a>Overwegingen

* Zorg ervoor dat het aantal virtuele netwerken dat is gekoppeld aan het circuit kleiner is dan 10 voordat u een downgrade uitvoert van Premium naar Standard. Als u dit niet doet, mislukt de update aanvraag en worden de Premium-tarieven in rekening gebracht.
* U moet alle virtuele netwerken in andere geopolitieke regio's ontkoppelen. Als u dit niet doet, mislukt de update aanvraag en worden de Premium-tarieven in rekening gebracht.
* De route tabel moet kleiner zijn dan 4.000 routes voor privé-peering. Als uw route tabel groter is dan 4.000 routes, wordt de BGP-sessie verbroken en wordt deze niet opnieuw ingeschakeld totdat het aantal aangekondigde voor voegsels onder 4.000 komt.

#### <a name="to-disable-the-premium-add-on"></a>De Premium-invoeg toepassing uitschakelen

U kunt de ExpressRoute Premium-invoeg toepassing voor uw bestaande circuit uitschakelen met behulp van de volgende Power shell-cmdlet:

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

### <a name="update-the-expressroute-circuit-bandwidth"></a>De band breedte van het ExpressRoute-circuit bijwerken

Raadpleeg de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor de ondersteunde bandbreedte opties voor uw provider. U kunt elke grootte kiezen die groter is dan de grootte van uw bestaande circuit, zolang de fysieke poort (waarop het circuit is gemaakt) toestaat.

> [!IMPORTANT]
> Mogelijk moet u het ExpressRoute-circuit opnieuw maken als er onvoldoende capaciteit is op de bestaande poort. U kunt het circuit niet upgraden als er geen extra capaciteit beschikbaar is op die locatie.
>
> U kunt de band breedte van een ExpressRoute-circuit zonder onderbreking niet verkleinen. Voor downgrade-band breedte moet u de inrichting van het ExpressRoute-circuit ongedaan maken en vervolgens een nieuw ExpressRoute-circuit opnieuw inrichten.
>
>

#### <a name="resize-a-circuit"></a>Het formaat van een circuit wijzigen

Nadat u hebt bepaald welke grootte u nodig hebt, kunt u de volgende opdracht gebruiken om het formaat van het circuit te wijzigen:

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

Zodra het circuit op de micro soft-zijde is gewijzigd, neemt u contact op met uw connectiviteits provider om de configuraties aan hun kant bij te werken om deze wijziging aan te passen. De optie voor het bijwerken van de band breedte vanaf dit punt wordt gestart.

Als de volgende fout wordt weer gegeven bij het verhogen van de band breedte van het circuit, betekent dit dat er onvoldoende band breedte beschikbaar is op de fysieke poort waarop uw bestaande circuit is gemaakt. U moet dit circuit verwijderen en een nieuw circuit maken met de grootte die u nodig hebt.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Inrichting ongedaan maken en een circuit verwijderen

### <a name="considerations"></a>Overwegingen

* U moet alle virtuele netwerken ontkoppelen van het ExpressRoute-circuit om deze bewerking te kunnen volt ooien. Controleer of er virtuele netwerken zijn die zijn gekoppeld aan het circuit als deze bewerking mislukt.
* Als de inrichtings status van de ExpressRoute-circuit service provider is **ingericht** of **ingericht** , moet u samen met uw service provider de inrichting van het circuit aan hun zijde opheffen. We blijven resources reserveren en u factureren totdat de service provider de inrichting van het circuit ongedaan heeft gemaakt en ons op de hoogte stelt.
* Als de service provider de inrichting van het circuit heeft ongedaan gemaakt (de inrichtings status van de service provider is ingesteld op **niet ingericht**), kunt u het circuit verwijderen. Hiermee wordt ook de facturering voor het circuit gestopt.

#### <a name="delete-a-circuit"></a>Een circuit verwijderen

U kunt het ExpressRoute-circuit verwijderen door de volgende opdracht uit te voeren:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
