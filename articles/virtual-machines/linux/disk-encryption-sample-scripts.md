---
title: Voorbeeldscripts voor Azure Disk Encryption
description: Dit artikel is de bijlage voor Microsoft Azure Disk Encryption voor Linux VM's.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: c98da4b41da183f56d80fad1e8c01706d1cfcf23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970513"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Voorbeeldscripts voor Azure Disk Encryption 

In dit artikel vindt u voorbeeldscripts voor het voorbereiden van vooraf versleutelde VHD's en andere taken.

 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Voorbeeld van PowerShell-scripts voor Azure Disk Encryption 

- **Alle versleutelde VM's in uw abonnement weergeven**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Alle geheimen van schijfversleuteling die worden gebruikt voor het versleutelen van VM's in een sleutelkluis weergeven** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Het PowerShell-script voor Azure Disk Encryption gebruiken
Als u al bekend bent met de vereisten voor Azure Disk Encryption, u het [PowerShell-script voor Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 )gebruiken. Zie de [Vm Quickstart versleutelen](disk-encryption-powershell-quickstart.md)voor een voorbeeld van het gebruik van dit PowerShell-script. U de opmerkingen uit een gedeelte van het script verwijderen, te beginnen bij regel 211, om alle schijven voor bestaande VM's in een bestaande brongroep te versleutelen. 

In de volgende tabel ziet u welke parameters kunnen worden gebruikt in het PowerShell-script: 


|Parameter|Beschrijving|Verplicht?|
|------|------|------|
|$resourceGroupName| Naam van de resourcegroep waartoe de KeyVault behoort.  Er wordt een nieuwe resourcegroep met deze naam gemaakt als deze niet bestaat.| True|
|$keyVaultName|Naam van de KeyVault waarin encryptiesleutels moeten worden geplaatst. Er wordt een nieuwe kluis met deze naam gemaakt als deze niet bestaat.| True|
|$location|Locatie van de KeyVault. Zorg ervoor dat de KeyVault- en VM's die moeten worden versleuteld, zich op dezelfde locatie bevinden. Haal een locatielijst op met `Get-AzLocation`.|True|
|$subscriptionId|Id van het Te gebruiken Azure-abonnement.  U kunt uw abonnements-ID ophalen met `Get-AzSubscription`.|True|
|$aadAppName|Naam van de Azure AD-toepassing die wordt gebruikt om geheimen naar KeyVault te schrijven. Als er nog geen toepassing met deze naam bestaat, wordt deze aangemaakt. Als deze app al bestaat, geeft u de parameter aadClientSecret door aan het script.|False|
|$aadClientSecret|Clientgeheim van de Azure AD-toepassing die eerder is gemaakt.|False|
|$keyEncryptionKeyName|Naam van optionele sleutelversleutelingssleutel in KeyVault. Er wordt een nieuwe sleutel met deze naam gemaakt als deze niet bestaat.|False|


### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>VM's versleutelen of decoderen zonder een Azure AD-app

- [Schijfversleuteling inschakelen op een bestaande of draaiende Linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Versleuteling uitschakelen op een draaiende Linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Het uitschakelen van versleuteling is alleen toegestaan op gegevensvolumes voor Linux VM's.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>VM's versleutelen of decoderen met een Azure AD-app (vorige release) 
 
- [Schijfversleuteling inschakelen op een bestaande of draaiende Linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Versleuteling uitschakelen op een draaiende Linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Het uitschakelen van versleuteling is alleen toegestaan op gegevensvolumes voor Linux VM's. 


- [Een nieuwe versleutelde beheerde schijf maken vanaf een vooraf versleutelde VHD/storage blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Hiermee maakt u een nieuwe versleutelde beheerde schijf op voorwaarde dat een vooraf versleutelde VHD en de bijbehorende versleutelingsinstellingen





## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Een OS-station versleutelen op een draaiende Linux-vm

### <a name="prerequisites-for-os-disk-encryption"></a>Vereisten voor os-schijfversleuteling

* De VM moet een distributie gebruiken die compatibel is met OS-schijfversleuteling zoals vermeld in de [ondersteunde besturingssystemen voor Azure Disk Encryption](disk-encryption-overview.md#supported-vm-sizes) 
* De VM moet worden gemaakt op de Marketplace-afbeelding in Azure Resource Manager.
* Azure VM met minimaal 4 GB RAM (aanbevolen grootte is 7 GB).
* (Voor RHEL en CentOS) Selinux uitschakelen. Zie "4.4.2 voor het uitschakelen van SELinux. SELinux uitschakelen" in de [SELinux Gebruikers- en Beheerdershandleiding](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) op de VM.
* Nadat u SELinux hebt uitgeschakeld, start u de VM ten minste één keer opnieuw op.

### <a name="steps"></a>Stappen
1. Maak een VM met een van de eerder opgegeven distributies.

   Voor CentOS 7.2 wordt os-schijfversleuteling ondersteund via een speciale afbeelding. Als u deze afbeelding wilt gebruiken, geeft u '7.2n' op als de SKU wanneer u de vm maakt:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Configureer de VM op basis van uw behoeften. Als u alle schijven (OS + data) gaat versleutelen, moeten de gegevensstations worden opgegeven en gemonteerd vanaf /etc/fstab.

   > [!NOTE]
   > Gebruik UUID=... om gegevensstations op te geven in /etc/fstab in plaats van de naam van het blokapparaat op te geven (bijvoorbeeld /dev/sdb1). Tijdens versleuteling verandert de volgorde van stations op de VM. Als uw VM afhankelijk is van een specifieke volgorde van blokapparaten, wordt deze niet gemonteerd na versleuteling.

3. Meld u af voor de SSH-sessies.

4. Als u het besturingssysteem wilt versleutelen, geeft u volumeType als **Alles** of **BE op** wanneer u versleuteling inschakelt.

   > [!NOTE]
   > Alle processen voor gebruikersruimte die `systemd` niet worden uitgevoerd `SIGKILL`als services, moeten worden gedood met een . Start de VM opnieuw op. Wanneer u os-schijfversleuteling inschakelt op een draaiende VM, plant u vm-downtime.

5. Controleer periodiek de voortgang van versleuteling met behulp van de instructies in de [volgende sectie](#monitoring-os-encryption-progress).

6. Nadat get-AzVmDiskEncryptionStatus "VMRestartPending" wordt weergegeven, start u uw VM opnieuw op door u aan te melden of door de portal, PowerShell of CLI te gebruiken.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Voordat u opnieuw wordt opgestart, raden we u aan [de opstartdiagnose](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) van de VM op te slaan.

## <a name="monitoring-os-encryption-progress"></a>Voortgang van de versleuteling van besturingssysteem controleren
U de voortgang van de versleuteling van besturingssysteemop drie manieren volgen:

* Gebruik `Get-AzVmDiskEncryptionStatus` de cmdlet en inspecteer het veld ProgressMessage:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Nadat de VM "OS disk encryption started" heeft bereikt, duurt het ongeveer 40 tot 50 minuten op een vm met premiumopslag.

  Vanwege [probleem #388](https://github.com/Azure/WALinuxAgent/issues/388) in WALinuxAgent, `OsVolumeEncrypted` en `DataVolumesEncrypted` verschijnen als `Unknown` in sommige distributies. Met WALinuxAgent versie 2.1.5 en hoger wordt dit probleem automatisch opgelost. Als u `Unknown` in de uitvoer ziet, u de status van schijfversleuteling verifiëren met behulp van de Azure Resource Explorer.

  Ga naar [Azure Resource Explorer](https://resources.azure.com/)en vouw deze hiërarchie vervolgens uit in het selectiepaneel aan de linkerkant:

  ~~~~
  |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
  ~~~~                

  Schuif in de InstanceView omlaag om de versleutelingsstatus van uw schijven te bekijken.

  ![VM-instantieweergave](./media/disk-encryption/vm-instanceview.png)

* Kijk naar [boot diagnostiek](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Berichten van de ADE-extensie moeten `[AzureDiskEncryption]`vooraf worden bevestigd met .

* Meld u aan bij de VM via SSH en ontvang het extensielogboek van:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  We raden u aan zich niet aan te melden bij de VM terwijl de OS-versleuteling aan de gang is. Kopieer de logboeken alleen wanneer de andere twee methoden zijn mislukt.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Een vooraf versleutelde Linux VHD voorbereiden
De voorbereiding voor vooraf versleutelde VHD's kan variëren afhankelijk van de distributie. Voorbeelden voor het voorbereiden van Ubuntu 16, openSUSE 13.2 en CentOS 7 zijn beschikbaar. 

### <a name="ubuntu-16"></a>Ubuntu 16
Versleuteling configureren tijdens de distributie-installatie door de volgende stappen uit te voeren:

1. Selecteer **Versleutelde volumes configureren** wanneer u de schijven partitiet.

   ![Ubuntu 16.04 Setup - Versleutelde volumes configureren](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Maak een aparte opstartschijf, die niet versleuteld mag worden. Versleutel je rootdrive.

   ![Ubuntu 16.04 Setup - Selecteer apparaten om te versleutelen](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Geef een wachtwoordzin. Dit is de wachtwoordzin die je hebt geüpload naar de sleutelkluis.

   ![Ubuntu 16.04 Setup - Passphrase](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Klaar met partitioneren.

   ![Ubuntu 16.04 Setup - Partitionering voltooien](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Wanneer u de virtuele machine opstart en om een wachtwoordzin wordt gevraagd, gebruikt u de wachtwoordzin die u in stap 3 hebt opgegeven.

   ![Ubuntu 16.04 Setup - Passphrase bieden bij het opstarten](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Bereid de VM voor op het uploaden naar Azure met behulp van [deze instructies.](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/) Voer de laatste stap (deprovisioning the VM) nog niet uit.

Versleuteling configureren om met Azure te werken door de volgende stappen uit te voeren:

1. Maak een bestand onder /usr/local/sbin/azure_crypt_key.sh, met de inhoud in het volgende script. Let op de KeyFileName, omdat het de wachtwoordzin bestandsnaam gebruikt door Azure.

    ```bash
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
   ```

2. Verander de crypte config in */etc/crypttab*. Dit ziet er als volgt uit:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. Uitvoerbare machtigingen toevoegen aan het script:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Bewerk */etc/initramfs-tools/modules* door regels toe te voegen:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Voer `update-initramfs -u -k all` uit om de initramfs bij te werken om de `keyscript` effect te laten uitvoeren.

7. Nu u de VM deprovisioneren.

   ![Ubuntu 16.04 Setup - update-initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Ga verder naar de volgende stap en upload uw VHD naar Azure.

### <a name="opensuse-132"></a>openSUSE 13.2
Ga als volgt te werk om versleuteling tijdens de distributie-installatie te configureren:
1. Wanneer u de schijven partitioneert, selecteert u **Volumegroep versleutelen**en voert u een wachtwoord in. Dit is het wachtwoord dat u uploadt naar uw sleutelkluis.

   ![openSUSE 13.2-installatie - Volumegroep versleutelen](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Start de VM op met uw wachtwoord.

   ![openSUSE 13.2 Setup - Passphrase bieden bij het opstarten](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. Bereid de VM voor op het uploaden naar Azure door de instructies te volgen in [Een SLES voorbereiden of virtuele machine openen voor Azure.](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131) Voer de laatste stap (deprovisioning the VM) nog niet uit.

Ga als volgt te werk om versleuteling te configureren voor het werken met Azure:
1. Bewerk de /etc/dracut.conf en voeg de volgende regel toe:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Reageer op deze regels tegen het einde van het bestand /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Sluit de volgende regel toe aan het begin van het bestand /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   En verander alle gebeurtenissen van:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   in:
   ```bash
    if [ 1 ]; then
   ```
4. Edit /usr/lib/dracut/modules.d/90crypt/crypt-ask.sh en toevoegen aan "# Open LUKS device":

    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Voer `/usr/sbin/dracut -f -v` uit om de initrd bij te werken.

6. Nu u de VM deprovisioneneren en uw VHD uploaden naar Azure.

### <a name="centos-7-and-rhel-81"></a>CentOS 7 en RHEL 8.1

Ga als volgt te werk om versleuteling tijdens de distributie-installatie te configureren:
1. Selecteer **Mijn gegevens versleutelen** wanneer u schijven partitiet.

   ![CentOS 7 Setup -Installatiebestemming](./media/disk-encryption/centos-encrypt-fig1.png)

2. Controleer of **Versleutelen** is geselecteerd voor rootpartitie.

   ![CentOS 7 Setup -Selecteer versleutelen voor rootpartitie](./media/disk-encryption/centos-encrypt-fig2.png)

3. Geef een wachtwoordzin. Dit is de wachtwoordzin die je uploadt naar je sleutelkluis.

   ![CentOS 7 Setup - passzin](./media/disk-encryption/centos-encrypt-fig3.png)

4. Wanneer u de virtuele machine opstart en om een wachtwoordzin wordt gevraagd, gebruikt u de wachtwoordzin die u in stap 3 hebt opgegeven.

   ![CentOS 7 Setup - Voer wachtwoordzin in bij het opstarten](./media/disk-encryption/centos-encrypt-fig4.png)

5. Bereid de VM voor op het uploaden naar Azure met behulp van de instructies 'CentOS 7.0+' in [Een op CentOS gebaseerde virtuele machine voorbereiden voor Azure.](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70) Voer de laatste stap (deprovisioning the VM) nog niet uit.

6. Nu u de VM deprovisioneneren en uw VHD uploaden naar Azure.

Ga als volgt te werk om versleuteling te configureren voor het werken met Azure:

1. Bewerk de /etc/dracut.conf en voeg de volgende regel toe:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Reageer op deze regels tegen het einde van het bestand /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Sluit de volgende regel toe aan het begin van het bestand /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   En verander alle gebeurtenissen van:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   tot
   ```bash
    if [ 1 ]; then
   ```
4. Bewerk /usr/lib/dracut/modules.d/90crypt/crypt-ask.sh en sluit het volgende toe na het "# Open LUKS-apparaat":
    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Voer de "/usr/sbin/dracut -f-v" uit om de initrd bij te werken.

    ![CentOS 7 Setup - run /usr/sbin/dracut -f -v](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Versleutelde VHD uploaden naar een Azure-opslagaccount
Nadat DM-Crypt-versleuteling is ingeschakeld, moet de lokale versleutelde VHD worden geüpload naar uw opslagaccount.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Upload het geheim voor de vooraf versleutelde VM naar uw sleutelkluis
Wanneer u een Azure AD-app (vorige release) versleutelt, moet het schijfversleutelingsgeheim dat u eerder hebt verkregen, als geheim in uw sleutelkluis worden geüpload. De sleutelkluis moet schijfversleuteling en machtigingen hebben ingeschakeld voor uw Azure AD-client.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Schijfversleutelingsgeheim niet versleuteld met een KEK
Als u het geheim in uw sleutelkluis wilt instellen, gebruikt u [Set-AzKeyVaultSecret.](/powershell/module/az.keyvault/set-azkeyvaultsecret) De wachtwoordzin wordt gecodeerd als een base64-tekenreeks en vervolgens geüpload naar de sleutelkluis. Zorg er bovendien voor dat de volgende tags worden ingesteld wanneer u het geheim in de sleutelkluis maakt.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Gebruik `$secretUrl` de in de volgende stap voor [het koppelen van de OS-schijf zonder kek te gebruiken.](#without-using-a-kek)

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Schijfversleuteling geheim versleuteld met een KEK
Voordat u het geheim uploadt naar de sleutelkluis, u het optioneel versleutelen met behulp van een sleutelversleutelingssleutel. Gebruik de wrap [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) om eerst het geheim te versleutelen met behulp van de sleutelversleutelingssleutel. De uitvoer van deze wrap-bewerking is een base64 URL gecodeerde tekenreeks, [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) die u vervolgens als geheim uploaden met behulp van de cmdlet.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Gebruik `$KeyEncryptionKey` `$secretUrl` en in de volgende stap voor [het koppelen van de OS-schijf met BEHULP van KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Een geheime URL opgeven wanneer u een os-schijf koppelt

###  <a name="without-using-a-kek"></a>Zonder gebruik van een KEK
Terwijl u de OS-schijf bevestigt, `$secretUrl`moet u slagen voor . De URL is gegenereerd in de sectie 'Schijfversleutelingsgeheim niet versleuteld met een KEK'-sectie.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Een KEK gebruiken
Wanneer u de OS-schijf bevestigt, passeert `$KeyEncryptionKey` en `$secretUrl`. De URL is gegenereerd in de sectie 'Schijfversleuteling geheim versleuteld met een KEK'.The URL is generated in the "Disk encryption secret encrypted with a KEK" sectie.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
