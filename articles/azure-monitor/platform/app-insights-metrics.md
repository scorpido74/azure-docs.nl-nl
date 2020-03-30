---
title: Op Azure Application Insights log gebaseerde statistieken | Microsoft Documenten
description: In dit artikel worden Azure Application Insights-statistieken weergegeven met ondersteunde aggregaties en dimensies. De details over op logboeken gebaseerde statistieken omvatten de onderliggende Kusto-queryinstructies.
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 12bc51e800ef5ccd4ad3c72d3860fb22bac5b749
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664912"
---
# <a name="application-insights-log-based-metrics"></a>Op logboekstatistieken gebaseerde loginzichten van application Insights

Met op application Insights log gebaseerde statistieken u de status van uw bewaakte apps analyseren, krachtige dashboards maken en waarschuwingen configureren. Er zijn twee soorten statistieken:

* [Log-based metrics](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) achter de scène worden vertaald in [Kusto query's](https://docs.microsoft.com/azure/kusto/query/) van opgeslagen gebeurtenissen.
* [Standaardstatistieken](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) worden opgeslagen als vooraf geaggregeerde tijdreeksen.

Aangezien *standaardstatistieken* vooraf worden samengevoegd tijdens het verzamelen, hebben ze betere prestaties op querytijd. Dit maakt hen een betere keuze voor dashboarding en in real-time alerting. De *op logboeken gebaseerde statistieken* hebben meer dimensies, waardoor ze de superieure optie zijn voor gegevensanalyse en ad-hocdiagnostiek. Gebruik de [naamruimtekiezer](metrics-getting-started.md#create-your-first-metric-chart) om te schakelen tussen log-gebaseerde en standaardstatistieken in [metrics explorer](metrics-getting-started.md).

## <a name="interpret-and-use-queries-from-this-article"></a>Query's uit dit artikel interpreteren en gebruiken

In dit artikel worden statistieken weergegeven met ondersteunde aggregaties en dimensies. De details over op logboeken gebaseerde statistieken omvatten de onderliggende Kusto-queryinstructies. Voor het gemak gebruikt elke query standaardinstellingen voor tijdgranulariteit, grafiektype en soms het splitsen van dimensie, wat het gebruik van de query in Log Analytics vereenvoudigt zonder dat deze hoeft te worden gewijzigd.

Wanneer u dezelfde statistiek in [metrics explorer](metrics-getting-started.md)plot , zijn er geen standaardwaarden - wordt de query dynamisch aangepast op basis van uw grafiekinstellingen:

- Het geselecteerde **tijdsbereik** wordt vertaald in een extra *waar tijdstempel...* clausule om alleen de gebeurtenissen uit geselecteerde tijdsbereik te kiezen. Bijvoorbeeld, een grafiek met gegevens voor de meest recente 24 uur, de query omvat *| waar timestamp > geleden (24 uur)*.

- De geselecteerde **Tijd granulariteit** wordt in de uiteindelijke *samenvatting ... per bin(tijdstempel, [tijdkorrel])* clausule.

- Alle geselecteerde **filterafmetingen** worden vertaald in aanvullende *functies waar* clausules.

- De geselecteerde **dimensie van de gesplitste grafiek** wordt vertaald in een extra samenvatting. Als u bijvoorbeeld uw grafiek splitst op *locatie*en plot met behulp van een tijdgranulariteit van 5 minuten, wordt de *samenvattingsclausule* samengevat *... per opslaglocatie (tijdstempel, 5 m), locatie*.

> [!NOTE]
> Als u nieuw bent in de Kusto-querytaal, begint u met het kopiëren en plakken van Kusto-instructies in het queryvenster Log Analytics zonder wijzigingen aan te brengen. Klik **op Uitvoeren** om het basisdiagram weer te geven. Als u de syntaxis van querytaal begint te begrijpen, u beginnen met het aanbrengen van kleine wijzigingen en de impact van uw wijziging zien. Het verkennen van uw eigen gegevens is een geweldige manier om te beginnen met het realiseren van de volledige kracht van [Log Analytics](../../azure-monitor/log-query/get-started-portal.md) en [Azure Monitor.](../../azure-monitor/overview.md)

## <a name="availability-metrics"></a>Beschikbaarheidsstatistieken

Met statistieken in de categorie Beschikbaarheid u de status van uw webtoepassing zien zoals waargenomen op punten over de hele wereld. [Configureer de beschikbaarheidstests](../../azure-monitor/app/monitor-web-app-availability.md) om statistieken uit deze categorie te gebruiken.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Beschikbaarheid (beschikbaarheidResultaten/beschikbaarheidPercentage)
De *statistiek Beschikbaarheid* geeft het percentage van de webtestuitvoeringen weer dat geen problemen heeft gedetecteerd. De laagst mogelijke waarde is 0, wat aangeeft dat alle webtestruns zijn mislukt. De waarde van 100 betekent dat alle webtestuitvoeringen aan de validatiecriteria voldoen.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|---|---|---|
|Percentage|Average|Locatie uitvoeren, Naam testen|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Duur beschikbaarheidstest (beschikbaarheidResultaten/duur)

De statistiek *Beschikbaarheidstestduur* geeft aan hoeveel tijd het heeft geduurd voordat de webtest is uitgevoerd. Voor de [webtests met meerdere stappen](../../azure-monitor/app/availability-multistep.md)geeft de statistiek de totale uitvoeringstijd van alle stappen weer.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|---|---|---|
|Milliseconden|Gemiddeld, Min, Max|Locatie uitvoeren, Testnaam, Testresultaat

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Beschikbaarheidstests (beschikbaarheidResultaten/aantal)

De statistiek *Beschikbaarheidstests* weerspiegelt het aantal webtests dat wordt uitgevoerd door Azure Monitor.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|---|---|---|
|Count|Count|Locatie uitvoeren, Testnaam, Testresultaat|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Browserstatistieken

Browserstatistieken worden verzameld door de Application Insights JavaScript SDK van echte browsers voor eindgebruikers. Ze bieden geweldige inzichten in de ervaring van uw gebruikers met uw web-app. Browserstatistieken worden meestal niet gesampled, wat betekent dat ze een hogere precisie van de gebruiksnummers bieden in vergelijking met serverstatistieken die mogelijk worden scheefgetrokken door steekproeven.

> [!NOTE]
> Als u browserstatistieken wilt verzamelen, moet uw toepassing worden geinstrumenteerd met de [Application Insights JavaScript SDK.](../../azure-monitor/app/javascript.md)

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Laadtijd van browserpagina's (browserTimings/totalDuration)

|Meeteenheid|Ondersteunde aggregaties|Vooraf geaggregeerde afmetingen|
|---|---|---|
|Milliseconden|Gemiddeld, Min, Max|Geen|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>Verwerkingstijd client (browserTiming/verwerkingDuur)

|Meeteenheid|Ondersteunde aggregaties|Vooraf geaggregeerde afmetingen|
|---|---|---|
|Milliseconden|Gemiddeld, Min, Max|Geen|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Verbindingstijd van pagina's-netwerk (browserTimings/netwerkDuur)

|Meeteenheid|Ondersteunde aggregaties|Vooraf geaggregeerde afmetingen|
|---|---|---|
|Milliseconden|Gemiddeld, Min, Max|Geen|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Ontvangstvan de responstijd (browserTimings/receiveDuration)

|Meeteenheid|Ondersteunde aggregaties|Vooraf geaggregeerde afmetingen|
|---|---|---|
|Milliseconden|Gemiddeld, Min, Max|Geen|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Aanvraagtijd verzenden (browserTimings/sendDuration)

|Meeteenheid|Ondersteunde aggregaties|Vooraf geaggregeerde afmetingen|
|---|---|---|
|Milliseconden|Gemiddeld, Min, Max|Geen|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>Foutstatistieken

De statistieken in **Fouten** tonen problemen met het verwerken van aanvragen, afhankelijkheidsoproepen en gegenereerde uitzonderingen.

### <a name="browser-exceptions-exceptionsbrowser"></a>Browseruitzonderingen (uitzonderingen/browser)

Deze statistiek geeft het aantal gegooide uitzonderingen weer van uw toepassingscode die in de browser wordt uitgevoerd. Alleen uitzonderingen die worden ```trackException()``` bijgehouden met een API-aanroep voor Application Insights, zijn opgenomen in de statistiek.

|Meeteenheid|Ondersteunde aggregaties|Vooraf geaggregeerde afmetingen|Opmerkingen|
|---|---|---|---|
|Count|Count|Geen|Op logboeken gebaseerde versie gebruikt **somaggregatie**|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Fouten in afhankelijkheidsoproepen (afhankelijkheden/mislukt)

Het aantal mislukte afhankelijkheidsoproepen.

|Meeteenheid|Ondersteunde aggregaties|Vooraf geaggregeerde afmetingen|Opmerkingen|
|---|---|---|---|
|Count|Count|Geen|Op logboeken gebaseerde versie gebruikt **somaggregatie**|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Uitzonderingen (uitzonderingen/aantal)

Elke keer dat u een uitzondering op Application Insights registreert, wordt er gebeld naar de [methode trackException()](../../azure-monitor/app/api-custom-events-metrics.md#trackexception) van de SDK. De statistiek Uitzonderingen geeft het aantal geregistreerde uitzonderingen weer.

|Meeteenheid|Ondersteunde aggregaties|Vooraf geaggregeerde afmetingen|Opmerkingen|
|---|---|---|---|
|Count|Count|Naam van cloudrol, cloudrolinstantie, apparaattype|Op logboeken gebaseerde versie gebruikt **somaggregatie**|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Mislukte aanvragen (aanvragen/mislukt)

Het aantal bijgehouden serveraanvragen dat is gemarkeerd als *mislukt*. Standaard markeert de Application Insights SDK automatisch elk serververzoek dat HTTP-antwoordcode 5xx of 4xx heeft geretourneerd als een mislukt verzoek. U deze logica aanpassen door *de eigenschap succes* van het item request telemetrie te wijzigen in een aangepaste [telemetrieinitialisator](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

|Meeteenheid|Ondersteunde aggregaties|Vooraf geaggregeerde afmetingen|Opmerkingen|
|---|---|---|---|
|Count|Count|Cloudrolinstantie, Cloudrolnaam, Echt of synthetisch verkeer, Prestatie aanvragen, Antwoordcode|Op logboeken gebaseerde versie gebruikt **somaggregatie**|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>Serveruitzonderingen (uitzonderingen/server)

Deze statistiek geeft het aantal serveruitzonderingen weer.

|Meeteenheid|Ondersteunde aggregaties|Vooraf geaggregeerde afmetingen|Opmerkingen|
|---|---|---|---|
|Count|Count|Naam van cloudrol, cloudrolinstantie|Op logboeken gebaseerde versie gebruikt **somaggregatie**|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>Prestatiemeteritems

Gebruik statistieken in de categorie **Prestatiemeteritems** om toegang te krijgen tot [systeemprestatiemeteritems die zijn verzameld door Application Insights.](../../azure-monitor/app/performance-counters.md)

### <a name="available-memory-performancecountersavailablememory"></a>Beschikbaar geheugen (performancecounters/beschikbaarGeheugen)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>Uitzonderingspercentage (prestatietellers/uitzonderingtarief)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP-aanvraaguitvoeringstijd (performanceCounters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP-aanvraagpercentage (prestatietellers/aanvragenPerSeconde)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>HTTP-aanvragen in toepassingswachtrij (performanceCounters/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>Proces-CPU (performancecounters/processCpuPercentage)

De statistiek geeft aan hoeveel van de totale processorcapaciteit wordt verbruikt door het proces dat uw bewaakte app host.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
|Percentage|Gemiddeld, Min, Max|Cloudrolinstantie

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Io-snelheid verwerken (prestatietellers/processIOBytesPerSeconde)

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
|Bytes per seconde|Gemiddeld, Min, Max|Cloudrolinstantie

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Privébytes verwerken (prestatiemeteritems/processPrivateBytes)

Hoeveelheid niet-gedeeld geheugen dat het bewaakte proces heeft toegewezen voor de gegevens.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
|Bytes|Gemiddeld, Min, Max|Cloudrolinstantie

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Processortijd (performancecounters/processorCpuPercentage)

CPU-verbruik door *alle* processen die op de bewaakte serverinstantie worden uitgevoerd.

|Meeteenheid|Ondersteunde aggregaties|Ondersteunde dimensies|
|---|---|---|
|Percentage|Gemiddeld, Min, Max|Cloudrolinstantie

>[!NOTE]
> De statistiek processortijd is niet beschikbaar voor de toepassingen die worden gehost in Azure App Services. Gebruik de [metriek Proces-CPU](#process-cpu-performancecountersprocesscpupercentage) om het CPU-gebruik van de webtoepassingen die worden gehost in App Services bij te houden.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>Serverstatistieken

### <a name="dependency-calls-dependenciescount"></a>Afhankelijkheidsoproepen (afhankelijkheden/aantal)

Deze statistiek heeft betrekking op het aantal afhankelijkheidsoproepen.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>Afhankelijkheidsduur (afhankelijkheden/duur)

Deze statistiek verwijst naar de duur van afhankelijkheidsoproepen.

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>Serveraanvragen (aanvragen/aantal)

Deze statistiek geeft het aantal inkomende serveraanvragen weer dat door uw webtoepassing is ontvangen.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>Serverresponstijd (aanvragen/duur)

Deze statistiek weerspiegelt de tijd die nodig was voor het verwerken van binnenkomende aanvragen door de servers.

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>Metrische gebruiksgegevens

### <a name="page-view-load-time-pageviewsduration"></a>Laadtijd van de paginaweergave (paginaWeergaven/duur)

Deze statistiek verwijst naar de hoeveelheid tijd die het duurde voordat PageView-gebeurtenissen werden geladen.

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>Paginaweergaven (pageViews/count)

Het aantal PageView-gebeurtenissen dat is geregistreerd met de TrackPageView() Application Insights API.

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Sessies (sessies/tellingen)

Deze statistiek verwijst naar het aantal verschillende sessie-id's.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>Sporen (sporen/telling)

Het aantal traceringsverklaringen dat is vastgelegd met de TrackTrace() Application Insights API-aanroep.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Gebruikers (gebruikers/aantal)

Het aantal verschillende gebruikers dat toegang heeft tot uw toepassing. De nauwkeurigheid van deze statistiek kan aanzienlijk worden beïnvloed door telemetriebemonstering en -filtering.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>Gebruikers, Geverifieerd (gebruikers/geverifieerd)

Het aantal verschillende gebruikers dat zich heeft geverifieerd in uw toepassing.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```
