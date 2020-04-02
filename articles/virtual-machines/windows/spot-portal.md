---
title: De portal gebruiken om Azure Spot VM's te implementeren
description: Meer informatie over het gebruik van Azure PowerShell om Spot VM's te implementeren om kosten te besparen.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 045cec080b9b1b8f2e4cb589b053c421897db5be
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547390"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>SpotVM's implementeren met de Azure-portal

Met behulp van [Spot VM's](spot-vms.md) u profiteren van onze ongebruikte capaciteit tegen een aanzienlijke kostenbesparing. Op elk moment waarop Azure de capaciteit terug nodig heeft, wordt spotvm's van Spot v.s. Daarom zijn Spot VM's ideaal voor workloads die onderbrekingen kunnen verwerken, zoals batchverwerkingstaken, dev/testomgevingen, grote compute workloads en meer.

De prijzen voor spotVM's zijn variabel, gebaseerd op regio en SKU. Zie VM-prijzen voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie. Zie [Spot VM's - Prijzen](spot-vms.md#pricing)voor meer informatie over het instellen van de max.

Je hebt de mogelijkheid om een maximale prijs die u bereid bent te betalen, per uur, voor de VM. De maximale prijs voor een Spot VM kan worden ingesteld in Amerikaanse dollars (USD), met behulp van maximaal 5 decimalen. De waarde `0.05701`is bijvoorbeeld een maximale prijs van $ 0,05701 USD per uur. Als u de maximale `-1`prijs instelt, wordt de VM niet uitgezet op basis van de prijs. De prijs voor de VM zal de huidige prijs voor spot of de prijs voor een standaard VM, die ooit minder is, zolang er capaciteit en quotum beschikbaar is.


## <a name="create-the-vm"></a>De virtuele machine maken

Het proces voor het maken van een VM die Spot VM's gebruikt is hetzelfde als beschreven in de [quickstart.](quick-create-portal.md) Wanneer u een VM implementeert, u ervoor kiezen een Azure-spotinstantie te gebruiken.


Op het tabblad **Basisbeginselen** in de sectie **Instantiedetails** is **Nee** de standaardinstelling voor het gebruik van een Azure-steuninstantie.

![Schermopname voor het kiezen van nee, geen Azure-spotinstantie gebruiken](media/spot-portal/no.png)

Het selecteert u **Ja,** de sectie wordt uitgebreid en u uw [uitzettingstype en uitzettingsbeleid](spot-vms.md#eviction-policy)kiezen. 

![Schermopname voor het kiezen van ja, gebruik een Azure-spotinstantie](media/spot-portal/yes.png)


## <a name="next-steps"></a>Volgende stappen

U ook Spot VM's maken met [PowerShell](spot-powershell.md).