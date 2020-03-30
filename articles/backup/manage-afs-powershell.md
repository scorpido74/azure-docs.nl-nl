---
title: Back-ups van Azure-bestandsshare beheren met PowerShell
description: Meer informatie over het gebruik van PowerShell voor het beheren en bewaken van Azure-bestandsshares, waareen een back-up van maakt door de Azure Backup-service.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a9dc421db740963fc5cd11e868eb383694376ce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083175"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>Back-ups van Azure-bestandsshare beheren met PowerShell

In dit artikel wordt beschreven hoe u Azure PowerShell gebruiken om de Azure-bestandsshares te beheren en te controleren waarvan wordt geback-upt van de Azure Backup-service.

> [!WARNING]
> Zorg ervoor dat de PS-versie is geüpgraded naar de minimale versie voor 'Az.RecoveryServices 2.6.0' voor AFS-back-ups. Voor meer informatie, verwijzen wij u naar [de sectie](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) waarin de vereiste voor deze wijziging.

## <a name="modify-the-protection-policy"></a>Het beveiligingsbeleid wijzigen

Als u het beleid wilt wijzigen dat wordt gebruikt voor het maken van back-ups van de Azure-bestandsshare, gebruikt u [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Geef het relevante back-upitem en het nieuwe back-upbeleid op.

In het volgende voorbeeld wordt het **beveiligingsbeleid van De AzureFS** gewijzigd van **dailyafs** naar **monthlyafs.**

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>Back-ups bijhouden en taken herstellen

On-demand back-up- en herstelbewerkingen retourneren een taak samen met een ID, zoals wordt weergegeven wanneer u [een on-demand back-up uitvoert.](backup-azure-afs-automation.md#trigger-an-on-demand-backup) Gebruik de cmdlet [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) om de voortgang en details van de taak bij te houden.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```

## <a name="stop-protection-on-a-file-share"></a>De beveiliging voor een bestandsshare stoppen

Er zijn twee manieren om het beveiligen van Azure-bestandsshares te stoppen:

* Alle toekomstige back-uptaken stoppen en alle herstelpunten *verwijderen*
* Stop alle toekomstige back-uptaken, maar laat de herstelpunten *achter*

Er kunnen kosten verbonden zijn aan het verlaten van de herstelpunten in opslag, omdat de onderliggende momentopnamen die door Azure Backup zijn gemaakt, worden bewaard. Het voordeel van het verlaten van de herstelpunten is echter dat u de bestandsshare later herstellen, indien gewenst. Zie de [prijsdetails](https://azure.microsoft.com/pricing/details/storage/files/)voor informatie over de kosten van het verlaten van de herstelpunten. Als u ervoor kiest om alle herstelpunten te verwijderen, u de bestandsshare niet herstellen.

## <a name="stop-protection-and-retain-recovery-points"></a>Stop de beveiliging en bewaar herstelpunten

Gebruik de cmdlet [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) om de beveiliging te stoppen met het bewaren van gegevens.

In het volgende voorbeeld wordt de beveiliging van het *aandeel van het afsfileshare-bestand* gestopt, maar worden alle herstelpunten behouden:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID
```

```output
WorkloadName     Operation         Status         StartTime                 EndTime                   JobID
------------     ---------         ------         ---------                 -------                   -----
afsfileshare     DisableBackup     Completed      1/26/2020 2:43:59 PM      1/26/2020 2:44:21 PM      98d9f8a1-54f2-4d85-8433-c32eafbd793f
```

Het kenmerk Taak-id in de uitvoer komt overeen met de taak-id van de taak die is gemaakt door de back-upservice voor uw 'stopbeveiliging'-bewerking. Als u de status van de taak wilt bijhouden, gebruikt u de cmdlet [Get-AzRecoveryServicesBackupJob.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-3.3.0)

## <a name="stop-protection-without-retaining-recovery-points"></a>Stop de beveiliging zonder herstelpunten te behouden

Als u de beveiliging wilt stoppen zonder herstelpunten te behouden, gebruikt u de cmdlet [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) en voegt u de parameter **-RemoveRecoveryPoints** toe.

In het volgende voorbeeld wordt de beveiliging van het *delen van afsfileshare-bestanden* gestopt zonder herstelpunten te behouden:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID -RemoveRecoveryPoints
```

```output
WorkloadName     Operation            Status         StartTime                 EndTime                   JobID
------------     ---------            ------         ---------                 -------                   -----
afsfileshare     DeleteBackupData     Completed      1/26/2020 2:50:57 PM      1/26/2020 2:51:39 PM      b1a61c0b-548a-4687-9d15-9db1cc5bcc85
```

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](manage-afs-backup.md) het beheren van azure-back-ups van bestandsshare in de Azure-portal.
