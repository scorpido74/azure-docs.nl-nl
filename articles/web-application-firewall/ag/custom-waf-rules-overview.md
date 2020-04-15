---
title: WAF-v2-regels (Azure Web Application Firewall) v2
description: In dit artikel vindt u een overzicht van waf-regels (Web Application Firewall) v2 op Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: c0f802f5113e38e811c110ee913099e76fa7be0b
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383817"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Aangepaste regels voor Web Application Firewall v2 op Azure Application Gateway

De WAF(Azure Application Gateway Web Application Firewall) v2 wordt geleverd met een vooraf geconfigureerde, door platforms beheerde regelset die bescherming biedt tegen veel verschillende soorten aanvallen. Deze aanvallen omvatten cross site scripting, SQL-injectie, en anderen. Als u een WAF-beheerder bent, u uw eigen regels schrijven om de basisregels (CRS)-regels uit te breiden. Uw regels kunnen aangevraagd verkeer blokkeren of toestaan op basis van overeenkomende criteria.

Met aangepaste regels u uw eigen regels maken die worden geëvalueerd voor elk verzoek dat door de WAF gaat. Deze regels hebben een hogere prioriteit dan de rest van de regels in de beheerde regelsets. De aangepaste regels bevatten een regelnaam, regelprioriteit en een reeks overeenkomende voorwaarden. Als aan deze voorwaarden is voldaan, wordt een actie ondernomen (om toe te staan of te blokkeren).

U bijvoorbeeld alle aanvragen blokkeren vanaf een IP-adres in het bereik 192.168.5.4/24. In deze regel is de operator *IPMatch,* de matchValues is het IP-adresbereik (192.168.5.4/24) en de actie is om het verkeer te blokkeren. U stelt ook de naam en prioriteit van de regel in.

Aangepaste regels ondersteunen het gebruik van samengestelde logica om meer geavanceerde regels te maken die voldoen aan uw beveiligingsbehoeften. Bijvoorbeeld (Voorwaarde 1 **en** Voorwaarde 2) **of** Voorwaarde 3). Dit betekent dat als aan voorwaarde 1 **en** voorwaarde 2 is voldaan **of** als voorwaarde 3 is voldaan, de WAF de actie moet ondernemen die is opgegeven in de aangepaste regel.

Verschillende matching voorwaarden binnen dezelfde regel worden altijd samengesteld met behulp van **en**. Blokkeer bijvoorbeeld verkeer vanaf een specifiek IP-adres en alleen als ze een bepaalde browser gebruiken.

Als u wilt **of** twee verschillende voorwaarden, moeten de twee voorwaarden in verschillende regels. Blokkeer bijvoorbeeld verkeer vanaf een specifiek IP-adres of blokkeer verkeer als ze een specifieke browser gebruiken.

> [!NOTE]
> Het maximum aantal WAF aangepaste regels is 100. Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)voor meer informatie over limieten voor toepassingsgateways.

Reguliere expressies worden ook ondersteund in aangepaste regels, net als in de CRS-regelsets. Zie Voorbeelden 3 en 5 voor voorbeelden in [Firewallregels voor webtoepassingen maken en gebruiken.](create-custom-waf-rules.md)

## <a name="allowing-vs-blocking"></a>Vs. blokkeren toestaan

Het toestaan en blokkeren van verkeer is eenvoudig met aangepaste regels. U bijvoorbeeld al het verkeer blokkeren dat afkomstig is van een reeks IP-adressen. U een andere regel maken om verkeer toe te staan als het verzoek afkomstig is van een specifieke browser.

Als u iets wilt `-Action` toestaan, moet u ervoor zorgen dat de parameter is ingesteld op **Toestaan**. Als u iets wilt `-Action` blokkeren, moet u ervoor zorgen dat de parameter is ingesteld op **Blokkeren.**

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

De `$BlockRule` vorige toewijzingen aan de volgende aangepaste regel in Azure Resource Manager:

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationConditon": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

Deze aangepaste regel bevat een naam, prioriteit, een actie en de reeks overeenkomende voorwaarden waaraan moet worden voldaan om de actie te laten plaatsvinden. Zie de volgende veldbeschrijvingen voor meer uitleg over deze velden. Zie Bijvoorbeeld aangepaste regels, zie [Firewallregels voor webtoepassingen maken en gebruiken](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Velden voor aangepaste regels

### <a name="name-optional"></a>Naam [optioneel]

De naam van de regel.  Het verschijnt in de logboeken.

### <a name="priority-required"></a>Prioriteit [vereist]

- Hiermee bepaalt u de regelwaarderingsvolgorde. Hoe lager de waarde, hoe eerder de evaluatie van de regel. Het toegestane bereik is van 1-100. 
- Moet uniek zijn in alle aangepaste regels. Een regel met prioriteit 40 wordt geëvalueerd vóór een regel met prioriteit 80.

### <a name="rule-type-required"></a>Regeltype [vereist]

Op dit moment moet **matchrule**zijn.

### <a name="match-variable-required"></a>Wedstrijdvariabele [vereist]

Moet een van de variabelen zijn:

- RemoteAddr – IP-adres/hostnaam van de externe computerverbinding
- AanvraagMethode – HTTP-aanvraagmethode (GET, POST, PUT, DELETE, enzovoort.)
- QueryString – Variabele in de URI
- PostArgs - Argumenten verzonden in de POST lichaam. Aangepaste regels met deze wedstrijdvariabele worden alleen toegepast als de koptekst 'Inhoud-type' is ingesteld op 'application/x-www-form-urlencoded' en 'multipart/form-data'.
- RequestUri – URI van het verzoek
- Kopteksten aanvragen – Kopteksten van de aanvraag
- RequestBody – Dit bevat de gehele aanvraaginstantie als geheel. Aangepaste regels met deze wedstrijdvariabele worden alleen toegepast als de koptekst 'Inhoud-type' is ingesteld op 'application/x-www-form-urlencoded'. 
- RequestCookies – Cookies van het verzoek

### <a name="selector-optional"></a>Kiezer [optioneel]

Beschrijft het veld van de matchVariable-verzameling. Als de matchVariable bijvoorbeeld RequestHeaders is, kan de kiezer zich op de header *User-Agent* begeven.

### <a name="operator-required"></a>Operator [vereist]

Moet een van de volgende marktdeelnemers zijn:

- IPMatch - alleen gebruikt wanneer Match Variable *RemoteAddr* is
- Gelijk – invoer is hetzelfde als de MatchValue
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex
- Geomatch (preview)

### <a name="negate-condition-optional"></a>Voorwaarde voor uitlaten [optioneel]

Ontkent de huidige toestand.

### <a name="transform-optional"></a>Transformeren [optioneel]

Een lijst met tekenreeksen met namen van transformaties die moeten worden uitgevoerd voordat de overeenkomst wordt geprobeerd. Dit kunnen de volgende transformaties zijn:

- Kleine letters
- Trim
- UrlDecode
- UrlEncode 
- Nulls verwijderen
- HtmlEntityDecode

### <a name="match-values-required"></a>Overeenkomen met waarden [vereist]

Lijst van waarden om tegen aan te passen, die kan worden gezien als *zijnde OF*'ed. Het kunnen bijvoorbeeld IP-adressen of andere tekenreeksen zijn. De waardenotatie is afhankelijk van de vorige operator.

### <a name="action-required"></a>Actie [vereist]

- Toestaan : hiermee machtigt u de transactie en slaat u alle andere regels over. De opgegeven aanvraag wordt toegevoegd aan de lijst met toegestane plaatsen en zodra deze overeenkomen, stopt de aanvraag de verdere evaluatie en wordt deze naar de backendpool verzonden. Regels die in de lijst met toegestane regels staan, worden niet geëvalueerd voor verdere aangepaste regels of beheerde regels.
- Blokkeren – Blokkeert de transactie op basis van *SecDefaultAction* (detectie/preventiemodus). Net als de actie Toestaan wordt de evaluatie, zodra de aanvraag is geëvalueerd en toegevoegd aan de bloklijst, gestopt en wordt de aanvraag geblokkeerd. Elk verzoek daarna voldoet aan dezelfde voorwaarden zal niet worden geëvalueerd en zal gewoon worden geblokkeerd. 
- Log - Laat de regel schrijven naar het logboek, maar laat de rest van de regels draaien voor evaluatie. De andere aangepaste regels worden geëvalueerd in volgorde van prioriteit, gevolgd door de beheerde regels.

## <a name="geomatch-custom-rules-preview"></a>Aangepaste regels voor Geomatch (voorbeeld)

Met aangepaste regels u aangepaste regels maken die voldoen aan de exacte behoeften van uw toepassingen en beveiligingsbeleid. U de toegang tot uw webapplicaties per land/regio beperken. Zie [Aangepaste regels voor Geomatch (voorbeeld) voor](geomatch-custom-rules.md)meer informatie.

## <a name="next-steps"></a>Volgende stappen

Nadat u meer te weten bent over aangepaste regels, [maakt u uw eigen aangepaste regels.](create-custom-waf-rules.md)
