---
title: 'Snelstartgids: client bibliotheek voor formulier herkenning voor Java script'
description: In deze Snelstartgids gaat u aan de slag met de formulier Recognizer-client bibliotheek voor Java script.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/08/2020
ms.author: pafarley
ms.openlocfilehash: c24f82d48a1452cdb272abca178a9ba924aacc20
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997586"
---
[Referentie documentatie](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer?view=azure-node-preview)  |  [Bron code](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/)  |  van bibliotheek [Pakket (NPM)](https://www.npmjs.com/package/@azure/ai-form-recognizer)  |  Voor [beelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* Een Azure Storage-blob die een set trainings gegevens bevat. Zie [een trainings gegevensverzameling bouwen voor een aangepast model](../../build-training-data-set.md) voor tips en opties voor het samen stellen van uw trainings gegevensverzameling. Voor deze Quick Start kunt u de bestanden in de map **Train** van de voor [beeld-gegevensset](https://go.microsoft.com/fwlink/?linkid=2090451)gebruiken.
* De huidige versie van [node. js](https://nodejs.org/)

## <a name="setting-up"></a>Instellen

### <a name="create-a-form-recognizer-azure-resource"></a>Een Azure-resource voor een formulier herkenning maken

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Omgevingsvariabelen maken

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp && cd myapp
```

Voer de `npm init` opdracht uit om een knooppunt toepassing met een `package.json` bestand te maken. 

```console
npm init
```

Maak een bestand met de naam `index.js` , open het en importeer de volgende bibliotheken:

```javascript
const { FormRecognizerClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");
const fs = require("fs");
```

Laad ook het omgevings variabelebestand.

```javascript
// Load the .env file if it exists
require("dotenv").config();
```

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Installeer de `ai-form-recognizer` en `dotenv` NPM-pakketten:

```console
npm install @azure/ai-form-recognizer dotenv
```

Het bestand van uw app `package.json` wordt bijgewerkt met de afhankelijkheden.


<!-- 
    Object model
-->

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de formulier Recognizer-client bibliotheek voor Java script:

* [De client verifiëren](#authenticate-the-client)
* [Formulier inhoud herkennen](#recognize-form-content)
* [Bevestigingen herkennen](#recognize-receipts)
* [Aangepast model trainen](#train-a-custom-model)
* [Formulieren analyseren met een aangepast model](#analyze-forms-with-a-custom-model)
* [Uw aangepaste modellen beheren](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>De client verifiëren

Maak in de `main` functie variabelen voor het Azure-eind punt en de sleutel van uw resource. Als u de omgevings variabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabele.

```javascript
// You will need to set these environment variables or edit the following values
const endpoint = process.env["FORM_RECOGNIZER_ENDPOINT"] || "<cognitive services endpoint>";
const apiKey = process.env["FORM_RECOGNIZER_KEY"] || "<api key>";
```

Verifieer vervolgens een client object met de abonnements variabelen die u hebt gedefinieerd. U gebruikt een **AzureKeyCredential** -object, zodat u, indien nodig, de API-sleutel kunt bijwerken zonder nieuwe client objecten te maken. U maakt ook een trainings-client object.

```javascript
const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));

const trainingClient = client.getFormTrainingClient();
```

### <a name="call-client-specific-functions"></a>Client-specifieke functies aanroepen

In het volgende code blok in `main` worden de client-objecten gebruikt voor het aanroepen van functies voor elk van de belangrijkste taken in de formulier Recognizer SDK. U definieert deze functies later op.

U moet ook verwijzingen toevoegen aan de Url's voor uw training en gegevens testen.
* Als u de SAS-URL voor uw aangepaste model trainings gegevens wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechter muisknop op uw container en selecteert u **gedeelde toegangs handtekening ophalen**. Zorg ervoor dat de machtigingen **lezen** en **lijst** zijn ingeschakeld en klik op **maken**. Kopieer vervolgens de waarde in de sectie **URL** . Het moet de volgende indeling hebben: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Als u een URL wilt ophalen van een formulier dat u wilt testen, kunt u de bovenstaande stappen gebruiken om de SAS-URL van een afzonderlijk document in Blob Storage op te halen. U kunt ook de URL van een document naar een andere locatie halen.
* Gebruik de bovenstaande methode om de URL van een kopie van de bevestiging te verkrijgen, of gebruik de URL van de voor beeld-installatie kopie.

> [!NOTE]
> De code fragmenten in deze hand leiding gebruiken externe formulieren die worden gebruikt voor Url's. Als u in plaats daarvan lokale formulier documenten wilt verwerken, raadpleegt u de bijbehorende methoden in de [referentie documentatie](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer?view=azure-node-preview).


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

## <a name="recognize-form-content"></a>Formulier inhoud herkennen

U kunt de formulier Recognizer gebruiken om tabellen, lijnen en woorden in documenten te herkennen, zonder dat u een model hoeft te trainen. Gebruik de methode **beginRecognizeContentFromUrl** om de inhoud van een bestand op een bepaalde URI te herkennen.

```javascript
async function GetContent( recognizerClient, invoiceUri)
{
    const poller = await client.beginRecognizeContentFromUrl(invoiceUri);
    await poller.pollUntilDone();
    const response = poller.getResult();
```

De geretourneerde waarde is een verzameling **FormPage** -objecten: één voor elke pagina in het verzonden document. Met de volgende code worden deze objecten door lopen en worden de geëxtraheerde sleutel-waardeparen en tabel gegevens afgedrukt.

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

## <a name="recognize-receipts"></a>Bevestigingen herkennen

In deze sectie wordt beschreven hoe u algemene velden van Amerikaanse ontvangsten kunt herkennen en extra heren met behulp van een vooraf getraind ontvangst model.

Als u ontvangst bewijzen wilt herkennen vanuit een URI, gebruikt u de methode **beginRecognizeReceiptsFromUrl** . De geretourneerde waarde is een verzameling **RecognizedReceipt** -objecten: één voor elke pagina in het verzonden document. Met de volgende code wordt een ontvangst bij de opgegeven URI verwerkt en worden de belangrijkste velden en waarden naar de console afgedrukt.

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


    const usReceipt = response.receipts[0];
    console.log("First receipt:");
    console.log(`Receipt type: ${usReceipt.receiptType}`);
    console.log(
        `Merchant Name: ${usReceipt.merchantName.value} (confidence: ${usReceipt.merchantName.confidence})`
    );
    console.log(
        `Transaction Date: ${usReceipt.transactionDate.value} (confidence: ${usReceipt.transactionDate.confidence})`
    );
    console.log("Receipt items:");
    console.log(`  name\tprice\tquantity\ttotalPrice`);
```

In het volgende code blok wordt de afzonderlijke items herhaald die op de ontvangst zijn gedetecteerd en worden de details ervan naar de console afgedrukt.

```csharp
    for (const item of usReceipt.items) {
        const name = `${optionalToString(item.name.value)} (confidence: ${optionalToString(
            item.name.confidence
        )})`;
        const price = `${optionalToString(item.price.value)} (confidence: ${optionalToString(
            item.price.confidence
        )})`;
        const quantity = `${optionalToString(item.quantity.value)} (confidence: ${optionalToString(
            item.quantity.confidence
        )})`;
        const totalPrice = `${optionalToString(item.totalPrice.value)} (confidence: ${optionalToString(
            item.totalPrice.confidence
        )})`;
        console.log(`  ${name}\t${price}\t${quantity}\t${totalPrice}`);
    }
```

Deze functie maakt gebruik van een hulp functie `optionalToString` . Deze functie definiëren in de hoofdmap van het script:

```javascript
function optionalToString(value) {
  return `${value || "<missing>"}`;
}
```


## <a name="train-a-custom-model"></a>Aangepast model trainen

In deze sectie ziet u hoe u een model kunt trainen met uw eigen gegevens. Met een getraind model kunnen gestructureerde gegevens worden uitgevoerd die de sleutel/waarde-relaties in het oorspronkelijke formulier document bevatten. Nadat u het model hebt getraind, kunt u het testen en opnieuw trainen en uiteindelijk gebruiken om gegevens te extra heren uit meer formulieren, afhankelijk van uw behoeften.

> [!NOTE]
> U kunt ook modellen trainen met een Graphical User Interface zoals het [hulp programma voor het labelen](../../quickstarts/label-tool.md)van het voor beeld van de formulier herkenning.

### <a name="train-a-model-without-labels"></a>Een model trainen zonder labels

Train aangepaste modellen om alle velden en waarden te herkennen die in uw aangepaste formulieren worden gevonden zonder hand matig labels te krijgen voor de trainings documenten.

Met de volgende functie wordt een model voor een bepaalde set documenten treinen en wordt de status van het model in de console afgedrukt. 

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
    console.log(`Created on: ${response.createdOn}`);
    console.log(`Last modified: ${response.lastModified}`);
```

Het geretourneerde **CustomFormModel** -object bevat informatie over de typen die het model kan herkennen en de velden die het kan ophalen van elk formulier type. In het volgende code blok wordt deze informatie in de-console afgedrukt.

```javascript
    if (response.models) {
        for (const submodel of response.models) {
            // since the training data is unlabeled, we are unable to return the accuracy of this model
            console.log("We have recognized the following fields");
            for (const key in submodel.fields) {
                const field = submodel.fields[key];
                console.log(`The model found field '${field.name}'`);
            }
        }
    }
```

Ten slotte retourneert deze methode de unieke ID van het model.

```csharp
    return response.modelId;
}
```

### <a name="train-a-model-with-labels"></a>Een model trainen met labels

U kunt ook aangepaste modellen trainen door de trainings documenten hand matig te labelen. Training met labels leidt tot betere prestaties in sommige scenario's. Als u met labels wilt trainen, moet u in uw Blob Storage-container een speciale label-informatie bestand (* \<filename\> . PDF. labels. json*) hebben. Het [hulp programma voor het labelen](../../quickstarts/label-tool.md) van het voor beeld van een formulier herkenning biedt een gebruikers interface die u kan helpen bij het maken van deze label bestanden. Zodra u deze hebt, kunt u de **beginTraining** -methode aanroepen met de para meter *uselabels* ingesteld op `true` .

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

In deze sectie wordt gedemonstreerd hoe u sleutel/waarde-informatie en andere inhoud uit uw aangepaste formulier typen kunt extra heren met behulp van modellen die u hebt getraind met uw eigen formulieren.

> [!IMPORTANT]
> Als u dit scenario wilt implementeren, moet u al een model hebben getraind, zodat u de ID ervan kunt door geven naar de onderstaande methode. Zie de sectie [een model trainen](#train-a-model-without-labels) .

U gebruikt de methode **beginRecognizeFormsFromUrl** . De geretourneerde waarde is een verzameling **RecognizedForm** -objecten: één voor elke pagina in het verzonden document.

```javascript
// Analyze PDF form document at an accessible URL
async function AnalyzePdfForm(client, modelId, formUrl)
{    
    const poller = await client.beginRecognizeFormsFromUrl(modelId, formUrl, {
        onProgress: (state) => {
            console.log(`status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
```

Met de volgende code worden de resultaten van de analyse naar de console afgedrukt. Alle herkende velden en bijbehorende waarden worden afgedrukt, samen met een betrouwbaarheids Score.

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

In deze sectie wordt beschreven hoe u de aangepaste modellen beheert die zijn opgeslagen in uw account. Met de volgende code worden alle model beheer taken in één functie als voor beeld gebruikt. Begin met het kopiëren van de onderstaande functie handtekening:

```javascript
async function ManageModels(trainingClient, trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Het aantal modellen in het FormRecognizer-resource account controleren

In het volgende code blok wordt het aantal modellen gecontroleerd dat u hebt opgeslagen in uw formulier Recognizer-account en vergelijkt deze met de limiet voor het account.

```csharp
    // First, we see how many custom models we have, and what our limit is
    const accountProperties = await trainingClient.getAccountProperties();
    console.log(
        `Our account has ${accountProperties.count} custom models, and we can have at most ${accountProperties.limit} custom models`
    );
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>De modellen weer geven die momenteel zijn opgeslagen in het resource-account

In het volgende code blok worden de huidige modellen in uw account vermeld en worden de details ervan in de-console weer gegeven. Er wordt ook een verwijzing naar het eerste model opgeslagen.

```javascript
    // Next, we get a paged async iterator of all of our custom models
    const result = trainingClient.listModels();

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

### <a name="get-a-specific-model-using-the-models-id"></a>Een specifiek model ophalen met de ID van het model

Het volgende code blok maakt gebruik van de model-ID die is opgeslagen in de vorige sectie en gebruikt deze om details over het model op te halen.

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

### <a name="delete-a-model-from-the-resource-account"></a>Een model uit het resource-account verwijderen

U kunt ook een model uit uw account verwijderen door te verwijzen naar de ID. Met deze code wordt het model dat in de vorige sectie wordt gebruikt, verwijderd.

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

Voer de toepassing uit met de `node` opdracht in uw Quick Start-bestand.

```console
node index.js
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="enable-logs"></a>Logboeken inschakelen
U kunt de volgende omgevings variabele instellen om logboeken voor fout opsporing weer te geven wanneer u deze bibliotheek gebruikt.

```
export DEBUG=azure*
```

Zie het [ @azure/logger pakket docs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger)voor meer gedetailleerde instructies voor het inschakelen van Logboeken.


## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u de python-client bibliotheek voor de formulier herkenning gebruikt om modellen te trainen en formulieren op verschillende manieren te analyseren. Vervolgens leert u tips voor het maken van een betere set met trainings gegevens en het produceren van nauw keurige modellen.

> [!div class="nextstepaction"]
> [Een set met trainingsgegevens samenstellen](../../build-training-data-set.md)

* [Wat is Form Recognizer?](../../overview.md)
* De voorbeeld code uit deze hand leiding is te vinden op [github](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples).