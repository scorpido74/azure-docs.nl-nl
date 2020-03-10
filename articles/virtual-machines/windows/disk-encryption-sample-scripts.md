---
title: Voorbeeld scripts Azure Disk Encryption
description: Dit artikel is de bijlage voor Microsoft Azure schijf versleuteling voor Windows-Vm's.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 50addbec1717c7bb76a248053dd889b09441f6f6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391488"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Voorbeeld scripts Azure Disk Encryption 

Dit artikel bevat voorbeeld scripts voor het voorbereiden van vooraf versleutelde Vhd's en andere taken.

 

## <a name="list-vms-and-secrets"></a>Vm's en geheimen weer geven

Alle versleutelde virtuele machines in uw abonnement weer geven:

```azurepowershell-interactive
$osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
$dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
```
Alle schijf versleutelings geheimen weer geven die worden gebruikt voor het versleutelen van Vm's in een sleutel kluis:

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

## <a name="the-azure-disk-encryption-prerequisites-scripts"></a>De Azure Disk Encryption vereisten scripts
Als u al bekend bent met de vereisten voor Azure Disk Encryption, kunt u het [Power shell-script Azure Disk Encryption vereisten](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 )gebruiken. Zie [Quick Start a VM versleutelen](disk-encryption-powershell-quickstart.md)voor een voor beeld van het gebruik van dit Power shell-script. U kunt de opmerkingen verwijderen uit een gedeelte van het script, begint bij regel 211, voor het versleutelen van alle schijven voor bestaande virtuele machines in een bestaande resourcegroep. 

De volgende tabel ziet u welke parameters kunnen worden gebruikt in het PowerShell-script: 

|Parameter|Beschrijving|Ingevuld?|
|------|------|------|
|$resourceGroupName| De naam van de resourcegroep waaraan de KeyVault behoort.  Een nieuwe resourcegroep met deze naam wordt gemaakt als deze niet bestaat.| True|
|$keyVaultName|De naam van de Sleutelkluis waar versleutelingssleutels in sleutels moeten worden geplaatst. Een nieuwe kluis met deze naam wordt gemaakt als deze niet bestaat.| True|
|$location|Locatie van de Key Vault. Zorg ervoor dat de Key Vault en de virtuele machines moeten worden versleuteld zijn op dezelfde locatie. Haal een locatielijst op met `Get-AzLocation`.|True|
|$subscriptionId|Id van het Azure-abonnement moet worden gebruikt.  U kunt uw abonnements-ID ophalen met `Get-AzSubscription`.|True|
|$aadAppName|De naam van de Azure AD-toepassing die wordt gebruikt voor het schrijven van geheimen naar Key Vault. Als er nog geen toepassing met deze naam bestaat, wordt deze aangemaakt. Als deze app al bestaat, moet u aadClientSecret parameter doorgeven aan het script.|False|
|$aadClientSecret|Clientgeheim van de Azure AD-toepassing die eerder is gemaakt.|False|
|$keyEncryptionKeyName|De naam van de optionele sleutel van versleutelingssleutel in Key Vault. Een nieuwe sleutel met deze naam wordt gemaakt als deze niet bestaat.|False|

## <a name="resource-manager-templates"></a>Resource Manager-sjablonen

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Versleutelen of ontsleutelen van virtuele machines zonder een Azure AD-app

- [Schijf versleuteling inschakelen op een bestaande of actieve Windows-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [Versleuteling uitschakelen op een actieve Windows-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Versleutelen of ontsleutelen van virtuele machines met een Azure AD-app (vorige versie) 
 
- [Schijf versleuteling inschakelen op een bestaande of actieve Windows-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [Versleuteling uitschakelen op een actieve Windows-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [Een nieuwe versleutelde beheerde schijf maken op basis van een vooraf versleutelde VHD/opslag-BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Maakt u een nieuwe versleutelde beheerde schijf die een vooraf gecodeerde VHD en de bijbehorende instellingen voor codering

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Een vooraf versleutelde Windows-VHD voorbereiden
De volgende secties zijn die nodig zijn voor het vooraf gecodeerde Windows VHD voorbereiden voor implementatie als een versleutelde VHD in Azure IaaS. Gebruik de informatie voor het voorbereiden en start een nieuwe Windows virtuele machine (VHD) voor Azure Site Recovery of Azure. Zie [een gegeneraliseerde VHD uploaden en deze gebruiken om nieuwe virtuele machines in azure te maken](upload-generalized-managed.md)voor meer informatie over het voorbereiden en uploaden van een VHD.

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Bijwerken van Groepsbeleid om toe te staan zonder TPM voor OS-beveiliging
Configureer de instelling van de BitLocker-groepsbeleid **BitLocker-stationsversleuteling**, die u vindt onder **lokaal computer beleid** > **computer configuratie** > **Beheersjablonen** **Windows-onderdelen**. >  Wijzig deze instelling in **stations met het besturings systeem** > **extra authenticatie vereisen bij het opstarten** > **BitLocker zonder een compatibele TPM toestaan**, zoals wordt weer gegeven in de volgende afbeelding:

![Microsoft Antimalware in Azure](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>BitLocker-onderdelen installeren
Voor Windows Server 2012 en hoger, gebruikt u de volgende opdracht:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Voor Windows Server 2008 R2, gebruikt u de volgende opdracht uit:

    ServerManagerCmd -install BitLockers

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Het volume van het besturings systeem voorbereiden voor BitLocker met behulp van `bdehdcfg`
Als u de besturingssysteem partitie wilt comprimeren en de machine wilt voorbereiden voor BitLocker, voert u de [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) indien nodig uit:

    bdehdcfg -target c: shrink -quiet 

### <a name="protect-the-os-volume-by-using-bitlocker"></a>Volume met het besturingssysteem beveiligen met behulp van BitLocker
Gebruik de [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) opdracht om versleuteling op het opstart volume in te scha kelen met behulp van een externe-sleutel beveiliging. Ook de externe sleutel (.bek-bestand) op de externe schijf of volume plaatsen. Versleuteling is ingeschakeld op het volume/opstarten van het systeem na het opnieuw opstarten.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> De virtuele machine met een afzonderlijke gegevens/resource VHD voorbereiden voor het ophalen van de externe sleutel met BitLocker.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Versleutelde VHD uploaden naar een Azure Storage-account
Nadat DM-cryptografie versleuteling is ingeschakeld, moet de lokale versleutelde VHD worden geüpload naar uw opslag account.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Upload het geheim voor de vooraf versleutelde virtuele machine naar uw sleutel kluis
Het geheim voor schijf versleuteling dat u eerder hebt verkregen, moet als geheim worden geüpload in uw sleutel kluis.  Hiervoor moeten de machtigingen set Secret en wrapkey worden verleend aan het account dat de geheimen uploadt.

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

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Het geheim voor schijf versleuteling is niet versleuteld met een KEK
Als u het geheim in uw sleutel kluis wilt instellen, gebruikt u [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). De wachtwoordzin wordt gecodeerd als een base64-teken reeks en vervolgens geüpload naar de sleutel kluis. Bovendien moet u ervoor dat de volgende codes zijn ingesteld wanneer u het geheim in de key vault maakt.

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


Gebruik de `$secretUrl` in de volgende stap voor [het koppelen van de besturingssysteem schijf zonder KEK te gebruiken](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Het geheim voor schijf versleuteling is versleuteld met een KEK
Voordat u het geheim naar de key vault uploaden, kunt u deze desgewenst versleutelen met behulp van een sleutel van versleutelingssleutel. Gebruik de omloop- [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) om het geheim eerst te versleutelen met behulp van de coderings sleutel sleutel. De uitvoer van deze terugloop bewerking is een met base64 gecodeerde URL-teken reeks, die u vervolgens als geheim kunt uploaden met behulp van de [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) -cmdlet.

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

Gebruik `$KeyEncryptionKey` en `$secretUrl` in de volgende stap voor [het koppelen van de besturingssysteem schijf met behulp van KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Geef een geheime URL op wanneer u een besturingssysteem schijf koppelt

###  <a name="without-using-a-kek"></a>Zonder gebruik te maken van een KEK
Wanneer u de besturingssysteem schijf koppelt, moet u `$secretUrl`door geven. De URL is gegenereerd in de sectie 'schijfversleuteling geheim niet versleuteld met een KEK-sleutel'.
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
Wanneer u de besturingssysteem schijf koppelt, geeft u `$KeyEncryptionKey` en `$secretUrl`door. De URL is gegenereerd in de sectie 'Versleutelingsgeheim van schijf versleuteld met een KEK-sleutel'.
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
