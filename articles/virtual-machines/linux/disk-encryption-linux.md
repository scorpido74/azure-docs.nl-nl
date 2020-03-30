---
title: Azure Disk Encryption-scenario's voor Linux-VM's
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure Disk Encryption voor Linux VM's voor verschillende scenario's
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 6a1ea5e09dc70b2c523bf23b28f726cbe2c0818d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062675"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Azure Disk Encryption-scenario's voor Linux-VM's

Azure Disk Encryption maakt gebruik van de DM-Crypt-functie van Linux om volumeversleuteling te bieden voor het besturingssysteem en de gegevensschijven van Virtuele Azure-machines (VM's) en is geïntegreerd met Azure Key Vault om u te helpen de schijfversleutelingssleutels en -geheimen te beheren en te beheren. Zie Azure Disk Encryption [for Linux VM's voor](disk-encryption-overview.md)een overzicht van de service.

Er zijn veel scenario's voor schijfversleuteling en de stappen kunnen variëren afhankelijk van het scenario. De volgende secties behandelen de scenario's in meer detail voor Linux VM's.

U schijfversleuteling alleen toepassen op virtuele machines met [ondersteunde VM-formaten en besturingssystemen.](disk-encryption-overview.md#supported-vms-and-operating-systems) U moet ook aan de volgende voorwaarden voldoen:

- [Aanvullende vereisten voor VM's](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Netwerkvereisten](disk-encryption-overview.md#networking-requirements)
- [Vereisten voor opslag van versleutelingssleutels](disk-encryption-overview.md#encryption-key-storage-requirements)

In alle gevallen moet u [een momentopname maken](snapshot-copy-managed-disk.md) en/of een back-up maken voordat schijven worden versleuteld. Back-ups zorgen ervoor dat een hersteloptie mogelijk is als er een onverwachte fout optreedt tijdens versleuteling. VM's met beheerde schijven vereisen een back-up voordat versleuteling plaatsvindt. Zodra een back-up is gemaakt, u de [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) gebruiken om beheerde schijven te versleutelen door de parameter -skipVmBackup op te geven. Zie het artikel [Azure Backup](../../backup/backup-azure-vms-encryption.md) voor meer informatie over het maken van back-ups en het herstellen van versleutelde VM's. 

>[!WARNING]
> - Als u azure-schijfversleuteling eerder met Azure AD hebt gebruikt om een vm te versleutelen, moet u deze optie blijven gebruiken om uw vm te versleutelen. Zie [Azure Disk Encryption with Azure AD (vorige release)](disk-encryption-overview-aad.md) voor meer informatie. 
>
> - Bij het versleutelen van Linux-besturingssysteemvolumes moet de VM als niet beschikbaar worden beschouwd. We raden ten zeerste aan om SSH-aanmeldingen te voorkomen terwijl de versleuteling aan de gang is om te voorkomen dat er problemen worden geblokkeerd bij het blokkeren van geopende bestanden die tijdens het versleutelingsproces moeten worden geopend. Als u de voortgang wilt controleren, gebruikt u de cmdlet [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell of de [opdracht CLI-codering voor vm-versleuteling.](/cli/azure/vm/encryption#az-vm-encryption-show) Dit proces kan naar verwachting een paar uur duren voor een 30GB OS volume, plus extra tijd voor het versleutelen van gegevensvolumes. De versleutelingstijd van het gegevensvolume is evenredig aan de grootte en hoeveelheid van de gegevensvolumes, tenzij de versleutelingsindeling alle opties wordt gebruikt. 
> - Het uitschakelen van versleuteling op Linux VM's wordt alleen ondersteund voor gegevensvolumes. Het wordt niet ondersteund op gegevens of BE-volumes als het os-volume is versleuteld.  

## <a name="install-tools-and-connect-to-azure"></a>Hulpprogramma's installeren en verbinding maken met Azure

Azure Disk Encryption kan worden ingeschakeld en beheerd via de [Azure CLI](/cli/azure) en [Azure PowerShell.](/powershell/azure/new-azureps-module-az) Hiervoor moet u de hulpprogramma's lokaal installeren en verbinding maken met uw Azure-abonnement.

### <a name="azure-cli"></a>Azure-CLI

De [Azure CLI 2.0](/cli/azure) is een opdrachtregelhulpmiddel voor het beheer van Azure-resources. De CLI is ontworpen om gegevens flexibel op te vragen, langdurige bewerkingen te ondersteunen als niet-blokkerende processen en scripting eenvoudig te maken. U het lokaal installeren door de stappen in [Azure CLI installeren](/cli/azure/install-azure-cli?view=azure-cli-latest)te volgen.

 

Als [u zich wilt aanmelden bij uw Azure-account met de Azure CLI,](/cli/azure/authenticate-azure-cli)gebruikt u de opdracht [AZ-aanmelding.](/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli
az login
```

Als u een tenant wilt selecteren om zich onder aan te melden, gebruikt u het als volgt:
    
```azurecli
az login --tenant <tenant>
```

Als je meerdere abonnementen hebt en een specifieke wilt opgeven, kun je je abonnementslijst met de lijst met [AZ-accounten](/cli/azure/account#az-account-list) opvragen en opgeven met [de AZ-accountset](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Zie [Aan de slag met Azure CLI 2.0 voor](/cli/azure/get-started-with-azure-cli)meer informatie. 

### <a name="azure-powershell"></a>Azure PowerShell
De [Azure PowerShell az-module](/powershell/azure/new-azureps-module-az) biedt een set cmdlets waarmee het [Azure Resource Manager-model](../../azure-resource-manager/management/overview.md) wordt gebruikt voor het beheer van uw Azure-resources. U het in uw browser gebruiken met [Azure Cloud Shell,](../../cloud-shell/overview.md)of u het installeren op uw lokale machine met behulp van de instructies in [Installeer de Azure PowerShell-module.](/powershell/azure/install-az-ps) 

Als u het al lokaal hebt geïnstalleerd, controleert u of u de nieuwste versie van azure PowerShell SDK-versie gebruikt om Azure Disk Encryption te configureren. Download de nieuwste versie van [Azure PowerShell-release.](https://github.com/Azure/azure-powershell/releases)

Als [u zich wilt aanmelden bij uw Azure-account met Azure PowerShell,](/powershell/azure/authenticate-azureps?view=azps-2.5.0)gebruikt u de cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```powershell
Connect-AzAccount
```

Als u meerdere abonnementen hebt en er een wilt opgeven, gebruikt u de cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) om ze weer te geven, gevolgd door de cmdlet [Set-AzContext:](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Als u de [cmdlet Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) uitvoert, wordt gecontroleerd of het juiste abonnement is geselecteerd.

Als u wilt controleren of de cmdlets azure-schijfversleuteling zijn geïnstalleerd, gebruikt u de cmdlet [Get-command:](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6)
     
```powershell
Get-command *diskencryption*
```
Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps)voor meer informatie. 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Versleuteling inschakelen op een bestaande of draaiende Linux-vm
In dit scenario u versleuteling inschakelen met behulp van de resourcebeheersjabloon, PowerShell-cmdlets of CLI-opdrachten. Zie het artikel [Azure Disk Encryption for Linux-extensie](../extensions/azure-disk-enc-linux.md) als u schemagegevens nodig hebt voor de extensie virtuele machine.

>[!IMPORTANT]
 >Het is verplicht om een op beheerde schijfgebaseerde VM-exemplaar buiten en vóór het inschakelen van Azure Disk Encryption te maken en/of een back-up te maken. Een momentopname van de beheerde schijf kan worden genomen van de portal of via [Azure Backup](../../backup/backup-azure-vms-encryption.md). Back-ups zorgen ervoor dat een hersteloptie mogelijk is in het geval van een onverwachte storing tijdens versleuteling. Zodra een back-up is gemaakt, kan de cmdlet Set-AzVMDiskEncryptionExtension worden gebruikt om beheerde schijven te versleutelen door de parameter -skipVmBackup op te geven. De opdracht Set-AzVMDiskEncryptionExtension mislukt tegen beheerde vm's op basis van schijven totdat er een back-up is gemaakt en deze parameter is opgegeven. 
>
>Versleuteling versleutelen of uitschakelen kan ertoe leiden dat de vm opnieuw wordt opgestart. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Versleuteling inschakelen op een bestaande of draaiende Linux-VM met Azure CLI 

U schijfversleuteling op uw versleutelde VHD inschakelen door het [Azure CLI-opdrachtregelgereedschap](/cli/azure/?view=azure-cli-latest) te installeren en te gebruiken. U kunt PowerShell in uw browser gebruiken met [Azure Cloud Shell](../../cloud-shell/overview.md), of installeren op uw lokale computer en dan gebruiken in een PowerShell-sessie. Als u versleuteling wilt inschakelen op bestaande of draaiende Linux VM's in Azure, gebruikt u de volgende CLI-opdrachten:

Gebruik de [inschakeling sein AZ VM](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) om versleuteling in te schakelen op een draaiende virtuele machine in Azure.

- **Een draaiende VM versleutelen:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Een draaiende VM versleutelen met KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > De syntaxis voor de waarde van de parameter schijfversleuteling-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI naar de KEK zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Controleer of de schijven versleuteld zijn:** Als u de versleutelingsstatus van een VM wilt controleren, gebruikt u de opdracht [az vm-versleuteling.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Versleuteling uitschakelen:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [voor uitschakelen van AZ VM-versleuteling.](/cli/azure/vm/encryption#az-vm-encryption-disable) Het uitschakelen van versleuteling is alleen toegestaan op gegevensvolumes voor Linux VM's.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Versleuteling inschakelen op een bestaande of draaiende Linux-VM met PowerShell
Gebruik de cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) om versleuteling in te schakelen op een draaiende virtuele machine in Azure. Maak een [momentopname](snapshot-copy-managed-disk.md) en/of maak een back-up van de VM met [Azure Backup](../../backup/backup-azure-vms-encryption.md) voordat schijven worden versleuteld. De parameter -skipVmBackup is al opgegeven in de PowerShell-scripts om een draaiende Linux-vm te versleutelen.

-  **Een draaiende VM versleutelen:** Het onderstaande script initialiseert uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit. De resourcegroep, VM en sleutelkluis zijn als vereisten gemaakt. Vervang MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden. Wijzig de parameter -VolumeType om op te geven welke schijven u versleutelt.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Een draaiende VM versleutelen met KEK:** Mogelijk moet u de parameter -VolumeType toevoegen als u gegevensschijven versleutelt en niet de osschijf. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > De syntaxis voor de waarde van de parameter schijfversleuteling-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI naar de KEK zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Controleer of de schijven versleuteld zijn:** Als u de versleutelingsstatus van een VM wilt controleren, gebruikt u de cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Schijfversleuteling uitschakelen:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Het uitschakelen van versleuteling is alleen toegestaan op gegevensvolumes voor Linux VM's.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Versleuteling inschakelen op een bestaande of draaiende Linux-vm met een sjabloon

U schijfversleuteling inschakelen op een bestaande of draaiende Linux-vm in Azure met behulp van de [sjabloon Resourcemanager.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)

1. Klik **op Implementeren naar Azure** op de Azure quickstart-sjabloon.

2. Selecteer het abonnement, de resourcegroep, de locatie van de resourcegroep, parameters, wettelijke voorwaarden en overeenkomst. Klik **op Maken** om versleuteling in te schakelen op de bestaande of draaiende VM.

In de volgende tabel worden sjabloonparameters resourcebeheer weergegeven voor bestaande of het uitvoeren van VM's:

| Parameter | Beschrijving |
| --- | --- |
| vmName | Naam van de VM om de versleutelingsbewerking uit te voeren. |
| keyVaultName | Naam van de sleutelkluis waarnaar de versleutelingssleutel moet worden geüpload. U het krijgen met `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` behulp van de `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`cmdlet of de azure cli- opdracht.|
| keyVaultResourceGroup | Naam van de resourcegroep die de sleutelkluis bevat. |
|  keyEncryptionKeyURL | URL van de sleutelversleutelingssleutel die wordt gebruikt om de versleutelingssleutel te versleutelen. Deze parameter is optioneel als u **nokek** selecteert in de vervolgkeuzelijst UseExistingKek. Als u **kek** selecteert in de vervolgkeuzelijst UseExistingKek, moet u de waarde _keyEncryptionKeyURL_ invoeren. |
| volumeType | Type volume waarop de versleutelingsbewerking wordt uitgevoerd. Geldige waarden zijn _OS,_ _Data_en _Alles_. 
| forceUpdateTag | Geef een unieke waarde als een GUID door elke keer dat de bewerking moet worden uitgevoerd. |
| location | Locatie voor alle bronnen. |

Zie [Azure Disk Encryption for Linux voor](https://docs.microsoft.com/azure/virtual-machines/extensions/azure-disk-enc-linux)meer informatie over het configureren van de sjabloon voor linux VM-schijfversleuteling.

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>De functie EncryptFormatAll gebruiken voor gegevensschijven op Linux VM's

De parameter **EncryptFormatAll** verkort de tijd voor het versleutelen van Linux-gegevensschijven. Partities die voldoen aan bepaalde criteria worden opgemaakt (met het huidige bestandssysteem) en vervolgens opnieuw worden gemonteerd naar waar het was voordat de opdracht werd uitgevoerd. Als u een gegevensschijf wilt uitsluiten die aan de criteria voldoet, u deze loskoppelen voordat u de opdracht uitvoert.

 Na het uitvoeren van deze opdracht worden alle stations die eerder zijn gemonteerd, geformatteerd en wordt de versleutelingslaag gestart bovenop het nu lege station. Wanneer deze optie is geselecteerd, wordt ook de tijdelijke resourceschijf die aan de VM is gekoppeld, versleuteld. Als het efemere station wordt gereset, wordt deze bij de volgende gelegenheid geformatteerd en opnieuw versleuteld voor de VM door de Azure Disk Encryption-oplossing. Zodra de resourceschijf is versleuteld, kan de [Microsoft Azure Linux Agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) de resourceschijf niet meer beheren en het swapbestand inschakelen, maar u het swapbestand handmatig configureren.

>[!WARNING]
> EncryptFormatAll mag niet worden gebruikt wanneer er gegevens over de gegevensvolumes van een VM nodig zijn. U schijven uitsluiten van versleuteling door ze te demonteren. Probeer eerst de EncryptFormatAll uit op een test-VM, begrijp de functieparameter en de implicatie ervan voordat u deze probeert op de productie-VM. De optie EncryptFormatAll maakt de gegevensschijf op en alle gegevens die erop staan, gaan verloren. Controleer voordat u verdergaat of schijven die u wilt uitsluiten, correct zijn ontkoppeld. </br></br>
 >Als u deze parameter instelt tijdens het bijwerken van versleutelingsinstellingen, kan dit leiden tot een herstart voordat de werkelijke versleuteling wordt bijgewerkt. In dit geval wilt u ook de schijf verwijderen die u niet wilt opgemaakt uit het fstab-bestand. Op dezelfde manier moet u de partitie die u wilt versleutelen toevoegen aan het fstab-bestand voordat u de versleutelingsbewerking start. 

### <a name="encryptformatall-criteria"></a>EncryptFormatAll-criteria
De parameter gaat al alle partities en versleutelt ze, zolang ze voldoen aan **alle** onderstaande criteria: 
- Is geen root/OS/boot partitie
- Is nog niet versleuteld
- Is geen BEK-volume
- Is geen RAID-volume
- Is geen LVM-volume
- Is gemonteerd

Versleutel de schijven die het RAID- of LVM-volume vormen in plaats van het RAID- of LVM-volume.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>De parameter EncryptFormatAll gebruiken met Azure CLI
Gebruik de [inschakeling sein AZ VM](/cli/azure/vm/encryption#az-vm-encryption-enable) om versleuteling in te schakelen op een draaiende virtuele machine in Azure.

-  **Een draaiende VM versleutelen met EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>De parameter EncryptFormatAll gebruiken met een PowerShell-cmdlet
Gebruik de cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) met de parameter EncryptFormatAll. 

**Een draaiende VM versleutelen met EncryptFormatAll:** Als voorbeeld, het script hieronder initialiseert uw variabelen en draait de Set-AzVMDiskEncryptionExtension cmdlet met de parameter EncryptFormatAll. De resourcegroep, VM en sleutelkluis zijn als vereisten gemaakt. Vervang MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>De parameter EncryptFormatAll gebruiken met Logische volumebeheer (LVM) 
Wij raden een LVM-on-crypt setup aan. Voor alle volgende voorbeelden, vervang het apparaat-pad en mountpoints met wat past bij uw use-case. Deze setup kan als volgt worden uitgevoerd:

- Voeg de gegevensschijven toe die de VM samenstellen.
- Deze schijven opmaken, monteren en toevoegen aan het fstab-bestand.

    1. Kies een partitiestandaard, maak een partitie die het hele station overspant en maak de partitie op. We gebruiken hier symlinks gegenereerd door Azure. Het gebruik van symlinks voorkomt dat problemen met betrekking tot apparaatnamen veranderen. Zie het artikel [Problemen met apparaatnamen oplossen](troubleshoot-device-names-problems.md) voor meer informatie.
    
         ```azurepowershell-interactive
         parted /dev/disk/azure/scsi1/lun0 mklabel gpt
         parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
         
         mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
         ```
    
    1. Monteer de schijven.
         
         `mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint`
    
    1. Toevoegen aan fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab`
    
    1. Voer de PowerShell-cmdlet Set-AzVMDiskEncryptionExtension uit met -EncryptFormatAll om deze schijven te versleutelen.

       ```azurepowershell-interactive
       $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
           
       Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
       ```

    1. Stel LVM in op deze nieuwe schijven. Let op: de versleutelde schijven worden ontgrendeld nadat de VM is voltooid met opstarten. De LVM-montage zal dus ook later moeten worden uitgesteld.


## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nieuwe VM's gemaakt van door de klant versleutelde VHD- en encryptiesleutels
In dit scenario u versleutelen inschakelen met PowerShell-cmdlets of CLI-opdrachten. 

Gebruik de instructies in de Azure Disk-versleuteling voor het voorbereiden van vooraf versleutelde afbeeldingen die in Azure kunnen worden gebruikt. Nadat de afbeelding is gemaakt, u de stappen in de volgende sectie gebruiken om een versleutelde Azure-vm te maken.

* [Een vooraf versleutelde Linux VHD voorbereiden](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Het is verplicht om een op beheerde schijfgebaseerde VM-exemplaar buiten en vóór het inschakelen van Azure Disk Encryption te maken en/of een back-up te maken. Een momentopname van de beheerde schijf kan uit de portal worden gehaald of [Azure Backup](../../backup/backup-azure-vms-encryption.md) kan worden gebruikt. Back-ups zorgen ervoor dat een hersteloptie mogelijk is in het geval van een onverwachte storing tijdens versleuteling. Zodra een back-up is gemaakt, kan de cmdlet Set-AzVMDiskEncryptionExtension worden gebruikt om beheerde schijven te versleutelen door de parameter -skipVmBackup op te geven. De opdracht Set-AzVMDiskEncryptionExtension mislukt tegen beheerde vm's op basis van schijven totdat er een back-up is gemaakt en deze parameter is opgegeven. 
>
> Versleuteling versleutelen of uitschakelen kan ertoe leiden dat de vm opnieuw wordt opgestart. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Azure PowerShell gebruiken om VM's te versleutelen met vooraf versleutelde VHD's 
U schijfversleuteling inschakelen op uw versleutelde VHD met behulp van de PowerShell-cmdlet [Set-AzVMOSDisk.](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples) Het onderstaande voorbeeld geeft u een aantal algemene parameters. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Versleuteling inschakelen op een nieuw toegevoegde gegevensschijf

U een nieuwe gegevensschijf toevoegen met behulp van [de AZ VM-schijf of](add-disk.md)via de [Azure-portal.](attach-disk-portal.md) Voordat u versleutelen, moet u eerst de nieuw aangesloten gegevensschijf monteren. U moet encryptie van het gegevensstation aanvragen, omdat het station onbruikbaar is terwijl de versleuteling wordt uitgevoerd. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure CLI

 Als de VM eerder is versleuteld met "Alles" dan moet de parameter --volume-type "Alles" blijven. Alle omvat zowel OS en data schijven. Als de VM eerder is versleuteld met een volumetype 'OS', moet de parameter --volume-type worden gewijzigd in 'Alles' zodat zowel het besturingssysteem als de nieuwe gegevensschijf worden opgenomen. Als de VM is versleuteld met alleen het volumetype "Gegevens", dan kan deze "Gegevens" blijven, zoals hieronder wordt aangetoond. Het toevoegen en koppelen van een nieuwe gegevensschijf aan een VM is onvoldoende voorbereiding op versleuteling. De nieuw aangesloten schijf moet ook worden opgemaakt en goed gemonteerd in de VM voordat versleuteling wordt inschakelt. Op Linux moet de schijf worden gemonteerd in / etc / fstab met een [hardnekkige blok apparaat naam](troubleshoot-device-names-problems.md).  

In tegenstelling tot de syntaxis van Powershell vereist de CLI niet dat de gebruiker een unieke sequentieversie levert wanneer de versleuteling wordt geactiveerd. De CLI genereert en gebruikt automatisch zijn eigen unieke volgordeversiewaarde.

-  **Gegevensvolumes van een draaiende VM versleutelen:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Gegevensvolumes van een draaiende VM versleutelen met KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure PowerShell
 Wanneer u Powershell gebruikt om een nieuwe schijf voor Linux te versleutelen, moet een nieuwe reeksversie worden opgegeven. De sequentieversie moet uniek zijn. Het onderstaande script genereert een GUID voor de sequentieversie. Maak een [momentopname](snapshot-copy-managed-disk.md) en/of maak een back-up van de VM met [Azure Backup](../../backup/backup-azure-vms-encryption.md) voordat schijven worden versleuteld. De parameter -skipVmBackup is al opgegeven in de PowerShell-scripts om een nieuw toegevoegde gegevensschijf te versleutelen.
 

-  **Gegevensvolumes van een draaiende VM versleutelen:** Het onderstaande script initialiseert uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit. De resourcegroep, VM en sleutelkluis moeten al als vereisten zijn gemaakt. Vervang MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden. Acceptabele waarden voor de parameter -VolumeType zijn Alle, OS en Gegevens. Als de VM eerder is versleuteld met een volumetype 'OS' of 'Alles', moet de parameter -VolumeType worden gewijzigd in 'Alles' zodat zowel het besturingssysteem als de nieuwe gegevensschijf worden opgenomen.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Gegevensvolumes van een draaiende VM versleutelen met KEK:** Acceptabele waarden voor de parameter -VolumeType zijn Alle, OS en Gegevens. Als de VM eerder is versleuteld met een volumetype 'OS' of 'Alles', moet de parameter -VolumeType worden gewijzigd in Alles, zodat zowel het besturingssysteem als de nieuwe gegevensschijf worden opgenomen.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > De syntaxis voor de waarde van de parameter schijfversleuteling-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI naar de KEK zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Versleuteling voor Linux VM's uitschakelen
U versleuteling uitschakelen met Azure PowerShell, de Azure CLI of met een resourcemanagersjabloon. 

>[!IMPORTANT]
>Versleuteling uitschakelen met Azure Disk Encryption op Linux VM's wordt alleen ondersteund voor gegevensvolumes. Het wordt niet ondersteund op gegevens of BE-volumes als het os-volume is versleuteld.  

- **Schijfversleuteling uitschakelen met Azure PowerShell:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Versleuteling uitschakelen met de Azure CLI:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [voor uitschakelen van AZ VM-versleuteling.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Versleuteling uitschakelen met een resourcemanagersjabloon:** Gebruik de [versleuteling uitschakelen op een draaiende Linux VM-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) om versleuteling uit te schakelen.
     1. Klik op **Implementeren in Azure**.
     2. Selecteer het abonnement, de resourcegroep, locatie, VM, juridische voorwaarden en overeenkomst.

## <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

Azure Disk Encryption werkt niet voor de volgende Linux-scenario's, -functies en -technologie:

- Versleutelen van basislaagVM's of VM's die zijn gemaakt via de klassieke methode voor het maken van vm's.
- Versleuteling uitschakelen op een os-station of gegevensstation van een Linux-vm wanneer het besturingssysteem is versleuteld.
- Versleutelen OS drive voor Linux virtuele machine schaal sets.
- Aangepaste afbeeldingen versleutelen op Linux VM's.
- Integratie met een on-premises sleutelbeheersysteem.
- Azure Files (gedeeld bestandssysteem).
- Network File System (NFS).
- Dynamische volumes.
- Kortstondige OS-schijven.
- Versleuteling van gedeelde/gedistribueerde bestandssystemen zoals (maar niet beperkt tot): DFS, GFS, DRDB en CephFS.
- Een versleutelde VM verplaatsen naar een ander abonnement.
- Kernel Crash Dump (kdump).

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
- [Voorbeeldscripts voor Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Problemen met Azure Disk Encryption oplossen](disk-encryption-troubleshooting.md)
