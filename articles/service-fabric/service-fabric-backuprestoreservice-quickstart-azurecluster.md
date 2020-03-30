---
title: Periodic backup and restore in Azure Service Fabric (Periodieke back-up en herstel in Azure Service Fabric)
description: Gebruik de periodieke back-up- en herstelfunctie van Service Fabric voor het inschakelen van periodieke back-ups van uw toepassingsgegevens.
author: hrushib
ms.topic: conceptual
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: f56fcb7d1dde700d954c3b55bcf8cd7759893521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259004"
---
# <a name="periodic-backup-and-restore-in-an-azure-service-fabric-cluster"></a>Periodieke back-up en herstel in een Azure Service Fabric-cluster
> [!div class="op_single_selector"]
> * [Clusters op Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Zelfstandige clusters](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric is een gedistribueerd systeemplatform dat het eenvoudig maakt om betrouwbare, gedistribueerde, op microservices gebaseerde cloudapplicaties te ontwikkelen en te beheren. Het maakt het mogelijk het uitvoeren van zowel stateless en stateful micro-diensten. Stateful services kunnen mutable, gezaghebbende status behouden buiten de aanvraag en reactie of een volledige transactie. Als een Stateful service gaat voor een lange tijd of verliest informatie als gevolg van een ramp, kan het nodig zijn om te worden hersteld naar een aantal recente back-up van de staat om te blijven leveren van service nadat het komt weer omhoog.

Service Fabric repliceert de status op meerdere knooppunten om ervoor te zorgen dat de service zeer beschikbaar is. Zelfs als één knooppunt in het cluster mislukt, blijft de service beschikbaar. In bepaalde gevallen is het echter nog steeds wenselijk dat de servicegegevens betrouwbaar zijn tegen bredere storingen.
 
De service kan bijvoorbeeld een back-up van de gegevens maken om te beschermen tegen de volgende scenario's:
- In het geval van het permanente verlies van een volledig Service Fabric cluster.
- Permanent verlies van een meerderheid van de replica's van een servicepartitie
- Administratieve fouten waarbij de status per ongeluk wordt verwijderd of beschadigd. Een beheerder met voldoende bevoegdheden verwijdert bijvoorbeeld ten onrechte de service.
- Bugs in de service die gegevensbeschadiging veroorzaken. Dit kan bijvoorbeeld gebeuren wanneer een upgrade van een servicecode begint met het schrijven van foutieve gegevens naar een betrouwbare verzameling. In een dergelijk geval moeten zowel de code als de gegevens mogelijk worden teruggezet naar een eerdere status.
- Offline gegevensverwerking. Het kan handig zijn om offline gegevens te verwerken voor business intelligence die los van de service gebeurt die de gegevens genereert.

Service Fabric biedt een ingebouwde API om [point-in-time back-up smaken en herstellen.](service-fabric-reliable-services-backup-restore.md) Toepassingsontwikkelaars kunnen deze API's gebruiken om periodiek een back-up te maken van de status van de service. Als servicebeheerders op een bepaald moment een back-up van buiten de service willen activeren, zoals voordat ze de toepassing upgraden, moeten ontwikkelaars back-ups (en herstel) als api van de service blootstellen. Het onderhouden van de back-ups is een extra kosten boven deze. U bijvoorbeeld elk half uur vijf incrementele back-ups maken, gevolgd door een volledige back-up. Na de volledige back-up u de eerdere incrementele back-ups verwijderen. Deze aanpak vereist extra code die leidt tot extra kosten tijdens de ontwikkeling van toepassingen.

De back-up- en herstelservice in Service Fabric maakt eenvoudige en automatische back-ups mogelijk van informatie die is opgeslagen in stateful services. Het periodiek back-upmaken van toepassingsgegevens is van fundamenteel belang voor het beschermen tegen gegevensverlies en onbeschikbaarheid van service. Service Fabric biedt een optionele back-up- en herstelservice, waarmee u periodieke back-ups van stateful Reliable Services (inclusief Actor Services) configureren zonder extra code te hoeven schrijven. Het vergemakkelijkt ook het herstellen van eerder genomen back-ups. 


Service Fabric biedt een set API's om de volgende functionaliteit te bereiken met betrekking tot periodieke back-up- en herstelfunctie:

- Plan periodieke back-ups van betrouwbare stateful-services en betrouwbare actoren met ondersteuning om back-ups te uploaden naar (externe) opslaglocaties. Ondersteunde opslaglocaties
    - Azure Storage
    - Bestandsshare (on-premises)
- Back-ups opsommen
- Een ad-hocback-up van een partitie activeren
- Een partitie herstellen met eerdere back-up
- Back-ups tijdelijk onderbreken
- Retentiebeheer van back-ups (aanstaande)

## <a name="prerequisites"></a>Vereisten
* Service Fabric-cluster met Fabric-versie 6.4 of hoger. Raadpleeg dit [artikel](service-fabric-cluster-creation-via-arm.md) voor stappen om het cluster Servicefabric te maken met behulp van Azure-resourcesjabloon.
* X.509-certificaat voor versleuteling van geheimen die nodig zijn om verbinding te maken met opslag om back-ups op te slaan. Raadpleeg [het artikel](service-fabric-cluster-creation-via-arm.md) om te weten hoe u een X.509-certificaat krijgen of maken.
* Service Fabric Betrouwbare Stateful applicatie gebouwd met behulp van Service Fabric SDK versie 3.0 of hoger. Voor toepassingen die op .NET Core 2.0 zijn gericht, moet de toepassing worden gebouwd met servicefabric SDK-versie 3.1 of hoger.
* Azure Storage-account maken voor het opslaan van toepassingsback-ups.
* Installeer Microsoft.ServiceFabric.Powershell.Http-module [In Preview] voor het voeren van configuratiegesprekken.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* Controleer of cluster is `Connect-SFCluster` verbonden met de opdracht voordat u een configuratieverzoek indient met de module Microsoft.ServiceFabric.Powershell.Http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Back-up- en herstelservice inschakelen

### <a name="using-azure-portal"></a>Azure Portal gebruiken

Schakel `Include backup restore service` het `+ Show optional settings` selectievakje `Cluster Configuration` in onder het tabblad In.

![Back-upherstelservice inschakelen met portal][1]


### <a name="using-azure-resource-manager-template"></a>Sjabloon Azure Resource Manager gebruiken
Eerst moet u de _back-up- en herstelservice_ in uw cluster inschakelen. Download de sjabloon voor het cluster dat u wilt implementeren. U de [voorbeeldsjablonen](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) gebruiken of een resourcemanagersjabloon maken. Schakel de _back-up- en herstelservice_ in met de volgende stappen:

1. Controleer of `apiversion` de **`2018-02-01`** bron `Microsoft.ServiceFabric/clusters` is ingesteld op de bron en zo niet, of deze wordt bijgewerkt zoals in het volgende fragment wordt weergegeven:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Schakel nu de _back-up- en herstelservice_ in door de volgende `addonFeatures` sectie onder sectie toe te `properties` voegen, zoals in het volgende fragment wordt weergegeven: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Configureer X.509-certificaat voor versleuteling van referenties. Dit is belangrijk om ervoor te zorgen dat de referenties die worden verstrekt om verbinding te maken met opslag worden versleuteld voordat ze blijven bestaan. Configureer versleutelingscertificaat `BackupRestoreService` door `fabricSettings` de volgende sectie onder sectie toe te voegen zoals in het volgende fragment wordt weergegeven: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. Zodra u uw clustersjabloon hebt bijgewerkt met de voorgaande wijzigingen, past u deze toe en laat u de implementatie/upgrade voltooien. Zodra de _back-up- en herstelservice_ is voltooid, wordt deze in uw cluster uitgevoerd. De Uri van `fabric:/System/BackupRestoreService` deze service is en de service kan worden gevestigd onder systeem service sectie in de Service Fabric explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Periodieke back-up inschakelen voor betrouwbare stateful service en betrouwbare actoren
Laten we stappen doorlopen om periodieke back-ups mogelijk te maken voor betrouwbare stateful service en betrouwbare actoren. Deze stappen gaan ervan uit
- Dat het cluster is ingesteld met behulp van X.509-beveiliging met _back-up- en herstelservice._
- Er wordt een betrouwbare stateful-service op het cluster geïmplementeerd. Voor het doel van deze quickstart `fabric:/SampleApp` gids, applicatie Uri is en de `fabric:/SampleApp/MyStatefulService`Uri voor betrouwbare Stateful service die behoren tot deze applicatie is . Deze service wordt geïmplementeerd met één partitie `974bd92a-b395-4631-8a7f-53bd4ae9cf22`en de partitie-id is .
- Het clientcertificaat met beheerdersrol is geïnstalleerd in _Mijn_ (_Persoonlijke_) winkelnaam van _CurrentUser_ certificaatwinkellocatie op de machine van waaruit onderstaande scripts worden aangeroepen. In dit `1b7ebe2174649c45474a4819dafae956712c31d3` voorbeeld wordt de duimafdruk van dit certificaat gebruikt. Zie Op rollen gebaseerd [toegangscontrole voor Service Fabric-clients voor](service-fabric-cluster-security-roles.md)meer informatie over clientcertificaten.

### <a name="create-backup-policy"></a>Back-upbeleid maken

Eerste stap is het maken van back-up beleid beschrijven back-up schema, doel opslag voor back-upgegevens, beleidsnaam, maximale incrementele back-ups worden toegestaan voordat het activeren van de volledige back-up en retentie beleid voor back-up opslag. 

Voor back-upopslag gebruikt u het Azure Storage-account dat hierboven is gemaakt. Container `backup-container` is geconfigureerd om back-ups op te slaan. Een container met deze naam wordt gemaakt, als het niet reeds bestaat, tijdens back-up uploaden. Vul `ConnectionString` een geldige verbindingstekenreeks voor het Azure `account-name` Storage-account, vervangen `account-key` door de naam van uw opslagaccount en met uw opslagaccountsleutel.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell met Microsoft.ServiceFabric.Powershell.Http-module

Voer volgende PowerShell-cmdlets uit voor het maken van nieuw back-upbeleid. Vervang `account-name` door de naam `account-key` van uw opslagaccount en door uw opslagaccountsleutel.

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container' -Basic -RetentionDuration '10.00:00:00'

```

#### <a name="rest-call-using-powershell"></a>Restcall met PowerShell

Voer het volgende PowerShell-script uit voor het aanroepen van de vereiste REST API om nieuw beleid te maken. Vervang `account-name` door de naam `account-key` van uw opslagaccount en door uw opslagaccountsleutel.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

```

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer gebruiken

1. Navigeer in De Verkenner naar het tabblad Back-ups en selecteer Acties > Back-upbeleid maken.

    ![Back-upbeleid maken][6]

2. Vul de informatie in. Voor Azure-clusters moet AzureBlobStore worden geselecteerd.

    ![Back-upbeleid Azure Blob-opslag maken][7]

### <a name="enable-periodic-backup"></a>Periodieke back-up inschakelen
Nadat het back-upbeleid is gedefinieerd om te voldoen aan de vereisten voor gegevensbescherming van de toepassing, moet het back-upbeleid aan de toepassing worden gekoppeld. Afhankelijk van de vereiste kan het back-upbeleid worden gekoppeld aan een toepassing, service of partitie.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell met Microsoft.ServiceFabric.Powershell.Http-module

```powershell

Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'

```
#### <a name="rest-call-using-powershell"></a>Restcall met PowerShell

Voer het volgende PowerShell-script uit voor het `BackupPolicy1` aanroepen van de `SampleApp`vereiste REST API om het back-upbeleid te koppelen aan de naam die in bovenstaande stap met toepassing is gemaakt.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer gebruiken

1. Selecteer een toepassing en ga naar actie. Klik op Toepassingsback-up inschakelen/bijwerken.

    ![Toepassingsback-up inschakelen][3]

2. Selecteer ten slotte het gewenste beleid en klik op Back-up inschakelen.

    ![Beleid selecteren][4]


### <a name="verify-that-periodic-backups-are-working"></a>Controleren of periodieke back-ups werken

Na het inschakelen van back-up op toepassingsniveau, zullen alle partities die behoren tot betrouwbare stateful-services en betrouwbare actoren onder de toepassing periodiek worden ondersteund volgens het bijbehorende back-upbeleid. 

![Statusgebeurtenis Partition BackedUp][0]

### <a name="list-backups"></a>Back-ups weergeven

Back-ups die zijn gekoppeld aan alle partities die behoren tot betrouwbare stateful-services en betrouwbare actoren van de toepassing, kunnen worden opgesomd met behulp van _GetBackups_ API. Back-ups kunnen worden opgesomd voor een toepassing, service of partitie.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell met Microsoft.ServiceFabric.Powershell.Http-module

```powershell
    
Get-SFApplicationBackupList -ApplicationId WordCount
```

#### <a name="rest-call-using-powershell"></a>Restcall met PowerShell

Voer het volgende PowerShell-script uit om de HTTP-API aan te `SampleApp` roepen om de back-ups op te sommen die zijn gemaakt voor alle partities in de toepassing.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer gebruiken

Als u back-ups wilt weergeven in De Verkenner, navigeert u naar een partitie en selecteert u het tabblad Back-ups.

![Back-ups opsommen][5]

## <a name="limitation-caveats"></a>Beperking/ kanttekeningen
- Service Fabric PowerShell-cmdlets staan in de preview-modus.
- Geen ondersteuning voor Service Fabric clusters op Linux.

## <a name="next-steps"></a>Volgende stappen
- [Inzicht in periodieke back-upconfiguratie](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Verwijzing naar REST API-back-up herstellen](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event-azure.png
[1]: ./media/service-fabric-backuprestoreservice/enable-backup-restore-service-with-portal.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/creation-bp.png