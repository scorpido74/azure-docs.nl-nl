---
title: 'Quickstart: Clientbibliotheek van Form Recognizer voor JavaScript'
description: In deze quickstart gaat u aan de slag met de clientbibliotheek van Form Recognizer voor JavaScript.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/08/2020
ms.author: pafarley
ms.openlocfilehash: f5bf8a0420614b69f4022891e76b5d959f5782f3
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035521"
---
[Referentiedocumentatie](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [Pakket (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [Voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/)
* Een Azure Storage-blob die een set trainingsgegevens bevat. Zie [Een set met trainingsgegevens voor een aangepast model bouwen](../../build-training-data-set.md) voor tips en opties voor het samenstellen van uw set met trainingsgegevens. Voor deze quickstart kunt u de bestanden in de map **Trainen** van de [set met voorbeeldgegevens](https://go.microsoft.com/fwlink/?linkid=2090451) gebruiken.
* De huidige versie van [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>Instellen

### <a name="create-a-form-recognizer-azure-resource"></a>Een Azure-resource voor Form Recognizer maken

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Omgevingsvariabelen maken

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map. 

```console
mkdir myapp && cd myapp
```

Voer de opdracht `npm init` uit om een knooppunttoepassing te maken met een `package.json`-bestand. 

```console
npm init
```

Maak een bestand met de naam `index.js` en importeer de volgende bibliotheken:

```javascript
const { FormRecognizerClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");
const fs = require("fs");
```

Laad ook de omgevingsvariabele file.

```javascript
// Load the .env file if it exists
require("dotenv").config();
```

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer als volgt de NPM-pakketten `ai-form-recognizer` en `dotenv`:

```console
npm install @azure/ai-form-recognizer dotenv
```

Het `package.json`-bestand van uw app wordt bijgewerkt met de afhankelijkheden.


<!-- 
    Object model
-->

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de clientbibliotheek van Form Recognizer voor JavaScript:

* [De client verifiëren](#authenticate-the-client)
* [Formulierinhoud herkennen](#recognize-form-content)
* [Ontvangstbewijzen herkennen](#recognize-receipts)
* [Aangepast model trainen](#train-a-custom-model)
* [Formulieren analyseren met een aangepast model](#analyze-forms-with-a-custom-model)
* [Uw aangepaste modellen beheren](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>De client verifiëren

Maak in de functie `main` variabelen voor het Azure-eindpunt en de Azure-sleutel van uw resource. Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabele.

```javascript
// You will need to set these environment variables or edit the following values
const endpoint = process.env["FORM_RECOGNIZER_ENDPOINT"] || "<cognitive services endpoint>";
const apiKey = process.env["FORM_RECOGNIZER_KEY"] || "<api key>";
```

Verifieer vervolgens een clientobject met de abonnementsvariabelen die u hebt gedefinieerd. U gebruikt een **AzureKeyCredential**-object, zodat u indien nodig de API-sleutel kunt bijwerken zonder nieuwe clientobjecten te maken. U maakt ook een clientobject voor training.

```javascript
const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
```

### <a name="call-client-specific-functions"></a>Clientspecifieke functies aanroepen

In het volgende codeblok in `main` worden de clientobjecten gebruikt voor het aanroepen van functies voor elk van de hoofdtaken in de Form Recognizer-SDK. U gaat deze functies later definiëren.

U moet ook verwijzingen naar de URL's toevoegen voor uw trainings- en testgegevens.
* Als u de SAS-URL voor de trainingsgegevens van uw aangepaste model wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechtermuisknop op uw container en selecteert u **Handtekening voor gedeelde toegang** ophalen. Controleer of de machtigingen **Lezen** en **Lijst** zijn ingeschakeld en klik op **Maken**. Kopieer vervolgens de waarde in de sectie **URL**. Deze moet de notatie `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` hebben.
* Als u een URL wilt ophalen van een formulier dat u wilt testen, kunt u de bovenstaande stappen gebruiken om de SAS-URL van een afzonderlijk document in Blob Storage op te halen. U kunt ook de URL gebruiken van een document dat zich elders bevindt.
* Gebruik bovenstaande methode ook om de URL te verkrijgen van een kopie van een ontvangstbewijs, of gebruik de meegeleverde URL van een voorbeeldkopie.

> [!NOTE]
> De codefragmenten in deze gids gebruiken externe formulieren die worden geopend middels URL's. Als u in plaats daarvan lokale formulierdocumenten wilt verwerken, raadpleegt u de gerelateerde methoden in de [referentiedocumentatie](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer).


```javascript
const trainingDataUrl = "<url/path to the labeled training documents>";
const formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
const receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media/contoso-allinone.jpg";


// Call Form Recognizer scenarios:
await GetContent(recognizerClient, formUrl);
await AnalyzeReceipt(recognizerClient, receiptUrl);
modelId = await TrainModel(trainingClient, trainingDataUrl);
await AnalyzePdfForm(recognizerClient, modelId, formUrl);
await ManageModels(trainingClient, trainingDataUrl);
```

## <a name="recognize-form-content"></a>Formulierinhoud herkennen

U kunt Form Recognizer gebruiken om tabellen, regels en woorden in documenten te herkennen, zonder dat u een model hoeft te trainen. Als u de inhoud van een bestand op een bepaalde URI wilt herkennen, gebruikt u de methode **beginRecognizeContentFromUrl**.

```javascript
async function GetContent( recognizerClient, invoiceUri)
{
    const poller = await client.beginRecognizeContentFromUrl(invoiceUri);
    await poller.pollUntilDone();
    const response = poller.getResult();
```

De geretourneerde waarde is een verzameling **FormPage**-objecten: één voor elke pagina in het ingediende document. Met de volgende code worden deze objecten doorlopen en worden de uitgepakte sleutel-/waardeparen en tabelgegevens afgedrukt.

```javascript
    for (const page of response.pages) {
    console.log(
        `Page ${page.pageNumber}: width ${page.width} and height ${page.height} with unit ${page.unit}`
    );
        for (const table of page.tables) {
            for (const row of table.rows) {
                for (const cell of row.cells) {
                    console.log(`cell [${cell.rowIndex},${cell.columnIndex}] has text ${cell.text}`);
                }
            }
        }
    }
}
```

## <a name="recognize-receipts"></a>Ontvangstbewijzen herkennen

In deze sectie wordt beschreven hoe u algemene velden in Amerikaanse ontvangstbewijzen kunt herkennen en uitpakken met behulp van een vooraf getraind ontvangstmodel.

Om ontvangstbewijzen te herkennen vanuit een URI, gebruikt u de methode **beginRecognizeReceiptsFromUrl**. De geretourneerde waarde is een verzameling **RecognizedReceipt**-objecten: één voor elke pagina in het ingediende document. Met de volgende code wordt een ontvangstbewijs op de opgegeven URI verwerkt en worden de belangrijkste velden en waarden op de console weergegeven.

```javascript
async function AnalyzeReceipt( client, receiptUri)
{
    const poller = await client.beginRecognizeReceiptsFromUrl(url, {
        includeTextDetails: true,
        onProgress: (state) => {
            console.log(`analyzing status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();


    const receipt = receipts[0];
    console.log("First receipt:");
    // For supported fields recognized by the service, please refer to https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult.
    const receiptTypeField = receipt.recognizedForm.fields["ReceiptType"];
    if (receiptTypeField.valueType === "string") {
        console.log(`  Receipt Type: '${receiptTypeField.value || "<missing>"}', with confidence of ${receiptTypeField.confidence}`);
    }
    const merchantNameField = receipt.recognizedForm.fields["MerchantName"];
    if (merchantNameField.valueType === "string") {
        console.log(`  Merchant Name: '${merchantNameField.value || "<missing>"}', with confidence of ${merchantNameField.confidence}`);
    }
    const transactionDate = receipt.recognizedForm.fields["TransactionDate"];
    if (transactionDate.valueType === "date") {
        console.log(`  Transaction Date: '${transactionDate.value || "<missing>"}', with confidence of ${transactionDate.confidence}`);
    }
```

In het volgende codeblok worden de afzonderlijke items die op het ontvangstbewijs zijn gedetecteerd doorlopen en worden de details ervan naar de console afgedrukt.

```javascript
    const itemsField = receipt.recognizedForm.fields["Items"];
    if (itemsField.valueType === "array") {
        for (const itemField of itemsField.value || []) {
            if (itemField.valueType === "object") {
                const itemNameField = itemField.value["Name"];
                if (itemNameField.valueType === "string") {
                    console.log(`    Item Name: '${itemNameField.value || "<missing>"}', with confidence of ${itemNameField.confidence}`);
                }
            }
        }
    }
}
```


## <a name="train-a-custom-model"></a>Aangepast model trainen

In deze sectie ziet u hoe u een model kunt trainen met uw eigen gegevens. Met een getraind model kunnen gestructureerde gegevens worden uitgevoerd waarin ook de sleutel-/waarderelaties uit het oorspronkelijke formulierdocument zijn opgenomen. Wanneer u het model hebt getraind, kunt u het testen en opnieuw trainen, en tot slot gebruiken om op betrouwbare wijze gegevens uit meer formulieren uit te pakken, al naargelang nodig is.

> [!NOTE]
> U kunt ook modellen trainen met een grafische gebruikersinterface, zoals het [voorbeeldhulpprogramma voor labelen van Form Recognizer](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Een model trainen zonder labels

Train aangepaste modellen om alle velden en waarden te herkennen die in uw aangepaste formulieren worden gevonden zonder de trainingsdocumenten handmatig te labelen.

Met de volgende functie wordt een model voor een bepaalde set documenten getraind en wordt de status van het model in de console weergegeven. 

```javascript
async function TrainModel(trainingClient, trainingDataUrl)
{
    const poller = await trainingClient.beginTraining(trainingDataUrl, false, {
        onProgress: (state) => {
            console.log(`training status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
    
    if (!response) {
        throw new Error("Expecting valid response!");
    }
    
    console.log(`Model ID: ${response.modelId}`);
    console.log(`Status: ${response.status}`);
    console.log(`Created on: ${response.requestedOn}`);
    console.log(`Last modified: ${response.completedOn}`);
```

Het geretourneerde **CustomFormModel**-object bevat informatie over de formuliertypen die het model kan herkennen en de velden die het uit elk formuliertype kan uitpakken. In het volgende codeblok wordt deze informatie op de console weergegeven.

```javascript
    if (response.submodels) {
        for (const submodel of response.submodels) {
            // since the training data is unlabeled, we are unable to return the accuracy of this model
            console.log("We have recognized the following fields");
            for (const key in submodel.fields) {
                const field = submodel.fields[key];
                console.log(`The model found field '${field.name}'`);
            }
        }
    }
```

Ten slotte retourneert deze methode de unieke id van het model.

```csharp
    return response.modelId;
}
```

### <a name="train-a-model-with-labels"></a>Een model trainen met labels

U kunt aangepaste modellen ook trainen door de trainingsdocumenten handmatig te labelen. Training met labels leidt in sommige scenario's tot betere prestaties. Als u met labels wilt trainen, moet uw Blob Storage-container naast de trainingsdocumenten ook speciale labelinformatiebestanden ( *\<filename\>.pdf.labels.json*) bevatten. Het [hulpprogramma voor labelen van Form Recognizer](../../quickstarts/label-tool.md) beschikt over een gebruikersinterface die u kan helpen bij het maken van deze labelbestanden. Zodra u deze hebt, kunt u de methode **beginTraining** aanroepen met de parameter *uselabels* ingesteld op `true`.

```javascript
async function TrainModelWithLabelsAsync(
    const poller = await trainingClient.beginTraining(trainingDataUrl, true, {
        onProgress: (state) => {
            console.log(`training status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const model = poller.getResult();
    
    return model.modelId;
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Formulieren analyseren met een aangepast model

In deze sectie ziet u hoe u sleutel-/waarde-informatie en andere inhoud uit uw aangepaste formuliertypen kunt uitpakken met behulp van modellen die u hebt getraind met uw eigen formulieren.

> [!IMPORTANT]
> Als u dit scenario wilt implementeren, moet u al een model hebben getraind, zodat u de id ervan kunt doorgeven aan onderstaande methode. Zie de sectie [Een model trainen](#train-a-model-without-labels).

U gebruikt de methode **beginRecognizeCustomFormsFromUrl**. De geretourneerde waarde is een verzameling **RecognizedForm**-objecten: één voor elke pagina in het ingediende document.

```javascript
// Analyze PDF form document at an accessible URL
async function AnalyzePdfForm(client, modelId, formUrl)
{    
    const poller = await client.beginRecognizeCustomFormsFromUrl(modelId, formUrl, {
        onProgress: (state) => {
            console.log(`status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
```

Met de volgende code worden de resultaten van de analyse op de console weergegeven. Alle herkende velden en bijbehorende waarden worden afgedrukt, samen met een betrouwbaarheidsscore.

```javascript
    console.log("Fields:");
    for (const fieldName in form.fields) {
        // each field is of type FormField
        const field = form.fields[fieldName];
        console.log(
            `Field ${fieldName} has value '${field.value}' with a confidence score of ${field.confidence}`
        );
    }
}
```

## <a name="manage-your-custom-models"></a>Uw aangepaste modellen beheren

In deze sectie wordt beschreven hoe u de aangepaste modellen beheert die zijn opgeslagen in uw account. Als voorbeeld worden met de volgende code alle modelbeheertaken in één functie uitgevoerd. Begin met het kopiëren van de onderstaande functiehandtekening:

```javascript
async function ManageModels(trainingClient, trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Het aantal modellen in het FormRecognizer-resourceaccount controleren

In het volgende codeblok wordt het aantal modellen gecontroleerd dat u in uw Form Recognizer-account hebt opgeslagen en wordt dit aantal vergeleken met de limiet voor het account.

```csharp
    // First, we see how many custom models we have, and what our limit is
    const accountProperties = await trainingClient.getAccountProperties();
    console.log(
        `Our account has ${accountProperties.customModelCount} custom models, and we can have at most ${accountProperties.customModelLimit} custom models`
    );
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>De modellen weergeven die momenteel zijn opgeslagen in het resource-account

In het volgende codeblok worden de huidige modellen in uw account vermeld en worden de details ervan in de console weergegeven. Er wordt ook een verwijzing naar het eerste model opgeslagen.

```javascript
    // Next, we get a paged async iterator of all of our custom models
    const result = trainingClient.listCustomModels();

    // We could print out information about first ten models
    // and save the first model id for later use
    let i = 0;
    let firstModel;
    for await (const model of result) {
        console.log(`model ${i++}:`);
        console.log(model);
        if (i === 1) {
            firstModel = model;
        }
        if (i > 10) {
            break;
        }
    }
```

### <a name="get-a-specific-model-using-the-models-id"></a>Een specifiek model ophalen met de id van het model

Het volgende codeblok maakt gebruik van de model-id die is opgeslagen in de vorige sectie en gebruikt deze om details over het model op te halen.

```csharp
    // Now we'll get the first custom model in the paged list
    const model = await trainingClient.getModel(firstModel.modelId);
    console.log(`Model Id: ${model.modelId}`);
    console.log(`Status: ${model.status}`);
    console.log("Documents used in training: [");
    for (const doc of model.trainingDocuments || []) {
        console.log(`  ${doc.documentName}`);
    }
    console.log("]");
```

### <a name="delete-a-model-from-the-resource-account"></a>Een model uit het resourceaccount verwijderen

U kunt een model ook uit uw account verwijderen door naar de id te verwijzen. Met deze code wordt het model verwijderd dat in de vorige sectie is gebruikt.

```javascript
    await client.deleteModel(firstModel.modelId);
    try {
        const deleted = await trainingClient.deleteModel(firstModel.modelId);
        console.log(deleted);
    } catch (err) {
        // Expected
        console.log(`Model with id ${firstModel.modelId} has been deleted`);
    }
}
```


## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `node` in uw quickstart-bestand.

```console
node index.js
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="enable-logs"></a>Logboeken inschakelen
U kunt de volgende omgevingsvariabele instellen om logboeken voor foutopsporing weer te geven wanneer u deze bibliotheek gebruikt.

```
export DEBUG=azure*
```

Raadpleeg de [@azure/logger pakket-documenten](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger) voor gedetailleerdere instructies over het inschakelen van logboeken.


## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de clientbibliotheek van Form Recognizer voor Python gebruikt om modellen te trainen en formulieren op verschillende manieren te analyseren. Vervolgens leert u tips voor het maken van een betere set met trainingsgegevens en het produceren van nauwkeurigere modellen.

> [!div class="nextstepaction"]
> [Een set met trainingsgegevens samenstellen](../../build-training-data-set.md)

* [Wat is Form Recognizer?](../../overview.md)
* De voorbeeldcode uit deze gids (en meer) is te vinden op [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples).