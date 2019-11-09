---
title: Integratie van de sensor partner
description: Hierin wordt de integratie van de sensor partner beschreven
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 7a85ed93d9ee01255d809cce84ebe24e6c3f71d1
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847401"
---
# <a name="sensor-partner-integration"></a>Integratie van de sensor partner

Dit artikel bevat informatie over het onderdeel Azure FarmBeats **Translator** , waarmee sensor-partner integratie mogelijk wordt.

Met dit onderdeel kunnen partners Sens oren ontwikkelen die worden geïntegreerd met FarmBeats, door gebruik te maken van onze API en om gegevens en telemetrie van klant apparaten naar de FarmBeats data hub te verzenden. De gegevens worden gevisualiseerd met behulp van de FarmBeats-Accelerator. Gegevens kunnen worden gebruikt voor gegevens fusie en voor het bouwen van machine taal/kunst matige intelligentie-modellen.

## <a name="link-farmbeats-account"></a>FarmBeats-account koppelen

Zodra de klanten apparaten/Sens oren hebben gekocht en geïmplementeerd, hebben ze toegang tot de apparaatgegevens en telemetrie op de SaaS-portal van de apparaatgegevens (software als een service). De partners van het apparaat moeten klanten in staat stellen hun account te koppelen aan hun FarmBeats-exemplaar in Azure. De volgende referenties zijn vereist om in te vullen door de klant/het systeem integrator:

   - Weergave naam (een optioneel veld voor gebruiker om een naam te definiëren voor deze integratie)
   - API-eind punt
   - Tenant-id
   - Client-id
   - Clientgeheim
   - EventHub connection string
   - Begindatum

   > [!NOTE]
   > Begin datum maakt historische gegevens invoer mogelijk met gegevens van de datum die door de gebruiker is opgegeven.

## <a name="unlink-farmbeats"></a>FarmBeats ontkoppelen

Klanten hebben de mogelijkheid om een bestaande FarmBeats-integratie te ontkoppelen. Het ontkoppelen van FarmBeats mag geen apparaat/sensor meta gegevens verwijderen die zijn gemaakt in de data hub van de klant. Het ontkoppelen gaat als volgt:

   - Hiermee wordt de telemetrische stroom gestopt.
   - Hiermee verwijdert en wist u de integratie referenties op de partner van het apparaat.

## <a name="edit-farmbeats-integration"></a>FarmBeats-integratie bewerken

De klant kan de FarmBeats-integratie-instellingen bewerken als het client geheim of het connection string verandert. In dit geval kan de klant alleen de volgende velden bewerken:

   - Weergave naam (indien van toepassing)
   - Client geheim (moet worden weer gegeven in de indeling ' 2x8 * * * * * * * * * * * * ' of ' weer geven/verbergen ' in plaats van gewone tekst)
   - Verbindings reeks (moet worden weer gegeven in de indeling ' 2x8 * * * * * * * * * * * * ' of ' weer geven/verbergen ' in plaats van gewone tekst)

## <a name="view-last-telemetry-sent"></a>Laatst verzonden telemetrie weer geven

U kunt de tijds tempel van de laatste telemetriegegevens weer geven die zijn **verzonden**. Dit is het tijdstip waarop de laatste telemetrie is verzonden naar FarmBeats.

## <a name="translator-development"></a>Translator-ontwikkeling

**Op rest API gebaseerde integratie**

De mogelijkheden voor het integreren van sensor gegevens van FarmBeats worden weer gegeven via de REST API. Mogelijkheden zijn: meta gegevens definitie, inrichting van apparaat/sensor, apparaat en sensor beheer.

**Telemetrie-opname**

De telemetriegegevens worden toegewezen aan een canonieke bericht dat is gepubliceerd in azure Event hub voor verwerking. Azure EventHub is een service waarmee realtime gegevens (telemetrie) kunnen worden opgenomen vanuit verbonden apparaten en toepassingen.

**API-ontwikkeling**

De Api's bevatten technische documentatie voor Swagger. Zie [Swagger](https://aka.ms/FarmBeatsDatahubSwagger) voor meer informatie over de api's en de bijbehorende aanvragen/antwoorden.

**Authenticatie**

FarmBeats maakt gebruik van de Active Directory-verificatie van Microsoft Azure. Azure App Service biedt ingebouwde ondersteuning voor verificatie en autorisatie.

Zie [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)voor meer informatie.

De FarmBeats data hub maakt gebruik van Bearer-verificatie, die de volgende referenties nodig heeft:
   - Client-id
   - Client geheim
   - Tenant-id

Met behulp van de bovenstaande referenties kan de aanroeper een toegangs token aanvragen, dat in de volgende API-aanvragen in de koptekst sectie als volgt moet worden verzonden:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Hieronder volgt een voor beeld van een python-code die het toegangs token bevat dat kan worden gebruikt voor volgende API-aanroepen naar FarmBeats: 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```


**HTTP-aanvraag headers**

Hier volgen de meest voorkomende aanvraag headers die moeten worden opgegeven bij het maken van een API-aanroep naar FarmBeats data hub:


**Header** | **Beschrijving en voor beeld**
--- | ---
Content-Type | De aanvraag indeling (content-type: Application/<format>) voor de indeling van de FarmBeats data hub-Api's is JSON. Content-type: Application/JSON
Autorisatie | Hiermee geeft u het toegangs token dat vereist is voor het maken van een API-aanroep autorisatie: Bearer < Access-token >
zodat | De antwoord indeling. Voor FarmBeats data hub-Api's is de indeling JSON Accept: Application/JSON

**API-aanvragen**

Als u een REST-API-aanvraag (Representational State overdracht) wilt maken, combineert u de HTTP-methode (GET, POST of PUT), de URL naar de API-service, de URI (Uniform Resource Identifier) voor een resource om een query uit te voeren, gegevens te verzenden naar, bij te werken of te verwijderen, en een of meer HTTP-aanvragen. koppen. De URL van de API-service is het API-eind punt van de klant. Hier volgt een voor beeld: https://\<yourdatahub-website-name >. azurewebsites. net

U kunt desgewenst query parameters toevoegen aan GET-aanroepen om te filteren, de grootte van de gegevens in de antwoorden te beperken en te sorteren.

De onderstaande voorbeeld aanvraag is om de lijst met apparaten op te halen:

```
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```
Voor de meeste GET-, POST-en PUT-aanroepen is een JSON-aanvraag tekst vereist.

De onderstaande voorbeeld aanvraag is het maken van een apparaat (dit voor beeld heeft een invoer-JSON met de hoofd tekst van de aanvraag).

```
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Gegevensindeling

JSON (JavaScript Object Notation) is een gemeen schappelijke, taal onafhankelijke gegevens indeling die een eenvoudige tekst weergave bevat van wille keurige gegevens structuren. Zie [JSON.org](http://json.org)voor meer informatie.

## <a name="metadata-specifications"></a>Specificaties van meta gegevens

FarmBeats data hub beschikt over de volgende Api's waarmee de apparaatgegevens de meta gegevens van een apparaat/sensor kunnen maken en beheren.  

- /**DeviceModel** : het model van het apparaat komt overeen met de meta gegevens van het apparaat, zoals de fabrikant, het type van het apparaat of de gateway of het knoop punt.  
- /**apparaat** -apparaat komt overeen met een fysiek apparaat dat aanwezig is in de farm.
- /**SensorModel** -sensor model komt overeen met de meta gegevens van de sensor, zoals de fabrikant, het type van de sensor ofwel analoog of digitaal, de sensor meting, zoals omgevings temperatuur, druk enz.
- /**sensor** -sensor komt overeen met een fysieke sensor waarmee waarden worden vastgelegd. Een sensor is doorgaans verbonden met een apparaat met een apparaat-ID.

  Model apparaat| DeviceModel komt overeen met de meta gegevens van het apparaat, zoals de fabrikant, het type van het apparaat of de gateway of het knoop punt.
  --- | ---
  Type (knoop punt, gateway)  | 1 ster |
  Fabrikant  | 2 sterren |
  Code  | Product code of model naam/nummer van het apparaat. Bijvoorbeeld EnviroMonitor # 6800 |
  Poorten  | Poort naam en-type (digitaal/analoog)  |
  Naam  | Naam voor het identificeren van de resource. Bijvoorbeeld model naam/product naam |
  Beschrijving  | Geef een zinvolle beschrijving van het model |
  Eigenschappen  | Aanvullende eigenschappen van de fabrikant |
  **Apparaatconfiguratie** | **Het apparaat komt overeen met een fysiek apparaat dat aanwezig is in de farm. Elk apparaat heeft een unieke apparaat-ID** |
DeviceModelId  |ID van het gekoppelde model. |
HardwareId   |De unieke ID voor het apparaat, zoals MAC-adres enz.,  |
ReportingInterval |Rapportage-interval in seconden |
Locatie    |Apparaat Latitude (-90 tot + 90)/Longitude (-180 tot 180)/Elevation (in meters) |
ParentDeviceId | ID van het bovenliggende apparaat waarmee dit apparaat is verbonden. Bijvoorbeeld. Een knoop punt dat is verbonden met een gateway; het knoop punt heeft parentDeviceID als de gateway |
  Naam  | Naam voor het identificeren van de resource.  Apparaat-partners moeten een naam verzenden die consistent is met de apparaatnaam op de partner zijde van het apparaat. Als de naam van het apparaat door de gebruiker is gedefinieerd op de apparaat-partner zijde, moet dezelfde door de gebruiker gedefinieerde naam worden door gegeven aan FarmBeats  |
  Beschrijving  | Geef een zinvolle beschrijving  |
  Eigenschappen  |Aanvullende eigenschappen van de fabrikant  |
  **Sensor model** | SensorModel komt overeen met de meta gegevens van de sensor, zoals de fabrikant, het type van de sensor ofwel analoog of digitaal, de sensor meting, zoals omgevings temperatuur, druk enz. |
  Type (analoog, digitaal)  |Analoge of digitale sensor vermelden|
  Fabricage  | naam van fabrikant |
  Code  | Product code of model naam/nummer. Bijvoorbeeld RS-CO2-N01  |
  > Naam SensorMeasures  | De naam van de sensor meting. Alleen kleine letters worden ondersteund. Geef voor meting van verschillende diepten de diepte op. Soil_moisture_15cm deze naam bijvoorbeeld moet consistent zijn met de telemetrie-gegevens. |
  sensorMeasures > Data type  | Het gegevens type telemetrie. Er wordt momenteel Double ondersteund  |
  SensorMeasures > type  | Meet type van de telemetrie van de sensor. Hieronder ziet u de door het systeem gedefinieerde typen: AmbientTemperature, CO2, depth, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitraat, O2, PH, fosfaat, PointInTime, kalium, druk, RainGauge, RelativeHumidity, zout, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Zie/ExtendedType-API voor meer informatie over het toevoegen van meer.
  SensorMeasures >-eenheid | De gegevens eenheid van de sensor-telemetrie. Hieronder ziet u de door het systeem gedefinieerde eenheden: eenheid, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, kwik, PSI, MilliMeter, CentiMeter, meter, inch, meter, mijl, kilo meter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, liter, MilliLiter, seconden, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour om meer toe te voegen, raadpleegt u/ Extended type-API
  SensorMeasures > aggregationType  | Ofwel geen, gemiddeld, maximum, minimum, StandardDeviation
  Diepte van SensorMeasures->  | De diepte van de sensor in centimeters (bijvoorbeeld meting van de vocht 10 cm onder het wegdek)
  Beschrijving van sensorMeasures->  | Geef een zinvolle beschrijving van de meting
  naam  | Naam voor het identificeren van de resource. Bijvoorbeeld model naam/product naam
  description  | Geef een zinvolle beschrijving van het model
  properties  | Aanvullende eigenschappen van de fabrikant
  **Sensoren**  |
  HardwareId  | Unieke ID voor de sensor die door de fabrikant is ingesteld
  sensorModelId  | ID van het gekoppelde sensor model.
  location  | Sensor Latitude (-90 tot + 90)/Longitude (-180 tot 180)/Elevation (in meters)
  naam van poort >  |Naam en type van de poort waarop de sensor is aangesloten op het apparaat. Dit moet dezelfde naam zijn als die is gedefinieerd in het model van het apparaat
  deviceId  | ID van het apparaat waarmee de sensor is verbonden
  naam  | Naam voor het identificeren van de resource. Bijvoorbeeld: naam van sensor/product naam en model nummer/product code.
  description  | Geef een zinvolle beschrijving
  properties  | Aanvullende eigenschappen van de fabrikant

 Zie [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)voor meer informatie over elk van de objecten en hun eigenschappen.

 > [!NOTE]
 > De Api's retour neren unieke Id's voor elk gemaakt exemplaar. Deze ID moet worden behouden door de vertaler voor het synchroniseren van apparaten en meta gegevens.


**Meta gegevens synchroniseren**

Het conversie programma moet updates voor de meta gegevens verzenden. Zo zijn bijvoorbeeld update scenario's: wijziging van de naam van het apparaat/de sensor, wijziging van de locatie van de apparaat/sensor.

Het conversie programma moet de mogelijkheid hebben om nieuwe apparaten en/of Sens oren toe te voegen die zijn geïnstalleerd door de gebruiker na het koppelen van FarmBeats. En als een apparaat/sensor door de gebruiker is bijgewerkt, moet dit op dezelfde manier worden bijgewerkt in FarmBeats voor het bijbehorende apparaat/sensor. Typische scenario's voor het bijwerken van het apparaat/de sensor zijn: wijziging van de locatie van het apparaat, het toevoegen van Sens oren in een knoop punt, enzovoort.


> [!NOTE]
> Verwijderen wordt niet ondersteund voor de meta gegevens van apparaten/sensors.
>
> Voor het bijwerken van meta gegevens is het verplicht om/Get/{id} aan te roepen op het apparaat/de sensor, de gewijzigde eigenschappen bij te werken en vervolgens een/put/{id} uit te voeren, zodat de door de gebruiker ingestelde eigenschappen niet verloren gaan

### <a name="adding-new-typesunit"></a>Nieuwe typen/eenheden toevoegen

FarmBeats biedt ondersteuning voor het toevoegen van nieuwe sensor metings typen en-eenheden. Voor meer informatie over de/ExtendedType-API, [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="telemetry-specifications"></a>Specificaties voor telemetrie

De telemetriegegevens worden toegewezen aan een canonieke bericht dat is gepubliceerd in azure Event hub voor verwerking. Azure EventHub is een service waarmee realtime gegevens (telemetrie) kunnen worden opgenomen vanuit verbonden apparaten en toepassingen.

## <a name="send-telemetry-data-to-farmbeats"></a>Telemetriegegevens naar FarmBeats verzenden

Als u telemetriegegevens wilt verzenden naar FarmBeats, moet u een client maken waarmee berichten worden verzonden naar een event hub in FarmBeats. Zie [telemetrie verzenden naar Event hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)voor meer informatie over telemetrie-gegevens.

Hier volgt een voor beeld van een python-code die telemetrie verzendt als een-client naar een opgegeven Event hub:

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

```
{
“deviceid”: “<id of the Device created>”,
 "timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>”
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": value
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": value
        }
      ]
    }
}

```

Alle sleutel namen in de telemetrie-JSON moeten een kleine letter zijn, bijvoorbeeld DeviceID, sensordata enzovoort.

Bijvoorbeeld telemetrie-bericht:


```
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}

```

## <a name="troubleshooterror-management"></a>Problemen oplossen/fouten beheer

**Problemen met de optie/ondersteuning oplossen**

In het geval dat de klant geen apparaatgegevens en/of telemetrie kan ontvangen in het opgegeven FarmBeats-exemplaar, moet de apparaat-partner ondersteuning en een mechanisme voor het oplossen van problemen bieden.

**Bewaren van telemetrie-gegevens**

De telemetriegegevens moeten ook worden bewaard voor een vooraf gedefinieerde periode, zodat hetzelfde kan nuttig zijn bij het opsporen van fouten of het opnieuw verzenden van de telemetrie in het geval van fouten of gegevens verlies.

**Fout beheer/fout melding**

In het geval van een fout die van invloed is op de gegevens stroom van het apparaat/de sensor, gegevens integratie of de telemetriegegevens in het partner systeem van het apparaat, moet dezelfde melding aan de klant worden gegeven. Een mechanisme voor het oplossen van de fout (en) moet ook worden ontworpen en geïmplementeerd.

**Controle lijst voor verbindingen**

Fabrikanten/partners van apparaten kunnen de volgende Sanity test/controle lijst hebben om ervoor te zorgen dat de door de klant verschafte referenties nauw keurig zijn.

   - Controleren of er een toegangs token is ontvangen met de geleverde referenties
   - Controleren of een API-aanroep slaagt met het toegangs token dat is ontvangen
   - Controleren of de EventHub-client verbinding tot stand is gebracht

## <a name="next-steps"></a>Volgende stappen

Zie [rest API](references-for-farmbeats.md#rest-api)voor meer informatie over rest API.
