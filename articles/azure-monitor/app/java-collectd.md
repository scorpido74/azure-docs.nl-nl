---
title: De prestaties van java-webapps op Linux controleren - Azure | Microsoft Documenten
description: Uitgebreide applicatieprestatiebewaking van uw Java-website met de CollectD-plug-in voor Application Insights.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 62a723dad7e9f6c2bfdabde159968d507d2d5d41
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537522"
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>verzameld: Linux performance metrics in Application Insights


Om linux systeemprestatiestatistieken in [Application Insights](../../azure-monitor/app/app-insights-overview.md)te verkennen, [installeerje verzameld](https://collectd.org/), samen met de Application Insights-plug-in. Deze open-source oplossing verzamelt verschillende systeem- en netwerkstatistieken.

Meestal gebruikt u verzameld als u [uw Java-webservice][java]al hebt geinstrumenteerd met Application Insights. Het geeft u meer gegevens om u te helpen de prestaties van uw app te verbeteren of problemen te diagnosticeren. 

## <a name="get-your-instrumentation-key"></a>Haal uw instrumentatiesleutel
Open in de [Microsoft Azure-portal](https://portal.azure.com)de [toepassingsbron waar](../../azure-monitor/app/app-insights-overview.md) u de gegevens wilt weergeven. (Of [een nieuwe resource maken](../../azure-monitor/app/create-new-resource.md ).)

Neem een kopie van de instrumentatiesleutel, die de bron identificeert.

![Blader door alles, open uw resource en selecteer en kopieer de instrumentatiesleutel in de vervolgkeuzelijst Essentials](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>Collectd installeren en de plug-in
Op uw Linux-servermachines:

1. Installeer [verzamelde](https://collectd.org/) versie 5.4.0 of hoger.
2. Download de [Application Insights collectd writer plugin](https://github.com/microsoft/ApplicationInsights-Java/tree/master/collectd/src/main/java/com/microsoft/applicationinsights/collectd/internal). Let op het versienummer.
3. Kopieer de plugin `/usr/share/collectd/java`JAR naar .
4. Bewerken `/etc/collectd/collectd.conf`:
   * Controleer of [de Java-plug-in](https://collectd.org/wiki/index.php/Plugin:Java) is ingeschakeld.
   * Werk de JVMArg voor de java.class.path bij om de volgende JAR op te nemen. Werk het versienummer bij dat overeenkomt met het nummer dat u hebt gedownload:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Voeg dit fragment toe met de instrumentatiesleutel van uw resource:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Hier vindt u een deel van een voorbeeldconfiguratiebestand:

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

Andere [verzamelde plug-ins](https://collectd.org/wiki/index.php/Table_of_Plugins)configureren, die verschillende gegevens uit verschillende bronnen kunnen verzamelen.

Opnieuw starten verzameld volgens de [handleiding](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Bekijk de gegevens in Application Insights
Open in uw toepassingsstatistiekenbron [Statistieken en voeg grafieken toe][metrics]en selecteer de statistieken die u wilt zien in de categorie Aangepast.

Standaard worden de statistieken samengevoegd over alle hostmachines waaruit de statistieken zijn verzameld. Als u de statistieken per host wilt weergeven, schakelt u in het blad Grafiekdetails groeperen in en kiest u voor groeperen op CollectD-Host.

## <a name="to-exclude-upload-of-specific-statistics"></a>Upload van specifieke statistieken uitsluiten
Standaard stuurt de Application Insights-plug-in alle gegevens die zijn verzameld door alle ingeschakelde verzamelde 'lees'-plug-ins. 

Ga als u gegevens uitvan specifieke plug-ins of gegevensbronnen:

* Bewerk het configuratiebestand. 
* Voeg `<Plugin ApplicationInsightsWriter>`in , voeg richtlijnlijnen als volgt toe:

| Richtlijn | Effect |
| --- | --- |
| `Exclude disk` |Alle gegevens die `disk` door de plug-in worden verzameld uitsluiten |
| `Exclude disk:read,write` |Sluit de `read` genoemde `write` bronnen `disk` en van de plug-in uit. |

Aparte richtlijnen met een nieuwe lijn.

## <a name="problems"></a>Problemen?
*Ik zie geen gegevens in het portaal*

* Open [Zoeken][diagnostic] om te zien of de ruwe gebeurtenissen zijn aangekomen. Soms duurt het langer voordat ze worden weergegeven in metrics explorer.
* Mogelijk moet u [firewalluitzonderingen instellen voor uitgaande gegevens](../../azure-monitor/app/ip-addresses.md)
* Tracering inschakelen in de Plug-in Application Insights. Voeg deze `<Plugin ApplicationInsightsWriter>`regel toe binnen :
  * `SDKLogger true`
* Open een terminal en begin verzameld in de verbose-modus, om eventuele problemen te zien die het rapporteert:
  * `sudo collectd -f`

## <a name="known-issue"></a>Bekend probleem

De Application Insights Write-plug-in is niet compatibel met bepaalde Lees-ins. Sommige plug-ins sturen soms "NaN" waar de Application Insights-plugin een floating-point-nummer verwacht.

Symptoom: Het verzamelde logboek toont fouten die "AI: ... Syntaxierfout: onverwacht token N".

Tijdelijke oplossing: Sluit gegevens uit die worden verzameld door de schrijfplug-ins. 

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md


