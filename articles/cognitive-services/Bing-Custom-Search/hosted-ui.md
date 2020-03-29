---
title: Een gehoste gebruikersinterface configureren voor aangepaste zoekopdrachten van Bing | Microsoft Documenten
titleSuffix: Azure Cognitive Services
description: Gebruik dit artikel om een gehoste gebruikersinterface voor bing Custom Search te configureren en te integreren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: 2cc89bf57167db75404c044f58d18ab48edfaf38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854072"
---
# <a name="configure-your-hosted-ui-experience"></a>Gehoste UI-ervaring configureren

Bing Custom Search biedt een gehoste gebruikersinterface die u eenvoudig integreren in uw webpagina's en webtoepassingen als een JavaScript-codefragment. Met de portal Aangepast zoeken bing u de indeling, kleur en zoekopties van de gebruikersinterface configureren.



## <a name="configure-the-custom-hosted-ui"></a>De aangepaste gehoste gebruikersinterface configureren

Voer de volgende stappen uit om een gehoste gebruikersinterface voor uw webtoepassingen te configureren. Terwijl u wijzigingen aanbrengt, geeft het deelvenster aan de rechterkant u een voorbeeld van uw gebruikersinterface. De weergegeven zoekresultaten zijn geen werkelijke resultaten voor uw instantie.

1. Meld u aan bij de portal Aangepast [zoeken](https://customsearch.ai)van Bing .  
  
2. Selecteer de instantie Aangepast zoeken bij Bing.

3. Klik op het tabblad **Hosted UI**.  
  
4. Selecteer een lay-out.

    |  |  |
    |---------|---------|
    |Zoekbalk en resultaten (standaard)    | Hiermee wordt een zoekvak met zoekresultaten eronder weergegeven.         |
    |Alleen resultaten     | Hiermee worden alleen zoekresultaten weergegeven zonder zoekvak. Wanneer u deze indeling gebruikt, moet`&q=<query string>`u de zoekopdracht () opgeven. Voeg de queryparameter toe aan de aanvraag-URL in het JavaScript-fragment of de HTML-eindpuntkoppeling.        |
    |Pop-over     | Biedt een zoekvak en geeft de zoekresultaten weer in een schuifoverlay.        |
    
5. Selecteer een kleurenthema. U de kleuren aanpassen aan uw toepassing door op **Thema aanpassen .** Als u een kleur wilt wijzigen, voert u de `#366eb8`RGB HEX-waarde van de kleur in (bijvoorbeeld) of klikt u op het kleurenvoorbeeld.

   U een voorbeeld van uw wijzigingen bekijken aan de rechterkant van de portal. Als u **op Opnieuw instellen naar standaard** klikt, worden de wijzigingen teruggezet naar de standaardkleuren voor het geselecteerde thema.

   > [!NOTE]
   > Houd rekening met toegankelijkheid bij het kiezen van kleuren.

6. Geef **onder Aanvullende configuraties**waarden op die geschikt zijn voor uw app. Deze instellingen zijn optioneel. Zie het voorbeeldvenster aan de rechterkant om het effect van het toepassen of verwijderen ervan te zien. Beschikbare configuratieopties zijn:  

7. Voer de zoekabonnementssleutel in of kies er een in de vervolgkeuzelijst. De vervolgkeuzelijst wordt gevuld met sleutels uit de abonnementen van uw Azure-account. Zie [API-account voor Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

8. Als u automatisch voorstellen hebt ingeschakeld, voert u de abonnementssleutel automatisch voor stellen of kiest u er een in de vervolgkeuzelijst. De vervolgkeuzelijst wordt gevuld met sleutels uit de abonnementen van uw Azure-account. Aangepaste Autosuggest vereist een specifieke abonnementslaag, zie de [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Aangepaste gebruikersinterface consumeren

Als u de gehoste gebruikersinterface wilt gebruiken, gaat u als gevolg van: 

- Het script opnemen op uw webpagina  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- Of gebruik de volgende URL in een webbrowser.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Voeg indien nodig de volgende queryparameters toe aan de URL. Zie [Aangepaste API-verwijzing voor zoekopdrachten](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) voor informatie over deze parameters.
  >
  > - q
  > - mkt
  > - Safesearch
  > - setlang

  > [!IMPORTANT]
  > De pagina kan uw privacyverklaring of andere mededelingen en voorwaarden niet weergeven. De geschiktheid voor uw gebruik kan variëren.  

Ga voor meer informatie, waaronder uw aangepaste configuratie-id, naar **Eindpunten** onder het tabblad **Productie.**

## <a name="configuration-options"></a>Configuratie-opties

U het gedrag van uw gehoste gebruikersinterface configureren door op **Extra configuraties**te klikken en waarden op te geven. Deze instellingen zijn optioneel. Zie het voorbeeldvenster aan de rechterkant om het effect van het toepassen of verwijderen ervan te zien. 

### <a name="web-search-configurations"></a>Webzoekconfiguraties

|  |  |
|---------|---------|
|Webresultaten ingeschakeld    | Hiermee bepaalt u of zoeken op het web is ingeschakeld (u ziet een tabblad Web boven aan de pagina)        |
|Automatisch voorstellen inschakelen     | Hiermee bepaalt u of aangepaste automatisch voorstellen is ingeschakeld (zie [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) voor extra kosten).        |
|Webresultaten per pagina    | Aantal zoekresultaten op het web dat tegelijk moet worden weergegeven (het maximum is 50 resultaten per pagina).        |
|Afbeeldingsbijschrift   | Hiermee bepaalt u of afbeeldingen worden weergegeven met zoekresultaten.|


De volgende configuraties worden weergegeven als u op **Geavanceerde configuraties weergeven**klikt:


|  | |
|---------|---------|
|Woorden markeren     | Hiermee bepaalt u of resultaten worden weergegeven met alle seintermen.         |
|Koppelingsdoel    |  Hiermee bepaalt u of de webpagina wordt geopend op een nieuw browsertabblad (Leeg) of op hetzelfde browsertabblad (zelf) wanneer de gebruiker op een zoekresultaat klikt.        |

### <a name="image-search-configurations"></a>Afbeeldingszoekconfiguraties

| | |
|---------|---------|
|Afbeeldingsresultaten ingeschakeld     | Hiermee bepaalt u of zoeken naar afbeeldingen is ingeschakeld (u ziet een tabblad Afbeeldingen boven aan de pagina).            |
|Afbeeldingsresultaten per pagina     | Aantal zoekresultaten van afbeeldingen dat tegelijk moet worden weergegeven (het maximum is 150 resultaten per pagina).          |

De volgende configuratie wordt weergegeven als u op **Geavanceerde configuraties weergeven**klikt.  
  
| | |
|---------|---------|
| Filters inschakelen     | Hiermee voegt u filters toe die de gebruiker kan gebruiken om de afbeeldingen te filteren die Bing retourneert. De gebruiker kan bijvoorbeeld de resultaten filteren op alleen geanimeerde GIF's.|

### <a name="video-search-configurations"></a>Videozoekconfiguraties

|  | |
|---------|---------|
|Videoresultaten ingeschakeld     | Hiermee bepaalt u of het zoeken naar video is ingeschakeld (u ziet een tabblad Video's boven aan de pagina).           |
|Videoresultaten per pagina   | Aantal videozoekresultaten dat tegelijk moet worden weergegeven (het maximum is 150 resultaten per pagina).        |

De volgende configuratie wordt weergegeven als u op **Geavanceerde configuraties weergeven**klikt.  
  
|  | |
|---------|---------|
|Filters inschakelen    | Hiermee voegt u filters toe die de gebruiker kan gebruiken om de video's te filteren die Bing retourneert. De gebruiker kan bijvoorbeeld de resultaten filteren voor video's met een specifieke resolutie of video's die in de afgelopen 24 uur zijn ontdekt.          |

### <a name="miscellaneous-configurations"></a>Diverse configuraties


| |  |
|---------|---------|
|Paginatitel   | Tekst die wordt weergegeven in het titelgebied van de pagina met zoekresultaten (niet voor een pop-over-indeling).        |
|Werkbalkthema    | Hiermee bepaalt u de achtergrondkleur van het titelgebied van de pagina met zoekresultaten. |

De volgende configuraties worden weergegeven als u op **Geavanceerde configuraties weergeven**klikt.  

|Kolom1  |Kolom2  |
|---------|---------|
|Tijdelijke aanduiding voor tekstvan het zoekvak   | Tekst die vóór invoer in het zoekvak wordt weergegeven.        |
|Url van titelkoppeling    |Doel voor de titelkoppeling.         |
|Logo URL     | Afbeelding die naast de titel wordt weergegeven.         |
|Favicon    | Pictogram weergegeven in de titelbalk van de browser.          |

De volgende configuraties zijn alleen van toepassing als u de gehoste gebruikersinterface gebruikt via het HTML-eindpunt (deze zijn niet van toepassing als u het JavaScript-fragment gebruikt).

- Paginatitel
- Werkbalkthema
- URL van titelkoppeling
- Logo URL
- Faviicon URL  

## <a name="next-steps"></a>Volgende stappen

- [Decoratiemarkeringen gebruiken om tekst te markeren](../bing-web-search/hit-highlighting.md)
- [Bladeren door webpagina's](./page-webpages.md)
