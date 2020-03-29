---
title: Overeenkomen met de voorwaarden in de standaardregelsengine voor Azure CDN | Microsoft Documenten
description: Referentiedocumentatie voor wedstrijdvoorwaarden in de standaardregelsengine voor Azure Content Delivery Network (Azure CDN).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 425266e2a7ca42bb17ca598ddfc2f2b86591f32e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900178"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Overeenkomen met de voorwaarden in de standaardregelsengine voor Azure CDN

In de [standaardregelsengine](cdn-standard-rules-engine.md) voor Azure Content Delivery Network (Azure CDN) bestaat een regel uit een of meer overeenkomstvoorwaarden en een actie. In dit artikel vindt u gedetailleerde beschrijvingen van de wedstrijdvoorwaarden die u gebruiken in de standaardregelsengine voor Azure CDN.

Het eerste deel van een regel is een wedstrijdvoorwaarde of een reeks wedstrijdvoorwaarden. In de standaardregelsengine voor Azure CDN kan elke regel maximaal vier overeenkomende voorwaarden hebben. Een overeenkomstvoorwaarde identificeert specifieke typen aanvragen waarvoor gedefinieerde acties worden uitgevoerd. Als u meerdere wedstrijdvoorwaarden gebruikt, worden de wedstrijdvoorwaarden gegroepeerd met behulp van EN logica.

U bijvoorbeeld een wedstrijdvoorwaarde gebruiken om:

- Filter aanvragen op basis van een specifiek IP-adres, land of regio.
- Filter aanvragen op koptekstgegevens.
- Filter aanvragen vanaf mobiele apparaten of desktopapparaten.

## <a name="match-conditions"></a>Overeenkomen met de voorwaarden

De volgende wedstrijdvoorwaarden zijn beschikbaar voor gebruik in de standaardregelsengine voor Azure CDN. 

### <a name="device-type"></a>Apparaattype 

Identificeert aanvragen vanaf een mobiel apparaat of desktopapparaat.  

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Gelijk is, niet gelijk aan | Mobiel, Desktop

### <a name="http-version"></a>HTTP-versie

Identificeert aanvragen op basis van de HTTP-versie van de aanvraag.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Gelijk is, niet gelijk aan | 2.0, 1.1, 1.0, 0.9, Alle

### <a name="request-cookies"></a>Cookies aanvragen

Identificeert aanvragen op basis van cookie-informatie in de binnenkomende aanvraag.

#### <a name="required-fields"></a>Vereiste velden

Cookienaam | Operator | Cookiewaarde | Case transformatie
------------|----------|--------------|---------------
Tekenreeks | [Lijst met standaardoperatoren](#standard-operator-list) | Tekenreeks, Int | Geen transformatie, naar hoofdletters, naar kleine letters

#### <a name="key-information"></a>Belangrijkste informatie

- U geen wildcardwaarden (inclusief sterretjes (\*)) gebruiken wanneer u een cookienaam opgeeft; u moet een exacte cookienaam gebruiken.
- U slechts één cookienaam per instantie van deze overeenkomstvoorwaarde opgeven.
- Cookienaamvergelijkingen zijn case-ongevoelig.
- Als u meerdere cookiewaarden wilt opgeven, gebruikt u één spatie tussen elke cookiewaarde. 
- Cookiewaarden kunnen profiteren van wildcardwaarden.
- Als een wildcardwaarde niet is opgegeven, voldoet alleen een exacte overeenkomst aan deze wedstrijdvoorwaarde. 'Waarde' komt bijvoorbeeld overeen met 'Waarde' maar niet 'Waarde1'. 

### <a name="post-argument"></a>Argument Bericht

Hiermee identificeert u aanvragen op basis van argumenten die zijn gedefinieerd voor de post-aanvraagmethode die in de aanvraag wordt gebruikt. 

#### <a name="required-fields"></a>Vereiste velden

Argumentnaam | Operator | Argumentwaarde | Case transformatie
--------------|----------|----------------|---------------
Tekenreeks | [Lijst met standaardoperatoren](#standard-operator-list) | Tekenreeks, Int | Geen transformatie, naar hoofdletters, naar kleine letters

### <a name="query-string"></a>Queryreeks

Hiermee worden aanvragen geïdentificeerd die een specifieke parameter voor querytekenreeksen bevatten. Deze parameter is ingesteld op een waarde die overeenkomt met een specifiek patroon. Querytekenreeksparameters (bijvoorbeeld **parameter=waarde)** in de aanvraag-URL bepalen of aan deze voorwaarde is voldaan. Met deze overeenkomstvoorwaarde wordt een parameter voor querytekenreeksen aan de naam geïdentificeerd en worden een of meer waarden voor de parameterwaarde geaccepteerd.

#### <a name="required-fields"></a>Vereiste velden

Operator | Queryreeks | Case transformatie
---------|--------------|---------------
[Lijst met standaardoperatoren](#standard-operator-list) | Tekenreeks, Int | Geen transformatie, naar hoofdletters, naar kleine letters

### <a name="remote-address"></a>Extern adres

Identificeert aanvragen op basis van de locatie of het IP-adres van de aanvrager.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|-----------------
Alle | N.v.t.
Geo Match | Landcode
IP-match | IP-adres (ruimtegescheiden)
Niet | N.v.t.
Niet Geo Match | Landcode
Geen IP-overeenkomst | IP-adres (ruimtegescheiden)

#### <a name="key-information"></a>Belangrijkste informatie

- Gebruik CIDR-notatie.
- Als u meerdere IP-adressen en IP-adresblokken wilt opgeven, gebruikt u één spatie tussen de waarden:
  - **IPv4-voorbeeld**: *1.2.3.4 10.20.30.40* komt overeen met aanvragen die binnenkomen vanaf adres 1.2.3.4 of 10.20.30.40.
  - **Voorbeeld van IPv6**: *1:2:3:5:5:7:8 10:20:30:50:60:70:80* komt overeen met aanvragen die binnenkomen vanaf elk adres 1:2:3:5:6:7:8: 8 of 10:20:40:50:60:70:80:80.
- De syntaxis voor een IP-adresblok is het basis-IP-adres, gevolgd door een slash door doorsturen en de grootte van het voorvoegsel. Bijvoorbeeld:
  - **Voorbeeld van IPv4**: *5.5.5.64/26* komt overeen met verzoeken die binnenkomen van adressen 5.5.5.64 tot en met 5.5.5.127.
  - **IPv6-voorbeeld**: *1:2:3:/48* komt overeen met aanvragen die binnenkomen van adressen 1:2:3:0:0:0:0 tot 1:2:3:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff.

### <a name="request-body"></a>Aanvraagbody

Identificeert aanvragen op basis van specifieke tekst die in de hoofdtekst van de aanvraag wordt weergegeven.

#### <a name="required-fields"></a>Vereiste velden

Operator | Aanvraagbody | Case transformatie
---------|--------------|---------------
[Lijst met standaardoperatoren](#standard-operator-list) | Tekenreeks, Int | Geen transformatie, naar hoofdletters, naar kleine letters

### <a name="request-header"></a>Aanvraagheader

Hiermee identificeert u aanvragen die een specifieke koptekst in de aanvraag gebruiken.

#### <a name="required-fields"></a>Vereiste velden

Headernaam | Operator | Headerwaarde | Case transformatie
------------|----------|--------------|---------------
Tekenreeks | [Lijst met standaardoperatoren](#standard-operator-list) | Tekenreeks, Int | Geen transformatie, naar hoofdletters, naar kleine letters

### <a name="request-method"></a>Aanvraagmethode

Hiermee identificeert u aanvragen die de opgegeven aanvraagmethode gebruiken.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Gelijk is, niet gelijk aan | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>Belangrijkste informatie

- Alleen de get-aanvraagmethode kan inhoud in de cache genereren in Azure CDN. Alle andere aanvraagmethoden worden via het netwerk geproxieerd. 

### <a name="request-protocol"></a>Aanvraagprotocol

Hiermee identificeert u aanvragen die het gebruikte opgegeven protocol gebruiken.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Gelijk is, niet gelijk aan | HTTP, HTTPS

### <a name="request-url"></a>Aanvraag-URL

Hiermee worden aanvragen geïdentificeerd die overeenkomen met de opgegeven URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Aanvraag-URL | Case transformatie
---------|-------------|---------------
[Lijst met standaardoperatoren](#standard-operator-list) | Tekenreeks, Int | Geen transformatie, naar hoofdletters, naar kleine letters

#### <a name="key-information"></a>Belangrijkste informatie

- Wanneer u deze regelvoorwaarde gebruikt, moet u protocolgegevens opnemen. Bijvoorbeeld: *https://www.\<yourdomain\>.com*.

### <a name="url-file-extension"></a>URL-bestandsextensie

Hiermee worden aanvragen geïdentificeerd die de opgegeven bestandsextensie opnemen in de bestandsnaam in de aanvragende URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Toestelnummer | Case transformatie
---------|-----------|---------------
[Lijst met standaardoperatoren](#standard-operator-list) | Tekenreeks, Int | Geen transformatie, naar hoofdletters, naar kleine letters

#### <a name="key-information"></a>Belangrijkste informatie

- Voor verlenging, neem geen toonaangevende periode; gebruik bijvoorbeeld *html* in plaats van *.html*.

### <a name="url-file-name"></a>URL-bestandsnaam

Hiermee worden aanvragen geïdentificeerd die de opgegeven bestandsnaam in de aanvragende URL bevatten.

#### <a name="required-fields"></a>Vereiste velden

Operator | Bestandsnaam | Case transformatie
---------|-----------|---------------
[Lijst met standaardoperatoren](#standard-operator-list) | Tekenreeks, Int | Geen transformatie, naar hoofdletters, naar kleine letters

#### <a name="key-information"></a>Belangrijkste informatie

- Als u meerdere bestandsnamen wilt opgeven, scheidt u elke bestandsnaam met één spatie. 

### <a name="url-path"></a>URL-pad

Hiermee worden aanvragen geïdentificeerd die het opgegeven pad in de aanvragende URL bevatten.

#### <a name="required-fields"></a>Vereiste velden

Operator | Waarde | Case transformatie
---------|-------|---------------
[Lijst met standaardoperatoren](#standard-operator-list) | Tekenreeks, Int | Geen transformatie, naar hoofdletters, naar kleine letters

#### <a name="key-information"></a>Belangrijkste informatie

- Een bestandsnaamwaarde kan profiteren van wildcardwaarden. Elk bestandsnaampatroon kan bijvoorbeeld bestaan uit een of meer sterretjes (*), waarbij elk sterretje overeenkomt met een reeks van een of meer tekens.

## <a name="reference-for-rules-engine-match-conditions"></a>Verwijzing naar regels motor overeenkomen met voorwaarden

### <a name="standard-operator-list"></a>Lijst met standaardoperatoren

Voor regels die waarden uit de lijst met standaardoperatoren accepteren, zijn de volgende operatoren geldig:

- Alle
- Is gelijk aan 
- Contains 
- Begint met 
- Eindigt met 
- Kleiner dan
- Minder dan of gelijk
- Groter dan
- Groter dan of gelijk
- Niet
- Bevat geen
- Niet begint met 
- Eindigt niet met 
- Niet minder dan
- Niet minder dan of gelijk
- Niet groter dan
- Niet groter dan of gelijk

Voor numerieke operatoren zoals *Kleiner dan* en Groter dan *of gelijken,* is de gebruikte vergelijking gebaseerd op lengte. In dit geval moet de waarde in de wedstrijdvoorwaarde een geheel getal zijn dat gelijk is aan de lengte die u wilt vergelijken. 

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure CDN](cdn-overview.md)
- [Naslaginformatie over standaardregelengine](cdn-standard-rules-engine-reference.md)
- [Acties in de standaardregels-engine](cdn-standard-rules-engine-actions.md)
- [HTTPS afdwingen met de standaardregelengine](cdn-standard-rules-engine.md)
