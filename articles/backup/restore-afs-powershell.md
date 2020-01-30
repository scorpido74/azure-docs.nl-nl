---
title: Azure Files herstellen met Power shell
description: In dit artikel leert u hoe u Azure Files kunt herstellen met behulp van de Azure Backup-service en Power shell.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: c1c116033dbf44e6e1f332195a18c7dfdbcd6c71
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776337"
---
# <a name="restore-azure-files-with-powershell"></a>Azure Files herstellen met Power shell

In dit artikel wordt uitgelegd hoe u een volledige bestands share of specifieke bestanden herstelt vanaf een herstel punt dat is gemaakt door de [Azure backup](backup-overview.md) -service met behulp van Azure Power shell.

U kunt een volledige bestands share of specifieke bestanden op de share herstellen. U kunt herstellen naar de oorspronkelijke locatie of naar een andere locatie.

## <a name="fetch-recovery-points"></a>Herstel punten ophalen

Gebruik [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) om alle herstel punten voor het back-upitem weer te geven.

In het volgende script:

* De variabele **$RP** is een matrix met herstel punten voor het geselecteerde back-upitem van de afgelopen zeven dagen.
* De matrix wordt in omgekeerde volg orde gesorteerd met het laatste herstel punt bij index **0**.
* Gebruik standaard-Power shell-matrix indexie om het herstel punt te kiezen.
* In het voor beeld selecteert **$RP [0]** het meest recente herstel punt.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

De uitvoer ziet er ongeveer als volgt uit.

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

Nadat het relevante herstel punt is geselecteerd, herstelt u de bestands share of het bestand naar de oorspronkelijke locatie of naar een andere locatie.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Een Azure-bestands share herstellen naar een alternatieve locatie

Gebruik [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) om het geselecteerde herstel punt te herstellen. Geef deze para meters op om de alternatieve locatie te identificeren:

* **TargetStorageAccountName**: het opslag account waarnaar de inhoud van de back-up wordt teruggezet. Het doel-opslag account moet zich op dezelfde locatie berichten als de kluis.
* **TargetFileShareName**: de bestands shares binnen het doel Storage-account waarnaar de inhoud van de back-up wordt teruggezet.
* **TargetFolder**: de map onder de bestands share waarop de gegevens worden teruggezet. Als de inhoud waarvan een back-up is gemaakt, moet worden hersteld naar een hoofdmap, geeft u de waarden van de doelmap op als een lege teken reeks.
* **ResolveConflict**: instructie als er een conflict is met de herstelde gegevens. Accepteert **overschrijven** of **overs Laan**.

Voer de cmdlet als volgt uit met de para meters:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

De opdracht retourneert een taak met een ID die moet worden bijgehouden, zoals wordt weer gegeven in het volgende voor beeld.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Een Azure-bestand terugzetten op een andere locatie

Gebruik [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) om het geselecteerde herstel punt te herstellen. Geef deze para meters op om de alternatieve locatie aan te duiden en het bestand dat u wilt herstellen, uniek te identificeren.

* **TargetStorageAccountName**: het opslag account waarnaar de inhoud van de back-up wordt teruggezet. Het doel-opslag account moet zich op dezelfde locatie berichten als de kluis.
* **TargetFileShareName**: de bestands shares binnen het doel Storage-account waarnaar de inhoud van de back-up wordt teruggezet.
* **TargetFolder**: de map onder de bestands share waarop de gegevens worden teruggezet. Als de inhoud waarvan een back-up is gemaakt, moet worden hersteld naar een hoofdmap, geeft u de waarden van de doelmap op als een lege teken reeks.
* **SourceFilePath**: het absolute pad van het bestand dat in de bestands share moet worden hersteld, als een teken reeks. Dit pad is hetzelfde pad dat wordt gebruikt in de Power shell **-cmdlet Get-AzStorageFile** .
* **SourceFileType**: Hiermee wordt aangegeven of een map of een bestand is geselecteerd. De **map** of het **bestand**wordt geaccepteerd.
* **ResolveConflict**: instructie als er een conflict is met de herstelde gegevens. Accepteert **overschrijven** of **overs Laan**.

De aanvullende para meters (SourceFilePath en SourceFileType) zijn alleen gerelateerd aan het afzonderlijke bestand dat u wilt herstellen.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Met deze opdracht wordt een taak geretourneerd met een ID die moet worden bijgehouden, zoals wordt weer gegeven in de vorige sectie.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Azure-bestands shares en-bestanden naar de oorspronkelijke locatie herstellen

Wanneer u naar een oorspronkelijke locatie herstelt, hoeft u geen doel-en doel-gerelateerde para meters op te geven. Alleen **ResolveConflict** moet worden gegeven.

#### <a name="overwrite-an-azure-file-share"></a>Een Azure-bestands share overschrijven

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Een Azure-bestand overschrijven

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](restore-afs.md) het herstellen van Azure files in de Azure Portal.
