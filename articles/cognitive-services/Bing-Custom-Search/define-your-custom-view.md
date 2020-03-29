---
title: Uw bing-zoekervaring configureren | Microsoft Documenten
titleSuffix: Azure Cognitive Services
description: Met de portal u een zoekinstantie maken die de segmenten van het web opgeeft. domeinen, subpagina's en webpagina's.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: c14376cc80373371ec5fcb8f22a00584a6b2f714
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220213"
---
# <a name="configure-your-bing-custom-search-experience"></a>Uw Bing Custom Search-ervaring configureren

Met een instantie Aangepast zoeken u de zoekervaring aanpassen om alleen inhoud op te nemen van websites waar uw gebruikers om geven. In plaats van een zoekopdracht in het hele web uit te voeren, zoekt Bing alleen naar de segmenten van het web die u interesseren. Als u een aangepaste weergave van het web wilt maken, gebruikt u de [portal](https://customsearch.ai) van Bing Aangepaste zoekopdrachten.

Met de portal u een zoekinstantie maken die de segmenten van het web opgeeft: domeinen, subpagina's en webpagina's, die u wilt dat Bing zoekt en de segmenten die u niet wilt dat deze zoekt. De portal kan ook inhoud voorstellen die u mogelijk wilt opnemen.

Gebruik het volgende bij het definiëren van uw websegmenten:

| Segmentnaam | Beschrijving                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domain     | Een domeinsegment bevat alle inhoud die binnen een internetdomein wordt gevonden. Bijvoorbeeld `www.microsoft.com`. Als u `www.` het weglaat, wordt Bing ook door de subdomeinen van het domein gezocht. Als u bijvoorbeeld `microsoft.com`opgeeft, retourneert Bing ook resultaten van `support.microsoft.com` of `technet.microsoft.com`. |
| Subpagina    | Een subpaginasegment bevat alle inhoud in de subpagina en paden eronder. U maximaal twee subpagina's in het pad opgeven. Bijvoorbeeld: `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Webpagina    | Een webpaginasegment kan alleen die webpagina bevatten in een aangepaste zoekopdracht. U optioneel opgeven of subpagina's moeten worden opgenomen.                                                                                                                                                                                  |

> [!IMPORTANT]
> Alle domeinen, subpagina's en webpagina's die u opgeeft, moeten openbaar zijn en door Bing worden geïndexeerd. Als u eigenaar bent van een openbare site die u in de zoekopdracht wilt opnemen en Bing deze niet heeft geïndexeerd, raadpleegt u de documentatie van de [Bing-webmaster](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) voor meer informatie over het indexeren van Bing. Zie ook de webmasterdocumentatie voor meer informatie over het feit dat Bing uw gecrawlde site bijwerkt als de index verouderd is.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Segmenten van het web toevoegen aan uw aangepaste zoekinstantie

Wanneer u uw aangepaste zoekinstantie maakt, u de segmenten van het web opgeven: domeinen, subpagina's en webpagina's die u wilt hebben opgenomen of geblokkeerd uit uw zoekresultaten. 

Als u de segmenten weet die u wilt opnemen in uw aangepaste zoekinstantie, voegt u deze toe aan de actieve lijst **van** uw instantie. 

Als u niet zeker weet welke segmenten u wilt opnemen, u zoekopdrachten naar Bing verzenden in het deelvenster **Voorbeeld** en de gewenste segmenten selecteren. Om dit te doen: 

1. selecteer 'Bing' in de vervolgkeuzelijst in het deelvenster Voorbeeld en voer een zoekopdracht in

2. Klik **op Site toevoegen** naast het resultaat dat u wilt opnemen. Klik vervolgens op OK.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Uw zoekervaring aanpassen met actieve en geblokkeerde lijsten 

U hebt toegang tot de lijst met actieve en geblokkeerde segmenten door te klikken op de tabbladen **Actief** en **Geblokkeerd** in uw aangepaste zoekinstantie. Segmenten die aan de actieve lijst zijn toegevoegd, worden opgenomen in uw aangepaste zoekopdracht. Geblokkeerde segmenten worden niet doorzocht en worden niet weergegeven in uw zoekresultaten.

Als u de segmenten van het web wilt opgeven waarop Bing moet zoeken, klikt u op het tabblad **Actief** en voegt u een of meer URL's toe. Als u URL's wilt bewerken of verwijderen, gebruikt u de opties onder de kolom **Besturingselementen.** 

Wanneer u URL's toevoegt aan de **actieve** lijst, u afzonderlijke URL's of meerdere URL's tegelijk toevoegen door een tekstbestand te uploaden met het pictogram uploaden.

![Het tabblad Normaal zoeken in Bing](media/file-upload-icon.png)

Als u een bestand wilt uploaden, maakt u een tekstbestand en geeft u één domein, subpagina of webpagina per regel op. Uw bestand wordt geweigerd als het niet correct is opgemaakt.

> [!NOTE]
> * U een bestand alleen uploaden naar de **actieve** lijst. U het niet gebruiken om segmenten toe te voegen aan de **lijst Geblokkeerd.**  
> * Als de **geblokkeerde** lijst een domein, subpagina of webpagina bevat die u in het uploadbestand hebt opgegeven, wordt deze verwijderd uit de **lijst Geblokkeerd** en toegevoegd aan de **active-lijst.**
> * Dubbele vermeldingen in uw uploadbestand worden genegeerd door Bing Custom Search. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Websitesuggesties ontvangen voor uw zoekervaring

Nadat u websegmenten aan de **actieve** lijst hebt toegevoegd, genereert de portal Voor aangepast zoeken bing onder aan het tabblad suggesties voor website en subpagina. Dit zijn segmenten waarvan Bing Custom Search denkt dat u deze wilt opnemen. Klik **op Vernieuwen** om bijgewerkte suggesties te ontvangen nadat de instellingen van uw aangepaste zoekinstantie zijn bijgewerkt. Deze sectie is alleen zichtbaar als er suggesties beschikbaar zijn.

## <a name="search-for-images-and-videos"></a>Zoeken naar afbeeldingen en video's

U op dezelfde manier naar afbeeldingen en video's zoeken als webinhoud met behulp van de [Bing Custom Image Search API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) of de Bing Custom Video Search [API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference). U deze resultaten weergeven met de [gehoste gebruikersinterface](hosted-ui.md)of de API's. 

Deze API's zijn vergelijkbaar met de niet-aangepaste [Bing Image Search](../Bing-Image-Search/overview.md) en Bing Video [Search](../Bing-Video-Search/search-the-web.md) API's, maar zoeken op het hele web, en vereisen niet de `customConfig` query parameter. Zie deze documentatiesets voor meer informatie over het werken met afbeeldingen en video's. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Uw zoekinstantie testen met het deelvenster Voorbeeld

U uw zoekexemplaar testen met het voorbeeldvenster aan de rechterkant van de portal om zoekopdrachten in te dienen en de resultaten te bekijken. 

1. Selecteer onder het zoekvak **Mijn instantie**. U de resultaten van uw zoekervaring vergelijken met Bing door **Bing**te selecteren. 
2. Selecteer een veilig zoekfilter en welke markt u wilt zoeken (zie [Queryparameters](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)).
3. Voer een query in en druk op Enter of klik op het zoekpictogram om de resultaten van de huidige configuratie weer te geven. U uw zoektype wijzigen dat u uitvoert door op **Web,** **Afbeelding**of **Video** te klikken om overeenkomstige resultaten te krijgen. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>De rang van specifieke zoekresultaten aanpassen

Met de portal u de zoekrangschikking van inhoud van specifieke domeinen, subpagina's en webpagina's aanpassen. Nadat u een zoekopdracht in het voorbeeldvenster hebt verzonden, bevat elk zoekresultaat een lijst met aanpassingen die u hiervoor maken:  

|            |                                                                                                                                                                      |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Blokkeren      | Hiermee verplaatst u het domein, de subpagina of de webpagina naar de lijst Geblokkeerd. Bing sluit inhoud van de geselecteerde site uit van het verschijnen in de zoekresultaten.                    |
| Boost      | Verhoogt inhoud uit het domein of subpagina om hoger te zijn in de zoekresultaten.                                                                                        |
| Degraderen     | Degradeert inhoud uit het domein of subpagina lager in de zoekresultaten. U selecteert of u inhoud wilt degraderen uit het domein of de subpagina waartoe de webpagina behoort. |
| Vastmaken aan boven | Hiermee verplaatst u het domein, de subpagina of de webpagina naar de **lijst vastgemaakte** lijst. Hierdoor wordt de webpagina weergegeven als het hoogste zoekresultaat voor een bepaalde zoekopdracht.                   |

Het aanpassen van de rang is niet beschikbaar voor zoekopdrachten naar afbeeldingen of video's.

### <a name="boosting-and-demoting-search-results"></a>Zoekresultaten stimuleren en degraderen

Je een domein of subpagina in de **actieve** lijst superboost, boostof degraderen. Standaard worden alle segmenten toegevoegd zonder classificatieaanpassingen. Segmenten van het web die zijn super versterkt of versterkt worden hoger gerangschikt in de zoekresultaten (met super boost ranking hoger dan boost). Items die worden gedegradeerd, worden lager gerangschikt in de zoekresultaten.

U items superboost, boost of degradeeren met de **besturingselementen Rangschikking aanpassen** in de **actieve** lijst of met de besturingselementen Boost en Dedemote in het deelvenster Voorbeeld. De service voegt het segment toe aan uw Actieve lijst en past de rangschikking dienovereenkomstig aan.

> [!NOTE] 
> Het stimuleren en degraderen van domeinen en subpagina's is een van de vele methoden die Bing Custom Search gebruikt om de volgorde van zoekresultaten te bepalen. Vanwege andere factoren die de rangschikking van verschillende webinhoud beïnvloeden, kunnen de effecten van het aanpassen van de rang variëren. Gebruik het deelvenster Voorbeeld om de effecten van het aanpassen van de rang van uw zoekresultaten te testen. 

Super boost, boost en degraderen zijn niet beschikbaar voor het zoeken naar afbeeldingen en video's.

## <a name="pin-slices-to-the-top-of-search-results"></a>Segmenten vastmaken aan de bovenkant van de zoekresultaten

Met de portal u url's ook boven aan de zoekresultaten vastmaken voor specifieke zoektermen, met behulp van het tabblad **Vastgemaakt.** Voer een URL en query in om de webpagina op te geven die als het hoogste resultaat wordt weergegeven. Houd er rekening mee dat u maximaal één webpagina per zoekopdracht vastmaken en dat alleen geïndexeerde webpagina's worden weergegeven in zoekopdrachten. Het vastmaken van resultaten is niet beschikbaar voor zoekopdrachten naar afbeeldingen of video's.

U een webpagina op twee manieren aan de bovenkant vastmaken:

* Voer op het tabblad **Vastgemaakt** de URL van de webpagina in die aan de bovenkant en de bijbehorende query moet worden vastgemaakt.

* Voer **in** het deelvenster Voorbeeld een zoekopdracht in en klik op zoeken. Zoek de webpagina die u wilt vastmaken voor uw query en klik op **Vastmaken aan bovenkant**. de webpagina en query worden toegevoegd aan de **lijst met vastgemaakte** gegevens.

### <a name="specify-the-pins-match-condition"></a>De overeenkomende voorwaarde van de pin opgeven

Webpagina's worden standaard alleen boven aan de zoekresultaten vastgemaakt wanneer de querytekenreeks van een gebruiker exact overeenkomt met een tekenreeks die in de **vastgemaakte** lijst wordt weergegeven. U dit gedrag wijzigen door een van de volgende wedstrijdvoorwaarden op te geven:

> [!NOTE]
> Alle vergelijkingen tussen de zoekopdracht van de gebruiker en de zoekopdracht van de pin zijn ongevoelig.

| Waarde | Beschrijving                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Begint met | De pin komt overeen als de querytekenreeks van de gebruiker begint met de querytekenreeks van de pin |
| Eindigt met   | De pin komt overeen als de querytekenreeks van de gebruiker eindigt met de querytekenreeks van de pin.  |
| Contains    | De pin komt overeen als de querytekenreeks van de gebruiker de querytekenreeks van de pin bevat.   |


Als u de wedstrijdconditie van de pin wilt wijzigen, klikt u op het pictogram bewerken van de pin. Klik in de kolom **Queryovereenkomst op** de vervolgkeuzelijst en selecteer de nieuwe voorwaarde die u wilt gebruiken. Klik vervolgens op het pictogram Opslaan om de wijziging op te slaan.

### <a name="change-the-order-of-your-pinned-sites"></a>De volgorde van uw vastgemaakte sites wijzigen

Als u de volgorde van uw pins wilt wijzigen, u ze slepen en neerzetten of hun bestelnummer bewerken door op het pictogram Bewerken in de kolom **Besturingselementen** van de **lijst Vastgemaakte** te klikken.

Als meerdere pins voldoen aan een wedstrijdvoorwaarde, gebruikt Bing Custom Search de hoogste in de lijst.

## <a name="view-statistics"></a>Statistieken bekijken

Als u zich op het juiste niveau hebt geabonneerd op Aangepast zoeken (zie de [prijspagina's),](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)wordt een tabblad **Statistieken** toegevoegd aan uw productie-instanties. Op het tabblad Statistieken vindt u meer informatie over de manier waarop uw aangepaste zoekeindpunten worden gebruikt, waaronder oproepvolume, topquery's, geografische distributie, antwoordcodes en veilig zoeken. U details filteren met behulp van de meegeleverde besturingselementen.

## <a name="usage-guidelines"></a>Gebruiksrichtlijnen

- Voor elke aangepaste zoekinstantie is het maximum aantal classificatieaanpassingen dat u aanbrengen in **actieve** en **geblokkeerde** segmenten beperkt tot 400.
- Het toevoegen van een segment aan de tabbladen Actief of Geblokkeerd telt als één rangschikkingsaanpassing.
- Het stimuleren en degraderen tellen als twee ranking aanpassingen.
- Voor elke aangepaste zoekinstantie is het maximum aantal pinnen dat u mag maken beperkt tot 200.

## <a name="next-steps"></a>Volgende stappen

- [Aangepaste zoekopdrachten aanroepen](./search-your-custom-view.md)
- [Gehoste UI-ervaring configureren](./hosted-ui.md)
- [Decoratiemarkeringen gebruiken om tekst te markeren](../bing-web-search/hit-highlighting.md)
- [Bladeren door webpagina's](./page-webpages.md)
