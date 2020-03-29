---
title: Cloud-init gebruiken om een swappartitie op een Linux-vm te configureren
description: Cloud-init gebruiken om een swappartitie in een Linux-VM te configureren tijdens het maken met de Azure CLI
author: rickstercdn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1247652e536042ee249054d86aed3c3f8e7aa7bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969209"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>Cloud-init gebruiken om een swappartitie op een Linux-vm te configureren
In dit artikel ziet u hoe u [cloud-init](https://cloudinit.readthedocs.io) gebruiken om de swappartitie op verschillende Linux-distributies te configureren. De swap partitie werd traditioneel geconfigureerd door de Linux Agent (WALA) op basis van welke distributies vereist een.  In dit document wordt een overzicht gegeven van het proces voor het bouwen van de swappartitie op aanvraag tijdens het inrichten van de tijd met behulp van cloud-init.  Zie [cloud-init-overzicht](using-cloud-init.md) voor meer informatie over hoe cloud-init native werkt in Azure en de ondersteunde Linux-distro's

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Swappartitie maken voor op Ubuntu gebaseerde afbeeldingen
Ubuntu-galerijafbeeldingen maken standaard op Azure geen swappartities. Als u de configuratie van swappartities wilt inschakelen tijdens vm-provisioningtijd met behulp van cloud-init, raadpleegt u het [AzureSwapPartitions-document](https://wiki.ubuntu.com/AzureSwapPartitions) op de Ubuntu-wiki.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Swappartitie maken voor afbeeldingen op basis van Red Hat en CentOS

Maak een bestand in uw huidige shell met de naam *cloud_init_swappart.txt* en plak de volgende configuratie. Maak in dit voorbeeld het bestand in de Cloud Shell niet op uw lokale machine. U kunt elke editor die u wilt gebruiken. Voer `sensible-editor cloud_init_swappart.txt` in voor het maken van het bestand en om een overzicht van beschikbare editors te zien. Kies #1 om de **nano-editor** te gebruiken. Zorg ervoor dat het hele cloud-init-bestand correct is gekopieerd, met name de eerste regel.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Voordat u deze afbeelding implementeert, moet u een resourcegroep maken met de opdracht [Az-groep maken.](/cli/azure/group) Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Maak nu een VM met [az vm maak](/cli/azure/vm) en `--custom-data cloud_init_swappart.txt` geef het cloud-init-bestand op met als volgt:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>Controleren of er een swappartitie is gemaakt
SSH naar het openbare IP-adres van uw VM weergegeven in de uitvoer van de vorige opdracht. Voer als volgt uw **eigen publicIpAddress in:**

```bash
ssh <publicIpAddress>
```

Zodra u SSH'ed in de vm hebt, controleert u of de swappartitie is gemaakt

```bash
swapon -s
```

De uitvoer van deze opdracht moet er als volgt uitzien:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Als u een bestaande Azure-afbeelding hebt die een swappartitie heeft geconfigureerd en u de swappartitieconfiguratie voor nieuwe afbeeldingen wilt wijzigen, moet u de bestaande swappartitie verwijderen. Zie 'Afbeeldingen aanpassen aan inrichten via cloud-init' document voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Zie het volgende voor aanvullende voorbeelden van configuratiewijzigingen in de cloudinit:
 
- [Een extra Linux-gebruiker toevoegen aan een vm](cloudinit-add-user.md)
- [Een package manager uitvoeren om bestaande pakketten bij te werken bij het eerste opstarten](cloudinit-update-vm.md)
- [De lokale hostnaam vm wijzigen](cloudinit-update-vm-hostname.md) 
- [Een toepassingspakket installeren, configuratiebestanden bijwerken en sleutels injecteren](tutorial-automate-vm-deployment.md)
