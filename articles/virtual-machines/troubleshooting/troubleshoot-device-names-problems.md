---
title: Problemen met de naamswijziging van Linux VM-apparaat in Azure oplossen | Microsoft Documenten
description: Legt uit waarom Linux VM-apparaatnamen veranderen en hoe het probleem op te lossen.
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
ms.openlocfilehash: 7d8a7e7e88837214042fb8f1c109c0b93bfe771b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058209"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Problemen met de naamvan Linux VM-apparaatwijzigingen oplossen

In dit artikel wordt uitgelegd waarom apparaatnamen veranderen nadat u een Linux-vm opnieuw hebt opgestart of de gegevensschijven opnieuw hebt bevestigd. Het artikel biedt ook oplossingen voor dit probleem.

## <a name="symptoms"></a>Symptomen
Mogelijk worden de volgende problemen ondervonden bij het uitvoeren van Linux VM's in Microsoft Azure:

- De VM wordt niet opgestart na een herstart.
- Wanneer gegevensschijven worden losgemaakt en opnieuw worden bevestigd, worden de namen van het schijfapparaat gewijzigd.
- Een toepassing of script dat verwijst naar een schijf met de naam van het apparaat, mislukt omdat de naam van het apparaat is gewijzigd.

## <a name="cause"></a>Oorzaak

Apparaatpaden in Linux zijn niet gegarandeerd consistent voor alle opnieuw opstarten. Apparaatnamen bestaan uit grote cijfers (letters) en kleine nummers. Wanneer het stuurprogramma voor het Linux-opslagapparaat een nieuw apparaat detecteert, wijst het stuurprogramma belangrijke en kleine nummers uit het beschikbare bereik toe aan het apparaat. Wanneer een apparaat wordt verwijderd, worden de apparaatnummers vrijgemaakt voor hergebruik.

Het probleem treedt op omdat het scannen van apparaten in Linux is gepland door het SCSI-subsysteem om asynchroon te gebeuren. Als gevolg hiervan kan de naam van een apparaatpad per herstart verschillen.

## <a name="solution"></a>Oplossing

Als u dit probleem wilt oplossen, gebruikt u permanente naamgeving. Er zijn vier manieren om permanente naamgeving te gebruiken: op bestandssysteemlabel, op UUID, op id of pad. We raden u aan het filesystem-label of UUID voor Azure Linux VM's te gebruiken.

De meeste distributies bieden de `fstab` **nofail-** of **nobootwait-parameters.** Met deze parameters kan een systeem worden opgestart wanneer de schijf niet kan worden gemonteerd bij het opstarten. Controleer uw distributiedocumentatie voor meer informatie over deze parameters. Zie [Verbinding maken met de Linux-vm om de nieuwe schijf te monteren](../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk)voor informatie over het configureren van een Linux-vm om een UUID te gebruiken wanneer u een gegevensschijf toevoegt.

Wanneer de Azure Linux-agent op een VM is geïnstalleerd, gebruikt de agent Udev-regels om een set symbolische koppelingen te maken onder het /dev/disk/azure-pad. Toepassingen en scripts gebruiken Udev-regels om schijven te identificeren die aan de VM zijn gekoppeld, samen met het schijftype en de schijf-LUN's.

Als u uw fstab al zo hebt bewerkt dat uw VM niet wordt opgestart en u niet naar uw VM ssh, u de [VM Serial Console](./serial-console-linux.md) gebruiken om de modus voor één [gebruiker](./serial-console-grub-single-user-mode.md) in te voeren en uw fstab te wijzigen.

### <a name="identify-disk-luns"></a>SchijfLUN's identificeren

Toepassingen gebruiken LUN's om alle aangesloten schijven te vinden en symbolische koppelingen te maken. De Azure Linux-agent bevat Udev-regels die symbolische koppelingen van een LUN naar de apparaten instellen:

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

LUN-informatie van het Linux-gastaccount `lsscsi` wordt opgehaald met behulp van of een soortgelijk hulpmiddel:

      $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

De LUN-informatie voor gasten wordt gebruikt met azure-abonnementsmetagegevens om de VHD in Azure Storage te vinden die de partitiegegevens bevat. U bijvoorbeeld de `az` CLI gebruiken:

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

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Ontdek filesystem UUIDs met behulp van blkid

Toepassingen en scripts lezen `blkid`de uitvoer van , of soortgelijke bronnen van informatie, om symbolische koppelingen te construeren in het /dev-pad. De uitvoer toont de uu-id's van alle schijven die zijn gekoppeld aan de VM en het bijbehorende apparaatbestand:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

De Udev-regels voor Azure Linux-agent bouwen een set symbolische koppelingen onder het /dev/disk/azure-pad:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Toepassingen gebruiken de koppelingen om het opstartschijfapparaat en de tijdelijke schijf (resource) te identificeren. In Azure moeten toepassingen in de paden /dev/disk/azure/root-part1 of /dev/disk/azure-resource-part1 kijken om deze partities te ontdekken.

Eventuele extra partities `blkid` uit de lijst bevinden zich op een gegevensschijf. Toepassingen behouden de UUID voor deze partities en gebruiken een pad om de naam van het apparaat tijdens runtime te ontdekken:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1


### <a name="get-the-latest-azure-storage-rules"></a>De nieuwste Azure Storage-regels opgebruiken

Voer de volgende opdrachten uit om de nieuwste Azure Storage-regels op te halen:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Zie ook

Raadpleeg voor meer informatie de volgende artikelen:

- [Ubuntu: UUID gebruiken](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: Aanhoudende naamgeving](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: Wat UUIDs voor u kunnen doen](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Inleiding tot apparaatbeheer in een modern Linux-systeem](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

