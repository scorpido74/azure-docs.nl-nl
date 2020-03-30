---
title: Periodieke back-up/herstel in zelfstandige Azure Service Fabric
description: Gebruik de periodieke back-up- en herstelfunctie van Service Fabric voor het inschakelen van periodieke back-ups van uw toepassingsgegevens.
author: hrushib
ms.topic: conceptual
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: 938cbbde9f53c52350ef64715f6c61c4aa961057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75526240"
---
# <a name="periodic-backup-and-restore-in-a-standalone-service-fabric"></a>Periodieke back-up en herstel in een standalone Service Fabric
> [!div class="op_single_selector"]
> * [Clusters op Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Zelfstandige clusters](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric is een gedistribueerd systeemplatform dat het eenvoudig maakt om betrouwbare, gedistribueerde, op microservices gebaseerde cloudapplicaties te ontwikkelen en te beheren. Het maakt het mogelijk het uitvoeren van zowel stateless en stateful micro-diensten. Stateful services kunnen mutable, gezaghebbende status behouden buiten de aanvraag en reactie of een volledige transactie. Als een Stateful service gaat voor een lange tijd of verliest informatie als gevolg van een ramp, kan het nodig zijn om te worden hersteld naar een aantal recente back-up van de staat om te blijven leveren van service nadat het komt weer omhoog.

Service Fabric repliceert de status op meerdere knooppunten om ervoor te zorgen dat de service zeer beschikbaar is. Zelfs als één knooppunt in het cluster mislukt, blijft de service beschikbaar. In bepaalde gevallen is het echter nog steeds wenselijk dat de servicegegevens betrouwbaar zijn tegen bredere storingen.
 
Een service kan bijvoorbeeld een back-up van de gegevens willen maken om te beschermen tegen de volgende scenario's:
- Permanent verlies van een volledig Service Fabric-cluster.
- Permanent verlies van een meerderheid van de replica's van een servicepartitie
- Administratieve fouten waarbij de status per ongeluk wordt verwijderd of beschadigd. Een beheerder met voldoende bevoegdheden verwijdert bijvoorbeeld ten onrechte de service.
- Bugs in de service die gegevensbeschadiging veroorzaken. Dit kan bijvoorbeeld gebeuren wanneer een upgrade van een servicecode begint met het schrijven van foutieve gegevens naar een betrouwbare verzameling. In een dergelijk geval moeten zowel de code als de gegevens mogelijk worden teruggezet naar een eerdere status.
- Offline gegevensverwerking. Het kan handig zijn om offline gegevens te verwerken voor business intelligence die los van de service gebeurt die de gegevens genereert.

Service Fabric biedt een ingebouwde API om [point-in-time back-up smaken en herstellen.](service-fabric-reliable-services-backup-restore.md) Toepassingsontwikkelaars kunnen deze API's gebruiken om periodiek een back-up te maken van de status van de service. Als servicebeheerders op een bepaald moment een back-up van buiten de service willen activeren, zoals voordat ze de toepassing upgraden, moeten ontwikkelaars back-ups (en herstel) als api van de service blootstellen. Het onderhouden van de back-ups is een extra kosten boven deze. U bijvoorbeeld elk half uur vijf incrementele back-ups maken, gevolgd door een volledige back-up. Na de volledige back-up u de eerdere incrementele back-ups verwijderen. Deze aanpak vereist extra code die leidt tot extra kosten tijdens de ontwikkeling van toepassingen.

Een back-up van de toepassingsgegevens op periodieke basis is een basisbehoefte voor het beheren van een gedistribueerde toepassing en het bewaken tegen verlies van gegevens of langdurig verlies van beschikbaarheid van de service. Service Fabric biedt een optionele back-up- en herstelservice, waarmee u periodieke back-ups van stateful Reliable Services (inclusief Actor Services) configureren zonder extra code te hoeven schrijven. Het vergemakkelijkt ook het herstellen van eerder genomen back-ups. 

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
* Service Fabric-cluster met Fabric-versie 6.4 of hoger. Raadpleeg dit [artikel](service-fabric-cluster-creation-for-windows-server.md) voor stappen om het vereiste pakket te downloaden.
* X.509-certificaat voor versleuteling van geheimen die nodig zijn om verbinding te maken met opslag om back-ups op te slaan. Raadpleeg [het artikel](service-fabric-windows-cluster-x509-security.md) om te weten hoe u een zelfondertekend X.509-certificaat verkrijgen of maken.

* Service Fabric Betrouwbare Stateful applicatie gebouwd met behulp van Service Fabric SDK versie 3.0 of hoger. Voor toepassingen die zich richten op .Net Core 2.0, moet de toepassing worden gebouwd met behulp van Service Fabric SDK-versie 3.1 of hoger.
* Installeer Microsoft.ServiceFabric.Powershell.Http-module [In Preview] voor het voeren van configuratiegesprekken.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* Controleer of cluster is `Connect-SFCluster` verbonden met de opdracht voordat u een configuratieverzoek indient met de module Microsoft.ServiceFabric.Powershell.Http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Back-up- en herstelservice inschakelen
Eerst moet u de _back-up- en herstelservice_ in uw cluster inschakelen. Download de sjabloon voor het cluster dat u wilt implementeren. U de [voorbeeldsjablonen](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)gebruiken. Schakel de _back-up- en herstelservice_ in met de volgende stappen:

1. Controleer of `apiversion` de `10-2017` is ingesteld op in het clusterconfiguratiebestand en zo niet, het bijwerken zoals weergegeven in het volgende fragment:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Schakel nu de _back-up- en herstelservice_ in door de volgende `addonFeatures` sectie onder sectie toe te `properties` voegen, zoals in het volgende fragment wordt weergegeven: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Configureer X.509-certificaat voor versleuteling van referenties. Dit is belangrijk om ervoor te zorgen dat de eventuele referenties om verbinding te maken met opslag worden versleuteld voordat ze blijven bestaan. Configureer versleutelingscertificaat `BackupRestoreService` door `fabricSettings` de volgende sectie onder sectie toe te voegen zoals in het volgende fragment wordt weergegeven: 

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

4. Zodra u uw clusterconfiguratiebestand hebt bijgewerkt met de voorgaande wijzigingen, past u deze toe en laat u de implementatie/upgrade voltooien. Zodra de _back-up- en herstelservice_ is voltooid, wordt deze in uw cluster uitgevoerd. De Uri van `fabric:/System/BackupRestoreService` deze service is en de service kan worden gevestigd onder systeem service sectie in de Service Fabric explorer. 



## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Periodieke back-up inschakelen voor betrouwbare stateful service en betrouwbare actoren
Laten we stappen doorlopen om periodieke back-ups mogelijk te maken voor betrouwbare stateful service en betrouwbare actoren. Deze stappen gaan ervan uit
- Dat het cluster is ingesteld met _back-up- en herstelservice._
- Er wordt een betrouwbare stateful-service op het cluster geïmplementeerd. Voor het doel van deze quickstart `fabric:/SampleApp` gids, applicatie Uri is en de `fabric:/SampleApp/MyStatefulService`Uri voor betrouwbare Stateful service die behoren tot deze applicatie is . Deze service wordt geïmplementeerd met één partitie `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`en de partitie-id is .  

### <a name="create-backup-policy"></a>Back-upbeleid maken

Eerste stap is het maken van back-up beleid beschrijven back-up schema, doel opslag voor back-upgegevens, beleidsnaam, maximale incrementele back-ups worden toegestaan voordat het activeren van de volledige back-up en retentie beleid voor back-up opslag. 

Maak voor back-upopslag bestandsshare en geef ReadWrite toegang tot deze bestandsshare voor alle servicefabricknooppuntmachines. In dit voorbeeld wordt `BackupStore` ervan uitgegaan `StorageServer`dat het aandeel met de naam aanwezig is op .


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell met Microsoft.ServiceFabric.Powershell.Http-module

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -FileShare -Path '\\StorageServer\BackupStore' -Basic -RetentionDuration '10.00:00:00'

```
#### <a name="rest-call-using-powershell"></a>Restcall met Powershell

Voer het volgende PowerShell-script uit voor het aanroepen van de vereiste REST API om nieuw beleid te maken.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
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
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer gebruiken

1. Navigeer in De Verkenner naar het tabblad Back-ups en selecteer Acties > Back-upbeleid maken.

    ![Back-upbeleid maken][6]

2. Vul de informatie in. Voor zelfstandige clusters moet FileShare worden geselecteerd.

    ![FileShare back-upbeleid maken][7]

### <a name="enable-periodic-backup"></a>Periodieke back-up inschakelen
Nadat het beleid is gedefinieerd om te voldoen aan de vereisten voor gegevensbescherming van de toepassing, moet het back-upbeleid aan de toepassing worden gekoppeld. Afhankelijk van de vereiste kan het back-upbeleid worden gekoppeld aan een toepassing, service of partitie.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell met Microsoft.ServiceFabric.Powershell.Http-module

```powershell
Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'
```

#### <a name="rest-call-using-powershell"></a>Restcall met Powershell
Voer het volgende PowerShell-script uit voor het `BackupPolicy1` aanroepen van de `SampleApp`vereiste REST API om het back-upbeleid te koppelen aan de naam die in bovenstaande stap met toepassing is gemaakt.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer gebruiken

1. Selecteer een toepassing en ga naar actie. Klik op Toepassingsback-up inschakelen/bijwerken.

    ![Toepassingsback-up inschakelen][3] 

2. Selecteer ten slotte het gewenste beleid en klik op Back-up inschakelen.

    ![Beleid selecteren][4]

### <a name="verify-that-periodic-backups-are-working"></a>Controleren of periodieke back-ups werken

Na het inschakelen van back-up voor de toepassing, zullen alle partities die behoren tot betrouwbare stateful-services en betrouwbare actoren onder de toepassing periodiek worden ondersteund volgens het bijbehorende back-upbeleid.

![Statusgebeurtenis Partition BackedUp][0]

### <a name="list-backups"></a>Back-ups weergeven

Back-ups die zijn gekoppeld aan alle partities die behoren tot betrouwbare stateful-services en betrouwbare actoren van de toepassing, kunnen worden opgesomd met behulp van _GetBackups_ API. Afhankelijk van de vereiste kunnen de back-ups worden opgesomd voor toepassing, service of partitie.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell met Microsoft.ServiceFabric.Powershell.Http-module

```powershell
    Get-SFApplicationBackupList -ApplicationId WordCount     
```

#### <a name="rest-call-using-powershell"></a>Restcall met Powershell

Voer het volgende PowerShell-script uit om de HTTP-API aan te `SampleApp` roepen om de back-ups op te sommen die zijn gemaakt voor alle partities in de toepassing.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Voorbeelduitvoer voor de bovenstaande run:

```
BackupId                : d7e4038e-2c46-47c6-9549-10698766e714
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.39.40.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2058
CreationTimeUtc         : 2018-04-01T19:39:40Z
FailureError            : 

BackupId                : 8c21398a-2141-4133-b4d7-e1a35f0d7aac
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.54.38.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2237
CreationTimeUtc         : 2018-04-01T19:54:38Z
FailureError            : 

BackupId                : fc75bd4c-798c-4c9a-beee-e725321f73b2
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 20.09.44.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2437
CreationTimeUtc         : 2018-04-01T20:09:44Z
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

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/create-bp-fileshare.png