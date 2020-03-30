---
title: Toegang tot een Azure Linux VM opnieuw instellen
description: Beheergebruikers beheren en de toegang op Linux VM's opnieuw instellen met de VMAccess-extensie en de Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2018
ms.author: akjosh
ms.openlocfilehash: bd9dc05a84a4ee54fce40e6c88e87ac90bfee8a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250359"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Beheer administratieve gebruikers, SSH en controleer of repareer schijven op Linux VM's met de VMAccess-extensie met de Azure CLI
## <a name="overview"></a>Overzicht
De schijf op uw Linux VM vertoont fouten. U reset op de een of andere manier het hoofdwachtwoord voor uw Linux-VM of hebt per ongeluk uw SSH-privésleutel verwijderd. Als dat gebeurde terug in de dagen van het datacenter, zou je nodig hebt om daar te rijden en open de KVM te krijgen op de server console. Denk aan de Azure VMAccess-extensie als die KVM-switch waarmee u toegang krijgt tot de console om de toegang tot Linux te resetten of onderhoud op schijfniveau uit te voeren.

In dit artikel ziet u hoe u de Azure VMAccess-extensie gebruiken om een schijf te controleren of te herstellen, gebruikerstoegang opnieuw in te stellen, beheerdersgebruikersaccounts te beheren of de SSH-configuratie op Linux bij te werken wanneer deze worden uitgevoerd als virtuele azure-machines voor Azure Resource Manager. Als u klassieke virtuele machines moet beheren - u de instructies in de [klassieke VM-documentatie](../linux/classic/reset-access-classic.md)volgen. 
 
> [!NOTE]
> Als u de VMAccess-extensie gebruikt om het wachtwoord van uw vm opnieuw in te stellen na het installeren van de AAD-inlogextensie, moet u de AAD-inlogextensie opnieuw uitvoeren om AAD Login voor uw machine opnieuw in te schakelen.

## <a name="prerequisites"></a>Vereisten
### <a name="operating-system"></a>Besturingssysteem

De VM Access-extensie kan worden uitgevoerd tegen deze Linux-distributies:

| Distributie | Versie |
|---|---|
| Ubuntu | 16,04 LTS, 14,04 LTS en 12,04 LTS |
| Debian | Debian 7.9+, 8.2+ |
| Red Hat | RHEL 6,7+, 7,1+ |
| Oracle Linux | 6,4+, 7,0+ |
| Suse | 11 en 12 |
| Opensuse | openSUSE Leap 42.2+ |
| CentOS | CentOS 6.3+, 7.0+ |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>Manieren om de VMAccess-extensie te gebruiken
Er zijn twee manieren waarop u de VMAccess-extensie op uw Linux-VM's gebruiken:

* Gebruik de Azure CLI en de vereiste parameters.
* [Gebruik onbewerkte JSON-bestanden die het VMAccess-extensieproces verwerken](#use-json-files-and-the-vmaccess-extension) en vervolgens reageren.

In de volgende voorbeelden worden de [az vm-gebruikersopdrachten](/cli/azure/vm/user) gebruikt. Om deze stappen uit te voeren, moet u de nieuwste [Azure CLI](/cli/azure/install-az-cli2) geïnstalleerd en ingelogd op een Azure-account met behulp van [az login](/cli/azure/reference-index).

## <a name="update-ssh-key"></a>SSH-sleutel bijwerken
In het volgende voorbeeld wordt de `azureuser` SSH-sleutel voor de gebruiker op de vm bijgewerkt met de naam: `myVM`

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **LET OP:** De `az vm user update` opdracht voegt de nieuwe tekst `~/.ssh/authorized_keys` van de openbare sleutel toe aan het bestand voor de beheerdergebruiker op de vm. Dit vervangt of verwijdert geen bestaande SSH-sleutels. Dit verwijdert geen eerdere sleutels die zijn ingesteld bij implementatietijd of latere updates via de VMAccess-extensie.

## <a name="reset-password"></a>Wachtwoord opnieuw instellen
In het volgende voorbeeld wordt `azureuser` het wachtwoord `myVM`voor de gebruiker op de vm met de naam opnieuw ingesteld:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>SSH opnieuw starten
In het volgende voorbeeld wordt de SSH-daemon opnieuw gestart en `myVM`wordt de SSH-configuratie gereset naar standaardwaarden op een vm met de naam:

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Een gebruiker met beheerdersrechten/sudo-gebruiker maken
In het volgende voorbeeld `myNewUser` wordt een gebruiker met de naam **Sudo-machtigingen** genaamerd. Het account gebruikt een SSH-sleutel `myVM`voor verificatie op de VM met de naam . Deze methode is ontworpen om u te helpen weer toegang te krijgen tot een VM in het geval dat de huidige referenties verloren gaan of vergeten. Als best practice moeten accounts met **sudo-machtigingen** worden beperkt.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Een gebruiker verwijderen
In het volgende voorbeeld `myNewUser` wordt een `myVM`gebruiker met de naam VM verwijderd:

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>JSON-bestanden en de VMAccess-extensie gebruiken
In de volgende voorbeelden worden onbewerkte JSON-bestanden gebruikt. Gebruik [de AZ VM-extensieset](/cli/azure/vm/extension) om vervolgens je JSON-bestanden aan te roepen. Deze JSON-bestanden kunnen ook worden aangeroepen vanuit Azure-sjablonen. 

### <a name="reset-user-access"></a>Gebruikerstoegang opnieuw instellen
Als u geen toegang meer hebt tot root op uw Linux-VM, u een VMAccess-script starten om de SSH-sleutel of het wachtwoord van een gebruiker bij te werken.

Als u de openbare SSH-sleutel van `update_ssh_key.json` een gebruiker wilt bijwerken, maakt u een bestand met de naam en voegt u instellingen toe in de volgende indeling. Vervang uw eigen `username` waarden `ssh_key` voor de parameters en parameters:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Voer het VMAccess-script uit met:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Als u een gebruikerswachtwoord opnieuw `reset_user_password.json` wilt instellen, maakt u een bestand met de naam en voegt u instellingen toe in de volgende indeling. Vervang uw eigen `username` waarden `password` voor de parameters en parameters:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Voer het VMAccess-script uit met:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>SSH opnieuw starten
Als u de SSH-daemon opnieuw wilt starten en de `reset_sshd.json`SSH-configuratie wilt resetten naar standaardwaarden, maakt u een bestand met de naam . Voeg de volgende inhoud toe:

```json
{
  "reset_ssh": true
}
```

Voer het VMAccess-script uit met:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Beheerdersbeherende gebruikers beheren

Als u een gebruiker wilt maken met **sudo-machtigingen** die een `create_new_user.json` SSH-sleutel voor verificatie gebruikt, maakt u een bestand met de naam en voegt u instellingen toe in de volgende indeling. Vervang uw eigen `username` waarden `ssh_key` voor de en parameters. Deze methode is ontworpen om u te helpen weer toegang te krijgen tot een VM in het geval dat de huidige referenties verloren gaan of vergeten. Als best practice moeten accounts met **sudo-machtigingen** worden beperkt.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Voer het VMAccess-script uit met:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Als u een gebruiker wilt `delete_user.json` verwijderen, maakt u een bestand met de naam en voegt u de volgende inhoud toe. Vervang uw eigen `remove_user` waarde voor de parameter:

```json
{
  "remove_user":"myNewUser"
}
```

Voer het VMAccess-script uit met:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>De schijf controleren of repareren
Met VMAccess u ook een schijf controleren en repareren die u aan de Linux-VM hebt toegevoegd.

Als u de schijf wilt controleren `disk_check_repair.json` en vervolgens wilt herstellen, maakt u een bestand met de naam en voegt u instellingen toe in de volgende indeling. Vervang uw eigen waarde `repair_disk`voor de naam van :

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Voer het VMAccess-script uit met:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van extensie-implementaties kunnen worden opgehaald uit de Azure-portal en met behulp van de Azure CLI. Als u de implementatiestatus van extensies voor een bepaalde VM wilt bekijken, voert u de volgende opdracht uit met de Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op de [FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer Ondersteuning krijgen. Lees de veelgestelde vragen over [Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure Support.
