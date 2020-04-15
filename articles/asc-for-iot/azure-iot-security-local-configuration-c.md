---
title: Lokale configuratie van beveiligingsagent (C)
description: Meer informatie over Azure Security Center voor lokale configuratievan de agent voor C.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: cd344b9bebb69af210c482f46af6b2dd7edf7816
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311708"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Meer informatie over het bestand LocalConfiguration.json - C-agent

De Azure Security Center for IoT-beveiligingsagent gebruikt configuraties van een lokaal configuratiebestand.
De beveiligingsagent leest de configuratie eenmaal, bij het opstarten van de agent.
De configuratie in het lokale configuratiebestand bevat verificatieconfiguratie en andere agentgerelateerde configuraties.
Het bestand bevat configuraties in "Key-Value"-paren in JSON-notatie en de configuraties worden ingevuld wanneer de agent is geïnstalleerd.

Standaard bevindt het bestand zich op: /var/ASCIoTAgent/LocalConfiguration.json

Wijzigingen in het configuratiebestand vinden plaats wanneer de agent opnieuw wordt opgestart.

## <a name="security-agent-configurations-for-c"></a>Configuratie van beveiligingsagents voor C

| Configuratienaam | Mogelijke waarden | Details |
|:-----------|:---------------|:--------|
| AgentId (AgentId) | GUID | De agent Uniek-id |
| TriggerdEventsInterval | ISO8601-tekenreeks | Interval van scheduler voor het verzamelen van geactiveerde gebeurtenissen |
| Connectiontimeout | ISO8601-tekenreeks | Tijdsperiode voordat de verbinding met IoThub wordt getimed |
| Verificatie | JsonObject | Verificatieconfiguratie. Dit object bevat alle informatie die nodig is voor verificatie tegen IoTHub |
| Identiteit | "DPS", "SecurityModule", "Apparaat" | Verificatie-identiteit - DPS als verificatie wordt uitgevoerd via DPS, SecurityModule als verificatie wordt gemaakt via referenties van beveiligingsmodules of als verificatie wordt gemaakt met apparaatreferenties |
| Verificatiemethode | "SasToken", "SelfSignedCertificate" | het gebruikersgeheim voor verificatie - Kies SasToken als het gebruiksgeheim een symmetrische sleutel is, kies zelfondertekend certificaat als het geheim een zelfondertekend certificaat is  |
| Filepath | Pad naar bestand (tekenreeks) | Pad naar het bestand dat het verificatiegeheim bevat |
| HostName | tekenreeks | De hostnaam van de azure iot-hub. meestal <mijn-hub>.azure-devices.net |
| DeviceId | tekenreeks | De id van het apparaat (zoals geregistreerd in Azure IoT Hub) |
| Dps | JsonObject | DPS-gerelateerde configuraties |
| IDScope | tekenreeks | ID-bereik van DPS |
| RegistratieId | tekenreeks  | DPS-apparaatregistratie-ID |
| Logboekregistratie | JsonObject | Agent logger gerelateerde configuraties |
| SystemLoggerMinimumSeverity SystemLoggerMinimumSeverity SystemLoggerMinimumSeverity SystemLogger | 0 <= getal <= 4 | logberichten gelijk en boven deze ernst worden geregistreerd op / var / log / syslog (0 is de laagste ernst) |
| DiagnosticEventMinimumErnst | 0 <= getal <= 4 | logberichten gelijk en boven deze ernst worden verzonden als diagnostische gebeurtenissen (0 is de laagste ernst) |

## <a name="security-agent-configurations-code-example"></a>Voorbeeld van beveiligingsagentconfiguraties

```JSON
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

- Lees het [overzicht](overview.md) van de Azure Security Center for IoT-service
- Meer informatie over Azure Security Center voor [IoT-architectuur](architecture.md)
- De Azure Security Center for IoT-service [inschakelen](quickstart-onboard-iot-hub.md)
- Lees de veelgestelde vragen over azure security center voor [IoT-service](resources-frequently-asked-questions.md)
- Meer informatie over hoe u toegang krijgt tot [ruwe beveiligingsgegevens](how-to-security-data-access.md)
- Aanbevelingen [begrijpen](concept-recommendations.md)
- [Beveiligingswaarschuwingen begrijpen](concept-security-alerts.md)
