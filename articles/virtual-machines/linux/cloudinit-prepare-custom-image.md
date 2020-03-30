---
title: Azure VM-afbeelding voorbereiden voor gebruik met cloud-init
description: Een reeds bestaande Azure VM-afbeelding voorbereiden voor implementatie met cloud-init
author: danis
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: danis
ms.openlocfilehash: fef41f4dc90c03e3efbe4c8a75e495c26eec64b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066818"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Een bestaande Linux Azure VM-afbeelding voorbereiden voor gebruik met cloud-init
In dit artikel ziet u hoe u een bestaande virtuele Azure-machine gebruiken en voorbereiden om opnieuw te worden geïmplementeerd en klaar voor gebruik in de cloud. De resulterende afbeelding kan worden gebruikt om een nieuwe virtuele machine of virtuele machine schaal sets te implementeren - die beide vervolgens verder kunnen worden aangepast door cloud-init tijdens de implementatie tijd.  Deze cloud-init-scripts worden op de eerste boot uitgevoerd zodra de resources zijn ingericht door Azure. Zie [cloud-init-overzicht](using-cloud-init.md) voor meer informatie over hoe cloud-init native werkt in Azure en de ondersteunde Linux-distro's

## <a name="prerequisites"></a>Vereisten
In dit document wordt ervan uitgegaan dat u al een draaiende Virtuele Azure-machine hebt waarop een ondersteunde versie van het Linux-besturingssysteem wordt uitgevoerd. U hebt de machine al geconfigureerd om aan uw behoeften te voldoen, alle benodigde modules te installeren, alle benodigde updates te verwerken en deze te testen om ervoor te zorgen dat deze aan uw eisen voldoet. 

## <a name="preparing-rhel-76--centos-76"></a>RHEL 7.6 / CentOS 7.6 voorbereiden
U moet SSH in uw Linux VM en voer de volgende commando's om cloud-init te installeren.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

Werk `cloud_init_modules` de `/etc/cloud/cloud.cfg` sectie in om de volgende modules op te nemen:

```bash
- disk_setup
- mounts
```

Hier is een voorbeeld van `cloud_init_modules` hoe een algemene sectie eruit ziet.

```bash
cloud_init_modules:
 - migrator
 - bootcmd
 - write-files
 - growpart
 - resizefs
 - disk_setup
 - mounts
 - set_hostname
 - update_hostname
 - update_etc_hosts
 - rsyslog
 - users-groups
 - ssh
```

Een aantal taken met betrekking tot het inrichten en verwerken `/etc/waagent.conf`van tijdelijke schijven moeten worden bijgewerkt in . Voer de volgende opdrachten uit om de juiste instellingen bij te werken.

```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cloud-init clean
```

Alleen Azure als gegevensbron voor de Azure Linux `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` Agent toestaan door een nieuw bestand te maken met behulp van een editor naar keuze met de volgende regel:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
```

Als uw bestaande Azure-image een swapbestand heeft geconfigureerd en u de swapbestandsconfiguratie voor nieuwe afbeeldingen met behulp van cloud-init wilt wijzigen, moet u het bestaande swapbestand verwijderen.

Voor red hat gebaseerde afbeeldingen - volg de instructies in het volgende Red Hat-document waarin wordt uitgelegd hoe [u het swapbestand verwijderen.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file)

Voor CentOS-afbeeldingen waarvoor swapfile is ingeschakeld, u de volgende opdracht uitvoeren om het swapbestand uit te schakelen:

```bash
sudo swapoff /mnt/resource/swapfile
```

Zorg ervoor dat de `/etc/fstab` swapfile referentie wordt verwijderd uit - het moet er ongeveer als de volgende uitvoer:

```output
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Als u ruimte wilt besparen en het swapbestand wilt verwijderen, u de volgende opdracht uitvoeren:

```bash
rm /mnt/resource/swapfile
```

## <a name="extra-step-for-cloud-init-prepared-image"></a>Extra stap voor cloud-init voorbereide afbeelding
> [!NOTE]
> Als uw afbeelding voorheen een **door de cloud voorbereide** en geconfigureerde afbeelding was, moet u de volgende stappen uitvoeren.

De volgende drie opdrachten worden alleen gebruikt als de VM die u aannueert om een nieuwe gespecialiseerde bronafbeelding te zijn, eerder is ingericht door cloud-init.  U hoeft deze NIET uit te voeren als uw afbeelding is geconfigureerd met behulp van de Azure Linux Agent.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Linux Agent-instelling afronden 
Alle Azure-platformafbeeldingen hebben de Azure Linux Agent geïnstalleerd, ongeacht of deze is geconfigureerd door cloud-init of niet.  Voer de volgende opdracht uit om de gebruiker te deprovisioneren vanaf de Linux-machine. 

```bash
sudo waagent -deprovision+user -force
```

Zie de [Azure Linux Agent](../extensions/agent-linux.md) voor meer informatie over de azure Linux Agent voor meer informatie.

Sluit de SSH-sessie af en voer vanuit uw bashshell de volgende AzureCLI-opdrachten uit om een nieuwe Azure VM-afbeelding te detoewijzen, generaliseren en maken.  Vervang `myResourceGroup` `sourceVmName` en met de juiste informatie die uw sourceVM weerspiegelt.

```azurecli
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Volgende stappen
Zie het volgende voor aanvullende voorbeelden van configuratiewijzigingen in de cloudinit:
 
- [Een extra Linux-gebruiker toevoegen aan een vm](cloudinit-add-user.md)
- [Een package manager uitvoeren om bestaande pakketten bij te werken bij het eerste opstarten](cloudinit-update-vm.md)
- [De lokale hostnaam vm wijzigen](cloudinit-update-vm-hostname.md) 
- [Een toepassingspakket installeren, configuratiebestanden bijwerken en sleutels injecteren](tutorial-automate-vm-deployment.md)
