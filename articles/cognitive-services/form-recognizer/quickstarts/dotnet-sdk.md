---
title: 'Quickstart: Client bibliotheek voor formulier herkenning voor .NET | Microsoft Docs'
description: Aan de slag met de Form Recognizer-client bibliotheek voor .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/12/2019
ms.author: pafarley
ms.openlocfilehash: ada570196c916a8101e8e968d284a3b280199cf3
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142807"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>Quickstart: Client bibliotheek voor formulier herkenning voor .NET

Aan de slag met de Form Recognizer-client bibliotheek voor .NET. Formulier herkenning is een cognitieve service die gebruikmaakt van machine learning technologie om sleutel-waardeparen en tabel gegevens uit formulier documenten te identificeren en uit te pakken. Vervolgens voert het gestructureerde gegevens uit die ook de relaties uit het oorspronkelijke bestand omvatten. Voer de volgende stappen uit om het SDK-pakket te installeren en de voorbeeld code voor basis taken uit te proberen.

Gebruik de Form Recognizer-client bibliotheek voor .NET voor het volgende:

* Een aangepast model voor formulier herkenning trainen
* Formulieren analyseren met een aangepast model
* Een lijst met aangepaste modellen ophalen

[](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | [Bron](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.FormRecognizer)codepakket | van de referentie documentatie bibliotheek[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak een gratis versie](https://azure.microsoft.com/free/).
* De huidige versie van [.net core](https://dotnet.microsoft.com/download/dotnet-core).
* Een Azure Storage-blob die een set trainings gegevens bevat. Zie [een trainings gegevensset voor een aangepast model bouwen](../build-training-data-set.md) voor tips en opties voor het samen stellen van uw trainings gegevens. 

## <a name="setting-up"></a>Instellen

### <a name="create-a-form-recognizer-azure-resource"></a>Een Azure-resource voor een formulier herkenning maken

[!INCLUDE [create resource](../includes/create-resource.md)]

Wanneer u een sleutel van uw proef abonnement of resource hebt ontvangen, [maakt u een omgevings variabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de `FORM_RECOGNIZER_KEY`sleutel met de naam.

### <a name="create-a-new-c-application"></a>Een nieuwe C# toepassing maken

In een console venster (zoals cmd, Power shell of bash) gebruikt u de `dotnet new` opdracht om een nieuwe console-app met de naam `formrecognizer-quickstart`te maken. Met deze opdracht maakt u een eenvoudig ' C# Hallo wereld '-project met één bron bestand: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Wijzig uw directory in de zojuist gemaakte app-map. Bouw vervolgens de toepassing met:

```console
dotnet build
```

De build-uitvoer mag geen waarschuwingen of fouten bevatten. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Open het _Program.cs_ -bestand in de map van het project in uw voorkeurs editor of IDE. Voeg de volgende `using` instructies toe:

```csharp
using Microsoft.Azure.CognitiveServices.FormRecognizer;
using Microsoft.Azure.CognitiveServices.FormRecognizer.Models;

using System;
using System.IO;
using System.Threading.Tasks;
```

Voeg vervolgens de volgende code toe aan de methode **Main** van de toepassing. U definieert deze asynchrone taak later op.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Installeer in de toepassingsmap de formulier Recognizer-client bibliotheek voor .NET met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

Als u de Visual Studio IDE gebruikt, is de client bibliotheek beschikbaar als een downloadbaar NuGet-pakket.

## <a name="object-model"></a>Object model

De volgende klassen behandelen de hoofd functionaliteit van de formulier Recognizer-SDK.

|Name|Description|
|---|---|
|[FormRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|Deze klasse is nodig voor alle functionaliteit voor formulier herkenning. U maakt de app met uw abonnements gegevens en gebruikt deze om instanties van andere klassen te maken.|
|[TrainRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| U gebruikt deze klasse om een aangepast model voor formulier herkenning te trainen met behulp van uw eigen invoer gegevens voor de training. |
|[TrainResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| Deze klasse levert de resultaten van een aangepaste model trein bewerking, met inbegrip van de model-ID, die u vervolgens kunt gebruiken voor het analyseren van formulieren. |
|[AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| Deze klasse levert de resultaten van een aangepaste model analyse bewerking. Het bevat een lijst met **ExtractedPage** -instanties. |
|[ExtractedPage](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| Deze klasse vertegenwoordigt alle gegevens die zijn geëxtraheerd uit één formulier document.|

## <a name="code-examples"></a>Code voorbeelden

<!--
    Include code snippets and short descriptions for each task you list in the the bulleted list. Briefly explain each operation, but include enough clarity to explain complex or otherwise tricky operations.

    Include links to the service's reference content when introducing a class for the first time
-->

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de formulier Recognizer-client bibliotheek voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Een aangepast model voor formulier herkenning trainen](#train-a-custom-model)
* [Formulieren analyseren met een aangepast model](#analyze-forms-with-a-custom-model)
* [Een lijst met aangepaste modellen ophalen](#get-a-list-of-custom-models)

### <a name="define-variables"></a>Variabelen definiëren

Voordat u een methode definieert, voegt u de volgende variabele definities toe boven aan de **programma** klasse. U moet een aantal van de variabelen zelf invullen. 

* U kunt de waarde van het eind punt van uw service vinden in de sectie **overzicht** van de Azure Portal. 
* Als u de SAS-URL voor uw trainings gegevens wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechter muisknop op uw container en selecteert u **gedeelde toegangs handtekening ophalen**. Zorg ervoor dat de machtigingen **lezen** en **lijst** zijn ingeschakeld en klik op **maken**. Kopieer vervolgens de waarde in de sectie **URL** . Het moet de volgende indeling hebben `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`:.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

### <a name="authenticate-the-client"></a>De client verifiëren

Definieer onder `Main` de-methode de taak waarnaar wordt verwezen in. `Main` Hier verifieert u het client object met behulp van de hierboven gedefinieerde abonnements variabelen. U definieert de andere methoden later op.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

### <a name="train-a-custom-model"></a>Aangepast model trainen

De volgende methode gebruikt uw formulier Recognizer-client object om een nieuw herkennings model te trainen in de documenten die zijn opgeslagen in uw Azure Blob-container. Er wordt een hulp methode gebruikt om informatie weer te geven over het nieuwe getrainde model (vertegenwoordigd door een [ModelResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview) -object) en het retourneert de model-id.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

De volgende Help-methode geeft informatie weer over een model voor formulier herkenning.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

### <a name="analyze-forms-with-a-custom-model"></a>Formulieren analyseren met een aangepast model

Deze methode maakt gebruik van de formulier Recognizer-client en een model-ID voor het analyseren van een PDF-formulier document en het extra heren van sleutel/waarde-gegevens. Er wordt een hulp methode gebruikt om de resultaten weer te geven (vertegenwoordigd door een [AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview) -object).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

Met de volgende Help-methode wordt informatie weer gegeven over een analyse bewerking.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

### <a name="get-a-list-of-custom-models"></a>Een lijst met aangepaste modellen ophalen

U kunt een lijst weer geven met alle getrainde modellen die deel uitmaken van uw account en u kunt informatie ophalen over het tijdstip waarop deze zijn gemaakt. De lijst met modellen wordt vertegenwoordigd door een [ModelsResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview) -object.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit door de `dotnet run` opdracht vanuit de toepassingsmap aan te roepen.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Als u een aangepast model hebt getraind dat u uit uw account wilt verwijderen, kunt u ook de volgende methode gebruiken:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u de formulier Recognizer .NET-client bibliotheek gebruikt voor het trainen van een aangepast model en het analyseren van formulieren. Vervolgens leert u tips voor het maken van een betere set met trainings gegevens en het produceren van nauw keurige modellen.

> [!div class="nextstepaction"]
>[Een set trainings gegevens bouwen](../build-training-data-set.md)

* [Wat is formulier herkenner?](../overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).
