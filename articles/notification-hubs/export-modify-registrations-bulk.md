---
title: Azure Notification Hubs-registraties in bulk exporteren en importeren | Microsoft Documenten
description: Meer informatie over het gebruik van bulkondersteuning voor Meldingenhubs om een groot aantal bewerkingen uit te voeren op een meldingshub of om alle registraties te exporteren.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/18/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 8eb03a42f38c0cc7fe82eda6a81d1c8c1213ec74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212396"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Azure Notification Hubs-registraties in bulk exporteren en importeren
Er zijn scenario's waarin het nodig is om grote aantallen registraties in een meldingshub te maken of te wijzigen. Sommige van deze scenario's zijn tagupdates na batchberekeningen of het migreren van een bestaande push-implementatie om Notification Hubs te gebruiken.

In dit artikel wordt uitgelegd hoe u een groot aantal bewerkingen uitvoert op een meldingshub of hoe u alle registraties in bulk exporteert.

## <a name="high-level-flow"></a>Stroom op hoog niveau
Batch-ondersteuning is ontworpen om langlopende taken te ondersteunen waarbij miljoenen registraties betrokken zijn. Om deze schaal te bereiken, gebruikt batchondersteuning Azure Storage om taakgegevens en uitvoer op te slaan. Voor bulkupdatebewerkingen moet de gebruiker een bestand maken in een blobcontainer, waarvan de inhoud de lijst is met registratieupdatebewerkingen. Bij het starten van de taak geeft de gebruiker een URL aan de invoerblob, samen met een URL naar een uitvoermap (ook in een blobcontainer). Nadat de taak is gestart, kan de gebruiker de status controleren door een URL-locatie op te vragen die is opgegeven bij het starten van de taak. Een specifieke taak kan alleen bewerkingen van een specifieke soort uitvoeren (maakt, wordt bijgewerkt of verwijderd). Exportbewerkingen worden analoog uitgevoerd.

## <a name="import"></a>Importeren

### <a name="set-up"></a>Instellen
In deze sectie wordt ervan uitgegaan dat u de volgende entiteiten hebt:

- Een ingerichte meldingshub.
- Een Blob-container voor Azure Storage.
- Verwijzingen naar het [Azure Storage NuGet-pakket](https://www.nuget.org/packages/windowsazure.storage/) en [het NuGet-pakket voor meldingen](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

### <a name="create-input-file-and-store-it-in-a-blob"></a>Invoerbestand maken en opslaan in een blob
Een invoerbestand bevat een lijst met registraties die in XML zijn geserialiseerd, één per rij. Met de Azure SDK ziet u in het volgende codevoorbeeld hoe u de registraties serialiseren en uploadt naar blobcontainer.

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(RegistrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> De voorgaande code serialiseert de registraties in het geheugen en uploadt vervolgens de hele stream naar een blob. Als u een bestand van meer dan een paar megabytes hebt geüpload, raadpleegt u de richtlijnen voor Azure-blobs voor het uitvoeren van deze stappen. [blokblobs](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs)bijvoorbeeld .

### <a name="create-url-tokens"></a>URL-tokens maken
Zodra uw invoerbestand is geüpload, genereert u de URL's die u aan uw meldingshub wilt verstrekken voor zowel het invoerbestand als de uitvoermap. U twee verschillende blobcontainers gebruiken voor invoer en uitvoer.

```csharp
static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + sasContainerToken);
}

static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Read
    };
    string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + "/" + filePath + sasToken);
}
```

### <a name="submit-the-job"></a>De taak verzenden
Met de twee invoer- en uitvoer-URL's u nu de batchtaak starten.

```csharp
NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
var createTask = client.SubmitNotificationHubJobAsync(
new NotificationHubJob {
        JobType = NotificationHubJobType.ImportCreateRegistrations,
        OutputContainerUri = outputContainerSasUri,
        ImportFileUri = inputFileSasUri
    }
);
createTask.Wait();

var job = createTask.Result;
long i = 10;
while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
{
    var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
    getJobTask.Wait();
    job = getJobTask.Result;
    Thread.Sleep(1000);
    i--;
}
```

Naast de url's voor invoer en `NotificationHubJob` uitvoer maakt `JobType` dit voorbeeld een object dat een object bevat, dat een van de volgende typen kan zijn:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

Zodra het gesprek is voltooid, wordt de taak voortgezet door de meldingshub en u de status ervan controleren met de oproep naar [GetNotificationHubJobAsync.](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet)

Bij het voltooien van de taak u de resultaten inspecteren door te kijken naar de volgende bestanden in uw uitvoermap:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Deze bestanden bevatten de lijst met geslaagde en mislukte bewerkingen van uw batch. De bestandsindeling `.cvs`is , waarin elke rij het regelnummer van het oorspronkelijke invoerbestand heeft, en de uitvoer van de bewerking (meestal de gemaakte of bijgewerkte registratiebeschrijving).

### <a name="full-sample-code"></a>Volledige voorbeeldcode
In de volgende voorbeeldcode worden registraties geïmporteerd in een meldingshub.

```csharp
using Microsoft.Azure.NotificationHubs;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;

namespace ConsoleApplication1
{
    class Program
    {
        private static string CONNECTION_STRING = "---";
        private static string HUB_NAME = "---";
        private static string INPUT_FILE_NAME = "CreateFile.txt";
        private static string STORAGE_ACCOUNT = "---";
        private static string STORAGE_PASSWORD = "---";
        private static StorageUri STORAGE_ENDPOINT = new StorageUri(new Uri("---"));

        static void Main(string[] args)
        {
            var descriptions = new[]
            {
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMkUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMjUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMhUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMdUxREQFBlVTTkMwMQ"),
            };

            //write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            //Lets import this file
            NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
            var createTask = client.SubmitNotificationHubJobAsync(
                new NotificationHubJob {
                    JobType = NotificationHubJobType.ImportCreateRegistrations,
                    OutputContainerUri = outputContainerSasUri,
                    ImportFileUri = inputFileSasUri
                }
            );
            createTask.Wait();

            var job = createTask.Result;
            long i = 10;
            while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
            {
                var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
                getJobTask.Wait();
                job = getJobTask.Result;
                Thread.Sleep(1000);
                i--;
            }
        }

        private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
        {
            StringBuilder builder = new StringBuilder();
            foreach (var registrationDescription in descriptions)
            {
                builder.AppendLine(RegistrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + "/" + filePath + sasToken);
        }

        static string GetJobPath(string namespaceName, string notificationHubPath, string jobId)
        {
            return string.Format(CultureInfo.InvariantCulture, @"{0}//{1}/{2}/", namespaceName, notificationHubPath, jobId);
        }
    }
}
```

## <a name="export"></a>Exporteren
De exportregistratie is vergelijkbaar met de invoer, met de volgende verschillen:

- U hebt alleen de uitvoer-URL nodig.
- U maakt een NotificationHubJob van type ExportRegistrations.

### <a name="sample-code-snippet"></a>Voorbeeld van voorbeeldcode
Hier is een voorbeeld code fragment voor het exporteren van registraties in Java:

```java
// submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over registraties:

- [Registratiebeheer](notification-hubs-push-notification-registration-management.md)
- [Labels voor registraties](notification-hubs-tags-segment-push-message.md)
- [Sjabloonregistraties](notification-hubs-templates-cross-platform-push-messages.md)
