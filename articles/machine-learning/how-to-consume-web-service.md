---
title: Client maken voor model dat is geïmplementeerd als webservice
titleSuffix: Azure Machine Learning
description: Meer informatie over het aanroepen van een eindpunt van webservices dat is gegenereerd toen een model werd geïmplementeerd vanuit Azure Machine Learning. Het eindpunt onthult een REST API, die u aanroepen om gevolgtrekking met het model uit te voeren. Maak clients voor deze API met behulp van de programmeertaal van uw keuze.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: 0222b63323c4e546628d790fabb881eba006494e
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383398"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Een Azure Machine Learning-model gebruiken dat is geïmplementeerd als webservice
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Als u een Azure Machine Learning-model als webservice implementeert, wordt een REST API-eindpunt gemaakt. U gegevens naar dit eindpunt verzenden en de voorspelling ontvangen die door het model wordt geretourneerd. In dit document leert u hoe u clients voor de webservice maakt met C#, Go, Java en Python.

U maakt een webservice wanneer u een model implementeert naar uw lokale omgeving, Azure Container Instances, Azure Kubernetes Service of field-programmeerbare gate arrays (FPGA). U haalt de URI op die wordt gebruikt om toegang te krijgen tot de webservice met behulp van de [Azure Machine Learning SDK.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) Als verificatie is ingeschakeld, u de SDK ook gebruiken om de verificatiesleutels of tokens te krijgen.

De algemene werkstroom voor het maken van een client die een webservice voor machine learning gebruikt, is:

1. Gebruik de SDK om de verbindingsgegevens te krijgen.
1. Bepaal het type aanvraaggegevens dat door het model wordt gebruikt.
1. Maak een toepassing die de webservice aanroept.

> [!TIP]
> De voorbeelden in dit document worden handmatig gemaakt zonder het gebruik van OpenAPI (Swagger) specificaties. Als u een OpenAPI-specificatie voor uw implementatie hebt ingeschakeld, u hulpprogramma's zoals [branie-codegen](https://github.com/swagger-api/swagger-codegen) gebruiken om clientbibliotheken voor uw service te maken.

## <a name="connection-information"></a>Verbindingsgegevens

> [!NOTE]
> Gebruik de Azure Machine Learning SDK om de webservicegegevens op te halen. Dit is een Python SDK. U elke taal gebruiken om een client voor de service te maken.

De klasse [azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) biedt de informatie die u nodig hebt om een client te maken. De `Webservice` volgende eigenschappen zijn handig voor het maken van een clienttoepassing:

* `auth_enabled`- Indien sleutelverificatie is `True`ingeschakeld; anders. `False`
* `token_auth_enabled`- Als tokenverificatie is `True`ingeschakeld; anders. `False`
* `scoring_uri`- Het REST API-adres.
* `swagger_uri`- Het adres van de OpenAPI-specificatie. Deze URI is beschikbaar als u automatische schemageneratie hebt ingeschakeld. Zie [Modellen implementeren met Azure Machine Learning voor](how-to-deploy-and-where.md)meer informatie.

Er zijn drie manieren om deze informatie op te halen voor geïmplementeerde webservices:

* Wanneer u een model `Webservice` implementeert, wordt een object geretourneerd met informatie over de service:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* U kunt `Webservice.list` een lijst met geïmplementeerde webservices ophalen voor modellen in uw werkruimte. U filters toevoegen om de lijst met geretourneerde informatie te verkleinen. Zie de [referentiedocumentatie Webservice.list](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) voor meer informatie over wat er gefilterd kan worden.

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Als u de naam van de geïmplementeerde service kent, u een nieuw exemplaar van, en de naam van `Webservice`de werkruimte en service als parameters opgeven. Het nieuwe object bevat informatie over de geïmplementeerde service.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

### <a name="secured-web-service"></a>Beveiligde webservice

Als u de geïmplementeerde webservice hebt beveiligd met een TLS/SSL-certificaat, u [HTTPS](https://en.wikipedia.org/wiki/HTTPS) gebruiken om verbinding te maken met de service met behulp van de score of branie URI. HTTPS helpt de communicatie tussen een client en een webservice te beveiligen door de communicatie tussen de twee te versleutelen. Versleuteling maakt gebruik [van Transport Layer Security (TLS).](https://en.wikipedia.org/wiki/Transport_Layer_Security) TLS wordt soms nog steeds aangeduid als *Secure Sockets Layer* (SSL), dat was de voorganger van TLS.

> [!IMPORTANT]
> Webservices die door Azure Machine Learning worden geïmplementeerd, ondersteunen alleen TLS-versie 1.2. Controleer bij het maken van een clienttoepassing of deze versie wordt ondersteund.

Zie [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)voor meer informatie.

### <a name="authentication-for-services"></a>Verificatie voor services

Azure Machine Learning biedt twee manieren om de toegang tot uw webservices te beheren.

|Verificatiemethode|Aci|AKS|
|---|---|---|
|Sleutel|Standaard uitgeschakeld| Standaard ingeschakeld|
|Token| Niet beschikbaar| Standaard uitgeschakeld |

Wanneer u een aanvraag verzendt naar een service die is beveiligd met een sleutel of token, gebruikt u de __kopautorisatie__ om de sleutel of token door te geven. De sleutel of token moet `Bearer <key-or-token>`worden `<key-or-token>` opgemaakt als , waar is uw sleutel of token waarde.

#### <a name="authentication-with-keys"></a>Verificatie met sleutels

Wanneer u verificatie inschakelt voor een implementatie, maakt u automatisch verificatiesleutels.

* Verificatie is standaard ingeschakeld wanneer u implementeert naar Azure Kubernetes Service.
* Verificatie is standaard uitgeschakeld wanneer u implementeert in Azure Container Instances.

Als u verificatie `auth_enabled` wilt beheren, gebruikt u de parameter wanneer u een implementatie maakt of bijwerkt.

Als verificatie is ingeschakeld, kunt `get_keys` u de methode gebruiken om een primaire en secundaire verificatiesleutel op te halen:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Als u een sleutel moet [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)regenereren, gebruik.

#### <a name="authentication-with-tokens"></a>Verificatie met tokens

Wanneer u tokenverificatie inschakelt voor een webservice, moet een gebruiker een Azure Machine Learning JWT-token aan de webservice verstrekken om toegang te krijgen tot de webservice. 

* Tokenverificatie is standaard uitgeschakeld wanneer u implementeert naar Azure Kubernetes Service.
* Tokenverificatie wordt niet ondersteund wanneer u wordt geïmplementeerd in Azure Container Instances.

Als u tokenverificatie `token_auth_enabled` wilt beheren, gebruikt u de parameter wanneer u een implementatie maakt of bijwerkt.

Als tokenverificatie is ingeschakeld, kunt `get_token` u de methode gebruiken om een token aan toonder op te halen en dat de vervaldatum van tokens:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Je moet na de tijd van `refresh_by` het token een nieuw token aanvragen. 

## <a name="request-data"></a>Gegevens aanvragen

De REST API verwacht dat de hoofdtekst van de aanvraag een JSON-document is met de volgende structuur:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> De structuur van de gegevens moet overeenkomen met wat het scorescript en -model in de service verwachten. Het scorescript kan de gegevens wijzigen voordat deze worden doorgegeven aan het model.

Het model in het voorbeeld [Trein in het notitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) verwacht bijvoorbeeld een array van 10 nummers. In het scorescript voor dit voorbeeld wordt een Numpy-array van de aanvraag gemaakt en wordt deze doorgegeven aan het model. In het volgende voorbeeld worden de gegevens weergegeven die deze service verwacht:

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

De webservice kan meerdere sets gegevens in één aanvraag accepteren. Hiermee wordt een JSON-document geretourneerd met een reeks reacties.

### <a name="binary-data"></a>Binaire gegevens

Zie [Binaire gegevens](how-to-deploy-and-where.md#binary)voor informatie over het inschakelen van ondersteuning voor binaire gegevens in uw service.

> [!TIP]
> Ondersteuning inschakelen voor binaire gegevens gebeurt in het score.py bestand dat wordt gebruikt door het geïmplementeerde model. Gebruik bij de client de HTTP-functionaliteit van uw programmeertaal. In het volgende fragment wordt bijvoorbeeld de inhoud van een JPG-bestand naar een webservice verzendt:
>
> ```python
> import requests
> # Load image data
> data = open('example.jpg', 'rb').read()
> # Post raw data to scoring URI
> res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/> octet-stream'})
> ```

### <a name="cross-origin-resource-sharing-cors"></a>Cross-origin resource sharing (CORS)

Zie [Cross-origin resource sharing](how-to-deploy-and-where.md#cors)voor informatie over het inschakelen van CORS-ondersteuning in uw service.

## <a name="call-the-service-c"></a>De service bellen (C#)

In dit voorbeeld wordt uitgelegd hoe u C# gebruikt om de webservice aan te roepen die is gemaakt vanuit het voorbeeld [van de trein binnen het notitieblok:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

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

## <a name="call-the-service-go"></a>De service bellen (Go)

In dit voorbeeld wordt uitgelegd hoe u Go to call de webservice gebruikt die is gemaakt vanuit het voorbeeld [van de trein binnen het notitieblok:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

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

## <a name="call-the-service-java"></a>Bel de dienst (Java)

In dit voorbeeld wordt uitgelegd hoe u Java gebruiken om de webservice aan te roepen die is gemaakt vanuit het voorbeeld van de [trein binnen het notitieblok:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

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

## <a name="call-the-service-python"></a>De service bellen (Python)

In dit voorbeeld wordt uitgelegd hoe u Python gebruiken om de webservice aan te roepen die is gemaakt vanuit het voorbeeld [van de trein binnen het notitieblok:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

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

## <a name="consume-the-service-from-power-bi"></a>De service gebruiken vanuit Power BI

Power BI ondersteunt het verbruik van Azure Machine Learning-webservices om de gegevens in Power BI te verrijken met voorspellingen. 

Als u een webservice wilt genereren die wordt ondersteund voor het verbruik in Power BI, moet het schema de indeling ondersteunen die vereist is voor Power BI. [Meer informatie over het maken van een door Power BI ondersteund schema](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#example-entry-script).

Zodra de webservice is geïmplementeerd, is deze mogelijk door de gegevensstromen van Power BI. [Meer informatie over het gebruik van een Azure Machine Learning-webservice van Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Volgende stappen

Als u een referentiearchitectuur wilt weergeven voor realtime scores van Python- en deep learning-modellen, gaat u naar het [Azure-architectuurcentrum](/azure/architecture/reference-architectures/ai/realtime-scoring-python).
