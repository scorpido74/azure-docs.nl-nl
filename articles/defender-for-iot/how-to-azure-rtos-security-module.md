---
title: Beveiligings module voor Azure RTO'S configureren en aanpassen
description: Meer informatie over het configureren en aanpassen van uw beveiligings module voor Azure RTO'S.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 9106e00fe2146978f97b480e3afd3b7ed58c9130
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935294"
---
# <a name="configure-and-customize-security-module-for-azure-rtos-preview"></a>Beveiligings module voor Azure RTO'S configureren en aanpassen (preview-versie)

Gebruik dit volgende bestand om het gedrag van uw apparaat te configureren.

## <a name="azure_iot_security_moduleincasc_porth"></a>azure_iot_security_module/Inc/asc_port. h

 Het standaard gedrag van elke configuratie vindt u in de volgende tabellen: 

### <a name="general"></a>Algemeen

| Naam | Type | Standaard | Details |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | Tekenreeks | --- | Unieke id van het apparaat  |
| ASC_SECURITY_MODULE_PENDING_TIME  | Getal | 300 | Time-out van beveiligings module in seconden. Als de tijd groter is dan de status wijziging wordt onderbroken. |

#### <a name="collection"></a>Verzameling

| Naam | Type | Standaard | Details |
| - | - | - | - |
| ASC_HIGH_PRIORITY_INTERVAL | Getal | 10 | Verzamel interval van de groep met hoge prioriteit in enkele seconden. |
| ASC_MEDIUM_PRIORITY_INTERVAL | Getal | 30 | Verzamel interval van gemiddelde prioriteits groep voor Collector in seconden. |
| ASC_LOW_PRIORITY_INTERVAL | Getal | 145.440  | Verzamel interval van groep met lage prioriteit in enkele seconden. |

#### <a name="collector-network-activity"></a>Activiteit netwerk verzamelaar

Als u de configuratie van de Collector netwerk activiteit wilt aanpassen, gebruikt u het volgende:

| Naam | Type | Standaard | Details |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boolean-waarde | onjuist | `TCP`Netwerk activiteit filteren |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boolean-waarde | onjuist | `UDP`Gebeurtenissen voor netwerk activiteit filteren |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boolean-waarde | onjuist | `ICMP`Gebeurtenissen voor netwerk activiteit filteren |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Boolean-waarde | true | Alleen unicast-binnenkomende pakketten vastleggen, wanneer deze zijn ingesteld op ONWAAR, ook broadcast en multi cast |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Getal | 64 | Maximum aantal IPv4-netwerk gebeurtenissen dat in het geheugen moet worden opgeslagen |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Getal | 64  | Maximum aantal IPv6-netwerk gebeurtenissen dat in het geheugen moet worden opgeslagen |


## <a name="compile-flags"></a>Compileer vlaggen
Met Compileer vlaggen kunt u de vooraf gedefinieerde configuraties onderdrukken.

### <a name="collectors"></a>Verzamelaars
| Naam | Type | Standaard | Details |
| - | - | - | - |
| collector_heartbeat_enabled | Boolean-waarde | AAN | De heartbeat-Collector inschakelen |
| collector_network_activity_enabled | Boolean-waarde | AAN | De netwerk activiteit verzamelaar inschakelen |
| collector_system_information_enabled | Boolean-waarde | AAN | De systeem informatie verzamelaar inschakelen |

## <a name="supported-security-alerts-and-recommendations"></a>Ondersteunde beveiligings waarschuwingen en aanbevelingen

De beveiligings module voor Azure RTO'S ondersteunt specifieke beveiligings waarschuwingen en aanbevelingen. Zorg ervoor dat u [de relevante waarschuwings-en aanbevelings waarden voor uw service bekijkt en wijzigt](concept-rtos-security-alerts-recommendations.md) .

## <a name="log-analytics-optional"></a>Log Analytics (optioneel)

Optioneel en niet vereist: het inschakelen en configureren van Log Analytics kan handig zijn wanneer u de gebeurtenissen en activiteiten van het apparaat verder wilt onderzoeken. Lees meer over het instellen en gebruiken van [log Analytics met de Defender voor IOT-service](how-to-security-data-access.md#log-analytics) voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

- Beveiligings module voor Azure RTO'S- [beveiligings waarschuwingen en-aanbevelingen](concept-rtos-security-alerts-recommendations.md) controleren en aanpassen
- Raadpleeg indien nodig de [beveiligings module voor de Azure rto's-API](azure-rtos-security-module-api.md) .

