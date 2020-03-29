---
title: Vereisten gebruiken en weergeven voor de Bing Search API's
titleSuffix: Azure Cognitive Services
description: De vereisten voor het weergeven van zoekresultaten van de Bing Search API's in uw toepassingen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 5575668f164b97142e7c4b2ddb2608c3173426a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "60499882"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Vereisten voor gebruik en weergave van Bing Zoeken-API's

Deze gebruiks- en weergavevereisten zijn van toepassing op elke implementatie van de inhoud en bijbehorende informatie van de volgende Bing Search API's, waaronder relaties, metagegevens en andere signalen.

- Aangepast zoeken met Bing
- Bing Entiteiten zoeken
- Bing Afbeeldingen zoeken
- Zoeken in Bing Nieuws
- Zoeken in Bing Video
- Bing Visuele zoekopdrachten
- Bing Webzoekopdrachten
- Bing Spellingcontrole
- Bing Automatische suggesties

## <a name="definitions"></a>Definities


|Termijn  |Beschrijving  |
|---------|---------|
|Antwoord     | Een categorie resultaten die in een reactie is geretourneerd. Een antwoord van de Bing Web Search API kan bijvoorbeeld antwoorden bevatten in de categorieën webpaginaresultaten, afbeelding, video, visual en nieuws. |
|Antwoord     | Alle antwoorden en bijbehorende gegevens die zijn ontvangen als reactie op één aanroep naar een Search API. |
|Resultaat    | Een informatiepunt in een antwoord. Bijvoorbeeld, de set van gegevens in verband met een enkel nieuwsartikel is een resultaat in een nieuwsantwoord. |
|Zoek-API's    | gezamenlijk de Bing Custom Search, Entity Search, Image Search, News Search, Video Search, Visual Search, Local Business Search en Web Search API's. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>Bing Spell Check en Bing Automatisch API-beperkingen voorstellen

Niet doen:

- Kopieer, bewaar of cache alle gegevens die u ontvangt van de Bing Spell Check of Bing Autosuggest API's.
- Gebruik gegevens die u ontvangt van Bing Spell Check of Bing Autosuggest API's als onderdeel van machine learning of vergelijkbare algoritmische activiteiten. Gebruik deze gegevens niet om nieuwe of bestaande services die u of derden aanbieden, te trainen, evalueren of verbeteren.

## <a name="bing-search-apis"></a>Bing Zoeken-API's

> [!NOTE]
> De vereisten in deze sectie zijn alleen van toepassing op de zoek-API's, die geen Bing Spell Check of Bing Autosuggest bevatten. 

### <a name="internet-search-experience-requirements"></a>Vereisten voor zoekervaring via internet

Alle gegevens die in reacties worden geretourneerd, mogen alleen worden gebruikt in zoekervaringen op internet. Een zoekervaring op internet betekent de weergegeven inhoud: 

- Is relevant en reageert op de directe query van de eindgebruiker, of een andere indicatie van hun zoekinteresse en -intentie (bijvoorbeeld een door de gebruiker aangegeven zoekopdracht). 

- Helpt gebruikers bij het vinden en navigeren naar de gegevensbronnen van de reactie. Bijvoorbeeld, het verstrekken van klikbare links van hyperlinks in het antwoord.

- Bevat meerdere resultaten waaruit de gebruiker kan kiezen. 

- Zijn in een plaatsing die gebruikers in staat stelt om te zoeken.

- Bevat een zichtbare indicatie dat de inhoud een zoekresultaat op internet is. Bijvoorbeeld een verklaring dat de inhoud "van het web" is.

- Bevat alle andere passende maatregelen om ervoor te zorgen dat uw Bing Search API-gegevens geen toepasselijke wetten of rechten van derden schenden. Raadpleeg uw juridische adviseurs om te bepalen welke maatregelen geschikt kunnen zijn.

De enige uitzondering op deze vereisten voor zoekervaring op internet is voor URL-detectie, zoals later in dit artikel wordt beschreven. 

### <a name="restrictions"></a>Beperkingen

Niet doen:

- Kopieer, sla gegevens uit reacties op of in de cache (met uitzondering van retentie voor zover toegestaan door [de continuïteit van de service](#continuity-of-service). 

- Gebruik gegevens die zijn ontvangen van de zoek-API's als onderdeel van machine learning of vergelijkbare algoritmische activiteiten. Gebruik deze gegevens niet om nieuwe of bestaande services die u of derden aanbieden, te trainen, evalueren of verbeteren.

- Wijzig de inhoud van de resultaten (anders dan ze te formatteren op een manier die niet in strijd is met een andere eis), tenzij vereist door de wet of overeengekomen door Microsoft. 

- Attributiegegevens en URL's die zijn gekoppeld aan resultaatinhoud weglaten.

- Bestel, ook door omissie, de resultaten die worden weergegeven in een antwoord wanneer een bestelling of rangschikking wordt verstrekt, tenzij vereist door de wet of overeengekomen door Microsoft. 

    > [!NOTE]
    > Deze vereiste is niet van toepassing op het opnieuw ordenen geïmplementeerd via de portal voor de Bing Custom Search API.

- Andere inhoud in een deel van een reactie weergeven op een manier die ertoe zou leiden dat een gebruiker zou geloven dat de andere inhoud deel uitmaakt van het antwoord. 

- Advertenties weergeven die niet door Microsoft worden geleverd op een pagina waarop een deel van een antwoord wordt weergegeven. 

- Alle advertenties weergeven op pagina's met reacties:
    - Vanuit de Bing-afbeelding, nieuwszoekopdrachten, videozoeken of visuele zoek-API's
    - Die worden gefilterd of voornamelijk (of uitsluitend) gefilterd tot beeld, nieuws en/of video of visuele zoekresultaten.

### <a name="notices-and-branding"></a>Aankondigingen en branding 
Doen:

- Voeg prominent een functionele hyperlink naar de [Microsoft-privacyverklaring toe](https://go.microsoft.com/fwlink/?LinkId=521839), in de buurt van elk punt in de gebruikerservaring (UX) dat een gebruiker de mogelijkheid biedt om een zoekopdracht in te voeren. Label de hyperlink **Microsoft Privacy Statement**.

- Geef bing-branding prominent weer, in overeenstemming met de [richtlijnen voor het gebruik van Bing-handelsmerken,](https://go.microsoft.com/fwlink/?linkid=833278)in de buurt van elk punt in de UX dat een gebruiker de mogelijkheid biedt om een zoekopdracht in te voeren. Dergelijke branding moet duidelijk aan de gebruiker dat Microsoft is het aandrijven van de internet zoekervaring.

- U elk antwoord (of gedeelte van een antwoord) dat wordt weergegeven vanuit de Bing Web Search, Image Search, News Search, Video Search en Visual Search API's aan Microsoft toeschrijven, tenzij Microsoft anders aangeeft schriftelijk voor uw gebruik. Dit wordt beschreven in [richtlijnen voor het gebruik van handelsmerken bing.](https://go.microsoft.com/fwlink/?linkid=833278) 

Niet doen:

- Kenmerkreacties (of delen van reacties) die worden weergegeven vanuit de Bing Custom Search API aan Microsoft, tenzij Microsoft anders schriftelijk specificeert voor uw specifieke gebruik.

### <a name="transferring-responses"></a>Reacties overdragen

Als u een gebruiker in staat stelt een reactie van een zoek-API over te zetten naar een andere gebruiker, bijvoorbeeld via een berichten-app of het plaatsen van sociale media, geldt het volgende: 

- Overgedragen antwoorden moeten:
  - Bestaan uit inhoud die niet is gewijzigd ten opzichte van de inhoud van de antwoorden die worden weergegeven aan de overdragende gebruiker. Opmaakwijzigingen zijn toegestaan.
  - Bevat geen gegevens in metagegevensformulier.
  - Voor reacties van de Bing-web-, beeld-, nieuws-, video- en visuele API's wordt de weergavetaal die de respons aangeeft, verkregen via een zoekervaring op internet die wordt aangedreven door Bing. U bijvoorbeeld taal weergeven zoals 'Aangedreven door Bing' of 'Meer informatie over deze afbeelding op Bing' of u het Bing-logo gebruiken.
  - Voor reacties uit de Bing Custom Search API wordt de taal weergegeven die het antwoord aangeeft via een zoekervaring op internet. U bijvoorbeeld taal weergeven, zoals 'Meer informatie over dit zoekresultaat'.
  - Geef de volledige query weer die wordt gebruikt om het antwoord te genereren.
  - Voeg een prominente link of vergelijkbare toeschrijving toe aan de onderliggende bron van het antwoord, direct of via de zoekmachine (bing.com, m.bing.com of uw aangepaste zoekservice, indien van toepassing).
- U mag de overdracht van reacties niet automatiseren. Een overdracht moet worden gestart door een gebruikersactie die duidelijk een intentie om een reactie over te dragen aangeeft.
- U mag een gebruiker alleen in staat stellen om reacties over te zetten die zijn weergegeven als reactie op de query die de gebruiker overdraagt.

### <a name="continuity-of-service"></a>Continuïteit van de dienstverlening 

Kopieer, bewaar of cache geen gegevens uit antwoorden van de Search API. Om de continuïteit van de toegang tot de service en het renderen van gegevens mogelijk te maken, u echter alleen resultaten behouden onder de volgende voorwaarden:

#### <a name="device"></a>Apparaat

U een gebruiker in staat stellen om de resultaten op een apparaat gedurende de laagste (i) 24 uur vanaf het tijdstip van de query te bewaren, of (ii) totdat een gebruiker een andere query voor bijgewerkte resultaten indient, op voorwaarde dat bewaarde resultaten alleen mogen worden gebruikt:

- Om de gebruiker toegang te geven tot resultaten die eerder aan die gebruiker op dat apparaat zijn geretourneerd (bijvoorbeeld in geval van onderbreking van de service).
- Om resultaten op te slaan die worden geretourneerd voor uw proactieve query, gepersonaliseerd in afwachting van de behoeften van de gebruiker, op basis van de signalen van die gebruiker (bijvoorbeeld in geval van verwachte onderbreking van de service).

#### <a name="server"></a>server

U resultaten die specifiek zijn voor één gebruiker veilig bewaren op een server die u beheert en alleen de bewaarde resultaten weergeven:

- Om de gebruiker toegang te geven tot een historisch rapport van resultaten die eerder in uw oplossing aan die gebruiker zijn geretourneerd. De resultaten mogen niet langer dan 21 dagen worden bewaard vanaf het moment van de eerste query van de eindgebruiker en (ii) worden weergegeven in reactie op de nieuwe of herhaalde query van een gebruiker.
- Om resultaten op te slaan die zijn geretourneerd voor uw proactieve query, gepersonaliseerd in afwachting van de behoeften van de gebruiker, op basis van de signalen van die gebruiker. U deze resultaten opslaan voor de laagste (i) 24 uur vanaf het moment van de query, of (ii) totdat een gebruiker een andere query indient voor bijgewerkte resultaten.

Wanneer behouden, kunnen de resultaten voor een specifieke gebruiker niet worden doorgesluisd met resultaten voor een andere gebruiker. Dat wil zeggen, de resultaten van elke gebruiker moeten apart worden bewaard en geleverd.

### <a name="general"></a>Algemeen 

Voor alle presentatie van bewaarde resultaten:

- Voeg een duidelijke, zichtbare melding toe van de tijd dat de query is verzonden.
- Presenteer de gebruiker met een knop of iets dergelijks om opnieuw op te vragen en bijgewerkte resultaten te verkrijgen. 
- Bewaar de Bing-branding bij de presentatie van de resultaten.
- Verwijder (en vernieuw indien nodig met een nieuwe query) de opgeslagen resultaten binnen de opgegeven termijnen.

### <a name="non-display-url-discovery"></a>URL-detectie niet weergeven 

U mag alleen zoekreacties gebruiken in een niet-internetzoekervaring met als enige doel URL's van informatiebronnen te ontdekken die reageren op een zoekopdracht van uw gebruiker of klant. U dergelijke URL's kopiëren in een rapport of een soortgelijk antwoord dat u geeft:

- Alleen aan die gebruiker of klant, als reactie op die vraag.
- Alleen als deze belangrijke extra waardevolle inhoud bevat, die relevant is voor de query.

De vorige secties van gebruiks- en weergavevereisten voor zoekopdrachten zijn niet van toepassing op dit niet-weergavegebruik, behalve voor: 

- Bewaar geen gegevens of inhoud van of afgeleid van het zoekantwoord, anders dan het beperkte URL-kopiëren dat eerder is beschreven, niet in de cache, kopieer of deze op slaat.
- Zorg ervoor dat uw gebruik van gegevens (inclusief de URL's) die van de zoek-API's zijn ontvangen, niet in strijd is met toepasselijke wetten of rechten van derden.
- Gebruik de gegevens (inclusief de URL's) die van de zoek-API's zijn ontvangen niet als onderdeel van een zoekindex of machine learning of vergelijkbare algoritmische activiteit. Gebruik deze gegevens niet om trein- of evaluatie- of services te maken die u of derden kunnen aanbieden.

## <a name="gdpr-compliance"></a>AVG-naleving  

Met betrekking tot persoonsgegevens die onder de Algemene Verordening Gegevensbescherming van de Europese Unie (AVG) vallen en die worden verwerkt in verband met oproepen naar de Search API's, Bing Spell Check API of Bing Autosuggest API, begrijpt u dat u en Microsoft onafhankelijke verwerkingsverantwoordelijken onder de AVG. U bent onafhankelijk verantwoordelijk voor uw naleving van de AVG.  

