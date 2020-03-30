---
title: Voorbeeldscripts voor Azure Disk Encryption
description: Dit artikel is de bijlage voor Microsoft Azure Disk Encryption voor Windows VM's.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 50addbec1717c7bb76a248053dd889b09441f6f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266726"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Voorbeeldscripts voor Azure Disk Encryption 

In dit artikel vindt u voorbeeldscripts voor het voorbereiden van vooraf versleutelde VHD's en andere taken.

 

## <a name="list-vms-and-secrets"></a>Lijst VM's en geheimen

Alle versleutelde VM's in uw abonnement weergeven:

```azurepowershell-interactive
$osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
$dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
```
Vermeld alle geheimen van schijfversleuteling die worden gebruikt voor het versleutelen van VM's in een sleutelkluis:

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

## <a name="the-azure-disk-encryption-prerequisites-scripts"></a>De vereiste vereisten voor Azure Disk Encryption
Als u al bekend bent met de vereisten voor Azure Disk Encryption, u het [PowerShell-script voor Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 )gebruiken. Zie de [Vm Quickstart versleutelen](disk-encryption-powershell-quickstart.md)voor een voorbeeld van het gebruik van dit PowerShell-script. U de opmerkingen uit een gedeelte van het script verwijderen, te beginnen bij regel 211, om alle schijven voor bestaande VM's in een bestaande brongroep te versleutelen. 

In de volgende tabel ziet u welke parameters kunnen worden gebruikt in het PowerShell-script: 

|Parameter|Beschrijving|Verplicht?|
|------|------|------|
|$resourceGroupName| Naam van de resourcegroep waartoe de KeyVault behoort.  Er wordt een nieuwe resourcegroep met deze naam gemaakt als deze niet bestaat.| True|
|$keyVaultName|Naam van de KeyVault waarin encryptiesleutels moeten worden geplaatst. Er wordt een nieuwe kluis met deze naam gemaakt als deze niet bestaat.| True|
|$location|Locatie van de KeyVault. Zorg ervoor dat de KeyVault- en VM's die moeten worden versleuteld, zich op dezelfde locatie bevinden. Haal een locatielijst op met `Get-AzLocation`.|True|
|$subscriptionId|Id van het Te gebruiken Azure-abonnement.  U kunt uw abonnements-ID ophalen met `Get-AzSubscription`.|True|
|$aadAppName|Naam van de Azure AD-toepassing die wordt gebruikt om geheimen naar KeyVault te schrijven. Als er nog geen toepassing met deze naam bestaat, wordt deze aangemaakt. Als deze app al bestaat, geeft u de parameter aadClientSecret door aan het script.|False|
|$aadClientSecret|Clientgeheim van de Azure AD-toepassing die eerder is gemaakt.|False|
|$keyEncryptionKeyName|Naam van optionele sleutelversleutelingssleutel in KeyVault. Er wordt een nieuwe sleutel met deze naam gemaakt als deze niet bestaat.|False|

## <a name="resource-manager-templates"></a>Resource Manager-sjablonen

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>VM's versleutelen of decoderen zonder een Azure AD-app

- [Schijfversleuteling inschakelen op een bestaande of met Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [Versleuteling uitschakelen op een draaiende Windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>VM's versleutelen of decoderen met een Azure AD-app (vorige release) 
 
- [Schijfversleuteling inschakelen op een bestaande of met Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [Versleuteling uitschakelen op een draaiende Windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [Een nieuwe versleutelde beheerde schijf maken vanaf een vooraf versleutelde VHD/storage blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Hiermee maakt u een nieuwe versleutelde beheerde schijf op voorwaarde dat een vooraf versleutelde VHD en de bijbehorende versleutelingsinstellingen

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Een vooraf versleutelde Windows VHD voorbereiden
De secties die volgen zijn nodig om een vooraf versleutelde Windows VHD voor te bereiden op implementatie als een versleutelde VHD in Azure IaaS. Gebruik de informatie om een nieuwe Windows VM (VHD) voor te bereiden en op te starten op Azure Site Recovery of Azure. Zie [Een gegeneraliseerde VHD uploaden en gebruiken om nieuwe VM's in Azure te maken](upload-generalized-managed.md)voor meer informatie over het voorbereiden en uploaden van een VHD.

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Groepsbeleid bijwerken om niet-TPM toe te staan voor OS-beveiliging
Configureer de BitLocker-groepsbeleidsinstelling **BitLocker-stationsversleuteling** > , die u vindt onder**Computerconfiguratiebeheersjablonen** > voor **lokale computerbeleid** > **Computer Configuration****Windows-onderdelen**. Wijzig deze instelling in **Stations voor besturingssystemen** > **Vereisen extra verificatie bij het opstarten** > **Toestaan BitLocker zonder compatibele TPM,** zoals wordt weergegeven in de volgende afbeelding:

![Microsoft Antimalware in Azure](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>BitLocker-functiecomponenten installeren
Gebruik voor Windows Server 2012 en hoger de volgende opdracht:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Voer voor Windows Server 2008 R2 de volgende opdracht in:

    ServerManagerCmd -install BitLockers

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Het besturingssysteemvolume voorbereiden op BitLocker met behulp van`bdehdcfg`
Als u de OS-partitie wilt comprimeren en de machine wilt voorbereiden op BitLocker, voert u de [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) indien nodig uit:

    bdehdcfg -target c: shrink -quiet 

### <a name="protect-the-os-volume-by-using-bitlocker"></a>Het besturingssysteemvolume beveiligen met BitLocker
Gebruik [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) de opdracht om versleuteling op het opstartvolume in te schakelen met behulp van een externe sleutelbeschermer. Plaats ook de externe sleutel (.bek-bestand) op de externe schijf of het externe volume. Versleuteling is ingeschakeld op het systeem/opstartvolume na de volgende herstart.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Bereid de VM voor met een afzonderlijke VHD voor gegevens/bron voor het verkrijgen van de externe sleutel met BitLocker.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Versleutelde VHD uploaden naar een Azure-opslagaccount
Nadat DM-Crypt-versleuteling is ingeschakeld, moet de lokale versleutelde VHD worden geüpload naar uw opslagaccount.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Upload het geheim voor de vooraf versleutelde VM naar uw sleutelkluis
Het schijfversleutelingsgeheim dat u eerder hebt verkregen, moet worden geüpload als een geheim in uw sleutelkluis.  Dit vereist het verlenen van de set geheime toestemming en de wrapkey toestemming voor het account dat de geheimen zal uploaden.

```powershell 
# Typically, account Id is the user principal name (in user@domain.com format)
$upn = (Get-AzureRmContext).Account.Id
Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# In cloud shell, the account ID is a managed service identity, so specify the username directly 
# $upn = "user@domain.com" 
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# When running as a service principal, retrieve the service principal ID from the account ID, and set access policy to that 
# $acctid = (Get-AzureRmContext).Account.Id
# $spoid = (Get-AzureRmADServicePrincipal -ServicePrincipalName $acctid).Id
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $spoid -BypassObjectIdValidation -PermissionsToKeys wrapKey -PermissionsToSecrets set

```

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Schijfversleutelingsgeheim niet versleuteld met een KEK
Als u het geheim in uw sleutelkluis wilt instellen, gebruikt u [Set-AzKeyVaultSecret.](/powershell/module/az.keyvault/set-azkeyvaultsecret) De wachtwoordzin wordt gecodeerd als een base64-tekenreeks en vervolgens geüpload naar de sleutelkluis. Zorg er bovendien voor dat de volgende tags worden ingesteld wanneer u het geheim in de sleutelkluis maakt.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Gebruik `$secretUrl` de in de volgende stap voor [het koppelen van de OS-schijf zonder kek te gebruiken.](#without-using-a-kek)

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Schijfversleuteling geheim versleuteld met een KEK
Voordat u het geheim uploadt naar de sleutelkluis, u het optioneel versleutelen met behulp van een sleutelversleutelingssleutel. Gebruik de wrap [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) om eerst het geheim te versleutelen met behulp van de sleutelversleutelingssleutel. De uitvoer van deze wrap-bewerking is een base64 URL gecodeerde tekenreeks, [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) die u vervolgens als geheim uploaden met behulp van de cmdlet.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Gebruik `$KeyEncryptionKey` `$secretUrl` en in de volgende stap voor [het koppelen van de OS-schijf met BEHULP van KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Een geheime URL opgeven wanneer u een os-schijf koppelt

###  <a name="without-using-a-kek"></a>Zonder gebruik van een KEK
Terwijl u de OS-schijf bevestigt, `$secretUrl`moet u slagen voor . De URL is gegenereerd in de sectie 'Schijfversleutelingsgeheim niet versleuteld met een KEK'-sectie.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Een KEK gebruiken
Wanneer u de OS-schijf bevestigt, passeert `$KeyEncryptionKey` en `$secretUrl`. De URL is gegenereerd in de sectie 'Schijfversleuteling geheim versleuteld met een KEK'.The URL is generated in the "Disk encryption secret encrypted with a KEK" sectie.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
