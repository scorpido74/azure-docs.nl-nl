---
title: 'Snelstartgids: client bibliotheek voor formulier herkenning voor Java'
description: In deze Snelstartgids gaat u aan de slag met de formulier Recognizer-client bibliotheek voor Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/15/2020
ms.author: pafarley
ms.openlocfilehash: 6ff56ca61304bdacb3512156babd637afd337c7e
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242188"
---
[Referentie documentatie](https://docs.microsoft.com/java/api/overview/azure/formrecognizer)  |  [Bron code](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src)  |  van bibliotheek [Pakket (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)  |  Voor [beelden](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* Een Azure Storage-blob die een set trainings gegevens bevat. Zie [een trainings gegevensverzameling bouwen voor een aangepast model](../../build-training-data-set.md) voor tips en opties voor het samen stellen van uw trainings gegevensverzameling. Voor deze Quick Start kunt u de bestanden in de map **Train** van de voor [beeld-gegevensset](https://go.microsoft.com/fwlink/?linkid=2090451)gebruiken.
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Het [hulp programma Gradle build](https://gradle.org/install/)of een andere afhankelijkheids Manager.

## <a name="setting-up"></a>Instellen

### <a name="create-a-form-recognizer-azure-resource"></a>Een Azure-resource voor een formulier herkenning maken

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Omgevingsvariabelen maken

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-gradle-project"></a>Een nieuw Gradle-project maken


Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor uw app en navigeer ernaar. 

```console
mkdir myapp && cd myapp
```

Voer de `gradle init` opdracht uit vanuit de werkmap. Met deze opdracht worden essentiële build-bestanden gemaakt voor Gradle, waaronder *Build. Gradle. KTS* , dat tijdens runtime wordt gebruikt om uw toepassing te maken en te configureren.

```console
gradle init --type basic
```

Wanneer u wordt gevraagd om een **DSL** te kiezen, selecteert u **Kotlin**.

Maak een map voor uw voor beeld-app. Voer de volgende opdracht uit in de werkmap:

```console
mkdir -p src/main/java
```

Ga naar de nieuwe map en maak een bestand met de naam *formrecognizer-QuickStart. java*. Open het bestand in uw voorkeurs editor of IDE en voeg de volgende- `import` instructies toe:

```java
import Azure.AI.FormRecognizer;
import Azure.AI.FormRecognizer.Models;

import java.util.concurrent.atomic.AtomicReference;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.util.Context;
```

Maak in de methode van de toepassing `main` variabelen voor het Azure-eind punt en de sleutel van uw resource. Als u de omgevings variabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabele. U definieert de methoden later.


```java
public static void Main(string[] args)
{
    String key = System.getenv("FORM_RECOGNIZER_KEY");
    String endpoint = System.getenv("FORM_RECOGNIZER_ENDPOINT");
}
```

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Deze Snelstartgids maakt gebruik van de Gradle dependency Manager. U vindt de client bibliotheek en informatie voor andere afhankelijkheids managers in de [centrale maven-opslag plaats](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

Zorg er in het bestand *Build. gradle. KTS* van het project voor dat u de client bibliotheek als een instructie opneemt `implementation` . 

```kotlin
dependencies {
    implementation group: 'com.azure', name: 'azure-ai-formrecognizer', version: '1.0.0-beta.3'
}
```

<!-- 
    Object model tbd
-->

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de formulier Recognizer-client bibliotheek voor Java:

* [De client verifiëren](#authenticate-the-client)
* [Formulier inhoud herkennen](#recognize-form-content)
* [Bevestigingen herkennen](#recognize-receipts)
* [Aangepast model trainen](#train-a-custom-model)
* [Formulieren analyseren met een aangepast model](#analyze-forms-with-a-custom-model)
* [Uw aangepaste modellen beheren](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>De client verifiëren

`Main`Voeg de volgende code toe in de-methode. Hier verifieert u twee client objecten met de abonnements variabelen die u hierboven hebt gedefinieerd. U gebruikt een **AzureKeyCredential** -object, zodat u, indien nodig, de API-sleutel kunt bijwerken zonder nieuwe client objecten te maken.

```java
FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder()
    .credential(new AzureKeyCredential("{key}"))
    .endpoint("{endpoint}")
    .buildClient();
    
FormTrainingClient trainingClient = recognizerClient.getFormTrainingClient();
```

### <a name="call-client-specific-methods"></a>Client-specifieke methoden aanroepen

In het volgende code blok wordt gebruikgemaakt van de client-objecten voor het aanroepen van methoden voor elk van de belangrijkste taken in de formulier Recognizer SDK. U definieert deze methoden later op.

U moet ook verwijzingen toevoegen aan de Url's voor uw training en gegevens testen. 
* Als u de SAS-URL voor uw aangepaste model trainings gegevens wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechter muisknop op uw container en selecteert u **gedeelde toegangs handtekening ophalen**. Zorg ervoor dat de machtigingen **lezen** en **lijst** zijn ingeschakeld en klik op **maken**. Kopieer vervolgens de waarde in de sectie **URL** . Het moet de volgende indeling hebben: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Als u een URL wilt ophalen van een formulier dat u wilt testen, kunt u de bovenstaande stappen gebruiken om de SAS-URL van een afzonderlijk document in Blob Storage op te halen. U kunt ook de URL van een document naar een andere locatie halen.
* Gebruik de bovenstaande methode om ook de URL van een kwitantie-installatie kopie op te halen.

> [!NOTE]
> De code fragmenten in deze hand leiding gebruiken externe formulieren die worden gebruikt voor Url's. Als u in plaats daarvan lokale formulier documenten wilt verwerken, raadpleegt u de bijbehorende methoden in de [referentie documentatie](https://docs.microsoft.com/java/api/overview/azure/formrecognizer).

```java
    string trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    string formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
    string receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media"
    + "/contoso-allinone.jpg";

    // Call Form Recognizer scenarios:
    System.out.println("Get form content...");
    GetContent(recognizerClient, formUrl);

    System.out.println("Analyze receipt...");
    AnalyzeReceipt(recognizerClient, receiptUrl);

    System.out.println("Train Model with training data...");
    modelId = TrainModel(trainingClient, trainingDataUrl);

    System.out.println("Analyze PDF form...");
    AnalyzePdfForm(recognizerClient, modelId, formUrl);

    System.out.println("Manage models...");
    ManageModels(trainingClient, trainingDataUrl) ;
```


## <a name="recognize-form-content"></a>Formulier inhoud herkennen

U kunt de formulier Recognizer gebruiken om tabellen, lijnen en woorden in documenten te herkennen, zonder dat u een model hoeft te trainen.

Gebruik de methode **beginRecognizeContentFromUrl** om de inhoud van een bestand op een bepaalde URI te herkennen.

```java
private static void GetContent(
    FormRecognizerClient recognizerClient, String invoiceUri)
{
    String analyzeFilePath = invoiceUri;
    SyncPoller<OperationResult, List<FormPage>> recognizeContentPoller =
        recognizerClient.beginRecognizeContentFromUrl(analyzeFilePath);
    
    List<FormPage> contentResult = recognizeContentPoller.getFinalResult();
```

De geretourneerde waarde is een verzameling **FormPage** -objecten: één voor elke pagina in het verzonden document. Met de volgende code worden deze objecten door lopen en worden de geëxtraheerde sleutel-waardeparen en tabel gegevens afgedrukt.

```java
    contentResult.forEach(formPage -> {
        // Table information
        System.out.println("----Recognizing content ----");
        System.out.printf("Has width: %d and height: %d, measured with unit: %s.%n", formPage.getWidth(),
            formPage.getHeight(),
            formPage.getUnit());
        formPage.getTables().forEach(formTable -> {
            System.out.printf("Table has %d rows and %d columns.%n", formTable.getRowCount(),
                formTable.getColumnCount());
            formTable.getCells().forEach(formTableCell -> {
                System.out.printf("Cell has text %s.%n", formTableCell.getText());
            });
            System.out.println();
        });
    });
}
```

## <a name="recognize-receipts"></a>Bevestigingen herkennen

In deze sectie wordt beschreven hoe u algemene velden van Amerikaanse ontvangsten kunt herkennen en extra heren met behulp van een vooraf getraind ontvangst model.

Als u ontvangst bewijzen wilt herkennen vanuit een URI, gebruikt u de methode **beginRecognizeReceiptsFromUrl** . De geretourneerde waarde is een verzameling **RecognizedReceipt** -objecten: één voor elke pagina in het verzonden document.

```java
private static void AnalyzeReceipt(
    FormRecognizerClient recognizerClient, string receiptUri)
{
    SyncPoller<OperationResult, List<RecognizedReceipt>> syncPoller =
        formRecognizerClient.beginRecognizeReceiptsFromUrl(receiptUri);
    List<RecognizedReceipt> receiptPageResults = syncPoller.getFinalResult();
```

In het volgende code blok wordt door de bevestigingen herhaald en worden de details ervan naar de console afgedrukt.

```java
    for (int i = 0; i < receiptPageResults.size(); i++) {
        RecognizedReceipt recognizedReceipt = receiptPageResults.get(i);
        Map<String, FormField> recognizedFields = recognizedReceipt.getRecognizedForm().getFields();
        System.out.printf("----------- Recognized Receipt page %d -----------%n", i);
        FormField merchantNameField = recognizedFields.get("MerchantName");
        if (merchantNameField != null) {
            if (merchantNameField.getFieldValue().getType() == FieldValueType.STRING) {
                System.out.printf("Merchant Name: %s, confidence: %.2f%n",
                    merchantNameField.getFieldValue().asString(),
                    merchantNameField.getConfidence());
            }
        }
        FormField merchantAddressField = recognizedFields.get("MerchantAddress");
        if (merchantAddressField != null) {
            if (merchantAddressField.getFieldValue().getType() == FieldValueType.STRING) {
                System.out.printf("Merchant Address: %s, confidence: %.2f%n",
                    merchantAddressField.getFieldValue().asString(),
                    merchantAddressField.getConfidence());
            }
        }
        FormField transactionDateField = recognizedFields.get("TransactionDate");
        if (transactionDateField != null) {
            if (transactionDateField.getFieldValue().getType() == FieldValueType.DATE) {
                System.out.printf("Transaction Date: %s, confidence: %.2f%n",
                    transactionDateField.getFieldValue().asDate(),
                    transactionDateField.getConfidence());
            }
        }
```
In het volgende code blok wordt de afzonderlijke items herhaald die op de ontvangst zijn gedetecteerd en worden de details ervan naar de console afgedrukt.

```java
        FormField receiptItemsField = recognizedFields.get("Items");
        if (receiptItemsField != null) {
            System.out.printf("Receipt Items: %n");
            if (receiptItemsField.getFieldValue().getType() == FieldValueType.LIST) {
                List<FormField> receiptItems = receiptItemsField.getFieldValue().asList();
                receiptItems.forEach(receiptItem -> {
                    if (receiptItem.getFieldValue().getType() == FieldValueType.MAP) {
                        receiptItem.getFieldValue().asMap().forEach((key, formField) -> {
                            if (key.equals("Name")) {
                                if (formField.getFieldValue().getType() == FieldValueType.STRING) {
                                    System.out.printf("Name: %s, confidence: %.2fs%n",
                                        formField.getFieldValue().asString(),
                                        formField.getConfidence());
                                }
                            }
                            if (key.equals("Quantity")) {
                                if (formField.getFieldValue().getType() == FieldValueType.INTEGER) {
                                    System.out.printf("Quantity: %d, confidence: %.2f%n",
                                        formField.getFieldValue().asInteger(), formField.getConfidence());
                                }
                            }
                            if (key.equals("Price")) {
                                if (formField.getFieldValue().getType() == FieldValueType.FLOAT) {
                                    System.out.printf("Price: %f, confidence: %.2f%n",
                                        formField.getFieldValue().asFloat(),
                                        formField.getConfidence());
                                }
                            }
                            if (key.equals("TotalPrice")) {
                                if (formField.getFieldValue().getType() == FieldValueType.FLOAT) {
                                    System.out.printf("Total Price: %f, confidence: %.2f%n",
                                        formField.getFieldValue().asFloat(),
                                        formField.getConfidence());
                                }
                            }
                        });
                    }
                });
            }
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

```java
private static String TrainModel(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    String trainingSetSource = "{unlabeled_training_set_SAS_URL}";
    SyncPoller<OperationResult, CustomFormModel> trainingPoller =
        formTrainingClient.beginTraining(trainingSetSource, false);
    
    CustomFormModel customFormModel = trainingPoller.getFinalResult();
    
    // Model Info
    System.out.printf("Model Id: %s%n", customFormModel.getModelId());
    System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
    System.out.printf("Model created on: %s%n", customFormModel.getCreatedOn());
    System.out.printf("Model last updated: %s%n%n", customFormModel.getCompletedOn());
```
Het geretourneerde **CustomFormModel** -object bevat informatie over de typen die het model kan herkennen en de velden die het kan ophalen van elk formulier type. In het volgende code blok wordt deze informatie in de-console afgedrukt.

```java 
    System.out.println("Recognized Fields:");
    // looping through the sub-models, which contains the fields they were trained on
    // Since the given training documents are unlabeled, we still group them but they do not have a label.
    customFormModel.getSubmodels().forEach(customFormSubModel -> {
        // Since the training data is unlabeled, we are unable to return the accuracy of this model
        customFormSubModel.getFieldMap().forEach((field, customFormModelField) ->
            System.out.printf("Field: %s Field Label: %s%n",
                field, customFormModelField.getLabel()));
    });
```

Ten slotte retourneert deze methode de unieke ID van het model.

```java
    return customFormModel.getModelId();
}
```

### <a name="train-a-model-with-labels"></a>Een model trainen met labels

U kunt ook aangepaste modellen trainen door de trainings documenten hand matig te labelen. Training met labels leidt tot betere prestaties in sommige scenario's. Als u met labels wilt trainen, moet u in uw Blob Storage-container, naast de trainings documenten, speciale label-informatie bestanden (* \<filename\>.pdf.labels.jsaan*) hebben. Het [hulp programma voor het labelen](../../quickstarts/label-tool.md) van het voor beeld van een formulier herkenning biedt een gebruikers interface die u kan helpen bij het maken van deze label bestanden. Zodra u deze hebt, kunt u de **beginTraining** -methode aanroepen met de para meter *useTrainingLabels* ingesteld op `true` .

```java
private static String TrainModelWithLabels(
    FormRecognizerClient trainingClient, String trainingDataUrl)
{
    // Train custom model
    String trainingSetSource = trainingDataUrl;
    SyncPoller<OperationResult, CustomFormModel> trainingPoller = client.beginTraining(trainingSetSource, true);

    CustomFormModel customFormModel = trainingPoller.getFinalResult();

    // Model Info
    System.out.printf("Model Id: %s%n", customFormModel.getModelId());
    System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
    System.out.printf("Model created on: %s%n", customFormModel.getRequestedOn());
    System.out.printf("Model last updated: %s%n%n", customFormModel.getCompletedOn());
```

De geretourneerde **CustomFormModel** geeft aan welke velden het model kan ophalen, samen met de geschatte nauw keurigheid van elk veld. In het volgende code blok wordt deze informatie in de-console afgedrukt.

```java
    // looping through the sub-models, which contains the fields they were trained on
    // The labels are based on the ones you gave the training document.
    System.out.println("Recognized Fields:");
    // Since the data is labeled, we are able to return the accuracy of the model
    customFormModel.getSubmodels().forEach(customFormSubModel -> {
        System.out.printf("Sub-model accuracy: %.2f%n", customFormSubModel.getAccuracy());
        customFormSubModel.getFieldMap().forEach((label, customFormModelField) ->
            System.out.printf("Field: %s Field Name: %s Field Accuracy: %.2f%n",
                label, customFormModelField.getName(), customFormModelField.getAccuracy()));
    });
    return customFormModel.getModelId();
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Formulieren analyseren met een aangepast model

In deze sectie wordt gedemonstreerd hoe u sleutel/waarde-informatie en andere inhoud uit uw aangepaste formulier typen kunt extra heren met behulp van modellen die u hebt getraind met uw eigen formulieren.

> [!IMPORTANT]
> Als u dit scenario wilt implementeren, moet u al een model hebben getraind, zodat u de ID ervan kunt door geven naar de onderstaande methode. Zie de sectie [een model trainen](#train-a-model-without-labels) .

U gebruikt de methode **beginRecognizeCustomFormsFromUrl** . De geretourneerde waarde is een verzameling **RecognizedForm** -objecten: één voor elke pagina in het verzonden document.

```java
// Analyze PDF form data
private static void AnalyzePdfForm(
    FormRecognizerClient formClient, String modelId, String pdfFormUrl)
{    
    String modelId = modelId;
    SyncPoller<OperationResult, List<RecognizedForm>> recognizeFormPoller =
        client.beginRecognizeCustomFormsFromUrl(pdfFormUrl, modelId);

    List<RecognizedForm> recognizedForms = recognizeFormPoller.getFinalResult();
```

Met de volgende code worden de resultaten van de analyse naar de console afgedrukt. Alle herkende velden en bijbehorende waarden worden afgedrukt, samen met een betrouwbaarheids Score.

```java
    recognizedForms.forEach(form -> {
        System.out.println("----------- Recognized Form -----------");
        System.out.printf("Form type: %s%n", form.getFormType());
        form.getFields().forEach((label, formField) -> {
            System.out.printf("Field %s has value %s with confidence score of %.2f.%n", label,
                formField.getFieldValue(),
                formField.getConfidence());
        });
        System.out.print("-----------------------------------");
    });
}
```

## <a name="manage-your-custom-models"></a>Uw aangepaste modellen beheren

In deze sectie wordt beschreven hoe u de aangepaste modellen beheert die zijn opgeslagen in uw account. Met de volgende code worden alle model beheer taken in één methode als voor beeld gebruikt. Kopieer eerst de volgende methode hand tekening:

```java
private static void ManageModels(
    FormRecognizerClient trainingClient, String trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Het aantal modellen in het FormRecognizer-resource account controleren

In het volgende code blok wordt het aantal modellen gecontroleerd dat u hebt opgeslagen in uw formulier Recognizer-account en vergelijkt deze met de limiet voor het account.

```java
    AtomicReference<String> modelId = new AtomicReference<>();

    // First, we see how many custom models we have, and what our limit is
    AccountProperties accountProperties = client.getAccountProperties();
    System.out.printf("The account has %s custom models, and we can have at most %s custom models",
        accountProperties.getCustomModelCount(), accountProperties.getCustomModelLimit());
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>De modellen weer geven die momenteel zijn opgeslagen in het resource-account

In het volgende code blok worden de huidige modellen in uw account vermeld en worden de details ervan in de-console weer gegeven.

```java    
    // Next, we get a paged list of all of our custom models
    PagedIterable<CustomFormModelInfo> customModels = client.getModelInfos();
    System.out.println("We have following models in the account:");
    customModels.forEach(customFormModelInfo -> {
        System.out.printf("Model Id: %s%n", customFormModelInfo.getModelId());
        // get custom model info
        modelId.set(customFormModelInfo.getModelId());
        CustomFormModel customModel = client.getCustomModel(customFormModelInfo.getModelId());
        System.out.printf("Model Id: %s%n", customModel.getModelId());
        System.out.printf("Model Status: %s%n", customModel.getModelStatus());
        System.out.printf("Created on: %s%n", customModel.getRequestedOn());
        System.out.printf("Updated on: %s%n", customModel.getCompletedOn());
        customModel.getSubmodels().forEach(customFormSubModel -> {
            System.out.printf("Custom Model Form type: %s%n", customFormSubModel.getFormType());
            System.out.printf("Custom Model Accuracy: %.2f%n", customFormSubModel.getAccuracy());
            if (customFormSubModel.getFieldMap() != null) {
                customFormSubModel.getFieldMap().forEach((fieldText, customFormModelField) -> {
                    System.out.printf("Field Text: %s%n", fieldText);
                    System.out.printf("Field Accuracy: %.2f%n", customFormModelField.getAccuracy());
                });
            }

        });
    });
```

### <a name="delete-a-model-from-the-resource-account"></a>Een model uit het resource-account verwijderen

U kunt ook een model uit uw account verwijderen door te verwijzen naar de ID.

```java
    // Delete Custom Model
    System.out.printf("Deleted model with model Id: %s operation completed with status: %s%n", modelId.get(),
        client.deleteModelWithResponse(modelId.get(), Context.NONE).getStatusCode());
}
```


## <a name="run-the-application"></a>De toepassing uitvoeren

U kunt de app bouwen met:

```console
gradle build
```

Voer de toepassing uit met het `run` doel:

```console
gradle run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Problemen oplossen

Van Recognizer-clients veroorzaken `ErrorResponseException` uitzonde ringen. Als u bijvoorbeeld probeert een ongeldige bestands bron-URL op te geven, `ErrorResponseException` treedt er een fout op die aangeeft dat de oorzaak van de fout is mislukt. In het volgende code fragment wordt de fout op de juiste wijze verwerkt door de uitzonde ring op te vangen en de aanvullende informatie over de fout weer te geven.

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>Client logboek registratie inschakelen
Azure Sdk's voor java bieden een consistent verhaal van de logboek registratie om hulp te helpen bij het oplossen van toepassings fouten en het versnellen van de oplossing. Met de logboeken die zijn geproduceerd, wordt de stroom van een toepassing vastgelegd voordat de Terminal status wordt bereikt om het basis probleem te vinden. Raadpleeg de [wiki logboek registratie](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK) voor meer informatie over het inschakelen van logboek registratie.

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u de Java-client bibliotheek van de formulier herkenning gebruikt om modellen te trainen en formulieren op verschillende manieren te analyseren. Vervolgens leert u tips voor het maken van een betere set met trainings gegevens en het produceren van nauw keurige modellen.

> [!div class="nextstepaction"]
> [Een set met trainingsgegevens samenstellen](../../build-training-data-set.md)

* [Wat is Form Recognizer?](../../overview.md)
* De voorbeeld code uit deze hand leiding is te vinden op [github](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples).