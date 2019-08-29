---
title: WinRM-toegang instellen voor een Azure-VM | Microsoft Docs
description: WinRM-toegang instellen voor gebruik met een virtuele Azure-machine die is gemaakt in het Resource Manager-implementatie model.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/16/2016
ms.author: kasing
ms.openlocfilehash: f7f57a43697a9376062bdd3baa2d5f7333bf4a7f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100161"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>WinRM-toegang instellen voor Virtual Machines in Azure Resource Manager

Hier volgen de stappen die u moet uitvoeren om een VM in te stellen met WinRM-connectiviteit

1. Een sleutelkluis maken
2. Een zelfondertekend certificaat maken
3. Uw zelfondertekende certificaat uploaden naar Key Vault
4. Haal de URL voor uw zelfondertekende certificaat op in de Key Vault
5. Verwijzen naar de URL van uw zelfondertekende certificaten tijdens het maken van een VM

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="step-1-create-a-key-vault"></a>Stap 1: Een sleutelkluis maken
U kunt de onderstaande opdracht gebruiken om de Key Vault te maken

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Stap 2: Een zelfondertekend certificaat maken
U kunt een zelfondertekend certificaat maken met dit Power shell-script

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Stap 3: Uw zelfondertekende certificaat uploaden naar de Key Vault
Voordat u het certificaat uploadt naar de Key Vault die u in stap 1 hebt gemaakt, moet dit worden geconverteerd naar een indeling die de micro soft. Compute-resource provider begrijpt. Met het onderstaande Power shell-script kunt u dit doen

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Stap 4: Haal de URL voor uw zelfondertekende certificaat op in de Key Vault
De resource provider micro soft. Compute heeft een URL nodig voor het geheim in het Key Vault tijdens het inrichten van de virtuele machine. Hierdoor kan de resource provider micro soft. Compute het geheim downloaden en het gelijkwaardige certificaat maken op de VM.

> [!NOTE]
> De URL van het geheim moet ook de versie bevatten. Een voor beeld van een URL ziet er\/als volgt uit:/contosovault.Vault.Azure.net:443/Secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Sjablonen
U kunt de koppeling naar de URL in de sjabloon ophalen met behulp van de onderstaande code

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
U kunt deze URL ophalen met behulp van de onderstaande Power shell-opdracht

    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Stap 5: Verwijzen naar de URL van uw zelfondertekende certificaten tijdens het maken van een VM
#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager sjablonen
Tijdens het maken van een VM via sjablonen, wordt in het gedeelte geheimen naar het certificaat verwezen en wordt de sectie winRM als hieronder beschreven:

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Een voorbeeld sjabloon voor het bovenstaande vindt u hier op [201-VM-WinRM-sleutel kluis-Windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

De bron code voor deze sjabloon is te vinden op [github](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Stap 6: Verbinding maken met de virtuele machine
Voordat u verbinding kunt maken met de virtuele machine, moet u ervoor zorgen dat uw computer is geconfigureerd voor het externe beheer van WinRM. Start Power shell als beheerder en voer de onderstaande opdracht uit om te controleren of u hebt ingesteld.

    Enable-PSRemoting -Force

> [!NOTE]
> U moet er mogelijk voor zorgen dat de WinRM-service wordt uitgevoerd als de bovenstaande niet werkt. U kunt dit doen met`Get-Service WinRM`
> 
> 

Nadat de installatie is voltooid, kunt u verbinding maken met de virtuele machine met behulp van de onderstaande opdracht

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
