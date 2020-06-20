---
title: 'Snelstartgids: client bibliotheek voor formulier herkenning voor .NET'
description: In deze Snelstartgids gaat u aan de slag met de formulier Recognizer-client bibliotheek voor .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: c4f4b07c9c1bd144dfbe5c60ff52cdfa4bf899ef
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85112104"
---
[Referentie documentatie](https://docs.microsoft.com/dotnet/api/overview/azure/formrecognizer?view=azure-dotnet-preview)  |  [Bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src)  |  van bibliotheek [Pakket (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer)  |  Voor [beelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak een gratis versie](https://azure.microsoft.com/free/).
* Een Azure Storage-blob die een set trainings gegevens bevat. Zie [een trainings gegevensverzameling bouwen voor een aangepast model](../../build-training-data-set.md) voor tips en opties voor het samen stellen van uw trainings gegevensverzameling. Voor deze Quick Start kunt u de bestanden in de map **Train** van de voor [beeld-gegevensset](https://go.microsoft.com/fwlink/?linkid=2090451)gebruiken.
* De huidige versie van [.net core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Instellen

### <a name="create-a-form-recognizer-azure-resource"></a>Een Azure-resource voor een formulier herkenning maken

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Omgevingsvariabelen maken

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

In een console venster (zoals cmd, Power shell of bash) gebruikt `dotnet new` u de opdracht om een nieuwe console-app met de naam te maken `formrecognizer-quickstart` . Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Wijzig uw map in de zojuist gemaakte app-map. Bouw vervolgens de toepassing met:

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

Open het *Program.cs* -bestand in de map van het project in uw voorkeurs editor of IDE. Voeg de volgende `using` instructies toe:

```csharp
using Azure.AI.FormRecognizer;
using Azure.AI.FormRecognizer.Models;

using System;
using System.IO;
using System.Threading.Tasks;
```

Voeg vervolgens de volgende code toe aan de methode **Main** van de toepassing. U definieert deze asynchrone taak later op. 

```csharp
static void Main(string[] args)
{
    var t1 = RunFormRecognizerClient();

    Task.WaitAll(t1);
}
```

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Installeer in de toepassingsmap de formulier Recognizer-client bibliotheek voor .NET met de volgende opdracht:

```console
dotnet add package Azure.AI.FormRecognizer --version 1.0.0-preview.3
```

Als u de Visual Studio IDE gebruikt, is de client bibliotheek beschikbaar als een downloadbaar NuGet-pakket.


<!-- Objet model TBD -->



## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de formulier Recognizer-client bibliotheek voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Formulier inhoud herkennen](#recognize-form-content)
* [Bevestigingen herkennen](#recognize-receipts)
* [Aangepast model trainen](#train-a-custom-model)
* [Formulieren analyseren met een aangepast model](#analyze-forms-with-a-custom-model)
* [Uw aangepaste modellen beheren](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>De client verifiëren

Definieer onder de- `Main` methode de taak waarnaar wordt verwezen in `Main` . Hier verifieert u twee client objecten met de abonnements variabelen die u hierboven hebt gedefinieerd. U gebruikt een **AzureKeyCredential** -object, zodat u, indien nodig, de API-sleutel kunt bijwerken zonder nieuwe client objecten te maken.

```csharp
static async Task RunFormRecognizerClient()
{ 
    string endpoint = Environment.GetEnvironmentVariable(
        "FORM_RECOGNIZER_ENDPOINT");
    string apiKey = Environment.GetEnvironmentVariable(
        "FORM_RECOGNIZER_KEY");
    var credential = new AzureKeyCredential(apiKey);
    
    var trainingClient = new FormTrainingClient(new Uri(endpoint), credential);
    var recognizerClient = new FormRecognizerClient(new Uri(endpoint), credential);
```

### <a name="call-client-specific-methods"></a>Client-specifieke methoden aanroepen

In het volgende code blok wordt gebruikgemaakt van de client-objecten voor het aanroepen van methoden voor elk van de belangrijkste taken in de formulier Recognizer SDK. U definieert deze methoden later op.

U moet ook verwijzingen toevoegen aan de Url's voor uw training en gegevens testen. 
* Als u de SAS-URL voor uw aangepaste model trainings gegevens wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechter muisknop op uw container en selecteert u **gedeelde toegangs handtekening ophalen**. Zorg ervoor dat de machtigingen **lezen** en **lijst** zijn ingeschakeld en klik op **maken**. Kopieer vervolgens de waarde in de sectie **URL** . Het moet de volgende indeling hebben: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Als u een URL wilt ophalen van een formulier dat u wilt testen, kunt u de bovenstaande stappen gebruiken om de SAS-URL van een afzonderlijk document in Blob Storage op te halen. U kunt ook de URL van een document naar een andere locatie halen.
* Gebruik de bovenstaande methode om de URL van een kopie van de bevestiging te verkrijgen, of gebruik de URL van de voor beeld-installatie kopie.

> [!NOTE]
> De code fragmenten in deze hand leiding gebruiken externe formulieren die worden gebruikt voor Url's. Als u in plaats daarvan lokale formulier documenten wilt verwerken, raadpleegt u de bijbehorende methoden in de [referentie documentatie](https://docs.microsoft.com/dotnet/api/overview/azure/formrecognizer?view=azure-dotnet-preview).

```csharp
    string trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    string formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
    string receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media"
    + "/contoso-allinone.jpg";

    // Call Form Recognizer scenarios:
    Console.WriteLine("Get form content...");
    await GetContent(recognizerClient, formUrl);

    Console.WriteLine("Analyze receipt...");
    await AnalyzeReceipt(recognizerClient, receiptUrl);

    Console.WriteLine("Train Model with training data...");
    Guid modelId = await TrainModel(trainingClient, trainingDataUrl);

    Console.WriteLine("Analyze PDF form...");
    await AnalyzePdfForm(recognizerClient, modelId, formUrl);

    Console.WriteLine("Manage models...");
    await ManageModels(trainingClient, trainingDataUrl) ;
}
```

## <a name="recognize-form-content"></a>Formulier inhoud herkennen

U kunt de formulier Recognizer gebruiken om tabellen, lijnen en woorden in documenten te herkennen, zonder dat u een model hoeft te trainen.

Gebruik de methode **StartRecognizeContentFromUri** om de inhoud van een bestand op een bepaalde URI te herkennen.

```csharp
private static async Task<Guid> GetContent(
    FormRecognizerClient recognizerClient, string invoiceUri)
{
    Response<IReadOnlyList<FormPage>> formPages = await recognizerClient
        .StartRecognizeContentFromUri(new Uri(invoiceUri))
        .WaitForCompletionAsync();
```

De geretourneerde waarde is een verzameling **FormPage** -objecten: één voor elke pagina in het verzonden document. Met de volgende code worden deze objecten door lopen en worden de geëxtraheerde sleutel-waardeparen en tabel gegevens afgedrukt.

```csharp
    foreach (FormPage page in formPages.Value)
    {
        Console.WriteLine($"Form Page {page.PageNumber} has {page.Lines.Count}" + 
            $" lines.");
    
        for (int i = 0; i < page.Lines.Count; i++)
        {
            FormLine line = page.Lines[i];
            Console.WriteLine($"    Line {i} has {line.Words.Count}" + 
                $" word{(line.Words.Count > 1 ? "s" : "")}," +
                $" and text: '{line.Text}'.");
        }
    
        for (int i = 0; i < page.Tables.Count; i++)
        {
            FormTable table = page.Tables[i];
            Console.WriteLine($"Table {i} has {table.RowCount} rows and" +
                $" {table.ColumnCount} columns.");
            foreach (FormTableCell cell in table.Cells)
            {
                Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex})"
                    $" contains text: '{cell.Text}'.");
            }
        }
    }
}
```

## <a name="recognize-receipts"></a>Bevestigingen herkennen

In deze sectie wordt beschreven hoe u algemene velden van Amerikaanse ontvangsten kunt herkennen en extra heren met behulp van een vooraf getraind ontvangst model.

Als u ontvangst bewijzen wilt herkennen vanuit een URI, gebruikt u de methode **StartRecognizeReceiptsFromUri** . De geretourneerde waarde is een verzameling **RecognizedReceipt** -objecten: één voor elke pagina in het verzonden document. Met de volgende code wordt een ontvangst bij de opgegeven URI verwerkt en worden de belangrijkste velden en waarden naar de console afgedrukt.

```csharp
private static async Task<Guid> AnalyzeReceipt(
    FormRecognizerClient recognizerClient, string receiptUri)
{
    RecognizedReceiptCollection receipts = await recognizerClient.StartRecognizeReceiptsFromUri(new Uri(receiptUri))
    .WaitForCompletionAsync();

    foreach (RecognizedReceipt receipt in receipts)
    {
    FormField merchantNameField;
    if (receipt.RecognizedForm.Fields.TryGetValue("MerchantName", out merchantNameField))
    {
        if (merchantNameField.Value.Type == FieldValueType.String)
        {
            string merchantName = merchantNameField.Value.AsString();

            Console.WriteLine($"Merchant Name: '{merchantName}', with confidence {merchantNameField.Confidence}");
        }
    }

    FormField transactionDateField;
    if (receipt.RecognizedForm.Fields.TryGetValue("TransactionDate", out transactionDateField))
    {
        if (transactionDateField.Value.Type == FieldValueType.Date)
        {
            DateTime transactionDate = transactionDateField.Value.AsDate();

            Console.WriteLine($"Transaction Date: '{transactionDate}', with confidence {transactionDateField.Confidence}");
        }
    }
```

In het volgende code blok wordt de afzonderlijke items herhaald die op de ontvangst zijn gedetecteerd en worden de details ervan naar de console afgedrukt.

```csharp
    FormField itemsField;
    if (receipt.RecognizedForm.Fields.TryGetValue("Items", out itemsField))
    {
        if (itemsField.Value.Type == FieldValueType.List)
        {
            foreach (FormField itemField in itemsField.Value.AsList())
            {
                Console.WriteLine("Item:");

                if (itemField.Value.Type == FieldValueType.Dictionary)
                {
                    IReadOnlyDictionary<string, FormField> itemFields = itemField.Value.AsDictionary();

                    FormField itemNameField;
                    if (itemFields.TryGetValue("Name", out itemNameField))
                    {
                        if (itemNameField.Value.Type == FieldValueType.String)
                        {
                            string itemName = itemNameField.Value.AsString();

                            Console.WriteLine($"    Name: '{itemName}', with confidence {itemNameField.Confidence}");
                        }
                    }

                    FormField itemTotalPriceField;
                    if (itemFields.TryGetValue("TotalPrice", out itemTotalPriceField))
                    {
                        if (itemTotalPriceField.Value.Type == FieldValueType.Float)
                        {
                            float itemTotalPrice = itemTotalPriceField.Value.AsFloat();

                            Console.WriteLine($"    Total Price: '{itemTotalPrice}', with confidence {itemTotalPriceField.Confidence}");
                        }
                    }
                }
            }
        }
    }
```

Ten slotte drukt het laatste code blok de totale waarde op de ontvangst af.

```csharp
    FormField totalField;
    if (receipt.RecognizedForm.Fields.TryGetValue("Total", out totalField))
    {
        if (totalField.Value.Type == FieldValueType.Float)
        {
            float total = totalField.Value.AsFloat();

            Console.WriteLine($"Total: '{total}', with confidence '{totalField.Confidence}'");
        }
    }
}
```

## <a name="train-a-custom-model"></a>Aangepast model trainen

In deze sectie ziet u hoe u een model kunt trainen met uw eigen gegevens. Met een getraind model kunnen gestructureerde gegevens worden uitgevoerd die de sleutel/waarde-relaties in het oorspronkelijke formulier document bevatten. Nadat u het model hebt getraind, kunt u het testen en opnieuw trainen en uiteindelijk gebruiken om gegevens te extra heren uit meer formulieren, afhankelijk van uw behoeften.

> [!NOTE]
> U kunt ook modellen trainen met een Graphical User Interface zoals het [hulp programma voor het labelen](../../quickstarts/label-tool.md)van het voor beeld van de formulier herkenning.

### <a name="train-a-model-without-labels"></a>Een model trainen zonder labels

Train aangepaste modellen om alle velden en waarden te herkennen die in uw aangepaste formulieren worden gevonden zonder hand matig labels te krijgen voor de trainings documenten.

Met de volgende methode wordt een model voor een gegeven reeks documenten treinen en wordt de status van het model in de-console afgedrukt. 

```csharp
private static async Task<Guid> TrainModel(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    CustomFormModel model = await trainingClient
        .StartTrainingAsync(new Uri(trainingFileUrl), useTrainingLabels: false).WaitForCompletionAsync();
    
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Requested on: {model.RequestedOn}");
    Console.WriteLine($"    Completed on: {model.CompletedOn}");
```

Het geretourneerde **CustomFormModel** -object bevat informatie over de typen die het model kan herkennen en de velden die het kan ophalen van elk formulier type. In het volgende code blok wordt deze informatie in de-console afgedrukt.

```csharp
    foreach (CustomFormSubmodel submodel in model.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
```

Ten slotte retourneert deze methode de unieke ID van het model.

```csharp
    return model.ModelId;
}
```

### <a name="train-a-model-with-labels"></a>Een model trainen met labels

U kunt ook aangepaste modellen trainen door de trainings documenten hand matig te labelen. Training met labels leidt tot betere prestaties in sommige scenario's. Als u met labels wilt trainen, moet u in uw Blob Storage-container, naast de trainings documenten, speciale label-informatie bestanden (* \<filename\>.pdf.labels.jsaan*) hebben. Het [hulp programma voor het labelen](../../quickstarts/label-tool.md) van het voor beeld van een formulier herkenning biedt een gebruikers interface die u kan helpen bij het maken van deze label bestanden. Zodra u deze hebt, kunt u de **StartTrainingAsync** -methode aanroepen met de para meter *uselabels* ingesteld op `true` .

```csharp
private static async Task<Guid> TrainModelWithLabelsAsync(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    CustomFormModel model = await trainingClient
    .StartTrainingAsync(new Uri(trainingFileUrl), useTrainingLabels: true).WaitForCompletionAsync();
    
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Requested on: {model.RequestedOn}");
    Console.WriteLine($"    Completed on: {model.CompletedOn}");
```

De geretourneerde **CustomFormModel** geeft aan welke velden het model kan ophalen, samen met de geschatte nauw keurigheid van elk veld. In het volgende code blok wordt deze informatie in de-console afgedrukt.

```csharp
    foreach (CustomFormSubmodel submodel in model.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Accuracy != null)
            {
                Console.Write($", Accuracy: {field.Accuracy}");
            }
            Console.WriteLine("");
        }
    }
    return model.ModelId;
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Formulieren analyseren met een aangepast model

In deze sectie wordt gedemonstreerd hoe u sleutel/waarde-informatie en andere inhoud uit uw aangepaste formulier typen kunt extra heren met behulp van modellen die u hebt getraind met uw eigen formulieren.

> [!IMPORTANT]
> Als u dit scenario wilt implementeren, moet u al een model hebben getraind, zodat u de ID ervan kunt door geven naar de onderstaande methode. Zie de sectie [een model trainen](#train-a-model-without-labels) .

U gebruikt de methode **StartRecognizeCustomFormsFromUri** . De geretourneerde waarde is een verzameling **RecognizedForm** -objecten: één voor elke pagina in het verzonden document.

```csharp
// Analyze PDF form data
private static async Task AnalyzePdfForm(
    FormRecognizerClient formClient, Guid modelId, string pdfFormFile)
{    
    Response<IReadOnlyList<RecognizedForm>> forms = await recognizerClient
        .StartRecognizeCustomFormsFromUri(modelId, new Uri(formUri))
        .WaitForCompletionAsync();
```

Met de volgende code worden de resultaten van de analyse naar de console afgedrukt. Alle herkende velden en bijbehorende waarden worden afgedrukt, samen met een betrouwbaarheids Score.

```csharp
    foreach (RecognizedForm form in forms.Value)
    {
        Console.WriteLine($"Form of type: {form.FormType}");
        foreach (FormField field in form.Fields.Values)
        {
            Console.WriteLine($"Field '{field.Name}: ");
    
            if (field.LabelText != null)
            {
                Console.WriteLine($"    Label: '{field.LabelText.Text}");
            }
    
            Console.WriteLine($"    Value: '{field.ValueText.Text}");
            Console.WriteLine($"    Confidence: '{field.Confidence}");
        }
    }
}
```

## <a name="manage-your-custom-models"></a>Uw aangepaste modellen beheren

In deze sectie wordt beschreven hoe u de aangepaste modellen beheert die zijn opgeslagen in uw account. Met de volgende code worden alle model beheer taken in één methode als voor beeld gebruikt. Kopieer eerst de volgende methode hand tekening:

```csharp
private static async Task ManageModels(
    FormRecognizerClient trainingClient, string trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Het aantal modellen in het FormRecognizer-resource account controleren

In het volgende code blok wordt het aantal modellen gecontroleerd dat u hebt opgeslagen in uw formulier Recognizer-account en vergelijkt deze met de limiet voor het account.

```csharp
    // Check number of models in the FormRecognizer account, 
    // and the maximum number of models that can be stored.
    AccountProperties accountProperties = trainingClient.GetAccountProperties();
    Console.WriteLine($"Account has {accountProperties.CustomModelCount} models.");
    Console.WriteLine($"It can have at most {accountProperties.CustomModelLimit}" +
        $" models.");
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>De modellen weer geven die momenteel zijn opgeslagen in het resource-account

In het volgende code blok worden de huidige modellen in uw account vermeld en worden de details ervan in de-console weer gegeven.

```csharp
    // List the first ten or fewer models currently stored in the account.
    Pageable<CustomFormModelInfo> models = trainingClient.GetModelInfos();
    
    foreach (CustomFormModelInfo modelInfo in models.Take(10))
    {
        Console.WriteLine($"Custom Model Info:");
        Console.WriteLine($"    Model Id: {modelInfo.ModelId}");
        Console.WriteLine($"    Model Status: {modelInfo.Status}");
        Console.WriteLine($"    Created On: {modelInfo.CreatedOn}");
        Console.WriteLine($"    Last Modified: {modelInfo.LastModified}");
    }
```

### <a name="get-a-specific-model-using-the-models-id"></a>Een specifiek model ophalen met de ID van het model

De volgende code blok treinen maken een nieuw model (net als in het gedeelte [een model trainen](#train-a-model-without-labels) ) en vervolgens wordt er een tweede verwijzing naar de sectie met de id opgehaald.

```csharp
    // Create a new model to store in the account
    CustomFormModel model = await trainingClient.StartTrainingAsync(
        new Uri(trainingFileUrl)).WaitForCompletionAsync();
    
    // Get the model that was just created
    CustomFormModel modelCopy = trainingClient.GetCustomModel(model.ModelId);
    
    Console.WriteLine($"Custom Model {modelCopy.ModelId} recognizes the following" +
        " form types:");
    
    foreach (CustomFormSubModel subModel in modelCopy.Models)
    {
        Console.WriteLine($"SubModel Form Type: {subModel.FormType}");
        foreach (CustomFormModelField field in subModel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
```

### <a name="delete-a-model-from-the-resource-account"></a>Een model uit het resource-account verwijderen

U kunt ook een model uit uw account verwijderen door te verwijzen naar de ID.

```csharp
    // Delete the model from the account.
    trainingClient.DeleteModel(model.ModelId);
}
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit vanuit de map van uw toepassing met de `dotnet run` opdracht.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer u communiceert met de Cognitive Services Form Recognizer-client bibliotheek met behulp van de .NET SDK, resulteert dit in fouten die door de service worden geretourneerd `RequestFailedException` . Ze bevatten dezelfde HTTP-status code die door een REST API aanvraag zou zijn geretourneerd.

Als u bijvoorbeeld een ontvangstbewijs installatie kopie met een ongeldige URI verzendt, wordt een `400` fout geretourneerd, met de melding "onjuiste aanvraag".

```csharp Snippet:FormRecognizerBadRequest
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();

}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

U ziet dat aanvullende informatie, zoals de client aanvraag-ID van de bewerking, wordt geregistreerd.

```
Message:
    Azure.RequestFailedException: Service request failed.
    Status: 400 (Bad Request)

Content:
    {"error":{"code":"FailedToDownloadImage","innerError":
    {"requestId":"8ca04feb-86db-4552-857c-fde903251518"},
    "message":"Failed to download image from input URL."}}

Headers:
    Transfer-Encoding: chunked
    x-envoy-upstream-service-time: REDACTED
    apim-request-id: REDACTED
    Strict-Transport-Security: REDACTED
    X-Content-Type-Options: REDACTED
    Date: Mon, 20 Apr 2020 22:48:35 GMT
    Content-Type: application/json; charset=utf-8
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u de formulier Recognizer .NET-client bibliotheek gebruikt om modellen te trainen en formulieren op verschillende manieren te analyseren. Vervolgens leert u tips voor het maken van een betere set met trainings gegevens en het produceren van nauw keurige modellen.

> [!div class="nextstepaction"]
> [Een set met trainingsgegevens samenstellen](../../build-training-data-set.md)

* [Wat is Form Recognizer?](../../overview.md)
* De voorbeeld code uit deze hand leiding is te vinden op [github](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md).