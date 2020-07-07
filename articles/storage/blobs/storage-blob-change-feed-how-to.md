---
title: Wijzigings feed in Azure Blob Storage verwerken (preview) | Microsoft Docs
description: Meer informatie over het verwerken van Logboeken voor wijzigings invoer in een .NET-client toepassing
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74111868"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Wijzigings feed in Azure Blob Storage verwerken (preview-versie)

Change feed biedt transactie logboeken van alle wijzigingen die plaatsvinden in de blobs en de BLOB-meta gegevens in uw opslag account. In dit artikel leest u hoe u wijzigingen in feeds kunt lezen met behulp van de processor bibliotheek voor het wijzigen van de blob.

Zie [Change feed in Azure Blob Storage (preview)](storage-blob-change-feed.md)voor meer informatie over de feed voor wijzigingen.

> [!NOTE]
> De wijzigings feed bevindt zich in de open bare preview en is beschikbaar in de regio's **westcentralus** en **westus2** . Zie [ondersteuning voor feeds wijzigen in Azure Blob Storage](storage-blob-change-feed.md)voor meer informatie over deze functie, samen met bekende problemen en beperkingen. De processor bibliotheek van de wijzigings feed kan nu worden gewijzigd en wanneer deze bibliotheek algemeen beschikbaar wordt.

## <a name="get-the-blob-change-feed-processor-library"></a>De processor bibliotheek voor het wijzigen van de BLOB-feed ophalen

1. Voeg in Visual Studio de URL toe `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json` aan uw NuGet-pakket bronnen. 

   Zie [pakket bronnen](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources)voor meer informatie.

2. Zoek in de NuGet package manager het pakket **micro soft. Azure. storage. Changefeed** en installeer het in uw project. 

   Zie [een pakket zoeken en installeren](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)voor meer informatie.

## <a name="connect-to-the-storage-account"></a>Verbinding maken met het opslag account

Parseer de connection string door de methode [Cloud Storage account. TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse) aan te roepen. 

Maak vervolgens een object dat Blob Storage in uw opslag account vertegenwoordigt door de methode [Cloud Storage account. CreateCloudBlobClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient) aan te roepen.

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

## <a name="initialize-the-change-feed"></a>De wijzigings feed initialiseren

Voeg de volgende using-instructies toe aan de bovenkant van het code bestand. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Maak vervolgens een instantie van de klasse **ChangeFeed** door de methode **GetContainerReference** aan te roepen. Geef de naam van de container voor de wijzigings invoer door.

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

## <a name="reading-records"></a>Records lezen

> [!NOTE]
> De wijzigings feed is een onveranderlijke en alleen-lezen entiteit in uw opslag account. Een wille keurig aantal toepassingen kan de wijzigings feed gelijktijdig en onafhankelijk van hun eigen gemak lezen en verwerken. Records worden niet uit de wijzigings feed verwijderd wanneer deze door een toepassing worden gelezen. De lees-of iteratie status van elke verbruikte lezer is onafhankelijk en wordt alleen door uw toepassing onderhouden.

De eenvoudigste manier om records te lezen is door een instantie van de klasse **ChangeFeedReader** te maken. 

In dit voor beeld worden alle records in de wijzigings feed door lopen en wordt er een paar waarden uit elke record naar de console afgedrukt. 
 
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

## <a name="resuming-reading-records-from-a-saved-position"></a>Het lezen van records vanuit een opgeslagen positie hervatten

U kunt ervoor kiezen om uw Lees positie op te slaan in uw wijzigings feed en de records in een later tijdstip te hervatten. U kunt de status van uw iteratie van de wijzigings feed op elk gewenst moment opslaan met de methode **ChangeFeedReader. SerializeState ()** . De status is een **teken reeks** en uw toepassing kan die status opslaan op basis van het ontwerp van uw toepassing (bijvoorbeeld: naar een Data Base of een bestand).

```csharp
    string currentReadState = processor.SerializeState();
```

U kunt door gaan met het door lopen van records van de laatste status door de **ChangeFeedReader** te maken met behulp van de methode **CreateChangeFeedReaderFromPointerAsync** .

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

## <a name="stream-processing-of-records"></a>Stroom verwerking van records

U kunt ervoor kiezen om wijzigingen in de feed-records te verwerken wanneer ze binnenkomen. Zie [specificaties](storage-blob-change-feed.md#specifications).

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

## <a name="reading-records-within-a-time-range"></a>Records in een tijds bereik lezen

De wijzigings feed is ingedeeld in uren segmenten op basis van de tijd voor het wijzigen van de gebeurtenis. Zie [specificaties](storage-blob-change-feed.md#specifications). U kunt records lezen van invoer segmenten die binnen een bepaald tijds bereik vallen.

In dit voor beeld worden de begin tijden van alle segmenten opgehaald. Vervolgens wordt deze lijst doorlopend totdat de begin tijd na het tijdstip van het laatste verbruikte segment of voorbij de eind tijd van het gewenste bereik valt. 

### <a name="selecting-segments-for-a-time-range"></a>Segmenten selecteren voor een tijds bereik

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

### <a name="reading-records-in-a-segment"></a>Records in een segment lezen

U kunt records uit afzonderlijke segmenten of bereiken van segmenten lezen.

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

## <a name="read-records-starting-from-a-time"></a>Records lezen vanaf een tijd

U kunt de records van de wijzigings feed lezen van een begin segment tot het einde. Net als bij het lezen van records binnen een tijds bereik, kunt u de segmenten weer geven en een segment kiezen om te beginnen met het herhalen van.

In dit voor beeld wordt de [Date Time offset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) opgehaald van het eerste segment dat moet worden verwerkt.

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

In dit voor beeld worden wijzigingen in feed-records verwerkt vanaf de [Date Time offset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) van een begin segment.

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
> In een segment van de kunnen wijzigingen in de feed worden vastgelegd in een of meer *chunkFilePath*. In het geval van meerdere *chunkFilePath* heeft het systeem intern de records in meerdere Shards gepartitioneerd om de door Voer van de publicatie te beheren. Het is gegarandeerd dat elke partitie van het segment wijzigingen voor wederzijds exclusieve blobs bevat en onafhankelijk kan worden verwerkt zonder de volg orde te schenden. U kunt de **ChangeFeedSegmentShardReader** -klasse gebruiken om records op het niveau van Shard te herhalen als dat het meest geschikt is voor uw scenario.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over feeds voor wijzigings Logboeken. Zie [Change feed in Azure Blob Storage (preview-versie)](storage-blob-change-feed.md)
