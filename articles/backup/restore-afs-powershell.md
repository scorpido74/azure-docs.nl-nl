---
title: Azure-bestanden herstellen met PowerShell
description: In dit artikel leest u hoe u Azure-bestanden herstellen met de Azure Backup-service en PowerShell.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 99aeaa6173bb5336e6e1719a9fc0df0c668374e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086820"
---
# <a name="restore-azure-files-with-powershell"></a>Azure-bestanden herstellen met PowerShell

In dit artikel wordt uitgelegd hoe u een volledige bestandsshare of specifieke bestanden herstellen vanuit een herstelpunt dat is gemaakt door de [Azure Backup-service](backup-overview.md) met Azure Powershell.

U een volledige bestandsshare of specifieke bestanden op het aandeel herstellen. U herstellen naar de oorspronkelijke locatie of naar een alternatieve locatie.

> [!WARNING]
> Zorg ervoor dat de PS-versie is geüpgraded naar de minimale versie voor 'Az.RecoveryServices 2.6.0' voor AFS-back-ups. Voor meer informatie, verwijzen wij u naar [de sectie](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) waarin de vereiste voor deze wijziging.

## <a name="fetch-recovery-points"></a>Herstelpunten ophalen

Gebruik [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) om alle herstelpunten voor het back-upitem weer te geven.

In het volgende script:

* De variabele **$rp** is een reeks herstelpunten voor het geselecteerde back-upitem van de afgelopen zeven dagen.
* De array wordt gesorteerd in omgekeerde volgorde van tijd met het laatste herstelpunt op index **0**.
* Gebruik standaard PowerShell-arrayindexering om het herstelpunt te kiezen.
* In het voorbeeld selecteert **$rp[0]** het laatste herstelpunt.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

De uitvoer is vergelijkbaar met de volgende.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

Nadat het desbetreffende herstelpunt is geselecteerd, herstelt u de bestandsshare of -bestand naar de oorspronkelijke locatie of naar een alternatieve locatie.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Een Azure-bestandsshare herstellen naar een alternatieve locatie

Gebruik het [Herstel-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) om te herstellen naar het geselecteerde herstelpunt. Geef deze parameters op om de alternatieve locatie te identificeren:

* **TargetStorageAccountName:** het opslagaccount waarop de back-upinhoud wordt hersteld. Het doelopslagaccount moet zich op dezelfde locatie bevinden als de kluis.
* **TargetFileShareName:** Het bestand wordt gedeeld in het doelopslagaccount waarop de back-upinhoud is hersteld.
* **TargetFolder:** de map onder de bestandsshare waaraan gegevens worden hersteld. Als de back-upinhoud moet worden hersteld naar een hoofdmap, geeft u de waarden van de doelmap als een lege tekenreeks.
* **ResolveConflict:** Instructie als er een conflict is met de herstelde gegevens. Accepteert **Overschrijven** of **Overslaan**.

Voer de cmdlet als volgt uit met de parameters:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

De opdracht retourneert een taak met een id die moet worden bijgehouden, zoals in het volgende voorbeeld wordt weergegeven.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Een Azure-bestand herstellen naar een alternatieve locatie

Gebruik het [Herstel-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) om te herstellen naar het geselecteerde herstelpunt. Geef deze parameters op om de alternatieve locatie te identificeren en om op unieke wijze het bestand te identificeren dat u wilt herstellen.

* **TargetStorageAccountName:** het opslagaccount waarop de back-upinhoud wordt hersteld. Het doelopslagaccount moet zich op dezelfde locatie bevinden als de kluis.
* **TargetFileShareName:** Het bestand wordt gedeeld in het doelopslagaccount waarop de back-upinhoud is hersteld.
* **TargetFolder:** de map onder de bestandsshare waaraan gegevens worden hersteld. Als de back-upinhoud moet worden hersteld naar een hoofdmap, geeft u de waarden van de doelmap als een lege tekenreeks.
* **SourceFilePath:** het absolute pad van het bestand, dat moet worden hersteld binnen de bestandsshare, als een tekenreeks. Dit pad is hetzelfde pad dat wordt gebruikt in de **cmdlet Get-AzStorageFile** PowerShell.
* **SourceFileType:** of een map of een bestand is geselecteerd. Accepteert **directory** of **bestand**.
* **ResolveConflict:** Instructie als er een conflict is met de herstelde gegevens. Accepteert **Overschrijven** of **Overslaan**.

De extra parameters (SourceFilePath en SourceFileType) zijn alleen gerelateerd aan het afzonderlijke bestand dat u wilt herstellen.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Met deze opdracht wordt een taak geretourneerd met een id die moet worden bijgehouden, zoals in de vorige sectie wordt weergegeven.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Azure-bestandsshares en -bestanden herstellen naar de oorspronkelijke locatie

Wanneer u herstelt naar een oorspronkelijke locatie, hoeft u geen doel- en doelgerelateerde parameters op te geven. Alleen **ResolveConflict** moet worden verstrekt.

#### <a name="overwrite-an-azure-file-share"></a>Een Azure-bestandsshare overschrijven

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Een Azure-bestand overschrijven

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](restore-afs.md) het herstellen van Azure-bestanden in de Azure-portal.
