---
title: Voorbeeld van Azure CLI-script - Twee VM's maken met een interne en externe NSG
description: Voorbeeld van Azure CLI-script - Twee virtuele machines maken met interne en externe NSG
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: gwallace
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.custom: mvc
ms.openlocfilehash: eb851b672a3cc9748d1aa5fbe27e4a7fac9d020e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74039926"
---
# <a name="secure-network-traffic-between-virtual-machines"></a>Netwerkverkeer tussen virtuele machines beveiligen

Met dit script worden twee virtuele machines gemaakt en wordt het binnenkomende verkeer naar beide machines beveiligd. Eén virtuele machine is toegankelijk is via internet en heeft een netwerkbeveiligingsgroep (NSG)die is geconfigureerd voor verkeer op poort 3389 en 80. De tweede virtuele machine is niet toegankelijk via internet en heeft een NSG die zo is geconfigureerd dat alleen verkeer van de eerste virtuele machine wordt toegelaten. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-windows-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Gebruik de volgende opdracht om de resourcegroep, VM, en alle gerelateerde resources te verwijderen.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt voor het maken van een resourcegroep, een virtuele machine en alle gerelateerde resources. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Hiermee maakt u een virtueel Azure-netwerk en -subnet. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet) | Hiermee maakt u een subnet. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Hiermee maakt u de virtuele machine en verbindt u deze met de netwerkkaart, het virtuele netwerk, het subnet en de netwerkbeveiligingsgroep. Met deze opdracht geeft u ook de installatiekopie van de virtuele machine op die moet worden gebruikt, samen met beheerdersreferenties.  |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Hiermee wordt een NSG-regel bijgewerkt. In dit voorbeeld wordt de regel voor de back-end zo bijgewerkt dat alleen verkeer van het front-end-subnet wordt doorgelaten. |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Hiermee wordt informatie over een regel voor een netwerkbeveiligingsgroep geretourneerd. In dit voorbeeld wordt de naam van de regel opgeslagen in een variabele voor gebruik verderop in het script. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

U kunt extra CLI-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Windows-VM's](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
