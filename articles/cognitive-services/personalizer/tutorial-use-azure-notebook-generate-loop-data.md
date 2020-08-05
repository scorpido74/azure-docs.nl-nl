---
title: 'Zelfstudie: Azure Notebook - Personalizer'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie wordt een Personalizer-lussysteem in een Azure Notebook gesimuleerd, waarmee wordt voorgesteld welk type koffie een klant moet bestellen. De gebruikers en hun voorkeuren worden opgeslagen in een gebruikersgegevensset. Informatie over de koffie is ook beschikbaar en opgeslagen in een koffie-gegevensset.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: tracking-python
ms.openlocfilehash: 62e9140a1bab44d913e164304af13204f375f461
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131970"
---
# <a name="tutorial-use-personalizer-in-azure-notebook"></a>Zelfstudie: Personalizer gebruiken in Azure Notebook

In deze zelfstudie wordt een Personalizer-lus in een Azure Notebook uitgevoerd, waarbij de end-to-end levenscyclus van een Personalizer-lus wordt gedemonstreerd.

De lus geeft aan welk type koffie een klant moet bestellen. De gebruikers en hun voorkeuren worden opgeslagen in een gebruikersgegevensset. Informatie over de koffie wordt opgeslagen in een koffie-gegevensset.

## <a name="users-and-coffee"></a>Gebruikers en koffie

Het notebook, dat gebruikersinteractie met een website simuleert, selecteert een willekeurige gebruiker, tijdstip van de dag en type weer door middel van de gegevensset. Een samenvatting van de gebruikersgegevens is:

|Klant - contextfuncties|Tijdstippen van de dag|Typen weer|
|--|--|--|
|Alice<br>Bob<br>Cathy<br>Dave|Ochtend<br>Middag<br>Avond|Zonnig<br>Regenachtig<br>Sneeuw|

Om Personalizer te helpen met leren, heeft het _systeem_ ook informatie over de koffieselectie voor elke persoon.

|Koffie - actiekenmerken|Typen temperatuur|Plaatsen van oorsprong|Typen branding|Biologisch|
|--|--|--|--|--|
|Cappuccino|Warm|Kenia|Donker|Biologisch|
|Cold brew|Koud|Brazilië|Licht|Biologisch|
|Iced mocha|Koud|Ethiopië|Licht|Niet biologisch|
|Latte|Warm|Brazilië|Donker|Niet biologisch|

Het **doel** van de Personalizer-lus is om zo vaak mogelijk de beste overeenkomst te vinden tussen de gebruikers en de koffie.

De code voor deze zelfstudie is beschikbaar in de [GitHub-opslagplaats met Personalizer-voorbeelden](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook).

## <a name="how-the-simulation-works"></a>Zo werkt de simulatie

Aan het begin van het actieve systeem zijn de suggesties van Personalizer slechts 20% tot 30% van de tijd juist. Dit succes wordt aangegeven door de reward die wordt teruggestuurd naar de Reward API van Personalizer, met een score van 1. Na een aantal Rank- en Reward-aanroepen wordt het systeem verbeterd.

Voer na de eerste aanvragen een offline evaluatie uit. Hierdoor kan Personalizer de gegevens beoordelen en een beter leerbeleid voorstellen. Pas het nieuwe leerbeleid toe en voer het notebook opnieuw uit met 20% van de eerdere aanvragen. De lus presteert beter met het nieuwe leerbeleid.

## <a name="rank-and-reward-calls"></a>Rank- en Reward-aanroepen

Voor elk van de duizenden aanroepen naar de Personalizer-service stuurt Azure Notebook de **Rank**-aanvraag naar de REST API:

* Een unieke id voor de Rank/Aanvraag-gebeurtenis
* Contextfuncties: een willekeurige keuze van de gebruiker, het weer en het tijdstip van de dag. Hierop wordt een gebruiker op een website of mobiel apparaat gesimuleerd
* Acties met functies - _Alle_ koffiegegevens - waaruit Personalizer een suggestie doet

Het systeem ontvangt de aanvraag en vergelijkt deze voorspelling met de bekende keuze van de gebruiker voor hetzelfde tijdstip van de dag en het weer. Als de bekende keuze hetzelfde is als de voorspelde keuze, wordt de **Reward** van 1 teruggestuurd naar Personalizer. Anders is de terug te sturen reward 0.

> [!Note]
> Dit is een simulatie, daardoor is het algoritme voor de reward eenvoudig. In een praktijkscenario moet het algoritme bedrijfslogica gebruiken, mogelijk met gewichten voor verschillende aspecten van de klantervaring, om de reward-score te bepalen.


## <a name="prerequisites"></a>Vereisten

* Een [Azure Notebook](https://notebooks.azure.com/)-account.
* Een [Azure Personalizer-resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
    * Als u de Personalizer-resource al hebt gebruikt, moet u ervoor zorgen dat de [gegevens in Azure Portal voor de resource zijn gewist](how-to-settings.md#clear-data-for-your-learning-loop).
* Upload alle bestanden voor [dit voorbeeld](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) in een Azure Notebook-project.

Bestandsbeschrijvingen:

* [Personalizer.ipynb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/Personalizer.ipynb) is de Jupyter-notebook voor deze zelfstudie.
* [Gebruikersgegevensset](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/users.json) wordt opgeslagen in een JSON-object.
* [Koffiegegevensset](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/coffee.json) wordt opgeslagen in een JSON-object.
* [Voorbeeldaanvraag JSON](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/example-rankrequest.json) is de verwachte indeling voor een POST-aanvraag naar de Rank-API.

## <a name="configure-personalizer-resource"></a>Azure Personalizer-resource configureren

Configureer in Azure Portal uw [Personalizer-resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) met de **frequentie voor het updatemodel** ingesteld op 15 seconden en een **reward-wachttijd** van 15 seconden. Deze waarden zijn te vinden op de pagina **[configuratie](how-to-settings.md#configure-service-settings-in-the-azure-portal)** .

|Instelling|Waarde|
|--|--|
|modelfrequentie bijwerken|15 seconden|
|wachttijd voor reward|15 seconden|

Deze waarden hebben een zeer korte duur, zodat wijzigingen in deze zelfstudie kunnen worden weergegeven. Deze waarden mogen niet worden gebruikt in een productiescenario zonder te valideren dat ze uw doel bereiken met uw Personalizer-lus.

## <a name="set-up-the-azure-notebook"></a>Azure Notebook instellen

1. Wijzig de kernel in `Python 3.6`.
1. Open het `Personalizer.ipynb`-bestand.

## <a name="run-notebook-cells"></a>Notebook-cellen uitvoeren

Voer elke uitvoerbare cel uit en wacht totdat deze is geretourneerd. U weet dat de bewerking is voltooid wanneer binnen de brackets naast de cel een getal wordt weergegeven in plaats van een `*`. In de volgende secties wordt uitgelegd wat elke cel programmatisch doet en wat u kunt verwachten voor de uitvoer.

### <a name="include-the-python-modules"></a>De python-modules toevoegen

Voeg de vereiste python-modules toe. De cel heeft geen uitvoer.

```python
import json
import matplotlib.pyplot as plt
import random
import requests
import time
import uuid
```

### <a name="set-personalizer-resource-key-and-name"></a>Resourcesleutel en naam van de Personalizer instellen

Zoek in Azure Portal uw sleutel en eindpunt op via de pagina **Snelstart** van uw Personalizer-resource. Wijzig de waarde van `<your-resource-name>` in de naam van uw Personalizer-resource. Wijzig de waarde van `<your-resource-key>` naar uw Personalizer-sleutel.

```python
# Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
personalization_base_url = "https://<your-resource-name>.cognitiveservices.azure.com/"
resource_key = "<your-resource-key>"
```

### <a name="print-current-date-and-time"></a>Huidige datum en tijd afdrukken
Gebruik deze functie om de begin- en eindtijd van de iteratieve functie en iteraties te noteren.

Deze cellen hebben geen uitvoer. De functie voert de huidige datum en tijd uit als deze wordt aangeroepen.

```python
# Print out current datetime
def currentDateTime():
    currentDT = datetime.datetime.now()
    print (str(currentDT))
```

### <a name="get-the-last-model-update-time"></a>De laatste updatetijd van het model ophalen

Wanneer de functie, `get_last_updated`, wordt aangeroepen, wordt in de functie de datum en tijd afgedrukt waarop het model voor het laatst is bijgewerkt.

Deze cellen hebben geen uitvoer. De functie voert de laatste modeltrainingsdatum uit als deze wordt aangeroepen.

De functie maakt gebruik van een GET REST API om [modeleigenschappen op te halen](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/GetModelProperties).

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

### <a name="get-policy-and-service-configuration"></a>Beleids- en serviceconfiguratie ophalen

Valideer de status van de service met deze twee REST-aanroepen.

Deze cellen hebben geen uitvoer. De functie voert de servicewaarden uit als hij wordt aangeroepen.

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

### <a name="construct-urls-and-read-json-data-files"></a>URL's maken en JSON-gegevensbestanden lezen

Met deze cel

* bouwt u de URL's die worden gebruikt in REST-aanroepen
* stelt u de beveiligingskoptekst in met uw Personalizer-resourcesleutel
* stelt u de willekeurige seed in voor de gebeurtenis-id van de Rank
* leest u bewerkingen in de JSON-gegevensbestanden
* roept u de `get_last_updated` methode aan - leerbeleid is verwijderd in voorbeelduitvoer
* roept u `get_service_settings` methode aan

De cel heeft uitvoer van de aanroep van `get_last_updated`- en `get_service_settings`-functies.

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

Controleer of de `rewardWaitTime` en `modelExportFrequency` van de uitvoer beide zijn ingesteld op 15 seconden.

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

Deze vorige cel is de eerste cel die Personalizer aanroept. Zorg ervoor dat de REST-statuscode in de uitvoer `<Response [200]>` is. Als er een fout optreedt, zoals 404, maar u zeker weet dat de resourcesleutel en de naam juist zijn, laadt u het notebook opnieuw.

Zorg ervoor dat het aantal koffiebereiding en het aantal gebruikers beide 4 is. Als er een fout optreedt, controleert u of u alle 3 de JSON-bestanden hebt geüpload.

### <a name="set-up-metric-chart-in-azure-portal"></a>Een metrische grafiek instellen in Azure Portal

Verderop in deze zelfstudie wordt het langdurige proces van 10.000 aanvragen weergegeven in de browser met een tekstvak dat steeds wordt bijgewerkt. Het kan eenvoudiger zijn om het in een grafiek of als een totaalsom te bekijken wanneer het langdurige proces wordt beëindigd. Als u deze informatie wilt weergeven, gebruikt u de metrische gegevens die bij de resource zijn meegeleverd. Nu u een aanvraag voor de service hebt voltooid kunt u de grafiek maken. Vervolgens moet u de grafiek periodiek vernieuwen terwijl het langdurige proces wordt uitgevoerd.

1. Selecteer uw Personalizer-resource in Azure Portal.
1. Selecteer in de resourcenavigatie **Metrische gegevens** onder Monitoring.
1. Selecteer in de grafiek **Metrische gegevens toevoegen**.
1. De naamruimte voor de resource en metrische gegevens is al ingesteld. U hoeft alleen de metrische gegevens van **geslaagde aanroepen** en de aggregatie van **sum** te selecteren.
1. Wijzig het tijdfilter in de afgelopen 4 uur.

    ![Stel een metrische grafiek in Azure Portal in waarbij u de metrische gegevens van de geslaagde aanroepen van de afgelopen 4 uur toevoegt.](./media/tutorial-azure-notebook/metric-chart-setting.png)

    U ziet nu drie geslaagde aanroepen in de grafiek.

### <a name="generate-a-unique-event-id"></a>Een unieke gebeurtenis-id genereren

Met deze functie genereert u een unieke id voor elke Rank-aanroep. De id wordt gebruikt om de Rank- en Reward-informatie te identificeren. Deze waarde kan afkomstig zijn van een bedrijfsproces, zoals een webweergave-id of een transactie-id.

De cel heeft geen uitvoer. Met de functie wordt de unieke id uitgevoerd als deze wordt aangeroepen.

```python
def add_event_id(rankjsonobj):
    eventid = uuid.uuid4().hex
    rankjsonobj["eventId"] = eventid
    return eventid
```

### <a name="get-random-user-weather-and-time-of-day"></a>Willekeurige gebruiker, weergave en tijdstip van de dag ophalen

Met deze functie worden een unieke gebruiker, het weer en het tijdstip van de dag geselecteerd. Vervolgens worden deze items aan het JSON-object toegevoegd om te verzenden naar de Rank-aanvraag.

De cel heeft geen uitvoer. Wanneer de functie wordt aangeroepen, wordt de naam van de willekeurige gebruiker, het willekeurige weer en de willekeurige tijd van de dag geretourneerd.

De lijst met 4 gebruikers en hun voorkeuren: slechts een paar voorkeuren worden weergegeven voor de overzichtelijkheid:

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

Met deze functie voegt u de volledige lijst met koffie toe aan het JSON-object dat naar de Rank-aanvraag wordt verzonden.

De cel heeft geen uitvoer. Met de functie wijzigt u de `rankjsonobj` als deze wordt aangeroepen.


Het voorbeeld van de functies van één koffie is:

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

### <a name="compare-prediction-with-known-user-preference"></a>Voorspellingen vergelijken met bekende gebruikersvoorkeuren

Deze functie wordt aangeroepen nadat de Rank-API is aangeroepen, voor elke iteratie.

Met deze functie vergelijkt u de voorkeuren van de gebruiker voor koffie, op basis van weer en tijdstip, met de suggestie van de Personalizer voor de gebruiker voor deze filters. Als het voorstel overeenkomt, wordt er een score van 1 geretourneerd, anders is de score 0. De cel heeft geen uitvoer. Met de functie voert u de score uit als deze wordt aangeroepen.

```python
def get_reward_from_simulated_data(name, weather, timeofday, prediction):
    if(userpref[name][weather][timeofday] == str(prediction)):
        return 1
    return 0
```

### <a name="loop-through-calls-to-rank-and-reward"></a>Doorloop aanroepen naar Rank en Reward

De volgende cel is het _belangrijkste_ werk van het Notebook, het ophalen van een willekeurige gebruiker, de lijst met koffie en het verzenden van beide naar de Rank-API. Vergelijking van de voorspelling met de bekende gebruikersvoorkeuren en deze vervolgens terugsturen naar de Personalizer-service.

De lus wordt `num_requests` keer uitgevoerd. Voor Personalizer zijn een paar duizend aanroepen naar Rank en Reward vereist om een model te kunnen creëren.

Hier volgt een voorbeeld van de JSON die wordt verzonden naar de Rank-API. De lijst met koffie is niet volledig met het oog op beknoptheid. U kunt de volledige JSON voor koffie bekijken in `coffee.json`.

JSON verzonden naar de Rank-API:

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

JSON-antwoord van de Rank-API:

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

Ten slotte toont elke lus de willekeurige selectie van de gebruiker, het weer, het tijdstip en de vastgestelde reward. De reward 1 geeft aan dat de Personalizer-resource de juiste koffiesoort heeft geselecteerd voor de geselecteerde gebruiker, het weer en het tijdstip van de dag.

```console
1 Alice Rainy Morning Latte 1
```

De functie gebruikt:

* Rank: een POST REST API om [Rank op te halen](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank).
* Reward: een POST REST API om [Reward te rapporteren](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward).

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

## <a name="run-for-10000-iterations"></a>10.000 iteraties uitvoeren
Voer voor de Personalizer-lus 10.000 iteraties uit. Dit is een langdurige gebeurtenis. Sluit de browser waarin het Notebook wordt uitgevoerd niet. Vernieuw de grafiek met metrische gegevens in Azure Portal regelmatig om het totale aantal aanroepen naar de service te bekijken. Wanneer u circa 20.000 aanroepen hebt, wordt er voor elke iteratie van de lus een Rank- en Reward-aanroep gedaan.

```python
# max iterations
num_requests = 200

# check last mod date N% of time - currently 10%
lastModCheck = int(num_requests * .10)

jsonTemplate = rankactionsjsonobj

# main iterations
[count, rewards] = iterations(num_requests, lastModCheck, jsonTemplate)
```



## <a name="chart-results-to-see-improvement"></a>Bekijk de resultaten in een grafiek om de verbeteringen te zien

Een grafiek maken op basis van de `count` en `rewards`.

```python
def createChart(x, y):
    plt.plot(x, y)
    plt.xlabel("Batch of rank events")
    plt.ylabel("Correct recommendations per batch")
    plt.show()
```

## <a name="run-chart-for-10000-rank-requests"></a>Grafiek uitvoeren voor 10.000 Rank-aanvragen

Voer de functie `createChart` uit.

```python
createChart(count,rewards)
```

## <a name="reading-the-chart"></a>De grafiek lezen

Dit diagram toont het succes van het model voor het huidige standaard leerbeleid.

![Dit diagram toont het succes van het huidige leerbeleid voor de duur van de test.](./media/tutorial-azure-notebook/azure-notebook-chart-results.png)


Het ideale doel dat aan het einde van de test wordt bereikt, is een gemiddeld succespercentage dat dicht bij 100 procent ligt, met aftrek van de exploratie. De standaard waarde voor exploratie is 20%.

`100-20=80`

Deze waarde voor exploratie vindt u in Azure Portal voor de Personalizer-resource op de **Configuratiepagina**.

Als u een beter leerbeleid wilt vinden op basis van uw gegevens naar de Rank-API, voert u een [offline evaluatie](how-to-offline-evaluation.md) uit in de portal voor uw Personalizer-lus.

## <a name="run-an-offline-evaluation"></a>Een offline-evaluatie uitvoeren

1. Open in Azure Portal de pagina **Evaluaties** van de Personalizer-resource.
1. Selecteer **Evaluatie maken**.
1. Voer de vereiste gegevens in, namelijk de naam van de evaluatie en het datumbereik voor de lus-evaluatie. Het datumbereik moet alleen de dagen bevatten die u wilt opnemen in de evaluatie.
    ![Open de pagina Evaluaties van de Personalizer-resource in Azure Portal. Selecteer Evaluatie maken. Voer de naam van de evaluatie en het datumbereik in.](./media/tutorial-azure-notebook/create-offline-evaluation.png)

    Het doel van deze offline-evaluatie is om te bepalen of er een beter leerbeleid is voor de functies en acties die in deze lus worden gebruikt. Zorg ervoor dat **Optimalisatie detecteren** is ingeschakeld om het betere leerbeleid te vinden.

1. Selecteer **OK** om de evaluatie te starten.
1. Deze **Evaluatie**pagina bevat de nieuwe evaluatie en de huidige status. Afhankelijk van hoeveel gegevens u hebt, kan deze evaluatie enige tijd in beslag nemen. U kunt na een paar minuten terugkeren naar deze pagina om de resultaten te bekijken.
1. Wanneer de evaluatie is voltooid, selecteert u de evaluatie en selecteert u **Vergelijken met ander leerbeleid**. Hier ziet u de beschikbare leerbeleidsregels en hoe die zich zouden gedragen met de gegevens.
1. Selecteer het bovenste trainingsbeleid in de tabel en selecteer **Toepassen**. Hiermee wordt het _beste_ leerbeleid toegepast op uw model en wordt het opnieuw getraind.

## <a name="change-update-model-frequency-to-5-minutes"></a>De frequentie van het updatemodel wijzigen in 5 minuten

1. Selecteer de pagina **Configuratie** in Azure Portal, terwijl u nog steeds op de Personalizer-resource bent.
1. Wijzig de **updatefrequentie van het model** en de **reward-wachttijd** in 5 minuten en selecteer **Opslaan**.

Meer informatie over de [reward-wachttijd](concept-rewards.md#reward-wait-time) en [updatefrequentie van het model](how-to-settings.md#model-update-frequency).

```python
#Verify new learning policy and times
get_service_settings()
```

Controleer of de `rewardWaitTime` en `modelExportFrequency` van de uitvoer beide zijn ingesteld op 5 minuten.
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

Ga terug naar het Azure-notebook en ga door met het uitvoeren van dezelfde lus, maar slechts voor 2.000 iteraties. Vernieuw de grafiek met metrische gegevens in Azure Portal regelmatig om het totale aantal aanroepen naar de service te bekijken. Wanneer u circa 4.000 aanroepen hebt, wordt er voor elke iteratie van de lus een Rank- en Reward-aanroep gedaan.

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

Voer de functie `createChart` uit.

```python
createChart(count2,rewards2)
```

## <a name="review-the-second-chart"></a>De tweede grafiek bekijken

In de tweede grafiek ziet u een zichtbare toename van de Rank-voorspellingen die met gebruikersvoorkeuren worden uitgelijnd.

![In de tweede grafiek ziet u een zichtbare toename van de Rank-voorspellingen die met gebruikersvoorkeuren worden uitgelijnd.](./media/tutorial-azure-notebook/azure-notebook-chart-results-happy-graph.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet van plan bent om door te gaan met de reeks zelfstudies, moet u de volgende resources opschonen:

* Verwijder uw Azure Notebook-project.
* Verwijder uw Personalizer-resource.

## <a name="next-steps"></a>Volgende stappen

De [Jupyter notebook- en gegevensbestanden](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) die in dit voorbeeld worden gebruikt, zijn beschikbaar in de GitHub-opslagplaats voor Personalizer.

