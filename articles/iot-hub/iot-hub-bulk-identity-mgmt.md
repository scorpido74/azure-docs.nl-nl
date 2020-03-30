---
title: Identiteiten van Azure IoT Hub-apparaatidentiteiten importeren/exporteren | Microsoft Documenten
description: De Azure IoT-service SDK gebruiken om bulkbewerkingen uit te voeren tegen het identiteitsregister om identiteiten van apparaten te importeren en te exporteren. Met importbewerkingen u apparaatidentiteiten in bulk maken, bijwerken en verwijderen.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: robinsh
ms.openlocfilehash: 2a0394e6e7c17e0a4954bbdddb1d5b2811959746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371576"
---
# <a name="import-and-export-iot-hub-device-identities-in-bulk"></a>Id's van IoT Hub-apparaten bulksgewijs importeren en exporteren

Elke IoT-hub heeft een identiteitsregister dat u gebruiken om bronnen per apparaat in de service te maken. Met het identiteitsregister u ook de toegang tot de apparaatgerichte eindpunten beheren. In dit artikel wordt beschreven hoe u apparaatidentiteiten in bulk importeert en exporteert naar en vanuit een identiteitsregister. Zie [Een IoT-hub](iot-hub-how-to-clone.md)klonen als u een werkend voorbeeld in C# wilt bekijken en hoe u deze mogelijkheid gebruiken bij het klonen van een hub naar een andere regio.

> [!NOTE]
> IoT Hub heeft onlangs virtuele netwerkondersteuning toegevoegd in een beperkt aantal regio's. Deze functie beveiligt import- en exportbewerkingen en elimineert de noodzaak om sleutels voor verificatie door te geven.  In eerste instantie is ondersteuning van virtueel netwerk alleen beschikbaar in deze regio's: *WestUS2*, *EastUS*en *SouthCentralUS*. Zie [IoT Hub Support voor virtuele netwerken voor](virtual-network-support.md)meer informatie over ondersteuning van virtuele netwerken en de API-aanroepen om deze te implementeren.

Import- en exportbewerkingen vinden plaats in de context van *Taken* waarmee u bulkservicebewerkingen uitvoeren op een IoT-hub.

De klasse **RegistryManager** bevat de methoden **ExportDevicesAsync** en **ImportDevicesAsync** die het **taakframework** gebruiken. Met deze methoden u het volledige identiteitsregister van een IoT-hub exporteren, importeren en synchroniseren.

In dit onderwerp wordt gesproken over het gebruik van het **registermanager-** en **taaksysteem** om bulkinvoer en -export van apparaten uit te voeren van en naar het identiteitsregister van een IoT-hub. U de Azure IoT Hub Device Provisioning Service ook gebruiken om zero-touch, just-in-time provisioning voor een of meer IoT-hubs mogelijk te maken zonder menselijke tussenkomst. Zie voor meer informatie de documentatie van de [inrichtingsservice](/azure/iot-dps).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

## <a name="what-are-jobs"></a>Wat zijn banen?

Identiteitsregisterbewerkingen gebruiken het **taaksysteem** wanneer de bewerking:

* Heeft een potentieel lange uitvoeringstijd in vergelijking met standaard run-time operaties.

* Retourneert een grote hoeveelheid gegevens aan de gebruiker.

In plaats van één API-aanroep te wachten of te blokkeren op het resultaat van de bewerking, maakt de bewerking asynchroon een **taak** voor die IoT-hub. De bewerking retourneert vervolgens onmiddellijk een object **JobProperties.**

In het volgende C#-codefragment ziet u hoe u een exporttaak maakt:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Als u de klasse **RegistryManager** wilt gebruiken in uw C#-code, voegt u het **NuGet-pakket Microsoft.Azure.Devices** nuGet toe aan uw project. De klasse **RegistryManager** bevindt zich in de naamruimte **Microsoft.Azure.Devices.**

U de klasse **RegistryManager** gebruiken om de status van de **taak** op te vragen met de metagegevens van geretourneerde **JobProperties.** Als u een instantie van de klasse **RegisterManager wilt** maken, gebruikt u de methode **CreateFromConnectionString.**

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Ga als verkenner voor de verbindingstekenreeks voor uw IoT-hub naar de Azure-portal:

- Ga naar uw IoT-hub.

- Selecteer **Beleid voor gedeelde toegang**.

- Selecteer een beleid, rekening houdend met de machtigingen die u nodig hebt.

- Kopieer de verbindingstekenreeks van het paneel aan de rechterkant van het scherm.

In het volgende C#-codefragment ziet u hoe u elke vijf seconden pollen om te zien of de taak is voltooid:

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

> [!NOTE]
> Als uw opslagaccount firewallconfiguraties heeft die de connectiviteit van IoT Hub beperken, u microsoft [vertrouwde first party-uitzondering](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) gebruiken (beschikbaar in bepaalde regio's voor IoT-hubs met beheerde service-identiteit).


## <a name="device-importexport-job-limits"></a>Taaklimieten voor het importeren/exporteren van apparaten

Slechts 1 actieve taak voor het importeren of exporteren van actieve apparaten is toegestaan op een moment voor alle IoT Hub-lagen. IoT Hub heeft ook limieten voor het aantal taken. Zie [Referentie - IoT Hub-quota en beperking](iot-hub-devguide-quotas-throttling.md)voor meer informatie.

## <a name="export-devices"></a>Exportapparaten

Gebruik de **methode ExportDevicesAsync** om het volledige identiteitsregister van een IoT-hub te exporteren naar een Azure Storage blob-container met behulp van een gedeelde toegangshandtekening (SAS). Zie Beperkte toegang tot Azure [Storage-bronnen verlenen met behulp van gedeelde toegangshandtekeningen (SAS)](../storage/common/storage-sas-overview.md)voor meer informatie over handtekeningen voor gedeelde toegang.

Met deze methode u betrouwbare back-ups van uw apparaatgegevens maken in een blobcontainer die u beheert.

De **exportdevicesAsync-methode** vereist twee parameters:

* Een *tekenreeks* die een URI van een blobcontainer bevat. Deze URI moet een SAS-token bevatten waarmee schrijftoegang tot de container wordt toegekend. De taak maakt een blokblob in deze container om de geserialiseerde exportapparaatgegevens op te slaan. Het SAS-token moet de volgende machtigingen bevatten:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* Een *booleaan* die aangeeft of u verificatiesleutels wilt uitsluiten van uw exportgegevens. Als **onwaar**, zijn verificatiesleutels opgenomen in exportuitvoer. Anders worden sleutels geëxporteerd als **null**.

In het volgende C#-codefragment ziet u hoe u een exporttaak initieert die apparaatverificatiesleutels in de exportgegevens bevat en vervolgens wordt gepeild voor voltooiing:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
  await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

De taak slaat de uitvoer op in de meegeleverde blobcontainer als een blokblob met de naam **devices.txt**. De uitvoergegevens bestaan uit GESERIALiseerde apparaatgegevens van JSON, met één apparaat per lijn.

In het volgende voorbeeld worden de uitvoergegevens weergegeven:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Als een apparaat dubbele gegevens heeft, worden de dubbele gegevens ook geëxporteerd samen met de apparaatgegevens. In het volgende voorbeeld wordt deze indeling weergegeven. Alle gegevens van de "twinETag" lijn tot het einde is twin data.

```json
{
   "id":"export-6d84f075-0",
   "eTag":"MQ==",
   "status":"enabled",
   "statusReason":"firstUpdate",
   "authentication":null,
   "twinETag":"AAAAAAAAAAI=",
   "tags":{
      "Location":"LivingRoom"
   },
   "properties":{
      "desired":{
         "Thermostat":{
            "Temperature":75.1,
            "Unit":"F"
         },
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
            "$lastUpdatedVersion":2,
            "Thermostat":{
               "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
               "$lastUpdatedVersion":2,
               "Temperature":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               },
               "Unit":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               }
            }
         },
         "$version":2
      },
      "reported":{
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:51.1309437Z"
         },
         "$version":1
      }
   }
}
```

Als u toegang tot deze gegevens in code nodig hebt, u deze gegevens eenvoudig deserialiseren met de klasse **ExportImportDevice.** In het volgende C#-codefragment ziet u hoe u apparaatgegevens leest die eerder naar een blokblob zijn geëxporteerd:

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), null, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

## <a name="import-devices"></a>Apparaten importeren

Met de **methode ImportDevicesAsync** in de klasse **RegistryManager** u bulkimport- en synchronisatiebewerkingen uitvoeren in een IoT-hub-identiteitsregister. Net als de **exportdevicesAsync-methode** gebruikt de methode **ImportDevicesAsync** het **taakframework.**

Wees voorzichtig met behulp van de **ImportDevicesAsync-methode,** omdat het niet alleen nieuwe apparaten in uw identiteitsregister instelt, maar ook bestaande apparaten kan bijwerken en verwijderen.

> [!WARNING]
> Een importbewerking kan niet ongedaan worden gemaakt. Maak altijd een back-up van uw bestaande gegevens met de **methode ExportDevicesAsync** naar een andere blobcontainer voordat u bulkwijzigingen aanbrengt in uw identiteitsregister.

De **methode ImportDevicesAsync** heeft twee parameters:

* Een *tekenreeks* die een URI van een [Azure Storage blob-container](../storage/index.yml) bevat die kan worden gebruikt als *invoer voor* de taak. Deze URI moet een SAS-token bevatten dat leestoegang tot de container verleent. Deze container moet een blob bevatten met de naam **devices.txt** die de geserialiseerde apparaatgegevens bevat die u wilt importeren in uw identiteitsregister. De importgegevens moeten apparaatgegevens bevatten in dezelfde JSON-indeling die de taak **ExporterenImportDevice** gebruikt wanneer er een **devices.txt-blob** wordt gemaakt. Het SAS-token moet de volgende machtigingen bevatten:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* Een *tekenreeks* die een URI van een [Azure Storage blob-container](https://azure.microsoft.com/documentation/services/storage/) bevat die kan worden gebruikt als *uitvoer* van de taak. De taak maakt een blokblob in deze container om foutgegevens van de voltooide **importtaak op**te slaan. Het SAS-token moet de volgende machtigingen bevatten:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> De twee parameters kunnen naar dezelfde blobcontainer wijzen. De afzonderlijke parameters maken eenvoudig meer controle over uw gegevens mogelijk, omdat de uitvoercontainer extra machtigingen vereist.

In het volgende C#-codefragment ziet u hoe u een importtaak initieert:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Deze methode kan ook worden gebruikt om de gegevens voor de apparaattweeling te importeren. De indeling voor de gegevensinvoer is dezelfde als de indeling die wordt weergegeven in de sectie **ExportDevicesAsync.** Op deze manier u de geëxporteerde gegevens opnieuw importeren. De **$metadata** is optioneel.

## <a name="import-behavior"></a>Importgedrag

U de **methode ImportDevicesAsync** gebruiken om de volgende bulkbewerkingen uit te voeren in uw identiteitsregister:

* Bulkregistratie van nieuwe apparaten
* Bulkverwijderingen van bestaande apparaten
* Wijzigingen in de bulkstatus (apparaten in- of uitschakelen)
* Bulktoewijzing van nieuwe apparaatverificatiesleutels
* Bulkautomatische regeneratie van apparaatverificatiesleutels
* Bulk update van dubbele gegevens

U elke combinatie van de voorgaande bewerkingen uitvoeren binnen één **ImportDevicesAsync-gesprek.** U bijvoorbeeld nieuwe apparaten registreren en tegelijkertijd bestaande apparaten verwijderen of bijwerken. Wanneer u samen met de **ExportDevicesAsync-methode** wordt gebruikt, u al uw apparaten volledig migreren van de ene IoT-hub naar de andere.

Als het importbestand twee metagegevens bevat, overschrijft deze metagegevens de bestaande dubbele metagegevens. Als het importbestand geen dubbele metagegevens bevat, worden alleen de `lastUpdateTime` metagegevens bijgewerkt met de huidige tijd.

Gebruik de optionele eigenschap **importMode** in de importserialisatiegegevens voor elk apparaat om het importproces per apparaat te beheren. De eigenschap **importMode** heeft de volgende opties:

| importMode | Beschrijving |
| --- | --- |
| **createOrUpdate** |Als een apparaat niet bestaat met de opgegeven **ID,** wordt het opnieuw geregistreerd. <br/>Als het apparaat al bestaat, wordt bestaande informatie overschreven met de verstrekte invoergegevens, zonder rekening te houden met de **ETag-waarde.** <br> De gebruiker kan optioneel dubbele gegevens opgeven, samen met de apparaatgegevens. De etag van de tweeling, indien gespecificeerd, wordt onafhankelijk van de etag van het apparaat verwerkt. Als er een mismatch is met de etag van de bestaande tweeling, wordt er een fout naar het logboekbestand geschreven. |
| **Maken** |Als een apparaat niet bestaat met de opgegeven **ID,** wordt het opnieuw geregistreerd. <br/>Als het apparaat al bestaat, wordt er een fout naar het logboekbestand geschreven. <br> De gebruiker kan optioneel dubbele gegevens opgeven, samen met de apparaatgegevens. De etag van de tweeling, indien gespecificeerd, wordt onafhankelijk van de etag van het apparaat verwerkt. Als er een mismatch is met de etag van de bestaande tweeling, wordt er een fout naar het logboekbestand geschreven. |
| **Update** |Als een apparaat al bestaat met de opgegeven **id,** wordt bestaande informatie overschreven met de verstrekte invoergegevens, zonder rekening te houden met de **ETag-waarde.** <br/>Als het apparaat niet bestaat, wordt er een fout naar het logboekbestand geschreven. |
| **updateIfMatchETag** |Als een apparaat al bestaat met de opgegeven **id,** wordt bestaande informatie alleen overschreven met de verstrekte invoergegevens als er een **ETag-overeenkomst** is. <br/>Als het apparaat niet bestaat, wordt er een fout naar het logboekbestand geschreven. <br/>Als er een **ETag-fout** is, wordt er een fout naar het logboekbestand geschreven. |
| **createorUpdateIfMatchETag** |Als een apparaat niet bestaat met de opgegeven **ID,** wordt het opnieuw geregistreerd. <br/>Als het apparaat al bestaat, wordt bestaande informatie alleen overschreven met de verstrekte invoergegevens als er een **ETag-overeenkomst** is. <br/>Als er een **ETag-fout** is, wordt er een fout naar het logboekbestand geschreven. <br> De gebruiker kan optioneel dubbele gegevens opgeven, samen met de apparaatgegevens. De etag van de tweeling, indien gespecificeerd, wordt onafhankelijk van de etag van het apparaat verwerkt. Als er een mismatch is met de etag van de bestaande tweeling, wordt er een fout naar het logboekbestand geschreven. |
| **Verwijderen** |Als een apparaat al bestaat met de opgegeven **ID,** wordt het verwijderd zonder rekening te houden met de **ETag-waarde.** <br/>Als het apparaat niet bestaat, wordt er een fout naar het logboekbestand geschreven. |
| **deleteIfMatchETag** |Als een apparaat al bestaat met de opgegeven **id,** wordt het alleen verwijderd als er een **ETag-overeenkomst** is. Als het apparaat niet bestaat, wordt er een fout naar het logboekbestand geschreven. <br/>Als er een ETag-fout is, wordt er een fout naar het logboekbestand geschreven. |

> [!NOTE]
> Als de serialisatiegegevens een **importMode-vlag** voor een apparaat niet expliciet definiëren, wordt het standaard **gemaaktofupdate** tijdens de importbewerking.

## <a name="import-devices-example--bulk-device-provisioning"></a>Voorbeeld van importapparaten – inrichten van bulkapparaten

In het volgende c#-codevoorbeeld ziet u hoe u meerdere apparaatidentiteiten genereren die:

* Neem verificatiesleutels op.
* Schrijf die apparaatgegevens naar een blokblob.
* Importeer de apparaten in het identiteitsregister.

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
  // Create a new ExportImportDevice
  // CryptoKeyGenerator is in the Microsoft.Azure.Devices.Common namespace
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to the list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write the list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the blob to add new devices
// Log information related to the job is written to the same container
// This normally takes 1 minute per 100 devices
JobProperties importJob =
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Voorbeeld van apparaten importeren – verwijdering van bulk

In het volgende codevoorbeeld ziet u hoe u de apparaten verwijdert die u hebt toegevoegd met behulp van het vorige codevoorbeeld:

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="get-the-container-sas-uri"></a>Haal de container SAS URI

In het volgende codevoorbeeld ziet u hoe u een [SAS URI genereert](../storage/common/storage-dotnet-shared-access-signature-part-1.md) met lees-, schrijf- en verwijdermachtigingen voor een blobcontainer:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u bulkbewerkingen uitvoeren tegen het identiteitsregister in een IoT-hub. Veel van deze bewerkingen, waaronder het verplaatsen van apparaten van de ene hub naar de andere, worden gebruikt in het [gedeelte Apparaten beheren die zijn geregistreerd in het Gedeelte IoT-hub van Hoe een IoT-hub te klonen.](iot-hub-how-to-clone.md#managing-the-devices-registered-to-the-iot-hub) 

Het klonen artikel heeft een werkend monster gekoppeld aan het, die zich bevindt in de IoT C # monsters op deze pagina: [Azure IoT Samples for C #](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/), met het project wordt ImportExportDevicesSample. U het voorbeeld downloaden en uitproberen; er zijn instructies in het artikel [Hoe een IoT Hub te klonen.](iot-hub-how-to-clone.md)

Ga voor meer informatie over het beheer van Azure IoT Hub naar de volgende artikelen:

* [IoT Hub-statistieken](iot-hub-metrics.md)
* [IoT Hub-logboeken](iot-hub-monitor-resource-health.md)

Zie:

* [Handleiding voor IoT Hub-ontwikkelaars](iot-hub-devguide.md)
* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Zie: 

* [Azure IoT Hub Device Provisioning Service](/azure/iot-dps)
