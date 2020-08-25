---
title: Problemen met de naam wijzigingen van Linux-VM'S in azure oplossen | Microsoft Docs
description: In dit artikel wordt uitgelegd waarom de namen van Linux VM-apparaten worden gewijzigd en hoe het probleem kan worden opgelost.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 31f64a504156134b1d622705d5301d9cd5a5f5b1
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2020
ms.locfileid: "88756823"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Naam wijzigingen van Linux VM-apparaat oplossen

In dit artikel wordt uitgelegd waarom apparaatnamen worden gewijzigd nadat u een virtuele Linux-machine opnieuw hebt opgestart of de gegevens schijven opnieuw koppelt. Het artikel bevat ook oplossingen voor dit probleem.

## <a name="symptoms"></a>Symptomen
U kunt de volgende problemen ondervinden bij het uitvoeren van Linux-Vm's in Microsoft Azure:

- De virtuele machine kan niet worden opgestart na het opnieuw opstarten.
- Wanneer gegevens schijven worden losgekoppeld en opnieuw worden gekoppeld, worden de namen van de schijf apparaten gewijzigd.
- Een toepassing of script dat verwijst naar een schijf met behulp van de apparaatnaam mislukt, omdat de naam van het apparaat is gewijzigd.

## <a name="cause"></a>Oorzaak

Device-paden in Linux zijn niet gegarandeerd consistent tijdens het opnieuw opstarten. Apparaatnamen bestaan uit hoofd letters en kleine getallen. Wanneer het stuur programma van het Linux-opslag apparaat een nieuw apparaat detecteert, wijst het stuur programma primaire en secundaire nummers van het beschik bare bereik toe aan het apparaat. Wanneer een apparaat wordt verwijderd, worden de nummers van de apparaten vrij voor hergebruik.

Het probleem treedt op omdat het scannen van apparaten in Linux wordt gepland door het SCSI-subsysteem om asynchroon te worden uitgevoerd. Als gevolg hiervan kan de padnaam van een apparaat variëren tijdens het opnieuw opstarten.

## <a name="solution"></a>Oplossing

U kunt dit probleem oplossen door permanente naamgeving te gebruiken. Er zijn vier manieren om permanente naamgeving te gebruiken: op bestandssysteem label, op UUID, op ID of op pad. U kunt het beste het bestandssysteem label of de UUID voor virtuele Azure Linux-machines gebruiken.

De meeste distributies bieden de `fstab` **nofail** **nobootwait** -para meters. Met deze para meters kan een systeem worden opgestart wanneer de schijf niet kan worden gekoppeld bij het opstarten. Raadpleeg uw distributie documentatie voor meer informatie over deze para meters. Zie [verbinding maken met de Linux-VM voor het koppelen van de nieuwe schijf](../linux/add-disk.md#format-and-mount-the-disk)voor meer informatie over het configureren van een virtuele Linux-machine voor het gebruik van een UUID wanneer u een gegevens schijf toevoegt.

Wanneer de Azure Linux-agent is geïnstalleerd op een virtuele machine, gebruikt de agent udev-regels voor het samen stellen van een set symbolische koppelingen onder het pad/dev/disk/Azure. Toepassingen en scripts gebruiken udev-regels voor het identificeren van schijven die zijn gekoppeld aan de virtuele machine, samen met het schijf type en schijf-Lun's.

Als u uw fstab al op een zodanige manier hebt bewerkt dat uw virtuele machine niet wordt opgestart en u niet naar uw VM kunt werken, kunt u de [VM-seriële console](./serial-console-linux.md) gebruiken om de [modus voor één gebruiker](./serial-console-grub-single-user-mode.md) in te voeren en uw fstab aan te passen.

### <a name="identify-disk-luns"></a>Schijf-Lun's identificeren

Toepassingen gebruiken Lun's om alle gekoppelde schijven te vinden en om symbolische koppelingen te maken. De Azure Linux-agent bevat udev-regels waarmee symbolische koppelingen van een LUN naar de apparaten worden ingesteld:

```console
$ tree /dev/disk/azure

/dev/disk/azure
├── resource -> ../../sdb
├── resource-part1 -> ../../sdb1
├── root -> ../../sda
├── root-part1 -> ../../sda1
└── scsi1
    ├── lun0 -> ../../../sdc
    ├── lun0-part1 -> ../../../sdc1
    ├── lun1 -> ../../../sdd
    ├── lun1-part1 -> ../../../sdd1
    ├── lun1-part2 -> ../../../sdd2
    └── lun1-part3 -> ../../../sdd3
```

LUN-informatie van het Linux-gast account wordt opgehaald met `lsscsi` of een vergelijkbaar hulp programma:

```console
$ sudo lsscsi

[1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

[2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

[3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

[5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

[5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd
```

De gast-LUN-informatie wordt gebruikt met meta gegevens van het Azure-abonnement om de VHD te vinden in Azure Storage die de partitie gegevens bevat. U kunt bijvoorbeeld de `az` CLI gebruiken:

```azurecli
$ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks
[
{
"caching": "None",
  "createOption": "empty",
"diskSizeGb": 1023,
  "image": null,
"lun": 0,
"managedDisk": null,
"name": "testVM-20170619-114353",
"vhd": {
  "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"
}
},
{
"caching": "None",
"createOption": "empty",
"diskSizeGb": 512,
"image": null,
"lun": 1,
"managedDisk": null,
"name": "testVM-20170619-121516",
"vhd": {
  "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"
  }
  }
]
```

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Bestandssysteem-UUID detecteren met behulp van blkid

Toepassingen en scripts lezen de uitvoer van `blkid` of soort gelijke informatie bronnen om symbolische koppelingen te maken in het pad/dev. In de uitvoer ziet u de UUID van alle schijven die zijn gekoppeld aan de virtuele machine en het bijbehorende apparaatbestand:

```console
$ sudo blkid -s UUID

/dev/sr0: UUID="120B021372645f72"
/dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
/dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
/dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"
```

De udev-regels van de Azure Linux-agent maken een set symbolische koppelingen onder het pad/dev/disk/Azure:

```console
$ ls -l /dev/disk/azure

total 0
lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1
```

Toepassingen gebruiken de koppelingen voor het identificeren van het opstart schijf apparaat en de bron schijf. In azure moeten toepassingen in de/dev/disk/Azure/root-part1-of/dev/disk/Azure-resource-part1-paden zoeken om deze partities te ontdekken.

Eventuele extra partities van de `blkid` lijst bevinden zich op een gegevens schijf. Toepassingen behouden de UUID voor deze partities en gebruiken een pad om de naam van het apparaat te detecteren tijdens runtime:

```console
$ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1
```


### <a name="get-the-latest-azure-storage-rules"></a>De meest recente Azure Storage-regels ophalen

Voer de volgende opdrachten uit om de meest recente Azure Storage regels op te halen:

```console
# sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
# sudo udevadm trigger --subsystem-match=block
```

## <a name="see-also"></a>Zie ook

Raadpleeg voor meer informatie de volgende artikelen:

- [Ubuntu: met UUID](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: permanente naamgeving](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: wat UUID voor u kan betekenen](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Inleiding tot Apparaatbeheer in een modern Linux-systeem](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

