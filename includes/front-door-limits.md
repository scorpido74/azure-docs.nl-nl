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
ms.openlocfilehash: 148ec3eccce71ab7a4a6c1391c0fa4753c248bd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334736"
---
| Resource | Limiet |
| --- | --- |
| Azure Front Door-bronnen per abonnement | 100 |
| Front-end hosts, die aangepaste domeinen per resource bevat | 500 |
| Routeringsregels per resource | 500 |
| Back-endpools per resource | 50 |
| Back-ends per back-end pool | 100 |
| Padpatronen die overeenkomen met een routeringsregel | 25 |
| URL's in één cache-zuiveringsgesprek | 100 |
| Aangepaste firewallregels voor webtoepassingen per beleid | 100 |
| Firewallbeleid voor webtoepassingen per abonnement | 100 |
| Webtoepassingsfirewall matchvoorwaarden per aangepaste regel | 10 |
| IP-adresbereiken van firewallfirewall per wedstrijdvoorwaarde | 600 |
| Tekenreeks firewallreeks voor webtoepassingen die overeenkomen met waarden per overeenkomstvoorwaarde | 10 |
| Overeenkomenmet de waardelengte van firewallreeks voor webtoepassingen | 256 |
| Naamvan de parameternaam van de firewall voor firewall POST | 256 |
| Http-koptekstlengte van webtoepassingsfirewall | 256 |
| Cookienaam lengte van firewallfirewall | 256 |
| Webapplication firewall HTTP-aanvraag lichaamsgrootte geïnspecteerd | 128 kB |
| Aangepaste antwoordtekstlengte van de firewall voor webtoepassingen | 2 KB |

### <a name="timeout-values"></a>Time-outwaarden
#### <a name="client-to-front-door"></a>Klant aan Voordeur
* Front Door heeft een idle TCP-verbindingstime-out van 61 seconden.

#### <a name="front-door-to-application-back-end"></a>Voordeur naar toepassing back-end
* Als het antwoord een geblokte respons is, wordt een 200 geretourneerd als en wanneer het eerste stuk wordt ontvangen.
* Nadat de HTTP-aanvraag naar de achterkant is doorgestuurd, wacht De voordeur 30 seconden op het eerste pakket van de achterkant. Vervolgens geeft het een 503-fout aan de client. Deze waarde is configureerbaar via het veldsendRecvTimeoutSeconds in de API.
    * Voor caching-scenario's is deze time-out niet configureerbaar en dus, als een aanvraag in de cache wordt opgeslagen en het duurt meer dan 30 seconden voor het eerste pakket van Front Door of vanaf de backend, wordt een 504-fout teruggestuurd naar de client. 
* Nadat het eerste pakket van de achterkant is ontvangen, wacht De Voordeur 30 seconden in een niet-actieve time-out. Vervolgens geeft het een 503-fout aan de client. Deze time-outwaarde is niet configureerbaar.
* Voordeur naar de back-end TCP sessie time-out is 90 seconden.

### <a name="upload-and-download-data-limit"></a>Limiet voor het uploaden en downloaden van gegevens

|  | Met chunked transfer codering (CTE) | Zonder HTTP chunking |
| ---- | ------- | ------- |
| **Downloaden** | Er is geen limiet aan de downloadgrootte. | Er is geen limiet aan de downloadgrootte. |
| **Uploaden** |    Er is geen limiet zolang elke CTE-upload minder dan 2 GB bedraagt. | De grootte mag niet groter zijn dan 2 GB. |

### <a name="other-limits"></a>Andere limieten
* Maximale URL-grootte - 8.192 bytes - Geeft maximale lengte van de ruwe URL op (schema + hostnaam + poort + pad + querytekenreeks van de URL)
* Maximale querytekenreeksgrootte - 4.096 bytes - Hiermee geeft u de maximale lengte van de querytekenreeks op in bytes.
* Maximale HTTP-antwoordkopgrootte van url van statussonde - 4.096 bytes - De maximale lengte van alle antwoordkoppen van statussondes opgegeven. 
