---
title: Aangepaste regels voor Azure Web Application Firewall (WAF) v2
description: Dit artikel bevat een overzicht van de aangepaste regels voor Web Application Firewall (WAF) v2 in Azure-toepassing gateway.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 8ae5c9b6b52ea13e3d0981664e8c920cc5b47a01
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263563"
---
# <a name="overview-custom-rules-for-web-application-firewall-v2"></a>Overzicht: aangepaste regels voor Web Application Firewall v2

Azure-toepassing gateway Web Application Firewall (WAF) v2 wordt geleverd met een vooraf geconfigureerde, door het platform beheerde regelset die bescherming biedt tegen vele verschillende soorten aanvallen. Deze aanvallen omvatten cross-site scripting, SQL-injectie en andere. Als u een WAF-beheerder bent, wilt u mogelijk uw eigen regels schrijven om de regels voor de kern regelset te verbeteren. Uw regels kunnen het aangevraagde verkeer blok keren of toestaan op basis van de overeenkomende criteria.

Met aangepaste regels kunt u uw eigen regels maken, die worden geëvalueerd voor elke aanvraag die via WAF wordt door gegeven. Deze regels bevatten een hogere prioriteit dan de rest van de regels in de beheerde regel sets. De aangepaste regels bevatten een regel naam, een regel prioriteit en een matrix met overeenkomende voor waarden. Als aan deze voor waarden wordt voldaan, wordt er een actie uitgevoerd om toe te staan of te blok keren.

U kunt bijvoorbeeld een regel maken om alle aanvragen van een IP-adres in het bereik 192.168.5.4/24 te blok keren. In deze regel is de operator *IPMatch*, *matchValues* het IP-adres bereik (192.168.5.4/24), en *actie* is het blok keren van het verkeer. U kunt ook de naam en prioriteit van de regel instellen.

Aangepaste regels bieden ondersteuning voor het gebruik van samengestelde logica om geavanceerde regels te maken die voldoen aan uw beveiligings behoeften. Bijvoorbeeld: "(voor waarde 1 *en* voor waarde 2) *of* voor waarde 3)" betekent dat als aan de voor waarde 1 *en* voor waarde 2 wordt voldaan, *of* als aan voor waarde 3 wordt voldaan, de actie moet worden opgegeven in de aangepaste regel.

Verschillende overeenkomende voor waarden binnen dezelfde regel worden altijd samengesteld met behulp van *en*. Een regel die bijvoorbeeld gebruikmaakt van *en* kan opgeven dat verkeer van een bepaald IP-adres moet worden geblokkeerd en alleen als er een bepaalde browser wordt gebruikt.

Als u *of* voor twee verschillende voor waarden wilt gebruiken, moeten de twee voor waarden in verschillende regels zijn. De regel die bijvoorbeeld gebruikmaakt van *of* kan opgeven dat verkeer van een bepaald IP-adres wordt geblokkeerd of dat verkeer wordt geblokkeerd als er een bepaalde browser wordt gebruikt.

> [!NOTE]
> Het maximum aantal aangepaste WAF-regels is 100. Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-subscription-service-limits.md#application-gateway-limits)voor meer informatie over Application Gateway limieten.

Reguliere expressies worden ook ondersteund in aangepaste regels, net zoals ze in de kern regel sets staan. Zie voor voor beelden van deze regels "voor beeld 3" en "voor beeld 5" in [aangepaste Web Application firewall regels maken en gebruiken](create-custom-waf-rules.md).

## <a name="allowing-or-blocking-traffic"></a>Verkeer toestaan of blok keren

Het toestaan of blok keren van verkeer is eenvoudig met aangepaste regels. U kunt bijvoorbeeld al het verkeer dat afkomstig is van een bereik van IP-adressen blok keren. U kunt een andere regel maken om verkeer toe te staan als de aanvraag afkomstig is uit een bepaalde browser.

Als u iets wilt toestaan, moet u ervoor zorgen dat de para meter `-Action` is ingesteld op **toestaan**. Als u iets wilt blok keren, moet u ervoor zorgen dat de para meter `-Action` is ingesteld op **blok keren**, zoals wordt weer gegeven in de volgende code:

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

De voor gaande `$BlockRule` verwijst naar de volgende aangepaste regel in Azure Resource Manager:

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

Deze aangepaste regel bevat een naam, een prioriteit, een actie en een matrix met overeenkomende voor waarden waaraan moet worden voldaan voordat de actie wordt uitgevoerd. Zie de volgende secties voor beschrijvingen van de velden met aangepaste regels. Zie [aangepaste Web Application firewall regels maken en gebruiken](create-custom-waf-rules.md)voor voor beelden van aangepaste regels.

## <a name="custom-rule-fields"></a>Aangepaste regel velden

### <a name="name-optional"></a>Naam (optioneel)

Dit is de naam van de regel. De naam wordt weer gegeven in de logboeken.

### <a name="priority-required"></a>Prioriteit (vereist)

- De prioriteit bepaalt de volg orde waarin de regels worden geëvalueerd. Hoe lager de waarde, hoe eerder de evaluatie van de regel. Het toegestane bereik is 1 tot en met 100.
- De prioriteit moet uniek zijn voor alle aangepaste regels. Een regel met een prioriteit van 40 wordt geëvalueerd vóór een regel met een prioriteit van 80.

### <a name="rule-type-required"></a>Regel type (vereist)

Op dit moment moet het regel type **MatchRule**zijn.

### <a name="match-variable-required"></a>Overeenkomende variabele (vereist)

De overeenkomende variabele moet een van de volgende zijn:

- RemoteAddr: het IP-adres of de hostnaam van de verbinding met de externe computer
- RequestMethod: de HTTP-aanvraag methode (GET, POST, PUT, DELETE, enzovoort).
- Query string: de variabele in de URI.
- PostArgs: de argumenten die worden verzonden in de hoofd tekst van het bericht. Aangepaste regels die gebruikmaken van deze overeenkomende variabele worden alleen toegepast als de header content-type is ingesteld op ' Application/x-www-form-urlencoded ' en ' meerdelige/form-data '.
- RequestUri: de URI van de aanvraag.
- RequestHeaders: de headers van de aanvraag.
- RequestBody: de variabele die de volledige hoofd tekst van de aanvraag als geheel bevat. Aangepaste regels die gebruikmaken van deze overeenkomende variabele worden alleen toegepast als de header content-type is ingesteld op ' Application/x-www-form-urlencoded '. 
- RequestCookies: de cookies van de aanvraag.

### <a name="selector-optional"></a>Selector (optioneel)

De selector beschrijft het veld van de matchVariable-verzameling. Als de matchVariable bijvoorbeeld ' RequestHeaders ' is, kan de selector zich op de header van de gebruiker-Agent bevindt.

### <a name="operator-required"></a>Operator (vereist)

De operator moet een van de volgende zijn:

- IPMatch: deze operator wordt alleen gebruikt als de match-variabele *RemoteAddr*is.
- Is gelijk aan: de invoer is hetzelfde als de MatchValue.
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- reguliere

### <a name="negate-condition-optional"></a>Voor waarde voor negatie (optioneel)

De huidige voor waarde wordt genegeerd.

### <a name="transform-optional"></a>Transformeren (optioneel)

Een lijst met teken reeksen met de namen van trans formaties die moeten worden voltooid voordat de overeenkomst wordt geprobeerd. De trans formaties omvatten:

- Kleine letters
- Interne
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Overeenkomende waarden (vereist)

Het veld matchValues is een lijst met waarden die moeten worden vergeleken, wat kan worden beschouwd als *or*' Ed '. De waarden kunnen bijvoorbeeld IP-adressen of andere teken reeksen zijn. De notatie van de waarde is afhankelijk van de vorige operator.

### <a name="action-required"></a>Actie (vereist)

Het veld actie biedt de volgende opties: 

- Toestaan: de trans actie autoriseren en alle volgende regels overs Laan. Dit betekent dat de opgegeven aanvraag wordt toegevoegd aan de lijst met toegestane aanvragen en, nadat deze is gevonden, de aanvraag niet meer kan worden geëvalueerd en wordt verzonden naar de back-end-pool. Regels die zich op de acceptatie lijst bevinden, worden niet geëvalueerd voor verdere aangepaste regels of beheerde regels.

- Blok keren: Hiermee blokkeert u de trans actie op basis van *SecDefaultAction* (detectie/preventie modus). Net als de actie toestaan, nadat de aanvraag is geëvalueerd en toegevoegd aan de lijst met geblokkeerde websites, wordt de evaluatie gestopt en wordt de aanvraag geblokkeerd. Volgende aanvragen die aan dezelfde voor waarden voldoen, worden niet geëvalueerd. Ze worden alleen geblokkeerd. 

- Log: Hiermee kan de regel naar het logboek schrijven en kunnen de rest van de regels worden uitgevoerd voor evaluatie. Volgende aangepaste regels worden geëvalueerd in volg orde van prioriteit, gevolgd door de beheerde regels.

## <a name="next-steps"></a>Volgende stappen

Nu u over aangepaste regels hebt geleerd, kunt u [uw eigen aangepaste regels maken](create-custom-waf-rules.md).
