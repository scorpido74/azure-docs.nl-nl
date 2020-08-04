---
title: Invoer script voor geavanceerde scenario's
titleSuffix: Azure Machine Learning entry script authoring
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 0499cd6885454604e89ce4cadc313b2f68c45156
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87544580"
---
# <a name="advanced-entry-script-authoring"></a>Geavanceerd invoer script ontwerpen

In dit artikel wordt beschreven hoe u invoer scripts voor gespecialiseerde use cases schrijft.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u al een getraind machine learning model hebt dat u wilt implementeren met Azure Machine Learning. Raadpleeg [deze zelf studie](how-to-deploy-and-where.md)voor meer informatie over het implementeren van modellen.

## <a name="automatically-generate-a-swagger-schema"></a>Automatisch een Swagger-schema genereren

Als u automatisch een schema voor uw webservice wilt genereren, geeft u een voor beeld van de invoer en/of uitvoer in de constructor voor een van de gedefinieerde type-objecten. Het type en het voor beeld worden gebruikt voor het automatisch maken van het schema. Azure Machine Learning maakt vervolgens tijdens de implementatie een [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger)-specificatie voor de webservice.

Deze typen worden momenteel ondersteund:

* `pandas`
* `numpy`
* `pyspark`
* Standard python-object

Als u schema's wilt genereren, neemt u het open-source `inference-schema` pakket op in het bestand met afhankelijkheden. Zie voor meer informatie over dit pakket [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) . Definieer de voorbeeld indelingen voor invoer en uitvoer in `input_sample` de `output_sample` variabelen en, die de aanvraag-en antwoord indelingen voor de webservice vertegenwoordigen. Gebruik deze voor beelden in de functie voor invoer en uitvoer op de `run()` functie. Het volgende voor beeld van scikit maakt gebruik van schema generatie.


## <a name="power-bi-compatible-endpoint"></a>Power BI compatibel eind punt 

In het volgende voor beeld ziet u hoe u de invoer gegevens definieert als een `<key: value>` woorden lijst met behulp van een data frame. Deze methode wordt ondersteund voor het gebruik van de geïmplementeerde webservice van Power BI. (Meer[informatie over het gebruik van de webservice van Power bi](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample, enforce_shape=False))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```


## <a name="binary-ie-image-data"></a><a id="binary-data"></a>Binaire (d.w.z. afbeeldings) gegevens

Als uw model binaire gegevens accepteert, zoals een afbeelding, moet u het bestand wijzigen dat `score.py` wordt gebruikt voor uw implementatie om onbewerkte HTTP-aanvragen te accepteren. Als u onbewerkte gegevens wilt accepteren, gebruikt u de `AMLRequest` klasse in uw invoer script en voegt `@rawhttp` u de decorator toe aan de `run()` functie.

Hier volgt een voor beeld van een `score.py` waarmee binaire gegevens worden geaccepteerd:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> De `AMLRequest` klasse bevindt zich in de `azureml.contrib` naam ruimte. Entiteiten in deze naam ruimte worden regel matig gewijzigd, terwijl we werken om de service te verbeteren. Alles in deze naam ruimte moet worden beschouwd als een preview-versie die niet volledig wordt ondersteund door micro soft.
>
> Als u dit in uw lokale ontwikkel omgeving moet testen, kunt u de onderdelen installeren met behulp van de volgende opdracht:
>
> ```shell
> pip install azureml-contrib-services
> ```

`AMLRequest`Met de klasse hebt u alleen toegang tot de onbewerkte geposte gegevens in de score.py, maar er is geen onderdeel aan de client zijde. Vanaf een-client post u gegevens als normaal. Met de volgende python-code wordt bijvoorbeeld een afbeeldings bestand gelezen en worden de gegevens Gepost:

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = requests.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

## <a name="cross-origin-resource-sharing-cors"></a>Cross-Origin-resource delen (CORS)

Cross-Origin-resource delen is een manier om te voor komen dat resources op een webpagina worden aangevraagd vanuit een ander domein. CORS werkt via HTTP-headers die worden verzonden met de aanvraag van de client en worden geretourneerd met de service reactie. Zie [Cross-Origin-resource delen](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) in Wikipedia voor meer informatie over CORS en geldige headers.

Als u uw model implementatie wilt configureren voor de ondersteuning van CORS, gebruikt u de- `AMLResponse` klasse in uw invoer script. Met deze klasse kunt u de headers op het antwoord object instellen.

In het volgende voor beeld wordt de `Access-Control-Allow-Origin` koptekst ingesteld voor het antwoord van het entry-script:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> De `AMLResponse` klasse bevindt zich in de `azureml.contrib` naam ruimte. Entiteiten in deze naam ruimte worden regel matig gewijzigd, terwijl we werken om de service te verbeteren. Alles in deze naam ruimte moet worden beschouwd als een preview-versie die niet volledig wordt ondersteund door micro soft.
>
> Als u dit in uw lokale ontwikkel omgeving moet testen, kunt u de onderdelen installeren met behulp van de volgende opdracht:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure Machine Learning worden alleen POST-en GET-aanvragen doorgestuurd naar de containers die de Score service uitvoeren. Dit kan fouten veroorzaken door browsers die gebruikmaken van OPTIONS-aanvragen voor CORS-aanvragen van voor bereidingen.
> 


## <a name="load-registered-models"></a>Geregistreerde modellen laden

Er zijn twee manieren om modellen te vinden in het involgings script:
* `AZUREML_MODEL_DIR`: Een omgevings variabele die het pad naar de model locatie bevat.
* `Model.get_model_path`: Een API die het pad naar het model bestand retourneert met de geregistreerde model naam.

#### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR is een omgevings variabele die tijdens de implementatie van de service wordt gemaakt. U kunt deze omgevings variabele gebruiken om de locatie van de geïmplementeerde model (en) te vinden.

In de volgende tabel wordt de waarde van AZUREML_MODEL_DIR beschreven, afhankelijk van het aantal geïmplementeerde modellen:

| Implementatie | Waarde van omgevings variabele |
| ----- | ----- |
| Eén model | Het pad naar de map die het model bevat. |
| Meerdere modellen | Het pad naar de map met alle modellen. Modellen bevinden zich op naam en versie in deze map ( `$MODEL_NAME/$VERSION` ) |

Tijdens model registratie en-implementatie worden modellen in het AZUREML_MODEL_DIR pad geplaatst en hun oorspronkelijke bestands namen blijven behouden.

Als u het pad naar een model bestand in het invoer script wilt ophalen, combineert u de omgevings variabele met het pad naar het bestand waarnaar u op zoek bent.

**Voor beeld van één model**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Voor beeld van meerdere modellen**

In dit scenario worden twee modellen geregistreerd bij de werk ruimte:

* `my_first_model`: Bevat één bestand ( `my_first_model.pkl` ) en er is slechts één versie ( `1` ).
* `my_second_model`: Bevat één bestand ( `my_second_model.pkl` ) en er zijn twee versies, `1` en `2` .

Wanneer de service is geïmplementeerd, worden beide modellen in de implementatie bewerking weer gegeven:

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

In de docker-installatie kopie die als host fungeert voor de service, `AZUREML_MODEL_DIR` bevat de omgevings variabele de map waarin de modellen zich bevinden.
In deze map bevindt elk model zich in een mappad van `MODEL_NAME/VERSION` . Waarbij de `MODEL_NAME` naam is van het geregistreerde model en `VERSION` de versie van het model is. De bestanden waaruit het geregistreerde model bestaan, worden opgeslagen in deze mappen.

In dit voor beeld zijn de paden `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` en `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl` .


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

### <a name="get_model_path"></a>get_model_path

Wanneer u een model registreert, geeft u een model naam op die wordt gebruikt voor het beheren van het model in het REGI ster. U gebruikt deze naam met de methode [model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) om het pad van het model bestand of de bestanden op het lokale bestands systeem op te halen. Als u een map of een verzameling bestanden registreert, retourneert deze API het pad van de map die de bestanden bevat.

Wanneer u een model registreert, geeft u het een naam. De naam komt overeen met de locatie waar het model lokaal of tijdens de service-implementatie wordt geplaatst.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met een mislukte implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Implementeren naar Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Client toepassingen maken voor het gebruik van webservices](how-to-consume-web-service.md)
* [Web-service bijwerken](how-to-deploy-update-web-service.md)
* [Een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Gebeurtenis waarschuwingen en triggers maken voor model implementaties](how-to-use-event-grid.md)
