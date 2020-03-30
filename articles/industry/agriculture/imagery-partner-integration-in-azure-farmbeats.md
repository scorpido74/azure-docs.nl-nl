---
title: Partnerintegratie voor afbeeldingen
description: In dit artikel wordt beschreven dat de partnerintegratie van afbeeldingen wordt beschreven.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 62e5b363f8008380a61e24c0549573a30ecaeb73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131858"
---
# <a name="imagery-partner-integration"></a>Partnerintegratie voor afbeeldingen

In dit artikel wordt beschreven hoe u de component Azure FarmBeats Translator gebruiken om afbeeldingen naar FarmBeats te verzenden. Agrarische beelden kunnen worden gegenereerd uit verschillende bronnen, zoals multispectrale camera's, satellieten en drones. Agrarische beeldenpartners kunnen integreren met FarmBeats om klanten op maat gemaakte kaarten voor hun boerderijen te bieden.

Gegevens, eenmaal beschikbaar, kunnen worden gevisualiseerd via de FarmBeats Accelerator en mogelijk worden gebruikt voor data fusion en machine learning/artificial intelligence (ML/AI) modelbuilding door agrarische bedrijven of systeemintegratoren van klanten.

FarmBeats biedt de mogelijkheid om:

- Definieer aangepaste afbeeldingstypen, bron- en bestandsindeling met behulp van /ExtendedType API's.
- Opname beelden gegevens uit verschillende bronnen via de /Scene en / SceneFile API's.

De volgende informatie is gericht op het verkrijgen van elke vorm van beeldmateriaal in het FarmBeats-systeem.

Wanneer u de sectie **Drone-beelden** selecteert, wordt een pop-up geopend om een afbeelding met hoge resolutie van het droneorthomozaïek weer te geven. U hebt toegang tot de partnersoftware, die helpt bij het plannen van dronevluchten en het krijgen van ruwe gegevens. U blijft de software van de partner gebruiken voor padplanning en orthomozaïekbeeldstiksels.

Drone-partners moeten klanten in staat stellen hun klantenaccount te koppelen aan hun FarmBeats-exemplaar op Azure.

U moet de volgende referenties in de dronepartnersoftware gebruiken om FarmBeats te koppelen:

- API-eindpunt
- Tenant-id
- Client-id
- Clientgeheim

## <a name="api-development"></a>API-ontwikkeling

De API's bevatten technische documentatie van Swagger. Zie [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)voor informatie over de API's en bijbehorende verzoeken of antwoorden.

## <a name="authentication"></a>Authentication

FarmBeats maakt gebruik van Microsoft Azure [Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) (Azure AD). Azure App Service biedt ingebouwde verificatie- en autorisatieondersteuning. 

Zie [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)voor meer informatie over Azure AD.   

FarmBeats Datahub maakt gebruik van verificatie aan toonder, waarvoor de volgende referenties nodig zijn:

- Client-id
- Clientgeheim
- Tenant-id

Met behulp van de vorige referenties kan de beller een toegangstoken aanvragen, dat in de volgende API-aanvragen in de sectie header moet worden verzonden:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

In het volgende voorbeeld van python-code wordt het toegangstoken opgehaald. U het token vervolgens gebruiken voor volgende API-aanroepen naar FarmBeats.

```python
from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net"   
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

## <a name="http-request-headers"></a>HTTP-aanvraagkoppen

Dit zijn de meest voorkomende aanvraagkoppen die moeten worden opgegeven wanneer u een API-aanroep naar FarmBeats Datahub maakt.

**Header** | **Beschrijving en voorbeeld**
--- | ---
Content-Type  | De aanvraagindeling (Inhoudstype:<format>toepassing/ ). Voor FarmBeats Datahub API's is de indeling JSON. Inhoudstype: toepassing/json
Autorisatie | Hiermee geeft u het toegangstoken op dat nodig is om een API-aanroep te maken. Autorisatie:> van Access-Token aan touw <
Accepteren  | De antwoordnotatie. Voor FarmBeats Datahub API's is de indeling JSON. Accepteren: toepassing/json


## <a name="api-requests"></a>API-aanvragen

Als u een REST API-aanvraag wilt indienen, combineert u:

- De HTTP-methode (GET, POST en PUT).
- De URL naar de API-service.
- De resource URI (om gegevens op te vragen, in te dienen, bij te werken of te verwijderen).
- Een of meer HTTP-aanvraagkoppen.

Optioneel u queryparameters opnemen in GET-oproepen om te filteren, de grootte van de gegevens te beperken en de gegevens in de antwoorden te sorteren.

De volgende voorbeeldaanvraag is om de lijst met apparaten op te halen:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

De meeste GET, POST, en PUT oproepen vereisen een JSON aanvraag lichaam.

De volgende voorbeeldaanvraag is het maken van een apparaat. Dit monster heeft een input JSON met de aanvraaginstantie.


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Gegevensindeling

JSON is een veelgebruikte taalonafhankelijke gegevensindeling die een eenvoudige tekstweergave van willekeurige gegevensstructuren biedt. Zie [JSON org](https://JSON.org)voor meer informatie.

## <a name="ingest-imagery-into-farmbeats"></a>Afbeeldingen innemen in FarmBeats

Nadat de partner referenties heeft om verbinding te maken met FarmBeats Datahub, neemt de partner de volgende stappen in de component Translator.

1.  Maak een nieuw uitgebreid type voor de volgende velden, in overeenstemming met het type afbeeldingen dat moet worden geüpload:

    - **Scènebron**: drone_partner_name
    - **Scènetype:** bijvoorbeeld drone
    - **Scènebestandstype:** bijvoorbeeld chlorofylindex
    - **Scènebestandsinhoudstype:** bijvoorbeeld afbeelding/tiff

2.  Bel de API /Farms om de lijst met farms binnen het Azure FarmBeats-systeem te krijgen.
3.  Geef de klant de mogelijkheid om één bedrijf te kiezen uit de lijst van boerderijen.

    Het partnersysteem moet de boerderij binnen de partnersoftware tonen om de padplanning en dronevlucht en beeldverzameling te doen.

4.  Bel de /Scene API en geef vereiste details om een nieuwe scène te maken met een unieke scène-ID.
5.  Ontvang een blob SAS URL om de vereiste afbeeldingen te uploaden naar FarmBeats Datahub, in de context van de gekozen farm, in het FarmBeats-systeem.

Hier is een gedetailleerde stroom over de API-aanroepen.

### <a name="step-1-extendedtype"></a>Stap 1: ExtendedType

Schakel de /ExtendedType API in om te zien of het type en de bestandsbron beschikbaar zijn op FarmBeats. Roep hiervoor een GET aan op de /ExtendedType API.

Hier zijn de systeemgedefinieerde waarden:

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```

Deze stap is een eenmalige installatie. Het bereik van dit nieuwe scènetype is beperkt tot het abonnement waarin de Azure FarmBeats is geïnstalleerd.

Als u bijvoorbeeld SceneSource: "SlantRange" wilt toevoegen, doet u een PUT op de ID van de /ExtendedType API met de belangrijkste invoerpayload 'SceneSource'.

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

Het groene veld is de nieuwe toevoeging aan de door het systeem gedefinieerde scènebronwaarden.

### <a name="step-2-get-farm-details"></a>Stap 2: Meer informatie over de boerderij

De scènes (.tiff- of CSV-bestanden) bevinden zich in de context van een farm. Je moet de boerderij details te krijgen door het doen van een GET op de / Farm API. De API retourneert de lijst met farms die beschikbaar zijn in FarmBeats. U de farm selecteren waarvoor u de gegevens wilt innemen.

GET /Farm reactie:

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-a-scene-id-post-call"></a>Stap 3: Een scène-id maken (post-oproep)

Maak een nieuwe scène (.tiff of CSV-bestand) met de opgegeven informatie, die de datum, volgorde en farm-id biedt waaraan de scène is gekoppeld. De metagegevens die aan de scène zijn gekoppeld, kunnen worden gedefinieerd onder eigenschappen, waaronder de duur en het type meting.

Als u een nieuwe scène maakt, wordt een nieuwe scène-id gemaakt, die is gekoppeld aan de farm. Nadat de scène-id is gemaakt, kan de gebruiker hetzelfde gebruiken om een nieuw bestand (.tiff of .csv) te maken en de inhoud van het bestand op te slaan.

Voorbeeld van invoerpayload voor de POST-aanroep op de /Scene API:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

API-respons:

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**Een scènebestand maken**

De scène-ID die in stap 3 is geretourneerd, is de invoer voor het scènebestand. Het scènebestand retourneert een SAS URL-token, dat 24 uur geldig is.

Als de gebruiker een programmatische manier nodig heeft om een stroom afbeeldingen te uploaden, kan de SDK voor blobopslag worden gebruikt om een methode te definiëren met behulp van de scènebestands-id, locatie en URL.

Voorbeeld van invoerpayload voor de POST-aanroep op de /SceneFile API:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
API-respons:

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

De POST-aanroep naar de /SceneFile-API retourneert een URL voor het uploaden van SAS, die kan worden gebruikt om het CSV- of .tiff-bestand te uploaden met behulp van de Azure Blob-opslagclient of -bibliotheek.


## <a name="next-steps"></a>Volgende stappen

Zie [REST API](rest-api-in-azure-farmbeats.md)voor meer informatie over rest api-gebaseerde integratiegegevens.
