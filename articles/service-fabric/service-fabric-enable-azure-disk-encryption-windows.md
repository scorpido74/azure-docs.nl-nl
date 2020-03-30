---
title: Schijfversleuteling inschakelen voor Windows-clusters
description: In dit artikel wordt beschreven hoe u schijfversleuteling voor Azure Service Fabric-clusterknooppunten inschakelt met Azure Key Vault in Azure Resource Manager.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: b08cdb63aa6f334c5a6f7c230b1624d232206c3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251822"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Schijfversleuteling inschakelen voor Azure Service Fabric-clusterknooppunten in Windows 
> [!div class="op_single_selector"]
> * [Schijfversleuteling voor Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Schijfversleuteling voor Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

In deze zelfstudie leert u hoe u schijfversleuteling inschakelt op clusterknooppunten van ServiceFabric in Windows. U moet deze stappen volgen voor elk knooppunttype en virtuele machineschaalsets. Voor het versleutelen van de knooppunten gebruiken we de azure disk encryptie-mogelijkheden op virtuele machine schaalsets.

De gids behandelt de volgende onderwerpen:

* Belangrijke concepten waar u rekening mee moet houden bij het inschakelen van schijfversleuteling op virtuele machineschaalsets van het Service Fabric-cluster in Windows.
* Stappen die moeten worden gevolgd voordat schijfversleuteling wordt ingeschakeld op clusterknooppunten van ServiceFabric in Windows.
* Stappen die moeten worden gevolgd om schijfversleuteling in te schakelen op clusterknooppunten van ServiceFabric in Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

**Zelfregistratie** 

De schijfversleutelingspreview voor de virtuele machineschaalset vereist zelfregistratie. Voer de volgende stappen uit: 

1. Voer eerst de volgende opdracht uit:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Wacht ongeveer 10 minuten tot de status *Geregistreerd*staat. U de status controleren door de volgende opdracht uit te voeren: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. Maak een sleutelkluis in hetzelfde abonnement en dezelfde regio als de schaalset en selecteer vervolgens het toegangsbeleid voor de sleutelkluis **ingeschakeldvoorschijfje** op de sleutelkluis met behulp van de PowerShell-cmdlet. U het beleid ook instellen met de Key Vault-gebruikersinterface in de Azure-portal met de volgende opdracht:
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
* Versleuteling wordt ondersteund voor besturingssysteem- en gegevensvolumes in virtuele machineschaalsets in Windows. Versleuteling uitschakelen wordt ook ondersteund voor OS- en gegevensvolumes voor virtuele machineschaalsets in Windows.
* Virtuele machine reimage en upgrade bewerkingen voor virtuele machine schaal sets worden niet ondersteund in de huidige preview.


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

Als u een aangepaste sjabloon moet maken die aan uw behoeften voldoet, raden we u ten zeerste aan om te beginnen met een van de sjablonen die beschikbaar zijn op de pagina Sjabloonvoorbeelden voor het maken van [azure-servicesfabric.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) Zie de volgende richtlijnen om [de sectie clustersjabloon aan te passen.][customize-your-cluster-template]

Als u al een aangepaste sjabloon hebt, controleert u of alle drie de certificaatgerelateerde parameters in de sjabloon en het parameterbestand als volgt worden genoemd en dat waarden als volgt niet worden vermeld:

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Een toepassing implementeren in een cluster van Servicefabric in Windows
Als u een toepassing in uw cluster wilt implementeren, volgt u de stappen en richtlijnen bij [Implementeren en verwijdert u toepassingen met PowerShell.](service-fabric-deploy-remove-applications.md)


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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Valideren of schijfversleuteling is ingeschakeld voor een virtuele machineschaal die is ingesteld in Windows
Krijg de status van een volledige virtuele machineschaalset of een instantie in een schaalset door de volgende opdrachten uit te voeren.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Bovendien u zich aanmelden bij de virtuele machineschaalset en ervoor zorgen dat de schijven zijn versleuteld.

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
Op dit punt moet u een beveiligd cluster hebben en weten hoe u schijfversleuteling in- en uitschakelt voor clusterknooppunten van Service Fabric en virtuele machineschaalsets. Zie [Schijfversleuteling voor Linux voor](service-fabric-enable-azure-disk-encryption-linux.md)vergelijkbare richtlijnen voor clusterknooppunten van Service Fabric in Linux.

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
