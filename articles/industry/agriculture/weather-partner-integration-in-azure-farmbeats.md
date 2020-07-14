---
title: Weer partner integratie
description: In dit artikel wordt beschreven hoe een weer gegevens provider kan worden geïntegreerd met FarmBeats
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: d4ceb25b9b81c831ed1b285a875742ebfaa6d24f
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232641"
---
# <a name="weather-partner-integration"></a>Weer partner integratie

Dit artikel bevat informatie over het docker-onderdeel van de Azure FarmBeats- **connector** dat weer gegevens providers kunnen ontwikkelen om te integreren met FarmBeats door gebruik te maken van de api's en om weer gegevens naar FarmBeats te verzenden. Zodra de gegevens beschikbaar zijn in FarmBeats, kan deze worden gebruikt voor gegevens fusie en voor het bouwen van machine learning/kunst matige Intelligence-modellen.

 > [!NOTE]
 > Voor het doel van deze documentatie gebruiken we een referentie-implementatie die is gebouwd met behulp van NOAA van Azure open gegevens sets en is beschikbaar op [https://github.com/azurefarmbeats/noaa_docker](https://github.com/azurefarmbeats/noaa_docker) .
 > De bijbehorende docker-installatie kopie is beschikbaar op[https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)

Een weer partner moet een docker-installatie kopie/programma (met de hieronder vermelde specificaties) leveren en de docker-installatie kopie hosten in een container register dat toegankelijk is voor klanten. De weer partner moet de volgende informatie aan haar klanten verstrekken:

- URL van docker-installatie kopie
- Docker-afbeeldings code
- Sleutels/referenties voor toegang tot de docker-installatie kopie
- Klantspecifieke API-sleutels/referenties voor toegang tot de gegevens van het systeem van de weer partner
- SKU-gegevens voor virtuele machines (partners kunnen dit bieden als hun docker specifieke VM-vereisten heeft, anders kunnen klanten kiezen uit ondersteunde VM-Sku's in Azure)

Met behulp van de bovenstaande docker-gegevens registreert de klant een weer partner in hun FarmBeats-exemplaar. Voor meer informatie over hoe klanten de docker kunnen gebruiken om weer gegevens op te nemen in FarmBeats, raadpleegt u de hand leiding om [weer gegevens](https://docs.microsoft.com/azure/industry/agriculture/get-weather-data-from-weather-partner) op te halen

## <a name="connector-docker-development"></a>Docker-ontwikkeling van connector

**Integratie op basis van REST API**

De FarmBeats-Api's bevatten technische documentatie voor Swagger. Zie [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)voor meer informatie over alle api's en hun bijbehorende aanvragen of antwoorden. 

Als u FarmBeats hebt geïnstalleerd, kunt u toegang krijgen tot uw FarmBeats Swagger op[https://yourfarmbeatswebsitename-api.azurewebsites.net](https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger)

Let op:-API wordt toegevoegd aan de naam van uw FarmBeats-website.
Het API-eind punt wordt:[https://yourfarmbeatswebsitename-api.azurewebsites.net](https://yourfarmbeatswebsitename-api.azurewebsites.net)

### <a name="datahub-lib"></a>Datahub lib

FarmBeats biedt een lib die kan worden gebruikt door de weer partner. De lib is momenteel beschikbaar als onderdeel van de referentie- [implementatie.](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib) In de toekomst is hetzelfde beschikbaar als een SDK voor meerdere talen.

### <a name="authentication"></a>Verificatie

**Verificatie met FarmBeats-Api's**

FarmBeats maakt gebruik van Bearer-verificatie en de Api's kunnen worden geopend door een toegangs token op te geven in de koptekst sectie van de aanvraag, zoals hieronder wordt beschreven:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Het toegangs token kan worden aangevraagd vanuit een Azure-functie die wordt uitgevoerd op het FarmBeats-exemplaar van de klant. De URL van de Azure-functie wordt als argument aan het docker-programma geleverd en het toegangs token kan worden verkregen door een GET-aanvraag voor de URL te maken. Het antwoord van de URL bevat het toegangs token. De Datahub lib biedt hulp functies om partners in staat te stellen het toegangs token op te halen. [Hier](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py) vindt u meer informatie.

Het toegangs token is slechts enkele uren geldig en moet opnieuw worden aangevraagd wanneer het is verlopen.

**Verificatie met Api's aan de partner zijde**

Om ervoor te zorgen dat klanten tijdens de uitvoering van de docker kunnen verifiëren met de Api's aan de partner zijde, moeten klanten de referenties tijdens de registratie van de partner als volgt opgeven:

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
De API-service serializeert deze dicteer en slaat deze op in een sleutel [kluis](https://docs.microsoft.com/azure/key-vault/basic-concepts).

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) wordt gebruikt voor het indelen van weer taken en het draaien van resources om de docker-code uit te voeren. Het biedt ook een mechanisme voor het veilig pushen van gegevens naar de virtuele machine waarop de docker-taak wordt uitgevoerd. De API-referenties, die nu veilig zijn opgeslagen in de sleutel kluis, worden gelezen als beveiligde teken reeksen uit de-/mnt/en als uitgebreide eigenschappen beschikbaar gemaakt in de werkmap van de docker-container als activity.jsop (het pad naar het bestand is "working_dir/activity.json") kan de docker-code de referenties van dit bestand tijdens runtime lezen om toegang te krijgen tot Api's van de client. De referenties zijn als volgt beschikbaar in het bestand:

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
Houd er rekening mee dat de "partnerCredentials" beschikbaar is op exact dezelfde manier als tijdens de partner registratie van de klant.

De FarmBeats lib biedt hulp functies om partners in staat te stellen de referenties van de activiteit eigenschappen te lezen. [Hier](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py) vindt u meer informatie.

De levens duur van het bestand is alleen tijdens de uitvoering van de docker-code en wordt verwijderd nadat de docker-uitvoering is beëindigd.

Zie voor meer informatie over de werking van ADF-pijp lijnen en-activiteiten [https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping) .

**HTTP-aanvraag headers**

Hier volgen de meest voorkomende aanvraag headers die moeten worden opgegeven wanneer u een API-aanroep naar FarmBeats maakt.

**Header** | **Beschrijving en voor beeld**
--- | ---
Content-Type | De aanvraag indeling (content-type: Application/ <format> ). Voor FarmBeats Datahub-Api's is de indeling JSON. Content-type: Application/JSON
Autorisatie | Hiermee geeft u het toegangs token op dat vereist is om een API-aanroep te maken. Autorisatie: Bearer <Access-token>
Accepteren | De antwoord indeling. Voor FarmBeats Datahub-Api's is de indeling JSON. Accepteren: toepassing/JSON

## <a name="data-format"></a>Gegevensindeling

JSON is een gemeen schappelijke taal onafhankelijke gegevens indeling die een eenvoudige tekst weergave bevat van wille keurige gegevens structuren. Zie [JSON.org](http://json.org)voor meer informatie.

## <a name="docker-specifications"></a>Docker-specificaties

Het docker-programma moet twee onderdelen bevatten: **Boots trap** en **taken**. Er kan meer dan een taak zijn.

### <a name="bootstrap"></a>Bootstrap

Dit onderdeel moet worden uitgevoerd wanneer de klant de docker-registratie op FarmBeats initieert. De argumenten (Arg1, arg2) die worden door gegeven aan dit programma zijn:

- FarmBeats API-eind punt: FarmBeats API-eind punt voor API-aanvragen: dit is het eind punt voor het maken van API-aanroepen naar de FarmBeats-implementatie.
- Azure function-URL: dit is uw eigen persoonlijke eind punt waarmee u uw toegangs token voor FarmBeats-Api's krijgt. Als u alleen een GET aanroept op deze URL, haalt u het toegangs token op in het antwoord.

De verantwoordelijkheid van de Boots trap is het maken van de vereiste meta gegevens zodat gebruikers uw taken kunnen uitvoeren om weer gegevens op te halen. Raadpleeg [hier](https://github.com/azurefarmbeats/noaa_docker)de referentie-implementatie. U kunt de bootstrap_manifest.jsop bestand bijwerken volgens uw behoeften en het referentie Boots trap programma maakt de vereiste meta gegevens voor u.

De volgende meta gegevens worden gemaakt als onderdeel van dit proces. 

 > [!NOTE]
 > **Houd er rekening mee** dat als u de bootstrap_manifest.jsop bestand bijwerkt zoals vermeld in de [referentie-implementatie](https://github.com/azurefarmbeats/noaa_docker), u de onderstaande meta gegevens niet hoeft te maken omdat de Boots trap hetzelfde op basis van het manifest bestand maakt.

- /**WeatherDataModel**: een WeatherDataModel is een model voor weer gave van gegevens die overeenkomen met de verschillende gegevens sets die door de bron worden verschaft. Een DailyForecastSimpleModel kan bijvoorbeeld één keer per dag de gemiddelde Tempe ratuur, vochtigheids graad en precipitatie gegevens geven, terwijl een DailyForecastAdvancedModel mogelijk veel meer informatie kan bieden op het uurloon. U kunt een wille keurig aantal WeatherDataModels maken.
- /**Taak type**: FarmBeats heeft een uitbreidbaar taak beheersysteem. Als weer gegeven gegevens provider hebt u verschillende gegevens sets/Api's (bijvoorbeeld GetDailyForecasts). u kunt ze inschakelen in FarmBeats als taak type. Zodra een taak type is gemaakt, kan een klant taken van dat type activeren om weer gegevens op te halen voor hun locatie/Farm van belang (Zie taak type en taak-Api's in [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)).

### <a name="jobs"></a>Taken

Dit onderdeel wordt elke keer aangeroepen wanneer een FarmBeats-gebruiker een taak uitvoert van uw/JobType die u hebt gemaakt als onderdeel van het Boots trap proces. De opdracht docker run voor de taak wordt gedefinieerd als onderdeel van de **/JobType** die u hebt gemaakt.
- De verantwoordelijkheid van de taak is om gegevens op te halen uit de bron en te pushen naar FarmBeats. De para meters die nodig zijn om de gegevens op te halen, moeten worden gedefinieerd als onderdeel van/JobType in het Boots trap proces.
- Als onderdeel van de taak moet het programma een **/WeatherDataLocation** maken op basis van de/WeatherDataModel die is gemaakt als onderdeel van het Boots trap proces. De **/WeatherDataLocation** komt overeen met een locatie (lat/long) die door de gebruiker wordt verschaft als een para meter voor de taak.

### <a name="details-of-the-objects"></a>Details van de objecten

  **WeatherDataModel** |  |
  --- | ---
  Naam  | Naam van het weer gegeven gegevens model |
  Beschrijving  | Geef een zinvolle beschrijving van het model op. |
  Eigenschappen  | Aanvullende eigenschappen die zijn gedefinieerd door de gegevens provider. |
  > naam weatherMeasures  | De naam van de weer maat meting. Bijvoorbeeld humidity_max |
  weatherMeasures > data type  | Dubbel of Enum. Als Enum, is measureEnumDefinition vereist |
  weatherMeasures > measureEnumDefinition  | Alleen vereist als data type Enum is. Bijvoorbeeld {"geen regen": 0, "sneeuw": 1, "Drizzle": 2, "regen": 3} |
  weatherMeasures > type  | type weer te gegeven telemetriegegevens. Bijvoorbeeld ' RelativeHumidity '. Hieronder ziet u de door het systeem gedefinieerde typen: AmbientTemperature, eenheid, CO2, depth, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitraat, O2, PH, fosfaat, PointInTime, kalium, druk, RainGauge, RelativeHumidity, zout, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Raadpleeg de/ExtendedType-API of in de [sectie typen en eenheden toevoegen](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) hieronder om meer toe te voegen
  weatherMeasures >-eenheid | Eenheid van weers-telemetrie-gegevens. Hieronder ziet u de door het systeem gedefinieerde eenheden: eenheid, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, kwik, PSI, MilliMeter, CentiMeter, meter, inch, meter, mijl, kilo meter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, liter, MilliLiter, seconden, UnixTimestamp, MicroMolPerMeterSquaredPerSecond en InchesPerHour. Als u meer wilt toevoegen, raadpleegt u de/ExtendedType-API of in de [sectie typen en eenheden toevoegen](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) hieronder.
  weatherMeasures > AggregationType  | Ofwel geen, gemiddeld, maximum, minimum, StandardDeviation, som, totaal
  Diepte van weatherMeasures->  | De diepte van de sensor in centimeters. Bijvoorbeeld de meting van het vocht 10 cm onder het wegdek.
  Beschrijving van weatherMeasures->  | Geef een duidelijke beschrijving van de meting op. |
  **Taak type** |  |
  Naam  | de naam van de taak, bijvoorbeeld Get_Daily_Forecast; de taak die door de klant wordt uitgevoerd om weer gegevens op te halen|
  pipelineDetails > para meters > naam  | naam van de para meter |
  pipelineDetails > para meters > type | een van de teken reeksen, int, float, BOOL, matrix |
  pipelineDetails >-para meters > isRequired | True waar als de vereiste para meter, False als dat niet het geval is; de standaard waarde is True |
  pipelineDetails > para meters > defaultValue | Standaard waarde van de para meter |
  pipelineDetails > para meters > beschrijving | Beschrijving van de para meter |
  Eigenschappen  | Aanvullende eigenschappen van de fabrikant.
  Eigenschappen > **programRunCommand** | opdracht docker run: deze opdracht wordt uitgevoerd wanneer de klant de weers-taak uitvoert. |
  **WeatherDataLocation** |  |
  weatherDataModelId  | ID van de bijbehorende WeatherDataModel die is gemaakt tijdens de Boots trap|
  location  | vertegenwoordigt de breedte graad, lengte graad en uitbrei ding |
  Naam | Naam van het object |
  Beschrijving | Beschrijving |
  farmId | **optioneel** ID van de farm die door de klant wordt gegeven als onderdeel van de taak parameter |
  Eigenschappen  | Aanvullende eigenschappen van de fabrikant.

 Zie [Swagger](https://aka.ms/FarmBeatsSwagger)voor meer informatie over elk van de objecten en hun eigenschappen.

 > [!NOTE]
 > De Api's retour neren unieke Id's voor elk gemaakt exemplaar. Deze ID moet worden behouden door de vertaler voor het synchroniseren van apparaten en meta gegevens.

**Meta gegevens synchroniseren**

De connector docker moet de mogelijkheid hebben om updates te verzenden voor de meta gegevens. Voor beelden van update scenario's zijn: toevoeging van nieuwe weers-para meters in de gegevensset van de weers provider, toevoeging van functionaliteit (bijvoorbeeld Toevoeging van een prognose van 30 dagen)

> [!NOTE]
> Verwijderen wordt niet ondersteund voor de meta gegevens, bijvoorbeeld. weer gegevens model.
>
> Voor het bijwerken van meta gegevens is het verplicht om/Get/{ID} aan te roepen op het weer gegevens model, de gewijzigde eigenschappen bij te werken en vervolgens een/Put/{ID} te maken, zodat alle door de gebruiker ingestelde eigenschappen niet verloren gaan.

## <a name="weather-data-telemetry-specifications"></a>Specificaties voor weer gegevens (telemetrie)

De weer gegevens worden toegewezen aan een canonieke bericht dat naar een Azure Event hub wordt gepusht voor verwerking. Azure EventHub is een service waarmee realtime gegevens (telemetrie) kunnen worden opgenomen vanuit verbonden apparaten en toepassingen. Als u weer gegevens naar FarmBeats wilt verzenden, moet u een client maken waarmee berichten worden verzonden naar een event hub in FarmBeats. Raadpleeg [telemetrie verzenden naar een event hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send) voor meer informatie over het verzenden van telemetrie

Hier volgt een voor beeld van een python-code die telemetrie verzendt als een client naar een opgegeven Event Hub.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

De canonieke bericht indeling is als volgt:

```json
{
   "weatherstations": [
   {
   "id": "id of the WeatherDataLocation",
   "weatherdata": [
   {
     "timestamp": "timestamp of the data. For historical, this is the time for which the observations are sent. For forecast this is the time for which data is forecasted. Format is ISO 8601. Default time-zone is UTC",
     "predictiontimestamp": "timestamp on which the forecast data is predicted i.e time of prediction. Required only for forecast data. Format is ISO 8601. Default timezone is UTC ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Hier volgt bijvoorbeeld een telemetrie-bericht:

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>Probleem oplossing en fouten beheer

**Fout registratie**

Omdat de partner taak wordt uitgevoerd in het bestaande taak raamwerk: de fouten worden op dezelfde manier vastgelegd als fouten voor andere bestaande taken in FarmBeats (zoals GetFarmData, SensorPlacement enzovoort). De ADF-activiteit die wordt uitgevoerd binnen de ADF-pijp lijn registreert zowel STDERR als STDOUT. Beide bestanden zijn beschikbaar in het opslag account ' datahublogs-xxx ' in de resource groep FarmBeats.

De Datahub lib biedt hulp functies om logboek registratie in te scha kelen als onderdeel van de algemene Datahub-Logboeken. [Hier](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py) vindt u meer informatie.

**Problemen met de optie of ondersteuning oplossen**

In het geval dat de klant geen weer gegevens kan ontvangen in het opgegeven FarmBeats-exemplaar, moet de weer partner ondersteuning en een mechanisme voor het oplossen van problemen bieden.

## <a name="add-extendedtype"></a>Extended type toevoegen

FarmBeats biedt ondersteuning voor het toevoegen van nieuwe sensor metings typen en-eenheden. Houd er rekening mee dat een weer partner een nieuwe eenheid/type kan toevoegen door de bootstrap_manifest.jsvoor het bestand in de referentie-implementatie [hier](https://github.com/azurefarmbeats/noaa_docker) bij te werken

Volg de onderstaande stappen om een nieuw type WeatherMeasure toe te voegen, bijvoorbeeld ' PrecipitationDepth '.

1. Een GET-aanvraag maken op/ExtendedType met de query filter-Key = WeatherMeasureType
2. Noteer de ID van het geretourneerde object.
3. Voeg het nieuwe type toe aan de lijst in het geretourneerde object en maak een PUT-aanvraag in de/ExtendedType{ID} met de volgende nieuwe lijst. De invoer lading moet hetzelfde zijn als het antwoord dat hierboven is ontvangen en de nieuwe eenheid toegevoegd aan het einde van de lijst met waarden.

Zie [Swagger](https://aka.ms/FarmBeatsSwagger)voor meer informatie over de/EXTENDEDTYPE-API.

## <a name="next-steps"></a>Volgende stappen

U hebt nu een docker voor connectors die kan worden geïntegreerd met FarmBeats. Vervolgens ziet u hoe u met uw docker weer gegevens kunt ophalen in FarmBeats. Zie [weer gegeven gegevens ophalen](get-weather-data-from-weather-partner.md).
