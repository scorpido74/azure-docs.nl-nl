---
title: Veelgestelde vragen over Azure-toepassing Insights | Microsoft Docs
description: Veelgestelde vragen over Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/16/2019
ms.openlocfilehash: 55a096cd4971664e55bb2cfd17f9f8927d7c32f5
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899525"
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights: veelgestelde vragen

## <a name="configuration-problems"></a>Configuratie problemen
*Ik ondervind problemen bij het instellen van mijn:*

* [.NET app](asp-net-troubleshoot-no-data.md)
* [Een app die al wordt uitgevoerd bewaken](monitor-performance-live-website-now.md#troubleshoot)
* [Diagnostische gegevens van Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Java-web-app](java-troubleshoot.md)

*Ik krijg geen gegevens van mijn server*

* [Firewall-uitzonde ringen instellen](ip-addresses.md)
* [Een ASP.NET-Server instellen](monitor-performance-live-website-now.md)
* [Een Java-Server instellen](java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Kan ik Application Insights gebruiken met...?

* [Web-apps op een IIS-server in een Azure VM-of Azure virtual machine-schaalset](azure-vm-vmss-apps.md)
* [Web-apps op een IIS-server, on-premises of in een VM](asp-net.md)
* [Java-Web-apps](java-get-started.md)
* [Node.js-apps](nodejs.md)
* [Web-apps op Azure](azure-web-apps.md)
* [Cloud Services op Azure](cloudservices.md)
* [App-servers die worden uitgevoerd in docker](docker.md)
* [Web-apps met één pagina](javascript.md)
* [SharePoint](sharepoint.md)
* [Windows-bureau blad-app](windows-desktop.md)
* [Andere platforms](platforms.md)

## <a name="is-it-free"></a>Is dit gratis?

Ja, voor experimenteel gebruik. In het Basic-prijs plan kan uw toepassing elke maand gratis een bepaalde hoeveelheid gegevens verzenden. De gratis limiet is groot genoeg voor ontwikkeling en het publiceren van een app voor een klein aantal gebruikers. U kunt een limiet instellen om te voor komen dat er meer dan een opgegeven hoeveelheid gegevens wordt verwerkt.

Grotere hoeveel heden telemetrie worden in rekening gebracht op basis van de GB. We bieden enkele tips over het [beperken van uw kosten](pricing.md).

In het Enter prise-plan worden kosten in rekening gebracht voor elke dag dat elk webserver knooppunt telemetrie verzendt. Het is geschikt als u doorlopend exporteren wilt gebruiken op een grote schaal.

[Lees het prijs plan](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-does-it-cost"></a>Wat kost deze oplossing?

* Open de **pagina gebruik en geschatte kosten** in een Application Insights resource. Er is een grafiek van recent gebruik. Als u wilt, kunt u een limiet voor gegevens volumes instellen.
* Open de [Blade Azure-facturering](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) om uw rekeningen voor alle resources weer te geven.

## <a name="q14"></a>Wat doet Application Insights wijzigen in mijn project?
De details zijn afhankelijk van het type project. Voor een webtoepassing:

* Voegt deze bestanden toe aan uw project:

  * ApplicationInsights. config.
  * AI. js
* Installeert deze NuGet-pakketten:

  * *Application Insights-API* : de core-API
  * *Application Insights-API voor webtoepassingen* : wordt gebruikt voor het verzenden van telemetrie van de server
  * *Application Insights-API voor Java script-toepassingen* : wordt gebruikt voor het verzenden van telemetrie van de client

    De pakketten bevatten de volgende assembly's:
  * Micro soft. ApplicationInsights
  * Micro soft. ApplicationInsights. platform
* Hiermee worden items ingevoegd in:

  * Web.config
  * packages. config
* (Alleen nieuwe projecten: als u [Application Insights toevoegt aan een bestaand project][start], moet u dit hand matig doen.) Hiermee worden fragmenten ingevoegd in de client-en server code om ze te initialiseren met de Application Insights Resource-ID. In een MVC-app wordt bijvoorbeeld code ingevoegd in de basis pagina weergaven/gedeeld/_Layout. cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Hoe kan ik upgrade van oudere SDK-versies?
Zie de [release opmerkingen](release-notes.md) voor de SDK die geschikt is voor uw type toepassing.

## <a name="update"></a>Hoe kan ik wijzigen met welke Azure-resource mijn project gegevens verzendt?
Klik in Solution Explorer met de rechter muisknop op `ApplicationInsights.config` en kies vervolgens **Update Application Insights**. U kunt de gegevens verzenden naar een bestaande of nieuwe resource in Azure. De update wizard wijzigt de instrumentatie sleutel in ApplicationInsights. config, waarmee wordt bepaald waar de server-SDK uw gegevens verzendt. Tenzij u ' Alles bijwerken ' uitschakelt, wordt ook de sleutel gewijzigd waar deze wordt weer gegeven op uw webpagina's.

## <a name="what-is-status-monitor"></a>Wat is Status Monitor?

Een bureau blad-app die u kunt gebruiken op uw IIS-webserver om Application Insights te configureren in web-apps. Er wordt geen telemetrie verzameld: u kunt deze stoppen wanneer u geen app configureert. 

[Meer informatie](monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Welke telemetrie wordt door Application Insights verzameld?

Van server web apps:

* HTTP-aanvragen
* [Afhankelijkheden](asp-net-dependencies.md). Aanroepen naar: SQL data bases; HTTP-aanroepen naar externe services; Azure Cosmos DB, tabel, Blob Storage en wachtrij. 
* [Uitzonde ringen](asp-net-exceptions.md) en stack traceringen.
* [Prestatie meter items](performance-counters.md) : als u [status monitor](monitor-performance-live-website-now.md)gebruikt, wordt [Azure monitoring voor app Services](azure-web-apps.md), [Azure monitoring voor VM of virtual machine Scale set](azure-vm-vmss-apps.md)of de [Application Insights verzamelde Writer](java-collectd.md).
* [Aangepaste gebeurtenissen en metrische gegevens](api-custom-events-metrics.md) die u codeert.
* [Traceer logboeken](asp-net-trace-logs.md) als u de juiste Collector configureert.

Van [client webpagina's](javascript.md):

* [Aantal pagina weergaven](usage-overview.md)
* [Ajax-aanroepen](asp-net-dependencies.md) Aanvragen die afkomstig zijn van een script dat wordt uitgevoerd.
* Laad gegevens pagina weergave
* Aantal gebruikers en sessies
* [Geverifieerde gebruikers-Id's](api-custom-events-metrics.md#authenticated-users)

Van andere bronnen, als u deze configureert:

* [Diagnostische gegevens van Azure](../platform/diagnostics-extension-to-application-insights.md)
* [Importeren naar Analytics](../platform/data-collector-api.md)
* [Log Analytics](../platform/data-collector-api.md)
* [Logstash](../platform/data-collector-api.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Kan ik een telemetrie uitfilteren of wijzigen?

Ja, op de server kunt u het volgende schrijven:

* Telemetrie-processor om eigenschappen te filteren of toe te voegen aan geselecteerde telemetriegegevens voordat ze vanuit uw app worden verzonden.
* De initialisatie functie voor telemetrie om eigenschappen toe te voegen aan alle items van telemetrie.

Meer informatie voor [ASP.net](api-filtering-sampling.md) of [Java](java-filter-telemetry.md).

## <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Hoe worden plaats, land/regio en andere geografische locatie gegevens berekend?

We zoeken het IP-adres (IPv4 of IPv6) van de webclient met behulp van [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Browser-telemetrie: we verzamelen het IP-adres van de afzender.
* Server-telemetrie: de Application Insights-module verzamelt het client-IP-adres. Het wordt niet verzameld als `X-Forwarded-For` is ingesteld.
* Raadpleeg dit [artikel](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection)voor meer informatie over hoe IP-adres en geolocatie gegevens worden verzameld in Application Insights.


U kunt de `ClientIpHeaderTelemetryInitializer` configureren om het IP-adres van een andere header te halen. In sommige systemen wordt het bijvoorbeeld verplaatst met een proxy, load balancer of CDN naar `X-Originating-IP`. [Meer informatie](https://apmtips.com/blog/2016/07/05/client-ip-address/).

U kunt [Power bi gebruiken](export-power-bi.md ) om uw aanvraag-telemetrie weer te geven op een kaart.


## <a name="data"></a>Hoe lang worden gegevens in de portal bewaard? Is het veilig?
Bekijk de [gegevens retentie en privacy][data].

## <a name="could-personal-data-be-sent-in-the-telemetry"></a>Kunnen persoons gegevens in de telemetrie worden verzonden?

Dit is mogelijk als uw code dergelijke gegevens verzendt. Dit kan ook gebeuren als variabelen in stack-traceringen persoons gegevens bevatten. Uw ontwikkel team moet risico beoordelingen uitvoeren om ervoor te zorgen dat persoons gegevens op de juiste wijze worden afgehandeld. Meer [informatie over gegevens retentie en privacy](data-retention-privacy.md).

**Alle** octetten van het webadres van de client zijn altijd ingesteld op 0 nadat de geografische locatie kenmerken zijn opgezocht.

## <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Mijn instrumentatie sleutel is zichtbaar in de bron van de webpagina. 

* Dit is gebruikelijk bij het controleren van oplossingen.
* Het kan niet worden gebruikt om uw gegevens te stelen.
* Het kan worden gebruikt om uw gegevens te scheef stellen of waarschuwingen te activeren.
* We hebben niet gehoord dat een klant dergelijke problemen heeft gehad.

U kunt het volgende doen:

* Gebruik twee afzonderlijke instrumentatie sleutels (afzonderlijke Application Insights resources) voor client-en Server gegevens. Of
* Schrijf een proxy die op uw server wordt uitgevoerd en laat de webclient gegevens verzenden via die proxy.

## <a name="post"></a>Hoe kan ik raadpleegt u POST gegevens in diagnostische Zoek opdrachten?
Er worden geen POST gegevens automatisch geregistreerd, maar u kunt een TrackTrace-aanroep gebruiken: plaats de gegevens in de bericht parameter. Dit heeft een maximale grootte die groter is dan de limieten voor teken reeks eigenschappen, maar u kunt er niet op filteren.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Moet ik een of meerdere Application Insights bronnen gebruiken?

Gebruik één resource voor alle onderdelen of rollen in één bedrijfs systeem. Gebruik afzonderlijke resources voor ontwikkelings-, test-en release versies en voor onafhankelijke toepassingen.

* [Bekijk de discussie hier](separate-resources.md)
* [Voor beeld-Cloud service met werk nemers en webrollen](cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Hoe kan ik de instrumentatie sleutel dynamisch wijzigen?

* [Discussie hier](separate-resources.md)
* [Voor beeld-Cloud service met werk nemers en webrollen](cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Wat zijn de gebruikers-en sessie aantallen?

* De Java script-SDK stelt een gebruikers cookie op de webclient in om terugkerende gebruikers te identificeren en een sessie cookie om activiteiten te groeperen.
* Als er geen script aan de client zijde is, kunt u [cookies instellen op de server](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Als een echte gebruiker uw site in verschillende browsers gebruikt of als u in-private/incognito Browse of verschillende computers gebruikt, worden deze meerdere keren geteld.
* Als u een aangemelde gebruiker op verschillende computers en browsers wilt identificeren, voegt u een aanroep toe aan [setAuthenticatedUserContext ()](api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a>Heb ik alles in Application Insights ingeschakeld?
| Wat u moet zien | Hoe kan ik het downloaden? | Waarom u wilt |
| --- | --- | --- |
| Beschikbaarheids grafieken |[Webtests](monitor-web-app-availability.md) |Weet u zeker dat uw web-app actief is |
| Server app-prestaties: reactie tijden,... |[Application Insights toevoegen aan uw project](asp-net.md) of [AI-status monitor op server installeren](monitor-performance-live-website-now.md) (of uw eigen code schrijven om [afhankelijkheden bij te houden](api-custom-events-metrics.md#trackdependency)) |Prestatie problemen detecteren |
| Telemetrie van afhankelijkheid |[AI-Status Monitor op server installeren](monitor-performance-live-website-now.md) |Problemen met data bases of andere externe onderdelen vaststellen |
| Stack traceringen ophalen van uitzonde ringen |[TrackException-aanroepen invoegen in uw code](asp-net-exceptions.md) (maar sommige worden automatisch gerapporteerd) |Uitzonde ringen detecteren en diagnosticeren |
| Logboek traceringen zoeken |[Een logboek registratie adapter toevoegen](asp-net-trace-logs.md) |Diagnose uitzonde ringen, prestatie problemen |
| Basis beginselen van client gebruik: pagina weergaven, sessies,... |[Java script-initialisatie functie in webpagina's](javascript.md) |Gebruiksanalyse |
| Aangepaste metrische gegevens van client |[Aanroepen bijhouden op webpagina's](api-custom-events-metrics.md) |Gebruikerservaring verbeteren |
| Aangepaste metrische gegevens voor de server |[Tracerings aanroepen op server](api-custom-events-metrics.md) |Business Intelligence |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Waarom zijn de aantallen in zoek-en metrische grafieken niet gelijk?

Door [steek proeven](sampling.md) wordt het aantal telemetriegegevens (aanvragen, aangepaste gebeurtenissen, enzovoort) verminderd die daad werkelijk vanuit uw app naar de portal worden verzonden. In de zoek opdracht ziet u het aantal items dat daad werkelijk is ontvangen. In metrische grafieken die een aantal gebeurtenissen weer geven, ziet u het aantal oorspronkelijke gebeurtenissen dat heeft plaatsgevonden. 

Elk item dat wordt verzonden, bevat een `itemCount`-eigenschap die laat zien hoeveel oorspronkelijke gebeurtenissen een item vertegenwoordigt. Als u de bemonsterings bewerking wilt observeren, kunt u deze query uitvoeren in Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automation

### <a name="configuring-application-insights"></a>Application Insights configureren

U kunt [Power shell-scripts](powershell.md) met Azure Broncontrole schrijven om het volgende te doen:

* Application Insights resources maken en bijwerken.
* Stel het prijs plan in.
* Haal de instrumentatie sleutel op.
* Een waarschuwing voor metrische gegevens toevoegen.
* Voeg een beschikbaarheids test toe.

U kunt geen metrisch Explorer-rapport instellen of continue export instellen.

### <a name="querying-the-telemetry"></a>Query's uitvoeren op de telemetrie

Gebruik de [rest API](https://dev.applicationinsights.io/) om [Analytics](analytics.md) -query's uit te voeren.

## <a name="how-can-i-set-an-alert-on-an-event"></a>Hoe kan ik een waarschuwing instellen voor een gebeurtenis?

Azure-waarschuwingen zijn alleen op metrische gegevens. Maak een aangepaste metriek die een drempel waarde voor waarden kruist wanneer uw gebeurtenis zich voordoet. Stel vervolgens een waarschuwing in voor de metrische gegevens. Houd er rekening mee dat: u ontvangt een melding wanneer de metriek de drempel in een van beide richtingen overschrijdt. u krijgt pas een melding als de eerste doorhaling, ongeacht of de aanvankelijke waarde hoog of laag is. Er is altijd een latentie van een paar minuten.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Zijn er kosten voor gegevens overdracht tussen een Azure-web-app en Application Insights?

* Als uw Azure-web-app wordt gehost in een Data Center waar zich een Application Insights verzamelings eindpunt bevindt, worden er geen kosten in rekening gebracht. 
* Als er geen eind punt van een verzameling in uw host Data Center is, worden door de telemetrie van uw app [uitgaande Azure-kosten](https://azure.microsoft.com/pricing/details/bandwidth/)in rekening gebracht.

Dit is niet afhankelijk van waar uw Application Insights-bron wordt gehost. Het hangt gewoon af van de distributie van onze eind punten.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Kan ik telemetrie verzenden naar de Application Insights Portal?

We raden u aan om onze Sdk's te gebruiken en de [SDK API](api-custom-events-metrics.md)te gebruiken. Er zijn varianten van de SDK voor verschillende [platforms](platforms.md). Deze Sdk's behandelen buffering, compressie, beperking, nieuwe pogingen, enzovoort. Het [opname schema](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) en het [eindpunt protocol](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) zijn echter openbaar.

## <a name="can-i-monitor-an-intranet-web-server"></a>Kan ik een intranet webserver bewaken?

Ja, maar u moet het verkeer naar onze services toestaan door Firewall-uitzonde ringen of proxy omleidingen.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Bekijk [hier](../../azure-monitor/app/ip-addresses.md)onze volledige lijst met Services en IP-adressen.

### <a name="firewall-exception"></a>Firewall-uitzonde ring

Hiermee staat u toe dat uw webserver telemetrie naar onze eind punten verzendt. 

### <a name="gateway-redirect"></a>Gateway omleiden

Verkeer van uw server naar een gateway op uw intranet routeren door eind punten in uw configuratie te overschrijven.
Als deze eigenschappen van het eind punt niet aanwezig zijn in uw configuratie, gebruiken deze klassen de standaard waarden die hieronder worden weer gegeven in het voor beeld ApplicationInsights. config. 

Uw gateway moet verkeer routeren naar het basis adres van het eind punt. Vervang in uw configuratie de standaard waarden door `http://<your.gateway.address>/<relative path>`.


#### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Voor beeld van ApplicationInsights. config met standaard eindpunten:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

_Opmerking ApplicationIdProvider is beschikbaar vanaf v 2.6.0_

### <a name="proxy-passthrough"></a>Passthrough van proxy

Passthrough van de proxy kan worden bereikt door een computer niveau of proxy op toepassings niveau te configureren.
Zie voor meer informatie het artikel van DOTNET op [DefaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Voor beeld van web. config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Kan ik beschik baarheid-webtests op een intranet server uitvoeren?

Onze [webtests](monitor-web-app-availability.md) worden uitgevoerd op de aanwezigheids punten die over de hele wereld worden gedistribueerd. Er zijn twee oplossingen:

* Firewall deur: Hiermee worden aanvragen van de server met [de lange en de lijst met webtest agents](ip-addresses.md)toegestaan.
* Schrijf uw eigen code voor het verzenden van periodieke aanvragen naar uw server vanuit uw intranet. U kunt Visual Studio-webtests voor dit doel uitvoeren. De tester kan de resultaten naar Application Insights verzenden met behulp van de API TrackAvailability ().

## <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Hoe lang duurt het voordat telemetrie is verzameld?

De meeste Application Insights gegevens hebben een latentie van minder dan vijf minuten. Sommige gegevens kunnen langer duren. meestal grotere logboek bestanden. Zie de [Application INSIGHTS Sla](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/)voor meer informatie.

## <a name="more-answers"></a>Meer antwoorden
* [Application Insights forum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: data-retention-privacy.md
[platforms]: platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md
