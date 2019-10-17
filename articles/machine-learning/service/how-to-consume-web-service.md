---
title: Client maken om geïmplementeerde webservice te gebruiken
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van een webservice die is gegenereerd bij het implementeren van een model met Azure Machine Learning model. De webservice toont een REST API. Clients voor deze API maken met behulp van de programmeer taal van uw keuze.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: fda6c72504a75d600931185e224bb46db03e23ed
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374295"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Een Azure Machine Learning model gebruiken dat is geïmplementeerd als een webservice

Als u een Azure Machine Learning model implementeert als een webservice, wordt een REST API gemaakt. U kunt gegevens naar deze API verzenden en de voor spelling ontvangen die door het model wordt geretourneerd. In dit document leest u hoe u clients voor de webservice maakt met behulp C#van, go, Java en python.

U maakt een webservice wanneer u een installatie kopie implementeert op Azure Container Instances, Azure Kubernetes-service of veld-Programmeer bare poort matrices (FPGA). U maakt installatie kopieën van geregistreerde modellen en Score bestanden. U haalt de URI op die wordt gebruikt om toegang te krijgen tot een webservice met behulp van de [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Als verificatie is ingeschakeld, kunt u ook de SDK gebruiken om de verificatie sleutels of-tokens op te halen.

De algemene werk stroom voor het maken van een client die gebruikmaakt van een machine learning-webservice:

1. Gebruik de SDK om de verbindings gegevens op te halen.
1. Bepaal het type aanvraag gegevens dat door het model wordt gebruikt.
1. Maak een toepassing die de webservice aanroept.

> [!TIP]
> De voor beelden in dit document zijn hand matig gemaakt zonder de specificaties voor het gebruik van OpenAPI (Swagger). Als u een OpenAPI-specificatie hebt ingeschakeld voor uw implementatie, kunt u hulpprogram ma's zoals [Swagger-CodeGen](https://github.com/swagger-api/swagger-codegen) gebruiken om client bibliotheken voor uw service te maken.

## <a name="connection-information"></a>Verbindingsgegevens

> [!NOTE]
> Gebruik de Azure Machine Learning SDK om informatie over de webservice op te halen. Dit is een python-SDK. U kunt elke taal gebruiken om een-client voor de service te maken.

De klasse [azureml. core. webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) bevat de informatie die u nodig hebt voor het maken van een-client. De volgende `Webservice` eigenschappen zijn handig voor het maken van een client toepassing:

* `auth_enabled`-als sleutel verificatie is ingeschakeld, `True`; zo niet, `False`.
* `token_auth_enabled`-als token verificatie is ingeschakeld, `True`; zo niet, `False`.
* `scoring_uri`: het REST API adres.
* `swagger_uri`: het adres van de OpenAPI-specificatie. Deze URI is beschikbaar als u automatische schema generatie hebt ingeschakeld. Zie [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md#schema)voor meer informatie.

Er zijn drie manieren om deze informatie op te halen voor geïmplementeerde webservices:

* Wanneer u een model implementeert, wordt er een `Webservice`-object geretourneerd met informatie over de service:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* U kunt `Webservice.list` gebruiken om een lijst met geïmplementeerde webservices voor modellen in uw werk ruimte op te halen. U kunt filters toevoegen om de lijst met geretourneerde gegevens te beperken. Zie de naslag documentatie voor de [webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) voor meer informatie over wat kan worden gefilterd.

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Als u de naam van de geïmplementeerde service weet, kunt u een nieuw exemplaar van `Webservice` maken en de werk ruimte en de service naam als para meters opgeven. Het nieuwe object bevat informatie over de geïmplementeerde service.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

### <a name="authentication-for-services"></a>Verificatie voor services

Azure Machine Learning biedt twee manieren om de toegang tot uw webservices te beheren.

|Verificatiemethode|ACI|AKS|
|---|---|---|
|Sleutel|Standaard uitgeschakeld| Standaard ingeschakeld|
|Token| Niet beschikbaar| Standaard uitgeschakeld |

Wanneer u een aanvraag verzendt naar een service die is beveiligd met een sleutel of token, gebruikt u de __autorisatie__ -header om de sleutel of het token door te geven. De sleutel of het token moet zijn geformatteerd als `Bearer <key-or-token>`, waarbij `<key-or-token>` uw sleutel of Token waarde is.

#### <a name="authentication-with-keys"></a>Verificatie met sleutels

Wanneer u verificatie voor een implementatie inschakelt, maakt u automatisch verificatie sleutels.

* Verificatie is standaard ingeschakeld wanneer u naar de Azure Kubernetes-service implementeert.
* Verificatie is standaard uitgeschakeld wanneer u implementeert naar Azure Container Instances.

Als u de verificatie wilt beheren, gebruikt u de para meter `auth_enabled` wanneer u een implementatie maakt of bijwerkt.

Als verificatie is ingeschakeld, kunt u de methode `get_keys` gebruiken om een primaire en secundaire verificatie sleutel op te halen:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Als u een sleutel opnieuw moet genereren, gebruikt u [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

#### <a name="authentication-with-tokens"></a>Verificatie met tokens

Wanneer u token verificatie inschakelt voor een webservice, moet een gebruiker een Azure Machine Learning JWT-token voor de webservice opgeven om deze te openen. 

* Token verificatie is standaard uitgeschakeld wanneer u implementeert in azure Kubernetes service.
* Token authenticatie wordt niet ondersteund wanneer u implementeert naar Azure Container Instances.

Als u de verificatie van tokens wilt beheren, gebruikt u de para meter `token_auth_enabled` wanneer u een implementatie maakt of bijwerkt.

Als token verificatie is ingeschakeld, kunt u de methode `get_token` gebruiken om een Bearer-token op te halen en de verval tijd van tokens:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> U moet een nieuw token aanvragen na de `refresh_by`-tijd van het token. 

## <a name="request-data"></a>Gegevens opvragen

De REST API verwacht dat de hoofd tekst van de aanvraag een JSON-document is met de volgende structuur:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> De structuur van de gegevens moet overeenkomen met het Score script en het model in de service. Het Score script kan de gegevens wijzigen voordat deze aan het model worden door gegeven.

Het model in het voor beeld van de [trein in notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) verwacht bijvoorbeeld een matrix van 10 getallen. Met het Score script voor dit voor beeld wordt een numpy-matrix gemaakt op basis van de aanvraag en door gegeven aan het model. In het volgende voor beeld ziet u de gegevens die deze service verwacht:

```json
{
    "data": 
        [
            [
                0.0199132141783263, 
                0.0506801187398187, 
                0.104808689473925, 
                0.0700725447072635, 
                -0.0359677812752396, 
                -0.0266789028311707, 
                -0.0249926566315915, 
                -0.00259226199818282, 
                0.00371173823343597, 
                0.0403433716478807
            ]
        ]
}
```

De webservice kan meerdere gegevens sets in één aanvraag accepteren. Er wordt een JSON-document geretourneerd met een matrix van antwoorden.

### <a name="binary-data"></a>Binaire gegevens

Zie [binaire gegevens](how-to-deploy-and-where.md#binary)voor meer informatie over het inschakelen van ondersteuning voor binaire gegevens in uw service.

### <a name="cross-origin-resource-sharing-cors"></a>Cross-Origin-resource delen (CORS)

Zie [Cross-Origin-resource delen](how-to-deploy-and-where.md#cors)voor meer informatie over het inschakelen van CORS-ondersteuning in uw service.

## <a name="call-the-service-c"></a>De service (C#) aanroepen

In dit voor beeld ziet u hoe C# u kunt gebruiken om de webservice aan te roepen die vanuit de [trein in een notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) -voor beeld is gemaakt:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

De geretourneerde resultaten zijn vergelijkbaar met het volgende JSON-document:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>De service aanroepen (Go)

In dit voor beeld ziet u hoe u kunt gebruiken om de webservice aan te roepen die is gemaakt vanuit de [trein in het notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) -voor beeld:

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

De geretourneerde resultaten zijn vergelijkbaar met het volgende JSON-document:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>De service aanroepen (Java)

Dit voor beeld laat zien hoe u Java gebruikt voor het aanroepen van de webservice die is gemaakt op basis van de [trein in het notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) -voor beeld:

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

De geretourneerde resultaten zijn vergelijkbaar met het volgende JSON-document:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>De service aanroepen (python)

In dit voor beeld wordt gedemonstreerd hoe u python gebruikt om de webservice aan te roepen die is gemaakt via de [trein in een notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) -voor beeld:

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

De geretourneerde resultaten zijn vergelijkbaar met het volgende JSON-document:

```JSON
[217.67978776218715, 224.78937091757172]
```

## <a name="consume-the-service-from-power-bi"></a>De service van Power BI gebruiken

Power BI ondersteunt het gebruik van Azure Machine Learning webservices om de gegevens in Power BI te verrijken met voor spellingen. 

Als u een webservice wilt genereren die wordt ondersteund voor gebruik in Power BI, moet het schema ondersteuning bieden voor de indeling die wordt vereist door Power BI. [Meer informatie over het maken van een door Power bi ondersteund schema](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#example-entry-script).

Zodra de webservice is geïmplementeerd, kan deze worden verbruikt vanaf Power BI gegevens stromen. [Meer informatie over het gebruik van een Azure machine learning-webservice van Power bi](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Volgende stappen

Ga naar het [Azure Architecture Center](/azure/architecture/reference-architectures/ai/realtime-scoring-python)om een referentie architectuur voor realtime-scores van python-en diepe leer modellen te bekijken.
