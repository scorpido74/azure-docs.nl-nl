---
title: REST API's gebruiken om CI/CD voor Azure Stream Analytics op IoT Edge uit te brengen
description: Meer informatie over het implementeren van een pijplijn voor continue integratie en implementatie voor Azure Stream Analytics met REST API's.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 328ca7cd2c6f76095c8334ae6fdb4aa75fbb867d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292006"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>CI/CD implementeren voor Stream Analytics op IoT Edge met API's

U continue integratie en implementatie inschakelen voor Azure Stream Analytics-taken met BEHULP van REST-API's. In dit artikel vindt u voorbeelden van welke API's u moet gebruiken en hoe u deze gebruiken. REST API's worden niet ondersteund op Azure Cloud Shell.

## <a name="call-apis-from-different-environments"></a>Apis bellen vanuit verschillende omgevingen

REST API's kunnen worden aangeroepen vanuit zowel Linux als Windows. In de volgende opdrachten wordt de juiste syntaxis voor de API-aanroep weergegeven. Specifiek API-gebruik wordt in latere secties van dit artikel beschreven.

### <a name="linux"></a>Linux

Voor Linux `Curl` u `Wget` of opdrachten gebruiken:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" { <url> }   
```

```bash
wget -q -O- --{ <method> } -data="<request body>" --header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

Gebruik Powershell voor Windows: 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url> -Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>Een ASA-taak maken op Edge 
 
Als u de taak Stream Analytics wilt maken, roept u de PUT-methode aan met behulp van de Stream Analytics API.

|Methode|Aanvraag-URL|
|------|-----------|
|PUT|`https://management.azure.com/subscriptions/{\**subscription-id**}/resourcegroups/{**resource-group-name**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**job-name**}?api-version=2017-04-01-preview`|
 
Voorbeeld van opdracht met behulp van **krul:**

```curl
curl -u { <username:password> } -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Voorbeeld van aanvraaginstantie in JSON:

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
Zie de [API-documentatie](/rest/api/streamanalytics/stream-analytics-job)voor meer informatie.  
 
## <a name="publish-edge-package"></a>Edge-pakket publiceren 
 
Als u een functie Stream Analytics op IoT Edge wilt publiceren, roept u de METHODE POST aan met de Edge Package Publish API.

|Methode|Aanvraag-URL|
|------|-----------|
|POST|`https://management.azure.com/subscriptions/{\**subscriptionid**}/resourceGroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**jobname**}/publishedgepackage?api-version=2017-04-01-preview`|

Deze asynchrone bewerking retourneert een status van 202 totdat de taak is gepubliceerd. De koptekst voor locatierespons bevat de URI die wordt gebruikt om de status van het proces te krijgen. Terwijl het proces wordt uitgevoerd, geeft een aanroep naar de URI in de locatiekopeen status van 202. Wanneer het proces is voltooid, geeft de URI in de locatiekop een status van 200 terug. 

Voorbeeld van een Edge-pakket publiceren oproep met behulp van **krul:** 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
Na het maken van de POST oproep, moet u verwachten dat een reactie met een leeg lichaam. Zoek naar de URL in het hoofd van het antwoord en neem deze op voor verder gebruik.
 
Voorbeeld van de URL van het hoofd van de respons:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
Een wachttijd voor één tot twee minuten voordat u de volgende opdracht uitvoert om een API-aanroep te maken met de URL die u in het hoofd van het antwoord hebt gevonden. Probeer de opdracht opnieuw als u geen 200-antwoord krijgt.
 
Voorbeeld van het maken van API-aanroep met geretourneerde URL met **krul:**

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

Het antwoord bevat de informatie die u moet toevoegen aan het Edge-implementatiescript. De onderstaande voorbeelden laten zien welke informatie u moet verzamelen en waar u deze toevoegen in het implementatiemanifest.
 
Voorbeeldreactiebody na publicatie:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["\],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}","PublishTimeStamp":"{publishtimestamp}"}}}}" 
  status : "Succeeded" 
} 
```

Voorbeeld van implementatiemanifest: 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

Na de configuratie van het implementatiemanifest verwijzen we je naar [Azure IoT Edge-modules implementeren met Azure CLI](../iot-edge/how-to-deploy-modules-cli.md) voor implementatie.


## <a name="next-steps"></a>Volgende stappen 
 
* [Azure Stream Analytics op IoT Edge](stream-analytics-edge.md)
* [ASA op IoT Edge-zelfstudie](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Stream Analytics Edge-taken ontwikkelen met behulp van Visual Studio-hulpprogramma's](stream-analytics-tools-for-visual-studio-edge-jobs.md)
