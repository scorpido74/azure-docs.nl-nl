---
title: Toepassingskaart in Azure Application Insights | Microsoft Documenten
description: Complexe toepassingstopologieën bewaken met de toepassingskaart
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.openlocfilehash: 7c5c9173704535b1e34ffde5867bd512e3e02ed8
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80989524"
---
# <a name="application-map-triage-distributed-applications"></a>Toepassingskaart: Triage Distributed Applications

Application Map helpt u prestatieknelpunten of fouthotspots te herkennen in alle onderdelen van uw gedistribueerde toepassing. Elk knooppunt op de kaart vertegenwoordigt een toepassingscomponent of de afhankelijkheden ervan; en heeft status van status en waarschuwingen. U doorklikken van elk onderdeel naar meer gedetailleerde diagnostiek, zoals Application Insights-gebeurtenissen. Als uw app Azure-services gebruikt, u ook doorklikken naar Azure-diagnostiek, zoals aanbevelingen van SQL Database Advisor.

## <a name="what-is-a-component"></a>Wat is een component?

Componenten zijn onafhankelijk inzetbare onderdelen van uw distributed/microservices-toepassing. Ontwikkelaars en operationele teams hebben zichtbaarheid op codeniveau of toegang tot telemetrie gegenereerd door deze toepassingscomponenten. 

* Componenten verschillen van "waargenomen" externe afhankelijkheden zoals SQL, EventHub etc. waartoe uw team/organisatie mogelijk geen toegang heeft (code of telemetrie).
* Onderdelen worden uitgevoerd op een willekeurig aantal server-/rol-/container-exemplaren.
* Componenten kunnen afzonderlijke instrumentatiesleutels voor Application Insights zijn (zelfs als abonnementen verschillend zijn) of verschillende rollen die rapporteren aan één instrumentatiesleutel voor Application Insights. De preview-kaartervaring toont de componenten, ongeacht hoe ze zijn ingesteld.

## <a name="composite-application-map"></a>Samengestelde toepassingskaart

U de volledige toepassingstopologie zien voor meerdere niveaus van gerelateerde toepassingscomponenten. Componenten kunnen verschillende Application Insights-bronnen of verschillende rollen in één resource zijn. De app-kaart vindt componenten door http-afhankelijkheidsoproepen te volgen die worden gevoerd tussen servers waarbij de Application Insights SDK is geïnstalleerd. 

Deze ervaring begint met het geleidelijk ontdekken van de componenten. Wanneer u de toepassingskaart voor het eerst laadt, wordt een set query's geactiveerd om de componenten met betrekking tot deze component te ontdekken. Een knop in de linkerbovenhoek wordt bijgewerkt met het aantal onderdelen in uw toepassing wanneer deze worden ontdekt. 

Bij het klikken op "Mapcomponenten bijwerken" wordt de kaart vernieuwd met alle componenten die tot dat moment worden ontdekt. Afhankelijk van de complexiteit van uw toepassing kan het laden van dit verzoek een minuut duren.

Als alle componenten rollen zijn binnen één Application Insights-bron, is deze detectiestap niet vereist. De initiële belasting voor een dergelijke toepassing zal alle componenten hebben.

![Schermafbeelding van toepassingskaart](media/app-map/app-map-001.png)

Een van de belangrijkste doelstellingen met deze ervaring is om complexe topologieën te kunnen visualiseren met honderden componenten.

Klik op een component om gerelateerde inzichten te zien en ga naar de ervaring voor de prestaties en uitval voor dat onderdeel.

![Vangbal](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Fouten onderzoeken

Selecteer **fouten onderzoeken** om het deelvenster Fouten te starten.

![Schermafbeelding van de knop Fouten onderzoeken](media/app-map/investigate-failures.png)

![Schermafbeelding van de ervaring met fouten](media/app-map/failures.png)

### <a name="investigate-performance"></a>Prestaties onderzoeken

Als u prestatieproblemen wilt oplossen, selecteert u **prestaties onderzoeken**.

![Schermafbeelding van de knop Prestaties onderzoeken](media/app-map/investigate-performance.png)

![Schermafbeelding van de prestatie-ervaring](media/app-map/performance.png)

### <a name="go-to-details"></a>Ga naar details

Selecteer **ga naar details** om de end-to-end transactieervaring te verkennen, die weergaven kan bieden tot op het niveau van de callstack.

![Schermafbeelding van de knop go-to-details](media/app-map/go-to-details.png)

![Schermafbeelding van end-to-end transactiegegevens](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>Logboeken weergeven (Analytics)

Als u uw toepassingengegevens verder wilt opvragen en onderzoeken, klikt u op **Weergave in Logboeken (Analytics).**

![Schermafbeelding van weergave in de knop Analyse](media/app-map/view-logs.png)

![Schermafbeelding van analyse-ervaring. Lijngrafiek die de gemiddelde responsduur van een aanvraag in de afgelopen 12 uur samenvat.](media/app-map/log-analytics.png)

### <a name="alerts"></a>Waarschuwingen

Als u actieve waarschuwingen en de onderliggende regels wilt weergeven die ervoor zorgen dat de waarschuwingen worden geactiveerd, selecteert u **waarschuwingen**.

![Schermafbeelding van de knop Waarschuwingen](media/app-map/alerts.png)

![Schermafbeelding van analyse-ervaring](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Naam van een cloudrol instellen

Application Map gebruikt de eigenschap **cloudrolnaam** om de componenten op de kaart te identificeren. De Application Insights SDK voegt automatisch de eigenschap van de naam van de cloudrol toe aan de telemetrie die door componenten wordt uitgezonden. De SDK voegt bijvoorbeeld een naam van de website of de naam van de servicerol toe aan de eigenschap van de naam van de cloudrol. Er zijn echter gevallen waarin u de standaardwaarde wilt overschrijven. Ga als een andere naam van de cloudrol overschrijven en wijzigt wat er op de toepassingskaart wordt weergegeven:

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

**Schrijf aangepaste TelemetrieInitializer zoals hieronder.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET-apps: initialisator laden in de actieve telemetrieconfiguratie**

In ApplicationInsights.config :

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Een alternatieve methode voor ASP.NET Web-apps is om de initialisator in code te instantiëren, bijvoorbeeld in Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> Het toevoegen `ApplicationInsights.config` van `TelemetryConfiguration.Active` initialisator met behulp van of het gebruik is niet geldig voor ASP.NET Core-toepassingen. 

**ASP.NET Core-apps: initialisator laden in de telemetrieconfiguratie**

Voor [ASP.NET Core-toepassingen](asp-net-core.md#adding-telemetryinitializers) `TelemetryInitializer` wordt het toevoegen van een nieuwe gedaan door deze toe te voegen aan de container Dependency Injection, zoals hieronder wordt weergegeven. Dit gebeurt `ConfigureServices` in de `Startup.cs` methode van uw klas.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

# <a name="java"></a>[Java](#tab/java)

**Java-agent**

Voor [Java-agent 3.0](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) wordt de naam van de cloudrol als volgt ingesteld:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

U ook de naam van ```APPLICATIONINSIGHTS_ROLE_NAME```de cloudrol instellen met behulp van de omgevingsvariabele.

**Java-SDK**

Als u de SDK gebruikt, te beginnen met Application Insights Java SDK 2.5.0, u de naam van de cloudrol opgeven door bijvoorbeeld aan uw `<RoleName>` `ApplicationInsights.xml` bestand toe te voegen.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Als u Spring Boot gebruikt met de starter Voor het voorspringen van Toepassing-inzichten, hoeft u alleen uw aangepaste naam voor de toepassing in het bestand application.properties in te stellen.

`spring.application.name=<name-of-app>`

Met de startstart van De Spring Boot wordt automatisch de naam van de cloudrol toegewezen aan de waarde die u invoert voor de eigenschap spring.application.name.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Alternatieve methode voor Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```
---

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Inzicht in de naam van de cloudrol binnen de context van de toepassingskaart

Wat betreft het over hoe u over **de naam van de cloudrol**moet denken, kan het handig zijn om te kijken naar een toepassingskaart met meerdere namen van een cloudrol:

![Schermafbeelding van toepassingskaart](media/app-map/cloud-rolename.png)

In de toepassingskaart boven elk van de namen in groene vakken zijn cloudrolnaamwaarden voor verschillende aspecten van deze specifieke gedistribueerde toepassing. Dus voor deze app bestaan `Authentication` `acmefrontend`de `Inventory Management`rollen `Payment Processing Worker Role`uit: , , a . 

In het geval van deze app vertegenwoordigt elk van deze cloudrolnamen ook een andere unieke Application Insights-bron met hun eigen instrumentatiesleutels. Aangezien de eigenaar van deze toepassing toegang heeft tot elk van deze vier verschillende Application Insights-bronnen, kan Application Map een kaart van de onderliggende relaties samenvoegen.

Voor de [officiële definities](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Als alternatief kan **de functie-instantie** van de cloudrol handig zijn voor scenario's waarin de naam van de **cloudrol** u vertelt dat het probleem zich ergens in uw webfront-end bevindt, maar u uw webfront-end uitvoeren op meerdere load-balanced servers, zodat u een laag dieper inzoomen via Kusto-query's en weet of het probleem van invloed is op alle front-endservers op het web of slechts één kan uiterst belangrijk zijn.

Een scenario waarin u de waarde voor een cloudrolinstantie zou willen overschrijven als uw app wordt uitgevoerd in een containeromgeving waarin alleen het kennen van de afzonderlijke server mogelijk niet genoeg informatie is om een bepaald probleem te vinden.

Zie [Eigenschappen toevoegen: ITelemetryInitializer](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)voor meer informatie over het overschrijven van de eigenschap cloudrolnaam met telemetrieinitializers.

## <a name="troubleshooting"></a>Problemen oplossen

Als u problemen ondervindt om toepassingstoewijzing te laten werken zoals verwacht, probeert u de volgende stappen:

### <a name="general"></a>Algemeen

1. Zorg ervoor dat u een officieel ondersteunde SDK gebruikt. Niet-ondersteunde SDK's of community-SDK's bieden mogelijk geen ondersteuning voor correlatie.

    Raadpleeg dit [artikel](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) voor een lijst met ondersteunde SDK's.

2. Upgrade alle componenten naar de nieuwste SDK-versie.

3. Als u Azure-functies met C#gebruikt, upgradet u naar [Functies V2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Bevestig [dat de naam van de cloudrol](#set-cloud-role-name) correct is geconfigureerd.

5. Als er een afhankelijkheid ontbreekt, zorgt u ervoor dat deze in de lijst met [automatisch verzamelde afhankelijkheden](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies) staat. Als dat niet zo is, kunt u de afhankelijkheid nog steeds handmatig traceren met een [aanroep voor het traceren van de afhankelijkheid](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Te veel knooppunten op de kaart

Application Map construeert een toepassingsknooppunt voor elke unieke naam van de cloudrol die aanwezig is in uw aanvraagtelemetrie en een afhankelijkheidsknooppunt voor elke unieke combinatie van type,doel en cloudrolnaam in uw afhankelijkheidstelemetrie. Als er meer dan 10.000 knooppunten in uw telemetrie zijn, kan Toepassingskaart niet alle knooppunten en koppelingen ophalen, zodat uw kaart onvolledig is. Als dit gebeurt, verschijnt er een waarschuwingsbericht bij het bekijken van de kaart.

Bovendien ondersteunt Application Map slechts maximaal 1000 afzonderlijke niet-gegroepeerde knooppunten die tegelijk worden weergegeven. Toepassingskaart vermindert de visuele complexiteit door afhankelijkheden te groeperen die hetzelfde type en bellers hebben, maar als uw telemetrie te veel unieke namen van de cloudrol of te veel afhankelijkheidstypen heeft, is die groepering onvoldoende en kan de kaart niet worden weergegeven.

Om dit op te lossen, moet u uw instrumentatie wijzigen om de naam van de cloudrol, het afhankelijkheidstype en de afhankelijkheidsdoelvelden goed in te stellen.

* Afhankelijkheidsdoel moet de logische naam van een afhankelijkheid vertegenwoordigen. In veel gevallen is het gelijk aan de server of resourcenaam van de afhankelijkheid. In het geval van HTTP-afhankelijkheden is deze bijvoorbeeld ingesteld op de hostnaam. Het mag geen unieke id's of parameters bevatten die van het ene verzoek naar het andere veranderen.

* Afhankelijkheidstype moet het logische type afhankelijkheid vertegenwoordigen. HTTP, SQL of Azure Blob zijn bijvoorbeeld typische afhankelijkheidstypen. Het mag geen unieke id's bevatten.

* Het doel van de naam van de cloudrol wordt beschreven in de [bovenstaande sectie](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name).

## <a name="portal-feedback"></a>Portalfeedback

Als u feedback wilt geven, gebruikt u de feedbackoptie.

![Afbeelding van MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel over de correlatie [van telemetrie](correlation.md)voor meer informatie over hoe correlatie werkt in Application Insights.
* De [end-to-end transactiediagnostische ervaring](transaction-diagnostics.md) correleert telemetrie aan de serverzijde van alle bewaakte toepassingsinzichten in één weergave.
* Raadpleeg het artikel [over aangepaste bewerkingen](custom-operations-tracking.md) van ASP.NET Core voor geavanceerde correlatiescenario's in ASP.NET Core en ASP.NET.
