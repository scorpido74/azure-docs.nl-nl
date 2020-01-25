---
title: Uw gegevens exporteren naar Azure Blob Storage | Microsoft Docs
description: Gegevens exporteren uit uw Azure IoT Central-toepassing naar Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 79d578c910c7d08355901308e00db5912d1f3baf
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721486"
---
# <a name="export-your-data-to-azure-blob-storage"></a>Uw gegevens exporteren naar Azure Blob Storage

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Dit onderwerp is van toepassing op beheerders.*

In dit artikel wordt beschreven hoe u de functie continue gegevens export in azure IoT Central gebruikt om regel matig gegevens te exporteren naar uw **Azure Blob-opslag account** of **Azure data Lake Storage Gen2-opslag account**. U kunt **metingen**, **apparaten**en **Apparaatinstellingen** exporteren naar bestanden in de JSON-of Apache Avro-indeling. De geëxporteerde gegevens kunnen worden gebruikt voor koude-pad analyses, zoals trainings modellen in de analyse van Azure Machine Learning of lange termijn in micro soft Power BI.

> [!Note]
> Wanneer u continue gegevens export inschakelt, worden er vanaf dat moment alleen gegevens opgehaald. Op dit moment kunnen geen gegevens worden opgehaald voor een tijd dat de continue gegevens export is uitgeschakeld. Als u meer historische gegevens wilt behouden, moet u de continue gegevens export voor tijdig inschakelen.


## <a name="prerequisites"></a>Vereisten

- U moet een beheerder zijn in uw IoT Central-toepassing


## <a name="set-up-export-destination"></a>Export bestemming instellen

Als u geen bestaande opslag hebt om naar te exporteren, voert u de volgende stappen uit:

1. Maak een [Nieuw opslag account in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Meer informatie over het maken van nieuwe [Azure Blob Storage-accounts](https://aka.ms/blobdocscreatestorageaccount) of [Azure data Lake Storage v2-opslag accounts](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

    > [!Note] 
    > Als u ervoor kiest om gegevens te exporteren naar een ADLS v2-opslag account, moet u **account type** kiezen als **BlobStorage**. 

    > [!Note] 
    > U kunt gegevens exporteren naar opslag accounts in andere abonnementen dan die voor uw betalen per gebruik-IoT Central-toepassing. In dit geval kunt u verbinding maken met behulp van een connection string.

2. Maak een container in uw opslag account. Ga naar uw opslagaccount. Selecteer onder **BLOB**-service **Bladeren door blobs**. Selecteer **+ container** aan de bovenkant om een nieuwe container te maken.

## <a name="set-up-continuous-data-export"></a>Continue gegevens export instellen

Nu u een opslag bestemming hebt waarnaar u gegevens kunt exporteren, voert u de volgende stappen uit om continue gegevens export in te stellen. 

1. Meld u aan bij uw IoT Central-toepassing.

2. Selecteer in het linkerdeel venster **gegevens export**.

    > [!Note]
    > Als het exporteren van gegevens niet in het linkerdeel venster wordt weer gegeven, bent u geen beheerder in uw app. Neem contact op met een beheerder om het exporteren van gegevens in te stellen.

3. Selecteer de knop **+ Nieuw** in de rechter bovenhoek. Kies **Azure Blob Storage** als doel van uw export. 

    > [!NOTE] 
    > Het maximum aantal exports per app is vijf. 

    ![Nieuwe continue gegevens export maken](media/howto-export-data/export-new2.png)

4. Selecteer in de vervolg keuzelijst de naam ruimte van uw **opslag account**. U kunt ook de laatste optie in de lijst kiezen die **een connection string invoert**. 

    > [!NOTE] 
    > U ziet alleen opslag accounts met naam ruimten in **hetzelfde abonnement als uw IOT Central-app**. Als u wilt exporteren naar een bestemming buiten dit abonnement, kiest u **een Connection String invoeren** en gaat u naar stap 5.

    > [!NOTE] 
    > Voor apps met een proef versie van 7 dagen is de enige manier om continue gegevens export te configureren via een connection string. De reden hiervoor is dat apps met een proef versie van zeven dagen geen bijbehorend Azure-abonnement hebben.

    ![Nieuwe export naar Blob maken](media/howto-export-data/export-create-blob2.png)

5. Beschrijving Als u **een connection string invoert**, wordt er een nieuw vak weer gegeven waarin u uw Connection String kunt plakken. Als u de connection string voor uw opslag account wilt ophalen, gaat u naar het opslag account in de Azure Portal:-onder **instellingen**, selecteert u **toegangs sleutels** : de Key1-verbindings reeks of de Key2-verbindings reeks kopiëren
 
6. Kies een container in de vervolg keuzelijst. Als u geen container hebt, gaat u naar uw opslag account in de Azure Portal:
    - Onder **BLOB service**selecteert u **blobs**. Klik op **+ container** en geef de container een naam. Kies een openbaar toegangs niveau voor uw gegevens (wille keurige gegevens worden geëxporteerd). Meer informatie over [Azure Storage docs](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

7. Kies de gewenste **gegevens indeling** : JSON-of [Apache Avro](https://avro.apache.org/docs/current/index.html) -indeling.

8. Geef bij te **exporteren gegevens**elk type gegevens op dat moet worden geëxporteerd door het type in te stellen **op on**.

9. Als u continue gegevens export wilt inschakelen, moet u ervoor zorgen dat de wissel knop voor het **exporteren van gegevens** is **ingeschakeld**. Selecteer **Opslaan**.

   ![Continue gegevensexport configureren](media/howto-export-data/export-list-blob2.png)

10. Na een paar minuten worden uw gegevens weer gegeven in uw opslag account.


## <a name="path-structure"></a>Structuur van pad

Gegevens over metingen, apparaten en apparaatinstellingen worden één keer per minuut geëxporteerd naar uw opslag account, waarbij elk bestand met de batch wijzigingen sinds het laatste geëxporteerde bestand. Geëxporteerde gegevens worden in drie mappen geplaatst in JSON-of Avro-indeling. De standaard paden in uw opslag account zijn:
- Berichten: {container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Apparaten: {container}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Apparaatinstellingen: {container}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}

U kunt door de geëxporteerde bestanden in de Azure Portal bladeren door naar het bestand te navigeren en op het tabblad **BLOB bewerken** te klikken.

## <a name="data-format"></a>Gegevensindeling 

### <a name="measurements"></a>Metingen

De geëxporteerde meet gegevens hebben alle nieuwe berichten die door IoT Central worden ontvangen van alle apparaten tijdens die tijd. De geëxporteerde bestanden gebruiken dezelfde indeling als de bericht bestanden die worden geëxporteerd door [IOT hub bericht routering](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) naar Blob Storage.

> [!NOTE]
> Zorg ervoor dat uw apparaten berichten verzenden met `contentType: application/JSON` en `contentEncoding:utf-8` (of `utf-16``utf-32`). Raadpleeg de [documentatie van IOT hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body) voor een voor beeld.

> [!NOTE]
> De apparaten die de metingen verzenden, worden weer gegeven met apparaat-Id's (Zie de volgende secties). Als u de namen van de apparaten wilt ophalen, exporteert u de moment opnamen van het apparaat. Correleer elke berichten record met behulp van de **connectionDeviceId** die overeenkomt met de **deviceId** van de apparaatgegevens.

In het volgende voor beeld ziet u een record in een gedecodeerd Avro-bestand:

```json
{ 
  "EnqueuedTimeUtc":"2019-06-11T00:00:08.2250000Z",
  "Properties":{},
  "SystemProperties":{ 
    "connectionDeviceId":"<deviceId>",
    "connectionAuthMethod":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"<generationId>",
    "enqueuedTime":"2019-06-11T00:00:08.2250000Z"
  },
  "Body":"{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Apparaten

Wanneer voortdurende gegevens export is ingeschakeld, wordt één moment opname met alle apparaten geëxporteerd. Elk apparaat omvat:
- `id` van het apparaat in IoT Central
- `name` van het apparaat
- `deviceId` van [Device Provisioning Service](/azure/iot-central/core/howto-connect-nodejs)
- Informatie over de apparaatprofiel
- Waarden van eigenschappen
- Waarden instellen

Een nieuwe moment opname wordt eenmaal per minuut geschreven. De moment opname bevat:

- Nieuwe apparaten die zijn toegevoegd sinds de laatste moment opname.
- Apparaten met gewijzigde eigenschaps-en instellings waarden sinds de laatste moment opname.

> [!NOTE]
> Apparaten die zijn verwijderd sinds de laatste moment opname zijn niet geëxporteerd. Op dit moment hebben de moment opnamen geen indica toren voor verwijderde apparaten.
>
> De sjabloon van het apparaat waartoe elk apparaat behoort, wordt vertegenwoordigd door een ID van een apparaat sjabloon. Als u de naam van de apparaatprofiel wilt ophalen, exporteert u de moment opnamen van de apparaatnaam.

Geëxporteerde bestanden bevatten één regel per record. In het volgende voor beeld ziet u een record in Avro-indeling, gedecodeerd:

```json
{ 
  "id":"<id>",
  "name":"Refrigerator 2",
  "simulated":true,
  "deviceId":"<deviceId>",
  "deviceTemplate":{ 
    "id":"<template id>",
    "version":"1.0.0"
  },
  "properties":{ 
    "cloud":{ 
      "location":"New York",
      "maintCon":true,
      "tempThresh":20
    },
    "device":{ 
      "lastReboot":"2018-02-09T22:22:47.156Z"
    }
  },
  "settings":{ 
    "device":{ 
      "fanSpeed":0
    }
  }
}
```

### <a name="device-templates"></a>Apparaatinstellingen

Wanneer continue gegevens export is ingeschakeld, wordt één moment opname met alle Apparaatinstellingen geëxporteerd. Elke apparaatprofiel bevat:
- `id` van de sjabloon voor het apparaat
- `name` van de sjabloon voor het apparaat
- `version` van de sjabloon voor het apparaat
- Meet gegevens typen en minimale/maximale waarden.
- Eigenschaps gegevens typen en standaard waarden.
- Gegevens typen en standaard waarden instellen.

Een nieuwe moment opname wordt eenmaal per minuut geschreven. De moment opname bevat:

- Nieuwe apparaatinstellingen zijn toegevoegd sinds de laatste moment opname.
- Apparaatinstellingen met gewijzigde metingen, eigenschappen en instellings definities sinds de laatste moment opname.

> [!NOTE]
> De apparaatinstellingen zijn verwijderd sinds de laatste moment opname is niet geëxporteerd. Op dit moment hebben de moment opnamen geen indica toren voor verwijderde Apparaatinstellingen.

Geëxporteerde bestanden bevatten één regel per record. In het volgende voor beeld ziet u een record in Avro-indeling, gedecodeerd:

```json
{ 
  "id":"<id>",
  "name":"Refrigerated Vending Machine",
  "version":"1.0.0",
  "measurements":{ 
    "telemetry":{ 
      "humidity":{ 
        "dataType":"double",
        "name":"Humidity"
      },
      "magnetometerX":{ 
        "dataType":"double",
        "name":"Magnetometer X"
      },
      "magnetometerY":{ 
        "dataType":"double",
        "name":"Magnetometer Y"
      },
      "magnetometerZ":{ 
        "dataType":"double",
        "name":"Magnetometer Z"
      }
    },
    "states":{ 
      "connectivity":{ 
        "dataType":"enum",
        "name":"Connectivity"
      }
    },
    "events":{ 
      "opened":{ 
        "name":"Door Opened",
        "category":"informational"
      }
    }
  },
  "settings":{ 
    "device":{ 
      "fanSpeed":{ 
        "dataType":"double",
        "name":"Fan Speed",
        "initialValue":0
      }
    }
  },
  "properties":{ 
    "cloud":{ 
      "location":{ 
        "dataType":"string",
        "name":"Location",
        "initialValue":"Seattle"
      },
      "maintCon":{ 
        "dataType":"boolean",
        "name":"Maintenance Contract",
        "initialValue":true
      },
      "tempThresh":{ 
        "dataType":"double",
        "name":"Temperature Alert Threshold",
        "initialValue":30
      }
    },
    "device":{ 
      "lastReboot":{ 
        "dataType":"dateTime",
        "name":"Last Reboot"
      }
    }
  }
}
```

## <a name="read-exported-avro-files"></a>Geëxporteerde Avro-bestanden lezen

Avro is een binaire indeling, zodat de bestanden niet in de onbewerkte staat kunnen worden gelezen. De bestanden kunnen worden gedecodeerd naar de JSON-indeling. In de volgende voor beelden ziet u hoe u de metingen, apparaten en Avro-bestanden kunt parseren. De voor beelden komen overeen met de voor beelden die in de vorige sectie zijn beschreven.

### <a name="read-avro-files-by-using-python"></a>Avro-bestanden lezen met behulp van python

#### <a name="install-pandas-and-the-pandavro-package"></a>Pandas en het pandavro-pakket installeren

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Een AVRO-bestand voor metingen parseren

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(
        lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(
        lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)
```

#### <a name="parse-a-devices-avro-file"></a>Een AVRO-bestand van een apparaat parseren

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["deviceId"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(
        lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(
        lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

#### <a name="parse-a-device-templates-avro-file"></a>Een AVRO-bestand van een apparaat-sjabloon parseren

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    templates = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available in the id and version columns.
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = templates["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>Avro-bestanden lezen met behulp vanC#

#### <a name="install-the-microsofthadoopavro-package"></a>Het pakket micro soft. Hadoop. Avro installeren

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Een AVRO-bestand voor metingen parseren

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

#### <a name="parse-a-devices-avro-file"></a>Een AVRO-bestand van een apparaat parseren

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("deviceId");

                    // The device template information is stored in a sub-record
                    // under the deviceTemplate field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "Device ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```

#### <a name="parse-a-device-templates-avro-file"></a>Een AVRO-bestand van een apparaat-sjabloon parseren

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

### <a name="read-avro-files-by-using-javascript"></a>Avro-bestanden lezen met behulp van Java script

#### <a name="install-the-avsc-package"></a>Het avsc-pakket installeren

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Een AVRO-bestand voor metingen parseren

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device ID and humidity from each record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the system properties.
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the body from a buffer to a string and parse it.
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property from the body.
        const humidity = body.humidity;

        // Log the retrieved device ID and humidity.
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-devices-avro-file"></a>Een AVRO-bestand van een apparaat parseren

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the deviceId property.
        const deviceId = record.deviceId;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`deviceID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-device-templates-avro-file"></a>Een AVRO-bestand van een apparaat-sjabloon parseren

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and version properties.
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device id and humidity.
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u uw gegevens kunt exporteren, gaat u verder met de volgende stap:

> [!div class="nextstepaction"]
> [Uw gegevens visualiseren in Power BI](howto-connect-powerbi.md)
