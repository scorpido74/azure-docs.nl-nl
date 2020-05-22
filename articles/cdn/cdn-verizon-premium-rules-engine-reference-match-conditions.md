---
title: Azure CDN van de overeenkomst voor waarden van de Verizon Premium-regels engine | Microsoft Docs
description: Referentie documentatie voor Azure Content Delivery Network van de overeenkomst voor waarden van de Verizon Premium-regels engine.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 3bc439e3244be63bff1c54d3230eda17dfb9d88d
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745593"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN van de overeenkomst voor waarden van de Verizon Premium-regels engine

In dit artikel vindt u gedetailleerde beschrijvingen van de beschik bare Matchings voorwaarden voor de Azure Content Delivery Network (CDN) van de Verizon Premium- [regels engine](cdn-verizon-premium-rules-engine.md).

Het tweede gedeelte van een regel is de match-voor waarde. Een match-voor waarde identificeert specifieke typen aanvragen waarvoor een set functies worden uitgevoerd.

U kunt bijvoorbeeld een match-voor waarde gebruiken voor het volgende:

- Aanvragen voor inhoud filteren op een bepaalde locatie.
- Aanvragen filteren die zijn gegenereerd op basis van een bepaald IP-adres of specifieke land/regio.
- Aanvragen filteren op header-informatie.

## <a name="always-match-condition"></a>Altijd overeenkomen met voor waarde

Met de voor waarde altijd vergelijken wordt een standaardset van functies toegepast op alle aanvragen.

Name | Doel
-----|--------
[Altijd](#always) | Hiermee wordt een standaardset met functies toegepast op alle aanvragen.

## <a name="device-match-condition"></a>Voor waarde voor overeenkomende apparaten

De voor waarde voor het vergelijken van apparaten identificeert aanvragen die zijn gemaakt op basis van de eigenschappen van een mobiel apparaat.  

Name | Doel
-----|--------
[Apparaat](#device) | Identificeert aanvragen die zijn gemaakt op basis van de eigenschappen van een mobiel apparaat.

## <a name="location-match-conditions"></a>Voor waarden voor locatie overeenkomst

Met de voor waarden voor de locatie overeenkomst worden aanvragen geïdentificeerd op basis van de locatie van de aanvrager.

Name | Doel
-----|--------
[Als getal](#as-number) | Identificeert aanvragen die afkomstig zijn van een bepaald netwerk.
[Land/regio](#country) | Identificeert aanvragen die afkomstig zijn uit de opgegeven landen/regio's.

## <a name="origin-match-conditions"></a>Voor waarden van oorsprong

De origin-voor waarden identificeren aanvragen die verwijzen naar Content Delivery Network opslag of een bron server van de klant.

Name | Doel
-----|--------
[CDN-oorsprong](#cdn-origin) | Hiermee worden aanvragen geïdentificeerd voor inhoud die is opgeslagen in Content Delivery Network opslag.
[Oorsprong van klant](#customer-origin) | Identificeert aanvragen voor inhoud die is opgeslagen op een specifieke bron server van de klant.

## <a name="request-match-conditions"></a>Voor waarden voor aanvraag overeenkomst

De aanvraag overeenkomst voor waarden identificeren aanvragen op basis van hun eigenschappen.

Name | Doel
-----|--------
[Client-IP-adres](#client-ip-address) | Identificeert aanvragen die afkomstig zijn van een bepaald IP-adres.
[Cookie parameter](#cookie-parameter) | Controleert de cookies die zijn gekoppeld aan elke aanvraag voor de opgegeven waarde.
[Cookie para meter regex](#cookie-parameter-regex) | Controleert de cookies die zijn gekoppeld aan elke aanvraag voor de opgegeven reguliere expressie.
[Edge CNAME](#edge-cname) | Identificeert aanvragen die verwijzen naar een specifieke Edge CNAME.
[Verwijzende domein](#referring-domain) | Hiermee worden aanvragen geïdentificeerd die van de opgegeven hostnamen zijn genoemd.
[Letterlijke aanvraag header](#request-header-literal) | Hiermee worden aanvragen geïdentificeerd die de opgegeven header set bevatten naar een opgegeven waarde.
[Regex-aanvraag header](#request-header-regex) | Hiermee worden aanvragen geïdentificeerd die de opgegeven header bevatten, ingesteld op een waarde die overeenkomt met de opgegeven reguliere expressie.
[Joker teken voor aanvraag header](#request-header-wildcard) | Hiermee worden aanvragen geïdentificeerd die de opgegeven header bevatten, ingesteld op een waarde die overeenkomt met het opgegeven patroon.
[Aanvraag methode](#request-method) | Hiermee worden aanvragen geïdentificeerd op basis van de HTTP-methode.
[Aanvraag schema](#request-scheme) | Hiermee worden aanvragen geïdentificeerd op basis van het HTTP-protocol.

## <a name="url-match-conditions"></a>Overeenkomende URL-voor waarden

De URL-matching-voor waarden identificeren aanvragen op basis van hun Url's.

Name | Doel
-----|--------
[URL-pad naar map](#url-path-directory) | Hiermee worden aanvragen geïdentificeerd op basis van het relatieve pad.
[Extensie van URL-pad](#url-path-extension) | Hiermee worden aanvragen geïdentificeerd op basis van de bestandsnaam extensie.
[URL-pad bestands naam](#url-path-filename) | Hiermee worden aanvragen geïdentificeerd op basis van de bestands naam.
[Letterlijke URL-pad](#url-path-literal) | Vergelijkt het relatieve pad van een aanvraag naar de opgegeven waarde.
[Regex URL-pad](#url-path-regex) | Vergelijkt het relatieve pad van een aanvraag naar de opgegeven reguliere expressie.
[Joker teken voor URL-pad](#url-path-wildcard) | Vergelijkt het relatieve pad van een aanvraag naar het opgegeven patroon.
[URL-query-letterlijke waarde](#url-query-literal) | Vergelijkt de query reeks van een aanvraag naar de opgegeven waarde.
[URL-query parameter](#url-query-parameter) | Identificeert aanvragen die de opgegeven query reeks parameter bevatten ingesteld op een waarde die overeenkomt met een opgegeven patroon.
[URL-query-regex](#url-query-regex) | Identificeert aanvragen die de opgegeven query reeks parameter bevatten ingesteld op een waarde die overeenkomt met een opgegeven reguliere expressie.
[URL-query Joker teken](#url-query-wildcard) | Vergelijkt de opgegeven waarde met de query teken reeks van de aanvraag.

## <a name="reference-for-rules-engine-match-conditions"></a>Naslag informatie voor de matching voorwaarden van de regels-engine

---

### <a name="always"></a>Altijd

Met de voor waarde altijd vergelijken wordt een standaardset van functies toegepast op alle aanvragen.

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>Als getal

Het AS-nummer netwerk wordt gedefinieerd door het autonome systeem nummer (ASN). 

De optie **overeenkomsten** / **komen niet overeen met** de voor waarden waaronder de voor waarde voor de overeenkomst met het nummer voldoet:

- **Overeenkomsten**: vereist dat het ASN van het client netwerk overeenkomt met een van de opgegeven asn's. 
- **Komt niet overeen**: vereist dat het ASN van het client netwerk niet overeenkomt met een van de opgegeven asn's.

Belang rijke informatie:

- Geef meerdere Asn's op door één spatie te scheiden. 64514 64515 komt bijvoorbeeld overeen met aanvragen die binnenkomen vanaf 64514 of 64515.
- Bepaalde aanvragen retour neren mogelijk geen geldige ASN. Een vraag teken (?) komt overeen met aanvragen waarvoor een geldig ASN niet kan worden bepaald.
- Geef het hele ASN op voor het gewenste netwerk. Gedeeltelijke waarden komen niet overeen.
- Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, is deze match-voor waarde incompatibel met de volgende functies:
  - Volledige cache opvulling
  - Standaard interne Max. leeftijd
  - Interne Max. duur forceren
  - Geen cache oorsprong negeren
  - Intern Max-verouderd

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>CDN-oorsprong

Aan de voor waarde van de CDN-oorsprong wordt voldaan wanneer aan de volgende voor waarden wordt voldaan:

- De inhoud van de CDN-opslag is aangevraagd.
- De aanvraag-URI gebruikt het type inhouds toegangs punt (bijvoorbeeld/000001) dat is gedefinieerd in deze match-voor waarde:
  - CDN-URL: de aanvraag-URI moet het geselecteerde inhouds toegangs punt bevatten.
  - Rand-CNAME-URL: de bijbehorende rand-CNAME-configuratie moet verwijzen naar het geselecteerde inhouds toegangs punt.
  
Belang rijke informatie:

- Het toegangs punt voor inhoud identificeert de service die de aangevraagde inhoud moet verwerken.
- Gebruik niet de instructie en als om bepaalde match voorwaarden te combi neren. Als u bijvoorbeeld een voor waarde voor het vergelijken van een CDN-oorsprong combineert met de voor waarde matching van klant, zou er een match-patroon ontstaan dat nooit kan worden gevonden. Daarom kunnen twee CDN-oorsprong matching voorwaarden niet worden gecombineerd met een AND-instructie.

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Client-IP-adres

De optie **treffers** / **komt niet overeen met** de voor waarden waaronder de voor waarde aan het client-IP-adres voldoet:

- **Overeenkomsten**: vereist dat het IP-adres van de client overeenkomt met een van de opgegeven IP-adressen. 
- **Komt niet overeen**: vereist dat het IP-adres van de client niet overeenkomt met een van de opgegeven IP-adressen. 

Belang rijke informatie:

- Gebruik CIDR-notatie.
- Geef meerdere IP-adressen en/of IP-adres blokken op door één spatie te scheiden. Bijvoorbeeld:
  - **IPv4-voor beeld**: 1.2.3.4 10.20.30.40 komt overeen met alle aanvragen die binnenkomen bij een van de adressen 1.2.3.4 of 10.20.30.40.
  - **IPv6-voor beeld**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 komt overeen met aanvragen die afkomstig zijn van een van de adressen 1:2:3:4:5:6:7:8 of 10:20:30:40:50:60:70:80.
- De syntaxis voor een IP-adres blok is het basis-IP-adres, gevolgd door een slash en de grootte van het voor voegsel. Bijvoorbeeld:
  - **IPv4-voor beeld**: 5.5.5.64/26 komt overeen met de aanvragen die afkomstig zijn van 5.5.5.64 via 5.5.5.127.
  - **IPv6-voor beeld**: 1:2:3:/48 komt overeen met aanvragen die afkomstig zijn van de adressen 1:2:3:0:0:0:0:0 tot en met 1:2: 3: FFFF: FFFF: FFFF: FFFF: FFFF.
- Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, is deze match-voor waarde incompatibel met de volgende functies:
  - Volledige cache opvulling
  - Standaard interne Max. leeftijd
  - Interne Max. duur forceren
  - Geen cache oorsprong negeren
  - Intern Max-verouderd

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Cookie parameter

De optie **treffers** / **komt niet overeen met** de voor waarden waaronder de voor waarde voor de cookie parameter overeenkomst wordt bereikt.

- **Overeenkomsten**: vereist dat een aanvraag de opgegeven cookie bevat met een waarde die overeenkomt met ten minste één van de waarden die zijn gedefinieerd in deze match-voor waarde.
- **Komt niet overeen**: vereist dat de aanvraag voldoet aan een van de volgende criteria:
  - Het bevat niet de opgegeven cookie.
  - Het bevat de opgegeven cookie, maar de bijbehorende waarde komt niet overeen met een van de waarden die zijn gedefinieerd in deze match-voor waarde.
  
Belang rijke informatie:

- Cookie naam:
  - Omdat joker tekens, met inbegrip van sterretjes (*), niet worden ondersteund wanneer u een cookie naam opgeeft, komen alleen exacte cookie namen overeen voor vergelijking.
  - Er kan slechts één cookie naam worden opgegeven per exemplaar van deze match-voor waarde.
  - Cookie naam vergelijkingen zijn niet hoofdletter gevoelig.
- Cookie waarde:
  - Geef meerdere cookie waarden op door één enkele spatie te scheiden.
  - Een cookie waarde kan profiteren van [Joker teken waarden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Als er geen joker teken waarde is opgegeven, wordt alleen een exacte overeenkomst aan deze overeenkomst voldaan. Als u bijvoorbeeld ' waarde ' opgeeft, komt dit overeen met ' waarde ', maar niet ' waarde1 ' of ' Value2 '.
  - Gebruik de optie **Case negeren** om te bepalen of een hoofdletter gevoelige vergelijking wordt uitgevoerd op basis van de cookie waarde van de aanvraag.
- Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, is deze match-voor waarde incompatibel met de volgende functies:
  - Volledige cache opvulling
  - Standaard interne Max. leeftijd
  - Interne Max. duur forceren
  - Geen cache oorsprong negeren
  - Intern Max-verouderd

[Terug naar boven](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Cookie para meter regex

Met de voor waarde regex van de cookie para meter definieert u een cookie naam en-waarde. U kunt [reguliere expressies](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) gebruiken om de gewenste cookie waarde te definiëren.

De optie **treffers** / **komt niet overeen met** de voor waarden waaronder de voor waarde regex van de cookie parameter matching wordt bereikt.

- **Overeenkomsten**: vereist dat een aanvraag de opgegeven cookie bevat met een waarde die overeenkomt met de opgegeven reguliere expressie.
- **Komt niet overeen**: vereist dat de aanvraag voldoet aan een van de volgende criteria:
  - Het bevat niet de opgegeven cookie.
  - Het bevat de opgegeven cookie, maar de bijbehorende waarde komt niet overeen met de opgegeven reguliere expressie.
  
Belang rijke informatie:

- Cookie naam:
  - Omdat reguliere expressies en Joker tekens, met inbegrip van sterretjes (*), niet worden ondersteund wanneer u een cookie naam opgeeft, komen alleen exacte cookie namen overeen voor vergelijking.
  - Er kan slechts één cookie naam worden opgegeven per exemplaar van deze match-voor waarde.
  - Cookie naam vergelijkingen zijn niet hoofdletter gevoelig.
- Cookie waarde:
  - Een cookie waarde kan gebruikmaken van reguliere expressies.
  - Gebruik de optie **Case negeren** om te bepalen of een hoofdletter gevoelige vergelijking wordt uitgevoerd op basis van de cookie waarde van de aanvraag.
- Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, is deze match-voor waarde incompatibel met de volgende functies:
  - Volledige cache opvulling
  - Standaard interne Max. leeftijd
  - Interne Max. duur forceren
  - Geen cache oorsprong negeren
  - Intern Max-verouderd

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Land/regio

U kunt de land code van een land opgeven. 

De **overeenkomende** / optie komt**niet overeen met** de voor waarden waaronder aan de voor waarde voor land overeenkomst wordt voldaan:

- **Overeenkomsten**: vereist dat de aanvraag de opgegeven land code waarden bevat. 
- **Komt niet overeen**: vereist dat de aanvraag de opgegeven land nummer waarden bevat.

Belang rijke informatie:

- Geef meerdere land codes op door één spatie te scheiden.
- Joker tekens worden niet ondersteund wanneer u een land nummer opgeeft.
- De land codes ' EU ' en ' AP ' omvatten niet alle IP-adressen in deze regio's.
- Bepaalde aanvragen retour neren mogelijk geen geldige land code. Een vraag teken (?) komt overeen met aanvragen waarvoor een geldige land code niet kan worden bepaald.
- Land codes zijn hoofdletter gevoelig.
- Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, is deze match-voor waarde incompatibel met de volgende functies:
  - Volledige cache opvulling
  - Standaard interne Max. leeftijd
  - Interne Max. duur forceren
  - Geen cache oorsprong negeren
  - Intern Max-verouderd

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Land filtering implementeren met behulp van de regel engine

Met deze match-voor waarde kunt u een groot aantal aanpassingen uitvoeren op basis van de locatie van waaruit een aanvraag afkomstig is. Het gedrag van de functie voor het filteren van landen kan bijvoorbeeld worden gerepliceerd via de volgende configuratie:

- URL-pad naar Joker teken: Stel de [voor waarde URL-pad voor joker tekens](#url-path-wildcard) in op de map die wordt beveiligd. 
    Voeg aan het einde van het relatieve pad een sterretje toe om ervoor te zorgen dat de toegang tot alle onderliggende items wordt beperkt door deze regel.

- Land overeenkomst: Stel de voor waarde land overeenkomst in op de gewenste set van landen/regio's.
  - Toestaan: Stel de voor waarde voor land overeenkomst in op **niet overeenkomen** met het toestaan dat alleen de opgegeven landen/regio's toegang hebben tot inhoud die is opgeslagen op de locatie die is gedefinieerd door de voor waarde joker tekens voor URL-pad.
  - Blok keren: Stel de voor waarde voor land overeenkomst in op **overeenkomsten** om te voor komen dat de opgegeven landen/regio's toegang hebben tot inhoud die is opgeslagen op de locatie die is gedefinieerd door de voor waarde joker tekens voor URL-pad.

- Toegang weigeren (403): Schakel de [functie toegang weigeren (403)](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) in om het deel van de functie voor het filteren van landen te repliceren of blok keren.

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Oorsprong van klant

Belang rijke informatie:

- Aan de voor waarde voor de oorsprong van de klant wordt voldaan, ongeacht of de inhoud wordt aangevraagd via een CDN-URL of een rand-CNAME-URL die verwijst naar de oorsprong van de geselecteerde klant.
- Een configuratie van een klant oorsprong waarnaar wordt verwezen door een regel, kan niet worden verwijderd van de pagina klant oorsprong. Voordat u een configuratie van de klant oorsprong probeert te verwijderen, moet u ervoor zorgen dat er niet naar de volgende configuraties wordt verwezen:
  - Voor waarde voor overeenkomst van klant oorsprong
  - Een Edge CNAME-configuratie
- Gebruik niet de instructie en als om bepaalde match voorwaarden te combi neren. Als u bijvoorbeeld een voor waarde voor het vergelijken van de oorsprong van een klant met een voor waarde voor het vergelijken van de CDN-oorsprong combineert, wordt een match-patroon gemaakt dat nooit kan worden gevonden. Daarom kunnen twee klant vergelijkings voorwaarden niet worden gecombineerd met een AND-instructie.

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Apparaat

De voor waarde voor het vergelijken van apparaten identificeert aanvragen die zijn gemaakt op basis van de eigenschappen van een mobiel apparaat. De detectie van mobiele apparaten wordt bereikt via [WURFL](http://wurfl.sourceforge.net/). 

De **overeenkomende** / optie komt**niet overeen met** de voor waarden waaronder aan de voor waarde voor het apparaat wordt voldaan:

- **Overeenkomsten**: vereist dat het apparaat van de aanvrager overeenkomt met de opgegeven waarde. 
- **Komt niet overeen**: vereist dat het apparaat van de aanvrager niet overeenkomt met de opgegeven waarde.

Belang rijke informatie:

- Gebruik de optie **Case negeren** om op te geven of de opgegeven waarde hoofdletter gevoelig is.
- Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, is deze match-voor waarde incompatibel met de volgende functies:
  - Volledige cache opvulling
  - Standaard interne Max. leeftijd
  - Interne Max. duur forceren
  - Geen cache oorsprong negeren
  - Intern Max-verouderd

#### <a name="string-type"></a>Teken reeks type

Een WURFL-capaciteit accepteert meestal een combi natie van cijfers, letters en symbolen. Als gevolg van de flexibele aard van deze mogelijkheid moet u kiezen hoe de waarde die is gekoppeld aan deze match voorwaarde wordt geïnterpreteerd. De volgende tabel beschrijft de beschik bare set opties:

Type     | Beschrijving
---------|------------
Letterlijke  | Selecteer deze optie om te voor komen dat de meeste tekens gebruikmaken van de [letterlijke waarde](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Vervanging | Selecteer deze optie om te profiteren van alle [joker tekens] ([Joker teken waarden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)).
Reguliere    | Selecteer deze optie om [reguliere expressies](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)te gebruiken. Reguliere expressies zijn handig voor het definiëren van een patroon van tekens.

#### <a name="wurfl-capabilities"></a>WURFL mogelijkheden

Een WURFL-mogelijkheid verwijst naar een categorie die mobiele apparaten beschrijft. De geselecteerde mogelijkheid bepaalt het type beschrijving van mobiele apparaten dat wordt gebruikt om aanvragen te identificeren.

De volgende tabel geeft een lijst van de WURFL-mogelijkheden en de bijbehorende variabelen voor de regel engine.

> [!NOTE]
> De volgende variabelen worden ondersteund in de header **client aanvraag wijzigen** en de functies van de **client reactie header wijzigen** .

Mogelijkheid | Variabele | Beschrijving | Voorbeeldwaarden
-----------|----------|-------------|----------------
Merk naam | % {wurfl_cap_brand_name} | Een teken reeks die de merk naam van het apparaat aangeeft. | Samsung
Besturings systeem van apparaat | % {wurfl_cap_device_os} | Een teken reeks die het besturings systeem aangeeft dat op het apparaat is geïnstalleerd. | iOS
Versie besturingssysteem apparaat | % {wurfl_cap_device_os_version} | Een teken reeks die het versie nummer aangeeft van het besturings systeem dat op het apparaat is geïnstalleerd. | 1.0.1
Dubbele stand | % {wurfl_cap_dual_orientation} | Een Booleaanse waarde die aangeeft of het apparaat dubbele stand-by ondersteunt. | waar
HTML voorkeurs DTD | % {wurfl_cap_html_preferred_dtd} | Een teken reeks die de voorkeurs Document Type Definition (DTD) van het mobiele apparaat voor HTML-inhoud aangeeft. | geen<br/>xhtml_basic<br/>HTML5
Afbeelding inbrengen | % {wurfl_cap_image_inlining} | Een Booleaanse waarde die aangeeft of het apparaat base64-gecodeerde installatie kopieën ondersteunt. | onjuist
Is Android | % {wurfl_vcap_is_android} | Een Booleaanse waarde die aangeeft of het apparaat het Android-besturings systeem gebruikt. | waar
Is IOS | % {wurfl_vcap_is_ios} | Een Booleaanse waarde die aangeeft of het apparaat gebruikmaakt van iOS. | onjuist
Is slimme TV | % {wurfl_cap_is_smarttv} | Een Booleaanse waarde die aangeeft of het apparaat een slimme TV is. | onjuist
Is smartphone | % {wurfl_vcap_is_smartphone} | Een Booleaanse waarde die aangeeft of het apparaat een smartphone is. | waar
Is Tablet | % {wurfl_cap_is_tablet} | Een Booleaanse waarde die aangeeft of het apparaat een Tablet is. Deze beschrijving is onafhankelijk van het besturings systeem. | waar
Is draadloos apparaat | % {wurfl_cap_is_wireless_device} | Een Booleaanse waarde die aangeeft of het apparaat wordt beschouwd als een draadloos apparaat. | waar
Marketing naam | % {wurfl_cap_marketing_name} | Een teken reeks die de marketing naam van het apparaat aangeeft. | BlackBerry 8100 Pearl
Mobiele browser | % {wurfl_cap_mobile_browser} | Een teken reeks die de browser aangeeft die wordt gebruikt om inhoud van het apparaat aan te vragen. | Chrome
Mobiele browser versie | % {wurfl_cap_mobile_browser_version} | Een teken reeks die de versie aangeeft van de browser die wordt gebruikt om inhoud van het apparaat aan te vragen. | 31
Model naam | % {wurfl_cap_model_name} | Een teken reeks die de naam van het model van het apparaat aangeeft. | S3
Progressieve down load | % {wurfl_cap_progressive_download} | Een Booleaanse waarde die aangeeft of het apparaat het afspelen van audio en video ondersteunt terwijl het nog steeds wordt gedownload. | waar
Release datum | % {wurfl_cap_release_date} | Een teken reeks die het jaar en de maand aangeeft waarop het apparaat is toegevoegd aan de WURFL-data base.<br/><br/>Formatteer`yyyy_mm` | 2013_december
Resolutie hoogte | % {wurfl_cap_resolution_height} | Een geheel getal dat de hoogte van het apparaat in pixels aangeeft. | 768
Resolutie breedte | % {wurfl_cap_resolution_width} | Een geheel getal dat de breedte van het apparaat in pixels aangeeft. | 1024

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>Edge CNAME

Belang rijke informatie:

- De lijst met beschik bare rand-CNAMEs is beperkt tot de Edge-CNAME die zijn geconfigureerd op de pagina Edge CNAMEs voor het platform waarop de regel engine wordt geconfigureerd.
- Voordat u probeert een Edge CNAME-configuratie te verwijderen, moet u ervoor zorgen dat er niet naar de voor waarde voor de CNAME-rand overeenkomst wordt verwezen. Edge CNAME-configuraties die zijn gedefinieerd in een regel, kunnen niet worden verwijderd van de pagina Edge CNAMEs.
- Gebruik niet de instructie en als om bepaalde match voorwaarden te combi neren. Als u bijvoorbeeld een voor waarde voor het afstemmen van een Edge-overeenkomst combineert met de voor waarde matching van klant, zou er een match-patroon ontstaan dat nooit kan worden gevonden. Daarom kunnen twee Edge-matching voorwaarden van de CNAME niet worden gecombineerd met een AND-instructie.
- Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, is deze match-voor waarde incompatibel met de volgende functies:
  - Volledige cache opvulling
  - Standaard interne Max. leeftijd
  - Interne Max. duur forceren
  - Geen cache oorsprong negeren
  - Intern Max-verouderd

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Verwijzende domein

De hostnaam die is gekoppeld aan de verwijzings groep waarlangs inhoud is aangevraagd, bepaalt of aan de voor waarde van de verwijzings domein wordt voldaan.

De optie **treffers** / **komt niet overeen met** de voor waarden waaronder de voor waarde voor het vergelijken van het domein wordt voldaan:

- **Overeenkomsten**: de verwijzende hostnaam moet overeenkomen met de opgegeven waarden. 
- **Komt niet overeen**: vereist dat de naam van de verwijzende host niet overeenkomt met de opgegeven waarde.

Belang rijke informatie:

- Geef meerdere hostnamen op door één spatie te scheiden.
- Deze match-voor waarde ondersteunt [Joker teken waarden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- Als de opgegeven waarde geen asterisk bevat, moet deze exact overeenkomen voor de hostnaam van de verwijzende host. Als u bijvoorbeeld ' mydomain.com ' opgeeft, komt dit niet overeen met ' www.mydomain.com '.
- Gebruik de optie **Case negeren** om te bepalen of er een hoofdletter gevoelige vergelijking wordt gemaakt.
- Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, is deze match-voor waarde incompatibel met de volgende functies:
  - Volledige cache opvulling
  - Standaard interne Max. leeftijd
  - Interne Max. duur forceren
  - Geen cache oorsprong negeren
  - Intern Max-verouderd

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Letterlijke aanvraag header

De optie **treffers** / **komt niet overeen met** de voor waarden waaronder wordt voldaan aan de voor waarde van de letterlijke koptekst van de aanvraag header.

- **Overeenkomsten**: vereist dat de aanvraag de opgegeven header bevat. De waarde van het veld moet overeenkomen met het account dat is gedefinieerd in deze match-voor waarde.
- **Komt niet overeen**: vereist dat de aanvraag voldoet aan een van de volgende criteria:
  - De opgegeven header is niet opgenomen.
  - Deze bevat de opgegeven header, maar de bijbehorende waarde komt niet overeen met die in deze match-voor waarde.
  
Belang rijke informatie:

- Vergelijkingen van header namen zijn altijd niet hoofdletter gevoelig. Gebruik de optie **Case negeren** om de hoofdletter gevoeligheid van de vergelijking van header waarden te beheren.
- Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, is deze match-voor waarde incompatibel met de volgende functies:
  - Volledige cache opvulling
  - Standaard interne Max. leeftijd
  - Interne Max. duur forceren
  - Geen cache oorsprong negeren
  - Intern Max-verouderd

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Regex-aanvraag header

De optie **treffers** / **komt niet overeen met** de voor waarden waaronder de voor waarde voor regex matching van de aanvraag header is voldaan.

- **Overeenkomsten**: vereist dat de aanvraag de opgegeven header bevat. De waarde moet overeenkomen met het patroon dat is gedefinieerd in de opgegeven [reguliere expressie](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Komt niet overeen**: vereist dat de aanvraag voldoet aan een van de volgende criteria:
  - De opgegeven header is niet opgenomen.
  - Deze bevat de opgegeven header, maar de bijbehorende waarde komt niet overeen met de opgegeven reguliere expressie.

Belang rijke informatie:

- Header naam:
  - Vergelijkingen van header namen zijn hoofdletter gevoelig.
  - Vervang spaties in de naam van de header door %20.
- Waarde van header:
  - Een header waarde kan gebruikmaken van reguliere expressies.
  - Gebruik de optie **Case negeren** om de hoofdletter gevoeligheid van de vergelijking van header waarden te beheren.
  - Er wordt alleen aan de voor waarde match voldaan wanneer een header waarde exact overeenkomt met ten minste één van de opgegeven patronen.
- Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, is deze match-voor waarde incompatibel met de volgende functies:
  - Volledige cache opvulling
  - Standaard interne Max. leeftijd
  - Interne Max. duur forceren
  - Geen cache oorsprong negeren
  - Intern Max-verouderd

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Joker teken voor aanvraag header

De optie **treffers** / **komt niet overeen met** de voor waarden waaronder de voor waarde voor het vergelijken van joker tekens in de aanvraag header wordt bereikt.

- **Overeenkomsten**: vereist dat de aanvraag de opgegeven header bevat. De waarde moet overeenkomen met ten minste één van de waarden die zijn gedefinieerd in deze match-voor waarde.
- **Komt niet overeen**: vereist dat de aanvraag voldoet aan een van de volgende criteria:
  - De opgegeven header is niet opgenomen.
  - Deze bevat de opgegeven header, maar de bijbehorende waarde komt niet overeen met een van de opgegeven waarden.
  
Belang rijke informatie:

- Header naam:
  - Vergelijkingen van header namen zijn hoofdletter gevoelig.
  - Spaties in de naam van de header moeten worden vervangen door %20. U kunt deze waarde ook gebruiken om spaties in een header waarde op te geven.
- Waarde van header:
  - Een header waarde kan gebruikmaken van [Joker teken waarden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Gebruik de optie **Case negeren** om de hoofdletter gevoeligheid van de vergelijking van header waarden te beheren.
  - Er wordt voldaan aan deze match-voor waarde wanneer een header waarde exact overeenkomt met ten minste een van de opgegeven patronen.
  - Geef meerdere waarden op door één spatie te scheiden.
- Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, is deze match-voor waarde incompatibel met de volgende functies:
  - Volledige cache opvulling
  - Standaard interne Max. leeftijd
  - Interne Max. duur forceren
  - Geen cache oorsprong negeren
  - Intern Max-verouderd

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Aanvraag methode

Er wordt alleen voldaan aan de voor waarde voor de overeenkomst methode voor de aanvraag wanneer assets worden aangevraagd via de geselecteerde aanvraag methode. De beschik bare aanvraag methoden zijn:

- GET
- HEAD
- POST
- Opties
- PUT
- DELETE
- TRACERINGS
- VERBINDING maken met

Belang rijke informatie:

- Standaard kan alleen de GET-aanvraag methode in de cache opgeslagen inhoud in het netwerk genereren. Alle andere aanvraag methoden worden via het netwerk geproxyeerd.
- Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, is deze match-voor waarde incompatibel met de volgende functies:
  - Volledige cache opvulling
  - Standaard interne Max. leeftijd
  - Interne Max. duur forceren
  - Geen cache oorsprong negeren
  - Intern Max-verouderd

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>Aanvraag schema

Er wordt alleen aan de voor waarde matching schema voor de aanvraag voldaan wanneer assets worden aangevraagd via het geselecteerde protocol. De beschik bare protocollen zijn:

- HTTP
- HTTPS

Belang rijke informatie:

- Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, is deze match-voor waarde incompatibel met de volgende functies:
  - Volledige cache opvulling
  - Standaard interne Max. leeftijd
  - Interne Max. duur forceren
  - Geen cache oorsprong negeren
  - Intern Max-verouderd

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>URL-pad naar map

Identificeert een aanvraag op basis van het relatieve pad, waarbij de bestands naam van het aangevraagde activum wordt uitgesloten.

De optie **treffers** / **komt niet overeen met** de voor waarden waaronder de voor waarde directory-pad naar URL overeenkomt.

- **Overeenkomsten**: vereist dat de aanvraag een relatief URL-pad bevat, met uitzonde ring van de bestands naam, die overeenkomt met het opgegeven URL-patroon.
- **Komt niet overeen**: vereist dat de aanvraag een relatief URL-pad bevat, met uitzonde ring van de bestands naam, dat niet overeenkomt met het opgegeven URL-patroon.

Belang rijke informatie:

- Gebruik de optie **relatief aan** om op te geven of de vergelijking van de URL vóór of na het toegangs punt van de inhoud wordt gestart. Het toegangs punt voor inhoud is het gedeelte van het pad dat wordt weer gegeven tussen de Verizon CDN-hostnaam en het relatieve pad naar het aangevraagde activum (bijvoorbeeld/800001/CustomerOrigin). Hiermee wordt een locatie aangeduid met het server type (bijvoorbeeld CDN of oorsprong van de klant) en het account nummer van uw klant.

   De volgende waarden zijn beschikbaar voor de optie **relatief op** :
  - **Root**: geeft aan dat het URL-vergelijkings punt direct na de CDN-hostnaam begint. 

  Bijvoorbeeld: http: \/ /WPC.0001. &lt; domein &gt; / **800001/myorigin/MyFolder**/index.htm

  - **Oorsprong**: geeft aan dat het URL-vergelijkings punt begint na het inhouds toegangs punt (bijvoorbeeld/000001 of/800001/myorigin). Omdat \* AZUREEDGE.net CNAME wordt gemaakt ten opzichte van de bronmap op de hostnaam van het Verizon CDN standaard, moeten Azure CDN gebruikers de waarde **Origin** gebruiken. 

  Bijvoorbeeld: https: \/ / &lt; endpoint &gt; . azureedge.net/**MyFolder**/index.htm 

  Deze URL verwijst naar de volgende Verizon CDN-hostnaam: http: \/ /WPC.0001. &lt; domein &gt; /800001/myorigin/**MyFolder**/index.htm

- Een Edge CNAME-URL wordt opnieuw naar een CDN-URL geschreven vóór de vergelijking van de URL.

    Bijvoorbeeld: beide van de volgende Url's verwijzen naar hetzelfde activum en hebben daarom hetzelfde URL-pad.
  - CDN-URL: http: \/ /WPC.0001. &lt; domein &gt; -800001/CustomerOrigin/Path/Asset.htm
    
  - Edge CNAME-URL: http: \/ / &lt; endpoint &gt; . azureedge.net/Path/Asset.htm
    
    Extra informatie:
  - Aangepast domein: https: \/ /My.domain.com/Path/Asset.htm
    
    - URL-pad (ten opzichte van hoofdmap):/800001/CustomerOrigin/path/
    
    - URL-pad (ten opzichte van oorsprong):/Path/

- Het gedeelte van de URL dat wordt gebruikt voor de vergelijking van de URL eindigt net vóór de bestands naam van de aangevraagde Asset. Een afsluitende slash is het laatste teken in dit type pad.

- Vervang eventuele spaties in het URL-pad patroon door %20.

- Elk patroon van een URL-pad kan een of meer sterretjes (*) bevatten, waarbij elk sterretje overeenkomt met een reeks van een of meer tekens.

- Geef in het patroon meerdere URL-paden op door één enkele spatie te scheiden.

    Bijvoorbeeld: */Sales/*/Marketing/

- Een URL-pad specificatie kan gebruikmaken van [joker tekens](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Gebruik de optie **Case negeren** om te bepalen of er een hoofdletter gevoelige vergelijking moet worden uitgevoerd.

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>Extensie van URL-pad

Hiermee worden aanvragen geïdentificeerd aan de hand van de bestands extensie van de aangevraagde Asset.

De optie **treffers** / **komt niet overeen met** de voor waarden waaronder de voor waarde voor de extensie van het URL-pad voldoet aan.

- **Overeenkomsten**: vereist dat de URL van de aanvraag een bestands extensie bevat die exact overeenkomt met het opgegeven patroon.

   Als u bijvoorbeeld ' htm ' opgeeft, worden de htm-assets overeenkomen, maar niet ' HTML-assets '.  

- **Komt niet overeen**: vereist dat de URL-aanvraag een bestands extensie bevat die niet overeenkomt met het opgegeven patroon.

Belang rijke informatie:

- Geef de bestands extensies op die overeenkomen in het vak **waarde** . Geen voorloop periode bevatten; Gebruik bijvoorbeeld htm in plaats van. htm.

- Gebruik de optie **Case negeren** om te bepalen of er een hoofdletter gevoelige vergelijking moet worden uitgevoerd.

- Geef meerdere bestands extensies op door elke uitbrei ding met één spatie te scheiden. 

    Bijvoorbeeld: htm HTML

- Als u bijvoorbeeld ' htm ' opgeeft, komt dit overeen met ' htm-' assets, maar niet ' HTML '-assets.

#### <a name="sample-scenario"></a>Voorbeeld scenario

De volgende voorbeeld configuratie gaat ervan uit dat aan deze match-voor waarde is voldaan wanneer een aanvraag overeenkomt met een van de opgegeven extensies.

Waarde-specificatie: ASP aspx php html

Aan deze match-voor waarde wordt voldaan wanneer Url's worden gevonden die met de volgende extensies eindigen:

- . asp
- . aspx
- . php
- .html

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>URL-pad bestands naam

Hiermee worden aanvragen geïdentificeerd op basis van de bestands naam van de aangevraagde Asset. In het kader van deze match-voor waarde bestaat een bestands naam uit de naam van het aangevraagde activum, een punt en de bestands extensie (bijvoorbeeld index. html).

De optie **treffers** / **komt niet overeen met** de voor waarden waaronder de voor waarde voor de bestands naam van URL-paden overeenkomt.

- **Overeenkomsten**: vereist dat de aanvraag een bestands naam bevat in het URL-pad dat overeenkomt met het opgegeven patroon.
- **Komt niet overeen**: vereist dat de aanvraag een bestands naam in het URL-pad bevat dat niet overeenkomt met het opgegeven patroon.

Belang rijke informatie:

- Gebruik de optie **Case negeren** om te bepalen of er een hoofdletter gevoelige vergelijking moet worden uitgevoerd.

- Als u meerdere bestands extensies wilt opgeven, scheidt u elke extensie met één spatie.

    Bijvoorbeeld: index. htm index. html

- Vervang spaties in een bestands naam waarde door "%20".

- De waarde van een bestands naam kan gebruikmaken van [joker tekens](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Elk bestands naam patroon kan bijvoorbeeld bestaan uit een of meer sterretjes (*), waarbij elk sterretje overeenkomt met een reeks van een of meer tekens.

- Als er geen joker tekens worden opgegeven, wordt alleen een exacte overeenkomst aan deze overeenkomst voldaan.

    Als u bijvoorbeeld ' Presentation. ppt ' opgeeft, komt dit overeen met een Asset met de naam ' Presentation. ppt ', maar niet de naam ' Presentation. pptx '.

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>Letterlijke URL-pad

Vergelijkt het URL-pad van een aanvraag, inclusief de bestands naam, naar de opgegeven waarde.

De optie **treffers** / **komt niet overeen met** de voor waarden waaronder de voor waarde voor de letterlijke URL voor het pad naar url's wordt bereikt.

- **Overeenkomsten**: vereist dat de aanvraag een URL-pad bevat dat overeenkomt met het opgegeven patroon.
- **Komt niet overeen**: vereist dat de aanvraag een URL-pad bevat dat niet overeenkomt met het opgegeven patroon.

Belang rijke informatie:

- Gebruik de optie **relatief aan** om op te geven of het URL-vergelijkings punt begint vóór of na het toegangs punt van de inhoud. 

    De volgende waarden zijn beschikbaar voor de optie **relatief op** :
  - **Root**: geeft aan dat het URL-vergelijkings punt direct na de CDN-hostnaam begint.

    Bijvoorbeeld: http: \/ /WPC.0001. &lt; domein &gt; / **800001/myorigin/MyFolder/index.htm**

  - **Oorsprong**: geeft aan dat het URL-vergelijkings punt begint na het inhouds toegangs punt (bijvoorbeeld/000001 of/800001/myorigin). Omdat \* AZUREEDGE.net CNAME wordt gemaakt ten opzichte van de bronmap op de hostnaam van het Verizon CDN standaard, moeten Azure CDN gebruikers de waarde **Origin** gebruiken. 

    Bijvoorbeeld: https: \/ / &lt; eind punt &gt; . azureedge.net/**MyFolder/index.htm**

  Deze URL verwijst naar de volgende Verizon CDN-hostnaam: http: \/ /WPC.0001. &lt; domein &gt; /800001/myorigin/**MyFolder/index.htm**

- Een Edge CNAME URL wordt herschreven naar een CDN-URL voordat een URL-vergelijking wordt gemaakt.

Zo verwijzen beide van de volgende Url's naar hetzelfde activum en hebben ze daarom hetzelfde URL-pad:

- CDN-URL: http: \/ /WPC.0001. &lt; domein &gt; -800001/CustomerOrigin/Path/Asset.htm
- Edge CNAME-URL: http: \/ / &lt; endpoint &gt; . azureedge.net/Path/Asset.htm

    Extra informatie:
    
    - URL-pad (ten opzichte van hoofdmap):/800001/CustomerOrigin/path/asset.htm
   
    - URL-pad (ten opzichte van oorsprong):/path/asset.htm

- Query reeksen in de URL worden genegeerd.
- Gebruik de optie **Case negeren** om te bepalen of er een hoofdletter gevoelige vergelijking moet worden uitgevoerd.
- De waarde die is opgegeven voor deze match-voor waarde wordt vergeleken met het relatieve pad van de exacte aanvraag van de client.

- Als u alle aanvragen voor een bepaalde map wilt vergelijken, gebruikt u de [map URL path](#url-path-directory) of de voor waarde [joker tekens voor URL-pad](#url-path-wildcard) .

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>Regex URL-pad

Vergelijkt het URL-pad van een aanvraag naar de opgegeven [reguliere expressie](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

De optie **treffers** / **komt niet overeen met** de voor waarden waaronder de voor waarde regex van het URL-pad wordt bereikt.

- **Overeenkomsten**: vereist dat de aanvraag een URL-pad bevat dat overeenkomt met de opgegeven reguliere expressie.
- **Komt niet overeen**: vereist dat de aanvraag een URL-pad bevat dat niet overeenkomt met de opgegeven reguliere expressie.

Belang rijke informatie:

- Een Edge CNAME-URL wordt herschreven naar een CDN-URL voorafgaand aan de vergelijking van de URL.

    Bijvoorbeeld: beide Url's verwijzen naar hetzelfde activum en hebben daarom hetzelfde URL-pad.

     - CDN-URL: http: \/ /WPC.0001. &lt; domein &gt; -800001/CustomerOrigin/Path/Asset.htm

     - Edge CNAME-URL: http: \/ /My.domain.com/Path/Asset.htm

    Extra informatie:
    
     - URL-pad:/800001/CustomerOrigin/path/asset.htm

- Query reeksen in de URL worden genegeerd.
    
- Gebruik de optie **Case negeren** om te bepalen of er een hoofdletter gevoelige vergelijking moet worden uitgevoerd.
    
- Spaties in het URL-pad moeten worden vervangen door %20.

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>Joker teken voor URL-pad

Vergelijkt het relatieve URL-pad van een aanvraag naar het opgegeven Joker teken patroon.

De optie **treffers** / **komt niet overeen met** de voor waarden waaronder de voor waarde voor het Joker teken van het URL-pad wordt bereikt.

- **Overeenkomsten**: vereist dat de aanvraag een URL-pad bevat dat overeenkomt met het opgegeven Joker teken patroon.
- **Komt niet overeen**: vereist dat de aanvraag een URL-pad bevat dat niet overeenkomt met het opgegeven Joker teken patroon.

Belang rijke informatie:

- **Ten opzichte** van optie: met deze optie bepaalt u of het URL-vergelijkings punt begint vóór of na het toegangs punt van de inhoud.

   Deze optie kan de volgende waarden hebben:
     - **Root**: geeft aan dat het URL-vergelijkings punt direct na de CDN-hostnaam begint.

       Bijvoorbeeld: http: \/ /WPC.0001. &lt; domein &gt; / **800001/myorigin/MyFolder/index.htm**

     - **Oorsprong**: geeft aan dat het URL-vergelijkings punt begint na het inhouds toegangs punt (bijvoorbeeld/000001 of/800001/myorigin). Omdat \* AZUREEDGE.net CNAME wordt gemaakt ten opzichte van de bronmap op de hostnaam van het Verizon CDN standaard, moeten Azure CDN gebruikers de waarde **Origin** gebruiken. 

       Bijvoorbeeld: https: \/ / &lt; eind punt &gt; . azureedge.net/**MyFolder/index.htm**

     Deze URL verwijst naar de volgende Verizon CDN-hostnaam: http: \/ /WPC.0001. &lt; domein &gt; /800001/myorigin/**MyFolder/index.htm**

- Een Edge CNAME-URL wordt herschreven naar een CDN-URL voorafgaand aan de vergelijking van de URL.

    Zo verwijzen beide van de volgende Url's naar hetzelfde activum en hebben ze daarom hetzelfde URL-pad:
     - CDN-URL: http://wpc.0001.&lt ;d omain &gt; /800001/CustomerOrigin/Path/Asset.htm
     - Edge CNAME-URL: http: \/ / &lt; endpoint &gt; . azureedge.net/Path/Asset.htm
    
    Extra informatie:
    
     - URL-pad (ten opzichte van hoofdmap):/800001/CustomerOrigin/path/asset.htm
    
     - URL-pad (ten opzichte van oorsprong):/path/asset.htm
    
- Geef meerdere URL-paden op door één spatie te scheiden.

   Bijvoorbeeld:/Marketing/Asset. */Sales/*. htm

- Query reeksen in de URL worden genegeerd.
    
- Gebruik de optie **Case negeren** om te bepalen of er een hoofdletter gevoelige vergelijking moet worden uitgevoerd.
    
- Vervang spaties in het URL-pad door %20.
    
- De waarde die is opgegeven voor een URL-pad kan gebruikmaken van [Joker teken waarden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Elk patroon van een URL-pad kan een of meer sterretjes (*) bevatten, waarbij elk sterretje kan overeenkomen met een reeks van een of meer tekens.

#### <a name="sample-scenarios"></a>Voorbeeld Scenario's

De voorbeeld configuraties in de volgende tabel gaan ervan uit dat aan deze match-voor waarde wordt voldaan wanneer een aanvraag overeenkomt met het opgegeven URL-patroon:

Waarde                   | Ten opzichte van    | Resultaat 
------------------------|----------------|-------
*/test.html */test.php  | Hoofdmap of oorsprong | Dit patroon wordt afgestemd op aanvragen voor activa met de naam ' test. html ' of ' test. php ' in een wille keurige map.
/80ABCD/origin/text/*   | Hoofdmap           | Dit patroon wordt vergeleken wanneer het aangevraagde activum voldoet aan de volgende criteria: <br />-Het moet zich bevinden op een oorsprong van de klant met de naam ' Origin '. <br />-Het relatieve pad moet beginnen met een map met de naam "text". Dat wil zeggen dat het aangevraagde activum zich bevindt in de map Text of in een recursieve submappen.
*/CSS/* */js/*          | Hoofdmap of oorsprong | Dit patroon wordt afgestemd op alle CDN-of Edge CNAME-Url's die een CSS-of js-map bevatten.
*. jpg *. gif *. png       | Hoofdmap of oorsprong | Dit patroon wordt afgestemd op alle CDN-en Edge CNAME-Url's die eindigen op. jpg,. GIF of. png. Een alternatieve manier om dit patroon op te geven is met de [voor waarde matching van URL-pad](#url-path-extension).
/images/* /media/*      | Oorsprong         | Dit patroon wordt afgestemd op CDN-of Edge CNAME-Url's waarvan het relatieve pad begint met een map ' Images ' of ' media '. <br />-CDN-URL: http: \/ /WPC.0001. &lt; domein &gt; /800001/myorigin/images/Sales/event1.png<br />-Voor beeld van Edge CNAME URL: http: \/ /CDN.mydomain.com/images/Sales/event1.png

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>URL-query-letterlijke waarde

Vergelijkt de query reeks van een aanvraag naar de opgegeven waarde.

De optie **treffers** / **komt niet overeen met** de voor waarden waaronder wordt voldaan aan de voor waarde URL-query letterlijke matching.

- **Overeenkomsten**: vereist dat de aanvraag een URL-query teken reeks bevat die overeenkomt met de opgegeven query reeks.
- **Komt niet overeen**: vereist dat de aanvraag een URL-query teken reeks bevat die niet overeenkomt met de opgegeven query reeks.

Belang rijke informatie:

- Alleen exacte query reeks overeenkomsten voldoen aan deze match-voor waarde.
    
- Gebruik de optie **Case negeren** om de hoofdletter gevoeligheid van de vergelijking van query reeksen te beheren.
    
- Neem geen voorloop aanhalings tekens (?) op in de tekst van de query teken reeks waarde.
    
- Voor bepaalde tekens is URL-code ring vereist. Gebruik het percentage symbool voor URL-code ring van de volgende tekens:

   Teken | URL-code ring
   ----------|---------
   Space     | %20
   &         | %25

- Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, is deze match-voor waarde incompatibel met de volgende functies:
   - Volledige cache opvulling
   - Standaard interne Max. leeftijd
   - Interne Max. duur forceren
   - Geen cache oorsprong negeren
   - Intern Max-verouderd

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>URL-query parameter

Hiermee worden aanvragen geïdentificeerd die de opgegeven query reeks parameter bevatten. Deze para meter wordt ingesteld op een waarde die overeenkomt met een opgegeven patroon. Query reeks parameters (bijvoorbeeld para meter = waarde) in de aanvraag-URL bepalen of aan deze voor waarde wordt voldaan. Deze match-voor waarde identificeert een query reeks parameter met de naam en accepteert een of meer waarden voor de waarde van de para meter. 

De optie **treffers** / **komt niet overeen met** de voor waarden waaronder de voor waarde voor het vergelijken van de URL-query parameter wordt bereikt.

- **Overeenkomsten**: vereist dat een aanvraag de opgegeven para meter bevat met een waarde die overeenkomt met ten minste één van de waarden die zijn gedefinieerd in deze match-voor waarde.
- **Komt niet overeen**: vereist dat de aanvraag voldoet aan een van de volgende criteria:
  - Deze bevat niet de opgegeven para meter.
  - Deze bevat de opgegeven para meter, maar de bijbehorende waarde komt niet overeen met een van de waarden die zijn gedefinieerd in deze match-voor waarde.

Deze match-voor waarde biedt een eenvoudige manier om combi Naties van parameter naam en-waarde op te geven. Als u een query teken reeks parameter zoekt, kunt u overwegen om de voor waarde voor het vergelijken van [URL-query's](#url-query-wildcard) te gebruiken.

Belang rijke informatie:

- Er kan alleen een query parameter naam met één URL worden opgegeven per exemplaar van deze match-voor waarde.
    
- Omdat Joker teken waarden niet worden ondersteund als een parameter naam is opgegeven, komen alleen exacte parameter namen overeen voor vergelijking.
- Parameter waarde (n) kunnen [Joker teken waarden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)bevatten.
   - Elk patroon van de parameter waarde kan bestaan uit een of meer sterretjes (*), waarbij elk sterretje kan overeenkomen met een reeks van een of meer tekens.
   - Voor bepaalde tekens is URL-code ring vereist. Gebruik het percentage symbool voor URL-code ring van de volgende tekens:

       Teken | URL-code ring
       ----------|---------
       Space     | %20
       &         | %25

- Geef meerdere parameter waarden voor de query teken reeks op door deze te scheiden met één spatie. Er wordt voldaan aan deze match-voor waarde wanneer een aanvraag een van de opgegeven combi Naties van naam/waarde bevat.

   - Voorbeeld 1:

     - Configuratie:

       Waardea ValueB

     - Deze configuratie komt overeen met de volgende query reeks parameters:

       Parameter1 = Waardea
    
       Parameter1 = ValueB

   - Voorbeeld 2:

     - Configuratie: 

        Waarde% 20A waarde% 20B

     - Deze configuratie komt overeen met de volgende query reeks parameters:

       Parameter1 = waarde% 20A

       Parameter1 = waarde% 20B

- Aan deze match-voor waarde wordt alleen voldaan als er een exacte overeenkomst is met ten minste één van de opgegeven combi Naties van naam/waarde van de query reeks.

   Als u bijvoorbeeld de configuratie in het vorige voor beeld gebruikt, wordt de combi natie van parameter naam/waarde "parameter1 = ValueAdd" niet beschouwd als een overeenkomst. Als u echter een van de volgende waarden opgeeft, komt deze overeen met die combi natie van naam/waarde:

   - Waardea ValueB ValueAdd
   - Waardea * ValueB

- Gebruik de optie **Case negeren** om de hoofdletter gevoeligheid van de vergelijking van query reeksen te beheren.
    
- Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, is deze match-voor waarde incompatibel met de volgende functies:
   - Volledige cache opvulling
   - Standaard interne Max. leeftijd
   - Interne Max. duur forceren
   - Geen cache oorsprong negeren
   - Intern Max-verouderd

#### <a name="sample-scenarios"></a>Voorbeeldscenario's

In het volgende voor beeld ziet u hoe deze optie in bepaalde situaties werkt:

Name  | Waarde |  Resultaat
------|-------|--------
Gebruiker  | Joe   | Dit patroon wordt vergeleken wanneer de query reeks voor een aangevraagde URL '? User = Joe ' is.
Gebruiker  | *     | Dit patroon wordt vergeleken wanneer de query reeks voor een aangevraagde URL een gebruikers parameter bevat.
Email | Joe\* | Dit patroon wordt vergeleken wanneer de query reeks voor een aangevraagde URL een e-mail parameter bevat die begint met ' Joe '.

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>URL-query-regex

Hiermee worden aanvragen geïdentificeerd die de opgegeven query reeks parameter bevatten. Deze para meter wordt ingesteld op een waarde die overeenkomt met een opgegeven [reguliere expressie](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

De optie **treffers** / **komt niet overeen met** de voor waarden waaronder de voor waarde URL-query regex matching is voldaan.

- **Overeenkomsten**: vereist dat de aanvraag een URL-query teken reeks bevat die overeenkomt met de opgegeven reguliere expressie.
- **Komt niet overeen**: vereist dat de aanvraag een URL-query teken reeks bevat die niet overeenkomt met de opgegeven reguliere expressie.

Belang rijke informatie:

- Alleen exacte overeenkomsten met de opgegeven reguliere expressie voldoen aan deze match-voor waarde.
    
- Gebruik de optie **Case negeren** om de hoofdletter gevoeligheid van de vergelijking van query reeksen te beheren.
    
- Voor de doel einden van deze optie begint een query teken reeks met het eerste teken na de scheidings markering (?) voor de query teken reeks.
    
- Voor bepaalde tekens is URL-code ring vereist. Gebruik het percentage symbool voor URL-code ring van de volgende tekens:

   Teken | URL-code ring | Waarde
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%1,25

   Houd er rekening mee dat percentage symbolen moeten worden voorafgegaan.

- Dubbele Escape speciale reguliere expressie tekens (bijvoorbeeld \^ $. +) om een back slash in de reguliere expressie op te neemt.

   Bijvoorbeeld:

   Waarde | Geïnterpreteerd als 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, is deze match-voor waarde incompatibel met de volgende functies:
   - Volledige cache opvulling
   - Standaard interne Max. leeftijd
   - Interne Max. duur forceren
   - Geen cache oorsprong negeren
   - Intern Max-verouderd

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>URL-query Joker teken

Vergelijkt de opgegeven waarde (n) met de query teken reeks van de aanvraag.

De optie **treffers** / **komt niet overeen met** de voor waarden waaronder de voor waarde voor de URL-query Joker teken overeenkomst wordt bereikt.

- **Overeenkomsten**: vereist dat de aanvraag een URL-query teken reeks bevat die overeenkomt met de opgegeven Joker teken waarde.
- **Komt niet overeen**: vereist dat de aanvraag een URL-query teken reeks bevat die niet overeenkomt met de opgegeven Joker teken waarde.

Belang rijke informatie:

- Voor de doel einden van deze optie begint een query teken reeks met het eerste teken na de scheidings markering (?) voor de query teken reeks.
- Parameter waarden kunnen [Joker teken waarden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)bevatten:
   - Elk patroon van de parameter waarde kan bestaan uit een of meer sterretjes (*), waarbij elk sterretje kan overeenkomen met een reeks van een of meer tekens.
   - Voor bepaalde tekens is URL-code ring vereist. Gebruik het percentage symbool voor URL-code ring van de volgende tekens:

     Teken | URL-code ring
     ----------|---------
     Space     | %20
     &         | %25

- Geef meerdere waarden op door één spatie te scheiden.

   Bijvoorbeeld: *parameter1 = waardea* *ValueB* *parameter1 = ValueC&Parameter2 = waarde*

- Alleen exacte overeenkomsten voor ten minste één van de opgegeven query reeks patronen voldoen aan deze match-voor waarde.
    
- Gebruik de optie **Case negeren** om de hoofdletter gevoeligheid van de vergelijking van query reeksen te beheren.
    
- Als gevolg van de manier waarop de cache-instellingen worden bijgehouden, is deze match-voor waarde incompatibel met de volgende functies:
   - Volledige cache opvulling
   - Standaard interne Max. leeftijd
   - Interne Max. duur forceren
   - Geen cache oorsprong negeren
   - Intern Max-verouderd

#### <a name="sample-scenarios"></a>Voorbeeldscenario's

In het volgende voor beeld ziet u hoe deze optie in bepaalde situaties werkt:

 Naam                 | Beschrijving
 ---------------------|------------
gebruiker = Joe              | Dit patroon wordt vergeleken wanneer de query reeks voor een aangevraagde URL '? User = Joe ' is.
\*gebruiker = \* \* OptOut =\* | Dit patroon wordt vergeleken wanneer de URL-query van de CDN de para meter user of OptOut bevat.

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Content Delivery Network](cdn-overview.md)
- [Referentie voor regels-engine](cdn-verizon-premium-rules-engine-reference.md)
- [Voorwaardelijke expressies in de regelengine](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Functies van de regelengine](cdn-verizon-premium-rules-engine-reference-features.md)
- [Standaard HTTP-gedrag negeren met de regel engine](cdn-verizon-premium-rules-engine.md)
