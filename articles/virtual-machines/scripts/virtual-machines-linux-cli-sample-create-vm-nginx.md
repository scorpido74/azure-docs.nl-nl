---
title: 'Azure CLI-voorbeeldscript: een virtuele Linux-machine met NGINX maken'
description: 'Azure CLI-voorbeeldscript: een virtuele Linux-machine met NGINX maken'
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 00bbc74a78be93dd7167625fa98aa7f9500c1be2
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502064"
---
# <a name="create-a-vm-with-nginx"></a>Een virtuele machine met NGINX maken

Met dit script maakt u een virtuele Azure-machine die de aangepaste scriptextensie voor virtuele Azure-machines gebruikt om NGINX te installeren. Nadat het script is uitgevoerd, is er een demo-website bereikbaar via het openbare IP-adres van de virtuele machine.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nginx/create-vm-nginx.sh "Quick Create VM")]

## <a name="custom-script-extension"></a>Aangepaste scriptextensie

De aangepaste scriptextensie kopieert dit script naar de virtuele machine. Het script wordt vervolgens uitgevoerd om een NGINX-webserver te installeren en configureren.

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep, VM, en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt voor het maken van een resourcegroep, een virtuele machine en alle gerelateerde resources. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az vm create](/cli/azure/vm) | Hiermee maakt u de virtuele machine. Met deze opdracht geeft u ook de installatiekopie van de virtuele machine op die moet worden gebruikt, samen met beheerdersreferenties.  |
| [az vm open-port](/cli/azure/network/nsg/rule) | Hiermee maakt u een regel voor de netwerkbeveiligingsgroep om binnenkomend verkeer toe te staan. In dit voorbeeld is poort 80 geopend voor HTTP-verkeer. |
| [azure vm extension set](/cli/azure/vm/extension) | Hiermee wordt de extensie van een virtuele machine toegevoegd aan een virtuele machine en wordt deze extensie vervolgens uitgevoerd. In dit voorbeeld wordt de aangepaste scriptextensie gebruikt om NGINX te installeren.|
| [az group delete](/cli/azure/vm/extension) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

U kunt extra CLI-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Linux-VM's](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
