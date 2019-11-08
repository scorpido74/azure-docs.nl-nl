---
title: Historische telemetriegegevens opnemen
description: Hierin wordt beschreven hoe u historische telemetriegegevens-gegevens ophaalt
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 0ff9e055ecc0c4f58e4b3df0494debbe3f4cd8a4
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797853"
---
# <a name="ingest-historical-telemetry-data"></a>Historische telemetriegegevens opnemen

In dit artikel wordt beschreven hoe u historische sensor gegevens opneemt in azure FarmBeats.

Het opnemen van historische gegevens van Internet of Things (IoT) voor resources zoals apparaten en Sens oren is een veelvoorkomend scenario in FarmBeats. U maakt meta gegevens voor apparaten en Sens oren en vervolgens neemt u de historische data op in een canonieke notatie van FarmBeats.

## <a name="before-you-begin"></a>Voordat u begint

Voordat u verder gaat met dit artikel, moet u ervoor zorgen dat u FarmBeats hebt geïnstalleerd en historische gegevens van IoT hebt verzameld.

## <a name="enable-partner-access"></a>Partner toegang inschakelen

U moet de partner integratie inschakelen voor uw Azure FarmBeats-exemplaar. Met deze stap maakt u een client die toegang heeft tot uw Azure-FarmBeats als uw apparaat-partner en biedt u de volgende waarden die vereist zijn in de volgende stappen.

- API-eind punt: dit is de data hub-URL, bijvoorbeeld https://<datahub>. azurewebsites.net
- Tenant-id
- Client-id
- Client geheim
- EventHub-verbindings reeks

Volg de onderstaande stappen om deze te genereren:

>[!NOTE]
> U moet een beheerder zijn om de volgende stappen uit te voeren.

1. Down load dit [script](https://aka.ms/farmbeatspartnerscript) en pak het uit op uw lokale station. U vindt er twee bestanden in het ZIP-bestand.
2. Meld u aan bij [Azure Portal](https://portal.azure.com/) en open Cloud shell (deze optie is beschikbaar in de rechter balk van de portal)  

    ![Maten van project-Farm](./media/for-tutorials/navigation-bar-1.png)

3. Zorg ervoor dat de omgeving is ingesteld op **Power shell**.

    ![Maten van project-Farm](./media/for-tutorials/power-shell-new-1.png)

4. Upload de twee bestanden die u hebt gedownload (uit stap 1 hierboven) in uw Cloud Shell.  

    ![Maten van project-Farm](./media/for-tutorials/power-shell-two-1.png)

5. Ga naar de map waarin de bestanden zijn geüpload (standaard wordt deze geüpload naar de basismap/home/username/.
6. Voer het script uit met behulp van de opdracht:  

    ```azurepowershell-interactive
    PS> ./generateCredentials.ps1
    ```

7. Volg de instructies op het scherm om de procedure te volt ooien.

    Neem contact op met de FarmBeats-beheerder voor het geval u geen toegang hebt tot FarmBeats of uw Azure-abonnement.

## <a name="create-devicesensor-metadata"></a>Meta gegevens van apparaat/sensor maken

 Nu u de vereiste referenties hebt, kunt u het apparaat en de Sens oren definiëren door de meta gegevens te maken met behulp van FarmBeats-Api's.

 FarmBeats data hub beschikt over de volgende Api's waarmee het maken en beheren van meta gegevens van apparaten/sensors mogelijk wordt.   

- /**DeviceModel** : het model van het apparaat komt overeen met de meta gegevens van het apparaat, zoals de fabrikant, het type van het apparaat of de gateway of het knoop punt.  
- /**apparaat** -apparaat komt overeen met een fysiek apparaat dat aanwezig is in de farm.  
- /**SensorModel** -sensor model correspondeert met de meta gegevens van de sensor, zoals de fabrikant, het type van de sensor analoog of digitaal, sensor meting, zoals omgevings temperatuur of druk.
- /**sensor** -sensor komt overeen met een fysieke sensor waarmee waarden worden vastgelegd. Een sensor is doorgaans verbonden met een apparaat met een apparaat-ID.  


|        Modus apparaat   |  Suggesties   |
| ------- | -------             |
|     Type (knoop punt, gateway)        |          1 ster      |
|          Fabrikant            |         2 sterren     |
|  Code                    |  Product code of model naam/nummer van het apparaat. Bijvoorbeeld EnviroMonitor # 6800.  |
|            Poorten          |     Poort naam en-type (digitaal/analoog)
|     Naam                 |  Naam voor het identificeren van de resource. Bijvoorbeeld model naam/product naam.
      Beschrijving     | Geef een zinvolle beschrijving van het model
|    Eigenschappen          |    Aanvullende eigenschappen van de fabrikant   |
|    **Apparaatconfiguratie**             |                      |
|   DeviceModelId     |     ID van het gekoppelde model van het apparaat  |
|  HardwareId          | De unieke ID voor het apparaat, zoals MAC-adres enz.,
|  ReportingInterval        |   Rapportage-interval in seconden
|  Locatie            |  Apparaat Latitude (-90 tot + 90)/Longitude (-180 tot 180)/Elevation (in meters)   
|ParentDeviceId       |    De ID van het bovenliggende apparaat waarmee dit apparaat is verbonden. Bijvoorbeeld een knoop punt dat is verbonden met een gateway. Een knoop punt heeft parentDeviceId als de gateway.  |
|    Naam            | Een naam om de resource te identificeren. Apparaat-partners moeten een naam verzenden die consistent is met de apparaatnaam aan de partner zijde. Als de naam van de partner apparaat door de gebruiker is gedefinieerd, moet dezelfde door de gebruiker gedefinieerde naam worden door gegeven aan FarmBeats.|
|     Beschrijving       |      Geef een zinvolle beschrijving  |
|     Eigenschappen    |  Aanvullende eigenschappen van de fabrikant
|     **Sensor model**        |          |
|       Type (analoog, digitaal)          |      type sensor, ongeacht of het analoog of digitaal is       |
|          Fabrikant            |       de fabrikant van de sensor     |
|     Code| Product code of model naam/nummer. Bijvoorbeeld RS-CO2-N01. |
|       > Naam SensorMeasures       | De naam van de sensor meting. Alleen kleine letters worden ondersteund. Geef voor meting van verschillende diepten de diepte op. Bijvoorbeeld soil_moisture_15cm. Deze naam moet consistent zijn met de telemetriegegevens  |
|          sensorMeasures > Data type       |Het gegevens type telemetrie. Er wordt momenteel Double ondersteund|
|    SensorMeasures > type    |Meet type van de telemetrie van de sensor. Hieronder ziet u de door het systeem gedefinieerde typen: AmbientTemperature, CO2, depth, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitraat, O2, PH, fosfaat, PointInTime, kalium, druk, RainGauge, RelativeHumidity, zout, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Raadpleeg/ExtendedType API om meer toe te voegen.|
|        SensorMeasures >-eenheid              | De gegevens eenheid van de sensor-telemetrie. Hieronder ziet u de door het systeem gedefinieerde eenheden: eenheid, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, kwik, PSI, MilliMeter, CentiMeter, meter, inch, meter, mijl, kilo meter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, liter, MilliLiter, seconden, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour om meer toe te voegen, raadpleegt u/ Extended type-API.|
|    SensorMeasures > aggregationType    |  De waarden kunnen none, Average, maximum, minimum of StandardDeviation zijn.  |
|          Naam            | Naam voor het identificeren van de resource. Bijvoorbeeld model naam/product naam.  |
|    Beschrijving        | Geef een zinvolle beschrijving van het model  |
|   Eigenschappen       |  Aanvullende eigenschappen van de fabrikant  |
|    **Sensoren**      |          |
| HardwareId          |   Unieke ID voor de sensor die door de fabrikant is ingesteld |
|  sensorModelId     |    ID van het gekoppelde sensor model   |
| location          |  Sensor Latitude (-90 tot + 90)/Longitude (-180 tot 180)/Elevation (in meters)|
|   naam van poort >        |  Naam en type van de poort waarop de sensor is aangesloten op het apparaat. Dit moet dezelfde naam zijn als die in het model van het apparaat is gedefinieerd. |
|    DeviceID  |    ID van het apparaat waarmee de sensor is verbonden     |
| Naam            |   Naam voor het identificeren van de resource. Bijvoorbeeld: naam van sensor/product naam en model nummer/product code.|
|    Beschrijving      | Geef een zinvolle beschrijving |
|    Eigenschappen        |Aanvullende eigenschappen van de fabrikant |

Zie [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)voor meer informatie over objecten.

**API-aanvraag voor het maken van meta gegevens**

Als u een API-aanvraag wilt maken, combineert u de HTTP-methode (POST), de URL naar de API-service, de URI naar een resource om een query uit te voeren, gegevens in te dienen om een aanvraag te maken of te verwijderen en een of meer HTTP-aanvraag headers toe te voegen. De URL naar de API-service is het API-eind punt, d.w.z. de data hub-URL (https://<yourdatahub>. azurewebsites.net)  

**Verificatie**:

De FarmBeats data hub maakt gebruik van Bearer-verificatie, die de volgende referenties nodig heeft die we in de bovenstaande sectie hebben gegenereerd.

- Client-id
- Client geheim
- Tenant-id  

Met behulp van de bovenstaande referenties kan de aanroeper een toegangs token aanvragen, dat in de volgende API-aanvragen in de koptekst sectie als volgt moet worden verzonden:

headers = *{"autorisatie": "Beer" + access_token,...}*

**HTTP-aanvraag headers**:

Hier volgen de meest voorkomende aanvraag headers die moeten worden opgegeven bij het maken van een API-aanroep naar FarmBeats data hub:

- Content-type: Application/JSON
- Autorisatie: Bearer < Access-token >
- Accepteren: toepassing/JSON

**Invoer lading voor het maken van meta gegevens**:

**DeviceModel**


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

Device
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
Sensoren

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
De onderstaande voorbeeld aanvraag is het maken van een apparaat (deze heeft een invoer JSON als Payload met de hoofd tekst van de aanvraag).  

```
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "
{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\",}"*
```

> [!NOTE]
> De Api's retour neren unieke Id's voor elk gemaakt exemplaar. U moet de Id's bewaren voor het verzenden van de bijbehorende telemetrie-berichten.

**Telemetrie verzenden**

Nu u de apparaten en Sens oren in FarmBeats hebt gemaakt, kunt u de bijbehorende telemetrie-berichten verzenden.  

**Telemetrie-client maken**

U moet de telemetrie naar Azure Event hub verzenden voor verwerking. Azure EventHub is een service waarmee realtime gegevens (telemetrie) kunnen worden opgenomen vanuit verbonden apparaten en toepassingen. Als u telemetriegegevens wilt verzenden naar FarmBeats, moet u een client maken die berichten verzendt naar een event hub in FarmBeats. Zie [Azure Event hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)voor meer informatie over het verzenden van telemetrie.

**Een telemetrie-bericht verzenden als de client**

Zodra u een verbinding hebt gemaakt als een EventHub-client, kunt u berichten verzenden naar de EventHub als een JSON-bestand.  
Converteer de historische sensor gegevens indeling naar een canonieke indeling die door Azure FarmBeats wordt begrepen. De canonieke bericht indeling is als volgt:  


 ```
  {   
      “deviceid”: “<id of the Device created>”,   
      "timestamp": "<timestamp in ISO 8601 format>",     
      "version" : "1",   
      "sensors":
      [     
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


Nadat u de bijbehorende apparaten en Sens oren hebt toegevoegd, moet u het DeviceID en de sensorid in het telemetrie-bericht ophalen, zoals beschreven in de vorige sectie

Voor beeld van een telemetrie-bericht:


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


## <a name="next-steps"></a>Volgende stappen

Zie [rest API](references-for-farmbeats.md#rest-api)voor meer informatie over op rest API gebaseerde integratie Details.
