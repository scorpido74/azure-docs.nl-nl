---
title: 'Quickstart: Java-webapp-analyses met Azure Application Insights'
description: 'Toepassingsprestaties bewaken met Application Insights voor Java-web-apps. '
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 05/24/2019
ms.openlocfilehash: 2355cb7ab995cab3060c7a94c9e7ea344bd9e92b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984637"
---
# <a name="quickstart-get-started-with-application-insights-in-a-java-web-project"></a>Snelstart: Aan de slag met Application Insights in een Java webproject

In deze quickstart gebruikt u Application Insights om automatisch het aanvragen van instrumenten in te dienen, afhankelijkheden bij te houden en prestatiemeteritems te verzamelen, prestatieproblemen en uitzonderingen te diagnosticeren en code te schrijven om bij te houden wat gebruikers met uw app doen.

Application Insights is een uitbreidbare analyseservice voor webontwikkelaars die u helpt de prestaties en het gebruik van uw live-toepassing te begrijpen. Application Insights biedt ondersteuning voor Java-apps die in Linux, Unix of Windows worden uitgevoerd.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Een werkende Java-applicatie.

## <a name="get-an-application-insights-instrumentation-key"></a>Een Application Insights-instrumentatiesleutel ophalen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Maak in de Azure Portaleen Application Insights-resource. Stel het toepassingstype in op Java-webtoepassing.

3. Zoek de instrumentatiesleutel van de nieuwe resource. U moet deze sleutel zo dadelijk in de code van uw project plakken.

    ![Op Eigenschappen klikken in het overzicht van de nieuwe resource en de instrumentatiesleutel kopiëren](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>De Application Insights-SDK voor Java toevoegen aan uw project

*Kies uw projecttype.*

# <a name="maven"></a>[Maven](#tab/maven)

Als uw project al is ingesteld om Maven te gebruiken voor build, voegt u de volgende code samen met uw *pom.xml-bestand.*

Vervolgens vernieuwt u de projectafhankelijkheden om de binaire bestanden te downloaden.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.5.0</version>
      </dependency>
    </dependencies>
```

# <a name="gradle"></a>[Gradle](#tab/gradle)

Als uw project al is ingesteld om Gradle te gebruiken voor build, voegt u de volgende code samen met uw *build.gradle-bestand.*

Vervolgens vernieuwt u de projectafhankelijkheden om de binaire bestanden te downloaden.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

# <a name="other-types"></a>[Andere typen](#tab/other)

Download de [nieuwste versie](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) en kopieer de vereiste bestanden in uw project, waarbij eventuele vorige versies worden vervangen.

---

### <a name="questions"></a>Vragen
* *Wat is de relatie `-web-auto` `-web` tussen `-core` de , en componenten?*
  * `applicationinsights-web-auto`geeft je metrics die het aantal AANVRAGEN en reactietijden van HTTP servlet-aanvragen bijhouden, door automatisch het applicatieinzichten-filter te registreren tijdens runtime.
  * `applicationinsights-web`geeft je ook statistieken die het aantal AANVRAGEN en reactietijden van HTTP-servlet bijhouden, maar vereist handmatige registratie van het Application Insights-servlet-filter in je sollicitatie.
  * `applicationinsights-core`geeft u alleen de kale API, bijvoorbeeld als uw toepassing niet op servlet-gebaseerd is.
  
* *Hoe moet ik de SDK bijwerken naar de nieuwste versie?*
  * Als u Gradle of Maven gebruikt...
    * Werk uw buildbestand bij om de nieuwste versie op te geven.
  * Als u afhankelijkheden handmatig beheert...
    * Download de meest recente [Application Insights-SDK voor Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) en vervang de oude versie. De wijzigingen worden beschreven in de [SDK-releaseopmerkingen](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="add-an-applicationinsightsxml-file"></a>Een *bestand ApplicationInsights.xml* toevoegen
Voeg *ApplicationInsights.xml* toe aan de map resources in uw project of zorg ervoor dat deze is toegevoegd aan het pad van de implementatieklasse van uw project. Kopieer de volgende XML-code naar het bestand.

Vervang de instrumentatiesleutel door de toets die u van de Azure-portal hebt gekregen.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">

   <!-- The key from the portal: -->
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>

   <!-- HTTP request component (not required for bare API) -->
   <TelemetryModules>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
   </TelemetryModules>

   <!-- Events correlation (not required for bare API) -->
   <!-- These initializers add context data to each event -->
   <TelemetryInitializers>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
   </TelemetryInitializers>

</ApplicationInsights>
```

Optioneel kan het configuratiebestand op elke locatie zijn die toegankelijk is voor uw toepassing.  De eigenschap `-Dapplicationinsights.configurationDirectory` systeem geeft de map op die *ApplicationInsights.xml*bevat. Bijvoorbeeld: een configuratiebestand in `E:\myconfigs\appinsights\ApplicationInsights.xml` wordt geconfigureerd met eigenschap `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* De instrumentatiesleutel wordt samen met alle telemetrie-items verzonden en instrueert Application Insights om deze in de resource weer te geven.
* Het onderdeel voor de HTTP-aanvraag is optioneel. Het verzendt automatisch telemetrie over aanvragen en reactietijden naar de portal.
* Correlatie tussen gebeurtenissen is een aanvulling op het onderdeel voor de HTTP-aanvraag. Het wijst een id toe aan elk verzoek dat door de server wordt ontvangen. Vervolgens voegt het deze id als eigenschap toe aan elk item van telemetrie als de eigenschap 'Operation.Id'. Op deze manier kunt u correlaties zichtbaar maken tussen de telemetrie die aan elke aanvraag is gekoppeld. Dit doet u door een filter in te stellen in [Diagnostische gegevens doorzoeken][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Andere manieren om de instrumentatiesleutel in te stellen
De Application Insights-SDK zoekt in deze volgorde naar de sleutel:

1. Systeemeigenschap: -DAPPINSIGHTS_INSTRUMENTATIONKEY=your_ikey
2. Omgevingsvariabele: APPINSIGHTS_INSTRUMENTATIONKEY
3. Configuratiebestand: *ApplicationInsights.xml*

U kunt de instrumentatiesleutel ook [instellen in code](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>Agent toevoegen

[Installeer de Java-agent](java-agent.md) om uitgaande HTTP-oproepen, JDBC-query's, logboekregistratie van toepassingen en een betere naamvan de bewerking vast te leggen.

## <a name="run-your-application"></a>Uw toepassing uitvoeren
Voer uw app uit in de foutopsporingsmodus op uw ontwikkelcomputer of publiceer de app op uw server.

## <a name="view-your-telemetry-in-application-insights"></a>Uw telemetrie in Application Insights weergeven
Ga terug naar uw Application Insights-bron in [de Microsoft Azure-portal.](https://portal.azure.com)

Gegevens van HTTP-aanvragen worden weergegeven op de overzichtsblade. (Als dit niet het geval is, wacht u een paar seconden en klikt u vervolgens op Vernieuwen.)

![Schermafbeelding van overzichtsvoorbeeldgegevens](./media/java-get-started/overview-graphs.png)

[Meer informatie over metrische gegevens.][metrics]

Klik in een grafiek voor gedetailleerdere cumulatieve metrische gegevens.

![Deelvenster Toepassingsinzichten met grafieken](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0 operation naming provided by agent]
-->

### <a name="instance-data"></a>Gegevens van exemplaren
Klik op een specifiek aanvraagtype om de afzonderlijke exemplaren weer te geven.

![Inzoomen op een specifieke voorbeeldweergave](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analyse: krachtige querytaal
Naarmate u meer gegevens verzamelt, kunt u query's uitvoeren voor zowel het samenvoegen van gegevens als het zoeken naar afzonderlijke exemplaren.  [Analyse](../../azure-monitor/app/analytics.md) is een krachtig hulpprogramma om inzicht te krijgen in prestaties en gebruik, en om diagnoses uit te voeren.

![Voorbeeld van het hulpprogramma Analyse](./media/java-get-started/0025.png)

## <a name="install-your-app-on-the-server"></a>Uw app installeren op de server
Publiceer nu uw app op de server, geef de app vrij voor gebruik en bekijk de telemetrische gegevens die in de portal binnenkomen.

* Controleer of de firewall het verzenden van telemetrie door uw app naar deze poorten toestaat:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Als uitgaand verkeer via een firewall moet worden gerouteerd, definieert u de systeemeigenschappen `http.proxyHost` en `http.proxyPort`.

* Installeer op Windows-servers:

  * [Microsoft Visual C++ Redistributable](https://www.microsoft.com/download/details.aspx?id=40784)

    (Dit onderdeel schakelt prestatiemeteritems in.)

## <a name="azure-app-service-config-spring-boot"></a>Azure App Service config (Spring Boot)

Voor Spring Boot-apps die op Windows worden uitgevoerd, is extra configuratie vereist om op Azure App Services te worden uitgevoerd. Wijzig **web.config** en voeg de volgende configuratie toe:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>Uitzonderingen en mislukte aanvragen
Niet-afgehandelde uitzonderingen en aanvraagfouten worden automatisch verzameld door het webfilter Application Insights.

Als u gegevens wilt verzamelen over andere uitzonderingen, u [oproepen invoegen om Exception() in uw code te volgen.][apiexceptions]

## <a name="monitor-method-calls-and-external-dependencies"></a>Methodeaanroepen en externe afhankelijkheden bewaken
[Installeer de Java-agent](java-agent.md) om gespecificeerde interne methoden en oproepen via JDBC vast te leggen, inclusief timinggegevens.

En voor automatische operatienaamgeving.

## <a name="w3c-distributed-tracing"></a>W3C distributed tracing

De Application Insights Java SDK ondersteunt nu [W3C distributed tracing.](https://w3c.github.io/trace-context/)

De inkomende SDK-configuratie wordt verder uitgelegd in ons artikel over [correlatie.](correlation.md)

Uitgaande SDK-configuratie wordt gedefinieerd in het [AI-Agent.xml-bestand.](java-agent.md)

## <a name="performance-counters"></a>Prestatiemeteritems
Open **Onderzoeken**, **Statistieken**, om een reeks prestatiemeteritems te bekijken.

![Schermafbeelding van het deelvenster Statistieken met geselecteerde privébytes voor processen](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Het verzamelen van prestatiemeteritems aanpassen
Als u het verzamelen van de standaardset prestatiemeteritems wilt uitschakelen, voegt u de volgende code toe onder het hoofdknooppunt van het bestand *ApplicationInsights.xml:*

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Verzamelen van aanvullende prestatiemeteritems
U kunt opgeven dat er aanvullende prestatiemeteritems moeten worden verzameld.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>JMX-tellers (weergegeven door de virtuele Java-machine)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` - De naam die wordt weergegeven in de Application Insights-portal.
* `objectName` - De JMX-objectnaam.
* `attribute` - Het kenmerk van de JMX-objectnaam dat moet worden opgehaald
* `type`(optioneel) - Het type kenmerk van het JMX-object:
  * Standaard: een eenvoudig type, zoals int of long.
  * `composite`: de gegevens van de prestatiemeteritems hebben de indeling 'Attribute.Data'
  * `tabular`: de gegevens van de prestatiemeteritems hebben de vorm van een rij in een tabel

#### <a name="windows-performance-counters"></a>Windows-prestatiemeteritems
Elk [Windows-prestatiemeteritem](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) maakt deel uit van een categorie (net zoals een veld deel uitmaakt van een klasse). Categorieën kunnen globaal zijn, maar ook genummerde of benoemde exemplaren hebben.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName: de naam die wordt weergegeven in de Application Insights-portal.
* categorynaam: de prestatiemeteritemcategorie (prestatie-object) waaraan dit prestatiemeteritem is gekoppeld.
* counterName: de naam van het prestatiemeteritem.
* instanceName: de naam van het exemplaar van de prestatiemeteritemcategorie, of een lege tekenreeks ("") als de categorie slechts één exemplaar bevat. Als de categorienaam Process is en het prestatiemeteritem dat u wilt verzamelen, afkomstig is uit het huidige JVM-proces waarop uw app wordt uitgevoerd, specificeert u `"__SELF__"`.

### <a name="unix-performance-counters"></a>Unix-prestatiemeteritems
* [Installeer collectd met de Application Insights-invoegtoepassing](java-collectd.md) om een scala aan systeem- en netwerkgegevens op te halen.

## <a name="get-user-and-session-data"></a>Gebruikers- en sessiegegevens ophalen
U verzendt nu telemetrie vanaf de webserver. Wilt u echt een volledig inzicht in uw toepassing, dan kunt u nog meer bewakingsopties toevoegen:

* [Voeg telemetrie toe aan uw webpagina's][usage] voor het bewaken van paginaweergaven en metrische gegevens over gebruikers.
* [Stel webtests in][availability] om te controleren of de toepassing live en responsief blijft.

## <a name="send-your-own-telemetry"></a>Uw eigen telemetrie verzenden
Nu u de SDK hebt geïnstalleerd, kunt u de API gebruiken voor het verzenden van uw eigen telemetrie.

* [Houd aangepaste gebeurtenissen en metrische gegevens bij][api] voor meer informatie over wat gebruikers met uw toepassing doen.
* [Doorzoek gebeurtenissen en logboeken][diagnostic] om problemen beter te kunnen analyseren.

## <a name="availability-web-tests"></a>Webtests voor beschikbaarheid
Application Insights kan uw website regelmatig testen om te controleren of deze actief is en goed reageert.

[Meer informatie over het instellen van webtests voor beschikbaarheid.][availability]

## <a name="questions-problems"></a>Vragen? Problemen?
[Problemen met Java oplossen](java-troubleshoot.md)

## <a name="next-steps"></a>Volgende stappen
* [Afhankelijkheidsaanroepen bewaken](java-agent.md)
* [Unix-prestatiemeteritems bewaken](java-collectd.md)
* Voeg [bewaking toe aan uw webpagina's](javascript.md) om de laadtijden, AJAX-aanroepen en browseruitzonderingen te bewaken.
* Typ [aangepaste telemetrie](../../azure-monitor/app/api-custom-events-metrics.md) om het gebruik in de browser of op de server bij te houden.
* [Analytics gebruiken](../../azure-monitor/app/analytics.md) voor krachtige query's via telemetrie vanuit uw app
* Voor meer informatie gaat u naar [Azure voor Java-ontwikkelaars](/java/azure).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
