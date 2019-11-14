---
title: Voor beeld van Azure CLI-script-Vm's opnieuw opstarten
description: Azure CLI-voorbeeldscript - Virtuele machines opnieuw opstarten op label en op id
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 5545fc07bd7b30225bcaf01eee8f10a141cd8d57
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040256"
---
# <a name="restart-vms"></a>Virtuele machines opnieuw opstarten

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Dit voorbeeld laat een aantal manieren zien waarop u enkele virtuele machines kunt maken en deze opnieuw kunt opstarten.

Met de eerste opdracht worden alle virtuele machines in de resourcegroep opnieuw opgestart.

```bash
az vm restart --ids $(az vm list --resource-group myResourceGroup --query "[].id" -o tsv)
```

Met de tweede opdracht worden de gelabelde virtuele machines opgevraagd met behulp van `az resource list` en wordt er gefilterd op de resources die virtuele machines zijn en worden deze machines vervolgens opnieuw opgestart.

```bash
az vm restart --ids $(az resource list --tag "restart-tag" --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv)
```

Dit voorbeeld werkt in een Bash-shell. Zie [Azure CLI installeren in Windows](/cli/azure/install-azure-cli-windows) voor opties voor het uitvoeren van Azure CLI-scripts in de Windows-client.


## <a name="sample-script"></a>Voorbeeldscript

Het voorbeeld bestaat uit drie scripts.
Met het eerste script worden de virtuele machines ingericht.
De optie no-wait zorgt ervoor dat de opdracht direct wordt uitgevoerd, dus zonder te wachten totdat elke virtuele machine is ingericht.
Het tweede script wacht totdat de virtuele machines volledig zijn ingericht.
Met het derde script worden alle ingerichte virtuele machines opnieuw opgestart en vervolgens alleen de gelabelde machines.

### <a name="provision-the-vms"></a>De virtuele machines inrichten

Met dit script maakt u eerst een resourcegroep en vervolgens drie virtuele machines om opnieuw op te starten.
Twee van de machines krijgen een label.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/provision.sh "Provision the VMs")]

### <a name="wait"></a>Wait

Met dit script wordt elke 20 seconden gecontroleerd wat de status van het inrichten is totdat alle drie de virtuele machines zijn ingericht, of totdat het inrichten van een van de machines is mislukt.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/wait.sh "Wait for the VMs to be provisioned")]

### <a name="restart-the-vms"></a>De virtuele machines opnieuw opstarten

Met dit script worden alle virtuele machines in de resourcegroep opnieuw opgestart, waarna alleen de gelabelde machines opnieuw worden opgestart.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart.sh "Restart VMs by tag")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Nadat het voorbeeldscript is uitgevoerd, kan de volgende opdracht worden gebruikt om de resourcegroep, de virtuele machines en alle gerelateerde resources te verwijderen.

```azurecli-interactive 
az group delete -n myResourceGroup --no-wait --yes
```

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten voor het maken van een resourcegroep, een virtuele machine, een beschikbaarheidsset, een load balancer en alle gerelateerde resources. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set) | Hiermee maakt u de virtuele machines.  |
| [az vm list](https://docs.microsoft.com/cli/azure/vm) | Gebruik deze opdracht met `--query` om te controleren of de virtuele machines zijn ingericht voordat ze opnieuw worden opgestart en om vervolgens de id's op te vragen van de virtuele machines die opnieuw moeten worden opgestart. |
| [az resource list](https://docs.microsoft.com/cli/azure/vm) | Gebruik deze opdracht met `--query` om de id's op te vragen van de virtuele machines met het label. |
| [az vm restart](https://docs.microsoft.com/cli/azure/vm) | Hiermee start u de virtuele machines opnieuw op. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

U kunt extra CLI-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Linux-VM's](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
