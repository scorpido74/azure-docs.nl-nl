---
title: Een VNet maken voor toepassingen met meerdere lagen - Azure CLI-scriptvoorbeeld
description: Azure CLI-voorbeeldscript - Het maken van een netwerk voor toepassingen met meerdere lagen.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.openlocfilehash: 39a993324a476f0e1f1d17858ae126742e4f69f9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74083850"
---
# <a name="create-a-network-for-multi-tier-applications-script-sample"></a>Voorbeeldscript voor het maken van een netwerk voor toepassingen met meerdere lagen

Dit voorbeeldscript maakt een virtueel netwerk met front-end en back-end-subnetten. Verkeer naar het front-end-subnet wordt beperkt tot HTTP en SSH, terwijl het verkeer naar het back-end-subnet wordt beperkt tot MySQL, poort 3306. Nadat het script is uitgevoerd, hebt u twee virtuele machines, één in elk subnet, waarop u webserver- en MySQL-software kunt implementeren.

U kunt het script uitvoeren vanuit de Azure [Cloud Shell](https://shell.azure.com/bash), of vanuit een lokale installatie van Azure CLI. Als u de CLI lokaal gebruikt, hebt u versie 2.0.28 of hoger nodig om dit script uit te voeren. Voer `az --version` uit om te zien welke versie is geïnstalleerd. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). Als u de CLI lokaal uitvoert, moet u ook `az login` uitvoeren om verbinding te maken met Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Voorbeeldscript


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.sh  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Voer de volgende opdracht uit om de resourcegroep, VM en alle gerelateerde resources te verwijderen:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt voor het maken van een resourcegroep, een virtueel netwerk en netwerkbeveiligingsgroepen. Elke opdracht in onderstaande tabel is een link naar opdracht-specifieke documentatie:

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az network vnet create](/cli/azure/network/vnet) | Hiermee maakt u een virtueel Azure-netwerk en front-end-subnet. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Hiermee maakt u een back-end-subnet. |
| [az network public-ip create](/cli/azure/network/public-ip) | Hiermee maakt u een openbaar IP-adres voor toegang tot de virtuele machine via het internet. |
| [az network nic create](/cli/azure/network/nic) | Hiermee maakt u virtuele netwerkinterfaces en koppelt deze aan de front-end en back-end-subnetten van het virtuele netwerk. |
| [az network nsg create](/cli/azure/network/nsg) | Hiermee maakt u netwerkbeveiligingsgroepen (NSG's) die zijn gekoppeld aan de front-end- en back-end-subnetten. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |Hiermee maakt u NSG-regels die bepaalde poorten tot specifieke subnetten blokkeren of toestaan. |
| [az vm create](/cli/azure/vm) | Hiermee maakt u virtuele machines en koppelt u een NIC aan elke virtuele machine. Met deze opdracht geeft u ook de installatiekopie van de virtuele machine op die moet worden gebruikt, evenals de beheerdersreferenties. |
| [az group delete](/cli/azure/group) | Hiermee verwijdert u een resourcegroep en de bijhorende resources. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Meer CLI-voorbeeldscripts voor virtuele netwerken kunt u vinden in [CLI-voorbeelden voor virtuele netwerken](../cli-samples.md).
