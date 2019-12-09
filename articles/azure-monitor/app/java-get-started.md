---
title: Analyse van Java-Web-apps met Azure-toepassing Insights
description: 'Toepassingsprestaties bewaken met Application Insights voor Java-web-apps. '
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 05/24/2019
ms.openlocfilehash: 0686cea590ca26096b443dba21b05dc3335c7add
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927261"
---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Aan de slag met Application Insights in een Java-webproject

[Application Insights](https://azure.microsoft.com/services/application-insights/) is een uitbreidbare analyseservice voor webontwikkelaars die u helpt de prestaties en het gebruik van uw live-toepassing te begrijpen. Gebruik het om de [aanvraag automatisch te instrumenteren, afhankelijkheden bij te houden en prestatie meter items te verzamelen](auto-collect-dependencies.md#java), prestatie problemen en uitzonde ringen te diagnosticeren en [code te schrijven][api] om te volgen wat gebruikers met uw app doen. 

![Scherm opname van voorbeeld gegevens van overzicht](./media/java-get-started/overview-graphs.png)

Application Insights biedt ondersteuning voor Java-apps die in Linux, Unix of Windows worden uitgevoerd.

U hebt de volgende zaken nodig:

* Java 7 of hoger
* Een abonnement op [Microsoft Azure](https://azure.microsoft.com/).

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. een Application Insights instrumentatie sleutel ophalen
1. Meld u aan bij de [Microsoft Azure Portal](https://portal.azure.com).
2. Maak een Application Insights-resource. Stel het toepassingstype in op Java-webtoepassing.

3. Zoek de instrumentatiesleutel van de nieuwe resource. U moet deze sleutel zo dadelijk in de code van uw project plakken.

    ![Op Eigenschappen klikken in het overzicht van de nieuwe resource en de instrumentatiesleutel kopiëren](./media/java-get-started/instrumentation-key-001.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. Voeg de Application Insights SDK voor Java toe aan uw project
*Kies de juiste methode voor uw project.*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Als u Maven gebruikt... <a name="maven-setup" />
Als uw project al is ingesteld om voor de build Maven te gebruiken, voegt u de volgende code in uw pom.xml-bestand in.

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

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Als u Gradle gebruikt... <a name="gradle-setup" />
Als uw project al is ingesteld om voor de build Gradle te gebruiken, voegt u de volgende code in uw build.gradle-bestand in.

Vervolgens vernieuwt u de projectafhankelijkheden om de binaire bestanden te downloaden.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>Als u afhankelijkheden handmatig beheert...
Download de [nieuwste versie](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) en kopieer de vereiste bestanden in uw project, waarbij eventuele vorige versies worden vervangen.

### <a name="questions"></a>Vragen...
* *Wat is de relatie tussen de `-web-auto`, `-web` en `-core` onderdelen?*
  * `applicationinsights-web-auto` geeft u metrische gegevens die het aantal HTTP servlet-aanvragen en-reactie tijden bijhouden door het Application Insights servlet-filter tijdens runtime automatisch te registreren.
  * `applicationinsights-web` biedt ook metrische gegevens die het aantal servlet en reactie tijden voor HTTP-aanvragen bijhouden, maar hiervoor hand matige registratie van het Application Insights servlet-filter in uw toepassing.
  * `applicationinsights-core` geeft u alleen de bare API, bijvoorbeeld als uw toepassing niet op servlet is gebaseerd.
  
* *Hoe moet ik de SDK bijwerken naar de nieuwste versie?*
  * Als u Gradle of Maven gebruikt...
    * Werk uw build-bestand bij om de meest recente versie op te geven.
  * Als u afhankelijkheden handmatig beheert...
    * Download de meest recente [Application Insights-SDK voor Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) en vervang de oude versie. De wijzigingen worden beschreven in de [SDK-releaseopmerkingen](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="3-add-an-applicationinsightsxml-file"></a>3. een ApplicationInsights. XML-bestand toevoegen
Voeg ApplicationInsights.xml toe aan de resourcesmap in uw project of plaats het in het implementatieklassepad van uw project. Kopieer de volgende XML-code naar het bestand.

Vervang de instrumentatiesleutel die u in de Azure Portal hebt verkregen.

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

Het configuratiebestand kan ook worden ondergebracht op een locatie die voor uw toepassing toegankelijk is.  Met systeemeigenschap `-Dapplicationinsights.configurationDirectory` wordt de map opgegeven die ApplicationInsights.xml bevat. Bijvoorbeeld: een configuratiebestand in `E:\myconfigs\appinsights\ApplicationInsights.xml` wordt geconfigureerd met eigenschap `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* De instrumentatiesleutel wordt samen met alle telemetrie-items verzonden en instrueert Application Insights om deze in de resource weer te geven.
* Het onderdeel voor de HTTP-aanvraag is optioneel. Het verzendt automatisch telemetrie over aanvragen en reactietijden naar de portal.
* Correlatie tussen gebeurtenissen is een aanvulling op het onderdeel voor de HTTP-aanvraag. Deze aanvulling wijst een id toe aan elke aanvraag die door de server wordt ontvangen en voegt deze id als de eigenschap 'Operation.Id' toe aan elk telemetrie-item. Hiermee kunt u de telemetrie die aan elke aanvraag is gekoppeld, correleren door een filter in [Diagnostische Zoek opdrachten][diagnostic]in te stellen.

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Andere manieren om de instrumentatiesleutel in te stellen
De Application Insights-SDK zoekt in deze volgorde naar de sleutel:

1. Systeem eigenschap:-DAPPINSIGHTS_INSTRUMENTATIONKEY = your_ikey
2. Omgevings variabele: APPINSIGHTS_INSTRUMENTATIONKEY
3. Configuratiebestand: ApplicationInsights.xml

U kunt de instrumentatiesleutel ook [instellen in code](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="4-add-agent"></a>4. agent toevoegen

[Installeer de Java-Agent voor het](java-agent.md) vastleggen van uitgaande HTTP-aanroepen, JDBC-query's, toepassings logboeken en betere bewerkings namen.

## <a name="5-run-your-application"></a>5. uw toepassing uitvoeren
Voer uw app uit in de foutopsporingsmodus op uw ontwikkelcomputer of publiceer de app op uw server.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. uw telemetrie in Application Insights weer geven
Ga terug naar uw Application Insights-resource in de [Microsoft Azure Portal](https://portal.azure.com).

Gegevens van HTTP-aanvragen worden weergegeven op de overzichtsblade. (Als dit niet het geval is, wacht u een paar seconden en klikt u vervolgens op Vernieuwen.)

![Scherm opname van voorbeeld gegevens van overzicht](./media/java-get-started/overview-graphs.png)

[Meer informatie over metrische gegevens.][metrics]

Klik in een grafiek voor gedetailleerdere cumulatieve metrische gegevens.

![Deel venster met Application Insights fouten met grafieken](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0 operation naming provided by agent]
-->

### <a name="instance-data"></a>Gegevens van exemplaren
Klik op een specifiek aanvraagtype om de afzonderlijke exemplaren weer te geven.

![Inzoomen op een specifieke voorbeeld weergave](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analyse: krachtige querytaal
Naarmate u meer gegevens verzamelt, kunt u query's uitvoeren voor zowel het samenvoegen van gegevens als het zoeken naar afzonderlijke exemplaren.  [Analyse](../../azure-monitor/app/analytics.md) is een krachtig hulpprogramma om inzicht te krijgen in prestaties en gebruik, en om diagnoses uit te voeren.

![Voorbeeld van het hulpprogramma Analyse](./media/java-get-started/0025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Installeer uw app op de server
Publiceer nu uw app op de server, geef de app vrij voor gebruik en bekijk de telemetrische gegevens die in de portal binnenkomen.

* Controleer of de firewall het verzenden van telemetrie door uw app naar deze poorten toestaat:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Als uitgaand verkeer via een firewall moet worden gerouteerd, definieert u de systeemeigenschappen `http.proxyHost` en `http.proxyPort`.

* Installeer op Windows-servers:

  * [Microsoft Visual C++ Redistributable](https://www.microsoft.com/download/details.aspx?id=40784)

    (Dit onderdeel schakelt prestatiemeteritems in.)

## <a name="azure-app-service-config-spring-boot"></a>Azure App Service config (veer boot)

Voor Spring boot-apps die worden uitgevoerd in Windows is aanvullende configuratie vereist om te worden uitgevoerd op Azure-app Services. Wijzig **Web. config** en voeg het volgende toe:

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
Niet-verwerkte uitzonde ringen en mislukte aanvragen worden automatisch door het webfilter van Application Insights verzameld.

Als u gegevens over andere uitzonde ringen wilt verzamelen, kunt u [aanroepen naar trackException () in uw code invoegen][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Methodeaanroepen en externe afhankelijkheden bewaken
[Installeer de Java-agent](java-agent.md) om gespecificeerde interne methoden en oproepen via JDBC vast te leggen, inclusief timinggegevens.

En voor automatische bewerkings naam.

## <a name="w3c-distributed-tracing"></a>Gedistribueerde W3C-tracering

De Application Insights Java SDK ondersteunt nu [W3C-gedistribueerde tracering](https://w3c.github.io/trace-context/).

De configuratie van de inkomende SDK wordt verder uitgelegd in ons artikel over [correlatie](correlation.md#telemetry-correlation-in-the-java-sdk).

De uitgaande SDK-configuratie wordt gedefinieerd in het bestand [AI-agent. XML](java-agent.md) .

## <a name="performance-counters"></a>Prestatiemeteritems
Open **onderzoek**, **metrische gegevens**om een aantal prestatie meter items weer te geven.

![Scherm afbeelding van het deel venster metrische gegevens met het proces eigen bytes geselecteerd](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Het verzamelen van prestatiemeteritems aanpassen
Als u het verzamelen van de standaardset prestatiemeteritems wilt uitschakelen, voegt u de volgende code toe onder het hoofdknooppunt van het ApplicationInsights.xml-bestand:

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
* `type` (optioneel) - Het type kenmerk van het JMX-object:
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

* [Voeg telemetrie toe aan uw webpagina's][usage] om pagina weergaven en metrische gegevens over gebruikers te controleren.
* [Stel webtests][availability] in om ervoor te zorgen dat uw toepassing Live en responsief blijft.

## <a name="send-your-own-telemetry"></a>Uw eigen telemetrie verzenden
Nu u de SDK hebt geïnstalleerd, kunt u de API gebruiken voor het verzenden van uw eigen telemetrie.

* [Houd aangepaste gebeurtenissen en metrische gegevens][api] bij om te zien wat gebruikers met uw toepassing doen.
* [Zoeken naar gebeurtenissen en logboeken][diagnostic] om problemen te onderzoeken.

## <a name="availability-web-tests"></a>Beschibaarheidswebtests
Application Insights kan uw website regelmatig testen om te controleren of deze actief is en goed reageert.

[Meer informatie over het instellen van Beschik baarheid van webtesten.][availability]

## <a name="questions-problems"></a>Vragen? Problemen?
[Problemen met Java oplossen](java-troubleshoot.md)

## <a name="next-steps"></a>Volgende stappen
* [Afhankelijkheidsaanroepen bewaken](java-agent.md)
* [Unix-prestatiemeteritems bewaken](java-collectd.md)
* Voeg [bewaking toe aan uw webpagina's](javascript.md) om de laadtijden, AJAX-aanroepen en browseruitzonderingen te bewaken.
* Typ [aangepaste telemetrie](../../azure-monitor/app/api-custom-events-metrics.md) om het gebruik in de browser of op de server bij te houden.
* Gebruik [Analytics](../../azure-monitor/app/analytics.md) om vanuit uw app krachtige query's voor telemetrie uit te voeren
* Voor meer informatie gaat u naar [Azure voor Java-ontwikkelaars](/java/azure).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
