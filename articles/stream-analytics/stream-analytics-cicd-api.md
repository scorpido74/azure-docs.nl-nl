---
title: REST-Api's gebruiken om CI/CD voor Azure Stream Analytics op IoT Edge uit te voeren
description: Informatie over het implementeren van een continue integratie en implementatiepijplijn voor Azure Stream Analytics met REST-API's.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 78f2e06947c2b81ffe5e6cd8a88438db4dabf158
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426422"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>CI/CD voor Stream Analytics implementeren op IoT Edge met behulp van Api's

U kunt continue integratie en implementatie voor Azure Stream Analytics-taken met behulp van REST-API's inschakelen. In dit artikel biedt voorbeelden op welke API's voor het gebruik en het gebruik ervan. REST-API's worden niet ondersteund op Azure Cloud Shell.

## <a name="call-apis-from-different-environments"></a>API's aanroepen vanuit verschillende omgevingen

REST-API's kunnen worden aangeroepen vanuit zowel Windows als Linux. De volgende opdrachten laten zien juiste syntaxis voor de API-aanroep. Het gebruik van specifieke API wordt in latere secties van dit artikel worden beschreven.

### <a name="linux"></a>Linux

Voor Linux, kunt u `Curl` of `Wget` opdrachten:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" { <url> }   
```

```bash
wget -q -O- --{ <method> } -data="<request body>" --header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

Voor Windows, moet u Powershell gebruiken: 

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
 
## <a name="create-an-asa-job-on-edge"></a>Maken van een ASA-taak voor edge-apparaten 
 
Voor het maken van Stream Analytics-taak, roept u de PUT-methode met behulp van de Stream Analytics-API.

|Methode|Aanvraag-URL|
|------|-----------|
|PUT|https://management.azure.com/subscriptions/{**abonnement-id**} /resourcegroups/ {**resource-group-name**} / providers/Microsoft.StreamAnalytics/streamingjobs/ {**taaknaam**}? api-version = 2017-04-01-preview|
 
Voorbeeld van het gebruik van de opdracht **curl**:

```curl
curl -u { <username:password> } -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Voorbeeld van aanvraagtekst in JSON:

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
 
Zie voor meer informatie de [API-documentatie](/rest/api/streamanalytics/stream-analytics-job).  
 
## <a name="publish-edge-package"></a>Edge-pakket publiceren 
 
Aanroepen voor het publiceren van een Stream Analytics-taak in IoT Edge, de POST-methode met de Edge-API voor het publiceren van pakket.

|Methode|Aanvraag-URL|
|------|-----------|
|POST|https://management.azure.com/subscriptions/{**subscriptionid**} /resourceGroups/ {**resourcegroupname**} / providers/Microsoft.StreamAnalytics/streamingjobs/ {**jobname**} / publishedgepackage? api-version = 2017-04-01 - Voorbeeld|

Deze asynchrone bewerking retourneert een status van 202 totdat de taak is gepubliceerd. De locatieheader van het antwoord bevat de URI die wordt gebruikt om op te halen van de status van het proces. Terwijl het proces wordt uitgevoerd, retourneert een aanroep naar de URI in de location-header 202 status. Wanneer het proces is voltooid, is de URI in de location-header geeft als resultaat de status van 200. 

Voorbeeld van een Edge-pakket publiceren met behulp van aanroep **curl**: 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
Nadat u de POST-aanroep, moet u een antwoord met een lege hoofdtekst verwachten. Zoek naar de URL die zich in de kop van het antwoord en leg deze vast voor gebruik.
 
Voorbeeld van de URL van de kop van de reactie:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
Een wachttijd voor het één tot twee minuten voordat u de volgende opdracht uit om te maken van een API-aanroep met de URL die u in de kop van het antwoord gevonden. Als u niet een 200-respons ontvangt, voert u de opdracht opnieuw uit.
 
Voorbeeld van het maken van API-aanroep met geretourneerde URL met **curl**:

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

Het antwoord bevat de informatie die u wilt toevoegen aan het Edge-script voor implementatie. De voorbeelden hieronder ziet u wat informatie die u nodig hebt voor het verzamelen en waar toe te voegen in de implementatie manifest.
 
Voorbeeld van de antwoordtekst na het publiceren is:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["\],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}","PublishTimeStamp":"{publishtimestamp}"}}}}" 
  status : "Succeeded" 
} 
```

Voorbeeld van implementatie-Manifest: 

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

Raadpleeg na de configuratie van de implementatie van het manifest, [implementeren Azure IoT Edge-modules met Azure CLI](../iot-edge/how-to-deploy-modules-cli.md) voor implementatie.


## <a name="next-steps"></a>Volgende stappen 
 
* [Azure Stream Analytics op IoT Edge](stream-analytics-edge.md)
* [ASA on IoT Edge-zelfstudie](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Stream Analytics Edge-taken met behulp van Visual Studio-hulpprogramma's ontwikkelen](stream-analytics-tools-for-visual-studio-edge-jobs.md)
