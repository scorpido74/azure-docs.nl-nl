---
title: Sleutelkluissleutel herstellen & geheim voor versleutelde VM
description: Meer informatie over het herstellen van key vault-sleutel en geheim in Azure Backup met PowerShell
ms.topic: conceptual
ms.date: 08/28/2017
ms.openlocfilehash: 35bcb919cadd46c603b1f2ad49742c5435f873d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75450053"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Key Vault-sleutel en -geheim voor versleutelde virtuele machines terugzetten met Azure Backup

In dit artikel wordt gesproken over het gebruik van Azure VM Backup om versleutelde Azure VM's te herstellen, als uw sleutel en geheim niet bestaan in de sleutelkluis. Deze stappen kunnen ook worden gebruikt als u een afzonderlijke kopie van de sleutel (sleutelsleutel) en een geheime (BitLocker-versleutelingssleutel) voor de herstelde VM wilt behouden.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

* **Back-ups van versleutelde VM's** - Er is een back-up gemaakt van versleutelde Azure VM's met Azure Backup. Raadpleeg het artikel [Back-ups beheren en herstellen van Azure VM's met PowerShell](backup-azure-vms-automation.md) voor meer informatie over het maken van back-ups van versleutelde Azure VM's.
* **Azure Key Vault configureren:** zorg ervoor dat de sleutelkluis waaraan sleutels en geheimen moeten worden hersteld, al aanwezig is. Raadpleeg het artikel [Aan de slag met Azure Key Vault](../key-vault/key-vault-get-started.md) voor meer informatie over sleutelkluisbeheer.
* **Schijf herstellen** : controleer of u de hersteltaak hebt geactiveerd voor het herstellen van schijven voor versleutelde vm met [PowerShell-stappen.](backup-azure-vms-automation.md#restore-an-azure-vm) Dit komt omdat deze taak een JSON-bestand genereert in uw opslagaccount met sleutels en geheimen voor het herstellen van de versleutelde vm.

## <a name="get-key-and-secret-from-azure-backup"></a>Sleutel en geheim ophalen van Azure Backup

> [!NOTE]
> Zodra de schijf is hersteld voor de versleutelde VM, moet u ervoor zorgen dat:
>
> * $details wordt gevuld met gegevens over de schijftaak herstellen, zoals vermeld in [PowerShell-stappen in de sectie Schijven herstellen](backup-azure-vms-automation.md#restore-an-azure-vm)
> * VM mag alleen worden gemaakt van herstelde schijven **nadat de sleutel en het geheim zijn hersteld naar de sleutelkluis.**

Queryer de herstelde schijfeigenschappen voor de taakgegevens.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Stel de Azure-opslagcontext in en herstel JSON-configuratiebestand met sleutel- en geheime gegevens voor versleutelde VM.Set the Azure storage context and restore JSON configuration file containing key and secret details for encrypted VM.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Sleutel herstellen

Zodra het JSON-bestand is gegenereerd in het bovenstaande doelpad, genereert u het belangrijkste blobbestand van de JSON en voert u het uit om de toetscmdlet te herstellen om de sleutel (KEK) terug in de sleutelkluis te plaatsen.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Geheim herstellen

Gebruik het JSON-bestand dat hierboven is gegenereerd om geheime naam en waarde te krijgen en het te voeden om geheime cmdlet in te stellen om het geheim (BEK) terug in de sleutelkluis te plaatsen.Gebruik deze cmdlets als uw **VM is versleuteld met BEK en KEK.**

**Gebruik deze cmdlets als uw Windows VM is versleuteld met BEK en KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Gebruik deze cmdlets als uw Linux VM is versleuteld met BEK en KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = <Key_url_of_newly_restored_key>;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Gebruik het JSON-bestand dat hierboven is gegenereerd om geheime naam en waarde te krijgen en het te voeden om geheime cmdlet in te stellen om het geheim (BEK) terug in de sleutelkluis te plaatsen.Gebruik deze cmdlets als uw **VM alleen met BEK is versleuteld.**

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
>
> * De waarde voor $secretname kan worden verkregen door te verwijzen naar de output van $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl en het gebruik van tekst na geheimen / bijvoorbeeld output geheime URL is https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 en geheime naam is B3284AA-DAAA-4AAA-B393-60CAA848AAAA
> * De waarde van de tag DiskEncryptionKeyFileName is hetzelfde als de geheime naam.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Virtuele machine maken vanaf herstelde schijf

Als u een back-up hebt gemaakt van versleutelde VM met Azure VM Backup, helpen de hierboven genoemde PowerShell-cmdlets u de sleutel en het geheim terug te zetten naar de sleutelkluis. Nadat u deze hebt hersteld, raadpleegt u het artikel [Back-up beheren en herstellen van Azure VM's met PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) om versleutelde VM's te maken van herstelde schijf, sleutel en geheim.

## <a name="legacy-approach"></a>Legacy-aanpak

De hierboven genoemde aanpak zou werken voor alle herstelpunten. Echter, de oudere aanpak van het verkrijgen van belangrijke en geheime informatie van herstelpunt, zou geldig zijn voor herstelpunten ouder dan 11 juli 2017 voor VM's versleuteld met BEHULP VAN BEK en KEK. Zodra de taak van de herstelschijf is voltooid voor versleutelde VM met [PowerShell-stappen,](backup-azure-vms-automation.md#restore-an-azure-vm)moet u ervoor zorgen dat $rp wordt gevuld met een geldige waarde.

### <a name="restore-key"></a>Sleutel herstellen

Gebruik de volgende cmdlets om belangrijke (KEK)-informatie te krijgen vanaf het herstelpunt en deze te voeden om de sleutelcmdlet te herstellen om het terug in de sleutelkluis te plaatsen.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Geheim herstellen

Gebruik de volgende cmdlets om geheime (BEK)-informatie van het herstelpunt te krijgen en deze te voeden om een geheime cmdlet in te stellen om het terug in de sleutelkluis te plaatsen.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
>
> * Waarde voor $secretname kan worden verkregen door te verwijzen naar de output van $rp1. KeyAndSecretDetails.SecretUrl and using text after secrets/ b.v. output secret URL is https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 and secret name is B3284AAA-DAAA-4AAA-B393-60CAA848AAAAA
> * De waarde van de tag DiskEncryptionKeyFileName is hetzelfde als de geheime naam.
> * Waarde voor DiskEncryptionKeyEncryptionKeyURL kan worden verkregen uit de sleutelkluis na het herstellen van de sleutels terug en het gebruik van [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey) cmdlet
>
>

## <a name="next-steps"></a>Volgende stappen

Nadat u de sleutel en het geheim hebt hersteld naar de sleutelkluis, raadpleegt u het artikel [Back-up beheren en herstellen van Azure VM's met PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) om versleutelde VM's te maken van herstelde schijf, sleutel en geheim.
