---
title: De portal gebruiken om Azure spot-Vm's te implementeren
description: Meer informatie over het gebruik van Azure PowerShell voor het implementeren van spot-Vm's om kosten op te slaan.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/25/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 6e7723a437e90807063e3c3b7af2bf068dca5b9f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82100648"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Plaats virtuele machines met behulp van de Azure Portal

Met behulp van [Spot vm's](spot-vms.md) kunt u profiteren van onze ongebruikte capaciteit tegen een aanzienlijke kosten besparing. Op elk moment dat Azure de capaciteit nodig heeft, verwijdert de Azure-infra structuur spot Vm's. Daarom zijn de virtuele machines geschikt voor werk belastingen die onderbrekingen kunnen afhandelen, zoals batch verwerkings taken, ontwikkel-en test omgevingen, grootschalige werk belastingen en meer.

Prijzen voor spot Vm's zijn variabel, op basis van de regio en de SKU. Zie prijzen voor VM'S voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie. Zie [Spot vm's-prijzen](spot-vms.md#pricing)voor meer informatie over het instellen van de maximum prijs.

U hebt de mogelijkheid om een maximum prijs voor de virtuele machine in te stellen die u wilt betalen, per uur. De maximale prijs voor een steun-VM kan worden ingesteld in Amerikaanse dollars (USD), met Maxi maal vijf decimalen. De waarde `0.05701` is bijvoorbeeld een maximum prijs van $0,05701 USD per uur. Als u de maximale prijs instelt op `-1` , wordt de VM niet verwijderd op basis van de prijs. De prijs voor de virtuele machine is de huidige prijs voor steun of de prijs voor een standaard-VM, die ooit kleiner is, zolang er capaciteit en quota beschikbaar zijn.


## <a name="create-the-vm"></a>De virtuele machine maken

Het proces voor het maken van een virtuele machine met behulp van spot Vm's is hetzelfde als in de [Snelstartgids](quick-create-portal.md). Wanneer u een virtuele machine implementeert, kunt u ervoor kiezen om een Azure spot-instantie te gebruiken.


Op het tabblad **basis beginselen** , in de sectie **Details van exemplaar** , is **Nee** de standaard waarde voor het gebruik van een Azure spot-instantie.

![Scherm opname voor het kiezen van Nee, gebruik geen Azure spot-instantie](media/spot-portal/no.png)

Als u **Ja**selecteert, wordt de sectie uitgebreid en kunt u het [verwijderings type en verwijderings beleid](spot-vms.md#eviction-policy)kiezen. 

![Scherm opname voor het kiezen van Ja, een Azure spot-instantie gebruiken](media/spot-portal/yes.png)


## <a name="next-steps"></a>Volgende stappen

U kunt ook spot Vm's maken met behulp van [Power shell](spot-powershell.md).