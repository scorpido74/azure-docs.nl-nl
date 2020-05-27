---
title: Azure CDN van de functies van de functie van Verizon Premium-regels | Microsoft Docs
description: Referentie documentatie voor Azure CDN van de functies van de Verizon Premium-regel engine.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: d2d4090934a940809fe75ad70e0650eb1c9353f1
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872716"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN van de functies van de functie van Verizon Premium-regels

In dit artikel vindt u gedetailleerde beschrijvingen van de beschik bare functies voor de [regels](cdn-verizon-premium-rules-engine.md)van Azure Content Delivery Network (CDN).

Het derde deel van een regel is de functie. Een functie definieert het type actie dat wordt toegepast op het aanvraag type dat wordt geïdentificeerd door een set matching voorwaarden.


Voor de meest recente functies raadpleegt u de documentatie van de [Verizon-regel engine](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions).


## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a>Naslag informatie over de functies van de functie voor Azure CDN van Verizon Premium-regels

---

### <a name="age-response-header"></a>Header van ouderdoms reactie

**Doel**: Hiermee wordt bepaald of een ouderdoms reactie header is opgenomen in het antwoord dat naar de aanvrager wordt verzonden.

Waarde|Resultaat
--|--
Ingeschakeld | De header van de leeftijds reactie is opgenomen in het antwoord dat naar de aanvrager wordt verzonden.
Uitgeschakeld | De header van de leeftijds reactie is uitgesloten van het antwoord dat naar de aanvrager is verzonden.

**Standaard gedrag**: uitgeschakeld.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-parameters"></a>Bandbreedte parameters

**Doel:** Bepaalt of para meters voor bandbreedte regeling (bijvoorbeeld ec_rate en ec_prebuf) actief zijn.

Met de para meters voor bandbreedte regeling wordt bepaald of het overdrachtsnelheid van de gegevens overdracht voor een client aanvraag beperkt is tot een aangepast aantal.

Waarde|Resultaat
--|--
Ingeschakeld|Hiermee kunnen de Pop's de bandbreedte beperkings aanvragen naleven.
Uitgeschakeld|Zorgt ervoor dat de Pop's de para meters voor bandbreedte beperking negeren. De aangevraagde inhoud wordt normaal bediend (dat wil zeggen, zonder bandbreedte beperking).

**Standaard gedrag:** Ingeschakeld.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-throttling"></a>Bandbreedte regeling

**Doel:** De band breedte beperken voor het antwoord dat door de Pop's wordt gegeven.

Beide opties moeten worden gedefinieerd om de bandbreedte beperking goed in te stellen.

Optie|Beschrijving
--|--
Kbytes per seconde|Stel deze optie in op de maximale band breedte (KB per seconde) die kan worden gebruikt voor het leveren van het antwoord.
Prebuf seconden|Stel deze optie in op het aantal seconden dat de Pop's moeten worden gewacht totdat de band breedte wordt beperkt. Het doel van deze tijds periode voor onbeperkte band breedte is om te voor komen dat een media speler Stuttering of buffer problemen ondervindt vanwege bandbreedte beperking.

**Standaard gedrag:** Geblokkeerd.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bypass-cache"></a>Cache overs Laan

**Doel:** Hiermee wordt bepaald of caching moet worden overgeslagen voor de aanvraag.

Waarde|Resultaat
--|--
Ingeschakeld|Zorgt ervoor dat alle aanvragen door lopen op de oorspronkelijke server, zelfs als de inhoud eerder in de cache op Pop's is opgeslagen.
Uitgeschakeld|Leidt ertoe dat Pop's activa in de cache opslaan volgens het cache beleid dat is gedefinieerd in de antwoord headers.

**Standaard gedrag:**

- **Http-groot:** Geblokkeerd

<!---
- **ADN:** Enabled

--->

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-http-methods"></a>Cacheable HTTP-methoden

**Doel:** Bepaalt de set extra HTTP-methoden die in het netwerk in de cache kunnen worden opgeslagen.

Belang rijke informatie:

- Deze functie gaat ervan uit dat het ophalen van antwoorden altijd in de cache moet worden opgeslagen. Als gevolg hiervan moet de GET HTTP-methode niet worden opgenomen bij het instellen van deze functie.
- Deze functie ondersteunt alleen de POST-HTTP-methode. POST-antwoord caching inschakelen door deze functie in te stellen op `POST` .
- Standaard worden alleen aanvragen waarvan de hoofd tekst kleiner is dan 14 KB in de cache opgeslagen. Gebruik de functie grootte van cacheable aanvraag hoofdtekst om de maximale grootte van de aanvraag tekst in te stellen.

**Standaard gedrag:** Alleen antwoorden ophalen worden in de cache opgeslagen.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-request-body-size"></a>Grootte van cacheable aanvraag hoofdtekst

**Doel:** Definieert de drempel waarde voor het bepalen of een POST-antwoord in de cache kan worden opgeslagen.

Deze drempel waarde wordt bepaald door de maximale grootte van de aanvraag tekst op te geven. Aanvragen die een grotere aanvraag tekst bevatten, worden niet in de cache opgeslagen.

Belang rijke informatie:

- Deze functie is alleen van toepassing wanneer POST-antwoorden in aanmerking komen voor caching. Gebruik de functie cacheable HTTP-methoden om caching van POST-aanvragen in te scha kelen.
- De aanvraag tekst wordt in aanmerking genomen voor:
    - x-www-form-urlencoded-waarden
    - Een unieke cache sleutel garanderen
- Het definiëren van een grote maximale aanvraag hoofdtekst kan van invloed zijn op de prestaties van de gegevens levering.
    - **Aanbevolen waarde:** 14 KB
    - **Minimum waarde:** 1 KB

**Standaard gedrag:** 14 KB

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-control-header-treatment"></a>Cache-controle header verwerking

**Doel:** Hiermee bepaalt u de generatie van `Cache-Control` headers door de pop wanneer de functie voor de externe Max-Age actief is.

De eenvoudigste manier om dit type configuratie te beheersen is het plaatsen van de verwerkings functies External Max-Age en de cache-Control header in dezelfde instructie.

Waarde|Resultaat
--|--
Overschrijven|Zorgt ervoor dat de volgende acties worden uitgevoerd:<br/> -Overschrijft de `Cache-Control` header die is gegenereerd door de oorspronkelijke server. <br/>-Voegt de `Cache-Control` koptekst die door de externe functie Max-Age wordt gemaakt, toe aan het antwoord.
Door geven|Zorgt ervoor dat de `Cache-Control` koptekst die wordt geproduceerd door de functie externe Max-Age nooit wordt toegevoegd aan het antwoord. <br/> Als de oorspronkelijke server een `Cache-Control` koptekst produceert, wordt deze door gegeven aan de eind gebruiker. <br/> Als de bron server geen `Cache-Control` header produceert, kan deze optie ertoe leiden dat de antwoord header geen `Cache-Control` header bevat.
Als ontbrekend toevoegen|Als er `Cache-Control` geen koptekst van de oorspronkelijke server is ontvangen, wordt met deze optie de `Cache-Control` header toegevoegd die wordt gemaakt door de externe functie Max-Age. Deze optie is handig om ervoor te zorgen dat aan alle activa een kop wordt toegewezen `Cache-Control` .
Verwijderen| Deze optie zorgt ervoor dat er `Cache-Control` geen koptekst wordt opgenomen in het antwoord van de header. Als er `Cache-Control` al een header is toegewezen, wordt deze verwijderd uit het antwoord van de header.

**Standaard gedrag:** Schrijft.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-query-string"></a>Cache sleutel query reeks

**Doel:** Hiermee wordt bepaald of de cache sleutel query teken reeks parameters bevat of uitsluit die aan een aanvraag zijn gekoppeld.

Belang rijke informatie:

- Geef een of meer parameter namen voor de query teken reeks op en Scheid elke parameter naam met één spatie.
- Deze functie bepaalt of query teken reeks parameters worden opgenomen of uitgesloten van de cache sleutel. Voor elke optie in de volgende tabel vindt u aanvullende informatie.

Type|Beschrijving
--|--
 Opnemen|  Geeft aan dat elke opgegeven para meter moet worden opgenomen in de cache sleutel. Er wordt een unieke cache sleutel gegenereerd voor elke aanvraag die een unieke waarde bevat voor een query reeks parameter die is gedefinieerd in deze functie.
 Alles toevoegen  |Hiermee wordt aangegeven dat voor elke aanvraag een unieke cache sleutel wordt gemaakt voor een activum dat een unieke query teken reeks bevat. Dit type configuratie wordt doorgaans niet aanbevolen omdat dit kan leiden tot een klein percentage aan cache treffers. Bij een laag aantal treffers in de cache wordt de belasting van de oorspronkelijke server verhoogd, omdat deze meer aanvragen moet verwerken. Deze configuratie dupliceert het cache gedrag ' unique-cache ' op de cache-pagina van de query-teken reeks.
 Exclude | Geeft aan dat alleen de opgegeven para meter (s) worden uitgesloten van de cache sleutel. Alle andere query reeks parameters zijn opgenomen in de cache sleutel.
 Alles uitsluiten  |Geeft aan dat alle query reeks parameters worden uitgesloten van de cache sleutel. Deze configuratie dupliceert de standaard cache-instelling voor cache op de pagina cache van de query-teken reeks.  

Met de regel engine kunt u de manier aanpassen waarop de cache van de query teken reeks wordt geïmplementeerd. U kunt bijvoorbeeld opgeven dat de caching van de query reeks alleen op bepaalde locaties of bestands typen wordt uitgevoerd.

Als u de cache-instelling voor de query reeks ' no-cache ' op de cache-pagina van de query-teken reeks wilt dupliceren, maakt u een regel met een URL-query Joker teken voor waarde en een functie voor bypass-cache. Stel de voor waarde voor het vergelijken van de URL-query in op een asterisk (*).

>[!IMPORTANT]
> Als token autorisatie is ingeschakeld voor een pad op dit account, is de modus standaard-cache de enige modus die kan worden gebruikt voor het opslaan van de query in de teken reeks cache. Zie [Cachegedrag in Azure CDN bepalen met queryreeksen](cdn-query-string-premium.md) voor meer informatie.

#### <a name="sample-scenarios"></a>Voorbeeld Scenario's

Het volgende voor beeld van het gebruik van deze functie biedt een voorbeeld aanvraag en de standaard cache sleutel:

- **Voorbeeld aanvraag:** http://wpc.0001.&lt ;D omain &gt; /800001/Origin/folder/Asset.htm? SessionID = 1234&language = EN&userid = 01
- **Standaard cache-sleutel:** /800001/Origin/folder/Asset.htm

##### <a name="include"></a>Opnemen

Voorbeeld configuratie:

- **Type:** Behoort
- **Para meter (s):** taal

Met dit type configuratie wordt de volgende query teken reeks parameter cache sleutel gegenereerd:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Alles toevoegen

Voorbeeld configuratie:

- **Type:** Alles toevoegen

Met dit type configuratie wordt de volgende query teken reeks parameter cache sleutel gegenereerd:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Exclude

Voorbeeld configuratie:

- **Type:** Weekend
- **Para meter (s):** gesessione gebruikers-id

Met dit type configuratie wordt de volgende query teken reeks parameter cache sleutel gegenereerd:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Alles uitsluiten

Voorbeeld configuratie:

- **Type:** Alles uitsluiten

Met dit type configuratie wordt de volgende query teken reeks parameter cache sleutel gegenereerd:

    /800001/Origin/folder/asset.htm

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-rewrite"></a>Cache sleutel opnieuw schrijven

**Doel:** Hiermee wordt de cache sleutel die is gekoppeld aan een aanvraag, opnieuw geschreven.

Een cache sleutel is het relatieve pad dat een Asset identificeert voor het opslaan in de cache. Met andere woorden, de servers controleren op een versie van een asset in de cache op basis van het pad zoals gedefinieerd door de cache sleutel.

Configureer deze functie door de volgende opties te definiëren:

Optie|Beschrijving
--|--
Oorspronkelijk pad| Definieer het relatieve pad naar de typen aanvragen waarvan de cache sleutel opnieuw wordt geschreven. U kunt een relatief pad definiëren door een basis pad voor de oorsprong te selecteren en vervolgens een reguliere-expressie patroon te definiëren.
Nieuw pad|Definieer het relatieve pad voor de nieuwe cache sleutel. U kunt een relatief pad definiëren door een basis pad voor de oorsprong te selecteren en vervolgens een reguliere-expressie patroon te definiëren. Dit relatieve pad kan dynamisch worden gemaakt door het gebruik van [http-variabelen](cdn-http-variables.md).

**Standaard gedrag:** De cache sleutel van een aanvraag wordt bepaald door de URI van de aanvraag.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="comment"></a>Opmerking

**Doel:** Hiermee kan een notitie worden toegevoegd binnen een regel.

Het enige gebruik van deze functie is om aanvullende informatie te verstrekken over het algemene doel van een regel of waarom een bepaalde overeenkomst voorwaarde of-functie is toegevoegd aan de regel.

Belang rijke informatie:

- Er kunnen Maxi maal 150 tekens worden opgegeven.
- Gebruik alleen alfanumerieke tekens.
- Deze functie is niet van invloed op het gedrag van de regel. Het is alleen bedoeld om een gebied te bieden waarin u informatie kunt opgeven voor toekomstige referentie of die u kan helpen bij het oplossen van problemen met de regel.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="complete-cache-fill"></a>Volledige cache opvulling

**Doel:** Hiermee wordt bepaald wat er gebeurt wanneer een aanvraag resulteert in een gedeeltelijke cache Missing in een POP.

In een gedeeltelijke cache-Missing wordt de cache status voor een Asset beschreven die niet volledig is gedownload naar een POP. Als een activum slechts gedeeltelijk in de cache wordt opgeslagen in een POP, wordt de volgende aanvraag voor dat activum opnieuw doorgestuurd naar de oorspronkelijke server.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
Een gedeeltelijke cache-Misser treedt doorgaans op wanneer een gebruiker een down load afbreekt of voor assets die alleen worden aangevraagd met behulp van HTTP-bereik aanvragen. Deze functie is vooral nuttig voor grote activa die doorgaans niet van begin tot eind worden gedownload (bijvoorbeeld Video's). Als gevolg hiervan is deze functie standaard ingeschakeld op het grote HTTP-platform. Het is uitgeschakeld op alle andere platforms.

Behoud de standaard configuratie voor het grote HTTP-platform, omdat dit de belasting van de bron server van de klant vermindert en de snelheid waarmee uw klanten uw inhoud downloaden, verhoogt.

Waarde|Resultaat
--|--
Ingeschakeld|Hiermee herstelt u het standaard gedrag. Het standaard gedrag is om te voor komen dat de POP een achtergrond ophaalt van de Asset van de oorspronkelijke server. Daarna bevinden de Asset zich in de lokale cache van de POP.
Uitgeschakeld|Hiermee voor komt u dat een POP een op de achtergrond ophalen voor de Asset uitvoert. Het resultaat is dat de volgende aanvraag voor dat activum vanuit die regio een POP heeft om deze aan te vragen bij de oorspronkelijke server van de klant.

**Standaard gedrag:** Ingeschakeld.

#### <a name="compatibility"></a>Compatibiliteit

Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, kan deze functie niet worden gekoppeld aan de volgende match-voor waarden:

- Als getal
- Client-IP-adres
- Cookie parameter
- Cookie para meter regex
- Land/regio
- Apparaat
- Micro soft Edge CNAME
- Verwijzende domein
- Letterlijke aanvraag header
- Regex-aanvraag header
- Joker teken voor aanvraag header
- Aanvraag methode
- Aanvraag schema
- URL-query-letterlijke waarde
- URL-query-regex
- URL-query Joker teken
- URL-query parameter

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="compress-file-types"></a>Bestands typen comprimeren

**Doel:** Hiermee definieert u de bestands indelingen voor de bestanden die op de server zijn gecomprimeerd.

Een bestands indeling kan worden opgegeven met behulp van het type Internet media (bijvoorbeeld inhouds type). Het media type van het internet is platform onafhankelijke meta gegevens waarmee de servers de bestands indeling van een bepaalde Asset kunnen identificeren. Hieronder vindt u een lijst met algemene Internet Media typen.

Type Internet Media|Beschrijving
--|--
tekst/zonder opmaak|Tekst bestanden zonder opmaak
text/html| HTML-bestanden
text/css|Cascading Style Sheet (CSS)
Application/x-java script|Javascript
toepassing/Java script|Javascript

Belang rijke informatie:

- Geef meerdere typen Internet media op door één spatie te scheiden.
- Deze functie comprimeert alleen assets waarvan de grootte kleiner is dan 1 MB. Grotere assets worden niet gecomprimeerd door de servers.
- Bepaalde typen inhoud, zoals afbeeldingen, Video's en audio-media-assets (bijvoorbeeld JPG, MP3, MP4 enzovoort), zijn al gecomprimeerd. Omdat extra compressie van deze typen assets de bestands grootte niet aanzienlijk vermindert, wordt aanbevolen dat u compressie niet inschakelt.
- Joker tekens, zoals sterretjes, worden niet ondersteund.
- Voordat u deze functie aan een regel toevoegt, moet u ervoor zorgen dat u de optie voor compressie uitgeschakeld instelt op de compressie pagina voor het platform waarop deze regel wordt toegepast.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="custom-log-field-1"></a>Aangepast logboek veld 1

**Doel:** Bepaalt de indeling en de inhoud die wordt toegewezen aan het aangepaste logboek veld in een onbewerkte logboek bestand.

Met dit aangepaste veld kunt u bepalen welke waarden voor de aanvraag-en antwoord header worden opgeslagen in uw logboek bestanden.

Het aangepaste logboek veld wordt standaard ' x-ec_custom-1 ' genoemd. De naam van dit veld kan worden aangepast op de pagina instellingen voor onbewerkte Logboeken.

De notatie voor het opgeven van aanvraag-en reactie headers wordt als volgt gedefinieerd:

Koptekst type|Indeling|Voorbeelden
-|-|-
Aanvraagkoptekst|`%{[RequestHeader]()}[i]()` | % {Accept-Encoding} i <br/> {Verwijzende} i <br/> % {Authorization} i
Reactieheader|`%{[ResponseHeader]()}[o]()`| % {Leeftijd} o <br/> % {Content-type} o <br/> % {Cookie} o

Belang rijke informatie:

- Een aangepast logboek veld kan een wille keurige combi natie van header velden en tekst zonder opmaak bevatten.
- Geldige tekens voor dit veld zijn: alfanumeriek (0-9, a-z en A-Z), streepjes, dubbele punten, punt komma's, apostrofs, komma's, punten, onderstrepings tekens, gelijktekens, haakjes, haakjes en spaties. Het symbool voor het percentage en de accolades zijn alleen toegestaan wanneer het wordt gebruikt om een koptekst veld op te geven.
- De spelling voor elk opgegeven header veld moet overeenkomen met de gewenste naam van de aanvraag/antwoord header.
- Als u meerdere kopteksten wilt opgeven, gebruikt u een scheidings teken om elke koptekst aan te geven. U kunt bijvoorbeeld een afkorting voor elke header gebruiken:
    - AE:% {Accept-Encoding} i A:% {Authorization} i CT:% {content-type} o

**Standaard waarde:** -

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Debug cache-antwoord headers

**Doel:** Hiermee wordt bepaald of een antwoord [X-EC-debug-antwoord headers](cdn-http-debug-headers.md)kan bevatten, dat informatie bevat over het cache beleid voor de aangevraagde Asset.

De reactie headers van debug-cache worden opgenomen in het antwoord wanneer beide volgende voor waarden van toepassing zijn:

- De functie voor de reactie headers van de debug-cache is ingeschakeld voor de opgegeven aanvraag.
- Met de opgegeven aanvraag wordt de set debug cache response headers gedefinieerd die worden opgenomen in het antwoord.

Fouten in de cache reactie van debug kunnen worden aangevraagd door de volgende header en de opgegeven instructies in de aanvraag op te nemen:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Voorbeeld:**

X-EC-debug: x-EU-cache, x-EC-check-cache, x-EG-cache-sleutel, x-EC-cache-status

Waarde|Resultaat
-|-
Ingeschakeld|Aanvragen voor de reactie headers van debug-cache retour neren een antwoord dat de X-EC-debug-header bevat.
Uitgeschakeld|De reactie header X-EC-debug wordt van het antwoord uitgesloten.

**Standaard gedrag:** Geblokkeerd.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Standaard interne Max. leeftijd

**Doel:** Hiermee wordt het standaard interval voor de maximum leeftijd van de POP-to-Origin-server cache hervalidatie bepaald. Met andere woorden, de hoeveelheid tijd die wordt door gegeven voordat een POP controleert of een activum in de cache overeenkomt met het activum dat op de oorspronkelijke server is opgeslagen.

Belang rijke informatie:

- Deze actie wordt alleen uitgevoerd voor antwoorden van een originele server die geen maximale leeftijds aanduiding in de of-header heeft `Cache-Control` toegewezen `Expires` .
- Deze actie wordt niet uitgevoerd voor activa die niet als cachebaar worden beschouwd.
- Deze actie heeft geen invloed op de hervalidaties van POP-caches in de browser. Deze typen hervalidaties worden bepaald door de `Cache-Control` of `Expires` -headers die naar de browser worden verzonden en die kunnen worden aangepast met de externe functie voor de maximale leeftijd.
- De resultaten van deze actie hebben geen waarneembaar effect op de antwoord headers en de inhoud die wordt geretourneerd van Pop's voor uw inhoud, maar kan ook invloed hebben op de hoeveelheid hervalidatie verkeer dat vanuit Pop's naar uw oorspronkelijke server wordt verzonden.
- Deze functie configureren door:
    - De status code selecteren waarvoor een standaard interne Max-leeftijd kan worden toegepast.
    - Geef een geheel getal op en selecteer vervolgens de gewenste tijds eenheid (bijvoorbeeld seconden, minuten, uren, enzovoort). Met deze waarde wordt het standaard interval voor een intern maximum leeftijd gedefinieerd.

- Als u de tijds eenheid instelt op ' uit ', wordt een standaard interne interval van 7 dagen toegewezen voor aanvragen waaraan geen maximale leeftijds aanduiding in hun of koptekst is toegewezen `Cache-Control` `Expires` .

**Standaard waarde:** 7 dagen

#### <a name="compatibility"></a>Compatibiliteit

Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, kan deze functie niet worden gekoppeld aan de volgende match-voor waarden:
- Als getal
- Client-IP-adres
- Cookie parameter
- Cookie para meter regex
- Land/regio
- Apparaat
- Edge CNAME
- Verwijzende domein
- Letterlijke aanvraag header
- Regex-aanvraag header
- Joker teken voor aanvraag header
- Aanvraag methode
- Aanvraag schema
- URL-query-letterlijke waarde
- URL-query-regex
- URL-query Joker teken
- URL-query parameter

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="deny-access-403"></a>Toegang weigeren (403)

**Doel**: Hiermee wordt bepaald of alle aanvragen worden afgewezen met een 403 niet-toegestaan antwoord.

Waarde | Resultaat
------|-------
Ingeschakeld| Zorgt ervoor dat alle aanvragen die voldoen aan de overeenkomende criteria, moeten worden afgewezen met een 403 verboden antwoord.
Uitgeschakeld| Hiermee herstelt u het standaard gedrag. Het standaard gedrag is het toestaan van de oorspronkelijke server om te bepalen welk type antwoord wordt geretourneerd.

**Standaard gedrag**: uitgeschakeld

> [!TIP]
   > Een mogelijk gebruik van deze functie is om deze te koppelen aan een match-voor waarde van de aanvraag header om de toegang tot HTTP-verwijzende sites die inline koppelingen naar uw inhoud gebruiken, te blok keren.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="expires-header-treatment"></a>Verwerking van verlooptde header

**Doel:** Hiermee bepaalt u de generatie van `Expires` kopteksten door een pop wanneer de functie voor de externe Max-Age actief is.

De eenvoudigste manier om dit type configuratie te behaalt, is door de verwerkings functies voor de externe en verlopende header in dezelfde instructie te plaatsen.

Waarde|Resultaat
--|--
Overschrijven|Zorgt ervoor dat de volgende acties worden uitgevoerd:<br/>-Overschrijft de `Expires` header die is gegenereerd door de oorspronkelijke server.<br/>-Voegt de `Expires` koptekst die door de externe functie Max-Age wordt gemaakt, toe aan het antwoord.
Door geven|Zorgt ervoor dat de `Expires` koptekst die wordt geproduceerd door de functie externe Max-Age nooit wordt toegevoegd aan het antwoord. <br/> Als de oorspronkelijke server een `Expires` koptekst produceert, wordt deze door gegeven aan de eind gebruiker. <br/>Als de oorspronkelijke server geen `Expires` header produceert, kan deze optie ertoe leiden dat de antwoord header geen `Expires` header bevat.
Als ontbrekend toevoegen| Als er `Expires` geen koptekst van de oorspronkelijke server is ontvangen, wordt met deze optie de `Expires` header toegevoegd die wordt gegenereerd door de externe functie Max-Age. Deze optie is handig om ervoor te zorgen dat aan alle activa een `Expires` kop wordt toegewezen.
Verwijderen| Zorgt ervoor dat er `Expires` geen koptekst is opgenomen in het antwoord van de header. Als er `Expires` al een header is toegewezen, wordt deze verwijderd uit het antwoord van de header.

**Standaard gedrag:** Schrijft

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="external-max-age"></a>Extern Max-Age

**Doel:** Hiermee wordt het maximale leeftijds interval voor browser naar hervalidatie van POP-caches bepaald. Met andere woorden, de hoeveelheid tijd die wordt door gegeven voordat een browser een nieuwe versie van een Asset van een POP kan controleren.

Als u deze functie inschakelt, worden `Cache-Control: max-age` de pop's gegenereerd en worden er `Expires` kopteksten naar de HTTP-client verzonden. Standaard worden deze headers die zijn gemaakt door de oorspronkelijke server overschreven. De header verwerking van de cache en de verlopende header verwerking kan echter worden gebruikt om dit gedrag te wijzigen.

Belang rijke informatie:

- Deze actie heeft geen invloed op de hervalidaties van de server cache met de oorspronkelijke POP. Deze typen hervalidaties worden bepaald door de en de headers die zijn `Cache-Control` `Expires` Ontvangen van de oorspronkelijke server en kunnen worden aangepast met de standaard interne Max-leeftijd en de functies voor het afdwingen van interne Max.
- U kunt deze functie configureren door een geheel getal op te geven en de gewenste tijds eenheid te selecteren (bijvoorbeeld seconden, minuten, uren, enzovoort).
- Als u deze functie instelt op een negatieve waarde, verzendt de Pop's een `Cache-Control: no-cache` en een `Expires` tijd die in het verleden is ingesteld met elke reactie op de browser. Hoewel een HTTP-client het antwoord niet in de cache opslaat, heeft deze instelling geen invloed op de mogelijkheid van de Pop's om de reactie van de oorspronkelijke server op te slaan in de cache.
- Als u de tijds eenheid instelt op uit, wordt deze functie uitgeschakeld. De-en-headers in de `Cache-Control` `Expires` cache met het antwoord van de oorspronkelijke server worden door gegeven aan de browser.

**Standaard gedrag:** Office

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="follow-redirects"></a>Omleidingen volgen

**Doel:** Hiermee wordt bepaald of aanvragen kunnen worden omgeleid naar de hostnaam die is opgegeven in de locatie-header die wordt geretourneerd door een server van de klant.

Belang rijke informatie:

- Verzoeken kunnen alleen worden omgeleid naar Edge CNAME-aanvragen die overeenkomen met hetzelfde platform.

Waarde|Resultaat
-|-
Ingeschakeld|Aanvragen kunnen worden omgeleid.
Uitgeschakeld|Aanvragen worden niet omgeleid.

**Standaard gedrag:** Geblokkeerd.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="force-internal-max-age"></a>Interne Max. duur forceren

**Doel:** Hiermee wordt het maximale leeftijds interval voor de hervalidatie van de server cache voor POP-to-Origin bepaald. Met andere woorden, de hoeveelheid tijd die wordt door gegeven voordat een POP kan controleren of een activum in de cache overeenkomt met het activum dat is opgeslagen op de bron server.

Belang rijke informatie:

- Deze functie overschrijft het maximale leeftijds interval dat is gedefinieerd in `Cache-Control` of `Expires` kopteksten die zijn gegenereerd op basis van een oorspronkelijke server.
- Deze functie heeft geen invloed op de hervalidaties van POP-caches in de browser. Deze typen hervalidaties worden bepaald door de `Cache-Control` of `Expires` kopteksten die naar de browser worden verzonden.
- Deze functie heeft geen waarneembaar effect op het antwoord dat door een POP aan de aanvrager wordt geleverd. Het kan echter ook invloed hebben op de hoeveelheid hervalidatie van verkeer dat van de Pop's naar de oorspronkelijke server wordt verzonden.
- Deze functie configureren door:
    - De status code selecteren waarvoor een intern maximum leeftijd wordt toegepast.
    - Geef een geheel getal op en selecteer de gewenste tijds eenheid (bijvoorbeeld seconden, minuten, uren, enzovoort). Met deze waarde wordt het maximale leeftijds interval van de aanvraag gedefinieerd.

- Als u de tijds eenheid instelt op uit, wordt deze functie uitgeschakeld. Een intern interval van Maxi maal leeftijd wordt niet toegewezen aan aangevraagde activa. Als de oorspronkelijke header geen cache-instructies bevat, wordt het activum in de cache opgeslagen volgens de actieve instelling in de standaard optie voor de interne Max.

**Standaard gedrag:** Office

#### <a name="compatibility"></a>Compatibiliteit

Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, kan deze functie niet worden gekoppeld aan de volgende match-voor waarden:
- Als getal
- Client-IP-adres
- Cookie parameter
- Cookie para meter regex
- Land/regio
- Apparaat
- Edge CNAME
- Verwijzende domein
- Letterlijke aanvraag header
- Regex-aanvraag header
- Joker teken voor aanvraag header
- Aanvraag methode
- Aanvraag schema
- URL-query-letterlijke waarde
- URL-query-regex
- URL-query Joker teken
- URL-query parameter

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="h264-support-http-progressive-download"></a>H. 264-ondersteuning (HTTP progressieve down load)

**Doel:** Bepaalt de typen H. 264-bestands indelingen die kunnen worden gebruikt om inhoud te streamen.

Belang rijke informatie:

- Definieer een door spaties gescheiden reeks toegestane bestands extensies van H. 264 in de optie Bestands extensies. Met de optie Bestands extensies wordt het standaard gedrag overschreven. Onderhoud van MP4-en F4V-ondersteuning door deze bestands extensies op te nemen bij het instellen van deze optie.
- Neem een punt op wanneer u elke bestands extensie opgeeft (bijvoorbeeld _. MP4_, _. F4V_).

**Standaard gedrag:** HTTP progressieve down loads biedt standaard ondersteuning voor MP4-en F4V-media.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="honor-no-cache-request"></a>Geen cache aanvragen

**Doel:** Hiermee wordt bepaald of er geen aanvragen van een HTTP-client worden doorgestuurd naar de oorspronkelijke server.

Er treedt een no-cache-aanvraag op wanneer de HTTP-client een- `Cache-Control: no-cache` en/of- `Pragma: no-cache` header in de HTTP-aanvraag verzendt.

Waarde|Resultaat
--|--
Ingeschakeld|Hiermee kunnen de aanvragen van een HTTP-client geen cache worden doorgestuurd naar de oorspronkelijke server. de oorspronkelijke server retourneert de antwoord headers en de hoofd tekst via de POP terug naar de HTTP-client.
Uitgeschakeld|Hiermee herstelt u het standaard gedrag. Het standaard gedrag is om te voor komen dat geen cache aanvragen worden doorgestuurd naar de oorspronkelijke server.

Voor alle productie verkeer wordt het nadrukkelijk aanbevolen om deze functie in te scha kelen met de standaard uitgeschakelde status. Als dat niet het geval is, worden de oorspronkelijke servers niet afgeschermd van eind gebruikers die per ongeluk veel no-cache aanvragen kunnen activeren bij het vernieuwen van webpagina's, of van de vele populaire media spelers die zijn gecodeerd om een no-cache-header met elke video aanvraag te verzenden. Deze functie kan echter nuttig zijn om te worden toegepast op bepaalde niet-productie staging of test directory's, zodat nieuwe inhoud op aanvraag kan worden opgehaald van de oorspronkelijke server.

De cache status die wordt gerapporteerd voor een aanvraag die kan worden doorgestuurd naar een originele server als gevolg van deze functie is `TCP_Client_Refresh_Miss` . Het rapport cache statussen, dat beschikbaar is in de module kern rapportage, biedt statistische informatie over de cache status. Met dit rapport kunt u het aantal en het percentage bijhouden van aanvragen die worden doorgestuurd naar een originele server als gevolg van deze functie.

**Standaard gedrag:** Geblokkeerd.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-origin-no-cache"></a>Geen cache oorsprong negeren

**Doel:** Hiermee wordt bepaald of de CDN de volgende instructies negeert die afkomstig zijn van een oorspronkelijke server:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Belang rijke informatie:

- Deze functie configureren door een door spaties gescheiden lijst met status codes te definiëren waarvoor de bovenstaande instructies worden genegeerd.
- De set geldige status codes voor deze functie zijn: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 en 505.
- Schakel deze functie uit door deze in te stellen op een lege waarde.

**Standaard gedrag:** Het standaard gedrag is om te voldoen aan de bovenstaande richt lijnen.

#### <a name="compatibility"></a>Compatibiliteit

Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, kan deze functie niet worden gekoppeld aan de volgende match-voor waarden:
- Als getal
- Client-IP-adres
- Cookie parameter
- Cookie para meter regex
- Land/regio
- Apparaat
- Edge CNAME
- Verwijzende domein
- Letterlijke aanvraag header
- Regex-aanvraag header
- Joker teken voor aanvraag header
- Aanvraag methode
- Aanvraag schema
- URL-query-letterlijke waarde
- URL-query-regex
- URL-query Joker teken
- URL-query parameter

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-unsatisfiable-ranges"></a>Unsatisfiable-bereiken negeren

**Doel:** Hiermee wordt bepaald welk antwoord wordt geretourneerd naar clients wanneer een aanvraag een aangevraagde bereik van 416 niet Satisfiable-status code genereert.

Deze status code wordt standaard geretourneerd wanneer de opgegeven byte-bereik aanvraag niet kan worden voldaan door een POP en een if-Range-aanvraag header veld niet is opgegeven.

Waarde|Resultaat
-|-
Ingeschakeld|Hiermee wordt voor komen dat de Pop's reageren op een ongeldige byte-bereik aanvraag met een 416 aangevraagde bereik geen Satisfiable-status code. In plaats daarvan leveren de servers de aangevraagde Asset en retour neren ze een 200 OK naar de client.
Uitgeschakeld|Hiermee herstelt u het standaard gedrag. Het standaard gedrag is om te voldoen aan het aangevraagde bereik van 416 geen Satisfiable-status code.

**Standaard gedrag:** Geblokkeerd.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="internal-max-stale"></a>Intern Max-verouderd

**Doel:** Hiermee bepaalt u hoe lang de normale verloop tijd van een activum in de cache kan worden uitgevoerd vanuit een POP wanneer de POP de asset in het cache geheugen niet opnieuw kan valideren met de oorspronkelijke server.

Normaal gesp roken stuurt de POP een validatie aanvraag naar de oorspronkelijke server als de maximale leeftijds periode van een activum is verlopen. De oorspronkelijke server reageert vervolgens met een 304 die niet is gewijzigd om de POP een nieuwe lease te geven op de asset in de cache of met 200 OK om de POP te voorzien van een bijgewerkte versie van de asset in het cache geheugen.

Als met de POP geen verbinding kan worden gemaakt met de bron server tijdens een hervalidatie, bepaalt deze interne maximum-verouderde functie of en hoe lang de POP de nu verouderde Asset mag blijven gebruiken.

Houd er rekening mee dat dit tijds interval begint wanneer de duur van het activum verloopt, niet wanneer de hervalidatie is mislukt. Daarom is de maximale periode gedurende welke een activum kan worden geleverd zonder geslaagde hervalidatie, de hoeveelheid tijd die is opgegeven door de combi natie van Max-Age plus Max-verouderd. Als een Asset bijvoorbeeld is opgeslagen in de cache bij 9:00 met een maximum leeftijd van 30 minuten en een maximum verouderd van 15 minuten, zou een mislukte hervalidatie poging op 9:44 resulteren in een eind gebruiker die de verouderde in de cache geplaatste Asset ontvangt, terwijl een mislukte hervalidatie poging om 9:46 zou resulteren in een time-out van een 504-Gateway.

Elke waarde die voor deze functie is geconfigureerd, is vervangen door `Cache-Control: must-revalidate` of headers die zijn `Cache-Control: proxy-revalidate` Ontvangen van de oorspronkelijke server. Als een van deze headers wordt ontvangen van de oorspronkelijke server wanneer een asset in eerste instantie in de cache wordt geplaatst, zal de POP geen verouderde asset in de cache hebben. Als in dat geval de POP niet opnieuw kan worden gevalideerd met de oorsprong wanneer het maximale leeftijds interval van het activum is verlopen, retourneert de POP een time-outfout van 504-Gateway.

Belang rijke informatie:

- Deze functie configureren door:
    - De status code selecteren waarvoor een Max-verouderd wordt toegepast.
    - Geef een geheel getal op en selecteer vervolgens de gewenste tijds eenheid (bijvoorbeeld seconden, minuten, uren, enzovoort). Met deze waarde wordt het interne Max-verouderde bedrag gedefinieerd dat wordt toegepast.

- Als u de tijds eenheid instelt op uit, wordt deze functie uitgeschakeld. Een asset in de cache wordt niet meer geleverd dan de normale verloop tijd.

**Standaard gedrag:** Twee minuten

#### <a name="compatibility"></a>Compatibiliteit

Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, kan deze functie niet worden gekoppeld aan de volgende match-voor waarden:
- Als getal
- Client-IP-adres
- Cookie parameter
- Cookie para meter regex
- Land/regio
- Apparaat
- Edge CNAME
- Verwijzende domein
- Letterlijke aanvraag header
- Regex-aanvraag header
- Joker teken voor aanvraag header
- Aanvraag methode
- Aanvraag schema
- URL-query-letterlijke waarde
- URL-query-regex
- URL-query Joker teken
- URL-query parameter

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="log-query-string"></a>Query reeks voor logboek

**Doel:** Hiermee wordt bepaald of een query reeks wordt opgeslagen samen met de URL in de logboeken van de toegang.

Waarde|Resultaat
-|-
Ingeschakeld|Hiermee staat u de opslag van query reeksen toe wanneer Url's worden opgenomen in een toegangs logboek. Als een URL geen query teken reeks bevat, heeft deze optie geen effect.
Uitgeschakeld|Hiermee herstelt u het standaard gedrag. Het standaard gedrag is het negeren van query reeksen bij het opnemen van Url's in een Access-logboek.

**Standaard gedrag:** Geblokkeerd.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="maximum-keep-alive-requests"></a>Maximum aantal Keep-Alive-aanvragen

**Doel:** Hiermee definieert u het maximum aantal aanvragen voor een keep-alive-verbinding voordat het wordt gesloten.

Het instellen van het maximum aantal aanvragen voor een lage waarde wordt afgeraden en kan leiden tot verminderde prestaties.

Belang rijke informatie:

- Geef deze waarde op als geheel getal.
- Neem geen komma's of punten op in de opgegeven waarde.

**Standaard waarde:** 10.000 aanvragen

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-request-header"></a>Header van client aanvraag wijzigen

**Doel:** Elke aanvraag bevat een set aanvraag headers waarmee deze wordt beschreven. Deze functie kan:

- De waarde die is toegewezen aan een aanvraag header toevoegen of overschrijven. Als de opgegeven aanvraag header niet bestaat, wordt deze door deze functie toegevoegd aan de aanvraag.
- Een aanvraag header uit de aanvraag verwijderen.

Aanvragen die worden doorgestuurd naar een originele server, worden weer gegeven in de wijzigingen die zijn aangebracht door deze functie.

Een van de volgende acties kan worden uitgevoerd op een aanvraag header:

Optie|Beschrijving|Voorbeeld
-|-|-
Toevoegen|De opgegeven waarde wordt toegevoegd aan het einde van de bestaande waarde van de aanvraag header.|**Waarde van aanvraag header (client):**<br/>Value1<br/>**Waarde van aanvraag header (regel Engine):**<br/>Enzovoort <br/>**Nieuwe waarde van aanvraag header:** <br/>Value1Value2
Overschrijven|De waarde van de aanvraag header wordt ingesteld op de opgegeven waarde.|**Waarde van aanvraag header (client):**<br/>Value1<br/>**Waarde van aanvraag header (regel Engine):**<br/>Enzovoort<br/>**Nieuwe waarde van aanvraag header:**<br/> Enzovoort <br/>
Verwijderen|Hiermee verwijdert u de opgegeven aanvraag header.|**Waarde van aanvraag header (client):**<br/>Value1<br/>**Configuratie van de header van de client aanvraag wijzigen:**<br/>Verwijder de aanvraag header in de vraag.<br/>**Daardoor**<br/>De opgegeven aanvraag header wordt niet doorgestuurd naar de oorspronkelijke server.

Belang rijke informatie:

- Zorg ervoor dat de waarde die is opgegeven in de optie name, overeenkomt met een exacte overeenkomst voor de gewenste aanvraag header.
- Er wordt geen rekening gehouden met het doel om een header te identificeren. U kunt bijvoorbeeld een van de volgende variaties van de `Cache-Control` naam van de header gebruiken om deze te identificeren:
    - Cache-Control
    - CACHE-CONTROL
    - cachE-Control
- Als u een header naam opgeeft, gebruikt u alleen alfanumerieke tekens, streepjes of onderstrepingen.
- Als u een koptekst verwijdert, voor komt u dat deze wordt doorgestuurd naar een originele server door de Pop's.
- De volgende headers zijn gereserveerd en kunnen niet worden gewijzigd door deze functie:
    - aanvrag
    - host
    - kopen
    - waarschuwing
    - x-doorgestuurd-voor
    - Alle header-namen die beginnen met ' x-EC ' zijn gereserveerd.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-response-header"></a>Reactie header van client wijzigen

Elk antwoord bevat een set antwoord headers waarmee deze wordt beschreven. Deze functie kan:

- De waarde die is toegewezen aan een antwoord header toevoegen of overschrijven. Als de opgegeven antwoord header niet bestaat, wordt deze door deze functie toegevoegd aan het antwoord.
- Een reactie header uit het antwoord verwijderen.

Standaard worden de waarden van de antwoord header gedefinieerd door een bron server en door de Pop's.

Een van de volgende acties kan worden uitgevoerd op een reactie header:

Optie|Beschrijving|Voorbeeld
-|-|-
Toevoegen|De opgegeven waarde wordt toegevoegd aan het einde van de bestaande waarde van de antwoord header.|**Waarde van de antwoord header (client):**<br />Value1<br/>**Waarde van de reactie header (regel Engine):**<br/>Enzovoort<br/>**Nieuwe waarde voor de reactie header:**<br/>Value1Value2
Overschrijven|De waarde van de antwoord header wordt ingesteld op de opgegeven waarde.|**Waarde van de antwoord header (client):**<br/>Value1<br/>**Waarde van de reactie header (regel Engine):**<br/>Enzovoort <br/>**Nieuwe waarde voor de reactie header:**<br/>Enzovoort <br/>
Verwijderen|Hiermee verwijdert u de opgegeven reactie header.|**Waarde van de antwoord header (client):**<br/>Value1<br/>**Configuratie van de client antwoord header wijzigen:**<br/>Verwijder de antwoord header in de vraag.<br/>**Daardoor**<br/>De opgegeven antwoord header wordt niet doorgestuurd naar de aanvrager.

Belang rijke informatie:

- Zorg ervoor dat de waarde die is opgegeven in de optie name, een exacte overeenkomst voor de gewenste reactie header is.
- Er wordt geen rekening gehouden met het doel om een header te identificeren. U kunt bijvoorbeeld een van de volgende variaties van de `Cache-Control` naam van de header gebruiken om deze te identificeren:
    - Cache-Control
    - CACHE-CONTROL
    - cachE-Control
- Als u een koptekst verwijdert, kan deze niet worden doorgestuurd naar de aanvrager.
- De volgende headers zijn gereserveerd en kunnen niet worden gewijzigd door deze functie:
    - accepteren-coderen
    - leeftijd
    - verbinding
    - content-encoding
    - content-length
    - content-Range
    - date
    - server
    - aanhangwagen
    - overdracht-encoding
    - upgrade
    - variatie
    - kopen
    - waarschuwing
    - Alle header-namen die beginnen met ' x-EC ' zijn gereserveerd.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="partial-cache-sharing"></a>Delen van cache gedeeltelijk

**Doel:** Hiermee wordt bepaald of een aanvraag gedeeltelijk in de cache opgeslagen inhoud kan genereren.

Deze gedeeltelijke cache kan vervolgens worden gebruikt om te voldoen aan nieuwe aanvragen voor die inhoud totdat de aangevraagde inhoud volledig in de cache is opgeslagen.

Waarde|Resultaat
-|-
Ingeschakeld|Aanvragen kunnen gedeeltelijk in de cache opgeslagen inhoud genereren.
Uitgeschakeld|Aanvragen kunnen alleen een volledig in cache opgeslagen versie van de aangevraagde inhoud genereren.

**Standaard gedrag:** Geblokkeerd.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="prevalidate-cached-content"></a>Inhoud in cache voorvalideren

**Doel:** Hiermee wordt bepaald of inhoud in de cache in aanmerking komt voor eerdere hervalidaties voordat de TTL verloopt.

Definieer de hoeveelheid tijd vóór de verval datum van de TTL van de aangevraagde inhoud, zodat deze in aanmerking komt voor eerdere hervalidaties.

Belang rijke informatie:

- Als u ' uit ' selecteert, moet de tijds eenheid opnieuw worden ingesteld om te worden uitgevoerd nadat de TTL van de inhoud in de cache is verlopen. De tijd mag niet worden opgegeven en wordt genegeerd.

**Standaard gedrag:** Office. Hervalidatie mag alleen plaatsvinden nadat de TTL van de inhoud in de cache is verlopen.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="proxy-special-headers"></a>Proxy-speciale headers

**Doel:** Hiermee wordt de set met [Verizon-specifieke HTTP-aanvraag headers](cdn-verizon-http-headers.md) gedefinieerd die worden doorgestuurd van een pop naar een bron server.

Belang rijke informatie:

- Elke CDN-specifieke aanvraag header die in deze functie is gedefinieerd, wordt doorgestuurd naar een originele server. Uitgesloten headers worden niet doorgestuurd.
- Als u wilt voor komen dat een CDN-specifieke aanvraag header wordt doorgestuurd, verwijdert u deze uit een lijst met door spaties gescheiden waarden in het veld koptekst.

De volgende HTTP-headers zijn opgenomen in de standaard lijst:
- Kopen
- X-doorgestuurd-voor
- X-doorgestuurd-proto
- X-host
- X-Midgress
- X-gateway-lijst
- X-EG-naam
- Host

**Standaard gedrag:** Alle CDN-specifieke aanvraag headers worden doorgestuurd naar de oorspronkelijke server.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="refresh-zero-byte-cache-files"></a>Cache bestanden met nul bytes vernieuwen

**Doel:** Hiermee wordt bepaald hoe de aanvraag van een HTTP-client voor een cache-Asset van 0 bytes door de Pop's wordt verwerkt.

Geldige waarden zijn:

Waarde|Resultaat
--|--
Ingeschakeld|Zorgt ervoor dat de POP de Asset opnieuw ophaalt van de oorspronkelijke server.
Uitgeschakeld|Hiermee herstelt u het standaard gedrag. Het standaard gedrag is het uitvoeren van geldige cache-assets op aanvraag.

Deze functie is niet vereist voor correcte caching en levering van inhoud, maar kan nuttig zijn als tijdelijke oplossing. Dynamische inhouds generator op originele servers kan bijvoorbeeld per ongeluk leiden tot 0-byte-antwoorden die naar de Pop's worden verzonden. Deze typen reacties worden meestal in de cache opgeslagen door de Pop's. Als u weet dat een antwoord van 0-bytes nooit een geldig antwoord voor dergelijke inhoud is, kan deze functie verhinderen dat deze typen assets aan uw clients worden geleverd.

**Standaard gedrag:** Geblokkeerd.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-cacheable-status-codes"></a>Cacheable status codes instellen

**Doel:** Hiermee definieert u de set status codes die kunnen resulteren in inhoud in de cache.

Caching is standaard alleen ingeschakeld voor 200 OK-antwoorden.

Definieer een door spaties gescheiden set van de gewenste status codes.

Belang rijke informatie:

- Schakel de functie oorsprong zonder cache negeren in. Als deze functie niet is ingeschakeld, worden niet-200 OK-antwoorden mogelijk niet in de cache opgeslagen.
- De set geldige status codes voor deze functie zijn: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 en 505.
- Deze functie kan niet worden gebruikt om caching uit te scha kelen voor antwoorden die een status code van 200 OK genereren.

**Standaard gedrag:** Caching is alleen ingeschakeld voor antwoorden die een status code van 200 OK genereren.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-client-ip-custom-header"></a>Aangepaste client-IP-header instellen

**Doel:** Hiermee wordt een aangepaste header toegevoegd waarmee de client wordt geïdentificeerd op basis van het IP-adres van de aanvraag.

De optie header naam definieert de naam van de aangepaste aanvraag header waarin het IP-adres van de client wordt opgeslagen.

Met deze functie kan een klant bron server client-IP-adressen vinden via een aangepaste aanvraag header. Als de aanvraag afkomstig is uit de cache, wordt de oorspronkelijke server niet op de hoogte gesteld van het IP-adres van de client. Daarom is het raadzaam deze functie te gebruiken met activa die niet in de cache zijn opgeslagen.

Zorg ervoor dat de opgegeven header naam niet overeenkomt met een van de volgende namen:

- Namen van de standaard aanvraag header. Een lijst met namen van standaard headers vindt u in [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Gereserveerde header-namen:
    - doorgestuurd: voor
    - host
    - variatie
    - kopen
    - waarschuwing
    - x-doorgestuurd-voor
    - Alle header-namen die beginnen met ' x-EC ' zijn gereserveerd.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-content-delivery-on-error"></a>Verouderde inhouds levering bij fout

**Doel:** Hiermee wordt bepaald of verlopen inhoud in de cache wordt bezorgd als er een fout optreedt tijdens de hervalidatie van de cache of wanneer de aangevraagde inhoud wordt opgehaald van de bron server van de klant.

Waarde|Resultaat
-|-
Ingeschakeld|Verouderde inhoud wordt aan de aanvrager geleverd als er een fout optreedt tijdens een verbinding met een bron server.
Uitgeschakeld|De fout van de bron server wordt doorgestuurd naar de aanvrager.

**Standaard gedrag:** Geblokkeerd

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-while-revalidate"></a>Verouderd tijdens opnieuw valideren

**Doel:** Verbetert de prestaties doordat de Pop's verouderde inhoud kunnen leveren aan de aanvrager terwijl er een hervalidatie plaatsvindt.

Belang rijke informatie:

- Het gedrag van deze functie is afhankelijk van de geselecteerde tijds eenheid.
    - **Tijds eenheid:** Geef een tijds duur op en selecteer een tijds eenheid (bijvoorbeeld seconden, minuten, uren, enzovoort) om verouderde levering van inhoud toe te staan. Met dit type installatie kan de CDN de tijds duur verlengen die inhoud kan leveren voordat de validatie volgens de volgende formule wordt vereist: **TTL**  +  **verouderd tijdens opnieuw valideren**
    - **Uit:** Selecteer uit om hervalidatie te vereisen voordat een aanvraag voor verouderde inhoud kan worden geleverd.
        - Geef geen tijds duur op omdat deze niet van toepassing is en wordt genegeerd.

**Standaard gedrag:** Office. Er moet een hervalidatie worden uitgevoerd voordat de aangevraagde inhoud kan worden geleverd.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth"></a>Token verificatie

**Doel:** Hiermee wordt bepaald of op tokens gebaseerde verificatie wordt toegepast op een aanvraag.

Als verificatie op basis van tokens is ingeschakeld, worden alleen aanvragen geaccepteerd die een versleuteld token leveren en voldoen aan de vereisten die door dat token zijn opgegeven.

De versleutelings sleutel die wordt gebruikt om token waarden te versleutelen en ontsleutelen, wordt bepaald door de primaire sleutel en de opties voor de back-upsleutel op de token auth-pagina. Houd er wel van uit dat versleutelings sleutels specifiek zijn voor het platform.

**Standaard gedrag:** Geblokkeerd.

Deze functie heeft voor rang op de meeste functies, met uitzonde ring van de functie voor het herschrijven van de URL.

Waarde | Resultaat
------|---------
Ingeschakeld | Beveiligt de aangevraagde inhoud met verificatie op basis van tokens. Alleen aanvragen van clients die een geldig token leveren en voldoen aan de vereisten, worden gehonoreerd. FTP-trans acties worden uitgesloten van verificatie op basis van tokens.
Uitgeschakeld| Hiermee herstelt u het standaard gedrag. Het standaard gedrag is om de verificatie configuratie op basis van tokens toe te staan om te bepalen of een aanvraag wordt beveiligd.

#### <a name="compatibility"></a>Compatibiliteit

Gebruik geen token verificatie met de voor waarde altijd overeenkomen.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-denial-code"></a>Weigerings code voor token auth

**Doel:** Hiermee wordt het type reactie bepaald dat wordt geretourneerd door een gebruiker wanneer een aanvraag wordt geweigerd vanwege verificatie op basis van tokens.

De beschik bare respons codes worden in de volgende tabel weer gegeven.

Reactiecode|Antwoord naam|Beschrijving
-------------|-------------|--------
301|Permanent verplaatst|Met deze status code worden niet-gemachtigde gebruikers omgeleid naar de URL die is opgegeven in de locatie-header.
302|Gevonden|Met deze status code worden niet-gemachtigde gebruikers omgeleid naar de URL die is opgegeven in de locatie-header. Deze status code is de industrie standaard methode voor het uitvoeren van een omleiding.
307|Tijdelijke omleiding|Met deze status code worden niet-gemachtigde gebruikers omgeleid naar de URL die is opgegeven in de locatie-header.
401|Niet geautoriseerd|Door deze status code te combi neren met de reactie header WWW-Authenticated kunt u een gebruiker vragen om verificatie.
403|Verboden|Dit bericht is het standaard status bericht 403 verboden dat een niet-gemachtigde gebruiker te zien krijgt bij het openen van beveiligde inhoud.
404|Kan het bestand niet vinden|Deze status code geeft aan dat de HTTP-client kan communiceren met de server, maar de aangevraagde inhoud is niet gevonden.

#### <a name="compatibility"></a>Compatibiliteit

Gebruik geen code voor de weigerings verificatie van tokens met een voor waarde altijd overeenkomen. In plaats daarvan gebruikt u de sectie voor het **verwerken van aangepaste weigering** op de pagina **token verificatie** van de portal **beheren** . Zie [Azure CDN-assets beveiligen met token verificatie](cdn-token-auth.md)voor meer informatie.

#### <a name="url-redirection"></a>URL-omleiding

Deze functie ondersteunt URL-omleiding naar een door de gebruiker gedefinieerde URL wanneer deze is geconfigureerd om een 3xx-status code te retour neren. Deze door de gebruiker gedefinieerde URL kan worden opgegeven door de volgende stappen uit te voeren:

1. Selecteer een 3xx-respons code voor de functie code voor Denial-en-Token verificatie.
2. Selecteer locatie in de optie optionele header naam.
3. Stel de optie optionele header-waarde in op de gewenste URL.

Als er geen URL is gedefinieerd voor een 3xx-status code, wordt de standaard reactie pagina voor een 3xx-status code geretourneerd aan de gebruiker.

URL-omleiding is alleen van toepassing op 3xx-antwoord codes.

De optie voor de optionele header-waarde ondersteunt alfanumerieke tekens, citaten en spaties.

#### <a name="authentication"></a>Verificatie

Deze functie biedt ondersteuning voor de mogelijkheid om de header WWW-Authenticate toe te voegen bij het reageren op een niet-geautoriseerde aanvraag voor inhoud die is beveiligd met verificatie op basis van tokens. Als de WWW-Authenticate-header is ingesteld op ' Basic ' in uw configuratie, wordt de niet-geautoriseerde gebruiker gevraagd om de account referenties op te vragen.

De bovenstaande configuratie kan worden bereikt door de volgende stappen uit te voeren:

1. Selecteer "401" als de antwoord code voor de functie code voor Denial-en-Token-auth.
2. Selecteer WWW-authenticatie in de optie optionele header naam.
3. Stel de optie optionele header waarde in op ' Basic '.

De WWW-Authenticate-header is alleen van toepassing op 401-antwoord codes.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-ignore-url-case"></a>URL-Case negeren van token verificatie

**Doel:** Hiermee wordt bepaald of URL-vergelijkingen die worden uitgevoerd door verificatie op basis van tokens hoofdletter gevoelig zijn.

De para meters die worden beïnvloed door deze functie zijn:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Geldige waarden zijn:

Waarde|Resultaat
---|----
Ingeschakeld|Zorgt ervoor dat de POP het hoofdletter gebruik negeert bij het vergelijken van Url's voor verificatie parameters op basis van tokens.
Uitgeschakeld|Hiermee herstelt u het standaard gedrag. Het standaard gedrag is voor URL-vergelijkingen voor token verificatie om hoofdletter gevoelig te zijn.

**Standaard gedrag:** Geblokkeerd.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-parameter"></a>Token auth-para meter

**Doel:** Hiermee wordt bepaald of de naam van de op tokens gebaseerde verificatie query teken reeks parameter moet worden gewijzigd.

Belang rijke informatie:

- De waarde optie definieert de naam van de query teken reeks parameter waarlangs een token kan worden opgegeven.
- De optie waarde kan niet worden ingesteld op ec_token.
- Zorg ervoor dat de naam die in de optie waarde is gedefinieerd, alleen geldige URL-tekens bevat.

Waarde|Resultaat
----|----
Ingeschakeld|De waarde optie definieert de naam van de query teken reeks parameter waarlangs de tokens moeten worden gedefinieerd.
Uitgeschakeld|Een token kan worden opgegeven als een niet-gedefinieerde query teken reeks parameter in de aanvraag-URL.

**Standaard gedrag:** Geblokkeerd. Een token kan worden opgegeven als een niet-gedefinieerde query teken reeks parameter in de aanvraag-URL.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-redirect"></a>URL-omleiding

**Doel:** Stuurt aanvragen via de locatie header.

Voor de configuratie van deze functie moet u de volgende opties instellen:

Optie|Beschrijving
-|-
Code|Selecteer de antwoord code die wordt geretourneerd naar de aanvrager.
Patroon van bron &| Deze instellingen definiëren een URI-patroon voor aanvragen dat het type aanvragen identificeert dat kan worden omgeleid. Alleen aanvragen waarvan de URL voldoet aan de volgende criteria, worden omgeleid: <br/> <br/> **Bron (of inhouds toegangs punt):** Selecteer een relatief pad dat een originele server aanduidt. Dit pad is de _/xxxx/_ -sectie en de naam van uw eind punt. <br/><br/> **Bron (patroon):** Er moet een patroon worden gedefinieerd waarmee aanvragen worden geïdentificeerd op relatief pad. In dit reguliere-expressie patroon moet een pad worden gedefinieerd dat direct na het eerder geselecteerde toegangs punt voor inhoud begint (zie hierboven). <br/> -Zorg ervoor dat de aanvraag-URI-criteria (dat wil zeggen, bron & patroon) dat eerder is gedefinieerd, niet conflicteren met de voor waarden voor de overeenkomst die voor deze functie zijn gedefinieerd. <br/> -Geef een patroon op. Als u een lege waarde gebruikt als patroon, worden alle teken reeksen overeenkomen.
Doel| Definieer de URL waarnaar de bovenstaande aanvragen worden omgeleid. <br/><br/> Deze URL dynamisch samen stellen met: <br/> -Een reguliere-expressie patroon <br/>- [HTTP-variabelen](cdn-http-variables.md) <br/><br/> Vervang de waarden die zijn vastgelegd in het bron patroon in het doel patroon door $_n_ , waarbij _n_ een waarde identificeert door de volg orde waarin deze is vastgelegd. $1 staat bijvoorbeeld voor de eerste waarde die is vastgelegd in het bron patroon, terwijl $2 de tweede waarde vertegenwoordigt. <br/>

Het is raadzaam om een absolute URL te gebruiken. Het gebruik van een relatieve URL kan CDN-Url's omleiden naar een ongeldig pad.

**Voorbeeld scenario**

In dit voor beeld ziet u hoe u een Edge CNAME-URL omleidt die wordt omgezet naar deze basis-CDN-URL: http: \/ /marketing.azureedge.net/brochures

Kwalificerende aanvragen worden omgeleid naar deze URL van de basis Edge CNAME: http: \/ /CDN.mydomain.com/resources

Deze URL-omleiding kan worden bereikt via de volgende configuratie: ![ URL-omleiding](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Belangrijke punten:**

- De URL-omleidings functie definieert de Url's van de aanvraag die worden omgeleid. Als gevolg hiervan zijn er geen aanvullende match-voor waarden vereist. Hoewel de match-voor waarde is gedefinieerd als ' Always ', worden alleen aanvragen die verwijzen naar de map ' brochures ' op de oorsprong van de klant, omgeleid.
- Alle overeenkomende aanvragen worden omgeleid naar de URL van de Edge CNAME die is gedefinieerd in de doel optie.
    - Voorbeeld scenario #1:
        - Voorbeeld aanvraag (CDN-URL): http: \/ /marketing.azureedge.net/brochures/widgets.PDF
        - Aanvraag-URL (na omleiding): http: \/ /CDN.mydomain.com/resources/widgets.PDF  
    - Voorbeeld scenario #2:
        - Voorbeeld aanvraag (Edge CNAME URL): http: \/ /marketing.mydomain.com/brochures/widgets.PDF
        - Aanvraag-URL (na omleiding): http: \/ /CDN.mydomain.com/resources/widgets.PDF-voorbeeld scenario
    - Voorbeeld scenario #3:
        - Voorbeeld aanvraag (Edge CNAME URL): http: \/ /brochures.mydomain.com/campaignA/Final/productC.ppt
        - Aanvraag-URL (na omleiding): http: \/ /CDN.mydomain.com/resources/campaignA/Final/productC.ppt 
- De variabele voor het aanvraag schema (% {Scheme}) wordt gebruikt in de doel optie, wat ervoor zorgt dat het schema van de aanvraag ongewijzigd blijft na omleiding.
- De URL-segmenten die van de aanvraag zijn vastgelegd, worden toegevoegd aan de nieuwe URL via ' $1 '.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-rewrite"></a>URL opnieuw genereren

**Doel:** Hiermee herschrijft u de aanvraag-URL.

Belang rijke informatie:

- Voor de configuratie van deze functie moet u de volgende opties instellen:

Optie|Beschrijving
-|-
 Patroon van bron & | Deze instellingen definiëren een URI-patroon voor aanvragen dat het type aanvragen identificeert dat kan worden herschreven. Alleen aanvragen waarvan de URL voldoet aan de volgende criteria, worden herschreven: <br/><br/>  - **Bron (of inhouds toegangs punt):** Selecteer een relatief pad dat een originele server aanduidt. Dit pad is de _/xxxx/_ -sectie en de naam van uw eind punt. <br/><br/> - **Bron (patroon):** Er moet een patroon worden gedefinieerd waarmee aanvragen worden geïdentificeerd op relatief pad. In dit reguliere-expressie patroon moet een pad worden gedefinieerd dat direct na het eerder geselecteerde toegangs punt voor inhoud begint (zie hierboven). <br/> Controleer of de aanvraag-URI-criteria (dat wil zeggen, bron & patroon) die eerder zijn gedefinieerd, niet conflicteren met een van de match-voor waarden die zijn gedefinieerd voor deze functie. Geef een patroon op. Als u een lege waarde gebruikt als patroon, worden alle teken reeksen overeenkomen.
 Doel  |Definieer de relatieve URL waarnaar de bovenstaande aanvragen worden herschreven: <br/>    1. Selecteer een toegangs punt voor inhoud dat een originele server identificeert. <br/>    2. het definiëren van een relatief pad met behulp van: <br/>        -Een reguliere-expressie patroon <br/>        - [HTTP-variabelen](cdn-http-variables.md) <br/> <br/> Vervang de waarden die zijn vastgelegd in het bron patroon in het doel patroon door $_n_ , waarbij _n_ een waarde identificeert door de volg orde waarin deze is vastgelegd. $1 staat bijvoorbeeld voor de eerste waarde die is vastgelegd in het bron patroon, terwijl $2 de tweede waarde vertegenwoordigt.

 Met deze functie kunnen Pop's de URL herschrijven zonder een traditionele omleiding uit te voeren. Dat wil zeggen dat de aanvrager dezelfde antwoord code ontvangt alsof de herschreven URL is aangevraagd.

**Voorbeeld scenario 1**

In dit voor beeld ziet u hoe u een Edge CNAME-URL omleidt die wordt omgezet naar deze basis-CDN-URL: http: \/ /marketing.azureedge.net/brochures/

Kwalificerende aanvragen worden omgeleid naar deze URL van de basis Edge CNAME: http: \/ /MyOrigin.azureedge.net/Resources/

Deze URL-omleiding kan worden bereikt via de volgende configuratie: ![ URL-omleiding](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Voorbeeld scenario 2**

In dit voor beeld ziet u hoe u een Edge CNAME-URL kunt omleiden van hoofd letters naar kleine letters met reguliere expressies.

Deze URL-omleiding kan worden bereikt via de volgende configuratie: ![ URL-omleiding](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)

**Belangrijke punten:**

- De functie voor het herschrijven van URL'S definieert de Url's van de aanvraag die worden herschreven. Als gevolg hiervan zijn er geen aanvullende match-voor waarden vereist. Hoewel de overeenkomst voor waarde is gedefinieerd als ' Always ', worden alleen aanvragen die verwijzen naar de map ' brochures ' op de oorsprong van de klant ' Marketing ', opnieuw geschreven.

- De URL-segmenten die van de aanvraag zijn vastgelegd, worden toegevoegd aan de nieuwe URL via ' $1 '.

#### <a name="compatibility"></a>Compatibiliteit

Deze functie bevat overeenkomende criteria waaraan moet worden voldaan voordat deze kan worden toegepast op een aanvraag. Deze functie is niet compatibel met de volgende match-voor waarden om het instellen van conflicterende match criteria te voor komen:

- Als getal
- CDN-oorsprong
- Client-IP-adres
- Oorsprong van klant
- Aanvraag schema
- URL-pad naar map
- Extensie van URL-pad
- URL-pad bestands naam
- Letterlijke URL-pad
- Regex URL-pad
- Joker teken voor URL-pad
- URL-query-letterlijke waarde
- URL-query parameter
- URL-query-regex
- URL-query Joker teken

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="user-variable"></a>Gebruikers variabele

**Doel:** Alleen voor intern gebruik.

[Terug naar boven](#azure-cdn-from-verizon-premium-rules-engine-features)
## <a name="next-steps"></a>Volgende stappen

- [Referentie voor regels-engine](cdn-verizon-premium-rules-engine-reference.md)
- [Voorwaardelijke expressies in de regelengine](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Criteria voor overeenkomst in de regelengine](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [HTTP-gedrag negeren met de regel engine](cdn-verizon-premium-rules-engine.md)
- [Overzicht van Azure CDN](cdn-overview.md)
