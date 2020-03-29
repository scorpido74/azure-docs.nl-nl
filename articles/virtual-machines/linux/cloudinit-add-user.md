---
title: Cloud-init gebruiken om een gebruiker toe te voegen aan een Linux-vm op Azure
description: Cloud-init gebruiken om een gebruiker toe te voegen aan een Linux-vm tijdens het maken met de Azure CLI
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: f1782bfe0c14e3b44703f89ec7f78590c1bb74c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969241"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Cloud-init gebruiken om een gebruiker toe te voegen aan een Linux-vm in Azure
In dit artikel ziet u hoe u [cloud-init](https://cloudinit.readthedocs.io) gebruikt om een gebruiker toe te voegen op een virtuele machine (VM) of vmss (virtuele machineschaalsets) op het inrichten van de tijd in Azure. Dit cloud-init-script wordt op de eerste boot uitgevoerd zodra de resources zijn ingericht door Azure. Zie [cloud-init-overzicht](using-cloud-init.md)voor meer informatie over hoe cloud-init native werkt in Azure en de ondersteunde Linux-distro's.

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Een gebruiker toevoegen aan een virtuele machine met cloud-init
Een van de eerste taken op een nieuwe Linux VM is het toevoegen van een extra gebruiker voor jezelf om het gebruik van *root*te voorkomen. SSH-sleutels zijn best practices voor beveiliging en bruikbaarheid. Toetsen worden toegevoegd aan het *bestand ~/.ssh/authorized_keys* met dit cloud-init-script.

Als u een gebruiker wilt toevoegen aan een Linux-vm, maakt u een bestand in uw huidige shell met de naam *cloud_init_add_user.txt* en plakt u de volgende configuratie. Maak in dit voorbeeld het bestand in de Cloud Shell niet op uw lokale machine. U kunt elke editor die u wilt gebruiken. Voer `sensible-editor cloud_init_add_user.txt` in voor het maken van het bestand en om een overzicht van beschikbare editors te zien. Kies #1 om de **nano-editor** te gebruiken. Zorg ervoor dat het hele cloud-init-bestand correct is gekopieerd, met name de eerste regel.  U moet uw eigen openbare sleutel (zoals de inhoud van *~/.ssh/id_rsa.pub)* voor de waarde van `ssh-authorized-keys:` - het is hier verkort om het voorbeeld te vereenvoudigen.

```yaml
#cloud-config
users:
  - default
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```
> [!NOTE] 
> Het #cloud-config-bestand `- default` bevat de parameter inbegrepen. Dit zal de gebruiker toevoegen aan de bestaande admin gebruiker gemaakt tijdens de inrichting. Als u een gebruiker `- default` maakt zonder de parameter, wordt de door het Azure-platform gemaakte automatisch gegenereerde beheergebruiker overschreven. 

Voordat u deze afbeelding implementeert, moet u een resourcegroep maken met de opdracht [Az-groep maken.](/cli/azure/group) Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Maak nu een VM met [az vm maak](/cli/azure/vm) en `--custom-data cloud_init_add_user.txt` geef het cloud-init-bestand op met als volgt:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH naar het openbare IP-adres van uw VM weergegeven in de uitvoer van de vorige opdracht. Voer als volgt uw **eigen publicIpAddress in:**

```bash
ssh <publicIpAddress>
```

Bekijk de inhoud van het */etc/group-bestand* als volgt om te bevestigen dat uw gebruiker is toegevoegd aan de VM en de opgegeven groepen:

```bash
cat /etc/group
```

In de volgende voorbeelduitvoer ziet u dat de gebruiker van het *bestand cloud_init_add_user.txt* is toegevoegd aan de VM en de juiste groep:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Volgende stappen
Zie het volgende voor aanvullende voorbeelden van configuratiewijzigingen in de cloudinit:
 
- [Een extra Linux-gebruiker toevoegen aan een vm](cloudinit-add-user.md)
- [Een package manager uitvoeren om bestaande pakketten bij te werken bij het eerste opstarten](cloudinit-update-vm.md)
- [De lokale hostnaam vm wijzigen](cloudinit-update-vm-hostname.md) 
- [Een toepassingspakket installeren, configuratiebestanden bijwerken en sleutels injecteren](tutorial-automate-vm-deployment.md)
