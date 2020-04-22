---
title: Plaatsingsgroepen voor nabijheid gebruiken
description: Meer informatie over het maken en gebruiken van nabijheidsplaatsingsgroepen voor virtuele machines in Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: f89b28e7a3c29e45efa2796788e27325c01d7098
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759252"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>VM's implementeren voor nabijheidsplaatsingsgroepen met Azure CLI

Als u VM's zo dicht mogelijk wilt plaatsen en de laagst mogelijke latentie wilt bereiken, moet u deze implementeren binnen een [plaatsingsgroep voor de nabijheid.](co-location.md#proximity-placement-groups)

Een plaatsingsgroep voor nabijheid is een logische groepering die wordt gebruikt om ervoor te zorgen dat Azure-rekenbronnen fysiek dicht bij elkaar staan. Plaatsingsgroepen voor nabijheidzijn handig voor workloads waarbij lage latentie een vereiste is.


## <a name="create-the-proximity-placement-group"></a>De plaatsingsgroep voor nabijheid maken
Maak een plaatsingsgroep [`az ppg create`](/cli/azure/ppg#az-ppg-create)voor nabijheid met behulp van . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Plaatsingsgroepen voor nabijheid weergeven

U al uw nabijheidsplaatsingsgroepen vermelden met behulp van [de AZ PPG-lijst.](/cli/azure/ppg#az-ppg-list)

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Een virtuele machine maken

Maak een VM in de plaatsingsgroep nabijheid met behulp van [de nieuwe az-vm](/cli/azure/vm#az-vm-create).

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

U de VM in de plaatsingsgroep nabijheid zien met behulp van [az ppg-show](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Beschikbaarheidssets
U ook een beschikbaarheidsset maken in uw nabijheidsplaatsingsgroep. Gebruik dezelfde `--ppg` parameter met [de beschikbaarheidsset van AZ VM om](/cli/azure/vm/availability-set#az-vm-availability-set-create) een beschikbaarheidsset te maken en alle VM's in de beschikbaarheidsset worden ook in dezelfde plaatsingsgroep voor nabijheid gemaakt.

## <a name="scale-sets"></a>Schaalsets

U ook een schaalset maken in uw plaatsingsgroep voor nabijheid. Gebruik dezelfde `--ppg` parameter met [az vmss maken](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) om een schaalset te maken en alle exemplaren worden gemaakt in dezelfde nabijheidsplaatsingsgroep.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Azure CLI-opdrachten](/cli/azure/ppg) voor plaatsingsgroepen voor nabijheidsplaatsing.
