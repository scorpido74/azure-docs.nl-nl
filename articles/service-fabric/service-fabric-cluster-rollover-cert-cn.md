---
title: Een Azure Service Fabric-clustercertificaat omrollen
description: Meer informatie over het doorrollen van een clustercertificaat voor Servicefabric dat is geïdentificeerd met de algemene naam van het certificaat.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 94cc6841886b1b0eb4271ac0f727a2e3561e0081
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451963"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Handmatig een clustercertificaat van servicestructuur omrollen
Wanneer een clustercertificaat voor ServiceFabric bijna verloopt, moet u het certificaat bijwerken.  Certificaatrollover is eenvoudig als het cluster is [ingesteld om certificaten te gebruiken op basis](service-fabric-cluster-change-cert-thumbprint-to-cn.md) van algemene naam (in plaats van duimafdruk).  Ontvang een nieuw certificaat van een certificaatautoriteit met een nieuwe vervaldatum.  Zelfondertekende certificaten worden geen ondersteuning voor productieservicefabricclusters, om certificaten op te nemen die zijn gegenereerd tijdens de werkstroom voor het maken van Azure-portalcluster. Het nieuwe certificaat moet dezelfde algemene naam hebben als het oudere certificaat. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Service Fabric-cluster gebruikt het gedeclareerde certificaat automatisch met een vervolg naar de toekomstige vervaldatum; wanneer er meer dan één certificaat op de host is geïnstalleerd. Een aanbevolen manier is het gebruik van een resourcemanagersjabloon voor het inrichten van Azure Resources. Voor een niet-productieomgeving kan het volgende script worden gebruikt om een nieuw certificaat naar een sleutelkluis te uploaden en installeert u het certificaat vervolgens op de virtuele machineschaalset: 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

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
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Computes Virtual Machine Scale Set Secrets ondersteunen niet dezelfde resource-id voor twee afzonderlijke geheimen, omdat elk geheim een unieke bron in versies is. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [clusterbeveiliging](service-fabric-cluster-security.md).
* [Clustercertificaten bijwerken en beheren](service-fabric-cluster-security-update-certs-azure.md)
