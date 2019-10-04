---
title: Doel intentie met REST-aanroep in python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: e86d1e16e7c61f851a75ad97d2744b0daa009617
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838515"
---
## <a name="prerequisites"></a>Vereisten

* [Python 3.6](https://www.python.org/downloads/) of later.
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS-sleutel ophalen

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent--programmatically"></a>De intentie programmatisch ophalen

U kunt Python gebruiken voor toegang tot de resultaten die u in het browservenster in de vorige stap hebt gezien.

1. Kopieer een van de volgende codefragmenten naar een bestand met de naam `quickstart-call-endpoint.py`:

    #### <a name="python-27tabp2"></a>[Python 2,7](#tab/P2)

    [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]    

    #### <a name="python-36tabp3"></a>[Python 3,6](#tab/P3)

    [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

    * * *

1. Vervang de waarde van het veld `Ocp-Apim-Subscription-Key` door de sleutel van uw LUIS-eindpunt.

1. Installeer afhankelijkheden met `pip install requests`.

1. Voer het script uit met behulp van `python ./quickstart-call-endpoint.py`. De uitvoer bestaat uit de JSON die u eerder hebt gezien in het browservenster.

## <a name="luis-keys"></a>LUIS-sleutels

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze snelstart, sluit u het Visual Studio-project en verwijdert u de projectmap uit het bestandssysteem. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uitingen en Train toevoegen met python](../luis-get-started-python-add-utterance.md)