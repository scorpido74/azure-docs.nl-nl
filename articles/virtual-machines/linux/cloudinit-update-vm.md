---
title: Cloud-init gebruiken in een Linux-VM op Azure
description: Cloud-init gebruiken om pakketten in een Linux-VM bij te werken en te installeren tijdens het maken met de Azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/20/2018
ms.author: cynthn
ms.openlocfilehash: 7b7a03572a001fc6d5114635b33510f1a4b1bc70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969144"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Cloud-init gebruiken om pakketten in een Linux-VM in Azure bij te werken en te installeren
In dit artikel ziet u hoe u [cloud-init](https://cloudinit.readthedocs.io) gebruiken om pakketten bij te werken op een Virtuele Linux-machine (VM) of de schaalsets van virtuele machines op inrichtingstijd in Azure. Deze cloud-init-scripts worden op de eerste boot uitgevoerd zodra de resources zijn ingericht door Azure. Zie [cloud-init-overzicht](using-cloud-init.md) voor meer informatie over hoe cloud-init native werkt in Azure en de ondersteunde Linux-distro's

## <a name="update-a-vm-with-cloud-init"></a>Een VM bijwerken met cloud-init
Om veiligheidsredenen u een vm configureren om de nieuwste updates toe te passen bij het opstarten. Aangezien cloud-init werkt in verschillende Linux distro's, is er geen noodzaak om te specificeren `apt` of `yum` voor de package manager. In plaats `package_upgrade` daarvan definieert en laat u het cloud-init-proces het juiste mechanisme bepalen voor de distro die in gebruik is. Met deze workflow u dezelfde cloud-init-scripts gebruiken voor distro's.

Als u het upgradeproces in actie wilt weergeven, maakt u een bestand in uw huidige shell met de naam *cloud_init_upgrade.txt* en plakt u de volgende configuratie. Maak in dit voorbeeld het bestand in de Cloud Shell niet op uw lokale machine. U kunt elke editor die u wilt gebruiken. Voer `sensible-editor cloud_init_upgrade.txt` in voor het maken van het bestand en om een overzicht van beschikbare editors te zien. Kies #1 om de **nano-editor** te gebruiken. Zorg ervoor dat het hele cloud-init-bestand correct is gekopieerd, met name de eerste regel.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Voordat u deze afbeelding implementeert, moet u een resourcegroep maken met de opdracht [Az-groep maken.](/cli/azure/group) Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Maak nu een VM met [az vm maak](/cli/azure/vm) en `--custom-data cloud_init_upgrade.txt` geef het cloud-init-bestand op met als volgt:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH naar het openbare IP-adres van uw VM weergegeven in de uitvoer van de vorige opdracht. Voer als volgt uw **eigen publicIpAddress in:**

```bash
ssh <publicIpAddress>
```

Voer de tool voor pakketbeheer uit en controleer op updates.

```bash
sudo yum update
```

Aangezien cloud-init heeft gecontroleerd op en updates hebt geïnstalleerd bij het opstarten, mogen er geen extra updates worden toegepast.  U ziet het updateproces, het aantal gewijzigde pakketten `httpd` en `yum history` de installatie van door de uitvoer uit te voeren en te controleren die vergelijkbaar is met de onderstaande.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Volgende stappen
Zie het volgende voor aanvullende voorbeelden van configuratiewijzigingen in de cloudinit:
 
- [Een extra Linux-gebruiker toevoegen aan een vm](cloudinit-add-user.md)
- [Een package manager uitvoeren om bestaande pakketten bij te werken bij het eerste opstarten](cloudinit-update-vm.md)
- [De lokale hostnaam vm wijzigen](cloudinit-update-vm-hostname.md) 
- [Een toepassingspakket installeren, configuratiebestanden bijwerken en sleutels injecteren](tutorial-automate-vm-deployment.md)
