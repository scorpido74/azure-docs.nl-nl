---
title: 'Azure ExpressRoute: een circuit wijzigen: Power shell: klassiek'
description: In dit artikel leidt u door de stappen om te controleren van de status, bijwerken of verwijderen en de inrichting van uw ExpressRoute-circuit voor het model van klassieke implementatie.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cherylmc
ms.openlocfilehash: a2a3bdeb0ee454185c92c6e49a17bda7e4e5d5f0
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083585"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Aanpassen van een ExpressRoute-circuit met behulp van PowerShell (klassiek)

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sjabloon](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-circuit-classic.md)
>

In dit artikel leidt u door de stappen om te controleren van de status, bijwerken of verwijderen en de inrichting van uw ExpressRoute-circuit voor het model van klassieke implementatie. Dit artikel is van toepassing op het klassieke implementatiemodel.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Over Azure-implementatiemodellen**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Voordat u begint

Installeer de nieuwste versies van de Azure SM (Service Management) PowerShell-modules en de ExpressRoute-module. U kunt de Azure Cloud shell-omgeving niet gebruiken om SM-modules uit te voeren.

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

## <a name="get-the-status-of-a-circuit"></a>Haal de status van een circuit

U kunt deze informatie op elk gewenst moment ophalen met behulp van de `Get-AzureCircuit` cmdlet. De oproep zonder parameters geeft een lijst van alle circuits.

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

Doordat de servicesleutel worden doorgegeven als parameter aan de aanroep krijgt u informatie over een specifieke ExpressRoute-circuit.

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

U kunt gedetailleerde beschrijvingen van alle parameters krijgen door het volgende voorbeeld uitvoert:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Een circuit wijzigen

U kunt bepaalde eigenschappen van een ExpressRoute-circuit wijzigen zonder gevolgen voor connectiviteit.

U kunt de volgende taken zonder uitvaltijd kunt doen:

* In- of uitschakelen van een premium-invoegtoepassing voor ExpressRoute voor uw ExpressRoute-circuit.
* De bandbreedte van uw ExpressRoute-circuit vergroten, mits er capaciteit beschikbaar is op de poort. Het downgraden van de bandbreedte van een circuit wordt niet ondersteund.
* De softwarelicentiecontrole abonnement van naar gebruik om onbeperkte gegevens te wijzigen. Wijzigen van de softwarelicentiecontrole plan van onbeperkte gegevens in naar gebruik wordt niet ondersteund.
* U kunt inschakelen en uitschakelen *klassieke bewerkingen toestaan*.

Raadpleeg de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor meer informatie over limieten en beperkingen.

### <a name="enable-the-expressroute-premium-add-on"></a>De add-on ExpressRoute premium inschakelen

U kunt de invoegtoepassing ExpressRoute premium inschakelen voor uw bestaande circuit met behulp van de volgende PowerShell-cmdlet:

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

Uw circuit, hebben nu de ExpressRoute premium-invoegtoepassing-functies ingeschakeld. Zodra de opdracht is uitgevoerd, begint de facturering voor de mogelijkheid van premium-invoegtoepassing.

### <a name="disable-the-expressroute-premium-add-on"></a>Uitschakelen van de premium-invoegtoepassing voor ExpressRoute

> [!IMPORTANT]
> Met deze bewerking kan mislukken als u resources die groter zijn dan wat is toegestaan voor de standard-circuit.
>
>

#### <a name="considerations"></a>Overwegingen

* Zorg ervoor dat het aantal virtuele netwerken die zijn gekoppeld aan het circuit minder dan 10 is voordat u een downgrade van premium naar standard uitvoeren. Als u dit niet doet, uw aanvraag voor bijwerken mislukt en u wordt gefactureerd in de premietarieven.
* U moet alle virtuele netwerken in andere geopolitieke regio's ontkoppelen. Als u dit niet doet, uw aanvraag voor bijwerken mislukt en u wordt gefactureerd in de premietarieven.
* De routetabel moet minder dan 4000 routes voor persoonlijke peering. Als de grootte van uw route-table groter dan 4000 routes is, wordt de BGP-sessie wordt geweigerd en wordt niet worden ingeschakeld totdat het aantal geadverteerde voorvoegsels lager 4.000 is.

#### <a name="to-disable-the-premium-add-on"></a>De premium-invoegtoepassing wilt uitschakelen

U kunt de premium-invoegtoepassing voor ExpressRoute voor uw bestaande circuit uitschakelen met behulp van de volgende PowerShell-cmdlet:

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

### <a name="update-the-expressroute-circuit-bandwidth"></a>Bijwerken van de bandbreedte van het ExpressRoute-circuit

Controleer de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor ondersteunde bandbreedte-opties voor uw provider. U kunt een grootte die groter is dan de grootte van uw bestaande circuit, zolang de fysieke poort (waarop uw circuit wordt gemaakt) kunt kiezen.

> [!IMPORTANT]
> U moet de ExpressRoute-circuit opnieuw te maken als er onvoldoende capaciteit op de bestaande poort. U kunt het circuit niet bijwerken als er geen extra capaciteit beschikbaar is op die locatie.
>
> U kunt de bandbreedte van een ExpressRoute-circuit zonder onderbreking niet reduceren. Het downgraden van bandbreedte, moet u de inrichting van het ExpressRoute-circuit ongedaan maken en vervolgens opnieuw inrichten van een nieuwe ExpressRoute-circuit.
>
>

#### <a name="resize-a-circuit"></a>Een circuit vergroten of verkleinen

Nadat u welke grootte die u nodig hebt besluit, kunt u de volgende opdracht uit om het formaat van uw circuit te:

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

Zodra uw circuit heeft zijn grote van aan de kant van Microsoft, moet u contact op met uw connectiviteitsprovider voor het bijwerken van configuraties op hun kant zodat deze overeenkomt met deze wijziging. Facturering begint op voor de optie bijgewerkte bandbreedte vanaf dit punt.

Als u de volgende fout ziet wanneer bandbreedte van het circuit vergroten, betekent dit er niet voldoende bandbreedte links op de fysieke poort waarin uw bestaande circuit wordt gemaakt. U moet dit circuit verwijderen en een nieuw circuit van de grootte die u moet maken.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Inrichting ongedaan maken en verwijderen van een circuit

### <a name="considerations"></a>Overwegingen

* U moet alle virtuele netwerken van het ExpressRoute-circuit voor deze bewerking te voltooien ontkoppelen. Controleer of er virtuele netwerken die zijn gekoppeld aan het circuit als deze bewerking is mislukt.
* Als het ExpressRoute-circuit serviceprovider-Inrichtingsstatus **Provisioning** of **ingerichte** moet u werken met uw serviceprovider inrichting ongedaan maken van het circuit aan hun kant. We blijven reserveer resources en factureren u totdat de service-provider is voltooid ongedaan maken van inrichting van het circuit en ons wordt geïnformeerd.
* Als de service-provider de inrichting van het circuit is beëindigd (de serviceprovider Inrichtingsstatus is ingesteld op **niet ingericht**), kunt u het circuit verwijderen. Hiermee wordt ook de facturering voor het circuit gestopt.

#### <a name="delete-a-circuit"></a>Een circuit verwijderen

U kunt uw ExpressRoute-circuit verwijderen door de volgende opdracht uit:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
