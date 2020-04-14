---
title: Azure CDN van Verizon Premium-regels enginefuncties | Microsoft Documenten
description: Referentiedocumentatie voor Azure CDN van de enginefuncties van Verizon Premium-regels.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 373e7838327d11b1b54278ee0c16c6e6ae554b0b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253489"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN van Verizon Premium regels engine functies

In dit artikel worden gedetailleerde beschrijvingen weergegeven van de beschikbare functies voor de CDN-rules [engine](cdn-verizon-premium-rules-engine.md)(Azure Content Delivery Network).

Het derde deel van een regel is de functie. Een functie definieert het type actie dat wordt toegepast op het aanvraagtype dat wordt geïdentificeerd door een reeks wedstrijdvoorwaarden.

## <a name="access-features"></a>Toegangsfuncties

Deze functies zijn ontworpen om de toegang tot inhoud te beheren.

Name | Doel
-----|--------
[Toegang weigeren (403)](#deny-access-403) | Hiermee bepaalt u of alle aanvragen worden afgewezen met een 403 Verboden antwoord.
[Token Auth](#token-auth) | Hiermee bepaalt u of verificatie op basis van tokens wordt toegepast op een aanvraag.
[Token Auth-ontkenningscode](#token-auth-denial-code) | Hiermee bepaalt u het type antwoord dat wordt geretourneerd aan een gebruiker wanneer een verzoek wordt geweigerd vanwege verificatie op basis van tokens.
[URL-aanvraag token-auth negeren](#token-auth-ignore-url-case) | Hiermee bepaalt u of URL-vergelijkingen die door tokengebaseerde verificatie zijn gemaakt, hoofdlettergevoelig zijn.
[Token Auth Parameter](#token-auth-parameter) | Hiermee bepaalt u of de parameter Token-Based Authentication query string moet worden gewijzigd.

## <a name="caching-features"></a>Caching-functies

Deze functies zijn ontworpen om aan te passen wanneer en hoe inhoud in de cache wordt opgeslagen.

Name | Doel
-----|--------
[Bandbreedteparameters](#bandwidth-parameters) | Hiermee bepaalt u of bandbreedtebeperkingsparameters (bijvoorbeeld ec_rate en ec_prebuf) actief zijn.
[Bandbreedtebeperking](#bandwidth-throttling) | Beperkt de bandbreedte voor de respons die wordt geboden door de point-of-presence (POP).
[Cache omzeilen](#bypass-cache) | Hiermee bepaalt u of de aanvraag caching moet omzeilen.
[Behandeling met koptekst cache-control](#cache-control-header-treatment) | Hiermee bepaalt `Cache-Control` u de generatie kopteksten door de POP wanneer de functie Externe Max-Age actief is.
[Querytekenreeks met cachetoets](#cache-key-query-string) | Hiermee bepaalt u of de cachesleutel querytekenreeksparameters bevat of uitsluit die aan een aanvraag zijn gekoppeld.
[Cachesleutel herschrijven](#cache-key-rewrite) | Hiermee herschrijft u de cachesleutel die aan een aanvraag is gekoppeld.
[Volledige cachevulling](#complete-cache-fill) | Hiermee bepaalt u wat er gebeurt wanneer een aanvraag resulteert in een gedeeltelijke cachemiss op een POP.
[Bestandstypen comprimeren](#compress-file-types) | Hiermee definieert u de bestandsindelingen voor de bestanden die op de server worden gecomprimeerd.
[Standaard interne max-leeftijd](#default-internal-max-age) | Hiermee bepaalt u het standaard interval voor de maximumleeftijd voor POP naar oorspronkelijke servercache.
[Header-behandeling verloopt](#expires-header-treatment) | Hiermee bepaalt `Expires` u de generatie kopteksten door een POP wanneer de functie Extern max-leeftijd actief is.
[Externe Max-Leeftijd](#external-max-age) | Hiermee bepaalt u het interval voor de maximale leeftijd voor browser tot pop-cachevalidatie.
[Force Internal Max-Age](#force-internal-max-age) | Hiermee bepaalt u het interval voor de maximale leeftijd van POP tot validatie van de oorspronkelijke servercache.
[H.264-ondersteuning (HTTP Progressive Download)](#h264-support-http-progressive-download) | Hiermee bepaalt u de typen H.264-bestandsindelingen die kunnen worden gebruikt om inhoud te streamen.
[Honor No-Cache Request](#honor-no-cache-request) | Hiermee bepaalt u of de aanvragen zonder cache van een HTTP-client worden doorgestuurd naar de oorspronkelijke server.
[Origin No-Cache negeren](#ignore-origin-no-cache) | Hiermee bepaalt u of het CDN bepaalde richtlijnen negeert die vanaf een oorsprongsserver worden weergegeven.
[Onsatisfiable Ranges negeren](#ignore-unsatisfiable-ranges) | Hiermee bepaalt u het antwoord dat wordt geretourneerd naar clients wanneer een aanvraag een statuscode voor een 416 aangevraagd bereik niet satisfiable genereert.
[Intern Max-Stale](#internal-max-stale) | Hiermee bepaalt u hoe lang voorbij de normale vervaldatum een in de cache opgeslagen asset kan worden geserveerd vanaf een POP wanneer de POP het in de cache opgeslagen element niet opnieuw kan valideren met de oorsprongsserver.
[Delen van gedeeltelijke cache](#partial-cache-sharing) | Hiermee bepaalt u of een aanvraag gedeeltelijk in de cache opgeslagen inhoud kan genereren.
[Inhoud in cache valideren](#prevalidate-cached-content) | Hiermee bepaalt u of inhoud in de cache in aanmerking komt voor vroegtijdige revalidatie voordat de TTL verloopt.
[Cachebestanden met nul byte vernieuwen](#refresh-zero-byte-cache-files) | Hiermee bepaalt u hoe de aanvraag van een HTTP-client voor een 0-byte-cache-element wordt verwerkt door de POP's.
[Statuscodes voor cache instellen](#set-cacheable-status-codes) | Hiermee definieert u de set statuscodes die kunnen resulteren in inhoud in de cache.
[Verouderde inhoudsweergave op fout](#stale-content-delivery-on-error) | Hiermee bepaalt u of verlopen inhoud in de cache wordt geleverd wanneer er een fout optreedt tijdens het opnieuw valideren van de cache of bij het ophalen van de gevraagde inhoud van de server van de oorsprong van de klant.
[Muf terwijl opnieuw valideren](#stale-while-revalidate) | Verbetert de prestaties doordat de POP's verouderde client kunnen serveren aan de aanvrager terwijl de revalidatie plaatsvindt.

## <a name="comment-feature"></a>Opmerkingsfunctie

Deze functie is ontworpen om aanvullende informatie binnen een regel te verstrekken.

Name | Doel
-----|--------
[Opmerking](#comment) | Hiermee kan een notitie worden toegevoegd binnen een regel.

## <a name="header-features"></a>Koptekstfuncties

Deze functies zijn ontworpen om kopteksten uit het verzoek of antwoord toe te voegen, te wijzigen of te verwijderen.

Name | Doel
-----|--------
[Header Leeftijdsreactie](#age-response-header) | Hiermee bepaalt u of een leeftijdsreactiekop is opgenomen in het antwoord dat naar de aanvrager is verzonden.
[Antwoordkoppen voor foutopsporingscache](#debug-cache-response-headers) | Hiermee bepaalt u of een antwoord de koptekst x-EC-Debug-foutopsporing kan bevatten, die informatie bevat over het cachebeleid voor het gevraagde element.
[Koptekst clientaanvraag wijzigen](#modify-client-request-header) | Overschrijft, voegt of verwijdert een koptekst uit een aanvraag.
[Koptekst clientreactie wijzigen](#modify-client-response-header) | Overschrijft, voegt of verwijdert een koptekst uit een antwoord.
[Aangepaste koptekst voor client-IP instellen](#set-client-ip-custom-header) | Hiermee kan het IP-adres van de aanvragende client als aangepaste aanvraagkopworden toegevoegd aan de aanvraag.

## <a name="logging-features"></a>Logboekfuncties

Deze functies zijn ontworpen om de gegevens die zijn opgeslagen in raw-logboekbestanden aan te passen.

Name | Doel
-----|--------
[Aangepast logboekveld 1](#custom-log-field-1) | Hiermee bepaalt u de indeling en de inhoud die is toegewezen aan het aangepaste logboekveld in een raw-logboekbestand.
[Querytekenreeks bijhouden](#log-query-string) | Hiermee bepaalt u of een querytekenreeks is opgeslagen samen met de URL in toegangslogboeken.


<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

### Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled


### Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin-features"></a>Origin-functies

Deze functies zijn ontworpen om te bepalen hoe het CDN communiceert met een origin-server.

Name | Doel
-----|--------
[Maximale keep-alive verzoeken](#maximum-keep-alive-requests) | Hiermee definieert u het maximum aantal aanvragen voor een Keep-Alive-verbinding voordat deze wordt gesloten.
[Speciale proxykoppen](#proxy-special-headers) | Hiermee definieert u de set CDN-specifieke aanvraagkoppen die van een POP naar een oorsprongsserver worden doorgestuurd.

## <a name="specialty-features"></a>Speciale functies

Deze functies bieden geavanceerde functionaliteit voor gevorderde gebruikers.

Name | Doel
-----|--------
[Http-methoden die in cache kunnen worden opgeslagen](#cacheable-http-methods) | Hiermee bepaalt u de set aanvullende HTTP-methoden die in de cache op het netwerk kunnen worden opgeslagen.
[Cacheable Request-hoofdgrootte](#cacheable-request-body-size) | Hiermee definieert u de drempelwaarde om te bepalen of een POST-antwoord in de cache kan worden opgeslagen.
[Gebruikersvariabele](#user-variable) | Alleen voor intern gebruik.

## <a name="url-features"></a>URL-functies

Met deze functies kan een verzoek worden doorgestuurd of opnieuw worden geschreven naar een andere URL.

Name | Doel
-----|--------
[Omleidingen volgen](#follow-redirects) | Hiermee bepaalt u of aanvragen kunnen worden doorgestuurd naar de hostnaam die is gedefinieerd in de kopwaarde Locatie die wordt geretourneerd door een server van klantoorsprong.
[URL-omleiding](#url-redirect) | Hiermee worden aanvragen omgeleid via de koptekst Locatie.
[URL opnieuw genereren](#url-rewrite)  | Herschrijft de url van het verzoek.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a>Azure CDN van Verizon Premium regels engine functies referentie

---

### <a name="age-response-header"></a>Header Leeftijdsreactie

**Doel:** hiermee bepaalt u of een leeftijdsreactiekop is opgenomen in het antwoord dat naar de aanvrager is verzonden.

Waarde|Resultaat
--|--
Ingeschakeld | De header Leeftijdsreactie wordt opgenomen in het antwoord dat naar de aanvrager is verzonden.
Uitgeschakeld | De header Leeftijdsreactie is uitgesloten van het antwoord dat naar de aanvrager wordt verzonden.

**Standaardgedrag:** uitgeschakeld.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-parameters"></a>Bandbreedteparameters

**Doel:** Hiermee bepaalt u of bandbreedtebeperkingsparameters (bijvoorbeeld ec_rate en ec_prebuf) actief zijn.

Bandbreedtebeperkingsparameters bepalen of de gegevensoverdrachtssnelheid voor de aanvraag van een client beperkt is tot een aangepaste snelheid.

Waarde|Resultaat
--|--
Ingeschakeld|Hiermee kunnen de POPPs verzoeken met bandbreedtebeperking honoreren.
Uitgeschakeld|Zorgt ervoor dat de POP's bandbreedtebeperkingsparameters negeren. De gevraagde inhoud wordt normaal weergegeven (dat wil zeggen, zonder bandbreedtebeperking).

**Standaardgedrag:** Ingeschakeld.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-throttling"></a>Bandbreedtebeperking

**Doel:** Beperkt de bandbreedte voor de respons van de POP's.

Beide opties moeten worden gedefinieerd om bandbreedtebeperking goed in te stellen.

Optie|Beschrijving
--|--
Kbytes per seconde|Stel deze optie in op de maximale bandbreedte (Kb per seconde) die kan worden gebruikt om de respons te leveren.
Prebuf seconden|Stel deze optie in op het aantal seconden dat de POP's moeten wachten tot de bandbreedte is beperkt. Het doel van deze periode van onbeperkte bandbreedte is om te voorkomen dat een mediaspeler stotteren of bufferen problemen als gevolg van bandbreedte beperking.

**Standaardgedrag:** Handicap.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bypass-cache"></a>Cache omzeilen

**Doel:** Hiermee bepaalt u of de aanvraag caching moet omzeilen.

Waarde|Resultaat
--|--
Ingeschakeld|Hiermee vallen alle aanvragen door naar de oorsprongsserver, zelfs als de inhoud eerder in de cache is opgeslagen op POP's.
Uitgeschakeld|Zorgt ervoor dat POP's elementen in de cache cache plaatsen volgens het cachebeleid dat is gedefinieerd in de antwoordkoppen.

**Standaardgedrag:**

- **HTTP Groot:** Handicap

<!---
- **ADN:** Enabled

--->

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-http-methods"></a>Http-methoden die in cache kunnen worden opgeslagen

**Doel:** Hiermee bepaalt u de set aanvullende HTTP-methoden die in de cache op het netwerk kunnen worden opgeslagen.

Belangrijkste informatie:

- Deze functie gaat ervan uit dat GET-antwoorden altijd in de cache moeten worden opgeslagen. Als gevolg hiervan mag de GET HTTP-methode niet worden opgenomen bij het instellen van deze functie.
- Deze functie ondersteunt alleen de HTTP-methode POST. Post-antwoordcaching inschakelen door `POST`deze functie in te stellen op .
- Standaard worden alleen aanvragen waarvan de body kleiner is dan 14 Kb in de cache opgeslagen. Gebruik de functie Cacheable Request Body Size om de maximale hoofdgrootte van de aanvraag in te stellen.

**Standaardgedrag:** Alleen GET-antwoorden worden in de cache opgeslagen.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-request-body-size"></a>Cacheable Request-hoofdgrootte

**Doel:** Hiermee definieert u de drempelwaarde om te bepalen of een POST-antwoord in de cache kan worden opgeslagen.

Deze drempel wordt bepaald door een maximale grootte van de aanvraaginstantie op te geven. Aanvragen die een grotere aanvraaginstantie bevatten, worden niet in de cache opgeslagen.

Belangrijkste informatie:

- Deze functie is alleen van toepassing wanneer postreacties in aanmerking komen voor caching. Gebruik de functie Cacheable HTTP-methoden om postaanvragen in te schakelen.
- De verzoekende instantie wordt in aanmerking genomen voor:
    - x-www-form-urlencoded waarden
    - Zorgen voor een unieke cachesleutel
- Het definiëren van een grote maximale grootte van de aanvraaglichaam kan van invloed zijn op de prestaties van de gegevensbezorging.
    - **Aanbevolen waarde:** 14 kb
    - **Minimumwaarde:** 1 kb

**Standaardgedrag:** 14 kb

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-control-header-treatment"></a>Behandeling met koptekst cache-control

**Doel:** Hiermee bepaalt `Cache-Control` u de generatie kopteksten door de POP wanneer de functie Extern max-leeftijd actief is.

De eenvoudigste manier om dit type configuratie te bereiken, is door de functies Extern max-age en de cache-control headerbehandeling in dezelfde instructie te plaatsen.

Waarde|Resultaat
--|--
Overschrijven|Zorgt ervoor dat de volgende acties plaatsvinden:<br/> - Overschrijft `Cache-Control` de header gegenereerd door de origin server. <br/>- Hiermee `Cache-Control` voegt u de header van de functie Extern Max-Leeftijd toe aan het antwoord.
Passeren|Zorgt ervoor `Cache-Control` dat de header die wordt geproduceerd door de functie Externe Max-Age nooit aan het antwoord wordt toegevoegd. <br/> Als de oorsprongsserver `Cache-Control` een koptekst produceert, wordt deze doorgegeven aan de eindgebruiker. <br/> Als de oorsprongsserver `Cache-Control` geen koptekst produceert, kan deze optie `Cache-Control` ertoe leiden dat de antwoordkop geen koptekst bevat.
Toevoegen als ontbreekt|Als `Cache-Control` een header niet is ontvangen van de `Cache-Control` origin-server, voegt deze optie de koptekst toe die wordt geproduceerd door de functie Extern max-leeftijd. Deze optie is handig om ervoor `Cache-Control` te zorgen dat aan alle assets een koptekst wordt toegewezen.
Verwijderen| Deze optie zorgt `Cache-Control` ervoor dat een koptekst niet wordt opgenomen in de header-respons. Als `Cache-Control` er al een koptekst is toegewezen, wordt deze verwijderd uit de koptekstrespons.

**Standaardgedrag:** Overschrijven.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-query-string"></a>Querytekenreeks met cachetoets

**Doel:** Hiermee bepaalt u of de cachesleutel querytekenreeksparameters bevat of uitsluit die aan een aanvraag zijn gekoppeld.

Belangrijkste informatie:

- Geef een of meer parameternamen voor querytekenreeksen op en scheid elke parameternaam met één spatie.
- Deze functie bepaalt of querytekenreeksparameters zijn opgenomen of uitgesloten van de cachesleutel. Voor elke optie in de volgende tabel wordt aanvullende informatie verstrekt.

Type|Beschrijving
--|--
 Opnemen|  Geeft aan dat elke opgegeven parameter moet worden opgenomen in de cachesleutel. Voor elk verzoek wordt een unieke cachesleutel gegenereerd die een unieke waarde bevat voor een parameter voor querytekenreeksen die in deze functie zijn gedefinieerd.
 Alles opnemen  |Geeft aan dat er voor elk verzoek een unieke cachesleutel wordt gemaakt voor een asset met een unieke querytekenreeks. Dit type configuratie wordt meestal niet aanbevolen omdat het kan leiden tot een klein percentage van de cache hits. Een laag aantal cachehits verhoogt de belasting op de origin-server, omdat deze meer aanvragen moet bevatten. Met deze configuratie wordt het cachinggedrag gedupliceerd dat bekend staat als 'unieke cache' op de pagina Caching query-string.
 Exclude | Geeft aan dat alleen de opgegeven parameter(s) is uitgesloten van de cachesleutel. Alle andere querytekenreeksparameters zijn opgenomen in de cachesleutel.
 Alles uitsluiten  |Geeft aan dat alle querytekenreeksparameters zijn uitgesloten van de cachesleutel. Met deze configuratie wordt het standaardcachegedrag in de cache gedupliceerd op de pagina Caching query-string.  

Met de regelsengine u de manier aanpassen waarop querytekenreeksen worden geïmplementeerd. U bijvoorbeeld opgeven dat querytekenreeksen alleen worden uitgevoerd op bepaalde locaties of bestandstypen.

Als u het querywerkgedrag 'no-cache' wilt dupliceren op de pagina Caching van querytekenreeksen, maakt u een regel die een url-querywildcard-overeenkomstvoorwaarde en een functie Cache omzeilt. Stel de overeenkomende url-querywildcard in op een sterretje (*).

>[!IMPORTANT]
> Als tokenautorisatie is ingeschakeld voor elk pad in dit account, is de standaardcachemodus de enige modus die kan worden gebruikt voor het caching van querytekenreeksen. Zie [Cachegedrag in Azure CDN bepalen met queryreeksen](cdn-query-string-premium.md) voor meer informatie.

#### <a name="sample-scenarios"></a>Voorbeeldscenario's

De volgende voorbeeldgebruik voor deze functie biedt een voorbeeldaanvraag en de standaardcachesleutel:

- **Voorbeeldaanvraag:** http://wpc.0001.&lt;Domain&gt;/800001/Origin/folder/asset.htm?sessionid=1234&language=NL&userid=01
- **Standaardcachesleutel:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Opnemen

Voorbeeldconfiguratie:

- **Type:** Opnemen
- **Parameter(s):** taal

Met dit type configuratie wordt de volgende cachesleutel voor de querytekenreeksparameter gegenereerd:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Alles opnemen

Voorbeeldconfiguratie:

- **Type:** Alles opnemen

Met dit type configuratie wordt de volgende cachesleutel voor de querytekenreeksparameter gegenereerd:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Exclude

Voorbeeldconfiguratie:

- **Type:** Uitsluiten
- **Parameter(s):** aanbevolen userid

Met dit type configuratie wordt de volgende cachesleutel voor de querytekenreeksparameter gegenereerd:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Alles uitsluiten

Voorbeeldconfiguratie:

- **Type:** Alles uitsluiten

Met dit type configuratie wordt de volgende cachesleutel voor de querytekenreeksparameter gegenereerd:

    /800001/Origin/folder/asset.htm

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-rewrite"></a>Cachesleutel herschrijven

**Doel:** Hiermee herschrijft u de cachesleutel die aan een aanvraag is gekoppeld.

Een cachesleutel is het relatieve pad dat een element identificeert met het oog op caching. Met andere woorden, de servers controleren op een versie in de cache van een asset op basis van het pad zoals gedefinieerd door de cachesleutel.

Configureer deze functie door beide volgende opties te definiëren:

Optie|Beschrijving
--|--
Origineel pad| Definieer het relatieve pad naar de typen aanvragen waarvan de cachesleutel is herschreven. Een relatief pad kan worden gedefinieerd door een basisoorsprongspad te selecteren en vervolgens een normaal expressiepatroon te definiëren.
Nieuw pad|Definieer het relatieve pad voor de nieuwe cachesleutel. Een relatief pad kan worden gedefinieerd door een basisoorsprongspad te selecteren en vervolgens een normaal expressiepatroon te definiëren. Dit relatieve pad kan dynamisch worden opgebouwd door het gebruik van [HTTP-variabelen](cdn-http-variables.md).

**Standaardgedrag:** De cachesleutel van een aanvraag wordt bepaald door de aanvraag URI.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="comment"></a>Opmerking

**Doel:** Hiermee kan een notitie worden toegevoegd binnen een regel.

Een gebruik voor deze functie is om aanvullende informatie te verstrekken over het algemene doel van een regel of waarom een bepaalde overeenkomst voorwaarde of functie is toegevoegd aan de regel.

Belangrijkste informatie:

- Er mogen maximaal 150 tekens worden opgegeven.
- Gebruik alleen alfanumerieke tekens.
- Deze functie heeft geen invloed op het gedrag van de regel. Het is alleen bedoeld om een gebied te bieden waar u informatie verstrekken voor toekomstige referentie of dat kan helpen bij het oplossen van problemen met de regel.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="complete-cache-fill"></a>Volledige cachevulling

**Doel:** Hiermee bepaalt u wat er gebeurt wanneer een aanvraag resulteert in een gedeeltelijke cachemiss op een POP.

Een gedeeltelijke cachemiss beschrijft de cachestatus voor een item dat niet volledig is gedownload naar een POP. Als een asset slechts gedeeltelijk in de cache wordt opgeslagen op een POP, wordt de volgende aanvraag voor dat item opnieuw doorgestuurd naar de oorsprongsserver.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
Een gedeeltelijke cachemisstreedt meestal nadat een gebruiker een download heeft afgebroken of voor assets die uitsluitend worden aangevraagd met HTTP-bereikaanvragen. Deze functie is het meest handig voor grote assets die doorgaans niet van begin tot eind worden gedownload (bijvoorbeeld video's). Als gevolg hiervan is deze functie standaard ingeschakeld op het HTTP Large-platform. Het is uitgeschakeld op alle andere platforms.

Bewaar de standaardconfiguratie voor het HTTP Large-platform, omdat het de belasting op uw server van de oorsprong van uw klant vermindert en de snelheid verhoogt waarmee uw klanten uw inhoud downloaden.

Waarde|Resultaat
--|--
Ingeschakeld|Hiermee herstelt u het standaardgedrag. Het standaardgedrag is om de POP te dwingen een achtergrondophalen van het item van de oorsprongsserver te starten. Waarna het item in de lokale cache van de POP staat.
Uitgeschakeld|Hiermee voorkomt u dat een POP een achtergrondophalen voor het item uitvoert. Het resultaat is dat de volgende aanvraag voor dat item uit die regio ervoor zorgt dat een POP het verzoek aanvraagt bij de server van de oorsprong van de klant.

**Standaardgedrag:** Ingeschakeld.

#### <a name="compatibility"></a>Compatibiliteit

Vanwege de manier waarop cache-instellingen worden bijgehouden, kan deze functie niet worden gekoppeld aan de volgende wedstrijdvoorwaarden:

- AS-nummer
- Client-IP-adres
- Cookieparameter
- Cookieparameter Regex
- Land
- Apparaat
- Microsoft Edge Cname
- Domein verwijzen
- Koptekst aanvragen Letterlijk
- Regex voor koptekst aanvragen
- Jokerteken aanvragen
- Aanvraagmethode
- Aanvraagregeling
- URL-query letterlijk
- URL-query Regex
- URL-queryjokerteken
- PARAMETER URL-query

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="compress-file-types"></a>Bestandstypen comprimeren

**Doel:** Hiermee definieert u de bestandsindelingen voor de bestanden die op de server worden gecomprimeerd.

Een bestandsindeling kan worden opgegeven met het type internetmedia (bijvoorbeeld Inhoudstype). Internetmediatype is platformonafhankelijke metagegevens waarmee de servers de bestandsindeling van een bepaald item kunnen identificeren. Hieronder vindt u een lijst met veelvoorkomende internetmediatypen.

Internetmediatype|Beschrijving
--|--
tekst/vlakte|Platte tekstbestanden
text/html| HTML-bestanden
text/css|Trapsgewijze stijlbladen (CSS)
toepassing/x-javascript|Javascript
toepassing/javascript|Javascript

Belangrijkste informatie:

- Geef meerdere internetmediatypen op door elk type te definiëren met één spatie.
- Deze functie comprimeert alleen activa waarvan de grootte minder dan 1 MB bedraagt. Grotere assets worden niet gecomprimeerd door de servers.
- Bepaalde soorten inhoud, zoals afbeeldingen, video en audiomedia (bijvoorbeeld JPG, MP3, MP4, enz.), zijn al gecomprimeerd. Omdat extra compressie op dit soort elementen de bestandsgrootte niet aanzienlijk vermindert, wordt aanbevolen dat u geen compressie op deze elementen inschakelt.
- Jokertekens, zoals sterretjes, worden niet ondersteund.
- Voordat u deze functie toevoegt aan een regel, moet u ervoor zorgen dat u de optie Compressie uitgeschakeld instelt op de compressiepagina voor het platform waarop deze regel wordt toegepast.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="custom-log-field-1"></a>Aangepast logboekveld 1

**Doel:** Hiermee bepaalt u de indeling en de inhoud die wordt toegewezen aan het aangepaste logboekveld in een raw-logboekbestand.

Met dit aangepaste veld u bepalen welke waardes en antwoordkopwaarden in uw logboekbestanden worden opgeslagen.

Standaard wordt het aangepaste logboekveld 'x-ec_custom-1' genoemd. De naam van dit veld kan worden aangepast op de pagina Raw Log Settings.

De indeling voor het opgeven van aanvraag- en antwoordkoppen wordt als volgt gedefinieerd:

Kopteksttype|Indeling|Voorbeelden
-|-|-
Aanvraagheader|`%{[RequestHeader]()}[i]()` | %{Accept-Encoding}i <br/> {Referrer}i <br/> %{Autorisatie}i
Reactieheader|`%{[ResponseHeader]()}[o]()`| %{Age}o <br/> %{Content-Type}o <br/> %{Cookie}o

Belangrijkste informatie:

- Een aangepast logboekveld kan elke combinatie van koptekstvelden en platte tekst bevatten.
- Geldige tekens voor dit veld zijn als volgt: alfanumeriek (0-9, a-z en A-Z), streepjes, dubbele punten, puntkomma's, apostrofs, komma's, perioden, underscores, gelijke tekens, haakjes, haakjes en spaties. Het percentagesymbool en de krullende accolades zijn alleen toegestaan wanneer ze worden gebruikt om een koptekstveld op te geven.
- De spelling voor elk opgegeven kopveld moet overeenkomen met de gewenste naam van de aanvraag/antwoordkop.
- Als u meerdere kopteksten wilt opgeven, gebruikt u een scheidingsteken om elke koptekst aan te geven. U bijvoorbeeld een afkorting gebruiken voor elke koptekst:
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o

**Standaardwaarde:** -

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Antwoordkoppen voor foutopsporingscache

**Doel:** Hiermee bepaalt u of een antwoord [x-EC-Debug-antwoordkoppen](cdn-http-debug-headers.md)kan bevatten, die informatie bevatten over het cachebeleid voor het gevraagde element.

De bugfoutcache-antwoordkoppen worden opgenomen in het antwoord wanneer beide van de volgende zijn waar:

- De functie Antwoordkoppen foutopsporing van de cache is ingeschakeld op het opgegeven verzoek.
- De opgegeven aanvraag definieert de set foutopsporingscacheresponskoppen die in het antwoord worden opgenomen.

Antwoordkoppen voor foutopsporingscache kunnen worden aangevraagd door de volgende koptekst en de opgegeven richtlijnen in het verzoek op te nemen:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Voorbeeld:**

X-EC-Debug: x-ec-cache, x-ec-check-cacheable, x-ec-cache-key, x-ec-cache-state

Waarde|Resultaat
-|-
Ingeschakeld|Aanvragen voor antwoordkoppen voor foutopsporingscache retourneert een antwoord met de koptekst X-EC-Debug.
Uitgeschakeld|De x-EC-Debug-antwoordkop wordt uitgesloten van het antwoord.

**Standaardgedrag:** Handicap.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Standaard interne max-leeftijd

**Doel:** Hiermee bepaalt u het standaard interval voor de maximumleeftijd voor POP naar oorspronkelijke servercache. Met andere woorden, de hoeveelheid tijd die vóór een POP wordt gegeven, controleert of een asset in de cache overeenkomt met het actief dat is opgeslagen op de oorsprongsserver.

Belangrijkste informatie:

- Deze actie vindt alleen plaats voor reacties van een oorsprongsserver die `Cache-Control` `Expires` geen maximale leeftijdsaanduiding in de of header heeft toegewezen.
- Deze actie vindt niet plaats voor activa die niet als cachebaar worden beschouwd.
- Deze actie heeft geen invloed op de validatie van de popcache. Deze typen revalidaties worden bepaald `Cache-Control` `Expires` door de of headers die naar de browser worden verzonden, die kunnen worden aangepast met de functie Extern max-leeftijd.
- De resultaten van deze actie hebben geen waarneembaar effect op de antwoordkoppen en de inhoud die is geretourneerd van POP's voor uw inhoud, maar het kan een effect hebben op de hoeveelheid revalidatieverkeer dat van POP's naar uw oorspronkelijke server wordt verzonden.
- Configureer deze functie op:
    - Het selecteren van de statuscode waarvoor een standaard interne max-leeftijd kan worden toegepast.
    - Het opgeven van een gehele waarde en vervolgens het selecteren van de gewenste tijdseenheid (bijvoorbeeld seconden, minuten, uren, enz.). Met deze waarde wordt het standaard interval voor de interne maximumleeftijd gedefinieerd.

- Als u de tijdeenheid instelt op 'Uit' wordt een standaard intern interval van de maximale `Cache-Control` `Expires` leeftijd van 7 dagen toegewezen voor aanvragen waarvoor geen maximale leeftijdsaanduiding in hun of koptekst is toegewezen.

**Standaardwaarde:** 7 dagen

#### <a name="compatibility"></a>Compatibiliteit

Vanwege de manier waarop cache-instellingen worden bijgehouden, kan deze functie niet worden gekoppeld aan de volgende wedstrijdvoorwaarden:
- AS-nummer
- Client-IP-adres
- Cookieparameter
- Cookieparameter Regex
- Land
- Apparaat
- RandCnaam
- Domein verwijzen
- Koptekst aanvragen Letterlijk
- Regex voor koptekst aanvragen
- Jokerteken aanvragen
- Aanvraagmethode
- Aanvraagregeling
- URL-query letterlijk
- URL-query Regex
- URL-queryjokerteken
- PARAMETER URL-query

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="deny-access-403"></a>Toegang weigeren (403)

**Doel**: Bepaalt of alle verzoeken worden afgewezen met een 403 Verboden antwoord.

Waarde | Resultaat
------|-------
Ingeschakeld| Hiermee worden alle verzoeken die voldoen aan de matchingcriteria afgewezen met een 403 Verboden antwoord.
Uitgeschakeld| Hiermee herstelt u het standaardgedrag. Het standaardgedrag is om de oorsprongsserver toe te staan om het type antwoord te bepalen dat wordt geretourneerd.

**Standaardgedrag:** uitgeschakeld

> [!TIP]
   > Een mogelijk gebruik voor deze functie is om deze te koppelen aan een voorwaarde voor een aanvraagheaderom de toegang tot HTTP-verwijzers te blokkeren die inline koppelingen naar uw inhoud gebruiken.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="expires-header-treatment"></a>Header-behandeling verloopt

**Doel:** Hiermee bepaalt `Expires` u de generatie kopteksten door een POP wanneer de functie Extern max-leeftijd actief is.

De eenvoudigste manier om dit type configuratie te bereiken, is door de functies Externe Max-Age en de expires Header Treatment in dezelfde instructie te plaatsen.

Waarde|Resultaat
--|--
Overschrijven|Zorgt ervoor dat de volgende acties zullen plaatsvinden:<br/>- Overschrijft `Expires` de header gegenereerd door de origin server.<br/>- Hiermee `Expires` voegt u de header van de functie Extern Max-Leeftijd toe aan het antwoord.
Passeren|Zorgt ervoor `Expires` dat de header die wordt geproduceerd door de functie Externe Max-Age nooit aan het antwoord wordt toegevoegd. <br/> Als de oorsprongsserver `Expires` een header produceert, wordt deze doorgegeven aan de eindgebruiker. <br/>Als de oorsprongsserver `Expires` geen koptekst produceert, kan deze optie `Expires` ertoe leiden dat de antwoordkop geen koptekst bevat.
Toevoegen als ontbreekt| Als `Expires` een header niet is ontvangen van de `Expires` origin-server, voegt deze optie de koptekst toe die is geproduceerd door de functie Extern max-leeftijd. Deze optie is handig om ervoor te `Expires` zorgen dat aan alle assets een header wordt toegewezen.
Verwijderen| Hiermee zorgt `Expires` u ervoor dat een koptekst niet wordt opgenomen in de header-respons. Als `Expires` er al een koptekst is toegewezen, wordt deze verwijderd uit het koptekstantwoord.

**Standaardgedrag:** Overschrijven

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="external-max-age"></a>Externe Max-Leeftijd

**Doel:** Hiermee bepaalt u het interval voor de maximale leeftijd voor browser tot pop-cachevalidatie. Met andere woorden, de hoeveelheid tijd die zal verstrijken voordat een browser kan controleren op een nieuwe versie van een item van een POP.

Als u deze `Cache-Control: max-age` `Expires` functie inschakelt, worden deze gegenereerd en worden kopteksten van de POP's gegenereerd en naar de HTTP-client verzonden. Standaard overschrijven deze kopteksten de kopteksten die door de origin-server zijn gemaakt. De cache-control header-behandeling en de functies Verloopt Header Treatment kunnen echter worden gebruikt om dit gedrag te wijzigen.

Belangrijkste informatie:

- Deze actie heeft geen invloed op de validatievan pop-to-origin-servercache. Deze typen revalidaties worden bepaald `Cache-Control` `Expires` door de headers en headers die van de origin-server worden ontvangen en kunnen worden aangepast met de standaard functie van intern max-age en de functie Force Internal Max-Age.
- Configureer deze functie door een gehele waarde op te geven en de gewenste tijdseenheid te selecteren (bijvoorbeeld seconden, minuten, uren, enz.).
- Als u deze functie instelt op een `Cache-Control: no-cache` negatieve `Expires` waarde, worden de POP's een en een tijd die in het verleden is ingesteld met elke reactie op de browser verzonden. Hoewel een HTTP-client het antwoord niet in de cache opslaat, heeft deze instelling geen invloed op de mogelijkheid van de POP-leden om het antwoord vanaf de oorspronkelijke server in de cache te plaatsen.
- Als u de tijdeenheid instelt op 'Uit' schakelt u deze functie uit. De `Cache-Control` `Expires` headers en headers die in de cache worden opgeslagen met de reactie van de oorsprongsserver, worden doorgegeven aan de browser.

**Standaardgedrag:** Uit

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="follow-redirects"></a>Omleidingen volgen

**Doel:** Hiermee bepaalt u of aanvragen kunnen worden doorgestuurd naar de hostnaam die is gedefinieerd in de kopwaarde Locatie die wordt geretourneerd door een server van klantoorsprong.

Belangrijkste informatie:

- Verzoeken kunnen alleen worden doorgestuurd naar edge CNAMEs die overeenkomen met hetzelfde platform.

Waarde|Resultaat
-|-
Ingeschakeld|Aanvragen kunnen worden doorgestuurd.
Uitgeschakeld|Verzoeken worden niet doorgestuurd.

**Standaardgedrag:** Handicap.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="force-internal-max-age"></a>Force Internal Max-Age

**Doel:** Hiermee bepaalt u het interval voor de maximale leeftijd van POP tot validatie van de oorspronkelijke servercache. Met andere woorden, de hoeveelheid tijd die voorbijgaat voordat een POP zal passeren, kan controleren of een asset in de cache overeenkomt met het actief dat is opgeslagen op de oorsprongsserver.

Belangrijkste informatie:

- Deze functie overschrijft het interval voor `Cache-Control` `Expires` de max-leeftijd die is gedefinieerd in of kopteksten die zijn gegenereerd vanaf een oorsprongsserver.
- Deze functie heeft geen invloed op de validatie van de popcache. Deze typen revalidaties worden bepaald `Cache-Control` `Expires` door de of headers die naar de browser worden verzonden.
- Deze functie heeft geen waarneembaar effect op het antwoord dat door een POP aan de aanvrager wordt geleverd. Het kan echter een effect hebben op de hoeveelheid revalidatieverkeer dat van de POP's naar de oorspronkelijke server wordt verzonden.
- Configureer deze functie op:
    - Het selecteren van de statuscode waarvoor een interne max-leeftijd wordt toegepast.
    - Het opgeven van een gehele waarde en het selecteren van de gewenste tijdseenheid (bijvoorbeeld seconden, minuten, uren, enz.). Deze waarde definieert het maximale leeftijdsinterval van de aanvraag.

- Als u de tijdeenheid instelt op 'Uit' schakelt u deze functie uit. Een intern interval voor de max-leeftijd wordt niet toegewezen aan aangevraagde activa. Als de oorspronkelijke koptekst geen caching-instructies bevat, wordt het element in de cache opgeslagen volgens de actieve instelling in de functie Standaard intern max-leeftijd.

**Standaardgedrag:** Uit

#### <a name="compatibility"></a>Compatibiliteit

Vanwege de manier waarop cache-instellingen worden bijgehouden, kan deze functie niet worden gekoppeld aan de volgende wedstrijdvoorwaarden:
- AS-nummer
- Client-IP-adres
- Cookieparameter
- Cookieparameter Regex
- Land
- Apparaat
- RandCnaam
- Domein verwijzen
- Koptekst aanvragen Letterlijk
- Regex voor koptekst aanvragen
- Jokerteken aanvragen
- Aanvraagmethode
- Aanvraagregeling
- URL-query letterlijk
- URL-query Regex
- URL-queryjokerteken
- PARAMETER URL-query

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="h264-support-http-progressive-download"></a>H.264-ondersteuning (HTTP Progressive Download)

**Doel:** Hiermee bepaalt u de typen H.264-bestandsindelingen die kunnen worden gebruikt om inhoud te streamen.

Belangrijkste informatie:

- Definieer een ruimte-afgebakende set toegestane H.264-bestandsnaamextensies in de optie Bestandsextensies. De optie Bestandsextensies overschrijft het standaardgedrag. Behoud MP4- en F4V-ondersteuning door deze bestandsnaamextensies op te geven bij het instellen van deze optie.
- Een periode opnemen waarin u elke bestandsnaamextensie opgeeft (bijvoorbeeld _.mp4_, _.f4v)._

**Standaardgedrag:** HTTP Progressive Download ondersteunt standaard MP4- en F4V-media.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="honor-no-cache-request"></a>Honor No-Cache Request

**Doel:** Hiermee bepaalt u of de aanvragen zonder cache van een HTTP-client worden doorgestuurd naar de oorspronkelijke server.

Er treedt een no-cache aanvraag `Cache-Control: no-cache` op wanneer `Pragma: no-cache` de HTTP-client een en/of koptekst verzendt in de HTTP-aanvraag.

Waarde|Resultaat
--|--
Ingeschakeld|Hiermee kan de no-cache-aanvragen van een HTTP-client worden doorgestuurd naar de oorsprongsserver en de oorsprongsserver de antwoordkoppen en de hoofdtekst via de POP terugsturen naar de HTTP-client.
Uitgeschakeld|Hiermee herstelt u het standaardgedrag. Het standaardgedrag is om te voorkomen dat aanvragen zonder cache worden doorgestuurd naar de origin-server.

Voor al het productieverkeer is het ten zeerste aan te raden om deze functie in de standaarduitgeschakelde status te laten. Anders worden oorsprongsservers niet afgeschermd van eindgebruikers die per ongeluk veel aanvragen zonder cache kunnen activeren bij het vernieuwen van webpagina's, of van de vele populaire mediaspelers die zijn gecodeerd om bij elk videoverzoek een header zonder cache te verzenden. Niettemin kan deze functie nuttig zijn om toe te passen op bepaalde niet-productie staging of testen mappen, om verse inhoud te trekken on-demand van de oorsprong server.

De cachestatus die wordt gerapporteerd voor een aanvraag die vanwege deze `TCP_Client_Refresh_Miss`functie naar een oorsprongsserver kan worden doorgestuurd, is . Het rapport Cachestatussen, dat beschikbaar is in de core-rapportagemodule, biedt statistische informatie op basis van cachestatus. Met dit rapport u het aantal en het percentage aanvragen bijhouden dat naar een oorsprongsserver wordt doorgestuurd vanwege deze functie.

**Standaardgedrag:** Handicap.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-origin-no-cache"></a>Origin No-Cache negeren

**Doel:** Hiermee bepaalt u of het CDN de volgende richtlijnen negeert die vanaf een oorsprongsserver worden weergegeven:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Belangrijkste informatie:

- Configureer deze functie door een ruimte-afgebakende lijst met statuscodes te definiëren waarvoor de bovenstaande richtlijnen worden genegeerd.
- De set geldige statuscodes voor deze functie zijn: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 en 505.
- Schakel deze functie uit door deze in te stellen op een lege waarde.

**Standaardgedrag:** Het standaardgedrag is om de bovenstaande richtlijnen te eren.

#### <a name="compatibility"></a>Compatibiliteit

Vanwege de manier waarop cache-instellingen worden bijgehouden, kan deze functie niet worden gekoppeld aan de volgende wedstrijdvoorwaarden:
- AS-nummer
- Client-IP-adres
- Cookieparameter
- Cookieparameter Regex
- Land
- Apparaat
- RandCnaam
- Domein verwijzen
- Koptekst aanvragen Letterlijk
- Regex voor koptekst aanvragen
- Jokerteken aanvragen
- Aanvraagmethode
- Aanvraagregeling
- URL-query letterlijk
- URL-query Regex
- URL-queryjokerteken
- PARAMETER URL-query

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-unsatisfiable-ranges"></a>Onsatisfiable Ranges negeren

**Doel:** Hiermee bepaalt u het antwoord dat wordt geretourneerd naar clients wanneer een aanvraag een statuscode voor een 416 aangevraagd bereik niet-satisfiable genereert.

Deze statuscode wordt standaard geretourneerd wanneer de opgegeven bytebereikaanvraag niet kan worden voldaan door een POP en een kopveld met aanvragen voor bereik niet is opgegeven.

Waarde|Resultaat
-|-
Ingeschakeld|Hiermee voorkomt u dat de POP's reageren op een ongeldige bytebereikaanvraag met een statuscode voor een niet-vernufterbereik van 416 aangevraagd bereik. In plaats daarvan leveren de servers het gevraagde item en sturen ze een 200 OK terug naar de client.
Uitgeschakeld|Hiermee herstelt u het standaardgedrag. Het standaardgedrag is om de 416 Gevraagde Bereik niet satisfiable statuscode te eren.

**Standaardgedrag:** Handicap.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="internal-max-stale"></a>Intern Max-Stale

**Doel:** Hiermee bepaalt u hoe lang voorbij de normale vervaldatum een in de cache opgeslagen asset kan worden geserveerd vanaf een POP wanneer de POP het in de cache opgeslagen element niet opnieuw kan valideren met de oorsprongsserver.

Normaal gesproken, wanneer de maximale leeftijd van een asset verstrijkt, stuurt de POP een revalidatieverzoek naar de oorsprongsserver. De oorsprong server zal dan reageren met ofwel een 304 Niet gewijzigd om de POP een nieuwe lease op de cache asset, of anders met 200 OK om de POP te voorzien van een bijgewerkte versie van de cache asset.

Als de POP niet in staat is om een verbinding met de oorsprongsserver tot stand te brengen tijdens een dergelijke revalidatie, bepaalt deze interne Max-Stale-functie of en hoe lang de POP het nu verouderde actief kan blijven bedienen.

Houd er rekening mee dat dit tijdsinterval begint wanneer de maximumleeftijd van het asset verloopt, niet wanneer de mislukte revalidatie plaatsvindt. Daarom is de maximale periode waarin een actief kan worden geserveerd zonder succesvolle revalidatie de hoeveelheid tijd die is opgegeven door de combinatie van max-age plus max-muf. Als een asset bijvoorbeeld om 9:00 in de cache is opgeslagen met een maximale leeftijd van 30 minuten en een maximale verouderde van 15 minuten, zou een mislukte revalidatiepoging om 9:44 ertoe leiden dat een eindgebruiker het verouderde item in de cache ontvangt, terwijl een mislukte validatiepoging van 9:46 ertoe zou leiden dat de eindgebruiker een time-out van 504 gateway ontvangt.

Elke waarde die voor deze functie `Cache-Control: must-revalidate` `Cache-Control: proxy-revalidate` is geconfigureerd, wordt vervangen door of kopteksten die van de oorspronkelijke server zijn ontvangen. Als een van deze headers wordt ontvangen van de oorsprongsserver wanneer een asset in eerste instantie in de cache wordt opgeslagen, wordt er geen verouderd item in de cache weergegeven. In een dergelijk geval, als de POP niet in staat is om opnieuw te valideren met de oorsprong wanneer het interval van de maximale leeftijd van het actief is verstreken, geeft de POP een time-outfout van 504 Gateway als resultaat.

Belangrijkste informatie:

- Configureer deze functie op:
    - Het selecteren van de statuscode waarvoor een max-stale wordt toegepast.
    - Het opgeven van een gehele waarde en vervolgens het selecteren van de gewenste tijdseenheid (bijvoorbeeld seconden, minuten, uren, enz.). Deze waarde definieert het interne max-muf dat wordt toegepast.

- Als u de tijdeenheid instelt op 'Uit' schakelt u deze functie uit. Een in de cache opgeslagen asset wordt niet meer weergegeven dan de normale vervaldatum.

**Standaardgedrag:** Twee minuten

#### <a name="compatibility"></a>Compatibiliteit

Vanwege de manier waarop cache-instellingen worden bijgehouden, kan deze functie niet worden gekoppeld aan de volgende wedstrijdvoorwaarden:
- AS-nummer
- Client-IP-adres
- Cookieparameter
- Cookieparameter Regex
- Land
- Apparaat
- RandCnaam
- Domein verwijzen
- Koptekst aanvragen Letterlijk
- Regex voor koptekst aanvragen
- Jokerteken aanvragen
- Aanvraagmethode
- Aanvraagregeling
- URL-query letterlijk
- URL-query Regex
- URL-queryjokerteken
- PARAMETER URL-query

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="log-query-string"></a>Querytekenreeks bijhouden

**Doel:** Hiermee bepaalt u of een querytekenreeks wordt opgeslagen samen met de URL in toegangslogboeken.

Waarde|Resultaat
-|-
Ingeschakeld|Hiermee u querytekenreeksen opslaan bij het opnemen van URL's in een toegangslogboek. Als een URL geen querytekenreeks bevat, heeft deze optie geen effect.
Uitgeschakeld|Hiermee herstelt u het standaardgedrag. Het standaardgedrag is het negeren van querytekenreeksen bij het opnemen van URL's in een toegangslogboek.

**Standaardgedrag:** Handicap.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="maximum-keep-alive-requests"></a>Maximale keep-alive verzoeken

**Doel:** Hiermee definieert u het maximum aantal aanvragen voor een Keep-Alive-verbinding voordat deze wordt gesloten.

Het instellen van het maximum aantal aanvragen op een lage waarde wordt afgeraden en kan leiden tot prestatiedegradatie.

Belangrijkste informatie:

- Geef deze waarde op als geheel geheel.
- Neem geen komma's of perioden op in de opgegeven waarde.

**Standaardwaarde:** 10.000 aanvragen

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-request-header"></a>Koptekst clientaanvraag wijzigen

**Doel:** Elke aanvraag bevat een set aanvraagkoppen die het beschrijven. Deze functie kan:

- De waarde toevoegen of overschrijven die is toegewezen aan een aanvraagkoptekst. Als de opgegeven aanvraagkop niet bestaat, wordt deze functie toegevoegd aan de aanvraag.
- Verwijder een aanvraagkoptekst uit de aanvraag.

Aanvragen die worden doorgestuurd naar een origin-server, weerspiegelen de wijzigingen die door deze functie zijn aangebracht.

Een van de volgende acties kan worden uitgevoerd op een aanvraagheader:

Optie|Beschrijving|Voorbeeld
-|-|-
Toevoegen|De opgegeven waarde wordt toegevoegd aan het einde van de bestaande waarde van de aanvraagkoptekst.|**Kopwaarde van de koptekst aanvragen (client):**<br/>Waarde1<br/>**Kopwaarde aanvragen (regelsengine):**<br/>Waarde2 <br/>**Waarde van nieuwe aanvraagkoptekst:** <br/>Waarde1Waarde2
Overschrijven|De waarde van de aanvraagkopwordt ingesteld op de opgegeven waarde.|**Kopwaarde van de koptekst aanvragen (client):**<br/>Waarde1<br/>**Kopwaarde aanvragen (regelsengine):**<br/>Waarde2<br/>**Waarde van nieuwe aanvraagkoptekst:**<br/> Waarde2 <br/>
Verwijderen|Hiermee verwijdert u de opgegeven aanvraagkoptekst.|**Kopwaarde van de koptekst aanvragen (client):**<br/>Waarde1<br/>**Configuratie van clientaanvragen-header wijzigen:**<br/>Verwijder de betreffende aanvraagkoptekst.<br/>**Resultaat:**<br/>De opgegeven aanvraagheader wordt niet doorgestuurd naar de oorspronkelijke server.

Belangrijkste informatie:

- Controleer of de waarde die is opgegeven in de optie Naam exact overeenkomt met de gewenste aanvraagkop.
- Zaak wordt niet in aanmerking genomen voor het identificeren van een header. Een van de volgende varianten van `Cache-Control` de kopnaam kan bijvoorbeeld worden gebruikt om deze te identificeren:
    - cachebeheer
    - CACHEBEHEER
    - cachE-Control
- Wanneer u een kopnaam opgeeft, gebruikt u alleen alfanumerieke tekens, streepjes of underscores.
- Als u een koptekst verwijderde, wordt voorkomen dat deze door de POP's naar een oorsprongsserver wordt doorgestuurd.
- De volgende kopteksten zijn gereserveerd en kunnen niet worden gewijzigd door deze functie:
    - Doorgestuurd
    - host
    - Via
    - waarschuwing
    - x-forwarded-for
    - Alle kopnamen die beginnen met "x-ec" zijn gereserveerd.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-response-header"></a>Koptekst clientreactie wijzigen

Elk antwoord bevat een set antwoordkoppen die het beschrijven. Deze functie kan:

- De waarde toevoegen of overschrijven die is toegewezen aan een antwoordkop. Als de opgegeven antwoordkop niet bestaat, wordt deze functie toegevoegd aan het antwoord.
- Verwijder een antwoordkop uit het antwoord.

Standaard worden de waarden van de antwoordkopwaarden gedefinieerd door een oorsprongsserver en door de POP's.

Een van de volgende acties kan worden uitgevoerd op een antwoordkop:

Optie|Beschrijving|Voorbeeld
-|-|-
Toevoegen|De opgegeven waarde wordt toegevoegd aan het einde van de bestaande waarde van de antwoordkop.|**Waarde van de antwoordkop (client):**<br />Waarde1<br/>**Waarde van de antwoordkop (engine voor regels):**<br/>Waarde2<br/>**Nieuwe waarde van de antwoordkop:**<br/>Waarde1Waarde2
Overschrijven|De waarde van de antwoordkop wordt ingesteld op de opgegeven waarde.|**Waarde van de antwoordkop (client):**<br/>Waarde1<br/>**Waarde van de antwoordkop (engine voor regels):**<br/>Waarde2 <br/>**Nieuwe waarde van de antwoordkop:**<br/>Waarde2 <br/>
Verwijderen|Hiermee verwijdert u de opgegeven antwoordkop.|**Waarde van de antwoordkop (client):**<br/>Waarde1<br/>**Configuratie van de clientresponskoptekst wijzigen:**<br/>Verwijder de antwoordkop in kwestie.<br/>**Resultaat:**<br/>De opgegeven antwoordkop wordt niet doorgestuurd naar de aanvrager.

Belangrijkste informatie:

- Controleer of de waarde die is opgegeven in de optie Naam exact overeenkomt met de gewenste antwoordkop.
- Zaak wordt niet in aanmerking genomen voor het identificeren van een header. Een van de volgende varianten van `Cache-Control` de kopnaam kan bijvoorbeeld worden gebruikt om deze te identificeren:
    - cachebeheer
    - CACHEBEHEER
    - cachE-Control
- Als u een koptekst verwijderd, wordt deze niet doorgestuurd naar de aanvrager.
- De volgende kopteksten zijn gereserveerd en kunnen niet worden gewijzigd door deze functie:
    - accepteren-codering
    - leeftijd
    - verbinding
    - inhoudscodering
    - inhoudslengte
    - inhoudsbereik
    - date
    - server
    - Trailer
    - overdrachtcodering
    - upgrade
    - Variëren
    - Via
    - waarschuwing
    - Alle kopnamen die beginnen met "x-ec" zijn gereserveerd.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="partial-cache-sharing"></a>Delen van gedeeltelijke cache

**Doel:** Hiermee bepaalt u of een aanvraag gedeeltelijk in de cache opgeslagen inhoud kan genereren.

Deze gedeeltelijke cache kan vervolgens worden gebruikt om nieuwe aanvragen voor die inhoud te vervullen totdat de gevraagde inhoud volledig in de cache is opgeslagen.

Waarde|Resultaat
-|-
Ingeschakeld|Aanvragen kunnen gedeeltelijk gecachede inhoud genereren.
Uitgeschakeld|Aanvragen kunnen alleen een volledig in de cache opgeslagen versie van de gevraagde inhoud genereren.

**Standaardgedrag:** Handicap.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="prevalidate-cached-content"></a>Inhoud in cache valideren

**Doel:** Hiermee bepaalt u of inhoud in de cache in aanmerking komt voor vroegtijdige revalidatie voordat de TTL verloopt.

Definieer de hoeveelheid tijd voorafgaand aan het verstrijken van de TTL van de gevraagde inhoud waarin deze in aanmerking komt voor vroegtijdige revalidatie.

Belangrijkste informatie:

- Als u 'Uit' selecteert als de tijdeenheid, moet opnieuw worden gevalideerd nadat de TTL in de cache is verlopen. Tijd mag niet worden opgegeven en wordt genegeerd.

**Standaardgedrag:** Uit. De validatie kan alleen plaatsvinden nadat de TTL in de cache is verlopen.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="proxy-special-headers"></a>Speciale proxykoppen

**Doel:** Hiermee definieert u de set [Verizon-specifieke HTTP-aanvraagkoppen](cdn-verizon-http-headers.md) die worden doorgestuurd van een POP naar een oorsprongsserver.

Belangrijkste informatie:

- Elke CDN-specifieke aanvraagkop die in deze functie is gedefinieerd, wordt doorgestuurd naar een oorsprongsserver. Uitgesloten kopteksten worden niet doorgestuurd.
- Als u wilt voorkomen dat een CDN-specifieke aanvraagkop wordt doorgestuurd, verwijdert u deze uit de lijst met spatiegescheiden in het veld koptekstlijst.

De volgende HTTP-headers zijn opgenomen in de standaardlijst:
- Via
- X-Doorgestuurd-Voor
- X-Forwarded-Proto
- X-Host
- X-Midgress
- X-Gateway-lijst
- X-EC-naam
- Host

**Standaardgedrag:** Alle CDN-specifieke aanvraagheaders worden doorgestuurd naar de origin-server.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="refresh-zero-byte-cache-files"></a>Cachebestanden met nul byte vernieuwen

**Doel:** Hiermee bepaalt u hoe de aanvraag van een HTTP-client voor een 0-byte-cache-element wordt verwerkt door de POP's.

Geldige waarden zijn:

Waarde|Resultaat
--|--
Ingeschakeld|Zorgt ervoor dat de POP het item opnieuw ophaalt van de oorsprongsserver.
Uitgeschakeld|Hiermee herstelt u het standaardgedrag. Het standaardgedrag is om geldige cache-elementen op verzoek weer te geven.

Deze functie is niet vereist voor het corrigeren van caching en het leveren van inhoud, maar kan nuttig zijn als tijdelijke oplossing. Dynamische contentgeneratoren op oorsprongsservers kunnen er bijvoorbeeld per ongeluk toe leiden dat antwoorden van 0 byte naar de POP's worden verzonden. Dit soort reacties worden meestal in de cache opgeslagen door de POP's. Als u weet dat een 0-byte-antwoord nooit een geldig antwoord is voor dergelijke inhoud, kan deze functie voorkomen dat dit soort activa aan uw klanten worden aangeboden.

**Standaardgedrag:** Handicap.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-cacheable-status-codes"></a>Statuscodes voor cache instellen

**Doel:** Hiermee definieert u de set statuscodes die kunnen resulteren in inhoud in de cache.

Standaard is caching alleen ingeschakeld voor 200 OK-antwoorden.

Definieer een ruimte-afgebakende set van de gewenste statuscodes.

Belangrijkste informatie:

- Schakel de functie Origin No-Cache negeren in. Als deze functie niet is ingeschakeld, worden niet-200 OK-antwoorden mogelijk niet in de cache opgeslagen.
- De set geldige statuscodes voor deze functie zijn: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 en 505.
- Deze functie kan niet worden gebruikt om caching uit te schakelen voor reacties die een 200 OK-statuscode genereren.

**Standaardgedrag:** Caching is alleen ingeschakeld voor reacties die een 200 OK-statuscode genereren.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-client-ip-custom-header"></a>Aangepaste koptekst voor client-IP instellen

**Doel:** Hiermee voegt u een aangepaste koptekst toe die de aanvragende client per IP-adres aan de aanvraag identificeert.

Met de optie Koptekstnaam wordt de naam gedefinieerd van de aangepaste aanvraagkopwaar het IP-adres van de client is opgeslagen.

Met deze functie kan een server van klantoorsprong ip-adressen van client achterhalen via een aangepaste aanvraagheader. Als het verzoek vanuit de cache wordt weergegeven, wordt de oorsprongsserver niet op de hoogte gebracht van het IP-adres van de client. Daarom wordt aanbevolen deze functie te gebruiken met elementen die niet in de cache worden opgeslagen.

Controleer of de opgegeven kopnaam niet overeenkomt met een van de volgende namen:

- Standaard kopnamen aanvragen. Een lijst met standaard kopnamen is te vinden in [RFC 2616.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)
- Gereserveerde koptekstnamen:
    - doorgestuurd-voor
    - host
    - Variëren
    - Via
    - waarschuwing
    - x-forwarded-for
    - Alle kopnamen die beginnen met "x-ec" zijn gereserveerd.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-content-delivery-on-error"></a>Verouderde inhoudsweergave op fout

**Doel:** Hiermee bepaalt u of verlopen inhoud in de cache wordt geleverd wanneer er een fout optreedt tijdens het opnieuw valideren van de cache of bij het ophalen van de gevraagde inhoud van de server van de oorsprong van de klant.

Waarde|Resultaat
-|-
Ingeschakeld|Verouderde inhoud wordt aan de aanvrager weergegeven wanneer er een fout optreedt tijdens een verbinding met een oorspronkelijke server.
Uitgeschakeld|De fout van de oorspronkelijke server wordt doorgestuurd naar de aanvrager.

**Standaardgedrag:** Handicap

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-while-revalidate"></a>Muf terwijl opnieuw valideren

**Doel:** Verbetert de prestaties doordat de POP's verouderde inhoud kunnen weergeven aan de aanvrager terwijl de revalidatie plaatsvindt.

Belangrijkste informatie:

- Het gedrag van deze functie is afhankelijk van de geselecteerde tijdseenheid.
    - **Tijdseenheid:** Geef een tijdsduur op en selecteer een tijdseenheid (bijvoorbeeld Seconden, Minuten, Uren, enz.) om verouderde inhoudslevering mogelijk te maken. Met dit type installatie kan het CDN de tijdsduur verlengen waarop het inhoud kan leveren voordat validatie vereist is volgens de volgende formule: **TTL** + **Stale While Revalidate Time**
    - **Uit:** Selecteer 'Uit' om een revalidatie te vereisen voordat een verzoek om verouderde inhoud kan worden weergegeven.
        - Geef geen tijdsduur op omdat deze niet van toepassing is en wordt genegeerd.

**Standaardgedrag:** Uit. De validatie moet plaatsvinden voordat de gevraagde inhoud kan worden weergegeven.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth"></a>Token Auth

**Doel:** Hiermee bepaalt u of verificatie op basis van tokens wordt toegepast op een aanvraag.

Als tokenverificatie is ingeschakeld, worden alleen verzoeken die een versleuteld token bieden en voldoen aan de vereisten die door dat token zijn opgegeven, gehonoreerd.

De versleutelingssleutel die wordt gebruikt om tokenwaarden te versleutelen en te decoderen, wordt bepaald door de primaire sleutel en de opties back-upsleutel op de pagina Token Auth. Houd er rekening mee dat versleutelingssleutels platformspecifiek zijn.

**Standaardgedrag:** Handicap.

Deze functie heeft voorrang op de meeste functies, met uitzondering van de functie URL Herschrijven.

Waarde | Resultaat
------|---------
Ingeschakeld | Beschermt de gevraagde inhoud met tokenverificatie. Alleen verzoeken van clients die een geldig token verstrekken en voldoen aan de vereisten, worden gehonoreerd. FTP-transacties zijn uitgesloten van tokenverificatie.
Uitgeschakeld| Hiermee herstelt u het standaardgedrag. Het standaardgedrag is om uw verificatieconfiguratie op token te laten bepalen of een aanvraag wordt beveiligd.

#### <a name="compatibility"></a>Compatibiliteit

Gebruik Token Auth niet met een voorwaarde Altijd overeenkomen.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-denial-code"></a>Token Auth-ontkenningscode

**Doel:** Hiermee bepaalt u het type antwoord dat wordt geretourneerd aan een gebruiker wanneer een verzoek wordt geweigerd vanwege verificatie op basis van tokens.

De beschikbare antwoordcodes staan in de volgende tabel.

Reactiecode|Reactienaam|Beschrijving
-------------|-------------|--------
301|Permanent verplaatst|Met deze statuscode worden onbevoegde gebruikers omgeleid naar de URL die is opgegeven in de kopwaarde Locatie.
302|Gevonden|Met deze statuscode worden onbevoegde gebruikers omgeleid naar de URL die is opgegeven in de kopwaarde Locatie. Deze statuscode is de industriestandaardmethode voor het uitvoeren van een omleiding.
307|Tijdelijke omleiding|Met deze statuscode worden onbevoegde gebruikers omgeleid naar de URL die is opgegeven in de kopwaarde Locatie.
401|Niet geautoriseerd|Door deze statuscode te combineren met de reactiekop van WWW-Authenticate u een gebruiker om verificatie vragen.
403|Verboden|Dit bericht is het standaard 403 Verboden statusbericht dat een onbevoegde gebruiker ziet wanneer hij toegang probeert te krijgen tot beveiligde inhoud.
404|Bestand niet gevonden|Deze statuscode geeft aan dat de HTTP-client met de server kon communiceren, maar dat de gevraagde inhoud niet is gevonden.

#### <a name="compatibility"></a>Compatibiliteit

Gebruik token auth-ontkenningscode niet met een voorwaarde Altijd overeenkomen. Gebruik in plaats daarvan de sectie **Aangepaste ontkenningsafhandeling** op de pagina **Token Auth** van de portal **Beheren.** Zie [Azure CDN-elementen beveiligen met tokenverificatie](cdn-token-auth.md)voor meer informatie.

#### <a name="url-redirection"></a>URL-omleiding

Deze functie ondersteunt URL-omleiding naar een door de gebruiker gedefinieerde URL wanneer deze is geconfigureerd om een 3xx-statuscode terug te sturen. Deze door de gebruiker gedefinieerde URL kan worden opgegeven door de volgende stappen uit te voeren:

1. Selecteer een 3xx-antwoordcode voor de functie Token Auth Denial Code.
2. Selecteer 'Locatie' in de optie Optionele koptekstnaam.
3. Stel de optie Optionele koptekstwaarde in op de gewenste URL.

Als een URL niet is gedefinieerd voor een 3xx-statuscode, wordt de standaardantwoordpagina voor een 3xx-statuscode teruggestuurd naar de gebruiker.

URL-omleiding is alleen van toepassing op 3xx-antwoordcodes.

De optie Optionele koptekstwaarde ondersteunt alfanumerieke tekens, aanhalingstekens en spaties.

#### <a name="authentication"></a>Verificatie

Deze functie ondersteunt de mogelijkheid om de HEADER WWW-Authenticate op te nemen wanneer u reageert op een ongeautoriseerd verzoek om inhoud die is beschermd door verificatie op basis van tokens. Als de HEADER WWW-Authenticate is ingesteld op 'basic' in uw configuratie, wordt de onbevoegde gebruiker om accountgegevens gevraagd.

De bovenstaande configuratie kan worden bereikt door de volgende stappen uit te voeren:

1. Selecteer '401' als antwoordcode voor de functie Token Auth Denial Code.
2. Selecteer 'WWW-Authenticeren' in de optie Optionele koptekstnaam.
3. Stel de optie Optionele koptekstwaarde in op 'basic'.

De HEADER WWW-Authenticate is alleen van toepassing op 401 antwoordcodes.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-ignore-url-case"></a>URL-aanvraag token-auth negeren

**Doel:** Hiermee bepaalt u of URL-vergelijkingen die door tokengebaseerde verificatie zijn gemaakt, hoofdlettergevoelig zijn.

De parameters die door deze functie worden beïnvloed, zijn:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Geldige waarden zijn:

Waarde|Resultaat
---|----
Ingeschakeld|Zorgt ervoor dat de POP de aanvraag negeert bij het vergelijken van URL's voor tokengebaseerde verificatieparameters.
Uitgeschakeld|Hiermee herstelt u het standaardgedrag. Het standaardgedrag is dat URL-vergelijkingen voor tokenverificatie hoofdlettergevoelig zijn.

**Standaardgedrag:** Handicap.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-parameter"></a>Token Auth Parameter

**Doel:** Hiermee bepaalt u of de parameter Token-Based Authentication query string moet worden gewijzigd.

Belangrijkste informatie:

- Met de optie Waarde wordt de naam van de querytekenreeksparameter gedefinieerd waarmee een token kan worden opgegeven.
- De optie Waarde kan niet worden ingesteld op ec_token.'
- Controleer of de naam die in de optie Waarde is gedefinieerd, alleen geldige URL-tekens bevat.

Waarde|Resultaat
----|----
Ingeschakeld|Met de optie Waarde wordt de naam van de querytekenreeksparameter gedefinieerd waarmee tokens moeten worden gedefinieerd.
Uitgeschakeld|Een token kan worden opgegeven als een ongedefinieerde querytekenreeksparameter in de url van de aanvraag.

**Standaardgedrag:** Handicap. Een token kan worden opgegeven als een ongedefinieerde querytekenreeksparameter in de url van de aanvraag.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-redirect"></a>URL-omleiding

**Doel:** Hiermee worden aanvragen omgeleid via de koptekst Locatie.

Voor de configuratie van deze functie moeten de volgende opties worden ingesteld:

Optie|Beschrijving
-|-
Code|Selecteer de antwoordcode die wordt geretourneerd naar de aanvrager.
Bron & patroon| Met deze instellingen wordt een URI-aanvraagpatroon gedefinieerd dat het type aanvragen identificeert dat kan worden omgeleid. Alleen aanvragen waarvan de URL aan beide volgende criteria voldoet, worden omgeleid: <br/> <br/> **Bron (of toegangspunt voor inhoud):** Selecteer een relatief pad dat een oorsprongsserver identificeert. Dit pad is de _sectie /XXXX/_ en uw eindpuntnaam. <br/><br/> **Bron (patroon):** Een patroon dat aanvragen identificeert op relatief pad moet worden gedefinieerd. Dit reguliere expressiepatroon moet een pad definiëren dat direct na het eerder geselecteerde toegangspunt voor inhoud begint (zie hierboven). <br/> - Zorg ervoor dat de eerder gedefinieerde uri-criteria voor aanvragen (dat wil zeggen Bron & Patroon) niet in strijd zijn met de voorwaarden die voor deze functie zijn gedefinieerd. <br/> - Geef een patroon op; Als u een lege waarde als patroon gebruikt, worden alle tekenreeksen gematcht.
Doel| Definieer de URL waarnaar de bovenstaande aanvragen worden doorgestuurd. <br/><br/> Bouw deze URL dynamisch met: <br/> - Een regelmatig expressiepatroon <br/>- [HTTP-variabelen](cdn-http-variables.md) <br/><br/> Vervang de waarden die in het bronpatroon zijn vastgelegd in het doelpatroon met $_n_ wanneer _n_ een waarde identificeert door de volgorde waarin deze is vastgelegd. $ 1 vertegenwoordigt bijvoorbeeld de eerste waarde die is vastgelegd in het bronpatroon, terwijl $ 2 de tweede waarde vertegenwoordigt. <br/>

Het is ten zeerste aan te raden om een absolute URL te gebruiken. Het gebruik van een relatieve URL kan CDN-URL's omleiden naar een ongeldig pad.

**Voorbeeldscenario**

In dit voorbeeld wordt uitgelegd hoe u een URL van de rand-CNAME die wordt opgelost, omleiden naar deze basis-CDN-URL: http:\//marketing.azureedge.net/brochures

In aanmerking komende aanvragen worden doorgestuurd naar deze URL\/van CNAME voor basisrand: http: /cdn.mydomain.com/resources

Deze URL-omleiding kan worden bereikt ![via de volgende configuratie: URL-omleiding](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Belangrijke punten:**

- De functie URL-omleiding definieert de aanvraag-URL's die worden omgeleid. Als gevolg hiervan zijn geen aanvullende wedstrijdvoorwaarden vereist. Hoewel de wedstrijdvoorwaarde is gedefinieerd als 'Altijd', worden alleen verzoeken die wijzen op de map 'brochures' over de oorsprong van de klant van de klant door de "marketing" omgeleid.
- Alle overeenkomende aanvragen worden doorgestuurd naar de edge CNAME URL die is gedefinieerd in de optie Bestemming.
    - Voorbeeldscenario #1:
        - Voorbeeldaanvraag (CDN-URL):\/http: /marketing.azureedge.net/brochures/widgets.pdf
        - URL aanvragen (na omleiding): http:\//cdn.mydomain.com/resources/widgets.pdf  
    - Voorbeeldscenario #2:
        - Voorbeeldaanvraag (URL van Edge\/CNAME): http: /marketing.mydomain.com/brochures/widgets.pdf
        - URL aanvragen (na omleiding): http:\//cdn.mydomain.com/resources/widgets.pdf Voorbeeldscenario
    - Voorbeeldscenario #3:
        - Voorbeeldaanvraag (URL van Edge\/CNAME): http: /brochures.mydomain.com/campaignA/final/productC.ppt
        - URL aanvragen (na omleiding): http:\//cdn.mydomain.com/resources/campaignA/final/productC.ppt 
- De variabele Aanvraagregeling (%{scheme}) wordt gebruikt in de optie Bestemming, die ervoor zorgt dat de regeling van de aanvraag ongewijzigd blijft na omleiding.
- De URL-segmenten die uit het verzoek zijn vastgelegd, worden via '$1' aan de nieuwe URL toegevoegd.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-rewrite"></a>URL opnieuw genereren

**Doel:** Herschrijft de url van het verzoek.

Belangrijkste informatie:

- Voor de configuratie van deze functie moeten de volgende opties worden ingesteld:

Optie|Beschrijving
-|-
 Bron & patroon | Met deze instellingen wordt een URI-aanvraagpatroon gedefinieerd dat het type aanvragen identificeert dat kan worden herschreven. Alleen aanvragen waarvan de URL aan beide volgende criteria voldoet, worden herschreven: <br/><br/>  - **Bron (of toegangspunt voor inhoud):** Selecteer een relatief pad dat een oorsprongsserver identificeert. Dit pad is de _sectie /XXXX/_ en uw eindpuntnaam. <br/><br/> - **Bron (patroon):** Een patroon dat aanvragen identificeert op relatief pad moet worden gedefinieerd. Dit reguliere expressiepatroon moet een pad definiëren dat direct na het eerder geselecteerde toegangspunt voor inhoud begint (zie hierboven). <br/> Controleer of de uri-criteria voor aanvragen (dat wil zeggen Bron & patroon) die eerder zijn gedefinieerd, niet in strijd zijn met een van de voor deze functie gedefinieerde wedstrijdvoorwaarden. Geef een patroon op; Als u een lege waarde als patroon gebruikt, worden alle tekenreeksen gematcht.
 Doel  |Definieer de relatieve URL waarvoor de bovenstaande aanvragen worden herschreven door: <br/>    1. Het selecteren van een toegangspunt voor inhoud dat een oorsprongsserver identificeert. <br/>    2. Het definiëren van een relatief pad met behulp van: <br/>        - Een regelmatig expressiepatroon <br/>        - [HTTP-variabelen](cdn-http-variables.md) <br/> <br/> Vervang de waarden die in het bronpatroon zijn vastgelegd in het doelpatroon met $_n_ wanneer _n_ een waarde identificeert door de volgorde waarin deze is vastgelegd. $ 1 vertegenwoordigt bijvoorbeeld de eerste waarde die is vastgelegd in het bronpatroon, terwijl $ 2 de tweede waarde vertegenwoordigt.

 Met deze functie kunnen de POP's de URL herschrijven zonder een traditionele omleiding uit te voeren. Dat wil zeggen dat de aanvrager dezelfde antwoordcode ontvangt als wanneer de herschreven URL is aangevraagd.

**Voorbeeldscenario 1**

In dit voorbeeld wordt uitgelegd hoe u een URL van de edge-CNAME die wordt opgelost, omleiden naar deze BASIS-CDN-URL: http:\//marketing.azureedge.net/brochures/

In aanmerking komende aanvragen worden doorgestuurd naar deze URL\/van CNAME voor basisrand: http: /MyOrigin.azureedge.net/resources/

Deze URL-omleiding kan worden bereikt ![via de volgende configuratie: URL-omleiding](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Voorbeeldscenario 2**

In dit voorbeeld wordt uitgelegd hoe u een URL van edge CNAME van HOOFDLETTERs naar kleine letters omleiden met behulp van reguliere expressies.

Deze URL-omleiding kan worden bereikt ![via de volgende configuratie: URL-omleiding](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)

**Belangrijke punten:**

- De functie URL Herschrijven definieert de aanvraag-URL's die worden herschreven. Als gevolg hiervan zijn geen aanvullende wedstrijdvoorwaarden vereist. Hoewel de wedstrijdvoorwaarde is gedefinieerd als 'Altijd', worden alleen verzoeken die wijzen op de map 'brochures' over de oorsprong van de klant 'marketing' herschreven.

- De URL-segmenten die uit het verzoek zijn vastgelegd, worden via '$1' aan de nieuwe URL toegevoegd.

#### <a name="compatibility"></a>Compatibiliteit

Deze functie bevat overeenkomende criteria waaraan moet worden voldaan voordat deze op een aanvraag kan worden toegepast. Om te voorkomen dat er conflicterende wedstrijdcriteria worden ingesteld, is deze functie niet compatibel met de volgende wedstrijdvoorwaarden:

- AS-nummer
- CDN-oorsprong
- Client-IP-adres
- Oorsprong van de klant
- Aanvraagregeling
- URL-padmap
- URL-padextensie
- URL-padbestandsnaam
- URL-pad letterlijk
- URL-pad Regex
- Wildcard voor URL-pad
- URL-query letterlijk
- PARAMETER URL-query
- URL-query Regex
- URL-queryjokerteken

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="user-variable"></a>Gebruikersvariabele

**Doel:** Alleen voor intern gebruik.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

## <a name="next-steps"></a>Volgende stappen

- [Verwijzingen naar de motor](cdn-verizon-premium-rules-engine-reference.md)
- [Regels engine voorwaardelijke expressies](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regels motor overeenkomen met voorwaarden](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [HTTP-gedrag overschrijven met de rules engine](cdn-verizon-premium-rules-engine.md)
- [Overzicht van Azure CDN](cdn-overview.md)