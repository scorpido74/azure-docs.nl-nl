---
title: Partnerintegratie voor afbeeldingen
description: Hierin wordt beschreven hoe u de integratie van image-partner
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 788ffd9e7036996f6ac1bc7fcbc33137aca40ee2
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132018"
---
# <a name="imagery-partner-integration"></a>Partnerintegratie voor afbeeldingen

In dit artikel wordt beschreven hoe u het onderdeel Azure FarmBeats Translator gebruikt om afbeeldings gegevens naar FarmBeats te verzenden. Gegevens over agrarische beelden kunnen afkomstig zijn uit verschillende bronnen, waaronder MultiSpectral-camera's, satellieten en drones. Agrarische installatie kopieën kunnen worden geïntegreerd met FarmBeats om klanten met aangepaste, gegenereerde kaarten voor hun farms te voorzien.

Gegevens, die beschikbaar zijn, kunnen worden gevisualiseerd via de FarmBeats-Accelerator en mogelijk worden gebruikt voor gegevens fusie en (Machine Learning/kunst matige intelligentie) ML/AI-model gebouw door agrarische ondernemingen of integrators van klant systemen.

FarmBeats biedt de mogelijkheid om het volgende te doen:

- Aangepaste afbeeldings typen, bron, bestands indeling definiëren met behulp van uitgebreide-type Api's
- Neem afbeeldings gegevens op uit verschillende bronnen via de scène & SceneFile-Api's.

De onderstaande informatie is gericht op het ophalen van een wille keurige vorm van installatie kopieën in het FarmBeats-systeem.

Wanneer u de sectie drone installatie kopie selecteert, wordt er een pop-up geopend met een afbeelding met een hoge resolutie van de drone-orthomosaic. U hebt toegang tot de partner software, die helpt om drone vluchten te plannen en onbewerkte gegevens op te halen. U kunt de software van de partner blijven gebruiken voor het plannen van paden en orthomosaic-installatie kopieën.

Drone-partners moeten klanten inschakelen om hun klant account te koppelen aan hun FarmBeats-exemplaar in Azure.

U moet de volgende referenties gebruiken in de drone-partner software voor het koppelen van FarmBeats:

- API-eindpunt
- Tenant-id
- Client-id
- Clientgeheim

## <a name="api-development"></a>API-ontwikkeling

De Api's bevatten technische documentatie voor Swagger. Raadpleeg [Swagger](https://aka.ms/FarmBeatsDatahubSwagger) voor informatie over de api's en bijbehorende aanvragen/antwoorden.

## <a name="authentication"></a>Verificatie

FarmBeats maakt gebruik van de [Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)van Microsoft Azure. Azure App Service biedt ingebouwde ondersteuning voor verificatie en autorisatie. 

Zie [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)voor meer informatie over.   

De FarmBeats data hub maakt gebruik van Bearer-verificatie, die de volgende referenties nodig heeft:

- Client-id
- Clientgeheim
- Tenant-id

Met de bovenstaande referenties kan de aanroeper een toegangs token aanvragen dat in de volgende API-aanvragen moet worden verzonden in de koptekst sectie:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Hieronder vindt u een voor beeld van een python-code waarmee het toegangs token wordt opgehaald. U kunt vervolgens het token gebruiken voor de volgende API-aanroepen naar FarmBeat:   
Azure importeren 

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

## <a name="http-request-headers"></a>HTTP-aanvraag headers

Hier volgen de meest voorkomende aanvraag headers die moeten worden opgegeven bij het maken van een API-aanroep naar FarmBeats data hub:

**Header** | **Beschrijving en voor beeld**
--- | ---
Content-Type  | De aanvraag indeling (content-type: Application/<format>) voor de indeling van de FarmBeats data hub-API is JSON. Content-type: Application/JSON
Autorisatie | Hiermee geeft u het toegangs token op dat vereist is om een API-aanroep te maken. Autorisatie: Bearer < Access-token >
Zodat  | De antwoord indeling. Voor FarmBeats data hub-Api's is de indeling JSON Accept: Application/JSON


## <a name="api-requests"></a>API-aanvragen

Als u een REST API aanvraag wilt indienen, combineert u de HTTP-methode (GET/POST/PUT), de URL van de API-service, de resource-URI (om een query uit te voeren, gegevens in te dienen, bij te werken of te verwijderen) en een of meer HTTP-aanvraag headers.

U kunt desgewenst query parameters toevoegen aan GET-aanroepen om te filteren, de grootte van de gegevens in de antwoorden te beperken en te sorteren.

De onderstaande voorbeeld aanvraag is om de lijst met apparaten op te halen:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

Voor de meeste GET-, POST-en PUT-aanroepen is een JSON-aanvraag tekst vereist.

De onderstaande voorbeeld aanvraag is het maken van een apparaat (deze heeft een invoer-JSON met de hoofd tekst van de aanvraag).


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Gegevens indeling

JSON (JavaScript Object Notation) is een gemeen schappelijke, taal onafhankelijke gegevens indeling die een eenvoudige tekst weergave bevat van wille keurige gegevens structuren. Zie [JSON org](https://JSON.org)(Engelstalig) voor meer informatie.

## <a name="ingesting-imagery-into-farmbeats"></a>Afbeeldingen opnemen in FarmBeats

Nadat de partner referenties heeft om verbinding te maken met de FarmBeats data hub, doet de partner het volgende in het onderdeel Translator:

1.  Maak een nieuw uitgebreid type voor de volgende velden, in overeenstemming met het type installatie kopie dat wordt geüpload:

    - Scène Bron: bijvoorbeeld < drone_partner_name >
    - Type scène: bijvoorbeeld <drone>
    - Type scène bestand: bijvoorbeeld <chlorophyll index>
    - Type inhoud van scène bestand: bijvoorbeeld < installatie kopie/TIFF->

2.  Roep de Farms-API aan om de lijst met Farms in het Azure FarmBeats-systeem op te halen.
3.  Geef de klant de mogelijkheid om één farm te kiezen uit de lijst met farms.

    Het partner systeem moet de farm binnen de partner software weer geven om de drone-vlucht en-installatie kopie verzameling uit te voeren.

4.  Roep de scène-API aan en geef de vereiste gegevens op om een nieuwe scène met een unieke SceneID te maken.
5.  Ontvang een BLOB SAS-URL voor het uploaden van de vereiste installatie kopieën in de FarmBeats data hub, in de context van de gekozen Farm, in het FarmBeats-systeem.

Hier volgt een gedetailleerde stroom van de API-aanroepen:

### <a name="step-1-extendedtype"></a>Stap 1: Extended type

Controleer de Extended type-API als het type en de bestands bron beschikbaar zijn op FarmBeats. U kunt dit doen door de/ExtendedType-API GET aan te roepen.

Hier volgen de door het systeem gedefinieerde waarden:

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

Dit is een eenmalige installatie, en het bereik van deze nieuwe scenetype is beperkt tot het abonnement waarin het FarmBeats-project wordt geïmplementeerd.

Voor beeld: om SceneSource toe te voegen: "SlantRange", plaatst u de ID van de/ExtendedType met de sleutel: "SceneSource" invoer Payload:

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

Groen veld is de nieuwe toevoeging aan de door het systeem gedefinieerde scène bron waarden.

### <a name="step-2-get-farmdetails"></a>Stap 2: FarmDetails ophalen

De scènes (TIFF-of CSV-bestanden) worden in de context van een farm. U moet de gegevens van de farm ophalen met behulp van de/farm-API. De API retourneert de lijst met beschik bare Farms in FarmBeats en u kunt de farm selecteren waarvoor u de gegevens wilt opnemen.

/Farm-antwoord ophalen:

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

### <a name="step-3-create-ascene-id-post-call"></a>Stap 3: een/scène-ID maken (post-aanroep)

Maak een nieuwe scène (TIFF-of CSV-bestand) met de opgegeven informatie, waarbij u de datum, de volg orde en de farm-ID opgeeft waarmee de scène wordt gekoppeld. De meta gegevens die aan de scène zijn gekoppeld, kunnen worden gedefinieerd onder eigenschappen, met inbegrip van de duur en het type van de meting.

Hiermee maakt u een nieuwe SceneID, die wordt gekoppeld aan de farm. Zodra de SceneID is gemaakt, kan de gebruiker dezelfde gebruiken om een nieuw bestand (TIFF of. CSV) te maken & de inhoud van het bestand op te slaan.

Voor beeld van een nettolading voor de API post Call on/scène

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

API-antwoord:

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

**Maken/SceneFile**

De scène-ID die u in stap drie hebt geretourneerd, is de invoer voor de SceneFile. De SceneFile retourneert een SAS-URL-token, dat 24 uur geldig is.

Als de gebruiker een webstroom van installatie kopieën op een programmatische manier moet uploaden, kan de SDK voor Blob Storage worden gebruikt om een methode te definiëren met behulp van de Scenefile-ID, locatie & URL.

Voor beeld van een nettolading voor de post-aanroep van de/Scenefile-API:

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
API-antwoord:

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

De post-aanroep van de/SceneFile-API retourneert een SAS-upload-URL, die kan worden gebruikt om het CSV-of TIFF-bestand te uploaden met behulp van de Azure Blob Storage-client/-bibliotheek.


## <a name="next-steps"></a>Volgende stappen

Zie [rest API](references-for-farmbeats.md#rest-api)voor meer informatie over op rest API gebaseerde integratie Details.
