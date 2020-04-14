---
title: Een cluster bijwerken om de algemene naam van het certificaat te gebruiken
description: Meer informatie over het overschakelen van een cluster van Servicefabric van het gebruik van certificaatduimafdrukken naar het gebruik van de algemene naam van het certificaat.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 1926b0501766eb0a5fe086ceada0c9bf45e3dcf6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272624"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Cluster van vingerafdruk van certificaat wijzigen in algemene naam
Geen twee certificaten kunnen dezelfde duimafdruk hebben, wat clustercertificaatrollover of beheer moeilijk maakt. Meerdere certificaten kunnen echter dezelfde algemene naam of onderwerp hebben.  Schakelen tussen een geïmplementeerd cluster vanuit vingerafdrukken voor certificaten naar het gebruik van gewone namen voor certificaten maakt het beheer van certificaten veel eenvoudiger. In dit artikel wordt beschreven hoe u een uitgevoerd servicestructuurcluster bijwerkt om de algemene naam van het certificaat te gebruiken in plaats van de duimafdruk van het certificaat.

>[!NOTE]
> Als u twee duimafdrukafdrukken in uw sjabloon hebt aangegeven, moet u twee implementaties uitvoeren.  De eerste implementatie wordt uitgevoerd voordat u de stappen in dit artikel volgt.  Bij de eerste implementatie wordt de eigenschap **thumbprint** in de sjabloon ingesteld op het certificaat dat wordt gebruikt en wordt de eigenschap **thumbprintSecondary** verwijderd.  Volg voor de tweede implementatie de stappen in dit artikel.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Een certificaat aanvragen
Ontvang eerst een certificaat van een [certificeringsinstantie (CA).](https://wikipedia.org/wiki/Certificate_authority)  De algemene naam van het certificaat moet zijn voor het aangepaste domein dat u bezit en gekocht bij een domeinregistrar. Bijvoorbeeld "azureservicefabricbestpractices.com"; degenen die geen Microsoft-werknemers zijn, kunnen geen certs voor MS-domeinen inrichten, dus u de DNS-namen van uw LB of Traffic Manager niet gebruiken als algemene namen voor uw certificaat, en u moet een [Azure DNS-zone inrichten](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) als uw aangepaste domein in Azure oplosbaar moet zijn. U wilt ook uw aangepaste domein dat u bezit als het 'managementEndpoint' van uw cluster declareren als u wilt dat portal de aangepaste domeinalias voor uw cluster weergeeft.

Voor testdoeleinden u een CA-ondertekend certificaat krijgen van een gratis of open certificaatautoriteit.

> [!NOTE]
> Zelfondertekende certificaten, inclusief certificaten die worden gegenereerd bij het implementeren van een Service Fabric-cluster in de Azure-portal, worden niet ondersteund. 

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Het certificaat uploaden en installeren in de schaalset
In Azure wordt een cluster van Servicefabric geïmplementeerd op een virtuele machineschaalset.  Upload het certificaat naar een sleutelkluis en installeer het vervolgens op de virtuele machineschaalset waarop het cluster wordt uitgevoerd.

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
> Scale set secrets ondersteunen niet dezelfde resource-ID voor twee afzonderlijke geheimen, omdat elk geheim een versieversie is, unieke bron. 

## <a name="download-and-update-the-template-from-the-portal"></a>De sjabloon downloaden en bijwerken van de portal
Het certificaat is geïnstalleerd op de onderliggende schaalset, maar u moet ook het cluster Servicefabric bijwerken om dat certificaat en de algemene naam ervan te gebruiken.  Download nu de sjabloon voor uw clusterimplementatie.  Meld u aan bij de [Azure-portal](https://portal.azure.com) en navigeer naar de brongroep die het cluster host.  Selecteer **Implementaties**in **Instellingen**.  Selecteer de meest recente implementatie en klik op **Sjabloon weergeven**.

![Sjablonen weergeven][image1]

Download de JSON-bestanden met sjabloon en parameters naar uw lokale computer.

Open eerst het parametersbestand in een teksteditor en voeg de volgende parameterwaarde toe:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Open vervolgens het sjabloonbestand in een teksteditor en breng drie updates uit om de algemene naam van het certificaat te ondersteunen.

1. Voeg in de sectie **parameters** een parameter *certificateCommonName* toe:
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Overweeg ook om de *certificaatafdruk te*verwijderen, er wordt mogelijk niet meer naar verwezen in de sjabloon Resourcebeheer.

2. Werk in de bron **Microsoft.Compute/virtualMachineScaleSets** de extensie voor virtuele machines bij om de algemene naam in certificaatinstellingen te gebruiken in plaats van de duimafdruk.  In **virtualMachineProfile**->**extensieProfiel**->**extensies**->**instellingen**->**instellingen**->**certificaat**, toevoegen `"commonNames": ["[parameters('certificateCommonName')]"],` en verwijderen `"thumbprint": "[parameters('certificateThumbprint')]",`.
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

3.  Werk in de **bron Microsoft.ServiceFabric/clusters** de API-versie bij naar '2018-02-01'.  Voeg ook een **instelling voor certificateCommonNames** toe met een eigenschap **commonNames** en verwijder de **certificaatinstelling** (met de eigenschap thumbprint) zoals in het volgende voorbeeld:
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

Zie Een [cluster van servicestructuur implementeren dat gebruikmaakt van de algemene naam van het certificaat in plaats van duimafdruk.](https://docs.microsoft.com/azure/service-fabric/service-fabric-create-cluster-using-cert-cn)

## <a name="deploy-the-updated-template"></a>De bijgewerkte sjabloon implementeren
Implementeer de bijgewerkte sjabloon opnieuw nadat u de wijzigingen hebt aangebracht.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [clusterbeveiliging](service-fabric-cluster-security.md).
* Meer informatie over het [overrollen van een clustercertificaat](service-fabric-cluster-rollover-cert-cn.md)
* [Clustercertificaten bijwerken en beheren](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
