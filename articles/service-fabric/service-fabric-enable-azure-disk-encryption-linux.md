---
title: Schijfversleuteling inschakelen voor Linux-clusters
description: In dit artikel wordt beschreven hoe u schijfversleuteling voor Azure Service Fabric-clusterknooppunten in Linux inschakelen met Azure Resource Manager en Azure Key Vault.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: c600d822d20b0e5a0ca613935b1dfa4be838fcec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252819"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Schijfversleuteling inschakelen voor Azure Service Fabric-clusterknooppunten in Linux 
> [!div class="op_single_selector"]
> * [Schijfversleuteling voor Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Schijfversleuteling voor Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

In deze zelfstudie leert u hoe u schijfversleuteling inschakelt op Azure Service Fabric-clusterknooppunten in Linux. U moet deze stappen volgen voor elk knooppunttype en virtuele machineschaalsets. Voor het versleutelen van de knooppunten gebruiken we de azure disk encryptie-mogelijkheden op virtuele machine schaalsets.

De gids behandelt de volgende onderwerpen:

* Belangrijke concepten om op de hoogte te zijn van het inschakelen van schijfversleuteling op servicefabricclusterclusterdetectiesets in Linux.
* Stappen die moeten worden gevolgd voordat schijfversleuteling op servicefabric-clusterknooppunten in Linux wordt ingeschakeld.
* Stappen die moeten worden gevolgd om schijfversleuteling in te schakelen op clusterknooppunten van Service Fabric in Linux.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

 **Zelfregistratie**

De schijfversleutelingspreview voor de virtuele machineschaalset vereist zelfregistratie. Voer de volgende stappen uit:

1. Voer de volgende opdracht uit: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Wacht ongeveer 10 minuten tot de status *Geregistreerd*staat. U de status controleren door de volgende opdracht uit te voeren:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault**

1. Maak een sleutelkluis in hetzelfde abonnement en dezelfde regio als de schaalset. Selecteer vervolgens het toegangsbeleid **voor toegang tot enabledfordiskversleuteling** op de sleutelkluis met behulp van de PowerShell-cmdlet. U het beleid ook instellen met de Key Vault-gebruikersinterface in de Azure-portal met de volgende opdracht:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Installeer de nieuwste versie van de [Azure CLI](/cli/azure/install-azure-cli), die de nieuwe encryptie commando's heeft.

3. Installeer de nieuwste versie van de [Azure SDK van Azure PowerShell](https://github.com/Azure/azure-powershell/releases) release. Hieronder volgen de cmdlets van de virtuele machineschaal azure disk encryptie om versleuteling in te schakelen ([instellen)](/powershell/module/az.compute/set-azvmssdiskencryptionextension)in te schakelen, de versleutelingsstatus op te halen[(ophalen)](/powershell/module/az.compute/get-azvmssvmdiskencryption)en versleuteling op de schaalsetinstantie te verwijderen ([uitschakelen).](/powershell/module/az.compute/disable-azvmssdiskencryption)


| Opdracht | Versie |  Bron  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 of hoger | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 of hoger | Az.Compute |
| Disable-AzVmssDiskEncryption Disable-AzVmssDiskEncryption   | 1.0.0 of hoger | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 of hoger | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 of hoger | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 of hoger | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Ondersteunde scenario's voor schijfversleuteling
* Versleuteling voor virtuele machineschaalsets wordt alleen ondersteund voor schaalsets die zijn gemaakt met beheerde schijven. Het wordt niet ondersteund voor native (of onbeheerde) schijfwaardensets.
* Zowel versleuteling als uitschakelen van versleuteling worden ondersteund voor OS- en gegevensvolumes in virtuele machineschaalsets in Linux. 
* Virtuele machine (VM) reimage en upgrade operaties voor virtuele machine schaal sets worden niet ondersteund in de huidige preview.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Een nieuw cluster maken en schijfversleuteling inschakelen

Gebruik de volgende opdrachten om een cluster te maken en schijfversleuteling in te schakelen met behulp van een Azure Resource Manager-sjabloon en een zelfondertekend certificaat.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure  

Meld u aan met de volgende opdrachten:

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>De aangepaste sjabloon gebruiken die u al hebt 

Als u een aangepaste sjabloon wilt maken, raden we u ten zeerste aan een van de sjablonen te gebruiken op de pagina Sjabloonvoorbeelden voor het maken van [azure-servicesfabric.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) 

Als u al een aangepaste sjabloon hebt, controleert u of alle drie de certificaatgerelateerde parameters in de sjabloon en het parameterbestand als volgt worden genoemd. Zorg er ook voor dat de waarden als volgt niet worden vermeld:

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Omdat alleen versleuteling van gegevensschijven wordt ondersteund voor virtuele machineschaalsets in Linux, moet u een gegevensschijf toevoegen met behulp van een resourcemanagersjabloon. Werk uw sjabloon als volgt bij voor het opmaken van gegevensschijven:

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
```
 

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Hier is de gelijkwaardige CLI commando. Wijzig de waarden in de declarestatements in de juiste waarden. De CLI ondersteunt alle andere parameters die de vorige PowerShell-opdracht ondersteunt.

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Een gegevensschijf aan een Linux-exemplaar monteren
Voordat u verdergaat met versleuteling op een virtuele machineschaalset, moet u ervoor zorgen dat de toegevoegde gegevensschijf correct is gemonteerd. Meld u aan bij de VM van het Linux-cluster en voer de opdracht **LSBLK** uit. De uitvoer moet die toegevoegde gegevensschijf in de kolom **Punt invoegen** weergeven weergeven.


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Toepassing implementeren in een Cluster van Servicefabric in Linux
Als u een toepassing wilt implementeren in uw cluster, volgt u de stappen en richtlijnen bij [Quickstart: Linux-containers implementeren voor servicestructuur.](service-fabric-quickstart-containers-linux.md)


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Schijfversleuteling inschakelen voor de eerder gemaakte virtuele machineschaalsets
Voer de volgende opdrachten uit om schijfversleuteling in te schakelen voor de virtuele machineschaalsets die u via de vorige stappen hebt gemaakt:
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Valideren of schijfversleuteling is ingeschakeld voor een virtuele machineschaal die is ingesteld in Linux
Voer de volgende opdrachten uit om de status van een volledige virtuele machineschaalset of een instantie in een schaalset te krijgen.
Daarnaast u zich aanmelden bij de VM van het Linux-cluster en de opdracht **LSBLK** uitvoeren. De uitvoer moet de toegevoegde gegevensschijf in de kolom **Punt instellen** weergeven weergeven en de kolom **Type** moet *Crypt*lezen.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Schijfversleuteling uitschakelen voor een virtuele machineschaal die is ingesteld in een cluster van Servicefabric
Schakel schijfversleuteling voor een virtuele machineschaal in door de volgende opdrachten uit te voeren. Houd er rekening mee dat het uitschakelen van schijfversleuteling van toepassing is op de volledige virtuele machineschaalset en niet op een afzonderlijke instantie.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Volgende stappen
Op dit punt moet u een beveiligd cluster hebben en weten hoe u schijfversleuteling in- en uitschakelt voor clusterknooppunten van Service Fabric en virtuele machineschaalsets. Zie [Schijfversleuteling voor Windows voor](service-fabric-enable-azure-disk-encryption-windows.md)vergelijkbare richtlijnen voor clusterknooppunten van Service Fabric in Linux. 
