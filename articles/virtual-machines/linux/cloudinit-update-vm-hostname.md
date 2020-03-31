---
title: Cloud-init gebruiken om hostnaam in te stellen voor een Linux-VM
description: Cloud-init gebruiken om een Linux-vm aan te passen tijdens het maken met de Azure CLI
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 631b8ef83d5fbf10ec401df7432b23238f2ae2e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969168"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Cloud-init gebruiken om hostnaam in te stellen voor een Linux-vm in Azure
In dit artikel ziet u hoe u [cloud-init](https://cloudinit.readthedocs.io) gebruiken om een specifieke hostnaam te configureren op een virtuele machine (VM) of vmss (virtual machine scale sets) op inrichtingstijd in Azure. Deze cloud-init-scripts worden op de eerste boot uitgevoerd zodra de resources zijn ingericht door Azure. Zie [cloud-init-overzicht](using-cloud-init.md) voor meer informatie over hoe cloud-init native werkt in Azure en de ondersteunde Linux-distro's

## <a name="set-the-hostname-with-cloud-init"></a>De hostnaam instellen met cloud-init
Standaard is de hostnaam dezelfde als de VM-naam wanneer u een nieuwe virtuele machine maakt in Azure.  Als u een cloud-init-script wilt uitvoeren om deze standaardhostnaam te wijzigen wanneer u een `--custom-data` VM in Azure maakt met [az-vm-maken,](/cli/azure/vm)geeft u het cloud-init-bestand op met de switch.  

Als u het upgradeproces in actie wilt weergeven, maakt u een bestand in uw huidige shell met de naam *cloud_init_hostname.txt* en plakt u de volgende configuratie. Maak in dit voorbeeld het bestand in de Cloud Shell niet op uw lokale machine. U kunt elke editor die u wilt gebruiken. Voer `sensible-editor cloud_init_hostname.txt` in voor het maken van het bestand en om een overzicht van beschikbare editors te zien. Kies #1 om de **nano-editor** te gebruiken. Zorg ervoor dat het hele cloud-init-bestand correct is gekopieerd, met name de eerste regel.  

```yaml
#cloud-config
hostname: myhostname
```

Voordat u deze afbeelding implementeert, moet u een resourcegroep maken met de opdracht [Az-groep maken.](/cli/azure/group) Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Maak nu een VM met [az vm maak](/cli/azure/vm) en `--custom-data cloud_init_hostname.txt` geef het cloud-init-bestand op met als volgt:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Eenmaal gemaakt, toont de Azure CLI informatie over de VM. Gebruik `publicIpAddress` de naar SSH om uw VM. Voer als volgt uw eigen adres in:

```bash
ssh <publicIpAddress>
```

Als u de vm-naam wilt zien, gebruikt u de `hostname` opdracht als volgt:

```bash
hostname
```

De VM moet de hostnaam rapporteren als die waarde die is ingesteld in het cloud-init-bestand, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```bash
myhostname
```

## <a name="next-steps"></a>Volgende stappen
Zie het volgende voor aanvullende voorbeelden van configuratiewijzigingen in de cloudinit:
 
- [Een extra Linux-gebruiker toevoegen aan een vm](cloudinit-add-user.md)
- [Een package manager uitvoeren om bestaande pakketten bij te werken bij het eerste opstarten](cloudinit-update-vm.md)
- [De lokale hostnaam vm wijzigen](cloudinit-update-vm-hostname.md) 
- [Een toepassingspakket installeren, configuratiebestanden bijwerken en sleutels injecteren](tutorial-automate-vm-deployment.md)
