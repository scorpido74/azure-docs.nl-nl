---
title: Linux VM's herstellen met chroot waar LVM (Logical Volume Manager) wordt gebruikt - Azure VM's
description: Herstel van Linux VM's met LZV's.
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: Linux chroot LVM
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/24/2019
ms.author: vilibert
ms.openlocfilehash: 20d710f717a9dff26f46ac7a201a9b694f3fbe84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684136"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Problemen met een Linux-vm oplossen wanneer er geen toegang is tot de Azure-seriële console en de schijfindeling LVM (Logisch volumebeheer) gebruikt

Deze handleiding voor probleemoplossing is van nut voor scenario's waarin een Linux-VM niet wordt opgestart, ssh niet mogelijk is en de onderliggende lay-out van het bestandssysteem is geconfigureerd met LVM (Logical Volume Manager).

## <a name="take-snapshot-of-the-failing-vm"></a>Maak een momentopname van de falende VM

Maak een momentopname van de betreffende VM. 

De momentopname wordt vervolgens gekoppeld **rescue** aan een reddings-VM. Volg [hier](https://docs.microsoft.com/azure/virtual-machines/linux/snapshot-copy-managed-disk#use-azure-portal) instructies over het maken van een **momentopname.**

## <a name="create-a-rescue-vm"></a>Een reddings-vm maken
Meestal wordt een reddings-VM van dezelfde of soortgelijke versie van het besturingssysteem aanbevolen. Dezelfde **regio** en **resourcegroep** van de betreffende VM gebruiken

## <a name="connect-to-the-rescue-vm"></a>Verbinding maken met de reddings-VM
Maak verbinding met **rescue** ssh in de reddings-VM. Verhoog privileges en word supergebruiker met

`sudo su -`

## <a name="attach-the-disk"></a>De schijf koppelen
Bevestig een schijf aan de **reddings-VM** gemaakt van de momentopname die eerder is gemaakt.

Azure-portal -> de > **Disks** **schijven voor de >** 

![Schijf maken](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

Bevolk de velden. Wijs een naam toe aan uw nieuwe schijf en selecteer dezelfde resourcegroep als de momentopname, de getroffen VM en Rescue VM.

Het **brontype** is **Momentopname** .
De **momentopname Bron** is de naam van de **momentopname** die eerder is gemaakt.

![schijf 2 maken](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

Maak een bevestigingspunt voor de aangesloten schijf.

`mkdir /rescue`

Voer de opdracht **fdisk -l** uit om te controleren of de momentopnameschijf is gekoppeld en alle beschikbare apparaten en partities weer te geven

`fdisk -l`

De meeste scenario's, de bijgevoegde snapshot schijf zal worden gezien als **/ dev / sdc** met twee partities **/ dev / sdc1** en **/ dev / sdc2**

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

De **\*** geeft een boot partitie, beide partities moeten worden gemonteerd.

Voer de opdracht **lsblk** uit om de LVMs van de betreffende VM te zien

`lsblk`

![Voer lsblk](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


Controleer of LvM's van de betreffende VM worden weergegeven.
Zo niet, gebruik dan de onderstaande opdrachten om ze in te schakelen en **lsblk**opnieuw uit te voeren.
Zorg ervoor dat de LVM's vanaf de aangesloten schijf zichtbaar zijn voordat u verdergaat.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

Zoek het pad om het logische volume te monteren dat de /(hoofd)partitie bevat. Het heeft de configuratie bestanden zoals / etc / standaard / grub

In dit voorbeeld is het nemen van de uitvoer van de vorige **lsblk-opdracht** **rootvg-rootlv** de juiste **root** LV om te monteren en kan worden gebruikt in de volgende opdracht.

De uitvoer van de volgende opdracht toont het pad om te monteren voor de **root** LV

`pvdisplay -m | grep -i rootlv`

![Rootlv](./media/chroot-logical-volume-manager/locate-rootlv.png)

Ga verder met het monteren van dit apparaat op de directory /rescue

`mount /dev/rootvg/rootlv /rescue`

De partitie monteren waarop de **bootvlag** is ingesteld op /rescue/boot

`
mount /dev/sdc1 /rescue/boot
`

Controleer of de bestandssystemen van de aangesloten schijf nu correct zijn gemonteerd met de **opdracht LSBLK**

![Voer lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

of de **opdracht df-Th**

![Df](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>Het verkrijgen van chroot toegang

Krijg **chroot** toegang, die u in staat zal stellen om verschillende fixes uit te voeren, lichte variaties bestaan voor elke Linux-distributie.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

Als er een fout wordt gemaakt, zoals:

**chroot: kan opdracht '/bin/bash' niet uitvoeren: Geen dergelijk bestand of map**

poging om de **usr** Logisch Volume te monteren

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> Wanneer u opdrachten uitvoert in een **chroot-omgeving,** moet u er rekening mee houden dat ze worden uitgevoerd tegen de gekoppelde OS-schijf en niet tegen de lokale **reddings-VM.** 

Opdrachten kunnen worden gebruikt om software te installeren, verwijderen en bijwerken. Problemen met VM's oplossen om fouten op te lossen.


Voer de lsblk commando en de / redding is nu ![/ en / redding / boot is / boot Chrooted](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>Fixes uitvoeren

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>Voorbeeld 1 - de VM configureren om op te starten vanuit een andere kernel

Een veelvoorkomend scenario is om een VM te dwingen om op te starten vanuit een vorige kernel, omdat de huidige geïnstalleerde kernel mogelijk beschadigd is geworden of een upgrade niet correct is voltooid.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*Walkthrough*

De **grep** opdracht geeft een lijst van de kernels die **grub.cfg** is zich bewust van.
![Kernels](./media/chroot-logical-volume-manager/kernels.png)

**grub2-editenv lijst** geeft aan welke kernel ![zal worden geladen bij volgende boot Kernel standaard](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2-set-default** wordt gebruikt om ![te veranderen naar een andere kernel Grub2 set](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2-editenv** lijst geeft aan welke kernel ![zal worden geladen bij volgende boot Nieuwe kernel](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-mkconfig** herbouwt grub.cfg met ![behulp van de vereiste versies Grub2 mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>Voorbeeld 2 - upgradepakketten

Een mislukte kernelupgrade kan de VM niet opstarten.
Monteer alle logische volumes zodat pakketten kunnen worden verwijderd of opnieuw kunnen worden geïnstalleerd

Voer de **lvs-opdracht uit** om te controleren welke **l.s.** beschikbaar zijn voor montage, elke VM, die is gemigreerd of afkomstig is van een andere cloudprovider, varieert in configuratie.

Verlaat de **chroot-omgeving** en monteer de vereiste **LV**

![Geavanceerd](./media/chroot-logical-volume-manager/advanced.png)

Nu weer toegang tot de **chroot-omgeving** door

`chroot /rescue`

Alle lvs moeten zichtbaar zijn als gemonteerde partities

![Geavanceerd](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

De geïnstalleerde **kernel opvragen**

![Geavanceerd](./media/chroot-logical-volume-manager/rpm-kernel.png)

Indien nodig de **kernel**
![Advanced verwijderen of upgraden](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>Voorbeeld 3 - Seriële console inschakelen
Als toegang niet mogelijk is geweest tot de Azure-seriële console, controleert u GRUB-configuratieparameters voor uw Linux-vm en corrigeert u deze. Gedetailleerde informatie is te vinden [in dit document](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-proactive-configuration)

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>Voorbeeld 4 - kernelloading met problematisch LVM-swapvolume

Een VM kan niet volledig opstarten en daalt in de **dracut** prompt.
Meer details over de fout kunnen worden gevonden vanaf azure seriële console of navigeren naar Azure portal -> opstartdiagnostiek -> Seriële logboek


Een soortgelijke fout kan aanwezig zijn:

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

De grub.cfg is in dit voorbeeld geconfigureerd om een LV te laden met de naam **rd.lvm.lv=VG/SwapVol** en de VM kan dit niet lokaliseren. Deze regel laat zien hoe de kernel wordt geladen verwijzend naar de LV SwapVol

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 Verwijder de gewraakte LV uit de /etc/default/grub configuratie en herbouw grub2.cfg


## <a name="exit-chroot-and-swap-the-os-disk"></a>Sluit chroot af en wissel de OS-schijf

Na het herstellen van het probleem gaat u verder met het loskoppelen en loskoppelen van de schijf van de reddings-VM, zodat deze kan worden verwisseld met de betreffende VM OS-schijf.

```
exit
cd /
umount /rescue/proc/
umount /rescue/sys/
umount /rescue/dev/pts
umount /rescue/dev/
umount /rescue/boot
umount /rescue
```

Maak de schijf los van de reddings-VM en voer een schijfswap uit.

Selecteer de VM van de **portalschijven** en selecteer **losmakende**
![schijf](./media/chroot-logical-volume-manager/detach-disk.png) 

De wijzigingen ![opslaan Los maken](./media/chroot-logical-volume-manager/save-detach.png) 

De schijf zal nu beschikbaar komen waardoor het kan worden verwisseld met de originele OS-schijf van de getroffen VM.

Navigeer in de Azure-portal naar de falende VM en selecteer
![ **Schijfswapschijf schijven** -> **swap os**](./media/chroot-logical-volume-manager/swap-disk.png) 

Vul de velden in de **schijf Kiezen** is de momentopnameschijf die in de vorige stap net is losgekoppeld. De VM-naam van de betreffende VM is ook vereist en selecteert **OK**

![Nieuwe os schijf](./media/chroot-logical-volume-manager/new-osdisk.png) 

Als de VM de schijfswap uitvoert, wordt de VM opnieuw opgestart zodra de schijfswapbewerking is voltooid.


## <a name="next-steps"></a>Volgende stappen
Meer informatie over

 [Azure Serial Console]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)

[Modus voor één gebruiker](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode)
