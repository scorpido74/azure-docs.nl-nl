---
title: Azure Disk Encryption scenario's voor Linux-Vm's
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure schijf versleuteling voor Linux-Vm's voor verschillende scenario's
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 19dcfb96f29939fd92f49ba288ddb6d9264e0f9a
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970600"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Azure Disk Encryption scenario's voor Linux-Vm's

Azure Disk Encryption maakt gebruik van de DM-cryptografie functie van Linux om volume versleuteling te bieden voor het besturings systeem en de gegevens schijven van Azure virtual machines (Vm's), en is geïntegreerd met Azure Key Vault om u te helpen de coderings sleutels en geheimen van de schijf te beheren en te controleren. Zie [Azure Disk Encryption voor Linux-vm's](disk-encryption-overview.md)voor een overzicht van de service.

Er zijn veel schijf versleutelings scenario's en de stappen kunnen variëren afhankelijk van het scenario. In de volgende secties vindt u meer informatie over de scenario's voor Linux-Vm's.

U kunt alleen schijf versleuteling Toep assen op virtuele machines met [ondersteunde VM-grootten en-besturings systemen](disk-encryption-overview.md#supported-vms-and-operating-systems). U moet ook aan de volgende vereisten voldoen:

- [Aanvullende vereisten voor Vm's](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Netwerk vereisten](disk-encryption-overview.md#networking-requirements)
- [Opslag vereisten voor de versleutelings sleutel](disk-encryption-overview.md#encryption-key-storage-requirements)

In alle gevallen moet u [een moment opname nemen](snapshot-copy-managed-disk.md) en/of een back-up maken voordat schijven worden versleuteld. Back-ups zorgen ervoor dat een optie voor siteherstel mogelijk als er een onverwachte fout optreedt tijdens het versleutelen. Virtuele machines met beheerde schijven moeten u een back-up voordat versleuteling plaatsvindt. Als er een back-up is gemaakt, kunt u de [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) gebruiken om beheerde schijven te versleutelen door de para meter-skipVmBackup op te geven. Zie het [Azure backup](../../backup/backup-azure-vms-encryption.md) -artikel voor meer informatie over het maken van back-ups en het herstellen van versleutelde vm's. 

>[!WARNING]
> - Als u eerder Azure Disk Encryption met Azure AD hebt gebruikt om een virtuele machine te versleutelen, moet u deze optie blijven gebruiken om uw virtuele machine te versleutelen. Zie [Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-overview-aad.md) voor meer informatie. 
>
> - Bij het versleutelen van Linux-besturingssysteem volumes moet de virtuele machine worden beschouwd als niet-beschikbaar. We raden u ten zeerste aan om SSH-aanmeldingen te voor komen terwijl de versleuteling wordt uitgevoerd om te voor komen dat er geopende bestanden worden geblokkeerd die moeten worden geopend tijdens het versleutelings proces. Als u de voortgang wilt controleren, gebruikt u de Power shell-cmdlet [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) of de [VM-versleutelings opdracht show](/cli/azure/vm/encryption#az-vm-encryption-show) cli. Dit proces kan naar verwachting enkele uren duren voor een besturingssysteem volume van 30 GB, plus extra tijd voor het versleutelen van gegevens volumes. De versleutelings tijd voor gegevens volumes is evenredig met de grootte en hoeveelheid van de gegevens volumes, tenzij de optie alle versleutelings indeling wordt gebruikt. 
> - Uitschakelen van versleuteling op Linux-VM's wordt alleen ondersteund voor de gegevensvolumes. Het wordt niet ondersteund op de gegevens of besturingssysteemvolumes als het volume met het besturingssysteem is versleuteld.  

## <a name="install-tools-and-connect-to-azure"></a>Hulpprogram ma's installeren en verbinding maken met Azure

Azure Disk Encryption kunnen worden ingeschakeld en beheerd via de [Azure cli](/cli/azure) en [Azure PowerShell](/powershell/azure/new-azureps-module-az). Hiervoor moet u de hulpprogram ma's lokaal installeren en verbinding maken met uw Azure-abonnement.

### <a name="azure-cli"></a>Azure CLI

De [Azure CLI 2,0](/cli/azure) is een opdracht regel programma voor het beheer van Azure-resources. De CLI is ontworpen voor flexibel gegevens op te vragen, langdurige bewerkingen als niet-blokkerende processen ondersteunen en scripts eenvoudig uitvoeren. U kunt deze lokaal installeren door de stappen in [de Azure cli installeren](/cli/azure/install-azure-cli?view=azure-cli-latest)te volgen.

 

Als u [zich wilt aanmelden bij uw Azure-account met de Azure cli](/cli/azure/authenticate-azure-cli), gebruikt u de opdracht [AZ login](/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```azurecli
az login
```

Als u selecteren van een tenant wilt onder aanmelden, gebruikt:
    
```azurecli
az login --tenant <tenant>
```

Als u meerdere abonnementen hebt en u een specifiek abonnement wilt opgeven, kunt u uw lijst met abonnementen ophalen met [AZ account list](/cli/azure/account#az-account-list) en opgeven met [AZ account set](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Zie [aan de slag met Azure CLI 2,0](/cli/azure/get-started-with-azure-cli)voor meer informatie. 

### <a name="azure-powershell"></a>Azure PowerShell
De [Azure PowerShell AZ-module](/powershell/azure/new-azureps-module-az) bevat een set cmdlets die gebruikmaken van het [Azure Resource Manager](../../azure-resource-manager/management/overview.md) model voor het beheren van uw Azure-resources. U kunt dit in uw browser gebruiken met [Azure Cloud shell](../../cloud-shell/overview.md), of u kunt het installeren op uw lokale computer met behulp van de instructies in [de Azure PowerShell-module installeren](/powershell/azure/install-az-ps). 

Als u al deze lokaal geïnstalleerd hebt, zorg er dan voor dat u de nieuwste versie van Azure PowerShell SDK-versie om te configureren van Azure Disk Encryption. Down load de nieuwste versie van [Azure PowerShell release](https://github.com/Azure/azure-powershell/releases).

Gebruik de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) om u aan [te melden bij uw Azure-account met Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0).

```powershell
Connect-AzAccount
```

Als u meerdere abonnementen hebt en er een wilt opgeven, gebruikt u de cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) om ze weer te geven, gevolgd door de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) :

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Als u de cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) uitvoert, wordt gecontroleerd of het juiste abonnement is geselecteerd.

Als u wilt controleren of de Azure Disk Encryption-cmdlets zijn geïnstalleerd, gebruikt u de cmdlet [Get-opdracht](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) :
     
```powershell
Get-command *diskencryption*
```
Zie [aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps)voor meer informatie. 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Versleuteling inschakelen op een bestaande of actieve virtuele Linux-machine
In dit scenario kunt u versleuteling inschakelen met behulp van de Resource Manager-sjabloon, de PowerShell-cmdlets of de CLI-opdrachten. Als u schema-informatie voor de extensie van de virtuele machine nodig hebt, raadpleegt u het artikel [Azure Disk Encryption voor Linux-extensie](../extensions/azure-disk-enc-linux.md) .

>[!IMPORTANT]
 >Dit is verplicht op momentopname en/of back-up een beheerde schijf op basis van de VM-exemplaar buiten en voordat Azure Disk Encryption wordt ingeschakeld. Een moment opname van de beheerde schijf kan worden gemaakt vanuit de portal of via [Azure backup](../../backup/backup-azure-vms-encryption.md). Back-ups zorgen ervoor dat een optie voor siteherstel mogelijk in het geval van een onverwachte fout opgetreden tijdens het versleutelen is. Als er een back-up is gemaakt, kan de cmdlet Set-AzVMDiskEncryptionExtension worden gebruikt om beheerde schijven te versleutelen door de para meter-skipVmBackup op te geven. De opdracht set-AzVMDiskEncryptionExtension werkt niet voor virtuele machines op basis van beheerde schijven tot er een back-up is gemaakt en deze para meter is opgegeven. 
>
>Versleutelen of als u versleuteling uitschakelt kan ertoe leiden dat de virtuele machine opnieuw op te starten. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Versleuteling inschakelen op een bestaande of actieve virtuele Linux-machine met behulp van Azure CLI 

U kunt schijf versleuteling inschakelen op uw versleutelde VHD door het opdracht regel programma van [Azure cli](/cli/azure/?view=azure-cli-latest) te installeren en te gebruiken. U kunt PowerShell in uw browser gebruiken met [Azure Cloud Shell](../../cloud-shell/overview.md), of installeren op uw lokale computer en dan gebruiken in een PowerShell-sessie. Gebruik de volgende CLI-opdrachten om versleuteling in te scha kelen voor bestaande of actieve virtuele Linux-machines in Azure:

Gebruik de opdracht [AZ VM Encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) om versleuteling in te scha kelen op een actieve virtuele machine in Azure.

- **Een actieve VM versleutelen:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Een actieve VM versleutelen met behulp van KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > De syntaxis voor de waarde van de schijf-versleuteling-keyvault-parameter is de volledige id-reeks: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
De syntaxis voor de waarde van de encryptiesleutel parameter is de volledige URI naar de KEK-sleutel in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Controleer of de schijven zijn versleuteld:** Als u de versleutelings status van een virtuele machine wilt controleren, gebruikt u de opdracht [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Versleuteling uitschakelen:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Als u versleuteling uitschakelt is alleen toegestaan op gegevensvolumes voor virtuele Linux-machines.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Versleuteling inschakelen op een bestaande of actieve virtuele Linux-machine met behulp van Power shell
Gebruik de cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) om versleuteling in te scha kelen op een actieve virtuele machine in Azure. Maak een [moment opname](snapshot-copy-managed-disk.md) en/of maak een back-up van de virtuele machine met [Azure backup](../../backup/backup-azure-vms-encryption.md) voordat schijven worden versleuteld. De para meter-skipVmBackup is al opgegeven in de Power shell-scripts voor het versleutelen van een actieve Linux-VM.

-  **Een actieve VM versleutelen:** Het onderstaande script initialiseert uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit. De resource groep, de VM en de sleutel kluis zijn gemaakt als vereisten. Vervang MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden. Wijzig de para meter-VolumeType om op te geven welke schijven u wilt versleutelen.

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
- **Een actieve VM versleutelen met behulp van KEK:** Mogelijk moet u de para meter-VolumeType toevoegen als u gegevens schijven versleutelt en niet de besturingssysteem schijf. 

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
    > De syntaxis voor de waarde van de schijf-versleuteling-keyvault-parameter is de volledige id-reeks: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> De syntaxis voor de waarde van de encryptiesleutel parameter is de volledige URI naar de KEK-sleutel in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Controleer of de schijven zijn versleuteld:** Gebruik de cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) om de versleutelings status van een virtuele machine te controleren. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Schijf versleuteling uitschakelen:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Als u versleuteling uitschakelt is alleen toegestaan op gegevensvolumes voor virtuele Linux-machines.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Versleuteling inschakelen op een bestaande of uitgevoerde Linux-VM met een sjabloon

U kunt schijf versleuteling inschakelen op een bestaande of uitgevoerde Linux-VM in azure met behulp van de [Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Klik op **implementeren naar Azure** op de Azure Quick Start-sjabloon.

2. Selecteer het abonnement, resourcegroep, locatie voor resourcegroep, parameters, juridische voorwaarden en -overeenkomst. Klik op **maken** om versleuteling in te scha kelen op de bestaande of actieve virtuele machine.

De volgende tabel bevat de parameters van de Resource Manager-sjabloon voor bestaande of uitvoeren van virtuele machines:

| Parameter | Beschrijving |
| --- | --- |
| vmName | De naam van de virtuele machine om uit te voeren van de versleutelingsbewerking. |
| keyVaultName | De naam van de sleutel kluis waarnaar de versleutelings sleutel moet worden geüpload. U kunt deze ophalen met behulp van de cmdlet-`(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` of de Azure CLI-opdracht `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`.|
| keyVaultResourceGroup | De naam van de resource groep die de sleutel kluis bevat. |
|  KeyEncryptionKeyURL | De URL van de sleutel versleutelings sleutel die wordt gebruikt voor het versleutelen van de versleutelings sleutel. Deze para meter is optioneel als u **nokek** selecteert in de vervolg keuzelijst UseExistingKek. Als u **Kek** selecteert in de vervolg keuzelijst UseExistingKek, moet u de _keyEncryptionKeyURL_ -waarde invoeren. |
| VolumeType | Het type volume dat de versleutelingsbewerking wordt uitgevoerd op. Geldige waarden zijn _besturings systeem_, _gegevens_en _alle_. 
| forceUpdateTag | In een unieke waarde, zoals een GUID doorgeven telkens wanneer de bewerking moet worden geforceerd uitvoeren. |
| resizeOSDisk | Moet de OS-partitie worden kleiner gemaakt zodat ze volledig OS VHD voordat het systeemvolume splitsen in beslag nemen. |
| locatie | Locatie voor alle resources. |


## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>De functie EncryptFormatAll gebruiken voor gegevens schijven op virtuele Linux-machines

De para meter **EncryptFormatAll** vermindert de tijd voor het versleutelen van Linux-gegevens schijven. Partities die aan bepaalde criteria voldoen, worden opgemaakt (met het huidige bestands systeem) en vervolgens opnieuw gekoppeld aan de locatie van de uitvoering van de opdracht. Als u uitsluiten van een gegevensschijf die voldoet aan de criteria wilt, kunt u het ontkoppelen voordat u de opdracht uitvoert.

 Nadat u deze opdracht hebt uitgevoerd, worden alle schijven die eerder zijn gekoppeld, geformatteerd en wordt de versleutelings laag boven op het lege station gestart. Wanneer deze optie is geselecteerd, wordt de tijdelijke resource-schijf die is gekoppeld aan de virtuele machine eveneens versleuteld. Als de kortstondige schijf opnieuw wordt ingesteld, wordt deze opnieuw geformatteerd en opnieuw versleuteld voor de virtuele machine met de Azure Disk Encryption-oplossing in de eerstvolgende gelegenheid installeren. Zodra de bron schijf is versleuteld, kan de [Microsoft Azure Linux-agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) de bron schijf niet beheren en het wissel bestand niet inschakelen, maar u kunt het wissel bestand ook hand matig configureren.

>[!WARNING]
> EncryptFormatAll mag niet worden gebruikt wanneer er benodigde gegevens op gegevensvolumes van een virtuele machine. U kunt schijven uitsluiten van versleuteling door ze te ontkoppelen. U moet eerst de EncryptFormatAll eerst op een virtuele testmachine uitproberen, inzicht in de functie-parameter en de gevolgen voor de voordat u deze op de VM voor productie. De optie EncryptFormatAll maakt op de gegevensschijf en alle gegevens op verloren. Voordat u verdergaat, controleert u of schijven die u wilt uitsluiten correct ontkoppeld. </br></br>
 >Als u deze parameter instellen tijdens het bijwerken van instellingen voor codering, kan dit leiden tot een opnieuw opstarten voordat de daadwerkelijke versleuteling. In dit geval is het ook wilt verwijderen van de schijf die u niet opmaken van het fstab-bestand wilt. Op dezelfde manier moet u de partitie die u versleutelen-indeling naar het fstab-bestand wilt voordat u begint de coderingsbewerking toevoegen. 

### <a name="encryptformatall-criteria"></a>EncryptFormatAll criteria
De para meter zorgt ervoor dat alle partities worden verplaatst en versleuteld zolang ze voldoen aan **alle** onderstaande criteria: 
- Is niet een hoofdmap/OS/opstartpartitie
- Is niet versleuteld
- Is geen BEK volume
- Is niet een RAID-volume
- Is niet een LVM-volume
- Is gekoppeld

Versleutel de schijven die het volume RAID- of LVM in plaats van het volume RAID- of LVM opstellen.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>De para meter EncryptFormatAll gebruiken met Azure CLI
Gebruik de opdracht [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) om versleuteling in te scha kelen op een actieve virtuele machine in Azure.

-  **Een actieve VM versleutelen met behulp van EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>De para meter EncryptFormatAll gebruiken met een Power shell-cmdlet
Gebruik de cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) met de para meter EncryptFormatAll. 

**Een actieve VM versleutelen met behulp van EncryptFormatAll:** Als voor beeld wordt met het onderstaande script de variabelen geïnitialiseerd en wordt de cmdlet Set-AzVMDiskEncryptionExtension uitgevoerd met de para meter EncryptFormatAll. De resource groep, de virtuele machine en de sleutel kluis zijn gemaakt als vereisten. Vervang MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden.
  
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


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Gebruik de para meter EncryptFormatAll met Logical Volume Manager (LVM) 
U wordt aangeraden een LVM-op-crypt-installatie. Voor de volgende voorbeelden wordt vervangen door de apparaat-path en stel de volgende parameter wat aansluit op uw use-casescenario. Deze instelling kan als volgt worden uitgevoerd:

- De gegevensschijven waaruit de virtuele machine toevoegen.
- Indeling, koppelen en deze schijven toevoegen aan het fstab-bestand.

    1. Formatteer de nieuw toegevoegde schijf. We gebruiken symlinks die hier worden gegenereerd door Azure. Met behulp van symlinks voorkomt u problemen met betrekking tot apparaatnamen wijzigen. Zie het artikel problemen [met apparaatnamen oplossen](troubleshoot-device-names-problems.md) voor meer informatie.
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    1. Koppel de schijven.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    1. Toevoegen aan fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    1. Voer de Power shell-cmdlet Set-AzVMDiskEncryptionExtension met-EncryptFormatAll uit om deze schijven te versleutelen.

       ```azurepowershell-interactive
       $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
           
       Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
       ```

    1. LVM boven op deze nieuwe schijven instellen. Houd er rekening mee dat de versleutelde schijven worden ontgrendeld nadat de virtuele machine wordt opgestart is voltooid. Het LVM-koppelen heeft dus ook later worden vertraagd.


## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nieuwe Vm's gemaakt op basis van door de klant versleutelde VHD-en versleutelings sleutels
In dit scenario kunt u inschakelen met behulp van PowerShell-cmdlets of de CLI-opdrachten te coderen. 

Gebruik de instructies in de Azure Disk Encryption zelfde scripts voor het voorbereiden van vooraf versleutelde installatie kopieën die kunnen worden gebruikt in Azure. Nadat de installatiekopie is gemaakt, kunt u de stappen in de volgende sectie om een versleutelde Azure-VM te maken.

* [Een vooraf versleutelde Linux-VHD voorbereiden](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Dit is verplicht op momentopname en/of back-up een beheerde schijf op basis van de VM-exemplaar buiten en voordat Azure Disk Encryption wordt ingeschakeld. Een moment opname van de beheerde schijf kan worden opgehaald uit de portal of [Azure backup](../../backup/backup-azure-vms-encryption.md) kunnen worden gebruikt. Back-ups zorgen ervoor dat een optie voor siteherstel mogelijk in het geval van een onverwachte fout opgetreden tijdens het versleutelen is. Als er een back-up is gemaakt, kan de cmdlet Set-AzVMDiskEncryptionExtension worden gebruikt om beheerde schijven te versleutelen door de para meter-skipVmBackup op te geven. De opdracht set-AzVMDiskEncryptionExtension werkt niet voor virtuele machines op basis van beheerde schijven tot er een back-up is gemaakt en deze para meter is opgegeven. 
>
> Versleutelen of als u versleuteling uitschakelt kan ertoe leiden dat de virtuele machine opnieuw op te starten. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Gebruik Azure PowerShell om Vm's te versleutelen met vooraf versleutelde Vhd's 
U kunt schijf versleuteling op uw versleutelde VHD inschakelen met behulp van de Power shell [-cmdlet Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). Het onderstaande voorbeeld hebt u enkele algemene parameters. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Schakelt u versleuteling op een nieuw toegevoegde gegevensschijf

U kunt een nieuwe gegevens schijf toevoegen met [AZ VM Disk attach](add-disk.md)of [via de Azure Portal](attach-disk-portal.md). Voordat u coderen kunt, moet u eerst de zojuist gekoppelde gegevensschijf koppelen. U moet versleuteling van het gegevensstation aanvragen omdat het station onbruikbaar is terwijl versleuteling uitgevoerd wordt. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Schakelt u versleuteling op een nieuw toegevoegde schijf met Azure CLI

 Als de virtuele machine eerder is versleuteld met ' all ', moet de para meter--type ' all ' blijven. Alle bevat zowel besturingssysteem en gegevensschijven. Als de virtuele machine eerder is versleuteld met het volume ' OS ', moet de para meter--volume-type worden gewijzigd in ' alle ', zodat zowel het besturings systeem als de nieuwe gegevens schijf worden opgenomen. Als de virtuele machine is versleuteld met alleen het volumetype van "Gegevens", kan het "Gegevens" blijven zoals hieronder wordt gedemonstreerd. Toe te voegen en een nieuwe gegevensschijf koppelen aan een virtuele machine is niet voldoende voorbereiding voor versleuteling. De zojuist gekoppelde schijf moet ook worden ingedeeld en correct is gekoppeld in de VM voordat versleuteling werd ingeschakeld. Op Linux moet de schijf worden gekoppeld in bestand/etc/fstab met een [permanente blok apparaatnaam](troubleshoot-device-names-problems.md).  

In tegenstelling tot de Powershell-syntaxis vereist de CLI niet de gebruiker voor de versie van een unieke reeks bij het inschakelen van versleuteling. De CLI genereert automatisch en wordt de waarde van een eigen unieke reeks versie.

-  **Gegevens volumes van een actieve VM versleutelen:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Versleutelen van gegevens volumes van een actieve VM met behulp van KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Schakelt u versleuteling op een nieuw toegevoegde schijf met Azure PowerShell
 Wanneer u Powershell gebruikt voor het versleutelen van een nieuwe schijf voor Linux, moet een nieuwe versie van de takenreeks worden opgegeven. De versie van de reeks moet uniek zijn. Het onderstaande script genereert een GUID voor de versie van de reeks. Maak een [moment opname](snapshot-copy-managed-disk.md) en/of maak een back-up van de virtuele machine met [Azure backup](../../backup/backup-azure-vms-encryption.md) voordat schijven worden versleuteld. De para meter-skipVmBackup is al opgegeven in de Power shell-scripts voor het versleutelen van een nieuw toegevoegde gegevens schijf.
 

-  **Gegevens volumes van een actieve VM versleutelen:** Het onderstaande script initialiseert uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit. De resourcegroep, de virtuele machine en de sleutelkluis moeten al zijn gemaakt voor de vereisten. Vervang MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden. Acceptabele waarden voor de parameter - VolumeType zijn alle, OS- en gegevens. Als de virtuele machine eerder is versleuteld met het volume ' OS ' of ' all ', moet de para meter-VolumeType worden gewijzigd in ' all ', zodat zowel het besturings systeem als de nieuwe gegevens schijf worden opgenomen.

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
- **Versleutelen van gegevens volumes van een actieve VM met behulp van KEK:** Acceptabele waarden voor de para meter-VolumeType zijn alle, het besturings systeem en de gegevens. Als de virtuele machine is versleuteld met een volumetype 'BS' of 'Alle', moet klikt u vervolgens de parameter - VolumeType worden gewijzigd in alle zodat zowel het besturingssysteem en de nieuwe gegevensschijf opgenomen worden.

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
    > De syntaxis voor de waarde van de para meter voor schijf versleuteling-sleutel kluis is de volledige id-teken reeks:/Subscriptions/[abonnement-ID-GUID]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[sleutel kluis-name]</br> De syntaxis voor de waarde van de encryptiesleutel parameter is de volledige URI naar de KEK-sleutel in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Versleuteling voor Linux-VM's uitschakelen
U kunt versleuteling met Azure PowerShell, de Azure CLI, uitschakelen of met een Resource Manager-sjabloon. 

>[!IMPORTANT]
>Versleuteling met Azure Disk Encryption voor virtuele Linux-machines uitschakelen wordt alleen ondersteund voor de gegevensvolumes. Het wordt niet ondersteund op de gegevens of besturingssysteemvolumes als het volume met het besturingssysteem is versleuteld.  

- **Schijf versleuteling uitschakelen met Azure PowerShell:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Versleuteling uitschakelen met Azure cli:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Versleuteling uitschakelen met een resource manager-sjabloon:** Gebruik de sjabloon [versleuteling uitschakelen in een actieve Linux-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) om versleuteling uit te scha kelen.
     1. Klik op **Implementeren in Azure**.
     2. Selecteer het abonnement, resourcegroep, locatie, VM, juridische voorwaarden en -overeenkomst.

## <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

Azure Disk Encryption werkt niet voor de volgende Linux-scenario's,-functies en-technologie:

- Het versleutelen van de virtuele machine van de Basic-laag of de virtuele machines die zijn gemaakt met de methode klassieke VM
- Versleuteling uitschakelen op een OS-station of gegevens station van een virtuele Linux-machine wanneer het station van het besturings systeem is versleuteld.
- OS-station voor schaal sets van virtuele Linux-machines versleutelen.
- Aangepaste installatie kopieën op Linux-Vm's versleutelen.
- Integratie met een on-premises sleutel beheersysteem.
- Azure-bestanden (gedeelde bestandssysteem).
- Network File System (NFS).
- Dynamische volumes.
- Tijdelijke OS-schijven.
- Versleuteling van gedeelde/gedistribueerde bestands systemen zoals (maar niet beperkt tot): DFS, GFS, DRDB en CephFS.
- Kernel-crash dump (kdump).

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
- [Voorbeeldscripts voor Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Problemen met Azure Disk Encryption oplossen](disk-encryption-troubleshooting.md)
