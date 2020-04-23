---
title: Azure-schijfversleuteling met Azure AD voor Windows VM's (vorige release)
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure Disk Encryption voor Windows IaaS VM's.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 8677fa2620c1edc646dcffe120938f03fd13a0e5
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085618"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Azure-schijfversleuteling met Azure AD voor Windows VM's (vorige release)

**De nieuwe release van Azure Disk Encryption elimineert de vereiste voor het verstrekken van een Azure AD-toepassingsparameter om VM-schijfversleuteling in te schakelen. Met de nieuwe release hoeft u geen Azure AD-referenties meer op te geven tijdens de stap versleuteling inschakelen. Alle nieuwe VM's moeten worden versleuteld zonder de Azure AD-toepassingsparameters met behulp van de nieuwe versie. Zie [Azure Disk Encryption for Windows VMS](disk-encryption-windows.md)als u instructies wilt weergeven om vm-schijfversleuteling in te schakelen met de nieuwe versie. VM's die al zijn versleuteld met Azure AD-toepassingsparameters worden nog steeds ondersteund en moeten worden gehandhaafd met de syntaxis van AAD.**


U veel schijfversleutelingsscenario's inschakelen en de stappen kunnen variëren afhankelijk van het scenario. De volgende secties behandelen de scenario's in meer detail voor Windows IaaS VM's. Voordat u schijfversleuteling gebruiken, moeten de [vereisten voor Azure-schijfversleuteling](disk-encryption-overview-aad.md) worden voltooid. 


>[!IMPORTANT]
> - U moet [een momentopname maken](snapshot-copy-managed-disk.md) en/of een back-up maken voordat schijven worden versleuteld. Back-ups zorgen ervoor dat een hersteloptie mogelijk is als er een onverwachte fout optreedt tijdens versleuteling. VM's met beheerde schijven vereisen een back-up voordat versleuteling plaatsvindt. Zodra een back-up is gemaakt, u de [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) gebruiken om beheerde schijven te versleutelen door de parameter -skipVmBackup op te geven. Zie [Back-ups maken en versleutelde Azure VM herstellen](../../backup/backup-azure-vms-encryption.md)voor meer informatie over het maken van back-ups en het herstellen van versleutelde Azure VM. 
>
> - Versleuteling versleutelen of uitschakelen kan ertoe leiden dat een virtuele machine opnieuw wordt opgestart.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Versleuteling inschakelen op nieuwe IaaS VM's die zijn gemaakt vanuit de Marketplace
U schijfversleuteling inschakelen op nieuwe IaaS Windows VM vanuit de Marketplace in Azure met behulp van een Resource Manager-sjabloon. De sjabloon maakt een nieuwe versleutelde Windows VM met de windows server 2012-galerieafbeelding.

1. Klik op de [sjabloon Resourcebeheer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)op **Implementeren naar Azure**.

2. Selecteer het abonnement, de resourcegroep, de locatie van de resourcegroep, parameters, wettelijke voorwaarden en overeenkomst. Klik **op Kopen** om een nieuwe IaaS VM te implementeren waar versleuteling is ingeschakeld.

3. Nadat u de sjabloon hebt geïmplementeerd, controleert u de VM-versleutelingsstatus met de gewenste methode:
     - Controleer met de Azure CLI met de opdracht [AZ VM-versleuteling.](/cli/azure/vm/encryption#az-vm-encryption-show) 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Controleer met Azure PowerShell met de cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Selecteer de vm en klik vervolgens op **Schijven** onder het kopje Instellingen om de **versleutelingsstatus** in de portal te verifiëren. In de grafiek onder **Versleuteling**ziet u of deze is ingeschakeld. 
           ![Azure-portal - Schijfversleuteling ingeschakeld](../media/disk-encryption/disk-encryption-fig2.png)

In de volgende tabel worden de sjabloonparameters resourcebeheer voor nieuwe VM's uit het Marketplace-scenario weergegeven met Azure AD-client-id:

| Parameter | Beschrijving | 
| --- | --- |
| adminUserName | Beheerdersgebruikersnaam voor de virtuele machine. |
| adminPassword | Beheerdersgebruikerswachtwoord voor de virtuele machine. |
| newStorageAccountName | Naam van het opslagaccount om OS en gegevens-VHD's op te slaan. |
| vmSize | Grootte van de VM. Momenteel worden alleen de Standard A-, D- en G-serie ondersteund. |
| virtualNetworkName | Naam van de VNet waartoe de VM NIC behoort. |
| subnetNaam | Naam van het subnet in het VNet waartoe de VM NIC behoort. |
| AADClientID | Client-id van de Azure AD-toepassing met machtigingen om geheimen te schrijven naar uw sleutelkluis. |
| AADClientSecret | Clientgeheim van de Azure AD-toepassing met machtigingen om geheimen te schrijven naar uw sleutelkluis. |
| keyVaultURL | URL van de sleutelkluis waarnaar de BitLocker-sleutel moet worden geüpload. U het krijgen met `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` behulp van de cmdlet of de Azure CLI`az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | URL van de sleutelversleutelingssleutel die wordt gebruikt om de gegenereerde BitLocker-sleutel te versleutelen (optioneel). </br> </br>KeyEncryptionKeyURL is een optionele parameter. U uw eigen KEK meenemen om de gegevensversleutelingssleutel (Passphrase secret) in uw sleutelkluis verder te beschermen. |
| keyVaultResourceGroup | Resourcegroep van de sleutelkluis. |
| vmName | Naam van de VM waarop de versleutelingsbewerking moet worden uitgevoerd. |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a>Versleuteling inschakelen op bestaande of met IaaS Windows VM's
In dit scenario u versleuteling inschakelen met behulp van een sjabloon, PowerShell-cmdlets of CLI-opdrachten. In de volgende secties wordt nader uitgelegd hoe u Azure Disk Encryption inschakelt. 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a>Versleuteling inschakelen op bestaande of draaiende VM's met Azure PowerShell 
Gebruik de cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) om versleuteling in te schakelen op een met iaas-virtuele machine in Azure. Zie de blogberichten [Azure Disk Encryption verkennen met Azure PowerShell - Deel 1](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) voor informatie over het inschakelen van versleuteling met Azure Disk [Encryption](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)met PowerShell-cmdlets.

-  **Een draaiende VM versleutelen met een clientgeheim:** Het onderstaande script initialiseert uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit. De resourcegroep, VM, key vault, AAD-app en clientsecret hadden al als vereisten moeten zijn gemaakt. Vervang MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID en My-AAD-client-secret door uw waarden.
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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Een draaiende VM versleutelen met KEK om het clientgeheim te verpakken:** Met Azure Disk Encryption u een bestaande sleutel in uw sleutelkluis opgeven om schijfversleutelingsgeheimen te verpakken die zijn gegenereerd terwijl versleuteling wordt in- of toegeleiden. Wanneer een sleutelversleutelingssleutel is opgegeven, gebruikt Azure Disk Encryption die sleutel om de versleutelingsgeheimen te verpakken voordat u naar Key Vault gaat. 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > De syntaxis voor de waarde van de parameter schijfversleuteling-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI naar de KEK zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Controleer of de schijven versleuteld zijn:** Als u de versleutelingsstatus van een IaaS VM wilt controleren, gebruikt u de cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Schijfversleuteling uitschakelen:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a>Versleuteling inschakelen op bestaande of draaiende VM's met Azure CLI
Gebruik de ingeschakelde az [vm-versleuteling](/cli/azure/vm/encryption#az-vm-encryption-enable) om versleuteling in te schakelen op een met iaas-virtuele machine in Azure.

- **Een draaiende VM versleutelen met een clientgeheim:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Een draaiende VM versleutelen met KEK om het clientgeheim te verpakken:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > De syntaxis voor de waarde van de parameter schijfversleuteling-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI naar de KEK zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Controleer of de schijven versleuteld zijn:** Als u de versleutelingsstatus van een IaaS VM wilt controleren, gebruikt u de opdracht [az vm-versleuteling.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Versleuteling uitschakelen:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [voor uitschakelen van AZ VM-versleuteling.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>De sjabloon Resourcemanager gebruiken
U schijfversleuteling inschakelen op bestaande of met IaaS Windows VM's in Azure met behulp van de [sjabloon Resourcebeheer om een draaiende Windows-vm te versleutelen.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)


1. Klik op de Azure-snelstartsjabloon op **Implementeren naar Azure**.

2. Selecteer het abonnement, de resourcegroep, de locatie van de resourcegroep, parameters, wettelijke voorwaarden en overeenkomst. Klik **op Kopen** om versleuteling in te schakelen op de bestaande of draaiende IaaS VM.

In de volgende tabel worden de sjabloonparameters resourcebeheer weergegeven voor bestaande of draaiende VM's die een Azure AD-client-id gebruiken:

| Parameter | Beschrijving |
| --- | --- |
| AADClientID | Client-id van de Azure AD-toepassing met machtigingen om geheimen naar de sleutelkluis te schrijven. |
| AADClientSecret | Clientgeheim van de Azure AD-toepassing met machtigingen om geheimen naar de sleutelkluis te schrijven. |
| keyVaultName | Naam van de sleutelkluis waarnaar de BitLocker-sleutel moet worden geüpload. U het krijgen met `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` behulp van de cmdlet of de azure cli-`az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | URL van de sleutelversleutelingssleutel die wordt gebruikt om de gegenereerde BitLocker-sleutel te versleutelen. Deze parameter is optioneel als u **nokek** selecteert in de vervolgkeuzelijst UseExistingKek. Als u **kek** selecteert in de vervolgkeuzelijst UseExistingKek, moet u de waarde _keyEncryptionKeyURL_ invoeren. |
| volumeType | Type volume waarop de versleutelingsbewerking wordt uitgevoerd. Geldige waarden zijn _OS,_ _Data_en _Alles_. |
| reeksVersie | De reeksversie van de bitlocker-bewerking. Dit versienummer verhogen telkens wanneer een schijfversleutelingsbewerking op dezelfde vm wordt uitgevoerd. |
| vmName | Naam van de VM waarop de versleutelingsbewerking moet worden uitgevoerd. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>Nieuwe IaaS VM's gemaakt van klantversleutelde VHD en encryptiesleutels
In dit scenario u versleutelen inschakelen met behulp van de resourcebeheersjabloon, PowerShell-cmdlets of CLI-opdrachten. In de volgende secties worden de sjabloon Resourcemanager en de OPDRACHTEN CLI nader toegelicht. 

Gebruik de instructies in de bijlage voor het voorbereiden van vooraf versleutelde afbeeldingen die in Azure kunnen worden gebruikt. Nadat de afbeelding is gemaakt, u de stappen in de volgende sectie gebruiken om een versleutelde Azure-vm te maken.

* [Een vooraf versleutelde Windows VHD voorbereiden](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"> </a> VM's versleutelen met vooraf versleutelde VHD's met Azure PowerShell
U schijfversleuteling inschakelen op uw versleutelde VHD met behulp van de PowerShell-cmdlet [Set-AzVMOSDisk.](/powershell/module/az.compute/set-azvmosdisk#examples) Het onderstaande voorbeeld geeft u een aantal algemene parameters. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Versleuteling inschakelen op een nieuw toegevoegde gegevensschijf
U [een nieuwe schijf toevoegen aan een Windows-vm met PowerShell](attach-disk-ps.md)of via de [Azure-portal.](attach-managed-disk-portal.md) 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure PowerShell
 Wanneer u Powershell gebruikt om een nieuwe schijf voor Windows VM's te versleutelen, moet een nieuwe reeksversie worden opgegeven. De sequentieversie moet uniek zijn. Het onderstaande script genereert een GUID voor de sequentieversie. In sommige gevallen kan een nieuw toegevoegde gegevensschijf automatisch worden versleuteld met de Azure Disk Encryption-extensie. Automatische versleuteling treedt meestal op wanneer de VM opnieuw wordt opgestart nadat de nieuwe schijf online komt. Dit wordt meestal veroorzaakt omdat 'Alles' is opgegeven voor het volumetype wanneer schijfversleuteling eerder op de VM werd uitgevoerd. Als automatische versleuteling optreedt op een nieuw toegevoegde gegevensschijf, raden we u aan de cmdlet Set-AzVmDiskEncryptionExtension opnieuw uit te voeren met een nieuwe reeksversie. Als uw nieuwe gegevensschijf automatisch is versleuteld en u niet wilt worden versleuteld, decodeert u eerst alle stations en versleutelt vervolgens opnieuw met een nieuwe reeksversie waarin het besturingssysteem voor het volumetype wordt opgegeven. 
 

-  **Een draaiende VM versleutelen met een clientgeheim:** Het onderstaande script initialiseert uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit. De resourcegroep, VM, key vault, AAD-app en clientsecret hadden al als vereisten moeten zijn gemaakt. Vervang MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID en My-AAD-client-secret door uw waarden. In dit voorbeeld wordt 'Alles' gebruikt voor de parameter -VolumeType, die zowel besturingssysteem- als gegevensvolumes bevat. Als u alleen het besturingssysteemvolume wilt versleutelen, gebruikt u 'OS' voor de parameter -VolumeType. 

     ```azurepowershell
      $sequenceVersion = [Guid]::NewGuid();
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;
    ```
- **Een draaiende VM versleutelen met KEK om het clientgeheim te verpakken:** Met Azure Disk Encryption u een bestaande sleutel in uw sleutelkluis opgeven om schijfversleutelingsgeheimen te verpakken die zijn gegenereerd terwijl versleuteling wordt in- of toegeleiden. Wanneer een sleutelversleutelingssleutel is opgegeven, gebruikt Azure Disk Encryption die sleutel om de versleutelingsgeheimen te verpakken voordat u naar Key Vault gaat. In dit voorbeeld wordt 'Alles' gebruikt voor de parameter -VolumeType, die zowel besturingssysteem- als gegevensvolumes bevat. Als u alleen het besturingssysteemvolume wilt versleutelen, gebruikt u 'OS' voor de parameter -VolumeType. 

     ```azurepowershell
     $sequenceVersion = [Guid]::NewGuid();
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

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > De syntaxis voor de waarde van de parameter schijfversleuteling-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI naar de KEK zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure CLI
  De opdracht Azure CLI biedt u automatisch een nieuwe reeksversie wanneer u de opdracht uitvoert om versleuteling in te schakelen. Acceptabele waarden voor de parameter volumeyyype zijn Alle, OS en Gegevens. Mogelijk moet u de parameter volumetype wijzigen in besturingssysteem of gegevens als u slechts één type schijf voor de virtuele machine versleutelt. De voorbeelden gebruiken "Alles" voor de parameter volumetype. 

-  **Een draaiende VM versleutelen met een clientgeheim:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Een draaiende VM versleutelen met KEK om het clientgeheim te verpakken:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Versleuteling inschakelen met verificatie op basis van Azure AD-clientcertificaat.
U clientcertificaatverificatie gebruiken met of zonder KEK. Voordat u de PowerShell-scripts gebruikt, moet u het certificaat al naar de sleutelkluis hebben en naar de VM hebben geïmplementeerd. Als u ook KEK gebruikt, zou de KEK al moeten bestaan. Zie de sectie [Op certificaten gebaseerde verificatie voor Azure AD](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) van het artikel over de vereisten voor meer informatie.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Versleuteling inschakelen met verificatie op basis van certificaten met Azure PowerShell

```powershell
## Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$VMRGName = 'MyVirtualMachineResourceGroup'
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Versleuteling inschakelen met verificatie op basis van certificaten en een KEK met Azure PowerShell

```powershell
# Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$VMRGName = 'MyVirtualMachineResourceGroup';
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable.

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>Versleuteling uitschakelen
U versleuteling uitschakelen met Azure PowerShell, de Azure CLI of met een resourcemanagersjabloon. 

- **Schijfversleuteling uitschakelen met Azure PowerShell:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Versleuteling uitschakelen met de Azure CLI:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [voor uitschakelen van AZ VM-versleuteling.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Versleuteling uitschakelen met een resourcemanagersjabloon:** 

    1. Klik **op Implementeren naar Azure** vanuit de [schijfversleuteling uitschakelen bij het uitvoeren van windows VM-sjabloon.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)
    2. Selecteer het abonnement, de resourcegroep, locatie, VM, juridische voorwaarden en overeenkomst.
    3.  Klik **op Kopen** om schijfversleuteling uit te schakelen op een windows-vm met windows. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
