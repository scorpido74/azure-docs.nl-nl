---
title: Aangepaste regel voor firewall voor Azure-voorbeheer
description: Meer informatie over het gebruik van waf-regels (Web Application Firewall) die uw webtoepassingen beschermen tegen schadelijke aanvallen.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475821"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Aangepaste regels voor webtoepassingsfirewall met Azure Front Door

Azure Web Application Firewall (WAF) met Front Door stelt u in staat om de toegang tot uw webtoepassingen te beheren op basis van de voorwaarden die u definieert. Een aangepaste WAF-regel bestaat uit een prioriteitsnummer, regeltype, overeenkomende voorwaarden en een actie. Er zijn twee soorten aangepaste regels: regels en regels voor tarieflimiet. Een overeenkomstregel regelt de toegang op basis van een reeks overeenkomende voorwaarden, terwijl een tarieflimietregel de toegang regelt op basis van overeenkomende voorwaarden en de tarieven van binnenkomende aanvragen. U een aangepaste regel uitschakelen om te voorkomen dat deze wordt geëvalueerd, maar de configuratie behouden blijft. 

## <a name="priority-match-conditions-and-action-types"></a>Prioriteits-, wedstrijdvoorwaarden en actietypen

U de toegang beheren met een aangepaste WAf-regel die een prioriteitsnummer, een regeltype, een matrix van wedstrijdvoorwaarden en een actie definieert. 

- **Prioriteit:** is een uniek geheel getal dat de volgorde van evaluatie van WAF-regels beschrijft. Regels met lagere prioriteitswaarden worden geëvalueerd vóór regels met hogere waarden. Prioriteitsnummers moeten uniek zijn tussen alle aangepaste regels.

- **Actie:** definieert hoe u een aanvraag routeren als een WAF-regel is gekoppeld. U een van de onderstaande acties kiezen die u wilt toepassen wanneer een aanvraag overeenkomt met een aangepaste regel.

    - *Toestaan* - WAF stuurt de zoektocht naar de back-end, registreert een vermelding in WAF logs en uitgangen.
    - *Blok* - Aanvraag wordt geblokkeerd, WAF stuurt antwoord naar client zonder het verzoek door te sturen naar de back-end. WAF registreert een vermelding in WAF-logboeken.
    - *Log* - WAF registreert een vermelding in WAF-logboeken en blijft de volgende regel evalueren.
    - *Redirect* - WAF verwijst verzoek om naar een opgegeven URI, registreert een vermelding in WAF-logboeken en sluit af.

- **Wedstrijdvoorwaarde:** definieert een wedstrijdvariabele, een operator en de wedstrijdwaarde. Elke regel kan meerdere wedstrijdvoorwaarden bevatten. Een overeenkomstvoorwaarde kan worden gebaseerd op geolocatie, IP-adressen (CIDR) van de client, grootte of tekenreeksovereenkomst. String match kan worden tegen een lijst van wedstrijd variabelen.
  - **Wedstrijdvariabele:**
    - Aanvraagmethode
    - QueryString
    - PostArgs (PostArgs)
    - RequestUri
    - RequestHeader
    - RequestBody RequestBody
    - Cookies
  - **Operator:**
    - Elke: wordt vaak gebruikt om standaardactie te definiëren als er geen regels zijn afgestemd. Elke is een match alle operator.
    - Is gelijk aan
    - Contains
    - LessThan: beperking van de grootte
    - GreaterThan: beperking van de grootte
    - LessThanOrEqual: beperking van de grootte
    - GreaterThanOrEqual: beperking van de grootte
    - BeginsWith
    - EndsWith
    - Regex
  
  - **Regex** ondersteunt de volgende bewerkingen niet: 
    - Backreferences en het vastleggen van subexpressies
    - Willekeurige beweringen met nulbreedte
    - Subroutinematige verwijzingen en recursieve patronen
    - Voorwaardelijke patronen
    - Backtracking controlewerkwoorden
    - De richtlijn \C single-byte
    - De \R newline match richtlijn
    - De richtlijn \K begin van het opnieuw instellen van de wedstrijd
    - Callouts en ingesloten code
    - Atoomgroepering en bezitterige kwantificeerders

  - **Ontkennen [optioneel]:** U de *ontmante* voorwaarde op true instellen als het resultaat van een aandoening moet worden ontkend.
      
  - **Transformeren [optioneel]:** Een lijst met tekenreeksen met namen van transformaties die moeten worden uitgevoerd voordat de overeenkomst wordt geprobeerd. Dit kunnen de volgende transformaties zijn:
     - Hoofdletters 
     - Kleine letters
     - Trim
     - Nulls verwijderen
     - UrlDecode
     - UrlEncode
     
   - **Wedstrijdwaarde:** Ondersteunde HTTP-aanvraagmethodewaarden zijn:
     - GET
     - POST
     - PUT
     - HEAD
     - DELETE
     - Lock
     - Ontgrendelen
     - PROFIEL
     - Opties
     - PROPFIND
     - PROPPATCH
     - MKCOL (MKCOL)
     - Kopiëren
     - Verplaatsen

## <a name="examples"></a>Voorbeelden

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Voorbeeld van aangepaste WAF-regels op basis van http-parameters

Hier is een voorbeeld dat de configuratie van een aangepaste regel met twee overeenkomende voorwaarden weergeeft. Aanvragen zijn afkomstig van een opgegeven site zoals gedefinieerd door de verwijzer en querytekenreeks bevat geen 'wachtwoord'.

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
Een voorbeeldconfiguratie voor het blokkeren van de "PUT"-methode wordt hieronder weergegeven:

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>Beperking van de grootte

U een aangepaste regel bouwen die een beperking van de grootte opgeeft voor een deel van een binnenkomende aanvraag. Onder regel wordt bijvoorbeeld een url van meer dan 100 tekens verwijderd.

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>Volgende stappen
- [Een Web Application Firewall-beleid configureren met Azure PowerShell](waf-front-door-custom-rules-powershell.md) 
- Meer informatie over [webapplicationfirewall met voordeur](afds-overview.md)
- Lees hoe u [een Front Door maakt](../../frontdoor/quickstart-create-front-door.md).

