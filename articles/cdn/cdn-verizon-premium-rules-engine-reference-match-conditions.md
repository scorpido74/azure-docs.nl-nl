---
title: Azure CDN van Verizon Premium regels engine match voorwaarden | Microsoft Documenten
description: Referentiedocumentatie voor Azure Content Delivery Network van de Verizon Premium-regels voldoen aan de voorwaarden.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 1660dca34b2f128ef5889145fcdeed0d2523b9bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593202"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN van Verizon Premium regels engine overeenkomen met voorwaarden

In dit artikel worden gedetailleerde beschrijvingen weergegeven van de beschikbare overeenkomstvoorwaarden voor het Azure Content Delivery Network (CDN) van de Verizon [Premium-regelsengine](cdn-verizon-premium-rules-engine.md).

Het tweede deel van een regel is de wedstrijdvoorwaarde. Een overeenkomstvoorwaarde identificeert specifieke soorten aanvragen waarvoor een reeks functies wordt uitgevoerd.

U bijvoorbeeld een wedstrijdvoorwaarde gebruiken om:

- Filter aanvragen voor inhoud op een bepaalde locatie.
- Filteraanvragen die zijn gegenereerd vanuit een bepaald IP-adres of land/regio.
- Filter aanvragen op koptekstgegevens.

## <a name="always-match-condition"></a>Altijd overeenkomen met de conditie

De voorwaarde Altijd overeenkomen past een standaardset functies toe op alle aanvragen.

Name | Doel
-----|--------
[Altijd](#always) | Hiermee past u een standaardset functies toe op alle aanvragen.

## <a name="device-match-condition"></a>Apparaatmatchconditie

De apparaatovereenkomst identificeert aanvragen vanaf een mobiel apparaat op basis van de eigenschappen ervan.  

Name | Doel
-----|--------
[Apparaat](#device) | Identificeert aanvragen vanaf een mobiel apparaat op basis van de eigenschappen ervan.

## <a name="location-match-conditions"></a>Locatie overeenkomen met voorwaarden

De voorwaarden voor locatie komen overeen met de voorwaarden voor aanvragen op basis van de locatie van de aanvrager.

Name | Doel
-----|--------
[AS-nummer](#as-number) | Identificeert aanvragen die afkomstig zijn van een bepaald netwerk.
[Land](#country) | Identificeert aanvragen die afkomstig zijn uit de opgegeven landen/regio's.

## <a name="origin-match-conditions"></a>Origin match voorwaarden

De Origin-overeenkomstvoorwaarden identificeren aanvragen die wijzen op de opslag van het Content Delivery Network of een server voor het oorsprong van de klant.

Name | Doel
-----|--------
[CDN-oorsprong](#cdn-origin) | Identificeert aanvragen voor inhoud die is opgeslagen in de opslag van het Content Delivery Network.
[Oorsprong van de klant](#customer-origin) | Identificeert aanvragen voor inhoud die is opgeslagen op een specifieke server van de oorsprong van de klant.

## <a name="request-match-conditions"></a>Wedstrijdvoorwaarden aanvragen

De voorwaarden voor aanvraag overeenkomen met aanvragen identificeren aanvragen op basis van hun eigenschappen.

Name | Doel
-----|--------
[Client-IP-adres](#client-ip-address) | Identificeert aanvragen die afkomstig zijn van een bepaald IP-adres.
[Cookieparameter](#cookie-parameter) | Hiermee controleert u de cookies die aan elk verzoek zijn gekoppeld op de opgegeven waarde.
[Cookieparameter Regex](#cookie-parameter-regex) | Hiermee controleert u de cookies die aan elk verzoek zijn gekoppeld voor de opgegeven reguliere expressie.
[RandCnaam](#edge-cname) | Hiermee identificeert u aanvragen die wijzen op een specifieke rand-CNAME.
[Domein verwijzen](#referring-domain) | Hiermee worden aanvragen geïdentificeerd die zijn doorverwezen vanuit de opgegeven hostnamen.
[Koptekst aanvragen Letterlijk](#request-header-literal) | Hiermee worden aanvragen geïdentificeerd die de opgegeven koptekstset bevatten op een opgegeven waarde.
[Regex voor koptekst aanvragen](#request-header-regex) | Hiermee worden aanvragen geïdentificeerd die de opgegeven koptekstset bevatten op een waarde die overeenkomt met de opgegeven reguliere expressie.
[Jokerteken aanvragen](#request-header-wildcard) | Hiermee worden aanvragen geïdentificeerd die de opgegeven koptekstset bevatten tot een waarde die overeenkomt met het opgegeven patroon.
[Aanvraagmethode](#request-method) | Identificeert aanvragen op hun HTTP-methode.
[Aanvraagregeling](#request-scheme) | Identificeert aanvragen op hun HTTP-protocol.

## <a name="url-match-conditions"></a>URL-overeenkomstvoorwaarden

De URL komt overeen met de voorwaarden voor het identificeren van aanvragen op basis van hun URL's.

Name | Doel
-----|--------
[URL-padmap](#url-path-directory) | Identificeert aanvragen op hun relatieve pad.
[URL-padextensie](#url-path-extension) | Hiermee worden aanvragen geïdentificeerd aan de andere extensie met bestandsnaam.
[URL-padbestandsnaam](#url-path-filename) | Identificeert aanvragen op hun bestandsnaam.
[URL-pad letterlijk](#url-path-literal) | Hiermee vergelijkt u het relatieve pad van een aanvraag met de opgegeven waarde.
[URL-pad Regex](#url-path-regex) | Hiermee vergelijkt u het relatieve pad van een aanvraag met de opgegeven reguliere expressie.
[Wildcard voor URL-pad](#url-path-wildcard) | Hiermee vergelijkt u het relatieve pad van een aanvraag met het opgegeven patroon.
[URL-query letterlijk](#url-query-literal) | Hiermee vergelijkt u de querytekenreeks van een aanvraag met de opgegeven waarde.
[PARAMETER URL-query](#url-query-parameter) | Hiermee worden aanvragen geïdentificeerd die de opgegeven parameter querytekenreeks bevatten die is ingesteld op een waarde die overeenkomt met een opgegeven patroon.
[URL-query Regex](#url-query-regex) | Hiermee worden aanvragen geïdentificeerd die de opgegeven parameter querytekenreeks bevatten die is ingesteld op een waarde die overeenkomt met een opgegeven reguliere expressie.
[URL-queryjokerteken](#url-query-wildcard) | Hiermee vergelijkt u de opgegeven waarde met de querytekenreeks van de aanvraag.

## <a name="reference-for-rules-engine-match-conditions"></a>Verwijzing naar regels motor overeenkomen met voorwaarden

---

### <a name="always"></a>Altijd

De voorwaarde Altijd overeenkomen past een standaardset functies toe op alle aanvragen.

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>AS-nummer

Het AS Number-netwerk wordt gedefinieerd door het autonome systeemnummer (ASN). 

De optie **Wedstrijden**/**komt niet overeen,** bepaalt de voorwaarden waaronder aan de voorwaarde AS-getalmatch is voldaan:

- **Overeenkomsten:** vereist dat de ASN van het clientnetwerk overeenkomt met een van de opgegeven ASN's. 
- **Komt niet overeen:** vereist dat de ASN van het clientnetwerk niet overeenkomt met een van de opgegeven ASN's.

Belangrijkste informatie:

- Geef meerdere ASN's op door elk met één spatie te definiëren. 64514 64515 komt bijvoorbeeld overeen met aanvragen die afkomstig zijn van 64514 of 64515.
- Bepaalde verzoeken kunnen een geldige ASN mogelijk niet retourneren. Een vraagteken (?) komt overeen met aanvragen waarvoor een geldige ASN niet kan worden bepaald.
- Geef de volledige ASN op voor het gewenste netwerk. Gedeeltelijke waarden worden niet geëvenaard.
- Vanwege de manier waarop cache-instellingen worden bijgehouden, is deze wedstrijdvoorwaarde niet compatibel met de volgende functies:
  - Volledige cachevulling
  - Standaard interne max-leeftijd
  - Force Internal Max-Age
  - Origin No-Cache negeren
  - Intern Max-Stale

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>CDN-oorsprong

Aan de voorwaarde van de CDN Origin-match is voldaan wanneer aan beide volgende voorwaarden is voldaan:

- Inhoud van CDN-opslag werd gevraagd.
- De aanvraag URI gebruikt het type inhoudstoegangspunt (bijvoorbeeld /000001) dat is gedefinieerd in deze overeenkomende voorwaarde:
  - CDN-URL: De aanvraag URI moet het geselecteerde inhoudstoegangspunt bevatten.
  - Url van Edge CNAME: de bijbehorende CNAME-configuratie met rand moet naar het geselecteerde toegangspunt voor inhoud wijzen.
  
Belangrijkste informatie:

- Het toegangspunt voor inhoud identificeert de service die de gevraagde inhoud moet weergeven.
- Gebruik geen EN ALS-instructie om bepaalde wedstrijdvoorwaarden te combineren. Als u bijvoorbeeld een CDN Origin-overeenkomstvoorwaarde combineert met een voorwaarde voor een klantoorsprongovereenkomst, wordt een overeenkomstpatroon gemaakt dat nooit kan worden geëvenaard. Om deze reden kunnen twee CDN Origin-wedstrijdvoorwaarden niet worden gecombineerd via een EN ALS-instructie.

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Client-IP-adres

De optie **Overeenkomsten**/**komt niet overeen** met de voorwaarden waaronder aan de voorwaarde van het IP-adres van de client is voldaan:

- **Overeenkomsten:** vereist dat het IP-adres van de client overeenkomt met een van de opgegeven IP-adressen. 
- **Komt niet overeen:** vereist dat het IP-adres van de client niet overeenkomt met een van de opgegeven IP-adressen. 

Belangrijkste informatie:

- Gebruik CIDR-notatie.
- Geef meerdere IP-adressen en/of IP-adresblokken op door elk van deze te beperken met één spatie. Bijvoorbeeld:
  - **IPv4-voorbeeld**: 1.2.3.4 10.20.30.40 komt overeen met aanvragen die binnenkomen vanaf adres 1.2.3.4 of 10.20.30.40.
  - **Voorbeeld van IPv6**: 1:2:3:5:5:7:8 10:20:30:50:60:70:80 komt overeen met aanvragen die binnenkomen vanaf elk adres 1:2:3:5:6:7:8: 8 of 10:20:40:50:60:70:80:80.
- De syntaxis voor een IP-adresblok is het basis-IP-adres, gevolgd door een slash door doorsturen en de grootte van het voorvoegsel. Bijvoorbeeld:
  - **Voorbeeld van IPv4**: 5.5.5.64/26 komt overeen met verzoeken die binnenkomen van adressen 5.5.5.64 tot en met 5.5.5.127.
  - **IPv6-voorbeeld**: 1:2:3:/48 komt overeen met aanvragen die binnenkomen van adressen 1:2:3:0:0:0:0 tot 1:2:3:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff.
- Vanwege de manier waarop cache-instellingen worden bijgehouden, is deze wedstrijdvoorwaarde niet compatibel met de volgende functies:
  - Volledige cachevulling
  - Standaard interne max-leeftijd
  - Force Internal Max-Age
  - Origin No-Cache negeren
  - Intern Max-Stale

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Cookieparameter

De optie **Wedstrijden**/**komt niet overeen,** bepaalt onder welke voorwaarden aan de voorwaarde cookieparameter match is voldaan.

- **Overeenkomsten:** vereist een verzoek om de opgegeven cookie te bevatten met een waarde die overeenkomt met ten minste één van de waarden die in deze overeenkomstvoorwaarde zijn gedefinieerd.
- **Komt niet overeen:** vereist dat de aanvraag aan een van de volgende criteria voldoet:
  - Het bevat niet de opgegeven cookie.
  - Het bevat de opgegeven cookie, maar de waarde komt niet overeen met een van de waarden die zijn gedefinieerd in deze overeenkomende voorwaarde.
  
Belangrijkste informatie:

- Cookienaam:
  - Omdat wildcardwaarden, inclusief sterretjes (*), niet worden ondersteund wanneer u een cookienaam opgeeft, komen alleen exacte cookienamen in aanmerking voor vergelijking.
  - Per instantie van deze wedstrijdvoorwaarde kan slechts één cookienaam worden opgegeven.
  - Cookienaamvergelijkingen zijn case-ongevoelig.
- Cookiewaarde:
  - Geef meerdere cookiewaarden op door elk van deze waarden te beperken met één spatie.
  - Een cookiewaarde kan profiteren van [wildcardwaarden.](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)
  - Als een wildcardwaarde niet is opgegeven, voldoet alleen een exacte overeenkomst aan deze wedstrijdvoorwaarde. Als u bijvoorbeeld 'Waarde' opgeeft, komt deze overeen met 'Waarde', maar niet 'Waarde1' of 'Waarde2'.
  - Gebruik de optie **Kwestie negeren** om te bepalen of er een hoofdlettergevoelige vergelijking wordt gemaakt met de cookiewaarde van het verzoek.
- Vanwege de manier waarop cache-instellingen worden bijgehouden, is deze wedstrijdvoorwaarde niet compatibel met de volgende functies:
  - Volledige cachevulling
  - Standaard interne max-leeftijd
  - Force Internal Max-Age
  - Origin No-Cache negeren
  - Intern Max-Stale

[Terug naar boven](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Cookieparameter Regex

De voorwaarde Voor Parameter Regex-overeenkomst voor cookies definieert een cookienaam en -waarde. U [reguliere expressies](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) gebruiken om de gewenste cookiewaarde te definiëren.

De optie **Wedstrijden**/**komt niet overeen,** bepaalt onder welke voorwaarden aan de voorwaarde van de Cookieparameter Regex-overeenkomst is voldaan.

- **Overeenkomsten:** vereist een verzoek om de opgegeven cookie te bevatten met een waarde die overeenkomt met de opgegeven reguliere expressie.
- **Komt niet overeen:** vereist dat de aanvraag aan een van de volgende criteria voldoet:
  - Het bevat niet de opgegeven cookie.
  - Het bevat de opgegeven cookie, maar de waarde komt niet overeen met de opgegeven reguliere expressie.
  
Belangrijkste informatie:

- Cookienaam:
  - Omdat reguliere expressies en wildcardwaarden, waaronder sterretjes (*), niet worden ondersteund wanneer u een cookienaam opgeeft, komen alleen exacte cookienaamovereenkomsten in aanmerking voor vergelijking.
  - Per instantie van deze wedstrijdvoorwaarde kan slechts één cookienaam worden opgegeven.
  - Cookienaamvergelijkingen zijn case-ongevoelig.
- Cookiewaarde:
  - Een cookiewaarde kan profiteren van reguliere expressies.
  - Gebruik de optie **Kwestie negeren** om te bepalen of er een hoofdlettergevoelige vergelijking wordt gemaakt met de cookiewaarde van het verzoek.
- Vanwege de manier waarop cache-instellingen worden bijgehouden, is deze wedstrijdvoorwaarde niet compatibel met de volgende functies:
  - Volledige cachevulling
  - Standaard interne max-leeftijd
  - Force Internal Max-Age
  - Origin No-Cache negeren
  - Intern Max-Stale

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Land

U een land opgeven via de landcode. 

De optie **Wedstrijden**/**komt niet overeen,** bepaalt onder welke voorwaarden aan de voorwaarde landenovereenkomst is voldaan:

- **Overeenkomsten:** vereist dat het verzoek de opgegeven landcodewaarden bevat. 
- **Komt niet overeen:** vereist dat de aanvraag niet de opgegeven landcodewaarden bevat.

Belangrijkste informatie:

- Geef meerdere landcodes op door elk land te definiëren met één spatie.
- Jokertekens worden niet ondersteund wanneer u een landcode opgeeft.
- De landencodes "EU" en "AP" omvatten niet alle IP-adressen in die regio's.
- Bepaalde verzoeken geven mogelijk geen geldige landcode terug. Een vraagteken (?) komt overeen met aanvragen waarvoor geen geldige landcode kan worden vastgesteld.
- Landcodes zijn hoofdlettergevoelig.
- Vanwege de manier waarop cache-instellingen worden bijgehouden, is deze wedstrijdvoorwaarde niet compatibel met de volgende functies:
  - Volledige cachevulling
  - Standaard interne max-leeftijd
  - Force Internal Max-Age
  - Origin No-Cache negeren
  - Intern Max-Stale

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Landfiltering implementeren met behulp van de rules engine

Met deze wedstrijdvoorwaarde u een veelheid aan aanpassingen uitvoeren op basis van de locatie van waaruit een aanvraag afkomstig is. Het gedrag van de functie Landfiltering kan bijvoorbeeld worden gerepliceerd via de volgende configuratie:

- URL Path Wildcard match: Stel de [URL Path Wildcard match voorwaarde](#url-path-wildcard) in op de map die zal worden beveiligd. 
    Een sterretje toevoegen aan het einde van het relatieve pad om ervoor te zorgen dat de toegang tot al zijn kinderen door deze regel wordt beperkt.

- Landenmatch: Stel de voorwaarde voor de landenovereenkomst in op de gewenste set landen.
  - Toestaan: stel de voorwaarde landenovereenkomst in **op Niet overeenkomen** om alleen de opgegeven landen toegang te geven tot inhoud die is opgeslagen op de locatie die is gedefinieerd door de voorwaarde URL-padwildcard.
  - Blokkeren: stel de voorwaarde voor de landenovereenkomst in op **Overeenkomsten** om te voorkomen dat de opgegeven landen toegang krijgen tot inhoud die is opgeslagen op de locatie die is gedefinieerd door de voorwaarde URL-padwildcard.

- Functie Toegang weigeren (403): schakel de [functie Toegang weigeren (403)](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) in om het gedeelte van de functie Landfiltering te repliceren of te blokkeren.

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Oorsprong van de klant

Belangrijkste informatie:

- Aan de voorwaarde klantoorsprongovereenkomst wordt voldaan, ongeacht of inhoud wordt aangevraagd via een CDN-URL of een edge CNAME-URL die verwijst naar de geselecteerde klantoorsprong.
- Een configuratie van de oorsprong van de klant waarnaar wordt verwezen door een regel, kan niet worden verwijderd van de pagina Klantoorsprong. Voordat u een configuratie van het klantoorsprong probeert te verwijderen, moet u ervoor zorgen dat er niet naar de volgende configuraties wordt verwezen:
  - Een klantorigin-overeenkomst
  - Een edge CNAME-configuratie
- Gebruik geen EN ALS-instructie om bepaalde wedstrijdvoorwaarden te combineren. Als u bijvoorbeeld een klantorigin-overeenkomstcombineer met een CDN Origin-overeenkomstvoorwaarde combineert, wordt een overeenkomstpatroon gemaakt dat nooit kan worden geëvenaard. Om deze reden kunnen twee klantorigin-overeenkomstvoorwaarden niet worden gecombineerd via een EN ALS-instructie.

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Apparaat

De apparaatovereenkomst identificeert aanvragen vanaf een mobiel apparaat op basis van de eigenschappen ervan. Detectie van mobiele apparaten wordt bereikt via [WURFL.](http://wurfl.sourceforge.net/) 

De optie **Wedstrijden**/**komt niet overeen,** bepaalt onder welke voorwaarden aan de voorwaarde apparaatmatch is voldaan:

- **Overeenkomsten:** het apparaat van de aanvrager moet overeenkomen met de opgegeven waarde. 
- **Komt niet overeen:** vereist dat het apparaat van de aanvrager niet overeenkomt met de opgegeven waarde.

Belangrijkste informatie:

- Gebruik de optie **Kwestie negeren** om op te geven of de opgegeven waarde hoofdlettergevoelig is.
- Vanwege de manier waarop cache-instellingen worden bijgehouden, is deze wedstrijdvoorwaarde niet compatibel met de volgende functies:
  - Volledige cachevulling
  - Standaard interne max-leeftijd
  - Force Internal Max-Age
  - Origin No-Cache negeren
  - Intern Max-Stale

#### <a name="string-type"></a>Tekenreekstype

Een WURFL-mogelijkheid accepteert doorgaans elke combinatie van cijfers, letters en symbolen. Vanwege het flexibele karakter van deze mogelijkheid moet u kiezen hoe de waarde die aan deze wedstrijdvoorwaarde is gekoppeld, wordt geïnterpreteerd. In de volgende tabel worden de beschikbare opties beschreven:

Type     | Beschrijving
---------|------------
Letterlijke  | Selecteer deze optie om te voorkomen dat de meeste tekens een speciale betekenis krijgen door hun [letterlijke waarde te](cdn-verizon-premium-rules-engine-reference.md#literal-values)gebruiken.
Wildcard | Selecteer deze optie om te profiteren van alle [wildcardtekens]([wildcardwaarden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Selecteer deze optie om [reguliere expressies](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)te gebruiken . Reguliere expressies zijn handig voor het definiëren van een patroon van tekens.

#### <a name="wurfl-capabilities"></a>WURFL-mogelijkheden

Een WURFL-mogelijkheid verwijst naar een categorie die mobiele apparaten beschrijft. De geselecteerde mogelijkheid bepaalt het type beschrijving van mobiele apparaten dat wordt gebruikt om aanvragen te identificeren.

De volgende tabel bevat WURFL-mogelijkheden en hun variabelen voor de rules engine.

> [!NOTE]
> De volgende variabelen worden ondersteund in de **header Clientaanvraag wijzigen** en **Clientresponseheader wijzigen.**

Mogelijkheid | Variabele | Beschrijving | Voorbeeldwaarden
-----------|----------|-------------|----------------
Merknaam | %{wurfl_cap_brand_name} | Een tekenreeks die de merknaam van het apparaat aangeeft. | Samsung
Apparaatbesturingssysteem | %{wurfl_cap_device_os} | Een tekenreeks die het besturingssysteem aangeeft dat op het apparaat is geïnstalleerd. | iOS
Versie besturingssysteem apparaat | %{wurfl_cap_device_os_version} | Een tekenreeks die het versienummer van het besturingssysteem aangeeft dat op het apparaat is geïnstalleerd. | 1.0.1
Dubbele oriëntatie | %{wurfl_cap_dual_orientation} | Een Booleaan die aangeeft of het apparaat een dubbele oriëntatie ondersteunt. | waar
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | Een tekenreeks die de gewenste documenttypedefinitie (DTD) van het mobiele apparaat aangeeft voor HTML-inhoud. | geen<br/>xhtml_basic<br/>html5
Afbeeldingslijnen | %{wurfl_cap_image_inlining} | Een Booleaan die aangeeft of het apparaat Base64 gecodeerde afbeeldingen ondersteunt. | false
Is Android | %{wurfl_vcap_is_android} | Een Booleaan die aangeeft of het apparaat het Android-besturingssysteem gebruikt. | waar
Is IOS | %{wurfl_vcap_is_ios} | Een Booleaan die aangeeft of het apparaat iOS gebruikt. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | Een Booleaan die aangeeft of het apparaat een smart-tv is. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | Een Booleaan die aangeeft of het apparaat een smartphone is. | waar
Is Tablet | %{wurfl_cap_is_tablet} | Een Booleaan die aangeeft of het apparaat een tablet is. Deze beschrijving is OS-onafhankelijk. | waar
Is draadloos apparaat | %{wurfl_cap_is_wireless_device} | Een Booleaan die aangeeft of het apparaat wordt beschouwd als een draadloos apparaat. | waar
Marketingnaam | %{wurfl_cap_marketing_name} | Een tekenreeks die de marketingnaam van het apparaat aangeeft. | BlackBerry 8100 Parel
Mobiele browser | %{wurfl_cap_mobile_browser} | Een tekenreeks die aangeeft welke browser wordt gebruikt om inhoud van het apparaat op te vragen. | Chrome
Versie van mobiele browser | %{wurfl_cap_mobile_browser_version} | Een tekenreeks die de versie van de browser aangeeft die wordt gebruikt om inhoud van het apparaat op te vragen. | 31
Modelnaam | %{wurfl_cap_model_name} | Een tekenreeks die de modelnaam van het apparaat aangeeft. | s3
Progressief downloaden | %{wurfl_cap_progressive_download} | Een Booleaan die aangeeft of het apparaat het afspelen van audio en video ondersteunt terwijl het nog wordt gedownload. | waar
Releasedatum | %{wurfl_cap_release_date} | Een tekenreeks die aangeeft op welk jaar en maand het apparaat is toegevoegd aan de WURFL-database.<br/><br/>Formaat:`yyyy_mm` | 2013_december
Resolutiehoogte | %{wurfl_cap_resolution_height} | Een geheel getal dat de hoogte van het apparaat in pixels aangeeft. | 768
Resolutiebreedte | %{wurfl_cap_resolution_width} | Een geheel getal dat de breedte van het apparaat in pixels aangeeft. | 1024

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>RandCnaam

Belangrijkste informatie:

- De lijst met beschikbare edge CNAMEs is beperkt tot de edge CNAMEs die zijn geconfigureerd op de Edge CNAMEs-pagina voor het platform waarop de rules engine wordt geconfigureerd.
- Voordat u een edge CNAME-configuratie probeert te verwijderen, moet u ervoor zorgen dat er geen verwijzing naar een randcname-overeenkomst wordt verwezen. Edge CNAME-configuraties die zijn gedefinieerd in een regel, kunnen niet worden verwijderd van de pagina Edge CNAMEs.
- Gebruik geen EN ALS-instructie om bepaalde wedstrijdvoorwaarden te combineren. Als u bijvoorbeeld een randcnaam-overeenkomstvoorwaarde combineert met een voorwaarde voor een klantoorsprongovereenkomst, wordt een overeenkomstpatroon gemaakt dat nooit kan worden geëvenaard. Om deze reden kunnen twee RandCname-overeenkomstvoorwaarden niet worden gecombineerd via een EN ALS-instructie.
- Vanwege de manier waarop cache-instellingen worden bijgehouden, is deze wedstrijdvoorwaarde niet compatibel met de volgende functies:
  - Volledige cachevulling
  - Standaard interne max-leeftijd
  - Force Internal Max-Age
  - Origin No-Cache negeren
  - Intern Max-Stale

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Domein verwijzen

De hostnaam die is gekoppeld aan de verwijzer waarmee inhoud is aangevraagd, bepaalt of aan de voorwaarde Verwijzend domein is voldaan.

De optie **Overeenkomsten**/**komt niet overeen,** bepaalt de voorwaarden waaronder aan de voorwaarde Referring Domain match is voldaan:

- **Overeenkomsten:** de verwijzende hostnaam moet overeenkomen met de opgegeven waarden. 
- **Komt niet overeen:** vereist dat de verwijzende hostnaam niet overeenkomt met de opgegeven waarde.

Belangrijkste informatie:

- Geef meerdere hostnamen op door elk nummer één te definiëren met één spatie.
- Deze overeenkomstvoorwaarde ondersteunt [wildcardwaarden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- Als de opgegeven waarde geen sterretje bevat, moet deze exact overeenkomen met de hostnaam van de verwijzer. Als u bijvoorbeeld 'mydomain.com' opgeeft, komt dit niet overeen met 'www.mydomain.com'.
- Gebruik de optie **Kwestie negeren** om te bepalen of er een hoofdlettergevoelige vergelijking wordt gemaakt.
- Vanwege de manier waarop cache-instellingen worden bijgehouden, is deze wedstrijdvoorwaarde niet compatibel met de volgende functies:
  - Volledige cachevulling
  - Standaard interne max-leeftijd
  - Force Internal Max-Age
  - Origin No-Cache negeren
  - Intern Max-Stale

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Koptekst aanvragen Letterlijk

De optie **Wedstrijden**/**komt niet overeen,** bepaalt de voorwaarden waaronder aan de letterlijke wedstrijdvoorwaarde voor de koptekst aanvragen is voldaan.

- **Overeenkomsten:** vereist dat het verzoek de opgegeven koptekst bevat. De waarde moet overeenkomen met de waarde die is gedefinieerd in deze wedstrijdvoorwaarde.
- **Komt niet overeen:** vereist dat de aanvraag aan een van de volgende criteria voldoet:
  - Het bevat niet de opgegeven koptekst.
  - Het bevat de opgegeven koptekst, maar de waarde komt niet overeen met de kop die is gedefinieerd in deze overeenkomende voorwaarde.
  
Belangrijkste informatie:

- Header naam vergelijkingen zijn altijd case-ongevoelig. Gebruik de optie **Kwestie negeren** om de hoofdlettergevoeligheid van vergelijkingen met koptekstwaarde te regelen.
- Vanwege de manier waarop cache-instellingen worden bijgehouden, is deze wedstrijdvoorwaarde niet compatibel met de volgende functies:
  - Volledige cachevulling
  - Standaard interne max-leeftijd
  - Force Internal Max-Age
  - Origin No-Cache negeren
  - Intern Max-Stale

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Regex voor koptekst aanvragen

De optie **Wedstrijden**/**komt niet overeen,** bepaalt de voorwaarden waaronder aan de regex-wedstrijdvoorwaarde voor de kopselectie aanvragen is voldaan.

- **Overeenkomsten:** vereist dat het verzoek de opgegeven koptekst bevat. De waarde moet overeenkomen met het patroon dat is gedefinieerd in de opgegeven [reguliere expressie](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Komt niet overeen:** vereist dat de aanvraag aan een van de volgende criteria voldoet:
  - Het bevat niet de opgegeven koptekst.
  - Het bevat de opgegeven koptekst, maar de waarde komt niet overeen met de opgegeven reguliere expressie.

Belangrijkste informatie:

- Koptekstnaam:
  - Header naam vergelijkingen zijn case-ongevoelig.
  - Vervang spaties in de koptekstnaam door '%20'.
- Koptekstwaarde:
  - Een kopwaarde kan profiteren van reguliere expressies.
  - Gebruik de optie **Kwestie negeren** om de hoofdlettergevoeligheid van vergelijkingen met koptekstwaarde te regelen.
  - Aan de wedstrijdvoorwaarde wordt alleen voldaan wanneer een kopwaarde exact overeenkomt met ten minste één van de opgegeven patronen.
- Vanwege de manier waarop cache-instellingen worden bijgehouden, is deze wedstrijdvoorwaarde niet compatibel met de volgende functies:
  - Volledige cachevulling
  - Standaard interne max-leeftijd
  - Force Internal Max-Age
  - Origin No-Cache negeren
  - Intern Max-Stale

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Jokerteken aanvragen

De optie **Wedstrijden**/**komt niet overeen,** bepaalt de voorwaarden waaronder aan de voorwaarde Wildcard voor jokerjokeraanvragen voor aanvragen is voldaan.

- **Overeenkomsten:** vereist dat het verzoek de opgegeven koptekst bevat. De waarde moet overeenkomen met ten minste één van de waarden die in deze overeenkomende voorwaarde zijn gedefinieerd.
- **Komt niet overeen:** vereist dat de aanvraag aan een van de volgende criteria voldoet:
  - Het bevat niet de opgegeven koptekst.
  - Het bevat de opgegeven koptekst, maar de waarde komt niet overeen met een van de opgegeven waarden.
  
Belangrijkste informatie:

- Koptekstnaam:
  - Header naam vergelijkingen zijn case-ongevoelig.
  - Spaties in de koptekstnaam moeten worden vervangen door '%20'. U deze waarde ook gebruiken om spaties in een kopwaarde op te geven.
- Koptekstwaarde:
  - Een kopwaarde kan profiteren van [wildcardwaarden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Gebruik de optie **Kwestie negeren** om de hoofdlettergevoeligheid van vergelijkingen met koptekstwaarde te regelen.
  - Aan deze wedstrijdvoorwaarde wordt voldaan wanneer een kopwaarde exact overeenkomt met ten minste één van de opgegeven patronen.
  - Geef meerdere waarden op door elk waardete te beperken met één spatie.
- Vanwege de manier waarop cache-instellingen worden bijgehouden, is deze wedstrijdvoorwaarde niet compatibel met de volgende functies:
  - Volledige cachevulling
  - Standaard interne max-leeftijd
  - Force Internal Max-Age
  - Origin No-Cache negeren
  - Intern Max-Stale

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Aanvraagmethode

De voorwaarde voor de overeenkomen met de aanvraagmethode wordt alleen voldaan wanneer assets worden aangevraagd via de geselecteerde aanvraagmethode. De beschikbare aanvraagmethoden zijn:

- GET
- HEAD
- POST
- Opties
- PUT
- DELETE
- Trace
- Verbinding

Belangrijkste informatie:

- Standaard kan alleen de get-aanvraagmethode inhoud in de cache op het netwerk genereren. Alle andere aanvraagmethoden worden via het netwerk geproxieerd.
- Vanwege de manier waarop cache-instellingen worden bijgehouden, is deze wedstrijdvoorwaarde niet compatibel met de volgende functies:
  - Volledige cachevulling
  - Standaard interne max-leeftijd
  - Force Internal Max-Age
  - Origin No-Cache negeren
  - Intern Max-Stale

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>Aanvraagregeling

De voorwaarde voor aanvraagregeling wordt alleen voldaan wanneer activa worden aangevraagd via het geselecteerde protocol. De beschikbare protocollen zijn:

- HTTP
- HTTPS

Belangrijkste informatie:

- Vanwege de manier waarop cache-instellingen worden bijgehouden, is deze wedstrijdvoorwaarde niet compatibel met de volgende functies:
  - Volledige cachevulling
  - Standaard interne max-leeftijd
  - Force Internal Max-Age
  - Origin No-Cache negeren
  - Intern Max-Stale

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>URL-padmap

Hiermee wordt een verzoek geïdentificeerd aan de basis van het relatieve pad, dat de bestandsnaam van het gevraagde actief uitsluit.

De optie **Overeenkomsten**/**komt niet overeen,** bepaalt de voorwaarden waaronder aan de voorwaarde URL Path Directory-overeenkomst is voldaan.

- **Overeenkomsten:** vereist dat het verzoek een relatief URL-pad bevat, met uitzondering van de bestandsnaam, dat overeenkomt met het opgegeven URL-patroon.
- **Komt niet overeen:** vereist dat het verzoek een relatief URL-pad bevat, met uitzondering van bestandsnaam, dat niet overeenkomt met het opgegeven URL-patroon.

Belangrijkste informatie:

- Gebruik de optie **Relatief om** op te geven of de URL-vergelijking begint voor of na het toegangspunt voor inhoud. Het toegangspunt voor inhoud is het gedeelte van het pad dat wordt weergegeven tussen de verizon-cdn-hostnaam en het relatieve pad naar het gevraagde element (bijvoorbeeld /800001/CustomerOrigin). Het identificeert een locatie op servertype (bijvoorbeeld CDN of klantoorsprong) en uw klantrekeningnummer.

   De volgende waarden zijn beschikbaar voor de optie **Relatief:**
  - **Hoofd:** geeft aan dat het URL-vergelijkingspunt direct na de CDN-hostnaam begint. 

  Bijvoorbeeld: http:\//wpc.0001. &lt;&gt;domein/**800001/myorigin/myfolder**/index.htm

  - **Oorsprong**: Geeft aan dat het URL-vergelijkingspunt begint na het toegangspunt voor inhoud (bijvoorbeeld /000001 of /800001/myorigin). Omdat \*de CNAME .azureedge.net gemaakt ten opzichte van de oorsprongsmap op de Verizon CDN-hostnaam standaard, moeten Azure CDN-gebruikers de **Origin-waarde** gebruiken. 

  Bijvoorbeeld: https:\//&lt;eindpunt&gt;.azureedge.net/**myfolder**/index.htm 

  Deze URL verwijst naar de volgende Verizon\/CDN hostname: http: /wpc.0001. &lt;domein&gt;/800001/myorigin/**myfolder**/index.htm

- Een URL van de rand-CNAME wordt opnieuw geschreven naar een CDN-URL voordat de URL-vergelijking wordt weergegeven.

    Beide url's wijzen bijvoorbeeld naar hetzelfde element en hebben daarom hetzelfde URL-pad.
  - CDN URL:\/http: /wpc.0001. &lt;domein&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Url van Edge CNAME:\//&lt;http: eindpunt&gt;.azureedge.net/path/asset.htm
    
    Extra informatie:
  - Aangepast domein:\/https: /my.domain.com/path/asset.htm
    
    - URL-pad (ten opzichte van root): /800001/CustomerOrigin/path/
    
    - URL-pad (ten opzichte van herkomst): /pad/

- Het gedeelte van de URL dat wordt gebruikt voor de URL-vergelijking eindigt net voor de bestandsnaam van het gevraagde item. Een trailing forward slash is het laatste teken in dit type pad.

- Vervang alle spaties in het URL-padpatroon door '%20'.

- Elk URL-padpatroon kan een of meer sterretjes (*) bevatten, waarbij elk sterretje overeenkomt met een reeks van een of meer tekens.

- Geef meerdere URL-paden op in het patroon door elk pad met één spatie af te definiëren.

    Bijvoorbeeld: */sales/ */marketing/

- Een URL-padspecificatie kan profiteren van [wildcardwaarden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Gebruik de optie **Kwestie negeren** om te bepalen of een hoofdlettergevoelige vergelijking wordt uitgevoerd.

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>URL-padextensie

Hiermee worden aanvragen geïdentificeerd aan de andere plaats in de bestandsextensie van het gevraagde actief.

De optie **Overeenkomsten**/**komt niet overeen,** bepaalt de voorwaarden waaronder aan de voorwaarde URL-padextensie is voldaan.

- **Overeenkomsten:** vereist dat de URL van het verzoek een bestandsextensie bevat die precies overeenkomt met het opgegeven patroon.

   Als u bijvoorbeeld 'htm'- elementen opgeeft, worden 'htm'-elementen gekoppeld, maar geen 'html'-elementen.  

- **Komt niet overeen:** vereist dat het URL-verzoek een bestandsextensie bevat die niet overeenkomt met het opgegeven patroon.

Belangrijkste informatie:

- Geef de bestandsextensies op die overeenkomen in het vak **Waarde.** Neem geen leidende periode op; gebruik bijvoorbeeld htm in plaats van .htm.

- Gebruik de optie **Kwestie negeren** om te bepalen of een hoofdlettergevoelige vergelijking wordt uitgevoerd.

- Geef meerdere bestandsextensies op door elke extensie te beperken met één spatie. 

    Bijvoorbeeld: htm html

- Als u bijvoorbeeld 'htm' opgeeft, komt het overeen met "htm"-assets, maar niet met "html"-elementen.

#### <a name="sample-scenario"></a>Voorbeeldscenario

In de volgende voorbeeldconfiguratie wordt ervan uitgegaan dat aan deze overeenkomstvoorwaarde is voldaan wanneer een aanvraag overeenkomt met een van de opgegeven extensies.

Waardespecificatie: asp aspx php html

Aan deze overeenkomstvoorwaarde wordt voldaan wanneer url's worden gevonden die eindigen met de volgende extensies:

- .asp
- .aspx
- .php
- .html

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>URL-padbestandsnaam

Hiermee worden aanvragen geïdentificeerd aan de met de bestandsnaam van het gevraagde actief. Voor de toepassing van deze overeenkomstvoorwaarde bestaat een bestandsnaam uit de naam van het gevraagde actief, een periode en de bestandsextensie (bijvoorbeeld index.html).

De optie **Overeenkomsten**/**komt niet overeen,** bepaalt de voorwaarden waaronder aan de voorwaarde URL Path Filename is voldaan.

- **Overeenkomsten:** vereist dat het verzoek een bestandsnaam in het URL-pad bevat die overeenkomt met het opgegeven patroon.
- **Komt niet overeen:** het verzoek moet een bestandsnaam bevatten in het URL-pad dat niet overeenkomt met het opgegeven patroon.

Belangrijkste informatie:

- Gebruik de optie **Kwestie negeren** om te bepalen of een hoofdlettergevoelige vergelijking wordt uitgevoerd.

- Als u meerdere bestandsextensies wilt opgeven, scheidt u elke extensie met één spatie.

    Bijvoorbeeld: index.htm index.html

- Spaties in een bestandsnaamwaarde vervangen door '%20'.

- Een bestandsnaamwaarde kan profiteren van [wildcardwaarden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Elk bestandsnaampatroon kan bijvoorbeeld bestaan uit een of meer sterretjes (*), waarbij elk sterretje overeenkomt met een reeks van een of meer tekens.

- Als wildcardtekens niet zijn opgegeven, voldoet alleen een exacte overeenkomst aan deze wedstrijdvoorwaarde.

    Als u bijvoorbeeld 'presentation.ppt' opgeeft, komt het overeen met een asset met de naam 'presentation.ppt', maar niet één met de naam 'presentation.pptx'.

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>URL-pad letterlijk

Hiermee vergelijkt het URL-pad van een aanvraag, inclusief bestandsnaam, met de opgegeven waarde.

De optie **Overeenkomsten**/**komt niet overeen,** bepaalt de voorwaarden waaronder aan de voorwaarde URL Pad Letterlijke overeenkomst is voldaan.

- **Overeenkomsten:** vereist dat het verzoek een URL-pad bevat dat overeenkomt met het opgegeven patroon.
- **Komt niet overeen:** het verzoek moet een URL-pad bevatten dat niet overeenkomt met het opgegeven patroon.

Belangrijkste informatie:

- Gebruik de optie **Relatief om** op te geven of het URL-vergelijkingspunt begint voor of na het toegangspunt voor inhoud. 

    De volgende waarden zijn beschikbaar voor de optie **Relatief:**
  - **Hoofd:** geeft aan dat het URL-vergelijkingspunt direct na de CDN-hostnaam begint.

    Bijvoorbeeld: http:\//wpc.0001. &lt;&gt;/**domein800001/myorigin/myfolder/index.htm**

  - **Oorsprong**: Geeft aan dat het URL-vergelijkingspunt begint na het toegangspunt voor inhoud (bijvoorbeeld /000001 of /800001/myorigin). Omdat \*de CNAME .azureedge.net gemaakt ten opzichte van de oorsprongsmap op de Verizon CDN-hostnaam standaard, moeten Azure CDN-gebruikers de **Origin-waarde** gebruiken. 

    Bijvoorbeeld: https:\//&lt;eindpunt&gt;.azureedge.net/**myfolder/index.htm**

  Deze URL verwijst naar de volgende Verizon\/CDN hostname: http: /wpc.0001. &lt;domein&gt;/800001/myorigin/**myfolder/index.htm**

- Een URL van de rand-CNAME wordt opnieuw geschreven naar een CDN-URL voordat een URL-vergelijking wordt gemaakt.

Beide URL's wijzen bijvoorbeeld naar hetzelfde element en hebben daarom hetzelfde URL-pad:

- CDN URL:\/http: /wpc.0001. &lt;domein&gt;/800001/CustomerOrigin/path/asset.htm
- Url van Edge CNAME:\//&lt;http: eindpunt&gt;.azureedge.net/path/asset.htm

    Extra informatie:
    
    - URLpad (ten opzichte van root): /800001/CustomerOrigin/path/asset.htm
   
    - URL-pad (ten opzichte van herkomst): /path/asset.htm

- Querytekenreeksen in de URL worden genegeerd.
- Gebruik de optie **Kwestie negeren** om te bepalen of een hoofdlettergevoelige vergelijking wordt uitgevoerd.
- De waarde die voor deze overeenkomende voorwaarde is opgegeven, wordt vergeleken met het relatieve pad van de exacte aanvraag van de client.

- Als u alle aanvragen aan een bepaalde map wilt koppelen, gebruikt u de [URL Path Directory](#url-path-directory) of de URL Path Wildcard-overeenkomstvoorwaarde. [URL Path Wildcard](#url-path-wildcard)

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>URL-pad Regex

Hiermee vergelijkt u het URL-pad van een aanvraag met de opgegeven [reguliere expressie](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

De optie **Wedstrijden**/**komt niet overeen,** bepaalt onder welke voorwaarden aan de voorwaarde URL Path Regex-overeenkomst is voldaan.

- **Overeenkomsten:** vereist dat het verzoek een URL-pad bevat dat overeenkomt met de opgegeven reguliere expressie.
- **Komt niet overeen:** het verzoek moet een URL-pad bevatten dat niet overeenkomt met de opgegeven reguliere expressie.

Belangrijkste informatie:

- Een URL van de rand-CNAME wordt opnieuw geschreven naar een CDN-URL voordat de URL wordt vergeleken.

    Beide URL's wijzen bijvoorbeeld op hetzelfde element en hebben daarom hetzelfde URL-pad.

     - CDN URL:\/http: /wpc.0001. &lt;domein&gt;/800001/CustomerOrigin/path/asset.htm

     - Url van Edge CNAME: http:\//my.domain.com/path/asset.htm

    Extra informatie:
    
     - URL-pad: /800001/CustomerOrigin/path/asset.htm

- Querytekenreeksen in de URL worden genegeerd.
    
- Gebruik de optie **Kwestie negeren** om te bepalen of een hoofdlettergevoelige vergelijking wordt uitgevoerd.
    
- Spaties in het URL-pad moeten worden vervangen door '%20'.

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>Wildcard voor URL-pad

Hiermee vergelijkt het relatieve URL-pad van een aanvraag met het opgegeven jokerpatroon.

De optie **Wedstrijden**/**komt niet overeen,** bepaalt de voorwaarden waaronder aan de voorwaarde URL Path Wildcard-overeenkomst is voldaan.

- **Overeenkomsten:** vereist dat het verzoek een URL-pad bevat dat overeenkomt met het opgegeven wildcardpatroon.
- **Komt niet overeen:** het verzoek moet een URL-pad bevatten dat niet overeenkomt met het opgegeven wildcardpatroon.

Belangrijkste informatie:

- **Ten opzichte van** de optie: deze optie bepaalt of het URL-vergelijkingspunt begint voor of na het toegangspunt voor inhoud.

   Met deze optie kunnen de volgende waarden worden waarden:
     - **Hoofd:** geeft aan dat het URL-vergelijkingspunt direct na de CDN-hostnaam begint.

       Bijvoorbeeld: http:\//wpc.0001. &lt;&gt;/**domein800001/myorigin/myfolder/index.htm**

     - **Oorsprong**: Geeft aan dat het URL-vergelijkingspunt begint na het toegangspunt voor inhoud (bijvoorbeeld /000001 of /800001/myorigin). Omdat \*de CNAME .azureedge.net gemaakt ten opzichte van de oorsprongsmap op de Verizon CDN-hostnaam standaard, moeten Azure CDN-gebruikers de **Origin-waarde** gebruiken. 

       Bijvoorbeeld: https:\//&lt;eindpunt&gt;.azureedge.net/**myfolder/index.htm**

     Deze URL verwijst naar de volgende Verizon\/CDN hostname: http: /wpc.0001. &lt;domein&gt;/800001/myorigin/**myfolder/index.htm**

- Een URL van de rand-CNAME wordt opnieuw geschreven naar een CDN-URL voordat de URL wordt vergeleken.

    Beide URL's wijzen bijvoorbeeld naar hetzelfde element en hebben daarom hetzelfde URL-pad:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Url van Edge CNAME:\//&lt;http: eindpunt&gt;.azureedge.net/path/asset.htm
    
    Extra informatie:
    
     - URLpad (ten opzichte van root): /800001/CustomerOrigin/path/asset.htm
    
     - URL-pad (ten opzichte van herkomst): /path/asset.htm
    
- Geef meerdere URL-paden op door elk pad te beperken met één spatie.

   Bijvoorbeeld: /marketing/asset.* /sales/*.htm

- Querytekenreeksen in de URL worden genegeerd.
    
- Gebruik de optie **Kwestie negeren** om te bepalen of een hoofdlettergevoelige vergelijking wordt uitgevoerd.
    
- Spaties in het URL-pad vervangen door '%20'.
    
- De waarde die is opgegeven voor een URL-pad kan profiteren van [wildcardwaarden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Elk URL-padpatroon kan een of meer sterretjes (*) bevatten, waarbij elk sterretje kan overeenkomen met een reeks van een of meer tekens.

#### <a name="sample-scenarios"></a>Voorbeeldscenario's

De voorbeeldconfiguraties in de volgende tabel gaan ervan uit dat aan deze overeenkomstvoorwaarde is voldaan wanneer een aanvraag overeenkomt met het opgegeven URL-patroon:

Waarde                   | Ten opzichte van    | Resultaat 
------------------------|----------------|-------
*/test.html */test.php  | Wortel of oorsprong | Dit patroon wordt geëvenaard door aanvragen voor elementen met de naam "test.html" of "test.php" in een map.
/80ABCD/origin/text/*   | Hoofdmap           | Dit patroon wordt geëvenaard wanneer het gevraagde actief aan de volgende criteria voldoet: <br />- Het moet zich bevinden op een oorsprong van de klant genaamd "oorsprong.". <br />- Het relatieve pad moet beginnen met een map met de naam 'tekst'. Dat wil zeggen dat het gevraagde item zich kan bevinden in de map 'tekst' of in een van de recursieve submappen.
*/css/* */js/*          | Wortel of oorsprong | Dit patroon wordt geëvenaard door alle CDN- of EDGE CNAME-URL's die een css- of js-map bevatten.
*.jpg *.gif *.png       | Wortel of oorsprong | Dit patroon wordt geëvenaard door alle CDN- of EDGE CNAME-URL's die eindigen met .jpg, .gif of .png. Een alternatieve manier om dit patroon op te geven is met de [url-padextensie-overeenkomstvoorwaarde](#url-path-extension).
/afbeeldingen/* /media/*      | Oorsprong         | Dit patroon wordt geëvenaard door CDN- of edge CNAME-URL's waarvan het relatieve pad begint met een map 'afbeeldingen' of 'media'. <br />- CDN URL:\/http: /wpc.0001. &lt;domein&gt;/800001/myorigin/images/sales/event1.png<br />- Voorbeeldrand CNAME-URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>URL-query letterlijk

Hiermee vergelijkt u de querytekenreeks van een aanvraag met de opgegeven waarde.

De optie **Overeenkomsten**/**komt niet overeen,** bepaalt de voorwaarden waaronder aan de voorwaarde LETTERLIJKe URL-queryovereenkomst is voldaan.

- **Overeenkomsten:** vereist dat het verzoek een URL-querytekenreeks bevat die overeenkomt met de opgegeven querytekenreeks.
- **Komt niet overeen:** het verzoek moet een URL-querytekenreeks bevatten die niet overeenkomt met de opgegeven querytekenreeks.

Belangrijkste informatie:

- Alleen exacte querytekenreeksovereenkomsten voldoen aan deze wedstrijdvoorwaarde.
    
- Gebruik de optie **Kwestie negeren** om de hoofdlettergevoeligheid van querytekenreeksvergelijkingen te beheren.
    
- Neem geen voorloopvraagteken (?) op in de waardetekst van de querytekenreeks.
    
- Voor bepaalde tekens is URL-codering vereist. Gebruik het percentagesymbool om de volgende tekens te coderen:

   Teken | URL-codering
   ----------|---------
   Space     | %20
   &         | %25

- Vanwege de manier waarop cache-instellingen worden bijgehouden, is deze wedstrijdvoorwaarde niet compatibel met de volgende functies:
   - Volledige cachevulling
   - Standaard interne max-leeftijd
   - Force Internal Max-Age
   - Origin No-Cache negeren
   - Intern Max-Stale

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>PARAMETER URL-query

Hiermee worden aanvragen geïdentificeerd die de opgegeven parameter querytekenreeks bevatten. Deze parameter is ingesteld op een waarde die overeenkomt met een opgegeven patroon. Querytekenreeksparameters (bijvoorbeeld parameter=waarde) in de aanvraag-URL bepalen of aan deze voorwaarde is voldaan. Met deze overeenkomstvoorwaarde wordt een parameter voor querytekenreeksen aan de naam geïdentificeerd en worden een of meer waarden voor de parameterwaarde geaccepteerd. 

De optie **Overeenkomsten**/**komt niet overeen,** bepaalt de voorwaarden waaronder aan de voorwaarde URL-queryparameter is voldaan.

- **Overeenkomsten:** vereist een aanvraag om de opgegeven parameter te bevatten met een waarde die overeenkomt met ten minste één van de waarden die in deze overeenkomstvoorwaarde zijn gedefinieerd.
- **Komt niet overeen:** vereist dat de aanvraag aan een van de volgende criteria voldoet:
  - Het bevat niet de opgegeven parameter.
  - Het bevat de opgegeven parameter, maar de waarde komt niet overeen met een van de waarden die zijn gedefinieerd in deze overeenkomstvoorwaarde.

Deze wedstrijdvoorwaarde biedt een eenvoudige manier om parameternaam/waardecombinaties op te geven. Voor meer flexibiliteit als u een querytekenreeksparameter matcht, u overwegen de voorwaarde [URL-querywildcard-overeenkomst](#url-query-wildcard) te gebruiken.

Belangrijkste informatie:

- Per instantie van deze overeenkomstvoorwaarde kan slechts één URL-queryparameter worden opgegeven.
    
- Omdat wildcardwaarden niet worden ondersteund wanneer een parameternaam is opgegeven, komen alleen exacte parameternaamovereenkomsten in aanmerking voor vergelijking.
- Parameterwaarde(en) kan [wildcardwaarden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)bevatten.
   - Elk parameterwaardepatroon kan bestaan uit een of meer sterretjes (*), waarbij elk sterretje kan overeenkomen met een reeks van een of meer tekens.
   - Voor bepaalde tekens is URL-codering vereist. Gebruik het percentagesymbool om de volgende tekens te coderen:

       Teken | URL-codering
       ----------|---------
       Space     | %20
       &         | %25

- Geef meerdere parameterwaarden voor querytekenreeksen op door elk van deze waarden met één spatie te beperken. Aan deze overeenkomstvoorwaarde wordt voldaan wanneer een aanvraag een van de opgegeven naam/waardecombinaties bevat.

   - Voorbeeld 1:

     - Configuratie:

       ValueA ValueB

     - Deze configuratie komt overeen met de volgende querytekenreeksparameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Voorbeeld 2:

     - Configuratie: 

        Waarde%20A-waarde%20B

     - Deze configuratie komt overeen met de volgende querytekenreeksparameters:

       Parameter1=Waarde%20A

       Parameter1=Waarde%20B

- Aan deze overeenkomstvoorwaarde wordt alleen voldaan wanneer er een exacte overeenkomst is met ten minste één van de opgegeven querytekenreeksnaam/waardecombinaties.

   Als u bijvoorbeeld de configuratie in het vorige voorbeeld gebruikt, wordt de parameternaam/waardecombinatie 'Parameter1=ValueAdd' niet als een overeenkomst beschouwd. Als u echter een van de volgende waarden opgeeft, komt deze overeen met die combinatie naam/waarde:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Gebruik de optie **Kwestie negeren** om de hoofdlettergevoeligheid van querytekenreeksvergelijkingen te beheren.
    
- Vanwege de manier waarop cache-instellingen worden bijgehouden, is deze wedstrijdvoorwaarde niet compatibel met de volgende functies:
   - Volledige cachevulling
   - Standaard interne max-leeftijd
   - Force Internal Max-Age
   - Origin No-Cache negeren
   - Intern Max-Stale

#### <a name="sample-scenarios"></a>Voorbeeldscenario's

In het volgende voorbeeld wordt uitgelegd hoe deze optie werkt in specifieke situaties:

Name  | Waarde |  Resultaat
------|-------|--------
Gebruiker  | Joe   | Dit patroon wordt geëvenaard wanneer de querytekenreeks voor een gevraagde URL '?user=joe' is.
Gebruiker  | *     | Dit patroon komt overeen wanneer de querytekenreeks voor een aangevraagde URL een parameter Gebruiker bevat.
Email | Joe\* | Dit patroon komt overeen wanneer de querytekenreeks voor een aangevraagde URL een parameter e-mail bevat die begint met 'Joe'.

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>URL-query Regex

Hiermee worden aanvragen geïdentificeerd die de opgegeven parameter querytekenreeks bevatten. Deze parameter is ingesteld op een waarde die overeenkomt met een opgegeven [reguliere expressie](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

De optie **Overeenkomsten**/**komt niet overeen,** bepaalt de voorwaarden waaronder aan de voorwaarde URL Query Regex-overeenkomst is voldaan.

- **Overeenkomsten:** vereist dat het verzoek een URL-querytekenreeks bevat die overeenkomt met de opgegeven reguliere expressie.
- **Komt niet overeen:** het verzoek moet een URL-querytekenreeks bevatten die niet overeenkomt met de opgegeven reguliere expressie.

Belangrijkste informatie:

- Alleen exacte overeenkomsten met de opgegeven reguliere expressie voldoen aan deze wedstrijdvoorwaarde.
    
- Gebruik de optie **Kwestie negeren** om de hoofdlettergevoeligheid van querytekenreeksvergelijkingen te beheren.
    
- Voor de toepassing van deze optie begint een querytekenreeks met het eerste teken na de vraagtekenscheiding (?) voor de querytekenreeks.
    
- Voor bepaalde tekens is URL-codering vereist. Gebruik het percentagesymbool om de volgende tekens te coderen:

   Teken | URL-codering | Waarde
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   Houd er rekening mee dat percentage symbolen moeten worden ontsnapt.

- Dubbele ontsnapping speciale reguliere expressie tekens \^(bijvoorbeeld $.+) om een backslash op te nemen in de reguliere expressie.

   Bijvoorbeeld:

   Waarde | Geïnterpreteerd als 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Vanwege de manier waarop cache-instellingen worden bijgehouden, is deze wedstrijdvoorwaarde niet compatibel met de volgende functies:
   - Volledige cachevulling
   - Standaard interne max-leeftijd
   - Force Internal Max-Age
   - Origin No-Cache negeren
   - Intern Max-Stale

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>URL-queryjokerteken

Hiermee vergelijkt u de opgegeven waarde(en) met de querytekenreeks van de aanvraag.

De optie **Overeenkomsten**/**komt niet overeen,** bepaalt de voorwaarden waaronder aan de voorwaarde URL-querywildcard is voldaan.

- **Overeenkomsten:** vereist dat het verzoek een URL-querytekenreeks bevat die overeenkomt met de opgegeven wildcardwaarde.
- **Komt niet overeen:** het verzoek moet een URL-querytekenreeks bevatten die niet overeenkomt met de opgegeven wildcardwaarde.

Belangrijkste informatie:

- Voor de toepassing van deze optie begint een querytekenreeks met het eerste teken na de vraagtekenscheiding (?) voor de querytekenreeks.
- Parameterwaarden kunnen [wildcardwaarden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)bevatten:
   - Elk parameterwaardepatroon kan bestaan uit een of meer sterretjes (*), waarbij elk sterretje kan overeenkomen met een reeks van een of meer tekens.
   - Voor bepaalde tekens is URL-codering vereist. Gebruik het percentagesymbool om de volgende tekens te coderen:

     Teken | URL-codering
     ----------|---------
     Space     | %20
     &         | %25

- Geef meerdere waarden op door elk waardete te beperken met één spatie.

   Bijvoorbeeld: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=Valued*

- Alleen exacte overeenkomsten met ten minste één van de opgegeven querytekenreekspatronen voldoen aan deze overeenkomstvoorwaarde.
    
- Gebruik de optie **Kwestie negeren** om de hoofdlettergevoeligheid van querytekenreeksvergelijkingen te beheren.
    
- Vanwege de manier waarop cache-instellingen worden bijgehouden, is deze wedstrijdvoorwaarde niet compatibel met de volgende functies:
   - Volledige cachevulling
   - Standaard interne max-leeftijd
   - Force Internal Max-Age
   - Origin No-Cache negeren
   - Intern Max-Stale

#### <a name="sample-scenarios"></a>Voorbeeldscenario's

In het volgende voorbeeld wordt uitgelegd hoe deze optie werkt in specifieke situaties:

 Name                 | Beschrijving
 ---------------------|------------
user=joe              | Dit patroon wordt geëvenaard wanneer de querytekenreeks voor een gevraagde URL '?user=joe' is.
\*gebruiker=\* \*optout=\* | Dit patroon komt overeen wanneer de CDN URL-query de parameter gebruiker of optout bevat.

[Terug naar boven](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Content Delivery Network](cdn-overview.md)
- [Verwijzingen naar de motor](cdn-verizon-premium-rules-engine-reference.md)
- [Regels engine voorwaardelijke expressies](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regels motorfuncties](cdn-verizon-premium-rules-engine-reference-features.md)
- [StandaardHTTP-gedrag overschrijven met de rules-engine](cdn-verizon-premium-rules-engine.md)
