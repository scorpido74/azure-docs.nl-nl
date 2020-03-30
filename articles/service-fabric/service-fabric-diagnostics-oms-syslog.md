---
title: Linux-clustergebeurtenissen bewaken in Azure Service Fabric
description: Meer informatie over het monitoren van een Service Fabric Linux-clustergebeurtenissen door servicefabric-platformgebeurtenissen naar Syslog te schrijven.
author: srrengar
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 5bd3bda71943b2ba8a34cd4fbd0b20917b875670
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645749"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Service Fabric Linux cluster gebeurtenissen in Syslog

Service Fabric onthult een reeks platformgebeurtenissen om u te informeren over belangrijke activiteiten in uw cluster. De volledige lijst van gebeurtenissen die worden blootgesteld is [hier](service-fabric-diagnostics-event-generation-operational.md)beschikbaar. Er zijn verschillende manieren waarop deze gebeurtenissen kunnen worden geconsumeerd. In dit artikel gaan we bespreken hoe servicefabric te configureren om deze gebeurtenissen naar Syslog te schrijven.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Inleiding

In de 6.4 release is de SyslogConsumer geïntroduceerd om de Service Fabric platform events naar Syslog te sturen voor Linux clusters. Zodra deze is ingeschakeld, worden gebeurtenissen automatisch naar Syslog verzonden, die kunnen worden verzameld en verzonden door de Log Analytics Agent.

Elk Syslog-evenement heeft 4 componenten
* Faciliteit
* Identiteit
* Bericht
* Severity

De SyslogConsumer schrijft alle `Local0`platform evenementen met behulp van Facility . U updaten naar elke geldige faciliteit door het veranderen van de config config. De gebruikte `ServiceFabric`identiteit is . Het veld Bericht bevat de hele gebeurtenis die is geserialiseerd in JSON, zodat deze kan worden opgevraagd en verbruikt door verschillende hulpprogramma's. 

## <a name="enable-syslogconsumer"></a>SyslogConsumer inschakelen

Om de SyslogConsumer in te schakelen, moet u een upgrade van uw cluster uitvoeren. De `fabricSettings` sectie moet worden bijgewerkt met de volgende code. Let op deze code bevat alleen secties met betrekking tot SyslogConsumer

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

Hier zijn de wijzigingen om uit te roepen
1. In de sectie Gemeenschappelijk is `LinuxStructuredTracesEnabled`er een nieuwe parameter genaamd . **Dit is vereist om Linux-gebeurtenissen gestructureerd en geserialiseerd te hebben wanneer deze naar Syslog worden verzonden.**
2. In de sectie Diagnostics is een nieuwe ConsumerInstance: SyslogConsumer toegevoegd. Dit vertelt het platform is er een andere consument van de gebeurtenissen. 
3. De nieuwe sectie SyslogConsumer `IsEnabled` `true`moet hebben als . Het is geconfigureerd om de Local0-faciliteit automatisch te gebruiken. U dit overschrijven door een andere parameter toe te voegen.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Azure Monitor registreert integratie
U deze Syslog-gebeurtenissen lezen in een bewakingstool zoals Azure Monitor-logboeken. U een Log Analytics-werkruimte maken met behulp van de Azure Marketplace met behulp van deze [instructies]. (.. /azure-monitor/learn/quick-create-workspace.md) U moet ook de agent Log Analytics aan uw cluster toevoegen om deze gegevens naar de werkruimte te verzamelen en te verzenden. Dit is dezelfde agent die wordt gebruikt om prestatiemeteritems te verzamelen. 

1. Navigeren naar `Advanced Settings` het blad

    ![Werkruimte-instellingen](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Klik op `Data`
3. Klik op `Syslog`
4. Configureer Local0 als de faciliteit om bij te houden. U een andere faciliteit toevoegen als u deze hebt gewijzigd in fabricSettings

    ![Syslog configureren](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Ga naar de queryverkenner `Logs` door in het menu van de werkruimtebron te klikken om te beginnen met query's

    ![Werkruimtelogboeken](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. U een `Syslog` query `ServiceFabric` uitvoeren tegen de tabel die zoekt als de ProcessName. De onderstaande query is een voorbeeld van hoe u de JSON in de gebeurtenis ontleden en de inhoud ervan weergeven

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Syslog-query](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

Het bovenstaande voorbeeld is een nodedown-gebeurtenis. U de volledige lijst van gebeurtenissen [hier](service-fabric-diagnostics-event-generation-operational.md)bekijken.

## <a name="next-steps"></a>Volgende stappen
* [Implementeer de Log Analytics-agent](service-fabric-diagnostics-oms-agent.md) op uw knooppunten om prestatiemeteritems te verzamelen en dockerstatistieken en -logboeken voor uw containers te verzamelen
* Maak kennis met de [functies voor zoeken en query's voor logboeken](../log-analytics/log-analytics-log-searches.md) die worden aangeboden als onderdeel van Azure Monitor-logboeken
* [View Designer gebruiken om aangepaste weergaven te maken in Azure Monitor-logboeken](../log-analytics/log-analytics-view-designer.md)
* Informatie over hoe [u azure-monitorlogboekenintegratie met Syslog implementeren](../log-analytics/log-analytics-data-sources-syslog.md).
