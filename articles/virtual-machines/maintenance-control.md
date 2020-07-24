---
title: Overzicht van onderhouds beheer voor virtuele Azure-machines met behulp van de Azure Portal
description: Meer informatie over hoe u kunt bepalen wanneer onderhoud wordt toegepast op uw Azure-Vm's met behulp van onderhouds beheer.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: c3e914b904b0f6f1d3a4fae6c43c81cdf4eae819
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080009"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>Platform updates beheren met onderhouds controle 

Platform updates beheren, die niet opnieuw moeten worden opgestart, met behulp van onderhouds beheer. Azure werkt de infra structuur regel matig bij om de betrouw baarheid, prestaties en beveiliging te verbeteren of nieuwe functies te starten. De meeste updates zijn transparant voor gebruikers. Sommige gevoelige werk belastingen, zoals games, mediastreaming en financiële trans acties, kunnen niet worden toegestaan tot zelfs enkele seconden van een VM-bevriezing of de verbinding met het beheer voor onderhoud. Onderhouds beheer biedt u de mogelijkheid te wachten op platform updates en deze toe te passen binnen een 35-daagse venster. 

Met de onderhouds controle kunt u bepalen wanneer u updates wilt Toep assen op uw geïsoleerde Vm's en voor Azure toegewezen hosts.

Met onderhouds controle kunt u het volgende doen:
- Batch updates in één update pakket.
- Wacht tot 35 dagen om updates toe te passen. 
- Platform updates voor uw onderhouds venster automatiseren met [Azure functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).
- Onderhouds configuraties werken in abonnementen en resource groepen. 

## <a name="limitations"></a>Beperkingen

- Vm's moeten op een [specifieke host](./linux/dedicated-hosts.md)staan of worden gemaakt met behulp van een [geïsoleerde VM-grootte](isolation.md).
- Na 35 dagen wordt een update automatisch toegepast.
- De gebruiker moet toegang hebben tot de **resource bijdrager** .

## <a name="management-options"></a>Beheer opties

U kunt onderhouds configuraties maken en beheren met een van de volgende opties:

- [Azure CLI](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure-portal](maintenance-control-portal.md)

Zie [onderhouds updates plannen met onderhouds beheer en Azure functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler)voor een Azure functions voor beeld.

## <a name="next-steps"></a>Volgende stappen

Zie [onderhoud en updates](maintenance-and-updates.md)voor meer informatie.
