---
title: Webservice gebruiken
titleSuffix: ML Studio (classic) - Azure
description: Zodra een machine learning-service is geïmplementeerd vanuit Azure Machine Learning Studio (klassiek), kan de RESTFul Web-service worden verbruikt als realtime service voor het aanvragen van aanvragen of als een batchuitvoeringsservice.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: de395f7167f0ab7d7c6429c14d5efce46831b576
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218240"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-classic-web-service"></a>Een webservice van Azure Machine Learning Studio (klassieke) gebruiken

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Zodra u een Azure Machine Learning Studio (klassiek) voorspellend model als webservice implementeert, u een REST-API gebruiken om deze gegevens te verzenden en voorspellingen te krijgen. U de gegevens in realtime of in batchmodus verzenden.

Hier vindt u meer informatie over het maken en implementeren van een Machine Learning Web service met Machine Learning Studio (klassiek):

* Zie [Uw eerste experiment maken](create-experiment.md)voor een zelfstudie over het maken van een experiment in Machine Learning Studio (klassiek.
* Zie [Een machine learning-webservice implementeren](deploy-a-machine-learning-web-service.md)voor meer informatie over het implementeren van een webservice.
* Ga voor meer informatie over Machine Learning in het algemeen naar het [Machine Learning Documentation Center.](https://azure.microsoft.com/documentation/services/machine-learning/)



## <a name="overview"></a>Overzicht
Met de Azure Machine Learning Web-service communiceert een externe toepassing in realtime met een machine learning-werkstroomscoremodel. Een Machine Learning Webservice-oproep retourneert voorspellingsresultaten naar een externe toepassing. Als u een Machine Learning Webservice-aanroep wilt voeren, passeert u een API-sleutel die wordt gemaakt wanneer u een voorspelling implementeert. De Machine Learning Web service is gebaseerd op REST, een populaire architectuur keuze voor web programmering projecten.

Azure Machine Learning Studio (klassiek) heeft twee soorten services:

* Request-Response Service (RRS) – Een lage latentie, zeer schaalbare service die een interface biedt voor de stateless modellen die zijn gemaakt en geïmplementeerd vanuit de Machine Learning Studio (klassiek).
* Batch Execution Service (BES) – Een asynchrone service die een batch scoort voor gegevensrecords.

Zie [Een machine learning-webservice implementeren](deploy-a-machine-learning-web-service.md)voor meer informatie over webservices voor Machine Learning.

## <a name="get-an-authorization-key"></a>Een autorisatiesleutel aanvragen
Wanneer u uw experiment implementeert, worden API-sleutels gegenereerd voor de webservice. U de sleutels op verschillende locaties ophalen.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Vanuit de Microsoft Azure Machine Learning Web Services-portal
Meld u aan bij de [Microsoft Azure Machine Learning Web Services-portal.](https://services.azureml.net)

Ga als volgende voor op een nieuwe machine learning-webservice:

1. Klik in de Azure Machine Learning Web Services-portal op **Webservices** in het bovenste menu.
2. Klik op de webservice waarvoor u de sleutel wilt ophalen.
3. Klik in het bovenste menu op **Consumeren**.
4. Kopieer en sla de **primaire sleutel op.**

Ga als lid van het opwat de API-sleutel voor een klassieke machine learning-webservice wordt opgehaald:

1. Klik in de Azure Machine Learning Web Services-portal op **Classic Web Services** in het bovenste menu.
2. Klik op de webservice waarmee u werkt.
3. Klik op het eindpunt waarvoor u de sleutel wilt ophalen.
4. Klik in het bovenste menu op **Consumeren**.
5. Kopieer en sla de **primaire sleutel op.**

### <a name="classic-web-service"></a>Klassieke webservice
 U ook een sleutel voor een Classic Web-service ophalen bij Machine Learning Studio (klassiek).

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (klassiek)
1. Klik in Machine Learning Studio (klassiek) op **WEB SERVICES** aan de linkerkant.
2. Klik op een webservice. De **API-sleutel** bevindt zich op het tabblad **DASHBOARD.**

## <a name="connect-to-a-machine-learning-web-service"></a><a id="connect"></a>Verbinding maken met een Machine Learning-webservice
U verbinding maken met een Machine Learning-webservice met behulp van elke programmeertaal die http-aanvraag en -antwoord ondersteunt. U voorbeelden in C#, Python en R bekijken op een helppagina voor machine learning-services.

**Help voor Machine Learning API** Machine Learning API-help wordt gemaakt wanneer u een webservice implementeert. Zie [Zelfstudie 3: Kredietrisicomodel implementeren](tutorial-part3-credit-risk-deploy.md).
De Machine Learning API-help bevat details over een voorspellingswebservice.

1. Klik op de webservice waarmee u werkt.
2. Klik op het eindpunt waarvoor u de HELP-pagina voor API wilt weergeven.
3. Klik in het bovenste menu op **Consumeren**.
4. Klik **op de helppagina api** onder de eindpunten Aanvraag-Antwoord of Batchexecution.

**Help voor Machine Learning-API's weergeven voor een nieuwe webservice**

In de [Azure Machine Learning Web Services Portal:](https://services.azureml.net/)

1. Klik op **WEB SERVICES** in het bovenste menu.
2. Klik op de webservice waarvoor u de sleutel wilt ophalen.

Klik **op Webservice gebruiken** om de URI's voor de services voor aanvraag- en batchuitvoeringsservices en voorbeeldcode in C#, R en Python op te halen.

Klik op **Swagger API** om swagger-gebaseerde documentatie voor de API's te krijgen die worden aangeroepen vanuit de meegeleverde URI's.

### <a name="c-sample"></a>C# Voorbeeld
Als u verbinding wilt maken met een Machine Learning-webservice, gebruikt u een **HttpClient** die ScoreData doorgeeft. ScoreData bevat een FeatureVector, een n-dimensionale vector van numerieke functies die de ScoreData vertegenwoordigt. U verifieert de Machine Learning-service met een API-sleutel.

Als u verbinding wilt maken met een Machine Learning-webservice, moet het **NuGet-pakket Microsoft.AspNet.WebApi.Client** zijn geïnstalleerd.

**Microsoft.AspNet.WebApi.Client NuGet installeren in Visual Studio**

1. Publiceer de gegevensset Downloaden van UCI: Webservice voor volwassenen 2 klasse.
2. Klik **op Tools** > **NuGet Package Manager** > Package**Manager Console**.
3. Kies **Installatiepakket Microsoft.AspNet.WebApi.Client**.

**Het codevoorbeeld uitvoeren**

1. Publiceer het experiment "Voorbeeld 1: Download gegevensset van UCI: 2-klasse gegevensset voor volwassenen", onderdeel van de voorbeeldverzameling Machine Learning.
2. Bewijs apiKey toe met de sleutel van een webservice. Zie **Hierboven een autorisatiesleutel krijgen.**
3. ServiceUri toewijzen met de URI aanvragen.

**Hier is hoe een compleet verzoek eruit zal zien.**
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

### <a name="python-sample"></a>Python-voorbeeld
Als u verbinding wilt maken met een Machine Learning-webservice, gebruikt u de **urllib2-bibliotheek** voor Python 2.X en **de urllib.request-bibliotheek** voor Python 3.X. U passeert ScoreData, die een FeatureVector bevat, een n-dimensionale vector van numerieke functies die de ScoreData vertegenwoordigt. U verifieert de Machine Learning-service met een API-sleutel.

**Het codevoorbeeld uitvoeren**

1. Implementeren "Voorbeeld 1: Download dataset van UCI: Adult 2 class dataset" experiment, onderdeel van de Machine Learning monsterverzameling.
2. Bewijs apiKey toe met de sleutel van een webservice. Zie het gedeelte **Een autorisatiesleutel opmaken** aan het begin van dit artikel.
3. ServiceUri toewijzen met de URI aanvragen.

**Hier is hoe een compleet verzoek eruit zal zien.**
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

### <a name="r-sample"></a>R-voorbeeld

Als u verbinding wilt maken met een Machine Learning Web Service, gebruikt u de **RCurl-** en **rjson-bibliotheken** om de aanvraag in te dienen en de geretourneerde JSON-respons te verwerken. U passeert ScoreData, die een FeatureVector bevat, een n-dimensionale vector van numerieke functies die de ScoreData vertegenwoordigt. U verifieert de Machine Learning-service met een API-sleutel.

**Hier is hoe een compleet verzoek eruit zal zien.**
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

Als u verbinding wilt maken met een Machine Learning Web Service, gebruikt u het **request** npm-pakket in uw project. U gebruikt het `JSON` object ook om uw invoer op te maken en het resultaat te ontken. Installeren `npm install request --save`met behulp `"request": "*"` van , of `dependencies` toe `npm install`te voegen aan uw package.json onder en uit te voeren .

**Hier is hoe een compleet verzoek eruit zal zien.**
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
