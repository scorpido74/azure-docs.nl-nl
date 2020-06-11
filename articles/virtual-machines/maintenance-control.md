---
title: Overzicht van onderhouds beheer voor virtuele Azure-machines met behulp van de Azure Portal
description: Meer informatie over hoe u kunt bepalen wanneer onderhoud wordt toegepast op uw Azure-Vm's met behulp van onderhouds beheer.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 950f4cfda73b40db0de8ba035868573cda1a5017
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84675791"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>Platform updates beheren met onderhouds controle 

Platform updates beheren, die niet opnieuw moeten worden opgestart, met behulp van onderhouds beheer. Azure werkt de infra structuur regel matig bij om de betrouw baarheid, prestaties en beveiliging te verbeteren of nieuwe functies te starten. De meeste updates zijn transparant voor gebruikers. Sommige gevoelige werk belastingen, zoals games, mediastreaming en financiële trans acties, kunnen niet worden toegestaan tot zelfs enkele seconden van een VM-bevriezing of de verbinding met het beheer voor onderhoud. Onderhouds beheer biedt u de mogelijkheid te wachten op platform updates en deze toe te passen binnen een 35-daagse venster. 

Met de onderhouds controle kunt u bepalen wanneer u updates wilt Toep assen op uw geïsoleerde Vm's en voor Azure toegewezen hosts.

Met onderhouds controle kunt u het volgende doen:
- Batch updates in één update pakket.
- Wacht tot 35 dagen om updates toe te passen. 
- Platform updates voor uw onderhouds venster automatiseren met Azure Functions.
- Onderhouds configuraties werken in abonnementen en resource groepen. 

## <a name="limitations"></a>Beperkingen

- Vm's moeten op een [specifieke host](./linux/dedicated-hosts.md)staan of worden gemaakt met behulp van een [geïsoleerde VM-grootte](./linux/isolation.md).
- Na 35 dagen wordt een update automatisch toegepast.
- De gebruiker moet toegang hebben tot de **resource bijdrager** .

## <a name="management-options"></a>Beheer opties

U kunt onderhouds configuraties maken en beheren met een van de volgende opties:

- [Azure-CLI](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure-portal](maintenance-control-portal.md)

## <a name="next-steps"></a>Volgende stappen

Zie [onderhoud en updates](maintenance-and-updates.md)voor meer informatie.
