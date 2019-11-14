---
title: Voor beeld van Azure CLI-script-twee virtuele machines maken met een interne en externe NSG
description: Voorbeeld van Azure CLI-script - Twee virtuele machines maken met interne en externe NSG
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 857a2862a7ddf515567f96b2e688e54a957cd3d9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039586"
---
# <a name="secure-network-traffic-between-virtual-machines"></a>Netwerkverkeer tussen virtuele machines beveiligen

Met dit script worden twee virtuele machines gemaakt en wordt het binnenkomende verkeer naar beide machines beveiligd. Eén virtuele machine is toegankelijk via internet en heeft een netwerkbeveiligingsgroep (NSG) die is geconfigureerd voor verkeer op poort 22 en 80. De tweede virtuele machine is niet toegankelijk via internet en heeft een NSG die zo is geconfigureerd dat alleen verkeer van de eerste virtuele machine wordt toegelaten.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep, VM, en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten voor het maken van een resourcegroep, een virtuele machine en alle gerelateerde resources. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Hiermee maakt u een virtueel Azure-netwerk en -subnet. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet) | Hiermee maakt u een subnet. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Hiermee maakt u de virtuele machine en verbindt u deze met de netwerkkaart, het virtuele netwerk, het subnet en de netwerkbeveiligingsgroep. Met deze opdracht geeft u ook de installatiekopie van de virtuele machine op die moet worden gebruikt, samen met beheerdersreferenties.  |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Hiermee wordt informatie over een regel voor een netwerkbeveiligingsgroep geretourneerd. In dit voorbeeld wordt de naam van de regel opgeslagen in een variabele voor gebruik verderop in het script. |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Hiermee wordt een NSG-regel bijgewerkt. In dit voorbeeld wordt de regel voor de back-end zo bijgewerkt dat alleen verkeer van het front-end-subnet wordt doorgelaten. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

U kunt extra CLI-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Linux-VM's](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
