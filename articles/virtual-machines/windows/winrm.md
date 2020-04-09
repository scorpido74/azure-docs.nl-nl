---
title: WinRM-toegang instellen voor een Azure VM
description: WinRM-toegang instellen voor gebruik met een virtuele Azure-machine die is gemaakt in het implementatiemodel resourcebeheer.
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/16/2016
ms.author: mimckitt
ms.openlocfilehash: 317e9376e0b8242758cd6e3f455b3f3dc9c0dc78
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879561"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>WinRM-toegang instellen voor virtuele machines in Azure Resource Manager

Dit zijn de stappen die u moet nemen om een VM met WinRM-connectiviteit in te stellen

1. Een sleutelkluis maken
2. Een zelfondertekend certificaat maken
3. Upload je zelfondertekende certificaat naar Key Vault
4. Download de URL voor uw zelfondertekende certificaat in de Key Vault
5. Uw URL met zelfondertekende certificaten naverwijzen tijdens het maken van een vm

 

## <a name="step-1-create-a-key-vault"></a>Stap 1: Een sleutelkluis maken
U de onderstaande opdracht gebruiken om de Key Vault te maken

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Stap 2: Een zelfondertekend certificaat maken
U een zelfondertekend certificaat maken met dit PowerShell-script

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Stap 3: Upload je zelfondertekende certificaat naar de Key Vault
Voordat u het certificaat uploadt naar de Key Vault die in stap 1 is gemaakt, moet het worden geconverteerd naar een indeling die de Microsoft.Compute-resourceprovider begrijpt. Het onderstaande PowerShell-script zal u toestaan dat te doen

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

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Stap 4: Download de URL voor je zelfondertekende certificaat in de Key Vault
De Microsoft.Compute-resourceprovider heeft een URL nodig voor het geheim in de Sleutelkluis terwijl de VM wordt ingericht. Hierdoor kan de Microsoft.Compute-resourceprovider het geheim downloaden en het gelijkwaardige certificaat op de VM maken.

> [!NOTE]
> De URL van het geheim moet de versie ook bevatten. Een voorbeeld-URL ziet\/er onder https uit: /contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Sjablonen
U de koppeling naar de URL in de sjabloon krijgen met de onderstaande code

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
U deze URL krijgen met de onderstaande PowerShell-opdracht

    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Stap 5: Referentie aan de URL van uw zelfondertekende certificaten tijdens het maken van een vm
#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen
Tijdens het maken van een VM door middel van sjablonen, het certificaat wordt verwezen in de geheimen sectie en de winRM sectie zoals hieronder:

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

Een voorbeeld sjabloon voor het bovenstaande is hier te vinden op [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Broncode voor deze template is te vinden op [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -ProvisionVMAgent -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Stap 6: Verbinding maken met de VM
Voordat u verbinding maken met de VM, moet u ervoor zorgen dat uw machine is geconfigureerd voor winrm-beheer op afstand. Start PowerShell als beheerder en voer de opdracht hieronder uit om ervoor te zorgen dat u bent ingesteld.

    Enable-PSRemoting -Force

> [!NOTE]
> Mogelijk moet u ervoor zorgen dat de WinRM-service wordt uitgevoerd als het bovenstaande niet werkt. Dat kan met behulp van`Get-Service WinRM`
> 
> 

Zodra de installatie is voltooid, u verbinding maken met de VM met de onderstaande opdracht

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
