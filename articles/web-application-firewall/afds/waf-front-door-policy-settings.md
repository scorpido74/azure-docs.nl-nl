---
title: Beleidsinstellingen voor Web Application Firewall met Azure Front Door
description: Lees webtoepassingsfirewall (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932615"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Beleidsinstellingen voor Web Application Firewall op Azure Front Door

Met een WAF-beleid (Web Application Firewall) u de toegang tot uw webtoepassingen beheren aan de hand van een reeks aangepaste en beheerde regels. De naam WAF-beleid moet uniek zijn. U ontvangt een validatiefout als u een bestaande naam probeert te gebruiken. Er zijn meerdere beleidsniveauinstellingen die van toepassing zijn op alle regels die zijn opgegeven voor dat beleid zoals beschreven in dit artikel.

## <a name="waf-state"></a>WAF-staat

Een WAF-beleid voor voordeur kan in een van de volgende twee staten:
- **Ingeschakeld:** Wanneer een beleid is ingeschakeld, controleert WAF actief binnenkomende aanvragen en onderneemt overeenkomstige acties volgens regeldefinities
- **Uitgeschakeld:** - Wanneer een beleid is uitgeschakeld, wordt de WAF-inspectie onderbroken. Inkomende aanvragen zullen WAF omzeilen en worden verzonden naar back-ends op basis van Front Door routing.

## <a name="waf-mode"></a>WAF-modus

WAF-beleid kan worden geconfigureerd om uit te voeren in de volgende twee modi:

- **Detectiemodus** Wanneer WAF wordt uitgevoerd in de detectiemodus, onderneemt het geen andere acties dan de aanvraag en de overeenkomende WAF-regel te controleren en te registreren op WAF-logboeken. Schakel logboekdiagnoses voor voordeur in (bij gebruik van portal kan dit worden bereikt door naar de sectie **Diagnostische gegevens** in de Azure-portal te gaan).

- **Preventiemodus** Wanneer waf is geconfigureerd om in de preventiemodus te worden uitgevoerd, voert u de opgegeven actie uit als een aanvraag overeenkomt met een regel. Alle overeenkomende aanvragen worden ook geregistreerd in de WAF-logboeken.

## <a name="waf-response-for-blocked-requests"></a>WAF-antwoord op geblokkeerde aanvragen

Wanneer WAF een aanvraag blokkeert vanwege een overeenkomende regel, wordt standaard een statuscode van 403 geretourneerd met - **Het verzoek wordt geblokkeerd** bericht. Er wordt ook een referentietekenreeks geretourneerd voor logboekregistratie.

U een aangepaste antwoordstatuscode en -antwoordbericht definiëren wanneer een verzoek wordt geblokkeerd door WAF. De volgende aangepaste statuscodes worden ondersteund:

- 200 OK
- 403 Verboden
- 405 Methode niet toegestaan
- 406 Niet aanvaardbaar
- 429 Te veel aanvragen

Aangepaste reactiestatuscode en reactiebericht is een instelling voor beleidsniveau. Zodra deze is geconfigureerd, krijgen alle geblokkeerde aanvragen dezelfde aangepaste reactiestatus en reactiebericht.

## <a name="uri-for-redirect-action"></a>URI voor omleidingsactie

U moet een URI definiëren om aanvragen om te leiden naar als de **actie REDIRECT** is geselecteerd voor een van de regels in een WAF-beleid. Deze omleiding URI moet een geldige HTTP(S) site en eenmaal geconfigureerd, alle aanvragen overeenkomende regels met een "REDIRECT" actie zal worden doorgestuurd naar de opgegeven site.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het definiëren van aangepaste [WAF-antwoorden](waf-front-door-configure-custom-response-code.md)
