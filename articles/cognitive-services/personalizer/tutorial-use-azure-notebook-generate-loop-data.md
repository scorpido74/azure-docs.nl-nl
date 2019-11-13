---
title: 'Zelf studie: Azure notebook-Personaler'
titleSuffix: Azure Cognitive Services
description: In deze zelf studie wordt een aangepaste lus _system in een Azure-notebook gesimuleerd, waarmee wordt voorgesteld welk type koffie een klant moet best Ellen. De gebruikers en hun voor keuren worden opgeslagen in een gegevensset van een gebruiker. Informatie over de koffie is ook beschikbaar en opgeslagen in een koffie-gegevensset.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 669ebbf595629e8093c51d76b0816edeb5f80f93
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007606"
---
# <a name="tutorial-use-personalizer-in-azure-notebook"></a>Zelf studie: persoonlijker gebruiken in azure notebook

In deze zelf studie wordt een aangepaste lus in een Azure-notitie blok uitgevoerd, waarbij de end-to-end levenscyclus van een Personaler-lus wordt aangetoond. 

De lus geeft aan welk type koffie een klant moet best Ellen. De gebruikers en hun voor keuren worden opgeslagen in een gegevensset van een gebruiker. Informatie over de koffie wordt opgeslagen in een koffie-gegevensset.

## <a name="users-and-coffee"></a>Gebruikers en koffie

De notebook selecteert een wille keurige gebruiker, tijdstip van de dag en het type weer van de gegevensset. Een samen vatting van de gebruikers gegevens is:

|Klanten-context functies|Tijdstippen van de dag|Typen weer|
|--|--|--|
|Els<br>Bob<br>Cathy<br>Dave|Ochtend<br>'S<br>'S avonds|Zon<br>Regen achtig<br>Besneeuwde| 

Om persoonlijker te kunnen leren, is de juiste koffie selectie voor elke persoon, maar kent het _systeem_ ook meer informatie over de koffie.

|Functies van de koffie actie|Typen temperatuur|Plaatsen van oorsprong|Typen koffie|Biologische|
|--|--|--|--|--|
|Cappacino|Warm|Kenia|Donker|Biologische|
|Koud Brew|Huizen|Brazilië|Licht|Biologische|
|Iced mocha|Huizen|Ethiopië|Licht|Niet biologisch|
|Latte|Warm|Brazilië|Donker|Niet biologisch|


Het **doel** van de aangepaste lus is het vinden van de beste overeenkomst tussen de gebruikers en de koffie, zo veel mogelijk tijd. 

De code voor deze zelf studie is beschikbaar in de [github-opslag plaats voor beelden van personaler](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook).

## <a name="how-the-simulation-works"></a>De werking van simulatie

Aan het begin van het actieve systeem zijn de suggesties van Personaler alleen geslaagd tussen 20% en 30% (aangegeven door de belonings Score van 1). Na sommige aanvragen verbetert het systeem.

Voer na de eerste 10.000-aanvragen een offline-evaluatie uit. Hierdoor kunnen persoonlijke instellingen de gegevens bekijken en een beter leer beleid Voorst Ellen. Pas het nieuwe leer beleid toe en voer het notitie blok opnieuw uit met 2.000-aanvragen. De lus is beter.

## <a name="rank-and-reward-calls"></a>Rang en beloning-aanroepen

Voor elk paar duizenden aanroepen naar de Personaler-service stuurt de Azure-notebook de **rang** aanvraag naar de rest API:

* Een unieke ID voor de positie/aanvraag gebeurtenis
* Context: een wille keurige keuze van de gebruiker, het weer en het tijdstip voor het simuleren van een gebruiker op een website of mobiel apparaat
* Functies: _alle_ koffie gegevens-waaruit een persoonlijke voor stel wordt gemaakt

Het systeem ontvangt de rang orde van de koffie keuzen en vergelijkt deze voor spelling met de bekende keuze van de gebruiker voor hetzelfde tijdstip van de dag en weer. Als de bekende keuze hetzelfde is als de voorspelde keuze, wordt de **vergoeding** van 1 teruggestuurd naar personaler. Anders is de vergoeding 0. 

> [!Note]
> Dit is een simulatie zodat het algoritme voor de beloning eenvoudig is. In een praktijk scenario moet het algoritme bedrijfs logica gebruiken, mogelijk met gewichten voor verschillende aspecten van de ervaring van de klant, om de belonings score te bepalen. 


## <a name="prerequisites"></a>Vereisten

* Een [Azure notebook](https://notebooks.azure.com/) -account. 
* Een [Azure personaler-resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). 
    * Als u de persoonlijke resource al hebt gebruikt, moet u ervoor zorgen dat u [de gegevens](how-to-settings.md#clear-data-for-your-learning-loop) in de Azure portal voor de resource wist. 
* Upload alle bestanden voor [dit voor beeld](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) naar een Azure notebook-project. 

Bestands beschrijvingen:

* [Personaler. ipynb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/Personalizer.ipynb) is de Jupyter-notebook voor deze zelf studie.
* [Gebruikers gegevensset](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/users.json) wordt opgeslagen in een JSON-object.
* Een [koffie gegevensset](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/coffee.json) wordt opgeslagen in een JSON-object. 
* [Voorbeeld aanvraag JSON](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/example-rankrequest.json) is de verwachte indeling voor een post-aanvraag naar de classificatie-API.

## <a name="configure-personalizer-resource"></a>Personaler-resource configureren

Configureer in de Azure Portal uw [persoonlijke resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) met de frequentie van het **Update model** ingesteld op 15 seconden en een **wacht tijd** van vijf tien seconden. Deze waarden zijn te vinden op de **[configuratie](how-to-settings.md#configure-service-settings-in-the-azure-portal)** pagina. 

|Instelling|Waarde|
|--|--|
|model frequentie bijwerken|15 seconden|
|wacht tijd op beloning|15 seconden|

Deze waarden hebben een zeer korte duur om wijzigingen in deze zelf studie weer te geven. Deze waarden mogen niet worden gebruikt in een productie scenario zonder te valideren dat ze uw doel hebben met uw Personaler-lus. 

## <a name="set-up-the-azure-notebook"></a>De Azure-notebook instellen

1. Wijzig de kernel in `Python 3.6`. 
1. Open het `Personalizer.ipynb`-bestand.

## <a name="run-notebook-cells"></a>Notebook-cellen uitvoeren

Voer elke uitvoer bare cel uit en wacht totdat deze is geretourneerd. U weet dat het wordt uitgevoerd wanneer de haakjes naast de cel een getal weer geven in plaats van een `*`. In de volgende secties wordt uitgelegd wat elke cel programmatisch en wat u kunt verwachten voor de uitvoer. 

### <a name="include-the-python-modules"></a>De python-modules toevoegen

Neem de vereiste python-modules op. De cel heeft geen uitvoer.

```python
import json
import matplotlib.pyplot as plt
import random 
import requests
import time
import uuid
```

### <a name="set-personalizer-resource-key-and-name"></a>Sleutel en naam van de persoonlijker-resource instellen

Zoek in de Azure Portal uw sleutel en eind punt op de pagina **Quick** start van uw persoonlijke resource. Wijzig de waarde van `<your-resource-name>` in de naam van uw Personaler-resource. Wijzig de waarde van `<your-resource-key>` naar uw persoonlijke sleutel. 

```python
# Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
personalization_base_url = "https://<your-resource-name>.cognitiveservices.azure.com/"
resource_key = "<your-resource-key>"
```

### <a name="print-current-date-and-time"></a>Huidige datum en tijd afdrukken
Gebruik deze functie om de begin-en eind tijd van de iteratieve functie en herhalingen te noteren.

Deze cellen hebben geen uitvoer. De functie voert de huidige datum en tijd uit als deze wordt aangeroepen.

```python
# Print out current datetime
def currentDateTime():
    currentDT = datetime.datetime.now()
    print (str(currentDT))
```

### <a name="get-the-last-model-update-time"></a>De laatste tijd voor het bijwerken van het model ophalen

Wanneer de functie, `get_last_updated`, wordt aangeroepen, wordt in de functie de datum en tijd afgedrukt waarop het model voor het laatst is bijgewerkt. 

Deze cellen hebben geen uitvoer. De functie voert de laatste model trainings datum uit als deze wordt aangeroepen.

De functie maakt gebruik van een GET REST API om [model eigenschappen](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/GetModelProperties)op te halen. 

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

### <a name="get-policy-and-service-configuration"></a>Beleid en service configuratie ophalen

Valideer de status van de service met deze twee REST-aanroepen.

Deze cellen hebben geen uitvoer. De functie voert de service waarden uit als deze wordt aangeroepen.

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

### <a name="construct-urls-and-read-json-data-files"></a>Url's maken en JSON-gegevens bestanden lezen

Deze cel 

* bouwt de Url's die worden gebruikt in REST-aanroepen 
* Hiermee stelt u de beveiligingskop tekst in met uw persoonlijke-resource sleutel 
* Hiermee wordt de wille keurige Seed ingesteld voor de positie gebeurtenis-ID
* Lees bewerkingen in de JSON-gegevens bestanden
* roept `get_last_updated` methode-Learning-beleid verwijderd in voorbeeld uitvoer
* roept `get_service_settings` methode aan

De cel heeft uitvoer van de aanroep van `get_last_updated`-en `get_service_settings`-functies.

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

Controleer of de `rewardWaitTime` van de uitvoer en `modelExportFrequency` beide zijn ingesteld op 15 seconden. 
    
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

### <a name="troubleshooting-the-first-rest-call"></a>Problemen met de eerste REST-aanroep oplossen

Deze vorige cel is de eerste cel die aan persoonlijke voor keuren wordt door gebeld. Zorg ervoor dat de REST-status code in de uitvoer `<Response [200]>`is. Als er een fout optreedt, zoals 404, maar u zeker weet dat de bron sleutel en de naam juist zijn, laadt u het notitie blok opnieuw.

Zorg ervoor dat het aantal koffie en gebruikers beide vier zijn. Als er een fout optreedt, controleert u of u alle drie de JSON-bestanden hebt geüpload. 

### <a name="set-up-metric-chart-in-azure-portal"></a>Een metrische grafiek instellen in Azure Portal

Verderop in deze zelf studie wordt het langlopende proces van 10.000-aanvragen weer gegeven in de browser met een tekstvak voor bijwerken. Het kan beter zijn om te zien in een grafiek of als een totale som wanneer het langlopende proces wordt beëindigd. Als u deze informatie wilt weer geven, gebruikt u de metrische gegevens die bij de resource zijn meegeleverd. U kunt de grafiek nu maken, zodat u een aanvraag voor de service hebt voltooid, en vervolgens de grafiek vervolgens periodiek kunt vernieuwen terwijl het langlopende proces wordt uitgevoerd.

1. Selecteer uw persoonlijke resource in het Azure Portal.
1. Selecteer in de resource navigatie de optie **metrische gegevens** onder bewaking. 
1. Selecteer **metrische gegevens toevoegen**in de grafiek.
1. De resource-en metrische naam ruimte zijn al ingesteld. U hoeft alleen de metrische gegevens voor **geslaagde aanroepen** en de aggregatie van de **som**te selecteren.
1. Wijzig het tijd filter in de laatste vier uur.

    ![Stel een metrische grafiek in Azure Portal in om de laatste vier uur metrische gegevens toe te voegen voor geslaagde aanroepen.](./media/tutorial-azure-notebook/metric-chart-setting.png)

    U ziet nu drie geslaagde aanroepen in de grafiek. 

### <a name="generate-a-unique-event-id"></a>Een unieke gebeurtenis-ID genereren

Deze functie genereert een unieke ID voor elke positie aanroep. De ID wordt gebruikt om de positie en belonings informatie te identificeren. Deze waarde kan afkomstig zijn van een bedrijfs proces, zoals een webweergave-ID of een trans actie-ID.

De cel heeft geen uitvoer. De functie voert de unieke ID uit als deze wordt aangeroepen.

```python
def add_event_id(rankjsonobj):
    eventid = uuid.uuid4().hex
    rankjsonobj["eventId"] = eventid
    return eventid
```

### <a name="get-random-user-weather-and-time-of-day"></a>Wille keurige gebruiker, weer gave en tijdstip van dag ophalen

Met deze functie worden een unieke gebruiker, het weer en het tijdstip van de dag geselecteerd. vervolgens worden deze items aan het JSON-object toegevoegd om te verzenden naar de rang aanvraag.

De cel heeft geen uitvoer. Wanneer de functie wordt aangeroepen, worden de naam van de wille keurige gebruiker, wille keurig weer en wille keurige tijd van de dag geretourneerd.

De lijst met vier gebruikers en hun voor keuren: alleen enkele voor keuren worden weer gegeven voor het boog gebruik. 

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


### <a name="add-all-coffee-data"></a>Alle koffie gegevens toevoegen

Deze functie voegt de volledige lijst met koffie toe aan het JSON-object dat naar de rang aanvraag verzendt. 

De cel heeft geen uitvoer. De functie wijzigt de `rankjsonobj` als deze wordt aangeroepen.


Het voor beeld van de functies van één koffie is: 

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

### <a name="compare-prediction-with-known-user-preference"></a>Voor spellingen vergelijken met bekende gebruikers voorkeur

Deze functie wordt aangeroepen nadat de rangorde API is aangeroepen, voor elke iteratie.

Deze functie vergelijkt de voor keuren van de gebruiker voor koffie, op basis van weer en tijdstip, met de suggestie van de persoonlijke voor keur voor de gebruiker voor deze filters. Als het voor stel overeenkomt, wordt er een Score van 1 geretourneerd, anders is de score 0. De cel heeft geen uitvoer. De functie voert de Score uit als deze wordt aangeroepen.

```python
def get_reward_from_simulated_data(name, weather, timeofday, prediction):
    if(userpref[name][weather][timeofday] == str(prediction)):
        return 1 
    return 0
``` 

### <a name="loop-through-calls-to-rank-and-reward"></a>Door loop-aanroepen naar positie en beloning

De volgende cel is het _belangrijkste_ werk van het notitie blok, het ophalen van een wille keurige gebruiker, het ophalen van de lijst met koffie en het verzenden van beide naar de classificatie-API. Vergelijking van de voor spelling met de bekende voor keuren van de gebruiker en vervolgens terugsturen naar de Personaler-service. 

De lus wordt voor `num_requests` tijden uitgevoerd. Persoonlijkere het maken van een model vereist een paar duizend aanroepen om te rangschikken. 

Hier volgt een voor beeld van de JSON die wordt verzonden naar de Rank-API. De lijst met koffie is niet volledig, voor een beknoptere keuze. U kunt de volledige JSON voor koffie bekijken in `coffee.json`.

JSON verzonden naar de positie-API:

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

JSON-antwoord van de positie-API:

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

Ten slotte toont elke lus de wille keurige selectie van de gebruiker, het weer, het tijdstip en de vastgestelde beloning. De beloning van 1 geeft aan dat de persoonlijke resource de juiste koffie-type heeft geselecteerd voor de gegeven gebruiker, het weer en het tijdstip van de dag.

```console
1 Alice Rainy Morning Latte 1
```

De functie gebruikt:

* Positie: een bericht REST API om de [positie te krijgen](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank). 
* Beloning: een bericht REST API om [beloningen te rapporteren](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward).

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

## <a name="run-for-10000-iterations"></a>Uitvoeren voor 10.000 herhalingen
Voer de Personaler-lus uit voor 10.000 iteraties. Dit is een langlopende gebeurtenis. Sluit de browser niet die het notitie blok uitvoert. Vernieuw de grafiek met metrische gegevens in de Azure Portal regel matig om de totale aanroepen naar de service te bekijken. Wanneer u circa 20.000-aanroepen hebt, worden er voor elke herhaling van de lus een rangings-en belonings oproep gedaan. 

```python
# max iterations
num_requests = 200

# check last mod date N% of time - currently 10%
lastModCheck = int(num_requests * .10)

jsonTemplate = rankactionsjsonobj

# main iterations
[count, rewards] = iterations(num_requests, lastModCheck, jsonTemplate)
```



## <a name="chart-results-to-see-improvement"></a>Grafiek resultaten om de verbeteringen te bekijken 

Een grafiek maken op basis van de `count` en `rewards`.

```python
def createChart(x, y):
    plt.plot(x, y)
    plt.xlabel("Batch of rank events")
    plt.ylabel("Correct recommendations per batch")
    plt.show()
```

## <a name="run-chart-for-10000-rank-requests"></a>Grafiek uitvoeren voor 10.000 Rank-aanvragen

Voer de `createChart`-functie uit.

```python
createChart(count,rewards)
```

## <a name="reading-the-chart"></a>De grafiek lezen

Dit diagram toont het succes van het model voor het huidige standaard Learning-beleid. 

![Dit diagram toont het succes van het huidige leer beleid voor de duur van de test.](./media/tutorial-azure-notebook/azure-notebook-chart-results.png)


Het ideale doel dat aan het einde van de test is, is het gemiddelde van een succes percentage dat dicht bij 100 procent min de exploratie ligt. De standaard waarde voor verkennen is 20%. 

`100-20=80`

Deze waarde voor verkennen vindt u op de pagina **configuratie** van de Azure portal voor de personaler-resource. 

Als u een beter leer beleid wilt vinden op basis van uw gegevens naar de Rank API, voert u een [offline-evaluatie](how-to-offline-evaluation.md) uit in de portal voor uw personaler-lus.

## <a name="run-an-offline-evaluation"></a>Een offline-evaluatie uitvoeren

1. Open de pagina **evaluaties** van de persoonlijke resource In het Azure Portal.
1. Selecteer **evaluatie maken**.
1. Voer de vereiste gegevens van de evaluatie naam en het datum bereik voor de lus-evaluatie in. Het datum bereik moet alleen de dagen bevatten waarop u bent gericht voor uw evaluatie. 
    Open de pagina evaluaties van de persoonlijke resource ![in het Azure Portal. Selecteer evaluatie maken. Voer de naam van de evaluatie en het datum bereik in.](./media/tutorial-azure-notebook/create-offline-evaluation.png)

    Het doel van deze offline-evaluatie is om te bepalen of er een beter leer beleid is voor de functies en acties die in deze lus worden gebruikt. Zorg ervoor dat **Optimalisatie detectie** is ingeschakeld om het betere leer beleid te vinden.

1. Selecteer **OK** om de evaluatie te starten. 
1. Deze **evaluaties** pagina bevat een lijst met de nieuwe evaluatie versie en de huidige status. Afhankelijk van hoeveel gegevens u hebt, kan deze evaluatie enige tijd duren. U kunt na een paar minuten terugkeren naar deze pagina om de resultaten te bekijken. 
1. Wanneer de evaluatie is voltooid, selecteert u de evaluatie en selecteert u vervolgens **vergelijking van verschillende leer beleidsregels**. Hier ziet u het beschik bare leer beleid en hoe ze zich gedragen met de gegevens. 
1. Selecteer het hoogste trainings beleid in de tabel en selecteer **Toep assen**. Hiermee wordt het _beste_ leer beleid toegepast op uw model en worden opnieuw getraind. 

## <a name="change-update-model-frequency-to-5-minutes"></a>De frequentie van het update model wijzigen in 5 minuten

1. Selecteer de pagina **configuratie** in het Azure Portal nog steeds op de persoonlijke resource. 
1. Wijzig de **verwerkings frequentie** van het model en stel de **belonings tijd** in op 5 minuten en selecteer **Opslaan**.

Meer informatie over de [wacht tijd](concept-rewards.md#reward-wait-time) en de [frequentie](how-to-settings.md#model-update-frequency)van het bijwerken van het model.

```python
#Verify new learning policy and times
get_service_settings()
```

Controleer of de `rewardWaitTime` van de uitvoer en `modelExportFrequency` beide zijn ingesteld op 5 minuten. 
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

## <a name="validate-new-learning-policy"></a>Nieuw leer beleid valideren 

Ga terug naar de Azure-notebook en ga door met dezelfde lus, maar alleen voor 2.000 iteraties. Vernieuw de grafiek met metrische gegevens in de Azure Portal regel matig om de totale aanroepen naar de service te bekijken. Wanneer u circa 4.000-aanroepen hebt, worden er voor elke herhaling van de lus een rangings-en belonings oproep gedaan. 

```python
# max iterations
num_requests = 2000

# check last mod date N% of time - currently 10%
lastModCheck2 = int(num_requests * .10)

jsonTemplate2 = rankactionsjsonobj

# main iterations
[count2, rewards2] = iterations(num_requests, lastModCheck2, jsonTemplate)
```

## <a name="run-chart-for-2000-rank-requests"></a>Grafiek uitvoeren voor 2.000 Rank-aanvragen

Voer de `createChart`-functie uit.

```python
createChart(count2,rewards2)
```

## <a name="review-the-second-chart"></a>De tweede grafiek bekijken

In de tweede grafiek ziet u een zicht bare toename van de positie van voor spellingen die met gebruikers voorkeuren worden uitgelijnd. 

![In de tweede grafiek ziet u een zicht bare toename van de positie van voor spellingen die met gebruikers voorkeuren worden uitgelijnd.](./media/tutorial-azure-notebook/azure-notebook-chart-results-happy-graph.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet van plan bent om door te gaan met de reeks zelf studies, moet u de volgende resources opschonen:

* Verwijder uw Azure notebook-project. 
* Verwijder uw persoonlijke resource. 

## <a name="next-steps"></a>Volgende stappen

De [Jupyter-notebook en gegevens bestanden](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) die in dit voor beeld worden gebruikt, zijn beschikbaar op de GitHub-opslag plaats voor personaler. 

