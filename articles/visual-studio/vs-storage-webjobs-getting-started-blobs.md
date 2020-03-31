---
title: Aan de slag met blob-opslag met Visual Studio (WebJob-projecten)
description: Aan de slag met Blob-opslag in een WebJob-project nadat u verbinding hebt gemaakt met een Azure-opslag met behulp van met Visual Studio verbonden services.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 90aa824b7df575eb2783ece5bd88322f0b55f0a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299973"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Aan de slag met Azure Blob-opslag- en Visual Studio-connected services (WebJob-projecten)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Overzicht
In dit artikel worden C#-codevoorbeelden weergegeven die laten zien hoe u een proces activeert wanneer een Azure-blob wordt gemaakt of bijgewerkt. De codevoorbeelden maken gebruik van de [WebJobs SDK-versie](https://github.com/Azure/azure-webjobs-sdk/wiki) 1.x. Wanneer u een opslagaccount toevoegt aan een WebJob-project met het dialoogvenster Verbonden services toevoegen van Visual Studio **Add,** wordt het juiste Azure Storage NuGet-pakket geïnstalleerd, worden de juiste .NET-verwijzingen aan het project toegevoegd en worden verbindingstekenreeksen voor het opslagaccount bijgewerkt in het app.config-bestand.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Een functie activeren wanneer een blob wordt gemaakt of bijgewerkt
In deze sectie ziet u hoe u het kenmerk **BlobTrigger** gebruikt.

 **Let op:** De WebJobs SDK scant logbestanden om te kijken naar nieuwe of gewijzigde blobs. Dit proces is inherent traag; een functie wordt mogelijk pas enkele minuten of langer na het maken van de blob geactiveerd.  Als uw toepassing blobs onmiddellijk moet verwerken, moet u een wachtrijbericht maken wanneer u de blob maakt en het kenmerk **QueueTrigger** gebruiken in plaats van het kenmerk **BlobTrigger** op de functie die de blob verwerkt.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Enkele tijdelijke aanduiding voor blobnaam met extensie
In het volgende codevoorbeeld worden tekstblobs die in de *invoercontainer* worden weergegeven, kopieën gemaakt van *de uitvoercontainer:*

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

De kenmerkconstructor neemt een tekenreeksparameter die de containernaam en een tijdelijke aanduiding voor de blobnaam opgeeft. Als in dit voorbeeld een blob met de naam *Blob1.txt* wordt gemaakt in de *invoercontainer,* wordt in de functie een blob met de naam *Blob1.txt* in de *uitvoercontainer* gemaakt.

U een naampatroon opgeven met de tijdelijke aanduiding voor de blobnaam, zoals in het volgende codevoorbeeld wordt weergegeven:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Deze code kopieert alleen blobs met namen die beginnen met 'origineel'. *Originele Blob1.txt* in de *invoercontainer* wordt bijvoorbeeld gekopieerd naar *copy-Blob1.txt* in de *uitvoercontainer.*

Als u een naampatroon moet opgeven voor blobnamen met krullende accolades in de naam, verdubbelt u de krullende accolades. Als u bijvoorbeeld blobs wilt vinden in de *afbeeldingencontainer* met namen als deze:

        {20140101}-soundfile.mp3

gebruik dit voor uw patroon:

        images/{{20140101}}-{name}

In het voorbeeld zou de waarde van de *naamaanduiding* *soundfile.mp3*zijn.

### <a name="separate-blob-name-and-extension-placeholders"></a>Afzonderlijke tijdelijke aanduidingen voor blobs en extensie
In het volgende codevoorbeeld wordt de bestandsextensie gewijzigd bij het kopieën van blobs die in de *invoercontainer* worden weergegeven, naar de *uitvoercontainer.* De code registreert de extensie van de *invoerblob* en stelt de extensie van de *uitvoerblob* in op *.txt*.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="types-that-you-can-bind-to-blobs"></a>Typen die u aan blobs binden
U het kenmerk **BlobTrigger** gebruiken voor de volgende typen:

* **tekenreeks**
* **Tekstlezer**
* **Streamen**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Andere typen gedeserialiseerd door [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Als u rechtstreeks met het Azure-opslagaccount wilt werken, u ook een **parameter CloudStorageAccount** toevoegen aan de methodehandtekening.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Tekstblobinhoud opmaken door aan tekenreeks te binden
Als tekstblobs worden verwacht, kan **BlobTrigger** worden toegepast op een **tekenreeksparameter.** In het volgende codevoorbeeld wordt een tekstblob gekoppeld aan een **tekenreeksparameter** met de naam **logMessage**. De functie gebruikt die parameter om de inhoud van de blob naar het WebJobs SDK-dashboard te schrijven.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Geserialiseerde blob-inhoud verkrijgen met ICloudBlobStreamBinder
In het volgende codevoorbeeld wordt een klasse gebruikt die **ICloudBlobStreamBinder** implementeert om het kenmerk **BlobTrigger** in te schakelen om een blob aan het **webimagetype** te binden.

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

De **WebImage** WebImage-bindingscode wordt geleverd in een klasse **WebImageBinder** die afkomstig is van **ICloudBlobStreamBinder.**

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="how-to-handle-poison-blobs"></a>Hoe om te gaan met gif blobs
Wanneer een **BlobTrigger-functie** mislukt, roept de SDK deze opnieuw aan, voor het geval de fout is veroorzaakt door een tijdelijke fout. Als de fout wordt veroorzaakt door de inhoud van de blob, mislukt de functie elke keer dat de blob wordt verwerkt. Standaard roept de SDK een functie tot 5 keer aan voor een bepaalde blob. Als de vijfde poging mislukt, voegt de SDK een bericht toe aan een wachtrij met de naam *webjobs-blobtrigger-poison*.

Het maximum aantal nieuwe pogingen is configureerbaar. Dezelfde **MaxDequeueCount-instelling** wordt gebruikt voor het verwerken van gifblobs en het verwerken van gifwachtrijberichten.

Het wachtrijbericht voor gifblobs is een JSON-object dat de volgende eigenschappen bevat:

* FunctionId (in de indeling *{WebJob-naam}*. Functies. *{Functienaam}*, bijvoorbeeld: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" of "PageBlob")
* ContainerName
* BlobName
* ETag (een blob-versie-id, bijvoorbeeld: "0x8D1DC6E70A277EF")

In het volgende codevoorbeeld heeft de functie **CopyBlob** code die ervoor zorgt dat deze elke keer dat deze wordt aangeroepen, mislukt. Nadat de SDK het voor het maximum aantal nieuwe pogingen aanroept, wordt een bericht gemaakt op de gifblobwachtrij en wordt dat bericht verwerkt door de functie **LogPoisonBlob.**

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

De SDK deserialiseert automatisch het JSON-bericht. Hier is de **PoisonBlobMessage** klasse:

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Blob-polling-algoritme
De WebJobs SDK scant alle containers die zijn opgegeven door **BlobTrigger-kenmerken** bij het starten van de toepassing. In een groot opslagaccount kan deze scan enige tijd duren, dus het kan even duren voordat er nieuwe blobs worden gevonden en **BlobTrigger-functies** worden uitgevoerd.

Als u nieuwe of gewijzigde blobs na het starten van de toepassing wilt detecteren, leest de SDK periodiek uit de blob-opslaglogboeken. De bloblogs worden gebufferd en worden slechts om de 10 minuten fysiek geschreven, dus er kan aanzienlijke vertraging optreden nadat een blob is gemaakt of bijgewerkt voordat de bijbehorende **BlobTrigger-functie** wordt uitgevoerd.

Er is een uitzondering voor blobs die u maakt met het kenmerk **Blob.** Wanneer de WebJobs SDK een nieuwe blob maakt, geeft deze de nieuwe blob onmiddellijk door aan overeenkomende **BlobTrigger-functies.** Als u dus een keten van blob-ingangen en uitgangen hebt, kan de SDK deze efficiënt verwerken. Maar als u wilt dat de blobverwerkingsfuncties voor blobs die op een **QueueTrigger** andere manier zijn gemaakt of bijgewerkt, met lage latentie worden **uitgevoerd.**

### <a name="blob-receipts"></a>Blob-ontvangstbewijzen
De WebJobs SDK zorgt ervoor dat geen enkele **BlobTrigger-functie** meer dan één keer wordt aangeroepen voor dezelfde nieuwe of bijgewerkte blob. Het doet dit door *blob-ontvangstbewijzen* te onderhouden om te bepalen of een bepaalde blobversie is verwerkt.

Blob-ontvangstbewijzen worden opgeslagen in een container met de naam *azure-webjobs-hosts* in het Azure-opslagaccount dat is opgegeven door de azureWebJobsStorage-verbindingstekenreeks. Een blob-ontvangstbewijs heeft de volgende informatie:

* De functie die is aangeroepen voor de blob ("*{WebJob-naam}*. Functies. *{Functienaam}*", bijvoorbeeld: "WebJob1.Functions.CopyBlob")
* De containernaam
* Het blobtype ('BlockBlob' of 'PageBlob')
* De blobnaam
* De ETag (een blob-versie-id, bijvoorbeeld: "0x8D1DC6E70A277EF")

Als u opwerking van een blob wilt forceren, u het blob-ontvangstbewijs voor die blob handmatig verwijderen uit de container *azure-webjobs-hosts.*

## <a name="related-topics-covered-by-the-queues-article"></a>Gerelateerde onderwerpen die in het wachtrijartikel aan bod komen
Zie [Azure queue storage gebruiken met de WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)voor informatie over het afhandelen van blobverwerking die wordt geactiveerd door een wachtrijbericht of voor WebJobs SDK-scenario's die niet specifiek zijn voor blobverwerking.

Verwante onderwerpen die in dat artikel aan de orde komen, zijn onder meer:

* Async-functies
* Meerdere exemplaren
* Sierlijke shutdown
* WebJobs SDK-kenmerken gebruiken in de hoofdtekst van een functie
* Stel de sdk-verbindingstekenreeksen in code in.
* Waarden instellen voor WebJobs SDK-constructorparameters in code
* Configureer **MaxDequeueCount** voor het verwerken van gifblobs.
* Een functie handmatig activeren
* Logboeken schrijven

## <a name="next-steps"></a>Volgende stappen
In dit artikel worden codevoorbeelden weergegeven waarin wordt weergegeven hoe u veelvoorkomende scenario's voor het werken met Azure-blobs verwerken. Zie [Azure WebJobs-documentatiebronnen](https://go.microsoft.com/fwlink/?linkid=390226)voor meer informatie over het gebruik van Azure WebJobs en de WebJobs SDK.

