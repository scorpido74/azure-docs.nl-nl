---
title: Azure Disk Encryption-scenario's voor Windows-VM's
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure Disk Encryption voor Windows VM's voor verschillende scenario's
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: ed64ee3d0e024c32be08ed4e010a6933033c3f87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476515"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Azure Disk Encryption-scenario's voor Windows-VM's

Azure Disk Encryption maakt gebruik van de Externe sleutelbeveiliging van BitLocker om volumeversleuteling te bieden voor het besturingssysteem en de gegevensschijven van Virtuele Azure-machines (VM's) en is geïntegreerd met Azure Key Vault om u te helpen de schijfversleutelingssleutels en -geheimen te beheren en te beheren. Zie Azure Disk Encryption [for Windows VM's voor](disk-encryption-overview.md)een overzicht van de service.

Er zijn veel scenario's voor schijfversleuteling en de stappen kunnen variëren afhankelijk van het scenario. De volgende secties behandelen de scenario's in meer detail voor Windows VM's.

U schijfversleuteling alleen toepassen op virtuele machines met [ondersteunde VM-formaten en besturingssystemen.](disk-encryption-overview.md#supported-vms-and-operating-systems) U moet ook aan de volgende voorwaarden voldoen:

- [Netwerkvereisten](disk-encryption-overview.md#networking-requirements)
- [Vereisten voor groepsbeleid](disk-encryption-overview.md#group-policy-requirements)
- [Vereisten voor opslag van versleutelingssleutels](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Als u azure-schijfversleuteling eerder met Azure AD hebt gebruikt om een vm te versleutelen, moet u deze optie blijven gebruiken om uw vm te versleutelen. Zie [Azure Disk Encryption with Azure AD (vorige release)](disk-encryption-overview-aad.md) voor meer informatie. 
>
> - U moet [een momentopname maken](snapshot-copy-managed-disk.md) en/of een back-up maken voordat schijven worden versleuteld. Back-ups zorgen ervoor dat een hersteloptie mogelijk is als er een onverwachte fout optreedt tijdens versleuteling. VM's met beheerde schijven vereisen een back-up voordat versleuteling plaatsvindt. Zodra een back-up is gemaakt, u de [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) gebruiken om beheerde schijven te versleutelen door de parameter -skipVmBackup op te geven. Zie [Back-ups maken en versleutelde Azure VM herstellen](../../backup/backup-azure-vms-encryption.md)voor meer informatie over het maken van back-ups en het herstellen van versleutelde Azure VM. 
>
> - Versleuteling versleutelen of uitschakelen kan ertoe leiden dat een virtuele machine opnieuw wordt opgestart.

## <a name="install-tools-and-connect-to-azure"></a>Hulpprogramma's installeren en verbinding maken met Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Versleuteling inschakelen op een bestaande of draaiende Windows-vm
In dit scenario u versleuteling inschakelen met behulp van de resourcebeheersjabloon, PowerShell-cmdlets of CLI-opdrachten. Zie het artikel [Azure Disk Encryption for Windows extension](../extensions/azure-disk-enc-windows.md) als u schema-informatie nodig hebt voor de extensie virtuele machine.

## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a>Versleuteling inschakelen op bestaande of met IaaS Windows VM's
U versleuteling inschakelen met behulp van een sjabloon, PowerShell-cmdlets of CLI-opdrachten. Zie het artikel [Azure Disk Encryption for Windows extension](../extensions/azure-disk-enc-windows.md) als u schema-informatie nodig hebt voor de extensie virtuele machine.

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Versleuteling inschakelen op bestaande of draaiende VM's met Azure PowerShell 
Gebruik de cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) om versleuteling in te schakelen op een met iaas-virtuele machine in Azure. 

-  **Een draaiende VM versleutelen:** Het onderstaande script initialiseert uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit. De resourcegroep, VM en sleutelkluis moeten al als vereisten zijn gemaakt. Vervang MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Een draaiende VM versleutelen met KEK:** 

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

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > De syntaxis voor de waarde van de parameter schijfversleuteling-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI naar de KEK zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Controleer of de schijven versleuteld zijn:** Als u de versleutelingsstatus van een IaaS VM wilt controleren, gebruikt u de cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Schijfversleuteling uitschakelen:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Het uitschakelen van gegevensschijfversleuteling voor de Windows-VM wanneer zowel de besturingssysteem als gegevensschijven zijn versleuteld, werkt niet zoals verwacht. Schakel in plaats daarvan versleuteling op alle schijven uit.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Versleuteling inschakelen op bestaande of draaiende VM's met de Azure CLI
Gebruik de ingeschakelde az [vm-versleuteling](/cli/azure/vm/encryption#az-vm-encryption-enable) om versleuteling in te schakelen op een met iaas-virtuele machine in Azure.

- **Een draaiende VM versleutelen:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Een draaiende VM versleutelen met KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > De syntaxis voor de waarde van de parameter schijfversleuteling-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI naar de KEK zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Controleer of de schijven versleuteld zijn:** Als u de versleutelingsstatus van een IaaS VM wilt controleren, gebruikt u de opdracht [az vm-versleuteling.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Versleuteling uitschakelen:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [voor uitschakelen van AZ VM-versleuteling.](/cli/azure/vm/encryption#az-vm-encryption-disable) Het uitschakelen van gegevensschijfversleuteling voor de Windows-VM wanneer zowel de besturingssysteem als gegevensschijven zijn versleuteld, werkt niet zoals verwacht. Schakel in plaats daarvan versleuteling op alle schijven uit.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>De sjabloon Resourcemanager gebruiken

U schijfversleuteling inschakelen op bestaande of met IaaS Windows VM's in Azure met behulp van de [sjabloon Resourcebeheer om een draaiende Windows-vm te versleutelen.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)


1. Klik op de Azure-snelstartsjabloon op **Implementeren naar Azure**.

2. Selecteer het abonnement, de brongroep, locatie, instellingen, juridische voorwaarden en overeenkomst. Klik **op Kopen** om versleuteling in te schakelen op de bestaande of draaiende IaaS VM.

In de volgende tabel worden de sjabloonparameters resourcebeheer voor bestaande of draaiende VM's weergegeven:

| Parameter | Beschrijving |
| --- | --- |
| vmName | Naam van de VM om de versleutelingsbewerking uit te voeren. |
| keyVaultName | Naam van de sleutelkluis waarnaar de BitLocker-sleutel moet worden geüpload. U het krijgen met `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` behulp van de cmdlet of de azure cli-`az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | Naam van de resourcegroep die de sleutelkluis bevat|
|  keyEncryptionKeyURL | De URL van de sleutelversleutelingssleutel&lt;in de&gt;indeling&lt;https://&gt;keyvault-naam .vault.azure.net/key/ sleutelnaam . Als u geen KEK wilt gebruiken, laat dit veld dan leeg. |
| volumeType | Type volume waarop de versleutelingsbewerking wordt uitgevoerd. Geldige waarden zijn _OS,_ _Data_en _Alles_. 
| forceUpdateTag | Geef een unieke waarde als een GUID door elke keer dat de bewerking moet worden uitgevoerd. |
| formaat OSDisk wijzigen | Moet de OS-partitie worden aangepast aan volledige OS VHD bezetten voordat splitsen systeemvolume. |
| location | Locatie voor alle bronnen. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nieuwe IaaS VM's gemaakt van klantversleutelde VHD en encryptiesleutels

In dit scenario u een nieuwe VM maken op basis van een vooraf versleutelde VHD en de bijbehorende versleutelingssleutels met PowerShell-cmdlets of CLI-opdrachten. 

Gebruik de instructies in [Een vooraf versleutelde Windows VHD voorbereiden.](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd) Nadat de afbeelding is gemaakt, u de stappen in de volgende sectie gebruiken om een versleutelde Azure-vm te maken.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>VM's versleutelen met vooraf versleutelde VHD's met Azure PowerShell
U schijfversleuteling inschakelen op uw versleutelde VHD met behulp van de PowerShell-cmdlet [Set-AzVMOSDisk.](/powershell/module/az.compute/set-azvmosdisk#examples) Het onderstaande voorbeeld geeft u een aantal algemene parameters. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Versleuteling inschakelen op een nieuw toegevoegde gegevensschijf
U [een nieuwe schijf toevoegen aan een Windows-vm met PowerShell](attach-disk-ps.md)of via de [Azure-portal.](attach-managed-disk-portal.md) 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure PowerShell
 Wanneer u Powershell gebruikt om een nieuwe schijf voor Windows VM's te versleutelen, moet een nieuwe reeksversie worden opgegeven. De sequentieversie moet uniek zijn. Het onderstaande script genereert een GUID voor de sequentieversie. In sommige gevallen kan een nieuw toegevoegde gegevensschijf automatisch worden versleuteld met de Azure Disk Encryption-extensie. Automatische versleuteling treedt meestal op wanneer de VM opnieuw wordt opgestart nadat de nieuwe schijf online komt. Dit wordt meestal veroorzaakt omdat 'Alles' is opgegeven voor het volumetype wanneer schijfversleuteling eerder op de VM werd uitgevoerd. Als automatische versleuteling optreedt op een nieuw toegevoegde gegevensschijf, raden we u aan de cmdlet Set-AzVmDiskEncryptionExtension opnieuw uit te voeren met een nieuwe reeksversie. Als uw nieuwe gegevensschijf automatisch is versleuteld en u niet wilt worden versleuteld, decodeert u eerst alle stations en versleutelt vervolgens opnieuw met een nieuwe reeksversie waarin het besturingssysteem voor het volumetype wordt opgegeven. 
  
 

-  **Een draaiende VM versleutelen:** Het onderstaande script initialiseert uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit. De resourcegroep, VM en sleutelkluis moeten al als vereisten zijn gemaakt. Vervang MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden. In dit voorbeeld wordt 'Alles' gebruikt voor de parameter -VolumeType, die zowel besturingssysteem- als gegevensvolumes bevat. Als u alleen het besturingssysteemvolume wilt versleutelen, gebruikt u 'OS' voor de parameter -VolumeType. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Een draaiende VM versleutelen met KEK:** In dit voorbeeld wordt 'Alles' gebruikt voor de parameter -VolumeType, die zowel besturingssysteem- als gegevensvolumes bevat. Als u alleen het besturingssysteemvolume wilt versleutelen, gebruikt u 'OS' voor de parameter -VolumeType.

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

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > De syntaxis voor de waarde van de parameter schijfversleuteling-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI naar de KEK zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure CLI
 De opdracht Azure CLI biedt u automatisch een nieuwe reeksversie wanneer u de opdracht uitvoert om versleuteling in te schakelen. In het voorbeeld wordt 'Alles' gebruikt voor de parameter volumetype. Mogelijk moet u de parameter volumetype in besturingssysteem wijzigen als u alleen de osschijf versleutelt. In tegenstelling tot de syntaxis van Powershell vereist de CLI niet dat de gebruiker een unieke sequentieversie levert wanneer de versleuteling wordt geactiveerd. De CLI genereert en gebruikt automatisch zijn eigen unieke volgordeversiewaarde.   

-  **Een draaiende VM versleutelen:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Een draaiende VM versleutelen met KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Versleuteling uitschakelen
U versleuteling uitschakelen met Azure PowerShell, de Azure CLI of met een resourcemanagersjabloon. Het uitschakelen van gegevensschijfversleuteling voor de Windows-VM wanneer zowel de besturingssysteem als gegevensschijven zijn versleuteld, werkt niet zoals verwacht. Schakel in plaats daarvan versleuteling op alle schijven uit.

- **Schijfversleuteling uitschakelen met Azure PowerShell:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Versleuteling uitschakelen met de Azure CLI:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [voor uitschakelen van AZ VM-versleuteling.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Versleuteling uitschakelen met een resourcemanagersjabloon:** 

    1. Klik **op Implementeren naar Azure** vanuit de [schijfversleuteling uitschakelen bij het uitvoeren van windows VM-sjabloon.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
    2. Selecteer het abonnement, de resourcegroep, locatie, VM, volumetype, juridische voorwaarden en overeenkomst.
    3.  Klik **op Kopen** om schijfversleuteling uit te schakelen op een windows-vm met windows. 

## <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

Azure Disk Encryption werkt niet voor de volgende scenario's, functies en technologie:

- Versleutelen van basislaagVM's of VM's die zijn gemaakt via de klassieke methode voor het maken van vm's.
- Vm's versleutelen die zijn geconfigureerd met softwaregebaseerde RAID-systemen.
- Vm's versleutelen die zijn geconfigureerd met Storage Spaces Direct (S2D) of Windows Server-versies vóór 2016 geconfigureerd met Windows-opslagruimten.
- Integratie met een on-premises sleutelbeheersysteem.
- Azure Files (gedeeld bestandssysteem).
- Network File System (NFS).
- Dynamische volumes.
- Windows Server-containers, die dynamische volumes voor elke container maken.
- Kortstondige OS-schijven.
- Versleuteling van gedeelde/gedistribueerde bestandssystemen zoals (maar niet beperkt tot) DFS, GFS, DRDB en CephFS.
- Een versleutelde VM's verplaatsen naar een ander abonnement.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
- [Voorbeeldscripts voor Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Problemen met Azure Disk Encryption oplossen](disk-encryption-troubleshooting.md)
