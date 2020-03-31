---
title: Overzicht van de communicatie van Reliable Services
description: Overzicht van het communicatiemodel Reliable Services, inclusief het openen van luisteraars op services, het oplossen van eindpunten en het communiceren tussen services.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 3c1a6cfa5227369bf1cde4af087019727c22c0c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462947"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Hoe de communicatie-API's voor betrouwbare services te gebruiken
Azure Service Fabric als platform is volledig agnostisch over communicatie tussen services. Alle protocollen en stapels zijn acceptabel, van UDP tot HTTP. Het is aan de serviceontwikkelaar om te kiezen hoe services moeten communiceren. Het application framework Reliable Services biedt ingebouwde communicatiestacks en API's die u gebruiken om uw aangepaste communicatiecomponenten te bouwen.

## <a name="set-up-service-communication"></a>Servicecommunicatie instellen
De Reliable Services API maakt gebruik van een eenvoudige interface voor servicecommunicatie. Als u een eindpunt voor uw service wilt openen, implementeert u deze interface:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

```java
public interface CommunicationListener {
    CompletableFuture<String> openAsync(CancellationToken cancellationToken);

    CompletableFuture<?> closeAsync(CancellationToken cancellationToken);

    void abort();
}
```

U vervolgens de implementatie van uw communicatielistener toevoegen door deze in een op service gebaseerde klassemethode te retourneren.

Voor staatloze diensten:

```csharp
public class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```
```java
public class MyStatelessService extends StatelessService {

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ...
    }
    ...
}
```

Voor statige diensten:

```java
    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        ...
    }
    ...
```

```csharp
public class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

In beide gevallen retourneert u een verzameling luisteraars. Hierdoor kan uw service op meerdere eindpunten luisteren, mogelijk met behulp van verschillende protocollen, met behulp van meerdere luisteraars. U bijvoorbeeld een HTTP-listener en een afzonderlijke WebSocket-listener hebben. Elke listener krijgt een naam en de resulterende *naamverzameling: adresparen* worden weergegeven als een JSON-object wanneer een client de luisteradressen voor een service-instantie of een partitie opvraagt.

In een staatloze service retourneert de overschrijving een verzameling ServiceInstanceListeners. A `ServiceInstanceListener` bevat een functie `ICommunicationListener(C#) / CommunicationListener(Java)` om een te maken en geeft het een naam. Voor stateful services retourneert de overschrijving een verzameling ServiceReplicaListeners. Dit is iets anders dan zijn stateloze tegenhanger, omdat een `ServiceReplicaListener` heeft een optie om een `ICommunicationListener` op secundaire replica's te openen. U niet alleen meerdere communicatielisteners in een service gebruiken, maar u ook opgeven welke luisteraars aanvragen op secundaire replica's accepteren en welke alleen op primaire replica's luisteren.

U bijvoorbeeld een ServiceRemotingListener hebben die Alleen RPC-aanroepen op primaire replica's gebruikt en een tweede aangepaste listener die leesverzoeken op secundaire replica's over HTTP neemt:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [!NOTE]
> Bij het maken van meerdere listeners voor een service **moet** elke listener een unieke naam krijgen.
>
>

Beschrijf ten slotte de eindpunten die nodig zijn voor de service in het [servicemanifest](service-fabric-application-and-service-manifests.md) onder de sectie op eindpunten.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

De communicatielistener heeft toegang tot de eindpuntbronnen `CodePackageActivationContext` die `ServiceContext`eraan zijn toegewezen vanuit de . De luisteraar kan dan beginnen met luisteren naar verzoeken wanneer deze wordt geopend.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Eindpuntresources zijn gemeenschappelijk voor het hele servicepakket en worden toegewezen door Service Fabric wanneer het servicepakket wordt geactiveerd. Meerdere servicereplica's die in dezelfde ServiceHost worden gehost, kunnen dezelfde poort delen. Dit betekent dat de communicatieluisteraar het delen van poorten moet ondersteunen. De aanbevolen manier om dit te doen is voor de communicatieluisteraar om de partitie-ID en replica/instantie-id te gebruiken wanneer het het luisteradres wordt gegenereerd.
>
>

### <a name="service-address-registration"></a>Registratie van serviceadres
Een systeemservice genaamd de *Naamgevingsservice* wordt uitgevoerd op servicefabricclusters. De naamgevingsservice is een registrar voor services en hun adressen waarop elke instantie of replica van de service luistert. Wanneer `OpenAsync(C#) / openAsync(Java)` de methode `ICommunicationListener(C#) / CommunicationListener(Java)` van een voltooid, wordt de retourwaarde geregistreerd in de naamgevingsservice. Deze retourwaarde die wordt gepubliceerd in de naamgevingsservice is een tekenreeks waarvan de waarde van alles kan zijn. Deze tekenreekswaarde is wat clients zien wanneer ze een adres voor de service van de naamgevingsservice vragen.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```
```java
public CompletableFuture<String> openAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.getCodePackageActivationContext.getEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.getPort();

    this.publishAddress = String.format("http://%s:%d/", FabricRuntime.getNodeContext().getIpAddressOrFQDN(), port);

    this.webApp = new WebApp(port);
    this.webApp.start();

    /* the string returned here will be published in the Naming Service.
     */
    return CompletableFuture.completedFuture(this.publishAddress);
}
```

Service Fabric biedt API's waarmee clients en andere services vervolgens dit adres kunnen aanvragen op servicenaam. Dit is belangrijk omdat het serviceadres niet statisch is. Services worden verplaatst in het cluster voor resource balancing en beschikbaarheidsdoeleinden. Dit is het mechanisme waarmee clients het luisteradres voor een service kunnen oplossen.

> [!NOTE]
> Zie [Service Fabric Web API-services met OWIN-zelfhosting](service-fabric-reliable-services-communication-webapi.md) voor C#, terwijl u voor Java uw eigen HTTP-serverimplementatie schrijven, https://github.com/Azure-Samples/service-fabric-java-getting-startedzie Voorbeeld van de EchoServer-toepassing op.
>
>

## <a name="communicating-with-a-service"></a>Communiceren met een service
De API voor Betrouwbare Services biedt de volgende bibliotheken om clients te schrijven die communiceren met services.

### <a name="service-endpoint-resolution"></a>Serviceeindpuntresolutie
De eerste stap naar communicatie met een service is het oplossen van een eindpuntadres van de partitie of instantie van de service waarmee u wilt praten. De `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` hulpprogrammaklasse is een basisprimitieve klasse die klanten helpt bij het bepalen van het eindpunt van een service tijdens runtime. In de terminologie van servicestructuur wordt het proces voor het bepalen van het eindpunt van een service de *serviceeindpuntresolutie*genoemd.

Als u verbinding wilt maken met services binnen een cluster, kan ServicePartitionResolver worden gemaakt met standaardinstellingen. Dit is het aanbevolen gebruik voor de meeste situaties:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Als u verbinding wilt maken met services in een ander cluster, kan een ServicePartitionResolver worden gemaakt met een set eindpunten van de clustergateway. Houd er rekening mee dat gatewayeindpunten slechts verschillende eindpunten zijn om verbinding te maken met hetzelfde cluster. Bijvoorbeeld:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

U `ServicePartitionResolver` kunt ook een functie krijgen `FabricClient` voor het maken van een om intern te gebruiken:

```csharp
public delegate FabricClient CreateFabricClientDelegate();
```
```java
public FabricServicePartitionResolver(CreateFabricClient createFabricClient) {
...
}

public interface CreateFabricClient {
    public FabricClient getFabricClient();
}
```

`FabricClient`is het object dat wordt gebruikt om te communiceren met het cluster Servicefabric voor verschillende beheerbewerkingen op het cluster. Dit is handig wanneer u meer controle wilt over hoe een resolver van een servicepartitie met uw cluster samenwerkt. `FabricClient`voert caching intern uit en is over het algemeen `FabricClient` duur om te maken, dus het is belangrijk om instanties zoveel mogelijk te hergebruiken.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Vervolgens wordt een resolve-methode gebruikt om het adres van een service of een servicepartitie voor partitieservices op te halen.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();

CompletableFuture<ResolvedServicePartition> partition =
    resolver.resolveAsync(new URI("fabric:/MyApp/MyService"), new ServicePartitionKey());
```

Een serviceadres kan eenvoudig worden opgelost met behulp van een ServicePartitionResolver, maar er is meer werk nodig om ervoor te zorgen dat het opgeloste adres correct kan worden gebruikt. Uw client moet detecteren of de verbindingspoging is mislukt vanwege een tijdelijke fout en opnieuw kan worden geprobeerd (bijvoorbeeld service verplaatst of tijdelijk niet beschikbaar is), of een permanente fout (bijvoorbeeld service is verwijderd of de gevraagde bron niet meer bestaat). Service-exemplaren of replica's kunnen om meerdere redenen van knooppunt naar knooppunt gaan. Het serviceadres dat is opgelost via ServicePartitionResolver is mogelijk verouderd tegen de tijd dat uw clientcode verbinding probeert te maken. In dat geval moet de klant het adres opnieuw oplossen. Het verstrekken `ResolvedServicePartition` van de vorige geeft aan dat de resolver opnieuw moet proberen in plaats van gewoon een in de cache opgeslagen adres op te halen.

De clientcode hoeft doorgaans niet rechtstreeks met de ServicePartitionResolver te werken. Het wordt gemaakt en doorgegeven aan communicatieclientfabrieken in de Reliable Services API. De fabrieken gebruiken de resolver intern om een clientobject te genereren dat kan worden gebruikt om met services te communiceren.

### <a name="communication-clients-and-factories"></a>Communicatieklanten en fabrieken
De bibliotheek in de communicatiefabriek implementeert een typisch foutafhandelingspatroon waarmee verbindingen met opgeloste serviceeindpunten eenvoudiger kunnen worden geprobeerd. De fabrieksbibliotheek biedt het mechanisme voor het opnieuw proberen terwijl u de fouthandlers levert.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`definieert de basisinterface die wordt geïmplementeerd door een communicatieclientfabriek die klanten produceert die met een Service Fabric-service kunnen praten. De implementatie van de CommunicationClientFactory is afhankelijk van de communicatiestack die wordt gebruikt door de Service Fabric-service waar de klant wil communiceren. De Api voor `CommunicationClientFactoryBase<TCommunicationClient>`Betrouwbare Diensten biedt een . Dit biedt een basisimplementatie van de CommunicationClientFactory-interface en voert taken uit die gemeenschappelijk zijn voor alle communicatiestacks. (Deze taken omvatten het gebruik van een ServicePartitionResolver om het eindpunt van de service te bepalen). Clients implementeren meestal de abstracte CommunicationClientFactoryBase-klasse om logica te verwerken die specifiek is voor de communicatiestack.

De communicatieclient ontvangt gewoon een adres en gebruikt het om verbinding te maken met een service. De klant kan elk protocol gebruiken dat hij wil.

```csharp
public class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```
```java
public class MyCommunicationClient implements CommunicationClient {

    private ResolvedServicePartition resolvedServicePartition;
    private String listenerName;
    private ResolvedServiceEndpoint endPoint;

    /*
     * Getters and Setters
     */
}
```

De klantenfabriek is primair verantwoordelijk voor het creëren van communicatieklanten. Voor clients die geen permanente verbinding onderhouden, zoals een HTTP-client, hoeft de fabriek alleen de client te maken en terug te sturen. Andere protocollen die een permanente verbinding onderhouden, zoals sommige binaire protocollen, moeten ook door de fabriek worden gevalideerd om te bepalen of de verbinding opnieuw moet worden gemaakt.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```
```java
public class MyCommunicationClientFactory extends CommunicationClientFactoryBase<MyCommunicationClient> {

    @Override
    protected boolean validateClient(MyCommunicationClient clientChannel) {
    }

    @Override
    protected boolean validateClient(String endpoint, MyCommunicationClient client) {
    }

    @Override
    protected CompletableFuture<MyCommunicationClient> createClientAsync(String endpoint) {
    }

    @Override
    protected void abortClient(MyCommunicationClient client) {
    }
}
```

Ten slotte is een uitzonderingshandler verantwoordelijk voor het bepalen welke actie moet worden ondernomen wanneer een uitzondering optreedt. Uitzonderingen worden gecategoriseerd in **opnieuw te proberen** en niet opnieuw te **proberen**.

* **Niet-herprobeerbare** uitzonderingen worden gewoon teruggeworpen naar de beller.
* **opnieuw te proberen** uitzonderingen worden verder gecategoriseerd in **voorbijgaande** en **niet-voorbijgaande**.
  * **Tijdelijke** uitzonderingen zijn uitzonderingen die eenvoudig opnieuw kunnen worden geprobeerd zonder het eindpuntadres van de service opnieuw op te lossen. Deze omvatten andere tijdelijke netwerkproblemen of servicefoutreacties dan die welke aangeven dat het eindpuntadres van de service niet bestaat.
  * **Niet-tijdelijke** uitzonderingen zijn uitzonderingen waarvoor het eindpuntadres van de service opnieuw moet worden opgelost. Deze omvatten uitzonderingen die aangeven dat het eindpunt van de service niet kon worden bereikt, wat aangeeft dat de service naar een ander knooppunt is verplaatst.

Het `TryHandleException` maakt een beslissing over een bepaalde uitzondering. Als het **niet weet** welke beslissingen te nemen over een uitzondering, moet het **terug false**. Als het **weet** welke beslissing te maken, moet het resultaat dienovereenkomstig en terug **te keren waar**.

```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
```java
public class MyExceptionHandler implements ExceptionHandler {

    @Override
    public ExceptionHandlingResult handleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings) {

        /* if exceptionInformation.getException() is known and is transient (can be retried without re-resolving)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), true, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;


        /* if exceptionInformation.getException() is known and is not transient (indicates a new service endpoint address must be resolved)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), false, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;

        /* if exceptionInformation.getException() is unknown (let the next ExceptionHandler attempt to handle it)
         */
        result = null;
        return false;

    }
}
```
### <a name="putting-it-all-together"></a>Alles samenvoegen
Met `ICommunicationClient(C#) / CommunicationClient(Java)`een `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`, `IExceptionHandler(C#) / ExceptionHandler(Java)` , en gebouwd `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` rond een communicatieprotocol, een wraps het allemaal samen en biedt de fout-handling en service partitie adres resolutie lus rond deze componenten.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```
```java
private MyCommunicationClientFactory myCommunicationClientFactory;
private URI myServiceUri;

FabricServicePartitionClient myServicePartitionClient = new FabricServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

CompletableFuture<?> result = myServicePartitionClient.invokeWithRetryAsync(client -> {
      /* Communicate with the service using the client.
       */
   });

```

## <a name="next-steps"></a>Volgende stappen
* [ASP.NET Core met betrouwbare services](service-fabric-reliable-services-communication-aspnetcore.md)
* [Externe proceduregesprekken met Betrouwbare Services remoting](service-fabric-reliable-services-communication-remoting.md)
* [WCF-communicatie met behulp van betrouwbare services](service-fabric-reliable-services-communication-wcf.md)
