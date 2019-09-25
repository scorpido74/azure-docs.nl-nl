---
title: Azure-toepassing gateway Web Application Firewall CRS-regel groepen en-regels
description: Deze pagina bevat informatie over Web Application Firewall CRS-regel groepen en-regels.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 4/11/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 9f90f373bd6f1cfd34de1605783bf3a7f0185f4c
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240344"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>CRS-regel groepen en-regels voor Web Application firewall

Application Gateway Web Application Firewall (WAF) beschermt webtoepassingen tegen veelvoorkomende beveiligings problemen en aanvallen. Dit doet u door regels die zijn gedefinieerd op basis van de OWASP core Rule sets 3,0 of 2.2.9. Deze regels kunnen per regel worden uitgeschakeld. Dit artikel bevat de huidige regels en Rules die worden aangeboden.

De volgende regel groepen en regels zijn beschikbaar wanneer u Application Gateway gebruikt met Web Application Firewall.

# <a name="owasp-30tabowasp3"></a>[OWASP 3.0](#tab/owasp3)

## <a name="owasp30"></a>Regel sets

### <a name="General"></a> <p x-ms-format-detection="none">Algemeen</p>

|ruleId|Description|
|---|---|
|200004|Mogelijke meerdelige niet-overeenkomende grens.|

### <a name="crs911"></a> <p x-ms-format-detection="none">AANVRAAG-911-METHODE AFDWINGEN</p>

|ruleId|Description|
|---|---|
|911100|De methode is niet toegestaan door het beleid|


### <a name="crs913"></a> <p x-ms-format-detection="none">AANVRAAG-913-SCANNER-DETECTIE</p>

|ruleId|Description|
|---|---|
|913100|Gebruikers agent gevonden die aan beveiligings scanner is gekoppeld|
|913110|De aanvraag header is gevonden die is gekoppeld aan de beveiligings scanner|
|913120|Bestands naam/argument van aanvraag gevonden die is gekoppeld aan beveiligings scanner|
|913101|Gebruikers agent gevonden die is gekoppeld aan scripting/algemene HTTP-client|
|913102|Gebruikers agent gevonden die is gekoppeld aan web crawler/bot|

### <a name="crs920"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|ruleId|Description|
|---|---|
|920100|Ongeldige regel voor HTTP-aanvraag|
|920130|De aanvraag tekst kan niet worden geparseerd.|
|920140|Strikte validatie van de aanvraag tekst voor meerdelige is mislukt|
|920160|De HTTP-header van de content-length is niet numeriek.|
|920170|GET-of HEAD-aanvraag met hoofd inhoud.|
|920180|POST-aanvraag mist header content-length.|
|920190|Range = ongeldige waarde voor laatste byte.|
|920210|Er zijn meerdere/conflicterende header gegevens gevonden.|
|920220|Poging tot misbruik van URL-code ring|
|920240|Poging tot misbruik van URL-code ring|
|920250|Poging tot misbruik van UTF8-code ring|
|920260|Volledige/halve breedte van Unicode misbruik van aanvallen|
|920270|Ongeldig teken in aanvraag (null-teken)|
|920280|Aanvraag voor ontbrekende host-header|
|920290|Lege host-header|
|920310|De aanvraag bevat een lege Accept-header|
|920311|De aanvraag bevat een lege Accept-header|
|920330|Lege koptekst gebruikers agent|
|920340|Aanvraag met inhoud, maar ontbrekende content-type-header|
|920350|Host-header is een numeriek IP-adres|
|920380|De aanvraag heeft te veel argumenten|
|920360|Argument naam is te lang|
|920370|Argument waarde te lang|
|920390|Totale grootte van argumenten is overschreden|
|920400|Geüploade bestands grootte te groot|
|920410|Totale grootte van geüploade bestanden te groot|
|920420|Het inhouds type van de aanvraag is niet toegestaan door het beleid|
|920430|De versie van het HTTP-protocol wordt niet toegestaan door het beleid|
|920440|De extensie van het URL-bestand wordt beperkt door het beleid|
|920450|De HTTP-header wordt beperkt door het beleid (% @ {MATCHED_VAR})|
|920200|Range = te veel velden (6 of meer)|
|920201|Range = te veel velden voor PDF-aanvraag (35 of meer)|
|920230|Meerdere URL-code ring gedetecteerd|
|920300|Aanvraag waarbij een Accept-header ontbreekt|
|920271|Ongeldig teken in de aanvraag (niet-afdruk bare tekens)|
|920320|Ontbrekende koptekst gebruikers agent|
|920272|Ongeldig teken in de aanvraag (buiten afdruk bare tekens onder ASCII 127)|
|920202|Range = te veel velden voor PDF-aanvraag (6 of meer)|
|920273|Ongeldig teken in aanvraag (buiten strikt ingesteld)|
|920274|Ongeldig teken in de aanvraag headers (buiten een zeer strikte set)|
|920460|Abnormale escape tekens|

### <a name="crs921"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|ruleId|Description|
|---|---|
|921100|HTTP-aanvraag smuggling-aanval.|
|921110|HTTP-aanvraag smuggling-aanval|
|921120|HTTP-antwoord voor het splitsen van een aanval|
|921130|HTTP-antwoord voor het splitsen van een aanval|
|921140|Aanval via kopteksten via HTTP-headers|
|921150|HTTP-header-injectie aanval via Payload (CR/LF)|
|921160|HTTP-header-injectie aanval via Payload (CR/LF en header-name gedetecteerd)|
|921151|HTTP-header-injectie aanval via Payload (CR/LF)|
|921170|HTTP-parameter vervuiling|
|921180|HTTP-parameter vervuiling (% @ {TX. 1})|

### <a name="crs930"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|ruleId|Description|
|---|---|
|930100|Pad overs passage (/. /)|
|930110|Pad overs passage (/. /)|
|930120|Poging tot toegang tot besturings systeem bestand|
|930130|Poging tot beperkte bestands toegang|

### <a name="crs931"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|ruleId|Description|
|---|---|
|931100|Mogelijke RFI-aanval (Remote File Include) = URL-para meter met IP-adres|
|931110|Mogelijke RFI-aanval (Remote File Include) = algemene RFI-kwets bare parameter naam die wordt gebruikt met de URL-nettolading|
|931120|Mogelijke RFI-aanval (Remote File Include) = URL Payload gebruikt met het vraag teken (?)|
|931130|Mogelijke RFI-aanval (Remote File Include) = niet-domein referentie/koppeling|

### <a name="crs932"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|ruleId|Description|
|---|---|
|932120|Externe opdracht uitvoering = Windows Power shell-opdracht gevonden|
|932130|Externe opdracht uitvoering = Unix-shell-expressie gevonden|
|932140|Externe opdracht uitvoering = Windows voor/als opdracht gevonden|
|932160|Externe opdracht uitvoering = Unix shell-code gevonden|
|932170|Uitvoering van externe opdracht = Shellshock (CVE-2014-6271)|
|932171|Uitvoering van externe opdracht = Shellshock (CVE-2014-6271)|

### <a name="crs933"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|ruleId|Description|
|---|---|
|933100|PHP-injectie aanval = openen/sluiten tag gevonden|
|933110|PHP-injectie aanval = PHP-script bestand gevonden|
|933120|PHP-injectie aanval = configuratie-instructie gevonden|
|933130|PHP-injectie aanval = variabelen gevonden|
|933150|Een PHP-injectie aanval = High-Risk PHP-functie naam gevonden|
|933160|PHP-injectie aanval = een PHP-functie aanroep met een hoog risico gevonden|
|933180|PHP-injectie aanval = variabele functie aanroep gevonden|
|933151|PHP-injectie aanval = middel grote risico PHP-functie naam gevonden|
|933131|PHP-injectie aanval = variabelen gevonden|
|933161|PHP-injectie aanval = lage waarde PHP-functie aanroep gevonden|
|933111|PHP-injectie aanval = PHP-script bestand gevonden|

### <a name="crs941"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|ruleId|Description|
|---|---|
|941100|XSS-aanval gedetecteerd via libinjection|
|941110|XSS-filter-categorie 1 = script label vector|
|941130|XSS-filter-categorie 3 = kenmerk vector|
|941140|XSS-filter-categorie 4 = java script-URI-vector|
|941150|XSS-filter-categorie 5 = niet-toegestane HTML-kenmerken|
|941180|Knooppunt: validatie van zwarte woorden lijst|
|941190|XSS met Style Sheets|
|941200|XSS met behulp van VML-frames|
|941210|XSS met verborgen java script|
|941220|XSS met behulp van een verborgen VB-script|
|941230|XSS met behulp van de tag embed|
|941240|XSS met het kenmerk ' Import ' of ' implementatie '|
|941260|XSS met de tag ' meta '|
|941270|XSS met ' link ' href|
|941280|XSS met ' basis code '|
|941290|XSS met de tag ' applet '|
|941300|XSS met de tag object|
|941310|US-ASCII-verkeerd ingedeelde code ring XSS-filter-aanval gedetecteerd.|
|941330|IE-XSS-filters-aanval gedetecteerd.|
|941340|IE-XSS-filters-aanval gedetecteerd.|
|941350|UTF-7-code ring IE XSS-aanval gedetecteerd.|
|941320|Mogelijke XSS-aanval gedetecteerd-HTML-label-handler|

### <a name="crs942"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|ruleId|Description|
|---|---|
|942100|SQL-injectie aanval gedetecteerd via libinjection|
|942110|SQL-injectie aanval: Algemene injectie tests gedetecteerd|
|942130|SQL-injectie aanval: SQL-Tautology gedetecteerd.|
|942140|SQL-injectie aanval = common DB-namen gedetecteerd|
|942160|Detecteert blind sqli tests met behulp van de slaap stand () of Bench Mark ().|
|942170|Detecteert SQL-benchmark-en slaapstandtoets-injectie pogingen, inclusief voorwaardelijke query's|
|942190|Detecteert pogingen voor het uitvoeren van MSSQL-code en het verzamelen van informatie|
|942200|MySQL-commentaar gedetecteerd-/Space-obfuscated-injecties en apostroffen-beëindiging|
|942230|Detecteert voorwaardelijke SQL-injectie pogingen|
|942260|Detecteert eenvoudige SQL-verificatie pogingen 2/3|
|942270|Er wordt gezocht naar eenvoudige SQL-injectie. Veelvoorkomende aanvals teken reeks voor mysql Oracle en andere.|
|942290|Hiermee vindt u eenvoudige MongoDB SQL-injectie pogingen|
|942300|Detecteert MySQL-opmerkingen, voor waarden en CH (a) r-injecties|
|942310|Detecteert geketende SQL-injectie pogingen 2/2|
|942320|Detecteert MySQL en opgeslagen procedure/functie-injecties van PostgreSQL|
|942330|Detecteert de klassieke SQL-injectie in protestes 1/2|
|942340|Detecteert eenvoudige SQL-verificatie pogingen 3/3|
|942350|Detecteert MySQL UDF-injectie en andere pogingen tot het manipuleren van gegevens/structuren|
|942360|Detecteert aaneengeschakelde Basic SQL-injectie en SQLLFI-pogingen|
|942370|Detecteert de klassieke SQL-injectie in protestes 2/2|
|942150|SQL-injectie aanval|
|942410|SQL-injectie aanval|
|942430|Beperkte afwijkings detectie voor SQL-tekens (argumenten): aantal speciale tekens is overschreden (12)|
|942440|SQL-commentaar reeks gedetecteerd.|
|942450|SQL hex-code ring geïdentificeerd|
|942251|Detecteert of er injecties zijn|
|942460|Waarschuwing voor anomalie detectie van meta tekens-terugkerende niet-woord tekens|

### <a name="crs943"></a> <p x-ms-format-detection="none">AANVRAAG-943-TOEPASSING-AANVAL-SESSIE-VASTLEG GING</p>

|ruleId|Description|
|---|---|
|943100|Mogelijke sessie-bindings aanval = cookie waarden instellen in HTML|
|943110|Mogelijke sessie-bindings aanval = SessionID-parameter naam met niet-domein verwijzing|
|943120|Mogelijke sessie-bindings aanval = SessionID-parameter naam zonder verwijzing|

# <a name="owasp-229tabowasp2"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="owasp229"></a>Regel sets

### <a name="crs20"></a>crs_20_protocol_violations

|ruleId|Description|
|---|---|
|960911|Ongeldige regel voor HTTP-aanvraag|
|981227|Apache-fout = ongeldige URI in aanvraag.|
|960912|De aanvraag tekst kan niet worden geparseerd.|
|960914|Strikte validatie van de aanvraag tekst voor meerdelige is mislukt|
|960915|De meerdelige parser heeft een mogelijke niet-overeenkomende grens gedetecteerd.|
|960016|De HTTP-header van de content-length is niet numeriek.|
|960011|GET-of HEAD-aanvraag met hoofd inhoud.|
|960012|POST-aanvraag mist header content-length.|
|960902|Ongeldig gebruik van id-code ring.|
|960022|De verwachte header is niet toegestaan voor HTTP 1,0.|
|960020|De header Pragma vereist Cache-Control voor HTTP/1.1-aanvragen.|
|958291|Het veld Range = bestaat en begint met 0.|
|958230|Range = ongeldige waarde voor laatste byte.|
|958295|Er zijn meerdere/conflicterende header gegevens gevonden.|
|950107|Poging tot misbruik van URL-code ring|
|950109|Meerdere URL-code ring gedetecteerd|
|950108|Poging tot misbruik van URL-code ring|
|950801|Poging tot misbruik van UTF8-code ring|
|950116|Volledige/halve breedte van Unicode misbruik van aanvallen|
|960901|Ongeldig teken in de aanvraag|
|960018|Ongeldig teken in de aanvraag|

### <a name="crs21"></a> crs_21_protocol_anomalies

|ruleId|Description|
|---|---|
|960008|Aanvraag voor ontbrekende host-header|
|960007|Lege host-header|
|960015|Aanvraag waarbij een Accept-header ontbreekt|
|960021|De aanvraag bevat een lege Accept-header|
|960009|Aanvraag voor ontbrekende header van gebruikers agent|
|960006|Lege koptekst gebruikers agent|
|960904|Aanvraag met inhoud, maar ontbrekende content-type-header|
|960017|Host-header is een numeriek IP-adres|

### <a name="crs23"></a>crs_23_request_limits

|ruleId|Description|
|---|---|
|960209|Argument naam is te lang|
|960208|Argument waarde te lang|
|960335|De aanvraag heeft te veel argumenten|
|960341|Totale grootte van argumenten is overschreden|
|960342|Geüploade bestands grootte te groot|
|960343|Totale grootte van geüploade bestanden te groot|

### <a name="crs30"></a>crs_30_http_policy

|ruleId|Description|
|---|---|
|960032|De methode is niet toegestaan door het beleid|
|960010|Het inhouds type van de aanvraag is niet toegestaan door het beleid|
|960034|De versie van het HTTP-protocol wordt niet toegestaan door het beleid|
|960035|De extensie van het URL-bestand wordt beperkt door het beleid|
|960038|De HTTP-header wordt beperkt door het beleid|

### <a name="crs35"></a>crs_35_bad_robots

|ruleId|Description|
|---|---|
|990002|Aanvraag geeft aan dat een beveiligings scanner de site heeft gescand|
|990901|Aanvraag geeft aan dat een beveiligings scanner de site heeft gescand|
|990902|Aanvraag geeft aan dat een beveiligings scanner de site heeft gescand|
|990012|Webcrawler voor Rogue web site|

### <a name="crs40"></a>crs_40_generic_attacks

|ruleId|Description|
|---|---|
|960024|Waarschuwing voor anomalie detectie van meta tekens-terugkerende niet-woord tekens|
|950008|Injectie van niet-gedocumenteerde ColdFusion-Tags|
|950010|LDAP-injectie aanval|
|950011|SSI-injectie aanval|
|950018|URL van Universal PDF XSS gedetecteerd.|
|950019|Aanval via e-mail injecteren|
|950012|HTTP-aanvraag smuggling-aanval.|
|950910|HTTP-antwoord voor het splitsen van een aanval|
|950911|HTTP-antwoord voor het splitsen van een aanval|
|950117|Aanval op externe bestands opname|
|950118|Aanval op externe bestands opname|
|950119|Aanval op externe bestands opname|
|950120|Mogelijke RFI-aanval (Remote File Include) = niet-domein referentie/koppeling|
|981133|Regel 981133|
|981134|Regel 981134|
|950009|Aanval op sessie binding|
|950003|Sessie-vastleg ging|
|950000|Sessie-vastleg ging|
|950005|Poging tot externe bestands toegang|
|950002|Toegang tot de systeem opdracht|
|950006|Systeem opdracht injectie|
|959151|PHP-injectie aanval|
|958976|PHP-injectie aanval|
|958977|PHP-injectie aanval|

### <a name="crs41sql"></a> crs_41_sql_injection_attacks

|ruleId|Description|
|---|---|
|981231|SQL-commentaar reeks gedetecteerd.|
|981260|SQL hex-code ring geïdentificeerd|
|981320|SQL-injectie aanval = common DB-namen gedetecteerd|
|981300|Regel 981300|
|981301|Regel 981301|
|981302|Regel 981302|
|981303|Regel 981303|
|981304|Regel 981304|
|981305|Regel 981305|
|981306|Regel 981306|
|981307|Regel 981307|
|981308|Regel 981308|
|981309|Regel 981309|
|981310|Regel 981310|
|981311|Regel 981311|
|981312|Regel 981312|
|981313|Regel 981313|
|981314|Regel 981314|
|981315|Regel 981315|
|981316|Regel 981316|
|981317|Waarschuwing voor afwijkings detectie van de SQL-instructie SELECT|
|950007|Aanval op een blinde SQL-injectie|
|950001|SQL-injectie aanval|
|950908|SQL-injectie aanval.|
|959073|SQL-injectie aanval|
|981272|Detecteert blind sqli tests met behulp van de slaap stand () of Bench Mark ().|
|981250|Detecteert SQL-benchmark-en slaapstandtoets-injectie pogingen, inclusief voorwaardelijke query's|
|981241|Detecteert voorwaardelijke SQL-injectie pogingen|
|981276|Er wordt gezocht naar eenvoudige SQL-injectie. Veelvoorkomende aanvals teken reeks voor mysql Oracle en andere.|
|981270|Hiermee vindt u eenvoudige MongoDB SQL-injectie pogingen|
|981253|Detecteert MySQL en opgeslagen procedure/functie-injecties van PostgreSQL|
|981251|Detecteert MySQL UDF-injectie en andere pogingen tot het manipuleren van gegevens/structuren|

### <a name="crs41xss"></a>crs_41_xss_attacks

|ruleId|Description|
|---|---|
|973336|XSS-filter-categorie 1 = script label vector|
|973338|XSS-filter-categorie 3 = java script-URI-vector|
|981136|Regel 981136|
|981018|Regel 981018|
|958016|XSS-aanvallen (cross-site scripting)|
|958414|XSS-aanvallen (cross-site scripting)|
|958032|XSS-aanvallen (cross-site scripting)|
|958026|XSS-aanvallen (cross-site scripting)|
|958027|XSS-aanvallen (cross-site scripting)|
|958054|XSS-aanvallen (cross-site scripting)|
|958418|XSS-aanvallen (cross-site scripting)|
|958034|XSS-aanvallen (cross-site scripting)|
|958019|XSS-aanvallen (cross-site scripting)|
|958013|XSS-aanvallen (cross-site scripting)|
|958408|XSS-aanvallen (cross-site scripting)|
|958012|XSS-aanvallen (cross-site scripting)|
|958423|XSS-aanvallen (cross-site scripting)|
|958002|XSS-aanvallen (cross-site scripting)|
|958017|XSS-aanvallen (cross-site scripting)|
|958007|XSS-aanvallen (cross-site scripting)|
|958047|XSS-aanvallen (cross-site scripting)|
|958410|XSS-aanvallen (cross-site scripting)|
|958415|XSS-aanvallen (cross-site scripting)|
|958022|XSS-aanvallen (cross-site scripting)|
|958405|XSS-aanvallen (cross-site scripting)|
|958419|XSS-aanvallen (cross-site scripting)|
|958028|XSS-aanvallen (cross-site scripting)|
|958057|XSS-aanvallen (cross-site scripting)|
|958031|XSS-aanvallen (cross-site scripting)|
|958006|XSS-aanvallen (cross-site scripting)|
|958033|XSS-aanvallen (cross-site scripting)|
|958038|XSS-aanvallen (cross-site scripting)|
|958409|XSS-aanvallen (cross-site scripting)|
|958001|XSS-aanvallen (cross-site scripting)|
|958005|XSS-aanvallen (cross-site scripting)|
|958404|XSS-aanvallen (cross-site scripting)|
|958023|XSS-aanvallen (cross-site scripting)|
|958010|XSS-aanvallen (cross-site scripting)|
|958411|XSS-aanvallen (cross-site scripting)|
|958422|XSS-aanvallen (cross-site scripting)|
|958036|XSS-aanvallen (cross-site scripting)|
|958000|XSS-aanvallen (cross-site scripting)|
|958018|XSS-aanvallen (cross-site scripting)|
|958406|XSS-aanvallen (cross-site scripting)|
|958040|XSS-aanvallen (cross-site scripting)|
|958052|XSS-aanvallen (cross-site scripting)|
|958037|XSS-aanvallen (cross-site scripting)|
|958049|XSS-aanvallen (cross-site scripting)|
|958030|XSS-aanvallen (cross-site scripting)|
|958041|XSS-aanvallen (cross-site scripting)|
|958416|XSS-aanvallen (cross-site scripting)|
|958024|XSS-aanvallen (cross-site scripting)|
|958059|XSS-aanvallen (cross-site scripting)|
|958417|XSS-aanvallen (cross-site scripting)|
|958020|XSS-aanvallen (cross-site scripting)|
|958045|XSS-aanvallen (cross-site scripting)|
|958004|XSS-aanvallen (cross-site scripting)|
|958421|XSS-aanvallen (cross-site scripting)|
|958009|XSS-aanvallen (cross-site scripting)|
|958025|XSS-aanvallen (cross-site scripting)|
|958413|XSS-aanvallen (cross-site scripting)|
|958051|XSS-aanvallen (cross-site scripting)|
|958420|XSS-aanvallen (cross-site scripting)|
|958407|XSS-aanvallen (cross-site scripting)|
|958056|XSS-aanvallen (cross-site scripting)|
|958011|XSS-aanvallen (cross-site scripting)|
|958412|XSS-aanvallen (cross-site scripting)|
|958008|XSS-aanvallen (cross-site scripting)|
|958046|XSS-aanvallen (cross-site scripting)|
|958039|XSS-aanvallen (cross-site scripting)|
|958003|XSS-aanvallen (cross-site scripting)|
|973300|Mogelijke XSS-aanval gedetecteerd-HTML-label-handler|
|973301|XSS-aanval gedetecteerd|
|973302|XSS-aanval gedetecteerd|
|973303|XSS-aanval gedetecteerd|
|973304|XSS-aanval gedetecteerd|
|973305|XSS-aanval gedetecteerd|
|973306|XSS-aanval gedetecteerd|
|973307|XSS-aanval gedetecteerd|
|973308|XSS-aanval gedetecteerd|
|973309|XSS-aanval gedetecteerd|
|973311|XSS-aanval gedetecteerd|
|973313|XSS-aanval gedetecteerd|
|973314|XSS-aanval gedetecteerd|
|973331|IE-XSS-filters-aanval gedetecteerd.|
|973315|IE-XSS-filters-aanval gedetecteerd.|
|973330|IE-XSS-filters-aanval gedetecteerd.|
|973327|IE-XSS-filters-aanval gedetecteerd.|
|973326|IE-XSS-filters-aanval gedetecteerd.|
|973346|IE-XSS-filters-aanval gedetecteerd.|
|973345|IE-XSS-filters-aanval gedetecteerd.|
|973324|IE-XSS-filters-aanval gedetecteerd.|
|973323|IE-XSS-filters-aanval gedetecteerd.|
|973348|IE-XSS-filters-aanval gedetecteerd.|
|973321|IE-XSS-filters-aanval gedetecteerd.|
|973320|IE-XSS-filters-aanval gedetecteerd.|
|973318|IE-XSS-filters-aanval gedetecteerd.|
|973317|IE-XSS-filters-aanval gedetecteerd.|
|973329|IE-XSS-filters-aanval gedetecteerd.|
|973328|IE-XSS-filters-aanval gedetecteerd.|

### <a name="crs42"></a>crs_42_tight_security

|ruleId|Description|
|---|---|
|950103|Inbreuk op pad|

### <a name="crs45"></a>crs_45_trojans

|ruleId|Description|
|---|---|
|950110|Back-uptoegang|
|950921|Back-uptoegang|
|950922|Back-uptoegang|

---

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het uitschakelen van WAF-regels: [WAF-regels aanpassen](application-gateway-customize-waf-rules-portal.md)
