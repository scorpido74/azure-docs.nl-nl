---
title: Formaat en bijsnijdafbeeldingsminiaturen wijzigen - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Sommige antwoorden van de Bing Search API's bevatten URL's tot miniatuurafbeeldingen die door Bing worden weergegeven, die u aanpassen en bijsnijden, en kunnen queryparameters bevatten.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 630b86f55a537d109c851cb585cfccc34d229f83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110640"
---
# <a name="resize-and-crop-thumbnail-images"></a>Miniatuurafbeeldingen wijzigen en bijsnijden

Sommige antwoorden van de Bing Search API's bevatten URL's tot miniatuurafbeeldingen die door Bing worden weergegeven, die u aanpassen en bijsnijden, en kunnen queryparameters bevatten. Bijvoorbeeld:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Als u een subset van deze miniaturen weergeeft, biedt u een optie om de resterende afbeeldingen weer te geven.

> [!NOTE]
> Zorg ervoor dat miniatuurafbeeldingen worden bijsnijden en het formaat van miniatuurafbeeldingen worden aangepast, zodat de rechten van derden worden aangepast, zoals vereist door de [gebruiks- en weergavevereisten van](use-display-requirements.md)Bing Search API.

## <a name="resize-a-thumbnail"></a>Het formaat van een miniatuur wijzigen 

Als u het formaat van een miniatuur `w` wilt wijzigen, `h` raadt Bing u aan slechts één van de queryparameters (breedte) of (hoogte) in de URL van de miniatuur op te geven. Als U alleen de hoogte of breedte opgeeft, behoudt Bing het oorspronkelijke aspect van de afbeelding. Geef de breedte en hoogte in pixels op. 

Als de oorspronkelijke miniatuur bijvoorbeeld 480x620 is:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

En u wilt de grootte `w` verkleinen, de parameter instellen `336`op een `h` nieuwe waarde (bijvoorbeeld) en de parameter verwijderen:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Als u alleen de hoogte of breedte van een miniatuur opgeeft, blijft de oorspronkelijke beeldverhouding behouden. Als u beide parameters opgeeft en de beeldverhouding niet wordt gehandhaafd, voegt Bing witte vulling toe aan de rand van de afbeelding.

Als u bijvoorbeeld het formaat van een afbeelding van 480x359 wijzigt tot 200x200 zonder bijsnijden, bevat de volledige breedte de afbeelding, maar de hoogte bevat 25 pixels witte vulling aan de boven- en onderkant van de afbeelding. Als de afbeelding 359x480 was, zouden de linker- en rechterranden witte vulling bevatten. Als u de afbeelding bijsnijdt, wordt er geen witte vulling toegevoegd.  

De volgende afbeelding toont de oorspronkelijke grootte van een miniatuurafbeelding (480x300).  
  
![Originele landschapsafbeelding](./media/resize-crop/bing-resize-crop-landscape.png)  
  
De volgende afbeelding toont de grootte van de afbeelding tot 200x200. De beeldverhouding blijft behouden en de boven- en onderkant zijn opgevuld met wit (de zwarte rand hier is inbegrepen om de vulling te tonen).  
  
![Afbeelding van grootte van het formaat](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Als u afmetingen opgeeft die groter zijn dan de oorspronkelijke breedte en hoogte van de afbeelding, voegt Bing witte vulling toe aan de linker- en bovenranden.  

## <a name="request-different-thumbnail-sizes"></a>Verschillende miniatuurformaten aanvragen

Als u een andere miniatuurafbeeldingsgrootte wilt aanvragen, verwijdert u `id` `pid` alle queryparameters uit de URL van de miniatuur, behalve de parameters en parameters. Voeg vervolgens `&w` de queryparameter `&h` (breedte) of (hoogte) toe met de gewenste afbeeldingsgrootte in pixels, maar niet beide. Bing behoudt de oorspronkelijke beeldverhouding. 

Als u de breedte van de afbeelding die door de bovenstaande URL is opgegeven, wilt vergroten tot 165 pixels, gebruikt u de volgende URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Als u een afbeelding aanvraagt die groter is dan de oorspronkelijke grootte van de afbeelding, voegt Bing indien nodig witte vulling rond de afbeelding toe. Als de oorspronkelijke grootte van de afbeelding bijvoorbeeld 474x316 is en u bent ingesteld `&w` op 500, geeft Bing een afbeelding van 500x333 terug. Deze afbeelding heeft 8,5 pixels witte vulling langs de boven- en onderkant en 13 pixels aan opvulling aan de linker- en rechterranden.

Als u wilt voorkomen dat Bing witte vulling toevoegt als de gevraagde `&p` grootte groter is dan de oorspronkelijke grootte van de afbeelding, stelt u de queryparameter in op 0. Als u bijvoorbeeld de `&p=0` parameter opneemt in de bovenstaande URL, retourneert Bing een afbeelding van 474x316 in plaats van een afbeelding van 500x333:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Als u `&w` beide `&h` en queryparameters opgeeft, behoudt Bing de beeldverhouding van de afbeelding en voegt u desbehoefte witte opvulling toe. Als de oorspronkelijke grootte van de afbeelding bijvoorbeeld 474x316 is en u de breedte-`&w=200&h=200`en hoogteparameters instelt op 200x200 ( ), retourneert Bing een afbeelding met 33 pixels witte vulling aan de boven- en onderkant. Als u `&p` de queryparameter opneemt, retourneert Bing een afbeelding van 200x134.

## <a name="crop-a-thumbnail"></a>Een miniatuur bijsnijden 

Als u een afbeelding `c` wilt bijsnijden, neemt u de parameter (bijsnijd)query op. U de volgende waarden gebruiken:
  
- `4`&mdash; Blind Ratio  
- `7`&mdash; Slimme verhouding  

### <a name="smart-ratio-cropping"></a>Bijsnijden in slimme verhouding

Als u Smart Ratio bijsnijden `c` aanvraagt `7`(door de parameter in te stellen op ), wordt een afbeelding vanuit het midden van het gebied van belang naar buiten bijgewassen, met behoud van de beeldverhouding. Het gebied van belang is het gebied van de afbeelding waarvan Bing bepaalt dat de meeste importonderdelen bevat. Hieronder ziet u een voorbeeld regio van belang.  
  
![Regio van belang](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Als u het formaat van een afbeelding wijzigt en slimme ratio bijsnijdt, verkleint Bing de afbeelding tot de gewenste grootte met behoud van de beeldverhouding. Bing snijdt de afbeelding vervolgens op basis van de afmetingen van de grootte. Als de grootte van de grootte bijvoorbeeld kleiner is dan of gelijk is aan de hoogte, wordt de afbeelding links en rechts van het middelpunt van het gebied bijsnijden. Anders zal Bing het bijsnijden naar de boven- en onderkant van het centrum van het gebied van belang.  
  
 
Het volgende toont de afbeelding teruggebracht tot 200x200 met behulp van Smart Ratio bijsnijden. Omdat Bing de afbeelding vanuit de linkerbovenhoek meet, wordt het onderste deel van de afbeelding bijgesneden. 
  
![Landschapsafbeelding bijgesneden tot 200x200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
Het volgende toont de afbeelding teruggebracht tot 200x100 met behulp van Smart Ratio bijsnijden. Omdat Bing de afbeelding vanuit de linkerbovenhoek meet, wordt het onderste deel van de afbeelding bijgesneden. 
   
![Landschapsafbeelding bijgesneden tot 200x100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
Het volgende toont de afbeelding teruggebracht tot 100x200 met behulp van Smart Ratio bijsnijden. Omdat Bing de afbeelding vanuit het midden meet, worden de linker- en rechterdelen van de afbeelding bijgesneden.
  
![Landschapsafbeelding bijgesneden tot 100x200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Als Bing het interessegebied van de afbeelding niet kan bepalen, gebruikt de service Blind Ratio bijsnijden.  

### <a name="blind-ratio-cropping"></a>Blind Ratio bijsnijden

Als u Bijsnijding van de `c` rolverhouding aanvraagt (door de parameter in te stellen op), `4`gebruikt Bing de volgende regels om de afbeelding bij te snijden.  
  
- Als `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`, wordt de afbeelding gemeten vanuit de linkerbovenhoek en bijgesneden aan de onderkant.  
- Als `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`, wordt de afbeelding gemeten vanuit het midden en bijgesneden naar links en rechts.  

Het volgende toont een portretafbeelding van 225x300.  
  
![Origineel zonnebloembeeld](./media/resize-crop/bing-resize-crop-sunflower.png)
  
Het volgende toont het beeld teruggebracht tot 200x200 met behulp van Blind Ratio bijsnijden. De afbeelding wordt gemeten vanaf de linkerbovenhoek, waardoor het onderste deel van de afbeelding wordt bijgesneden.  
  
![Zonnebloembeeld bijgesneden tot 200x200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
Het volgende toont het beeld teruggebracht tot 200x100 met behulp van Blind Ratio bijsnijden. De afbeelding wordt gemeten vanaf de linkerbovenhoek, waardoor het onderste deel van de afbeelding wordt bijgesneden.  
  
![Zonnebloembeeld bijgesneden tot 200x100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
Het volgende toont het beeld teruggebracht tot 100x200 met behulp van Blind Ratio bijsnijden. De afbeelding wordt gemeten vanuit het midden, waardoor de linker- en rechterdelen van de afbeelding worden bijgesneden.  
  
![Zonnebloembeeld bijgesneden tot 100x200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Volgende stappen

* [Wat zijn de Bing Search API's?](bing-api-comparison.md)
* [Vereisten voor gebruik en weergave van Bing Zoeken-API's](use-display-requirements.md)
