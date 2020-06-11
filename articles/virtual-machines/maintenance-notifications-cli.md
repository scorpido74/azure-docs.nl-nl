---
title: Onderhouds meldingen ophalen met de CLI
description: Bekijk onderhouds meldingen voor virtuele machines die in Azure worden uitgevoerd en start self-service onderhoud met behulp van de Azure CLI.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 289733c4cee23a37c26df0b613a470925756f0eb
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84674836"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Geplande onderhouds meldingen verwerken met Azure CLI

**Dit artikel is van toepassing op virtuele machines met Linux en Windows.**

U kunt de CLI gebruiken om te zien wanneer Vm's zijn gepland voor [onderhoud](maintenance-notifications.md). Informatie over gepland onderhoud is beschikbaar via [AZ VM Get-instance-View](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view).
 
Onderhouds informatie wordt alleen geretourneerd als er onderhoud wordt gepland. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Onderhoud starten

Met de volgende aanroep wordt het onderhoud op een VM gestart als deze `IsCustomerInitiatedMaintenanceAllowed` is ingesteld op waar.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Klassieke implementaties

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Als u nog steeds oudere Vm's hebt die zijn geïmplementeerd met behulp van het klassieke implementatie model, kunt u de klassieke Azure-CLI gebruiken om te zoeken naar Vm's en het onderhoud te initiëren.

Zorg ervoor dat u zich in de juiste modus bevindt voor gebruik met een klassieke virtuele machine door het volgende te typen:

```
azure config mode asm
```

Als u de onderhouds status van een virtuele machine met de naam *myVM*wilt ophalen, typt u:

```
azure vm show myVM 
``` 

Als u onderhoud wilt starten op uw klassieke virtuele machine met de naam *myVM* in de *myService* -service en *myDeployment* -implementatie, typt u:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Volgende stappen

U kunt gepland onderhoud ook afhandelen met behulp van de [Azure PowerShell](maintenance-notifications-powershell.md) of [Portal](maintenance-notifications-portal.md).
