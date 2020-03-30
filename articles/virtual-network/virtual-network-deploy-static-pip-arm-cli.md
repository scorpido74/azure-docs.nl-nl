---
title: Een VM maken met een statisch openbaar IP-adres - Azure CLI | Microsoft Documenten
description: Meer informatie over het maken van een VM met een statisch openbaar IP-adres met de Azure command-line interface (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 4b8f91d7d9fc414ed0ae6387c25f71b1601e0b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76043404"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Een virtuele machine maken met een statisch openbaar IP-adres met de Azure CLI

U een virtuele machine maken met een statisch openbaar IP-adres. Met een openbaar IP-adres u vanaf het internet communiceren met een virtuele machine. Wijs een statisch openbaar IP-adres toe, in plaats van een dynamisch adres, om ervoor te zorgen dat het adres nooit verandert. Meer informatie over [statische openbare IP-adressen](virtual-network-ip-addresses-overview-arm.md#allocation-method). Zie [IP-adressen toevoegen, wijzigen of verwijderen](virtual-network-network-interface-addresses.md)als u een openbaar IP-adres wilt wijzigen dat is toegewezen aan een bestaande virtuele machine van dynamisch naar statisch, of om met privé-IP-adressen te werken. Openbare IP-adressen hebben een [nominale vergoeding](https://azure.microsoft.com/pricing/details/ip-addresses)en er is een [limiet](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) aan het aantal openbare IP-adressen dat u per abonnement gebruiken.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

U de volgende stappen uitvoeren vanaf uw lokale computer of met de Azure Cloud Shell. Als u uw lokale computer wilt gebruiken, moet u ervoor zorgen dat de [Azure CLI is geïnstalleerd.](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) Als u de Azure Cloud Shell wilt gebruiken, selecteert **u Probeer het** in de rechterbovenhoek van een opdrachtvak dat volgt. De Cloud Shell ondertekent u in Azure.

1. Als u de Cloud Shell gebruikt, gaat u naar stap 2. Open een opdrachtsessie en `az login`meld u aan bij Azure met .
2. Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voorbeeld wordt een resourcegroep in de Azure-regio Oost-VS gezoend:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Maak een virtuele machine met de opdracht [az vm create](/cli/azure/vm#az-vm-create). De `--public-ip-address-allocation=static` optie wijst een statisch openbaar IP-adres toe aan de virtuele machine. In het volgende voorbeeld wordt een Virtuele Ubuntu-machine gemaakt met een statisch, eenvoudig SKU-openbaar IP-adres met de naam *myPublicIpAddress:*

   ```azurecli-interactive
   az vm create \
     --resource-group myResourceGroup \
     --name myVM \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --public-ip-address myPublicIpAddress \
     --public-ip-address-allocation static
   ```

   Als het openbare IP-adres een standaard `--public-ip-sku Standard` SKU moet zijn, voegt u deze toe aan de vorige opdracht. Meer informatie over [SKU's voor openbaar IP-adres](virtual-network-ip-addresses-overview-arm.md#sku). Als de virtuele machine wordt toegevoegd aan de back-endpool van een openbare Azure Load Balancer, moet de SKU van het openbare IP-adres van de virtuele machine overeenkomen met de SKU van het openbare IP-adres van de load balancer. Zie Azure [Load Balancer](../load-balancer/concepts-limitations.md#skus)voor meer informatie.

4. Bekijk het toegewezen openbare IP-adres en bevestig dat het is gemaakt als een statisch, basis SKU-adres, met [openbare IP-show van het AZ-netwerk:](/cli/azure/network/public-ip#az-network-public-ip-show)

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Azure heeft een openbaar IP-adres toegewezen aan adressen die worden gebruikt in de regio waarin u de virtuele machine hebt gemaakt. U kunt de lijst met bereiken (voorvoegsels) downloaden voor de Azure-clouds [Openbaar](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) en [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Wijzig de IP-adresinstellingen in het besturingssysteem van de virtuele machine niet. Het besturingssysteem is niet op de hoogte van openbare IP-adressen van Azure. Hoewel u privé-IP-adresinstellingen aan het besturingssysteem toevoegen, raden we u aan dit niet te doen tenzij dat nodig is, en pas na het lezen [een privé-IP-adres toevoegen aan een besturingssysteem.](virtual-network-network-interface-addresses.md#private)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, als deze niet meer nodig zijn:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [openbare IP-adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure
- Meer informatie over alle [instellingen voor openbare IP-adres](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Meer informatie over [privé-IP-adressen](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) en het toewijzen van een [statisch privé-IP-adres](virtual-network-network-interface-addresses.md#add-ip-addresses) aan een virtuele Azure-machine
- Meer informatie over [het](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) maken van virtuele Linux- en [Windows-machines](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
