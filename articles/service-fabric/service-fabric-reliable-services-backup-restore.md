---
title: Back-up en herstel van Service Fabric
description: Conceptuele documentatie voor Service Fabric Backup and Restore, een service voor het configureren van back-ups van betrouwbare stateful services en betrouwbare actoren.
author: mcoskun
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: mcoskun
ms.openlocfilehash: ac6bb14517b67a4b308460583e8c9fb99a2df9f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75922782"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Back-ups en herstel van betrouwbare services en betrouwbare actoren
Azure Service Fabric is een platform met hoge beschikbaarheid dat de status over meerdere knooppunten repliceert om deze hoge beschikbaarheid te behouden.  Dus, zelfs als een knooppunt in het cluster mislukt, blijven de services beschikbaar. Hoewel deze ingebouwde redundantie die door het platform wordt geleverd voor sommigen voldoende kan zijn, is het in bepaalde gevallen wenselijk dat de service een back-up maakt van gegevens (naar een externe winkel).

> [!NOTE]
> Het is essentieel om een back-up te maken en uw gegevens te herstellen (en te testen of deze werken zoals verwacht), zodat u herstellen van scenario's voor gegevensverlies.
> 

> [!NOTE]
> Microsoft raadt aan om [periodieke back-ups en herstel](service-fabric-backuprestoreservice-quickstart-azurecluster.md) te gebruiken voor het configureren van gegevensback-up van betrouwbare stateful-services en betrouwbare actoren. 
> 


Een service kan bijvoorbeeld een back-up van gegevens maken om te beschermen tegen de volgende scenario's:

- In het geval van het permanente verlies van een volledig Service Fabric cluster.
- Permanent verlies van een meerderheid van de replica's van een servicepartitie
- Administratieve fouten waarbij de status per ongeluk wordt verwijderd of beschadigd. Dit kan bijvoorbeeld gebeuren als een beheerder met voldoende bevoegdheden de service ten onrechte verwijdert.
- Bugs in de service die gegevensbeschadiging veroorzaken. Dit kan bijvoorbeeld gebeuren wanneer een upgrade van een servicecode begint met het schrijven van foutieve gegevens naar een betrouwbare verzameling. In een dergelijk geval moeten zowel de code als de gegevens mogelijk worden teruggezet naar een eerdere status.
- Offline gegevensverwerking. Het kan handig zijn om offline gegevens te verwerken voor business intelligence die los van de service gebeurt die de gegevens genereert.

Met de functie Back-up/herstel kunnen services die zijn gebouwd op de API voor Betrouwbare Services back-ups maken en herstellen. De back-up-API's die door het platform worden geleverd, maken back-up(s) van de status van een servicepartitie mogelijk, zonder lees- of schrijfbewerkingen te blokkeren. Met de herstel-API's kan de status van een servicepartitie worden hersteld vanuit een gekozen back-up.

## <a name="types-of-backup"></a>Typen back-ups
Er zijn twee back-upopties: Volledig en incrementeel.
Een volledige back-up is een back-up die alle gegevens bevat die nodig zijn om de status van de replica opnieuw te maken: controlepunten en alle logboekrecords.
Omdat het de controlepunten en het logboek heeft, kan een volledige back-up vanzelf worden hersteld.

Het probleem met volledige back-ups doet zich voor wanneer de controlepunten groot zijn.
Een replica met een status van 16 GB heeft bijvoorbeeld controlepunten die ongeveer 16 GB bedragen.
Als we een herstelpuntdoelstelling van vijf minuten hebben, moet de replica elke vijf minuten worden geback-upt.
Elke keer dat het een back-up moet kopiëren 16 GB van checkpoints in aanvulling op 50 MB (configureerbaar met behulp `CheckpointThresholdInMB`van) ter waarde van logs.

![Voorbeeld van volledige back-up.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

De oplossing voor dit probleem is incrementele back-ups, waarbij back-ups alleen de gewijzigde logboekrecords bevatten sinds de laatste back-up.

![Voorbeeld van incrementele back-up.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Aangezien incrementele back-ups alleen wijzigingen zijn sinds de laatste back-up (bevat niet de controlepunten), zijn ze meestal sneller, maar kunnen ze niet alleen worden hersteld.
Om een incrementele back-up te herstellen, is de volledige back-upketen vereist.
Een back-upketen is een keten van back-ups die beginnen met een volledige back-up en gevolgd worden door een aantal aaneengesloten incrementele back-ups.

## <a name="backup-reliable-services"></a>Back-up maken van betrouwbare services
De auteur van de service heeft volledige controle over wanneer u back-ups moet maken en waar back-ups worden opgeslagen.

Als u een back-up wilt starten, `BackupAsync`moet de service de overgenomen lidfunctie aanroepen.  
Back-ups kunnen alleen worden gemaakt van primaire replica's, en ze vereisen schrijfstatus worden verleend.

Zoals hieronder `BackupAsync` wordt weergegeven, neemt in een `BackupDescription` object, waar men kan een volledige `Func<< BackupInfo, CancellationToken, Task<bool>>>` of incrementele back-up op te geven, evenals een callback functie, die wordt aangeroepen wanneer de back-up map is lokaal gemaakt en is klaar om te worden verplaatst naar een aantal externe opslag.

```csharp

BackupDescription myBackupDescription = new BackupDescription(BackupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Verzoek om een incrementele `FabricMissingFullBackupException`back-up te nemen kan mislukken met . Deze uitzondering geeft aan dat een van de volgende dingen gebeurt:

- de replica heeft nog nooit een volledige back-up genomen, omdat het is geworden primaire,
- sommige logboekrecords sinds de laatste back-up is afgekapt of
- replica de `MaxAccumulatedBackupLogSizeInMB` limiet gepasseerd.

Gebruikers kunnen de kans vergroten dat ze incrementele `MinLogSizeInMB` `TruncationThresholdFactor`back-ups kunnen maken door het configureren of .
Als u deze waarden verhoogt, wordt het gebruik per replica verhoogd.
Zie [Configuratie van betrouwbare services](service-fabric-reliable-services-configuration.md) voor meer informatie

`BackupInfo`geeft informatie over de back-up, inclusief de locatie van`BackupInfo.Directory`de map waar de runtime de back-up heeft opgeslagen ( ). De callback-functie `BackupInfo.Directory` kan de functie verplaatsen naar een externe winkel of een andere locatie.  Deze functie retourneert ook een bool die aangeeft of deze in staat is om de back-upmap naar de doellocatie te verplaatsen.

De volgende code laat `BackupCallbackAsync` zien hoe de methode kan worden gebruikt om de back-up naar Azure Storage te uploaden:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

In het voorgaande `ExternalBackupStore` voorbeeld is de voorbeeldklasse die wordt gebruikt `UploadBackupFolderAsync` om te communiceren met Azure Blob-opslag en is de methode die de map comprimeert en plaatst in de Azure Blob-opslag.

Opmerking:

  - Er kan slechts één back-upoperatie tijdens de vlucht per replica op een bepaald moment zijn. Meer dan `BackupAsync` een gesprek per `FabricBackupInProgressException` keer zal gooien om inflight back-ups te beperken tot een.
  - Als een replica mislukt terwijl een back-up aan de gang is, is de back-up mogelijk niet voltooid. Zodra de failover is voltooid, is het dus de verantwoordelijkheid `BackupAsync` van de service om de back-up opnieuw op te starten door een beroep te doen op desnoods.

## <a name="restore-reliable-services"></a>Betrouwbare services herstellen
In het algemeen vallen de gevallen waarin u mogelijk een herstelbewerking moet uitvoeren, in een van de volgende categorieën:

  - De servicepartitie heeft gegevens verloren. De schijf voor twee van de drie replica's voor een partitie (inclusief de primaire replica) wordt bijvoorbeeld beschadigd of gewist. Het nieuwe primaire kan nodig zijn om gegevens van een back-up te herstellen.
  - De hele service gaat verloren. Een beheerder verwijdert bijvoorbeeld de volledige service en dus moeten de service en de gegevens worden hersteld.
  - De service heeft corrupte toepassingsgegevens gerepliceerd (bijvoorbeeld vanwege een toepassingsbug). In dit geval moet de service worden bijgewerkt of teruggedraaid om de oorzaak van de corruptie te verwijderen en moeten niet-corrupte gegevens worden hersteld.

Hoewel veel benaderingen mogelijk zijn, bieden `RestoreAsync` we enkele voorbeelden over het gebruik om te herstellen van de bovenstaande scenario's.

## <a name="partition-data-loss-in-reliable-services"></a>Verlies van partitiegegevens in betrouwbare services
In dit geval detecteert de runtime automatisch het `OnDataLossAsync` gegevensverlies en beroept zich op de API.

De auteur van de service moet het volgende uitvoeren om te herstellen:

  - Overschrijf de methode `OnDataLossAsync`voor virtuele basisklasse .
  - Zoek de nieuwste back-up op de externe locatie die de back-ups van de service bevat.
  - Download de nieuwste back-up (en comprimeer de back-up in de back-upmap als deze is gecomprimeerd).
  - De `OnDataLossAsync` methode `RestoreContext`biedt een . Roep `RestoreAsync` de API `RestoreContext`aan op de opgegeven .
  - Terugkeer waar als de restauratie een succes was.

Hieronder volgt een voorbeeld `OnDataLossAsync` implementatie van de methode:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription`doorgegeven aan `RestoreContext.RestoreAsync` de oproep `BackupFolderPath`bevat een lid genaamd .
Wanneer u één volledige `BackupFolderPath` back-up herstelt, moet deze worden ingesteld op het lokale pad van de map met uw volledige back-up.
Bij het herstellen van een volledige back-up en een aantal incrementele back-ups, `BackupFolderPath` moet worden ingesteld op het lokale pad van de map die niet alleen de volledige back-up bevat, maar ook alle incrementele back-ups.
`RestoreAsync`oproep kan `FabricMissingFullBackupException` gooien `BackupFolderPath` als de meegeleverde niet een volledige back-up bevatten.
Het kan `ArgumentException` ook `BackupFolderPath` gooien als heeft een gebroken keten van incrementele back-ups.
Bijvoorbeeld, als het bevat de volledige back-up, de eerste incrementele en de derde incrementele back-up, maar geen de tweede incrementele back-up.

> [!NOTE]
> Het herstelbeleid is standaard ingesteld op Veilig.  Dit betekent `RestoreAsync` dat de API zal mislukken met ArgumentException als deze detecteert dat de back-upmap een status bevat die ouder is dan of gelijk is aan de status in deze replica.  `RestorePolicy.Force`kan worden gebruikt om deze veiligheidscontrole over te slaan. Dit is opgegeven `RestoreDescription`als onderdeel van .
> 

## <a name="deleted-or-lost-service"></a>Verwijderde of verloren service
Als een service wordt verwijderd, moet u de service eerst opnieuw maken voordat de gegevens kunnen worden hersteld.  Het is belangrijk om de service met dezelfde configuratie te maken, bijvoorbeeld partitioneringsschema, zodat de gegevens naadloos kunnen worden hersteld.  Zodra de service is ingeschakeld, moet`OnDataLossAsync` de API om gegevens (hierboven) te herstellen worden aangeroepen op elke partitie van deze service. Een manier om dit te bereiken is door [fabricclient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx) op elke partitie te gebruiken.  

Vanaf dit punt is de implementatie hetzelfde als het bovenstaande scenario. Elke partitie moet de nieuwste relevante back-up van de externe winkel herstellen. Een voorbehoud is dat de partitie-ID nu kan zijn veranderd, omdat de runtime partitie-id's dynamisch creëert. De service moet dus de juiste partitie-informatie en servicenaam opslaan om de juiste nieuwste back-up te identificeren die voor elke partitie moet worden hersteld.

> [!NOTE]
> Het wordt niet aanbevolen `FabricClient.ServiceManager.InvokeDataLossAsync` om op elke partitie te gebruiken om de hele service te herstellen, omdat dat uw clusterstatus kan beschadigen.
> 

## <a name="replication-of-corrupt-application-data"></a>Replicatie van corrupte toepassingsgegevens
Als de nieuw geïmplementeerde toepassingsupgrade een bug heeft, kan dat leiden tot beschadiging van gegevens. Een upgrade van een toepassing kan bijvoorbeeld beginnen met het bijwerken van elke record van telefoonnummers in een betrouwbaar woordenboek met een ongeldig netnummer.  In dit geval worden de ongeldige telefoonnummers gerepliceerd omdat Service Fabric zich niet bewust is van de aard van de gegevens die worden opgeslagen.

Het eerste wat u moet doen nadat u zo'n flagrante bug detecteert die gegevenscorruptie veroorzaakt, is de service op toepassingsniveau te bevriezen en, indien mogelijk, te upgraden naar de versie van de toepassingscode die de bug niet heeft.  Echter, zelfs nadat de servicecode is vastgesteld, kunnen de gegevens nog steeds beschadigd zijn, en dus moeten gegevens mogelijk worden hersteld.  In dergelijke gevallen is het mogelijk niet voldoende om de nieuwste back-up te herstellen, omdat de nieuwste back-ups ook beschadigd kunnen zijn.  Zo moet je de laatste back-up die werd gemaakt voordat de gegevens beschadigd.

Als u niet zeker weet welke back-ups beschadigd zijn, u een nieuw Cluster Van Service Fabric implementeren en de back-ups van de getroffen partities herstellen, net als het bovenstaande scenario 'Verwijderd of verloren service'.  Voor elke partitie begint u met het herstellen van de back-ups van de meest recente naar de minst. Zodra u een back-up hebt gevonden die de corruptie niet heeft, verplaatst/verwijdert u alle back-ups van deze partitie die recenter waren (dan die back-up). Herhaal dit proces voor elke partitie. Wanneer de `OnDataLossAsync` partitie in het productiecluster wordt aangeroepen, is de laatste back-up in het externe archief de back-up die door het bovenstaande proces is gekozen.

Nu kunnen de stappen in de sectie 'Verwijderde of verloren service' worden gebruikt om de status van de service naar de status te herstellen voordat de buggycode de status heeft beschadigd.

Opmerking:

  - Wanneer u herstelt, bestaat de kans dat de back-up die wordt hersteld ouder is dan de status van de partitie voordat de gegevens verloren zijn gegaan. Daarom moet u alleen herstellen als laatste redmiddel om zoveel mogelijk gegevens te herstellen.
  - De tekenreeks die het pad van de back-upmap en de paden van bestanden in de back-upmap vertegenwoordigt, kan groter zijn dan 255 tekens, afhankelijk van het pad van FabricDataRoot en de lengte van de naam van het toepassingstype. Dit kan ertoe leiden dat `Directory.Move`sommige .NET-methoden, zoals , de `PathTooLongException` uitzondering gooien. Een tijdelijke oplossing is om direct kernel32 API's te bellen, zoals `CopyFile`.

## <a name="back-up-and-restore-reliable-actors"></a>Back-up en herstel betrouwbare actoren


Reliable Actors Framework is gebouwd op de top van betrouwbare diensten. De ActorService, die de actor(s) host, is een stateful betrouwbare service. Daarom is alle back-up- en herstelfunctionaliteit die beschikbaar is in Betrouwbare Services ook beschikbaar voor betrouwbare actoren (behalve gedragdat specifiek is voor de statusprovider). Aangezien back-ups per partitie worden genomen, worden staten voor alle actoren in die partitie een back-up gemaakt (en de restauratie is vergelijkbaar en zal per partitie plaatsvinden). Als u back-ups/herstel wilt uitvoeren, moet de service-eigenaar van de service een aangepaste actorserviceklasse maken die is afgeleid van de klasse ActorService en vervolgens back-ups/hersteluitvoeren die vergelijkbaar is met betrouwbare services zoals hierboven beschreven in eerdere secties.

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo)
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

Wanneer u een aangepaste actorserviceklasse maakt, moet u dat ook registreren bij het registreren van de actor.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

De standaardprovider voor betrouwbare `KvsActorStateProvider`actoren is . Incrementele back-up is `KvsActorStateProvider`standaard niet ingeschakeld voor . U incrementele `KvsActorStateProvider` back-up inschakelen door te maken met de juiste instelling in de constructor en deze vervolgens door te geven aan ActorService-constructor zoals weergegeven in het volgende codefragment:

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo, null, null, new KvsActorStateProvider(true)) // Enable incremental backup
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

Nadat incrementele back-up is ingeschakeld, kan het nemen van een incrementele back-up mislukken met FabricMissingFullBackupException om een van de volgende redenen en moet u een volledige back-up maken voordat u incrementele back-up(s) maakt:

  - De replica heeft nooit een volledige back-up genomen sinds het primaire werd.
  - Sommige van de log records werden afgekapt sinds de laatste back-up werd genomen.

Wanneer incrementele back-up is ingeschakeld, `KvsActorStateProvider` gebruikt u geen cirkelvormige buffer om de logboekrecords te beheren en wordt deze periodiek afgekapt. Als de gebruiker gedurende een periode van 45 minuten geen back-up maakt, wordt de logboekrecords automatisch afgekapt door de gebruiker. Dit interval kan worden geconfigureerd `logTruncationIntervalInMinutes` `KvsActorStateProvider` door op te geven in constructor (vergelijkbaar met bij het inschakelen van incrementele back-up). De logboekrecords kunnen ook worden afgekapt als primaire replica een andere replica moet bouwen door alle gegevens te verzenden.

Wanneer u herstelt vanuit een back-upketen, vergelijkbaar met Betrouwbare Services, moet het BackupFolderPath submappen bevatten met één submap met volledige back-up en andere submappen met incrementele back-up(s). De herstel-API gooit FabricException met de juiste foutmelding als de validatie van de back-upketen mislukt. 

> [!NOTE]
> `KvsActorStateProvider`hiermee wordt de optie RestorePolicy.Safe genegeerd. Ondersteuning voor deze functie is gepland in een aankomende release.
> 

## <a name="testing-back-up-and-restore"></a>Back-ups testen en herstellen
Het is belangrijk om ervoor te zorgen dat kritieke gegevens worden geback-upt en kunnen worden hersteld van. Dit kan worden gedaan `Start-ServiceFabricPartitionDataLoss` door een beroep te doen op de cmdlet in PowerShell die gegevensverlies in een bepaalde partitie kan veroorzaken om te testen of de functionaliteit voor het back-upen en herstellen van gegevens voor uw service werkt zoals verwacht.  Het is ook mogelijk om programmatisch dataverlies aan te roepen en ook van die gebeurtenis te herstellen.

> [!NOTE]
> U een voorbeeldimplementatie van back-up- en herstelfunctionaliteit vinden in de Web Reference App op GitHub. Kijk op `Inventory.Service` de dienst voor meer details.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Onder de motorkap: meer details over back-up en herstel
Hier is wat meer details over back-up en herstel.

### <a name="backup"></a>Back-up
De Reliable State Manager biedt de mogelijkheid om consistente back-ups te maken zonder lees- of schrijfbewerkingen te blokkeren. Om dit te doen, maakt het gebruik van een controlepunt en log persistentie mechanisme.  De Reliable State Manager neemt fuzzy (lichtgewicht) checkpoints op bepaalde punten om de druk van het transactionele logboek te verlichten en de hersteltijden te verbeteren.  Wanneer `BackupAsync` wordt aangeroepen, instrueert de Betrouwbare Staatsmanager alle betrouwbare objecten om hun laatste controlepuntbestanden naar een lokale back-upmap te kopiëren.  Vervolgens kopieert Reliable State Manager alle logboekrecords, vanaf de startaanwijzer tot de laatste logboekrecord in de back-upmap.  Aangezien alle logboekrecords tot de laatste logboekrecord zijn opgenomen in de back-up en de Reliable State Manager de`CommitAsync` logboekregistratie behoudt, garandeert de Reliable State Manager dat alle transacties die zijn vastgelegd (met succes zijn geretourneerd) in de back-up zijn opgenomen.

Elke transactie die `BackupAsync` zich na is aangeroepen, kan wel of niet in de back-up staan.  Zodra de lokale back-upmap is ingevuld door het platform (dat wil zeggen dat de lokale back-up is voltooid door de runtime), wordt de back-upvan de service-terugroepen aangeroepen.  Deze callback is verantwoordelijk voor het verplaatsen van de back-upmap naar een externe locatie, zoals Azure Storage.

### <a name="restore"></a>Herstellen
De Reliable State Manager biedt de mogelijkheid om `RestoreAsync` te herstellen van een back-up met behulp van de API.  
De `RestoreAsync` methode `RestoreContext` op kan alleen `OnDataLossAsync` worden aangeroepen binnen de methode.
De bool `OnDataLossAsync` terug door geeft aan of de dienst zijn status hersteld van een externe bron.
Als `OnDataLossAsync` de retourneert true, zal Service Fabric alle andere replica's van deze primaire herbouwen. Servicefabric zorgt ervoor dat `OnDataLossAsync` replica's die eerst worden gebeld, worden overgezet naar de primaire rol, maar die geen leesstatus of schrijfstatus krijgen.
Dit houdt in dat voor `RunAsync` StatefulService-uitvoerders pas wordt gebeld als `OnDataLossAsync` deze succesvol is voltooid.
Dan `OnDataLossAsync` zal worden aangeroepen op de nieuwe primaire.
Totdat een service deze API met succes voltooit (door true of false terug te sturen) en de relevante herconfiguratie voltooit, blijft de API één voor één worden aangeroepen.

`RestoreAsync`eerst alle bestaande status in de primaire replica waarnaar is aangeroepen. Vervolgens maakt de Betrouwbare Staatsmanager alle betrouwbare objecten die in de back-upmap aanwezig zijn. Vervolgens worden de betrouwbare objecten geïnstrueerd om te herstellen vanuit hun controlepunten in de back-upmap. Ten slotte herstelt de Reliable State Manager zijn eigen status van de logboekrecords in de back-upmap en voert het herstel uit. Als onderdeel van het herstelproces worden bewerkingen vanaf het 'beginpunt' die logboekrecords in de back-upmap hebben vastgelegd, opnieuw afgespeeld naar de objecten Met betrekking tot de betrouwbare objecten. Deze stap zorgt ervoor dat de herstelstatus consistent is.

## <a name="next-steps"></a>Volgende stappen
  - [Betrouwbare verzamelingen](service-fabric-work-with-reliable-collections.md)
  - [Betrouwbare services snel van start](service-fabric-reliable-services-quick-start.md)
  - [Meldingen van betrouwbare services](service-fabric-reliable-services-notifications.md)
  - [Configuratie van betrouwbare services](service-fabric-reliable-services-configuration.md)
  - [Ontwikkelaarsreferentie voor betrouwbare verzamelingen](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Periodic backup and restore in Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md) (Periodieke back-up en herstel in Azure Service Fabric)

