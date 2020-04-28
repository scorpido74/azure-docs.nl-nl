---
title: 'Een mislukt circuit opnieuw instellen-ExpressRoute: Power shell: Azure | Microsoft Docs'
description: Dit artikel helpt u bij het opnieuw instellen van een ExpressRoute-circuit met de status mislukt.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: deeb1c65cae7e3a5b42230dbda1ad8efa717ba0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73748103"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Een ExpressRoute-circuit met fouten opnieuw instellen

Wanneer een bewerking in een ExpressRoute-circuit niet kan worden voltooid, kan het circuit de status mislukt hebben. Dit artikel helpt u bij het opnieuw instellen van een mislukt Azure ExpressRoute-circuit.

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="reset-a-circuit"></a>Een circuit opnieuw instellen

1. Installeer de meest recente versie van de PowerShell-cmdlets van Azure Resource Manager. Zie [Azure PowerShell installeren en configureren](/powershell/azure/install-az-ps) voor meer informatie.

2. Open de PowerShell-console met verhoogde rechten en maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. Als u meerdere Azure-abonnementen hebt, controleert u de abonnementen voor het account.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. Geef het abonnement op dat u wilt gebruiken.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. Voer de volgende opdrachten uit om een circuit met de status mislukt opnieuw in te stellen:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

Het circuit zou nu in orde moeten zijn. Open een ondersteunings ticket met [ondersteuning van micro soft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) als het circuit nog steeds de status Mislukt heeft.

## <a name="next-steps"></a>Volgende stappen

Open een ondersteunings ticket met [micro soft ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) als u nog steeds problemen ondervindt.
