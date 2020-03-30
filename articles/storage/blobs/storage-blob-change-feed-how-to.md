---
title: Proceswijzigingsfeed in Azure Blob Storage (Preview) | Microsoft Documenten
description: Meer informatie over het verwerken van feedlogboeken voor wijzigingen in een .NET-clienttoepassing
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74111868"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Voert voor proceswijziging in Azure Blob Storage (voorbeeld)

De feed wijzigen biedt transactielogboeken van alle wijzigingen die zich voordoen in de blobs en de blobmetagegevens in uw opslagaccount. In dit artikel ziet u hoe u feedrecords voor wijzigingen lezen met behulp van de feedprocessorbibliotheek voor blobwijzigingen.

Zie [Feed wijzigen in Azure Blob Storage (Preview)](storage-blob-change-feed.md)voor meer informatie over de wijzigingsfeed.

> [!NOTE]
> De wijzigingsfeed is openbaar en is beschikbaar in de regio's **Westcentralus** en **Westus2.** Zie [Feedondersteuning wijzigen in Azure Blob Storage](storage-blob-change-feed.md)voor meer informatie over deze functie en bekende problemen en beperkingen. De bibliotheek voor de wijzigingsfeedprocessor kan worden gewijzigd tussen nu en wanneer deze bibliotheek algemeen beschikbaar wordt.

## <a name="get-the-blob-change-feed-processor-library"></a>De feedprocessorbibliotheek voor blobwijzigingen weergeven

1. Voeg in Visual Studio `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json` de URL toe aan uw NuGet-pakketbronnen. 

   Zie [pakketbronnen](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources)voor meer informatie.

2. Zoek in NuGet Package Manager het **Microsoft.Azure.Storage.Changefeed-pakket** en installeer het in uw project. 

   Zie [Een pakket zoeken en installeren](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)voor meer informatie.

## <a name="connect-to-the-storage-account"></a>Verbinding maken met het opslagaccount

Ontwende de verbindingstekenreeks door de [methode CloudStorageAccount.TryParse aan](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse) te roepen. 

Maak vervolgens een object dat Blob Storage vertegenwoordigt in uw opslagaccount door de [methode CloudStorageAccount.CreateCloudBlobClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient) aan te roepen.

```cs
public bool GetBlobClient(ref CloudBlobClient cloudBlobClient, string storageConnectionString)
{
    if (CloudStorageAccount.TryParse
        (storageConnectionString, out CloudStorageAccount storageAccount))
        {
            cloudBlobClient = storageAccount.CreateCloudBlobClient();

            return true;
        }
        else
        {
            return false;
        }
    }
}
```

## <a name="initialize-the-change-feed"></a>De wijzigingsfeed initialiseren

Voeg het volgende toe met behulp van instructies aan de bovenkant van uw codebestand. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Maak vervolgens een instantie van de klasse **ChangeFeed** door de **methode GetContainerReference** aan te roepen. Geef de naam van de wijzigingsvoercontainer door.

```csharp
public async Task<ChangeFeed> GetChangeFeed(CloudBlobClient cloudBlobClient)
{
    CloudBlobContainer changeFeedContainer =
        cloudBlobClient.GetContainerReference("$blobchangefeed");

    ChangeFeed changeFeed = new ChangeFeed(changeFeedContainer);
    await changeFeed.InitializeAsync();

    return changeFeed;
}
```

## <a name="reading-records"></a>Het lezen van records

> [!NOTE]
> De wijzigingsfeed is een onveranderlijke en alleen-lezen entiteit in uw opslagaccount. Elk aantal toepassingen kan de wijzigingsfeed gelijktijdig en onafhankelijk lezen en verwerken op hun eigen gemak. Records worden niet verwijderd uit de wijzigingsfeed wanneer een toepassing ze leest. De lees- of iteratiestatus van elke verbruikende lezer is onafhankelijk en wordt alleen door uw toepassing onderhouden.

De eenvoudigste manier om records te lezen is door een instantie van de klasse **ChangeFeedReader** te maken. 

In dit voorbeeld worden alle records in de wijzigingsfeed doorgestoofd en wordt vervolgens een paar waarden van elke record naar de console afgedrukt. 
 
```csharp
public async Task ProcessRecords(ChangeFeed changeFeed)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="resuming-reading-records-from-a-saved-position"></a>Leesrecords hervatten vanuit een opgeslagen positie

U ervoor kiezen om uw leespositie op te slaan in uw wijzigingsfeed en de records op een later tijdstip te herhalen. U de status van uw iteratie van de wijzigingsfeed op elk gewenst moment opslaan met de methode **ChangeFeedReader.SerializeState().** De status is een **tekenreeks** en uw toepassing kan die status opslaan op basis van het ontwerp van uw toepassing (bijvoorbeeld naar een database of een bestand).

```csharp
    string currentReadState = processor.SerializeState();
```

U door records van de laatste status blijven herhalen door de **ChangeFeedReader te** maken met de methode **CreateChangeFeedReaderFromPointerAsync.**

```csharp
public async Task ProcessRecordsFromLastPosition(ChangeFeed changeFeed, string lastReadState)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderFromPointerAsync(lastReadState);

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}

```

## <a name="stream-processing-of-records"></a>Verwerking van records streamen

U ervoor kiezen om feedrecords te wijzigen zodra deze binnenkomen. Zie [Specificaties](storage-blob-change-feed.md#specifications).

```csharp
public async Task ProcessRecordsStream(ChangeFeed changeFeed, int waitTimeMs)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    while (true)
    {
        do
        {
            currentRecord = await processor.GetNextItemAsync();

            if (currentRecord != null)
            {
                string subject = currentRecord.record["subject"].ToString();
                string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
                string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

                Console.WriteLine("Subject: " + subject + "\n" +
                    "Event Type: " + eventType + "\n" +
                    "Api: " + api);
            }

        } while (currentRecord != null);

        await Task.Delay(waitTimeMs);
    }
}
```

## <a name="reading-records-within-a-time-range"></a>Records lezen binnen een tijdsbereik

De wijzigingsfeed is ingedeeld in segmenten per uur op basis van de gebeurtenistijd wijzigen. Zie [Specificaties](storage-blob-change-feed.md#specifications). U records lezen uit feedsegmenten wijzigen die binnen een bepaald tijdsbereik vallen.

In dit voorbeeld worden de begintijden van alle segmenten opeen. Vervolgens wordt deze lijst doorgezet totdat de begintijd voorbij de tijd van het laatste verbruikssegment of voorbij de eindtijd van het gewenste bereik ligt. 

### <a name="selecting-segments-for-a-time-range"></a>Segmenten voor een tijdsbereik selecteren

```csharp
public async Task<List<DateTimeOffset>> GetChangeFeedSegmentRefsForTimeRange
    (ChangeFeed changeFeed, DateTimeOffset startTime, DateTimeOffset endTime)
{
    List<DateTimeOffset> result = new List<DateTimeOffset>();

    DateTimeOffset stAdj = startTime.AddMinutes(-15);
    DateTimeOffset enAdj = endTime.AddMinutes(15);

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;

    List<DateTimeOffset> segments = 
        (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();

    foreach (var segmentStart in segments)
    {
        if (lastConsumable.CompareTo(segmentStart) < 0)
        {
            break;
        }

        if (enAdj.CompareTo(segmentStart) < 0)
        {
            break;
        }

        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);

        bool overlaps = stAdj.CompareTo(segmentEnd) < 0 && 
            segmentStart.CompareTo(enAdj) < 0;

        if (overlaps)
        {
            result.Add(segmentStart);
        }
    }

    return result;
}
```

### <a name="reading-records-in-a-segment"></a>Records lezen in een segment

U records uit afzonderlijke segmenten of segmentenreeksen lezen.

```csharp
public async Task ProcessRecordsInSegment(ChangeFeed changeFeed, DateTimeOffset segmentOffset)
{
    ChangeFeedSegment segment = new ChangeFeedSegment(segmentOffset, changeFeed);
    await segment.InitializeAsync();

    ChangeFeedSegmentReader processor = await segment.CreateChangeFeedSegmentReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="read-records-starting-from-a-time"></a>Records vanaf een tijd lezen

U de records van de wijzigingsfeed van een beginsegment tot het einde lezen. Net als bij het lezen van records binnen een tijdsbereik, u de segmenten weergeven en een segment kiezen om te beginnen met herhalen.

In dit voorbeeld wordt de [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) van het eerste segment verwerkt.

```csharp
public async Task<DateTimeOffset> GetChangeFeedSegmentRefAfterTime
    (ChangeFeed changeFeed, DateTimeOffset timestamp)
{
    DateTimeOffset result = new DateTimeOffset();

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;
    DateTimeOffset lastConsumableEnd = lastConsumable.AddMinutes(60);

    DateTimeOffset timestampAdj = timestamp.AddMinutes(-15);

    if (lastConsumableEnd.CompareTo(timestampAdj) < 0)
    {
        return result;
    }

    List<DateTimeOffset> segments = (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();
    foreach (var segmentStart in segments)
    {
        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);
        if (timestampAdj.CompareTo(segmentEnd) <= 0)
        {
            result = segmentStart;
            break;
        }
    }

    return result;
}
```

In dit voorbeeld worden feedrecords gewijzigd vanaf de [DatumTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) van een beginsegment.

```csharp
public async Task ProcessRecordsStartingFromSegment(ChangeFeed changeFeed, DateTimeOffset segmentStart)
{
    TimeSpan waitTime = new TimeSpan(60 * 1000);

    ChangeFeedSegment segment = new ChangeFeedSegment(segmentStart, changeFeed);

    await segment.InitializeAsync();

    while (true)
    {
        while (!await IsSegmentConsumableAsync(changeFeed, segment))
        {
            await Task.Delay(waitTime);
        }

        ChangeFeedSegmentReader reader = await segment.CreateChangeFeedSegmentReaderAsync();

        do
        {
            await reader.CheckForFinalizationAsync();

            ChangeFeedRecord currentItem = null;
            do
            {
                currentItem = await reader.GetNextItemAsync();
                if (currentItem != null)
                {
                    string subject = currentItem.record["subject"].ToString();
                    string eventType = ((GenericEnum)currentItem.record["eventType"]).Value;
                    string api = ((GenericEnum)((GenericRecord)currentItem.record["data"])["api"]).Value;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            } while (currentItem != null);

            if (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized)
            {
                await Task.Delay(waitTime);
            }
        } while (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized);

        segment = await segment.GetNextSegmentAsync(); // TODO: What if next window doesn't yet exist?
        await segment.InitializeAsync(); // Should update status, shard list.
    }
}

private async Task<bool> IsSegmentConsumableAsync(ChangeFeed changeFeed, ChangeFeedSegment segment)
{
    if (changeFeed.LastConsumable >= segment.startTime)
    {
        return true;
    }
    await changeFeed.InitializeAsync();
    return changeFeed.LastConsumable >= segment.startTime;
}
```

>[!TIP]
> Een segment van de kan feedlogs wijzigen in een of meer *chunkFilePath*. In het geval van meerdere *chunkFilePath* heeft het systeem de records intern verdeeld in meerdere shards om publicatiedoorvoer te beheren. Het is gegarandeerd dat elke partitie van het segment wijzigingen zal bevatten voor wederzijds exclusieve blobs en onafhankelijk kan worden verwerkt zonder de volgorde te schenden. U de klasse **ChangeFeedSegmentShardReader** gebruiken om records op shardniveau te herhalen als dat het meest efficiënt is voor uw scenario.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over feedlogboeken wijzigen. Zie [Feed wijzigen in Azure Blob Storage (Voorbeeld)](storage-blob-change-feed.md)
