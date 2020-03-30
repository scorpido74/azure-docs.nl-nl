---
title: REST gebruiken om ML-resources te beheren
titleSuffix: Azure Machine Learning
description: RestAPI's gebruiken om Azure ML-resources te maken, uit te voeren en te verwijderen
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/31/2020
ms.openlocfilehash: 419dbd998abc5cbd2da64a990e13d46f3fb2efbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77580625"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Azure ML-resources maken, uitvoeren en verwijderen met REST

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Er zijn verschillende manieren om uw Azure ML-resources te beheren. U de [portal,](https://portal.azure.com/) [command-line interface](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)of [Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)gebruiken. U ook kiezen voor de REST API. De REST API gebruikt HTTP-werkwoorden op een standaardmanier om resources te maken, op te halen, bij te werken en te verwijderen. De REST API werkt met elke taal of tool die HTTP-aanvragen kan indienen. De eenvoudige structuur van REST maakt het vaak een goede keuze in scriptingomgevingen en voor MLOps-automatisering. 

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een autorisatietoken ophalen
> * Een restaanvraag met correct opgemaakte aanvraag maken met behulp van serviceprincipal-verificatie
> * GET-aanvragen gebruiken om informatie op te halen over de hiërarchische bronnen van Azure ML
> * PUT- en POST-aanvragen gebruiken om resources te maken en te wijzigen
> * Delete-aanvragen gebruiken om resources op te schonen 
> * Sleutelautorisatie gebruiken om geïmplementeerde modellen te scoren

## <a name="prerequisites"></a>Vereisten

- Een **Azure-abonnement** waarvoor u beheerdersrechten hebt. Als je een dergelijk abonnement niet hebt, probeer dan het [gratis of betaalde persoonlijke abonnement](https://aka.ms/AMLFree)
- Een [Azure Machine Learning Workspace](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
- Administratieve REST-aanvragen maken gebruik van serviceprincipal-verificatie. De stappen uitvoeren in [Verificatie instellen voor Azure Machine Learning-resources en -werkstromen](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) om een serviceprincipal in uw werkruimte te maken
- Het **krulnut.** Het **curl-programma** is beschikbaar in het [Windows-subsysteem voor Linux](https://aka.ms/wslinstall/) of een UNIX-distributie. In PowerShell is **curl** een alias voor `curl -d "key=val" -X POST uri` `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri` **Invoke-WebRequest** en wordt . 

## <a name="retrieve-a-service-principal-authentication-token"></a>Een serviceprincipal-verificatietoken ophalen

Administratieve REST-aanvragen worden geverifieerd met een impliciete oauth2-stroom. Deze verificatiestroom maakt gebruik van een token dat wordt verstrekt door de serviceprincipal van uw abonnement. Als u dit token wilt ophalen, hebt u het oog op:

- Uw tenant-id (het identificeren van de organisatie waartoe uw abonnement behoort)
- Uw client-id (die wordt gekoppeld aan het gemaakte token)
- Uw klant geheim (die u moet beschermen)

Deze waarden moeten worden weergegeven vanaf het antwoord op de creatie van uw serviceprincipal zoals besproken in [Verificatie instellen voor Azure Machine Learning-resources en -werkstromen.](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) Als u uw bedrijfsabonnement gebruikt, hebt u mogelijk geen toestemming om een serviceprincipal te maken. In dat geval moet u een [gratis of betaald persoonlijk abonnement](https://aka.ms/AMLFree)gebruiken.

Ga als lid van het opmaken van een token:

1. Een terminalvenster openen
1. Voer de volgende code in op de opdrachtregel
1. Vervang uw eigen `{your-tenant-id}` `{your-client-id}`waarden `{your-client-secret}`voor , en . In dit artikel, snaren omgeven door krullende haakjes zijn variabelen die u moet vervangen door uw eigen juiste waarden.
1. De opdracht uitvoeren

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

Het antwoord moet een toegangstoken bieden dat goed is voor een uur:

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

Noteer het token, omdat u het gebruikt om alle volgende administratieve verzoeken te verifiëren. U doet dit door een autorisatiekop in te stellen in alle aanvragen:

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

Houd er rekening mee dat de waarde begint met de tekenreeks 'Drager' inclusief een enkele spatie voordat u het token toevoegt.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Een lijst met resourcegroepen die aan uw abonnement zijn gekoppeld

Voer het als nodig op om de lijst met resourcegroepen op te halen die aan uw abonnement zijn gekoppeld:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

In Azure worden veel REST API's gepubliceerd. Elke serviceprovider werkt zijn API bij op zijn eigen cadans, maar doet dit zonder bestaande programma's te breken. De serviceprovider gebruikt `api-version` het argument om compatibiliteit te garanderen. Het `api-version` argument varieert van service tot service. Voor de Machine Learning-service is de `2019-11-01`huidige API-versie bijvoorbeeld . Voor opslagaccounts is `2019-06-01`het. Voor sleutelkluizen is `2019-09-01`het. Alle REST-aanroepen `api-version` moeten het argument instellen op de verwachte waarde. U vertrouwen op de syntaxis en semantiek van de opgegeven versie, zelfs als de API blijft evolueren. Als u een verzoek zonder `api-version` argument naar een provider verzendt, bevat het antwoord een door de mens leesbare lijst met ondersteunde waarden. 

De bovenstaande oproep zal resulteren in een verdichte JSON reactie van het formulier: 

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


## <a name="drill-down-into-workspaces-and-their-resources"></a>Inzoomen op werkruimten en hun bronnen

Als u de set werkruimten in een resourcegroep `{your-subscription-id}`wilt `{your-resource-group}`ophalen, voert u het volgende uit, waarbij u de volgende handelingen uitvoert, waarbij u vervangt en `{your-access-token}`: 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Opnieuw ontvangt u een JSON-lijst, deze keer met een lijst, waarvan elk item een werkruimte beschrijft:

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

Als u wilt werken met resources in een werkruimte, schakelt u over van de algemene **management.azure.com** server naar een REST API-server die specifiek is voor de locatie van de werkruimte. Let op de `discoveryUrl` waarde van de sleutel in de bovenstaande JSON-respons. Als je die URL ontvangt, ontvang je een antwoord als:

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

De waarde `api` van het antwoord is de URL van de server die u gebruikt voor aanvullende aanvragen. Als u experimenten wilt weergeven, stuurt u bijvoorbeeld de volgende opdracht. Vervang `regional-api-server` door de `api` waarde van het `centralus.api.azureml.ms`antwoord (bijvoorbeeld). Ook `your-subscription-id`vervangen `your-resource-group` `your-workspace-name`, `your-access-token` , , en zoals gebruikelijk:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Als u geregistreerde modellen in uw werkruimte wilt ophalen, verzendt u ook:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Merk op dat om experimenten `history/v1.0` op te sommen waarmee `modelmanagement/v1.0`het pad begint terwijl ze modellen moeten weergeven, het pad begint met . De REST API is verdeeld in verschillende operationele groepen, elk met een apart pad. De API-verwijzingsdocumenten op de onderstaande koppelingen geven de bewerkingen, parameters en antwoordcodes voor de verschillende bewerkingen weer.

|Onderwerp|Pad|Naslaginformatie|
|-|-|-|
|Artefacten|artefact/v2.0/|[REST API-verwijzing](https://docs.microsoft.com/rest/api/azureml/artifacts)|
|Gegevenswinkels|datastore/v1.0/|[REST API-verwijzing](https://docs.microsoft.com/rest/api/azureml/datastores)|
|Hyperparameters afstemmen|hyperdrive/v1.0/|[REST API-verwijzing](https://docs.microsoft.com/rest/api/azureml/hyperparametertuning)|
|Modellen|modelmanagement/v1.0/|[REST API-verwijzing](https://docs.microsoft.com/rest/api/azureml/modelsanddeployments/mlmodels)|
|Uitvoer.gesch|uitvoering/v1.0/ en geschiedenis/v1.0/|[REST API-verwijzing](https://docs.microsoft.com/rest/api/azureml/runs)|

U de REST API verkennen met behulp van het algemene patroon van:

|URL-component|Voorbeeld|
|-|-|
| https://| |
| regionale-api-server/ | centralus.api.azureml.ms/ |
| operations-path/ | geschiedenis/v1.0/ |
| abonnementen/{your-subscription-id}/ | abonnementen/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroepen/{uw-resourcegroep}/ | resourceGroepen/MyResourceGroup/ |
| providers/operation-provider/ | providers/Microsoft.MachineLearningServices/ |
| provider-resource-path/ | werkruimten/MLWorkspace/MyWorkspace/FirstExperiment/runs/1/ |
| operations-endpoint/ | artefacten/metagegevens/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Bronnen maken en wijzigen met PUT- en POST-aanvragen

Naast het ophalen van resources met het GET-werkwoord, ondersteunt de REST API het maken van alle resources die nodig zijn om ML-oplossingen te trainen, implementeren en bewaken. 

Voor het trainen en uitvoeren van ML-modellen zijn rekenresources vereist. U de rekenbronnen van een werkruimte weergeven met: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Als u een benoemde compute resource wilt maken of overschrijven, gebruikt u een PUT-aanvraag. In het volgende, naast de nu bekende `your-subscription-id` `your-resource-group`vervangingen van , `your-workspace-name`, en `your-access-token` `vmPriority`, `scaleSettings` `adminUserName`substituut `your-compute-name`, en waarden voor `location`, `vmSize`, , , , en `adminUserPassword`. Zoals opgegeven in de verwijzing bij [Machine Learning Compute - Create Or Update SDK Reference,](https://docs.microsoft.com/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate)maakt de volgende opdracht een speciale Standard_D1 met één knooppunt (een basis-CPU-compute resource) die na 30 minuten wordt afgebroken:

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
> In Windows-terminals moet u mogelijk ontsnappen aan de dubbele aanhalingstekens bij het verzenden van JSON-gegevens. Dat wil zeggen, `"location"` `\"location\"`tekst zoals wordt . 

Een succesvol verzoek `201 Created` krijgt een antwoord, maar houd er rekening mee dat dit antwoord gewoon betekent dat het inrichtingsproces is begonnen. Je moet de portal peilen (of gebruiken) om de succesvolle voltooiing ervan te bevestigen.

### <a name="create-an-experimental-run"></a>Een experimentele run maken

Als u een run in een experiment wilt starten, hebt u een zip-map nodig met uw trainingsscript en gerelateerde bestanden en een JSON-bestand met rundefinitie. De zip-map moet het Python-invoerbestand in de hoofdmap hebben. Als voorbeeld, rits een triviaal Python-programma zoals het volgende in een map genaamd **train.zip**.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Sla dit volgende fragment op als **definition.json**. Bevestig dat de waarde 'Script' overeenkomt met de naam van het Python-bestand dat u zojuist hebt opgeritst. Bevestig dat de waarde 'Doel' overeenkomt met de naam van een beschikbare rekenbron. 

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

Plaats deze bestanden op `multipart/form-data` de server met inhoud:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

Een succesvol POST-verzoek `200 OK` genereert een status, met een antwoordtekst die de id van de gemaakte run bevat:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

U een run controleren met behulp van het REST-ful patroon dat nu bekend moet zijn:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>Resources verwijderen die u niet meer nodig hebt

Sommige, maar niet alle bronnen ondersteunen het werkwoord DELETE. Controleer de [API-verwijzing](https://docs.microsoft.com/rest/api/azureml/) voordat u zich verbindt met de REST API voor use-cases voor verwijdering. Als u bijvoorbeeld een model wilt verwijderen, u het:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>REST gebruiken om een geïmplementeerd model te scoren

Hoewel het mogelijk is om een model zo te implementeren dat het wordt geverifieerd met een serviceprincipal, gebruiken de meeste clientgerichte implementaties verificatie op basis van sleutels. De juiste sleutel vindt u op de pagina van uw implementatie op het tabblad **Eindpunten** van Studio. Op dezelfde locatie wordt de score van uw eindpunt URI weergegeven. De ingangen van uw model moeten worden `data`gemodelleerd als een JSON-array met de naam:

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Een werkruimte maken met REST 

Elke Azure ML-werkruimte is afhankelijk van vier andere Azure-resources: een containerregister met beheer ingeschakeld, een sleutelkluis, een Application Insights-bron en een opslagaccount. U geen werkruimte maken totdat deze bronnen bestaan. Raadpleeg de REST API-referentie voor de details van het maken van elk van deze bronnen.

Als u een werkruimte wilt maken, `management.azure.com`plaatst u een aanroep die vergelijkbaar is met het volgende op . Hoewel deze oproep vereist dat u een groot aantal variabelen instelt, is deze structureel identiek aan andere oproepen die in dit artikel zijn besproken. 

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

U ontvangt `202 Accepted` een antwoord en, in de `Location` geretourneerde kopteksten, een URI. U deze URI ophalen voor informatie over de implementatie, inclusief nuttige foutopsporingsgegevens als er een probleem is met een van uw afhankelijke bronnen (bijvoorbeeld als u bent vergeten beheerderstoegang in uw containerregister in te schakelen). 

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="resource-provider-errors"></a>Fouten in resourceprovider

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>De werkruimte verplaatsen

> [!WARNING]
> Het verplaatsen van uw Azure Machine Learning-werkruimte naar een ander abonnement of het verplaatsen van het eigen abonnement naar een nieuwe tenant wordt niet ondersteund. Dit kan fouten veroorzaken.

### <a name="deleting-the-azure-container-registry"></a>Het Azure-containerregister verwijderen

De Azure Machine Learning-werkruimte gebruikt Azure Container Registry (ACR) voor sommige bewerkingen. Het zal automatisch een ACR-exemplaar maken wanneer het er voor het eerst een nodig heeft.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Volgende stappen

- Bekijk de volledige [AzureML REST API-referentie.](https://docs.microsoft.com/rest/api/azureml/)
- Meer informatie over het gebruik van Studio & Designer to [Predict auto prijs met de ontwerper (preview)](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score).
- Ontdek [Azure Machine Learning met Jupyter-notitieblokken.](https://docs.microsoft.com/azure//machine-learning/samples-notebooks)
