---
title: Schakel Azure Disk Encryption voor virtuele Windows IaaS-machines
description: In dit artikel vindt u instructies over het inschakelen van Microsoft Azure Disk Encryption voor Windows IaaS-VM's.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: 109c954ab877d0b8d348fc4b9d7de01c19e41344
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958821"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>Schakel Azure Disk Encryption voor virtuele Windows IaaS-machines

In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure schijf versleuteling voor virtuele Windows IaaS-machines (Vm's). Voordat u schijf versleuteling kunt gebruiken, moet u eerst de [Azure Disk Encryption vereisten](../security/azure-security-disk-encryption-prerequisites.md)volt ooien. 

Het wordt ook ten zeerste aanbevolen om [een moment opname te maken](../virtual-machines/windows/snapshot-copy-managed-disk.md) en/of een back-up van uw schijven uit te zetten vóór versleuteling. Back-ups zorgen ervoor dat een optie voor siteherstel mogelijk als er een onverwachte fout optreedt tijdens het versleutelen. Virtuele machines met beheerde schijven moeten u een back-up voordat versleuteling plaatsvindt. Als er een back-up is gemaakt, kunt u de [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) gebruiken om beheerde schijven te versleutelen door de para meter-skipVmBackup op te geven. Zie [back-up en herstel van versleuteld Azure VM](../backup/backup-azure-vms-encryption.md) -artikel voor meer informatie over het maken van back-ups en het herstellen van versleutelde vm's.

>[!WARNING]
> - Als u eerder Azure Disk Encryption hebt gebruikt [met Azure AD-App](azure-security-disk-encryption-prerequisites-aad.md) voor het versleutelen van deze VM, moet u deze optie blijven gebruiken om uw virtuele machine te versleutelen. U kunt [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) op deze versleutelde VM niet gebruiken omdat dit geen ondersteund scenario is, wat betekent dat het uitschakelen van de Aad-toepassing voor deze versleutelde virtuele machine niet wordt ondersteund. 
> - Azure Disk Encryption moet de Key Vault en de virtuele machines in dezelfde regio worden geplaatst. Maak en gebruik van een Key Vault die zich in dezelfde regio als de virtuele machine moeten worden versleuteld. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningWinVM"></a> Schakelt u versleuteling op bestaande of het uitvoeren van IaaS-VM's voor Windows
U kunt versleuteling inschakelen met behulp van een sjabloon, Power shell-cmdlets of CLI-opdrachten. Als u schema-informatie voor de extensie van de virtuele machine nodig hebt, raadpleegt u de [Azure Disk Encryption voor Windows-extensie](../virtual-machines/extensions/azure-disk-enc-windows.md) artikel.

>[!IMPORTANT]
 > Dit is verplicht op momentopname en/of back-up een beheerde schijf op basis van de VM-exemplaar buiten en voordat Azure Disk Encryption wordt ingeschakeld. Een momentopname van de beheerde schijf kan worden uitgevoerd vanuit de portal of [Azure Backup](../backup/backup-azure-vms-encryption.md) kan worden gebruikt. Back-ups zorgen ervoor dat een optie voor siteherstel mogelijk in het geval van een onverwachte fout opgetreden tijdens het versleutelen is. Als er een back-up is gemaakt, kan de cmdlet Set-AzVMDiskEncryptionExtension worden gebruikt om beheerde schijven te versleutelen door de para meter-skipVmBackup op te geven. De opdracht set-AzVMDiskEncryptionExtension werkt niet voor virtuele machines op basis van beheerde schijven tot er een back-up is gemaakt en deze para meter is opgegeven. 
>
> Versleutelen of als u versleuteling uitschakelt kan ertoe leiden dat de virtuele machine opnieuw op te starten. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Schakelt u versleuteling op bestaande of virtuele machines uitvoeren met Azure PowerShell 
Gebruik de cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) om versleuteling in te scha kelen op een actieve IaaS-virtuele machine in Azure. 

-  **Een actieve virtuele machine versleutelen:** Het onderstaande script initialiseert uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit. De resourcegroep, de virtuele machine en de sleutelkluis moeten al zijn gemaakt voor de vereisten. Vervang MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden.

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
- **Een actieve virtuele machine met behulp van de KEK versleutelen:** 

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
   > De syntaxis voor de waarde van de schijf-versleuteling-keyvault-parameter is de volledige id-reeks: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> De syntaxis voor de waarde van de encryptiesleutel parameter is de volledige URI naar de KEK-sleutel in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Controleer of de schijven zijn versleuteld:** Gebruik de cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) om de versleutelings status van een IaaS-VM te controleren. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Schijf versleuteling uitschakelen:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Versleuteling van de gegevens schijf op een Windows-VM uitschakelen wanneer zowel het besturings systeem als de gegevens schijven zijn versleuteld, werkt niet zoals verwacht. Schakel versleuteling uit op alle schijven in plaats daarvan.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Schakelt u versleuteling op bestaande of virtuele machines uitvoeren met Azure CLI
Gebruik de [az vm encryption inschakelen](/cli/azure/vm/encryption#az-vm-encryption-enable) opdracht schakelt u versleuteling op een actieve IaaS-virtuele machine in Azure.

- **Een actieve virtuele machine versleutelen:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Een actieve virtuele machine met behulp van de KEK versleutelen:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > De syntaxis voor de waarde van de schijf-versleuteling-keyvault-parameter is de volledige id-reeks: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> De syntaxis voor de waarde van de encryptiesleutel parameter is de volledige URI naar de KEK-sleutel in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Controleer of de schijven zijn versleuteld:** Als u de versleutelings status van een IaaS-VM wilt controleren, gebruikt u de opdracht [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Versleuteling uitschakelen:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Versleuteling van de gegevens schijf op een Windows-VM uitschakelen wanneer zowel het besturings systeem als de gegevens schijven zijn versleuteld, werkt niet zoals verwacht. Schakel versleuteling uit op alle schijven in plaats daarvan.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
 
  > [!NOTE]
  >Dit is verplicht op momentopname en/of back-up een beheerde schijf op basis van de VM-exemplaar buiten en voordat Azure Disk Encryption wordt ingeschakeld. Een momentopname van de beheerde schijf kan worden uitgevoerd vanuit de portal of [Azure Backup](../backup/backup-azure-vms-encryption.md) kan worden gebruikt. Back-ups zorgen ervoor dat een optie voor siteherstel mogelijk in het geval van een onverwachte fout opgetreden tijdens het versleutelen is. Als er een back-up is gemaakt, kan de cmdlet Set-AzVMDiskEncryptionExtension worden gebruikt om beheerde schijven te versleutelen door de para meter-skipVmBackup op te geven. Met deze opdracht uitvoeren op basis van virtuele machines op beheerde schijven op basis van totdat een back-up is gemaakt en deze parameter is opgegeven. 
  >
  >Versleutelen of als u versleuteling uitschakelt kan ertoe leiden dat de virtuele machine opnieuw op te starten. 

### <a name="bkmk_RunningWinVMwRM"> </a>Met behulp van de Resource Manager-sjabloon

U kunt inschakelen schijfversleuteling voor de bestaande of IaaS Windows-VM's uitvoeren in Azure met behulp van de [Resource Manager-sjabloon voor het versleutelen van een actieve Windows-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. Klik op de Azure-quickstart-sjabloon, **implementeren in Azure**.

2. Selecteer het abonnement, resourcegroep, locatie, instellingen, juridische voorwaarden en -overeenkomst. Klik op **aankoop** versleuteling op de bestaande of actieve IaaS-VM inschakelen.

De volgende tabel bevat de Resource Manager-Sjabloonparameters voor bestaande of uitvoeren van virtuele machines:

| Parameter | Description |
| --- | --- |
| vmName | De naam van de virtuele machine om uit te voeren van de versleutelingsbewerking. |
| keyVaultName | De naam van de sleutelkluis waarin de BitLocker-sleutel moet worden geüpload naar. U kunt deze ophalen met behulp van de cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` of de Azure CLI-opdracht `az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | Naam van de resourcegroep waarin de key vault|
|  KeyEncryptionKeyURL | De URL van de Key Encryption Key, in de notatie https://@no__t -0keyvault-name&gt;.vault.azure.net/key/&lt;key-name @ no__t-3. Als u geen KEK wilt gebruiken, laat u dit veld leeg. |
| VolumeType | Het type volume dat de versleutelingsbewerking wordt uitgevoerd op. Geldige waarden zijn _OS_, _gegevens_, en _alle_. 
| forceUpdateTag | In een unieke waarde, zoals een GUID doorgeven telkens wanneer de bewerking moet worden geforceerd uitvoeren. |
| resizeOSDisk | Moet de OS-partitie worden kleiner gemaakt zodat ze volledig OS VHD voordat het systeemvolume splitsen in beslag nemen. |
| location | Locatie voor alle resources. |

## <a name="encrypt-virtual-machine-scale-sets"></a>Versleutelen van de virtuele-machineschaalsets

[Azure virtuele-machineschaalsets](../virtual-machine-scale-sets/overview.md) kunt u maken en beheren van een groep identieke, met gelijke taakverdeling VM's worden geladen. Het aantal VM-exemplaren kan automatisch toenemen of afnemen in reactie op vraag of een ingesteld schema. De CLI of Azure PowerShell gebruiken voor het versleutelen van virtuele-machineschaalsets.

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Versleutelen van de virtuele-machineschaalsets met Azure PowerShell

Gebruik de cmdlet [set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/set-azvmssdiskencryptionextension) om versleuteling in te scha kelen voor een schaalset voor virtuele Windows-machines. De resource groep, de schaalset voor virtuele machines en de sleutel kluis moeten al zijn gemaakt als vereisten.

-  **Versleutelen van een actieve virtuele-machineschaalset**:
    ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:

    ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $KeyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     Set-AzRmVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $KeyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

   >[!NOTE]
   > De syntaxis voor de waarde van de schijf-versleuteling-keyvault-parameter is de volledige id-reeks: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> De syntaxis voor de waarde van de encryptiesleutel parameter is de volledige URI naar de KEK-sleutel in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Versleutelings status ophalen voor een schaalset voor virtuele machines:** Gebruik de cmdlet [Get-AzVmssDiskEncryption](/powershell/module/az.compute/get-azvmssdiskencryption) .
    
    ```azurepowershell-interactive
    get-AzVmssVMDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

- **Versleuteling uitschakelen voor een schaalset voor virtuele machines**: Gebruik de cmdlet [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/disable-azvmssdiskencryption) . 

    ```azurepowershell-interactive
    Disable-AzVmssDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Versleutelen van de virtuele-machineschaalsets met Azure CLI

Gebruik de [az vmss versleuteling inschakelen](/cli/azure/vmss/encryption#az-vmss-encryption-enable) versleuteling op een Windows-virtuele-machineschaalset inschakelen. Als u het beleid voor upgrades op de schaal die is ingesteld op handmatig hebt ingesteld, start u de codering met [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). De resource groep, de schaalset voor virtuele machines en de sleutel kluis moeten al zijn gemaakt als vereisten.

-  **Versleutelen van een actieve virtuele-machineschaalset**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **Versleutelen van een actieve virtuele-machineschaalset met behulp van de KEK-sleutel voor de sleutel Inpakken**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
     
   >[!NOTE]
   > De syntaxis voor de waarde van de schijf-versleuteling-keyvault-parameter is de volledige id-reeks: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> De syntaxis voor de waarde van de encryptiesleutel parameter is de volledige URI naar de KEK-sleutel in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Versleutelings status ophalen voor een schaalset voor virtuele machines:** Gebruiken [AZ vmss Encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
     az vmss encryption show --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

- **Versleuteling uitschakelen voor een schaalset voor virtuele machines**: Gebruiken [AZ vmss Encryption Disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-windows-virtual-machine-scale-sets"></a>Azure Resource Manager sjablonen voor schaal sets voor virtuele Windows-machines

Als u virtuele-machine schaal sets voor Windows wilt versleutelen of ontsleutelen, gebruikt u de volgende Azure Resource Manager sjablonen en instructies:

- [Versleuteling inschakelen op een Windows-schaalset voor virtuele machines](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)
- [Versleuteling uitschakelen voor een Windows-schaalset voor virtuele machines](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

     1. Klik op **Implementeren in Azure**.
     2. Vul de vereiste velden in en ga akkoord met de voor waarden.
     3. Klik op **kopen** om de sjabloon te implementeren.

## <a name="bkmk_VHDpre"> </a> Nieuwe IaaS VM's die worden gemaakt op basis van de klant versleuteld VHD en versleuteling sleutels

In dit scenario kunt u inschakelen met behulp van PowerShell-cmdlets of de CLI-opdrachten te coderen. 

Volg de instructies in de bijlage voor het maken van vooraf gecodeerde-installatiekopieën die kunnen worden gebruikt in Azure. Nadat de installatiekopie is gemaakt, kunt u de stappen in de volgende sectie om een versleutelde Azure-VM te maken.

* [Vooraf gecodeerde Windows VHD voorbereiden](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Vooraf gecodeerde Linux-VHD voorbereiden](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Dit is verplicht op momentopname en/of back-up een beheerde schijf op basis van de VM-exemplaar buiten en voordat Azure Disk Encryption wordt ingeschakeld. Een momentopname van de beheerde schijf kan worden uitgevoerd vanuit de portal of [Azure Backup](../backup/backup-azure-vms-encryption.md) kan worden gebruikt. Back-ups zorgen ervoor dat een optie voor siteherstel mogelijk in het geval van een onverwachte fout opgetreden tijdens het versleutelen is. Als er een back-up is gemaakt, kan de cmdlet Set-AzVMDiskEncryptionExtension worden gebruikt om beheerde schijven te versleutelen door de para meter-skipVmBackup op te geven. De opdracht set-AzVMDiskEncryptionExtension werkt niet voor virtuele machines op basis van beheerde schijven tot er een back-up is gemaakt en deze para meter is opgegeven. 
>
>Versleutelen of als u versleuteling uitschakelt kan ertoe leiden dat de virtuele machine opnieuw op te starten. 


### <a name="bkmk_VHDprePSH"> </a> Versleutelen van VM's met vooraf gecodeerde VHD's met Azure PowerShell
U kunt schijf versleuteling op uw versleutelde VHD inschakelen met behulp van de Power shell [-cmdlet Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). Het onderstaande voorbeeld hebt u enkele algemene parameters. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Schakelt u versleuteling op een nieuw toegevoegde gegevensschijf
U kunt [een nieuwe schijf toevoegen aan een Windows-VM met behulp van PowerShell](../virtual-machines/windows/attach-disk-ps.md), of [via Azure portal](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Schakelt u versleuteling op een nieuw toegevoegde schijf met Azure PowerShell
 Wanneer u Powershell gebruikt voor het versleutelen van een nieuwe schijf voor Windows-VM's, kan een nieuwe versie van de takenreeks moet worden opgegeven. De versie van de reeks moet uniek zijn. Het onderstaande script genereert een GUID voor de versie van de reeks. In sommige gevallen kan een schijf met toegevoegde gegevens automatisch worden versleuteld door de Azure Disk Encryption-extensie. Automatische versleuteling treedt meestal op wanneer de virtuele machine opnieuw wordt opgestart nadat de nieuwe schijf online komt. Dit wordt meestal veroorzaakt doordat 'Alle' is opgegeven voor het volumetype wanneer schijfversleuteling is eerder is uitgevoerd op de virtuele machine. Als Automatische versleuteling op een nieuw toegevoegde gegevens schijf plaatsvindt, raden we u aan om de cmdlet Set-AzVmDiskEncryptionExtension opnieuw uit te voeren met een nieuwe reeks versie. Als uw nieuwe gegevensschijf automatisch versleuteld is en u niet wenst dat moeten worden versleuteld, alle stations eerst ontsleutelen en opnieuw versleutelen met een nieuwe reeks versie besturingssysteem op te geven voor het volumetype. 
  
 

-  **Een actieve virtuele machine versleutelen:** Het onderstaande script initialiseert uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit. De resourcegroep, de virtuele machine en de sleutelkluis moeten al zijn gemaakt voor de vereisten. Vervang MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden. In dit voorbeeld maakt gebruik van 'Alle' voor de parameter - VolumeType, waaronder zowel besturingssysteem en volumes. Als u wilt dat alleen voor het versleutelen van het volume met het besturingssysteem, gebruikt u 'BS' voor de parameter - VolumeType. 

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
- **Een actieve virtuele machine met behulp van de KEK versleutelen:** In dit voorbeeld maakt gebruik van 'Alle' voor de parameter - VolumeType, waaronder zowel besturingssysteem en volumes. Als u wilt dat alleen voor het versleutelen van het volume met het besturingssysteem, gebruikt u 'BS' voor de parameter - VolumeType.

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
    > De syntaxis voor de waarde van de schijf-versleuteling-keyvault-parameter is de volledige id-reeks: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> De syntaxis voor de waarde van de encryptiesleutel parameter is de volledige URI naar de KEK-sleutel in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Schakelt u versleuteling op een nieuw toegevoegde schijf met Azure CLI
 De Azure CLI-opdracht wordt automatisch een nieuwe versie van de reeks voor u opgeeft bij het uitvoeren van de opdracht voor het inschakelen van versleuteling. Het voorbeeld maakt gebruik van 'Alle' voor de parameter volumetype. Mogelijk moet u wijzigt u de parameter volumetype naar van het besturingssysteem als u alleen voor de besturingssysteemschijf versleutelen bent. In tegenstelling tot de Powershell-syntaxis vereist de CLI niet de gebruiker voor de versie van een unieke reeks bij het inschakelen van versleuteling. De CLI genereert automatisch en wordt de waarde van een eigen unieke reeks versie.   

-  **Een actieve virtuele machine versleutelen:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Een actieve virtuele machine met behulp van de KEK versleutelen:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Schakel versleuteling uit
U kunt versleuteling met Azure PowerShell, de Azure CLI, uitschakelen of met een Resource Manager-sjabloon. Versleuteling van de gegevens schijf op een Windows-VM uitschakelen wanneer zowel het besturings systeem als de gegevens schijven zijn versleuteld, werkt niet zoals verwacht. Schakel versleuteling uit op alle schijven in plaats daarvan.

- **Schijf versleuteling uitschakelen met Azure PowerShell:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Versleuteling uitschakelen met Azure CLI:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Schakel versleuteling met Resource Manager-sjabloon:** 

    1. Klik op **implementeren in Azure** uit de [schijfversleuteling voor het uitvoeren van Windows-VM uitschakelen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) sjabloon.
    2. Selecteer het abonnement, resourcegroep, locatie, VM, volumetype, juridische voorwaarden en -overeenkomst.
    3.  Klik op **aankoop** schijfversleuteling op een actieve Windows-virtuele machine uitschakelen. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Schakel Azure Disk Encryption voor Linux](azure-security-disk-encryption-linux.md)
