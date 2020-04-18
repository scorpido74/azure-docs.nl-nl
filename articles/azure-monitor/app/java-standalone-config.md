---
title: Overal Java-toepassingen bewaken - Azure Monitor Application Insights
description: Codeless application performance monitoring voor Java-toepassingen die in elke omgeving worden uitgevoerd zonder de app in te voeren. Zoek de hoofdoorzaak van de problemen d met behulp van gedistribueerde tracering en toepassingskaart.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 5d930d349a2ab1efbd7a61904874bf6bdb411889
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641886"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Configuratieopties - Java standalone agent voor Azure Monitor Application Insights



## <a name="connection-string-and-role-name"></a>Verbindingstekenreeks en rolnaam

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

De verbindingstekenreeks is vereist en de rolnaam is belangrijk wanneer u gegevens van verschillende toepassingen naar dezelfde Application Insights-bron verzendt.

Hieronder vindt u meer details en extra configuratieopties voor meer informatie.

## <a name="configuration-file-path"></a>Bestandspad voor configuratie

Standaard verwacht Application Insights Java 3.0 Preview dat `ApplicationInsights.json`het configuratiebestand een naam `applicationinsights-agent-3.0.0-PREVIEW.jar`krijgt en zich in dezelfde map bevindt als .

U uw eigen configuratiebestandspad opgeven met

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`omgevingsvariabele, of
* `applicationinsights.configurationFile`Java-systeemeigenschap

Als u een relatief pad opgeeft, wordt dit `applicationinsights-agent-3.0.0-PREVIEW.jar` opgelost ten opzichte van de map waar zich bevindt.

## <a name="connection-string"></a>Verbindingsreeks

Dit is vereist. U uw verbindingstekenreeks vinden in uw bron Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Verbindingstekenreeks voor toepassingsinzichten":::

U de verbindingstekenreeks ook `APPLICATIONINSIGHTS_CONNECTION_STRING`instellen met behulp van de omgevingsvariabele.

## <a name="cloud-role-name"></a>Naam van cloudrol

De naam van de cloudrol wordt gebruikt om de component op de toepassingskaart te labelen.

Als u de naam van de cloudrol wilt instellen:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

Als de naam van de cloudrol niet is ingesteld, wordt de naam van de Application Insights-bron gebruikt om de component op de toepassingskaart te labelen.

U ook de naam van `APPLICATIONINSIGHTS_ROLE_NAME`de cloudrol instellen met behulp van de omgevingsvariabele.

## <a name="cloud-role-instance"></a>Cloudrolinstantie

Cloudrolinstantie standaard voor de naam van de machine.

Als u de instantie van de cloudrol wilt instellen op iets anders in plaats van de naam van de machine:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

U de cloudrolinstantie ook `APPLICATIONINSIGHTS_ROLE_INSTANCE`instellen met behulp van de omgevingsvariabele.

## <a name="application-log-capture"></a>Het vastleggen van toepassingslogboeken

Application Insights Java 3.0 Preview legt automatisch applicatielogging vast via Log4j, Logback en java.util.logging.

Standaard wordt alle logboekregistratie `WARN` vastgelegd die op niveau of hoger wordt uitgevoerd.

Als u deze drempelwaarde wilt wijzigen:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "ERROR"
        }
      }
    }
  }
}
```

Dit zijn `threshold` de geldige waarden die `ApplicationInsights.json` u in het bestand opgeven en hoe deze overeenkomen met logboekniveaus in verschillende logboekframeworks:

| `threshold`  | Log4j  | Aanmelding | JUL     |
|--------------|--------|---------|---------|
| UIT          | UIT    | UIT     | UIT     |
| Fatale        | Fatale  | FOUT   | Ernstige  |
| FOUT/ERNSTIG | FOUT  | FOUT   | Ernstige  |
| WAARSCHUWING/WAARSCHUWING | Waarschuwen   | Waarschuwen    | WAARSCHUWING |
| Info         | Info   | Info    | Info    |
| Config       | FOUTOPSPORING  | FOUTOPSPORING   | Config  |
| FOUTOPSPORING/BOETE   | FOUTOPSPORING  | FOUTOPSPORING   | Fijn    |
| Fijnere        | FOUTOPSPORING  | FOUTOPSPORING   | Fijnere   |
| TRACE/FINEST | Trace  | Trace   | Beste  |
| ALLE          | ALLE    | ALLE     | ALLE     |

## <a name="jmx-metrics"></a>JMX-statistieken

Als je een aantal JMX-statistieken hebt die je wilt vastleggen:

```json
{
  "instrumentationSettings": {
    "preview": {
        "jmxMetrics": [
        {
          "objectName": "java.lang:type=ClassLoading",
          "attribute": "LoadedClassCount",
          "display": "Loaded Class Count"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Code Cache",
          "attribute": "Usage.used",
          "display": "Code Cache Used"
        }
      ]
    }
  }
}
```

## <a name="micrometer"></a>Micrometer

Als uw toepassing [Micrometer](https://micrometer.io)gebruikt, voegt Application Insights 3.0 (te beginnen met Preview.2) zichzelf nu toe aan het globale register van micrometer en legt u micrometerstatistieken vast.

Als u deze functie wilt uitschakelen:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "micrometer": {
          "enabled": false
        }
      }
    }
  }
}
```

## <a name="heartbeat"></a>Hartslag

Standaard verzendt Application Insights Java 3.0 Preview eenmaal per 15 minuten een heartbeatstatistiek. Als u de hartslagstatistiek gebruikt om waarschuwingen te activeren, u de frequentie van deze hartslag verhogen:

```json
{
  "instrumentationSettings": {
    "preview": {
        "heartbeat": {
            "intervalSeconds": 60
        }
    }
  }
}
```

> [!NOTE]
> U de frequentie van deze heartbeat niet verlagen, omdat de heartbeatgegevens ook worden gebruikt om het gebruik van Application Insights bij te houden.

## <a name="sampling"></a>Steekproeven

Sampling is handig als u de kosten moet verlagen.
De bemonstering wordt uitgevoerd als functie op de bedrijfs-ID (ook wel trace-ID genoemd), zodat dezelfde bedrijfs-ID altijd tot dezelfde bemonsteringsbeslissing zal leiden. Dit zorgt ervoor dat u geen delen van een gedistribueerde transactie krijgt die worden bemonsterd terwijl andere delen ervan worden bemonsterd.

Als u bijvoorbeeld steekproeven instelt op 10%, ziet u slechts 10% van uw transacties, maar elk van deze 10% heeft volledige end-to-end transactiegegevens.

Hier is een voorbeeld hoe u de steekproef instelt op **10% van alle transacties** - zorg ervoor dat u de bemonsteringsfrequentie instelt die correct is voor uw use case:

```json
{
  "instrumentationSettings": {
    "preview": {
        "sampling": {
            "fixedRate": {
                "percentage": 10
            }
          }
        }
    }
}
```

## <a name="http-proxy"></a>HTTP-proxy

Als uw toepassing zich achter een firewall bevindt en niet rechtstreeks verbinding kan maken met Application Insights (zie [IP-adressen die worden gebruikt door Application Insights),](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses)u Application Insights Java 3.0 Preview configureren om een HTTP-proxy te gebruiken:

```json
{
  "instrumentationSettings": {
    "preview": {
      "httpProxy": {
        "host": "myproxy",
        "port": 8080
      }
    }
  }
}
```

## <a name="self-diagnostics"></a>Zelfdiagnostiek

"Zelfdiagnostiek" verwijst naar interne logging van Application Insights Java 3.0 Preview.

Dit kan handig zijn voor het opsporen en diagnosticeren van problemen met Application Insights zelf.

Standaard wordt deze logboeken `warn`logboeken op console met niveau, overeenkomend met deze configuratie:

```json
{
  "instrumentationSettings": {
    "preview": {
        "selfDiagnostics": {
            "destination": "console",
            "level": "WARN"
        }
    }
  }
}
```

Geldige niveaus `OFF` `ERROR`zijn `WARN` `INFO`, `DEBUG`, `TRACE`, , en .

Als u zich wilt aanmelden bij een bestand in plaats van u te registreren bij de console:

```json
{
  "instrumentationSettings": {
    "preview": {
        "selfDiagnostics": {
            "destination": "file",
            "directory": "/var/log/applicationinsights",
            "level": "WARN",
            "maxSizeMB": 10
        }    
    }
  }
}
```

Bij het gebruik van bestandsregistratie, zodra het bestand hits `maxSizeMB`, zal het rollover, het bijhouden van alleen de meest recent voltooide log bestand in aanvulling op de huidige log bestand.
