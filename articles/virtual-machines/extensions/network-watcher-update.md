---
title: De Network Watcher-extensie bijwerken naar de nieuwste versie
description: Meer informatie over het bijwerken van Network Watcher-uitbrei ding naar de nieuwste versie
services: virtual-machines-windows
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: c386685d63894472623ffc4392a529541a91391c
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410315"
---
# <a name="how-to-update-the-network-watcher-extension-to-the-latest-the-version"></a>De Network Watcher-extensie bijwerken naar de nieuwste versie 

## <a name="overview"></a>Overzicht

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) is een bewakings-, diagnose-en analyse service voor netwerk prestaties waarmee Azure-netwerken kunnen worden bewaakt. De extensie van de virtuele machine van Network Watcher agent is een vereiste voor het vastleggen van netwerk verkeer op aanvraag en andere geavanceerde functionaliteit op virtuele machines van Azure. De uitbrei ding Network Watcher wordt gebruikt door functies als verbindings monitor, verbindings monitor (preview), verbindings problemen oplossen en pakket opname.   

## <a name="prerequisites"></a>Vereisten
In dit document wordt ervan uitgegaan dat u de extensie Network Watcher hebt geïnstalleerd in uw virtuele machine en instructies wilt geven voor het bijwerken van deze naar de nieuwste versie. 

## <a name="latest-version"></a>Nieuwste versie
De meest recente versie van de uitbrei ding van de Network Watcher is momenteel `1.4.1654.1` .

## <a name="updating-your-extension"></a>Uw extensie bijwerken 

### <a name="check-your-extension-version"></a>Controleer de versie van de extensie  

**De Azure Portal gebruiken**

1. Ga naar de Blade extensies van de virtuele machine in de Azure Portal.   
2. Klik op de extensie ' AzureNetworkWatcher ' om het deel venster met details weer te geven.  
3. Zoek het versie nummer in het veld ' versie '.  

**Azure CLI gebruiken** Voer de onderstaande opdracht uit vanaf een Azure CLI-prompt.   

```azurecli
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

Zoek de uitbrei ding AzureNetworkWatcher in de uitvoer en Identificeer het versie nummer in het veld ' TypeHandlerVersion ' in de uitvoer.  


**Power shell gebruiken** Voer de volgende opdrachten uit vanuit een Power shell-prompt:   

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

Zoek de uitbrei ding AzureNetworkWatcher in de uitvoer en Identificeer het versie nummer in het veld ' TypeHandlerVersion ' in de uitvoer.   


### <a name="update-your-extension"></a>Uw extensie bijwerken

Als uw versie lager is dan `1.4.1654.1` (de huidige meest recente versie), werkt u de extensie bij met een van de volgende opties. 

**Optie 1: Power shell gebruiken**

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
```


**Optie 2: Azure CLI gebruiken**  

Upgrade afdwingen 

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Als dat niet werkt. Verwijder de uitbrei ding en installeer deze opnieuw met behulp van de onderstaande stappen. Hiermee wordt automatisch de meest recente versie toegevoegd. 

De uitbrei ding verwijderen 

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

De extensie opnieuw installeren

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"  

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" 

```

**Optie 3: uw Vm's opnieuw opstarten**

Als u de automatische upgrade hebt ingesteld op True voor de NetworkWatcher-extensie. Als u de VM opnieuw opstart, installeert u de nieuwste extensie.


## <a name="support"></a>Ondersteuning

Als u op elk gewenst moment meer hulp nodig hebt, kunt u de documentatie voor de Network Watcher-extensie ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux), [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)) raadplegen of contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.
