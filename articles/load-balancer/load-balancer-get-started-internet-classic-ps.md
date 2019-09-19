---
title: Een internetgerichte load balancer maken - klassieke versie van Azure PowerShell
titlesuffix: Azure Load Balancer
description: Meer informatie over het maken van een internetgerichte load balancer in de klassieke modus met PowerShell
services: load-balancer
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: 7bfe5a78f9725205db52fe6790d8a261752cfc4b
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090881"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Aan de slag met het maken van een internetgerichte load balancer (klassiek) in PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure-CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Voordat u met Azure-resources gaat werken, is het belang rijk te weten dat Azure momenteel twee implementatie modellen heeft: Azure Resource Manager en klassiek. Zorg ervoor dat u begrijpt wat [implementatiemodellen en hulpprogramma's](../azure-classic-rm.md) zijn voordat u met een Azure-resource gaat werken. U kunt de documentatie voor verschillende hulpprogramma's bekijken door op de tabbladen boven aan dit artikel te klikken. Dit artikel is van toepassing op het klassieke implementatiemodel. Hier vindt u [meer informatie over hoe u een internetgerichte load balancer maakt met Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-load-balancer-using-powershell"></a>Een load balancer instellen met behulp van PowerShell

Volg de onderstaande stappen om een load balancer in te stellen met behulp van PowerShell:

1. Als u Azure PowerShell nog niet eerder hebt gebruikt, kunt u [Azure PowerShell installeren en configureren](/powershell/azure/overview) raadplegen en de instructies helemaal tot aan het einde volgen om u aan te melden bij Azure en uw abonnement te selecteren.
2. Nadat u een virtuele machine hebt gemaakt, kunt u PowerShell-cmdlets gebruiken om een load balancer toe te voegen aan een virtuele machine binnen dezelfde cloudservice.

In het volgende voorbeeld voegt u een set met gelijke taakverdeling met de naam webfarm toe aan de cloudservice mytestcloud (of myctestcloud.cloudapp.net) en voegt u de eindpunten voor de load balancer toe aan virtuele machines met de naam web1 en web2. De load balancer ontvangt netwerkverkeer via poort 80 en verdeelt taken tussen virtuele machines die zijn gedefinieerd door het lokale eindpunt (in dit geval poort 80) met TCP.

### <a name="step-1"></a>Stap 1

Maak een eindpunt met gelijke taakverdeling voor de eerste VM 'web1'

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

### <a name="step-2"></a>Stap 2

Maak een ander eindpunt voor de tweede VM 'web2' met dezelfde naam voor de set met gelijke taakverdeling

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Verwijder een virtuele machine uit een load balancer

U kunt Remove-AzureEndpoint gebruiken om het eindpunt van een virtuele machine uit de load balancer te verwijderen

```powershell
Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin | Update-AzureVM
```

## <a name="next-steps"></a>Volgende stappen

U kunt ook [aan de slag gaan met het maken van een interne load balancer](load-balancer-get-started-ilb-classic-ps.md) en configureren welk type [distributiemodus](load-balancer-distribution-mode.md) er moet worden gebruikt voor specifiek gedrag voor netwerkverkeer van de load balancer.

Als uw toepassing verbindingen actief moet houden voor servers achter een load balancer, krijgt u meer inzicht in [niet-actieve TCP-time-outinstellingen voor een load balancer](load-balancer-tcp-idle-timeout.md). Op deze manier krijgt u meer informatie over het gedrag van niet-actieve verbindingen wanneer u Azure Load Balancer gebruikt.
