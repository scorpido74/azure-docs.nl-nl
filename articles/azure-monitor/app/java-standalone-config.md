---
title: Java-toepassingen overal bewaken-Azure Monitor Application Insights
description: Toepassings prestatie bewaking voor Java-toepassingen die worden uitgevoerd in een wille keurige omgeving zonder de app te instrumenteren. Zoek de hoofd oorzaak van de problemen d met behulp van gedistribueerde tracering en toepassings overzicht.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 3a01f2e9339f3ba4f8f50fc41617fabf9da9d8cd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87014198"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Configuratie opties-Java zelfstandige agent voor Azure Monitor Application Insights



## <a name="connection-string-and-role-name"></a>Verbindings reeks en rolnaam

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

De connection string is vereist en de rolnaam is belang rijk telkens wanneer u gegevens van verschillende toepassingen naar dezelfde Application Insights resource verzendt.

Meer informatie en aanvullende configuratie opties vindt u hieronder voor meer informatie.

## <a name="configuration-file-path"></a>Pad naar configuratie bestand

Application Insights Java 3,0 Preview verwacht dat het configuratie bestand wordt benoemd `ApplicationInsights.json` en zich in dezelfde map als bevindt `applicationinsights-agent-3.0.0-PREVIEW.5.jar` .

U kunt uw eigen pad naar een configuratie bestand opgeven met

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`omgevings variabele of
* `applicationinsights.configurationFile`Java-systeem eigenschap

Als u een relatief pad opgeeft, wordt dit omgezet ten opzichte van de map waar `applicationinsights-agent-3.0.0-PREVIEW.5.jar` zich bevindt.

## <a name="connection-string"></a>Verbindingsreeks

Dit is vereist. U kunt uw connection string vinden in uw Application Insights-resource:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Verbindings reeks Application Insights":::

U kunt de connection string ook instellen met behulp van de omgevings variabele `APPLICATIONINSIGHTS_CONNECTION_STRING` .

## <a name="cloud-role-name"></a>Rolnaam van Cloud

De naam van de Cloud functie wordt gebruikt om het onderdeel op het toepassings overzicht te labelen.

Als u de naam van de Cloud functie wilt instellen:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

Als de naam van de Cloud functie niet is ingesteld, wordt de naam van de Application Insights resource gebruikt voor het labelen van het onderdeel op de toepassings toewijzing.

U kunt ook de naam van de Cloud functie instellen met behulp van de omgevings variabele `APPLICATIONINSIGHTS_ROLE_NAME` .

## <a name="cloud-role-instance"></a>Cloud rolinstantie

De computer naam van de instantie van de Cloud wordt standaard ingesteld.

Als u de Cloud rolinstantie wilt instellen op iets anders dan de naam van de computer:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

U kunt ook de Cloud rolinstantie instellen met behulp van de omgevings variabele `APPLICATIONINSIGHTS_ROLE_INSTANCE` .

## <a name="application-log-capture"></a>Toepassings logboeken vastleggen

Application Insights Java 3,0 Preview registreert automatisch toepassings logboeken via Log4j, logback en Java. util. log.

Standaard worden alle logboek registraties vastgelegd op `WARN` niveau of hoger.

Als u deze drempel waarde wilt wijzigen:

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

Dit zijn de geldige `threshold` waarden die u in het bestand kunt opgeven `ApplicationInsights.json` en de manier waarop ze overeenkomen met de registratie niveaus in verschillende registratie raamwerken:

| `threshold`  | Log4j  | Logback | JUL     |
|--------------|--------|---------|---------|
| UIT          | UIT    | UIT     | UIT     |
| FATALE        | FATALE  | FOUT   | ZEER  |
| FOUT/ERNSTIG | FOUT  | FOUT   | ZEER  |
| WAARSCHUWEN/WAARSCHUWING | WETEN   | WETEN    | WAARSCHUWING |
| VALUTA         | VALUTA   | VALUTA    | VALUTA    |
| CONFIGURATIES       | FOUTOPSPORING  | FOUTOPSPORING   | CONFIGURATIES  |
| FOUTEN OPSPOREN/VERFIJNEN   | FOUTOPSPORING  | FOUTOPSPORING   | BLIJVEN    |
| KLEINERE        | FOUTOPSPORING  | FOUTOPSPORING   | KLEINERE   |
| TRACERING/KLEINSTE | TRACERINGS  | TRACERINGS   | MEEST  |
| ALL          | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>Metrische gegevens van JMX

Als u bepaalde JMX-metrische gegevens hebt die u wilt vastleggen:

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

## <a name="micrometer-including-metrics-from-spring-boot-actuator"></a>Micrometer (inclusief metrische gegevens van de lente-boot-klep)

Als uw toepassing [micrometer](https://micrometer.io)gebruikt, worden de metrische gegevens die worden verzonden naar het globale REGI ster van micrometer vastgelegd in Application Insights 3,0 (te beginnen met preview. 2).

Als uw toepassing [veer boot Actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)gebruikt, wordt in Application Insights 3,0 (te beginnen met preview. 4) nu de metrische gegevens vastgelegd die zijn geconfigureerd met een Spring boot-klep (die gebruikmaakt van micrometer, maar het globale REGI ster van micrometer niet gebruikt).

Als u deze functies wilt uitschakelen:

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

Application Insights Java 3,0 Preview verzendt standaard elke 15 minuten een heartbeat-metriek. Als u de heartbeat-metriek gebruikt om waarschuwingen te activeren, kunt u de frequentie van deze heartbeat verhogen:

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
> U kunt de frequentie van deze heartbeat niet verlagen, omdat de heartbeat-gegevens ook worden gebruikt om Application Insights gebruik bij te houden.

## <a name="sampling"></a>Steekproeven

Steek proeven zijn handig als u kosten wilt verlagen.
Steek proeven worden uitgevoerd als een functie van de bewerkings-ID (ook wel traceer-ID genoemd), zodat dezelfde bewerkings-ID altijd resulteert in dezelfde steekproef beslissing. Dit zorgt ervoor dat er geen delen van een gedistribueerde trans actie worden opgehaald, terwijl andere onderdelen hiervan worden bemonsterd.

Als u bijvoorbeeld steek proeven instelt op 10%, worden er slechts 10% van uw trans acties weer geven, maar elke waarde van 10% heeft volledige end-to-end-transactie gegevens.

Hier volgt een voor beeld van het instellen van de steek proef op **10% van alle trans acties** . Controleer of u de sampling frequentie hebt ingesteld die juist is voor uw gebruiks voorbeeld:

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

Als uw toepassing zich achter een firewall bevindt en niet rechtstreeks verbinding kan maken met Application Insights (Zie [IP-adressen die worden gebruikt door Application Insights](./ip-addresses.md)), kunt u Application Insights Java 3,0 Preview configureren voor het gebruik van een http-proxy:

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

## <a name="self-diagnostics"></a>Zelf diagnostische gegevens

' Zelf diagnostische gegevens ' verwijst naar interne logboek registratie van Application Insights Java 3,0 Preview.

Dit kan handig zijn voor herkennen en het diagnosticeren van problemen met Application Insights zichzelf.

Standaard meldt de app aan op de console met niveau `warn` , die overeenkomt met deze configuratie:

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

Geldige niveaus zijn,,,, `OFF` `ERROR` `WARN` `INFO` `DEBUG` en `TRACE` .

Als u zich wilt aanmelden bij een bestand in plaats van zich aan te melden bij de console:

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

Wanneer u logboek registratie gebruikt en het bestand is gevonden, `maxSizeMB` wordt het overschakelt, waarbij alleen het meest recente voltooide logboek bestand naast het huidige logboek bestand wordt bewaard.
