---
title: 'Quickstart: Clientbibliotheek van Form Recognizer voor .NET'
description: In deze quickstart gaat u aan de slag met de clientbibliotheek van Form Recognizer voor .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: 2b46e115b6360b161a1b2ad9b176f3afbfaf27d0
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86277959"
---
[Referentiedocumentatie](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre) | [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [Pakket (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [Voorbeelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak een gratis abonnement aan](https://azure.microsoft.com/free/).
* Een Azure Storage-blob die een set trainingsgegevens bevat. Zie [Een set met trainingsgegevens voor een aangepast model bouwen](../../build-training-data-set.md) voor tips en opties voor het samenstellen van uw set met trainingsgegevens. Voor deze quickstart kunt u de bestanden in de map **Trainen** van de [set met voorbeeldgegevens](https://go.microsoft.com/fwlink/?linkid=2090451) gebruiken.
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Instellen

### <a name="create-a-form-recognizer-azure-resource"></a>Een Azure-resource voor Form Recognizer maken

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Omgevingsvariabelen maken

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `formrecognizer-quickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Wijzig uw map in de zojuist gemaakte app-map. Maak de toepassing vervolgens met:

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

Open vanuit de projectmap het bestand *Program.cs* in uw favoriete editor of IDE. Voeg de volgende `using`-instructies toe:

```csharp
using Azure.AI.FormRecognizer;
using Azure.AI.FormRecognizer.Models;
using Azure.AI.FormRecognizer.Training;

using System;
using System.IO;
using System.Threading.Tasks;
```

Voeg de volgende code toe aan de **Main**-methode. U gaat deze asynchrone taak later definiëren. 

```csharp
static void Main(string[] args)
{
    var t1 = RunFormRecognizerClient();

    Task.WaitAll(t1);
}
```

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer in de toepassingsmap de Form Recognizer-clientbibliotheek voor .NET met de volgende opdracht:

```console
dotnet add package Azure.AI.FormRecognizer --version 1.0.0-preview.3
```

Als u de Visual Studio-IDE, gebruikt, is de clientbibliotheek beschikbaar als downloadbaar NuGet-pakket.


<!-- Objet model TBD -->



## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de clientbibliotheek van Form Recognizer voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Formulierinhoud herkennen](#recognize-form-content)
* [Ontvangstbewijzen herkennen](#recognize-receipts)
* [Aangepast model trainen](#train-a-custom-model)
* [Formulieren analyseren met een aangepast model](#analyze-forms-with-a-custom-model)
* [Uw aangepaste modellen beheren](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>De client verifiëren

Onder de `Main`-methode definieert u de taak waarnaar wordt verwezen in `Main`. Hier gaat u twee clientobjecten verifiëren met behulp van de abonnementsvariabelen die u hierboven hebt gedefinieerd. U gebruikt een **AzureKeyCredential**-object, zodat u indien nodig de API-sleutel kunt bijwerken zonder nieuwe clientobjecten te maken.

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

### <a name="call-client-specific-methods"></a>Clientspecifieke methodes aanroepen

In het volgende codeblok worden de clientobjecten gebruikt voor het aanroepen van methodes voor elk van de hoofdtaken in de Form Recognizer-SDK. U gaat deze methodes later definiëren.

U moet ook verwijzingen naar de URL's toevoegen voor uw trainings- en testgegevens. 
* Als u de SAS-URL voor de trainingsgegevens van uw aangepaste model wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechtermuisknop op uw container en selecteert u **Handtekening voor gedeelde toegang ophalen**. Controleer of de machtigingen **Lezen** en **Lijst** zijn ingeschakeld en klik op **Maken**. Kopieer vervolgens de waarde in de sectie **URL**. Deze moet de notatie `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` hebben.
* Als u een URL wilt ophalen van een formulier dat u wilt testen, kunt u de bovenstaande stappen gebruiken om de SAS-URL van een afzonderlijk document in Blob Storage op te halen. U kunt ook de URL gebruiken van een document dat zich elders bevindt.
* Gebruik bovenstaande methode ook om de URL te verkrijgen van een kopie van een ontvangstbewijs, of gebruik de meegeleverde URL van een voorbeeldkopie.

> [!NOTE]
> De codefragmenten in deze gids gebruiken externe formulieren die worden geopend middels URL's. Als u in plaats daarvan lokale formulierdocumenten wilt verwerken, raadpleegt u de gerelateerde methoden in de [referentiedocumentatie](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre).

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

## <a name="recognize-form-content"></a>Formulierinhoud herkennen

U kunt Form Recognizer gebruiken om tabellen, regels en woorden in documenten te herkennen, zonder dat u een model hoeft te trainen.

Als u de inhoud van een bestand op een bepaalde URI wilt herkennen, gebruikt u de methode **StartRecognizeContentFromUrl**.

```csharp
private static async Task<Guid> GetContent(
    FormRecognizerClient recognizerClient, string invoiceUri)
{
    Response<FormPageCollection> formPages = await recognizerClient
        .StartRecognizeContentFromUri(new Uri(invoiceUri))
        .WaitForCompletionAsync();
```

De geretourneerde waarde is een verzameling **FormPage**-objecten: één voor elke pagina in het ingediende document. Met de volgende code worden deze objecten doorlopen en worden de uitgepakte sleutel-/waardeparen en tabelgegevens afgedrukt.

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
                Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex})" +
                    $" contains text: '{cell.Text}'.");
            }
        }
    }
}
```

## <a name="recognize-receipts"></a>Ontvangstbewijzen herkennen

In deze sectie wordt beschreven hoe u veelvoorkomende velden in Amerikaanse ontvangstbewijzen kunt herkennen en extraheren met behulp van een vooraf getraind ontvangstbewijsmodel.

Om ontvangstbewijzen te herkennen vanuit een URI, gebruikt u de methode **StartRecognizeReceiptsFromUrl**. De geretourneerde waarde is een verzameling **RecognizedReceipt**-objecten: één voor elke pagina in het ingediende document. Met de volgende code wordt een ontvangstbewijs op de opgegeven URI verwerkt en worden de belangrijkste velden en waarden op de console weergegeven.

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

In het volgende codeblok worden de afzonderlijke items die op het ontvangstbewijs zijn gedetecteerd doorlopen en worden de details ervan naar de console afgedrukt.

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

Ten slotte wordt met het laatste codeblok de totale waarde van het ontvangstbewijs afgedrukt.

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

In deze sectie ziet u hoe u een model kunt trainen met uw eigen gegevens. Met een getraind model kunnen gestructureerde gegevens worden uitgevoerd waarin ook de sleutel-waarderelaties uit het oorspronkelijke formulierdocument zijn opgenomen. Nadat u het model hebt getraind, kunt u het testen, opnieuw trainen en hiermee uiteindelijk gegevens naar behoefte extraheren uit meer formulieren.

> [!NOTE]
> U kunt ook modellen trainen met een grafische gebruikersinterface, zoals het [voorbeeldhulpprogramma voor labelen van Form Recognizer](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Een model trainen zonder labels

Train aangepaste modellen om alle velden en waarden te herkennen die in uw aangepaste formulieren worden gevonden zonder de trainingsdocumenten handmatig te labelen.

Met de volgende methode wordt een model voor een bepaalde set documenten getraind en wordt de status van het model in de console weergegeven. 

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

Het geretourneerde **CustomFormModel**-object bevat informatie over de formuliertypen die het model kan herkennen en de velden die het uit elk formuliertype kan uitpakken. In het volgende codeblok wordt deze informatie op de console weergegeven.

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

Ten slotte retourneert deze methode de unieke id van het model.

```csharp
    return model.ModelId;
}
```

### <a name="train-a-model-with-labels"></a>Een model trainen met labels

U kunt aangepaste modellen ook trainen door de trainingsdocumenten handmatig te labelen. Training met labels leidt in sommige scenario's tot betere prestaties. Als u met labels wilt trainen, moet uw Blob Storage-container naast de trainingsdocumenten ook speciale bestanden met labelinformatie ( *\<filename\>.pdf.labels.json*) bevatten. Het [hulpprogramma voor labelen van Form Recognizer](../../quickstarts/label-tool.md) beschikt over een gebruikersinterface die u kan helpen om deze labelbestanden te maken. Zodra u deze hebt, kunt u de methode **StartTrainingAsync** aanroepen met de parameter *uselabels* ingesteld op `true`.

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

Het geretourneerde **CustomFormModel** geeft aan welke velden het model kan extraheren, samen met de geschatte nauwkeurigheid van elk veld. In het volgende codeblok wordt deze informatie op de console weergegeven.

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

In deze sectie ziet u hoe u belangrijke/waardevolle informatie en andere inhoud uit uw aangepaste formuliertypen kunt ophalen met behulp van modellen die u hebt getraind met uw eigen formulieren.

> [!IMPORTANT]
> Als u dit scenario wilt implementeren, moet u al een model hebben getraind, zodat u de id ervan kunt doorgeven aan onderstaande methode. Bekijk de sectie [Een model trainen](#train-a-model-without-labels).

U gebruikt de methode **StartRecognizeCustomFormsFromUriRecognizeCustomFormsFromUrl**. De geretourneerde waarde is een verzameling **RecognizedForm**-objecten: één voor elke pagina in het ingediende document.

```csharp
// Analyze PDF form data
private static async Task AnalyzePdfForm(
    FormRecognizerClient formClient, Guid modelId, string pdfFormFile)
{    
    Response<IReadOnlyList<RecognizedForm>> forms = await recognizerClient
        .StartRecognizeCustomFormsFromUri(modelId, new Uri(formUri))
        .WaitForCompletionAsync();
```

Met de volgende code worden de resultaten van de analyse op de console weergegeven. Alle herkende velden en bijbehorende waarden worden afgedrukt, samen met een betrouwbaarheidsscore.

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

In deze sectie wordt beschreven hoe u de aangepaste modellen beheert die zijn opgeslagen in uw account. Als voorbeeld worden met de volgende code alle modelbeheertaken in één methode uitgevoerd. Begin met het kopiëren van de onderstaande methodehandtekening:

```csharp
private static async Task ManageModels(
    FormRecognizerClient trainingClient, string trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Het aantal modellen in het FormRecognizer-resourceaccount controleren

In het volgende codeblok wordt het aantal modellen gecontroleerd dat u in uw Form Recognizer-account hebt opgeslagen en wordt dit aantal vergeleken met de limiet voor het account.

```csharp
    // Check number of models in the FormRecognizer account, 
    // and the maximum number of models that can be stored.
    AccountProperties accountProperties = trainingClient.GetAccountProperties();
    Console.WriteLine($"Account has {accountProperties.CustomModelCount} models.");
    Console.WriteLine($"It can have at most {accountProperties.CustomModelLimit}" +
        $" models.");
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>De modellen weergeven die momenteel zijn opgeslagen in het resource-account

In het volgende codeblok worden de huidige modellen in uw account vermeld en worden de details ervan in de console weergegeven.

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

### <a name="get-a-specific-model-using-the-models-id"></a>Een specifiek model ophalen met de id van het model

Het volgende codeblok traint een nieuw model (net zoals in de sectie [Een model trainen](#train-a-model-without-labels)) en haalt dan met behulp van zijn id een tweede verwijzing op.

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

### <a name="delete-a-model-from-the-resource-account"></a>Een model uit het resourceaccount verwijderen

U kunt een model ook uit uw account verwijderen door naar de id te verwijzen.

```csharp
    // Delete the model from the account.
    trainingClient.DeleteModel(model.ModelId);
}
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `dotnet run`.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer met behulp van de .NET SDK de Cognitive Services Form Recognizer-clientbibliotheek gebruikt, resulteren fouten geretourneerd door de service in een `RequestFailedException`. Ze bevatten dezelfde HTTP-statuscode die een REST API-aanvraag zou retourneren.

Als u bijvoorbeeld een afbeelding van een ontvangstbewijs verstuurt met een ongeldige URI, dan wordt een `400`-fout geretourneerd met het bericht 'Foute aanvraag'.

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

U ziet dat aanvullende informatie, zoals de clientaanvraag-id van de bewerking, wordt geregistreerd.

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

In deze quickstart hebt u de clientbibliotheek van Form Recognizer voor .NET gebruikt om modellen te trainen en formulieren op verschillende manieren te analyseren. Vervolgens krijgt u tips om een betere set met trainingsgegevens te maken en nauwkeurigere modellen te produceren.

> [!div class="nextstepaction"]
> [Een set met trainingsgegevens samenstellen](../../build-training-data-set.md)

* [Wat is Form Recognizer?](../../overview.md)
* De voorbeeldcode uit deze gids (en meer) is te vinden op [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md).
