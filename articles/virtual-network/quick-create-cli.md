---
title: 'Een virtueel netwerk maken: quickstart - Azure CLI'
titlesuffix: Azure Virtual Network
description: In deze quickstart leert u hoe u een virtueel netwerk maakt met Azure CLI. Met een virtueel netwerk kunnen Azure-resources met elkaar en met internet communiceren.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2019
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1feae201738a560c4cdb56f703c4af9a38af86d1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88056785"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Quickstart: Een virtueel netwerk maken met Azure CLI

Met een virtueel netwerk kunnen Azure-resources, zoals virtuele machines, privé met elkaar en met internet communiceren. In deze snelstart leert u hoe u een virtueel netwerk maakt. Nadat u een virtueel netwerk hebt gemaakt, implementeert u twee virtuele machines in het virtuele netwerk. Vervolgens maakt u verbinding met de virtuele machines via internet en is er privécommunicatie via het nieuwe virtuele netwerk mogelijk.
## <a name="prerequisites"></a>Vereisten
Als u nog geen abonnement op Azure hebt, maak dan nu een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Azure CLI lokaal te installeren en te gebruiken, moet u voor deze snelstart versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om na te gaan welke versie er is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) voor installatie- of upgrade-informatie.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Een resourcegroep en een virtueel netwerk maken

Voordat u een virtueel netwerk kunt maken, moet u een resourcegroep maken die het virtuele netwerk host. Maak een resourcegroep maken met [az group create](/cli/azure/group). In dit voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *US - oost*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Maak een virtueel netwerk met [az network vnet create](/cli/azure/network/vnet). In dit voorbeeld wordt een standaard virtueel netwerk gemaakt met de naam *myVirtualNetwork* met één subnet genaamd *default*:

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak twee VM’s in het virtuele netwerk.

### <a name="create-the-first-vm"></a>De eerste VM maken

Maak een VM met [az vm create](/cli/azure/vm). Als SSH-sleutels niet al bestaan op de standaardsleutellocatie, worden ze met deze opdracht gemaakt. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`. Met de optie `--no-wait` wordt de virtuele machine op de achtergrond gemaakt, zodat u kunt doorgaan met de volgende stap. In dit voorbeeld wordt een VM met de naam *myVM1* gemaakt:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>De tweede VM maken

Aangezien u de optie `--no-wait` in de vorige stap hebt gebruikt, kunt u verder gaan en de tweede virtuele machine met de naam *myVm2* maken.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Azure CLI-uitvoerbericht

Het maken van de VM's duurt enkele minuten. Nadat Azure de virtuele machines heeft gemaakt, wordt met Azure CLI als volgt uitvoer geretourneerd:

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
  "zones": ""
}
```

Let op het **openbare IP-adres**. U gebruikt dit adres om in de volgende stap verbinding te maken met de virtuele machine via internet.

## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Vervang in deze opdracht `<publicIpAddress>` door het openbare IP-adres van de VM *myVm2*:

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Communiceren tussen VM's

Voer de volgende opdracht in om privécommunicatie tussen de virtuele machines *myVm2* en *myVm1* te bevestigen:

```bash
ping myVm1 -c 4
```

U ontvangt vier reacties van *10.0.0.4*.

Sluit de SSH-sessie met de virtuele machine *myVm2* af.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt [az group delete](/cli/azure/group) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, als deze niet meer nodig zijn:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een standaard virtueel netwerk en twee virtuele machines gemaakt. U hebt met één virtuele machine verbinding gemaakt via internet en er is er privécommunicatie tussen de twee virtuele machines geweest.
Azure staat onbeperkte privécommunicatie tussen virtuele machines toe. Daarentegen zijn standaard alleen inkomende verbindingen met extern bureaublad met Windows-VM's via internet toegestaan. Ga verder naar het volgende artikel voor meer informatie over het configureren van verschillende typen VM-netwerkcommunicatie:
> [!div class="nextstepaction"]
> [Netwerkverkeer filteren](tutorial-filter-network-traffic.md)
