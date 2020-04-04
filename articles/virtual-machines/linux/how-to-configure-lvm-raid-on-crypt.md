---
title: LVM en RAID configureren op versleutelde apparaten - Azure Disk Encryption
description: Dit artikel bevat instructies voor het configureren van LVM en RAID op versleutelde apparaten voor Linux VM's.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 4e342ff44af38b8e79dc8695c1270b1f5c68e0a8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657445"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>LVM en RAID configureren op versleutelde apparaten

Dit artikel is een stapsgewijs proces voor het uitvoeren van Logical Volume Management (LVM) en RAID op versleutelde apparaten. Het proces is van toepassing op de volgende omgevingen:

- Linux-distributies
    - RHEL 7,6+
    - Ubuntu 18.04+
    - SUSE 12+
- Azure Disk Encryption single-pass extensie
- Azure Disk Encryption dual-pass extensie


## <a name="scenarios"></a>Scenario's

De procedures in dit artikel ondersteunen de volgende scenario's:  

- LVM configureren bovenop versleutelde apparaten (LVM-on-crypt)
- RAID configureren bovenop versleutelde apparaten (RAID-on-crypt)

Nadat het onderliggende apparaat of de onderliggende apparaten zijn versleuteld, u de LVM- of RAID-structuren bovenop die gecodeerde laag maken. 

De fysieke volumes (PC's) worden gemaakt bovenop de gecodeerde laag. De fysieke volumes worden gebruikt om de volumegroep te maken. U maakt de volumes en voegt de vereiste vermeldingen toe op /etc/fstab. 

![Diagram van de lagen van LVM-structuren](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

Op een vergelijkbare manier wordt het RAID-apparaat gemaakt bovenop de gecodeerde laag op de schijven. Bovenop het RAID-apparaat wordt een bestandssysteem gemaakt dat als normaal apparaat wordt toegevoegd aan /etc/fstab.

## <a name="considerations"></a>Overwegingen

Wij raden u aan LVM-on-crypt te gebruiken. RAID is een optie wanneer LVM niet kan worden gebruikt vanwege specifieke toepassings- of omgevingsbeperkingen.

U gebruikt de optie **EncryptFormatAll.** Zie [De functie EncryptFormatAll gebruiken voor gegevensschijven op Linux VM's voor](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vms)meer informatie over deze optie.

Hoewel u deze methode gebruiken wanneer u het besturingssysteem ook versleutelt, versleutelen we hier alleen gegevensstations.

De procedures gaan ervan uit dat u de vereisten al hebt gecontroleerd in [Azure Disk Encryption-scenario's op Linux VM's](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux) en in [Quickstart: Een Linux-VM maken en versleutelen met de Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstart).

De Dual-Pass-versie azure diskencryptie bevindt zich op een afschrijvingspad en mag niet langer worden gebruikt op nieuwe versleutelingen.

## <a name="general-steps"></a>Algemene stappen

Wanneer u de "on-crypt"-configuraties gebruikt, gebruikt u het proces dat in de volgende procedures wordt beschreven.

>[!NOTE] 
>We gebruiken variabelen in het hele artikel. Vervang de waarden dienovereenkomstig.

### <a name="deploy-a-vm"></a>Een virtuele machine implementeren 
De volgende opdrachten zijn optioneel, maar we raden u aan deze toe te passen op een nieuw geïmplementeerde virtuele machine (VM).

PowerShell:

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Azure CLI:

```bash
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>Schijven aan de VM koppelen
Herhaal de volgende `$N` opdrachten voor het aantal nieuwe schijven dat u aan de vm wilt koppelen.

PowerShell:

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Azure CLI:

```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>Controleren of de schijven zijn gekoppeld aan de VM
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Lijst met bijgevoegde schijven in PowerShell](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Azure CLI:

```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Lijst met bijgevoegde schijven in azure cli](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

Portal:

![Lijst met bijgevoegde schijven in de portal](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

Besturingssysteem:

```bash
lsblk 
```
![Lijst met bijgevoegde schijven in het besturingssysteem](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>De schijven configureren om te worden versleuteld
Deze configuratie gebeurt op het niveau van het besturingssysteem. De bijbehorende schijven zijn geconfigureerd voor een traditionele versleuteling via Azure Disk Encryption:

- Op de schijven worden bestandssystemen gemaakt.
- Er worden tijdelijke bevestigingspunten gemaakt om de bestandssystemen te monteren.
- Bestandssystemen zijn geconfigureerd op /etc/fstab om te worden gemonteerd bij het opstarten.

Controleer de apparaatletter die aan de nieuwe schijven is toegewezen. In dit voorbeeld gebruiken we vier gegevensschijven.

```bash
lsblk 
```
![Gegevensschijven die aan het besturingssysteem zijn gekoppeld](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>Een bestandssysteem maken boven op elke schijf
Deze opdracht verlegt de creatie van een ext4-bestandssysteem op elke schijf die is gedefinieerd op het "in" deel van de "voor"-cyclus.

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Het creëren van een ext4-bestandssysteem](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

Zoek de universeel unieke id (UUID) van de bestandssystemen die u onlangs hebt gemaakt, maak een tijdelijke map, voeg de bijbehorende vermeldingen toe op /etc/fstab en monteer alle bestandssystemen.

Deze opdracht wordt ook herhaald op elke schijf die is gedefinieerd op het "in" deel van de "voor"-cyclus:

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>Controleren of de schijven goed zijn gemonteerd
```bash
lsblk
```
![Lijst van gemonteerde tijdelijke bestandssystemen](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

Controleer ook of de schijven zijn geconfigureerd:

```bash
cat /etc/fstab
```
![Configuratie-informatie via fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>De gegevensschijven versleutelen
PowerShell met behulp van een sleutelversleutelingssleutel (KEK):

```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```

Azure CLI met een KEK:

```bash
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
### <a name="verify-the-encryption-status"></a>De versleutelingsstatus verifiëren

Ga alleen door naar de volgende stap wanneer alle schijven zijn versleuteld.

PowerShell:

```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![Versleutelingsstatus in PowerShell](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Azure CLI:

```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Versleutelingsstatus in azure cli](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

Portal:

![Versleutelingsstatus in de portal](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

OS-niveau:

```bash
lsblk
```
![Versleutelingsstatus in het besturingssysteem](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

De extensie voegt de bestandssystemen toe aan /var/lib/azure_disk_encryption_config/azure_crypt_mount (een oude encryptie) of aan /etc/crypttab (nieuwe encryptie).

>[!NOTE] 
>Wijzig geen van deze bestanden.

Dit bestand zorgt ervoor dat deze schijven tijdens het opstartproces worden geactiveerd, zodat LVM of RAID ze later kunnen gebruiken. 

Maak je geen zorgen over de bevestigingspunten in dit bestand. Azure Disk Encryption verliest de mogelijkheid om de schijven te laten monteren als een normaal bestandssysteem nadat we een fysiek volume of een RAID-apparaat bovenop die versleutelde apparaten hebben gemaakt. (Hiermee wordt de bestandssysteemindeling verwijderd die we tijdens het voorbereidingsproces hebben gebruikt.)

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>De tijdelijke mappen en tijdelijke fstab-items verwijderen
U ontkoppelt de bestandssystemen op de schijven die worden gebruikt als onderdeel van LVM.

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
En verwijder de /etc/fstab items:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Controleer of de schijven niet zijn gemonteerd en of de vermeldingen op /etc/fstab zijn verwijderd

```bash
lsblk
```
![Verificatie dat tijdelijke bestandssystemen zijn ontkoppeld](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

En controleer of de schijven zijn geconfigureerd:
```bash
cat /etc/fstab
```
![Controleren of tijdelijke fstab-items worden verwijderd](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>Stappen voor LVM-on-crypt
Nu de onderliggende schijven zijn versleuteld, u de LVM-structuren maken.

In plaats van de apparaatnaam te gebruiken, gebruikt u de /dev/mapperpaden voor elk van de schijven om een fysiek volume te maken (op de cryptelaag bovenop de schijf, niet op de schijf zelf).

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>LVM bovenop de versleutelde lagen configureren
#### <a name="create-the-physical-volumes"></a>De fysieke volumes maken
U krijgt een waarschuwing waarin wordt gevraagd of het ok is om de handtekening van het bestandssysteem uit te wissen. Ga verder door **y**in te voeren of gebruik **echo "y"** zoals afgebeeld:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Verificatie of er een fysiek volume is gemaakt](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>De /dev/mapper/device namen hier moeten worden vervangen voor uw werkelijke waarden op basis van de output van **lsblk**.

#### <a name="verify-the-information-for-physical-volumes"></a>De informatie voor fysieke volumes verifiëren
```bash
pvs
```

![Informatie over fysieke volumes](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>De volumegroep maken
Maak de volumegroep met dezelfde apparaten die al zijn geïnitialiseerd:

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>De informatie voor de volumegroep controleren

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![Informatie voor de volumegroep](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>Logische volumes maken

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>De gemaakte logische volumes controleren

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![Informatie voor logische volumes](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>Bestandssystemen maken bovenop de structuren voor logische volumes

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>De bevestigingspunten voor de nieuwe bestandssystemen maken

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Voeg de nieuwe bestandssystemen toe aan /etc/fstab en monteer ze

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>Controleren of de nieuwe bestandssystemen zijn gemonteerd

```bash
lsblk -fs
df -h
```
![Informatie voor gemonteerde bestandssystemen](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

Op deze variant van **lsblk**, we zijn een lijst van de apparaten met de afhankelijkheden in omgekeerde volgorde. Met deze optie u de apparaten identificeren die zijn gegroepeerd op basis van het logische volume in plaats van de oorspronkelijke /dev/sd[schijf] apparaatnamen.

Het is belangrijk om ervoor te zorgen dat de **nofail-optie** wordt toegevoegd aan de opties voor het instellen van het bevestigingspunt van de LVM-volumes die zijn gemaakt bovenop een apparaat dat is versleuteld via Azure Disk Encryption. Het voorkomt dat het besturingssysteem vast komt te zitten tijdens het opstartproces (of in de onderhoudsmodus).

Als u de **optie nofail** niet gebruikt:

- Het besturingssysteem komt nooit in de fase waarin Azure Disk Encryption wordt gestart en de gegevensschijven worden ontgrendeld en gemonteerd. 
- De versleutelde schijven worden ontgrendeld aan het einde van het opstartproces. De LVM-volumes en bestandssystemen worden automatisch gemonteerd totdat Azure Disk Encryption ze ontgrendelt. 

U het opnieuw opstarten van de VM testen en valideren dat de bestandssystemen ook automatisch worden gemonteerd na het opstarten. Dit proces kan enkele minuten duren, afhankelijk van het aantal en de grootte van bestandssystemen.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Start de VM opnieuw op en verifieer na opnieuw opstarten

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>Stappen voor RAID-on-crypt
Nu de onderliggende schijven zijn versleuteld, u doorgaan met het maken van de RAID-structuren. Het proces is hetzelfde als het proces voor LVM, maar in plaats van de apparaatnaam te gebruiken, gebruikt u de /dev/mapperpaden voor elke schijf.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>RAID configureren bovenop de versleutelde laag van de schijven
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Informatie voor geconfigureerde RAID via de mdadm-opdracht](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>De /dev/mapper/device namen hier moeten worden vervangen door uw werkelijke waarden, gebaseerd op de output van **lsblk**.

### <a name="checkmonitor-raid-creation"></a>RAID-creatie controleren/controleren
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![Status van RAID](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>Een bestandssysteem maken bovenop het nieuwe RAID-apparaat
```bash
mkfs.ext4 /dev/md10
```

Maak een nieuw bevestigingspunt voor het bestandssysteem, voeg het nieuwe bestandssysteem toe aan /etc/fstab en monteer het:

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

Controleer of het nieuwe bestandssysteem is gemonteerd:

```bash
lsblk -fs
df -h
```
![Informatie voor gemonteerde bestandssystemen](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Het is belangrijk om ervoor te zorgen dat de **nofail-optie** wordt toegevoegd aan de opties voor het instellen van het bevestigingspunt van de RAID-volumes die zijn gemaakt bovenop een apparaat dat is versleuteld via Azure Disk Encryption. Het voorkomt dat het besturingssysteem vast komt te zitten tijdens het opstartproces (of in de onderhoudsmodus).

Als u de **optie nofail** niet gebruikt:

- Het besturingssysteem komt nooit in de fase waarin Azure Disk Encryption wordt gestart en de gegevensschijven worden ontgrendeld en gemonteerd.
- De versleutelde schijven worden ontgrendeld aan het einde van het opstartproces. De RAID-volumes en bestandssystemen worden automatisch gemonteerd totdat Azure Disk Encryption ze ontgrendelt.

U het opnieuw opstarten van de VM testen en valideren dat de bestandssystemen ook automatisch worden gemonteerd na het opstarten. Dit proces kan enkele minuten duren, afhankelijk van het aantal en de grootte van bestandssystemen.

```bash
shutdown -r now
```

En wanneer u inloggen:

```bash
lsblk
df -h
```
## <a name="next-steps"></a>Volgende stappen

- [Problemen met Azure Disk Encryption oplossen](disk-encryption-troubleshooting.md)

