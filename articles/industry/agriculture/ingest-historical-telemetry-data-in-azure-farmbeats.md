---
title: Historische telemetriegegevens opnemen
description: In dit artikel wordt beschreven hoe u historische telemetriegegevens innemen.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e0a5e89f256b562ce5f702e9ff1388cb4d021bf5
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437692"
---
# <a name="ingest-historical-telemetry-data"></a>Historische telemetriegegevens opnemen

In dit artikel wordt beschreven hoe u historische sensorgegevens inneemt in Azure FarmBeats.

Het innemen van historische gegevens uit IoT-bronnen (Internet of Things), zoals apparaten en sensoren, is een veelvoorkomend scenario in FarmBeats. U maakt metadata voor apparaten en sensoren en neemt vervolgens de historische gegevens in farmbeats in een canonieke indeling in.

## <a name="before-you-begin"></a>Voordat u begint

Voordat u verder gaat met dit artikel, moet u ervoor zorgen dat u FarmBeats hebt geïnstalleerd en historische gegevens van uw IoT-apparaten hebt verzameld. U moet ook toegang tot partners inschakelen zoals vermeld in de volgende stappen.

## <a name="enable-partner-access"></a>Partnertoegang inschakelen

U moet partnerintegratie inschakelen voor uw Azure FarmBeats-exemplaar. Met deze stap wordt een client gemaakt die toegang heeft tot uw Azure FarmBeats-exemplaar als uw apparaatpartner en krijgt u de volgende waarden die in de volgende stappen vereist zijn:

- API-eindpunt: dit is de URL van\<Datahub, bijvoorbeeld https:// datahub>.azurewebsites.net
- Tenant-id
- Client-id
- Clientgeheim
- EventHub-verbindingstekenreeks

Volg deze stappen:

> [!NOTE]
> U moet een beheerder zijn om de volgende stappen uit te voeren.

1. Meld u aan bij https://portal.azure.com/.

2. **Als u op FarmBeats versie 1.2.7 of hoger bent, slaat u de stappen a, b en c over en gaat u naar stap 3.** U de FarmBeats-versie controleren door het pictogram **Instellingen** in de rechterbovenhoek van de FarmBeats-gebruikersinterface te selecteren.

      a.  Ga naar **Azure Active Directory** > **App-registraties**

      b. Selecteer de **appregistratie** die is gemaakt als onderdeel van uw FarmBeats-implementatie. Het heeft dezelfde naam als uw FarmBeats datahub.

      c. Selecteer **Een API blootmaken** > selecteer **Een clienttoepassing toevoegen** en voer **04b07795-8ddb-461a-bbee-02f9e1bf7b46** in en controleer **Scope toestaan**. Dit geeft toegang tot de Azure CLI (Cloud Shell) om de onderstaande stappen uit te voeren:

3. Open Cloud Shell. Deze optie is beschikbaar op de werkbalk in de rechterbovenhoek van de Azure-portal.

    ![Werkbalk Azure-portal](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. Zorg ervoor dat de omgeving is ingesteld op **PowerShell**. Standaard is het ingesteld op Bash.

    ![PowerShell-werkbalkinstelling](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. Ga naar je thuisgids.

    ```azurepowershell-interactive 
    cd  
    ```

6. Voer de volgende opdracht uit. Hiermee wordt een script gedownload naar uw thuismap.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

7. Voer het volgende script uit. Het script vraagt om de tenant-id, die kan worden verkregen op de pagina **Azure Active Directory** > **Overview.**

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

8. Volg de instructies op het scherm om de waarden vast te leggen voor **API-eindpunt,** **tenant-id,** **client-id,** **clientgeheim**en **EventHub-verbindingstekenreeks**.


## <a name="create-device-or-sensor-metadata"></a>Apparaat- of sensormetagegevens maken

 Nu u over de vereiste referenties beschikt, u het apparaat en de sensoren definiëren. Maak hiervoor de metagegevens door FarmBeats API's aan te roepen. Zorg ervoor dat u de API's belt als de client-app die u in het bovenstaande gedeelte hebt gemaakt.

 FarmBeats Datahub heeft de volgende API's die het maken en beheren van apparaat- of sensormetadata mogelijk maken.

 > [!NOTE]
 > Als partner heb je alleen toegang om de metadata te lezen, te maken en bij te werken; **verwijderen, is beperkt tot de partner.**

- /**DeviceModel**: DeviceModel komt overeen met de metadata van het apparaat, zoals de fabrikant en het type apparaat, dat een gateway of een knooppunt is.
- /**Apparaat**: Apparaat komt overeen met een fysiek apparaat dat op de boerderij aanwezig is.
- /**SensorModel**: SensorModel komt overeen met de metadata van de sensor, zoals de fabrikant, het type sensor, dat analoog of digitaal is, en de sensormeting, zoals omgevingstemperatuur en druk.
- /**Sensor**: Sensor komt overeen met een fysieke sensor die waarden registreert. Een sensor is meestal verbonden met een apparaat met een apparaat-id.  


|        DeviceModel   |  Suggesties   |
| ------- | -------             |
|     Tekst (knooppunt, gateway)        |          Type apparaat - Knooppunt of gateway      |
|          Fabrikant            |         Naam van de fabrikant    |
|  Productcode                    |  Apparaatproductcode of modelnaam of -nummer. Bijvoorbeeld EnviroMonitor#6800.  |
|            Poorten          |     Poortnaam en type, die digitaal of analoog is.
|     Name                 |  Naam om de bron te identificeren. Bijvoorbeeld de modelnaam of productnaam.
      Beschrijving     | Geef een zinvolle beschrijving van het model.
|    Eigenschappen          |    Extra eigenschappen van de fabrikant.   |
|    **Apparaat**             |                      |
|   DeviceModelId     |     ID van het bijbehorende apparaatmodel.  |
|  HardwareId (HardwareId)          | Unieke ID voor het apparaat, zoals het MAC-adres.
|  RapportageInterval        |   Rapportage-interval in seconden.
|  Locatie            |  Apparaatbreedte (-90 tot +90), lengtegraad (-180 tot 180) en hoogte (in meters).   
|ParentDeviceId       |    ID van het bovenliggende apparaat waarop dit apparaat is verbonden. Bijvoorbeeld een knooppunt dat is verbonden met een gateway. Een knooppunt heeft parentDeviceId als gateway.  |
|    Name            | Een naam om de bron te identificeren. Apparaatpartners moeten een naam verzenden die overeenkomt met de apparaatnaam aan de partnerkant. Als de naam van het partnerapparaat door de gebruiker is gedefinieerd, moet dezelfde door de gebruiker gedefinieerde naam worden doorgegeven aan FarmBeats.|
|     Beschrijving       |      Geef een zinvolle beschrijving. |
|     Eigenschappen    |  Extra eigenschappen van de fabrikant.
|     **SensorModel**        |          |
|       Type (analoog, digitaal)          |      Het type sensor, of het nu analoog of digitaal is.       |
|          Fabrikant            |       De fabrikant van de sensor.     |
|     Productcode| Productcode of modelnaam of -nummer. Bijvoorbeeld RS-CO2-N01. |
|       SensorMaatregelen > naam       | Naam van de sensormeting. Alleen kleine letters worden ondersteund. Voor metingen uit verschillende dieptes moet u de diepte opgeven. Bijvoorbeeld soil_moisture_15cm. Deze naam moet overeenkomen met de telemetriegegevens.  |
|          Sensormaatregelen > DataType       |Telemetriegegevenstype. Momenteel wordt dubbel ondersteund.|
|    Sensormaatregelen > type    |Meettype van de telemetriegegevens van de sensor. De systeemgedefinieerde types zijn AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitraat, O2, PH, Fosfaat, PointInTime, Kalium, Druk, Regenmeter, RelatieveVochtigheid, Zoutgehalte, BodemVocht, BodemTemperatuur, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Als u meer wilt toevoegen, raadpleegt u de /ExtendedType API.|
|        SensorMaatregelen >-eenheid              | Eenheid van sensor telemetriegegevens. De systeemgedefinieerde eenheden zijn NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, Kilometer, MilesPerHour, MilesPerSeconde, KMPerhour, KMPerseconde, MeterPeruur, MeterperSeconde, Graad, WattperSquareMeter, KiloPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, Milliliter, Seconden, UnixTimestamp, MicroMolPerMeterSquaredPerSeconde, InchesPerHour Om meer toe te voegen, verwijzen naar de /ExtendTypeed API.|
|    Sensormaatregelen > AggregationType    |  Waarden kunnen geen, gemiddeld, maximum, minimum of StandaardAfwijking zijn.  |
|          Name            | Naam om een resource te identificeren. Bijvoorbeeld de modelnaam of productnaam.  |
|    Beschrijving        | Geef een zinvolle beschrijving van het model.|
|   Eigenschappen       |  Extra eigenschappen van de fabrikant.|
|    **Sensor**      |          |
| HardwareId (HardwareId)          |   Unieke ID voor de sensor ingesteld door de fabrikant.|
|  SensorModelId     |    ID van het bijbehorende sensormodel.|
| Locatie          |  Sensorbreedte (-90 tot +90), lengtegraad (-180 tot 180) en hoogte (in meters).|
|   Naam poort>        |  Naam en type poort waarmee de sensor op het apparaat is verbonden. Dit moet dezelfde naam hebben als gedefinieerd in het apparaatmodel.|
|    DeviceID  |    ID van het apparaat waarmee de sensor is verbonden. |
| Name            |   Naam om bron te identificeren. Bijvoorbeeld sensornaam of productnaam en modelnummer of productcode.|
|    Beschrijving      | Geef een zinvolle beschrijving.|
|    Eigenschappen        |Extra eigenschappen van de fabrikant.|

Zie [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)voor meer informatie over objecten.

### <a name="api-request-to-create-metadata"></a>API-aanvraag om metagegevens te maken

Als u een API-aanvraag wilt indienen, combineert u de methode HTTP (POST), de URL naar de API-service en de URI naar een bron om gegevens op te vragen, te maken of een aanvraag te verwijderen. Vervolgens voegt u een of meer HTTP-aanvraagkoppen toe. De URL naar de API-service is het API-eindpunt,\<dat wil zeggen de URL van Datahub (https:// yourdatahub>.azurewebsites.net).  

### <a name="authentication"></a>Verificatie

FarmBeats Datahub maakt gebruik van verificatie aan toonder, waarvoor de volgende referenties nodig zijn die in de vorige sectie zijn gegenereerd:

- Client-id
- Clientgeheim
- Tenant-id

Met behulp van deze referenties kan de beller een toegangstoken aanvragen. Het token moet als volgt worden verzonden in de volgende API-aanvragen in de sectie koptekst:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

De volgende voorbeeldpythoncode geeft het toegangstoken, dat kan worden gebruikt voor volgende API-aanroepen naar FarmBeats: 

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

Dit zijn de meest voorkomende aanvraagkoppen die moeten worden opgegeven wanneer u een API-aanroep naar FarmBeats Datahub maakt:

- **Content-Type**: toepassing/json
- **Autorisatie**:> voor toegang <
- **Accepteren**: aanvraag/json

### <a name="input-payload-to-create-metadata"></a>Payload invoeren om metagegevens te maken

DeviceModel


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Apparaat

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
Sensor

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Met de volgende voorbeeldaanvraag wordt een apparaat gemaakt. Dit verzoek heeft input JSON als payload met de aanvraag instantie.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

Hieronder is een voorbeeldcode in Python. Het toegangstoken dat in dit voorbeeld wordt gebruikt, is hetzelfde als tijdens de verificatie.

```python
import requests
import json

# Got access token - Calling the Device Model API

headers = {
    "Authorization": "Bearer " + access_token,
    "Content-Type" : "application/json"
    }
payload = '{"type" : "Node", "productCode" : "TestCode", "ports": [{"name": "port1","type": "Analog"}], "name" : "DummyDevice"}'
response = requests.post(ENDPOINT + "/DeviceModel", data=payload, headers=headers)
```

> [!NOTE]
> De API's retourneren unieke id's voor elke gemaakte instantie. U moet de id's behouden om de bijbehorende telemetrieberichten te verzenden.

### <a name="send-telemetry"></a>Telemetrie verzenden

Nu u de apparaten en sensoren in FarmBeats hebt gemaakt, u de bijbehorende telemetrieberichten verzenden.

### <a name="create-a-telemetry-client"></a>Een telemetrieclient maken

U moet de telemetrie naar Azure Event Hubs verzenden voor verwerking. Azure Event Hubs is een service die realtime gegevens (telemetrie) inopname van verbonden apparaten en toepassingen mogelijk maakt. Als u telemetriegegevens naar FarmBeats wilt verzenden, maakt u een client die berichten verzendt naar een gebeurtenishub in FarmBeats. Zie [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)voor meer informatie over het verzenden van telemetrie.

### <a name="send-a-telemetry-message-as-the-client"></a>Een telemetriebericht verzenden als de client

Nadat u een verbinding hebt gemaakt als een Gebeurtenishubs-client, u berichten als JSON naar de gebeurtenishub verzenden.

Hier vindt u voorbeeld-Python-code die telemetrie als client naar een opgegeven gebeurtenishub verzendt:

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

Converteer de historische sensorgegevensindeling naar een canonieke indeling die Azure FarmBeats begrijpt. De canonieke berichtindeling ziet er als volgt uit:

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
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

Nadat u de bijbehorende apparaten en sensoren hebt toegevoegd, u de apparaat-id en de sensor-ID in het telemetriebericht verkrijgen, zoals beschreven in de vorige sectie.

Hier is een voorbeeld van een telemetriebericht:


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
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

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Kan telemetriegegevens niet bekijken na het innemen van historische/streaminggegevens van uw sensoren

**Symptoom:** Apparaten of sensoren worden geïmplementeerd en u hebt de apparaten/sensoren op FarmBeats gemaakt en telemetrie ingenomen naar de EventHub, maar u geen telemetriegegevens op FarmBeats krijgen of bekijken.

**Corrigerende maatregelen**:

1. Zorg ervoor dat u de juiste partnerregistratie hebt gedaan - u dit controleren door naar uw datahub-branie te gaan, naar /Partner API te navigeren, een get te doen en te controleren of de partner is geregistreerd. Zo niet, volg [dan de stappen hier](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) om partner toe te voegen.

2. Controleer of u de metagegevens (DeviceModel, Device, SensorModel, Sensor) hebt gemaakt met behulp van de clientreferenties van de partner.

3. Controleer of u de juiste telemetrieberichtindeling hebt gebruikt (zoals hieronder is aangegeven):

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
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

## <a name="next-steps"></a>Volgende stappen

Zie [REST API](rest-api-in-azure-farmbeats.md)voor meer informatie over rest api-gebaseerde integratiegegevens.
