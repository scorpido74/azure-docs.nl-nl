---
title: Intentie met REST-aanroep in Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 7199dfaaa476e4be27929010b76a6e0544857bdb
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838523"
---
## <a name="prerequisites"></a>Vereisten

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio code](https://code.visualstudio.com/) of uw favoriete IDE
* Id van openbare app: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS-sleutel ophalen

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>De intentie programmatisch ophalen

U kunt Java gebruiken voor toegang tot de dezelfde resultaten die u in het browservenster in de vorige stap hebt gezien. Zorg ervoor dat u de Apache-bibliotheken aan uw project toevoegt.

1. Kopieer de volgende code om een klasse te maken in een bestand met de naam `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Vervang de waarde van de variabele `YOUR-KEY` door de LUIS-sleutel.

3. Vervang door het bestandspad en compileer het Java-programma vanaf een opdracht regel: `javac -cp .;<FILE_PATH>\* LuisGetRequest.java`.

4. Vervang door het bestandspad en voer de toepassing uit vanaf een opdracht regel: `java -cp .;<FILE_PATH>\* LuisGetRequest.java`. De uitvoer bestaat uit de JSON die u eerder hebt gezien in het browservenster.

    ![In het consolevenster wordt het JSON-resultaat van LUIS weergegeven](../media/luis-get-started-java-get-intent/console-turn-on.png)
    


## <a name="luis-keys"></a>LUIS-sleutels

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze snelstart, sluit u het Visual Studio-project en verwijdert u de projectmap uit het bestandssysteem. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uitingen en Train toevoegen met Java](../luis-get-started-java-add-utterance.md)