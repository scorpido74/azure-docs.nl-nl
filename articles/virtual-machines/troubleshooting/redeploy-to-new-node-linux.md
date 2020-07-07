---
title: Linux Virtual Machines opnieuw implementeren in azure | Microsoft Docs
description: Virtuele Linux-machines opnieuw implementeren in azure om problemen met SSH-verbindingen te verhelpen.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 18e96f9463176b0fce04252492eea6dbede416c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79531104"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Een Linux-virtual machine opnieuw implementeren naar een nieuw Azure-knooppunt
Als u problemen ondervindt met het oplossen van problemen met SSH of toepassing op een virtuele Linux-machine (VM) in azure, is het mogelijk dat u de VM opnieuw implementeert. Wanneer u een virtuele machine opnieuw implementeert, wordt de virtuele machine verplaatst naar een nieuw knoop punt in de Azure-infra structuur en wordt deze vervolgens weer toegezonden. Alle configuratie opties en gekoppelde resources worden bewaard. In dit artikel wordt beschreven hoe u een VM opnieuw implementeert met behulp van Azure CLI of de Azure Portal.

> [!NOTE]
> Nadat u een virtuele machine opnieuw hebt geïmplementeerd, gaat de tijdelijke schijf verloren en worden dynamische IP-adressen die zijn gekoppeld aan de virtuele netwerk interface, bijgewerkt. 


## <a name="use-the-azure-cli"></a>Azure CLI gebruiken
Installeer de nieuwste [Azure cli](/cli/azure/install-az-cli2) en meld u aan bij uw Azure-account met behulp van [AZ login](/cli/azure/reference-index).

Implementeer uw VM opnieuw met [AZ VM redeploy](/cli/azure/vm). In het volgende voor beeld wordt de virtuele machine met de naam *myVM* in de resource groep met de naam *myResourceGroup*opnieuw geïmplementeerd:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>De klassieke Azure-CLI gebruiken

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Installeer de [nieuwste Azure Classic cli](../../cli-install-nodejs.md) en meld u aan bij uw Azure-account. Zorg ervoor dat u zich in de modus Resource Manager () bevindt `azure config mode arm` .

In het volgende voor beeld wordt de virtuele machine met de naam *myVM* in de resource groep met de naam *myResourceGroup*opnieuw geïmplementeerd:

```console
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Volgende stappen
Als u problemen ondervindt met het verbinding maken met uw virtuele machine, vindt u specifieke hulp bij het [oplossen van problemen met ssh-verbindingen](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) of [gedetailleerde stappen](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor het oplossen van SSH. Als u geen toegang krijgt tot een toepassing die wordt uitgevoerd op uw virtuele machine, kunt u ook [problemen met het oplossen van toepassingen](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)lezen.


