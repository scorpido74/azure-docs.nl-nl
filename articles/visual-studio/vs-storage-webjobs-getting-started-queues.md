---
title: Aan de slag met Queue Storage met Visual Studio (webtaaks-projecten)
description: Hoe u aan de slag gaat met Azure Queue Storage in een Webtaak-project nadat u verbinding hebt gemaakt met een opslag account met behulp van Visual Studio Connected Services.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ffba203bafaf3837cd2d7fc1a6fd962a6926b186
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298743"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Aan de slag met Azure Queue Storage en Visual Studio Connected Services (project taak)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Overzicht
In dit artikel wordt beschreven hoe u aan de slag gaat met Azure Queue Storage in een Visual Studio Azure-webproject, nadat u een Azure-opslag account hebt gemaakt of hiernaar wordt verwezen met behulp van het dialoog venster **verbonden services toevoegen** in Visual Studio. Wanneer u een opslag account toevoegt aan een project taak met behulp van het dialoog venster **verbonden services toevoegen** van Visual Studio, worden de juiste Azure Storage NuGet-pakketten geïnstalleerd, worden de juiste .net-verwijzingen toegevoegd aan het project en worden verbindings reeksen voor het opslag account wordt bijgewerkt in het bestand app. config.  

In dit artikel C# vindt u voor beelden van code die laten zien hoe u de Azure WebJobs SDK versie 1. x kunt gebruiken met de Azure Queue Storage-service.

Azure Queue Storage is een service voor de opslag van grote aantallen berichten die via HTTP of HTTPS overal vandaan kunnen worden opgevraagd met geverifieerde aanroepen. Een enkel wachtrijbericht mag maximaal 64 KB groot zijn en een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteitslimiet van een opslagaccount. Zie [aan de slag met Azure Queue Storage met behulp van .net](../storage/queues/storage-dotnet-how-to-use-queues.md) voor meer informatie. Zie [ASP.net](https://www.asp.net)voor meer informatie over ASP.net.

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Een functie activeren wanneer een wachtrij bericht wordt ontvangen
Als u een functie wilt schrijven die de webjobs SDK aanroept wanneer een wachtrij bericht wordt ontvangen, gebruikt u het kenmerk **Queue trigger** . De kenmerk-constructor neemt een teken reeks parameter op waarmee de naam van de wachtrij wordt opgegeven die moet worden gecontroleerd. Als u wilt zien hoe u de wachtrij naam dynamisch kunt instellen, leest u [hoe u configuratie opties instelt](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Teken reeks wachtrij berichten
In het volgende voor beeld bevat de wachtrij een teken reeks bericht, dus **Queue trigger** wordt toegepast op een teken reeks parameter met de naam **logMessage** die de inhoud van het wachtrij bericht bevat. De functie [schrijft een logboek bericht naar het dash board](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

Naast **teken reeksen**kan de para meter een byte matrix, een **CloudQueueMessage** -object of een poco die u definieert.

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplain_old_clr_object-queue-messages"></a>POCO [(onbewerkt oud CLR-object](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) berichten in wachtrij
In het volgende voor beeld bevat het wachtrij bericht JSON voor een **BlobInformation** -object dat een eigenschap **blobnaam** bevat. De SDK deserialiseren automatisch het object.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

De SDK gebruikt het [NuGet-pakket Newton soft. json](https://www.nuget.org/packages/Newtonsoft.Json) om berichten te serialiseren en deserialiseren. Als u wachtrij berichten maakt in een programma dat geen gebruikmaakt van de webjobs SDK, kunt u code schrijven zoals in het volgende voor beeld om een POCO-wachtrij bericht te maken dat de SDK kan parseren.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Async-functies
Met de volgende async-functie wordt [een logboek naar het dash board geschreven](#how-to-write-logs).

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

Async-functies kunnen een [annulerings token](https://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken)hebben, zoals wordt weer gegeven in het volgende voor beeld, waarmee een BLOB wordt gekopieerd. (Zie de sectie [blobs](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) voor een uitleg van de tijdelijke aanduiding **Queue trigger** .)

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
    CancellationToken token)
{
    await blobInput.CopyToAsync(blobOutput, 4096, token);
}
```

## <a name="types-the-queuetrigger-attribute-works-with"></a>Typen het kenmerk Queue trigger werkt met
U kunt **Queue trigger** gebruiken met de volgende typen:

* **tekenreeksexpressie**
* Een POCO-type geserialiseerd als JSON
* **byte []**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Polling-algoritme
De SDK implementeert een wille keurig exponentiële uitstel algoritme om het effect van polling bij een niet-actieve wachtrij op kosten voor opslag transacties te verminderen.  Wanneer een bericht wordt gevonden, wacht de SDK twee seconden en vervolgens wordt een ander bericht gecontroleerd. Wanneer er geen bericht wordt gevonden, wacht het ongeveer vier seconden voordat u het opnieuw probeert. Na volgende mislukte pogingen om een wachtrij bericht op te halen, blijft de wacht tijd toenemen totdat de maximale wacht tijd is bereikt. de standaard waarde is één minuut. [De maximale wacht tijd kan worden geconfigureerd](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Meerdere exemplaren
Als uw web-app op meerdere exemplaren wordt uitgevoerd, worden op elke machine een doorlopende webtaken uitgevoerd en wordt elke machine gewacht op Triggers en wordt geprobeerd om functies uit te voeren. In sommige scenario's kan dit ertoe leiden dat sommige functies dezelfde gegevens twee keer verwerken. Daarom moeten functies worden idempotent (geschreven zodat ze herhaaldelijk met dezelfde invoer gegevens worden aangeroepen) geen dubbele resultaten oplevert.  

## <a name="parallel-execution"></a>Parallelle uitvoering
Als er meerdere functies worden geluisterd naar verschillende wacht rijen, wordt deze door de SDK parallel aangeroepen wanneer berichten tegelijk worden ontvangen.

Hetzelfde geldt wanneer er meerdere berichten voor één wachtrij worden ontvangen. Standaard haalt de SDK een batch van 16 wachtrij berichten tegelijk op en wordt de functie uitgevoerd die deze parallel verwerkt. [De Batch grootte kan worden geconfigureerd](#how-to-set-configuration-options). Wanneer het aantal dat wordt verwerkt, wordt ingesteld op de helft van de Batch grootte, haalt de SDK een nieuwe batch op en begint deze met de verwerking van die berichten. Daarom is het maximum aantal gelijktijdige berichten dat per functie wordt verwerkt, één en een halve keer de Batch grootte. Deze limiet geldt afzonderlijk voor elke functie met een **Queue trigger** -kenmerk. Als u geen parallelle uitvoering wilt voor berichten die worden ontvangen in één wachtrij, stelt u de Batch grootte in op 1.

## <a name="get-queue-or-queue-message-metadata"></a>Meta gegevens van wachtrij of wachtrij bericht ophalen
U kunt de volgende bericht eigenschappen ophalen door para meters toe te voegen aan de methode handtekening:

* **Date Time offset** expirationTime
* **Date Time offset** insertionTime
* **Date Time offset** nextVisibleTime
* **teken reeks** Queue trigger (bevat bericht tekst)
* **teken reeks** -id
* **teken reeks** popReceipt
* **int** dequeueCount

Als u rechtstreeks met de Azure Storage API wilt werken, kunt u ook een **Cloud Storage account** -para meter toevoegen.

In het volgende voor beeld worden al deze meta gegevens naar een INFO toepassings logboek geschreven. In het voor beeld bevatten zowel logMessage als Queue trigger de inhoud van het wachtrij bericht.

```csharp
public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
    DateTimeOffset expirationTime,
    DateTimeOffset insertionTime,
    DateTimeOffset nextVisibleTime,
    string id,
    string popReceipt,
    int dequeueCount,
    string queueTrigger,
    CloudStorageAccount cloudStorageAccount,
    TextWriter logger)
{
    logger.WriteLine(
        "logMessage={0}\n" +
        "expirationTime={1}\ninsertionTime={2}\n" +
        "nextVisibleTime={3}\n" +
        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
        "queue endpoint={7} queueTrigger={8}",
        logMessage, expirationTime,
        insertionTime,
        nextVisibleTime, id,
        popReceipt, dequeueCount,
        cloudStorageAccount.QueueEndpoint,
        queueTrigger);
}
```

Hier volgt een voor beeld van een logboek dat is geschreven door de voorbeeld code:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Correct afsluiten
Een functie die wordt uitgevoerd in een doorlopende Webtaak kan een **CancellationToken** -para meter accepteren waarmee het besturings systeem de functie op de hoogte stelt wanneer de Webtaak wordt beëindigd. U kunt deze melding gebruiken om ervoor te zorgen dat de functie niet onverwacht wordt beëindigd op een manier die gegevens in een inconsistente status laat.

In het volgende voor beeld ziet u hoe u kunt controleren op het bedreigende taak einde van een functie.

```csharp
public static void GracefulShutdownDemo(
            [QueueTrigger("inputqueue")] string inputText,
            TextWriter logger,
            CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(1000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

**Opmerking:** Het dash board toont mogelijk niet de status en de uitvoer van functies die zijn afgesloten.

Zie [webjobs-correct afsluiten](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR)voor meer informatie.   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Een wachtrij bericht maken tijdens het verwerken van een wachtrij bericht
Als u een functie wilt schrijven die een nieuw wachtrij bericht maakt, gebruikt u het kenmerk **wachtrij** . Net als bij **Queue trigger**geeft u de naam van de wachtrij door als een teken reeks of kunt u [de wachtrij naam dynamisch instellen](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Teken reeks wachtrij berichten
Het volgende niet-async-code voorbeeld maakt een nieuw wachtrij bericht in de wachtrij met de naam ' output Queue ' met dezelfde inhoud als het wachtrij bericht dat in de wachtrij met de naam ' inputqueue ' is ontvangen. (Voor asynchrone functies gebruikt u **IAsyncCollector @ no__t-1T >** zoals verderop in deze sectie wordt weer gegeven.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplain_old_clr_object-queue-messages"></a>POCO [(onbewerkt oud CLR-object](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) berichten in wachtrij
Als u een wachtrij bericht wilt maken dat een POCO bevat in plaats van een teken reeks, geeft u het type POCO door als een uitvoer parameter voor de constructor van de **wachtrij** kenmerk.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

De SDK serialeert het object automatisch naar JSON. Er wordt altijd een wachtrij bericht gemaakt, zelfs als het object null is.

### <a name="create-multiple-messages-or-in-async-functions"></a>Meerdere berichten of asynchrone functies maken
Als u meerdere berichten wilt maken, maakt u het parameter type voor de uitvoer wachtrij **ICollector @ no__t-1T >** of **IAsyncCollector @ no__t-3T gebruiken >** , zoals in het volgende voor beeld wordt weer gegeven.

```csharp
public static void CreateQueueMessages(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
    TextWriter logger)
{
    logger.WriteLine("Creating 2 messages in outputqueue");
    outputQueueMessage.Add(queueMessage + "1");
    outputQueueMessage.Add(queueMessage + "2");
}
```

Elk wachtrij bericht wordt onmiddellijk gemaakt wanneer de **add** -methode wordt aangeroepen.

### <a name="types-that-the-queue-attribute-works-with"></a>Typen waarvoor het wachtrij kenmerk werkt
U kunt het kenmerk **wachtrij** gebruiken voor de volgende parameter typen:

* **uitvoer teken reeks** (maakt wachtrij bericht als de parameter waarde niet-null is wanneer de functie wordt beëindigd)
* **out-byte []** (werkt als **teken reeks**)
* **out CloudQueueMessage** (werkt als **teken reeks**)
* **out poco** (een serialiseerbaar type wordt een bericht met een null-object gemaakt als de para meter null is wanneer de functie wordt beëindigd)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (voor het rechtstreeks maken van berichten met de Azure Storage-API)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>De SDK-kenmerken van webjobs in de hoofd tekst van een functie gebruiken
Als u in uw functie wat werk moet doen voordat u een webjobs SDK-kenmerk gebruikt, zoals een **wachtrij**, **BLOB**of **tabel**, kunt u de **IBinder** -interface gebruiken.

In het volgende voor beeld wordt een invoer wachtrij bericht gebruikt en wordt er een nieuw bericht gemaakt met dezelfde inhoud in een uitvoer wachtrij. De naam van de uitvoer wachtrij wordt ingesteld op code in de hoofd tekst van de functie.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

De **IBinder** -interface kan ook worden gebruikt met de **tabel** -en **BLOB** -kenmerken.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Blobs en tabellen lezen en schrijven tijdens het verwerken van een wachtrij bericht
Met de kenmerken **BLOB** en **Table** kunt u blobs en tabellen lezen en schrijven. De voor beelden in deze sectie zijn van toepassing op blobs. Zie [Azure Blob Storage gebruiken met de Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)voor code voorbeelden die laten zien hoe u processen kunt activeren wanneer blobs worden gemaakt of bijgewerkt.
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Teken reeks wachtrij berichten waarmee BLOB-bewerkingen worden geactiveerd
Voor een wachtrij bericht dat een teken reeks bevat, is **Queue trigger** een tijdelijke aanduiding die u kunt gebruiken in de para meter **BlobPath** van het **BLOB** -kenmerk die de inhoud van het bericht bevat.

In het volgende voor beeld worden **Stream** -objecten gebruikt voor het lezen en schrijven van blobs. Het wachtrij bericht is de naam van een blob die zich in de textblobs-container bevindt. Er wordt een kopie van de blob met '-New ' toegevoegd aan de naam in dezelfde container gemaakt.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

De constructor van het **BLOB** -kenmerk gebruikt een **blobPath** -para meter die de container en de blobnaam opgeeft. Zie [Azure Blob Storage gebruiken met de Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)voor meer informatie over deze tijdelijke aanduiding.

Wanneer het kenmerk een **Stream** -object bijstelt, geeft een andere constructor-para meter de **FileAccess** -modus op als lezen, schrijven of lezen/schrijven.

In het volgende voor beeld wordt een **CloudBlockBlob** -object gebruikt voor het verwijderen van een blob. Het wachtrij bericht is de naam van de blob.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplain_old_clr_object-queue-messages"></a>POCO [(onbewerkt oud CLR-object](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) berichten in wachtrij
Voor een POCO die is opgeslagen als JSON in het wachtrij bericht, kunt u tijdelijke aanduidingen gebruiken die de naam eigenschappen van het object in de para meter **blobPath** van het **wachtrij** kenmerk hebben. U kunt ook eigenschaps namen van meta gegevens in de wachtrij als tijdelijke aanduidingen gebruiken. Zie [meta gegevens van wachtrij of wachtrij bericht ophalen](#get-queue-or-queue-message-metadata).

In het volgende voor beeld wordt een BLOB naar een nieuwe BLOB gekopieerd met een andere extensie. Het bericht in de wachtrij is een **BlobInformation** -object dat **BLOB** -en **BlobNameWithoutExtension** -eigenschappen bevat. De eigenschapnamen worden gebruikt als tijdelijke aanduidingen in het BLOB-pad voor de **BLOB** -kenmerken.

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

De SDK gebruikt het [NuGet-pakket Newton soft. json](https://www.nuget.org/packages/Newtonsoft.Json) om berichten te serialiseren en deserialiseren. Als u wachtrij berichten maakt in een programma dat geen gebruikmaakt van de webjobs SDK, kunt u code schrijven zoals in het volgende voor beeld om een POCO-wachtrij bericht te maken dat de SDK kan parseren.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Als u een aantal werk in uw functie moet uitvoeren voordat u een BLOB aan een object koppelt, kunt u het kenmerk gebruiken in de hoofd tekst van de functie, zoals wordt weer gegeven in [Webjobs SDK-kenmerken gebruiken in de hoofd tekst van een functie](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Typen waarmee u het kenmerk BLOB kunt gebruiken
Het kenmerk **BLOB** kan worden gebruikt met de volgende typen:

* **Stream** (lezen of schrijven, opgegeven met behulp van de para meter FileAccess-constructor)
* **TextReader**
* **TextWriter**
* **teken reeks** (lezen)
* **out-teken reeks** (write; maakt alleen een BLOB als de teken reeks parameter niet-null is wanneer de functie wordt geretourneerd)
* POCO (lezen)
* out POCO (write; maakt altijd een blob, maakt als null-object als de POCO-para meter null is wanneer de functie wordt geretourneerd)
* **CloudBlobStream** (schrijven)
* **ICloudBlob** (lezen of schrijven)
* **CloudBlockBlob** (lezen of schrijven)
* **CloudPageBlob** (lezen of schrijven)

## <a name="how-to-handle-poison-messages"></a>Poison-berichten verwerken
Berichten waarvan de inhoud ertoe leidt dat een functie mislukt, worden *verontreinigde berichten*genoemd. Wanneer de functie mislukt, wordt het wachtrij bericht niet verwijderd en wordt het uiteindelijk opnieuw opgehaald, waardoor de cyclus wordt herhaald. De SDK kan de cyclus automatisch onderbreken na een beperkt aantal iteraties, of u kunt dit hand matig doen.

### <a name="automatic-poison-message-handling"></a>Automatische verwerking van verontreinigde berichten
De SDK zal een functie aanroepen tot vijf keer om een wachtrij bericht te verwerken. Als de vijfde poging mislukt, wordt het bericht verplaatst naar een verontreinigde wachtrij. U kunt zien hoe u het maximum aantal nieuwe pogingen configureert voor het [instellen van configuratie opties](#how-to-set-configuration-options).

De verontreinigde wachtrij heeft de naam *{originalqueuename}* -Poison. U kunt een functie schrijven om berichten uit de verontreinigde wachtrij te verwerken door ze te registreren of om een melding te verzenden dat er hand matige aandacht nodig is.

In het volgende voor beeld wordt de functie **CopyBlob** mislukt wanneer een wachtrij bericht de naam bevat van een blob die niet bestaat. Als dat gebeurt, wordt het bericht verplaatst van de copyblobqueue-wachtrij naar de copyblobqueue-verontreinigde wachtrij. De **ProcessPoisonMessage** registreert vervolgens het verontreinigde bericht.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}

public static void ProcessPoisonMessage(
    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
{
    logger.WriteLine("Failed to copy blob, name=" + blobName);
}
```

In de volgende afbeelding ziet u de console-uitvoer van deze functies wanneer een verontreinigd bericht wordt verwerkt.

![Console-uitvoer voor verwerking van verontreinigde berichten](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Verwerking van hand matig verontreinigd bericht
U kunt het aantal keren dat een bericht voor verwerking is opgehaald door een **int** -para meter met de naam **dequeueCount** toe te voegen aan uw functie. U kunt vervolgens het aantal dewachtrijs in functie code controleren en uw eigen verwerkings verwerking van verontreinigde berichten uitvoeren wanneer het aantal een drempel waarde overschrijdt, zoals wordt weer gegeven in het volgende voor beeld.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
    TextWriter logger)
{
    if (dequeueCount > 3)
    {
        logger.WriteLine("Failed to copy blob, name=" + blobName);
    }
    else
    {
        blobInput.CopyTo(blobOutput, 4096);
    }
}
```

## <a name="how-to-set-configuration-options"></a>Configuratie opties instellen
U kunt het type **JobHostConfiguration** gebruiken om de volgende configuratie opties in te stellen:

* De SDK-verbindings reeksen instellen in de code.
* Configureer **Queue trigger** -instellingen, zoals maximum aantal dewachtrij-items.
* Haal de wachtrij namen op uit de configuratie.

### <a name="set-sdk-connection-strings-in-code"></a>SDK-verbindings reeksen instellen in code
Door de SDK-verbindings reeksen in code in te stellen, kunt u uw eigen connection string namen gebruiken in configuratie bestanden of omgevings variabelen, zoals wordt weer gegeven in het volgende voor beeld.

```csharp
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    var _serviceBusConn = ConfigurationManager
        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    config.ServiceBusConnectionString = _serviceBusConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="configure-queuetrigger--settings"></a>Queue trigger-instellingen configureren
U kunt de volgende instellingen configureren die van toepassing zijn op de verwerking van wachtrij berichten:

* Het maximum aantal wachtrij berichten dat tegelijkertijd wordt opgehaald om parallel te worden uitgevoerd (de standaard waarde is 16).
* Het maximum aantal nieuwe pogingen voordat een wachtrij bericht wordt verzonden naar een verontreinigde wachtrij (de standaard waarde is 5).
* De maximale wacht tijd vóór het navragen wanneer een wachtrij leeg is (standaard is 1 minuut).

In het volgende voor beeld ziet u hoe u deze instellingen configureert:

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Waarden voor de para meters van de SDK voor webjobs in code instellen
Soms wilt u een wachtrij naam, een blobnaam of container of een tabel naam in code opgeven in plaats van deze vast te geven. U kunt bijvoorbeeld de naam van de wachtrij voor **Queue trigger** opgeven in een configuratie bestand of omgevings variabele.

U kunt dit doen door een **NameResolver** -object door te geven aan het type **JobHostConfiguration** . U neemt speciale tijdelijke aanduidingen op, omgeven door percentage (%) teken in de para meters van de webjobs SDK-kenmerk-constructor en uw **NameResolver** -code geeft de werkelijke waarden op die moeten worden gebruikt in plaats van deze tijdelijke aanduidingen.

Stel dat u een wachtrij met de naam logqueuetest in de test omgeving en één met de naam logqueueprod in productie wilt gebruiken. In plaats van een in code vastgelegde wachtrij naam, wilt u de naam opgeven van een vermelding in de **appSettings** -verzameling die de daad werkelijke wachtrij naam zou hebben. Als de sleutel **appSettings** logqueue is, kan de functie er als volgt uitzien.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

De **NameResolver** -klasse kan vervolgens de wachtrij naam ophalen uit **appSettings** , zoals wordt weer gegeven in het volgende voor beeld:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

U geeft de klasse **NameResolver** door aan het object **JobHost** , zoals wordt weer gegeven in het volgende voor beeld.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Opmerking:** De namen van de wachtrij, tabel en BLOB worden omgezet elke keer dat een functie wordt aangeroepen, maar de namen van de BLOB-containers worden alleen omgezet wanneer de toepassing wordt gestart. U kunt de naam van de BLOB-container niet wijzigen terwijl de taak wordt uitgevoerd.

## <a name="how-to-trigger-a-function-manually"></a>Een functie hand matig activeren
Als u een functie hand matig wilt activeren, gebruikt u de methode **call** of **CallAsync** voor het object **JobHost** en het kenmerk **NoAutomaticTrigger** voor de functie, zoals in het volgende voor beeld wordt weer gegeven.

```csharp
public class Program
{
    static void Main(string[] args)
    {
        JobHost host = new JobHost();
        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
    }

    [NoAutomaticTrigger]
    public static void CreateQueueMessage(
        TextWriter logger,
        string value,
        [Queue("outputqueue")] out string message)
    {
        message = value;
        logger.WriteLine("Creating queue message: ", message);
    }
}
```

## <a name="how-to-write-logs"></a>Logboeken schrijven
In het dash board worden logboeken op twee locaties weer gegeven: de pagina voor de Webtaak en de pagina voor een bepaalde webaanroep.

![Logboeken op de pagina Webtaak](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Aanroep pagina van de functie Logboeken](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Uitvoer van console methoden die u aanroept in een functie of in de **Main ()** -methode wordt weer gegeven op de dashboard pagina voor de Webtaak en niet op de pagina voor een bepaalde methode aanroep. Uitvoer van het TextWriter-object dat u ophaalt uit een para meter in de hand tekening van de methode wordt weer gegeven op de dashboard pagina voor een methode aanroep.

Console-uitvoer kan niet worden gekoppeld aan een bepaalde methode aanroep, omdat de console een enkelvoudige thread heeft, terwijl veel taak functies tegelijkertijd kunnen worden uitgevoerd. Daarom biedt de SDK elke functie aanroep met een eigen uniek logboek schrijver-object.

Als u [Logboeken voor tracering van toepassingen](../app-service/troubleshoot-dotnet-visual-studio.md#logsoverview)wilt schrijven, gebruikt u **console. out** (maakt logboeken die zijn gemarkeerd als info) en **console. fout** (maakt logboeken die zijn gemarkeerd als fout). U kunt ook [Tracering of TraceSource](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)gebruiken. deze biedt uitgebreide, waarschuwings-en kritieke niveaus naast info en fout. Logboeken voor toepassings tracering worden weer gegeven in de logboek bestanden van de web-app, Azure-tabellen of Azure-blobs, afhankelijk van hoe u uw Azure-web-app hebt geconfigureerd. Net als bij alle console-uitvoer, worden de meest recente 100-toepassings logboeken ook weer gegeven op de dashboard pagina voor de Webtaak, niet op de pagina voor een functie aanroep.

Console-uitvoer wordt alleen in het dash board weer gegeven als het programma wordt uitgevoerd in een Azure-Webtaak, niet als het programma lokaal of in een andere omgeving wordt uitgevoerd.

U kunt logboek registratie uitschakelen door de dashboard connection string in te stellen op null. Zie [configuratie opties instellen](#how-to-set-configuration-options)voor meer informatie.

In het volgende voor beeld ziet u verschillende manieren om logboeken te schrijven:

```csharp
public static void WriteLog(
    [QueueTrigger("logqueue")] string logMessage,
    TextWriter logger)
{
    Console.WriteLine("Console.Write - " + logMessage);
    Console.Out.WriteLine("Console.Out - " + logMessage);
    Console.Error.WriteLine("Console.Error - " + logMessage);
    logger.WriteLine("TextWriter - " + logMessage);
}
```

In het dash board webjobs SDK wordt de uitvoer van het **TextWriter** -object weer gegeven wanneer u naar de pagina gaat voor een bepaalde functie aanroep en selecteert u **uitvoer in-/uitschakelen**:

![Aanroep koppeling](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Aanroep pagina van de functie Logboeken](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

In het dash board webjobs SDK worden de meest recente 100 regels van de console-uitvoer weer gegeven wanneer u naar de pagina voor de Webtaak gaat (niet voor het aanroepen van de functie) en selecteert u **uitvoer in-/uitschakelen**.

![Uitvoer in-/uitschakelen](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

In een doorlopende Webtaak worden toepassings logboeken weer gegeven in/data/Jobs/Continuous/ *{webjobname}* /job_log.txt in het web-app-bestands systeem.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

De toepassings logboeken van een Azure-Blob zien er als volgt uit: 2014-09-26T21:01:13, Information, contosoadsnew, 491e54, 635473620738373502, 0, 17404, 17, console. write-Hallo wereld!, 2014-09-26T21:01:13, error, contosoadsnew, 491e54, 635473620738373502, 0, 17404, 19, console. fout-Hallo wereld!, 2014-09-26T21:01:13, Information, contosoadsnew, 491e54, 635473620738529920, 0, 17404, 17, console. out-Hallo wereld!,

En in een Azure-tabel de **console. out** en **console. fout** logboeken zien er als volgt uit:

![Info logboek in tabel](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Fouten logboek in tabel](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Volgende stappen
In dit artikel vindt u code voorbeelden die laten zien hoe u veelvoorkomende scenario's kunt verwerken voor het werken met Azure-wacht rijen. Zie [Azure WebJobs documentation resources](https://go.microsoft.com/fwlink/?linkid=390226)(Engelstalig) voor meer informatie over het gebruik van Azure WebJobs en de webjobs SDK.

