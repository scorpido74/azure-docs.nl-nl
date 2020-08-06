---
title: Voorbeeld van Azure CLI-script - Twee virtuele machines maken met een interne en externe NSG
description: Voorbeeld van Azure CLI-script - Twee virtuele machines maken met interne en externe NSG
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 0d0e3dd53c592c991e342240b9c973e1677969d6
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498375"
---
# <a name="secure-network-traffic-between-virtual-machines"></a>Netwerkverkeer tussen virtuele machines beveiligen

Met dit script worden twee virtuele machines gemaakt en wordt binnenkomend verkeer op beide beveiligd. Eén virtuele machine is toegankelijk is via internet en heeft een netwerkbeveiligingsgroep (NSG)die is geconfigureerd voor verkeer op poort 3389 en 80. De tweede virtuele machine is niet toegankelijk via internet en heeft een NSG die zo is geconfigureerd dat alleen verkeer van de eerste virtuele machine wordt toegelaten. 

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

In dit script worden de volgende opdrachten gebruikt voor het maken van een resourcegroep, een virtuele machine en alle gerelateerde resources. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az network vnet create](/cli/azure/network/vnet) | Hiermee maakt u een virtueel Azure-netwerk en -subnet. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet) | Hiermee maakt u een subnet. |
| [az vm create](/cli/azure/vm) | Hiermee maakt u de virtuele machine en verbindt u deze met de netwerkkaart, het virtuele netwerk, het subnet en de netwerkbeveiligingsgroep. Met deze opdracht geeft u ook de installatiekopie van de virtuele machine op die moet worden gebruikt, samen met beheerdersreferenties.  |
| [az network nsg rule update](/cli/azure/network/nsg/rule) | Hiermee wordt een NSG-regel bijgewerkt. In dit voorbeeld wordt de regel voor de back-end zo bijgewerkt dat alleen verkeer van het front-end-subnet wordt doorgelaten. |
| [az network nsg rule list](/cli/azure/network/nsg/rule) | Hiermee wordt informatie over een regel voor een netwerkbeveiligingsgroep geretourneerd. In dit voorbeeld wordt de naam van de regel opgeslagen in een variabele voor gebruik verderop in het script. |
| [az group delete](/cli/azure/vm/extension) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-scriptvoorbeelden voor virtuele machines vindt u in de [Azure-documentatie voor Windows-VM's](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
