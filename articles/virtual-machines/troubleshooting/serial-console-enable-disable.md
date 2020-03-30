---
title: De Azure Serial Console inschakelen en uitschakelen | Microsoft Documenten
description: De Azure Serial Console-service inschakelen en uitschakelen
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451308"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>De Azure Serial Console in- en uitschakelen

Net als elke andere bron kan de Azure Serial Console worden ingeschakeld en uitgeschakeld. Seriële console is standaard ingeschakeld voor alle abonnementen in het hele Azure.Serial Console is defaultd enabled for all subscriptions in global Azure. Als u momenteel Seriële console uitschakelt, schakelt u de service voor uw hele abonnement uit. Voor het uitschakelen of opnieuw inschakelen van Seriële console voor een abonnement is toegang tot het aantal bijdragen of hoger in het abonnement vereist.

U ook seriële console uitschakelen voor een afzonderlijke vm- of virtuele machineschaalsetinstantie door opstartdiagnoses uit te schakelen. U hebt toegang tot het niveau van de inzender nodig of hoger op zowel de VM/virtuele machineschaalset als uw opslagaccount voor opstartdiagnostiek.

## <a name="vm-level-disable"></a>VM-niveau uitschakelen
De seriële console kan worden uitgeschakeld voor een specifieke VM- of virtuele machineschaal die is ingesteld door de instelling voor opstartdiagnoses uit te schakelen. Schakel opstartdiagnoses uit de Azure-portal uit om de seriële console voor de VM of de virtuele machineschaalset uit te schakelen. Als u seriële console gebruikt op een virtuele machineschaalset, moet u ervoor zorgen dat u de exemplaren van de virtuele machineschaalschaal upgradet naar het nieuwste model.


## <a name="subscription-level-enabledisable"></a>Inschakeling op abonnementsniveau inschakelen/uitschakelen

> [!NOTE]
> Zorg ervoor dat u zich in de juiste cloud bevindt (Azure Public Cloud, Azure US Government Cloud) voordat u deze opdracht uitvoert. U `az cloud list` controleren met en `az cloud set -n <Name of cloud>`stel uw cloud met.

### <a name="azure-cli"></a>Azure-CLI

Seriële console kan worden uitgeschakeld en opnieuw worden ingeschakeld voor een volledig abonnement met behulp van de volgende opdrachten in de Azure CLI (u de knop 'Probeer het' gebruiken om een exemplaar van de Azure Cloud Shell te starten waarin u de opdrachten uitvoeren):

Als u de seriële console voor een abonnement wilt uitschakelen, gebruikt u de volgende opdrachten:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Als u seriële console voor een abonnement wilt inschakelen, gebruikt u de volgende opdrachten:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Als u de huidige ingeschakelde/uitgeschakelde status van seriële console voor een abonnement wilt krijgen, gebruikt u de volgende opdrachten:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

### <a name="powershell"></a>PowerShell

Seriële console kan ook worden ingeschakeld en uitgeschakeld met PowerShell.

Als u de seriële console voor een abonnement wilt uitschakelen, gebruikt u de volgende opdrachten:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Als u seriële console voor een abonnement wilt inschakelen, gebruikt u de volgende opdrachten:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [Azure Serial Console voor Linux VM's](./serial-console-linux.md)
* Meer informatie over de [Azure Serial Console voor Windows VM's](./serial-console-windows.md)
* Meer informatie over [energiebeheeropties in de Azure Serial Console](./serial-console-power-options.md)