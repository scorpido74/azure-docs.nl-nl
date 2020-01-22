---
title: Webservice gebruiken
titleSuffix: ML Studio (classic) - Azure
description: Zodra een machine learning-service is geïmplementeerd vanuit Azure Machine Learning Studio (klassiek), kan de REST-webservice worden gebruikt als een real-time aanvraag-antwoord service of als een batch-uitvoerings service.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: 7626714812b44119099344b52fe7506989555a57
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314297"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-classic-web-service"></a>Een Azure Machine Learning Studio-webservice (klassiek) gebruiken

Nadat u een Azure Machine Learning Studio (klassiek) voorspellend model als een webservice hebt geïmplementeerd, kunt u een REST API gebruiken om gegevens te verzenden en voor spellingen te ontvangen. U kunt de gegevens verzenden in realtime of in de batchmodus.

U vindt hier meer informatie over het maken en implementeren van een Machine Learning-webservice met behulp van Machine Learning Studio (klassiek):

* Zie [uw eerste experiment maken](create-experiment.md)voor een zelf studie over het maken van een experiment in machine learning Studio (klassiek).
* Zie voor meer informatie over het implementeren van een webservice [een Machine Learning-webservice implementeren](deploy-a-machine-learning-web-service.md).
* Voor meer informatie over Machine Learning in het algemeen, gaat u naar de [Machine Learning-documentatiecentrum](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Overzicht
Met de webservice Azure Machine Learning communiceert een externe toepassing met een Machine Learning scoremodel werkstromen in realtime. Aanroep van een Machine Learning-webservice retourneert voorspellingsresultaten naar een externe toepassing. Als u een Machine Learning-webservice aanroepen, moet u een API-sleutel die wordt gemaakt wanneer u een voorspelling implementeert doorgeven. De Machine Learning-webservice is gebaseerd op REST, een populaire architectuur voor webprogrammering.

Azure Machine Learning Studio (klassiek) heeft twee soorten services:

* Request-Response Service (RR'S): een laag latentie, uiterst schaal bare service die een interface biedt voor de stateless modellen die zijn gemaakt en geïmplementeerd vanuit de Machine Learning Studio (klassiek).
* Batchuitvoeringsservice (BES): een asynchrone service die scores een batch voor gegevensrecords.

Zie voor meer informatie over Machine Learning-webservices, [een Machine Learning-webservice implementeren](deploy-a-machine-learning-web-service.md).

## <a name="get-an-authorization-key"></a>Een autorisatie sleutel ophalen
Wanneer u uw experiment implementeert, wordt API-sleutels worden gegenereerd voor de webservice. U kunt de sleutels ophalen vanaf verschillende locaties.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Vanuit de portal voor Microsoft Azure Machine Learning-webservices
Aanmelden bij de [Microsoft Azure Machine Learning-webservices](https://services.azureml.net) portal.

Ophalen van de API-sleutel voor een nieuwe Machine-Learning-webservice:

1. Klik in het portal voor Azure Machine Learning-webservices op **webservices** het bovenste menu.
2. Klik op de webservice waarvoor u wilt ophalen van de sleutel.
3. Klik op het bovenste menu **verbruiken**.
4. Kopieer en bewaar de **primaire sleutel**.

Ophalen van de API-sleutel voor een klassieke Machine-Learning-webservice:

1. Klik in het portal voor Azure Machine Learning-webservices op **klassieke webservices** het bovenste menu.
2. Klik op de Web-service waarmee u werkt.
3. Klik op het eindpunt waarvoor u wilt ophalen van de sleutel.
4. Klik op het bovenste menu **verbruiken**.
5. Kopieer en bewaar de **primaire sleutel**.

### <a name="classic-web-service"></a>Klassieke webservice
 U kunt ook een sleutel voor een klassieke webservice ophalen van Machine Learning Studio (klassiek).

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (klassiek)
1. Klik in Machine Learning Studio (klassiek) op **Web Services** aan de linkerkant.
2. Klik op een webservice. De **API-sleutel** is op de **DASHBOARD** tabblad.

## <a id="connect"></a>Verbinding maken met een Machine Learning-webservice
U kunt verbinding maken met een Machine Learning-webservice met behulp van elke programmeertaal die ondersteuning biedt voor HTTP-aanvraag en antwoord. Vindt u voorbeelden in C#, Python / R van een help-pagina Machine Learning-webservice.

**Machine Learning API help** help voor Machine Learning API wordt gemaakt wanneer u een webservice implementeert. Zie [zelf studie 3: een model voor het credit risico implementeren](tutorial-part3-credit-risk-deploy.md).
De help van Machine Learning API bevat details over een voorspelling webservice.

1. Klik op de Web-service waarmee u werkt.
2. Klik op het eindpunt waarvan u wilt om de API Help-pagina weer te geven.
3. Klik op het bovenste menu **verbruiken**.
4. Klik op **API help-pagina** onder de Request Response- of Batch Execution-eindpunten.

**Machine Learning API weergeven help voor een nieuwe webservice**

In de [Azure Machine Learning Web Services-Portal](https://services.azureml.net/):

1. Klik op **WEBSERVICES** in het bovenste menu.
2. Klik op de webservice waarvoor u wilt ophalen van de sleutel.

Klik op **webservice gebruiken** om de uri's op te halen voor de Services aanvraag/antwoord en batch uitvoering en voorbeeld C#code in, R en python.

Klik op **Swagger API** Swagger ophalen op basis van documentatie voor de API's met de naam van de opgegeven URI's.

### <a name="c-sample"></a>Voorbeeld van C#
Gebruiken voor verbinding met een Machine Learning-webservice, een **HttpClient** ScoreData wordt doorgegeven. ScoreData bevat een FeatureVector, een n-dimensionale vector van numerieke functies die de ScoreData vertegenwoordigt. U kunt verifiëren met de service Machine Learning met een API-sleutel.

Verbinding maken met een Machine Learning-webservice, het **Microsoft.AspNet.WebApi.Client** NuGet-pakket moet worden geïnstalleerd.

**Microsoft.AspNet.WebApi.Client NuGet in Visual Studio installeren**

1. Publiceren van de gegevensset downloaden uit UCI: volwassenen 2 klasse gegevensset-webservice.
2. Klik op **Hulpprogramma's** > **NuGet Package Manager** > **Package Manager-console**.
3. Kies **Install-Package Microsoft.AspNet.WebApi.Client**.

**Het codevoorbeeld uitvoeren**

1. Publiceren ' voorbeeld 1: downloaden van de gegevensset van UCI: volwassene 2 klasse gegevensset "experiment, deel van de Machine Learning-voorbeeld-verzameling.
2. ApiKey toewijzen met de sleutel van een webservice. Zie hierboven **een autorisatie sleutel ophalen** .
3. Toewijzen serviceUri aan de aanvraag-URI.

**Hier volgt een volledige aanvraag bekijken.**
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.NET application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the request ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Voorbeeld van Python
Voor verbinding met een Machine Learning-webservice, gebruikt u de **urllib2** -bibliotheek voor Python 2.X en **urllib.request** -bibliotheek voor Python 3.X. U geeft ScoreData, waarin een FeatureVector, een n-dimensionale vector van numerieke functies die de ScoreData vertegenwoordigt. U kunt verifiëren met de service Machine Learning met een API-sleutel.

**Het codevoorbeeld uitvoeren**

1. Implementeren "voorbeeld 1: downloaden van de gegevensset van UCI: volwassene 2 klasse gegevensset" experiment, deel van de Machine Learning-voorbeeld-verzameling.
2. ApiKey toewijzen met de sleutel van een webservice. Zie de sectie **een autorisatie sleutel ophalen aan** het begin van dit artikel.
3. Toewijzen serviceUri aan de aanvraag-URI.

**Hier volgt een volledige aanvraag bekijken.**
```python
import urllib2 # urllib.request and urllib.error for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(url, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>Voorbeeld van R

Voor verbinding met een Machine Learning-webservice, gebruikt u de **RCurl** en **rjson** bibliotheken die u moet maken van de aanvraag en verwerken van het geretourneerde JSON-antwoord. U geeft ScoreData, waarin een FeatureVector, een n-dimensionale vector van numerieke functies die de ScoreData vertegenwoordigt. U kunt verifiëren met de service Machine Learning met een API-sleutel.

**Hier volgt een volledige aanvraag bekijken.**
```r
library("RCurl")
library("rjson")

# Accept SSL certificates issued by public Certificate Authorities
options(RCurlOptions = list(cainfo = system.file("CurlSSL", "cacert.pem", package = "RCurl")))

h = basicTextGatherer()
hdr = basicHeaderGatherer()

req = list(
    Inputs = list(
            "input1" = list(
                list(
                        'column1' = "value1",
                        'column2' = "value2",
                        'column3' = "value3"
                    )
            )
        ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "<your-api-key>" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

h$reset()
curlPerform(url = "<your-api-uri>",
httpheader=c('Content-Type' = "application/json", 'Authorization' = authz_hdr),
postfields=body,
writefunction = h$update,
headerfunction = hdr$update,
verbose = TRUE
)

headers = hdr$value()
httpStatus = headers["status"]
if (httpStatus >= 400)
{
print(paste("The request failed with status code:", httpStatus, sep=" "))

# Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
print(headers)
}

print("Result:")
result = h$value()
print(fromJSON(result))
```

### <a name="javascript-sample"></a>JavaScript-voorbeeld

Voor verbinding met een Machine Learning-webservice, gebruikt u de **aanvraag** npm-pakket in uw project. U gebruikt ook de `JSON` object om uw invoer en het resultaat kan parseren. Installeren met behulp van `npm install request --save`, of toe te voegen `"request": "*"` aan uw package.json onder `dependencies` en uit te voeren `npm install`.

**Hier volgt een volledige aanvraag bekijken.**
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```
