---
title: LVM en RAID on-crypt configureren op een Linux VM
description: Dit artikel bevat instructies voor het configureren van LVM en RAID on crypt op Linux VM's.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 78ba47ba887cf7c90adf70d9d444fbd8a3c721cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284905"
---
# <a name="how-to-configure-lvm-and-raid-on-crypt"></a>LVM en RAID on-crypt configureren

Dit document is een stapsgewijs proces over het uitvoeren van LVM op crypte en Raid op crypteconfiguraties.

### <a name="environment"></a>Omgeving

- Linux-distributies
    - RHEL 7,6+
    - Ubuntu 18.04+
    - SUSE 12+
- ADE Single Pass
- ADE Dual Pass


## <a name="scenarios"></a>Scenario's

**Dit scenario is van toepassing op ADE dual-pass en single-pass extensies.**  

- LVM configureren bovenop versleutelde apparaten (LVM-on-Crypt)
- RAID configureren bovenop versleutelde apparaten (RAID-on-Crypt)

Zodra het onderliggende apparaat(en) is versleuteld, u de LVM- of RAID-structuren bovenop die gecodeerde laag maken. De fysieke volumes (PV) worden gemaakt bovenop de versleutelde laag.
De fysieke volumes worden gebruikt om de volumegroep te maken.
U maakt de volumes en voegt de vereiste vermeldingen toe op /etc/fstab. 

![Schijven aan PowerShell koppelen controleren](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

Op een vergelijkbare manier wordt het RAID-apparaat gemaakt bovenop de gecodeerde laag op de schijven. Bovenop het RAID-apparaat wordt een filesystem gemaakt dat als een gewoon apparaat wordt toegevoegd aan /etc/fstab.

### <a name="considerations"></a>Overwegingen

De aanbevolen methode om te gebruiken is LVM-on-Crypt.

RAID wordt overwogen wanneer LVM niet kan worden gebruikt vanwege specifieke beperkingen van de toepassing/omgeving.

U gebruikt de optie EncryptFormatAll, informatie over deze https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vmsfunctie vindt u hier: .

Hoewel deze methode kan worden gedaan wanneer u het besturingssysteem ook versleutelt, versleutelen we alleen gegevensstations.

Deze procedure gaat ervan uit dat u de https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux hier https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstartgenoemde vereisten al hebt bekeken: en hier .

De ADE dual pass-versie bevindt zich op het afkooppad en mag niet langer worden gebruikt op nieuwe ADE-versleutelingen.

### <a name="procedure"></a>Procedure

Wanneer u de configuraties 'op crypte' gebruikt, volgt u het onderstaande proces:

>[!NOTE] 
>We gebruiken variabelen in het hele document en vervangen de waarden dienovereenkomstig.
## <a name="general-steps"></a>Algemene stappen
### <a name="deploy-a-vm"></a>Een virtuele machine implementeren 
>[!NOTE] 
>Hoewel dit optioneel is, raden we u aan dit toe te passen op een nieuw geïmplementeerde VM.

PowerShell
```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Cli:
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
### <a name="attach-disks-to-the-vm"></a>Schijven aan de vm koppelen:
Herhaal dit voor $N aantal nieuwe schijven die u aan de VM PowerShell wilt koppelen
```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```
Cli:
```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```
### <a name="verify-the-disks-are-attached-to-the-vm"></a>Controleer of de schijven zijn gekoppeld aan de vm:
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Controleer de aan](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png) u gekoppelde schijven powershell CLI:
```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Controleer de bijgevoegde](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png) ![CLI-portal:](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png) controleer de schijven die zijn aangesloten bij CLI OS:
```bash
lsblk 
```
![Schijven aan gekoppelde portal controleren](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)
### <a name="configure-the-disks-to-be-encrypted"></a>De schijven configureren om te worden versleuteld
Deze configuratie wordt gedaan dat het besturingssysteem niveau, de bijbehorende schijven zijn geconfigureerd voor een traditionele ADE-encryptie:

Filesystems worden gemaakt op de top van de schijven.

Er worden tijdelijke bevestigingspunten gemaakt om de bestandssystemen te monteren.

De Filesystems zijn geconfigureerd op /etc/fstab om te worden gemonteerd op het opstarten.

Controleer de apparaatletter die aan de nieuwe schijven is toegewezen, in dit voorbeeld gebruiken we vier gegevensschijven

```bash
lsblk 
```
![Schijven aan gekoppeld besturingssysteem controleren](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-filesystem-on-top-of-each-disk"></a>Maak een bestandssysteem bovenop elke schijf.
Deze opdracht verlegt een ext4 filesystem creatie op elke schijf gedefinieerd op het "in" deel van de "voor" cyclus.
```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Controleer schijven bijgevoegd](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png) os Zoek de UUID van de bestandssystemen die onlangs zijn gemaakt, maak een tijdelijke map om het te monteren, voeg de bijbehorende vermeldingen toe op /etc/fstab en monteer alle bestandssystemen.

Deze opdracht wordt ook herhaald op elke schijf die is gedefinieerd op het "in" deel van de "voor"-cyclus:
```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 
### <a name="verify-the-disks-are-mounted-properly"></a>Controleer of de schijven goed zijn gemonteerd:
```bash
lsblk
```
![Controleer temp bestandssystemen](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png) gemonteerd en geconfigureerd:
```bash
cat /etc/fstab
```
![Controleer fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)
### <a name="encrypt-the-data-disks"></a>De gegevensschijven versleutelen:
PowerShell met KEK:
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
CLI met KEK:
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
![Controleer encryptie](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png) ps CLI:
```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Encryptie CLI](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png) Portal ![controleren:](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png) controleer versleuteling OS-niveau:
```bash
lsblk
```
![Encryptie CLI controleren](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

De extensie voegt de bestandssystemen toe aan "/var/lib/azure_disk_encryption_config/azure_crypt_mount" (een oude versleuteling) of wordt toegevoegd aan "/etc/crypttab" (nieuwe versleutelingen).

Wijzig geen van deze bestanden.

Dit bestand gaat zorgen voor het activeren van deze schijven tijdens het opstartproces, zodat ze later kunnen worden gebruikt door LVM of RAID. 

Maak je geen zorgen over de mount punten op dit bestand, als ADE verliest de mogelijkheid om de schijven gemonteerd als een normaal bestandssysteem nadat we een fysiek volume of een raid-apparaat te maken op de top van die versleutelde apparaten (die zich te ontdoen van het bestandssysteem formaat dat we gebruikten tijdens de voorbereidingsproces).
### <a name="remove-the-temp-folders-and-temp-fstab-entries"></a>De tijdelijke mappen en tijdelijke fstab-items verwijderen
U ontkoppelt de bestandssystemen op de schijven die worden gebruikt als onderdeel van LVM
```bash
for disk in c d e f; do umount /tempdata${disk}; done
```
En verwijder de /etc/fstab items:
```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Controleer of de schijven niet zijn gemonteerd en of de vermeldingen op /etc/fstab zijn verwijderd
```bash
lsblk
```
![Controleer tijdelijke bestandssystemen](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png) ongemonteerd en geconfigureerd:
```bash
cat /etc/fstab
```
![Controleer temp fstab-items worden verwijderd](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)
## <a name="for-lvm-on-crypt"></a>Voor LVM-on-crypt
Nu de onderliggende schijven zijn versleuteld, u doorgaan met het maken van de LVM-structuren.

In plaats van de apparaatnaam te gebruiken, gebruikt u de /dev/mapperpaden voor elk van de schijven om een fysiek volume te maken (op de cryptelaag bovenop de schijf niet op de schijf zelf).
### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>LVM bovenop de versleutelde lagen configureren
#### <a name="create-the-physical-volumes"></a>De fysieke volumes maken
Je krijgt een waarschuwing met de vraag of het ok is om de handtekening van het bestandssysteem uit te wissen. 

U doorgaan met het invoeren van 'y' of gebruik maken van de echo y zoals getoond:
```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![pvcreate](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)
>[!NOTE] 
>De /dev/mapper/device namen hier moeten worden vervangen voor uw werkelijke waarden op basis van de output van lsblk.
#### <a name="verify-the-physical-volumes-information"></a>De gegevens over de fysieke volumes verifiëren
```bash
pvs
```
![fysieke volumes controleren 1](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)
#### <a name="create-the-volume-group"></a>De volumegroep maken
De VG maken met dezelfde apparaten die al zijn geïnitialiseerd
```bash
vgcreate vgdata /dev/mapper/
```
### <a name="check-the-volume-group-information"></a>De informatie over de volumegroep controleren
```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![fysieke volumes 2 controleren](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)
#### <a name="create-logical-volumes"></a>Logische volumes maken
```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 
#### <a name="check-the-logical-volumes-created"></a>Controleer de logische volumes die zijn gemaakt
```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![controleren lvs](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)
#### <a name="create-filesystems-on-top-of-the-logical-volumes-structures"></a>Bestandssystemen maken bovenop de logische structuur(en)
```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```
#### <a name="create-the-mount-points-for-the-new-filesystems"></a>Maak de bevestigingspunten voor de nieuwe bestandssystemen
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
#### <a name="verify-that-the-new-filesystems-are-mounted"></a>Controleren of de nieuwe bestandssystemen zijn gemonteerd
```bash
lsblk -fs
df -h
```
![logische volumes](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png) controleren Op deze variant van lsblk vermelden we de apparaten met de afhankelijkheden van omgekeerde volgorde, deze optie helpt bij het identificeren van de apparaten gegroepeerd op het logische volume in plaats van de oorspronkelijke /dev/sd[schijf] apparaatnamen.

Belangrijk: Zorg ervoor dat de optie 'nofail' wordt toegevoegd aan de opties voor bevestigingspunten van de LVM-volumes die bovenop een ade-versleuteld apparaat zijn gemaakt. Is belangrijk om te voorkomen dat het besturingssysteem vast komt te zitten tijdens het opstartproces (of in de onderhoudsmodus). 

De versleutelde schijf wordt ontgrendeld aan het einde van het opstartproces, de LVM-volumes en bestandssystemen worden automatisch gemonteerd.

Als de nofail-optie niet wordt gebruikt, komt het besturingssysteem nooit in het stadium waarin ADE wordt gestart en worden de gegevensschijf(en) ontgrendeld en gemonteerd.

U testen opnieuw opstarten van de VM en het valideren van de bestandssystemen worden ook automatisch gemonteerd na het opstarten. 

Houd er rekening mee dat dit proces enkele minuten kan duren, afhankelijk van het aantal bestandssystemen en de
#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Start de VM opnieuw op en verifieer na opnieuw opstarten
```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="for-raid-on-crypt"></a>Voor RAID-on-Crypt
Nu de onderliggende schijven zijn versleuteld, u doorgaan met het maken van de RAID-structuren, hetzelfde als LVM, in plaats van de naam van het apparaat te gebruiken, de /dev/mapper-paden voor elk van de schijven te gebruiken.

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
![mdadm maken](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)
>[!NOTE] 
>De /dev/mapper/device namen hier moeten worden vervangen voor uw werkelijke waarden op basis van de output van lsblk.
#### <a name="checkmonitor-the-raid-creation"></a>Controleer/monitor de RAID-creatie:
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![check mdadm](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)
#### <a name="create-a-filesystem-on-top-of-the-new-raid-device"></a>Maak een bestandssysteem bovenop het nieuwe Raid-apparaat:
```bash
mkfs.ext4 /dev/md10
```
Maak een nieuw mountpoint voor het bestandssysteem, voeg het nieuwe bestandssysteem toe aan /etc/fstab en monteer het
```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```
Controleren of de nieuwe bestandssystemen zijn gemonteerd
```bash
lsblk -fs
df -h
```
![check mdadm](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Belangrijk: Zorg ervoor dat de optie 'nofail' wordt toegevoegd aan de opties voor het aanzetten van de RAID-volumes die bovenop een ade-versleuteld apparaat zijn gemaakt. 

Is zeer belangrijk om te voorkomen dat het OS vast komt te zitten tijdens het opstartproces (of in de onderhoudsmodus). 

De versleutelde schijf wordt ontgrendeld aan het einde van het opstartproces en de RAID-volumes en bestandssystemen worden automatisch gemonteerd totdat ze door ADE worden ontgrendeld, als de nofail-optie niet wordt gebruikt.

Het BE zal nooit in het stadium komen waar ADE wordt gestart, en de gegevensschijven worden ontgrendeld en gemonteerd.

U testen opnieuw opstarten van de VM en het valideren van de bestandssystemen worden ook automatisch gemonteerd na het opstarten. Houd er rekening mee dat dit proces enkele minuten kan duren, afhankelijk van het aantal bestandssystemen en de
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

