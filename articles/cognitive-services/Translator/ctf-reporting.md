---
title: Collaborative Translation Framework (CTF) Reporting - Translator Text API
titleSuffix: Azure Cognitive Services
description: Hoe te gebruiken Collaborative Translation Framework (CTF) rapportage.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 6a197095d97e67f7548e60375148cff57e47b797
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595937"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>CTF-rapportage (Collaborative Translation Framework) gebruiken

> [!NOTE]
> Deze methode wordt afgeschaft. Het is niet beschikbaar in V3.0 van de Translator Text API.
> 
> Het Collaborative Translations Framework (CTF), voorheen beschikbaar voor V2.0 van de Translator Text API, is per 1 februari 2018 afgeschaft. Met de functies AddTranslation en AddTranslationArray kunnen gebruikers correcties inschakelen via het Collaborative Translation Framework. Na 31 januari 2018 accepteerden deze twee functies geen nieuwe inzendingen voor zinnen en ontvangen gebruikers een foutmelding. Deze functies zijn buiten gebruik gesteld en zullen niet worden vervangen.

De Collaborative Translation Framework (CTF) Reporting API retourneert statistieken en de werkelijke inhoud in de CTF-winkel. Deze API verschilt van de methode GetTranslations(), omdat deze:
* Retourneert de vertaalde inhoud en het totale aantal alleen van uw account (appId- of Azure Marketplace-account).
* Geeft als resultaat de vertaalde inhoud en het totale aantal inhoud zonder dat de bronzin overeenkomt.
* Retourneert de automatische vertaling niet (machinevertaling).

## <a name="endpoint"></a>Eindpunt
Het eindpunt van de CTF Reporting API ishttps://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Methoden
| Name |    Beschrijving|
|:---|:---|
| GetUserTranslationCounts-methode | Het aantal vertalingen dat door de gebruiker is gemaakt, wordt opgemaakt. |
| GetUserTranslations-methode | Hiermee haalt u de vertalingen op die door de gebruiker zijn gemaakt. |

Met deze methoden u:
* Haal de volledige set gebruikersvertalingen en correcties op onder uw account-id om te downloaden.
* Verkrijg de lijst van de frequente bijdragers. Zorg ervoor dat de juiste gebruikersnaam is opgegeven in AddTranslation().
* Bouw een gebruikersinterface (UI) waarmee uw vertrouwde gebruikers alle beschikbare kandidaten kunnen zien, indien nodig beperkt tot een deel van uw site, op basis van het URI-voorvoegsel.

> [!NOTE]
> Beide methoden zijn relatief traag en duur. Het wordt aanbevolen om ze spaarzaam te gebruiken.

## <a name="getusertranslationcounts-method"></a>GetUserTranslationCounts-methode

Met deze methode wordt het aantal vertalingen dat door de gebruiker is gemaakt, aangenomen. Het bevat de lijst met vertaaltellingen gegroepeerd door het uriPrefix, van, naar, gebruiker, minRating en maxRating request parameters.

**Syntaxis**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslationCount[]GetUserTranslationCounts(
>            string appId,
>            string uriPrefix,
>            string from,
>            string to,
>            int? minRating,
>            int? maxRating,
>            string user,
>            string category
>            DateTime? minDateUtc,
>            DateTime? maxDateUtc,
>            int? skip,
>            int? take);
> ```

**Parameters**

| Parameter | Beschrijving |
|:---|:---|
| appId | **Vereist** Als de kopautorisatie wordt gebruikt, laat u het appid-veld leeg, anders geeft u een tekenreeks op met 'Drager' + + + toegangstoken.|
| uriVoorvoegsel | **Optioneel** Een tekenreeks met voorvoegsel van URI van de vertaling.|
| from | **Optioneel** Een tekenreeks die de taalcode van de vertaaltekst weergeeft. |
| tot | **Optioneel** Een tekenreeks die de taalcode weergeeft om de tekst naar te vertalen.|
| minRating minRating| **Optioneel** Een gehele waarde die de minimale kwaliteitsbeoordeling voor de vertaalde tekst vertegenwoordigt. De geldige waarde ligt tussen -10 en 10. De standaardwaarde is 1.|
| maxRating maxRating maxRating maxRating| **Optioneel** Een gehele waarde die de maximale kwaliteitsbeoordeling voor de vertaalde tekst vertegenwoordigt. De geldige waarde ligt tussen -10 en 10. De standaardwaarde is 1.|
| gebruiker | **Optioneel** Een tekenreeks die wordt gebruikt om het resultaat te filteren op basis van de afzender van de inzending. |
| category| **Optioneel** Een tekenreeks met de categorie of het domein van de vertaling. Deze parameter ondersteunt alleen de standaardoptie algemeen.|
| minDateUtc minDateUtc| **Optioneel** De datum vanaf wanneer u de vertalingen wilt ophalen. De datum moet in de UTC-indeling staan. |
| maxDateUtc| **Optioneel** De datum tot wanneer u de vertalingen wilt ophalen. De datum moet in de UTC-indeling staan. |
| skip| **Optioneel** Het aantal resultaten dat u op een pagina wilt overslaan. Als u bijvoorbeeld de eerste 20 rijen van de resultaten wilt overslaan en de weergave van de 21e resultatenrecord wilt weergeven, geeft u 20 voor deze parameter op. De standaardwaarde voor deze parameter is 0.|
| Nemen | **Optioneel** Het aantal resultaten dat u wilt ophalen. Het maximum aantal van elke aanvraag is 100. De standaardwaarde is 100.|

> [!NOTE]
> De parameters voor het overslaan en nemen van aanvragen maken paginatie mogelijk voor een groot aantal resultaatrecords.

**Retourwaarde**

De resultaatset bevat array van het **UserTranslationCount**. Elke UserTranslationCount heeft de volgende elementen:

| Veld | Beschrijving |
|:---|:---|
| Count| Het aantal resultaten dat wordt opgehaald|
| Van | De brontaal|
| Waardering| De beoordeling die door de indiener wordt toegepast in de methode AddTranslation()|
| Handeling| De doeltaal|
| Uri| De URI die is toegepast in de methode AddTranslation()|
| Gebruiker| De gebruikersnaam|

**Uitzonderingen**

| Uitzondering | Bericht | Voorwaarden |
|:---|:---|:---|
| ArgumentoutofrangeException | De parameter '**maxDateUtc**' moet groter zijn dan of gelijk zijn aan '**minDateUtc**'.| De waarde van de parameter **maxDateUtc** is kleiner dan de waarde van de parameter **minDateUtc**.|
| TranslateapiException | IP is meer dan het quotum.| <ul><li>De limiet voor het aantal aanvragen per minuut is bereikt.</li><li>De aanvraaggrootte blijft beperkt op 10000 tekens.</li><li>Een uur- en een dagquotum beperken het aantal tekens dat de Microsoft Translator API accepteert.</li></ul>|
| TranslateapiException | AppId is over het quotum.| De toepassings-ID overschreed het uur- of dagquotum.|

> [!NOTE]
> Het quotum wordt aangepast om de eerlijkheid van alle gebruikers van de service te waarborgen.

**Codevoorbeelden bekijken op GitHib**
* [C #](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [Php](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>GetUserTranslations-methode

Met deze methode worden de vertalingen opgehaald die door de gebruiker zijn gemaakt. Het biedt de vertalingen gegroepeerd door het uriPrefix, van, naar, gebruiker en minRating en maxRating aanvraag parameters.

**Syntaxis**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslation[] GetUserTranslations (
>             string appId,
>             string uriPrefix,
>             string from,
>             string to,
>             int? minRating,
>             int? maxRating,
>             string user,
>             string category
>             DateTime? minDateUtc,
>             DateTime? maxDateUtc,
>             int? skip,
>             int? take);
> ```

**Parameters**

| Parameter | Beschrijving |
|:---|:---|
| appId | **Vereist** Als de kopautorisatie wordt gebruikt, laat u het appid-veld leeg, anders geeft u een tekenreeks op met 'Drager' + + + toegangstoken.|
| uriVoorvoegsel| **Optioneel** Een tekenreeks met voorvoegsel van URI van de vertaling.|
| from| **Optioneel** Een tekenreeks die de taalcode van de vertaaltekst weergeeft.|
| tot| **Optioneel** Een tekenreeks die de taalcode weergeeft om de tekst naar te vertalen.|
| minRating minRating| **Optioneel** Een gehele waarde die de minimale kwaliteitsbeoordeling voor de vertaalde tekst vertegenwoordigt. De geldige waarde ligt tussen -10 en 10. De standaardwaarde is 1.|
| maxRating maxRating maxRating maxRating| **Optioneel** Een gehele waarde die de maximale kwaliteitsbeoordeling voor de vertaalde tekst vertegenwoordigt. De geldige waarde ligt tussen -10 en 10. De standaardwaarde is 1.|
| gebruiker| **Optionele. Een tekenreeks die wordt gebruikt om het resultaat te filteren op basis van de afzender van de indiening**|
| category| **Optioneel** Een tekenreeks met de categorie of het domein van de vertaling. Deze parameter ondersteunt alleen de standaardoptie algemeen.|
| minDateUtc minDateUtc| **Optioneel** De datum vanaf wanneer u de vertalingen wilt ophalen. De datum moet in de UTC-indeling staan.|
| maxDateUtc| **Optioneel** De datum tot wanneer u de vertalingen wilt ophalen. De datum moet in de UTC-indeling staan.|
| skip| **Optioneel** Het aantal resultaten dat u op een pagina wilt overslaan. Als u bijvoorbeeld de eerste 20 rijen van de resultaten wilt overslaan en de weergave van de 21e resultatenrecord wilt weergeven, geeft u 20 voor deze parameter op. De standaardwaarde voor deze parameter is 0.|
| Nemen| **Optioneel** Het aantal resultaten dat u wilt ophalen. Het maximum aantal van elke aanvraag is 100. De standaardinstelling is 50.|

> [!NOTE]
> De parameters voor het overslaan en nemen van aanvragen maken paginatie mogelijk voor een groot aantal resultaatrecords.

**Retourwaarde**

De resultaatset bevat array van de **UserTranslation**. Elke UserTranslation heeft de volgende elementen:

| Veld | Beschrijving |
|:---|:---|
| CreatedDateUtc| De aanmaakdatum van de vermelding met AddTranslation()|
| Van| De brontaal|
| OriginalText (origineletekst)| De brontaaltekst die wordt gebruikt bij het indienen van de aanvraag|
|Waardering |De beoordeling die door de indiener wordt toegepast in de methode AddTranslation()|
|Handeling|    De doeltaal|
|Vertaalde tekst|    De vertaling zoals ingediend in de methode AddTranslation() methode aanroepen|
|Uri|   De URI die is toegepast in de methode AddTranslation()|
|Gebruiker   |De gebruikersnaam|

**Uitzonderingen**

| Uitzondering | Bericht | Voorwaarden |
|:---|:---|:---|
| ArgumentoutofrangeException | De parameter '**maxDateUtc**' moet groter zijn dan of gelijk zijn aan '**minDateUtc**'.| De waarde van de parameter **maxDateUtc** is kleiner dan de waarde van de parameter **minDateUtc**.|
| TranslateapiException | IP is meer dan het quotum.| <ul><li>De limiet voor het aantal aanvragen per minuut is bereikt.</li><li>De aanvraaggrootte blijft beperkt op 10000 tekens.</li><li>Een uur- en een dagquotum beperken het aantal tekens dat de Microsoft Translator API accepteert.</li></ul>|
| TranslateapiException | AppId is over het quotum.| De toepassings-ID overschreed het uur- of dagquotum.|

> [!NOTE]
> Het quotum wordt aangepast om de eerlijkheid van alle gebruikers van de service te waarborgen.

**Codevoorbeelden bekijken op GitHib**
* [C #](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [Php](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
