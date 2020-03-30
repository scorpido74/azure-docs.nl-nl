---
title: Containerslogboeken weergeven in Azure Service Fabric
description: Beschrijft hoe u containerlogboeken weergeven voor een uitvoerende Service Fabric-containerservices met Service Fabric Explorer.
ms.topic: conceptual
ms.date: 05/15/2018
ms.openlocfilehash: c47a408b272f95dbfcf3d791c644bfeb52254a72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458182"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Logboeken weergeven voor een servicefabric-containerservice
Azure Service Fabric is een containerorchestrator en ondersteunt zowel Linux- als [Windows-containers.](service-fabric-containers-overview.md)  In dit artikel wordt beschreven hoe u containerlogboeken van een lopende containerservice of een dode container weergeven, zodat u problemen diagnosticeren en oplossen.

## <a name="access-the-logs-of-a-running-container"></a>Toegang tot de logboeken van een lopende container
Containerlogboeken zijn toegankelijk via [Service Fabric Explorer.](service-fabric-visualizing-your-cluster.md)  Open in een webbrowser Service Fabric Explorer vanaf het beheereindpunt `http://mycluster.region.cloudapp.azure.com:19080/Explorer`van het cluster door te navigeren naar .  

Containerlogboeken bevinden zich op het clusterknooppunt waarop de containerservice-instantie wordt uitgevoerd. Als voorbeeld, krijg de logs van de web front-end container van de [Linux Voting sample applicatie](service-fabric-quickstart-containers-linux.md). Vouw in de structuurweergave de fabric Van **Cluster**>**Clustertoepassingen**>**VotingType**>**uit:/Stemmen/azurevotefront**.  Vouw vervolgens de partitie uit (d1aa737e-f22a-e347-be16-eec90be24bc1, in dit voorbeeld) en zie dat de container op clusterknooppunt *_lnxvm_0*.

Zoek in de structuurweergave het codepakket op het *_lnxvm_0* knooppunt door **knooppunten**>uit te vouwen **_lnxvm_0**>**fabric:/Voting**>**azurevotfrontPkg**>**Code Packages**>**code**.  Selecteer vervolgens de optie **Containerlogboeken** om de containerlogboeken weer te geven.

![Service Fabric-platform][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Toegang tot de logboeken van een dode of gecrashte container
Vanaf v6.2 u de logboeken ook ophalen voor een dode of gecrashte container met [REST API's](/rest/api/servicefabric/sfclient-index) of [SFCTL-opdrachten (Service Fabric CLI).](service-fabric-cli.md)

### <a name="set-container-retention-policy"></a>Bewaarbeleid voor containers instellen
Om gemakkelijker opstartfouten bij containers te analyseren, ondersteunt Service Fabric (versie 6.1 of hoger) het bewaren van containers die zijn gestopt of niet kunnen starten. Dit beleid kan worden ingesteld in het bestand **ApplicationManifest.xml**, zoals u in het volgende fragment ziet:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

De instelling **ContainersRetentionCount** geeft aan hoeveel containers er moeten worden bewaard wanneer ze fouten genereren. Als er een negatieve waarde wordt opgegeven, worden alle niet goed werkende containers bewaard. Wanneer het kenmerk **ContainersRetentionCount** niet is opgegeven, worden er geen containers bewaard. Het kenmerk **ContainersRetentionCount** ondersteunt ook toepassingsparameters, zodat gebruikers verschillende waarden kunnen opgeven voor test- en productieclusters. Bij het gebruik van deze functies dient u plaatsingsbeperkingen in te stellen om de containerservice op een bepaald knooppunt te richten. Zo voorkomt u dat de containerservice naar een ander knooppunt wordt verplaatst. Containers die met behulp van deze functie zijn bewaard, moeten handmatig worden verwijderd.

De instelling **RunInteractive** komt overeen `--interactive` `tty` met Docker's en [vlaggen.](https://docs.docker.com/engine/reference/commandline/run/#options) Wanneer deze instelling is ingesteld op true in het manifestbestand, worden deze vlaggen gebruikt om de container te starten.  

### <a name="rest"></a>REST
Gebruik de bewerking [Containerlogboeken uitgezocht](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) op knooppunt om de logboeken voor een gecrashte container op te halen. Geef de naam op van het knooppunt waarop de container werd uitgevoerd, de naam van de toepassing, de naam van het servicemanifest en de naam van het codepakket.  Geef `&Previous=true` op. Het antwoord bevat de containerlogboeken voor de dode container van de instantie codepakket.

De aanvraag URI heeft het volgende formulier:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Voorbeeldaanvraag:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200 Reactieorgaan:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Servicefabric (SFCTL)
Gebruik de opdracht [sfctl-service get-container-logs](service-fabric-sfctl-service.md) om de logboeken voor een gecrashte container op te halen.  Geef de naam op van het knooppunt waarop de container werd uitgevoerd, de naam van de toepassing, de naam van het servicemanifest en de naam van het codepakket. Geef `--previous` de vlag op.  Het antwoord bevat de containerlogboeken voor de dode container van de instantie codepakket.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Reactie:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Volgende stappen
- Werk door de [zelfstudie van de Linux-containertoepassing maken.](service-fabric-tutorial-create-container-images.md)
- Meer informatie over [Service Fabric en containers](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
