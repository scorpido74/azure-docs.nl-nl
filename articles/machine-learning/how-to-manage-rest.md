---
title: REST gebruiken voor het beheren van ML-resources
titleSuffix: Azure Machine Learning
description: REST-Api's gebruiken om Azure ML-resources te maken, uit te voeren en te verwijderen
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/31/2020
ms.openlocfilehash: 6d6e7d564722d1c2ad4713dd1d39e7cba5ed0605
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964954"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Azure ML-resources maken, uitvoeren en verwijderen met REST

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Er zijn verschillende manieren om uw Azure ML-resources te beheren. U kunt de [Portal](https://portal.azure.com/), de [opdracht regel interface](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)of de [python-SDK](https://docs.microsoft.com/python/api/overview/azureml-sdk/?view=azure-ml-py)gebruiken. U kunt ook de REST API kiezen. De REST API maakt gebruik van HTTP-termen op een standaard manier om resources te maken, op te halen, bij te werken en te verwijderen. De REST API werkt met elke taal of elk hulp programma waarmee HTTP-aanvragen kunnen worden gemaakt. Met de eenvoudige structuur van de REST is het vaak een goede keuze in script omgevingen en voor MLOps Automation. 

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een autorisatie Token ophalen
> * Een goed opgemaakte REST-aanvraag maken met Service-Principal-verificatie
> * GET-aanvragen gebruiken om informatie op te halen over de hiërarchische resources van Azure ML
> * PUT-en POST-aanvragen gebruiken om resources te maken en te wijzigen
> * DELETE-aanvragen gebruiken om resources op te schonen 
> * Verificatie op basis van een sleutel gebruiken om geïmplementeerde modellen te scoren

## <a name="prerequisites"></a>Vereisten

- Een **Azure-abonnement** waarvoor u beheerders rechten hebt. Als u geen abonnement hebt, probeer [dan het gratis of betaalde persoonlijke abonnement](https://aka.ms/AMLFree)
- Een [Azure machine learning-werkruimte](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
- Beheer REST-aanvragen gebruiken Service-Principal-verificatie. Volg de stappen in [verificatie instellen voor Azure machine learning resources en werk stromen](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) om een service-principal te maken in uw werk ruimte
- Het **krul** -hulp programma. Het **krul** programma is beschikbaar in het [Windows-subsysteem voor Linux](https://aka.ms/wslinstall/) of een Unix-distributie. In Power shell is **krul** een alias voor **invoke-WebRequest** en wordt `curl -d "key=val" -X POST uri` `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`. 

## <a name="retrieve-a-service-principal-authentication-token"></a>Een Service-Principal-verificatie Token ophalen

Beheer REST-aanvragen worden geverifieerd met een OAuth2 impliciete stroom. Deze verificatie stroom maakt gebruik van een token van de service-principal van uw abonnement. Als u dit token wilt ophalen, hebt u het volgende nodig:

- Uw Tenant-ID (die de organisatie identificeert waartoe uw abonnement behoort)
- De client-ID (die wordt gekoppeld aan het gemaakte token)
- Uw client geheim (dat u moet beveiligen)

U moet deze waarden van het antwoord hebben op het maken van de Service-Principal zoals wordt beschreven in [verificatie instellen voor Azure machine learning resources en werk stromen](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication). Als u uw bedrijfs abonnement gebruikt, bent u mogelijk niet gemachtigd om een service-principal te maken. In dat geval moet u een [gratis of betaald persoonlijk abonnement](https://aka.ms/AMLFree)gebruiken.

Een Token ophalen:

1. Een terminalvenster openen
1. Voer de volgende code in op de opdracht regel
1. Vervang uw eigen waarden door `{your-tenant-id}`, `{your-client-id}`en `{your-client-secret}`. In dit artikel zijn teken reeksen omgeven door accolades variabelen die u moet vervangen door uw eigen juiste waarden.
1. De opdracht uitvoeren

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

Het antwoord moet gedurende één uur een geldig toegangs token bieden:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

Noteer het token, zoals u het gebruikt om alle volgende beheer aanvragen te verifiëren. U doet dit door in alle aanvragen een autorisatie-header in te stellen:

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

Houd er rekening mee dat de waarde begint met de teken reeks ' Bearer ', inclusief één spatie voordat u het token toevoegt.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Een lijst ophalen met resource groepen die zijn gekoppeld aan uw abonnement

Voer de volgende handelingen uit om de lijst met resource groepen die zijn gekoppeld aan uw abonnement op te halen:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

In Azure worden veel REST-Api's gepubliceerd. Elke service provider werkt de API op hun eigen uitgebracht bij, maar zonder de bestaande Program ma's te verbreken. De service provider gebruikt het argument `api-version` om compatibiliteit te garanderen. Het argument `api-version` varieert per service. Voor de Machine Learning-service is de huidige API-versie `2019-11-01`. Voor opslag accounts is het `2019-06-01`. Voor sleutel kluizen is het `2019-09-01`. Bij alle REST-aanroepen moet het argument `api-version` worden ingesteld op de verwachte waarde. U kunt vertrouwen op de syntaxis en de semantiek van de opgegeven versie, zelfs als de API blijft ontwikkelen. Als u een aanvraag verzendt naar een provider zonder het argument `api-version`, bevat het antwoord een lijst met door de mens lees bare waarden die worden ondersteund. 

De bovenstaande aanroep resulteert in een gecomprimeerde JSON-reactie van het formulier: 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>Inzoomen op werk ruimten en hun resources

Als u de set met werk ruimten in een resource groep wilt ophalen, voert u de volgende handelingen uit, vervangt u `{your-subscription-id}`, `{your-resource-group}`en `{your-access-token}`: 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

U ontvangt een JSON-lijst, deze keer met een lijst, elk item waarvan een werk ruimte wordt weer gegeven:

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

Als u wilt werken met resources binnen een werk ruimte, schakelt u van de algemene **Management.Azure.com** -server naar een rest API-server die specifiek is voor de locatie van de werk ruimte. Noteer de waarde van de `discoveryUrl` sleutel in de bovenstaande JSON-reactie. Als u die URL krijgt, ontvangt u een antwoord op de volgende manier:

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

De waarde van de `api` antwoord is de URL van de server die u gaat gebruiken voor aanvullende aanvragen. Als u experimenten wilt weer geven, verzendt u bijvoorbeeld de volgende opdracht. Vervang `regional-api-server` door de waarde van het `api`-antwoord (`centralus.api.azureml.ms`). Vervang `your-subscription-id`, `your-resource-group`, `your-workspace-name`en `your-access-token` zoals gebruikelijk:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Als u geregistreerde modellen wilt ophalen in uw werk ruimte, verzendt u het volgende:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

U ziet dat bij het weer geven van experimenten het pad begint met `history/v1.0` terwijl de lijst modellen wordt weer gegeven, het pad begint met `modelmanagement/v1.0`. De REST API is onderverdeeld in verschillende operationele groepen, elk met een uniek pad. De API-referentie documenten op de koppelingen hieronder staan de bewerkingen, para meters en antwoord codes voor de verschillende bewerkingen.

|Gebied|Pad|Referentie|
|-|-|-|
|Artifacts|artefact/v 2.0/|[Naslaginformatie over REST-API](https://docs.microsoft.com/rest/api/azureml/artifacts)|
|Gegevens archieven|Data Store/v 1.0/|[Naslaginformatie over REST-API](https://docs.microsoft.com/rest/api/azureml/datastores)|
|afstemming afstemmen|Hyperdrive/v 1.0/|[Naslaginformatie over REST-API](https://docs.microsoft.com/rest/api/azureml/hyperparametertuning)|
|Modellen|Modelmanagement/v 1.0/|[Naslaginformatie over REST-API](https://docs.microsoft.com/rest/api/azureml/modelsanddeployments/mlmodels)|
|Uitvoer.gesch|uitvoering/v 1.0/en geschiedenis/v 1.0/|[Naslaginformatie over REST-API](https://docs.microsoft.com/rest/api/azureml/runs)|

U kunt de REST API verkennen met behulp van het algemene patroon van:

|URL-onderdeel|Voorbeeld|
|-|-|
| https://| |
| regionaal-API-server/ | centralus.api.azureml.ms/ |
| bewerkingen-pad/ | geschiedenis/v 1.0/ |
| abonnementen/{uw-abonnement-id}/ | abonnementen/abcde123-ABAB-ABAB-1234-0123456789abc/ |
| resourceGroups/{uw-Resource-Group}/ | resourceGroups/MyResourceGroup/ |
| providers/Operation-provider/ | providers/micro soft. MachineLearningServices/ |
| provider-resource-pad/ | werk ruimten/MLWorkspace/MyWorkspace/FirstExperiment/runs/1/ |
| bewerkingen-eind punt/ | artefacten/meta gegevens/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Resources maken en wijzigen met behulp van PUT-en POST-aanvragen

Naast het ophalen van resources met de bewerking GET, ondersteunt de REST API het maken van alle resources die nodig zijn voor het trainen, implementeren en bewaken van ML-oplossingen. 

Voor trainings-en actieve ML-modellen zijn reken resources vereist. U kunt de reken resources van een werk ruimte weer geven met: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Als u een benoemde Compute-resource wilt maken of overschrijven, gebruikt u een PUT-aanvraag. In de volgende, naast de nu vertrouwde vervangingen van `your-subscription-id`, `your-resource-group`, `your-workspace-name`en `your-access-token`, substitueer `your-compute-name`en waarden voor `location`, `vmSize`, `vmPriority`, `scaleSettings`, `adminUserName`en `adminUserPassword`. Zoals beschreven in de referentie bij [machine learning Compute-SDK-verwijzing maken of bijwerken](https://docs.microsoft.com/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate), maakt de volgende opdracht een toegewezen Standard_D1 met één knoop punt (een basis-CPU-reken resource) die na 30 minuten omlaag wordt geschaald:

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> In Windows-terminals moet u mogelijk de dubbele aanhalings tekens verescapeen bij het verzenden van JSON-gegevens. Dat wil zeggen dat tekst, zoals `"location"`, wordt `\"location\"`. 

Een geslaagde aanvraag ontvangt een `201 Created` antwoord, maar houd er rekening mee dat dit antwoord simpelweg betekent dat het inrichtings proces is gestart. U moet pollen (of de portal gebruiken) om de geslaagde voltooiing te bevestigen.

### <a name="create-an-experimental-run"></a>Een experimentele uitvoering maken

Als u een run binnen een experiment wilt starten, hebt u een zip-map met uw trainings script en gerelateerde bestanden nodig en een JSON-bestand met de definitie van de run. De map zip moet het python-invoer bestand hebben in de hoofdmap. Een voor beeld: een trivial python-programma zoals het volgende in een map met de naam **Train. zip**.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Sla dit volgende fragment op als **definitie. json**. Bevestig dat de waarde ' script ' overeenkomt met de naam van het python-bestand dat u net hebt ingepakt. Bevestig dat de doel waarde overeenkomt met de naam van een beschik bare Compute-resource. 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

Post deze bestanden op de server met behulp van `multipart/form-data` inhoud:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

Met een succes volle POST-aanvraag wordt een `200 OK` status gegenereerd met een antwoord tekst die de id van de gemaakte uitvoering bevat:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

U kunt een uitvoering bewaken met het REST-volledige-patroon dat nu bekend moet zijn:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>Resources verwijderen die u niet meer nodig hebt

Sommige, maar niet alle resources ondersteunen de VERWIJDERings bewerking. Controleer de [API-verwijzing](https://docs.microsoft.com/rest/api/azureml/) voordat u de rest API doorvoert voor het gebruik van verwijderings aanvragen. Als u een model wilt verwijderen, kunt u bijvoorbeeld het volgende gebruiken:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>REST gebruiken om een geïmplementeerd model te scoren

Hoewel het mogelijk is om een model te implementeren, zodat het wordt geverifieerd met een Service-Principal, gebruiken de meeste client gerichte implementaties op basis van sleutel verificatie. U vindt de juiste sleutel op de pagina van de implementatie op het tabblad **eind punten** van Studio. Op dezelfde locatie wordt de Score-URI van uw eind punt weer gegeven. De invoer van uw model moet worden gemodelleerd als een JSON-matrix met de naam `data`:

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Een werk ruimte maken met behulp van REST 

Elke Azure ML-werk ruimte heeft een afhankelijkheid van vier andere Azure-resources: een container register met ingeschakelde beheerders, een sleutel kluis, een Application Insights bron en een opslag account. U kunt pas een werk ruimte maken als deze resources bestaan. Raadpleeg de REST API verwijzing voor de details van het maken van deze resources.

Als u een werk ruimte wilt maken, plaatst u een gesprek dat lijkt op het volgende: `management.azure.com`. Hoewel voor deze aanroep een groot aantal variabelen moet worden ingesteld, is deze structuur identiek aan andere aanroepen die in dit artikel worden besproken. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

U ontvangt een `202 Accepted` antwoord en, in de geretourneerde headers, een `Location`-URI. U kunt deze URI ophalen voor informatie over de implementatie, met inbegrip van nuttige informatie over fout opsporing als er een probleem is met een van de afhankelijke bronnen (bijvoorbeeld als u bent verg eten om beheerders toegang in te scha kelen in uw container register). 

## <a name="next-steps"></a>Volgende stappen

- Verken de volledige [referentie voor AzureML-rest API](https://docs.microsoft.com/rest/api/azureml/).
- Meer informatie over het gebruik van Studio & Designer voor het voors [pellen van prijzen voor auto Mobile met de ontwerp functie (preview)](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score).
- Verken [Azure machine learning met Jupyter-notebooks](https://docs.microsoft.com/azure//machine-learning/samples-notebooks).
