---
title: Problemen met toepassingsinzichten in een Java-webproject oplossen
description: Handleiding voor probleemoplossing - live Java-apps monitoren met Application Insights.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 04e98938bc5dd17816ae873f122073212275a414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657177"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Probleemoplossing voor en antwoorden op vragen over Application Insights voor Java
Vragen of problemen met [Azure Application Insights in Java][java]? Hier volgen enkele tips.

## <a name="build-errors"></a>Fouten maken
**In Eclipse of Intellij Idea, bij het toevoegen van de Application Insights SDK via Maven of Gradle, krijg ik build of checksum validatie fouten.**

* Als het `<version>` afhankelijkheidselement een patroon met jokertekens gebruikt (bijvoorbeeld `<version>[2.0,)</version>` (Maven) `version:'2.0.+'`of (Gradle), probeert `2.0.1`u in plaats daarvan een specifieke versie op te geven zoals . Zie de [release notes](https://github.com/Microsoft/ApplicationInsights-Java/releases) voor de nieuwste versie.

## <a name="no-data"></a>Geen gegevens
**Ik heb Application Insights met succes toegevoegd en mijn app uitgevoerd, maar ik heb nog nooit gegevens in de portal gezien.**

* Wacht een minuut en klik op Vernieuwen. De grafieken vernieuwen zichzelf regelmatig, maar u ook handmatig vernieuwen. Het vernieuwingsinterval is afhankelijk van het tijdsbereik van de grafiek.
* Controleer of u een instrumentatiesleutel hebt gedefinieerd in het bestand ApplicationInsights.xml (in de map resources in uw project) of geconfigureerd als omgevingsvariabele.
* Controleer of er `<DisableTelemetry>true</DisableTelemetry>` geen knooppunt in het xml-bestand is.
* In uw firewall moet u mogelijk TCP-poorten 80 en 443 openen voor uitgaand verkeer naar dc.services.visualstudio.com. Bekijk de [volledige lijst met firewalluitzonderingen](../../azure-monitor/app/ip-addresses.md)
* Bekijk in het startbord van Microsoft Azure de servicestatuskaart. Als er een aantal waarschuwingsaanwijzingen zijn, wacht u tot ze zijn teruggekeerd naar OK en sluit en opent u het toepassingsblad van Application Insights opnieuw.
* [Schakel logboekregistratie](#debug-data-from-the-sdk) in `<SDKLogger />` door een element onder het hoofdknooppunt toe te voegen in het bestand ApplicationInsights.xml (in de bronnenmap in uw project) en controleer op vermeldingen die voorafgingen aan AI: INFO/WARN/ERROR voor verdachte logboeken. 
* Zorg ervoor dat het juiste ApplicationInsights.xml-bestand is geladen door de Java SDK, door te kijken naar de uitvoerberichten van de console voor een instructie 'Configuratiebestand is met succes gevonden'.
* Als het config-bestand niet wordt gevonden, controleert u de uitvoerberichten om te zien waar het config-bestand wordt gezocht en controleert u of de ApplicationInsights.xml zich in een van die zoeklocaties bevindt. Als vuistregel u het config-bestand in de buurt van de Application Insights SDK JARs plaatsen. Bijvoorbeeld: in Tomcat betekent dit de map WEB-INF/classes. Tijdens de ontwikkeling u ApplicationInsights.xml plaatsen in de bronnenmap van uw webproject.
* Kijk ook op [GitHub problemen pagina](https://github.com/Microsoft/ApplicationInsights-Java/issues) voor bekende problemen met de SDK.
* Zorg ervoor dat u dezelfde versie van Application Insights-kern-, web-, agent- en logboekregistratie-appenders gebruikt om problemen met het conflict in de versie te voorkomen.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Ik gebruikte om gegevens te zien, maar het is gestopt
* Controleer de [status blog](https://blogs.msdn.com/b/applicationinsights-status/).
* Heeft u uw maandelijkse quotum van gegevenspunten bereikt? Open Instellingen/Quota en Prijzen om erachter te komen. Als dat het geval is, u uw abonnement upgraden of betalen voor extra capaciteit. Zie het [prijsschema](https://azure.microsoft.com/pricing/details/application-insights/).
* Heb je onlangs een upgrade van uw SDK? Zorg ervoor dat er alleen unieke SDK-potten in de projectmap aanwezig zijn. Er mogen geen twee verschillende versies van SDK aanwezig zijn.
* Bent u op zoek naar de juiste AI-bron? Gelieve de iKey van uw toepassing af te koppelen aan de bron waar u telemetrie verwacht. Ze zouden hetzelfde moeten zijn.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Ik zie niet alle gegevens die ik verwacht
* Open de pagina Gebruik en geschatte kosten en controleer of [de bemonstering](../../azure-monitor/app/sampling.md) in werking is. (100% transmissie betekent dat de bemonstering niet in werking is.) De Application Insights-service kan worden ingesteld om slechts een fractie van de telemetrie die vanuit uw app binnenkomt, te accepteren. Zo blijft u binnen uw maandelijkse quotum van telemetrie.
* Is SDK Sampling ingeschakeld? Zo ja, dan worden de gegevens bemonsterd tegen de snelheid die is opgegeven voor alle toepasselijke typen.
* Bent u het uitvoeren van een oudere versie van Java SDK? Vanaf versie 2.0.1 hebben we het fouttolerantiemechanisme geïntroduceerd om intermitterende netwerk- en backendstoringen en gegevenspersistentie op lokale stations te verwerken.
* Wordt u gewurgd door overmatige telemetrie? Als u INFO-logboekregistratie inschakelt, ziet u een logboekbericht 'App is throttled'. Onze huidige limiet is 32k telemetrie items /second.

### <a name="java-agent-cannot-capture-dependency-data"></a>Java-agent kan geen afhankelijkheidsgegevens vastleggen
* Hebt u Java-agent geconfigureerd door [Java-agent configureren](java-agent.md) te volgen?
* Zorg ervoor dat zowel de java-agent pot als het AI-Agent.xml-bestand in dezelfde map worden geplaatst.
* Zorg ervoor dat de afhankelijkheid die u automatisch probeert te verzamelen, wordt ondersteund voor het automatisch verzamelen. Momenteel ondersteunen we alleen MySQL, MsSQL, Oracle DB en Azure Cache voor afhankelijkheidsverzameling van Redis.

## <a name="no-usage-data"></a>Geen gebruiksgegevens
**Ik zie gegevens over verzoeken en reactietijden, maar geen paginaweergave, browser of gebruikersgegevens.**

U hebt uw app zo ingesteld dat u telemetrie vanaf de server verzendt. Nu is uw volgende stap het [instellen van uw webpagina's om telemetrie te verzenden vanuit de webbrowser.][usage]

Als uw client een app is in een [telefoon of een ander apparaat,][platforms]u vanaf daar telemetrie verzenden.

Gebruik dezelfde instrumentatiesleutel om zowel uw client- als servertelemetrie in te stellen. De gegevens worden weergegeven in dezelfde Application Insights-bron en u gebeurtenissen van client en server correleren.


## <a name="disabling-telemetry"></a>Telemetrie uitschakelen
**Hoe kan ik het verzamelen van telemetrie uitschakelen?**

In code:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Of**

ApplicationInsights.xml bijwerken (in de map resources in uw project). Voeg het volgende toe onder het hoofdknooppunt:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Met de XML-methode moet u de toepassing opnieuw starten wanneer u de waarde wijzigt.

## <a name="changing-the-target"></a>Het doel wijzigen
**Hoe kan ik wijzigen naar welke Azure-bron mijn project gegevens verzendt?**

* [Download de instrumentatiesleutel van de nieuwe bron.][java]
* Als u Application Insights aan uw project hebt toegevoegd met de Azure Toolkit voor Eclipse, klikt u met de rechtermuisknop op uw webproject, selecteert u **Azure**, **Configureer toepassingsinzichten**en wijzigt u de sleutel.
* Als u de instrumentatiesleutel als omgevingsvariabele had geconfigureerd, u de waarde van de omgevingsvariabele bijwerken met nieuwe iKey.
* Werk anders de sleutel in ApplicationInsights.xml bij in de map resources in uw project.

## <a name="debug-data-from-the-sdk"></a>Foutopsporingsgegevens van de SDK

**Hoe kom ik erachter wat de SDK doet?**

Als u meer informatie wilt krijgen over `<SDKLogger/>` wat er in de API gebeurt, voegt u onder het hoofdknooppunt van het configuratiebestand ApplicationInsights.xml toe.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

U de logger ook instrueren om naar een bestand uit te geven:

```XML
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Spring Boot Starter

Als u SDK-logboekregistratie met Spring Boot Apps wilt inschakelen `application.properties` met de Start voor het voorjaar van toepassing- inzicht, voegt u het volgende toe aan het bestand:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

of om af te drukken op standaardfout:

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Java-agent

Als u het bestand [AI-Agent.xml](java-agent.md)wilt inschakelen voor het bijwerken van JVM-agentlogging:

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>Java-opdrachtregeleigenschappen
_Sinds versie 2.4.0_

Logboekregistratie inschakelen met opties voor opdrachtregel, zonder configuratiebestanden te wijzigen:

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

of om af te drukken op standaardfout:

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>Het Azure-startscherm
**Ik ben op zoek naar [de Azure portal](https://portal.azure.com). Zegt de kaart me iets over mijn app?**

Nee, het toont de status van Azure-servers over de hele wereld.

*Op het Azure-startscherm (startscherm) hoe vind ik gegevens over mijn app?*

Als u [uw app voor Toepassingsstatistieken instelt,][java]klikt u op Bladeren, selecteert u Toepassingsinzichten en selecteert u de app-bron die u voor uw app hebt gemaakt. Om er in de toekomst sneller te komen, u uw app vastmaken aan het startbord.

## <a name="intranet-servers"></a>Intranetservers
**Kan ik een server op mijn intranet controleren?**

Ja, mits uw server telemetrie kan verzenden naar de Application Insights-portal via het openbare internet.

In uw firewall moet u mogelijk TCP-poorten 80 en 443 openen voor uitgaand verkeer naar dc.services.visualstudio.com en f5.services.visualstudio.com.

## <a name="data-retention"></a>Bewaartijd van gegevens
**Hoe lang worden gegevens bewaard in de portal? Is het veilig?**

Zie [Gegevensbewaring en privacy][data].

## <a name="debug-logging"></a>Foutopsporingslogboeken
Toepassingen insights `org.apache.http`gebruikt . Dit wordt verplaatst binnen Application Insights kernpotten `com.microsoft.applicationinsights.core.dependencies.http`onder de naamruimte. Hierdoor kunnen Application Insights scenario's verwerken `org.apache.http` waarin verschillende versies van hetzelfde bestaan in één codebasis.

>[!NOTE]
>Als u delogboekregistratie van DEBUG-niveau inschakelt voor alle naamruimten `org.apache.http` in de `com.microsoft.applicationinsights.core.dependencies.http`app, wordt deze gehonoreerd door alle uitvoerende modules, inclusief een andere naam als . Application Insights kan geen filtering toepassen voor deze oproepen omdat de logcall wordt uitgevoerd door de Apache-bibliotheek. Debug-niveaulogboekregistratie produceert een aanzienlijke hoeveelheid logboekgegevens en wordt niet aanbevolen voor live productie-exemplaren.


## <a name="next-steps"></a>Volgende stappen
**Ik heb Application Insights ingesteld voor mijn Java-server-app. Wat kan ik nog meer doen?**

* [De beschikbaarheid van uw webpagina's controleren][availability]
* [Het gebruik van webpagina's controleren][usage]
* [Gebruik bijhouden en problemen in uw apparaat-apps diagnosticeren][platforms]
* [Code schrijven om het gebruik van uw app bij te houden][track]
* [Diagnostische logboeken vastleggen][javalogs]

## <a name="get-help"></a>Help opvragen
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Een probleem indienen op GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md

