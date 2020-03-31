---
title: vCPU-quota voor Azure
description: Meer informatie over vCPU-quota voor Azure.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 20017f42031e126bbdae40f66bf4c36efd12f17c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75893470"
---
# <a name="virtual-machine-vcpu-quotas"></a>vCPU-quota virtuele machines

De vCPU-quota voor virtuele machines en virtuele machineschaalsets zijn gerangschikt in twee lagen voor elk abonnement, in elke regio. De eerste laag is de Total Regional vCPU's, en de tweede laag is de verschillende VM-grootte familie kernen, zoals de D-serie vCPU's. Elke keer dat een nieuwe vm wordt geïmplementeerd, mogen de vCPU's voor de VM het vCPU-quotum voor de vm-groottefamilie of het totale regionale vCPU-quotum niet overschrijden. Als een van deze quota wordt overschreden, is de VM-implementatie niet toegestaan. Er is ook een quotum voor het totale aantal virtuele machines in de regio. De details over elk van deze quota zijn te zien in de sectie **Gebruik + quota** van de pagina **Abonnement** in de [Azure-portal](https://portal.azure.com)of u de waarden opvragen met PowerShell.

   
 
## <a name="check-usage"></a>Gebruik controleren

U de [cmdlet Get-AzVMUsage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmusage) gebruiken om uw quotumgebruik te controleren.

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

De output zal er hetzelfde uitzien als dit:

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count
```


## <a name="reserved-vm-instances"></a>Gereserveerde VM-instanties
Gereserveerde VM-exemplaren, die zijn uitgerust met één abonnement zonder flexibiliteit in vm-grootte, voegen een nieuw aspect toe aan de vCPU-quota. Deze waarden beschrijven het aantal exemplaren van de opgegeven grootte dat in het abonnement moet kunnen worden geïmplementeerd. Ze werken als tijdelijke aanduiding in het quotasysteem om ervoor te zorgen dat quota is gereserveerd om ervoor te zorgen dat gereserveerde VM-exemplaren in het abonnement kunnen worden geïmplementeerd. Als een specifiek abonnement bijvoorbeeld 10 Standard_D1 gereserveerde VM-exemplaren heeft, is de gebruikslimiet voor Standard_D1 gereserveerde VM-exemplaren 10. Hierdoor zorgt Azure ervoor dat er altijd ten minste 10 vCPU's beschikbaar zijn in het Total Regional vCPU-quotum voor Standard_D1 gevallen en zijn er ten minste 10 vCPU's beschikbaar in het Standard D Family vCPU-quotum voor Standard_D1 exemplaren.

Als er een quotumverhoging nodig is om een RI met één abonnement aan te schaffen, u [een quotumverhoging](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) aanvragen voor uw abonnement.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits?toc=/azure/billing/TOC.json)voor meer informatie over facturering en quota.
