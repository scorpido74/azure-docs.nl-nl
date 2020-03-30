---
title: Servicecommunicatie met de ASP.NET Core
description: Meer informatie over het gebruik van ASP.NET Core in stateloze en stateful Azure Service Fabric Reliable Services-toepassingen.
author: vturecek
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 0d432bd19d0689ef508fca0bf24eed4406929f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639629"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core in Azure Service Fabric Reliable Services

ASP.NET Core is een open-source en cross-platform framework. Dit framework is ontworpen voor het bouwen van cloudgebaseerde, met internetverbinding verbonden toepassingen, zoals web-apps, IoT-apps en mobiele back-ends.

Dit artikel is een uitgebreide handleiding voor het hosten van ASP.NET Core-services in Service Fabric Reliable Services met behulp van de **Microsoft.ServiceFabric.AspNetCore.** set NuGet-pakketten.

Zie [Zelfstudie: Een toepassing maken en implementeren met een ASP.NET Core Web API front-endservice en een stateful back-endservice](service-fabric-tutorial-create-dotnet-app.md)voor een inleidende zelfstudie over ASP.NET Core in Service Fabric en instructies voor het instellen van uw ontwikkelomgeving.

De rest van dit artikel gaat ervan uit dat je al bekend bent met ASP.NET Core. Zo niet, lees dan door de [ASP.NET Core fundamentals](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core in de Service Fabric-omgeving

Zowel ASP.NET Core- als Service Fabric-apps kunnen worden uitgevoerd op .NET Core of full .NET Framework. U ASP.NET Core op twee verschillende manieren gebruiken in Service Fabric:
 - **Gehost als een gast uitvoerbaar**. Op deze manier wordt voornamelijk gebruikt om bestaande ASP.NET Core-toepassingen op Service Fabric uit te voeren zonder codewijzigingen.
 - **Run binnen een betrouwbare service.** Op deze manier zorgt een betere integratie met de Service Fabric runtime en maakt stateful ASP.NET Core-services.

In de rest van dit artikel wordt uitgelegd hoe ASP.NET Core worden gebruikt in een betrouwbare service, via de ASP.NET Core-integratiecomponenten die worden verzonden met de Service Fabric SDK.

## <a name="service-fabric-service-hosting"></a>Service Fabric-servicehosting

In Service Fabric worden een of meer exemplaren en/of replica's van uw service uitgevoerd in een *servicehostproces:* een uitvoerbaar bestand waarin uw servicecode wordt uitgevoerd. U bent als serviceauteur eigenaar van het servicehostproces en Service Fabric activeert en bewaakt het voor u.

Traditionele ASP.NET (tot MVC 5) is nauw gekoppeld aan IIS via System.Web.dll. ASP.NET Core biedt een scheiding tussen de webserver en uw webtoepassing. Deze scheiding maakt het mogelijk webapplicaties draagbaar te zijn tussen verschillende webservers. Het maakt het ook mogelijk webservers te worden *self-hosted*. Dit betekent dat u een webserver starten in uw eigen proces, in tegenstelling tot een proces dat eigendom is van speciale webserversoftware, zoals IIS.

Als u een Service Fabric-service wilt combineren en ASP.NET, als gast uitvoerbaar of in een betrouwbare service, moet u ASP.NET kunnen starten binnen uw servicehostproces. ASP.NET Core self-hosting u dit doen.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hosting ASP.NET Core in een betrouwbare service
Meestal maken zelfgehoste ASP.NET Kerntoepassingen een WebHost in het ingangspunt van een toepassing, zoals de `static void Main()` methode in `Program.cs`. In dit geval is de levenscyclus van de WebHost gebonden aan de levenscyclus van het proces.

![Hosting ASP.NET Core in een proces][0]

Maar het ingangspunt voor toepassingen is niet de juiste plek om een WebHost te maken in een betrouwbare service. Dat komt omdat het invoerpunt voor toepassingen alleen wordt gebruikt om een servicetype te registreren met de runtime servicefabric, zodat het exemplaren van dat servicetype kan maken. De WebHost moet worden gemaakt in een betrouwbare service zelf. Binnen het servicehostproces kunnen service-exemplaren en/of replica's meerdere levenscycli doorlopen. 

Een instantie Betrouwbare Service wordt vertegenwoordigd door uw `StatelessService` `StatefulService`serviceklasse die afkomstig is van of . De communicatiestack voor een service `ICommunicationListener` is opgenomen in een implementatie in uw serviceklasse. De `Microsoft.ServiceFabric.AspNetCore.*` NuGet-pakketten bevatten `ICommunicationListener` implementaties van die start en beheren de ASP.NET Core WebHost voor kestrel of HTTP.sys in een betrouwbare service.

![Diagram voor het hosten van ASP.NET Core in een betrouwbare service][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
De `ICommunicationListener` implementaties voor Torenvalk en `Microsoft.ServiceFabric.AspNetCore.*` HTTP.sys in de NuGet-pakketten hebben vergelijkbare gebruikspatronen. Maar ze voeren iets verschillende acties uit die specifiek zijn voor elke webserver. 

Beide communicatielisteners bieden een constructeur die de volgende argumenten aanneemt:
 - **`ServiceContext serviceContext`**: Dit `ServiceContext` is het object dat informatie bevat over de lopende service.
 - **`string endpointName`**: Dit is de `Endpoint` naam van een configuratie in ServiceManifest.xml. Het is vooral waar de twee communicatie luisteraars verschillen. HTTP.sys *requires* vereist `Endpoint` een configuratie, terwijl Torenvalk dat niet doet.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Dit is een lambda die u implementeert, waarin u een `IWebHost`. Hiermee u `IWebHost` de manier configureren waarop u normaal gesproken in een ASP.NET Core-toepassing zou configureren. De lambda biedt een URL die voor u wordt gegenereerd, afhankelijk van `Endpoint` de integratieopties voor Service Fabric die u gebruikt en de configuratie die u biedt. U die URL vervolgens wijzigen of gebruiken om de webserver te starten.

## <a name="service-fabric-integration-middleware"></a>Service Fabric integratie middleware
Het `Microsoft.ServiceFabric.AspNetCore` NuGet-pakket `UseServiceFabricIntegration` bevat `IWebHostBuilder` de uitbreidingsmethode waarop Service Fabric-bewuste middleware wordt toegevoegd. Deze middleware configureert de Torenvalk of HTTP.sys `ICommunicationListener` om een unieke service-URL te registreren met de Service Fabric Naming Service. Vervolgens valideert het clientaanvragen om ervoor te zorgen dat clients verbinding maken met de juiste service. 

Deze stap is nodig om te voorkomen dat clients per ongeluk verbinding maken met de verkeerde service. Dat komt omdat in een gedeelde hostomgeving, zoals Service Fabric, meerdere webtoepassingen op dezelfde fysieke of virtuele machine kunnen worden uitgevoerd, maar geen unieke hostnamen gebruiken. Dit scenario wordt in de volgende sectie nader beschreven.

### <a name="a-case-of-mistaken-identity"></a>Een geval van verkeerde identiteit
Servicereplica's, ongeacht het protocol, luisteren naar een unieke IP:-poortcombinatie. Zodra een servicereplica is begonnen met luisteren op een IP:poorteindpunt, wordt dat eindpunt gerapporteerd aan de Service Fabric Naming Service. Daar kunnen klanten of andere diensten het ontdekken. Als services dynamisch toegewezen toepassingspoorten gebruiken, kan een servicereplica toevallig hetzelfde IP:-poorteindpunt van een andere service gebruiken die voorheen op dezelfde fysieke of virtuele machine was toegewezen. Dit kan ertoe leiden dat een client per ongeluk verbinding maakt met de verkeerde service. Dit scenario kan het gevolg zijn als de volgende reeks gebeurtenissen optreedt:

 1. Service A luistert op 10.0.0.1:30000 via HTTP. 
 2. Client lost Service A op en krijgt adres 10.0.0.1:30000.
 3. Service A wordt verplaatst naar een ander knooppunt.
 4. Service B is geplaatst op 10.0.0.1 en maakt toevallig gebruik van dezelfde poort 30000.
 5. Client probeert verbinding te maken met service A met adres in de cache 10.0.0.1:30000.
 6. Client is nu met succes verbonden met service B, niet realiserend dat deze is aangesloten op de verkeerde service.

Dit kan leiden tot bugs op willekeurige momenten die moeilijk te diagnosticeren kunnen zijn.

### <a name="using-unique-service-urls"></a>Unieke service-URL's gebruiken
Om deze bugs te voorkomen, kunnen services een eindpunt naar de naamgevingsservice plaatsen met een unieke id en die unieke id valideren tijdens clientaanvragen. Dit is een samenwerkingsverband tussen diensten in een niet-vijandige-tenant vertrouwde omgeving. Het biedt geen veilige serviceverificatie in een vijandige tenant-omgeving.

In een vertrouwde omgeving voegt de middleware `UseServiceFabricIntegration` die door de methode wordt toegevoegd automatisch een unieke id toe aan het adres dat naar de naamgevingsservice is geplaatst. Het valideert die id op elke aanvraag. Als de id niet overeenkomt, retourneert de middleware onmiddellijk een HTTP 410 Gone-antwoord.

Services die een dynamisch toegewezen poort gebruiken, moeten gebruik maken van deze middleware.

Services die gebruik maken van een vaste unieke poort hebben dit probleem niet in een coöperatieve omgeving. Een vaste unieke poort wordt meestal gebruikt voor extern gerichte services die een bekende poort nodig hebben voor clienttoepassingen om verbinding mee te maken. De meeste internetgerichte webtoepassingen gebruiken bijvoorbeeld poort 80 of 443 voor webbrowserverbindingen. In dit geval mag de unieke id niet zijn ingeschakeld.

In het volgende diagram ziet u de aanvraagstroom met de middleware ingeschakeld:

![Service Fabric ASP.NET Core-integratie][2]

Zowel Kestrel als `ICommunicationListener` HTTP.sys implementaties gebruiken dit mechanisme op precies dezelfde manier. Hoewel HTTP.sys aanvragen intern kan differentiëren op basis van unieke URL-paden met *not* behulp van de `ICommunicationListener` onderliggende functie voor het delen van **http.sys-poorten,** wordt die functionaliteit niet gebruikt door de HTTP.sys-implementatie. Dat komt omdat het resulteert in HTTP 503 en HTTP 404 foutstatuscodes in het eerder beschreven scenario. Dat maakt het op zijn beurt moeilijk voor klanten om de bedoeling van de fout te bepalen, omdat HTTP 503 en HTTP 404 vaak worden gebruikt om andere fouten aan te geven. 

Zo standaardiseren zowel Kestrel `ICommunicationListener` als HTTP.sys implementaties `UseServiceFabricIntegration` op middleware die door de uitbreidingsmethode worden geleverd. Clients hoeven daarom alleen een serviceeindpunt uit te voeren dat opnieuw wordt opgelost op HTTP 410-antwoorden.

## <a name="httpsys-in-reliable-services"></a>HTTP.sys in Betrouwbare Services
U HTTP.sys gebruiken in Betrouwbare services door het **Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet-pakket te importeren. Dit pakket `HttpSysCommunicationListener`bevat een `ICommunicationListener`implementatie van . `HttpSysCommunicationListener`hiermee u een ASP.NET Core WebHost maken in een betrouwbare service met HTTP.sys als webserver.

HTTP.sys is gebouwd op de [Windows HTTP Server API.](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx) Deze API gebruikt het **HTTP.sys-kernelstuurprogramma** om HTTP-aanvragen te verwerken en door te sturen naar processen die webtoepassingen uitvoeren. Hierdoor kunnen meerdere processen op dezelfde fysieke of virtuele machine webtoepassingen hosten op dezelfde poort, gedesbigbiguated door een uniek URL-pad of hostnaam. Deze functies zijn handig in Service Fabric voor het hosten van meerdere websites in hetzelfde cluster.

>[!NOTE]
>HTTP.sys-implementatie werkt alleen op het Windows-platform.

In het volgende diagram ziet u hoe HTTP.sys het **HTTP.sys-kernelstuurprogramma** in Windows gebruikt voor het delen van poorten:

![HTTP.sys-diagram][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP.sys in een staatloze service
Als `HttpSys` u deze wilt gebruiken `CreateServiceInstanceListeners` in een `HttpSysCommunicationListener` service zonder staat, overschrijft u de methode en retourneert u een instantie:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HTTP.sys in een stateful service

`HttpSysCommunicationListener`is momenteel niet ontworpen voor gebruik in stateful **HTTP.sys** services vanwege complicaties met de onderliggende HTTP.sys-poortdelingfunctie. Zie voor meer informatie het volgende gedeelte over dynamische poorttoewijzing met HTTP.sys. Voor stateful diensten, Torenvalk is de voorgestelde webserver.

### <a name="endpoint-configuration"></a>Eindpuntconfiguratie

Er `Endpoint` is een configuratie vereist voor webservers die de Windows HTTP Server API gebruiken, waaronder HTTP.sys. Webservers die de Windows HTTP Server API gebruiken, moeten eerst hun URL reserveren met HTTP.sys (dit wordt normaal gesproken bereikt met het [netsh-hulpprogramma).](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) 

Deze actie vereist verhoogde bevoegdheden die uw services standaard niet hebben. De opties 'http' of 'https' voor de `Protocol` eigenschap van de `Endpoint` configuratie in ServiceManifest.xml worden specifiek gebruikt om de runtime van Service Fabric te instrueren om namens u een URL te registreren bij HTTP.sys. Het doet dit met behulp van de [*sterke wildcard*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) URL voorvoegsel.

Als u bijvoorbeeld `http://+:80` wilt reserveren voor een service, gebruikt u de volgende configuratie in ServiceManifest.xml:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

En de naam van het `HttpSysCommunicationListener` eindpunt moet worden doorgegeven aan de constructeur:

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>HTTP.sys gebruiken met een statische poort
Als u een statische poort met HTTP.sys `Endpoint` wilt gebruiken, geeft u het poortnummer in de configuratie op:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>HTTP.sys gebruiken met een dynamische poort
Als u een dynamisch toegewezen poort met HTTP.sys wilt gebruiken, laat u de `Port` eigenschap in de `Endpoint` configuratie weg:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Een dynamische poort die `Endpoint` door een configuratie wordt toegewezen, biedt slechts één poort *per hostproces.* Met het huidige Service Fabric-hostingmodel kunnen meerdere service-exemplaren en/of replica's in hetzelfde proces worden gehost. Dit betekent dat elk dezelfde poort deelt `Endpoint` wanneer deze via de configuratie wordt toegewezen. Meerdere **HTTP.sys-exemplaren** kunnen een poort delen met behulp van de onderliggende functie voor het delen van **HTTP.sys-poorten.** Maar het wordt niet `HttpSysCommunicationListener` ondersteund door vanwege de complicaties die het introduceert voor verzoeken van klanten. Voor dynamisch poortgebruik is Torenvalk de voorgestelde webserver.

## <a name="kestrel-in-reliable-services"></a>Torenvalk in betrouwbare diensten
U Kestrel gebruiken in Betrouwbare Services door het **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet-pakket te importeren. Dit pakket `KestrelCommunicationListener`bevat een `ICommunicationListener`implementatie van . `KestrelCommunicationListener`stelt u in staat om een ASP.NET Core WebHost te maken in een betrouwbare service met behulp van Torenvalk als webserver.

Torenvalk is een cross-platform webserver voor ASP.NET Core. In tegenstelling tot HTTP.sys gebruikt Kestrel geen gecentraliseerde endpointmanager. Ook in tegenstelling tot HTTP.sys ondersteunt Kestrel geen poortdeling tussen meerdere processen. Elk geval van Torenvalk moet gebruik maken van een unieke poort. Voor meer informatie over Torenvalk, zie de [implementatie details](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2).

![Torenvalkdiagram][4]

### <a name="kestrel-in-a-stateless-service"></a>Torenvalk in een staatloze dienst
Als `Kestrel` u deze wilt gebruiken `CreateServiceInstanceListeners` in een `KestrelCommunicationListener` service zonder staat, overschrijft u de methode en retourneert u een instantie:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Torenvalk in een statige dienst
Als `Kestrel` u deze wilt gebruiken `CreateServiceReplicaListeners` in een `KestrelCommunicationListener` stateful service, overschrijft u de methode en retourneert u een instantie:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

In dit voorbeeld wordt een `IReliableStateManager` singleton-exemplaar van wordt verstrekt aan de WebHost-afhankelijkheidsinjectiecontainer. Dit is niet strikt noodzakelijk, maar `IReliableStateManager` het stelt u in staat om te gebruiken en betrouwbare collecties in uw MVC controller actiemethoden.

Een `Endpoint` configuratienaam *not* wordt `KestrelCommunicationListener` niet verstrekt aan een stateful service. Dit wordt in de volgende sectie nader toegelicht.

### <a name="configure-kestrel-to-use-https"></a>Kestrel configureren voor gebruik van HTTPS
Wanneer u HTTPS inschakelt met Torenvalk in uw service, moet u verschillende luisteropties instellen. Werk `ServiceInstanceListener` het eindpunt bijom een *eindpunthttps-eindpunt* te gebruiken en te luisteren op een specifieke poort (zoals poort 443). Wanneer u de webhost configureert om de Kestrel-webserver te gebruiken, moet u Torenvalk configureren om naar IPv6-adressen te luisteren op alle netwerkinterfaces: 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Zie [Torenvalk configureren om HTTPS te gebruiken](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https)voor een volledig voorbeeld in een zelfstudie.


### <a name="endpoint-configuration"></a>Eindpuntconfiguratie
Een `Endpoint` configuratie is niet vereist om Torenvalk te gebruiken. 

Torenvalk is een eenvoudige standalone webserver. In tegenstelling tot HTTP.sys (of HttpListener) heeft het geen `Endpoint` configuratie nodig in ServiceManifest.xml omdat er geen URL-registratie nodig is voordat deze wordt gestart. 

#### <a name="use-kestrel-with-a-static-port"></a>Kestrelen gebruiken met een statische poort
U een statische `Endpoint` poort configureren in de configuratie van ServiceManifest.xml voor gebruik met Torenvalk. Hoewel dit niet strikt noodzakelijk is, biedt het twee potentiële voordelen:
 - Als de poort niet in het bereik van de toepassingspoort valt, wordt deze geopend via de OS-firewall van Service Fabric.
 - De URL die `KestrelCommunicationListener` u via wordt verstrekt, gebruikt deze poort.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Als `Endpoint` een een is geconfigureerd, moet `KestrelCommunicationListener` de naam worden doorgegeven aan de constructor: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Als ServiceManifest.xml geen `Endpoint` configuratie gebruikt, laat u `KestrelCommunicationListener` de naam in de constructor weg. In dit geval zal het gebruik maken van een dynamische poort. Zie de volgende sectie voor meer informatie hierover.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Gebruik Torenvalk met een dynamische poort
Torenvalk kan de automatische poorttoewijzing `Endpoint` vanuit de configuratie in ServiceManifest.xml niet gebruiken. Dat komt omdat automatische poorttoewijzing vanuit een `Endpoint` configuratie een unieke poort per *hostproces*toewijst en één hostproces meerdere Torenvalk-exemplaren kan bevatten. Dit werkt niet met Torenvalk omdat het geen ondersteuning biedt voor het delen van poorten. Daarom moet elke Torenvalk-instantie op een unieke poort worden geopend.

Als u dynamische poorttoewijzing met Torenvalk wilt gebruiken, laat u de `Endpoint` configuratie in ServiceManifest.xml volledig weg en geeft u geen eindpuntnaam als volgt door aan de `KestrelCommunicationListener` constructor:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

In deze `KestrelCommunicationListener` configuratie selecteert u automatisch een ongebruikte poort uit het bereik van de toepassingspoort.

Voor HTTPS moet het eindpunt zijn geconfigureerd met HTTPS-protocol zonder een poort die is opgegeven in ServiceManifest.xml en de naam van het eindpunt doorgeven aan de constructor KestrelCommunicationListener.


## <a name="service-fabric-configuration-provider"></a>Leverancier van servicefabric-configuratie
De app-configuratie in ASP.NET Core is gebaseerd op sleutelwaardeparen die zijn ingesteld door de configuratieprovider. Lees [Configuratie in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) om meer te weten te komen over algemene ASP.NET Core-configuratieondersteuning.

In dit gedeelte wordt beschreven hoe de provider van de `Microsoft.ServiceFabric.AspNetCore.Configuration` Service Fabric-configuratie integreert met ASP.NET Core-configuratie door het NuGet-pakket te importeren.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Opstartextensies addServiceFabricConfiguration
Nadat u `Microsoft.ServiceFabric.AspNetCore.Configuration` het NuGet-pakket hebt geïmporteerd, moet u de bron voor servicestructuurconfiguratie registreren met ASP.NET Core-configuratie-API. U doet dit door **addservicefabricconfiguratie-extensies** in de `Microsoft.ServiceFabric.AspNetCore.Configuration` naamruimte te controleren tegen `IConfigurationBuilder`.

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

Nu heeft de ASP.NET Core-service toegang tot de configuratie-instellingen van Service Fabric, net als alle andere toepassingsinstellingen. U bijvoorbeeld het optiepatroon gebruiken om instellingen in sterk getypte objecten te laden.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Standaardsleuteltoewijzing
Standaard bevat de provider van de Service Fabric-configuratie de naam van het pakket, de naam van de sectie en de naam van de eigenschap. Samen vormen deze de ASP.NET Core-configuratiesleutel:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Als u bijvoorbeeld een configuratiepakket `MyConfigPackage` hebt met de naam van de volgende `IConfiguration` inhoud, is de configuratiewaarde beschikbaar op ASP.NET Core via *MyConfigPackage:MyConfigSection:MyParameter.*
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Configuratieopties voor servicefabric
De servicefabric-configuratieprovider `ServiceFabricConfigurationOptions` ondersteunt ook om het standaardgedrag van sleuteltoewijzing te wijzigen.

#### <a name="encrypted-settings"></a>Versleutelde instellingen
Service Fabric ondersteunt versleutelde instellingen, net als de servicefabric-configuratieprovider. De versleutelde instellingen worden niet standaard gedecodeerd naar ASP.NET Core. `IConfiguration` De versleutelde waarden worden daar opgeslagen. Maar als u de waarde wilt decoderen om op te slaan in ASP.NET Core `AddServiceFabricConfiguration` IConfiguration, u de *decryptValue-vlag* als volgt in de extensie instellen:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Meerdere configuratiepakketten
Service Fabric ondersteunt meerdere configuratiepakketten. Standaard is de pakketnaam opgenomen in de configuratiesleutel. Maar u kunt `IncludePackageName` de vlag als volgt op onwaar instellen:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Aangepaste sleuteltoewijzing, waardeextractie en gegevenspopulatie
De servicefabric-configuratieprovider ondersteunt ook meer geavanceerde `ExtractKeyFunc` scenario's om de `ExtractValueFunc`sleuteltoewijzing aan te passen en de waarden op maat te extraheren met . U zelfs het hele proces van het vullen van gegevens `ConfigAction`wijzigen van de configuratie van Service Fabric naar ASP.NET Core-configuratie met behulp van.

In de volgende voorbeelden `ConfigAction` ziet u hoe u gegevenspopulatie aanpassen:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>Configuratie-updates
De servicefabric-configuratieprovider ondersteunt ook configuratie-updates. U ASP.NET `IOptionsMonitor` Core gebruiken om wijzigingsmeldingen te ontvangen en vervolgens configuratiegegevens `IOptionsSnapshot` opnieuw te laden. Zie [ASP.NET Kernopties voor](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)meer informatie.

Deze opties worden standaard ondersteund. Er is geen verdere codering nodig om configuratie-updates in te schakelen.

## <a name="scenarios-and-configurations"></a>Scenario's en configuraties
In deze sectie vindt u de combinatie van webserver, poortconfiguratie, servicestructuur-integratieopties en diverse instellingen die we aanbevelen om de volgende scenario's op te lossen:
 - Extern blootgestelde ASP.NET Core stateless services
 - Intern-only ASP.NET Core stateless services
 - Interne ASP.NET Core stateful services

Een **extern belichte service** is een service die een eindpunt blootlegt dat van buiten het cluster wordt aangeroepen, meestal via een load balancer.

Een **interne** service is een service waarvan het eindpunt alleen vanuit het cluster wordt aangeroepen.

> [!NOTE]
> Stateful service eindpunten over het algemeen niet moeten worden blootgesteld aan het internet. Clusters achter load balancers die niet op de hoogte zijn van de serviceoplossing van de Service Fabric-service, zoals Azure Load Balancer, kunnen stateful-services niet blootstellen. Dat komt omdat de load balancer het verkeer niet kan lokaliseren en routeren naar de juiste stateful servicereplica. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Extern blootgestelde ASP.NET Core stateless services
Torenvalk is de voorgestelde webserver voor front-endservices die externe, internetgerichte HTTP-eindpunten blootleggen. In Windows kan HTTP.sys mogelijkheden bieden voor het delen van poorten, waarmee u meerdere webservices op dezelfde set knooppunten hosten met dezelfde poort. In dit scenario worden de webservices onderscheiden op naam of pad van de host, zonder afhankelijk te zijn van een front-end proxy of gateway om HTTP-routering te bieden.
 
Wanneer blootgesteld aan het internet, een stateless service moet gebruik maken van een bekende en stabiele eindpunt dat bereikbaar is via een load balancer. U geeft deze URL aan de gebruikers van uw toepassing. Wij raden de volgende configuratie aan:

|  |  | **Opmerkingen** |
| --- | --- | --- |
| Webserver | Torenvalk | Torenvalk is de favoriete webserver, omdat het wordt ondersteund in Windows en Linux. |
| Poortconfiguratie | statisch | Een bekende statische poort moet worden `Endpoints` geconfigureerd in de configuratie van ServiceManifest.xml, zoals 80 voor HTTP of 443 voor HTTPS. |
| ServiceFabric-integratieopties | Geen | Gebruik `ServiceFabricIntegrationOptions.None` de optie bij het configureren van Middleware voor servicefabric-integratie, zodat de service niet probeert inkomende aanvragen voor een unieke id te valideren. Externe gebruikers van uw toepassing zullen de unieke identificerende informatie die de middleware gebruikt, niet kennen. |
| Aantal instanties | -1 | In typische use cases moet de instelling voor het aantal gevallen worden ingesteld op *-1*. Dit wordt gedaan zodat een instantie beschikbaar is op alle knooppunten die verkeer ontvangen van een load balancer. |

Als meerdere extern belichte services dezelfde set knooppunten delen, u HTTP.sys gebruiken met een uniek maar stabiel URL-pad. U dit bereiken door de URL te wijzigen die wordt verstrekt bij het configureren van IWebHost. Houd er rekening mee dat dit alleen geldt voor HTTP.sys.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Alleen intern staatloze ASP.NET Core-service
Stateloze services die alleen vanuit het cluster worden aangeroepen, moeten unieke URL's en dynamisch toegewezen poorten gebruiken om de samenwerking tussen meerdere services te garanderen. Wij raden de volgende configuratie aan:

|  |  | **Opmerkingen** |
| --- | --- | --- |
| Webserver | Torenvalk | Hoewel u HTTP.sys gebruiken voor interne stateless services, is Torenvalk de beste server waarmee meerdere service-exemplaren een host kunnen delen.  |
| Poortconfiguratie | dynamisch toegewezen | Meerdere replica's van een stateful service kunnen een hostproces of host-besturingssysteem delen en hebben dus unieke poorten nodig. |
| ServiceFabric-integratieopties | GebruikUniqueServiceUrl | Bij dynamische poorttoewijzing voorkomt deze instelling het eerder beschreven foutieve identiteitsprobleem. |
| InstanceCount | elke | De instelling voor het aantal instantie en de instantie kan worden ingesteld op elke waarde die nodig is om de service te kunnen bedienen. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Alleen intern stateful ASP.NET Core-service
Stateful services die alleen vanuit het cluster worden aangeroepen, moeten dynamisch toegewezen poorten gebruiken om de samenwerking tussen meerdere services te waarborgen. Wij raden de volgende configuratie aan:

|  |  | **Opmerkingen** |
| --- | --- | --- |
| Webserver | Torenvalk | Het `HttpSysCommunicationListener` is niet ontworpen voor gebruik door stateful services waarin replica's een hostproces delen. |
| Poortconfiguratie | dynamisch toegewezen | Meerdere replica's van een stateful service kunnen een hostproces of host-besturingssysteem delen en hebben dus unieke poorten nodig. |
| ServiceFabric-integratieopties | GebruikUniqueServiceUrl | Bij dynamische poorttoewijzing voorkomt deze instelling het eerder beschreven foutieve identiteitsprobleem. |

## <a name="next-steps"></a>Volgende stappen
[Foutopsporing uitvoeren in uw Service Fabric-toepassing met behulp van Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
