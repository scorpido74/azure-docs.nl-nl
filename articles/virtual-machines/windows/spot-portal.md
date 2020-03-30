---
title: De portal gebruiken om Azure Spot VM's te implementeren
description: Meer informatie over het gebruik van Azure PowerShell om Spot VM's te implementeren om kosten te besparen.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 8845d0f9277384c1cc32b31b5ea5151cd0668c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77158975"
---
# <a name="preview-deploy-spot-vms-using-the-azure-portal"></a>Voorbeeld: SpotVM's implementeren met de Azure-portal

Met behulp van [Spot VM's](spot-vms.md) u profiteren van onze ongebruikte capaciteit tegen een aanzienlijke kostenbesparing. Op elk moment waarop Azure de capaciteit terug nodig heeft, wordt spotvm's van Spot v.s. Daarom zijn Spot VM's ideaal voor workloads die onderbrekingen kunnen verwerken, zoals batchverwerkingstaken, dev/testomgevingen, grote compute workloads en meer.

De prijzen voor spotVM's zijn variabel, gebaseerd op regio en SKU. Zie VM-prijzen voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie. Zie [Spot VM's - Prijzen](spot-vms.md#pricing)voor meer informatie over het instellen van de max.

Je hebt de mogelijkheid om een maximale prijs die u bereid bent te betalen, per uur, voor de VM. De maximale prijs voor een Spot VM kan worden ingesteld in Amerikaanse dollars (USD), met behulp van maximaal 5 decimalen. De waarde `0.05701`is bijvoorbeeld een maximale prijs van $ 0,05701 USD per uur. Als u de maximale `-1`prijs instelt, wordt de VM niet uitgezet op basis van de prijs. De prijs voor de VM zal de huidige prijs voor spot of de prijs voor een standaard VM, die ooit minder is, zolang er capaciteit en quotum beschikbaar is.

> [!IMPORTANT]
> Spotexemplaren staan momenteel in openbare preview.
> Deze preview-versie wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.
>


## <a name="create-the-vm"></a>De virtuele machine maken

Het proces voor het maken van een VM die Spot VM's gebruikt is hetzelfde als beschreven in de [quickstart.](quick-create-portal.md) Wanneer u een VM implementeert, u ervoor kiezen een Azure-spotinstantie te gebruiken.


Op het tabblad **Basisbeginselen** in de sectie **Instantiedetails** is **Nee** de standaardinstelling voor het gebruik van een Azure-steuninstantie.

![Schermopname voor het kiezen van nee, geen Azure-spotinstantie gebruiken](media/spot-portal/no.png)

Het selecteert u **Ja,** de sectie wordt uitgebreid en u uw [uitzettingstype en uitzettingsbeleid](spot-vms.md#eviction-policy)kiezen. 

![Schermopname voor het kiezen van ja, gebruik een Azure-spotinstantie](media/spot-portal/yes.png)


## <a name="next-steps"></a>Volgende stappen

U ook Spot VM's maken met [PowerShell](spot-powershell.md).