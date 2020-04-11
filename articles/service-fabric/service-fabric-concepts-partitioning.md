---
title: Services voor partitioneringsservice
description: Beschrijft hoe u servicefabric-stateful services verdelen. Partities maken gegevensopslag op de lokale machines mogelijk, zodat gegevens en rekenkracht samen kunnen worden geschaald.
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: 4edfaa74fe109c688cad733d16031e87fff1e46f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115168"
---
# <a name="partition-service-fabric-reliable-services"></a>Partitioneren van Service Fabric-betrouwbare services
Dit artikel geeft een inleiding tot de basisconcepten van het partitioneren van betrouwbare services voor Azure Service Fabric. De broncode die in het artikel wordt gebruikt, is ook beschikbaar op [GitHub.](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions)

## <a name="partitioning"></a>Partitionering
Partitioneren is niet uniek voor Service Fabric. In feite is het een kernpatroon van het bouwen van schaalbare services. In bredere zin kunnen we partitioneren als een concept van het verdelen van status (data) en compute in kleinere toegankelijke eenheden om schaalbaarheid en prestaties te verbeteren. Een bekende vorm van partitionering is [gegevenspartitionering][wikipartition], ook wel bekend als sharding.

### <a name="partition-service-fabric-stateless-services"></a>Statusloze services voor partitieservicefabric
Voor stateloze services u denken aan een partitie die een logische eenheid is die een of meer exemplaren van een service bevat. Figuur 1 toont een statusloze service met vijf exemplaren verdeeld over een cluster met één partitie.

![Staatloze service](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Er zijn echt twee soorten stateless service oplossingen. De eerste is een service die de status extern blijft bestaan, bijvoorbeeld in een Azure SQL-database (zoals een website die de sessie-informatie en -gegevens opslaat). De tweede is alleen berekeningen services (zoals een rekenmachine of afbeelding miniaturen) die geen permanente status beheren.

In beide gevallen is het partitioneren van een stateless service een zeer zeldzaam scenario - schaalbaarheid en beschikbaarheid wordt normaal gesproken bereikt door meer exemplaren toe te voegen. De enige keer dat u meerdere partities voor stateless service-exemplaren wilt overwegen, is wanneer u aan speciale routeringsaanvragen moet voldoen.

Neem bijvoorbeeld een geval waarin gebruikers met id's in een bepaald bereik alleen door een bepaalde service-instantie mogen worden bediend. Een ander voorbeeld van wanneer u een stateless service partitioneren, is wanneer u een echt verdeelde backend hebt (bijvoorbeeld een geshard SQL-database) en u wilt bepalen welke service-instantie naar de databasemoet schrijven - of ander voorbereidingswerk binnen de stateless-service uitvoeren waarvoor dezelfde partitioneringsinformatie vereist is als in de backend wordt gebruikt. Dit soort scenario's kan ook worden opgelost op verschillende manieren en vereisen niet noodzakelijkerwijs service partitionering.

De rest van deze walkthrough richt zich op stateful services.

### <a name="partition-service-fabric-stateful-services"></a>Stateful services partition service fabric
Service Fabric maakt het eenvoudig om schaalbare stateful services te ontwikkelen door een eersteklas manier aan te bieden om de partitiestatus (gegevens) te verdelen. Conceptueel u een partitie van een stateful service zien als een schaaleenheid die zeer betrouwbaar is via [replica's](service-fabric-availability-services.md) die worden gedistribueerd en gebalanceerd over de knooppunten in een cluster.

Partitionering in het kader van Service Fabric stateful services verwijst naar het proces om te bepalen dat een bepaalde servicepartitie verantwoordelijk is voor een deel van de volledige status van de service. (Zoals eerder vermeld, een partitie is een set van [replica's).](service-fabric-availability-services.md) Een groot ding over Service Fabric is dat het plaatst de partities op verschillende knooppunten. Hierdoor kunnen ze uitgroeien tot de resourcelimiet van een knooppunt. Naarmate de gegevensbehoeften toenemen, groeien partities en brengt Service Fabric partities tussen knooppunten opnieuw in evenwicht. Dit zorgt voor een voortdurend efficiënt gebruik van hardwaremiddelen.

Als u een voorbeeld wilt geven, zegt u dat u begint met een cluster met 5 node en een service die is geconfigureerd om 10 partities en een doel van drie replica's te hebben. In dit geval zou Service Fabric de replica's over het cluster in evenwicht brengen en distribueren - en zou u eindigen met twee primaire [replica's](service-fabric-availability-services.md) per knooppunt.
Als u het cluster nu moet uitschalen naar 10 knooppunten, brengt Service Fabric de primaire [replica's](service-fabric-availability-services.md) opnieuw in evenwicht tussen alle 10 knooppunten. Als u teruggeschaald wordt naar 5 knooppunten, brengt Service Fabric alle replica's opnieuw in evenwicht op de 5 knooppunten.  

Figuur 2 toont de verdeling van 10 partities voor en na het schalen van het cluster.

![Statige dienst](./media/service-fabric-concepts-partitioning/partitions.png)

Als gevolg hiervan wordt de scale-out bereikt omdat aanvragen van clients worden verdeeld over computers, de algehele prestaties van de toepassing worden verbeterd en de discussie over de toegang tot brokken gegevens wordt verminderd.

## <a name="plan-for-partitioning"></a>Plannen voor partitionering
Voordat u een service implementeert, moet u altijd rekening houden met de partitioneringsstrategie die nodig is om uit te schalen. Er zijn verschillende manieren, maar ze richten zich allemaal op wat de applicatie moet bereiken. Voor de context van dit artikel, laten we eens kijken naar enkele van de belangrijkste aspecten.

Een goede aanpak is om na te denken over de structuur van de staat die moet worden verdeeld, als de eerste stap.

Laten we een eenvoudig voorbeeld nemen. Als u een dienst voor een provincie-brede opiniepeiling zou bouwen, kon u een verdeling voor elke stad in de provincie creëren. Vervolgens u de stemmen voor elke persoon in de stad opslaan in de partitie die overeenkomt met die stad. Figuur 3 illustreert een reeks mensen en de stad waarin ze wonen.

![Eenvoudige partitie](./media/service-fabric-concepts-partitioning/cities.png)

Aangezien de bevolking van steden sterk varieert, u eindigen met een aantal partities die veel gegevens bevatten (bijvoorbeeld Seattle) en andere partities met zeer weinig staat (bijvoorbeeld Kirkland). Dus wat is de impact van het hebben van partities met ongelijke hoeveelheden staat?

Als u over het voorbeeld opnieuw denkt, u gemakkelijk zien dat de verdeling die de stemmen voor Seattle houdt meer verkeer dan Kirkland één zal krijgen. Standaard zorgt Service Fabric ervoor dat er ongeveer hetzelfde aantal primaire en secundaire replica's op elk knooppunt staan. Dus je kan eindigen met knooppunten die replica's die meer verkeer en anderen die minder verkeer dienen te houden. U wilt bij voorkeur warme en koude plekken als deze in een cluster vermijden.

Om dit te voorkomen, moet u twee dingen doen, vanuit een partitionering oogpunt:

* Probeer de status te partitioneren zodat deze gelijkmatig over alle partities wordt verdeeld.
* Rapportbelasting van elk van de replica's voor de service. (Voor informatie over hoe, check out dit artikel over [Metrics en Load](service-fabric-cluster-resource-manager-metrics.md)). Service Fabric biedt de mogelijkheid om de belasting te melden die wordt verbruikt door services, zoals de hoeveelheid geheugen of het aantal records. Op basis van de gerapporteerde statistieken detecteert Service Fabric dat sommige partities hogere belastingen bevatten dan andere en herinevenwicht en herinevenwicht en brengt het cluster opnieuw in evenwicht door replica's naar meer geschikte knooppunten te verplaatsen, zodat het algehele knooppunt overbelast is.

Soms u niet weten hoeveel gegevens er in een bepaalde partitie zullen zijn. Dus een algemene aanbeveling is om beide te doen - eerste, door het aannemen van een partitionering strategie die de gegevens gelijkmatig verspreidt over de partities en ten tweede, door het melden van belasting.  De eerste methode voorkomt situaties die in het stemvoorbeeld worden beschreven, terwijl de tweede helpt tijdelijke verschillen in toegang of belasting in de loop van de tijd glad te strijken.

Een ander aspect van partitieplanning is om het juiste aantal partities te kiezen om mee te beginnen.
Vanuit het oogpunt van Service Fabric is er niets dat u ervan weerhoudt om met een hoger aantal partities te beginnen dan verwacht voor uw scenario.
In feite, ervan uitgaande dat het maximum aantal partities is een geldige aanpak.

In zeldzame gevallen hebt u uiteindelijk meer partities nodig dan u in eerste instantie hebt gekozen. Aangezien u het aantal partities na het feit niet wijzigen, moet u een aantal geavanceerde partitiebenaderingen toepassen, zoals het maken van een nieuwe service-instantie van hetzelfde servicetype. U moet ook een aantal client-side logica implementeren die de aanvragen doorstuurt naar de juiste service-instantie, op basis van de klantkennis die uw clientcode moet onderhouden.

Een andere overweging voor het partitioneren van planning is de beschikbare computerbronnen. Aangezien de status moet worden geopend en opgeslagen, bent u verplicht om te volgen:

* Limieten voor netwerkbandbreedte
* Systeemgeheugenlimieten
* Limieten voor schijfopslag

Dus wat gebeurt er als u in een lopend cluster resourcebeperkingen tegenkomt? Het antwoord is dat u het cluster eenvoudig uitschalen om aan de nieuwe vereisten te voldoen.

[De capaciteitsplanningshandleiding](service-fabric-capacity-planning.md) biedt richtlijnen voor het bepalen van het aantal knooppunten dat uw cluster nodig heeft.

## <a name="get-started-with-partitioning"></a>Aan de slag met partitioneren
In deze sectie wordt beschreven hoe u aan de slag met het partitioneren van uw service.

Service Fabric biedt een keuze uit drie partitieschema's:

* Bereikverdeling (ook wel uniformint64Partition genoemd).
* Benoemde partitionering. Toepassingen die dit model gebruiken, hebben meestal gegevens die kunnen worden gebucketd, binnen een begrensde set. Enkele veelvoorkomende voorbeelden van gegevensvelden die worden gebruikt als benoemde partitiesleutels, zijn regio's, postcodes, klantgroepen of andere bedrijfsgrenzen.
* Singleton partitioneren. Singleton-partities worden meestal gebruikt wanneer de service geen extra routering vereist. Stateless services maken bijvoorbeeld standaard gebruik van dit partitieschema.

Named en Singleton partitionering schemes are special forms of ranged partitions. Standaard gebruiken de Visual Studio-sjablonen voor Service Fabric bereikbare partitionering, omdat dit de meest voorkomende en nuttige is. De rest van dit artikel richt zich op de ranged partitionering regeling.

### <a name="ranged-partitioning-scheme"></a>Bereikverdelingsschema
Dit wordt gebruikt om een geheel getalbereik (geïdentificeerd door een low key en high key) en een aantal partities (n) op te geven. Het maakt n partities, elk verantwoordelijk voor een niet-overlappende subrange van de totale partitie sleutel bereik. Een bereikverdelingsschema met een lage toets van 0, een hoge toets van 99 en een telling van 4 maakt bijvoorbeeld vier partities, zoals hieronder wordt weergegeven.

![Bereikverdeling](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Een gemeenschappelijke aanpak is het maken van een hash op basis van een unieke sleutel binnen de gegevensset. Enkele veelvoorkomende voorbeelden van sleutels zijn een voertuigidentificatienummer (VIN), een werknemers-ID of een unieke tekenreeks. Door deze unieke sleutel te gebruiken, genereert u vervolgens een hash-code, modulus het sleutelbereik, om te gebruiken als uw sleutel. U de boven- en ondergrenzen van het toegestane sleutelbereik opgeven.

### <a name="select-a-hash-algorithm"></a>Een hash-algoritme selecteren
Een belangrijk onderdeel van hashing is het selecteren van uw hash-algoritme. Een overweging is of het doel is om vergelijkbare toetsen dicht bij elkaar te groeperen (plaatsgevoelige hashing)-- of als activiteit breed moet worden verdeeld over alle partities (distributiehashing), wat vaker voorkomt.

De kenmerken van een goede distributie hashing algoritme zijn dat het gemakkelijk te berekenen, het heeft weinig botsingen, en het verdeelt de toetsen gelijkmatig. Een goed voorbeeld van een efficiënt hash algoritme is het [FNV-1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) hash algoritme.

Een goede bron voor algemene hash code algoritme keuzes is de [Wikipedia-pagina over hash functies](https://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Een stateful service bouwen met meerdere partities
Laten we uw eerste betrouwbare stateful service maken met meerdere partities. In dit voorbeeld bouwt u een zeer eenvoudige toepassing waarbij u alle achternamen wilt opslaan die met dezelfde letter in dezelfde partitie beginnen.

Voordat u een code schrijft, moet u nadenken over de partities en partitiesleutels. Je hebt 26 partities nodig (één voor elke letter in het alfabet), maar hoe zit het met de lage en hoge toetsen?
Omdat we letterlijk één partitie per letter willen hebben, kunnen we 0 gebruiken als de low key en 25 als de hoge toets, omdat elke letter zijn eigen sleutel is.

> [!NOTE]
> Dit is een vereenvoudigd scenario, omdat de verdeling in werkelijkheid ongelijk zou zijn. Achternamen die beginnen met de letters "S" of "M" komen vaker voor dan die beginnen met "X" of "Y".
> 
> 

1. Open **Visual Studio** > **File New** > **New** > **Project**.
2. Kies in het dialoogvenster **Nieuw project** de toepassing Servicefabric.
3. Noem het project "AlphabetPartitions".
4. Kies in het dialoogvenster **Een service maken** de optie **Stateful-service** en noem deze "Alphabet.Processing".
5. Stel het aantal partities in. Open het bestand Applicationmanifest.xml in de map ApplicationPackageRoot van het project AlphabetPartitions en werk de parameter Processing_PartitionCount naar 26, zoals hieronder weergegeven.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    U moet ook de LowKey- en HighKey-eigenschappen van het element StatefulService bijwerken in het toepassingsmanifest.xml zoals hieronder wordt weergegeven.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. Als u wilt dat de service toegankelijk is, opent u een eindpunt op een poort door het eindpuntelement van ServiceManifest.xml (in de map PackageRoot) toe te voegen voor de alphabet.processing-service zoals hieronder wordt weergegeven:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Nu is de service geconfigureerd om te luisteren naar een intern eindpunt met 26 partities.
7. Vervolgens moet u de `CreateServiceReplicaListeners()` methode van de klasse Processing overschrijven.
   
   > [!NOTE]
   > Voor dit voorbeeld gaan we ervan uit dat u een eenvoudige HttpCommunicationListener gebruikt. Zie [The Reliable Service communication model](service-fabric-reliable-services-communication.md)voor meer informatie over betrouwbare servicecommunicatie.
   > 
   > 
8. Een aanbevolen patroon voor de URL waarop een replica `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`luistert, is de volgende indeling: .
    Zo wilt u uw communicatieluisteraar configureren om te luisteren op de juiste eindpunten en met dit patroon.
   
    Meerdere replica's van deze service kunnen op dezelfde computer worden gehost, dus dit adres moet uniek zijn voor de replica. Dit is de reden waarom partitie-ID + replica-ID in de URL staan. HttpListener kan op meerdere adressen op dezelfde poort luisteren, zolang het URL-voorvoegsel uniek is.
   
    De extra GUID is er voor een geavanceerde case waar secundaire replica's ook luisteren naar alleen-lezen verzoeken. Wanneer dat het geval is, wilt u ervoor zorgen dat een nieuw uniek adres wordt gebruikt bij de overgang van primair naar secundair om clients te dwingen het adres opnieuw op te lossen. '+' wordt hier als adres gebruikt, zodat de replica luistert op alle beschikbare hosts (IP, FQDN, localhost, enz.) De onderstaande code toont een voorbeeld.
   
    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
         return new[] { new ServiceReplicaListener(context => this.CreateInternalListener(context))};
    }
    private ICommunicationListener CreateInternalListener(ServiceContext context)
    {
   
         EndpointResourceDescription internalEndpoint = context.CodePackageActivationContext.GetEndpoint("ProcessingServiceEndpoint");
         string uriPrefix = String.Format(
                "{0}://+:{1}/{2}/{3}-{4}/",
                internalEndpoint.Protocol,
                internalEndpoint.Port,
                context.PartitionId,
                context.ReplicaOrInstanceId,
                Guid.NewGuid());
   
         string nodeIP = FabricRuntime.GetNodeContext().IPAddressOrFQDN;
   
         string uriPublished = uriPrefix.Replace("+", nodeIP);
         return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInternalRequest);
    }
    ```
   
    Het is ook vermeldenswaard dat de gepubliceerde URL iets verschilt van het voorvoegsel van de luister-URL.
    De luister-URL wordt gegeven aan HttpListener. De gepubliceerde URL is de URL die wordt gepubliceerd naar de Service Fabric Naming Service, die wordt gebruikt voor servicedetectie. Klanten zullen vragen om dit adres via die discovery service. Het adres dat clients krijgen, moet het werkelijke IP of FQDN van het knooppunt hebben om verbinding te maken. Dus je moet '+' te vervangen door ip of FQDN van het knooppunt zoals hierboven weergegeven.
9. De laatste stap is het toevoegen van de verwerkingslogica aan de service zoals hieronder wordt weergegeven.
   
    ```csharp
    private async Task ProcessInternalRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        string output = null;
        string user = context.Request.QueryString["lastname"].ToString();
   
        try
        {
            output = await this.AddUserAsync(user);
        }
        catch (Exception ex)
        {
            output = ex.Message;
        }
   
        using (HttpListenerResponse response = context.Response)
        {
            if (output != null)
            {
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    private async Task<string> AddUserAsync(string user)
    {
        IReliableDictionary<String, String> dictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<String, String>>("dictionary");
   
        using (ITransaction tx = this.StateManager.CreateTransaction())
        {
            bool addResult = await dictionary.TryAddAsync(tx, user.ToUpperInvariant(), user);
   
            await tx.CommitAsync();
   
            return String.Format(
                "User {0} {1}",
                user,
                addResult ? "successfully added" : "already exists");
        }
    }
    ```
   
    `ProcessInternalRequest`hiermee worden de waarden gelezen van de parameter `AddUserAsync` querytekenreeks die wordt gebruikt `dictionary`om de partitie aan te roepen en wordt aangebeld om de achternaam toe te voegen aan het betrouwbare woordenboek .
10. Laten we een stateloze service toevoegen aan het project om te zien hoe u een bepaalde partitie aanroepen.
    
    Deze service fungeert als een eenvoudige webinterface die de achternaam accepteert als parameter querytekenreeks, de partitiesleutel bepaalt en deze naar de alphabet.processing-service stuurt voor verwerking.
11. Kies in het dialoogvenster **Een service maken** de optie **Staatloze** service en noem deze "Alphabet.Web" zoals hieronder wordt weergegeven.
    
    ![Schermafbeelding van stateloze service](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Werk de eindpuntgegevens in servicemanifest.xml van de Alphabet.WebApi-service bij om een poort te openen zoals hieronder wordt weergegeven.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. U moet een verzameling ServiceInstanceListeners retourneren in het klassenweb. Nogmaals, u ervoor kiezen om een eenvoudige HttpCommunicationListener te implementeren.
    
    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] {new ServiceInstanceListener(context => this.CreateInputListener(context))};
    }
    private ICommunicationListener CreateInputListener(ServiceContext context)
    {
        // Service instance's URL is the node's IP & desired port
        EndpointResourceDescription inputEndpoint = context.CodePackageActivationContext.GetEndpoint("WebApiServiceEndpoint")
        string uriPrefix = String.Format("{0}://+:{1}/alphabetpartitions/", inputEndpoint.Protocol, inputEndpoint.Port);
        var uriPublished = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
        return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInputRequest);
    }
    ```
14. Nu moet u de verwerkingslogica implementeren. De HttpCommunicationListener `ProcessInputRequest` belt wanneer er een verzoek binnenkomt. Dus laten we doorgaan en voeg de code hieronder.
    
    ```csharp
    private async Task ProcessInputRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        String output = null;
        try
        {
            string lastname = context.Request.QueryString["lastname"];
            char firstLetterOfLastName = lastname.First();
            ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    
            ResolvedServicePartition partition = await this.servicePartitionResolver.ResolveAsync(alphabetServiceUri, partitionKey, cancelRequest);
            ResolvedServiceEndpoint ep = partition.GetEndpoint();
    
            JObject addresses = JObject.Parse(ep.Address);
            string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
            UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
            primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
            string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    
            output = String.Format(
                    "Result: {0}. <p>Partition key: '{1}' generated from the first letter '{2}' of input value '{3}'. <br>Processing service partition ID: {4}. <br>Processing service replica address: {5}",
                    result,
                    partitionKey,
                    firstLetterOfLastName,
                    lastname,
                    partition.Info.Id,
                    primaryReplicaAddress);
        }
        catch (Exception ex) { output = ex.Message; }
    
        using (var response = context.Response)
        {
            if (output != null)
            {
                output = output + "added to Partition: " + primaryReplicaAddress;
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    ```
    
    Laten we er stap voor stap doorheen lopen. De code leest de eerste letter `lastname` van de querytekenreeksparameter in een char. Vervolgens bepaalt het de partitiesleutel voor deze letter door `A` de hexadecimale waarde van de hexadecimale waarde van de eerste letter van de achternamen af te trekken.
    
    ```csharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Vergeet niet dat we in dit voorbeeld 26 partities gebruiken met één partitiesleutel per partitie.
    Vervolgens verkrijgen we de `partition` servicepartitie voor `ResolveAsync` deze sleutel `servicePartitionResolver` met behulp van de methode op het object. `servicePartitionResolver`wordt gedefinieerd als
    
    ```csharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    De `ResolveAsync` methode neemt de service URI, de partitiesleutel en een annuleringstoken als parameters. De service URI voor `fabric:/AlphabetPartitions/Processing`de verwerkingsservice is . Vervolgens krijgen we het eindpunt van de partitie.
    
    ```csharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Ten slotte bouwen we de URL van het eindpunt plus de querystring en bellen we de verwerkingsservice.
    
    ```csharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Zodra de verwerking is gedaan, schrijven we de output terug.
15. De laatste stap is het testen van de service. Visual Studio gebruikt toepassingsparameters voor lokale en cloudimplementatie. Als u de service met 26 partities lokaal `Local.xml` wilt testen, moet u het bestand bijwerken in de map ApplicationParameters van het project AlfabetPartities zoals hieronder wordt weergegeven:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Zodra u de implementatie hebt voltooid, u de service en alle partities controleren in de Service Fabric Explorer.
    
    ![Schermafbeelding van De Verkenner van de servicestof](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. In een browser u de partitielogica testen door . `http://localhost:8081/?lastname=somename` U zult zien dat elke achternaam die begint met dezelfde letter wordt opgeslagen in dezelfde partitie.
    
    ![Schermafbeelding van browser](./media/service-fabric-concepts-partitioning/samplerunning.png)

De volledige broncode van het voorbeeld is beschikbaar op [GitHub.](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions)

## <a name="next-steps"></a>Volgende stappen
Zie het volgende voor informatie over servicefabric-concepten:

* [Beschikbaarheid van Service Fabric-services](service-fabric-availability-services.md)
* [Schaalbaarheid van servicefabric-services](service-fabric-concepts-scalability.md)
* [Capaciteitsplanning voor Service Fabric-toepassingen](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
