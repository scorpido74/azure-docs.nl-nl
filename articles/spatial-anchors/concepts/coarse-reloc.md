---
title: Grove herlokalisatie
description: Meer informatie over het gebruik van Grove herlokalisatie om ankers bij u in de buurt te vinden.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4c1604eaad1ebdedf6a360a647fe5b9f95c829c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844391"
---
# <a name="coarse-relocalization"></a>Coarse-relokalisatie

Grove herlokalisatie is een functie die een eerste antwoord geeft op de vraag: *Waar is mijn apparaat nu / Welke inhoud moet ik observeren?* Het antwoord is niet precies, maar in plaats daarvan is in de vorm: *Je bent dicht bij deze ankers; probeer het lokaliseren van een van hen*.

Grove herlokalisatie werkt door verschillende sensormetingen op het apparaat te koppelen aan zowel het maken als het opvragen van ankers. Voor buitenscenario's zijn de sensorgegevens meestal de GPS-positie (Global Positioning System) van het apparaat. Wanneer GPS niet beschikbaar of onbetrouwbaar is (zoals binnenshuis), bestaan de sensorgegevens uit de WiFi-toegangspunten en Bluetooth-bakens binnen bereik. Alle verzamelde sensorgegevens dragen bij aan het onderhouden van een ruimtelijke index die wordt gebruikt door de Azure Spatial Anchors om snel de ankers te bepalen die zich binnen ongeveer 100 meter van uw apparaat bevinden.

De snelle look-up van ankers mogelijk gemaakt door grove herlokalisatie vereenvoudigt de ontwikkeling van toepassingen ondersteund door world-scale collecties van (laten we zeggen, miljoenen geo-gedistribueerde) ankers. De complexiteit van ankerbeheer is allemaal verborgen, zodat u zich meer concentreren op uw geweldige toepassingslogica. Al het anker zwaar tillen wordt voor u gedaan achter de schermen door Azure Spatial Anchors.

## <a name="collected-sensor-data"></a>Verzamelde sensorgegevens

De sensorgegevens die u naar de ankerservice verzenden, zijn een van de volgende:

* GPS-positie: breedtegraad, lengtegraad, hoogte.
* Signaalsterkte van WiFi-toegangspunten binnen bereik.
* Signaalsterkte van Bluetooth-bakens binnen bereik.

In het algemeen moet uw toepassing apparaatspecifieke machtigingen verkrijgen om toegang te krijgen tot GPS-, WiFi- of BLE-gegevens. Bovendien is een deel van de bovenstaande sensorgegevens niet beschikbaar door het ontwerp op bepaalde platforms. Om rekening te houden met deze situaties, het verzamelen van sensorgegevens is optioneel en is standaard uitgeschakeld.

## <a name="set-up-the-sensor-data-collection"></a>De sensorgegevensverzameling instellen

Laten we beginnen met het maken van een sensor vingerafdruk provider en het maken van de sessie bewust van het:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[ObjC ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="java"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

Vervolgens moet u beslissen welke sensoren u wilt gebruiken voor grove herlokalisatie. Deze beslissing is specifiek voor de toepassing die u ontwikkelt, maar de aanbevelingen in de volgende tabel moeten u een goed uitgangspunt geven:


|             | Binnen | Buiten |
|-------------|---------|----------|
| Gps         | Uit | Aan |
| WiFi        | Aan | Aan (optioneel) |
| BLE-bakens | Aan (optioneel met kanttekeningen, zie hieronder) | Uit |


### <a name="enabling-gps"></a>GPS inschakelen

Ervan uitgaande dat uw toepassing al toestemming heeft om toegang te krijgen tot de GPS-positie van het apparaat, u Azure Spatial Anchors configureren om het te gebruiken:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[ObjC ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

Houd er bij het gebruik van GPS in uw toepassing rekening mee dat de metingen van de hardware doorgaans:

* asynchrone en lage frequentie (minder dan 1 Hz).
* onbetrouwbaar / luidruchtig (gemiddeld 7 m standaarddeviatie).

In het algemeen zullen zowel het apparaat BE als Azure Spatial Anchors wat filtering en extrapolatie op het ruwe GPS-signaal doen in een poging om deze problemen te beperken. Deze extra-verwerking vereist extra tijd voor convergentie, dus voor de beste resultaten moet u proberen om:

* maak zo vroeg mogelijk één sensorvingerafdrukprovider in uw toepassing
* houd de sensor vingerafdruk provider in leven tussen meerdere sessies
* de sensorvingerafdrukprovider delen tussen meerdere sessies

Als u van plan bent om de sensorvingerafdrukprovider buiten een ankersessie te gebruiken, moet u deze starten voordat u sensorschattingen opvraagt. De volgende code zorgt er bijvoorbeeld voor dat de positie van uw apparaat op de kaart in realtime wordt bijgewerkt:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objc"></a>[ObjC ObjC](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="java"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>WiFi inschakelen

Ervan uitgaande dat uw toepassing al toestemming heeft om toegang te krijgen tot de WiFi-status van het apparaat, u Azure Spatial Anchors configureren om deze te gebruiken:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[ObjC ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

Houd er bij het gebruik van WiFi in uw toepassing rekening mee dat de metingen van de hardware doorgaans:

* asynchrone en lage frequentie (minder dan 0,1 Hz).
* mogelijk gasgeven d.m.v. os-niveau.
* onbetrouwbaar / luidruchtig (gemiddeld 3-dBm standaarddeviatie).

Azure Spatial Anchors probeert tijdens een sessie een gefilterde WiFi-signaalsterktekaart te maken in een poging deze problemen te beperken. Voor de beste resultaten moet je proberen om:

* maak de sessie ruim voordat u het eerste anker plaatst.
* houd de sessie zo lang mogelijk in leven (dat wil zeggen, maak alle ankers en query in één sessie).

### <a name="enabling-bluetooth-beacons"></a>Bluetooth-bakens inschakelen

Ervan uitgaande dat uw toepassing al toestemming heeft om toegang te krijgen tot de Bluetooth-status van het apparaat, u Azure Spatial Anchors configureren om het te gebruiken:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[ObjC ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

Beacons zijn doorgaans veelzijdige apparaten, waar alles - inclusief UUID's en MAC-adressen - kan worden geconfigureerd. Deze flexibiliteit kan problematisch zijn voor Azure Spatial Anchors omdat het van mening is dat bakens uniek worden geïdentificeerd door hun uuid's. Niet om deze uniciteit te garanderen zal waarschijnlijk leiden tot ruimtelijke wormgaten. Voor de beste resultaten moet je:

* unieke uuid's toe te wijzen aan uw bakens.
* implementeren - meestal in een normaal patroon, zoals een raster.
* geef de lijst met unieke beacon-uuids door aan de sensorvingerafdrukprovider:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[ObjC ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="java"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Azure Spatial Anchors volgen alleen Bluetooth-bakens die zich in de lijst met bekende gebruikersinterface-u-id's voor de nabijheid van bakens bevinden. Kwaadaardige bakens geprogrammeerd om toegestane uu-id's hebben kan nog steeds een negatieve invloed hebben op de kwaliteit van de service wel. Daarom moet u bakens alleen gebruiken in samengestelde ruimten waar u de implementatie ervan beheren.

## <a name="querying-with-sensor-data"></a>Query's met sensorgegevens

Zodra u ankers hebt gemaakt met bijbehorende sensorgegevens, u beginnen ze op te halen met behulp van de sensormetingen die door uw apparaat worden gerapporteerd. U hoeft de service niet langer een lijst met bekende ankers te verstrekken die u verwacht te vinden - in plaats daarvan laat u de service gewoon de locatie van uw apparaat weten, zoals gerapporteerd door de sensoren aan boord. Azure Spatial Anchors zal dan de set ankers dicht bij uw apparaat achterhalen en proberen deze visueel aan te passen.

Als u query's wilt laten gebruiken, maakt u eerst criteria voor 'near device':

# <a name="c"></a>[C #](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objc"></a>[ObjC ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="java"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

De `DistanceInMeters` parameter bepaalt hoe ver we de ankergrafiek verkennen om inhoud op te halen. Stel bijvoorbeeld dat u enige ruimte met ankers bij een constante dichtheid van 2 elke meter hebt bevolkt. Bovendien observeert de camera op uw apparaat één anker en heeft de service deze met succes gelokaliseerd. U bent waarschijnlijk geïnteresseerd in het ophalen van alle ankers die u in de buurt hebt geplaatst in plaats van het enkele anker dat u momenteel observeert. Ervan uitgaande dat de ankers die u hebt geplaatst zijn aangesloten in een grafiek, kan de service alle ankers in de buurt voor u ophalen door de randen in de grafiek te volgen. De hoeveelheid grafiek traversal gedaan `DistanceInMeters`wordt gecontroleerd door ; u krijgt alle ankers die zijn aangesloten op de ankers `DistanceInMeters`die u hebt gevonden, die dichterbij zijn dan .

Houd er rekening mee `MaxResultCount` dat grote waarden voor de prestaties negatief kunnen beïnvloeden. Stel het in op een redelijke waarde voor uw toepassing.

Tot slot moet u de sessie vertellen om de op sensoren gebaseerde look up te gebruiken:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[ObjC ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="java"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>Verwachte resultaten

Gps-apparaten van consumentenkwaliteit zijn meestal vrij onnauwkeurig. Een studie van [Zandenbergen en Barbeau (2011)][6] meldt de mediane nauwkeurigheid van mobiele telefoons met assisted GPS (A-GPS) te worden ongeveer 7 meter - een vrij grote waarde te negeren! Om rekening te houden met deze meetfouten, behandelt de service de ankers als waarschijnlijkheidsverdelingen in gps-ruimte. Als zodanig, een anker is nu het gebied van de ruimte die het meest waarschijnlijk (dat wil zeggen, met meer dan 95% vertrouwen) bevat zijn ware, onbekende GPS-positie.

Dezelfde redenering wordt toegepast bij het bevragen met GPS. Het apparaat wordt voorgesteld als een andere ruimtelijke vertrouwensregio rond zijn ware, onbekende GPS-positie. Het ontdekken van ankers in de buurt vertaalt zich in het simpelweg vinden van de ankers met vertrouwensgebieden *dicht genoeg bij* het vertrouwensgebied van het apparaat, zoals geïllustreerd in de afbeelding hieronder:

![Selectie van ankerkandidaten met GPS](media/coarse-reloc-gps-separation-distance.png)

De nauwkeurigheid van het GPS-signaal, zowel op ankercreatie als tijdens query's, heeft een grote invloed op de set geretourneerde ankers. In tegenstelling, query's op basis van WiFi / beacons zal rekening houden met alle ankers die ten minste een access point / beacon gemeen met de query. In die zin wordt het resultaat van een query op basis van WiFi / beacons meestal bepaald door het fysieke bereik van de toegangspunten / bakens, en obstakels voor het milieu.

De onderstaande tabel schat de verwachte zoekruimte voor elk sensortype:

| Sensor      | Straal van zoekruimte (ca.) | Details |
|-------------|:-------:|---------|
| Gps         | 20 m - 30 m | Bepaald door de GPS-onzekerheid onder andere factoren. De gerapporteerde nummers worden geschat voor de mediane GPS-nauwkeurigheid van mobiele telefoons met A-GPS, dat is 7 meter. |
| WiFi        | 50 m - 100 m | Bepaald door het bereik van de draadloze toegangspunten. Afhankelijk van de frequentie, zendersterkte, fysieke obstakels, interferentie, enzovoort. |
| BLE-bakens |  70 m | Bepaald door het bereik van het baken. Afhankelijk van de frequentie, transmissiesterkte, fysieke obstakels, interferentie, enzovoort. |

## <a name="per-platform-support"></a>Ondersteuning per platform

De volgende tabel geeft een overzicht van de sensorgegevens die op elk van de ondersteunde platforms zijn verzameld, samen met platformspecifieke kanttekeningen:


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| Gps         | N.v.t. | Ondersteund via [LocationManager][3] API's (zowel GPS als NETWERK) | Ondersteund via [CLLocationManager][4] API's |
| WiFi        | Ondersteund met een snelheid van ongeveer één scan om de 3 seconden | Ondersteund. Beginnend met API niveau 28, worden WiFi-scans beperkt tot 4 gesprekken per 2 minuten. Vanaf Android 10 kan de beperking worden uitgeschakeld in het instellingenmenu Ontwikkelaars. Zie voor meer informatie de [Android-documentatie.][5] | N/A - geen openbare API |
| BLE-bakens | Beperkt tot [Eddystone][1] en [iBeacon][2] | Beperkt tot [Eddystone][1] en [iBeacon][2] | Beperkt tot [Eddystone][1] en [iBeacon][2] |

## <a name="next-steps"></a>Volgende stappen

Gebruik grove herlokalisatie in een app.

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Doelstelling-C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
