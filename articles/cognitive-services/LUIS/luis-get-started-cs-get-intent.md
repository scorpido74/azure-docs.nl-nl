---
title: 'Quickstart: Intentie, C# -Luis'
titleSuffix: Azure Cognitive Services
description: In deze C#-snelstart gebruikt u een beschikbare openbare LUIS-app om de intentie van een gebruiker te bepalen aan de hand van beschrijvende tekst.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: f7bea7320c99487e5f8f534f337a744344efa658
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375552"
---
# <a name="quickstart-get-intent-using-c"></a>Snelstart: Intentie ophalen met behulp van C#

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Vereisten

* [Visual Studio Community 2017-editie](https://visualstudio.microsoft.com/vs/community/)
* C#-programmeertaal (meegeleverd met VS Community 2017)
* Id van openbare app: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS-sleutel ophalen

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>De intentie via een browser ophalen

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>De intentie programmatisch ophalen

Gebruik C# om een query uit te voeren voor de GET [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) van het voorspellingseindpunt om dezelfde resultaten te behalen als die u kreeg in het browservenster in het vorige gedeelte. 

1. Maak in Visual Studio een nieuwe consoletoepassing. 

    ![Een nieuwe consoletoepassing maken in Visual Studio](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. Selecteer de optie **Referentie toevoegen** in het Visual Studio-project in de Solutions Explorer en selecteer vervolgens **System.Web** op het tabblad Assembly's.

    ![selecteer Verwijzing toevoegen en selecteer vervolgens System.Web op het tabblad Assembly's](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Overschrijf Program.cs met de volgende code:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Vervang de waarde van `YOUR_KEY` door uw LUIS-sleutel.

5. Bouw en voer de consoletoepassing uit. De uitvoer bestaat uit de JSON die u eerder hebt gezien in het browservenster.

    ![In het consolevenster wordt het JSON-resultaat van LUIS weergegeven](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>LUIS-sleutels

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze snelstart, sluit u het Visual Studio-project en verwijdert u de projectmap uit het bestandssysteem. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Utterances toevoegen en trainen met C#](luis-get-started-cs-add-utterance.md)
