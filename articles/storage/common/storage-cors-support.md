---
title: Ondersteuning voor het delen van middelen van oorsprong (CORS) | Microsoft Documenten
description: Meer informatie over het inschakelen van CORS-ondersteuning voor de Microsoft Azure Storage Services.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bb296db0d97382deac984369704777de5d5cb362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65147693"
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>CorS-ondersteuning (Cross-Origin Resource Sharing) voor de Azure Storage Services
Vanaf versie 2013-08-15 ondersteunen de Azure-opslagservices Cross-Origin Resource Sharing (CORS) voor de blob-, tabel-, wachtrij- en bestandsservices. CORS is een HTTP-functie waarmee een webtoepassing die onder het ene domein wordt uitgevoerd, toegang heeft tot bronnen in een ander domein. Webbrowsers implementeren een beveiligingsbeperking die bekend staat als [beleid van dezelfde oorsprong,](https://www.w3.org/Security/wiki/Same_Origin_Policy) waardoor een webpagina api's in een ander domein niet kan aanroepen. CORS biedt een veilige manier om het ene domein (het oorsprongsdomein) api's in een ander domein te laten aanroepen. Zie de [CORS-specificatie](https://www.w3.org/TR/cors/) voor meer informatie over CORS.

U CORS-regels afzonderlijk instellen voor elk van de opslagservices door [De eigenschappen van Blob-service instellen,](https://msdn.microsoft.com/library/hh452235.aspx) [Serviceeigenschappen voor wachtrijservice instellen](https://msdn.microsoft.com/library/hh452232.aspx)en Eigenschappen van [tabelservice instellen.](https://msdn.microsoft.com/library/hh452240.aspx) Zodra u de CORS-regels voor de service hebt ingesteld, wordt een correct geautoriseerd verzoek tegen de service vanuit een ander domein geëvalueerd om te bepalen of dit is toegestaan volgens de regels die u hebt opgegeven.

> [!NOTE]
> Houd er rekening mee dat CORS geen verificatiemechanisme is. Elk verzoek tegen een opslagbron wanneer CORS is ingeschakeld, moet over een juiste verificatiehandtekening beschikken of moeten worden ingediend tegen een openbare resource.
> 
> 

## <a name="understanding-cors-requests"></a>Cors-verzoeken begrijpen
Een CORS-verzoek van een oorsprongsdomein kan bestaan uit twee afzonderlijke verzoeken:

* Een preflight-verzoek, waarin de cors-beperkingen die door de service zijn opgelegd, worden opgevraagd. De preflight-aanvraag is vereist, tenzij de aanvraagmethode een [eenvoudige methode](https://www.w3.org/TR/cors/)is, wat betekent GET, HEAD of POST.
* De werkelijke aanvraag, gemaakt tegen de gewenste bron.

### <a name="preflight-request"></a>Preflight-aanvraag
De preflight-aanvraag vraagt de CORS-beperkingen die door de eigenaar van het account zijn ingesteld voor de opslagservice. De webbrowser (of andere user agent) stuurt een OPTIONS-verzoek met de aanvraagkoppen, de methode en het oorsprongsdomein. De opslagservice evalueert de beoogde bewerking op basis van een vooraf geconfigureerde set CORS-regels die aangeven welke oorsprongsdomeinen, aanvraagmethoden en aanvraagkoppen kunnen worden opgegeven op een werkelijke aanvraag voor een opslagbron.

Als CORS is ingeschakeld voor de service en er een CORS-regel is die overeenkomt met de preflight-aanvraag, reageert de service met statuscode 200 (OK) en bevat de vereiste Access-Control-headers in het antwoord.

Als CORS niet is ingeschakeld voor de service of als er geen CORS-regel overeenkomt met de preflight-aanvraag, reageert de service met statuscode 403 (Verboden).

Als de opties-aanvraag niet de vereiste CORS-headers bevat (de headers Origin en Access-Control-Request-Method), reageert de service met statuscode 400 (Slechte aanvraag).

Houd er rekening mee dat een preflight-aanvraag wordt beoordeeld op basis van de service (Blob, Wachtrij en tabel) en niet tegen de gevraagde bron. De eigenaar van het account moet CORS hebben ingeschakeld als onderdeel van de accountservice-eigenschappen om het verzoek te laten slagen.

### <a name="actual-request"></a>Werkelijke aanvraag
Zodra de preflight-aanvraag is geaccepteerd en het antwoord is geretourneerd, verzendt de browser de werkelijke aanvraag tegen de opslagbron. De browser weigert het daadwerkelijke verzoek onmiddellijk als het preflight-verzoek wordt afgewezen.

De werkelijke aanvraag wordt behandeld als een normaal verzoek tegen de opslagservice. De aanwezigheid van de Origin-header geeft aan dat de aanvraag een CORS-verzoek is en dat de service de overeenkomende CORS-regels controleert. Als er een overeenkomst wordt gevonden, worden de Access-Control-headers aan het antwoord toegevoegd en teruggestuurd naar de client. Als er geen overeenkomst wordt gevonden, worden de CORS Access-Control-headers niet geretourneerd.

## <a name="enabling-cors-for-the-azure-storage-services"></a>CORS inschakelen voor de Azure Storage-services
CORS-regels zijn ingesteld op serviceniveau, dus u moet CORS voor elke service (Blob, Wachtrij en tabel) afzonderlijk in- of uitschakelen. CORS is standaard uitgeschakeld voor elke service. Als u CORS wilt inschakelen, moet u de juiste service-eigenschappen instellen met versie 2013-08-15 of hoger en CORS-regels toevoegen aan de service-eigenschappen. Raadpleeg De eigenschappen van [De Klodderservice](https://msdn.microsoft.com/library/hh452235.aspx)instellen, [Service-eigenschappen voor](https://msdn.microsoft.com/library/hh452232.aspx)het instellen of uitschakelen van CORS voor een service en het instellen van CORS-regels. [Set Table Service Properties](https://msdn.microsoft.com/library/hh452240.aspx)

Hier vindt u een voorbeeld van één CORS-regel die is opgegeven via een bewerking Serviceeigenschappen instellen:

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Elk element dat in de CORS-regel is opgenomen, wordt hieronder beschreven:

* **AllowedOrigins**: De herkomstdomeinen die via CORS een verzoek tegen de opslagservice mogen indienen. Het oorsprongsdomein is het domein waaruit de aanvraag afkomstig is. Houd er rekening mee dat de oorsprong een exacte case-gevoelige match moet zijn met de oorsprong die de gebruikersleeftijd naar de service stuurt. U het jokerteken '*' ook gebruiken om alle oorsprongsdomeinen via CORS verzoeken te laten indienen. In het bovenstaande voorbeeld kunnen\/de domeinen\/http: /www.contoso.com en http: /www.fabrikam.com aanvragen indienen tegen de service met CORS.
* **AllowedMethods**: De methoden (HTTP-aanvraagwerkwoorden) die het oorsprongsdomein kan gebruiken voor een CORS-aanvraag. In het bovenstaande voorbeeld zijn alleen PUT- en GET-aanvragen toegestaan.
* **Toegestane headers:** de aanvraagkoppen die het oorsprongsdomein kan opgeven op het CORS-verzoek. In het bovenstaande voorbeeld zijn alle metadataheaders die beginnen met x-ms-meta-data, x-ms-meta-target en x-ms-meta-abc toegestaan. Houd er rekening mee dat het jokerteken '*' aangeeft dat elke koptekst die begint met het opgegeven voorvoegsel is toegestaan.
* **ExposedHeaders**: De antwoordkoppen die in het antwoord op het CORS-verzoek kunnen worden verzonden en door de browser worden blootgesteld aan de uitgever van het verzoek. In het bovenstaande voorbeeld wordt de browser geïnstrueerd om elke koptekst die begint met x-ms-meta bloot te leggen.
* **MaxAgeInSeconds:** de maximale hoeveelheid tijd die een browser moet cache de preflight OPTIES verzoek.

De Azure-opslagservices ondersteunen het opgeven van vooraf vastgestelde kopteksten voor zowel de elementen **AllowedHeaders** als **ExposedHeaders.** Als u een categorie kopteksten wilt toestaan, u een gemeenschappelijk voorvoegsel voor die categorie opgeven. Als u bijvoorbeeld *x-ms-meta** opgeeft als een vooraf vastgestelde koptekst, wordt een regel bepaald die overeenkomt met alle kopteksten die beginnen met x-ms-meta.

De volgende beperkingen gelden voor cors-regels:

* U maximaal vijf CORS-regels per opslagservice opgeven (Blob, Tabel en Wachtrij).
* De maximale grootte van alle INSTELLINGEN van de CORS-regels op het verzoek, met uitzondering van XML-tags, mag niet groter zijn dan 2 KB.
* De lengte van een toegestane koptekst, blootgestelde koptekst of toegestane oorsprong mag niet meer dan 256 tekens bedragen.
* Toegestane kopteksten en blootgestelde kopteksten kunnen:
  * Letterlijke kopteksten, waarbij de exacte kopnaam wordt opgegeven, zoals **x-ms-meta-verwerkt**. Op de aanvraag kunnen maximaal 64 letterlijke kopteksten worden opgegeven.
  * Vooraf vastgelegde kopteksten, waarbij een voorvoegsel van de koptekst wordt verstrekt, zoals **x-ms-meta-data***. Als u op deze manier een voorvoegsel opgeeft, kan of wordt elke koptekst die begint met het opgegeven voorvoegsel, mogelijk of blootgesteld. Op de aanvraag kunnen maximaal twee vooraf vastgestelde kopteksten worden opgegeven.
* De methoden (of HTTP-werkwoorden) die zijn opgegeven in het element **Toegestane methoden** moeten voldoen aan de methoden die worden ondersteund door API's van Azure-opslagservice. Ondersteunde methoden zijn DELETE, GET, HEAD, MERGE, POST, OPTIONS en PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>CorS-regelevaluatielogica begrijpen
Wanneer een opslagservice een preflight of daadwerkelijke aanvraag ontvangt, evalueert deze die aanvraag op basis van de CORS-regels die u voor de service hebt ingesteld via de juiste bewerking Set Service Properties. CORS-regels worden geëvalueerd in de volgorde waarin ze zijn ingesteld in de aanvraaginstantie van de bewerking Set Service Properties.

Cors-regels worden als volgt beoordeeld:

1. Eerst wordt het **oorsprongsdomein** van de aanvraag gecontroleerd op de domeinen die worden vermeld voor het element Toegestane Oorsprong. Als het oorsprongsdomein in de lijst is opgenomen of als alle domeinen zijn toegestaan met het jokerteken '*', gaat de evaluatie van de regels door. Als het oorsprongsdomein niet is opgenomen, mislukt de aanvraag.
2. Vervolgens wordt de methode (of HTTP-werkwoord) van de aanvraag gecontroleerd op basis van de methoden die worden vermeld in het element **Toegestane methoden.** Als de methode in de lijst is opgenomen, gaat de evaluatie van de regels door; zo niet, dan is het verzoek mislukt.
3. Als de aanvraag overeenkomt met een regel in het oorsprongsdomein en de bijbehorende methode, wordt die regel geselecteerd om de aanvraag te verwerken en worden er geen verdere regels geëvalueerd. Voordat de aanvraag kan slagen, worden de headers die op de aanvraag zijn opgegeven, echter gecontroleerd op de kopteksten in het element **Toegestane headers.** Als de verzonden kopteksten niet overeenkomen met de toegestane kopteksten, mislukt de aanvraag.

Aangezien de regels worden verwerkt in de volgorde waarin ze aanwezig zijn in de aanvraaginstantie, raden aanbevolen aanbevolen procedures u aan om eerst de meest beperkende regels met betrekking tot de oorsprong in de lijst op te geven, zodat deze eerst worden geëvalueerd. Geef aan het einde van de lijst regels op die minder beperkend zijn, bijvoorbeeld een regel om alle oorsprong toe te staan.

### <a name="example--cors-rules-evaluation"></a>Voorbeeld – Evaluatie van de CORS-regels
In het volgende voorbeeld wordt een gedeeltelijke aanvraaginstantie voor een bewerking weergegeven om CORS-regels voor de opslagservices in te stellen. Zie [Eigenschappen van Blob-service](https://msdn.microsoft.com/library/hh452235.aspx)instellen , [Serviceeigenschappen voor wachtrijservice instellen](https://msdn.microsoft.com/library/hh452232.aspx)en Eigenschappen van [tabelservice instellen](https://msdn.microsoft.com/library/hh452240.aspx) voor meer informatie over het maken van de aanvraag.

```xml
<Cors>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>PUT,HEAD</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>*</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
    </CorsRule>
</Cors>
```

Overweeg vervolgens de volgende CORS-aanvragen:

| Aanvraag |  |  | Antwoord |  |
| --- | --- | --- | --- | --- |
| **Methode** |**Oorsprong** |**Aanvraagheaders** |**Regelovereenkomst** |**Resultaat** |
| **ZET** |http:\//www.contoso.com |x-ms-blob-content-type |Eerste regel |Geslaagd |
| **Toevoegen** |http:\//www.contoso.com |x-ms-blob-content-type |Tweede regel |Geslaagd |
| **Toevoegen** |http:\//www.contoso.com |x-ms-client-request-id |Tweede regel |Fout |

De eerste aanvraag komt overeen met de eerste regel - het oorsprongsdomein komt overeen met de toegestane oorsprong, de methode komt overeen met de toegestane methoden en de koptekst komt overeen met de toegestane kopteksten en slaagt dus.

De tweede aanvraag komt niet overeen met de eerste regel omdat de methode niet overeenkomt met de toegestane methoden. Het komt echter overeen met de tweede regel, dus het slaagt.

De derde aanvraag komt overeen met de tweede regel in het oorsprongsdomein en de methode, zodat er geen verdere regels worden geëvalueerd. De *x-ms-client-request-id header* is echter niet toegestaan door de tweede regel, dus het verzoek mislukt, ondanks het feit dat de semantiek van de derde regel het mogelijk zou hebben gemaakt om te slagen.

> [!NOTE]
> Hoewel in dit voorbeeld een minder restrictieve regel vóór een restrictievere regel wordt weergegeven, is het in het algemeen de beste praktijk om eerst de meest beperkende regels op te sommen.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Begrijpen hoe de koptekst Variëren is ingesteld
De koptekst *Variëren* is een standaard HTTP/1.1-header die bestaat uit een set van koptekstvelden voor aanvragen die de browser of gebruikersagent adviseren over de criteria die door de server zijn geselecteerd om de aanvraag te verwerken. De *Vary-header* wordt voornamelijk gebruikt voor caching door proxy's, browsers en CDN's, die deze gebruiken om te bepalen hoe de respons in de cache moet worden opgeslagen. Zie voor meer informatie de specificatie voor de [kop -Header Variëren](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Wanneer de browser of een andere user agent het antwoord van een CORS-verzoek in de cache opslaat, wordt het oorsprongsdomein in de cache opgeslagen als de toegestane oorsprong. Wanneer een tweede domein dezelfde aanvraag voor een opslagbron uitgeeft terwijl de cache actief is, haalt de gebruikersagent het oorsprongsdomein in de cache op. Het tweede domein komt niet overeen met het domein in de cache, dus de aanvraag mislukt wanneer het anders zou slagen. In bepaalde gevallen stelt Azure Storage de koptekst Variëren in op **Origin** om de gebruikersagent te instrueren het volgende CORS-verzoek naar de service te verzenden wanneer het aanvragende domein verschilt van de oorsprong in de cache.

Azure Storage stelt de *koptekst Variëren* in de volgende gevallen in op **Origin** voor werkelijke GET/HEAD-aanvragen:

* Wanneer de oorsprong van het verzoek precies overeenkomt met de toegestane oorsprong die is gedefinieerd door een CORS-regel. Om een exacte overeenkomst te zijn, mag de CORS-regel geen jokerteken bevatten .
* Er is geen regel die overeenkomt met de oorsprong van de aanvraag, maar CORS is ingeschakeld voor de opslagservice.

In het geval dat een GET/HEAD-aanvraag overeenkomt met een CORS-regel die alle oorsprong toestaat, geeft het antwoord aan dat alle oorsprong is toegestaan en de cache van de gebruikersagent latere aanvragen van elk herkomstdomein toestaat terwijl de cache actief is.

Houd er rekening mee dat voor aanvragen met andere methoden dan GET/HEAD, de opslagservices de koptekst Variëren niet instellen, omdat reacties op deze methoden niet in de cache worden opgeslagen door gebruikersagents.

In de volgende tabel wordt aangegeven hoe Azure-opslag reageert op GET/HEAD-aanvragen op basis van de eerder genoemde gevallen:

| Aanvraag | Accountinstelling en resultaat van regelevaluatie |  |  | Antwoord |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| **Origin header aanwezig op aanvraag** |**CORS-regel(en) die voor deze service is opgegeven** |**Er bestaat een overeenkomende regel die alle oorsprong(*) toestaat** |**Overeenkomende regel bestaat voor exacte oorsprong smet** |**Respons omvat Vary header ingesteld op Origin** |**Antwoord omvat Access-Control-Allowed-Origin: "*"** |**Respons bevat Access-Control-Exposed-Headers** |
| Nee |Nee |Nee |Nee |Nee |Nee |Nee |
| Nee |Ja |Nee |Nee |Ja |Nee |Nee |
| Nee |Ja |Ja |Nee |Nee |Ja |Ja |
| Ja |Nee |Nee |Nee |Nee |Nee |Nee |
| Ja |Ja |Nee |Ja |Ja |Nee |Ja |
| Ja |Ja |Nee |Nee |Ja |Nee |Nee |
| Ja |Ja |Ja |Nee |Nee |Ja |Ja |

## <a name="billing-for-cors-requests"></a>Facturering voor CORS-aanvragen
Er worden succesvolle preflight-aanvragen in rekening gebracht als u CORS hebt ingeschakeld voor een van de opslagservices voor uw account (door [De eigenschappen van Blob-service instellen,](https://msdn.microsoft.com/library/hh452235.aspx) [Wachtrijserviceeigenschappen instellen](https://msdn.microsoft.com/library/hh452232.aspx)of Eigenschappen van [tabelservice instellen)](https://msdn.microsoft.com/library/hh452240.aspx)in te schakelen. Als u de kosten wilt minimaliseren, u overwegen het **element MaxAgeInSeconden** in uw CORS-regels in te stellen op een grote waarde, zodat de gebruikersagent de aanvraag in de cache opslaat.

Mislukte preflight-aanvragen worden niet in rekening gebracht.

## <a name="next-steps"></a>Volgende stappen
[Blob-serviceeigenschappen instellen](https://msdn.microsoft.com/library/hh452235.aspx)

[Eigenschappen van wachtrijservice instellen](https://msdn.microsoft.com/library/hh452232.aspx)

[Eigenschappen van tabelservice instellen](https://msdn.microsoft.com/library/hh452240.aspx)

[W3C-specificatie voor het delen van resources van W3C-bronnen](https://www.w3.org/TR/cors/)

