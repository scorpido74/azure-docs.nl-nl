---
title: Afbeeldingen beheren met de API-console - Inhoudsmoderator
titleSuffix: Azure Cognitive Services
description: Gebruik de Image Moderation API in Azure Content Moderator om scan- en-review moderatieworkflows voor afbeeldingsinhoud te starten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 714621fdcc307ee8b29567fc0d95ca41d31aa9e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75448262"
---
# <a name="moderate-images-from-the-api-console"></a>Matige afbeeldingen van de API-console

Gebruik de [Image Moderation API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) in Azure Content Moderator om scan- en-review moderatieworkflows voor afbeeldingsinhoud te starten. De moderatietaak scant uw inhoud op godslastering en vergelijkt deze met aangepaste en gedeelde blocklists.

## <a name="use-the-api-console"></a>De API-console gebruiken
Voordat u de API testen in de online console, hebt u uw abonnementssleutel nodig. Dit bevindt zich op het tabblad **Instellingen** in het vak **Ocp-Apim-Subscription-Key.** Zie [Overzicht](overview.md) voor meer informatie.

1. Ga naar [API-verwijzing voor imagemoderatie](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

   De **pagina Afbeelding - Evalueren** van de pagina voor beeldbeheer wordt geopend.

2. Selecteer **voor open API-testconsole**het gebied dat uw locatie het meest beschrijft. 

   ![Afbeelding uitproberen - Selectie paginaregio evalueren](images/test-drive-region.png)
  
   De **afbeelding - API-console evalueren** wordt geopend.

3. Voer in het vak **Ocp-Apim-Subscription-Key** uw abonnementssleutel in.

   ![Afbeelding uitproberen - Console-abonnementssleutel evalueren](images/try-image-api-1.PNG)

4. Gebruik in het vak **Hoofdaanvraag** de standaardvoorbeeldafbeelding of geef een afbeelding op die u wilt scannen. U de afbeelding zelf indienen als binaire bitgegevens of een openbaar toegankelijke URL voor een afbeelding opgeven. 

   Gebruik in dit voorbeeld het pad in het **hoofdvak Aanvragen** en selecteer **Vervolgens Verzenden**. 

   ![Afbeelding proberen - Instantie consoleaanvraag evalueren](images/try-image-api-2.PNG)

   Dit is de afbeelding op die URL:

   ![Afbeelding proberen - Voorbeeldafbeelding voor de console evalueren](images/sample-image.jpg) 

5. Selecteer **Verzenden**.

6. De API retourneert een waarschijnlijkheidsscore voor elke classificatie. Het geeft ook een bepaling van de vraag of het beeld voldoet aan de voorwaarden **(waar** of **onwaar).** 

   ![Afbeelding proberen - Beoordeling van de kansscore en conditiebepaling van de console](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Gezichtsdetectie

U de API voor beeldbeheer gebruiken om gezichten in een afbeelding te vinden. Deze optie kan handig zijn als u zorgen heeft over de privacy en wilt voorkomen dat een specifiek gezicht op uw platform wordt geplaatst. 

1. Selecteer in de referentie van de [API voor beeldbeheer](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), in het linkermenu onder **Afbeelding**, **Zoek vlakken**. 

   De pagina **Afbeelding - Gezichten zoeken** wordt geopend.

2. Selecteer **voor open API-testconsole**het gebied dat uw locatie het meest beschrijft. 

   ![Afbeelding uitproberen - Selectie gezichten paginagebied zoeken](images/test-drive-region.png)

   De **API-console Afbeelding - Gezichten zoeken** wordt geopend.

3. Geef een afbeelding op die u wilt scannen. U de afbeelding zelf indienen als binaire bitgegevens of een openbaar toegankelijke URL voor een afbeelding opgeven. In dit voorbeeld wordt gekoppeld aan een afbeelding die wordt gebruikt in een CNN-verhaal.

   ![Afbeelding uitproberen - Voorbeeldafbeelding Gezichten zoeken](images/try-image-api-face-image.jpg)

   ![Afbeelding uitproberen - Voorbeeldaanvraag Gezichten zoeken](images/try-image-api-face-request.png)

4. Selecteer **Verzenden**. In dit voorbeeld vindt de API twee gezichten en retourneert de coördinaten in de afbeelding.

   ![Afbeelding uitproberen - voorbeeldinhoud van gezichten zoeken](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Tekstdetectie via OCR-mogelijkheden

U de OCR-mogelijkheid voor inhoudgebruiken om tekst in afbeeldingen te detecteren.

1. Selecteer **OCR**in de referentie van api voor [beeldbeheer](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), in het linkermenu onder **Afbeelding**. 

   De **pagina Afbeelding - OCR** wordt geopend.

2. Selecteer **voor open API-testconsole**het gebied dat uw locatie het meest beschrijft. 

   ![Afbeelding - OCR-paginaregioselectie](images/test-drive-region.png)

   De **Image - OCR** API-console wordt geopend.

3. Voer in het vak **Ocp-Apim-Subscription-Key** uw abonnementssleutel in.

4. Gebruik in het vak **Hoofdaanvraag** de standaardvoorbeeldafbeelding. Dit is dezelfde afbeelding die wordt gebruikt in de vorige sectie.

5. Selecteer **Verzenden**. De uitgepakte tekst wordt weergegeven in JSON:

   ![Afbeelding - ocr-voorbeeldinhoudvak](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Volgende stappen

Gebruik de REST API in uw code of volg de [.NET SDK quickstart](dotnet-sdk-quickstart.md) om afbeeldingsmoderatie toe te voegen aan uw toepassing.
