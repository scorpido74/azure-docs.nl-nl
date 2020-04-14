---
title: HTTP-variabelen voor Azure CDN-regels engine | Microsoft Documenten
description: Met HTTP-variabelen u metagegevens voor HTTP-aanvragen en antwoorden ophalen.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: allensu
ms.openlocfilehash: b9ced5d4a81effcd73e0243d09bb83ed0fe7667c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253693"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>HTTP-variabelen voor Azure CDN-regelsengine
HTTP-variabelen bieden de middelen waarmee u metagegevens voor HTTP-aanvragen en antwoorden ophalen. Deze metagegevens kunnen vervolgens worden gebruikt om een aanvraag of een antwoord dynamisch te wijzigen. Het gebruik van HTTP-variabelen is beperkt tot de volgende kenmerken van de rules engine:

- [Cachesleutel herschrijven](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Koptekst clientaanvraag wijzigen](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Koptekst clientreactie wijzigen](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL-omleiding](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL opnieuw genereren](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definities
In de volgende tabel worden de ondersteunde HTTP-variabelen beschreven. Een lege waarde wordt geretourneerd wanneer GEO-metagegevens (bijvoorbeeld postcode) niet beschikbaar zijn voor een bepaald verzoek.


| Name | Variabele | Beschrijving | Voorbeeldwaarde |
| ---- | -------- | ----------- | ------------ |
| ASN (aanvrager) | %{geo_asnum} | Geeft het AS-nummer van de aanvrager aan. <br /><br />**Afgeschaft:** %{virt_dst_asnum}. <br />Deze variabele is afgeschaft ten gunste van %{geo_asnum}. Hoewel een regel die deze afgeschafte variabele gebruikt, blijft werken, moet u deze bijwerken om de nieuwe variabele te gebruiken. | AS15133 |
| Stad (Aanvrager) | %{geo_city} | Geeft de stad van de aanvrager aan. | Los Angeles |
| Continent (Aanvrager) | %{geo_continent} | Geeft het continent van de aanvrager aan via de afkorting. <br />Geldige waarden zijn: <br />AF: Afrika<br />AS: Azië<br />EU: Europa<br />NA: Noord-Amerika<br />OC: Oceanië<br />SA: Zuid-Amerika<br /><br />**Afgeschaft:** %{virt_dst_continent}. <br />Deze variabele is afgeschaft ten gunste van %{geo_continent}. <br />Hoewel een regel die deze afgeschafte variabele gebruikt, blijft werken, moet u deze bijwerken om de nieuwe variabele te gebruiken.| N.v.t. |
| Cookiewaarde | %{cookie_Cookie} | Geeft als resultaat de waarde die overeenkomt met de cookiesleutel die wordt geïdentificeerd door de cookieterm. | Voorbeeldgebruik: <br />%{cookie__utma}<br /><br />Voorbeeldwaarde:<br />111662281.2.10.1222100123 |
| Land (aanvrager) | %{geo_country} | Geeft het land van herkomst van de aanvrager aan via zijn landcode. <br />**Afgeschaft:** %{virt_dst_country}. <br /><br />Deze variabele is afgeschaft ten gunste van %{geo_country}. Hoewel een regel die deze afgeschafte variabele gebruikt, blijft werken, moet u deze bijwerken om de nieuwe variabele te gebruiken. | VS |
| Aangewezen marktgebied (aanvrager) | %{geo_dma_code} |Geeft de mediamarkt van de aanvrager aan op zijn regiocode. <br /><br />Dit veld is alleen van toepassing op aanvragen die afkomstig zijn uit de Verenigde Staten.| 745 |
| HTTP-aanvraagmethode | %{request_method} | Geeft de HTTP-aanvraagmethode aan. | GET |
| HTTP-statuscode | %{status} | Geeft de HTTP-statuscode voor het antwoord aan. | 200 |
| IP-adres (aanvrager) | %{virt_dst_addr} | Geeft het IP-adres van de aanvrager aan. | 192.168.1.1 |
| Latitude (aanvrager) | %{geo_latitude} | Geeft de breedtegraad van de aanvrager aan. | 34.0995 |
| Longitude (Aanvrager) | %{geo_longitude} | Geeft de longitude van de aanvrager aan. | -118.4143 |
| Metropolitan Statistical Area (Aanvrager) | %{geo_metro_code} | Geeft het grootstedelijk gebied van de aanvrager aan. <br /><br />Dit veld is alleen van toepassing op aanvragen die afkomstig zijn uit de Verenigde Staten.<br />| 745 |
| Poort (aanvrager) | %{virt_dst_port} | Geeft de tijdelijke poort van de aanvrager aan. | 55885 |
| Postcode (aanvrager) | %{geo_postal_code} | Geeft de postcode van de aanvrager aan. | 90210 |
| Querytekenreeks gevonden | %{is_args} | De waarde voor deze variabele is afhankelijk van de vraag of de aanvraag een querytekenreeks bevat.<br /><br />- Query string gevonden: ?<br />- Geen querytekenreeks: NULL | ? |
| Parameter querytekenreeks gevonden | %{is_amp} | De waarde voor deze variabele is afhankelijk van de vraag of de aanvraag ten minste één querytekenreeksparameter bevat.<br /><br />- Gevonden parameter: &<br />- Geen parameters: NULL | & |
| Parameterwaarde querytekenreeks | %{arg_&lt;&gt;parameter } | Geeft als resultaat de waarde die overeenkomt &lt;&gt; met de parameter querytekenreeks die wordt geïdentificeerd door de parameterterm. | Voorbeeldgebruik: <br />%{arg_language}<br /><br />Parameter voorbeeldquerytekenreeks: <br />?taal=nl<br /><br />Voorbeeldwaarde: nl |
| Waarde van querytekenreeks | %{query_string} | Geeft de volledige querytekenreekswaarde aan die is gedefinieerd in de aanvraag-URL. |key1=val1&toets2=val2&toets3=val3 |
| Verwijzerdomein | %{referring_domain} | Geeft het domein aan dat is gedefinieerd in de koptekst van de verwijzingsaanvraag. | <www.google.com> |
| Regio (aanvrager) | %{geo_region} | Hiermee geeft u het gebied van de aanvrager (bijvoorbeeld staat of provincie) aan via de alfanumerieke afkorting. | CA |
| Waarde voor koptekst aanvragen | %{http_RequestHeader} | Geeft als resultaat de waarde die overeenkomt met de aangezochte koptekst die wordt geïdentificeerd door de term RequestHeader. <br /><br />Als de naam van de aanvraagkop een streepje bevat (bijvoorbeeld User-Agent), vervang deze dan door een underscore (bijvoorbeeld User_Agent).| Voorbeeldgebruik: %{http_Connection}<br /><br />Voorbeeldwaarde: Keep-Alive | 
| Host aanvragen | %{host} | Geeft de host aan die is gedefinieerd in de aanvraag-URL. | <www.mydomain.com> |
| Aanvraagprotocol | %{request_protocol} | Geeft het aanvraagprotocol aan. | HTTP/1.1 |
| Aanvraagregeling | %{regeling} | Geeft het aanvraagschema aan. |http |
| URI aanvragen (relatief) | %{request_uri} | Geeft het relatieve pad aan, inclusief de querytekenreeks, gedefinieerd in de aanvraag-URI. | /marketing/foo.js?loggedin=true |
| URI aanvragen (Relatief zonder querytekenreeks) | %{uri} | Geeft het relatieve pad naar de gevraagde inhoud aan. <br /><br/>Belangrijkste informatie:<br />- Dit relatieve pad sluit de querytekenreeks uit.<br />- Dit relatieve pad weerspiegelt de herschrijft van de URL. Een URL wordt herschreven onder de volgende voorwaarden:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL Herschrijven Functie: deze functie herschrijft het relatieve pad dat is gedefinieerd in de aanvraag URI.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL van Edge CNAME: Dit type aanvraag wordt herschreven naar de bijbehorende CDN-URL. |/800001/corigin/rewrittendir/foo.js |
| Aanvraag-URI | %{verzoek} | Beschrijft het verzoek. <br />Syntaxis: &lt;&gt; &lt;HTTP-methode relatief pad&gt; &lt;HTTP-protocol&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Waarde van antwoordkoptekst | %{resp_&lt;ResponseHeader&gt;} | Geeft als resultaat de waarde die &lt;overeenkomt&gt; met de antwoordkop die wordt aangeduid met de term ResponseHeader. <br /><br />Als de naam van de antwoordkop een streepje bevat (bijvoorbeeld User-Agent), vervang deze dan door een underscore (bijvoorbeeld User_Agent). | Voorbeeldgebruik: %{resp_Content_Length}<br /><br />Voorbeeldwaarde: 100 |

## <a name="usage"></a>Gebruik
In de volgende tabel wordt de juiste syntaxis beschreven voor het opgeven van een HTTP-variabele.


| Syntaxis | Voorbeeld | Beschrijving |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariabele&gt;} | %{host} | Gebruik deze syntaxis om de volledige &lt;waarde&gt;te krijgen die overeenkomt met de opgegeven HTTPVariable . |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | Gebruik deze syntaxis om de aanvraag in &lt;te stellen voor&gt;de volledige waarde die overeenkomt met de opgegeven HTTPVariableLimiter . |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.\.([^ :]+)/cdn.$2.$3:80} | Gebruik een reguliere &lt;expressie voor HTTPVariableDelimiterExpression&gt; om de waarde van een HTTP-variabele te vervangen, te verwijderen of te manipuleren. |

HTTP-variabele namen ondersteunen alleen alfabetische tekens en underscores. Niet-ondersteunde tekens converteren naar underscores.

## <a name="delimiter-reference"></a>Referentie van de scheidingsteken
Een scheidingsteken kan na een HTTP-variabele worden opgegeven om een van de volgende effecten te bereiken:

- Transformeer de waarde die aan de variabele is gekoppeld.

     Voorbeeld: Converteer de volledige waarde naar kleine letters.

- Verwijder de waarde die aan de variabele is gekoppeld.

- De waarde van de variabele manipuleren.

     Voorbeeld: Gebruik reguliere expressies om de waarde te wijzigen die is gekoppeld aan de HTTP-variabele.

De scheidingstekens worden beschreven in de volgende tabel.

| Scheidingsteken | Beschrijving |
| --------- | ----------- |
| := | Geeft aan dat een standaardwaarde wordt toegewezen aan de variabele wanneer deze: <br />- Vermist <br />- Ingesteld op NULL. |
| :+ | Geeft aan dat een standaardwaarde wordt toegewezen aan de variabele wanneer er een waarde aan is toegewezen. |
| : | Hiermee geeft u aan dat een subtekenreeks van de waarde die aan de variabele is toegewezen, wordt uitgebreid. |
| # | Geeft aan dat het patroon dat na deze scheidingwordt opgegeven, moet worden verwijderd wanneer het wordt gevonden aan het begin van de waarde die aan de variabele is gekoppeld. |
| % | Geeft aan dat het patroon dat na deze scheidingwordt opgegeven, moet worden verwijderd wanneer het wordt gevonden aan het einde van de waarde die aan de variabele is gekoppeld. <br />Deze definitie is alleen van toepassing wanneer het %-symbool als scheidingsteken wordt gebruikt. |
| / | Hiermee wordt een HTTP-variabele of een patroon gedefinieerd. |
| // | Alle exemplaren van het opgegeven patroon zoeken en vervangen. |
| /= | Alle exemplaren van het opgegeven patroon zoeken, kopiëren en herschrijven. |
| , | Converteer de waarde die is gekoppeld aan de HTTP-variabele in kleine letters. |
| ^ | Converteer de waarde die is gekoppeld aan de HTTP-variabele in hoofdletters. |
| ,, | Converteer alle exemplaren van het opgegeven teken in de waarde die is gekoppeld aan de HTTP-variabele in kleine letters. |
| ^^ | Converteer alle exemplaren van het opgegeven teken in de waarde die is gekoppeld aan de HTTP-variabele in hoofdletters. |

## <a name="exceptions"></a>Uitzonderingen
In de volgende tabel worden omstandigheden beschreven waaronder de opgegeven tekst niet wordt behandeld als een HTTP-variabele.

| Voorwaarde | Beschrijving | Voorbeeld |
| --------- | ----------- | --------|
| Ontsnappend % symbool | Het percentage symbool kan worden ontsnapt door het gebruik van een backslash. <br />De voorbeeldwaarde rechts wordt behandeld als een letterlijke waarde en niet als een HTTP-variabele.| \%{host} |
| Onbekende variabelen | Een lege tekenreeks wordt altijd geretourneerd voor onbekende variabelen. | %{unknown_variable} |
| Ongeldige tekens of syntaxis | Variabelen die ongeldige tekens of syntaxis bevatten, worden behandeld als letterlijke waarden. <br /><br />Voorbeeld #1: de opgegeven waarde bevat een ongeldig teken (bijvoorbeeld -). <br /><br />Voorbeeld #2: De opgegeven waarde bevat een dubbele set krullende accolades. <br /><br />Voorbeeld #3: de opgegeven waarde mist een sluitende krullende brace.<br /> | Voorbeeld #1: %{resp_user-agent} <br /><br />Voorbeeld #2: %{host}} <br /><br />Voorbeeld #3: %{host |
| Ontbrekende variabelenaam | Een NULL-waarde wordt altijd geretourneerd wanneer een variabele niet is opgegeven. | %{} |
| Achterliggende tekens | Tekens die een variabele volgen, worden behandeld als letterlijke waarden. <br />De monsterwaarde rechts bevat een achterliggende krullende brace die wordt behandeld als een letterlijke waarde. | %{host}} |

## <a name="setting-default-header-values"></a>Standaardkopwaarden instellen
Een standaardwaarde kan aan een koptekst worden toegewezen wanneer deze aan een van de volgende voorwaarden voldoet:
- Ontbrekende/ontsette
- Ingesteld op NULL.

In de volgende tabel wordt beschreven hoe u een standaardwaarde definieert.

| Voorwaarde | Syntaxis | Voorbeeld | Beschrijving |
| --------- | ------ | --------| ----------- |
| Stel een koptekst in op een standaardwaarde wanneer deze aan een van de volgende voorwaarden voldoet: <br /><br />- Ontbrekende header <br /><br />- Koptekstwaarde is ingesteld op NULL.| %{Variabele:=Waarde} | %{http_referrer:=niet gespecificeerd} | De referrer-header wordt alleen ingesteld op *niet-gespecificeerd* wanneer deze ontbreekt of is ingesteld op NULL. Er zal geen actie worden ondernomen als deze is ingesteld. |
| Stel een koptekst in op een standaardwaarde wanneer deze ontbreekt. | %{Variable=Waarde} | %{http_referrer=niet gespecificeerd} | De referrer-header wordt alleen ingesteld op *niet-gespecificeerd* wanneer deze ontbreekt. Er zal geen actie worden ondernomen als deze is ingesteld. |
| Stel de koptekst in op een standaardwaarde wanneer deze niet aan een van de volgende voorwaarden voldoet: <br /><br />- Vermist<br /><br /> - Ingesteld op NULL. | %{Variabele:+Waarde} | %{http_referrer:+niet-gespecificeerd} | De referrer-header wordt alleen ingesteld op *niet-gespecificeerd* wanneer er een waarde aan is toegewezen. Er vindt geen actie plaats als deze ontbreekt of is ingesteld op NULL. |

## <a name="manipulating-variables"></a>Variabelen manipuleren
Variabelen kunnen op de volgende manieren worden gemanipuleerd:

- Subtekenreeksen uitbreiden
- Patronen verwijderen

### <a name="substring-expansion"></a>Subtekenreeksuitbreiding
Standaard wordt een variabele uitgebreid naar de volledige waarde. Gebruik de volgende syntaxis om alleen een subtekenreeks van de waarde van de variabele uit te vouwen.

`%<Variable>:<Offset>:<Length>}`

Belangrijkste informatie:

- De waarde die aan de periode Verschuiving is toegewezen, bepaalt het beginteken van de subtekenreeks:

     - Positief: het beginteken van de subtekenreeks wordt berekend op basis van het eerste teken in de tekenreeks.
     - Nul: Het beginteken van de subtekenreeks is het eerste teken in de tekenreeks.
     - Negatief: het beginteken van de subtekenreeks wordt berekend op basis van het laatste teken in de tekenreeks.

- De lengte van de subtekenreeks wordt bepaald door de *term Lengte:*

     - Weggelaten: Als u de term Lengte weglaat, kan de subtekenreeks alle tekens bevatten tussen het beginteken en het einde van de tekenreeks.
     - Positief: hiermee bepaalt u de lengte van de subtekenreeks van het beginteken naar rechts.
     - Negatief: hiermee bepaalt u de lengte van de subtekenreeks van het beginteken naar links.

#### <a name="example"></a>Voorbeeld:

In het volgende voorbeeld wordt de volgende URL van de voorbeeldaanvraag gebaseerd:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

De volgende tekenreeks toont verschillende methoden voor het manipuleren van variabelen:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Op basis van de URL van de voorbeeldaanvraag produceert de bovenstaande variabele manipulatie de volgende waarde:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Patroonverwijdering
Tekst die overeenkomt met een specifiek patroon kan worden verwijderd uit het begin of het einde van de waarde van een variabele.

| Syntaxis | Actie |
| ------ | ------ |
| %{Variable#Pattern} | Verwijder tekst wanneer het opgegeven patroon wordt gevonden aan het begin van de waarde van een variabele. |
| %{Variabel%Patroon} | Verwijder tekst wanneer het opgegeven patroon wordt gevonden aan het einde van de waarde van een variabele. |

#### <a name="example"></a>Voorbeeld:

In dit voorbeeldscenario wordt de *request_uri* variabele ingesteld op:

`/800001/myorigin/marketing/product.html?language=en-US`

In de volgende tabel ziet u hoe deze syntaxis werkt.

| Syntaxis van het voorbeeld | Resultaten | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/klantoorsprong | /customerorigin/myorigin/marketing/product.html?language=en-US | Omdat de variabele begint met het patroon, is deze vervangen. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Omdat de variabele niet eindigt met het patroon, was er geen verandering.|

### <a name="find-and-replace"></a>Zoeken en vervangen
De syntaxis zoeken en vervangen wordt beschreven in de volgende tabel.

| Syntaxis | Actie |
| ------ | ------ |
| %{Variabele/Zoeken/Vervangen} | Het eerste optreden van het opgegeven patroon zoeken en vervangen. |
| %{Variabele/Find/Replace} | Alle exemplaren van het opgegeven patroon zoeken en vervangen. |
| %{Variabele^} |Converteer de volledige waarde naar hoofdletters. |
| %{Variabele^Find} | Zet het eerste optreden van het opgegeven patroon om in hoofdletters. |
| %{Variabele,} | Converteer de volledige waarde in kleine letters. |
| %{Variabele, Zoeken} | Converteer de eerste instantie van het opgegeven patroon in kleine letters. |

### <a name="find-and-rewrite"></a>Zoeken en herschrijven
Gebruik de tekst die overeenkomt met het opgegeven patroon bij het herschrijven van een variant op zoeken en vervangen. De syntaxis zoeken en herschrijven wordt beschreven in de volgende tabel.

| Syntaxis | Actie |
| ------ | ------ |
| %{Variabele/=Zoeken/Herschrijven} | Alle exemplaren van het opgegeven patroon zoeken, kopiëren en herschrijven. |
| %{Variabele/^Zoeken/Herschrijven} | Zoek, kopieer en herschrijf het opgegeven patroon wanneer het gebeurt aan het begin van de variabele. |
| %{Variabele/$Find/Herschrijven} | Zoek, kopieer en herschrijf het opgegeven patroon wanneer het zich aan het einde van de variabele voordoet. |
| %{Variabele/Zoeken} | Alle exemplaren van het opgegeven patroon zoeken en verwijderen. |

Belangrijkste informatie:

- Vouw tekst uit die overeenkomt met het opgegeven patroon door een dollarteken op te geven, gevolgd door een geheel geheel geheel getal (bijvoorbeeld $ 1).

- Er kunnen meerdere patronen worden opgegeven. De volgorde waarin het patroon is opgegeven, bepaalt het geheel getal dat eraan wordt toegewezen. In het volgende voorbeeld komt het eerste patroon overeen met 'www.', het tweede patroon komt overeen met het domein op het tweede niveau en het derde patroon komt overeen met het domein op het hoogste niveau:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- De herschreven waarde kan bestaan uit elke combinatie van tekst en deze tijdelijke aanduidingen.

    In het vorige voorbeeld wordt de `cdn.$2.$3:80` hostnaam herschreven naar (bijvoorbeeld cdn.mydomain.com:80).

- Het geval van een tijdelijke aanduiding voor een patroon (bijvoorbeeld $ 1) kan worden gewijzigd via de volgende vlaggen:
     - U: Hoofdletters de uitgebreide waarde.

         Voorbeeldsyntaxis:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Kleine letters de uitgebreide waarde.

         Voorbeeldsyntaxis:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Een operator moet vóór het patroon worden opgegeven. De opgegeven operator bepaalt het gedrag voor het vastleggen van patronen:

     - `=`: Geeft aan dat alle exemplaren van het opgegeven patroon moeten worden vastgelegd en herschreven.
     - `^`: Geeft aan dat alleen tekst die begint met het opgegeven patroon wordt vastgelegd.
     - `$`: Geeft aan dat alleen tekst die eindigt met het opgegeven patroon wordt vastgelegd.
 
- Als u de waarde */Herschrijven* weglaat, wordt de tekst die overeenkomt met het patroon verwijderd.


