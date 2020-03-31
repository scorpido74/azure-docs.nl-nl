---
title: Azure Diagnostics-gegevens naar toepassingsinzichten verzenden
description: Werk de openbare configuratie van Azure Diagnostics bij om gegevens naar Application Insights te verzenden.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/19/2016
ms.openlocfilehash: 80d971abd248ca8253a374b488c693ea9aa2ea3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672324"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Diagnostische gegevens over Cloud Service, Virtual Machine of Service Fabric naar Application Insights verzenden
Cloudservices, virtuele machines, virtuele machineschaalsets en servicestructuur gebruiken allemaal de Azure Diagnostics-extensie om gegevens te verzamelen.  Azure-diagnose verzendt gegevens naar Azure Storage-tabellen.  U echter ook alle of een subset van de gegevens naar andere locaties leiden met Azure Diagnostics-extensie 1.5 of hoger.

In dit artikel wordt beschreven hoe u gegevens van de Azure Diagnostics-extensie naar Application Insights verzendt.

## <a name="diagnostics-configuration-explained"></a>Diagnostische configuratie uitgelegd
De Azure diagnostics extension 1.5 introduceerde sinks, die extra locaties zijn waar u diagnostische gegevens verzenden.

Voorbeeldconfiguratie van een sink voor Application Insights:

```XML
<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "ApplicationInsights",
            "ApplicationInsights": "{Insert InstrumentationKey}",
            "Channels": {
                "Channel": [
                    {
                        "logLevel": "Error",
                        "name": "MyTopDiagData"
                    },
                    {
                        "logLevel": "Error",
                        "name": "MyLogData"
                    }
                ]
            }
        }
    ]
}
```
- Het kenmerk **Sink** *Sink-naam* is een tekenreekswaarde die de gootsteen op unieke wijze identificeert.

- Het element **ApplicationInsights** geeft de instrumentatiesleutel op van de bron Toepassingsinzichten waarin de Azure-diagnostische gegevens worden verzonden.
    - Zie [Een nieuwe Application Insights-bron maken](../../azure-monitor/app/create-new-resource.md ) voor meer informatie over het maken van een resource en het verkrijgen van de instrumentatiesleutel als u geen bestaande Application Insights-bron hebt.
    - Als u een Cloud Service ontwikkelt met Azure SDK 2.8 en hoger, wordt deze instrumentatiesleutel automatisch ingevuld. De waarde is gebaseerd op de **APPINSIGHTS_INSTRUMENTATIONKEY** serviceconfiguratie-instelling bij het verpakken van het Cloud Service-project. Zie [Toepassingsinzichten gebruiken met CloudServices](../../azure-monitor/app/cloudservices.md).

- Het element **Kanalen** bevat een of meer **kanaalelementen.**
    - Het *naamkenmerk* verwijst op unieke wijze naar dat kanaal.
    - Met het kenmerk *loglevel* u het logboekniveau opgeven dat het kanaal toestaat. De beschikbare logniveaus in volgorde van de meeste tot minste informatie zijn:
        - Verbose
        - Informatie
        - Waarschuwing
        - Fout
        - Kritiek

Een kanaal werkt als een filter en stelt u in staat om specifieke logboekniveaus te selecteren die u naar de doelgootsteen wilt verzenden. U bijvoorbeeld uitgebreide logboeken verzamelen en deze naar de opslag verzenden, maar alleen fouten naar de gootsteen verzenden.

De volgende afbeelding toont deze relatie.

![Openbare configuratie van diagnostische gegevens](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

In de volgende afbeelding worden de configuratiewaarden en de manier waarop deze werken samengevat. U meerdere sinks in de configuratie opnemen op verschillende niveaus in de hiërarchie. De gootsteen op het hoogste niveau fungeert als een globale instelling en de instelling die is opgegeven bij het afzonderlijke element fungeert als een overschrijving naar die globale instelling.

![Diagnostische sinks configuratie met Application Insights](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Voorbeeld van volledige sinkconfiguratie
Hier is een compleet voorbeeld van het openbare configuratiebestand dat
1. stuurt alle fouten naar Application Insights (opgegeven op het knooppunt **DiagnosticMonitorConfiguration)**
2. stuurt ook Verbose-niveaulogboeken voor de toepassingslogboeken (opgegeven op het knooppunt **Logboeken).**

```XML
<WadCfg>
  <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
    <DiagnosticInfrastructureLogs />
    <PerformanceCounters>
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
    </PerformanceCounters>
    <WindowsEventLog scheduledTransferPeriod="PT1M">
      <DataSource name="Application!*" />
    </WindowsEventLog>
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
  </DiagnosticMonitorConfiguration>

<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
  </SinksConfig>
</WadCfg>
```
```JSON
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
        "overallQuotaInMB": 4096,
        "sinks": "ApplicationInsights.MyTopDiagData", "_comment": "All info below sent to this channel",
        "DiagnosticInfrastructureLogs": {
        },
        "PerformanceCounters": {
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                },
                {
                    "counterSpecifier": "\\Memory\\Available MBytes",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "DataSource": [
                {
                    "name": "Application!*"
                }
            ]
        },
        "Logs": {
            "scheduledTransferPeriod": "PT1M",
            "scheduledTransferLogLevelFilter": "Verbose",
            "sinks": "ApplicationInsights.MyLogData", "_comment": "This specific info sent to this channel"
        }
    },
    "SinksConfig": {
        "Sink": [
            {
                "name": "ApplicationInsights",
                "ApplicationInsights": "{Insert InstrumentationKey}",
                "Channels": {
                    "Channel": [
                        {
                            "logLevel": "Error",
                            "name": "MyTopDiagData"
                        },
                        {
                            "logLevel": "Verbose",
                            "name": "MyLogData"
                        }
                    ]
                }
            }
        ]
    }
}
```
In de vorige configuratie hebben de volgende regels de volgende betekenissen:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Alle gegevens verzenden die worden verzameld door Azure-diagnose

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Alleen foutlogboeken verzenden naar de sink Application Insights

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Verbose-toepassingslogboeken naar Application Insights verzenden

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyLogData",
}
```

## <a name="limitations"></a>Beperkingen

- **Kanalen loggen alleen type en niet prestatiemeteritems.** Als u een kanaal opgeeft met een prestatiemeterelement, wordt dit genegeerd.
- **Het logboekniveau voor een kanaal mag het logboekniveau niet overschrijden voor wat wordt verzameld door Azure-diagnose.** U bijvoorbeeld geen fouten in het logboeken verzamelen en probeert Verbose-logboeken naar de gootsteen Voor toepassingsinzicht te verzenden. Het *kenmerk scheduledTransferLogLevelFilter* moet altijd gelijke of meer logboeken verzamelen dan de logboeken die u naar een gootsteen probeert te verzenden.
- **U blobgegevens die zijn verzameld door azure-diagnostische extensie niet verzenden naar Application Insights.** Bijvoorbeeld alles wat is opgegeven onder het knooppunt *Mappen.* Voor Crash Dumps wordt de werkelijke crashdump verzonden naar blobopslag en wordt alleen een melding verzonden dat de crashdump is gegenereerd naar Application Insights.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [weergeven van uw Azure-diagnostische gegevens](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices) in Application Insights.
* Gebruik [PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md) om de Azure-diagnostische extensie voor uw toepassing in te schakelen.
* [Visual Studio gebruiken](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) om de Azure-diagnostische extensie voor uw toepassing in te schakelen

