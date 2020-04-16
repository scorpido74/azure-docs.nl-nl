---
title: 'Snelstart: clientbibliotheek formulierherkenning voor .NET'
description: Ga aan de slag met de form recognizer-clientbibliotheek voor .NET om met deze quickstart gestructureerde gegevensuitvoer te trainen, extraheren, analyseren en op te halen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 8d05f5ad07f9b3affba9442d6caa6e63162b813c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399411"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>Snelstart: clientbibliotheek formulierherkenning voor .NET

Ga aan de slag met de form recognizer-clientbibliotheek voor .NET. Form Recognizer is een cognitive service die machine learning-technologie gebruikt om sleutel-/waardeparen en tabelgegevens uit formulierdocumenten te identificeren en te extraheren. Vervolgens voert het gestructureerde gegevens uit die ook de relaties uit het oorspronkelijke bestand omvatten. Volg deze stappen om het SDK-pakket te installeren en probeer de voorbeeldcode voor basistaken uit.

Gebruik de clientbibliotheek Formulierherkenning voor .NET om:

* [Een aangepast formulierherkenningsmodel trainen](#train-a-custom-model)
* [Een lijst met uitgepakte sleutels ophalen](#get-a-list-of-extracted-keys)
* [Formulieren analyseren met een aangepast model](#analyze-forms-with-a-custom-model)
* [Een lijst met aangepaste modellen](#get-a-list-of-custom-models)
* [Een aangepast model verwijderen](#delete-a-custom-model)

[Broncodepakket voor naslagdocumentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | [Library source code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/FormRecognizer) | [bibliotheek (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een.](https://azure.microsoft.com/free/)
* Een Azure Storage-blob met een set trainingsgegevens. Zie [Een trainingsgegevensset maken voor een aangepast model](../build-training-data-set.md) voor tips en opties voor het samenstellen van uw trainingsgegevens. Voor deze snelle start u de bestanden gebruiken onder de map **Trein** van de [voorbeeldgegevensset.](https://go.microsoft.com/fwlink/?linkid=2090451)
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Instellen

### <a name="create-a-form-recognizer-azure-resource"></a>Een Azure-bron voor formulierherkenning maken

[!INCLUDE [create resource](../includes/create-resource.md)]

Nadat u een sleutel en eindpunt hebt gekregen, [maakt u omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor respectievelijk de sleutel en het eindpunt, met de naam `FORM_RECOGNIZER_KEY` en `FORM_RECOGNIZER_ENDPOINT`,

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Gebruik de `dotnet new` opdracht om in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe console-app met de naam `formrecognizer-quickstart`te maken. Met deze opdracht wordt een eenvoudig "Hello World" C#-project gemaakt met één bronbestand: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Wijzig uw map in de nieuw gemaakte app-map. Bouw vervolgens de applicatie met:

```console
dotnet build
```

De buildoutput mag geen waarschuwingen of fouten bevatten. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Open in de projectmap het _Program.cs_ bestand in uw voorkeurseditor of IDE. Voeg de volgende `using` instructies toe:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_using)]

Voeg vervolgens de volgende code toe aan de **hoofdmethode van** de toepassing. U definieert deze asynchrone taak later.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer in de toepassingsmap de clientbibliotheek Formulierherkenning voor .NET met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

Als u de Visual Studio IDE gebruikt, is de clientbibliotheek beschikbaar als downloadbaar NuGet-pakket.

## <a name="object-model"></a>Objectmodel

De volgende klassen behandelen de belangrijkste functionaliteit van de Form Recognizer SDK.

|Naam|Beschrijving|
|---|---|
|[FormRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|Deze klasse is nodig voor alle Form Recognizer-functionaliteit. U wilt deze met uw abonnementsgegevens en u gebruikt deze om exemplaren van andere klassen te produceren.|
|[TrainRequest TrainRequest TrainRequest TrainRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| U gebruikt deze klasse om een aangepast Formulierherkenningsmodel te trainen met uw eigen trainingsinvoergegevens. |
|[TrainResult TrainResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| Deze klasse levert de resultaten van een aangepast model Trein bewerking, met inbegrip van het model-ID, die u vervolgens gebruiken om formulieren te analyseren. |
|[AnalyseResultaat](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| Deze klasse levert de resultaten van een aangepaste model analyse bewerking. Het bevat een lijst met **extrauitgepakte pagina-exemplaren.** |
|[Geëxtraheerd pagina](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| Deze klasse vertegenwoordigt alle gegevens die uit één formulierdocument zijn geëxtraheerd.|

## <a name="code-examples"></a>Codevoorbeelden

In deze codefragmenten ziet u hoe u de volgende taken uitvoeren met de clientbibliotheek Formulierherkenning voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Een aangepast formulierherkenningsmodel trainen](#train-a-custom-model)
* [Een lijst met uitgepakte sleutels ophalen](#get-a-list-of-extracted-keys)
* [Formulieren analyseren met een aangepast model](#analyze-forms-with-a-custom-model)
* [Een lijst met aangepaste modellen](#get-a-list-of-custom-models)
* [Een aangepast model verwijderen](#delete-a-custom-model)

## <a name="define-variables"></a>Variabelen definiëren

Voordat u methoden definieert, voegt u de volgende variabele definities toe aan de bovenkant van de **klasse Programma.** Je moet een aantal van de variabelen zelf invullen. 

* Als u de SAS-URL voor uw trainingsgegevens wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechtermuisknop op uw container en selecteert **u Handtekening voor gedeelde toegang ophalen.** Controleer of de machtigingen **Lezen** en **Lijst** zijn ingeschakeld en klik op **Maken**. Kopieer vervolgens de waarde in de **sectie URL.** Het zou de `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`vorm moeten hebben: .
* Als u een voorbeeldformulier nodig hebt om te analyseren, u een van de bestanden gebruiken onder de map **Testen** van de [voorbeeldgegevensset.](https://go.microsoft.com/fwlink/?linkid=2090451) Deze handleiding maakt alleen gebruik van PDF-formulieren.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

## <a name="authenticate-the-client"></a>De client verifiëren

Definieer `Main` onder de methode de taak `Main`waarnaar wordt verwezen in . Hier verifieert u het clientobject met behulp van de abonnementsvariabelen die u hierboven hebt gedefinieerd. U definieert de andere methoden later.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

## <a name="train-a-custom-model"></a>Aangepast model trainen

De volgende methode gebruikt het clientobject Form Recognizer om een nieuw herkenningsmodel te trainen op de documenten die zijn opgeslagen in uw Azure-blobcontainer. Het maakt gebruik van een helpermethode om informatie weer te geven over het nieuw getrainde model (vertegenwoordigd door een [Object ModelResult)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview) en het retourneert de model-ID.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

In de volgende helpermethode wordt informatie weergegeven over een formulierherkenningsmodel.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

## <a name="get-a-list-of-extracted-keys"></a>Een lijst met uitgepakte sleutels ophalen

Zodra de training is voltooid, houdt het aangepaste model een lijst bij met sleutels die het uit de trainingsdocumenten heeft gehaald. Het verwacht dat toekomstige formulierdocumenten deze sleutels bevatten en de bijbehorende waarden in de bewerking Analyseren extraheren. Gebruik de volgende methode om de lijst met uitgepakte toetsen op te halen en af te drukken op de console. Dit is een goede manier om te controleren of het trainingsproces effectief was.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getkeys)]

## <a name="analyze-forms-with-a-custom-model"></a>Formulieren analyseren met een aangepast model

Deze methode gebruikt de form recognizeer-client en een model-id om een PDF-formulierdocument te analyseren en sleutel-/waardegegevens te extraheren. Het maakt gebruik van een helpermethode om de resultaten weer te geven (weergegeven door een [object AnalyzeResult).](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)

> [!NOTE]
> Met de volgende methode wordt een PDF-formulier geanalyseerd. Zie de volledige voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer)voor vergelijkbare methoden die JPEG- en PNG-formulieren analyseren.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

In de volgende helpermethode wordt informatie weergegeven over een bewerking Analyseren.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

## <a name="get-a-list-of-custom-models"></a>Een lijst met aangepaste modellen

U een lijst retourneren met alle getrainde modellen die bij uw account horen en u informatie ophalen over wanneer ze zijn gemaakt. De lijst met modellen wordt weergegeven door een [object ModelsResult.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="delete-a-custom-model"></a>Een aangepast model verwijderen

Als u het aangepaste model uit uw account wilt verwijderen, gebruikt u de volgende methode:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing `dotnet run` uit door de opdracht aan te roepen vanuit uw toepassingsmap.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Als u bovendien een aangepast model hebt getraind dat u uit uw account wilt verwijderen, voert u de methode uit in [Een aangepast model verwijderen](#delete-a-custom-model).

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de clientbibliotheek Formulierherkenning .NET gebruikt om een aangepast model te trainen en formulieren te analyseren. Leer vervolgens tips om een betere trainingsgegevensset te maken en nauwkeurigere modellen te produceren.

> [!div class="nextstepaction"]
> [Een set met trainingsgegevens samenstellen](../build-training-data-set.md)

* [Wat is Form Recognizer?](../overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer)
