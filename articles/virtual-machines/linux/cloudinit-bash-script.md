---
title: Cloud-init gebruiken om een bashscript uit te voeren in een Linux-vm op Azure
description: Cloud-init gebruiken om een bashscript in een Linux-vm uit te voeren tijdens het maken met de Azure CLI
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: e2f19ceb6c7f19ba749b46a3553036587be6a71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969215"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Cloud-init gebruiken om een bashscript uit te voeren in een Linux-vm in Azure
In dit artikel ziet u hoe u [cloud-init](https://cloudinit.readthedocs.io) gebruiken om een bestaand bashscript uit te voeren op een Virtuele Linux-machine (VM) of vmss (virtual machine scale sets) op het inrichten van de tijd in Azure. Deze cloud-init-scripts worden op de eerste boot uitgevoerd zodra de resources zijn ingericht door Azure. Zie [cloud-init-overzicht](using-cloud-init.md) voor meer informatie over hoe cloud-init native werkt in Azure en de ondersteunde Linux-distro's

## <a name="run-a-bash-script-with-cloud-init"></a>Een bashscript uitvoeren met cloud-init
Met cloud-init hoeft u uw bestaande scripts niet om te zetten in een cloud-config, cloud-init accepteert meerdere invoertypen, waarvan er één een bashscript is.

Als u de Linux Custom Script Azure Extension hebt gebruikt om uw scripts uit te voeren, u ze migreren om cloud-init te gebruiken. Azure-extensies zijn echter geïntegreerd rapportage om te waarschuwen voor scriptfouten, een cloud-init-afbeeldingimplementatie mislukt NIET als het script mislukt.

Als u deze functionaliteit in actie wilt zien, maakt u een eenvoudig bashscript voor het testen. Net als het `#cloud-config` cloud-init-bestand moet dit script lokaal zijn voor waar u de AzureCLI-opdrachten uitvoert om uw virtuele machine in te richten.  Maak in dit voorbeeld het bestand in de Cloud Shell niet op uw lokale machine. U kunt elke editor die u wilt gebruiken. Voer `sensible-editor simple_bash.sh` in voor het maken van het bestand en om een overzicht van beschikbare editors te zien. Kies #1 om de **nano-editor** te gebruiken. Zorg ervoor dat het hele cloud-init-bestand correct is gekopieerd, met name de eerste regel.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Voordat u deze afbeelding implementeert, moet u een resourcegroep maken met de opdracht [Az-groep maken.](/cli/azure/group) Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Maak nu een VM met [az vm maak](/cli/azure/vm) `--custom-data simple_bash.sh` en geef het bash-scriptbestand op met als volgt:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Controleren of bashscript is uitgevoerd
SSH naar het openbare IP-adres van uw VM weergegeven in de uitvoer van de vorige opdracht. Voer als volgt uw **eigen publicIpAddress in:**

```bash
ssh <publicIpAddress>
```

Wijzig de **/tmp-map** en controleer of het bestand myScript.txt bestaat en dat er de juiste tekst in zit.  Als dit niet het zo is, u de **/var/log/cloud-init.log** controleren voor meer details.  Zoek naar het volgende item:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Volgende stappen
Zie het volgende voor aanvullende voorbeelden van configuratiewijzigingen in de cloudinit:
 
- [Een extra Linux-gebruiker toevoegen aan een vm](cloudinit-add-user.md)
- [Een package manager uitvoeren om bestaande pakketten bij te werken bij het eerste opstarten](cloudinit-update-vm.md)
- [De lokale hostnaam vm wijzigen](cloudinit-update-vm-hostname.md) 
- [Een toepassingspakket installeren, configuratiebestanden bijwerken en sleutels injecteren](tutorial-automate-vm-deployment.md)
