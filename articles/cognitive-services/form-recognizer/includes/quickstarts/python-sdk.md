---
title: 'Snelstartgids: client bibliotheek voor formulier herkenning voor python'
description: In deze Snelstartgids gaat u aan de slag met de formulier Recognizer-client bibliotheek voor python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/15/2020
ms.author: pafarley
ms.openlocfilehash: fafb16d1f8c37eab7ebaaeacb3a3e9b218347a04
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85112039"
---
[Referentie documentatie](https://docs.microsoft.com/python/api/overview/azure/formrecognizer?view=azure-python-preview)  |  [Bron code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer)  |  van bibliotheek [Pakket (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/)  |  Voor [beelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* Een Azure Storage-blob die een set trainings gegevens bevat. Zie [een trainings gegevensverzameling bouwen voor een aangepast model](../../build-training-data-set.md) voor tips en opties voor het samen stellen van uw trainings gegevensverzameling. Voor deze Quick Start kunt u de bestanden in de map **Train** van de voor [beeld-gegevensset](https://go.microsoft.com/fwlink/?linkid=2090451)gebruiken.
* [Python 2,7 of 3,5 of hoger](https://www.python.org/)

## <a name="setting-up"></a>Instellen

### <a name="create-a-form-recognizer-azure-resource"></a>Een Azure-resource voor een formulier herkenning maken

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Omgevingsvariabelen maken

[!INCLUDE [environment-variables](../environment-variables.md)]


### <a name="create-a-new-python-application"></a>Een nieuwe python-toepassing maken

Maak een nieuwe python-toepassing in uw voorkeurs editor of IDE. Importeer vervolgens de volgende bibliotheken.

```python
import os
import azure.ai.formrecognizer
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import ResourceNotFoundError
```

Maak variabelen voor het Azure-eind punt en de sleutel van uw resource. Als u de omgevings variabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabele.

```python
endpoint = os.environ["FORM_RECOGNIZER_ENDPOINT"]
key = os.environ["FORM_RECOGNIZER_KEY"]
```

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Na de installatie van python kunt u de client bibliotheek installeren met:

```console
pip install azure_ai_formrecognizer
```

<!-- 
tbd object model
-->

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de formulier Recognizer-client bibliotheek voor python:

* [De client verifiëren](#authenticate-the-client)
* [Formulier inhoud herkennen](#recognize-form-content)
* [Bevestigingen herkennen](#recognize-receipts)
* [Aangepast model trainen](#train-a-custom-model)
* [Formulieren analyseren met een aangepast model](#analyze-forms-with-a-custom-model)
* [Uw aangepaste modellen beheren](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>De client verifiëren

Hier verifieert u twee client objecten met de abonnements variabelen die u hierboven hebt gedefinieerd. U gebruikt een **AzureKeyCredential** -object, zodat u, indien nodig, de API-sleutel kunt bijwerken zonder nieuwe client objecten te maken.

```python
form_recognizer_client = FormRecognizerClient(endpoint=self.endpoint, credential=AzureKeyCredential(self.key))

form_training_client = FormTrainingClient(self.endpoint, AzureKeyCredential(self.key))
```

## <a name="define-variables"></a>Variabelen definiëren

> [!NOTE]
> De code fragmenten in deze hand leiding gebruiken externe formulieren die worden gebruikt voor Url's. Als u in plaats daarvan lokale formulier documenten wilt verwerken, raadpleegt u de bijbehorende methoden in de [referentie documentatie](https://docs.microsoft.com/python/api/overview/azure/formrecognizer?view=azure-python-preview) en voor [beelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

U moet ook verwijzingen toevoegen aan de Url's voor uw training en gegevens testen.
* Als u de SAS-URL voor uw aangepaste model trainings gegevens wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechter muisknop op uw container en selecteert u **gedeelde toegangs handtekening ophalen**. Zorg ervoor dat de machtigingen **lezen** en **lijst** zijn ingeschakeld en klik op **maken**. Kopieer vervolgens de waarde in de sectie **URL** . Het moet de volgende indeling hebben: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Als u een URL wilt ophalen van een formulier dat u wilt testen, kunt u de bovenstaande stappen gebruiken om de SAS-URL van een afzonderlijk document in Blob Storage op te halen. U kunt ook de URL van een document naar een andere locatie halen.
* Gebruik de bovenstaande methode om de URL van een kopie van de bevestiging te verkrijgen, of gebruik de URL van de voor beeld-installatie kopie.

```python
trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>"
formUrl = "<SAS-URL-of-a-form-in-blob-storage>"
receiptUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/receipt/contoso-receipt.png"
```

## <a name="recognize-form-content"></a>Formulier inhoud herkennen

U kunt de formulier Recognizer gebruiken om tabellen, lijnen en woorden in documenten te herkennen, zonder dat u een model hoeft te trainen.

Gebruik de methode **begin_recognize_content** om de inhoud van een bestand op een bepaalde URL te herkennen.

```Python
poller = form_recognizer_client.begin_recognize_content_from_url(formUrl)
contents = poller.result()
```

De geretourneerde waarde is een verzameling **FormPage** -objecten: één voor elke pagina in het verzonden document. Met de volgende code worden deze objecten door lopen en worden de geëxtraheerde sleutel-waardeparen en tabel gegevens afgedrukt.

```python
for idx, content in enumerate(contents):
    print("----Recognizing content from page #{}----".format(idx))
    print("Has width: {} and height: {}, measured with unit: {}".format(
        content.width,
        content.height,
        content.unit
    ))
    for table_idx, table in enumerate(content.tables):
        print("Table # {} has {} rows and {} columns".format(table_idx, table.row_count, table.column_count))
        for cell in table.cells:
            print("...Cell[{}][{}] has text '{}' within bounding box '{}'".format(
                cell.row_index,
                cell.column_index,
                cell.text,
                format_bounding_box(cell.bounding_box)
            ))
    for line_idx, line in enumerate(content.lines):
        print("Line # {} has word count '{}' and text '{}' within bounding box '{}'".format(
            line_idx,
            len(line.words),
            line.text,
            format_bounding_box(line.bounding_box)
        ))
    print("----------------------------------------")
```

De bovenstaande code maakt gebruik van een hulp functie `format_bounding_box` om de coördinaten van een selectie kader te vereenvoudigen. Afzonderlijk definiëren:

```python
def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])
```

## <a name="recognize-receipts"></a>Bevestigingen herkennen

In deze sectie wordt beschreven hoe u algemene velden van Amerikaanse ontvangsten kunt herkennen en extra heren met behulp van een vooraf getraind ontvangst model. Gebruik de methode **begin_recognize_receipts_from_url** om ontvangst bewijzen te herkennen vanuit een URL. 

```python
poller = form_recognizer_client.begin_recognize_receipts_from_url(receiptUrl)
receipts = poller.result()
```

De geretourneerde waarde is een verzameling **RecognizedReceipt** -objecten: één voor elke pagina in het verzonden document. Met het volgende code blok worden basis gegevens over de ontvangst naar de console afgedrukt.

```python
for idx, receipt in enumerate(receipts):
    print("--------Recognizing receipt #{}--------".format(idx))
    receipt_type = receipt.fields.get("ReceiptType")
    if receipt_type:
        print("Receipt Type: {} has confidence: {}".format(receipt_type.value, receipt_type.confidence))
    merchant_name = receipt.fields.get("MerchantName")
    if merchant_name:
        print("Merchant Name: {} has confidence: {}".format(merchant_name.value, merchant_name.confidence))
    transaction_date = receipt.fields.get("TransactionDate")
    if transaction_date:
        print("Transaction Date: {} has confidence: {}".format(transaction_date.value, transaction_date.confidence))
```

In het volgende code blok wordt de afzonderlijke items herhaald die op de ontvangst zijn gedetecteerd en worden de details ervan naar de console afgedrukt.


```python
    print("Receipt items:")
    for idx, item in enumerate(receipt.fields.get("Items").value):
        print("...Item #{}".format(idx))
        item_name = item.value.get("Name")
        if item_name:
            print("......Item Name: {} has confidence: {}".format(item_name.value, item_name.confidence))
        item_quantity = item.value.get("Quantity")
        if item_quantity:
            print("......Item Quantity: {} has confidence: {}".format(item_quantity.value, item_quantity.confidence))
        item_price = item.value.get("Price")
        if item_price:
            print("......Individual Item Price: {} has confidence: {}".format(item_price.value, item_price.confidence))
        item_total_price = item.value.get("TotalPrice")
        if item_total_price:
            print("......Total Item Price: {} has confidence: {}".format(item_total_price.value, item_total_price.confidence))
```

Ten slotte drukt het laatste code blok de rest van de grote ontvangst gegevens af.

```python
    subtotal = receipt.fields.get("Subtotal")
    if subtotal:
        print("Subtotal: {} has confidence: {}".format(subtotal.value, subtotal.confidence))
    tax = receipt.fields.get("Tax")
    if tax:
        print("Tax: {} has confidence: {}".format(tax.value, tax.confidence))
    tip = receipt.fields.get("Tip")
    if tip:
        print("Tip: {} has confidence: {}".format(tip.value, tip.confidence))
    total = receipt.fields.get("Total")
    if total:
        print("Total: {} has confidence: {}".format(total.value, total.confidence))
    print("--------------------------------------")
```


## <a name="train-a-custom-model"></a>Aangepast model trainen

In deze sectie ziet u hoe u een model kunt trainen met uw eigen gegevens. Met een getraind model kunnen gestructureerde gegevens worden uitgevoerd die de sleutel/waarde-relaties in het oorspronkelijke formulier document bevatten. Nadat u het model hebt getraind, kunt u het testen en opnieuw trainen en uiteindelijk gebruiken om gegevens te extra heren uit meer formulieren, afhankelijk van uw behoeften.

> [!NOTE]
> U kunt ook modellen trainen met een Graphical User Interface zoals het [hulp programma voor het labelen](../../quickstarts/label-tool.md)van het voor beeld van de formulier herkenning.

### <a name="train-a-model-without-labels"></a>Een model trainen zonder labels

Train aangepaste modellen om alle velden en waarden te herkennen die in uw aangepaste formulieren worden gevonden zonder hand matig labels te krijgen voor de trainings documenten.

De volgende code gebruikt de training-client met de functie **begin_training** om een model op een bepaalde set documenten te trainen.

```python
poller = form_training_client.begin_training(self.trainingDataUrl, use_training_labels=False)
model = poller.result()
```

Het geretourneerde **CustomFormSubmodel** -object bevat informatie over de typen die het model kan herkennen en de velden die het kan ophalen van elk formulier type. In het volgende code blok wordt deze informatie in de-console afgedrukt.

```python
# Custom model information
print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Created on: {}".format(model.requested_on))
print("Last modified: {}".format(model.completed_on))

print("Recognized fields:")
# Looping through the submodels, which contains the fields they were trained on
for submodel in model.submodels:
    print("...The submodel has form type '{}'".format(submodel.form_type))
    for name, field in submodel.fields.items():
        print("...The model found field '{}' to have label '{}'".format(
            name, field.label
        ))
```

### <a name="train-a-model-with-labels"></a>Een model trainen met labels

U kunt ook aangepaste modellen trainen door de trainings documenten hand matig te labelen. Training met labels leidt tot betere prestaties in sommige scenario's. 

> [!IMPORTANT]
> Als u met labels wilt trainen, moet u in uw Blob Storage-container, naast de trainings documenten, speciale label-informatie bestanden (* \<filename\>.pdf.labels.jsaan*) hebben. Het [hulp programma voor het labelen](../../quickstarts/label-tool.md) van het voor beeld van een formulier herkenning biedt een gebruikers interface die u kan helpen bij het maken van deze label bestanden. Zodra u deze hebt, kunt u de **begin_training** -functie aanroepen met de para meter *use_training_labels* ingesteld op `true` .

```python
poller = form_training_client.begin_training(self.trainingDataUrl, use_training_labels=True)
model = poller.result()
```

De geretourneerde **CustomFormSubmodel** geeft aan welke velden het model kan ophalen, samen met de geschatte nauw keurigheid van elk veld. In het volgende code blok wordt deze informatie in de-console afgedrukt.

```python
# Custom model information
print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Created on: {}".format(model.created_on))
print("Last modified: {}".format(model.last_modified))

print("Recognized fields:")
# looping through the submodels, which contains the fields they were trained on
# The labels are based on the ones you gave the training document.
for submodel in model.submodels:
    print("...The submodel with form type {} has accuracy '{}'".format(submodel.form_type, submodel.accuracy))
    for name, field in submodel.fields.items():
        print("...The model found field '{}' to have name '{}' with an accuracy of {}".format(
            name, field.name, field.accuracy
        ))
```

## <a name="analyze-forms-with-a-custom-model"></a>Formulieren analyseren met een aangepast model

In deze sectie wordt gedemonstreerd hoe u sleutel/waarde-informatie en andere inhoud uit uw aangepaste formulier typen kunt extra heren met behulp van modellen die u hebt getraind met uw eigen formulieren.

> [!IMPORTANT]
> Als u dit scenario wilt implementeren, moet u al een model hebben getraind, zodat u de ID ervan kunt door geven naar de onderstaande methode. Zie de sectie [een model trainen](#train-a-model-without-labels) .

U gebruikt de **begin_recognize_custom_forms_from_url** methode. De geretourneerde waarde is een verzameling **RecognizedForm** -objecten: één voor elke pagina in het verzonden document.

```python
# Make sure your form's type is included in the list of form types the custom model can recognize
poller = form_recognizer_client.begin_recognize_custom_forms_from_url(
    model_id=model.model_id, form_url=formUrl)
forms = poller.result()
```

Met de volgende code worden de resultaten van de analyse naar de console afgedrukt. Alle herkende velden en bijbehorende waarden worden afgedrukt, samen met een betrouwbaarheids Score.

```python
for idx, form in enumerate(forms):
    print("--------Recognizing Form #{}--------".format(idx))
    print("Form {} has type {}".format(idx, form.form_type))
    for name, field in form.fields.items():
        # each field is of type FormField
        # The value of the field can also be a FormField, or a list of FormFields
        # In our sample, it is just a FormField.
        print("...Field '{}' has value '{}' with a confidence score of {}".format(
            name, field.value, field.confidence
        ))
        # label data is populated if you are using a model trained with unlabeled data, since the service needs to make predictions for
        # labels if not explicitly given to it.
        if field.label_data:
            print("...Field '{}' has label '{}' with a confidence score of {}".format(
                name,
                field.label_data.text,
                field.confidence
            ))
    print("-----------------------------------")
```

## <a name="manage-your-custom-models"></a>Uw aangepaste modellen beheren

In deze sectie wordt beschreven hoe u de aangepaste modellen beheert die zijn opgeslagen in uw account. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Het aantal modellen in het FormRecognizer-resource account controleren

In het volgende code blok wordt het aantal modellen gecontroleerd dat u hebt opgeslagen in uw formulier Recognizer-account en vergelijkt deze met de limiet voor het account.

```python
# First, we see how many custom models we have, and what our limit is
account_properties = form_training_client.get_account_properties()
print("Our account has {} custom models, and we can have at most {} custom models".format(
    account_properties.custom_model_count, account_properties.custom_model_limit
))
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>De modellen weer geven die momenteel zijn opgeslagen in het resource-account

In het volgende code blok worden de huidige modellen in uw account vermeld en worden de details ervan in de-console weer gegeven. Er wordt ook een verwijzing naar het eerste model opgeslagen.

```python
# Next, we get a paged list of all of our custom models
custom_models = form_training_client.list_custom_models()

print("We have models with the following ids:")

# Let's pull out the first model
first_model = next(custom_models)
print(first_model.model_id)
for model in custom_models:
    print(model.model_id)
```

### <a name="get-a-specific-model-using-the-models-id"></a>Een specifiek model ophalen met de ID van het model

Het volgende code blok maakt gebruik van de model-ID die is opgeslagen in de vorige sectie en gebruikt deze om details over het model op te halen.

```python
# Now we'll get the first custom model in the paged list
custom_model = form_training_client.get_custom_model(model_id=first_model.model_id)
print("Model ID: {}".format(custom_model.model_id))
print("Status: {}".format(custom_model.status))
print("Created on: {}".format(custom_model.requested_on))
print("Last modified: {}".format(custom_model.completed_on))
```

### <a name="delete-a-model-from-the-resource-account"></a>Een model uit het resource-account verwijderen

U kunt ook een model uit uw account verwijderen door te verwijzen naar de ID. Met deze code wordt het model dat in de vorige sectie wordt gebruikt, verwijderd.

```python
form_training_client.delete_model(model_id=custom_model.model_id)

# Confirm deletion:
try:
    form_training_client.get_custom_model(model_id=custom_model.model_id)
except ResourceNotFoundError:
    print("Successfully deleted model with id {}".format(custom_model.model_id))
}
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de `python` opdracht in uw Quick Start-bestand.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="general"></a>Algemeen

De client bibliotheek van de formulier herkenning leidt tot uitzonde ringen die zijn gedefinieerd in [Azure core](https://aka.ms/azsdk-python-azure-core).

## <a name="logging"></a>Logboekregistratie

Deze bibliotheek maakt gebruik van de [standaard logboek registratie bibliotheek](https://docs.python.org/3/library/logging.html) voor logboek registratie. Basis informatie over HTTP-sessies (Url's, kopteksten, enzovoort) wordt vastgelegd op het INFO niveau.

Gedetailleerde logboek registratie van fout opsporing, met inbegrip van aanvraag/antwoord-instanties en unredacted headers, kan worden ingeschakeld op een client met het `logging_enable` trefwoord argument:

```python
import sys
import logging
from azure.ai.formrecognizer import FormRecognizerClient
from azure.core.credentials import AzureKeyCredential

# Create a logger for the 'azure' SDK
logger = logging.getLogger('azure')
logger.setLevel(logging.DEBUG)

# Configure a console output
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

endpoint = "https://<my-custom-subdomain>.cognitiveservices.azure.com/"
credential = AzureKeyCredential("<api_key>")

# This client will log detailed information about its HTTP sessions, at DEBUG level
form_recognizer_client = FormRecognizerClient(endpoint, credential, logging_enable=True)
```

Zo `logging_enable` kunt u ook gedetailleerde logboek registratie inschakelen voor één bewerking, zelfs wanneer deze niet is ingeschakeld voor de client:

```python
poller = form_recognizer_client.begin_recognize_receipts(receipt, logging_enable=True)
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u de python-client bibliotheek voor de formulier herkenning gebruikt om modellen te trainen en formulieren op verschillende manieren te analyseren. Vervolgens leert u tips voor het maken van een betere set met trainings gegevens en het produceren van nauw keurige modellen.

> [!div class="nextstepaction"]
> [Een set met trainingsgegevens samenstellen](../../build-training-data-set.md)

* [Wat is Form Recognizer?](../../overview.md)
* De voorbeeld code uit deze hand leiding is te vinden op [github](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).
