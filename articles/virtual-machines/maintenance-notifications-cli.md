---
title: Ontvang onderhoudsmeldingen via de CLI
description: Bekijk onderhoudsmeldingen voor virtuele machines die in Azure worden uitgevoerd en start selfserviceonderhoud met de Azure CLI.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 4ad57c1c71a51f948bd405a5487a1e27e36bfff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920889"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Geplande onderhoudsmeldingen verwerken met de Azure CLI

**Dit artikel is van toepassing op virtuele machines met zowel Linux als Windows.**

U de CLI gebruiken om te zien wanneer VM's zijn gepland voor [onderhoud.](maintenance-notifications.md) Geplande onderhoudsinformatie is beschikbaar via [de AZ VM get-instance-view.](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view)
 
Onderhoudsinformatie wordt alleen geretourneerd als er onderhoud is gepland. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Onderhoud starten

De volgende aanroep start het `IsCustomerInitiatedMaintenanceAllowed` onderhoud van een vm als deze is ingesteld op true.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Klassieke implementaties

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Als u nog steeds verouderde VM's hebt die zijn geïmplementeerd met behulp van het klassieke implementatiemodel, u de klassieke AZURE-CLI gebruiken om vm's op te vragen en onderhoud te starten.

Zorg ervoor dat u in de juiste modus bent om met klassieke VM te werken door te typen:

```
azure config mode asm
```

Typ het type als u de onderhoudsstatus van een VM met de naam *myVM*wilt krijgen:

```
azure vm show myVM 
``` 

Typ: *myVM* *myService* *myDeployment*

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Volgende stappen

U ook gepland onderhoud afhandelen via de [Azure PowerShell](maintenance-notifications-powershell.md) of [portal.](maintenance-notifications-portal.md)
