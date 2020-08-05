---
title: Voorbeeld van Azure CLI-script - IIS installeren
description: Voorbeeld van Azure CLI-script - IIS installeren
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/28/2017
ms.author: cynthn
ms.openlocfilehash: 140804bf8436357ccd5c645279a3821a96b247e6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082491"
---
# <a name="create-a-virtual-machine-with-the-azure-cli-with-a-custom-script-to-install-iis"></a>Een virtuele machine maken met de Azure CLI met een aangepast script om IIS te installeren

Met dit script maakt u een virtuele machine in Azure waarop Windows Server 2016 wordt uitgevoerd, waarna de aangepaste scriptextensie voor de virtuele Azure-machine wordt gebruikt om IIS te installeren. Nadat het script is uitgevoerd, is de standaardwebsite van IIS bereikbaar via het openbare IP-adres van de virtuele machine.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-windows-iis/create-vm-windows-iis.sh "Quick Create VM")]

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
| [az vm create](/cli/azure/vm) | Hiermee maakt u de virtuele machine en verbindt u deze met de netwerkkaart, het virtuele netwerk, het subnet en de netwerkbeveiligingsgroep. Met deze opdracht geeft u ook de installatiekopie van de virtuele machine op die moet worden gebruikt, samen met beheerdersreferenties.  |
| [az vm open-port](/cli/azure/network/nsg/rule) | Hiermee maakt u een regel voor de netwerkbeveiligingsgroep om binnenkomend verkeer toe te staan. In dit voorbeeld is poort 80 geopend voor HTTP-verkeer. |
| [azure vm extension set](/cli/azure/vm/extension) | Hiermee wordt de extensie van een virtuele machine toegevoegd aan een virtuele machine en wordt deze extensie vervolgens uitgevoerd. In dit voorbeeld wordt de aangepaste scriptextensie gebruikt om IIS te installeren.|
| [az group delete](/cli/azure/vm/extension) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-scriptvoorbeelden voor virtuele machines vindt u in de [Azure-documentatie voor Windows-VM's](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
