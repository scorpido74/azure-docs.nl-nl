---
title: Een import-/importtaak voor Azure importeren/exporteren herstellen - v1 | Microsoft Documenten
description: Meer informatie over het herstellen van een importtaak die is gemaakt en uitgevoerd met de Azure Import/Export-service.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: f5db321d8c4a6e42591a82b0ed8eb6bc6e93bad4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973880"
---
# <a name="repairing-an-import-job"></a>Een importtaak herstellen
De Microsoft Azure Import/Export-service kan sommige bestanden of delen van een bestand niet kopiëren naar de Windows Azure Blob-service. Enkele redenen voor mislukkingen zijn:  
  
-   Beschadigde bestanden  
  
-   Beschadigde schijven  
  
-   De opslagaccountsleutel is gewijzigd tijdens het overzetten van het bestand.  
  
U het Microsoft Azure Import/Export Tool uitvoeren met de kopieerlogboekbestanden van de importtaak en het hulpprogramma uploadt de ontbrekende bestanden (of delen van een bestand) naar uw Windows Azure-opslagaccount om de importtaak te voltooien.  
  
## <a name="repairimport-parameters"></a>Parameters voor Herstellenimporteren

Met **RepairImport**kunnen de volgende parameters worden opgegeven: 
  
|||  
|-|-|  
|**/r:**<RepairFile\>|**Vereist.** Pad naar het reparatiebestand, dat de voortgang van de reparatie bijhoudt en u een onderbroken reparatie hervatten. Elk station moet één en slechts één reparatiebestand hebben. Wanneer u een reparatie voor een bepaald station start, geeft u het pad door aan een reparatiebestand, dat nog niet bestaat. Als u een onderbroken reparatie wilt hervatten, moet u de naam van een bestaand reparatiebestand doorgeven. Het reparatiebestand dat overeenkomt met het doelstation moet altijd worden opgegeven.|  
|**/logdir:**<LogDirectory\>|**Optionele.** De logboekmap. Verbose log bestanden zijn geschreven naar deze directory. Als er geen logboekmap is opgegeven, wordt de huidige map gebruikt als logboekmap.|  
|**/d:**<TargetDirectories\>|**Vereist.** Een of meer puntkomma-gescheiden mappen die de oorspronkelijke bestanden bevatten die zijn geïmporteerd. Het importstation kan ook worden gebruikt, maar is niet vereist als er alternatieve locaties van originele bestanden beschikbaar zijn.|  
|**/bk:**<BitLockerKey\>|**Optionele.** U moet de BitLocker-sleutel opgeven als u wilt dat de tool een versleuteld station ontgrendelt waar de originele bestanden beschikbaar zijn.|  
|**/sn:**<StorageAccountName\>|**Vereist.** De naam van de opslagaccount voor de importtaak.|  
|**/sk:**<StorageAccountKey\>|**Vereist** als en alleen als een container SAS niet is opgegeven. De accountsleutel voor de opslagaccount voor de importtaak.|  
|**/csas:**<ContainerSas\>|**Vereist** als en alleen als de opslagaccountsleutel niet is opgegeven. De container SAS voor toegang tot de blobs die zijn gekoppeld aan de importtaak.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**Vereist.** Pad naar het logboekbestand voor het kopiëren van stations (een groot logboek of foutlogboek). Het bestand wordt gegenereerd door de Windows Azure Import/Export-service en kan worden gedownload van de blob-opslag die aan de taak is gekoppeld. Het kopieerlogboekbestand bevat informatie over mislukte blobs of bestanden, die moeten worden gerepareerd.|  
|**/PathMapFile:**<DrivePathMapFile\>|**Optionele.** Ga naar een tekstbestand dat kan worden gebruikt om dubbelzinnigheden op te lossen als u meerdere bestanden met dezelfde naam hebt die u in dezelfde taak importeerde. De eerste keer dat het hulpprogramma wordt uitgevoerd, kan het dit bestand vullen met alle dubbelzinnige namen. Volgende uitvoeringen van het hulpprogramma gebruiken dit bestand om de dubbelzinnigheden op te lossen.|  
  
## <a name="using-the-repairimport-command"></a>De opdracht RepairImport gebruiken  
Als u importgegevens wilt herstellen door de gegevens over het netwerk te streamen, moet `/d` u de mappen opgeven die de oorspronkelijke bestanden bevatten die u importeerde met behulp van de parameter. U moet ook het logboekbestand opgeven dat u hebt gedownload van uw opslagaccount. Een typische opdrachtregel om een importtaak met gedeeltelijke fouten te herstellen, lijkt op:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
In het volgende voorbeeld van een kopielogboekbestand is een stuk van 64 k van een bestand beschadigd op het station dat is verzonden voor de importtaak. Aangezien dit de enige fout is die is aangegeven, is de rest van de blobs in de taak geïmporteerd.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
Wanneer dit logboek wordt doorgegeven aan het Azure Import/Export Tool, probeert het hulpprogramma de import voor dit bestand te voltooien door de ontbrekende inhoud over het netwerk te kopiëren. Naar aanleiding van het bovenstaande voorbeeld `\animals\koala.jpg` zoekt de tool `C:\Users\bob\Pictures` `X:\BobBackup\photos`naar het oorspronkelijke bestand binnen de twee mappen en . Als het `C:\Users\bob\Pictures\animals\koala.jpg` bestand bestaat, kopieert het Azure Import/Export Tool `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`het ontbrekende gegevensbereik naar de bijbehorende blob.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Conflicten oplossen bij het gebruik van RepairImport  
In sommige situaties kan het hulpprogramma het benodigde bestand mogelijk niet vinden of openen om een van de volgende redenen: het bestand kan niet worden gevonden, het bestand is niet toegankelijk, de bestandsnaam is dubbelzinnig of de inhoud van het bestand is niet langer correct.  
  
Er kan een dubbelzinnige fout optreden `\animals\koala.jpg` als het hulpprogramma probeert te `C:\Users\bob\pictures` `X:\BobBackup\photos`zoeken en er een bestand met die naam onder beide en . Dat wil `C:\Users\bob\pictures\animals\koala.jpg` zeggen, zowel en `X:\BobBackup\photos\animals\koala.jpg` bestaan op de import job drives.  
  
Met `/PathMapFile` de optie u deze fouten oplossen. U de naam van het bestand opgeven, die de lijst met bestanden bevat die het hulpprogramma niet correct heeft kunnen identificeren. In het volgende opdrachtregelvoorbeeld wordt `9WM35C2V_pathmap.txt`het volgende voorbeeld van de opdrachtregel ingevuld:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Het hulpprogramma schrijft vervolgens de `9WM35C2V_pathmap.txt`problematische bestandspaden naar , een op elke regel. Het bestand kan bijvoorbeeld de volgende vermeldingen bevatten nadat de opdracht is uitgevoerd:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Voor elk bestand in de lijst moet u proberen het bestand te vinden en te openen om ervoor te zorgen dat het beschikbaar is voor het hulpprogramma. Als u het gereedschap expliciet wilt vertellen waar u een bestand moet vinden, u het padkaartbestand wijzigen en het pad toevoegen aan elk bestand op dezelfde regel, gescheiden door een tabteken:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Nadat u de benodigde bestanden beschikbaar hebt gesteld voor het hulpprogramma of het padkaartbestand hebt bijgewerkt, u het hulpprogramma opnieuw uitvoeren om het importproces te voltooien.  
  
## <a name="next-steps"></a>Volgende stappen
 
* [Het azure-import/exporthulpprogramma instellen](storage-import-export-tool-setup-v1.md)   
* [Harde schijven voorbereiden voor een importtaak](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [De taakstatus controleren met kopielogboekbestanden](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Een exporttaak herstellen](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Problemen met het hulpprogramma Azure Import/Export oplossen](storage-import-export-tool-troubleshooting-v1.md)
