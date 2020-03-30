---
title: Partnerintegratie voor sensoren
description: In dit artikel wordt beschreven dat sensorpartner wordt betrokken.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 48a2ed5e4774ac07b4b8fa72a5ee0be86811cfb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298730"
---
# <a name="sensor-partner-integration"></a>Partnerintegratie voor sensoren

In dit artikel vindt u informatie over de component Azure FarmBeats **Translator,** waarmee sensorpartnerintegratie mogelijk is.

Met behulp van dit onderdeel kunnen partners integreren met FarmBeats met FarmBeats Datahub API's en klantgegevens en telemetrie naar FarmBeats Datahub sturen. Zodra de gegevens beschikbaar zijn in FarmBeats, wordt deze gevisualiseerd met behulp van de FarmBeats Accelerator en kan deze worden gebruikt voor datafusion en voor het bouwen van machine learning/kunstmatige intelligentie modellen.

## <a name="before-you-start"></a>Voordat u begint

Als u de component Translator wilt ontwikkelen, hebt u de volgende referenties nodig waarmee u toegang krijgt tot de FarmBeats-API's.

- API-eindpunt
- Tenant-id
- Client-id
- Clientgeheim
- EventHub-verbindingstekenreeks

Zie deze sectie voor het verkrijgen van de bovenstaande referenties: [Apparaatintegratie inschakelen](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>Vertalerontwikkeling

**REST API-gebaseerde integratie**

Sensordata-integratiemogelijkheden van FarmBeats worden via de REST API belicht. Tot de mogelijkheden behoren de definitie van metadata, het inrichten van apparaten en sensoren en het apparaat- en sensorbeheer.

**Telemetrie-opname**

De telemetriegegevens worden toegewezen aan een canoniek bericht dat is gepubliceerd op Azure Event Hubs voor verwerking. Azure Event Hubs is een service die realtime gegevens (telemetrie) inopname van verbonden apparaten en toepassingen mogelijk maakt.

**API-ontwikkeling**

De API's bevatten technische documentatie van Swagger. Zie [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)voor meer informatie over de API's en de bijbehorende verzoeken of antwoorden.

**Verificatie**

FarmBeats maakt gebruik van Microsoft Azure Active Directory-verificatie.Azure App Service biedt ingebouwde verificatie- en autorisatieondersteuning.

Zie [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)voor meer informatie.

FarmBeats Datahub maakt gebruik van verificatie aan toonder, waarvoor de volgende referenties nodig zijn:
   - Client-id
   - Clientgeheim
   - Tenant-id

Met behulp van deze referenties kan de beller een toegangstoken aanvragen. Het token moet als volgt worden verzonden in de volgende API-aanvragen in de sectie koptekst:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

De volgende voorbeeldPython-code geeft het toegangstoken, dat kan worden gebruikt voor volgende API-aanroepen naar FarmBeats.

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


**HTTP-aanvraagkoppen**

Dit zijn de meest voorkomende aanvraagkoppen die moeten worden opgegeven wanneer u een API-aanroep naar FarmBeats Datahub maakt.


**Header** | **Beschrijving en voorbeeld**
--- | ---
Content-Type | De aanvraagindeling (Inhoudstype:<format>toepassing/ ). Voor FarmBeats Datahub API's is de indeling JSON. Inhoudstype: toepassing/json
Autorisatie | Hiermee geeft u het toegangstoken op dat nodig is om een API-aanroep te maken. Autorisatie:> van Access-Token aan touw <
Accepteren | De antwoordnotatie. Voor FarmBeats Datahub API's is de indeling JSON. Accepteren: toepassing/json

**API-aanvragen**

Als u een REST API-aanvraag wilt indienen, combineert u de methode HTTP (GET, POST of PUT), de URL naar de API-service, de Uniform Resource Identifier (URI) naar een resource om gegevens op te vragen, bij te werken, bij te werken of te verwijderen en een of meer HTTP-aanvraagkoppen. De URL naar de API-service is het API-eindpunt dat u opgeeft. Hier is een voorbeeld: https://\<yourdatahub-website-naam>.azurewebsites.net

Optioneel u queryparameters opnemen in GET-oproepen om te filteren, de grootte van de gegevens te beperken en de gegevens in de antwoorden te sorteren.

De volgende voorbeeldaanvraag is om de lijst met apparaten te krijgen.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
De meeste GET, POST, en PUT oproepen vereisen een JSON aanvraag lichaam.

De volgende voorbeeldaanvraag is het maken van een apparaat. (Dit voorbeeld heeft een input JSON met de aanvraaginstantie.)

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Gegevensindeling

JSON is een veelgebruikte taalonafhankelijke gegevensindeling die een eenvoudige tekstweergave van willekeurige gegevensstructuren biedt. Zie [json.org voor](http://json.org)meer informatie.

## <a name="metadata-specifications"></a>Specificaties metagegevens

FarmBeats Datahub heeft de volgende API's waarmee apparaatpartners apparaat- of sensormetagegevens kunnen maken en beheren.

- /**DeviceModel**: DeviceModel komt overeen met de metadata van het apparaat, zoals de fabrikant en het type apparaat, dat gateway of node is.
- /**Apparaat**: Apparaat komt overeen met een fysiek apparaat dat op de boerderij aanwezig is.
- /**SensorModel**: SensorModel komt overeen met de metadata van de sensor, zoals de fabrikant, het type sensor, dat analoog of digitaal is, en de sensormeting, zoals omgevingstemperatuur en druk.
- /**Sensor**: Sensor komt overeen met een fysieke sensor die waarden registreert. Een sensor is meestal verbonden met een apparaat met een apparaat-id.

  **DeviceModel** |  |
  --- | ---
  Tekst (knooppunt, gateway)  | Type apparaat - Knooppunt of gateway |
  Fabrikant  | Naam van de fabrikant |
  Productcode  | Apparaatproductcode of modelnaam of -nummer. Bijvoorbeeld EnviroMonitor#6800. |
  Poorten  | Poortnaam en type, die digitaal of analoog is.  |
  Name  | Naam om bron te identificeren. Bijvoorbeeld modelnaam of productnaam. |
  Beschrijving  | Geef een zinvolle beschrijving van het model. |
  Eigenschappen  | Extra eigenschappen van de fabrikant. |
  **Apparaat** |  |
  DeviceModelId  |ID van het bijbehorende apparaatmodel. |
  HardwareId (HardwareId)   |Unieke ID voor het apparaat, zoals een MAC-adres.  |
  RapportageInterval |Rapportage-interval in seconden. |
  Locatie    |Apparaatbreedte (-90 tot +90), lengtegraad (-180 tot 180) en hoogte (in meters). |
  ParentDeviceId | ID van het bovenliggende apparaat waarop dit apparaat is verbonden. Als een knooppunt bijvoorbeeld is verbonden met een gateway, heeft het knooppunt parentDeviceID als gateway. |
  Name  | Naam om de bron te identificeren. Apparaatpartners moeten een naam verzenden die overeenkomt met de apparaatnaam aan de kant van de apparaatpartner. Als de apparaatnaam aan de kant van de apparaatpartner door de gebruiker is gedefinieerd, moet dezelfde door de gebruiker gedefinieerde naam worden doorgegeven aan FarmBeats.  |
  Beschrijving  | Geef een zinvolle beschrijving.  |
  Eigenschappen  |Extra eigenschappen van de fabrikant.  |
  **SensorModel** |  |
  Type (analoog, digitaal)  |Noem analoge of digitale sensor.|
  Fabrikant  | Naam van de fabrikant. |
  Productcode  | Productcode of modelnaam of -nummer. Bijvoorbeeld RS-CO2-N01.  |
  SensorMaatregelen > naam  | Naam van de sensormeting. Alleen kleine letters worden ondersteund. Voor metingen uit verschillende dieptes moet u de diepte opgeven. Bijvoorbeeld soil_moisture_15cm. Deze naam moet consistent zijn met de telemetriegegevens. |
  Sensormaatregelen > DataType  | Telemetriegegevenstype. Momenteel wordt dubbel ondersteund. |
  Sensormaatregelen > type  | Meettype van de telemetriegegevens van de sensor. Hieronder volgen de systeemgedefinieerde typen: AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitraat, O2, PH, Fosfaat, PointInTime, Kalium, Druk, Regenmeter, Relatieve Vochtigheid, Zoutgehalte, SoilMoisture, Bodemtemperatuur, zonnestraling, toestand, timeduration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Als u meer wilt toevoegen, raadpleegt u de /ExtendedType API.
  SensorMaatregelen >-eenheid | Eenheid van sensor telemetriegegevens. Hieronder volgen de systeemgedefinieerde eenheden: NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSeconde, MeterPerHour, MetersPerSeconde, Graad, WattPerSquareMeter, KiloWatPerSquareMeter, MilliWatPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MillisiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSeconde en InchesPerHour. Als u meer wilt toevoegen, raadpleegt u de /ExtendedType API.
  Sensormaatregelen > AggregationType  | Ofwel geen, gemiddelde, maximum, minimum, of StandardDeviation.
  SensorMaatregelen > diepte  | De diepte van de sensor in centimeters. Bijvoorbeeld het meten van vocht 10 cm onder de grond.
  Sensormaatregelen > beschrijving  | Geef een zinvolle beschrijving van de meting.
  Name  | Naam om bron te identificeren. Bijvoorbeeld de modelnaam of productnaam.
  Beschrijving  | Geef een zinvolle beschrijving van het model.
  Eigenschappen  | Extra eigenschappen van de fabrikant.
  **Sensor**  |  |
  HardwareId (HardwareId)  | Unieke ID voor de sensor ingesteld door de fabrikant.
  SensorModelId  | ID van het bijbehorende sensormodel.
  Locatie  | Sensorbreedte (-90 tot +90), lengtegraad (-180 tot 180) en hoogte (in meters).
  Naam poort>  |Naam en type poort waarmee de sensor op het apparaat is verbonden. Dit moet dezelfde naam hebben als gedefinieerd in het apparaatmodel.
  DeviceId  | ID van het apparaat waarmee de sensor is verbonden.
  Name  | Naam om de bron te identificeren. Bijvoorbeeld de sensornaam of productnaam en modelnummer of productcode.
  Beschrijving  | Geef een zinvolle beschrijving.
  Eigenschappen  | Extra eigenschappen van de fabrikant.

 Zie [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)voor informatie over elk van de objecten en hun eigenschappen.

 > [!NOTE]
 > De API's retourneren unieke id's voor elke gemaakte instantie. Deze ID moet worden bewaard door de vertaler voor apparaatbeheer en synchronisatie van metagegevens.


**Synchronisatie van metagegevens**

De vertaler moet updates verzenden over de metagegevens. Updatescenario's zijn bijvoorbeeld het wijzigen van de naam van het apparaat of de sensor en het wijzigen van de locatie van het apparaat of de sensor.

De vertaler moet de mogelijkheid hebben om nieuwe apparaten of sensoren toe te voegen die zijn geïnstalleerd door de gebruiker na koppeling van FarmBeats. Als een apparaat of sensor door de gebruiker is bijgewerkt, moet hetzelfde worden bijgewerkt in FarmBeats voor het bijbehorende apparaat of sensor. Typische scenario's die een apparaat of sensor moeten bijwerken, zijn een wijziging in een apparaatlocatie of de toevoeging van sensoren in een knooppunt.


> [!NOTE]
> Verwijderen wordt niet ondersteund voor apparaat- of sensormetagegevens.
>
> Als u metagegevens wilt bijwerken, is het verplicht om /Get/{id} op het apparaat of de sensor te bellen, de gewijzigde eigenschappen bij te werken en vervolgens een /Put/{id} uit te voeren, zodat alle eigenschappen die door de gebruiker zijn ingesteld, niet verloren gaan.

### <a name="add-new-types-and-units"></a>Nieuwe typen en eenheden toevoegen

FarmBeats ondersteunt het toevoegen van nieuwe sensormetingstypen en -eenheden. Zie [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)voor meer informatie over de /ExtendedType API.

## <a name="telemetry-specifications"></a>Telemetriespecificaties

De telemetriegegevens worden toegewezen aan een canoniek bericht dat is gepubliceerd op Azure Event Hubs voor verwerking. Azure Event Hubs is een service die realtime gegevens (telemetrie) inopname van verbonden apparaten en toepassingen mogelijk maakt.

## <a name="send-telemetry-data-to-farmbeats"></a>Telemetriegegevens verzenden naar FarmBeats

Als u telemetriegegevens naar FarmBeats wilt verzenden, maakt u een client die berichten verzendt naar een gebeurtenishub in FarmBeats. Zie [Telemetrie verzenden naar een gebeurtenishub voor](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)meer informatie over telemetriegegevens.

Hier is een voorbeeld python-code die telemetrie als client naar een opgegeven gebeurtenishub verzendt.

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

De canonieke berichtindeling ziet er als volgt uit:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```
Alle belangrijke namen in de telemetrie JSON moeten kleine letters zijn. Voorbeelden zijn deviceid en sensordata.

Hier is bijvoorbeeld een telemetriebericht:


```json
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

> [!NOTE]
> De volgende secties zijn gerelateerd aan andere wijzigingen (bijv. Gebruikersinterface, foutmanagement enz.) waar de sensorpartner naar kan verwijzen bij de ontwikkeling van de component Translator.


## <a name="link-a-farmbeats-account"></a>Een FarmBeats-account koppelen

Nadat klanten apparaten of sensoren hebben gekocht en geïmplementeerd, hebben ze toegang tot de apparaatgegevens en telemetrie op de software as a service (SaaS)-portal van de apparaatpartners. Apparaatpartners kunnen klanten in staat stellen hun account te koppelen aan hun FarmBeats-exemplaar op Azure door een manier te bieden om de volgende referenties in te voeren:

   - Weergavenaam (een optioneel veld voor gebruikers om een naam voor deze integratie te definiëren)
   - API-eindpunt
   - Tenant-id
   - Client-id
   - Clientgeheim
   - EventHub-verbindingstekenreeks
   - Begindatum

   > [!NOTE]
   > De begindatum maakt de historische gegevensfeed mogelijk, dat wil zeggen de gegevens vanaf de door de gebruiker opgegeven datum.

## <a name="unlink-farmbeats"></a>FarmBeats ontkoppelen

Apparaatpartners kunnen klanten in staat stellen een bestaande FarmBeats-integratie los te koppelen. Het ontkoppelen van FarmBeats mag geen apparaat- of sensormetagegevens verwijderen die zijn gemaakt in FarmBeats Datahub. Het ontkoppelen gaat als volgt te werk:

   - Hiermee stopt de telemetriestroom.
   - Hiermee worden de integratiereferenties van de apparaatpartner verwijderd en gewist.

## <a name="edit-farmbeats-integration"></a>Integratie farmbeats bewerken

Apparaatpartners kunnen klanten in staat stellen de integratie-instellingen farmbeats te bewerken als de clientgeheim of verbindingstekenreeks verandert. In dit geval zijn alleen de volgende velden bewerkbaar:

   - Weergavenaam (indien van toepassing)
   - Clientgeheim (moet worden weergegeven in de indeling "2x8*********** * **** of de functie Weergeven/verbergen in plaats van duidelijke tekst)
   - Verbindingstekenreeks (moet worden weergegeven in de indeling "2x8*********** of Functie Weergeven/verbergen in plaats van duidelijke tekst)

## <a name="view-the-last-telemetry-sent"></a>Bekijk de laatste verzonden telemetrie

Apparaatpartners kunnen klanten in staat stellen de tijdstempel van de laatste telemetrie die is verzonden, te bekijken onder **Verzonden telemetrie.** Dit is het moment waarop de nieuwste telemetrie met succes naar FarmBeats is verzonden.

## <a name="troubleshooting-and-error-management"></a>Probleemoplossing en foutbeheer

**Probleemoplossing, optie of ondersteuning**

Als de klant apparaatgegevens of telemetrie niet kan ontvangen in het opgegeven FarmBeats-exemplaar, moet de apparaatpartner ondersteuning bieden en een mechanisme bieden voor het oplossen van problemen.

**Bewaring van telemetriegegevens**

De telemetriegegevens moeten ook worden bewaard voor een vooraf bepaalde periode, zodat het nuttig kan zijn bij het debuggen of opnieuw verzenden van de telemetrie als er een fout of gegevensverlies optreedt.

**Foutbeheer of foutmelding**

Als een fout van invloed is op de metagegevens van het apparaat of de sensor of de gegevensstroom voor gegevensintegratie of telemetrie in het apparaatpartnersysteem, moet de klant een melding ontvangen. Er moet ook een mechanisme worden ontworpen en geïmplementeerd om eventuele fouten op te lossen.

**Verbindingschecklist**

Fabrikanten of partners van apparaten kunnen de volgende checklist gebruiken om ervoor te zorgen dat de door de klant verstrekte referenties juist zijn:

   - Controleer of een toegangstoken wordt ontvangen met de referenties die zijn verstrekt.
   - Controleer of een API-aanroep slaagt met het toegangstoken dat is ontvangen.
   - Controleer of de EventHub-clientverbinding tot stand is gekomen.

## <a name="next-steps"></a>Volgende stappen

Zie [REST API](rest-api-in-azure-farmbeats.md)voor meer informatie over de REST API.
