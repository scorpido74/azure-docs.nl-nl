---
title: On-demand back-up in Azure Service Fabric
description: Gebruik de back-up- en herstelfunctie in Service Fabric om een back-up te maken van uw toepassingsgegevens op basis van een behoefte.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d5eada62bec49fe771373671e9438d2786d6b165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458417"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>On-demand back-up in Azure Service Fabric

U back-ups maken van gegevens van betrouwbare stateful-services en betrouwbare actoren om scenario's voor rampen of gegevensverlies aan te pakken.

Azure Service Fabric heeft functies voor de [periodieke back-up van gegevens](service-fabric-backuprestoreservice-quickstart-azurecluster.md) en de back-up van gegevens op basis van behoefte. On-demand back-up is handig omdat deze wordt beschermd tegen _gegevensverlies_/_als gevolg_ van geplande wijzigingen in de onderliggende service of de omgeving.

De on-demand back-upfuncties zijn handig voor het vastleggen van de status van de services voordat u handmatig een service- of serviceomgevingbewerking activeert. Als u bijvoorbeeld een wijziging aanbrengt in servicebinaries bij het upgraden of downgraden van de service. In een dergelijk geval kan on-demand back-up helpen de gegevens te beschermen tegen corruptie door toepassingscodebugs.
## <a name="prerequisites"></a>Vereisten

- Installeer Microsoft.ServiceFabric.Powershell.Http-module [In Preview] voor het voeren van configuratiegesprekken.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Controleer of cluster is `Connect-SFCluster` verbonden met de opdracht voordat u een configuratieverzoek indient met de module Microsoft.ServiceFabric.Powershell.Http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggering-on-demand-backup"></a>Back-up smaken op aanvraag activeren

On-demand back-up vereist opslaggegevens voor het uploaden van back-upbestanden. U geeft de on-demand back-uplocatie op, hetzij in het periodieke back-upbeleid of in een on-demand back-upaanvraag.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Back-up op aanvraag naar opslag die is opgegeven door een periodiek back-upbeleid

U het periodieke back-upbeleid configureren om een partitie van een betrouwbare stateful-service of betrouwbare actor te gebruiken voor extra on-demand back-up naar opslag.

Het volgende geval is de voortzetting van het scenario in [het inschakelen van periodieke back-up voor betrouwbare stateful service en betrouwbare actoren](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). In dit geval schakelt u een back-upbeleid in om een partitie te gebruiken en vindt een back-up plaats op een ingestelde frequentie in Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell met Microsoft.ServiceFabric.Powershell.Http-module

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>Restcall met Powershell

Gebruik de [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API om triggering in te stellen `974bd92a-b395-4631-8a7f-53bd4ae9cf22`voor de on-demand back-up voor partitie-id.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Gebruik de [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API om tracking in te schakelen voor de voortgang van de [back-up op aanvraag.](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)

### <a name="on-demand-backup-to-specified-storage"></a>Back-up op aanvraag naar opgegeven opslag

U on-demand back-up aanvragen voor een partitie van een betrouwbare stateful-service of betrouwbare actor. Geef de opslaggegevens op als onderdeel van de on-demand back-upaanvraag.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell met Microsoft.ServiceFabric.Powershell.Http-module

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Restcall met Powershell

Gebruik de [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API om triggering in te stellen `974bd92a-b395-4631-8a7f-53bd4ae9cf22`voor de on-demand back-up voor partitie-id. Neem de volgende Azure Storage-gegevens op:

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

U de [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API gebruiken om tracking in te stellen voor de voortgang van de [back-up op aanvraag.](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)

### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer gebruiken
Controleer of de geavanceerde modus is ingeschakeld in de instellingen van Service Fabric Explorer.
1. Selecteer de gewenste partities en klik op Acties. 
2. Selecteer Trigger Partition Backup en vul gegevens in voor Azure:

    ![Partitieback-up activeren][0]

    of FileShare:

    ![Bestandsshare over de partitieback-up activeren][1]

## <a name="tracking-on-demand-backup-progress"></a>Voortgang van back-ups op aanvraag bijhouden

Een partitie van een betrouwbare stateful-service of betrouwbare actor accepteert slechts één on-demand back-upaanvraag tegelijk. Een ander verzoek kan alleen worden geaccepteerd nadat de huidige on-demand back-upaanvraag is voltooid.

Verschillende partities kunnen back-upaanvragen op aanvraag tegelijkertijd activeren.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell met Microsoft.ServiceFabric.Powershell.Http-module

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>Restcall met Powershell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

On-demand back-upaanvragen kunnen in de volgende statussen zijn:

- **Geaccepteerd**: De back-up is gestart op de partitie en is in uitvoering.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Succes,** **Mislukt**of **Time-out:** Een aangevraagde on-demand back-up kan worden voltooid in een van de volgende staten:
  - **Succes**: Een back-upstatus _voor succes_ geeft aan dat er een back-up is gemaakt van de partitiestatus. Het antwoord biedt _BackupEpoch_ en _BackupLSN_ voor de partitie, samen met de tijd in UTC.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Fout:** een back-upstatus _van een fout_ geeft aan dat er een fout is opgetreden tijdens de back-up van de status van de partitie. De oorzaak van de storing wordt vermeld in reactie.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Time-out**: een _time-outback-upstatus_ geeft aan dat de back-up van de partitiestatus niet in een bepaalde tijd kan worden gemaakt. De standaardtime-outwaarde is 10 minuten. Start in dit scenario een nieuwe on-demand back-upaanvraag met een grotere [Back-uptimeout.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout)
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>Volgende stappen

- [Inzicht in periodieke back-upconfiguratie](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Referentie back-upREST-API back-up](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/trigger-partition-backup.png
[1]: ./media/service-fabric-backuprestoreservice/trigger-backup-fileshare.png