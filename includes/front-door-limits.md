---
title: bestand opnemen
description: bestand opnemen
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: 37ebe2f0c5cbbaca712e69ab4484379ecf0f4830
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72237334"
---
| Bron | Standaard/maximum limiet |
| --- | --- |
| Azure front-deur service resources per abonnement | 100 |
| Front-end-hosts, met inbegrip van aangepaste domeinen per resource | 100 |
| Routerings regels per resource | 100 |
| Back-endservers per resource | 50 |
| Back-ends per back-end-pool | 100 |
| Patroon paden die overeenkomen voor een routerings regel | 25 |
| Aangepaste Web Application Firewall regels per beleid | 10 |
| Web Application firewall-beleid per abonnement | 100 |
| Overeenkomende Firewall voor waarden per aangepaste regel voor Web Application | 10 |
| IP-adresbereiken van Web Application firewall per match-voor waarde | 600 |
| Firewall teken reeks voor webtoepassing overeenkomende waarden per match-voor waarde | 10 |
| De lengte van de teken reeks voor de firewall voor webtoepassingen | 256 |
| Lengte van de para meter voor de naam van de hoofd tekst van Web Application firewall | 256 |
| Lengte van HTTP-header voor Web Application firewall | 256 |
| Lengte van cookie naam Web Application firewall | 256 |
| Grootte van de HTTP-aanvraag van de firewall voor webtoepassingen geinspected | 128 KB |
| Lengte van aangepaste antwoord tekst voor Web Application firewall | 2 KB |

### <a name="timeout-values"></a>Time-outwaarden
#### <a name="client-to-front-door"></a>Client naar voor deur
- De voor deur heeft een time-out van 61 seconden voor inactieve TCP-verbindingen.

#### <a name="front-door-to-application-back-end"></a>Back-end van front-deur naar toepassing
- Als het antwoord een gedeeld antwoord is, wordt een 200 geretourneerd als of wanneer de eerste segment wordt ontvangen.
- Nadat de HTTP-aanvraag naar de back-end is doorgestuurd, wacht de front deur 30 seconden voor het eerste pakket van de back-end. Vervolgens wordt er een 503-fout naar de client geretourneerd.
- Nadat het eerste pakket is ontvangen van de back-end, wacht de front deur 30 seconden in een time-out voor inactiviteit. Vervolgens wordt er een 503-fout naar de client geretourneerd.
- De voor deur van de back-end van de TCP-sessie is 30 minuten.

### <a name="upload-and-download-data-limit"></a>Gegevens limiet uploaden en downloaden

|  | Met gesegmenteerde overdrachts codering (CTE) | Zonder HTTP-Chunking |
| ---- | ------- | ------- |
| **Downloaden** | Er is geen limiet voor de download grootte. | Er is geen limiet voor de download grootte. |
| **Wijzigingen** |  Er is geen limiet, zolang elke CTE-upload kleiner is dan 2 GB. | De grootte kan niet groter zijn dan 2 GB. |

### <a name="other-limits"></a>Andere limieten
- Maximale URL-grootte-8.192 bytes-Hiermee geeft u de maximum lengte van de onbewerkte URL op (schema + hostnaam + poort + pad + query teken reeks van de URL)
- Maximale grootte van de query teken reeks-4.096 bytes-Hiermee geeft u de maximum lengte van de query reeks op in bytes.
