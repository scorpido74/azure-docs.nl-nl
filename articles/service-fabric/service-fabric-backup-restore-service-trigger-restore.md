---
title: Back-up herstellen in Azure Service Fabric
description: Gebruik de functie periodieke back-up en herstel in Service Fabric voor het herstellen van gegevens uit een back-up van uw toepassingsgegevens.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 1737102ee652cc2263bd0a908c1336bc93a6757b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377902"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Back-up herstellen in Azure Service Fabric

In Azure Service Fabric kunnen betrouwbare stateful-services en betrouwbare actoren een veranderlijke, gezaghebbende status behouden nadat een aanvraag- en antwoordtransactie is voltooid. Een stateful dienst kan gaan voor een lange tijd of verliest informatie als gevolg van een ramp. Als dat gebeurt, moet de service worden hersteld vanaf de nieuwste aanvaardbare back-up, zodat deze kan blijven werken.

U bijvoorbeeld een service configureren om een back-up van de gegevens te maken om te beschermen tegen de volgende scenario's:

- **Geval van disaster recovery**: Permanent verlies van een volledig Service Fabric-cluster.
- **Geval van gegevensverlies**: Permanent verlies van een meerderheid van de replica's van een servicepartitie.
- **Geval van gegevensverlies**: Per ongeluk verwijderen of beschadiging van de service. Een beheerder verwijdert bijvoorbeeld ten onrechte de service.
- **Geval van gegevensbeschadiging:** Bugs in de service veroorzaken gegevensbeschadiging. Er kan bijvoorbeeld gegevensbeschadiging optreden wanneer een upgrade van een servicecode foutieve gegevens naar een betrouwbare verzameling schrijft. In een dergelijk geval moet u mogelijk zowel de code als de gegevens naar een eerdere status herstellen.

## <a name="prerequisites"></a>Vereisten

- Als u een herstel wilt activeren, moet de _FAS (Fault Analysis Service)_ zijn ingeschakeld voor het cluster.
- De _Back-upherstelservice (BRS)_ heeft de back-up gemaakt.
- Het herstel kan alleen worden geactiveerd bij een partitie.
- Installeer Microsoft.ServiceFabric.Powershell.Http-module [In Preview] voor het voeren van configuratiegesprekken.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Controleer of cluster is `Connect-SFCluster` verbonden met de opdracht voordat u een configuratieverzoek indient met de module Microsoft.ServiceFabric.Powershell.Http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggered-restore"></a>Getriggerd herstel

Een herstel kan worden geactiveerd voor een van de volgende scenario's:

- Gegevens herstel voor _herstel na noodgevallen_.
- Gegevensherstel voor _gegevensbeschadiging/gegevensverlies_.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Herstel van gegevens in geval van herstel na noodgevallen

Als een volledig cluster servicestructuur verloren gaat, u de gegevens voor de partities van de betrouwbare service en betrouwbare actoren herstellen. De gewenste back-up kan worden geselecteerd uit de lijst wanneer u [GetBackupAPI gebruikt met back-upopslaggegevens.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation) De back-up opsomming kan zijn voor een toepassing, service of partitie.

Ga er in het volgende voorbeeld van uit dat het verloren cluster hetzelfde cluster is waarnaar wordt verwezen in [Periodieke back-up inschakelen voor betrouwbare service en betrouwbare actoren.](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors) In dit `SampleApp` geval wordt het geïmplementeerd met een back-upbeleid ingeschakeld en worden de back-ups geconfigureerd voor Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell met Microsoft.ServiceFabric.Powershell.Http-module

```powershell
Get-SFBackupsFromBackupLocation -Application -ApplicationName 'fabric:/SampleApp' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Restcall met Powershell

Voer een PowerShell-script uit om de REST-API te gebruiken om `SampleApp` een lijst met de back-ups die zijn gemaakt voor alle partities in de toepassing, terug te sturen. De API vereist de back-upopslaggegevens om de beschikbare back-ups weer te geven.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Voorbeelduitvoer voor de bovenstaande run:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            :
```

Als u het herstel wilt activeren, kiest u een van de back-ups. De huidige back-up voor noodherstel kan bijvoorbeeld de volgende back-up zijn:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Voor de herstel-API moet u de gegevens _BackupId_ en _BackupLocation_ opgeven.

U moet ook een doelpartitie kiezen in het alternatieve cluster zoals beschreven in het [partitieschema.](service-fabric-concepts-partitioning.md#get-started-with-partitioning) De alternatieve clusterback-up wordt hersteld naar de partitie die is opgegeven in het partitieschema van het oorspronkelijke verloren cluster.

Als de partitie-id `1c42c47f-439e-4e09-98b9-88b8f60800c6`op het alternatieve cluster is, `974bd92a-b395-4631-8a7f-53bd4ae9cf22` kunt u deze toewijzen aan de oorspronkelijke clusterpartitie-id door de hoge toets en de toets voor _Bereikverdeling (UniformInt64Partition)_ te vergelijken.

Voor _Benoemde partitionering_wordt de naamwaarde vergeleken met het identificeren van de doelpartitie in een alternatief cluster.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell met Microsoft.ServiceFabric.Powershell.Http-module

```powershell

Restore-SFPartition  -PartitionId '1c42c47f-439e-4e09-98b9-88b8f60800c6' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Restcall met Powershell

U vraagt het herstel aan tegen de back-upclusterpartitie met behulp van de volgende [herstel-API:](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)

```powershell

$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
    BackupStorage  = $StorageInfo
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

U de voortgang van een herstel bijhouden met TrackRestoreProgress.

### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer gebruiken
U een herstel activeren vanuit Service Fabric Explorer. Controleer of de geavanceerde modus is ingeschakeld in de instellingen van Service Fabric Explorer.
1. Selecteer de gewenste partities en klik op Acties. 
2. Selecteer Trigger partitie herstel en vul gegevens in voor Azure:

    ![Partitieherstel activeren][2]

    of FileShare:

    ![Bestandsshare herstellen van partitie herstellen activeren][3]

### <a name="data-restore-for-_data-corruption__data-loss_"></a>Gegevensherstel voor _gegevensverlies_/_gegevens_

Voor _gegevensverlies_ of _gegevensbeschadiging_kunnen back-uppartities voor betrouwbare stateful-service en partities met betrouwbare actoren worden hersteld naar een van de gekozen back-ups.

Het volgende voorbeeld is een voortzetting van Het inschakelen van [periodieke back-up voor betrouwbare stateful service en betrouwbare actoren](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). In dit voorbeeld is een back-upbeleid ingeschakeld voor de partitie en maakt de service back-ups op een gewenste frequentie in Azure Storage.

Selecteer een back-up uit de uitvoer van [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). In dit scenario wordt de back-up gegenereerd vanuit hetzelfde cluster als voorheen.

Als u het herstel wilt activeren, kiest u een back-up uit de lijst. Selecteer de volgende back-up voor de huidige beschadiging _van gegevensgegevens:_/_data corruption_

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Geef voor de herstel-API de gegevens _BackupId_ en _BackupLocation_ op. Het cluster heeft een back-up ingeschakeld, zodat de _BRS (Service_ Fabric Backup Restore Service) de juiste opslaglocatie identificeert vanuit het bijbehorende back-upbeleid.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell met Microsoft.ServiceFabric.Powershell.Http-module

```powershell
Restore-SFPartition  -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'

```

#### <a name="rest-call-using-powershell"></a>Restcall met Powershell

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

U de herstelvoortgang bijhouden met TrackRestoreProgress.

## <a name="track-restore-progress"></a>Voortgang herstel bijhouden

Een partitie van een betrouwbare stateful service of betrouwbare actor accepteert slechts één herstelverzoek tegelijk. Een partitie accepteert alleen een andere aanvraag nadat de huidige herstelaanvraag is voltooid. Meerdere herstelaanvragen kunnen tegelijkertijd op verschillende partities worden geactiveerd.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell met Microsoft.ServiceFabric.Powershell.Http-module

```powershell
    Get-SFPartitionRestoreProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'
```

#### <a name="rest-call-using-powershell"></a>Restcall met Powershell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

De herstelaanvraag vordert in de volgende volgorde:

1. **Geaccepteerd:** een _geaccepteerde_ herstelstatus geeft aan dat de aangevraagde partitie is geactiveerd met de juiste aanvraagparameters.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **InProgress**: Een _herstelstatus van InProgress_ geeft aan dat er een herstel plaatsvindt in de partitie met de back-up die in aanvraag wordt vermeld. De partitie rapporteert de _status van gegevensverlies._
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Succes,** **Mislukt**of **Time-out**: Een aangevraagd herstel kan worden voltooid in een van de volgende statussen. Elke staat heeft de volgende betekenis- en reactiedetails:
    - **Succes**: Een _herstelstatus voor een succes_ geeft een herwonnen partitiestatus aan. De partitierapporten _RestoredEpoch-_ en _RestoredLSN-statussen_ samen met de tijd in UTC.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Fout:** de herstelstatus _van een fout_ geeft de fout van de herstelaanvraag aan. De oorzaak van de storing wordt gemeld.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Time-out**: een _time-outherstelstatus_ geeft aan dat de aanvraag een time-out heeft. Maak een nieuwe herstelaanvraag met een grotere [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). De standaardtime-out is 10 minuten. Zorg ervoor dat de partitie zich niet in de status van gegevensverlies bevindt voordat u opnieuw herstel aanvraagt.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Automatisch herstellen

U functies betrouwbare service en partities met betrouwbare actoren configureren in het cluster Servicefabric voor _automatisch herstellen._ In het back-upbeleid ingesteld `AutoRestore` op _true_. Als _u automatisch herstellen inschakelt,_ worden gegevens van de nieuwste partitieback-up automatisch hersteld wanneer gegevensverlies wordt gerapporteerd. Zie voor meer informatie:

- [Automatisch herstelinschakelen in back-upbeleid](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [Verwijzing naar RestorePartition-API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
- [GetPartitionRestoreProgress API-verwijzing](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Volgende stappen
- [Inzicht in periodieke back-upconfiguratie](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Verwijzing naar REST API-back-up herstellen](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[2]: ./media/service-fabric-backuprestoreservice/restore-partition-backup.png
[3]: ./media/service-fabric-backuprestoreservice/restore-partition-fileshare.png