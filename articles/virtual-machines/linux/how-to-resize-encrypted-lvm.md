---
title: De grootte van versleutelde Logical Volume Management-schijven wijzigen met Azure Disk Encryption
description: In dit artikel vindt u instructies voor het wijzigen van de grootte van met ADE versleutelde schijven met behulp van beheer
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: ba652b9424b8d5ce1b6a2c5b7d70b8fd9e999323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343969"
---
# <a name="how-to-resize-logical-volume-management-devices-encrypted-with-azure-disk-encryption"></a>Het formaat wijzigen van beheer apparaten voor logische volumes die zijn versleuteld met Azure Disk Encryption

Dit artikel is een stapsgewijze procedure voor het wijzigen van de grootte van ADE versleutelde gegevens schijven met behulp van LVM (Logical Volume Management) op Linux, die van toepassing zijn op meerdere scenario's.

Het proces is van toepassing op de volgende omgevingen:

- Linux-distributies
    - RHEL 7+
    - Ubuntu 16 +
    - SUSE 12 +
- Azure Disk Encryption extensie voor één stap
- Azure Disk Encryption-extensie voor dubbel slagen

## <a name="considerations"></a>Overwegingen

In dit document wordt ervan uitgegaan dat:

1. Er is een bestaande LVM-configuratie.
   
   Controleer [LVM op een Linux-VM configureren](configure-lvm.md) voor meer informatie over het configureren van LVM op een virtuele Linux-machine.

2. De schijven zijn al versleuteld met Azure Disk Encryption controleren [LVM op crypt configureren](how-to-configure-lvm-raid-on-crypt.md) voor meer informatie over het configureren van LVM-on-crypt.

3. U hebt de vereiste vereisten voor Linux en LVM om deze voor beelden te volgen.

4. U begrijpt dat de aanbeveling om gegevens schijven op Azure te gebruiken, zoals vermeld bij het [oplossen van problemen met apparaatnamen](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-device-names-problems), het gebruik van de/dev/disk/SCSI1/-paden.

## <a name="scenarios"></a>Scenario's

De procedures in dit artikel zijn van toepassing op de volgende scenario's:

### <a name="for-traditional-lvm-and-lvm-on-crypt-configurations"></a>Voor traditionele LVM-en LVM-op-cryptografie configuraties

- Een logisch volume uitbreiden wanneer er ruimte beschikbaar is in de VG

### <a name="for-traditional-lvm-encryption-the-logical-volumes-are-encrypted-not-the-whole-disk"></a>Voor traditionele LVM-versleuteling (de logische volumes worden versleuteld, niet op de hele schijf)

- Een traditioneel LVM-volume uitbreiden een nieuwe HW toevoegen
- Het uitbreiden van een traditioneel LVM volume het formaat van een bestaande HW wijzigen

### <a name="for-lvm-on-crypt-recommended-method-the-entire-disk-is-encrypted-not-only-the-logical-volume"></a>Voor LVM-on-cryptografie (aanbevolen methode wordt de hele schijf versleuteld, niet alleen het logische volume)

- Een LVM uitbreiden op het crypt-volume een nieuwe HW toevoegen
- Een LVM uitbreiden op het crypt-volume het formaat van een bestaande HW wijzigen

> [!NOTE]
> Het wordt niet aanbevolen om traditionele LVM-versleuteling en LVM te combi neren op dezelfde VM.

> [!NOTE]
> In deze voor beelden worden de bestaande namen gebruikt voor schijven, fysieke volumes, volume groepen, logische volumes, bestands systemen, UUID en mountpoints, moet u de waarden die in deze voor beelden worden gegeven, vervangen door uw omgeving.

#### <a name="extending-a-logical-volume-when-theres-available-space-in-the-vg"></a>Een logisch volume uitbreiden wanneer er ruimte beschikbaar is in de VG

Traditionele methode die wordt gebruikt om de grootte van logische volumes aan te passen, kan worden toegepast op niet-versleutelde schijven, traditionele LVM versleutelde volumes en LVM-op-cryptografie configuraties.

1. Controleer de huidige grootte van het bestands systeem dat we willen verg Roten:

    ``` bash
    df -h /mountpoint
    ```

    ![scenarioa-controle-FS1](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Controleer of de VG voldoende ruimte heeft om de LV te verg Roten

    ``` bash
    vgs
    ```

    ![scenarioa-check-VG](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    U kunt ook ' vgdisplay ' gebruiken

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioa-check-vgdisplay](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Bepaal voor welk logisch volume het formaat moet worden gewijzigd

    ``` bash
    lsblk
    ```

    ![scenarioa-check-lsblk1](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    Voor LVM-op-cryptografie ligt het verschil in deze uitvoer, wat aangeeft dat de versleutelde laag zich op de versleutelde laag bevindt met de hele schijf

    ![scenarioa-check-lsblk2](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. Controleer de grootte van het logische volume

    ``` bash
    lvdisplay lvname
    ```

    ![scenarioa-check-lvdisplay01](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. De LV-grootte verg Roten met behulp van '-r ' voor online formaat wijziging van het bestands systeem

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scenarioa-formaat wijzigen-LV](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. De nieuwe grootten voor de LV en het bestands systeem controleren

    ``` bash
    df -h /mountpoint
    ```

    ![scenarioa-check-FS](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    De nieuwe grootte wordt weer gegeven, geeft aan dat er een succes volle grootte van de LV en het bestands systeem is

7. U kunt de LV-gegevens opnieuw controleren om de wijzigingen op het niveau LV te bevestigen

    ``` bash
    lvdisplay lvname
    ```

    ![scenarioa-check-lvdisplay2](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extending-a-traditional-lvm-volume-adding-a-new-pv"></a>Een traditioneel LVM-volume uitbreiden een nieuwe HW toevoegen

Van toepassing wanneer u een nieuwe schijf moet toevoegen om de grootte van de volume groep te verhogen.

1. Controleer de huidige grootte van het bestands systeem dat we willen verg Roten:

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-check-FS](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. De huidige fysieke volume configuratie controleren

    ``` bash
    pvs
    ```

    ![scenariob-controle-PVS](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. De huidige VG-gegevens controleren

    ``` bash
    vgs
    ```

    ![scenariob-controle-VGS](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. De huidige schijf lijst controleren

    Gegevens schijven moeten worden geïdentificeerd door de apparaten te controleren onder/dev/disk/Azure/SCSI1/

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-controle-scs1](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Controleer de uitvoer van lsblk 

    ``` bash
    lsbk
    ```

    ![scenariob-controle-lsblk](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. De nieuwe schijf aan de virtuele machine koppelen

    Volg stap 4 van het volgende document

   - [Een schijf koppelen aan een VM](attach-disk-portal.md)

7. Nadat de schijf is aangesloten, controleert u de schijf lijst, ziet u de nieuwe schijf

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-controle-scsi12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![scenariob-controle-lsblk12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Een nieuwe HW maken boven op de nieuwe gegevens schijf

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![scenariob-pvcreate](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Deze methode maakt gebruik van de hele schijf als een HW zonder partitie. u kunt eventueel ' fdisk ' gebruiken om een partitie te maken en deze partitie vervolgens gebruiken voor de ' pvcreate '.

9. Controleer of de PV is toegevoegd aan de lijst met hw.

    ``` bash
    pvs
    ```

    ![scenariob-controle-pvs1](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. De VG uitbreiden door de nieuwe HW toe te voegen

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![scenariob-VG-Extend](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. De nieuwe VG-grootte controleren

    ``` bash
    vgs
    ```

    ![scenariob-controle-vgs1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Gebruik lsblk om te bepalen voor welke LV het formaat moet worden gewijzigd

    ``` bash
    lsblk
    ```

    ![scenariob-controle-lsblk1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. De LV-grootte uitbreiden met behulp van '-r ' om een online toename van het bestands systeem uit te voeren

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scenariob-lvextend](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. De nieuwe grootten van LV en het bestands systeem controleren

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-controle-FS1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    Is belang rijk om te weten dat wanneer ADE wordt gebruikt voor traditionele LVM-configuraties, de versleutelde laag wordt gemaakt op het niveau LV, niet op schijf niveau.

    Op dit moment wordt de versleutelde laag uitgebreid naar de nieuwe schijf.
    De werkelijke gegevens schijf heeft geen versleutelings instellingen op platform niveau, dus de versleutelings status niet wordt bijgewerkt.

    >[!NOTE]
    >Dit zijn een aantal redenen waarom LVM-on-cryptografie de aanbevolen aanpak is. 

15. Controleer de versleutelings informatie in de portal:

    ![scenariob-controle-portal1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    U moet een nieuwe LV toevoegen en de uitbrei ding inschakelen op de VM om de versleutelings instellingen op de schijf bij te werken.
    
16. Voeg een nieuwe LV toe, maak een bestands systeem aan en voeg deze toe aan bestand/etc/fstab

17. Stel de versleutelings extensie opnieuw in om de versleutelings instellingen op de nieuwe gegevens schijf op platform niveau te stem pelen.

    Voorbeeld:

    CLI

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Controleer de versleutelings informatie in de portal:

    ![scenariob-controle-portal2](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

19. Nadat de versleutelings instellingen zijn bijgewerkt, kunt u de nieuwe LV verwijderen. u moet ook de vermelding verwijderen uit bestand/etc/fstab en/etc/crypttab die hiervoor zijn gemaakt.

    ![scenariob-delete-fstab-crypttab](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

20. Het logische volume ontkoppelen

    ``` bash
    umount /mountpoint
    ```

21. De versleutelde laag van het volume sluiten

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

22. LV verwijderen

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extending-a-traditional-lvm-volume-resizing-an-existing-pv"></a>Het uitbreiden van een traditioneel LVM volume het formaat van een bestaande HW wijzigen

Voor bepaalde scenario's of beperkingen moet u het formaat van een bestaande schijf wijzigen.

1. De versleutelde schijven identificeren

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenarioc-controle-SCSI1](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![scenarioc-controle-lsblk](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. De HW-informatie controleren

    ``` bash
    pvs
    ```

    ![scenarioc-controle-PVS](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    Alle ruimte op alle PVs wordt momenteel gebruikt

3. Controleer de VGs-gegevens

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![scenarioc-controle-VGS](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Controleer de grootte van de schijf. u kunt fdisk of lsblk gebruiken om de grootte van het station weer te geven

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc-check-fdisk](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    We hebben vastgesteld welke PVs aan welke LVs worden gekoppeld met behulp van lsblk-FS, maar u kunt deze ook identificeren door ' lvdisplay ' uit te voeren

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![check-lvdisplay](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    In dit voor beeld zijn alle 4 gegevens stations onderdeel van hetzelfde VG en een enkele LV, uw configuratie kan afwijken van dit voor deel.

5. Controleer het huidige bestandssysteem gebruik:

    ``` bash
    df -h /datalvm*
    ```

    ![scenarioc-selectie-DF](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Wijzig de grootte van de gegevens schijf (s):

    U kunt verwijzen naar [Linux-schijven](expand-disks.md) (alleen naar het formaat van de schijf wijzigen), u kunt de portal, CLI of Power shell gebruiken om deze stap uit te voeren.

    >[!NOTE]
    >Houd er rekening mee dat het wijzigen van de grootte van bewerkingen op virtuele schijven niet kan worden uitgevoerd als de virtuele machine wordt uitgevoerd. U moet de toewijzing van de virtuele machine voor deze stap ongedaan maken

7. Wanneer de grootte van de schijven wordt gewijzigd in de vereiste waarde, start u de virtuele machine en controleert u de nieuwe grootte met behulp van fdisk

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc-controle-fdisk1](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    Voor dit specifieke geval is/dev/sdd gewijzigd van 5G naar 20G

8. De huidige PV-grootte controleren

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc-controle-pvdisplay](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    Zelfs als de grootte van de schijf is gewijzigd, heeft de PV nog steeds de vorige grootte.

9. De PV verg Roten of verkleinen

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![scenarioc-controle-pvresize](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. De PV-grootte controleren

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc-controle-pvdisplay1](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Pas dezelfde procedure toe voor alle schijven waarvan u de grootte wilt wijzigen.

11. Controleer de VG-gegevens

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioc-controle-vgdisplay1](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    De VG heeft nu ruimte om toe te wijzen aan de LVs

12. Grootte van LV wijzigen

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![scenarioc-controle-lvresize1](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. FS-grootte controleren

    ``` bash
    df -h /datalvm2
    ```

    ![scenarioc-controle-df3](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extending-an-lvm-on-crypt-volume-adding-a-new-pv"></a>Een LVM-op-cryptografie volume uitbreiden een nieuwe HW toevoegen

Deze methode volgt de stappen van [LVM op crypt configureren](how-to-configure-lvm-raid-on-crypt.md) om een nieuwe schijf toe te voegen en deze te configureren onder een LVM-op-cryptografie configuratie.

U kunt deze methode gebruiken om ruimte toe te voegen aan een bestaande LV of in plaats daarvan om nieuwe VGs of LVs te maken.

1. De huidige grootte van uw VG controleren

    ``` bash
    vgdisplay vgname
    ```

    ![scenario-check-vg01](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Controleer de grootte van de FS en LV die u wilt verhogen

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![scenario-check-lv01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![scenario-check-fs01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Voeg een nieuwe gegevens schijf toe aan de VM en Identificeer deze.

    Controleer de schijven voordat u de schijf toevoegt

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![scenario-check-newdisk01](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Controleer de schijven voordat u de nieuwe schijf toevoegt

    ``` bash
    lsblk
    ```

    ![scenario-check-newdisk002](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Voeg een nieuwe schijf toe met Power shell, de Azure CLI of de Azure Portal. Controleer hoe u [een schijf koppelt](attach-disk-portal.md) voor naslag informatie over het toevoegen van schijven aan een virtuele machine.

    Volgens het kernel name-schema voor de apparaten die door het nieuwe station normaal worden toegewezen, krijgt de volgende beschik bare letter de nieuwe toegevoegde schijf SDD.

4. De schijven controleren na het toevoegen van de nieuwe schijf

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![scenario-check-newdisk02](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)-

    ``` bash
    lsblk
    ```

    ![scenario-check-newdisk003](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Een bestands systeem maken boven op de recent toegevoegde schijf

    De recent toegevoegde schijf koppelen aan de gekoppelde apparaten op/dev/disk/Azure/SCSI1/

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![scenario-check-newdisk03](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![scenario-mkfs01](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Een nieuwe tijdelijke koppel punt maken voor de nieuwe toegevoegde schijf

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Het onlangs gemaakte bestands systeem toevoegen aan bestand/etc/fstab

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. De nieuwe gemaakte FS koppelen met mount-a

    ``` bash
    mount -a
    ```

9. Controleren of de nieuwe toegevoegde FS is gekoppeld

    ``` bash
    df -h
    ```

    ![Resize-LVM-scenario-DF](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![Resize-LVM-scenario-lsblk](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. De versleuteling die eerder is gestart voor gegevens stations opnieuw starten

    Voor LVM-on-crypt is het raadzaam om EncryptFormatAll te gebruiken anders kan een dubbele versleuteling optreden tijdens het instellen van extra schijven.

    Zie voor meer informatie over het gebruik [LVM configureren op crypt](how-to-configure-lvm-raid-on-crypt.md).

    Voorbeeld:

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    Wanneer de versleuteling is voltooid, ziet u een crypt-laag op de zojuist toegevoegde schijf

    ``` bash
    lsblk
    ```

    ![scenario-lsblk2](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. De versleutelde laag van de nieuwe schijf ontkoppelen

    ``` bash
    umount ${newmount}
    ```

12. De huidige PVS-gegevens controleren

    ``` bash
    pvs
    ```

    ![scenario-currentpvs](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Een HW maken boven op de versleutelde laag van de schijf

    Haal de apparaatnaam op uit de vorige lsblk-opdracht en voeg/dev/mapper voor de apparaatnaam toe om de HW te maken

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![scenario-pvcreate](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Er wordt een waarschuwing weer gegeven over het wissen van de huidige ext4 FS-hand tekening. dit wordt verwacht, antwoord y aan deze vraag.

14. Controleer of de nieuwe hw is toegevoegd aan de LVM-configuratie

    ``` bash
    pvs
    ```

    ![scenario-newpv](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Voeg de nieuwe HW toe aan de VG die u wilt verhogen

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![scenario-vgextent](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. De nieuwe grootte en beschik bare ruimte van de VG controleren

    ``` bash
    vgdisplay vgname
    ```

    ![scenario-vgdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Let op de toename van het totale aantal PE en de vrije PE/grootte

17. Verhoog de grootte van de LV en het bestands systeem met behulp van de optie-r op lvextend (in dit voor beeld nemen we de totale beschik bare ruimte in de VG in en voegt u deze toe aan het gegeven logische volume)

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![scenario-lvextend](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

18. De grootte van de LV controleren

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![scenario-lvdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

19. Controleer de grootte van het bestands systeem waarvan u zojuist het formaat hebt gewijzigd

    ``` bash
    df -h mountpoint
    ```

    ![scenario-DF1](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

20. Controleer of de laag LVM is gemaakt boven op de versleutelde laag

    ``` bash
    lsblk
    ```

    ![scenario-lsblk3](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    Als u lsblk zonder opties gebruikt, worden de koppel punten niet meerdere keren weer gegeven, omdat deze worden gesorteerd op apparaat-en logische volumes, kunt u lsblk-FS (-s) gebruiken om de sortering ongedaan te maken zodat de mountpoints één keer worden weer gegeven, waarna de schijven meerdere keren worden weer gegeven.

    ``` bash
    lsblk -fs
    ```

    ![scenario-lsblk4](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extending-an-lvm-on-crypt-volume-resizing-an-existing-pv"></a>Een LVM uitbreiden op het crypt-volume het formaat van een bestaande HW wijzigen

1. De versleutelde schijven identificeren

    ``` bash
    lsblk
    ```

    ![scenariof-lsblk01](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![scenariof-lsblk012](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. Uw HW-gegevens controleren

    ``` bash
    pvs
    ```

    ![scenariof-pvs1](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Controleer uw VG-gegevens

    ``` bash
    vgs
    ```

    ![scenariof-vgs](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Controleer uw LV-gegevens

    ``` bash
    lvs
    ```

    ![scenariof-lvs](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Het bestandssysteem gebruik controleren

    ``` bash
    df -h /mountpoint(s)
    ```

    ![LVM-scenariof-FS](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. Controleer de grootte van uw schijven

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk01](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Grootte van de gegevensschijf wijzigen

    U kunt verwijzen naar [Linux-schijven](expand-disks.md) (alleen naar het formaat van de schijf wijzigen), u kunt de portal, CLI of Power shell gebruiken om deze stap uit te voeren.

    >[!NOTE]
    >Houd er rekening mee dat het wijzigen van de grootte van bewerkingen op virtuele schijven niet kan worden uitgevoerd als de virtuele machine wordt uitgevoerd. U moet de toewijzing van de virtuele machine voor deze stap ongedaan maken

8. Controleer de grootte van uw schijven

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk02](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    Houd er rekening mee dat (in dit geval) de grootte van beide schijven van 2 tot 4 GB is gewijzigd, maar de FS-, LV-en HW-grootte hetzelfde blijven.

9. De huidige PV-grootte controleren

    Houd er rekening mee dat op LVM-op-cryptografie de PV het/dev/Mapper/apparaat is, niet het/dev/sd *-apparaat

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof-pvs](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. De PV verg Roten of verkleinen

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![scenariof-formaat-HW](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. De PV-grootte controleren na het formaat

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof-HW](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. De versleutelde laag op het PV formaat wijzigen

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Pas dezelfde procedure toe voor alle schijven waarvan u de grootte wilt wijzigen.

13. Controleer uw VG-gegevens

    ``` bash
    vgdisplay vgname
    ```

    ![scenariof-vg](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    De VG heeft nu ruimte om toe te wijzen aan de LVs

14. De LV-gegevens controleren

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof-LV](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Het FS-gebruik controleren

    ``` bash
    df -h /mountpoint
    ```

    ![scenariof-FS](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Grootte van LV wijzigen

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![scenariof-lvresize](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    We gebruiken de optie-r om ook de grootte van het FS-formaat te wijzigen

17. De LV-gegevens controleren

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof-lvsize](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Het bestandssysteem gebruik controleren

    ``` bash
    df -h /mountpoint
    ```

    ![Bestands systeem maken](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

    Dezelfde procedure voor het wijzigen van de grootte Toep assen op extra LV waarvoor deze is vereist

## <a name="next-steps"></a>Volgende stappen

- [Problemen met Azure Disk Encryption oplossen](disk-encryption-troubleshooting.md)
