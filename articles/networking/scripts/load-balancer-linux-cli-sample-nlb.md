---
title: Voor beeld van Azure CLI-script-taak verdeling verkeer naar Vm's voor hoge Beschik baarheid | Microsoft Docs
description: Voor beeld van Azure CLI-script-taak verdeling verkeer naar Vm's voor hoge Beschik baarheid
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: KumudD
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: allensu
ms.openlocfilehash: bf5f4bb6bd15fa1fd440e679913a370bbe30b663
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890318"
---
# <a name="load-balance-traffic-to-vms-for-high-availability"></a>Verkeer verdelen naar virtuele machines voor hoge beschikbaarheid

Met dit voorbeeldscript maakt u alles wat nodig is voor het uitvoeren van verschillende virtuele Ubuntu-machines die zijn geconfigureerd in een maximaal beschikbare configuratie met gelijke taakverdeling. Nadat het script is uitgevoerd, beschikt u over drie virtuele machines, die gekoppeld zijn aan een Azure-beschikbaarheidsset en toegankelijk zijn via een Azure Load Balancer. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Gebruik de volgende opdracht om de resourcegroep, VM, en alle gerelateerde resources te verwijderen.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten voor het maken van een resourcegroep, een virtuele machine, een beschikbaarheidsset, een load balancer en alle gerelateerde resources. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Hiermee maakt u een virtueel Azure-netwerk en -subnet. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) | Hiermee maakt u een openbaar IP-adres met een statisch IP-adres en een bijbehorende DNS-naam. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb) | Hiermee maakt u een Azure-load balancer. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe) | Hiermee maakt u een load balancer-test. Een load balancer-test wordt gebruikt voor het bewaken van elke virtuele machine in de load balancer-set. Als een virtuele machine niet meer toegankelijk is, wordt verkeer niet meer naar die virtuele machine doorgestuurd. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule) | Hiermee maakt u een load balancer-regel. In dit voorbeeld wordt een regel gemaakt voor poort 80. Wanneer HTTP-verkeer bij de load balancer aankomt, wordt dat doorgestuurd naar poort 80 van een van de virtuele machines in de LB-set. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule) | Hiermee maakt u een load balancer-regel voor Network Address Translation (NAT).  NAT-regels wijzen een poort van de load balancer toe aan een poort op een virtuele machine. In dit voorbeeld wordt een NAT-regel gemaakt voor SSH-verkeer naar elke virtuele machine in de load balancer-set.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg) | Hiermee maakt u een netwerkbeveiligingsgroep (NSG), die fungeert als een beveiligingsgrens tussen het internet en de virtuele machine. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Hiermee maakt u een NSG-regel om binnenkomend verkeer toe te staan. In dit voorbeeld wordt poort 22 geopend voor SSH-verkeer. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic) | Hiermee maakt u een netwerkkaart die gekoppeld wordt aan het virtuele netwerk, het subnet en de NSG. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule) | Hiermee maakt u een beschikbaarheidsset. Beschikbaarheidssets waarborgen de uptime van toepassingen door de virtuele machines te verdelen over fysieke resources, zodat als er een fout optreedt, niet de hele set wordt getroffen. |
| [az vm create](/cli/azure/vm) | Hiermee maakt u de virtuele machine en verbindt u deze met de netwerkkaart, het virtuele netwerk, het subnet en de netwerkbeveiligingsgroep. Met deze opdracht geeft u ook de installatiekopie van de virtuele machine op die moet worden gebruikt, samen met beheerdersreferenties.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Hiermee verwijdert u een resourcegroep met inbegrip van alle ingesloten resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-voorbeeldscripts voor Azure-netwerken vindt u in de [Documentatie over Azure-netwerken](../cli-samples.md).
