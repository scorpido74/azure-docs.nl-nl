---
title: Quotafouten
description: Beschrijft hoe u resourcequotafouten oplossen bij het implementeren van resources met Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 410b086b39d63d03491d390364f4aec6300fc7c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273785"
---
# <a name="resolve-errors-for-resource-quotas"></a>Resolve errors for resource quotas (Fouten voor resourcequota oplossen)

In dit artikel worden quotafouten beschreven die u tegenkomen bij het implementeren van resources.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptoom

Als u een sjabloon implementeert waarmee resources worden gemaakt die uw Azure-quota overschrijden, krijgt u een implementatiefout die lijkt op:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Of, u zien:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Oorzaak

Quota worden toegepast per resourcegroep, abonnement, account en andere bereiken. Uw abonnement mag bijvoorbeeld worden geconfigureerd om het aantal kerngeheugens voor een regio te beperken. Als u een virtuele machine probeert te implementeren met meer kerngeheugens dan is toegestaan, ontvangt u een foutmelding die aangeeft dat het quotum is overschreden.
Zie [Azure-abonnements- en servicelimieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor volledige quotumgegevens.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="azure-cli"></a>Azure-CLI

Gebruik de `az vm list-usage` opdracht voor Azure CLI om quota voor virtuele machines te vinden.

```azurecli
az vm list-usage --location "South Central US"
```

Welke rendementen:

```output
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

Gebruik voor PowerShell de opdracht **Get-AzVMUsage** om quota voor virtuele machines te vinden.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Welke rendementen:

```output
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Oplossing

Als u een quotumverhoging wilt aanvragen, gaat u naar de portal en slaat u een ondersteuningsprobleem in. Vraag in het ondersteuningsprobleem een verhoging van uw quotum aan voor de regio waarin u wilt implementeren.

> [!NOTE]
> Houd er rekening mee dat voor resourcegroepen het quotum voor elke afzonderlijke regio is, niet voor het hele abonnement. Als u 30 kernen in West-VS moet implementeren, moet u 30 Resource Manager-kernen in West-VS aanvragen. Als u 30 cores moet implementeren in een van de regio's waartoe u toegang hebt, moet u vragen om 30 Resource Manager-kernen in alle regio's.
>
>

1. Selecteer **Abonnementen**.

   ![Abonnementen](./media/error-resource-quota/subscriptions.png)

2. Selecteer het abonnement waarvoor het quotum moet worden verhoogd.

   ![Abonnement selecteren](./media/error-resource-quota/select-subscription.png)

3. Selecteer **Gebruik + quota**

   ![Gebruiken en quota selecteren](./media/error-resource-quota/select-usage-quotas.png)

4. Selecteer In de rechterbovenhoek de optie **Aanvraag verhogen**.

   ![Aanvraagverhoging](./media/error-resource-quota/request-increase.png)

5. Vul de formulieren in voor het type quotum dat u wilt verhogen.

   ![Formulier invullen](./media/error-resource-quota/forms.png)