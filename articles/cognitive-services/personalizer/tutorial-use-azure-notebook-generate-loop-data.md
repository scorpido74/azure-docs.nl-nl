---
title: 'Zelfstudie: Azure Notebook - Personalizer'
titleSuffix: Azure Cognitive Services
description: Deze zelfstudie simuleert een Personalizer-lus _system in een Azure Notebook, die suggereert welk type koffie een klant moet bestellen. De gebruikers en hun voorkeuren worden opgeslagen in een gebruikersgegevensset. Informatie over de koffie is ook beschikbaar en opgeslagen in een koffiedataset.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: diberry
ms.openlocfilehash: 03e8b658f7edf4640d738e5ea3af84953185d0f5
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986832"
---
# <a name="tutorial-use-personalizer-in-azure-notebook"></a>Zelfstudie: Personalizer gebruiken in Azure Notebook

In deze zelfstudie wordt een personalizerlus uitgevoerd in een Azure Notebook, waarin de levenscyclus van een personalizerlus wordt weergegeven.

De lus suggereert welk type koffie een klant moet bestellen. De gebruikers en hun voorkeuren worden opgeslagen in een gebruikersgegevensset. Informatie over de koffie wordt opgeslagen in een koffiedataset.

## <a name="users-and-coffee"></a>Gebruikers en koffie

Het notitieblok, dat de interactie van de gebruiker met een website simuleert, selecteert een willekeurige gebruiker, het tijdstip van de dag en het type weer uit de gegevensset. Een samenvatting van de gebruikersinformatie is:

|Klanten - contextfuncties|Tijden van de Dag|Soorten weer|
|--|--|--|
|Alice<br>Bob<br>Cathy<br>Dave|Ochtend<br>Middag<br>Avond|Zonnige<br>Regenachtige<br>Besneeuwde|

Om Personalizer te helpen leren, weet het _systeem_ na verloop van tijd ook details over de koffieselectie voor elke persoon.

|Koffie - actiefuncties|Soorten temperatuur|Plaatsen van oorsprong|Soorten gebraden vlees|organisch|
|--|--|--|--|--|
|Cappacino|Warm|Kenia|Donker|organisch|
|Koud brouwsel|Koud|Brazilië|Licht|organisch|
|IJsmok|Koud|Ethiopië|Licht|Niet biologisch|
|Latte|Warm|Brazilië|Donker|Niet biologisch|

Het **doel** van de Personalizer loop is om de beste match tussen de gebruikers en de koffie zo veel mogelijk van de tijd te vinden.

De code voor deze zelfstudie is beschikbaar in de [Personalizer Samples GitHub repository.](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook)

## <a name="how-the-simulation-works"></a>Hoe de simulatie werkt

Aan het begin van het loopsysteem zijn de suggesties van Personalizer slechts 20% tot 30% succesvol. Dit succes wordt aangegeven door de beloning die wordt teruggestuurd naar Personalizer's Reward API, met een score van 1. Na een aantal Rank en Reward gesprekken, het systeem verbetert.

Voer na de eerste aanvragen een offline evaluatie uit. Hierdoor kan Personalizer de gegevens bekijken en een beter leerbeleid voorstellen. Pas het nieuwe leerbeleid toe en voer het notitieblok opnieuw uit met 20% van het vorige aantal aanvragen. De lus zal beter presteren met het nieuwe leerbeleid.

## <a name="rank-and-reward-calls"></a>Rang- en beloningsoproepen

Voor elk van de paar duizend oproepen naar de Personalizer-service stuurt het Azure-notitieblok het **rank-verzoek** naar de REST-API:

* Een unieke id voor het evenement Rang/Aanvraag
* Contextfuncties - Een willekeurige keuze van de gebruiker, het weer en het tijdstip van de dag - het simuleren van een gebruiker op een website of mobiel apparaat
* Acties met functies - _Alle_ koffiegegevens - waaruit Personalizer een suggestie doet

Het systeem ontvangt de aanvraag en vergelijkt die voorspelling vervolgens met de bekende keuze van de gebruiker voor hetzelfde tijdstip van de dag en het weer. Als de bekende keuze hetzelfde is als de voorspelde keuze, wordt de **beloning** van 1 teruggestuurd naar Personalizer. Anders is de teruggezonden beloning 0.

> [!Note]
> Dit is een simulatie, zodat het algoritme voor de beloning is eenvoudig. In een real-world scenario moet het algoritme bedrijfslogica gebruiken, mogelijk met gewichten voor verschillende aspecten van de ervaring van de klant, om de beloningsscore te bepalen.


## <a name="prerequisites"></a>Vereisten

* Een [Azure Notebook-account.](https://notebooks.azure.com/)
* Een [Azure Personalizer-bron](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
    * Als u de personalizerbron al hebt gebruikt, moet u de gegevens in de Azure-portal voor de bron [wissen.](how-to-settings.md#clear-data-for-your-learning-loop)
* Upload alle bestanden voor [dit voorbeeld](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) naar een Azure Notebook-project.

Bestandsbeschrijvingen:

* [Personalizer.ipynb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/Personalizer.ipynb) is de Jupyter notebook voor deze tutorial.
* [De gegevensset van de gebruiker](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/users.json) wordt opgeslagen in een JSON-object.
* [De gegevensset voor koffie](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/coffee.json) wordt opgeslagen in een JSON-object.
* [Voorbeeldaanvraag JSON](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/example-rankrequest.json) is de verwachte indeling voor een POST-aanvraag voor de Rank-API.

## <a name="configure-personalizer-resource"></a>Gepersonaliseerde bron configureren

Configureer in de [Azure-portal uw Personalizer-bron](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) met de frequentie van het **updatemodel** ingesteld op 15 seconden en een wachttijd voor **beloning** van 15 seconden. Deze waarden zijn te vinden op de pagina **[Configuratie.](how-to-settings.md#configure-service-settings-in-the-azure-portal)**

|Instelling|Waarde|
|--|--|
|updatemodelfrequentie|15 seconden|
|beloning wachttijd|15 seconden|

Deze waarden hebben een zeer korte duur om wijzigingen in deze zelfstudie weer te geven. Deze waarden mogen niet worden gebruikt in een productiescenario zonder te valideren dat ze uw doel bereiken met uw Personalizer-lus.

## <a name="set-up-the-azure-notebook"></a>Het Azure-notitieblok instellen

1. De kernel `Python 3.6`wijzigen in .
1. Open het `Personalizer.ipynb`-bestand.

## <a name="run-notebook-cells"></a>Notitieblokcellen uitvoeren

Voer elke uitvoerbare cel uit en wacht tot deze is terug. U weet dat dit gebeurt wanneer de haakjes naast de `*`cel een getal weergeven in plaats van een . In de volgende secties wordt uitgelegd wat elke cel programmatisch doet en wat u verwachten voor de uitvoer.

### <a name="include-the-python-modules"></a>De python-modules opnemen

Neem de vereiste python-modules op. De cel heeft geen uitvoer.

```python
import json
import matplotlib.pyplot as plt
import random
import requests
import time
import uuid
```

### <a name="set-personalizer-resource-key-and-name"></a>Persoonlijkeresourcessleutel en -naam instellen

Zoek in de Azure-portal uw sleutel en eindpunt op de **Quickstart-pagina** van uw personalizerbron. Wijzig de `<your-resource-name>` waarde van de naam van uw personalizerbron. Wijzig de `<your-resource-key>` waarde van uw personalizersleutel.

```python
# Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
personalization_base_url = "https://<your-resource-name>.cognitiveservices.azure.com/"
resource_key = "<your-resource-key>"
```

### <a name="print-current-date-and-time"></a>Huidige datum en tijd afdrukken
Gebruik deze functie om de begin- en eindtijden van de iteraties van de iteraties op te merken.

Deze cellen hebben geen output. De functie geeft de huidige datum en tijd uit wanneer deze wordt aangeroepen.

```python
# Print out current datetime
def currentDateTime():
    currentDT = datetime.datetime.now()
    print (str(currentDT))
```

### <a name="get-the-last-model-update-time"></a>De laatste updatetijd van het model ophalen

Wanneer de `get_last_updated`functie , wordt aangeroepen, drukt de functie de laatst gewijzigde datum en tijd af waarop het model is bijgewerkt.

Deze cellen hebben geen output. De functie geeft de laatste modeltrainingsdatum uit wanneer deze wordt aangeroepen.

De functie maakt gebruik van een GET REST API om modeleigenschappen te [krijgen.](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/GetModelProperties)

```python
# ititialize variable for model's last modified date
modelLastModified = ""
```

```python
def get_last_updated(currentModifiedDate):

    print('-----checking model')

    # get model properties
    response = requests.get(personalization_model_properties_url, headers = headers, params = None)

    print(response)
    print(response.json())

    # get lastModifiedTime
    lastModifiedTime = json.dumps(response.json()["lastModifiedTime"])

    if (currentModifiedDate != lastModifiedTime):
        currentModifiedDate = lastModifiedTime
        print(f'-----model updated: {lastModifiedTime}')
```

### <a name="get-policy-and-service-configuration"></a>Beleid en serviceconfiguratie ophalen

Valideer de status van de service met deze twee REST-oproepen.

Deze cellen hebben geen output. De functie geeft de servicewaarden wel weer wanneer deze worden aangeroepen.

```python
def get_service_settings():

    print('-----checking service settings')

    # get learning policy
    response = requests.get(personalization_model_policy_url, headers = headers, params = None)

    print(response)
    print(response.json())

    # get service settings
    response = requests.get(personalization_service_configuration_url, headers = headers, params = None)

    print(response)
    print(response.json())
```

### <a name="construct-urls-and-read-json-data-files"></a>URL's en json-gegevensbestanden lezen

Deze cel

* bouwt de URL's die worden gebruikt in REST-aanroepen
* hiermee stelt u de beveiligingskop in met uw personalizer-bronsleutel
* hiermee wordt het willekeurige zaad voor de Rank-gebeurtenis-id ingesteld
* leest in de JSON-gegevensbestanden
* aanroepmethode `get_last_updated` - leerbeleid is verwijderd in voorbeelduitvoer
* aanroepen `get_service_settings` methode

De cel heeft uitvoer `get_last_updated` van `get_service_settings` de aanroep naar en functies.

```python
# build URLs
personalization_rank_url = personalization_base_url + "personalizer/v1.0/rank"
personalization_reward_url = personalization_base_url + "personalizer/v1.0/events/" #add "{eventId}/reward"
personalization_model_properties_url = personalization_base_url + "personalizer/v1.0/model/properties"
personalization_model_policy_url = personalization_base_url + "personalizer/v1.0/configurations/policy"
personalization_service_configuration_url = personalization_base_url + "personalizer/v1.0/configurations/service"

headers = {'Ocp-Apim-Subscription-Key' : resource_key, 'Content-Type': 'application/json'}

# context
users = "users.json"

# action features
coffee = "coffee.json"

# empty JSON for Rank request
requestpath = "example-rankrequest.json"

# initialize random
random.seed(time.time())

userpref = None
rankactionsjsonobj = None
actionfeaturesobj = None

with open(users) as handle:
    userpref = json.loads(handle.read())

with open(coffee) as handle:
    actionfeaturesobj = json.loads(handle.read())

with open(requestpath) as handle:
    rankactionsjsonobj = json.loads(handle.read())

get_last_updated(modelLastModified)
get_service_settings()

print(f'User count {len(userpref)}')
print(f'Coffee count {len(actionfeaturesobj)}')
```

Controleer of de `rewardWaitTime` uitvoer `modelExportFrequency` is en beide zijn ingesteld op 15 seconden.

```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:00:15', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:00:15', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

### <a name="troubleshooting-the-first-rest-call"></a>Problemen oplossen met het eerste REST-gesprek

Deze vorige cel is de eerste cel die naar Personalizer roept. Controleer of de REST-statuscode `<Response [200]>`in de uitvoer is . Als er een fout optreedt, zoals 404, maar u weet zeker dat uw resourcesleutel en -naam correct zijn, laadt u het notitieblok opnieuw.

Zorg ervoor dat de telling van koffie en gebruikers is zowel 4. Als u een foutmelding krijgt, controleert u of u alle 3 JSON-bestanden hebt geüpload.

### <a name="set-up-metric-chart-in-azure-portal"></a>Metrische grafiek instellen in Azure-portal

Later in deze zelfstudie is het langlopende proces van 10.000 aanvragen zichtbaar vanuit de browser met een updatetekstvak. Het kan gemakkelijker zijn om te zien in een grafiek of als een totale som, wanneer het langlopende proces eindigt. Als u deze informatie wilt bekijken, gebruikt u de statistieken die bij de bron zijn geleverd. U de grafiek maken nu u een aanvraag voor de service hebt voltooid en vervolgens de grafiek periodiek vernieuwen terwijl het langlopende proces wordt uitgevoerd.

1. Selecteer in de Azure-portal uw personalizerbron.
1. Selecteer in de resourcenavigatie de optie **Statistieken** onder Controle.
1. Selecteer in de grafiek **Statistiek toevoegen**.
1. De resource en metrische naamruimte zijn al ingesteld. U hoeft alleen de statistiek van **succesvolle oproepen** en de samenvoeging van **de som**te selecteren.
1. Wijzig het tijdfilter naar de laatste 4 uur.

    ![Stel metrische grafiek in Azure-portal in, waarbij metrische gegevens worden toegevoegd voor succesvolle gesprekken van de afgelopen 4 uur.](./media/tutorial-azure-notebook/metric-chart-setting.png)

    U ziet drie geslaagde oproepen in de grafiek.

### <a name="generate-a-unique-event-id"></a>Een unieke gebeurtenis-id genereren

Deze functie genereert een unieke ID voor elke rangoproep. De ID wordt gebruikt om de rang en beloning oproep informatie te identificeren. Deze waarde kan afkomstig zijn van een bedrijfsproces, zoals een webview-id of transactie-ID.

De cel heeft geen uitvoer. De functie geeft de unieke ID bij het aangeroepen.

```python
def add_event_id(rankjsonobj):
    eventid = uuid.uuid4().hex
    rankjsonobj["eventId"] = eventid
    return eventid
```

### <a name="get-random-user-weather-and-time-of-day"></a>Krijg willekeurige gebruiker, het weer en het tijdstip van de dag

Deze functie selecteert een unieke gebruiker, het weer en het tijdstip van de dag en voegt deze items vervolgens toe aan het JSON-object dat u naar de rangaanvraag wilt verzenden.

De cel heeft geen uitvoer. Wanneer de functie wordt genoemd, retourneert de naam van de willekeurige gebruiker, het willekeurige weer en het willekeurige tijdstip van de dag.

De lijst van 4 gebruikers en hun voorkeuren - slechts enkele voorkeuren worden weergegeven voor beknoptheid:

```json
{
  "Alice": {
    "Sunny": {
      "Morning": "Cold brew",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Bob": {
    "Sunny": {
      "Morning": "Cappucino",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Cathy": {
    "Sunny": {
      "Morning": "Latte",
      "Afternoon": "Cold brew",
      "Evening": "Cappucino"
    }...
  },
  "Dave": {
    "Sunny": {
      "Morning": "Iced mocha",
      "Afternoon": "Iced mocha",
      "Evening": "Iced mocha"
    }...
  }
}
```

```python
def add_random_user_and_contextfeatures(namesoption, weatheropt, timeofdayopt, rankjsonobj):
    name = namesoption[random.randint(0,3)]
    weather = weatheropt[random.randint(0,2)]
    timeofday = timeofdayopt[random.randint(0,2)]
    rankjsonobj['contextFeatures'] = [{'timeofday': timeofday, 'weather': weather, 'name': name}]
    return [name, weather, timeofday]
```


### <a name="add-all-coffee-data"></a>Alle koffiegegevens toevoegen

Met deze functie wordt de volledige lijst met koffie toegevoegd aan het JSON-object dat u naar de rangaanvraag wilt verzenden.

De cel heeft geen uitvoer. De functie verandert `rankjsonobj` de wanneer aangeroepen.


Het voorbeeld van de kenmerken van een enkele koffie is:

```json
{
    "id": "Cappucino",
    "features": [
    {
        "type": "hot",
        "origin": "kenya",
        "organic": "yes",
        "roast": "dark"

    }
}
```

```python
def add_action_features(rankjsonobj):
    rankjsonobj["actions"] = actionfeaturesobj
```

### <a name="compare-prediction-with-known-user-preference"></a>Voorspelling vergelijken met bekende gebruikersvoorkeur

Deze functie wordt aangeroepen nadat de Rank API is aangeroepen, voor elke iteratie.

Deze functie vergelijkt de voorkeur van de gebruiker voor koffie, op basis van het weer en het tijdstip van de dag, met de suggestie van de personalisator voor de gebruiker voor deze filters. Als de suggestie overeenkomt, wordt een score van 1 geretourneerd, anders is de score 0. De cel heeft geen uitvoer. De functie geeft de uitvoer van de score wanneer aangeroepen.

```python
def get_reward_from_simulated_data(name, weather, timeofday, prediction):
    if(userpref[name][weather][timeofday] == str(prediction)):
        return 1
    return 0
```

### <a name="loop-through-calls-to-rank-and-reward"></a>Loop door oproepen naar rang en beloning

De volgende cel is het _belangrijkste_ werk van de Notebook, het krijgen van een willekeurige gebruiker, het krijgen van de koffielijst, het verzenden van zowel naar de Rank API. De voorspelling vergelijken met de bekende voorkeuren van de gebruiker en vervolgens de beloning terugsturen naar de Personalizer-service.

De lus `num_requests` loopt voor tijden. Personalizer heeft een paar duizend oproepen naar Rank en Reward nodig om een model te maken.

Een voorbeeld van de JSON die naar de Rank API wordt verzonden volgt. De lijst van koffie is niet compleet, voor beknoptheid. U de gehele JSON `coffee.json`voor koffie in zien.

JSON verzonden naar de Rank API:

```json
{
   'contextFeatures':[
      {
         'timeofday':'Evening',
         'weather':'Snowy',
         'name':'Alice'
      }
   ],
   'actions':[
      {
         'id':'Cappucino',
         'features':[
            {
               'type':'hot',
               'origin':'kenya',
               'organic':'yes',
               'roast':'dark'
            }
         ]
      }
        ...rest of coffee list
   ],
   'excludedActions':[

   ],
   'eventId':'b5c4ef3e8c434f358382b04be8963f62',
   'deferActivation':False
}
```

JSON-antwoord van de Rank API:

```json
{
    'ranking': [
        {'id': 'Latte', 'probability': 0.85 },
        {'id': 'Iced mocha', 'probability': 0.05 },
        {'id': 'Cappucino', 'probability': 0.05 },
        {'id': 'Cold brew', 'probability': 0.05 }
    ],
    'eventId': '5001bcfe3bb542a1a238e6d18d57f2d2',
    'rewardActionId': 'Latte'
}
```

Ten slotte toont elke lus de willekeurige selectie van de gebruiker, het weer, het tijdstip van de dag en de vastberaden beloning. De beloning van 1 geeft aan dat de personalizerbron het juiste koffietype heeft geselecteerd voor de opgegeven gebruiker, het weer en het tijdstip van de dag.

```console
1 Alice Rainy Morning Latte 1
```

De functie gebruikt:

* Rang: een POST REST API om rang te [krijgen.](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)
* Beloning: een POST REST API om beloning te [rapporteren.](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)

```python
def iterations(n, modelCheck, jsonFormat):

    i = 1

    # default reward value - assumes failed prediction
    reward = 0

    # Print out dateTime
    currentDateTime()

    # collect results to aggregate in graph
    total = 0
    rewards = []
    count = []

    # default list of user, weather, time of day
    namesopt = ['Alice', 'Bob', 'Cathy', 'Dave']
    weatheropt = ['Sunny', 'Rainy', 'Snowy']
    timeofdayopt = ['Morning', 'Afternoon', 'Evening']


    while(i <= n):

        # create unique id to associate with an event
        eventid = add_event_id(jsonFormat)

        # generate a random sample
        [name, weather, timeofday] = add_random_user_and_contextfeatures(namesopt, weatheropt, timeofdayopt, jsonFormat)

        # add action features to rank
        add_action_features(jsonFormat)

        # show JSON to send to Rank
        print('To: ', jsonFormat)

        # choose an action - get prediction from Personalizer
        response = requests.post(personalization_rank_url, headers = headers, params = None, json = jsonFormat)

        # show Rank prediction
        print ('From: ',response.json())

        # compare personalization service recommendation with the simulated data to generate a reward value
        prediction = json.dumps(response.json()["rewardActionId"]).replace('"','')
        reward = get_reward_from_simulated_data(name, weather, timeofday, prediction)

        # show result for iteration
        print(f'   {i} {currentDateTime()} {name} {weather} {timeofday} {prediction} {reward}')

        # send the reward to the service
        response = requests.post(personalization_reward_url + eventid + "/reward", headers = headers, params= None, json = { "value" : reward })

        # for every N rank requests, compute total correct  total
         total =  total + reward

        # every N iteration, get last updated model date and time
        if(i % modelCheck == 0):

            print("**** 10% of loop found")

            get_last_updated(modelLastModified)

        # aggregate so chart is easier to read
        if(i % 10 == 0):
            rewards.append( total)
            count.append(i)
             total = 0

        i = i + 1

    # Print out dateTime
    currentDateTime()

    return [count, rewards]
```

## <a name="run-for-10000-iterations"></a>Uitvoeren voor 10.000 iteraties
Voer de Personalizer-lus uit voor 10.000 iteraties. Dit is een langlopend evenement. Sluit de browser waarop het notitieblok wordt uitgevoerd niet. Vernieuw de grafiek met statistieken in de Azure-portal periodiek om de totale aanroepen naar de service weer te geven. Wanneer u ongeveer 20.000 oproepen, een rang en beloning oproep voor elke iteratie van de lus, de iteraties worden gedaan.

```python
# max iterations
num_requests = 200

# check last mod date N% of time - currently 10%
lastModCheck = int(num_requests * .10)

jsonTemplate = rankactionsjsonobj

# main iterations
[count, rewards] = iterations(num_requests, lastModCheck, jsonTemplate)
```



## <a name="chart-results-to-see-improvement"></a>Grafiekresultaten om verbetering te zien

Maak een grafiek `count` `rewards`van de en .

```python
def createChart(x, y):
    plt.plot(x, y)
    plt.xlabel("Batch of rank events")
    plt.ylabel("Correct recommendations per batch")
    plt.show()
```

## <a name="run-chart-for-10000-rank-requests"></a>Grafiek uitvoeren voor 10.000 rangaanvragen

Voer `createChart` de functie uit.

```python
createChart(count,rewards)
```

## <a name="reading-the-chart"></a>De grafiek lezen

Deze grafiek toont het succes van het model voor het huidige standaardleerbeleid.

![Deze grafiek toont het succes van het huidige leerbeleid voor de duur van de test.](./media/tutorial-azure-notebook/azure-notebook-chart-results.png)


Het ideale doel dat tegen het einde van de test, de lus is gemiddeld een slagingspercentage dat dicht bij 100 procent minus de exploratie. De standaardwaarde van verkenning is 20%.

`100-20=80`

Deze verkenningswaarde wordt gevonden in de Azure-portal voor de personalizerbron op de **pagina Configuratie.**

Voer een [offline evaluatie](how-to-offline-evaluation.md) uit in de portal voor uw Personalizer-lus om een beter leerbeleid te vinden, op basis van uw gegevens naar de Rank API.

## <a name="run-an-offline-evaluation"></a>Een offline evaluatie uitvoeren

1. Open de pagina **Evaluatievan** de gebruikers van personalizer.
1. Selecteer **Evaluatie maken**.
1. Voer de vereiste gegevens van de evaluatienaam en het datumbereik voor de lusevaluatie in. Het datumbereik mag alleen de dagen bevatten waarop u zich richt voor uw evaluatie.
    ![Open de pagina Evaluatievan de gebruikers van personalizer. Selecteer Evaluatie maken. Voer de evaluatienaam en het datumbereik in.](./media/tutorial-azure-notebook/create-offline-evaluation.png)

    Het doel van het uitvoeren van deze offline evaluatie is om te bepalen of er een beter leerbeleid is voor de functies en acties die in deze lus worden gebruikt. Als u dat betere leerbeleid wilt vinden, moet u ervoor zorgen dat **Optimalisatiedetectie** is ingeschakeld.

1. Selecteer **OK** om met de evaluatie te beginnen.
1. Op deze pagina **Evaluaties** vindt u de nieuwe evaluatie en de huidige status. Afhankelijk van hoeveel gegevens u hebt, kan deze evaluatie enige tijd duren. U na enkele minuten terugkomen op deze pagina om de resultaten te bekijken.
1. Wanneer de evaluatie is voltooid, selecteert u de evaluatie en selecteert u **Vergelijking van verschillende leerbeleid**. Dit toont het beschikbare leerbeleid en hoe ze zich zouden gedragen met de gegevens.
1. Selecteer het meest beste leerbeleid in de tabel en selecteer **Toepassen**. Dit past het _beste_ leerbeleid toe op uw model en hertraint.

## <a name="change-update-model-frequency-to-5-minutes"></a>Updatemodelfrequentie wijzigen in 5 minuten

1. Selecteer in de Azure-portal, nog steeds op de personalizerbron, de pagina **Configuratie.**
1. Wijzig de frequentie van de **modelupdate** en **beloon de wachttijd** in 5 minuten en selecteer **Opslaan**.

Meer informatie over de [wachttijd voor beloning](concept-rewards.md#reward-wait-time) en de frequentie van de [modelupdate](how-to-settings.md#model-update-frequency).

```python
#Verify new learning policy and times
get_service_settings()
```

Controleer of de `rewardWaitTime` uitvoer `modelExportFrequency` is en beide zijn ingesteld op 5 minuten.
```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:05:00', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:05:00', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

## <a name="validate-new-learning-policy"></a>Nieuw leerbeleid valideren

Ga terug naar het Azure-notitieblok en ga verder met dezelfde lus, maar voor slechts 2.000 iteraties. Vernieuw de grafiek met statistieken in de Azure-portal periodiek om de totale aanroepen naar de service weer te geven. Wanneer u ongeveer 4.000 oproepen, een rang en beloning oproep voor elke iteratie van de lus, de iteraties worden gedaan.

```python
# max iterations
num_requests = 2000

# check last mod date N% of time - currently 10%
lastModCheck2 = int(num_requests * .10)

jsonTemplate2 = rankactionsjsonobj

# main iterations
[count2, rewards2] = iterations(num_requests, lastModCheck2, jsonTemplate)
```

## <a name="run-chart-for-2000-rank-requests"></a>Grafiek uitvoeren voor 2.000 rangaanvragen

Voer `createChart` de functie uit.

```python
createChart(count2,rewards2)
```

## <a name="review-the-second-chart"></a>De tweede grafiek bekijken

De tweede grafiek moet een zichtbare toename van rangvoorspellingen weergeven die overeenkomen met de voorkeuren van de gebruiker.

![De tweede grafiek moet een zichtbare toename van rangvoorspellingen weergeven die overeenkomen met de voorkeuren van de gebruiker.](./media/tutorial-azure-notebook/azure-notebook-chart-results-happy-graph.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet van plan bent de zelfstudiereeks voort te zetten, ruimt u de volgende bronnen op:

* Verwijder uw Azure Notebook-project.
* Verwijder uw Personalizer-bron.

## <a name="next-steps"></a>Volgende stappen

De [Jupyter-notebook en gegevensbestanden](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) die in dit voorbeeld worden gebruikt, zijn beschikbaar op de GitHub-repo voor personalizer.

