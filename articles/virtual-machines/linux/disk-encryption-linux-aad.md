---
title: Azure Disk Encryption met Azure AD App Linux IaaS-VM's (vorige versie)
description: In dit artikel vindt u instructies over het inschakelen van Microsoft Azure Disk Encryption voor Linux IaaS-VM's.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: ee365d37a957350fa8a68da0f34149d3210d6238
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970611"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Azure Disk Encryption met Azure AD inschakelen op virtuele Linux-machines (vorige versie)

De nieuwe versie van Azure Disk Encryption elimineert de vereiste voor het leveren van een Azure Active Directory (Azure AD)-toepassings parameter om VM-schijf versleuteling in te scha kelen. Met de nieuwe versie hoeft u geen Azure AD-referenties meer op te geven tijdens het inschakelen van de versleutelings stap. Alle nieuwe Vm's moeten worden versleuteld zonder de para meters van de Azure AD-toepassing met behulp van de nieuwe release. Zie [Azure Disk Encryption voor Linux-vm's](disk-encryption-linux.md)voor instructies over het inschakelen van VM-schijf versleuteling met behulp van de nieuwe versie. Vm's die al zijn versleuteld met Azure AD-toepassings parameters, worden nog steeds ondersteund en blijven behouden met de AAD-syntaxis.

U kunt veel schijf versleutelings scenario's inschakelen en de stappen kunnen variëren afhankelijk van het scenario. In de volgende secties vindt u meer informatie over de scenario's voor Linux Infrastructure as a Service (IaaS)-Vm's. U kunt alleen schijf versleuteling Toep assen op virtuele machines met [ondersteunde VM-grootten en-besturings systemen](disk-encryption-overview.md#supported-vms-and-operating-systems). U moet ook aan de volgende vereisten voldoen:

- [Aanvullende vereisten voor Vm's](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Netwerken en groepsbeleid](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Opslag vereisten voor de versleutelings sleutel](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Neem een [moment opname](snapshot-copy-managed-disk.md), maak een back-up of beide voordat u de schijven versleutelt. Back-ups zorgen ervoor dat een optie voor siteherstel mogelijk als er een onverwachte fout optreedt tijdens het versleutelen. Virtuele machines met beheerde schijven moeten u een back-up voordat versleuteling plaatsvindt. Nadat er een back-up is gemaakt, kunt u de cmdlet Set-AzVMDiskEncryptionExtension gebruiken om beheerde schijven te versleutelen door de para meter-skipVmBackup op te geven. Zie [Azure backup](../../backup/backup-azure-vms-encryption.md)voor meer informatie over het maken van back-ups en het herstellen van versleutelde vm's. 

>[!WARNING]
 > - Als u eerder Azure Disk Encryption hebt gebruikt [met de Azure AD-App voor het](disk-encryption-overview-aad.md) versleutelen van deze VM, moet u deze optie blijven gebruiken om uw virtuele machine te versleutelen. U kunt [Azure Disk Encryption](disk-encryption-overview.md) niet gebruiken op deze versleutelde virtuele machine omdat dit geen ondersteund scenario is, wat betekent dat het uitschakelen van de Azure AD-toepassing voor deze versleutelde virtuele machine nog niet wordt ondersteund.
 > - Om ervoor te zorgen dat de versleutelings geheimen geen regionale grenzen overschrijden, moeten Azure Disk Encryption de sleutel kluis en de virtuele machines in dezelfde regio naast elkaar hebben. Een sleutel kluis maken en gebruiken die zich in dezelfde regio bevinden als de virtuele machine die moet worden versleuteld.
 > - Wanneer u Linux-besturingssysteem volumes versleutelt, kan het proces enkele uren duren. Het is normaal dat de besturingssysteem volumes van Linux langer duren dan de gegevens volumes die moeten worden versleuteld.
> - Wanneer u Linux OS-volumes versleutelt, moet de virtuele machine worden beschouwd als niet-beschikbaar. We raden u ten zeerste aan om SSH-aanmeldingen te voor komen terwijl de versleuteling wordt uitgevoerd om te voor komen dat geopende bestanden worden geblokkeerd die moeten worden geopend tijdens het versleutelings proces. Als u de voortgang wilt controleren, gebruikt u de opdrachten [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) of [VM-versleutelings weergave](/cli/azure/vm/encryption#az-vm-encryption-show) . U kunt verwachten dat dit proces enkele uren in beslag neemt voor een besturingssysteem volume van 30 GB, plus extra tijd voor het versleutelen van gegevens volumes. De versleutelings tijd voor gegevens volumes is evenredig met de grootte en hoeveelheid van de gegevens volumes, tenzij de optie voor het **versleutelen** van een indeling wordt gebruikt. 
 > - Uitschakelen van versleuteling op Linux-VM's wordt alleen ondersteund voor de gegevensvolumes. Het wordt niet ondersteund op gegevens of besturingssysteem volumes als het volume van het besturings systeem is versleuteld. 

 

## <a name="bkmk_RunningLinux"></a> Versleuteling inschakelen op een bestaande of actieve IaaS Linux VM

In dit scenario kunt u versleuteling inschakelen met behulp van de Azure Resource Manager-sjabloon, Power shell-cmdlets of Azure CLI-opdrachten. 

>[!IMPORTANT]
 >Het is verplicht een moment opname te maken of een back-up te maken van een VM-exemplaar op basis van een beheerde schijf buiten en voordat u Azure Disk Encryption inschakelt. U kunt een moment opname van de beheerde schijf maken vanuit de Azure Portal, of u kunt [Azure backup](../../backup/backup-azure-vms-encryption.md)gebruiken. Back-ups zorgen ervoor dat een optie voor siteherstel mogelijk in het geval van een onverwachte fout opgetreden tijdens het versleutelen is. Nadat er een back-up is gemaakt, gebruikt u de cmdlet Set-AzVMDiskEncryptionExtension om beheerde schijven te versleutelen door de para meter-skipVmBackup op te geven. De opdracht set-AzVMDiskEncryptionExtension mislukt voor virtuele machines op basis van een schijf totdat er een back-up is gemaakt en deze para meter is opgegeven. 
>
>Het versleutelen of uitschakelen van versleuteling kan ertoe leiden dat de virtuele machine opnieuw wordt opgestart. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Versleuteling inschakelen op een bestaande of uitgevoerde Linux-VM met behulp van de Azure cli 
U kunt schijf versleuteling inschakelen op uw versleutelde VHD door het opdracht regel programma van [Azure CLI 2,0](/cli/azure) te installeren en gebruiken. U kunt PowerShell in uw browser gebruiken met [Azure Cloud Shell](../../cloud-shell/overview.md), of installeren op uw lokale computer en dan gebruiken in een PowerShell-sessie. Gebruik de volgende CLI-opdrachten voor het inschakelen van versleuteling op bestaande of IaaS virtuele Linux-machines uitvoeren in Azure:

Gebruik de opdracht [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) om versleuteling in te scha kelen op een actieve IaaS virtuele machine in Azure.

-  **Een actieve VM versleutelen met behulp van een client geheim:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Een actieve VM versleutelen met behulp van KEK om het client geheim te laten teruglopen:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > De syntaxis voor de waarde van de para meter Disk-Encryption-sleutel kluis is de volledige id-teken reeks:/Subscriptions/[abonnement-ID-GUID]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[sleutel kluis-naam].</br> </br> De syntaxis voor de waarde van de para meter Key-Encryption-Key is de volledige URI naar de KEK zoals in: https://[sleutel kluis-name]. kluis. Azure. net/Keys/[kekname]/[Kek-unique-id].

- **Controleer of de schijven zijn versleuteld:** Als u de versleutelings status van een IaaS-VM wilt controleren, gebruikt u de opdracht [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Versleuteling uitschakelen:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Als u versleuteling uitschakelt is alleen toegestaan op gegevensvolumes voor virtuele Linux-machines.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"></a> Versleuteling inschakelen op een bestaande of uitgevoerde Linux-VM met behulp van Power shell
Gebruik de cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) om versleuteling in te scha kelen op een actieve IaaS-virtuele machine in Azure. Neem een [moment opname](snapshot-copy-managed-disk.md) of maak een back-up van de virtuele machine met [Azure backup](../../backup/backup-azure-vms-encryption.md) voordat de schijven zijn versleuteld. De para meter-skipVmBackup is al opgegeven in de Power shell-scripts voor het versleutelen van een actieve Linux-VM.

- **Een actieve VM versleutelen met behulp van een client geheim:** Het volgende script initialiseert uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit. De resource groep, de VM, de sleutel kluis, de Azure AD-app en het client geheim moeten al zijn gemaakt als vereisten. Vervang MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, my-AAD-client-ID en My-AAD-client-Secret door uw waarden. Wijzig de para meter-VolumeType om op te geven welke schijven u wilt versleutelen.

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
- **Een actieve VM versleutelen met behulp van KEK om het client geheim te laten teruglopen:** Met Azure Disk Encryption kunt u een bestaande sleutel in de sleutel kluis opgeven om schijf versleutelings geheimen te verpakken die zijn gegenereerd tijdens het inschakelen van versleuteling. Wanneer een sleutel versleutelings sleutel is opgegeven, gebruikt Azure Disk Encryption die sleutel om de versleutelings geheimen op te slaan voordat u naar de sleutel kluis schrijft. Wijzig de para meter-VolumeType om op te geven welke schijven u wilt versleutelen. 

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
  > De syntaxis voor de waarde van de para meter Disk-Encryption-sleutel kluis is de volledige id-teken reeks:/Subscriptions/[abonnement-ID-GUID]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[sleutel kluis-name].</br> </br>
  De syntaxis voor de waarde van de para meter Key-Encryption-Key is de volledige URI naar de KEK zoals in: https://[sleutel kluis-name]. kluis. Azure. net/Keys/[kekname]/[Kek-unique-id]. 
    
- **Controleer of de schijven zijn versleuteld:** Gebruik de cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) om de versleutelings status van een IaaS-VM te controleren. 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Schijf versleuteling uitschakelen:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Als u versleuteling uitschakelt is alleen toegestaan op gegevensvolumes voor virtuele Linux-machines.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"></a> Versleuteling op een bestaande of actieve IaaS Linux-VM inschakelen met een sjabloon

U kunt schijf versleuteling inschakelen op een bestaande of actieve IaaS Linux-VM in azure met behulp van de [Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Selecteer **implementeren naar Azure** in de Azure Quick Start-sjabloon.

2. Selecteer het abonnement, resourcegroep, locatie voor resourcegroep, parameters, juridische voorwaarden en -overeenkomst. Selecteer **maken** om versleuteling in te scha kelen op de bestaande of actieve IaaS-VM.

De volgende tabel bevat de parameters van de Resource Manager-sjabloon voor bestaande of uitvoeren van virtuele machines die gebruikmaken van een Azure AD-client-ID:

| Parameter | Beschrijving |
| --- | --- |
| AADClientID | Client-ID van de Azure AD-toepassing met machtigingen voor geheimen schrijven naar de key vault. |
| AADClientSecret | Clientgeheim van de Azure AD-toepassing met machtigingen voor het schrijven van geheimen voor uw key vault. |
| keyVaultName | De naam van de sleutelkluis waarin de sleutel moet worden geüpload naar. U kunt deze ophalen met behulp van de Azure CLI-opdracht `az keyvault show --name "MySecureVault" --query KVresourceGroup`. |
|  KeyEncryptionKeyURL | URL van de sleutel van versleutelingssleutel die wordt gebruikt om de gegenereerde sleutel te versleutelen. Deze para meter is optioneel als u **nokek** selecteert in de vervolg keuzelijst **UseExistingKek** . Als u **Kek** selecteert in de vervolg keuzelijst **UseExistingKek** , moet u de _keyEncryptionKeyURL_ -waarde invoeren. |
| VolumeType | Het type volume dat de versleutelingsbewerking wordt uitgevoerd op. Geldige ondersteunde waarden zijn _besturings systeem_ of _alle_. (Zie Ondersteunde Linux-distributies en hun versies voor besturings systeem-en gegevens schijven in de sectie vereisten eerder.) |
| SequenceVersion | De versie van de volgorde van de BitLocker-bewerking. Dit versienummer verhoogd telkens wanneer een schijfversleuteling bewerking wordt uitgevoerd op dezelfde VM. |
| vmName | De naam van de virtuele machine die de versleutelingsbewerking moet worden uitgevoerd op. |
| wachtwoordzin | Typ een sterke wachtwoordzin in als de versleutelingssleutel voor servicegegevens. |



## <a name="bkmk_EFA"> </a>De functie EncryptFormatAll gebruiken voor gegevens schijven op virtuele Linux IaaS-machines
De para meter EncryptFormatAll vermindert de tijd voor het versleutelen van Linux-gegevens schijven. Partities die aan bepaalde criteria voldoen, worden opgemaakt (met hun huidige bestands systeem). Vervolgens worden ze opnieuw gekoppeld aan waar ze zich bevonden voordat de opdracht werd uitgevoerd. Als u een gegevens schijf wilt uitsluiten die aan de criteria voldoet, kunt u deze ontkoppelen voordat u de opdracht uitvoert.

 Nadat u deze opdracht hebt uitgevoerd, worden alle schijven die eerder zijn gekoppeld, geformatteerd. Vervolgens begint de versleutelings laag boven op het station dat nu leeg is. Als deze optie is geselecteerd, wordt de tijdelijke bron schijf die is gekoppeld aan de virtuele machine, ook versleuteld. Als het tijdelijke station opnieuw wordt ingesteld, wordt het opnieuw geformatteerd en opnieuw versleuteld voor de virtuele machine door de Azure Disk Encryption oplossing bij de volgende mogelijkheid.

>[!WARNING]
> EncryptFormatAll mag niet worden gebruikt wanneer er gegevens nodig zijn op de gegevens volumes van de virtuele machine. U kunt schijven uitsluiten van versleuteling door deze te ontkoppelen. Probeer eerst de para meter EncryptFormatAll op een VM testen om inzicht te krijgen in de para meter en de implicatie van de functie voordat u deze op de productie-VM probeert te gebruiken. De EncryptFormatAll-optie formatteert de gegevens schijf, zodat alle gegevens erop verloren gaan. Voordat u doorgaat, controleert u of alle schijven die u wilt uitsluiten, correct zijn ontkoppeld. </br></br>
 >Als u deze para meter instelt terwijl u de versleutelings instellingen bijwerkt, kan dit ertoe leiden dat de computer opnieuw wordt opgestart vóór de daad werkelijke versleuteling. In dit geval wilt u ook de schijf die u niet wilt Format teren uit het fstab-bestand verwijderen. U moet ook de partitie die u wilt versleutelen, toevoegen aan het fstab-bestand voordat u de versleutelings bewerking initieert. 

### <a name="bkmk_EFACriteria"></a> EncryptFormatAll criteria
De para meter doorloopt alle partities en versleutelt deze zolang *ze voldoen aan de volgende* criteria: 
- Is niet een hoofdmap/OS/opstartpartitie
- Is niet versleuteld
- Is geen BEK volume
- Is niet een RAID-volume
- Is niet een LVM-volume
- Is gekoppeld

Versleutel de schijven die het volume RAID- of LVM in plaats van het volume RAID- of LVM opstellen.

### <a name="bkmk_EFATemplate"></a> De para meter EncryptFormatAll gebruiken met een sjabloon
Als u de optie EncryptFormatAll wilt gebruiken, gebruikt u een bestaande Azure Resource Manager sjabloon die een virtuele Linux-machine versleutelt en wijzigt u het veld **EncryptionOperation** voor de AzureDiskEncryption-resource.

1. Gebruik bijvoorbeeld de [Resource Manager-sjabloon om een actieve Linux IaaS-VM te versleutelen](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Selecteer **implementeren naar Azure** in de Azure Quick Start-sjabloon.
3. Wijzig het veld **EncryptionOperation** van **EnableEncryption** in **EnableEncryptionFormatAl**.
4. Selecteer het abonnement, resourcegroep, locatie voor resourcegroep, andere parameters, juridische voorwaarden en -overeenkomst. Selecteer **maken** om versleuteling in te scha kelen op de bestaande of actieve IaaS-VM.


### <a name="bkmk_EFAPSH"></a> De para meter EncryptFormatAll gebruiken met een Power shell-cmdlet
Gebruik de cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) met de para meter EncryptFormatAll.

**Een actieve VM versleutelen met behulp van een client geheim en EncryptFormatAll:** Het volgende script initialiseert bijvoorbeeld uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit met de para meter EncryptFormatAll. De resource groep, de VM, de sleutel kluis, de Azure AD-app en het client geheim moeten al zijn gemaakt als vereisten. Vervang MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, my-AAD-client-ID en My-AAD-client-Secret door uw waarden.
  
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


### <a name="bkmk_EFALVM"></a> Gebruik de para meter EncryptFormatAll met Logical Volume Manager (LVM) 
U wordt aangeraden een LVM-op-crypt-installatie. Voor alle volgende voor beelden vervangt u het pad naar het apparaat en de mountpoints door een wille keurige wens. Deze instelling kan als volgt worden uitgevoerd:

- De gegevensschijven waaruit de virtuele machine toevoegen.
- Indeling, koppelen en deze schijven toevoegen aan het fstab-bestand.

    1. Formatteer de nieuw toegevoegde schijf. We gebruiken symlinks die hier worden gegenereerd door Azure. Met behulp van symlinks voorkomt u problemen met betrekking tot apparaatnamen wijzigen. Zie problemen [met apparaatnamen oplossen](troubleshoot-device-names-problems.md)voor meer informatie.
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. Koppel de schijven.
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. Toevoegen aan fstab.
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. Voer de Power shell-cmdlet Set-AzVMDiskEncryptionExtension met-EncryptFormatAll uit om deze schijven te versleutelen.
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. LVM boven op deze nieuwe schijven instellen. Houd er rekening mee dat de versleutelde schijven worden ontgrendeld nadat de virtuele machine wordt opgestart is voltooid. Het LVM-koppelen heeft dus ook later worden vertraagd.




## <a name="bkmk_VHDpre"></a> Nieuwe IaaS-vm's gemaakt op basis van door de klant versleutelde VHD-en versleutelings sleutels
In dit scenario kunt u inschakelen met behulp van de Resource Manager-sjabloon, de PowerShell-cmdlets of de CLI-opdrachten te coderen. De volgende secties wordt uitgelegd in meer detail de Resource Manager-sjabloon en de CLI-opdrachten. 

Volg de instructies in de bijlage voor het maken van vooraf gecodeerde-installatiekopieën die kunnen worden gebruikt in Azure. Nadat de installatiekopie is gemaakt, kunt u de stappen in de volgende sectie om een versleutelde Azure-VM te maken.

* [Een vooraf versleutelde Linux-VHD voorbereiden](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Het is verplicht een moment opname te maken of een back-up te maken van een VM-exemplaar op basis van een beheerde schijf buiten en voordat u Azure Disk Encryption inschakelt. U kunt een moment opname van de beheerde schijf maken vanuit de portal of u kunt [Azure backup](../../backup/backup-azure-vms-encryption.md)gebruiken. Back-ups zorgen ervoor dat een optie voor siteherstel mogelijk in het geval van een onverwachte fout opgetreden tijdens het versleutelen is. Nadat er een back-up is gemaakt, gebruikt u de cmdlet Set-AzVMDiskEncryptionExtension om beheerde schijven te versleutelen door de para meter-skipVmBackup op te geven. De opdracht set-AzVMDiskEncryptionExtension mislukt voor virtuele machines op basis van een schijf totdat er een back-up is gemaakt en deze para meter is opgegeven. 
>
>Het versleutelen of uitschakelen van versleuteling kan ertoe leiden dat de virtuele machine opnieuw wordt opgestart.



### <a name="bkmk_VHDprePSH"></a> Azure PowerShell gebruiken om IaaS vm's te versleutelen met vooraf versleutelde vhd's 
U kunt schijf versleuteling op uw versleutelde VHD inschakelen met behulp van de Power shell [-cmdlet Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). In het volgende voor beeld krijgt u enkele algemene para meters. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Schakelt u versleuteling op een nieuw toegevoegde gegevensschijf
U kunt een nieuwe gegevens schijf toevoegen met behulp van [AZ VM Disk attach](add-disk.md) of [via de Azure Portal](attach-disk-portal.md). Voordat u coderen kunt, moet u eerst de zojuist gekoppelde gegevensschijf koppelen. U moet versleuteling van het gegevens station aanvragen omdat het station niet kan worden gebruikt wanneer versleuteling wordt uitgevoerd. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met de Azure CLI
 Als de virtuele machine eerder is versleuteld met ' all ', moet de para meter--volume-type allemaal blijven. Alle bevat zowel besturingssysteem en gegevensschijven. Als de virtuele machine eerder is versleuteld met het volume type ' OS ', moet de para meter--volume-type worden gewijzigd in alle, zodat zowel het besturings systeem als de nieuwe gegevens schijf worden opgenomen. Als de virtuele machine is versleuteld met alleen het volume type ' gegevens ', kunnen er gegevens worden bewaard, zoals hier wordt gedemonstreerd. Het toevoegen en koppelen van een nieuwe gegevens schijf aan een VM is niet voldoende voor bereidingen voor versleuteling. De nieuw gekoppelde schijf moet ook worden geformatteerd en op de juiste wijze in de VM zijn gekoppeld voordat u versleuteling inschakelt. Op Linux moet de schijf worden gekoppeld in bestand/etc/fstab met een [permanente blok apparaatnaam](troubleshoot-device-names-problems.md). 

In tegens telling tot de Power shell-syntaxis vereist de CLI niet dat u een unieke volgorde versie opgeeft wanneer u versleuteling inschakelt. De CLI genereert automatisch en wordt de waarde van een eigen unieke reeks versie.

-  **Een actieve VM versleutelen met behulp van een client geheim:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Een actieve VM versleutelen met behulp van KEK om het client geheim te laten teruglopen:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Schakelt u versleuteling op een nieuw toegevoegde schijf met Azure PowerShell
 Wanneer u Power shell gebruikt om een nieuwe schijf voor Linux te versleutelen, moet u een nieuwe reeks versie opgeven. De versie van de reeks moet uniek zijn. Met het volgende script wordt een GUID voor de reeks versie gegenereerd. 
 

-  **Een actieve VM versleutelen met behulp van een client geheim:** Het volgende script initialiseert uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit. De resource groep, de VM, de sleutel kluis, de Azure AD-app en het client geheim moeten al zijn gemaakt als vereisten. Vervang MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, my-AAD-client-ID en My-AAD-client-Secret door uw waarden. De parameter - VolumeType is ingesteld op gegevensschijven en niet de besturingssysteemschijf. Als de virtuele machine eerder is versleuteld met het volume type ' OS ' of ' all ', moet de para meter-VolumeType worden gewijzigd in alle, zodat zowel het besturings systeem als de nieuwe gegevens schijf worden opgenomen.

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
- **Een actieve VM versleutelen met behulp van KEK om het client geheim te laten teruglopen:** Met Azure Disk Encryption kunt u een bestaande sleutel in de sleutel kluis opgeven om schijf versleutelings geheimen te verpakken die zijn gegenereerd tijdens het inschakelen van versleuteling. Wanneer een sleutel versleutelings sleutel is opgegeven, gebruikt Azure Disk Encryption die sleutel om de versleutelings geheimen op te slaan voordat u naar de sleutel kluis schrijft. De parameter - VolumeType is ingesteld op gegevensschijven en niet de besturingssysteemschijf. Als de virtuele machine eerder is versleuteld met het volume type ' OS ' of ' all ', moet de para meter-VolumeType worden gewijzigd in alle, zodat zowel het besturings systeem als de nieuwe gegevens schijf worden opgenomen.

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
> De syntaxis voor de waarde van de para meter Disk-Encryption-sleutel kluis is de volledige id-teken reeks:/Subscriptions/[abonnement-ID-GUID]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[sleutel kluis-naam]. </br> </br>
De syntaxis voor de waarde van de para meter Key-Encryption-Key is de volledige URI naar de KEK zoals in: https://[sleutel kluis-name]. kluis. Azure. net/Keys/[kekname]/[Kek-unique-id].

## <a name="disable-encryption-for-linux-vms"></a>Versleuteling voor Linux-VM's uitschakelen
U kunt versleuteling uitschakelen met behulp van Azure PowerShell, de Azure CLI of een resource manager-sjabloon. 

>[!IMPORTANT]
>Versleuteling met Azure Disk Encryption voor virtuele Linux-machines uitschakelen wordt alleen ondersteund voor de gegevensvolumes. Het wordt niet ondersteund op gegevens of besturingssysteem volumes als het volume van het besturings systeem is versleuteld. 

- **Schijf versleuteling uitschakelen met Azure PowerShell:** Als u versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Versleuteling uitschakelen met Azure cli:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Versleuteling uitschakelen met een resource manager-sjabloon:** Als u versleuteling wilt uitschakelen, gebruikt u de sjabloon [versleuteling uitschakelen voor een actieve Linux-VM](https://aka.ms/decrypt-linuxvm) .
     1. Selecteer **implementeren naar Azure**.
     2. Selecteer het abonnement, resourcegroep, locatie, VM, juridische voorwaarden en -overeenkomst.
     3. Selecteer **aankoop** om schijf versleuteling uit te scha kelen op een actieve Windows-VM. 


## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Disk Encryption voor Linux](disk-encryption-overview-aad.md)
- [Een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-key-vault-aad.md)
