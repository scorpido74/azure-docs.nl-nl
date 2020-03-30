---
title: 'Azure ExpressRoute: klassieke circuits verplaatsen naar Resource Manager'
description: Op deze pagina wordt beschreven hoe u een klassiek circuit verplaatsen naar het implementatiemodel resourcebeheer met PowerShell.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.openlocfilehash: 4e49a3bc803733f5e78207fa3573c93395924d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74080160"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>ExpressRoute-circuits verplaatsen van klassiek naar Resource Manager-implementatiemodel met PowerShell

Als u een ExpressRoute-circuit wilt gebruiken voor zowel de klassieke als de implementatiemodellen van Resource Manager, moet u het circuit verplaatsen naar het implementatiemodel van Resource Manager. Met de volgende secties u uw circuit verplaatsen met PowerShell.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* Controleer of u zowel de klassieke als Az Azure PowerShell-modules lokaal op uw computer hebt geïnstalleerd. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) voor meer informatie.
* Zorg ervoor dat u de [vereisten,](expressroute-prerequisites.md) [routeringsvereisten](expressroute-routing.md)en [werkstromen](expressroute-workflows.md) hebt gecontroleerd voordat u met de configuratie begint.
* Bekijk de informatie die wordt verstrekt onder [Het verplaatsen van een ExpressRoute-circuit van klassiek naar Resource Manager](expressroute-move.md). Zorg ervoor dat u de grenzen en beperkingen volledig begrijpt.
* Controleer of het circuit volledig operationeel is in het klassieke implementatiemodel.
* Controleer of u een resourcegroep hebt die is gemaakt in het implementatiemodel resourcebeheer.

## <a name="move-an-expressroute-circuit"></a>Een ExpressRoute-circuit verplaatsen

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Stap 1: Verzamel circuitdetails van het klassieke implementatiemodel

Meld u aan bij de klassieke Azure-omgeving en verzamel de servicesleutel.

1. Meld u aan bij uw Azure-account.

   ```powershell
   Add-AzureAccount
   ```

2. Selecteer het juiste Azure-abonnement.

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. Importeer de PowerShell-modules voor Azure en ExpressRoute.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. Gebruik de cmdlet hieronder om de servicesleutels voor al uw ExpressRoute-circuits te krijgen. Nadat u de sleutels hebt opgehaald, kopieert u de **servicesleutel** van het circuit dat u wilt verplaatsen naar het implementatiemodel resourcebeheer.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Stap 2: Aanmelden en een resourcegroep maken

Meld u aan bij de resourcemanager-omgeving en maak een nieuwe resourcegroep.

1. Meld u aan bij uw Azure Resource Manager-omgeving.

   ```powershell
   Connect-AzAccount
   ```

2. Selecteer het juiste Azure-abonnement.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Wijzig het onderstaande fragment om een nieuwe resourcegroep te maken als u nog geen resourcegroep hebt.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Stap 3: Het ExpressRoute-circuit verplaatsen naar het implementatiemodel resourcebeheer

U bent nu klaar om uw ExpressRoute-circuit te verplaatsen van het klassieke implementatiemodel naar het implementatiemodel van Resource Manager. Voordat u verdergaat, bekijkt u de informatie die wordt verstrekt in [Het verplaatsen van een ExpressRoute-circuit van de klassieker naar het implementatiemodel resourcebeheer.](expressroute-move.md)

Als u uw circuit wilt verplaatsen, wijzigt u het volgende fragment en voert u het volgende fragment uit:

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

In de klassieke modus heeft een ExpressRoute-circuit niet het concept om aan een regio gebonden te zijn. In Resourcemanager moet elke resource echter worden toegewezen aan een Azure-regio. De regio die is opgegeven in de cmdlet Move-AzExpressRouteCircuit kan technisch elke regio zijn. Voor organisatorische doeleinden u een regio kiezen die uw peeringlocatie nauw vertegenwoordigt.

> [!NOTE]
> Nadat de verhuizing is voltooid, wordt de nieuwe naam die in de vorige cmdlet wordt vermeld, gebruikt om de bron aan te pakken. Het circuit zal in wezen worden hernoemd.
> 

## <a name="modify-circuit-access"></a>Toegang tot het circuit wijzigen

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>ExpressRoute-circuittoegang inschakelen voor beide implementatiemodellen

Nadat u uw klassieke ExpressRoute-circuit hebt verplaatst naar het implementatiemodel van Resource Manager, u toegang tot beide implementatiemodellen inschakelen. Voer de volgende cmdlets uit om toegang tot beide implementatiemodellen mogelijk te maken:

1. Haal de details van het circuit.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Stel 'Klassieke bewerkingen toestaan' in op TRUE.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Werk het circuit bij. Nadat deze bewerking is voltooid, u het circuit bekijken in het klassieke implementatiemodel.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. Voer de volgende cmdlet uit om de details van het ExpressRoute circuit te krijgen. U moet de vermelde servicesleutel kunnen zien.

   ```powershell
   get-azurededicatedcircuit
   ```

5. U nu koppelingen naar het ExpressRoute-circuit beheren met behulp van de klassieke opdrachten voor implementatiemodellen voor klassieke VNets en de opdrachten Resource Manager voor VNets voor Resource Manager. Met de volgende artikelen u koppelingen naar het ExpressRoute-circuit beheren:

    * [Uw virtuele netwerk koppelen aan uw ExpressRoute-circuit in het implementatiemodel Resource Manager](expressroute-howto-linkvnet-arm.md)
    * [Koppel uw virtuele netwerk aan uw ExpressRoute-circuit in het klassieke implementatiemodel](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>ExpressRoute-circuittoegang tot het klassieke implementatiemodel uitschakelen

Voer de volgende cmdlets uit om de toegang tot het klassieke implementatiemodel uit te schakelen.

1. Meer informatie over het ExpressRoute-circuit.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Stel 'Klassieke bewerkingen toestaan' in op FALSE.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Werk het circuit bij. Nadat deze bewerking is voltooid, u het circuit niet meer bekijken in het klassieke implementatiemodel.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>Volgende stappen

* [Routering voor uw ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-arm.md)
* [Koppel uw virtuele netwerk aan uw ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
