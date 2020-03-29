---
title: Azure-schijfversleuteling met Azure AD App Linux IaaS VM's (vorige release)
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure Disk Encryption for Linux IaaS VM's.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: ee365d37a957350fa8a68da0f34149d3210d6238
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970611"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Azure-schijfversleuteling inschakelen met Azure AD op Linux VM's (vorige release)

De nieuwe versie van Azure Disk Encryption elimineert de vereiste voor het verstrekken van een Azure Active Directory (Azure AD) toepassingsparameter om VM-schijfversleuteling in te schakelen. Met de nieuwe release hoeft u geen Azure AD-referenties meer op te geven tijdens de stap versleuteling inschakelen. Alle nieuwe VM's moeten worden versleuteld zonder de Azure AD-toepassingsparameters met behulp van de nieuwe versie. Zie [Azure Disk Encryption for Linux VMS voor](disk-encryption-linux.md)instructies over het inschakelen van VM-schijfversleuteling met behulp van de nieuwe versie. VM's die al zijn versleuteld met Azure AD-toepassingsparameters worden nog steeds ondersteund en moeten worden gehandhaafd met de syntaxis van AAD.

U veel schijfversleutelingsscenario's inschakelen en de stappen kunnen variëren afhankelijk van het scenario. De volgende secties behandelen de scenario's in meer detail voor Linux infrastructuur as a service (IaaS) VM's. U schijfversleuteling alleen toepassen op virtuele machines met [ondersteunde VM-formaten en besturingssystemen.](disk-encryption-overview.md#supported-vms-and-operating-systems) U moet ook aan de volgende voorwaarden voldoen:

- [Aanvullende vereisten voor VM's](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Netwerk- en groepsbeleid](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Vereisten voor opslag van versleutelingssleutels](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Maak een [momentopname,](snapshot-copy-managed-disk.md)maak een back-up of beide voordat u de schijven versleutelt. Back-ups zorgen ervoor dat een hersteloptie mogelijk is als er een onverwachte fout optreedt tijdens versleuteling. VM's met beheerde schijven vereisen een back-up voordat versleuteling plaatsvindt. Nadat een back-up is gemaakt, u de cmdlet Set-AzVMDiskEncryptionExtension gebruiken om beheerde schijven te versleutelen door de parameter -skipVmBackup op te geven. Zie [Azure Backup](../../backup/backup-azure-vms-encryption.md)voor meer informatie over het maken van back-ups en het herstellen van versleutelde VM's. 

>[!WARNING]
 > - Als u eerder [Azure Disk Encryption met de Azure AD-app](disk-encryption-overview-aad.md) hebt gebruikt om deze vm te versleutelen, moet u deze optie blijven gebruiken om uw vm te versleutelen. U [Azure Disk Encryption](disk-encryption-overview.md) niet gebruiken op deze versleutelde VM omdat dit geen ondersteund scenario is, wat betekent dat het overschakelen van de Azure AD-toepassing voor deze versleutelde VM nog niet wordt ondersteund.
 > - Om ervoor te zorgen dat de versleutelingsgeheimen de regionale grenzen niet overschrijden, heeft Azure Disk Encryption de sleutelkluis en de VM's nodig om zich in dezelfde regio te bevinden. Maak en gebruik een sleutelkluis die zich in dezelfde regio bevindt als de VM die moet worden versleuteld.
 > - Wanneer u Linux OS-volumes versleutelt, kan het proces enkele uren duren. Het is normaal dat Linux OS-volumes langer duren dan gegevensvolumes om te versleutelen.
> - Wanneer u Linux-besturingssysteemvolumes versleutelt, moet de VM als niet beschikbaar worden beschouwd. We raden u ten zeerste aan om SSH-aanmeldingen te vermijden terwijl de versleuteling aan de gang is om te voorkomen dat geopende bestanden worden geblokkeerd die tijdens het versleutelingsproces moeten worden geopend. Als u de voortgang wilt controleren, gebruikt u de opdrachten [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) of [vm-versleuteling.](/cli/azure/vm/encryption#az-vm-encryption-show) U verwachten dat dit proces een paar uur in beslag neemt voor een besturingssysteemvolume van 30 GB, plus extra tijd voor het versleutelen van gegevensvolumes. De versleutelingstijd van het gegevensvolume is evenredig aan de grootte en hoeveelheid van de gegevensvolumes, tenzij de **versleutelingsindeling alle** opties wordt gebruikt. 
 > - Het uitschakelen van versleuteling op Linux VM's wordt alleen ondersteund voor gegevensvolumes. Het wordt niet ondersteund op gegevens of besturingssysteemvolumes als het besturingssysteemvolume is versleuteld. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"> </a> Versleuteling inschakelen op een bestaande of draaiende IaaS Linux VM

In dit scenario u versleuteling inschakelen met behulp van de azure resource manager-sjabloon, PowerShell-cmdlets of Azure CLI-opdrachten. 

>[!IMPORTANT]
 >Het is verplicht om een momentopname te maken of een back-up te maken van een beheerde vm-instantie op basis van schijven buiten en voordat azure-schijfversleuteling wordt inschakeld. U een momentopname van de beheerde schijf maken vanuit de Azure-portal of azure [backup](../../backup/backup-azure-vms-encryption.md)gebruiken. Back-ups zorgen ervoor dat een hersteloptie mogelijk is in het geval van een onverwachte storing tijdens versleuteling. Nadat een back-up is gemaakt, gebruikt u de cmdlet Set-AzVMDiskEncryptionExtension om beheerde schijven te versleutelen door de parameter -skipVmBackup op te geven. De opdracht Set-AzVMDiskEncryptionExtension mislukt tegen beheerde vm's op schijf totdat een back-up is gemaakt en deze parameter is opgegeven. 
>
>Versleuteling versleutelen of uitschakelen kan ertoe leiden dat de vm opnieuw wordt opgestart. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Versleuteling inschakelen op een bestaande of draaiende Linux-VM met behulp van de Azure CLI 
U schijfversleuteling op uw versleutelde VHD inschakelen door het [Azure CLI 2.0-opdrachtregelgereedschap](/cli/azure) te installeren en te gebruiken. U kunt PowerShell in uw browser gebruiken met [Azure Cloud Shell](../../cloud-shell/overview.md), of installeren op uw lokale computer en dan gebruiken in een PowerShell-sessie. Als u versleuteling wilt inschakelen op bestaande of met IaaS Linux VM's in Azure, gebruikt u de volgende CLI-opdrachten:

Gebruik de ingeschakelde az [vm-versleuteling](/cli/azure/vm/encryption#az-vm-encryption-enable) om versleuteling in te schakelen op een met iaas-virtuele machine in Azure.

-  **Een draaiende VM versleutelen met behulp van een clientgeheim:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Versleutel een draaiende VM met KEK om het clientgeheim te verpakken:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > De syntaxis voor de waarde van de parameter schijfversleuteling-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI naar de KEK zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

- **Controleer of de schijven zijn versleuteld:** Als u de versleutelingsstatus van een IaaS VM wilt controleren, gebruikt u de opdracht [az vm-versleuteling.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Versleuteling uitschakelen:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [voor uitschakelen van AZ VM-versleuteling.](/cli/azure/vm/encryption#az-vm-encryption-disable) Het uitschakelen van versleuteling is alleen toegestaan op gegevensvolumes voor Linux VM's.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"> </a> Versleuteling inschakelen op een bestaande of draaiende Linux-VM met PowerShell
Gebruik de cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) om versleuteling in te schakelen op een met iaas-virtuele machine in Azure. Maak een [momentopname](snapshot-copy-managed-disk.md) of maak een back-up van de VM met [Azure Backup](../../backup/backup-azure-vms-encryption.md) voordat de schijven worden versleuteld. De parameter -skipVmBackup is al opgegeven in de PowerShell-scripts om een draaiende Linux-vm te versleutelen.

- **Een draaiende VM versleutelen met behulp van een clientgeheim:** Het volgende script initialiseert uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit. De brongroep, VM, key vault, Azure AD-app en clientgeheim moeten al als vereisten zijn gemaakt. Vervang MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID en My-AAD-client-secret door uw waarden. Wijzig de parameter -VolumeType om op te geven welke schijven u versleutelt.

     ```azurepowershell
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $KVRGname = 'MyKeyVaultResourceGroup';
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Versleutel een draaiende VM met KEK om het clientgeheim te verpakken:** Met Azure Disk Encryption u een bestaande sleutel in uw sleutelkluis opgeven om schijfversleutelingsgeheimen te verpakken die zijn gegenereerd terwijl versleuteling wordt in- of toegeleiden. Wanneer een sleutelversleutelingssleutel is opgegeven, gebruikt Azure Disk Encryption die sleutel om de versleutelingsgeheimen te verpakken voordat u naar de sleutelkluis schrijft. Wijzig de parameter -VolumeType om op te geven welke schijven u versleutelt. 

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > De syntaxis voor de waarde van de parameter schijfversleuteling-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br>
  De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI naar de KEK zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]. 
    
- **Controleer of de schijven zijn versleuteld:** Als u de versleutelingsstatus van een IaaS VM wilt controleren, gebruikt u de cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Schijfversleuteling uitschakelen:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Het uitschakelen van versleuteling is alleen toegestaan op gegevensvolumes voor Linux VM's.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"> </a> Versleuteling inschakelen op een bestaande of draaiende IaaS Linux VM met een sjabloon

U schijfversleuteling inschakelen op een bestaande of met IaaS Linux VM in Azure met behulp van de [sjabloon ResourceManager.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)

1. Selecteer **Implementeren naar Azure** op de Azure quickstart-sjabloon.

2. Selecteer het abonnement, de resourcegroep, de locatie van de resourcegroep, parameters, wettelijke voorwaarden en overeenkomst. Selecteer **Maken** om versleuteling in te schakelen op de bestaande of draaiende IaaS VM.

In de volgende tabel worden sjabloonparameters resourcebeheer weergegeven voor bestaande of draaiende VM's die een Azure AD-client-id gebruiken:

| Parameter | Beschrijving |
| --- | --- |
| AADClientID | Client-id van de Azure AD-toepassing met machtigingen om geheimen naar de sleutelkluis te schrijven. |
| AADClientSecret | Clientgeheim van de Azure AD-toepassing met machtigingen om geheimen te schrijven naar uw sleutelkluis. |
| keyVaultName | Naam van de sleutelkluis waarnaar de sleutel moet worden geüpload. U het krijgen met `az keyvault show --name "MySecureVault" --query KVresourceGroup`de opdracht Azure CLI. |
|  keyEncryptionKeyURL | URL van de sleutelversleutelingssleutel die wordt gebruikt om de gegenereerde sleutel te versleutelen. Deze parameter is optioneel als u **nokek** selecteert in de vervolgkeuzelijst **UseExistingKek.** Als u **kek** selecteert in de vervolgkeuzelijst **UseExistingKek,** moet u de waarde _keyEncryptionKeyURL_ invoeren. |
| volumeType | Type volume waarop de versleutelingsbewerking wordt uitgevoerd. Geldige ondersteunde waarden zijn _OS_ of _Alles_. (Zie eerder ondersteunde Linux-distributies en hun versies voor besturingssysteem- en gegevensschijven in de sectie vereisten.) |
| reeksVersie | De reeksversie van de bitlocker-bewerking. Dit versienummer verhogen telkens wanneer een schijfversleutelingsbewerking op dezelfde vm wordt uitgevoerd. |
| vmName | Naam van de VM waarop de versleutelingsbewerking moet worden uitgevoerd. |
| wachtwoordzin | Typ een sterke wachtwoordzin als de gegevensversleutelingssleutel. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>Gebruik de functie EncryptFormatAll voor gegevensschijven op Linux IaaS VM's
De parameter EncryptFormatAll verkort de tijd voor het versleutelen van Linux-gegevensschijven. Partities die aan bepaalde criteria voldoen, worden opgemaakt (met hun huidige bestandssysteem). Dan worden ze weer gemonteerd naar waar ze waren voor de executie. Als u een gegevensschijf wilt uitsluiten die aan de criteria voldoet, u deze loskoppelen voordat u de opdracht uitvoert.

 Nadat u deze opdracht hebt uitgevoerd, worden alle stations die eerder zijn gemonteerd, opgemaakt. Dan begint de versleutelingslaag bovenop het nu lege station. Wanneer deze optie is geselecteerd, wordt ook de tijdelijke resourceschijf die aan de VM is gekoppeld, versleuteld. Als het efemere station wordt gereset, wordt deze geformatteerd en opnieuw versleuteld voor de VM door de Azure Disk Encryption-oplossing bij de volgende gelegenheid.

>[!WARNING]
> EncryptFormatAll mag niet worden gebruikt wanneer er benodigde gegevens over de gegevensvolumes van een VM zijn. U schijven uitsluiten van versleuteling door ze te demonteren. Probeer eerst de parameter EncryptFormatAll op een test-VM om de functieparameter en de implicatie ervan te begrijpen voordat u deze op de productie-VM probeert. De optie EncryptFormatAll maakt de gegevensschijf op, zodat alle gegevens erop verloren gaan. Controleer voordat u verder gaat of alle schijven die u wilt uitsluiten, correct zijn verwijderd. </br></br>
 >Als u deze parameter instelt terwijl u versleutelingsinstellingen bijwerkt, kan dit leiden tot een herstart voordat de werkelijke versleuteling wordt bijgewerkt. In dit geval wilt u ook de schijf verwijderen die u niet wilt opmaken uit het fstab-bestand. Op dezelfde manier moet u de partitie die u wilt versleutelen toevoegen aan het fstab-bestand voordat u de versleutelingsbewerking start. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"> </a> EncryptFormatAll-criteria
De parameter gaat door alle partities en versleutelt ze zolang ze voldoen aan *alle* volgende criteria: 
- Is geen root/OS/boot partitie
- Is nog niet versleuteld
- Is geen BEK-volume
- Is geen RAID-volume
- Is geen LVM-volume
- Is gemonteerd

Versleutel de schijven die het RAID- of LVM-volume vormen in plaats van het RAID- of LVM-volume.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"> </a> De parameter EncryptFormatAll gebruiken met een sjabloon
Als u de optie EncryptFormatAll wilt gebruiken, gebruikt u een reeds bestaande Azure Resource Manager-sjabloon die een Linux-VM versleutelt en wijzigt u het veld **EncryptionOperation** voor de AzureDiskEncryption-bron.

1. Gebruik bijvoorbeeld de [sjabloon Resource Manager om een draaiende Linux IaaS VM te versleutelen.](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm) 
2. Selecteer **Implementeren naar Azure** op de Azure quickstart-sjabloon.
3. Wijzig het veld **EncryptionOperation** van **Versleuteling inschakelen** in **EnableEncryptionFormatAl**.
4. Selecteer het abonnement, de resourcegroep, de locatie van de resourcegroep, andere parameters, juridische voorwaarden en overeenkomst. Selecteer **Maken** om versleuteling in te schakelen op de bestaande of draaiende IaaS VM.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"> </a> De parameter EncryptFormatAll gebruiken met een PowerShell-cmdlet
Gebruik de cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) met de parameter EncryptFormatAll.

**Een draaiende VM versleutelen met behulp van een clientgeheim en EncryptFormatAll:** Als voorbeeld, het volgende script initialiseert uw variabelen en voert de Set-AzVMDiskEncryptionExtension cmdlet met de EncryptFormatAll parameter. De brongroep, VM, key vault, Azure AD-app en clientgeheim moeten al als vereisten zijn gemaakt. Vervang MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID en My-AAD-client-secret door uw waarden.
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"> </a> De parameter EncryptFormatAll gebruiken met Logische volumebeheer (LVM) 
Wij raden een LVM-on-crypt setup aan. Voor alle volgende voorbeelden, vervang het apparaat-pad en mountpoints met wat past bij uw use case. Deze setup kan als volgt worden uitgevoerd:

- Voeg de gegevensschijven toe die de VM samenstellen.
- Deze schijven opmaken, monteren en toevoegen aan het fstab-bestand.

    1. Maak de nieuw toegevoegde schijf op. We gebruiken hier symlinks gegenereerd door Azure. Het gebruik van symlinks voorkomt dat problemen met betrekking tot apparaatnamen veranderen. Zie [Problemen met apparaatnamen oplossen](troubleshoot-device-names-problems.md)voor meer informatie .
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. Monteer de schijven.
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. Toevoegen aan fstab.
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. Voer de PowerShell-cmdlet Set-AzVMDiskEncryptionExtension uit met -EncryptFormatAll om deze schijven te versleutelen.
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. Stel LVM in op deze nieuwe schijven. Let op: de versleutelde schijven worden ontgrendeld nadat de VM is voltooid met opstarten. De LVM-montage zal dus ook later moeten worden uitgesteld.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a> Nieuwe IaaS VM's gemaakt van klantversleutelde VHD en encryptiesleutels
In dit scenario u versleutelen inschakelen met behulp van de resourcebeheersjabloon, PowerShell-cmdlets of CLI-opdrachten. In de volgende secties worden de sjabloon Resourcemanager en de OPDRACHTEN CLI nader toegelicht. 

Gebruik de instructies in de bijlage voor het voorbereiden van vooraf versleutelde afbeeldingen die in Azure kunnen worden gebruikt. Nadat de afbeelding is gemaakt, u de stappen in de volgende sectie gebruiken om een versleutelde Azure-vm te maken.

* [Een vooraf versleutelde Linux VHD voorbereiden](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Het is verplicht om een momentopname te maken of een back-up te maken van een beheerde vm-instantie op basis van schijven buiten en voordat azure-schijfversleuteling wordt inschakeld. U een momentopname van de beheerde schijf maken vanaf de portal of azure [backup](../../backup/backup-azure-vms-encryption.md)gebruiken. Back-ups zorgen ervoor dat een hersteloptie mogelijk is in het geval van een onverwachte storing tijdens versleuteling. Nadat een back-up is gemaakt, gebruikt u de cmdlet Set-AzVMDiskEncryptionExtension om beheerde schijven te versleutelen door de parameter -skipVmBackup op te geven. De opdracht Set-AzVMDiskEncryptionExtension mislukt tegen beheerde vm's op schijf totdat een back-up is gemaakt en deze parameter is opgegeven. 
>
>Versleuteling versleutelen of uitschakelen kan ertoe leiden dat de vm opnieuw wordt opgestart.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"> </a> Azure PowerShell gebruiken om IaaS-VM's te versleutelen met vooraf versleutelde VHD's 
U schijfversleuteling inschakelen op uw versleutelde VHD met behulp van de PowerShell-cmdlet [Set-AzVMOSDisk.](/powershell/module/az.compute/set-azvmosdisk#examples) In het volgende voorbeeld ziet u een aantal algemene parameters. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Versleuteling inschakelen op een nieuw toegevoegde gegevensschijf
U een nieuwe gegevensschijf toevoegen door [de AZ VM-schijf te koppelen](add-disk.md) of via de [Azure-portal.](attach-disk-portal.md) Voordat u versleutelen, moet u eerst de nieuw aangesloten gegevensschijf monteren. U moet versleuteling van het gegevensstation aanvragen omdat het station onbruikbaar is terwijl de versleuteling wordt uitgevoerd. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met de Azure CLI
 Als de VM eerder is versleuteld met 'Alles', dan moet de parameter --volume-type Alles blijven. Alle omvat zowel OS en data schijven. Als de VM eerder is versleuteld met een volumetype 'OS', moet de parameter -volume-type worden gewijzigd in Alles, zodat zowel het besturingssysteem als de nieuwe gegevensschijf worden opgenomen. Als de VM is versleuteld met alleen het volumetype 'Gegevens', dan kan deze gegevens blijven zoals hier wordt gedemonstreerd. Het toevoegen en koppelen van een nieuwe gegevensschijf aan een vm is niet voldoende voorbereiding voor versleuteling. De nieuw aangesloten schijf moet ook worden opgemaakt en goed worden gemonteerd in de VM voordat u versleuteling inschakelt. Op Linux moet de schijf worden gemonteerd in /etc/fstab met een [permanente naam van blokapparaten.](troubleshoot-device-names-problems.md) 

In tegenstelling tot de syntaxis van Powershell hoeft u geen unieke sequentieversie te leveren wanneer u versleuteling inschakelt. De CLI genereert en gebruikt automatisch zijn eigen unieke volgordeversiewaarde.

-  **Een draaiende VM versleutelen met behulp van een clientgeheim:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Versleutel een draaiende VM met KEK om het clientgeheim te verpakken:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure PowerShell
 Wanneer u Powershell gebruikt om een nieuwe schijf voor Linux te versleutelen, moet een nieuwe reeksversie worden opgegeven. De sequentieversie moet uniek zijn. Het volgende script genereert een GUID voor de sequentieversie. 
 

-  **Een draaiende VM versleutelen met behulp van een clientgeheim:** Het volgende script initialiseert uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit. De brongroep, VM, key vault, Azure AD-app en clientgeheim moeten al als vereisten zijn gemaakt. Vervang MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID en My-AAD-client-secret door uw waarden. De parameter -VolumeType is ingesteld op gegevensschijven en niet op de osschijf. Als de VM eerder is versleuteld met een volumetype 'OS' of 'Alles', moet de parameter -VolumeType worden gewijzigd in Alles, zodat zowel het besturingssysteem als de nieuwe gegevensschijf worden opgenomen.

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup'; 
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **Versleutel een draaiende VM met KEK om het clientgeheim te verpakken:** Met Azure Disk Encryption u een bestaande sleutel in uw sleutelkluis opgeven om schijfversleutelingsgeheimen te verpakken die zijn gegenereerd terwijl versleuteling wordt in- of toegeleiden. Wanneer een sleutelversleutelingssleutel is opgegeven, gebruikt Azure Disk Encryption die sleutel om de versleutelingsgeheimen te verpakken voordat u naar de sleutelkluis schrijft. De parameter -VolumeType is ingesteld op gegevensschijven en niet op de osschijf. Als de VM eerder is versleuteld met een volumetype 'OS' of 'Alles', moet de parameter -VolumeType worden gewijzigd in Alles, zodat zowel het besturingssysteem als de nieuwe gegevensschijf worden opgenomen.

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $vmName = 'MyExtraSecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> De syntaxis voor de waarde van de parameter schijfversleuteling-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]. </br> </br>
De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI naar de KEK zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

## <a name="disable-encryption-for-linux-vms"></a>Versleuteling voor Linux VM's uitschakelen
U versleuteling uitschakelen met Azure PowerShell, de Azure CLI of een resourcemanagersjabloon. 

>[!IMPORTANT]
>Versleuteling uitschakelen met Azure Disk Encryption op Linux VM's wordt alleen ondersteund voor gegevensvolumes. Het wordt niet ondersteund op gegevens of besturingssysteemvolumes als het besturingssysteemvolume is versleuteld. 

- **Schijfversleuteling uitschakelen met Azure PowerShell:** Als u versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Versleuteling uitschakelen met de Azure CLI:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [voor uitschakelen van AZ VM-versleuteling.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Versleuteling uitschakelen met een resourcemanagersjabloon:** Als u versleuteling wilt uitschakelen, gebruikt u de [versleuteling uitschakelen op een draaiende Linux VM-sjabloon.](https://aka.ms/decrypt-linuxvm)
     1. Selecteer **Implementeren naar Azure**.
     2. Selecteer het abonnement, de resourcegroep, locatie, VM, juridische voorwaarden en overeenkomst.
     3. Selecteer **Kopen** om schijfversleuteling uit te schakelen op een windows-vm met windows. 


## <a name="next-steps"></a>Volgende stappen

- [Overzicht azure disk encryptie voor Linux](disk-encryption-overview-aad.md)
- [Een sleutelkluis voor Azure-schijfversleuteling maken en configureren met Azure AD (vorige release)](disk-encryption-key-vault-aad.md)
