---
title: Voor beelden van Azure CLI-apps installeren
description: Met dit script maakt u een virtuele-machineschaalset die in Ubuntu wordt uitgevoerd en die de Custom Script-extensie gebruikt voor het installeren van een eenvoudige webtoepassing.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 06fd454afbe052473542725d8a05eebebd6c2e73
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351006"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-the-azure-cli"></a>Apps installeren in een virtuele-machineschaalset met de Azure CLI
Met dit script maakt u een virtuele-machineschaalset die in Ubuntu wordt uitgevoerd en die de Custom Script-extensie gebruikt voor het installeren van een eenvoudige webtoepassing. Nadat het script is uitgevoerd, kunt u de web-app via een webbrowser openen.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/install-apps/install-apps.sh "Install apps into a scale set")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Gebruik de volgende opdracht om de resourcegroep, de schaalset en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script
In dit script worden de volgende opdrachten gebruikt voor het maken van een resourcegroep, een virtuele-machineschaalset en alle gerelateerde resources. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/ad/group) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az vmss create](/cli/azure/vmss) | Hiermee maakt u de virtuele machine en verbindt u deze met het virtuele netwerk, het subnet en de netwerkbeveiligingsgroep. Om het verkeer te distribueren naar de verschillende VM-exemplaren, wordt er ook een load balancer gemaakt. Met deze opdracht geeft u ook de VM-installatiekopie op die moet worden gebruikt, samen met beheerdersreferenties.  |
| [az vmss extension set](/cli/azure/vmss/extension) | Hiermee installeert u de aangepaste scriptextensie van Azure om een script uit te voeren dat de gegevensschijven op elk VM-exemplaar voorbereidt. |
| [az network lb rule create](/cli/azure/network/lb/rule) | Hiermee maakt u een load balancer-regel voor het distribueren van verkeer op TCP-poort 80 voor VM-exemplaren in de schaalset. |
| [az network public-ip show](/cli/azure/network/public-ip) | Hiermee haalt u informatie op over het toegewezen openbare IP-adres dat door de load balancer wordt gebruikt. |
| [az group delete](/cli/azure/ad/group) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure/overview) voor meer informatie over de Azure CLI.

U kunt extra Azure CLI-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor virtuele-machineschaalsets](../cli-samples.md).
