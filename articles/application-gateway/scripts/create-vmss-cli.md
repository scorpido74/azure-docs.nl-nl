---
title: Azure CLI-voorbeeldscript - Webverkeer beheren | Microsoft Docs
description: Azure CLI-voorbeeldscript - Webverkeer beheren met een toepassingsgateway en een schaalset voor virtuele machines.
services: application-gateway
documentationcenter: networking
author: vhorne
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: f1815d3dbf70e5599aa53633b43029b16cf94037
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76273856"
---
# <a name="manage-web-traffic-using-the-azure-cli"></a>Webverkeer beheren met de Azure CLI

Met dit script maakt u een toepassingsgateway die gebruikmaakt van een virtuele-machineschaalset die is ingesteld voor back-endservers. De toepassingsgateway kan vervolgens worden geconfigureerd om webverkeer te beheren. Nadat het script is uitgevoerd, kunt u de toepassingsgateway testen met behulp van het openbare IP-adres.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Gebruik de volgende opdracht om de resourcegroep, de toepassingsgateway en alle gerelateerde resources te verwijderen.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om de implementatie te maken. Elk item in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Hiermee maakt u een virtueel netwerk. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Hiermee maakt u een subnet in een virtueel netwerk. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest) | Hiermee maakt u het openbare IP-adres voor de toepassingsgateway. |
| [az network application-gateway create](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest) | Hiermee maakt u een toepassingsgateway. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss) | Hiermee maakt u een schaalset voor virtuele machines. |
| [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip) | Hiermee haalt u het openbare IP-adres van de toepassingsgateway op. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure/overview) voor meer informatie over de Azure CLI.

Aanvullende CLI-scriptvoorbeelden voor de toepassingsgateway vindt u in de [Documentatie voor Azure Windows-VM's](../cli-samples.md).
