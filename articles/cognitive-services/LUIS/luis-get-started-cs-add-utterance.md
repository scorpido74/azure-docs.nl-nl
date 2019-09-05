---
title: 'Quickstart: Wijzigen, app trainen, C# -Luis'
titleSuffix: Azure Cognitive Services
description: Voeg in deze C#-snelstart voorbeeldutterances toe aan een Home Automation-app en train de app.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 43bbcbcad8b4c03829ba702cecfff1e64b8e27b4
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375568"
---
# <a name="quickstart-change-model-using-c"></a>Snelstartgids: Model wijzigen met behulp van C#

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* De meest recente [**Visual Studio Community edition**](https://www.visualstudio.com/downloads/).
* Programmeertaal C# moet geïnstalleerd zijn.
* NuGet-pakketten [JsonFormatterPlus](https://www.nuget.org/packages/JsonFormatterPlus) en [CommandLine](https://www.nuget.org/packages/CommandLineParser/)

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>JSON-bestand met voorbeeldutterances

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Snelstartcode maken 

Maak in Visual Studio een nieuwe **Windows Classic bureau blad-console** -app met behulp van de .NET Framework. Geef het project `ConsoleApp1`een naam.

![Visual Studio-projecttype](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

### <a name="add-the-systemweb-dependency"></a>De afhankelijkheid System.Web toevoegen

Voor het Visual Studio-project is **System.Web** nodig. Klik in de Solution Explorer met de rechter muisknop op **verwijzingen** en selecteer **referentie toevoegen** uit de sectie assembly's.

![System.web-referentie toevoegen](./media/luis-quickstart-cs-add-utterance/system.web.png)

### <a name="add-other-dependencies"></a>Andere afhankelijkheden toevoegen

Voor het Visual Studio-project zijn **JsonFormatterPlus** en **CommandLineParser** nodig. Klik in Solution Explorer met de rechtermuisknop op **Verwijzingen** en selecteer **NuGet-pakketten beheren...** . Blader naar een van de twee pakketten en voeg deze toe. 

![Afhankelijkheden van derden toevoegen](./media/luis-quickstart-cs-add-utterance/add-dependencies.png)


### <a name="write-the-c-code"></a>De C#-code schrijven
Het bestand **Program.cs** moet er als volgt uitzien:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Werk de afhankelijkheden zo bij:

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=1-11 "Add the dependencies")]


Voeg de LUIS-id's en tekenreeksen toe aan de klasse **Program**.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=19-30&dedent=8 "Add the LUIS IDs and strings")]

Voeg een klasse toe aan de klasse **Program** toe om opdrachtregelparameters te beheren.

   [!code-csharp[Add class to manage command line parameters.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=32-46 "Add class to manage command-line parameters.")]

Voeg de GET-aanvraagmethode toe aan de klasse **Program**.

   [!code-csharp[Add the GET request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=49-59 "Add the GET request.")]


Voeg de POST-aanvraagmethode toe aan de klasse **Program**. 

   [!code-csharp[Add the POST request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=60-76 "Add the POST request.")]

Voeg voorbeeldutterances toe uit de bestandsmethode aan de klasse **Program**.

   [!code-csharp[Add example utterances from file.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=77-86 "Add example utterances from file.")]

Nadat de wijzigingen op het model zijn toegepast, traint u het model. Voeg methode toe aan de klasse **Program**.

   [!code-csharp[After the changes are applied to the model, train the model.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=87-96 "After the changes are applied to the model, train the model.")]

De training wordt mogelijk niet meteen voltooid, controleer de status om te controleren of de training is voltooid. Voeg methode toe aan de klasse **Program**.

   [!code-csharp[Training may not complete immediately, check status to verify training is complete.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=97-103 "Training may not complete immediately, check status to verify training is complete.")]

Voeg de main-code toe om opdrachtregelargumenten te beheren. Voeg methode toe aan de klasse **Program**.

   [!code-csharp[To manage command line arguments, add the main code.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=104-137 "To manage command-line arguments, add the main code.")]

### <a name="copy-utterancesjson-to-output-directory"></a>Kopieer utterances.json naar de uitvoerdirectory

Voeg in de Solution Explorer het `utterances.json` door met de rechter muisknop te klikken in de project naam van de Solution Explorer, selecteer vervolgens **toevoegen**en selecteer vervolgens **bestaand item**. Selecteer het `utterances.json` bestand. Hiermee voegt u het bestand toe aan het project. Vervolgens moet de map worden toegevoegd aan de uitvoermap. Klik met de rechter `utterances.json` muisknop op de en selecteer **Eigenschappen**. Markeer in de eigenschappenvensters de **Build Action** `Content`en de **Copy to Output Build Directory** `Copy Always`.  

![Het JSON-bestand markeren als inhoud](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="build-code"></a>Code bouwen

Bouw de code in Visual Studio. 

## <a name="run-code"></a>Code uitvoeren

Voer de toepassing uit vanaf een opdrachtregel in de directory /bin/Debug van het project. 

```console
ConsoleApp1.exe --add utterances.json --train --status
```

Deze opdrachtregel toont de resultaten van het aanroepen van de API voor het toevoegen van utterances. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Resources opschonen
Verwijder wanneer u klaar bent met de snelstart alle bestanden die in de snelstart zijn gemaakt. 

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"] 
> [Programmatisch een LUIS-app bouwen](luis-tutorial-node-import-utterances-csv.md) 
