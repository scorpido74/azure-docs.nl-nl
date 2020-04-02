---
title: CRS-regelgroepen en -regels
titleSuffix: Azure Web Application Firewall
description: Op deze pagina vindt u informatie over CRS-regelgroepen en -regels voor webtoepassingen.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 563aa701c1403a1ef26c6073496e7e59c7c5096c
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521847"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>CRS-regelgroepen en regels voor Web Application Firewall CRS

Application Gateway web application firewall (WAF) beschermt webapplicaties tegen veelvoorkomende kwetsbaarheden en exploits. Dit gebeurt aan de hand van regels die zijn gedefinieerd op basis van de OWASP-kernregelsets 3.1, 3.0 of 2.2.9. Deze regels kunnen regel regel voor regel worden uitgeschakeld. Dit artikel bevat de huidige regels en regelsets die worden aangeboden.

## <a name="core-rule-sets"></a>Core Rule Sets

De Application Gateway WAF wordt standaard voorgeconfigureerd met CRS 3.0. Maar u ervoor kiezen om CRS 3.1 of CRS 2.2.9 in plaats daarvan te gebruiken. CRS 3.1 biedt nieuwe regel sets te verdedigen tegen Java-infecties, een eerste set van het uploaden van bestanden controles, vaste false positives, en nog veel meer. CRS 3.0 biedt minder false positives in vergelijking met CRS 2.2.9. U ook [regels aanpassen aan uw behoeften.](application-gateway-customize-waf-rules-portal.md)

> [!div class="mx-imgBorder"]
> ![Regels beheren](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

De WAF beschermt tegen de volgende kwetsbaarheden op het web:

- SQL-injectieaanvallen
- Cross-site scripting-aanvallen
- Andere veelvoorkomende aanvallen, zoals commando-injectie, HTTP-verzoeksmokkel, HTTP-responssplitsing en opname van externe bestanden
- HTTP-protocolschendingen
- HTTP-protocolafwijkingen, zoals ontbrekende hostuser-agent en headers accepteren
- Bots, crawlers en scanners
- Algemene foutconfiguraties van toepassingen (bijvoorbeeld Apache en IIS)

### <a name="owasp-crs-31"></a>OWASP CRS 3.1

CRS 3.1 bevat 13 regelgroepen, zoals in de volgende tabel wordt weergegeven. Elke groep bevat meerdere regels, die kunnen worden uitgeschakeld.

|Regelgroep|Beschrijving|
|---|---|
|**[Algemeen](#general-31)**|Algemene groep|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|Lock-down methoden (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|Beschermen tegen poort- en milieuscanners|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-31)**|Beschermen tegen protocol- en coderingsproblemen|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|Beschermen tegen header injectie, verzoek smokkel, en reactie splitsen|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|Beschermen tegen bestands- en padaanvallen|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|Beschermen tegen RFI-aanvallen (Remote File Inclusion)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|Opnieuw aanvallen op externe code-uitvoering beveiligen|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|Beschermen tegen PHP-injectie-aanvallen|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|Beschermen tegen scriptaanvallen op verschillende plaatsen|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|Beschermen tegen SQL-injectie-aanvallen|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|Beschermen tegen aanvallen op sessiefixatie|
|**[REQUEST-944-APPLICATION-ATTACK-SESSION-JAVA](#crs944-31)**|Beschermen tegen JAVA-aanvallen|

### <a name="owasp-crs-30"></a>OWASP CRS 3.0

CRS 3.0 bevat 12 regelgroepen, zoals in de volgende tabel wordt weergegeven. Elke groep bevat meerdere regels, die kunnen worden uitgeschakeld.

|Regelgroep|Beschrijving|
|---|---|
|**[Algemeen](#general-30)**|Algemene groep|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|Lock-down methoden (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|Beschermen tegen poort- en milieuscanners|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-30)**|Beschermen tegen protocol- en coderingsproblemen|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|Beschermen tegen header injectie, verzoek smokkel, en reactie splitsen|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|Beschermen tegen bestands- en padaanvallen|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|Beschermen tegen RFI-aanvallen (Remote File Inclusion)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|Opnieuw aanvallen op externe code-uitvoering beveiligen|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|Beschermen tegen PHP-injectie-aanvallen|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|Beschermen tegen scriptaanvallen op verschillende plaatsen|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|Beschermen tegen SQL-injectie-aanvallen|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|Beschermen tegen aanvallen op sessiefixatie|

### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

CRS 2.2.9 bevat 10 regelgroepen, zoals in de volgende tabel wordt weergegeven. Elke groep bevat meerdere regels, die kunnen worden uitgeschakeld.

|Regelgroep|Beschrijving|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|Beschermen tegen protocolschendingen (zoals ongeldige tekens of een GET met een aanvraaginstantie)|
|**[crs_21_protocol_anomalies](#crs21)**|Beschermen tegen onjuiste header-informatie|
|**[crs_23_request_limits](#crs23)**|Beschermen tegen argumenten of bestanden die beperkingen overschrijden|
|**[crs_30_http_policy](#crs30)**|Beveiligen tegen beperkte methoden, kopteksten en bestandstypen|
|**[crs_35_bad_robots](#crs35)**|Beschermen tegen webcrawlers en scanners|
|**[crs_40_generic_attacks](#crs40)**|Bescherm tegen generieke aanvallen (zoals sessiefixatie, opname van externe bestanden en PHP-injectie)|
|**[crs_41_sql_injection_attacks](#crs41sql)**|Beschermen tegen SQL-injectie-aanvallen|
|**[crs_41_xss_attacks](#crs41xss)**|Beschermen tegen scriptaanvallen op verschillende plaatsen|
|**[crs_42_tight_security](#crs42)**|Beschermen tegen pad-traversal aanvallen|
|**[crs_45_trojans](#crs45)**|Bescherm tegen backdoor trojans|

De volgende regelgroepen en regels zijn beschikbaar wanneer u Web Application Firewall gebruikt op Application Gateway.

# <a name="owasp-31"></a>[OWASP 3.1](#tab/owasp31)

## <a name="rule-sets"></a><a name="owasp31"></a>Regelsets

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-31"></a> <p x-ms-format-detection="none">Algemeen</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|200004|Mogelijke meerdelige ongeëvenaarde grens.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-31"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|911100|Methode is niet toegestaan door beleid|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-31"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|913100|Gevonden user-agent die is gekoppeld aan beveiligingsscanner|
|913101|Gevonden user-agent die is gekoppeld aan scripting/generieke HTTP-client|
|913102|Gevonden user-agent die is gekoppeld aan webcrawler/bot|
|913110|Gevonden aanvraagkop die is gekoppeld aan beveiligingsscanner|
|913120|Gevonden aanvraagbestandsnaam/argument in verband met beveiligingsscanner|


### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-31"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|920100|Ongeldige HTTP-aanvraagregel|
|920120|Geprobeerde meerdelige/formuliergegevensbypass|
|920121|Geprobeerde meerdelige/formuliergegevensbypass|
|920130|Kan geen verzoeklichaam ontsmetten.|
|920140|Meerdelige aanvraaginstantie heeft strikte validatie niet doorbroken|
|920160|Http-header over inhoudslengte is niet numeriek.|
|920170|GET of HEAD Request met body content.|
|920171|GET- of HEAD-aanvraag met transfer-encoding.|
|920180|POST-verzoek ontbreekt Content-Length Header.|
|920190|Bereik = Ongeldige laatste bytewaarde.|
|920200|Bereik = Te veel velden (6 of meer)|
|920201|Bereik = Te veel velden voor pdf-aanvraag (35 of meer)|
|920202|Bereik = Te veel velden voor pdf-aanvraag (6 of meer)|
|920210|Gevonden gegevens met meerdere/conflicterende verbindingsheaders.|
|920220|URL-aanvalspoging voor misbruikcoderen|
|920230|Meerdere URL-codering gedetecteerd|
|920240|URL-aanvalspoging voor misbruikcoderen|
|920250|UTF8 Codering Misbruik Attack Poging|
|920260|Unicode Volledige / halve breedte misbruik aanval poging|
|920270|Ongeldig teken in aanvraag (null-teken)|
|920271|Ongeldig teken in aanvraag (niet-afdrukbare tekens)|
|920272|Ongeldig teken in aanvraag (buiten afdrukbare chars onder ascii 127)|
|920273|Ongeldig teken in aanvraag (buiten zeer strikte set)|
|920274|Ongeldig teken in aanvraagkoppen (buiten zeer strikte set)|
|920280|Een hostkoptekst aanvragen|
|920290|Lege hostkoptekst|
|920300|Verzoek om het missen van een koptekst voor accepteren|
|920310|Aanvraag heeft een lege koptekst accepteren|
|920311|Aanvraag heeft een lege koptekst accepteren|
|920320|Koptekst van ontbrekende agent|
|920330|Koptekst van de gebruikeragent leegmaken|
|920340|Inhoud aanvragen, maar koptekst van Ontbrekende inhoudstype|
|920341|Voor aanvraag met inhoud is een header van Contenttype vereist|
|920350|Hostheader is een numeriek IP-adres|
|920360|Argumentnaam te lang|
|920370|Argumentwaarde te lang|
|920380|Te veel argumenten in aanvraag|
|920390|Totale grootte van argumenten overschreden|
|920400|Geüploade bestandsgrootte te groot|
|920410|Totale geüploade bestanden grootte te groot|
|920420|Inhoudstype aanvragen is niet toegestaan door beleid|
|920430|HTTP-protocolversie is niet toegestaan door beleid|
|920440|URL-bestandsextensie wordt beperkt door beleid|
|920450|HTTP-header wordt beperkt door beleid (%@{MATCHED_VAR})|
|920460|Abnormale escape-personages|
|920470|Header Illegaal inhoudstype|
|920480|Parameter Charset beperken in de koptekst van het inhoudstype|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-31"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|921110|HTTP Verzoek Smokkelaanval|
|921120|HTTP-reactie splitsen aanval|
|921130|HTTP-reactie splitsen aanval|
|921140|HTTP Header Injection Attack via headers|
|921150|HTTP Header Injection Attack via payload (CR/LF gedetecteerd)|
|921151|HTTP Header Injection Attack via payload (CR/LF gedetecteerd)|
|921160|HTTP Header Injection Attack via payload (CR/LF en header-name gedetecteerd)|
|921170|HTTP-parametervervuiling|
|921180|HTTP-parametervervuiling (%{TX.1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-31"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|930100|Pad Traversal Attack (/.. /)|
|930110|Pad Traversal Attack (/.. /)|
|930120|Poging tot toegang tot het besturingssysteem|
|930130|Poging tot beperkte bestandstoegang|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-31"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|931100|Mogelijke RFI-aanval (Remote File Inclusion) = URL-parameter met IP-adres|
|931110|Mogelijke Remote File Inclusion (RFI) Attack = Common RFI Vulnerable Parameter Name used w/URL Payload|
|931120|Mogelijke Remote File Inclusion (RFI) Attack = URL Payload Used w/Trailing Question Mark Character (?)|
|931130|Mogelijke RFI-aanval (Remote File Inclusion) = Referentie/Link buiten het domein|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-31"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|932100|Uitvoering van opdrachten op afstand: Unix-commando-injectie|
|932105|Uitvoering van opdrachten op afstand: Unix-commando-injectie|
|932106|Uitvoering van opdrachten op afstand: Unix-commando-injectie|
|932110|Uitvoering van opdrachten op afstand: Windows-opdracht-injectie|
|932115|Uitvoering van opdrachten op afstand: Windows-opdracht-injectie|
|932120|Uitvoering van opdrachten op afstand = Windows PowerShell-opdracht gevonden|
|932130|Uitvoering van opdrachten op afstand = Unix-shell-expressie gevonden|
|932140|Uitvoering van opdrachten op afstand = Windows VOOR/ALS, opdracht gevonden|
|932160|Uitvoering van opdrachten op afstand = Unix-shellcode gevonden|
|932170|Uitvoering van commando's op afstand = Shellshock (CVE-2014-6271)|
|932171|Uitvoering van commando's op afstand = Shellshock (CVE-2014-6271)|
|932180|Poging tot uploaden met beperkte bestanden|
|932190|Remote Command Execution: Wildcard bypass techniek poging|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-31"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|933100|PHP Injection Attack = Opening/Closing Tag Gevonden|
|933110|PHP Injection Attack = PHP Script File Upload gevonden|
|933111|PHP Injection Attack: PHP Script File Upload gevonden|
|933120|PHP-injectieaanval = configuratierichtlijn gevonden|
|933130|PHP Injection Attack = Variabelen gevonden|
|933131|PHP Injection Attack: Variabelen gevonden|
|933140|PHP Injection Attack: I/O Stream Gevonden|
|933150|PHP Injection Attack = High-Risk PHP functienaam gevonden|
|933151|PHP Injection Attack: Medium-Risk PHP functienaam gevonden|
|933160|PHP Injection Attack = High-Risk PHP Function Call Gevonden|
|933161|PHP-injectieaanval: PHP-functieoproep met een lage waarde gevonden|
|933170|PHP Injection Attack: Serialized Object Injection|
|933180|PHP-injectieaanval = variabele functieoproep gevonden|
|933190|PHP Injection Attack: PHP Closing Tag Gevonden|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-31"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|941100|XSS-aanval gedetecteerd via libinjection|
|941101|XSS-aanval gedetecteerd via libinjection|
|941110|XSS-filter - Categorie 1 = Scripttagvector|
|941130|XSS-filter - Categorie 3 = Kenmerkvector|
|941140|XSS-filter - Categorie 4 = Javascript URI Vector|
|941150|XSS-filter - Categorie 5 = Niet-toegestane HTML-kenmerken|
|941160|NoScript XSS InjectionChecker: HTML-injectie|
|941170|NoScript XSS InjectionChecker: Attribuutinjectie|
|941180|Trefwoorden voor knooppuntvalidator blacklist|
|941190|XSS met stijlbladen|
|941200|XSS met VML-frames|
|941210|XSS met versluierde Javascript|
|941220|XSS met versluierd VB Script|
|941230|XSS met 'embed'-tag|
|941240|XSS met kenmerk 'importeren' of 'implementatie'|
|941250|IE XSS-filters - Aanval gedetecteerd|
|941260|XSS met 'meta'-tag|
|941270|XSS met 'link' href|
|941280|XSS met 'base'-tag|
|941290|XSS met 'applet' tag|
|941300|XSS met 'object'-tag|
|941310|US-ASCII Misvormde encoding XSS Filter - Attack Gedetecteerd.|
|941320|Mogelijke XSS-aanval gedetecteerd - HTML-taghandler|
|941330|IE XSS-filters - Aanval gedetecteerd.|
|941340|IE XSS-filters - Aanval gedetecteerd.|
|941350|UTF-7 Codering IE XSS - Aanval gedetecteerd.|


### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-31"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|942100|SQL Injection Attack gedetecteerd via libinjection|
|942110|SQL Injection Attack: Common Injection Testing gedetecteerd|
|942120|SQL Injection Attack: SQL Operator gedetecteerd|
|942130|SQL Injection Attack: SQL Tautology gedetecteerd.|
|942140|SQL Injection Attack = Gemeenschappelijke DB-namen gedetecteerd|
|942150|SQL-injectieaanval|
|942160|Detecteert blinde sqli-tests met slaap() of benchmark().|
|942170|Detecteert SQL-benchmark- en slaapinjectiepogingen, inclusief voorwaardelijke query's|
|942180|Detecteert eenvoudige SQL-verificatiebypasspogingen 1/3|
|942190|Detecteert MSSQL-code-uitvoering en pogingen tot het verzamelen van informatie|
|942200|Detecteert MySQL-commentaar-/ruimteversluierde injecties en backtick-beëindiging|
|942210|Detecteert geketende SQL-injectiepogingen 1/2|
|942220|Op zoek naar integer overloop aanvallen, deze zijn afkomstig van skipfish, behalve 3.00738585072|
|942230|Detecteert voorwaardelijke SQL-injectiepogingen|
|942240|Detecteert MySQL charset-switch- en MSSQL-dos-pogingen|
|942250|Detecteert MATCH TEGEN, VOEG EN VOER ONMIDDELLIJKE injecties UIT|
|942251|Detecteert HAVING-injecties|
|942260|Detecteert eenvoudige SQL-verificatiebypasspogingen 2/3|
|942270|Op zoek naar een basis sql injectie. Algemene aanvalstekenreeks voor mysql oracle en anderen|
|942280|Detecteert Postgres pg_sleep injectie, wachten op vertraging aanvallen en database shutdown pogingen|
|942290|Vindt basispoging tot SQL-injectie van MongoDB|
|942300|Detecteert MySQL-opmerkingen, -voorwaarden en ch(a)r-injecties|
|942310|Detecteert geketende SQL-injectiepogingen 2/2|
|942320|Detecteert MySQL- en PostgreSQL-injecties voor opgeslagen procedure/functie|
|942330|Detecteert klassieke SQL-injectiesinsonderingen 1/2|
|942340|Detecteert eenvoudige SQL-verificatiebypasspogingen 3/3|
|942350|Detecteert MySQL UDF-injectie en andere pogingen tot gegevens-/structuurmanipulatie|
|942360|Detecteert gelijktijdige basisSQL-injectie en SQLLFI-pogingen|
|942361|Detecteert basisSQL-injectie op basis van trefwoordwijziging of -unie|
|942370|Detecteert klassieke SQL-injectiesinsonderingen 2/2|
|942380|SQL-injectieaanval|
|942390|SQL-injectieaanval|
|942400|SQL-injectieaanval|
|942410|SQL-injectieaanval|
|942420|Beperkte SQL-tekenanomaliedetectie (cookies): aantal speciale tekens overschreden (8)|
|942421|Beperkte SQL-tekenanomaliedetectie (cookies): aantal speciale tekens overschreden (3)|
|942430|Beperkte SQL-tekenanomaliedetectie (args): aantal speciale tekens overschreden (12)|
|942431|Beperkte SQL-tekenanomaliedetectie (args): aantal speciale tekens overschreden (6)|
|942432|Beperkte SQL-tekenanomaliedetectie (args): aantal speciale tekens overschreden (2)|
|942440|SQL-commentaarreeks gedetecteerd.|
|942450|SQL Hex-codering geïdentificeerd|
|942460|Meta-Character Anomalie Detectie Alert - Repetitieve niet-Word Tekens|
|942470|SQL-injectieaanval|
|942480|SQL-injectieaanval|
|942490|Detecteert klassieke SQL-injectiesinsonderingen 3/3|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-31"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|943100|Mogelijke sessiefixatieaanval = Cookiewaarden instellen in HTML|
|943110|Mogelijke sessiefixatieaanval = Parameternaam sessionID met niet-domeinverwijzer|
|943120|Mogelijke sessiefixatieaanval = Parameternaam sessionID zonder verwijzer|

### <a name="p-x-ms-format-detectionnonerequest-944-application-attack-session-javap"></a><a name="crs944-31"></a> <p x-ms-format-detection="none">REQUEST-944-APPLICATION-ATTACK-SESSION-JAVA</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|944120|Mogelijke uitvoering van payload en uitvoering van opdrachten op afstand|
|944130|Verdachte Java-lessen|
|944200|Exploitatie van Java deserialization Apache Commons|

# <a name="owasp-30"></a>[OWASP 3.0](#tab/owasp30)

## <a name="rule-sets"></a><a name="owasp30"></a>Regelsets

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-30"></a> <p x-ms-format-detection="none">Algemeen</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|200004|Mogelijke meerdelige ongeëvenaarde grens.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-30"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|911100|Methode is niet toegestaan door beleid|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-30"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|913100|Gevonden user-agent die is gekoppeld aan beveiligingsscanner|
|913110|Gevonden aanvraagkop die is gekoppeld aan beveiligingsscanner|
|913120|Gevonden aanvraagbestandsnaam/argument in verband met beveiligingsscanner|
|913101|Gevonden user-agent die is gekoppeld aan scripting/generieke HTTP-client|
|913102|Gevonden user-agent die is gekoppeld aan webcrawler/bot|

### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-30"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|920100|Ongeldige HTTP-aanvraagregel|
|920130|Kan geen verzoeklichaam ontsmetten.|
|920140|Meerdelige aanvraaginstantie heeft strikte validatie niet doorbroken|
|920160|Http-header over inhoudslengte is niet numeriek.|
|920170|GET of HEAD Request met body content.|
|920180|POST-verzoek ontbreekt Content-Length Header.|
|920190|Bereik = Ongeldige laatste bytewaarde.|
|920210|Gevonden gegevens met meerdere/conflicterende verbindingsheaders.|
|920220|URL-aanvalspoging voor misbruikcoderen|
|920240|URL-aanvalspoging voor misbruikcoderen|
|920250|UTF8 Codering Misbruik Attack Poging|
|920260|Unicode Volledige / halve breedte misbruik aanval poging|
|920270|Ongeldig teken in aanvraag (null-teken)|
|920280|Een hostkoptekst aanvragen|
|920290|Lege hostkoptekst|
|920310|Aanvraag heeft een lege koptekst accepteren|
|920311|Aanvraag heeft een lege koptekst accepteren|
|920330|Koptekst van de gebruikeragent leegmaken|
|920340|Inhoud aanvragen, maar koptekst van Ontbrekende inhoudstype|
|920350|Hostheader is een numeriek IP-adres|
|920380|Te veel argumenten in aanvraag|
|920360|Argumentnaam te lang|
|920370|Argumentwaarde te lang|
|920390|Totale grootte van argumenten overschreden|
|920400|Geüploade bestandsgrootte te groot|
|920410|Totale geüploade bestanden grootte te groot|
|920420|Inhoudstype aanvragen is niet toegestaan door beleid|
|920430|HTTP-protocolversie is niet toegestaan door beleid|
|920440|URL-bestandsextensie wordt beperkt door beleid|
|920450|HTTP-header wordt beperkt door beleid (%@{MATCHED_VAR})|
|920200|Bereik = Te veel velden (6 of meer)|
|920201|Bereik = Te veel velden voor pdf-aanvraag (35 of meer)|
|920230|Meerdere URL-codering gedetecteerd|
|920300|Verzoek om het missen van een koptekst voor accepteren|
|920271|Ongeldig teken in aanvraag (niet-afdrukbare tekens)|
|920320|Koptekst van ontbrekende agent|
|920272|Ongeldig teken in aanvraag (buiten afdrukbare chars onder ascii 127)|
|920202|Bereik = Te veel velden voor pdf-aanvraag (6 of meer)|
|920273|Ongeldig teken in aanvraag (buiten zeer strikte set)|
|920274|Ongeldig teken in aanvraagkoppen (buiten zeer strikte set)|
|920460|Abnormale ontsnappingstekens|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-30"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|921100|HTTP Verzoek Smokkelaanval.|
|921110|HTTP Verzoek Smokkelaanval|
|921120|HTTP-reactie splitsen aanval|
|921130|HTTP-reactie splitsen aanval|
|921140|HTTP Header Injection Attack via headers|
|921150|HTTP Header Injection Attack via payload (CR/LF gedetecteerd)|
|921160|HTTP Header Injection Attack via payload (CR/LF en header-name gedetecteerd)|
|921151|HTTP Header Injection Attack via payload (CR/LF gedetecteerd)|
|921170|HTTP-parametervervuiling|
|921180|HTTP-parametervervuiling (%@{TX.1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-30"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|930100|Pad Traversal Attack (/.. /)|
|930110|Pad Traversal Attack (/.. /)|
|930120|Poging tot toegang tot het besturingssysteem|
|930130|Poging tot beperkte bestandstoegang|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-30"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|931100|Mogelijke RFI-aanval (Remote File Inclusion) = URL-parameter met IP-adres|
|931110|Mogelijke Remote File Inclusion (RFI) Attack = Common RFI Vulnerable Parameter Name used w/URL Payload|
|931120|Mogelijke Remote File Inclusion (RFI) Attack = URL Payload Used w/Trailing Question Mark Character (?)|
|931130|Mogelijke RFI-aanval (Remote File Inclusion) = Referentie/Link buiten het domein|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-30"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|932120|Uitvoering van opdrachten op afstand = Windows PowerShell-opdracht gevonden|
|932130|Uitvoering van opdrachten op afstand = Unix-shell-expressie gevonden|
|932140|Uitvoering van opdrachten op afstand = Windows VOOR/ALS, opdracht gevonden|
|932160|Uitvoering van opdrachten op afstand = Unix-shellcode gevonden|
|932170|Uitvoering van commando's op afstand = Shellshock (CVE-2014-6271)|
|932171|Uitvoering van commando's op afstand = Shellshock (CVE-2014-6271)|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-30"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|933100|PHP Injection Attack = Opening/Closing Tag Gevonden|
|933110|PHP Injection Attack = PHP Script File Upload gevonden|
|933120|PHP-injectieaanval = configuratierichtlijn gevonden|
|933130|PHP Injection Attack = Variabelen gevonden|
|933150|PHP Injection Attack = High-Risk PHP functienaam gevonden|
|933160|PHP Injection Attack = High-Risk PHP Function Call Gevonden|
|933180|PHP-injectieaanval = variabele functieoproep gevonden|
|933151|PHP Injection Attack = Medium-Risk PHP functienaam gevonden|
|933131|PHP Injection Attack = Variabelen gevonden|
|933161|PHP-injectieaanval = PHP-functieoproep met een lage waarde gevonden|
|933111|PHP Injection Attack = PHP Script File Upload gevonden|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-30"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|941100|XSS-aanval gedetecteerd via libinjection|
|941110|XSS-filter - Categorie 1 = Scripttagvector|
|941130|XSS-filter - Categorie 3 = Kenmerkvector|
|941140|XSS-filter - Categorie 4 = Javascript URI Vector|
|941150|XSS-filter - Categorie 5 = Niet-toegestane HTML-kenmerken|
|941180|Trefwoorden voor knooppuntvalidator blacklist|
|941190|XSS met stijlbladen|
|941200|XSS met VML-frames|
|941210|XSS met versluierde Javascript|
|941220|XSS met versluierd VB Script|
|941230|XSS met 'embed'-tag|
|941240|XSS met kenmerk 'importeren' of 'implementatie'|
|941260|XSS met 'meta'-tag|
|941270|XSS met 'link' href|
|941280|XSS met 'base'-tag|
|941290|XSS met 'applet' tag|
|941300|XSS met 'object'-tag|
|941310|US-ASCII Misvormde encoding XSS Filter - Attack Gedetecteerd.|
|941330|IE XSS-filters - Aanval gedetecteerd.|
|941340|IE XSS-filters - Aanval gedetecteerd.|
|941350|UTF-7 Codering IE XSS - Aanval gedetecteerd.|
|941320|Mogelijke XSS-aanval gedetecteerd - HTML-taghandler|

### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-30"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|942100|SQL Injection Attack gedetecteerd via libinjection|
|942110|SQL Injection Attack: Common Injection Testing gedetecteerd|
|942130|SQL Injection Attack: SQL Tautology gedetecteerd.|
|942140|SQL Injection Attack = Gemeenschappelijke DB-namen gedetecteerd|
|942160|Detecteert blinde sqli-tests met slaap() of benchmark().|
|942170|Detecteert SQL-benchmark- en slaapinjectiepogingen, inclusief voorwaardelijke query's|
|942190|Detecteert MSSQL-code-uitvoering en pogingen tot het verzamelen van informatie|
|942200|Detecteert MySQL-commentaar-/ruimteversluierde injecties en backtick-beëindiging|
|942230|Detecteert voorwaardelijke SQL-injectiepogingen|
|942260|Detecteert eenvoudige SQL-verificatiebypasspogingen 2/3|
|942270|Op zoek naar een basis sql injectie. Gemeenschappelijke aanval string voor mysql oracle en anderen.|
|942290|Vindt basispoging tot SQL-injectie van MongoDB|
|942300|Detecteert MySQL-opmerkingen, -voorwaarden en ch(a)r-injecties|
|942310|Detecteert geketende SQL-injectiepogingen 2/2|
|942320|Detecteert MySQL- en PostgreSQL-injecties voor opgeslagen procedure/functie|
|942330|Detecteert klassieke SQL-injectiesinsonderingen 1/2|
|942340|Detecteert eenvoudige SQL-verificatiebypasspogingen 3/3|
|942350|Detecteert MySQL UDF-injectie en andere pogingen tot gegevens-/structuurmanipulatie|
|942360|Detecteert gelijktijdige basisSQL-injectie en SQLLFI-pogingen|
|942370|Detecteert klassieke SQL-injectiesinsonderingen 2/2|
|942150|SQL-injectieaanval|
|942410|SQL-injectieaanval|
|942430|Beperkte SQL-tekenanomaliedetectie (args): aantal speciale tekens overschreden (12)|
|942440|SQL-commentaarreeks gedetecteerd.|
|942450|SQL Hex-codering geïdentificeerd|
|942251|Detecteert HAVING-injecties|
|942460|Meta-Character Anomalie Detectie Alert - Repetitieve niet-Word Tekens|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-30"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId (RuleId)|Beschrijving|
|---|---|
|943100|Mogelijke sessiefixatieaanval = Cookiewaarden instellen in HTML|
|943110|Mogelijke sessiefixatieaanval = Parameternaam sessionID met niet-domeinverwijzer|
|943120|Mogelijke sessiefixatieaanval = Parameternaam sessionID zonder verwijzer|

# <a name="owasp-229"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="rule-sets"></a><a name="owasp229"></a>Regelsets

### <a name="crs_20_protocol_violations"></a><a name="crs20"></a>crs_20_protocol_violations

|RuleId (RuleId)|Beschrijving|
|---|---|
|960911|Ongeldige HTTP-aanvraagregel|
|981227|Apache-fout = Ongeldige URI in aanvraag.|
|960912|Kan geen verzoeklichaam ontsmetten.|
|960914|Meerdelige aanvraaginstantie heeft strikte validatie niet doorbroken|
|960915|Meerdelige parser detecteerde een mogelijke ongeëvenaarde grens.|
|960016|Http-header over inhoudslengte is niet numeriek.|
|960011|GET of HEAD Request met body content.|
|960012|POST-verzoek ontbreekt Content-Length Header.|
|960902|Ongeldig gebruik van identiteitscodering.|
|960022|Verwacht koptekst niet toegestaan voor HTTP 1.0.|
|960020|Pragma Header vereist Cache-Control Header voor HTTP/1.1-aanvragen.|
|958291|Bereik = veld bestaat en begint met 0.|
|958230|Bereik = Ongeldige laatste bytewaarde.|
|958295|Gevonden gegevens met meerdere/conflicterende verbindingsheaders.|
|950107|URL-aanvalspoging voor misbruikcoderen|
|950109|Meerdere URL-codering gedetecteerd|
|950108|URL-aanvalspoging voor misbruikcoderen|
|950801|UTF8 Codering Misbruik Attack Poging|
|950116|Unicode Volledige / halve breedte misbruik aanval poging|
|960901|Ongeldig teken in aanvraag|
|960018|Ongeldig teken in aanvraag|

### <a name="crs_21_protocol_anomalies"></a><a name="crs21"></a>crs_21_protocol_anomalies

|RuleId (RuleId)|Beschrijving|
|---|---|
|960008|Een hostkoptekst aanvragen|
|960007|Lege hostkoptekst|
|960015|Verzoek om het missen van een koptekst voor accepteren|
|960021|Aanvraag heeft een lege koptekst accepteren|
|960009|Een koptekst van een agent aanvragen|
|960006|Koptekst van de gebruikeragent leegmaken|
|960904|Inhoud aanvragen, maar koptekst van Ontbrekende inhoudstype|
|960017|Hostheader is een numeriek IP-adres|

### <a name="crs_23_request_limits"></a><a name="crs23"></a>crs_23_request_limits

|RuleId (RuleId)|Beschrijving|
|---|---|
|960209|Argumentnaam te lang|
|960208|Argumentwaarde te lang|
|960335|Te veel argumenten in aanvraag|
|960341|Totale grootte van argumenten overschreden|
|960342|Geüploade bestandsgrootte te groot|
|960343|Totale geüploade bestanden grootte te groot|

### <a name="crs_30_http_policy"></a><a name="crs30"></a>crs_30_http_policy

|RuleId (RuleId)|Beschrijving|
|---|---|
|960032|Methode is niet toegestaan door beleid|
|960010|Inhoudstype aanvragen is niet toegestaan door beleid|
|960034|HTTP-protocolversie is niet toegestaan door beleid|
|960035|URL-bestandsextensie wordt beperkt door beleid|
|960038|HTTP-header wordt beperkt door beleid|

### <a name="crs_35_bad_robots"></a><a name="crs35"></a>crs_35_bad_robots

|RuleId (RuleId)|Beschrijving|
|---|---|
|990002|Verzoek geeft een beveiligingsscanner aan die de site heeft gescand|
|990901|Verzoek geeft een beveiligingsscanner aan die de site heeft gescand|
|990902|Verzoek geeft een beveiligingsscanner aan die de site heeft gescand|
|990012|Rogue website crawler|

### <a name="crs_40_generic_attacks"></a><a name="crs40"></a>crs_40_generic_attacks

|RuleId (RuleId)|Beschrijving|
|---|---|
|960024|Meta-Character Anomalie Detectie Alert - Repetitieve niet-Word Tekens|
|950008|Injectie van ColdFusion-tags zonder papieren|
|950010|LDAP-injectieaanval|
|950011|SSI injectie aanval|
|950018|Universele PDF XSS URL gedetecteerd.|
|950019|E-mail injectie aanval|
|950012|HTTP Verzoek Smokkelaanval.|
|950910|HTTP-reactie splitsen aanval|
|950911|HTTP-reactie splitsen aanval|
|950117|Aanval op het opnemen van bestanden op afstand|
|950118|Aanval op het opnemen van bestanden op afstand|
|950119|Aanval op het opnemen van bestanden op afstand|
|950120|Mogelijke RFI-aanval (Remote File Inclusion) = Referentie/Link buiten het domein|
|981133|Regel 981133|
|981134|Regel 981134|
|950009|Sessiefixatie-aanval|
|950003|Sessiefixatie|
|950000|Sessiefixatie|
|950005|Poging tot rastoegang|
|950002|Toegang tot systeemopdracht|
|950006|Injectie systeemopdracht|
|959151|PHP-injectieaanval|
|958976|PHP-injectieaanval|
|958977|PHP-injectieaanval|

### <a name="crs_41_sql_injection_attacks"></a><a name="crs41sql"></a>crs_41_sql_injection_attacks

|RuleId (RuleId)|Beschrijving|
|---|---|
|981231|SQL-commentaarreeks gedetecteerd.|
|981260|SQL Hex-codering geïdentificeerd|
|981320|SQL Injection Attack = Gemeenschappelijke DB-namen gedetecteerd|
|981300|Regel 981300|
|981301|Regel 981301|
|981302|Artikel 981302|
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
|981317|SQL SELECT-instructieanomaliedetectiewaarschuwing|
|950007|Blinde SQL-injectieaanval|
|950001|SQL-injectieaanval|
|950908|SQL-injectieaanval.|
|959073|SQL-injectieaanval|
|981272|Detecteert blinde sqli-tests met slaap() of benchmark().|
|981250|Detecteert SQL-benchmark- en slaapinjectiepogingen, inclusief voorwaardelijke query's|
|981241|Detecteert voorwaardelijke SQL-injectiepogingen|
|981276|Op zoek naar een basis sql injectie. Gemeenschappelijke aanval string voor mysql oracle en anderen.|
|981270|Vindt basispoging tot SQL-injectie van MongoDB|
|981253|Detecteert MySQL- en PostgreSQL-injecties voor opgeslagen procedure/functie|
|981251|Detecteert MySQL UDF-injectie en andere pogingen tot gegevens-/structuurmanipulatie|

### <a name="crs_41_xss_attacks"></a><a name="crs41xss"></a>crs_41_xss_attacks

|RuleId (RuleId)|Beschrijving|
|---|---|
|973336|XSS-filter - Categorie 1 = Scripttagvector|
|973338|XSS-filter - Categorie 3 = Javascript URI Vector|
|981136|Regel 981136|
|981018|Regel 981018|
|958016|XSS-aanval (Cross-site Scripting)|
|958414|XSS-aanval (Cross-site Scripting)|
|958032|XSS-aanval (Cross-site Scripting)|
|958026|XSS-aanval (Cross-site Scripting)|
|958027|XSS-aanval (Cross-site Scripting)|
|958054|XSS-aanval (Cross-site Scripting)|
|958418|XSS-aanval (Cross-site Scripting)|
|958034|XSS-aanval (Cross-site Scripting)|
|958019|XSS-aanval (Cross-site Scripting)|
|958013|XSS-aanval (Cross-site Scripting)|
|958408|XSS-aanval (Cross-site Scripting)|
|958012|XSS-aanval (Cross-site Scripting)|
|958423|XSS-aanval (Cross-site Scripting)|
|958002|XSS-aanval (Cross-site Scripting)|
|958017|XSS-aanval (Cross-site Scripting)|
|958007|XSS-aanval (Cross-site Scripting)|
|958047|XSS-aanval (Cross-site Scripting)|
|958410|XSS-aanval (Cross-site Scripting)|
|958415|XSS-aanval (Cross-site Scripting)|
|958022|XSS-aanval (Cross-site Scripting)|
|958405|XSS-aanval (Cross-site Scripting)|
|958419|XSS-aanval (Cross-site Scripting)|
|958028|XSS-aanval (Cross-site Scripting)|
|958057|XSS-aanval (Cross-site Scripting)|
|958031|XSS-aanval (Cross-site Scripting)|
|958006|XSS-aanval (Cross-site Scripting)|
|958033|XSS-aanval (Cross-site Scripting)|
|958038|XSS-aanval (Cross-site Scripting)|
|958409|XSS-aanval (Cross-site Scripting)|
|958001|XSS-aanval (Cross-site Scripting)|
|958005|XSS-aanval (Cross-site Scripting)|
|958404|XSS-aanval (Cross-site Scripting)|
|958023|XSS-aanval (Cross-site Scripting)|
|958010|XSS-aanval (Cross-site Scripting)|
|958411|XSS-aanval (Cross-site Scripting)|
|958422|XSS-aanval (Cross-site Scripting)|
|958036|XSS-aanval (Cross-site Scripting)|
|958000|XSS-aanval (Cross-site Scripting)|
|958018|XSS-aanval (Cross-site Scripting)|
|958406|XSS-aanval (Cross-site Scripting)|
|958040|XSS-aanval (Cross-site Scripting)|
|958052|XSS-aanval (Cross-site Scripting)|
|958037|XSS-aanval (Cross-site Scripting)|
|958049|XSS-aanval (Cross-site Scripting)|
|958030|XSS-aanval (Cross-site Scripting)|
|958041|XSS-aanval (Cross-site Scripting)|
|958416|XSS-aanval (Cross-site Scripting)|
|958024|XSS-aanval (Cross-site Scripting)|
|958059|XSS-aanval (Cross-site Scripting)|
|958417|XSS-aanval (Cross-site Scripting)|
|958020|XSS-aanval (Cross-site Scripting)|
|958045|XSS-aanval (Cross-site Scripting)|
|958004|XSS-aanval (Cross-site Scripting)|
|958421|XSS-aanval (Cross-site Scripting)|
|958009|XSS-aanval (Cross-site Scripting)|
|958025|XSS-aanval (Cross-site Scripting)|
|958413|XSS-aanval (Cross-site Scripting)|
|958051|XSS-aanval (Cross-site Scripting)|
|958420|XSS-aanval (Cross-site Scripting)|
|958407|XSS-aanval (Cross-site Scripting)|
|958056|XSS-aanval (Cross-site Scripting)|
|958011|XSS-aanval (Cross-site Scripting)|
|958412|XSS-aanval (Cross-site Scripting)|
|958008|XSS-aanval (Cross-site Scripting)|
|958046|XSS-aanval (Cross-site Scripting)|
|958039|XSS-aanval (Cross-site Scripting)|
|958003|XSS-aanval (Cross-site Scripting)|
|973300|Mogelijke XSS-aanval gedetecteerd - HTML-taghandler|
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
|973331|IE XSS-filters - Aanval gedetecteerd.|
|973315|IE XSS-filters - Aanval gedetecteerd.|
|973330|IE XSS-filters - Aanval gedetecteerd.|
|973327|IE XSS-filters - Aanval gedetecteerd.|
|973326|IE XSS-filters - Aanval gedetecteerd.|
|973346|IE XSS-filters - Aanval gedetecteerd.|
|973345|IE XSS-filters - Aanval gedetecteerd.|
|973324|IE XSS-filters - Aanval gedetecteerd.|
|973323|IE XSS-filters - Aanval gedetecteerd.|
|973348|IE XSS-filters - Aanval gedetecteerd.|
|973321|IE XSS-filters - Aanval gedetecteerd.|
|973320|IE XSS-filters - Aanval gedetecteerd.|
|973318|IE XSS-filters - Aanval gedetecteerd.|
|973317|IE XSS-filters - Aanval gedetecteerd.|
|973329|IE XSS-filters - Aanval gedetecteerd.|
|973328|IE XSS-filters - Aanval gedetecteerd.|

### <a name="crs_42_tight_security"></a><a name="crs42"></a>crs_42_tight_security

|RuleId (RuleId)|Beschrijving|
|---|---|
|950103|Path Traversal Attack|

### <a name="crs_45_trojans"></a><a name="crs45"></a>crs_45_trojans

|RuleId (RuleId)|Beschrijving|
|---|---|
|950110|Toegang via de achterdeur|
|950921|Toegang via de achterdeur|
|950922|Toegang via de achterdeur|

---

## <a name="next-steps"></a>Volgende stappen

- [Firewallregels voor webtoepassingen aanpassen met de Azure-portal](application-gateway-customize-waf-rules-portal.md)
