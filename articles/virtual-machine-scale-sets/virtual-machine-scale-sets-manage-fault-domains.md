---
title: Foutdomeinen beheren in Azure-schaalsets voor virtuele machines
description: Meer informatie over het kiezen van het juiste aantal fd's tijdens het maken van een virtuele machineschaalset.
author: rajsqr
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: drewm
ms.openlocfilehash: 297837354cea4bb5ccdcc03261810dcffd144243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275732"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Het juiste aantal foutdomeinen kiezen voor een virtuele-machineschaalset
Virtuele machineschaalsets worden standaard gemaakt met vijf foutdomeinen in Azure-regio's zonder zones. Voor de regio's die de zonale implementatie van virtuele machineschaalsets ondersteunen en deze optie is geselecteerd, is de standaardwaarde van het aantal foutdomeinen 1 voor elk van de zones. FD=1 impliceert in dit geval dat de VM-exemplaren die behoren tot de schaalset op basis van de beste inspanning over vele racks zullen worden verspreid.

U ook overwegen om het aantal foutdomeinen van de schaalsetfout af te stemmen op het aantal foutdomeinen managed disks. Deze uitlijning kan helpen voorkomen dat het quorum verlies als een volledig foutdomein van beheerde schijven uitvalt. Het aantal FD-gegevens kan worden ingesteld op minder dan of gelijk aan het aantal foutdomeinen voor beheerde schijven dat beschikbaar is in elk van de regio's. Raadpleeg dit [document](../virtual-machines/windows/manage-availability.md) voor meer informatie over het aantal foutdomeinen voor beheerde schijven per regio.

## <a name="rest-api"></a>REST API
U de `properties.platformFaultDomainCount` eigenschap instellen op 1, 2 of 3 (standaard van 5 als deze niet is opgegeven). Raadpleeg [hier](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)de documentatie voor REST API.

## <a name="azure-cli"></a>Azure-CLI
U de `--platform-fault-domain-count` parameter instellen op 1, 2 of 3 (standaard van 5 als deze niet is opgegeven). Raadpleeg [hier](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)de documentatie voor Azure CLI .

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

Het duurt enkele minuten om alle schaalsetresources en VM's te maken en te configureren.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [beschikbaarheids- en redundantiefuncties](../virtual-machines/windows/availability.md) voor Azure-omgevingen.
