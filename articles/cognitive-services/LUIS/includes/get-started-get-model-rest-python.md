---
title: 'Model krijgen met REST-oproep in C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/14/2020
ms.author: diberry
ms.openlocfilehash: 4d8da7d2bc51c4fc4ebc8d71f230f24f20b3aa24
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368466"
---
## <a name="prerequisites"></a>Vereisten

* Azure Language Understanding - Authoring resource 32 tekensleutel en url voor het schrijven van eindpunten. Maken met de [Azure-portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) of [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importeer de [TravelAgent-app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) vanuit de GitHub-repository die gebruik maakt van de GitHub-opslagplaats voor cognitieve services.
* De LUIS toepassings-ID voor de geïmporteerde TravelAgent-app. De toepassings-id wordt weergegeven op het toepassingsdashboard.
* De versie-id in de toepassing die de utterances ontvangt. De standaard-id is '0.1'.
* [Python 3.6](https://www.python.org/downloads/) of hoger.
* [Visual Studio-code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>JSON-bestand met voorbeeldutterances

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Model programmatisch wijzigen

1. Maak een nieuw bestand met de naam `model.py`. Voeg de volgende code toe:

    [!code-python[Add example utterances to Language Understanding in python](~/samples-luis/documentation-samples/quickstarts/change-model/python/3.x/add-utterances-3-6.py)]

1. Vervang de waarden `YOUR-` die beginnen met uw eigen waarden.

    |Informatie|Doel|
    |--|--|
    |`YOUR-KEY`|Uw 32 teken authoring toets.|
    |`YOUR-ENDPOINT`| Uw URL-eindpunt voor het ontwerpen. Bijvoorbeeld `replace-with-your-resource-name.api.cognitive.microsoft.com`. U stelt uw resourcenaam in toen u de resource maakte.|
    |`YOUR-APP-ID`| Uw LUIS-app-id. |

    Toegewezen sleutels en resources zijn zichtbaar in de LUIS-portal in de sectie Beheren op de pagina **Azure-bronnen.** De app-id is beschikbaar in dezelfde sectie Beheren, op de pagina **Toepassingsinstellingen.**

1. Voer de volgende opdracht in om het bestand uit te voeren met een opdrachtprompt in dezelfde map als waar u het bestand hebt gemaakt:

    ```console
    python model.py
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze quickstart, verwijdert u het bestand uit het bestandssysteem.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor een app](../luis-concept-best-practices.md)
