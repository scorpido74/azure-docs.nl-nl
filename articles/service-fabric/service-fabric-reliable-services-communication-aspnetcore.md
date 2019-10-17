---
title: Service communicatie met de ASP.NET Core | Microsoft Docs
description: Meer informatie over het gebruik van ASP.NET Core in stateless en stateful Reliable Services.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 39e6273382133493a77321deed2baec4718bc912
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72383669"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core in azure Service Fabric Reliable Services

ASP.NET Core is een open-source-en platformoverschrijdende Framework. Dit framework is ontworpen voor het bouwen van Cloud toepassingen die zijn verbonden met internet, zoals web apps, IoT apps en mobiele back-ends.

Dit artikel is een diep gaande hand leiding voor het hosten van ASP.NET Core Services in Service Fabric Reliable Services met behulp van de **micro soft. ServiceFabric. AspNetCore.** set NuGet-pakketten.

Zie [zelf studie: een toepassing maken en implementeren met een ASP.net core web API-front-end-service en een stateful back-end-service](service-fabric-tutorial-create-dotnet-app.md)voor een inleidende zelf studie over ASP.NET Core in service Fabric en instructies over het instellen van uw ontwikkel omgeving.

In de rest van dit artikel wordt ervan uitgegaan dat u al bekend bent met ASP.NET Core. Als dat niet het geval is, lees dan de [ASP.net core fundamentals](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core in de Service Fabric omgeving

Zowel ASP.NET Core-als Service Fabric-apps kunnen worden uitgevoerd op .NET core-of volledige .NET Framework. U kunt ASP.NET Core op twee verschillende manieren gebruiken in Service Fabric:
 - **Gehost als een gast-uitvoerbaar bestand**. Op deze manier wordt hoofd zakelijk gebruikt om bestaande ASP.NET Core-toepassingen uit te voeren op Service Fabric zonder code wijzigingen.
 - **Binnen een betrouw bare service worden uitgevoerd**. Op deze manier kunt u de integratie met de Service Fabric-runtime verbeteren en stateful ASP.NET Core-Services toestaan.

In de rest van dit artikel wordt uitgelegd hoe u ASP.NET Core in een betrouw bare service kunt gebruiken via de ASP.NET Core-integratie onderdelen die bij de Service Fabric SDK worden geleverd.

## <a name="service-fabric-service-hosting"></a>Hosting van Service Fabric-service

In Service Fabric worden een of meer instanties en/of replica's van de service uitgevoerd in een *hostproces*: een uitvoerbaar bestand dat uw service code uitvoert. Als auteur van een service kunt u eigenaar zijn van het proces van de servicehost en Service Fabric het activeren en controleren.

Traditionele ASP.NET (tot MVC 5) is nauw gekoppeld aan IIS via System. Web. dll. ASP.NET Core biedt een schei ding tussen de webserver en uw webtoepassing. Met deze schei ding kunnen webtoepassingen worden overdraagbaar tussen verschillende webservers. Ook kunnen webservers *zichzelf hosten*. Dit betekent dat u een webserver in uw eigen proces kunt starten, in tegens telling tot een proces dat eigendom is van toegewezen webserver software, zoals IIS.

Als u een Service Fabric-service en ASP.NET wilt combi neren als een uitvoerbaar gast bestand of in een betrouw bare service, moet u ASP.NET in uw hostproces van de service kunnen starten. Met ASP.NET Core self-hosting kunt u dit doen.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>ASP.NET Core hosten in een betrouw bare service
Meestal maken zelf-hostende ASP.NET Core toepassingen een WebHost in het ingangs punt van een toepassing, zoals de `static void Main()`-methode in `Program.cs`. In dit geval is de levens cyclus van de WebHost gebonden aan de levens cyclus van het proces.

![ASP.NET Core in een proces hosten][0]

Maar het ingangs punt van de toepassing is niet de juiste plek om een WebHost in een betrouw bare service te maken. Dat komt doordat het ingangs punt van de toepassing alleen wordt gebruikt voor het registreren van een service type bij de runtime van Service Fabric, zodat het instanties van dat Service type kan maken. De WebHost moet worden gemaakt in een betrouw bare service zelf. Binnen het proces van de servicehost kunnen service-exemplaren en/of replica's meerdere levens cycli passeren. 

Een betrouwbaar service-exemplaar wordt vertegenwoordigd door de service klasse die is afgeleid van `StatelessService` of `StatefulService`. De communicatie stack voor een service bevindt zich in een `ICommunicationListener`-implementatie in uw service klasse. De NuGet-pakketten van `Microsoft.ServiceFabric.AspNetCore.*` bevatten implementaties van `ICommunicationListener` waarmee de ASP.NET Core WebHost voor Kestrel of HTTP. sys in een betrouw bare service wordt gestart en beheerd.

![Diagram voor het hosten van ASP.NET Core in een betrouw bare service][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
De `ICommunicationListener`-implementaties voor Kestrel en HTTP. sys in de `Microsoft.ServiceFabric.AspNetCore.*` NuGet-pakketten hebben vergelijk bare gebruiks patronen. Maar ze voeren enigszins verschillende acties uit die specifiek zijn voor elke webserver. 

Beide communicatie listeners bieden een constructor die de volgende argumenten gebruikt:
 - **`ServiceContext serviceContext`** : dit is het `ServiceContext`-object dat informatie bevat over de uitgevoerde service.
 - **`string endpointName`** : dit is de naam van een `Endpoint`-configuratie in ServiceManifest. XML. Het is hoofd zakelijk waar de twee communicatie-listeners verschillen. HTTP. sys *vereist* een `Endpoint` configuratie, terwijl Kestrel niet is.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`** : dit is een lambda die u implementeert, waarin u een `IWebHost` maakt en retourneert. U kunt hiermee `IWebHost` configureren zoals u normaal gesp roken zou doen in een ASP.NET Core toepassing. De Lambda bevat een URL die voor u wordt gegenereerd, afhankelijk van de Service Fabric integratie opties die u gebruikt en de `Endpoint`-configuratie die u opgeeft. U kunt deze URL vervolgens wijzigen of gebruiken om de webserver te starten.

## <a name="service-fabric-integration-middleware"></a>Middleware voor Service Fabric integratie
Het NuGet-pakket van `Microsoft.ServiceFabric.AspNetCore` bevat de uitbreidings methode `UseServiceFabricIntegration` op `IWebHostBuilder` waarmee u Service Fabric bewuste middleware toevoegt. Deze middleware configureert de Kestrel of HTTP. sys `ICommunicationListener` om een unieke service-URL te registreren bij de Service Fabric Naming Service. Vervolgens worden client aanvragen gevalideerd om ervoor te zorgen dat clients verbinding maken met de juiste service. 

Deze stap is nodig om te voor komen dat clients per ongeluk verbinding maken met de verkeerde service. Dat komt doordat meerdere webtoepassingen in een omgeving met gedeelde host, zoals Service Fabric, op dezelfde fysieke of virtuele machine kunnen worden uitgevoerd, maar geen unieke hostnamen. Dit scenario wordt uitgebreid beschreven in de volgende sectie.

### <a name="a-case-of-mistaken-identity"></a>Een geval van een onjuiste identiteit
Service replica's, ongeacht het Protocol, kunnen Luis teren naar een unieke combi natie van IP: poort. Zodra een service replica is begonnen met Luis teren op een IP: poort eindpunt, wordt het eind punt gerapporteerd aan de Service Fabric Naming Service. Daar kunnen clients of andere services het detecteren. Als services dynamisch toegewezen toepassings poorten gebruiken, kan een service replica het hetzelfde IP-adres van de poort van een andere service die eerder op dezelfde fysieke of virtuele machine wordt gebruikt, per keer gebruiken. Dit kan ertoe leiden dat een client per ongeluk verbinding maakt met de verkeerde service. Dit scenario kan optreden als de volgende reeks gebeurtenissen zich voordoet:

 1. Service A luistert op 10.0.0.1:30000 via HTTP. 
 2. De client lost Service A op en krijgt adres 10.0.0.1:30000.
 3. Service een verplaatst naar een ander knoop punt.
 4. Service B wordt op 10.0.0.1 geplaatst en maakt gebruik van dezelfde poort 30000.
 5. De client probeert verbinding te maken met de service A met het adres in de cache 10.0.0.1:30000.
 6. De client is nu verbonden met Service B, maar het is niet gelukt om verbinding te hebben met de verkeerde service.

Dit kan leiden tot fouten op wille keurige tijdstippen die lastig kunnen worden vastgesteld.

### <a name="using-unique-service-urls"></a>Unieke service-Url's gebruiken
Om deze fouten te voor komen, kunnen services een eind punt plaatsen op de Naming Service met een unieke id en vervolgens die unieke id valideren tijdens client aanvragen. Dit is een coöperatieve actie tussen services in een niet-vijandelijke, vertrouwde Tenant omgeving. Het biedt geen veilige verificatie van de service in een vijandelijke Tenant omgeving.

In een vertrouwde omgeving voegt de middleware die wordt toegevoegd met de methode `UseServiceFabricIntegration` automatisch een unieke id toe aan het adres dat naar de Naming Service wordt gepost. De id controleert op elke aanvraag. Als de id niet overeenkomt, retourneert de middleware onmiddellijk een HTTP 410-antwoord.

Services die gebruikmaken van een dynamisch toegewezen poort, moeten gebruikmaken van deze middleware.

Services die gebruikmaken van een vaste unieke poort, hebben dit probleem niet in een samenwerkings omgeving. Een vaste unieke poort wordt doorgaans gebruikt voor extern gerichte services die een bekende poort nodig hebben voor client toepassingen om verbinding mee te maken. De meeste Internet gerichte webtoepassingen gebruiken bijvoorbeeld poort 80 of 443 voor webbrowser verbindingen. In dit geval moet de unieke id niet worden ingeschakeld.

In het volgende diagram ziet u de aanvraag stroom waarbij de middleware is ingeschakeld:

![Integratie van Service Fabric ASP.NET Core][2]

Zowel Kestrel als HTTP. sys `ICommunicationListener`-implementaties gebruiken dit mechanisme op dezelfde manier. Hoewel HTTP. sys aanvragen intern kan onderscheiden op basis van unieke URL-paden door gebruik te maken van de onderliggende **http. sys** -functie voor het delen van poorten, wordt deze functionaliteit *niet* gebruikt door de http. sys `ICommunicationListener`-implementatie. Dat komt doordat de HTTP 503-en HTTP 404-fout status codes in het scenario dat eerder is beschreven, worden veroorzaakt. Op die manier is het voor clients lastig om het doel van de fout te bepalen, omdat HTTP 503 en HTTP 404 vaak worden gebruikt om andere fouten aan te geven. 

Dit betekent dat zowel Kestrel als HTTP. sys `ICommunicationListener` implementaties standaard worden gestandardization op middleware die door de uitbreidings methode `UseServiceFabricIntegration` wordt gegeven. Daarom hoeven clients alleen een actie voor het opnieuw oplossen van het service-eind punt uit te voeren op HTTP 410-reacties.

## <a name="httpsys-in-reliable-services"></a>HTTP. sys in Reliable Services
U kunt HTTP. sys in Reliable Services gebruiken door het pakket **micro soft. ServiceFabric. AspNetCore. HttpSys** NuGet te importeren. Dit pakket bevat `HttpSysCommunicationListener`, een implementatie van `ICommunicationListener`. met `HttpSysCommunicationListener` kunt u een ASP.NET Core WebHost in een betrouw bare service maken met behulp van HTTP. sys als de webserver.

HTTP. sys is gebaseerd op de [Windows HTTP-Server-API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Deze API gebruikt het **http. sys-** kernelstuurprogramma om HTTP-aanvragen te verwerken en te routeren naar processen die webtoepassingen uitvoeren. Hierdoor kunnen meerdere processen op dezelfde fysieke of virtuele machine fungeren als host voor webtoepassingen op dezelfde poort, disambiguated door een unieke URL-pad of hostnaam. Deze functies zijn handig in Service Fabric voor het hosten van meerdere websites in hetzelfde cluster.

>[!NOTE]
>HTTP. sys-implementatie werkt alleen op het Windows-platform.

In het volgende diagram ziet u hoe HTTP. sys het stuur programma **http. sys** gebruikt in Windows voor het delen van poorten:

![HTTP. sys-diagram][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP. sys in een stateless service
Als u `HttpSys` in een stateless service wilt gebruiken, overschrijft u de methode `CreateServiceInstanceListeners` en retourneert u een `HttpSysCommunicationListener`-exemplaar:

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

### <a name="httpsys-in-a-stateful-service"></a>HTTP. sys in een stateful service

`HttpSysCommunicationListener` is momenteel niet ontworpen voor gebruik in stateful Services vanwege complicaties met de onderliggende **http. sys-** functie voor het delen van poorten. Zie de volgende sectie over dynamische poort toewijzing met HTTP. sys voor meer informatie. Voor stateful Services is Kestrel de voorgestelde webserver.

### <a name="endpoint-configuration"></a>Eindpunt configuratie

Een `Endpoint`-configuratie is vereist voor webservers die gebruikmaken van de Windows HTTP-Server-API, met inbegrip van HTTP. sys. Webservers die gebruikmaken van de Windows HTTP-Server-API moeten eerst hun URL reserveren met HTTP. sys (dit wordt normaal gesp roken uitgevoerd met het hulp programma [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) ). 

Voor deze actie zijn verhoogde bevoegdheden vereist die uw services niet standaard hebben. De opties ' http ' of ' https ' voor de eigenschap `Protocol` van de configuratie van de `Endpoint` in ServiceManifest. XML worden speciaal gebruikt om de Service Fabric runtime te instrueren een URL te registreren met HTTP. sys namens u. Dit wordt gedaan met behulp van het URL-voor voegsel voor [*sterke joker tekens*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) .

Als u bijvoorbeeld `http://+:80` voor een service wilt reserveren, gebruikt u de volgende configuratie in ServiceManifest. XML:

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

En de naam van het eind punt moet worden door gegeven aan de constructor `HttpSysCommunicationListener`:

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

#### <a name="use-httpsys-with-a-static-port"></a>HTTP. sys gebruiken met een statische poort
Geef het poort nummer op in de configuratie van `Endpoint` als u een statische poort met HTTP. sys wilt gebruiken:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>HTTP. sys gebruiken met een dynamische poort
Als u een dynamisch toegewezen poort met HTTP. sys wilt gebruiken, laat u de eigenschap `Port` in de `Endpoint`-configuratie weg:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Een dynamische poort die wordt toegewezen door een configuratie van @no__t 0 biedt slechts één poort *per hostproces*. Met het huidige Service Fabric hosting model kunnen meerdere service-exemplaren en/of replica's worden gehost in hetzelfde proces. Dit betekent dat elke poort hetzelfde is wanneer deze wordt toegewezen via de configuratie van `Endpoint`. Meerdere **http. sys-** exemplaren kunnen een poort delen met behulp van de onderliggende **http. sys-** functie voor het delen van poorten. Maar het wordt niet ondersteund door `HttpSysCommunicationListener` vanwege de complicaties die het biedt voor client aanvragen. Voor dynamisch poort gebruik is Kestrel de aanbevolen webserver.

## <a name="kestrel-in-reliable-services"></a>Kestrel in Reliable Services
U kunt Kestrel in Reliable Services gebruiken door het NuGet-pakket **micro soft. ServiceFabric. AspNetCore. Kestrel** te importeren. Dit pakket bevat `KestrelCommunicationListener`, een implementatie van `ICommunicationListener`. met `KestrelCommunicationListener` kunt u een ASP.NET Core WebHost in een betrouw bare service maken met behulp van Kestrel als de webserver.

Kestrel is een cross-platform webserver voor ASP.NET Core. In tegens telling tot HTTP. sys gebruikt Kestrel geen gecentraliseerd eindpunt beheer. Ook in tegens telling tot HTTP. sys biedt Kestrel geen ondersteuning voor het delen van poorten tussen meerdere processen. Elk exemplaar van Kestrel moet een unieke poort gebruiken. Zie de [Implementatie Details](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2)voor meer informatie over Kestrel.

![Kestrel diagram][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel in een stateless service
Als u `Kestrel` in een stateless service wilt gebruiken, overschrijft u de methode `CreateServiceInstanceListeners` en retourneert u een `KestrelCommunicationListener`-exemplaar:

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

### <a name="kestrel-in-a-stateful-service"></a>Kestrel in een stateful service
Als u `Kestrel` in een stateful service wilt gebruiken, overschrijft u de methode `CreateServiceReplicaListeners` en retourneert u een `KestrelCommunicationListener`-exemplaar:

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

In dit voor beeld wordt er een singleton-exemplaar van `IReliableStateManager` aan de container van de WebHost-afhankelijkheids injectie gegeven. Dit is niet strikt nood zakelijk, maar u kunt wel gebruikmaken van `IReliableStateManager` en betrouw bare verzamelingen in uw actie methoden van MVC-controller.

Een `Endpoint`-configuratie naam wordt *niet* aan `KestrelCommunicationListener` in een stateful service gegeven. Dit wordt gedetailleerd beschreven in de volgende sectie.

### <a name="configure-kestrel-to-use-https"></a>Kestrel configureren voor gebruik van HTTPS
Wanneer u HTTPS inschakelt met Kestrel in uw service, moet u verschillende luisterende opties instellen. Werk de `ServiceInstanceListener` bij om een *EndpointHttps* -eind punt te gebruiken en luister naar een specifieke poort (zoals poort 443). Wanneer u de webhost configureert voor het gebruik van de Kestrel-webserver, moet u Kestrel configureren om te Luis teren naar IPv6-adressen op alle netwerk interfaces: 

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

Zie [Kestrel configureren voor het gebruik van HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https)voor een volledig voor beeld in een zelf studie.


### <a name="endpoint-configuration"></a>Eindpunt configuratie
Een `Endpoint`-configuratie is niet vereist voor het gebruik van Kestrel. 

Kestrel is een eenvoudige, zelfstandige webserver. In tegens telling tot HTTP. sys (of HttpListener), is er geen `Endpoint` configuratie nodig in ServiceManifest. XML omdat hiervoor geen URL-registratie is vereist voordat de service wordt gestart. 

#### <a name="use-kestrel-with-a-static-port"></a>Kestrel gebruiken met een statische poort
U kunt een statische poort configureren in de `Endpoint`-configuratie van ServiceManifest. XML voor gebruik met Kestrel. Hoewel dit niet strikt nood zakelijk is, biedt dit twee mogelijke voor delen:
 - Als de poort niet in het poort bereik van de toepassing valt, wordt deze geopend via de firewall van het besturings systeem door Service Fabric.
 - Voor de URL die u via `KestrelCommunicationListener` hebt ontvangen, wordt deze poort gebruikt.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Als een `Endpoint` is geconfigureerd, moet de naam ervan worden door gegeven aan de `KestrelCommunicationListener`-constructor: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Als ServiceManifest. XML geen `Endpoint`-configuratie gebruikt, laat u de naam in de `KestrelCommunicationListener`-constructor weg. In dit geval wordt een dynamische poort gebruikt. Zie de volgende sectie voor meer informatie hierover.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Kestrel gebruiken met een dynamische poort
Kestrel kan de automatische poort toewijzing niet gebruiken van de `Endpoint`-configuratie in ServiceManifest. XML. Dat komt doordat bij een automatische poort toewijzing van een `Endpoint`-configuratie een unieke poort per *hostproces*wordt toegewezen en één hostproces meerdere Kestrel-exemplaren kan bevatten. Dit werkt niet met Kestrel omdat het geen ondersteuning biedt voor het delen van poorten. Daarom moet elk Kestrel-exemplaar op een unieke poort worden geopend.

Als u dynamische poort toewijzing met Kestrel wilt gebruiken, laat u de `Endpoint`-configuratie in ServiceManifest. XML volledig weg en geeft u de naam van het eind punt niet door aan de constructor `KestrelCommunicationListener`, als volgt:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

In deze configuratie selecteert `KestrelCommunicationListener` automatisch een ongebruikte poort uit het poort bereik van de toepassing.

## <a name="service-fabric-configuration-provider"></a>Configuratie provider Service Fabric
App-configuratie in ASP.NET Core is gebaseerd op sleutel-waardeparen die zijn ingesteld door de configuratie provider. Lees de [configuratie in ASP.net core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) voor meer informatie over algemene ondersteuning voor ASP.net core-configuratie.

In deze sectie wordt beschreven hoe de Service Fabric-configuratie provider integreert met ASP.NET Core configuratie door het pakket voor de `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet te importeren.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration-opstart extensies
Nadat u het NuGet-pakket van `Microsoft.ServiceFabric.AspNetCore.Configuration` hebt geïmporteerd, moet u de Service Fabric configuratie bron registreren bij ASP.NET Core Configuratie-API. U doet dit door **AddServiceFabricConfiguration** -uitbrei dingen in de naam ruimte `Microsoft.ServiceFabric.AspNetCore.Configuration` te controleren op `IConfigurationBuilder`.

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

De ASP.NET Core-Service heeft nu toegang tot de Service Fabric configuratie-instellingen, net als bij andere toepassings instellingen. U kunt bijvoorbeeld het patroon opties gebruiken om instellingen te laden in sterk getypte objecten.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Standaard sleutel toewijzing
Standaard bevat de Service Fabric-configuratie provider de naam van het pakket, de naam van de sectie en de naam van de eigenschap. Deze vormen samen de ASP.NET Core configuratie sleutel, als volgt:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Als u bijvoorbeeld een configuratie pakket met de naam `MyConfigPackage` met de volgende inhoud hebt, is de configuratie waarde beschikbaar op ASP.NET Core `IConfiguration` via *MyConfigPackage: MyConfigSection: MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Configuratie opties Service Fabric
De Service Fabric-configuratie provider ondersteunt ook `ServiceFabricConfigurationOptions` om het standaard gedrag van sleutel toewijzing te wijzigen.

#### <a name="encrypted-settings"></a>Versleutelde instellingen
Service Fabric ondersteunt versleutelde instellingen, zoals de Service Fabric-configuratie provider. De versleutelde instellingen worden niet standaard naar ASP.NET Core `IConfiguration` gedecodeerd. In plaats daarvan worden de versleutelde waarden opgeslagen. Maar als u de waarde die u wilt opslaan in ASP.NET Core IConfiguration wilt ontsleutelen, kunt u als volgt de vlag *DecryptValue* instellen op False in de `AddServiceFabricConfiguration`-extensie:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Meerdere configuratie pakketten
Service Fabric ondersteunt meerdere configuratie pakketten. De naam van het pakket is standaard opgenomen in de configuratie sleutel. U kunt de vlag `IncludePackageName` echter als volgt instellen op False:
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
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Aangepaste sleutel toewijzing, waarde extractie en gegevens populatie
De Service Fabric-configuratie provider biedt ook ondersteuning voor geavanceerdere scenario's voor het aanpassen van de sleutel toewijzing met `ExtractKeyFunc` en aangepaste-extractie van de waarden met `ExtractValueFunc`. U kunt zelfs het hele proces voor het invullen van gegevens van Service Fabric configuratie naar ASP.NET Core configuratie wijzigen met behulp van `ConfigAction`.

De volgende voor beelden laten zien hoe u `ConfigAction` kunt gebruiken om de gegevens populatie aan te passen:
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
De configuratie provider Service Fabric ondersteunt ook configuratie-updates. U kunt ASP.NET Core `IOptionsMonitor` gebruiken om wijzigings meldingen te ontvangen en vervolgens `IOptionsSnapshot` gebruiken om configuratie gegevens opnieuw te laden. Zie [ASP.net core Options (opties](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)) voor meer informatie.

Deze opties worden standaard ondersteund. Er is geen verdere code ring nodig om configuratie-updates in te scha kelen.

## <a name="scenarios-and-configurations"></a>Scenario's en configuraties
In deze sectie vindt u een combi natie van webserver, poort configuratie, Service Fabric integratie opties en diverse instellingen voor het oplossen van de volgende scenario's:
 - Extern beschikbaar ASP.NET Core stateless Services
 - Alleen intern ASP.NET Core stateless Services
 - Alleen intern ASP.NET Core stateful Services

Een **extern** beschik bare service is een eind punt dat wordt aangeroepen van buiten het cluster, meestal via een Load Balancer.

Een service met **alleen interne** services waarvan het eind punt alleen binnen het cluster wordt aangeroepen.

> [!NOTE]
> Stateful service-eind punten mogen doorgaans niet worden blootgesteld aan Internet. Clusters achter de load balancers die geen inzicht hebben in Service Fabric service oplossing, zoals Azure Load Balancer, kunnen stateful Services niet weer geven. Dat komt doordat de load balancer geen verkeer naar de juiste stateful service replica kunt vinden en routeren. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Extern beschikbaar ASP.NET Core stateless Services
Kestrel is de voorgestelde webserver voor front-end-services die externe, Internet gerichte HTTP-eind punten beschikbaar maken. In Windows kan HTTP. sys mogelijkheden voor het delen van poorten bieden, waarmee u meerdere webservices op dezelfde set knoop punten kunt hosten met behulp van dezelfde poort. In dit scenario worden de webservices onderscheiden door de hostnaam of het pad, zonder dat hiervoor een front-end-proxy of-gateway nodig is om HTTP-route ring te bieden.
 
Wanneer een stateless service wordt blootgesteld aan Internet, moet een bekende en stabiele eind punt worden gebruikt dat bereikbaar is via een load balancer. U geeft deze URL naar de gebruikers van uw toepassing. We raden u aan de volgende configuratie uit te voeren:

|  |  | **Opmerkingen** |
| --- | --- | --- |
| Webserver | Kestrel | Kestrel is de voorkeurs webserver, omdat deze wordt ondersteund in Windows en Linux. |
| Poort configuratie | storing | Een bekende statische poort moet worden geconfigureerd in de `Endpoints`-configuratie van ServiceManifest. XML, zoals 80 voor HTTP of 443 voor HTTPS. |
| ServiceFabricIntegrationOptions | Geen | Gebruik de optie `ServiceFabricIntegrationOptions.None` bij het configureren van Service Fabric-integratie middleware, zodat de service niet probeert binnenkomende aanvragen voor een unieke id te valideren. Externe gebruikers van uw toepassing weten niet de unieke identiteits gegevens die door de middleware worden gebruikt. |
| Aantal instanties | -1 | Bij typische gebruiks voorbeelden moet de instelling voor het aantal instanties worden ingesteld op *-1*. Dit wordt gedaan, zodat er een exemplaar beschikbaar is op alle knoop punten die verkeer ontvangen van een load balancer. |

Als meerdere extern beschik bare Services dezelfde set knoop punten delen, kunt u HTTP. sys gebruiken met een uniek, maar een stabiel URL-pad. U kunt dit doen door de URL die u hebt ingevoerd bij het configureren van IWebHost te wijzigen. Houd er rekening mee dat dit alleen van toepassing is op HTTP. sys.

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

### <a name="internal-only-stateless-aspnet-core-service"></a>Alleen stateless ASP.NET Core Service met alleen interne status
Stateless services die alleen binnen het cluster worden aangeroepen, moeten unieke Url's en dynamisch toegewezen poorten gebruiken om de samen werking tussen meerdere services te garanderen. We raden u aan de volgende configuratie uit te voeren:

|  |  | **Opmerkingen** |
| --- | --- | --- |
| Webserver | Kestrel | Hoewel u HTTP. sys kunt gebruiken voor interne stateless Services, is Kestrel de beste server waarmee meerdere service-exemplaren een host kunnen delen.  |
| Poort configuratie | dynamisch toegewezen | Meerdere replica's van een stateful service kunnen een hostproces of host-besturings systeem delen, waardoor er unieke poorten nodig zijn. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Met dynamische poort toewijzing voor komt u met deze instelling dat eerder beschreven identiteits problemen worden opgelost. |
| InstanceCount | Iedere | De instelling voor het aantal instanties kan worden ingesteld op elke waarde die nodig is om de service te kunnen uitvoeren. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Alleen stateful ASP.NET Core-Service
Stateful services die alleen binnen het cluster worden aangeroepen, moeten dynamisch toegewezen poorten gebruiken om de samen werking tussen meerdere services te garanderen. We raden u aan de volgende configuratie uit te voeren:

|  |  | **Opmerkingen** |
| --- | --- | --- |
| Webserver | Kestrel | De `HttpSysCommunicationListener` is niet ontworpen voor gebruik door stateful services waarin replica's een hostproces delen. |
| Poort configuratie | dynamisch toegewezen | Meerdere replica's van een stateful service kunnen een hostproces of host-besturings systeem delen, waardoor er unieke poorten nodig zijn. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Met dynamische poort toewijzing voor komt u met deze instelling dat eerder beschreven identiteits problemen worden opgelost. |

## <a name="next-steps"></a>Volgende stappen
[Foutopsporing uitvoeren in uw Service Fabric-toepassing met behulp van Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
