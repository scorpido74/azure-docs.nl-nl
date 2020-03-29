---
title: Het Azure Import/Export Tool instellen | Microsoft Documenten
description: Meer informatie over het instellen van het hulpprogramma voor het voorbereiden en repareren van de schijf voor de Azure Import/Export-service.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/29/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 01432ab68fc399f3e97eac2de5a7c356bef7078a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74979033"
---
# <a name="setting-up-the-azure-importexport-tool"></a>Het Azure Import/Export-hulpprogramma instellen

Het Hulpprogramma voor importeren/exporteren van Microsoft Azure is het hulpprogramma voor het voorbereiden en herstellen van stations die u gebruiken met de Microsoft Azure Import/Export-service. U het gereedschap gebruiken voor de volgende functies:

* Voordat u een importtaak maakt, u deze tool gebruiken om gegevens te kopiëren naar de harde schijven die u naar een Azure-datacenter gaat verzenden.
* Nadat een importtaak is voltooid, u deze tool gebruiken om blobs te herstellen die beschadigd zijn, ontbraken of in conflict zijn met andere blobs.
* Nadat u de schijven van een voltooide exporttaak hebt ontvangen, u deze tool gebruiken om bestanden te herstellen die beschadigd of beschadigd zijn op de stations.

## <a name="prerequisites"></a>Vereisten

Als u **stations voorbereidt op** een importtaak, moet aan de volgende voorwaarden worden voldaan:

* U moet een actief Azure-abonnement hebben.
* Uw abonnement moet een opslagaccount bevatten met voldoende beschikbare ruimte om de bestanden die u gaat importeren op te slaan.
* U hebt ten minste één van de toegangssleutels voor opslagaccount nodig.
* U hebt een computer (de "kopieermachine") nodig met Windows 7, Windows Server 2008 R2 of een nieuwer Windows-besturingssysteem geïnstalleerd.
* Het .NET Framework 4 moet op de kopieermachine worden geïnstalleerd.
* BitLocker moet zijn ingeschakeld op de kopieermachine.
* U hebt een of meer lege 2,5-inch of 3,5-inch SATAII of III of SSD harde schijven aangesloten op de kopieermachine.
* De bestanden die u wilt importeren, moeten toegankelijk zijn vanaf de kopieermachine, of ze zich nu op een netwerkshare of een lokale harde schijf bevinden.

Als u **een import** probeert te herstellen die gedeeltelijk is mislukt, moet u het zelfde

* De kopielogbestanden
* De opslagaccountsleutel

Als u **een export** probeert te herstellen die gedeeltelijk is mislukt, moet u het zelfde

* De kopielogbestanden
* De manifestbestanden (optioneel)
* De opslagaccountsleutel

## <a name="installing-the-azure-importexport-tool"></a>Het azure-import/exporthulpprogramma installeren

Download eerst [het Azure Import/Export Tool](https://www.microsoft.com/download/details.aspx?id=55280) en haal het naar `c:\WAImportExport`een map op uw computer, bijvoorbeeld .

Het Azure Import/Export Tool bestaat uit de volgende bestanden:

* gegevensset.csv
* driveset.csv
* hddid.dll
* Microsoft.Data.Services.Client.dll
* Microsoft.WindowsAzure.Storage.dll
* Microsoft.WindowsAzure.Storage.pdb
* Microsoft.WindowsAzure.Storage.xml
* WAImportImport.exe
* WAImportExport.exe.config
* WAImportExport.pdb
* WAImportExportCore.dll
* WAImportExportCore.pdb
* WAImportExportRepair.dll
* WAImportExportRepair.pdb

Open vervolgens een opdrachtpromptvenster in **de beheerdersmodus**en wijzig in de map met de uitgepakte bestanden.

Voer het gereedschap (`WAImportExport.exe`) uit zonder parameters als u help voor de opdracht wilt uitvoeren:

```
WAImportExport, a client tool for Windows Azure Import/Export Service. Microsoft (c) 2013


Copy directories and/or files with a new copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>]
        [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]
        DataSet:<dataset.csv>

Add more drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>

Abort an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession

Resume an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession

List drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListDrives

List copy sessions:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListCopySessions

Repair a Drive:
    WAImportExport.exe RepairImport | RepairExport
        /r:<RepairFile> [/logdir:<LogDirectory>]
        [/d:<TargetDirectories>] [/bk:<BitLockerKey>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        [/CopyLogFile:<DriveCopyLogFile>] [/ManifestFile:<DriveManifestFile>]
        [/PathMapFile:<DrivePathMapFile>]

Preview an Export Job:
    WAImportExport.exe PreviewExport
        [/logdir:<LogDirectory>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        /ExportBlobListFile:<ExportBlobListFile> /DriveSize:<DriveSize>

Parameters:

    /j:<JournalFile>
        - Required. Path to the journal file. A journal file tracks a set of drives and
          records the progress in preparing these drives. The journal file must always
          be specified.
    /logdir:<LogDirectory>
        - Optional. The log directory. Verbose log files as well as some temporary
          files will be written to this directory. If not specified, current directory
          will be used as the log directory. The log directory can be specified only
          once for the same journal file.
    /id:<SessionId>
        - Optional. The session Id is used to identify a copy session. It is used to
          ensure accurate recovery of an interrupted copy session.
    /ResumeSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to resume the session.
    /AbortSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to abort the session.
    /sn:<StorageAccountName>
        - Required. Only applicable for RepairImport and RepairExport. The name of
          the storage account.
    /sk:<StorageAccountKey>
        - Required. The key of the storage account.
    /InitialDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of drives to prepare.
    /AdditionalDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of additional drives to be added.
    /r:<RepairFile>
        - Required. Only applicable for RepairImport and RepairExport.
          Path to the file for tracking repair progress. Each drive must have one
          and only one repair file.
    /d:<TargetDirectories>
        - Required. Only applicable for RepairImport and RepairExport.
          For RepairImport, one or more semicolon-separated directories to repair;
          For RepairExport, one directory to repair, e.g. root directory of the drive.
    /CopyLogFile:<DriveCopyLogFile>
        - Required. Only applicable for RepairImport and RepairExport. Path to the
          drive copy log file (verbose or error).
    /ManifestFile:<DriveManifestFile>
        - Required. Only applicable for RepairExport. Path to the drive manifest file.
    /PathMapFile:<DrivePathMapFile>
        - Optional. Only applicable for RepairImport. Path to the file containing
          mappings of file paths relative to the drive root to locations of actual files
          (tab-delimited). When first specified, it will be populated with file paths
          with empty targets, which means either they are not found in TargetDirectories,
          access denied, with invalid name, or they exist in multiple directories. The
          path map file can be manually edited to include the correct target paths and
          specified again for the tool to resolve the file paths correctly.
    /ExportBlobListFile:<ExportBlobListFile>
        - Required. Path to the XML file containing list of blob paths or blob path
          prefixes for the blobs to be exported. The file format is the same as the
          blob list blob format in the Put Job operation of the Import/Export Service
          REST API.
    /DriveSize:<DriveSize>
        - Required. Size of drives to be used for export. For example, 500GB, 1.5TB.
          Note: 1 GB = 1,000,000,000 bytes
                1 TB = 1,000,000,000,000 bytes
    /DataSet:<dataset.csv>
        - Required. A .csv file that contains a list of directories and/or a list files
          to be copied to target drives.

    /silentmode
        - Optional. If not specified, it will remind you the requirement of drives and
          need your confirmation to continue.

Examples:

    Copy a data set to a drive:
    WAImportExport.exe PrepImport
        /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GEL
        xmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /InitialDriveSet:driveset1.csv
        /DataSet:data.csv

    Copy another dataset to the same drive following the above command:
    WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#2 /DataSet:dataset2.csv

    Preview how many 1.5 TB drives are needed for an export job:
    WAImportExport.exe PreviewExport
        /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7K
        ysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\temp\myexportbloblist.xml
        /DriveSize:1.5TB

    Repair an finished import job:
    WAImportExport.exe RepairImport
        /r:9WM35C2V.rep /d:X:\ /bk:442926-020713-108086-436744-137335-435358-242242-2795
        98 /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94
        f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\temp\9WM35C2V_error.log
```

## <a name="next-steps"></a>Volgende stappen

* [Harde schijven voorbereiden voor een importtaak](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Op voorhand het schijfgebruik voor een exporttaak bekijken](../storage-import-export-tool-previewing-drive-usage-export-v1.md)
* [De taakstatus controleren met kopielogboekbestanden](../storage-import-export-tool-reviewing-job-status-v1.md)
* [Een importtaak herstellen](../storage-import-export-tool-repairing-an-import-job-v1.md)
* [Een exporttaak herstellen](../storage-import-export-tool-repairing-an-export-job-v1.md)
* [Problemen met het hulpprogramma Azure Import/Export oplossen](storage-import-export-tool-troubleshooting-v1.md)
