---
title: 'Azure CLI-voorbeeldscript: de LAMP Stack implementeren in een virtuele-machineschaalset met gelijke taakverdeling'
description: Gebruik een aangepaste scriptextensie om de LAMP Stack te implementeren in een VM-schaalset met gelijke taakverdeling op Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
ms.author: cynthn
tags: azure-service-management
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.custom: mvc, devx-track-azurecli
ms.date: 04/05/2017
ms.openlocfilehash: e71b2f940c637a8b1375be71f9b6ca95fd76628e
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501862"
---
# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>De LAMP Stack implementeren in een VM-schaalset met gelijke taakverdeling

In dit voorbeeld maakt u een VM-schaalset en past u een extensie toe waarmee een aangepast script wordt uitgevoerd om de LAMP Stack te implementeren op elke virtuele machine in de schaalset.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Create virtual machine scale set with LAMP stack")]

## <a name="connect"></a>Verbinding maken

Gebruik deze code om te zien hoe u verbinding maakt met uw virtuele machines en schaalset.

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Access the virtual machine scale set")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Gebruik de volgende opdracht om de resourcegroep, de schaalset en virtuele machines, en alle gerelateerde resources te verwijderen.

```azurecli-interactive 
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten voor het maken van een resourcegroep, een virtuele machine, een beschikbaarheidsset, een load balancer en alle gerelateerde resources. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az vmss create](/cli/azure/vmss) | Hiermee maakt u een VM-schaalset |
| [az network lb rule create](/cli/azure/network/lb/rule) | Een eindpunt met gelijke taakverdeling toevoegen |
| [az vmss extension set](/cli/azure/vmss/extension) | Hiermee maakt u de extensie voor het uitvoeren van het aangepaste script bij de implementatie van een virtuele machine. |
| [az vmss update-instances](/cli/azure/vmss) | Hiermee voert u het aangepaste script uit op de VM-instanties die werden geïmplementeerd voordat de extensie op de schaalset werd toegepast. |
| [az vmss scale](/cli/azure/vmss) | Vergroot de schaalset door meer VM-instanties toe te voegen. Het aangepaste script wordt op deze instanties uitgevoerd wanneer ze worden geïmplementeerd. |
| [az network public-ip list](/cli/azure/network/public-ip) | Haal de IP-adressen van de virtuele machines op die in het voorbeeld zijn gemaakt. |
| [az network lb show](/cli/azure/network/lb) | Hiermee haalt u de frontend- en backendpoorten op die door de load balancer worden gebruikt. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

U kunt extra CLI-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Linux-VM's](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
