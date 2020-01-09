---
title: Een cluster bijwerken voor het gebruik van de algemene naam van het certificaat
description: Meer informatie over het overschakelen van een Service Fabric cluster met behulp van certificaat vingerafdrukten naar het gebruik van de algemene naam van het certificaat.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 66c49ccb7b7633d0eff392b676bb381118eb64a2
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610196"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Wijzig het cluster van de vinger afdruk van het certificaat in de algemene naam
Er kunnen niet twee certificaten dezelfde vinger afdruk hebben, waardoor de rollover van het cluster certificaat of het beheer lastig wordt. Meerdere certificaten kunnen echter dezelfde algemene naam of hetzelfde onderwerp hebben.  Schakelen tussen een geïmplementeerd cluster vanuit vingerafdrukken voor certificaten naar het gebruik van gewone namen voor certificaten maakt het beheer van certificaten veel eenvoudiger. In dit artikel wordt beschreven hoe u een actief Service Fabric cluster bijwerkt om de algemene naam van het certificaat te gebruiken in plaats van de vinger afdruk van het certificaat.

>[!NOTE]
> Als u twee vinger afdruk hebt gedefinieerd in uw sjabloon, moet u twee implementaties uitvoeren.  De eerste implementatie wordt uitgevoerd voordat u de stappen in dit artikel volgt.  Met de eerste implementatie wordt uw **vingerafdruk** eigenschap in de sjabloon ingesteld op het certificaat dat wordt gebruikt en wordt de eigenschap **thumbprintSecondary** verwijderd.  Volg de stappen in dit artikel voor de tweede implementatie.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Een certificaat ophalen
Haal eerst een certificaat op bij een certificerings [instantie (CA)](https://wikipedia.org/wiki/Certificate_authority).  De algemene naam van het certificaat moet de hostnaam van het cluster zijn.  Bijvoorbeeld ' myclustername.southcentralus.cloudapp.azure.com '.  

Voor test doeleinden kunt u een door een CA ondertekend certificaat ontvangen van een gratis of open certificerings instantie.

> [!NOTE]
> Zelfondertekende certificaten, inclusief verbindingen die zijn gegenereerd bij het implementeren van een Service Fabric cluster in de Azure Portal, worden niet ondersteund.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Upload het certificaat en installeer het in de schaalset
In azure wordt een Service Fabric cluster geïmplementeerd op een schaalset met virtuele machines.  Upload het certificaat naar een sleutel kluis en installeer het op de virtuele-machine schaalset waarop het cluster wordt uitgevoerd.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName `
    -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

>[!NOTE]
> Het aantal geheimen van een schaalset biedt geen ondersteuning voor dezelfde resource-ID voor twee afzonderlijke geheimen, omdat elk geheim een unieke, specifieke bron is. 

## <a name="download-and-update-the-template-from-the-portal"></a>De sjabloon downloaden en bijwerken vanuit de portal
Het certificaat is geïnstalleerd op de onderliggende schaalset, maar u moet ook het Service Fabric cluster bijwerken om dat certificaat en de algemene naam te gebruiken.  Down load nu de sjabloon voor de cluster implementatie.  Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar de resource groep die als host fungeert voor het cluster.  Selecteer in **instellingen**de optie **implementaties**.  Selecteer de meest recente implementatie en klik op **sjabloon weer geven**.

![Sjablonen weer geven][image1]

De JSON-bestanden van de sjabloon en de para meters downloaden naar uw lokale computer.

Open eerst het parameter bestand in een tekst editor en voeg de volgende parameter waarde toe:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Open vervolgens het sjabloon bestand in een tekst editor en maak drie updates ter ondersteuning van de algemene naam van het certificaat.

1. Voeg in de sectie **para meters** een *certificateCommonName* -para meter toe:
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    U kunt ook overwegen om de *certificateThumbprint*te verwijderen. er wordt mogelijk niet meer naar verwezen in de Resource Manager-sjabloon.

2. In de resource **micro soft. Compute/virtualMachineScaleSets** werkt u de extensie van de virtuele machine bij voor het gebruik van de algemene naam in certificaat instellingen in plaats van de vinger afdruk.  In **virtualMachineProfile**->**extensionProfile**->**uitbrei dingen**->**Eigenschappen**->**instellingen**->**certificaat**, `"commonNames": ["[parameters('certificateCommonName')]"],` toevoegen en `"thumbprint": "[parameters('certificateThumbprint')]",`verwijderen.
    ```json
        "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                            "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                            "nodeTypeRef": "[variables('vmNodeType0Name')]",
                            "dataPath": "D:\\SvcFab",
                            "durabilityLevel": "Bronze",
                            "enableParallelJobs": true,
                            "nicPrefixOverride": "[variables('subnet0Prefix')]",
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        "typeHandlerVersion": "1.0"
                    }
                },
    ```

3.  Werk in de resource **micro soft. ServiceFabric/clusters** de API-versie bij naar ' 2018-02-01 '.  Voeg ook een **certificateCommonNames** -instelling toe met een **commonNames** -eigenschap en verwijder de **certificaat** instelling (met de eigenschap vinger afdruk), zoals in het volgende voor beeld:
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
            "addonFeatures": [
                "DnsService",
                "RepairManager"
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": ""
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
    ```

Zie [Deploy a Service Fabric cluster die de algemene naam van een certificaat gebruikt in plaats van een vinger afdruk](https://docs.microsoft.com/azure/service-fabric/service-fabric-create-cluster-using-cert-cn) voor meer informatie.

## <a name="deploy-the-updated-template"></a>De bijgewerkte sjabloon implementeren
Implementeer de bijgewerkte sjabloon opnieuw nadat u de wijzigingen hebt aangebracht.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [cluster beveiliging](service-fabric-cluster-security.md).
* Meer informatie over het [overkantelen van een cluster certificaat](service-fabric-cluster-rollover-cert-cn.md)
* [Cluster certificaten bijwerken en beheren](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
