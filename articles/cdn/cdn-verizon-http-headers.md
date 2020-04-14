---
title: Verizon-specifieke HTTP-headers voor Azure CDN-regels engine | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u Verizon-specifieke HTTP-headers gebruikt met Azure CDN-regelsengine.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: allensu
ms.openlocfilehash: d2208f6769c8051b38bdafb92d62ec03cb2d668c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253557"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Verizon-specifieke HTTP-headers voor Azure CDN-regelsengine

Voor **Azure CDN Premium van Verizon-producten** kan de POP-server een of meer gereserveerde kopteksten (of speciale proxykoppen) toevoegen aan het clientverzoek aan de POP wanneer een HTTP-aanvraag naar de origin-server wordt verzonden. Deze headers zijn een aanvulling op de standaard doorsturen headers ontvangen. Zie [Velden aanvragen voor](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields)informatie over standaardaanvraagkoppen.

Als u wilt voorkomen dat een van deze gereserveerde kopteksten wordt toegevoegd in het POP-aanvraag azure CDN (Content Delivery Network) aan de oorspronkelijke server, moet u een regel maken met de [functie Speciale proxykoppen](cdn-verizon-premium-rules-engine-reference-features.md#proxy-special-headers) in de regelengine. Sluit in deze regel de koptekst uit die u wilt verwijderen uit de standaardlijst met kopteksten in het veld Kopteksten. Als u de [functie Antwoordkoppen foutopsporing](cdn-verizon-premium-rules-engine-reference-features.md#debug-cache-response-headers)hebt ingeschakeld, moet `X-EC-Debug` u de benodigde kopteksten toevoegen. 

Als u bijvoorbeeld `Via` de koptekst wilt verwijderen, moet het veld kopteksten van de regel de volgende lijst met kopteksten bevatten: *X-Forwarded-For, X-Forwarded-Proto, X-Host, X-Midgress, X-Gateway-List, X-EC-Name, Host*. 

![Regel speciale proxykopteksten](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

In de volgende tabel worden de kopteksten beschreven die door de Verizon CDN POP in het verzoek kunnen worden toegevoegd:

Aanvraagheader | Beschrijving | Voorbeeld
---------------|-------------|--------
[Via](#via-request-header) | Hiermee identificeert u de POP-server die het verzoek heeft geproxied naar een oorsprongsserver. | HTTP/1.1 ECS (dca/1A2B)
X-Doorgestuurd-Voor | Geeft het IP-adres van de aanvrager aan.| 10.10.10.10
X-Forwarded-Proto | Geeft het protocol van het verzoek aan. | http
X-Host | Geeft de hostnaam van het verzoek aan. | cdn.mydomain.com
X-Midgress | Geeft aan of de aanvraag is geproxied via een extra CDN-server. Bijvoorbeeld een POP-server-to-origin shield-server of een POP-server-naar-ADN-gatewayserver. <br />Deze koptekst wordt alleen aan de aanvraag toegevoegd wanneer het midgress-verkeer plaatsvindt. In dit geval is de koptekst ingesteld op 1 om aan te geven dat de aanvraag is geproxied via een extra CDN-server.| 1
[Host](#host-request-header) | Hiermee identificeert u de host en de poort waar de gevraagde inhoud kan worden gevonden. | marketing.mydomain.com:80
[X-Gateway-lijst](#x-gateway-list-request-header) | ADN: identificeert de failoverlijst van ADN Gateway-servers die zijn toegewezen aan een klantoorsprong. <br />Oorsprongsschild: geeft de set van origin shield-servers aan die zijn toegewezen aan de oorsprong van een klant. | `icn1,hhp1,hnd1`
X-EC-_&lt;naam&gt;_ | Aanvragen headers die beginnen met *X-EC* (bijvoorbeeld X-EC-Tag, [X-EC-Debug)](cdn-http-debug-headers.md)zijn gereserveerd voor gebruik door het CDN.| waf-productie

## <a name="via-request-header"></a>Via aanvraagheader
De indeling waarmee `Via` de aanvraagkop een POP-server identificeert, wordt opgegeven met de volgende syntaxis:

`Via: Protocol from Platform (POP/ID)` 

De termen die in de syntaxis worden gebruikt, worden als volgt gedefinieerd:
- Protocol: geeft de versie van het protocol aan (bijvoorbeeld HTTP/1.1) die wordt gebruikt om de aanvraag te proxyn. 

- Platform: Geeft het platform aan waarop de inhoud is aangevraagd. Voor dit veld gelden de volgende codes: 

    Code | Platform
    -----|---------
    ECAcc | HTTP Groot
    Ecs   | HTTP Klein
    Ecd   | Netwerk voor toepassingsbezorging (ADN)

- POP: Geeft de [POP](cdn-pop-abbreviations.md) aan die de aanvraag heeft afgehandeld. 

- ID: Alleen voor intern gebruik.

### <a name="example-via-request-header"></a>Voorbeeld via aanvraagheader

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Koptekst hostaanvraag
De POP-servers overschrijven de `Host` koptekst wanneer beide voorwaarden waar zijn:
- De bron voor de gevraagde inhoud is een server van de oorsprong van de klant.
- De http-hostheader optie van de desbetreffende klantoorsprong is niet leeg.

De `Host` aanvraagkopwordt overschreven om de waarde weer te geven die is gedefinieerd in de optie HTTP-hostheader.
Als de http-hostheaderoptie van de klant is `Host` ingesteld op leeg, wordt de door de aanvrager verzonden header die door de aanvrager wordt ingediend, doorgestuurd naar de oorspronkelijke server van de klant.

## <a name="x-gateway-list-request-header"></a>Koptekst x-gateway-lijst-aanvraag
Een POP-server voegt de aanknopingskop 'X-Gateway-List-aanvraag toe wanneer aan een van de volgende voorwaarden is voldaan:
- De aanvraag verwijst naar het ADN-platform.
- De aanvraag wordt doorgestuurd naar een server van de oorsprong van de klant die wordt beschermd door de Origin Shield-functie.

