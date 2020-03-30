---
title: Linux-virtuele machines opnieuw implementeren in Azure | Microsoft Documenten
description: Linux-virtuele machines opnieuw implementeren in Azure om problemen met ssh-verbindingen te beperken.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531104"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Een Linux-virtual machine opnieuw implementeren naar een nieuw Azure-knooppunt
Als u problemen ondervindt bij het oplossen van problemen met SSH of toepassingstoegang tot een Virtuele Linux-machine (VM) in Azure, kan het opnieuw implementeren van de VM helpen. Wanneer u een VM opnieuw implementeert, wordt de VM verplaatst naar een nieuw knooppunt binnen de Azure-infrastructuur en wordt deze weer ingeschakeld. Al uw configuratieopties en bijbehorende resources blijven behouden. In dit artikel ziet u hoe u een VM opnieuw implementeert met Azure CLI of de Azure-portal.

> [!NOTE]
> Nadat u een vm opnieuw hebt geïmplementeerd, gaat de tijdelijke schijf verloren en worden dynamische IP-adressen die zijn gekoppeld aan de virtuele netwerkinterface bijgewerkt. 


## <a name="use-the-azure-cli"></a>Azure CLI gebruiken
Installeer de nieuwste [Azure CLI](/cli/azure/install-az-cli2) en meld u aan bij uw Azure-account met behulp van [az login](/cli/azure/reference-index).

Implementeer uw VM opnieuw met [de herimplementatie van az vm](/cli/azure/vm). In het volgende voorbeeld wordt de VM met de naam *myVM* opnieuw geïmplementeerd in de resourcegroep met de naam *myResourceGroup:*

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>De Azure-klassieker CLI gebruiken

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Installeer de [nieuwste Azure-klassieker CLI](../../cli-install-nodejs.md) en meld u aan bij uw Azure-account. Zorg ervoor dat u zich`azure config mode arm`in de resourcemanagermodus bevindt ( ).

In het volgende voorbeeld wordt de VM met de naam *myVM* opnieuw geïmplementeerd in de resourcegroep met de naam *myResourceGroup:*

```console
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Volgende stappen
Als u problemen ondervindt bij het maken van verbinding met uw vm, u specifieke hulp vinden bij het oplossen van problemen met [SSH-verbindingen](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) of [gedetailleerde stappen voor het oplossen van problemen met SSH.](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Als u geen toegang hebt tot een toepassing die op uw vm wordt uitgevoerd, u ook [problemen met het oplossen van toepassingen](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)lezen.


