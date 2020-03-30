---
title: Voorbeeld van aangepaste vaardigheid (Python)
titleSuffix: Azure Cognitive Search
description: Voor Python-ontwikkelaars leert u de hulpprogramma's en technieken voor het bouwen van een aangepaste vaardigheid met Azure-functies en Visual Studio. Aangepaste vaardigheden bevatten door de gebruiker gedefinieerde modellen of logica die u toevoegen aan een met AI verrijkte indexeringspijplijn in Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: fc69761a05ea381d39d58d5ebf0046e0d9874961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210462"
---
# <a name="example-create-a-custom-skill-using-python"></a>Voorbeeld: Een aangepaste vaardigheid maken met Python

In dit azure cognitive search-vaardigheidssetvoorbeeld leert u hoe u een aangepaste web-API-vaardigheid maakt met Python en Visual Studio Code. In het voorbeeld wordt een [Azure-functie](https://azure.microsoft.com/services/functions/) gebruikt die de [aangepaste vaardigheidsinterface](cognitive-search-custom-skill-interface.md)implementeert.

De aangepaste vaardigheid is eenvoudig door het ontwerp (het concatenates twee snaren) zodat u zich concentreren op de tools en technologieën die worden gebruikt voor aangepaste vaardigheid ontwikkeling in Python. Zodra u slaagt met een eenvoudige vaardigheid, u vertakken met meer complexe scenario's.

## <a name="prerequisites"></a>Vereisten

+ Bekijk de [aangepaste vaardigheidsinterface](cognitive-search-custom-skill-interface.md) voor een introductie in de invoer-/uitvoerinterface die een aangepaste vaardigheid moet implementeren.

+ Stel uw omgeving in. We hebben [deze zelfstudie end-to-end](https://docs.microsoft.com/azure/python/tutorial-vs-code-serverless-python-01) gevolgd om de Azure-functie zonder server in te stellen met behulp van Visual Studio Code- en Python-extensies. De zelfstudie leidt u door de installatie van de volgende gereedschappen en componenten: 

  + [Python 3.75](https://www.python.org/downloads/release/python-375/)
  + [Visual Studio-code](https://code.visualstudio.com/)
  + [Python-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Core-hulpprogramma's voor Azure-functies](https://docs.microsoft.com/azure/azure-functions/functions-run-local#v2)
  + [Azure Functions extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) (Azure Functions-extensie voor Visual Studio Code)

## <a name="create-an-azure-function"></a>Een Azure-functie maken

In dit voorbeeld wordt een Azure-functie gebruikt om het concept van het hosten van een web-API aan te tonen, maar andere benaderingen zijn mogelijk. Zolang je voldoet aan de [interface-eisen voor een cognitieve vaardigheid,](cognitive-search-custom-skill-interface.md)de aanpak die u neemt is onbelangrijk. Azure Functions maakt het echter eenvoudig om een aangepaste vaardigheid te maken.

### <a name="create-a-function-app"></a>Een functie-app maken

De Azure Functions-projectsjabloon in Visual Studio Code maakt een project dat kan worden gepubliceerd in een functie-app in Azure. Met een functie-app kunt u functies groeperen in een logische eenheid, zodat u resources kunt beheren, implementeren en delen.

1. Druk in Visual Studio Code op F1 om het opdrachtpalet te openen. Zoek en selecteer `Azure Functions: Create new project...`in het opdrachtpalet .

1. Kies een maplocatie voor uw projectwerkruimte en kies **Selecteren**.

    > [!NOTE]
    > Deze stappen zijn ontworpen om buiten een werkruimte te worden voltooid. Selecteer daarom geen projectmap die deel uitmaakt van een werkruimte.

1. Selecteer een taal voor uw functie-app-project. Selecteer Voor deze zelfstudie **Python**.
1. Selecteer de Python-versie (versie 3.7.5 wordt ondersteund door Azure-functies)
1. Selecteer een sjabloon voor de eerste functie van uw project. Selecteer **HTTP-trigger** om een HTTP-geactiveerde functie te maken in de nieuwe functie-app.
1. Geef een functienaam op. In dit geval, laten we gebruik maken **van Concatenator** 
1. Selecteer **Functie** als machtigingsniveau. Dit betekent dat we een [functiesleutel](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) bieden om het HTTP-eindpunt van de functie aan te roepen. 
1. Selecteer hoe u uw project wilt openen. Selecteer voor deze stap **Toevoegen aan werkruimte om** de functie-app in de huidige werkruimte te maken.

Het functie-appproject wordt in een nieuwe werkruimte gemaakt. Het project bevat de configuratiebestanden [host.json](../azure-functions/functions-host-json.md) en [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file), plus eventuele taalspecifieke projectbestanden. 

Er wordt ook een nieuwe HTTP-geactiveerde functie gemaakt in de map **Concatenator** van het functie-app-project. Binnen in het zal er\_\_een bestand genaamd " init__.py", met deze inhoud:

```py
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )

```

Laten we nu die code aanpassen om de [aangepaste vaardigheidsinterface](cognitive-search-custom-skill-interface.md)te volgen). Wijzig de code met de volgende inhoud:

```py
import logging
import azure.functions as func
import json

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    try:
        body = json.dumps(req.get_json())
    except ValueError:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )
    
    if body:
        result = compose_response(body)
        return func.HttpResponse(result, mimetype="application/json")
    else:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )


def compose_response(json_data):
    values = json.loads(json_data)['values']
    
    # Prepare the Output before the loop
    results = {}
    results["values"] = []
    
    for value in values:
        output_record = transform_value(value)
        if output_record != None:
            results["values"].append(output_record)
    return json.dumps(results, ensure_ascii=False)

## Perform an operation on a record
def transform_value(value):
    try:
        recordId = value['recordId']
    except AssertionError  as error:
        return None

    # Validate the inputs
    try:         
        assert ('data' in value), "'data' field is required."
        data = value['data']        
        assert ('text1' in data), "'text1' field is required in 'data' object."
        assert ('text2' in data), "'text2' field is required in 'data' object."
    except AssertionError  as error:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Error:" + error.args[0] }   ]       
            })

    try:                
        concatenated_string = value['data']['text1'] + " " + value['data']['text2']  
        # Here you could do something more interesting with the inputs

    except:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Could not complete operation for record." }   ]       
            })

    return ({
            "recordId": recordId,
            "data": {
                "text": concatenated_string
                    }
            })
```

De **transform_value** methode voert een bewerking uit op één record. U de methode aanpassen om aan uw specifieke behoeften te voldoen. Vergeet niet om de benodigde invoervalidatie uit te voeren en eventuele fouten en waarschuwingen die zijn geproduceerd als de bewerking niet voor de record kon worden voltooid, terug te sturen.

### <a name="debug-your-code-locally"></a>Uw code lokaal debuggen

Visual Studio Code maakt het gemakkelijk om de code te debuggen. Druk op 'F5' of ga naar het menu **Foutopsporing** en selecteer **Foutopsporing starten**.

U alle breekpunten op de code door te raken 'F9' op de lijn van belang.

Zodra u bent begonnen met debuggen, wordt uw functie lokaal uitgevoerd. U een tool zoals Postman of Fiddler gebruiken om het verzoek aan localhost uit te geven. Let op de locatie van uw lokale eindpunt in het terminalvenster. 

## <a name="publish-your-function"></a>Uw functie publiceren

Wanneer u tevreden bent met het functiegedrag, u het publiceren.

1. Druk in Visual Studio Code op F1 om het opdrachtpalet te openen. Zoek en selecteer In het opdrachtpalet **Deploy naar functie-app...**. 

1. Selecteer het Azure-abonnement waar u uw toepassing wilt implementeren.

1. Selecteren **+ Nieuwe functie-app maken in Azure**

1. Voer een wereldwijd unieke naam in voor uw functie-app.

1. Selecteer Python-versie (Python 3.7.x werkt voor deze functie).

1. Selecteer een locatie voor de nieuwe resource (bijvoorbeeld West US 2).

Op dit moment worden de benodigde resources gemaakt in uw Azure-abonnement om de nieuwe Azure-functie op Azure te hosten. Wacht totdat de installatie is voltooid. In het uitvoervenster ziet u de status van het implementatieproces.

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar **Alle bronnen** en zoek naar de functie die u onder de naam hebt gepubliceerd. Als u de **concatenator**hebt genoemd, selecteert u de resource.

1. Klik op de knop URL van **de functie</> ophalen.** Hiermee u de URL kopiëren om de functie aan te roepen.

## <a name="test-the-function-in-azure"></a>De functie testen in Azure

Nu u de standaardhostsleutel hebt, test u uw functie als volgt:

```http
POST [Function URL you copied above]
```

### <a name="request-body"></a>Aanvraagtekst
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "text1":  "Hello",
                "text2":  "World"
            }
        },
        {
            "recordId": "e2",
            "data": "This is an invalid input"
        }
    ]
}
```

In dit voorbeeld moet hetzelfde resultaat worden opgeleverd dat u eerder hebt gezien bij het uitvoeren van de functie in de lokale omgeving.

## <a name="connect-to-your-pipeline"></a>Verbinding maken met uw pijplijn

Nu je een nieuwe aangepaste vaardigheid hebt, kun je deze toevoegen aan je vaardigheden. In het onderstaande voorbeeld ziet u hoe u de vaardigheid aanroepen om de titel en de auteur van het document samen te stellen tot één veld dat we merged_title_author noemen. Vervang `[your-function-url-here]` door de URL van uw nieuwe Azure-functie.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new search custom skill",
        "uri": "https://[your-function-url-here]",        
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "text1",
              "source": "/document/metadata_title"
            },
            {
              "name": "text2",
              "source": "/document/metadata_author"
            },
          ],
          "outputs": [
            {
              "name": "text",
              "targetName": "merged_title_author"
            }
          ]
      }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen
Gefeliciteerd! Je hebt je eerste aangepaste vaardigheid gemaakt. Nu u hetzelfde patroon volgen om uw eigen aangepaste functionaliteit toe te voegen. Klik op de volgende links voor meer informatie.

+ [Power Skills: een opslagplaats van aangepaste vaardigheden](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Een aangepaste vaardigheid toevoegen aan een AI-verrijkingspijplijn](cognitive-search-custom-skill-interface.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
+ [Skillset (REST) maken](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Verrijkte velden in kaart brengen](cognitive-search-output-field-mapping.md)
