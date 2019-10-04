---
title: Voorbeeld scripts Azure Disk Encryption
description: Dit artikel is de bijlage voor Microsoft Azure schijf versleuteling voor Linux-Vm's.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 088ca5c20b0681cdd36da1b8a187873399aa32c6
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828462"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Voorbeeld scripts Azure Disk Encryption 

Dit artikel bevat voorbeeld scripts voor het voorbereiden van vooraf versleutelde Vhd's en andere taken.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>PowerShell-voorbeeldscripts voor Azure Disk Encryption 

- **Lijst met alle versleutelde virtuele machines in uw abonnement**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Lijst van alle schijf encryption geheimen die worden gebruikt voor het versleutelen van virtuele machines in een key vault** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Het Power shell-script voor de Azure Disk Encryption vereisten gebruiken
Als u al bekend met de vereisten voor Azure Disk Encryption bent, kunt u de [PowerShell script met vereisten voor Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Zie voor een voorbeeld van het gebruik van dit PowerShell-script, de [een snelstartgids van Virtual machines versleutelen](disk-encryption-powershell-quickstart.md). U kunt de opmerkingen verwijderen uit een gedeelte van het script, begint bij regel 211, voor het versleutelen van alle schijven voor bestaande virtuele machines in een bestaande resourcegroep. 

De volgende tabel ziet u welke parameters kunnen worden gebruikt in het PowerShell-script: 


|Parameter|Description|Ingevuld?|
|------|------|------|
|$resourceGroupName| De naam van de resourcegroep waaraan de KeyVault behoort.  Een nieuwe resourcegroep met deze naam wordt gemaakt als deze niet bestaat.| True|
|$keyVaultName|De naam van de Sleutelkluis waar versleutelingssleutels in sleutels moeten worden geplaatst. Een nieuwe kluis met deze naam wordt gemaakt als deze niet bestaat.| True|
|$location|Locatie van de Key Vault. Zorg ervoor dat de Key Vault en de virtuele machines moeten worden versleuteld zijn op dezelfde locatie. Haal een locatielijst op met `Get-AzLocation`.|True|
|$subscriptionId|Id van het Azure-abonnement moet worden gebruikt.  U kunt uw abonnements-ID ophalen met `Get-AzSubscription`.|True|
|$aadAppName|De naam van de Azure AD-toepassing die wordt gebruikt voor het schrijven van geheimen naar Key Vault. Als er nog geen toepassing met deze naam bestaat, wordt deze aangemaakt. Als deze app al bestaat, moet u aadClientSecret parameter doorgeven aan het script.|False|
|$aadClientSecret|Clientgeheim van de Azure AD-toepassing die eerder is gemaakt.|False|
|$keyEncryptionKeyName|De naam van de optionele sleutel van versleutelingssleutel in Key Vault. Een nieuwe sleutel met deze naam wordt gemaakt als deze niet bestaat.|False|


### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Versleutelen of ontsleutelen van virtuele machines zonder een Azure AD-app

- [Schijf versleuteling inschakelen op een bestaande of actieve virtuele Linux-machine](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Schakel versleuteling uit op een actieve Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Als u versleuteling uitschakelt is alleen toegestaan op gegevensvolumes voor virtuele Linux-machines.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Versleutelen of ontsleutelen van virtuele machines met een Azure AD-app (vorige versie) 
 
- [Schijf versleuteling inschakelen op een bestaande of actieve virtuele Linux-machine](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Schakel versleuteling uit op een actieve Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Als u versleuteling uitschakelt is alleen toegestaan op gegevensvolumes voor virtuele Linux-machines. 


- [Maak een nieuwe versleutelde beheerde schijf van een vooraf gecodeerde VHD/opslag-blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Maakt u een nieuwe versleutelde beheerde schijf die een vooraf gecodeerde VHD en de bijbehorende instellingen voor codering





## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Een OS-station versleutelen op een actieve Linux-VM

### <a name="prerequisites-for-os-disk-encryption"></a>Vereisten voor versleuteling van de OS-schijf

* De virtuele machine moet gebruikmaken van een distributie die compatibel is met de besturingssysteem schijf versleuteling, zoals vermeld in de [Azure Disk Encryption ondersteunde besturings systemen](disk-encryption-overview.md#supported-vm-sizes) 
* De virtuele machine moet worden gemaakt via de Marketplace-installatiekopie in Azure Resource Manager.
* Azure-VM met ten minste 4 GB aan RAM-geheugen (aanbevolen grootte is 7 GB).
* (Voor RHEL en CentOS) SELinux uitschakelen. Als u wilt uitschakelen SELinux, Zie "4.4.2. Uitschakelen van SELinux' in de [SELinux gebruikers en beheerders van handleiding](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) op de virtuele machine.
* Nadat u SELinux hebt uitgeschakeld, start u de virtuele machine ten minste één keer opnieuw.

### <a name="steps"></a>Stappen
1. Een virtuele machine maken met behulp van een van de distributies die eerder zijn opgegeven.

   OS-schijfversleuteling wordt ondersteund voor 7.2 CentOS, via een speciale installatiekopie. Geef voor het gebruik van deze installatiekopie, "7.2n' als de SKU bij het maken van de virtuele machine:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Configureer de virtuele machine op basis van uw behoeften. Als u schijven voor het versleutelen van alle de (OS + gegevens), de gegevensstations moeten worden opgegeven en koppelbaar uit/etc/fstab gaan.

   > [!NOTE]
   > Gebruik de UUID =... om op te geven gegevensstations in/etc/fstab in plaats van de apparaatnaam blokkeren (bijvoorbeeld/dev/sdb1) op te geven. Tijdens het versleutelen wijzigt de volgorde van de stations op de virtuele machine. Als uw virtuele machine is afhankelijk van een specifieke volgorde van de blokapparaten, mislukt dit ze na versleuteling te koppelen.

3. Afmelden bij de SSH-sessies.

4. Geef voor het versleutelen van het besturingssysteem, volumeType als **alle** of **OS** wanneer u versleuteling inschakelt.

   > [!NOTE]
   > Alle gebruikersruimte processen die niet worden uitgevoerd als `systemd` services moeten worden afgesloten met een `SIGKILL`. Start opnieuw op de virtuele machine. Wanneer u versleuteling van OS-schijf op een actieve virtuele machine inschakelt, plan dan op downtime van VM's.

5. Controleer regelmatig de voortgang van versleuteling met behulp van de instructies in de [volgende sectie](#monitoring-os-encryption-progress).

6. Na Get-AzVmDiskEncryptionStatus wordt "VMRestartPending" weer gegeven, start u de VM opnieuw op door u aan te melden of door de portal, Power shell of CLI te gebruiken.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Voordat u het opnieuw opstarten, wordt aangeraden dat u opslaat [diagnostische gegevens over opstarten](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) van de virtuele machine.

## <a name="monitoring-os-encryption-progress"></a>Voortgang van de OS-versleuteling controleren
U kunt de OS-versleuteling wordt uitgevoerd op drie manieren controleren:

* Gebruik de `Get-AzVmDiskEncryptionStatus` cmdlet en het veld ProgressMessage inspecteren:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Wanneer de virtuele machine bereikt 'OS-schijf versleuteling aan de slag', duurt het ongeveer 40 tot 50 minuten back virtuele machine op een Premium-opslag.

  Vanwege [uitgeven #388](https://github.com/Azure/WALinuxAgent/issues/388) in WALinuxAgent, `OsVolumeEncrypted` en `DataVolumesEncrypted` weergegeven als `Unknown` in bepaalde distributies. Met versie 2.1.5 WALinuxAgent en later, dit probleem automatisch is opgelost. Als u ziet `Unknown` in de uitvoer kunt u schijfversleuteling status controleren met behulp van Azure Resource Explorer.

  Ga naar [Azure Resource Explorer](https://resources.azure.com/), en vouw vervolgens deze hiërarchie in het deelvenster selectie op links:

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

  Schuif omlaag om te zien van de coderingsstatus van uw schijven in de InstanceView.

  ![Instantieweergave van virtuele machine](./media/disk-encryption/vm-instanceview.png)

* Bekijk [diagnostische gegevens over opstarten](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Berichten van de extensie ADE moeten worden voorafgegaan door `[AzureDiskEncryption]`.

* Meld u aan met de virtuele machine via SSH en ophalen van het extensielogboek uit:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  We raden aan dat u niet aanmelden bij de virtuele machine terwijl OS-versleuteling uitgevoerd wordt. Kopieer de logboeken alleen als de andere twee methoden zijn mislukt.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Een vooraf versleutelde Linux-VHD voorbereiden
De voorbereiding voor vooraf gecodeerde VHD's kan variëren afhankelijk van de distributie. Voor beelden van het voorbereiden van Ubuntu 16, openSUSE 13,2 en CentOS 7 zijn beschikbaar. 

### <a name="ubuntu-16"></a>Ubuntu 16
Versleuteling tijdens de installatie van de distributie configureren met de volgende stappen:

1. Selecteer **configureren van versleutelde volumes** wanneer u de schijven partitioneren.

   ![Ubuntu 16.04 instellen - versleutelde volumes configureren](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Maak een afzonderlijke opstartstation, die niet moet worden versleuteld. Codeer uw basisstation.

   ![Ubuntu 16.04-installatie - optie apparaten voor het versleutelen van](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Geef een wachtwoordzin. Dit is de wachtwoordzin op die u hebt geüpload naar de key vault.

   ![Ubuntu 16.04 instellen - wachtwoordzin opgeven](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Voltooi partitioneren.

   ![Ubuntu 16.04 instellen - partitionering voltooien](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Wanneer u de virtuele machine worden opgestart en wordt gevraagd een wachtwoordzin, gebruikt u de wachtwoordzin die u hebt opgegeven in stap 3.

   ![Ubuntu 16.04 instellen - wachtwoordzin opgeven bij het opstarten](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. De virtuele machine voorbereiden voor het uploaden naar Azure met [deze instructies](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). De laatste stap (opheffen van inrichting van de virtuele machine) worden niet uitgevoerd nog.

Configureren van versleuteling voor Azure door de volgende stappen:

1. Maak een bestand onder /usr/local/sbin/azure_crypt_key.sh, met de inhoud in het volgende script. Let op de KeyFileName, omdat het is de bestandsnaam van de wachtwoordzin die wordt gebruikt door Azure.

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

2. Wijzigen van de configuratie van de crypt in */etc/crypttab*. Dit ziet er als volgt uit:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. Uitvoerbare machtigingen toevoegen aan het script:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Bewerken */etc/initramfs-tools/modules* door regels toe te voegen:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Voer `update-initramfs -u -k all` om bij te werken van de initramfs waarmee de `keyscript` van kracht.

7. Nu kunt u de virtuele machine inrichting.

   ![Ubuntu 16.04 Setup - update-initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Ga door met de volgende stap en uw VHD uploaden naar Azure.

### <a name="opensuse-132"></a>openSUSE 13.2
Voer de volgende stappen uit voor het configureren van versleuteling tijdens de installatie van softwaredistributie:
1. Wanneer u de schijven partitioneren, selecteert u **Volumegroep versleutelen**, en voer een wachtwoord. Dit is het wachtwoord die u naar de sleutelkluis uploaden gaat.

   ![openSUSE 13.2 Setup - Volumegroep versleutelen](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Start de virtuele machine met behulp van uw wachtwoord.

   ![openSUSE 13.2 instellen - wachtwoordzin opgeven bij het opstarten](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. De virtuele machine voorbereiden voor het uploaden naar Azure door de instructies in [een SLES of opensuse gebaseerde virtuele machine voor Azure voorbereiden](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). De laatste stap (opheffen van inrichting van de virtuele machine) worden niet uitgevoerd nog.

Voor het configureren van versleuteling met Azure werkt, moet u de volgende stappen uitvoeren:
1. Bewerk de /etc/dracut.conf en voeg de volgende regel toe:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Opmerkingen bij de volgende regels aan het einde van het bestand /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
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

3. De volgende regel aan het begin van het bestand /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh toevoegen:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   En wijzig alle instanties van:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   in:
   ```bash
    if [ 1 ]; then
   ```
4. Bewerk /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh en voegt deze toe aan '# Open LUKS apparaat':

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
5. Voer `/usr/sbin/dracut -f -v` om bij te werken van de initrd.

6. U kunt nu de inrichting van de virtuele machine ongedaan maken en uw VHD uploaden naar Azure.

### <a name="centos-7"></a>CentOS 7
Voer de volgende stappen uit voor het configureren van versleuteling tijdens de installatie van softwaredistributie:
1. Selecteer **mijn gegevens versleutelen** wanneer u schijven partitioneren.

   ![CentOS 7 Setup - installatie van doel](./media/disk-encryption/centos-encrypt-fig1.png)

2. Zorg ervoor dat **versleutelen** is geselecteerd als de hoofdpartitie.

   ![CentOS 7 Setup - selecteren voor hoofdpartitie versleutelen](./media/disk-encryption/centos-encrypt-fig2.png)

3. Geef een wachtwoordzin. Dit is de wachtwoordzin op die u naar de sleutelkluis uploaden gaat.

   ![Installatie van centOS 7 - wachtwoordzin opgeven](./media/disk-encryption/centos-encrypt-fig3.png)

4. Wanneer u de virtuele machine worden opgestart en wordt gevraagd een wachtwoordzin, gebruikt u de wachtwoordzin die u hebt opgegeven in stap 3.

   ![CentOS 7 instellen - wachtwoordzin invoeren op opstartstatus](./media/disk-encryption/centos-encrypt-fig4.png)

5. De virtuele machine voorbereiden voor het uploaden naar Azure met behulp van de instructies "CentOS 7.0 +" in [een CentOS gebaseerde virtuele machine voor Azure voorbereiden](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). De laatste stap (opheffen van inrichting van de virtuele machine) worden niet uitgevoerd nog.

6. U kunt nu de inrichting van de virtuele machine ongedaan maken en uw VHD uploaden naar Azure.

Voor het configureren van versleuteling met Azure werkt, moet u de volgende stappen uitvoeren:

1. Bewerk de /etc/dracut.conf en voeg de volgende regel toe:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Opmerkingen bij de volgende regels aan het einde van het bestand /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
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

3. De volgende regel aan het begin van het bestand /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh toevoegen:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   En wijzig alle instanties van:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   tot
   ```bash
    if [ 1 ]; then
   ```
4. Bewerk /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh en voegt u het volgende na het '# Open LUKS apparaat':
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
5. Voer de ' / usr/sbin/dracut - f - v ' om bij te werken van de initrd.

    ![Installatie van centOS 7 - /usr/sbin/dracut -f - v wordt uitgevoerd](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Versleutelde VHD uploaden naar een Azure Storage-account
Nadat DM-cryptografie versleuteling is ingeschakeld, moet de lokale versleutelde VHD worden geüpload naar uw opslag account.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Upload het geheim voor de vooraf versleutelde virtuele machine naar uw sleutel kluis
Wanneer u versleutelt met behulp van een Azure AD-app (vorige versie), moet het geheim voor versleuteling van de schijf die u eerder hebt verkregen worden geüpload als een geheim in uw key vault. De key vault moet schijfversleuteling en machtigingen die zijn ingeschakeld voor uw Azure AD-client bevatten.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Het geheim voor schijf versleuteling is niet versleuteld met een KEK
Als u het geheim in uw sleutel kluis wilt instellen, gebruikt u [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). De wachtwoordzin wordt gecodeerd als een base64-teken reeks en vervolgens geüpload naar de sleutel kluis. Bovendien moet u ervoor dat de volgende codes zijn ingesteld wanneer u het geheim in de key vault maakt.

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


Gebruik de `$secretUrl` in de volgende stap voor [de OS-schijf koppelen zonder KEK](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Het geheim voor schijf versleuteling is versleuteld met een KEK
Voordat u het geheim naar de key vault uploaden, kunt u deze desgewenst versleutelen met behulp van een sleutel van versleutelingssleutel. Gebruik de wrap [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) voor het eerst het geheim met behulp van de sleutel van versleutelingssleutel te versleutelen. De uitvoer van deze bewerking wrap is een tekenreeks met Base 64 URL-codering, die u vervolgens als een geheim uploaden met behulp van kunt de [ `Set-AzKeyVaultSecret` ](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet.

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

Gebruik `$KeyEncryptionKey` en `$secretUrl` in de volgende stap voor [Bezig met koppelen van de besturingssysteemschijf met behulp van de KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Geef een geheime URL op wanneer u een besturingssysteem schijf koppelt

###  <a name="without-using-a-kek"></a>Zonder gebruik te maken van een KEK
Terwijl u de besturingssysteemschijf koppelen bent, moet u doorgeven `$secretUrl`. De URL is gegenereerd in de sectie 'schijfversleuteling geheim niet versleuteld met een KEK-sleutel'.
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
Wanneer u de besturingssysteemschijf koppelen, `$KeyEncryptionKey` en `$secretUrl`. De URL is gegenereerd in de sectie 'Versleutelingsgeheim van schijf versleuteld met een KEK-sleutel'.
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
