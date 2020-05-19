---
title: Reportingive Translatoring Framework (COLLABORATIVE)-conversie
titleSuffix: Azure Cognitive Services
description: COLLABORATIVE-rapportage (Collaborative Translatoring Framework) gebruiken.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 1bf6fefbe7d2ea3fccc393f4445fceec44ed4117
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584668"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>CTF-rapportage (Collaborative Translation Framework) gebruiken

> [!NOTE]
> Deze methode is afgeschaft. Het is niet beschikbaar in V 3.0 van Translator.
> 
> Het Framework voor gezamenlijke vertalingen (COLLABORATIVE), dat voorheen beschikbaar was voor V 2.0 of Translator, werd vanaf 1 februari 2018 afgeschaft. Met de functies AddTranslation en AddTranslationArray kunnen gebruikers correcties inschakelen via het Framework voor samen werking. Na 31 januari 2018 hebben deze twee functies geen nieuwe zin-inzendingen geaccepteerd en ontvangen gebruikers een fout bericht. Deze functies zijn buiten gebruik gesteld en worden niet vervangen.

De rapportage-API voor samen werking van het samenwerkings raamwerk (COLLABORATIVE) retourneert statistieken en de werkelijke inhoud in de COLLABORATIVE-opslag. Deze API wijkt af van de methode GetTranslations (), omdat:
* Retourneert de vertaalde inhoud en het totale aantal van uw account (appId of Azure Marketplace-account).
* Retourneert de vertaalde inhoud en het totale aantal, zonder dat er een overeenkomst van de bron zin vereist is.
* Retourneert niet de automatische vertaling (machine vertaling).

## <a name="endpoint"></a>Eindpunt
Het eind punt van de COLLABORATIVE-rapportage-API is https://api.microsofttranslator.com/v2/beta/ctfreporting.svc .

## <a name="methods"></a>Methoden
| Naam | Beschrijving|
|:---|:---|
| Methode GetUserTranslationCounts | Aantal vertalingen ophalen dat door de gebruiker is gemaakt. |
| Methode GetUserTranslations | Hiermee worden de vertalingen opgehaald die door de gebruiker zijn gemaakt. |

Met deze methoden kunt u het volgende doen:
* Haal de volledige set van gebruikers vertalingen en correcties op onder uw account-ID om deze te downloaden.
* Haal de lijst met veelgebruikte mede werkers op. Zorg ervoor dat de juiste gebruikers naam is opgenomen in AddTranslation ().
* Bouw een gebruikers interface (UI) waarmee uw vertrouwde gebruikers alle beschik bare kandidaten kunnen zien, indien nodig beperkt tot een deel van uw site, op basis van het voor voegsel van de URI.

> [!NOTE]
> Beide methoden zijn relatief langzaam en duur. Het is raadzaam om deze spaarzaam te gebruiken.

## <a name="getusertranslationcounts-method"></a>Methode GetUserTranslationCounts

Met deze methode wordt het aantal vertalingen opgehaald dat door de gebruiker is gemaakt. Hiermee wordt de lijst met Vertaal aantallen gegroepeerd op basis van de aanvraag parameters uriPrefix, from, to, User, minRating en maxRating.

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
| appId | **Vereist** Als de autorisatie-header wordt gebruikt, laat u het veld AppID leeg een teken reeks opgeven met ' Bearer ' + ' "+ toegangs token.|
| uriPrefix | **Optioneel** Een teken reeks met het voor voegsel van de URI van de vertaling.|
| from | **Optioneel** Een teken reeks die de taal code van de tekst van de vertaling aangeeft. |
| tot | **Optioneel** Een teken reeks die de taal code vertegenwoordigt voor het vertalen van de tekst in.|
| minRating| **Optioneel** Een integer-waarde voor de minimale kwaliteits classificatie voor de vertaalde tekst. De geldige waarde is tussen-10 en 10. De standaardwaarde is 1.|
| maxRating| **Optioneel** Een integer-waarde voor de maximale kwaliteits classificatie voor de vertaalde tekst. De geldige waarde is tussen-10 en 10. De standaardwaarde is 1.|
| gebruiker | **Optioneel** Een teken reeks die wordt gebruikt om het resultaat te filteren op basis van de afzender van de verzen ding. |
| category| **Optioneel** Een teken reeks met de categorie of het domein van de vertaling. Deze para meter ondersteunt alleen de standaard optie algemeen.|
| minDateUtc| **Optioneel** De datum vanaf wanneer u de vertalingen wilt ophalen. De datum moet de UTC-notatie hebben. |
| maxDateUtc| **Optioneel** De datum totdat u de vertalingen wilt ophalen. De datum moet de UTC-notatie hebben. |
| skip| **Optioneel** Het aantal resultaten dat u wilt overs laan op een pagina. Als u bijvoorbeeld de eerste twintig rijen van de resultaten wilt overs Laan en wilt weer geven uit de 21e resultaten record, geeft u 20 op voor deze para meter. De standaard waarde voor deze para meter is 0.|
| Houd | **Optioneel** Het aantal resultaten dat u wilt ophalen. Het maximum aantal van elke aanvraag is 100. De standaardwaarde is 100.|

> [!NOTE]
> De para meters overs Laan en aanvraag volgen inschakelen paginering voor een groot aantal resultaat records.

**Retour waarde**

De resultatenset bevat de matrix van de **UserTranslationCount**. Elke UserTranslationCount heeft de volgende elementen:

| Veld | Beschrijving |
|:---|:---|
| Count| Het aantal resultaten dat wordt opgehaald|
| Van | De bron taal|
| Waardering| De classificatie die door de indiener wordt toegepast in de aanroep van de methode AddTranslation ()|
| Handeling| De doel taal|
| URI| De URI die wordt toegepast in de aanroep van de methode AddTranslation ()|
| Gebruiker| De gebruikers naam|

**Uitzonderingen**

| Uitzondering | Bericht | Voorwaarden |
|:---|:---|:---|
| ArgumentOutOfRangeException | De para meter '**maxDateUtc**' moet groter zijn dan of gelijk zijn aan '**minDateUtc**'.| De waarde van de para meter **maxDateUtc** is kleiner dan de waarde van de para meter **minDateUtc**.|
| TranslateApiException | Het IP-adres overschrijdt het quotum.| <ul><li>De limiet voor het aantal aanvragen per minuut is bereikt.</li><li>De aanvraag grootte blijft beperkt tot 10000 tekens.</li><li>Een uur en een dagelijks quotum beperken het aantal tekens dat door de vertaler wordt geaccepteerd.</li></ul>|
| TranslateApiException | AppId overschrijdt het quotum.| De toepassings-ID heeft het uurtarief of het dagelijkse quotum overschreden.|

> [!NOTE]
> Het quotum wordt aangepast om ervoor te zorgen dat verdeling over alle gebruikers van de service.

**Code voorbeelden weer geven op GitHib**
* [G #](https://github.com/MicrosoftTranslator/CustomTranslator-API-CSharp)
* [PHP](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-PHP)

## <a name="getusertranslations-method"></a>Methode GetUserTranslations

Met deze methode worden de vertalingen opgehaald die door de gebruiker zijn gemaakt. Het biedt de vertalingen gegroepeerd op basis van de aanvraag parameters uriPrefix, from, to, User en minRating en maxRating.

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
| appId | **Vereist** Als de autorisatie-header wordt gebruikt, laat u het veld AppID leeg een teken reeks opgeven met ' Bearer ' + ' "+ toegangs token.|
| uriPrefix| **Optioneel** Een teken reeks met het voor voegsel van de URI van de vertaling.|
| from| **Optioneel** Een teken reeks die de taal code van de tekst van de vertaling aangeeft.|
| tot| **Optioneel** Een teken reeks die de taal code vertegenwoordigt voor het vertalen van de tekst in.|
| minRating| **Optioneel** Een integer-waarde voor de minimale kwaliteits classificatie voor de vertaalde tekst. De geldige waarde is tussen-10 en 10. De standaardwaarde is 1.|
| maxRating| **Optioneel** Een integer-waarde voor de maximale kwaliteits classificatie voor de vertaalde tekst. De geldige waarde is tussen-10 en 10. De standaardwaarde is 1.|
| gebruiker| **Beschrijving. Een teken reeks die wordt gebruikt om het resultaat te filteren op basis van de afzender van de verzen ding**|
| category| **Optioneel** Een teken reeks met de categorie of het domein van de vertaling. Deze para meter ondersteunt alleen de standaard optie algemeen.|
| minDateUtc| **Optioneel** De datum vanaf wanneer u de vertalingen wilt ophalen. De datum moet de UTC-notatie hebben.|
| maxDateUtc| **Optioneel** De datum totdat u de vertalingen wilt ophalen. De datum moet de UTC-notatie hebben.|
| skip| **Optioneel** Het aantal resultaten dat u wilt overs laan op een pagina. Als u bijvoorbeeld de eerste twintig rijen van de resultaten wilt overs Laan en wilt weer geven uit de 21e resultaten record, geeft u 20 op voor deze para meter. De standaard waarde voor deze para meter is 0.|
| Houd| **Optioneel** Het aantal resultaten dat u wilt ophalen. Het maximum aantal van elke aanvraag is 100. De standaardwaarde is 50.|

> [!NOTE]
> De para meters overs Laan en aanvraag volgen inschakelen paginering voor een groot aantal resultaat records.

**Retour waarde**

De resultatenset bevat de matrix van de **UserTranslation**. Elke UserTranslation heeft de volgende elementen:

| Veld | Beschrijving |
|:---|:---|
| CreatedDateUtc| De aanmaak datum van de vermelding met behulp van AddTranslation ()|
| Van| De bron taal|
| OriginalText| De tekst van de bron taal die wordt gebruikt bij het verzenden van de aanvraag|
|Waardering |De classificatie die door de indiener wordt toegepast in de aanroep van de methode AddTranslation ()|
|Handeling|    De doel taal|
|TranslatedText|    De vertaling als verzonden in de aanroep van de methode AddTranslation ()|
|URI|   De URI die wordt toegepast in de aanroep van de methode AddTranslation ()|
|Gebruiker   |De gebruikers naam|

**Uitzonderingen**

| Uitzondering | Bericht | Voorwaarden |
|:---|:---|:---|
| ArgumentOutOfRangeException | De para meter '**maxDateUtc**' moet groter zijn dan of gelijk zijn aan '**minDateUtc**'.| De waarde van de para meter **maxDateUtc** is kleiner dan de waarde van de para meter **minDateUtc**.|
| TranslateApiException | Het IP-adres overschrijdt het quotum.| <ul><li>De limiet voor het aantal aanvragen per minuut is bereikt.</li><li>De aanvraag grootte blijft beperkt tot 10000 tekens.</li><li>Een uur en een dagelijks quotum beperken het aantal tekens dat door de vertaler wordt geaccepteerd.</li></ul>|
| TranslateApiException | AppId overschrijdt het quotum.| De toepassings-ID heeft het uurtarief of het dagelijkse quotum overschreden.|

> [!NOTE]
> Het quotum wordt aangepast om ervoor te zorgen dat verdeling over alle gebruikers van de service.

**Code voorbeelden weer geven op GitHib**
* [G #](https://github.com/MicrosoftTranslator/CustomTranslator-API-CSharp)
* [PHP](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-PHP)
