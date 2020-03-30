---
title: Schalen met meerdere instanties - Azure SignalR-service
description: In veel schalingsscenario's moet de klant vaak meerdere exemplaren inrichten en configureren om ze samen te gebruiken, om een grootschalige implementatie te maken. Voor sharding is bijvoorbeeld meerdere instantiesondersteuning vereist.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: 43d703312cbc1fc067a2d51d5623ed028ba01405
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158154"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Hoe schaal je SignalR-service met meerdere exemplaren?
De nieuwste SignalR Service SDK ondersteunt meerdere eindpunten voor SignalR Service-exemplaren. U deze functie gebruiken om de gelijktijdige verbindingen te schalen of te gebruiken voor berichten over verschillende regio's.

## <a name="for-aspnet-core"></a>Voor ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Hoe voeg je meerdere eindpunten van config toe?

Config met `Azure:SignalR:ConnectionString` `Azure:SignalR:ConnectionString:` toets of voor SignalR Service-verbindingstekenreeks.

Als de sleutel `Azure:SignalR:ConnectionString:`begint met , `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`moet `Name` `EndpointType` deze in `ServiceEndpoint` formaat zijn, waar en zijn eigenschappen van het object, en zijn toegankelijk vanuit code.

U meerdere instantieverbindingstekenreeksen `dotnet` toevoegen met de volgende opdrachten:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Hoe voeg je meerdere eindpunten toe uit code?

Er `ServicEndpoint` wordt een klasse geïntroduceerd om de eigenschappen van een Azure SignalR Service-eindpunt te beschrijven.
U meerdere instantieeindpunten configureren wanneer u Azure SignalR Service SDK gebruikt via:
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>Hoe u de endpointrouter aanpassen?

Standaard gebruikt de SDK de [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) om eindpunten op te halen.

#### <a name="default-behavior"></a>Standaardgedrag 
1. Routering van clientaanvragen

    Wanneer `/negotiate` client met de app-server. Sdk selecteert standaard **willekeurig** één eindpunt uit de set beschikbare serviceeindpunten.

2. Serverberichtroutering

    Wanneer *het verzenden van berichten naar een specifieke **-verbinding***, en de doelverbinding wordt doorgestuurd naar de huidige server, gaat het bericht rechtstreeks naar dat verbonden eindpunt. Anders worden de berichten verzonden naar elk Azure SignalR-eindpunt.

#### <a name="customize-routing-algorithm"></a>Routeringsalgoritme aanpassen
U uw eigen router maken wanneer u speciale kennis hebt om te bepalen naar welke eindpunten de berichten moeten gaan.

Een aangepaste router wordt hieronder gedefinieerd als `east-` een voorbeeld wanneer groepen `east`die beginnen met altijd naar het eindpunt met de naam :

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

Een ander voorbeeld hieronder, dat het standaardonderhandelingsgedrag overschrijft, om de eindpunten te selecteren, is afhankelijk van waar de app-server zich bevindt.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Vergeet niet om de router te registreren bij DI container met behulp van:

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>Voor ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Hoe voeg je meerdere eindpunten van config toe?

Config met `Azure:SignalR:ConnectionString` `Azure:SignalR:ConnectionString:` toets of voor SignalR Service-verbindingstekenreeks.

Als de sleutel `Azure:SignalR:ConnectionString:`begint met , `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`moet `Name` `EndpointType` deze in `ServiceEndpoint` formaat zijn, waar en zijn eigenschappen van het object, en zijn toegankelijk vanuit code.

U meerdere instantieverbindingstekenreeksen toevoegen aan: `web.config`

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Hoe voeg je meerdere eindpunten toe uit code?

Er `ServicEndpoint` wordt een klasse geïntroduceerd om de eigenschappen van een Azure SignalR Service-eindpunt te beschrijven.
U meerdere instantieeindpunten configureren wanneer u Azure SignalR Service SDK gebruikt via:

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>Hoe router aanpassen?

Het enige verschil tussen ASP.NET SignalR en ASP.NET Core `GetNegotiateEndpoint`SignalR is het http-contexttype voor . Voor ASP.NET SignalR is het van [het type IOwinContext.](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19)

Hieronder is de aangepaste onderhandelen voorbeeld voor ASP.NET SignalR:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Vergeet niet om de router te registreren bij DI container met behulp van:

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>Configuratie in scenario's voor meerdere regio's

Het `ServiceEndpoint` object `EndpointType` heeft een `primary` `secondary`eigenschap met waarde of .

`primary`eindpunten zijn voorkeurseindpunten om clientverkeer te ontvangen en worden beschouwd als betrouwbaardere netwerkverbindingen; `secondary` eindpunten worden beschouwd als minder betrouwbare netwerkverbindingen en worden alleen gebruikt voor het gebruik van server naar clientverkeer, bijvoorbeeld het uitzenden van berichten, niet voor het nemen van client naar serververkeer.

In gevallen van verschillende regio's kan het netwerk instabiel zijn. Voor één *app-server in Oost-VS*kan het eindpunt van de SignalR-service in dezelfde regio `secondary` *oost-VS* worden geconfigureerd als `primary` eindpunten in andere regio's die zijn gemarkeerd als . In deze configuratie kunnen serviceeindpunten in andere regio's berichten **ontvangen** van deze *Oost-Amerikaanse* app-server, maar er worden geen **cross-region** clients doorgestuurd naar deze app-server. De architectuur wordt weergegeven in het onderstaande diagram:

![Cross-Geo Infra](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Wanneer een `/negotiate` client probeert met de app-server, met de standaardrouter, **selecteert** `primary` SDK willekeurig één eindpunt uit de set beschikbare eindpunten. Wanneer het primaire eindpunt niet beschikbaar is, selecteert SDK `secondary` vervolgens **willekeurig** uit alle beschikbare eindpunten. Het eindpunt is gemarkeerd als **beschikbaar** wanneer de verbinding tussen server en het serviceeindpunt in leven is.

In het scenario voor meerdere `/negotiate` regio's, wanneer een client probeert met `primary` de app-server gehost in *Oost-VS,* standaard het eindpunt in dezelfde regio retourneert. Wanneer niet alle *Oost-Amerikaanse* eindpunten beschikbaar zijn, wordt de client doorgestuurd naar eindpunten in andere regio's. Fail-over sectie hieronder beschrijft het scenario in detail.

![Normaal onderhandelen](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Fail-over

Wanneer `primary` niet alle eindpunten beschikbaar `/negotiate` zijn, kiest `secondary` de klant uit de beschikbare eindpunten. Dit fail-over mechanisme vereist dat elk `primary` eindpunt moet dienen als eindpunt voor ten minste één app-server.

![Fail-over](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Volgende stappen

In deze handleiding leert u hoe u meerdere instanties configureert in dezelfde toepassing voor scenario's voor schalen, sharden en meerdere regio's.

Meerdere endpoints-ondersteuning kan ook worden gebruikt in scenario's voor hoge beschikbaarheid en noodherstel.

> [!div class="nextstepaction"]
> [Setup SignalR Service voor disaster recovery en hoge beschikbaarheid](./signalr-concept-disaster-recovery.md)
