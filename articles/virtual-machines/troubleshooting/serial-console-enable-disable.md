---
title: De Azure Serial console in-en uitschakelen | Microsoft Docs
description: De Azure Serial console-service in-en uitschakelen
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: e09e08f8ba36cf576bc27551254225adee3bb0fd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451308"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>De Azure Serial console in-en uitschakelen

Net als elke andere resource kan de Azure-seriële console worden in-en uitgeschakeld. Seriële console is standaard ingeschakeld voor alle abonnementen in het wereld wijde Azure. Op dit moment wordt de service voor uw hele abonnement uitgeschakeld door de seriële console uit te scha kelen. Voor het uitschakelen of opnieuw inschakelen van een seriële console voor een abonnement is toegang op Inzender niveau of hoger vereist voor het abonnement.

U kunt ook de seriële console uitschakelen voor een afzonderlijke VM of een installatie kopie van een virtuele machine met behulp van diagnostische gegevens over opstarten uitschakelen. U hebt toegang tot Inzender niveau of hoger nodig op zowel de VM/virtuele-machine schaalset als het opslag account voor diagnostische gegevens over opstarten.

## <a name="vm-level-disable"></a>VM-niveau uitschakelen
De seriële console kan worden uitgeschakeld voor een specifieke VM of schaalset voor virtuele machines door de instelling diagnostische gegevens over opstarten uit te scha kelen. Schakel diagnostische gegevens over opstarten uit van de Azure Portal om de seriële console uit te scha kelen voor de virtuele machine of de schaalset van de VM. Als u een seriële console gebruikt voor een schaalset voor virtuele machines, moet u ervoor zorgen dat u de instanties van de schaalset voor virtuele machines bijwerkt naar het meest recente model.


## <a name="subscription-level-enabledisable"></a>Op het niveau van het abonnement inschakelen/uitschakelen

> [!NOTE]
> Zorg ervoor dat u zich in de juiste Cloud bevindt (Azure Public Cloud, Azure U.S. Government Cloud) voordat u deze opdracht uitvoert. U kunt controleren met `az cloud list` en uw Cloud instellen met `az cloud set -n <Name of cloud>`.

### <a name="azure-cli"></a>Azure-CLI

Seriële console kunnen worden uitgeschakeld en opnieuw worden ingeschakeld voor een volledig abonnement met behulp van de volgende opdrachten in de Azure CLI (gebruik de knop ' proberen ') om een exemplaar van de Azure Cloud Shell te starten waarin u de opdrachten kunt uitvoeren):

Als u de seriële console voor een abonnement wilt uitschakelen, gebruikt u de volgende opdrachten:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Gebruik de volgende opdrachten om seriële console in te scha kelen voor een abonnement:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Gebruik de volgende opdrachten om de huidige ingeschakelde/uitgeschakelde status van de seriële console voor een abonnement te verkrijgen:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

### <a name="powershell"></a>PowerShell

Seriële console kunnen ook worden ingeschakeld en uitgeschakeld met behulp van Power shell.

Als u de seriële console voor een abonnement wilt uitschakelen, gebruikt u de volgende opdrachten:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Gebruik de volgende opdrachten om seriële console in te scha kelen voor een abonnement:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [Azure Serial console voor Linux vm's](./serial-console-linux.md)
* Meer informatie over de [Azure Serial console voor Windows-vm's](./serial-console-windows.md)
* Meer informatie over [Opties voor energie beheer in de Azure seriële console](./serial-console-power-options.md)