---
title: vCPU quota's voor Azure | Microsoft Docs
description: Meer informatie over vCPU-quota's voor Azure.
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
ms.openlocfilehash: add0170c016b1a8226424ccf9b25cfde3a4c196f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091425"
---
# <a name="virtual-machine-vcpu-quotas"></a>vCPU-quota virtuele machines

De vCPU-quota's voor virtuele machines en virtuele-machine schaal sets worden gerangschikt in twee lagen voor elk abonnement, in elke regio. De eerste laag is het totale regionale Vcpu's en de tweede laag is de verschillende kernen van de VM-grootte, zoals de Vcpu's van de D-serie. Telkens wanneer een nieuwe virtuele machine wordt geïmplementeerd, mag de Vcpu's voor de virtuele machine niet groter zijn dan het vCPU quotum voor de VM-grootte familie of het totale regionale vCPU-quotum. Als een van deze quota wordt overschreden, is de implementatie van de VM niet toegestaan. Er is ook een quotum voor het totale aantal virtuele machines in de regio. De details van elk van deze quota's kunnen worden weer gegeven in de sectie **gebruik en quota's** van de pagina **abonnement** in de [Azure Portal](https://portal.azure.com), of u kunt een query uitvoeren voor de waarden met behulp van de Azure cli.


## <a name="check-usage"></a>Gebruik controleren

U kunt het quotum gebruik controleren met behulp van [AZ VM List-Usage](/cli/azure/vm).

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

De uitvoer ziet er ongeveer als volgt:


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
Gereserveerde VM-instanties, die zijn afgestemd op één abonnement zonder flexibiliteit voor VM-grootte, zullen een nieuw aspect toevoegen aan de vCPU quota's. Deze waarden beschrijven het aantal exemplaren van de opgegeven grootte dat in het abonnement moet worden geïmplementeerd. Ze werken als tijdelijke aanduiding in het quota systeem om ervoor te zorgen dat het quotum wordt gereserveerd zodat Azure-reserve ringen in het abonnement kunnen worden geïmplementeerd. Als een specifiek abonnement bijvoorbeeld 10 Standard_D1-reserve ringen heeft, is de limiet voor gebruik voor Standard_D1-reserve ringen 10. Dit zorgt ervoor dat Azure in het totale regionale Vcpu's-quotum altijd ten minste 10 Vcpu's beschikbaar is om te worden gebruikt voor Standard_D1-instanties en dat er ten minste 10 Vcpu's beschikbaar zijn in het vCPU-quotum van de standaard D-serie om te worden gebruikt voor Standard_D1-exemplaren.

Als een quotum toename vereist is om één enkel abonnement RI te kopen, kunt u [een quotum verhoging aanvragen](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) voor uw abonnement.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure-abonnement en service limieten, quota's en beperkingen](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json)voor meer informatie over facturering en quota's.
