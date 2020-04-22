---
title: Toepassingscertificaat toevoegen aan een cluster in Powershell
description: 'Azure PowerShell-voorbeeldscript: een toepassingscertificaat toevoegen aan een Service Fabric-cluster.'
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: d657ef8d28b36d93bc923036254e446c7be4c2c8
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769523"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Een toepassingscertificaat toevoegen aan een Service Fabric-cluster

Dit voorbeeldscript doorloopt hoe u een certificaat maakt in Key Vault en implementeert het vervolgens naar een van de virtuele machineschaalsets waarop uw cluster wordt uitgevoerd. Dit scenario maakt geen gebruik van Service Fabric rechtstreeks, maar is eerder afhankelijk van Key Vault en van virtuele machineschaalsets.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installeer indien nodig de Azure PowerShell met de instructie in `Connect-AzAccount` de [Azure PowerShell-handleiding](/powershell/azure/overview) en voer deze uit om een verbinding met Azure te maken. 

## <a name="create-a-certificate-in-key-vault"></a>Een certificaat maken in Key Vault

```powershell
$VaultName = ""
$CertName = ""
$SubjectName = "CN="

$policy = New-AzKeyVaultCertificatePolicy -SubjectName $SubjectName -IssuerName Self -ValidityInMonths 12
Add-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName -CertificatePolicy $policy
```

## <a name="or-upload-an-existing-certificate-into-key-vault"></a>Of upload een bestaand certificaat naar Key Vault

```powershell
$VaultName= ""
$CertName= ""
$CertPassword= ""
$PathToPFX= ""

$bytes = [System.IO.File]::ReadAllBytes($PathToPFX)
$base64 = [System.Convert]::ToBase64String($bytes)
$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $CertPassword
   } | ConvertTo-Json
$contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
$content = [System.Convert]::ToBase64String($contentbytes)

$SecretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

# Upload the certificate to the key vault as a secret
$Secret = Set-AzKeyVaultSecret -VaultName $VaultName -Name $CertName -SecretValue $SecretValue

```

## <a name="update-virtual-machine-scale-sets-profile-with-certificate"></a>Profiel van virtuele machineschaalsets bijwerken met certificaat

```powershell
$ResourceGroupName = ""
$VMSSName = ""
$CertStore = "My" # Update this with the store you want your certificate placed in, this is LocalMachine\My

# If you have added your certificate to the keyvault certificates, use
$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName).SecretId -CertificateStore $CertStore

# Otherwise, if you have added your certificate to the keyvault secrets, use
$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultSecret -VaultName $VaultName -Name $CertName).Id -CertificateStore $CertStore

$VMSS = Get-AzVmss -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMSSName

# If this KeyVault is already known by the virtual machine scale set, for example if the cluster certificate is deployed from this keyvault, use
$VMSS.virtualmachineprofile.osProfile.secrets[0].vaultCertificates.Add($certConfig)

# Otherwise use
$VMSS = Add-AzVmssSecret -VirtualMachineScaleSet $VMSS -SourceVaultId (Get-AzKeyVault -VaultName $VaultName).ResourceId  -VaultCertificate $CertConfig
```

## <a name="update-the-virtual-machine-scale-set"></a>De virtuele machineschaalset bijwerken
```powershell
Update-AzVmss -ResourceGroupName $ResourceGroupName -VirtualMachineScaleSet $VMSS -VMScaleSetName $VMSSName
```

> Als u wilt dat het certificaat op meerdere knooppunttypen in uw cluster wordt geplaatst, moeten de tweede en derde delen van dit script worden herhaald voor elk knooppunttype dat het certificaat moet hebben.

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Nieuw-AzKeyVaultCertificateBeleid](/powershell/module/az.keyvault/New-AzKeyVaultCertificatePolicy) | Hiermee maakt u een in-memorybeleid dat het certificaat vertegenwoordigt |
| [Add-AzKeyVaultCertificaat](/powershell/module/az.keyvault/Add-AzKeyVaultCertificate)| Implementeert het beleid voor key vault-certificaten |
| [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/Set-AzKeyVaultSecret)| Implementeert het beleid voor Key Vault Secrets |
| [Nieuw-AzVmssVaultCertificateConfig](/powershell/module/az.compute/New-AzVmssVaultCertificateConfig) | Hiermee maakt u een in-memory config die het certificaat in een vm weergeeft |
| [Get-AzVmss](/powershell/module/az.compute/Get-AzVmss) |  |
| [Add-AzVmssSecret](/powershell/module/az.compute/Add-AzVmssSecret) | Hiermee voegt u het certificaat toe aan de in-memory definitie van de virtuele machineschaalset |
| [Update-AzVmss](/powershell/module/az.compute/Update-AzVmss) | Implementeert de nieuwe definitie van de virtuele machineschaalset |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

Meer voorbeelden voor Azure Powershell voor Azure Service Fabric vindt u in de [voorbeelden van Azure PowerShell](../service-fabric-powershell-samples.md).
