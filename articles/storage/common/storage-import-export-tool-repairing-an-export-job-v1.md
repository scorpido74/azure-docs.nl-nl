---
title: Een exporttaak voor Azure Import/Export herstellen - v1 | Microsoft Documenten
description: Meer informatie over het herstellen van een exporttaak die is gemaakt en uitgevoerd met de Azure Import/Export-service.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: b2ba30bddfc6364c79e1bb01d30cde63b261a07f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978012"
---
# <a name="repairing-an-export-job"></a>Een exporttaak herstellen
Nadat een exporttaak is voltooid, u het hulpprogramma voor importeren/exporteren van Microsoft Azure on-premises uitvoeren om:  
  
1.  Download alle bestanden die de Azure Import/Export-service niet kan exporteren.  
  
2.  Valideren of de bestanden op het station correct zijn geëxporteerd.  
  
U moet connectiviteit hebben met Azure Storage om deze functionaliteit te kunnen gebruiken.  
  
De opdracht voor het herstellen van een importtaak is **RepairExport**.

## <a name="repairexport-parameters"></a>Parameters Voor reparatieexport

Met **RepairExport**kunnen de volgende parameters worden opgegeven:  
  
|Parameter|Beschrijving|  
|---------------|-----------------|  
|**/r:<RepairFile\>**|Vereist. Pad naar het reparatiebestand, dat de voortgang van de reparatie bijhoudt en u een onderbroken reparatie hervatten. Elk station moet één en slechts één reparatiebestand hebben. Wanneer u een reparatie voor een bepaald station start, gaat u op het pad over naar een reparatiebestand dat nog niet bestaat. Als u een onderbroken reparatie wilt hervatten, moet u de naam van een bestaand reparatiebestand doorgeven. Het reparatiebestand dat overeenkomt met het doelstation moet altijd worden opgegeven.|  
|**/logdir:<LogDirectory\>**|Optioneel. De logboekmap. Verbose log bestanden zullen worden geschreven naar deze directory. Als er geen logboekmap is opgegeven, wordt de huidige map gebruikt als logboekmap.|  
|**/d:<TargetDirectory\>**|Vereist. De map om te valideren en te herstellen. Dit is meestal de hoofdmap van het exportstation, maar kan ook een netwerkbestandsshare zijn met een kopie van de geëxporteerde bestanden.|  
|**/bk:<BitLockerKey\>**|Optioneel. U moet de BitLocker-sleutel opgeven als u wilt dat het hulpprogramma een versleutelde code ontgrendelt waar de geëxporteerde bestanden worden opgeslagen.|  
|**/sn:<StorageAccountName\>**|Vereist. De naam van de opslagrekening voor de exporttaak.|  
|**/sk:<StorageAccountKey\>**|**Vereist** als en alleen als een container SAS niet is opgegeven. De accountsleutel voor de opslagrekening voor de exporttaak.|  
|**/csas:<ContainerSas\>**|**Vereist** als en alleen als de opslagaccountsleutel niet is opgegeven. De containerSAS voor toegang tot de blobs die zijn gekoppeld aan de exporttaak.|  
|**/CopyLogFile:<DriveCopyLogFile\>**|Vereist. Het pad naar het logboekbestand voor stationskopie. Het bestand wordt gegenereerd door de Windows Azure Import/Export-service en kan worden gedownload van de blob-opslag die aan de taak is gekoppeld. Het kopieerlogboekbestand bevat informatie over mislukte blobs of bestanden die moeten worden gerepareerd.|  
|**/ManifestFile:<DriveManifestFile\>**|Optioneel. Het pad naar het manifestbestand van het exportstation. Dit bestand wordt gegenereerd door de Windows Azure Import/Export-service en is opgeslagen op het exportstation en optioneel in een blob in het opslagaccount dat aan de taak is gekoppeld.<br /><br /> De inhoud van de bestanden op het exportstation wordt geverifieerd met de MD5-hashes in dit bestand. Bestanden waarvan is vastgesteld dat ze beschadigd zijn, worden gedownload en herschreven naar de doelmappen.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>De modus Herstelexport gebruiken om mislukte export te corrigeren  
U het Azure Import/Export Tool gebruiken om bestanden te downloaden die niet zijn geëxporteerd. Het logboekbestand bevat een lijst met bestanden die niet zijn geëxporteerd.  
  
De oorzaken van exportfouten zijn onder andere de volgende mogelijkheden:  
  
-   Beschadigde schijven  
  
-   De opslagaccountsleutel is gewijzigd tijdens het overdrachtsproces  
  
Als u het gereedschap wilt uitvoeren in de **modus Reparatieexport,** moet u eerst het station met de geëxporteerde bestanden op uw computer aansluiten. Voer vervolgens het Azure Import/Export Tool uit en geef `/d` het pad naar dat station op met de parameter. U moet ook het pad opgeven naar het kopieerlogboekbestand van het station dat u hebt gedownload. In het volgende voorbeeld van de opdrachtregel wordt het hulpprogramma uitgevoerd om bestanden te herstellen die niet zijn geëxporteerd:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Het volgende is een voorbeeld van een logboekbestand voor kopiëren dat aangeeft dat één blok in de blob niet kan worden geëxporteerd:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/desert.jpg</BlobPath>  
    <FilePath>\pictures\wild\desert.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList>  
      <Block Offset="65536" Length="65536" Id="AQAAAA==" Status="Failed" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
Het kopieerlogboekbestand geeft aan dat er een fout is opgetreden terwijl de Windows Azure Import/Export-service een van de blokken van de blob downloadde naar het bestand op het exportstation. De andere onderdelen van het bestand zijn met succes gedownload en de bestandslengte is correct ingesteld. In dit geval opent de tool het bestand op het station, downloadt u het blok van het opslagaccount en schrijft u het naar het bestandsbereik vanaf offset 65536 met lengte 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Reparatieexporteren gebruiken om de inhoud van het station te valideren  
U Azure Import/Export ook gebruiken met de optie **ReparatieExport** om de inhoud op het station te valideren. Het manifestbestand op elk exportstation bevat MD5's voor de inhoud van het station.  
  
De Azure Import/Export-service kan de manifestbestanden tijdens het exportproces ook opslaan in een opslagaccount. De locatie van de manifestbestanden is beschikbaar via de bewerking [Taak downloaden](/rest/api/storageimportexport/jobs) wanneer de taak is voltooid. Zie [Manifestbestandsindeling voor de service Importeren/exporteren](storage-import-export-file-format-metadata-and-properties.md) voor meer informatie over de indeling van een bedrijfsmanifestbestand.  
  
In het volgende voorbeeld ziet u hoe u het Azure Import/Export Tool uitvoert met de parameters **/ManifestFile** en **/CopyLogFile:**  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
Het volgende is een voorbeeld van een manifestbestand:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveManifest Version="2011-10-01">  
  <Drive>  
    <DriveId>9WM35C3U</DriveId>  
    <ClientCreator>Windows Azure Import/Export service</ClientCreator>  
    <BlobList>
      <Blob>  
        <BlobPath>pictures/city/redmond.jpg</BlobPath>  
        <FilePath>\pictures\city\redmond.jpg</FilePath>  
        <Length>15360</Length>  
        <PageRangeList>  
          <PageRange Offset="0" Length="3584" Hash="72FC55ED9AFDD40A0C8D5C4193208416" />  
          <PageRange Offset="3584" Length="3584" Hash="68B28A561B73D1DA769D4C24AA427DB8" />  
          <PageRange Offset="7168" Length="512" Hash="F521DF2F50C46BC5F9EA9FB787A23EED" />  
        </PageRangeList>  
        <PropertiesPath Hash="E72A22EA959566066AD89E3B49020C0A">\pictures\city\redmond.jpg.properties</PropertiesPath>  
      </Blob>  
      <Blob>  
        <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
        <FilePath>\pictures\wild\canyon.jpg</FilePath>  
        <Length>10884</Length>  
        <BlockList>  
          <Block Offset="0" Length="2721" Id="AAAAAA==" Hash="263DC9C4B99C2177769C5EBE04787037" />  
          <Block Offset="2721" Length="2721" Id="AQAAAA==" Hash="0C52BAE2CC20EFEC15CC1E3045517AA6" />  
          <Block Offset="5442" Length="2721" Id="AgAAAA==" Hash="73D1CB62CB426230C34C9F57B7148F10" />  
          <Block Offset="8163" Length="2721" Id="AwAAAA==" Hash="11210E665C5F8E7E4F136D053B243E6A" />  
        </BlockList>  
        <PropertiesPath Hash="81D7F81B2C29F10D6E123D386C3A4D5A">\pictures\wild\canyon.jpg.properties</PropertiesPath>  
      </Blob> 
    </BlobList>  
 </Drive>  
</DriveManifest>  
``` 
  
Na het voltooien van het reparatieproces, zal de tool lezen door elk bestand waarnaar in het manifestbestand wordt verwezen en de integriteit van het bestand met de Hashes van MD5 verifiëren. Voor het manifest hierboven, zal het gaan door de volgende componenten.  

```  
G:\pictures\city\redmond.jpg, offset 0, length 3584  
  
G:\pictures\city\redmond.jpg, offset 3584, length 3584  
  
G:\pictures\city\redmond.jpg, offset 7168, length 3584  
  
G:\pictures\city\redmond.jpg.properties  
  
G:\pictures\wild\canyon.jpg, offset 0, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 2721, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 5442, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 8163, length 2721  
  
G:\pictures\wild\canyon.jpg.properties  
```

Elk onderdeel dat niet de verificatie heeft, wordt door de tool gedownload en opnieuw naar hetzelfde bestand op het station geschreven.  
  
## <a name="next-steps"></a>Volgende stappen
 
* [Het azure-import/exporthulpprogramma instellen](storage-import-export-tool-setup-v1.md)   
* [Harde schijven voorbereiden voor een importtaak](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [De taakstatus controleren met kopielogboekbestanden](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Een importtaak herstellen](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Problemen met het hulpprogramma Azure Import/Export oplossen](storage-import-export-tool-troubleshooting-v1.md)
