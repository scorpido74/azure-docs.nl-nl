---
title: vCPU-quota voor Azure
description: Meer informatie over vCPU-quota voor Azure.
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: c194dbeb0183e64535342f8aaf9a770a93b3e332
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896184"
---
# <a name="virtual-machine-vcpu-quotas"></a>vCPU-quota virtuele machines

De vCPU-quota voor virtuele machines en virtuele machineschaalsets zijn gerangschikt in twee lagen voor elk abonnement, in elke regio. De eerste laag is de Total Regional vCPU's, en de tweede laag is de verschillende VM-grootte familie kernen, zoals de D-serie vCPU's. Elke keer dat een nieuwe vm wordt geïmplementeerd, mogen de vCPU's voor de VM het vCPU-quotum voor de vm-groottefamilie of het totale regionale vCPU-quotum niet overschrijden. Als een van deze quota wordt overschreden, is de VM-implementatie niet toegestaan. Er is ook een quotum voor het totale aantal virtuele machines in de regio. De details over elk van deze quota zijn te zien in de sectie **Gebruik + quota** van de pagina **Abonnement** in de [Azure-portal](https://portal.azure.com)of u de waarden opvragen met de Azure CLI.


## <a name="check-usage"></a>Gebruik controleren

U uw quotumgebruik controleren met behulp van [het az vm-lijstgebruik.](/cli/azure/vm)

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

De uitvoer zal er ongeveer als volgt uitzien:


```
Name                                CurrentValue    Limit
--------------------------------  --------------  -------
Availability Sets                              0     2000
Total Regional vCPUs                          29      100
Virtual Machines                               7    10000
Virtual Machine Scale Sets                     0     2000
Standard DSv3 Family vCPUs                     8      100
Standard DSv2 Family vCPUs                     3      100
Standard Dv3 Family vCPUs                      2      100
Standard D Family vCPUs                        8      100
Standard Dv2 Family vCPUs                      8      100
Basic A Family vCPUs                           0      100
Standard A0-A7 Family vCPUs                    0      100
Standard A8-A11 Family vCPUs                   0      100
Standard DS Family vCPUs                       0      100
Standard G Family vCPUs                        0      100
Standard GS Family vCPUs                       0      100
Standard F Family vCPUs                        0      100
Standard FS Family vCPUs                       0      100
Standard Storage Managed Disks                 5    10000
Premium Storage Managed Disks                  5    10000
```

## <a name="reserved-vm-instances"></a>Gereserveerde VM-instanties
Gereserveerde VM-exemplaren, die zijn uitgerust met één abonnement zonder flexibiliteit in vm-grootte, voegen een nieuw aspect toe aan de vCPU-quota. Deze waarden beschrijven het aantal exemplaren van de opgegeven grootte dat in het abonnement moet kunnen worden geïmplementeerd. Ze werken als tijdelijke aanduiding in het quotasysteem om ervoor te zorgen dat quota is gereserveerd om ervoor te zorgen dat Azure-reserveringen in het abonnement kunnen worden geïmplementeerd. Als een specifiek abonnement bijvoorbeeld 10 Standard_D1 reserveringen heeft, is de gebruikslimiet voor Standard_D1 reserveringen 10. Hierdoor zorgt Azure ervoor dat er altijd ten minste 10 vCPU's beschikbaar zijn in het Total Regional vCPU-quotum voor Standard_D1 gevallen en zijn er ten minste 10 vCPU's beschikbaar in het Standard D Family vCPU-quotum voor Standard_D1 exemplaren.

Als er een quotumverhoging nodig is om een RI met één abonnement aan te schaffen, u [een quotumverhoging](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) voor uw abonnement aanvragen.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits?toc=/azure/billing/TOC.json)voor meer informatie over facturering en quota.
