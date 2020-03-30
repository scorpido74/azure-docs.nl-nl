---
title: Een time-out van load balancer TCP inAzure configureren
titleSuffix: Azure Load Balancer
description: In dit artikel leest u hoe u een time-out van Azure Load Balancer TCP-idle configureert.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: d0bb73b58aa23e5f7eb784772acf37b05df463ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456825"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>TCP-instellingen voor idle time-out configureren voor Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor dit artikel versie 5.4.1 of hoger van de Azure PowerShell-module vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="tcp-idle-timeout"></a>TCP-inactieve time-out
Azure Load Balancer heeft een niet-actieve time-outinstelling van 4 minuten tot 30 minuten. Standaard is het ingesteld op 4 minuten. Als een periode van inactiviteit langer is dan de time-outwaarde, is er geen garantie dat de TCP- of HTTP-sessie wordt gehandhaafd tussen de client en uw cloudservice.

Wanneer de verbinding wordt gesloten, kan uw clienttoepassing het volgende foutbericht ontvangen: "De onderliggende verbinding is gesloten: een verbinding die naar verwachting in leven zou worden gehouden, is door de server gesloten."

Een gangbare praktijk is het gebruik van een TCP keep-alive. Deze praktijk houdt de verbinding langer actief. Zie deze [.NET-voorbeelden](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)voor meer informatie . Met keep-alive ingeschakeld, pakketten worden verzonden tijdens perioden van inactiviteit op de verbinding. Keep-alive pakketten zorgen ervoor dat de niet-actieve time-outwaarde niet wordt bereikt en dat de verbinding gedurende een lange periode wordt onderhouden.

De instelling werkt alleen voor binnenkomende verbindingen. Om te voorkomen dat de verbinding verloren gaat, configureert u de TCP-instand met een interval dat minder is dan de inactieve time-outinstelling of verhoogt u de inactieve time-outwaarde. Om deze scenario's te ondersteunen, is ondersteuning toegevoegd voor een configureerbare niet-actieve time-out.

TCP keep-alive werkt voor scenario's waarin de levensduur van de batterij geen beperking is. Het wordt niet aanbevolen voor mobiele toepassingen. Het gebruik van een TCP-inlevende in een mobiele toepassing kan de batterij van het apparaat sneller leeglaten.

![TCP-time-out](./media/load-balancer-tcp-idle-timeout/image1.png)

In de volgende secties wordt beschreven hoe u inactieve time-outinstellingen voor openbare IP- en load balancer-resources wijzigen.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>De TCP-time-out voor uw openbaar IP-exemplaar op instantieniveau configureren tot 15 minuten

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` is optioneel. Als deze niet is ingesteld, is de standaardtime-out 4 minuten. Het aanvaardbare time-out bereik is 4 tot 30 minuten.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>De TCP-time-out instellen op een regel die in balans is met de belasting

Als u de niet-actieve time-out voor een load balancer wilt instellen, wordt de 'IdleTimeoutInMinutes' ingesteld op de regel voor load-balanced. Bijvoorbeeld:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Volgende stappen

[Overzicht van interne load balancer](load-balancer-internal-overview.md)

[Aan de slag met het configureren van een op internet gerichte load balancer](quickstart-create-standard-load-balancer-powershell.md)

[Een distributiemodus voor de load balancer configureren](load-balancer-distribution-mode.md)
