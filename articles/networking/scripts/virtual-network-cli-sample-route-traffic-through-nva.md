---
title: Azure CLI-voorbeeldscript - verkeer routeren via een virtueel netwerkapparaat | Microsoft Docs
description: Azure CLI-voorbeeldscript - verkeer routeren via een virtueel netwerkapparaat met firewall.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 55b9c9cba4a34fc9e209e03565861885ea192cc1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890224"
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Verkeer routeren via een virtueel netwerk apparaat

Dit voorbeeldscript maakt een virtueel netwerk met front-end en back-end-subnetten. Er wordt ook een virtuele machine gemaakt waarvoor doorsturen via IP is ingeschakeld, voor het routeren van netwerkverkeer tussen de twee subnetten. Na het uitvoeren van het script kunt u netwerksoftware, bijvoorbeeld een firewalltoepassing, op de VM implementeren.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Voorbeeldscript


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Gebruik de volgende opdracht om de resourcegroep, VM, en alle gerelateerde resources te verwijderen.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt voor het maken van een resourcegroep, een virtueel netwerk en netwerkbeveiligingsgroepen. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az network vnet create](/cli/azure/network/vnet) | Hiermee maakt u een virtueel Azure-netwerk en front-end-subnet. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Hiermee maakt u back-end- en DMZ-subnetten. |
| [az network public-ip create](/cli/azure/network/public-ip) | Hiermee maakt u een openbaar IP-adres voor toegang tot de virtuele machine via internet. |
| [az network nic create](/cli/azure/network/nic) | Hiermee maakt u een virtuele netwerkinterface en schakelt u doorsturen via IP in voor deze interface. |
| [az network nsg create](/cli/azure/network/nsg) | Hiermee maakt u een netwerkbeveiligingsgroep (NSG). |
| [az network nsg rule create](/cli/azure/network/nsg/rule) | Hiermee maakt u NSG-regels waarmee inkomend verkeer via HTTP- en HTTPS-poorten naar de VM worden toegestaan. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet)| Hiermee koppelt u de NSG's en routetabellen aan subnetten. |
| [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create)| Hiermee maakt u een routetabel voor alle routes. |
| [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create)| Maakt routes voor het routeren van verkeer tussen subnetten en Internet via de VM. |
| [az vm create](/cli/azure/vm) | Hiermee maakt u een virtuele machine en koppelt u de NIC hieraan. Met deze opdracht geeft u ook de installatiekopie van de virtuele machine op die moet worden gebruikt, evenals de beheerdersreferenties. |
| [az group delete](/cli/azure/group) | Hiermee verwijdert u een resourcegroep en de bijhorende resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Extra Network CLI-voorbeeld scripts vindt u in de [documentatie](../cli-samples.md) van het overzicht van Azure-netwerken
