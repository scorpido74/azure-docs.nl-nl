---
title: Service kosten verminderen met behulp van Azure Advisor | Microsoft Docs
description: Gebruik Azure Advisor om de kosten van uw Azure-implementaties te optimaliseren.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 144b63131be68c9209835a1b8b3a01062245cfdd
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326579"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Service kosten verminderen met behulp van Azure Advisor

Advisor helpt u uw totale Azure-uitgaven te optimaliseren en te verlagen door inactieve en gelaagde bronnen te identificeren. U kunt aanbevelingen ontvangen op het tabblad **kosten** van het Advisor-dash board.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimaliseer de uitgaven van de virtuele machine door het formaat ervan te wijzigen of door ondergebruikte instanties te afsluiten 

Hoewel bepaalde toepassings scenario's kunnen leiden tot een laag gebruik door ontwerp, bespaart u vaak geld door de grootte en het aantal van uw virtuele machines te beheren. Advisor bewaakt het gebruik van uw virtuele machines gedurende 7 dagen en identificeert vervolgens virtuele machines met weinig gebruik.  Virtuele machines worden beschouwd als laag gebruik als het CPU-gebruik 5% of minder is en het netwerk gebruik lager is dan 2% en de drempel waarde voor geheugen druk nummers heeft of als de huidige werk belasting kan worden aangepast aan de grootte van een kleinere virtuele machine.

De aanbevolen acties zijn uitgeschakeld of worden verg root of verkleind, wat specifiek is voor de resource die wordt aanbevolen voor. Advisor toont u de geschatte kosten besparingen voor de aanbevolen acties/formaat wijzigen of afsluiten. Voor de aanbevolen actie voor verg Roten/verkleinen biedt Advisor ook huidige en doel-SKU-gegevens. 

Als u meer wilt weten over het identificeren van gefactureerde virtuele machines, kunt u de regel voor het CPU-gebruik aanpassen op basis van een abonnement.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Kosten verlagen door oningerichte ExpressRoute-circuits te elimineren

Advisor identificeert ExpressRoute-circuits die de provider status hebben van *niet* meer dan één maand, en raadt u aan het circuit te verwijderen als u niet van plan bent om het circuit in te richten met uw connectiviteits provider.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Kosten verlagen door niet-actieve virtuele netwerk gateways te verwijderen of opnieuw te configureren

Advisor identificeert virtuele netwerk gateways die gedurende meer dan 90 dagen inactief zijn geweest. Omdat deze gateways per uur worden gefactureerd, moet u overwegen om deze te configureren of te verwijderen als u deze niet meer wilt gebruiken. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Gereserveerde instanties van virtuele machines kopen om geld te besparen op kosten voor betalen per gebruik

Advisor controleert uw gebruik van de virtuele machine in de afgelopen 30 dagen en bepaalt of u geld kunt besparen door een Azure-reserve ring aan te schaffen. Advisor toont u de regio's en grootten waar u mogelijk de meeste besparingen hebt en toont u de geschatte besparingen bij het aanschaffen van reserve ringen. Met Azure-reserve ringen kunt u de basis kosten voor uw virtuele machines vooraf aanschaffen. Kortingen worden automatisch toegepast op nieuwe of bestaande virtuele machines met dezelfde grootte en regio als uw reserve ringen. [Meer informatie over Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor waarschuwt u ook voor gereserveerde instanties die u in de komende 30 dagen verloopt. U wordt aangeraden nieuwe gereserveerde instanties te kopen om te voor komen dat betalen per gebruik wordt betaald.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Niet-gekoppelde open bare IP-adressen verwijderen om geld te besparen

Advisor identificeert open bare IP-adressen die momenteel niet zijn gekoppeld aan Azure-resources, zoals load balancers of Vm's. Deze open bare IP-adressen worden gefactureerd met een nominale belasting. Als u niet van plan bent om ze te gebruiken, kunnen de kosten worden bespaard als ze worden verwijderd.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Azure Data Factory pijp lijnen verwijderen die niet werken

Azure Advisor detecteert Azure Data Factory pijp lijnen die herhaaldelijk mislukken en raadt u aan om de problemen op te lossen of de mislukte pijp lijnen te verwijderen als ze niet meer nodig zijn. U wordt gefactureerd voor deze pijp lijnen, zelfs als ze niet voor u zijn. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Standaard momentopnamen gebruiken voor Managed Disks
Voor het besparen van 60% kosten, raden we u aan om uw moment opnamen op te slaan in de standaard opslag, ongeacht het opslag type van de bovenliggende schijf. Dit is de standaard optie voor het Managed Disks moment opnamen. Azure Advisor worden moment opnamen geïdentificeerd die zijn opgeslagen Premium Storage en raden u aan om uw moment opname te migreren van Premium naar standaard opslag. [Meer informatie over prijzen voor beheerde schijven](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Toegang tot kosten aanbevelingen in Azure Advisor

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open vervolgens [Advisor](https://aka.ms/azureadvisordashboard).

2.  Klik op het Advisor-dash board op het tabblad **kosten** .

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Advisor-aanbevelingen:
* [Inleiding tot Advisor](advisor-overview.md)
* [Aan de slag](advisor-get-started.md)
* [Aanbevelingen voor Advisor-prestaties](advisor-cost-recommendations.md)
* [Aanbevelingen voor hoge Beschik baarheid van Advisor](advisor-cost-recommendations.md)
* [Aanbevelingen voor de beveiliging van Advisor](advisor-cost-recommendations.md)
