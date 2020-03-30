---
title: Overzicht van Azure-paginablobs | Microsoft Documenten
description: Een overzicht van Azure-paginablobs en hun voordelen, inclusief use cases met voorbeeldscripts.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/13/2019
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.openlocfilehash: 060e1d01e5f078bad9852ae35d0af9142192a7b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68985620"
---
# <a name="overview-of-azure-page-blobs"></a>Overzicht van Azure-paginablobs

Azure Storage biedt drie soorten blob-opslag: Blobs blokkeren, blobs toevoegen en paginablobs. Blokblobs zijn samengesteld uit blokken en zijn ideaal voor het opslaan van tekst of binaire bestanden, en voor het efficiënt uploaden van grote bestanden. Het toevoegen van blobs bestaat ook uit blokken, maar ze zijn geoptimaliseerd voor het toevoegen van bewerkingen, waardoor ze ideaal zijn voor het registreren van scenario's. Paginablobs bestaan uit pagina's van 512-byte pagina's tot 8 TB in totale grootte en zijn ontworpen voor frequente willekeurige lees- en schrijfbewerkingen. Paginablobs vormen de basis van Azure IaaS-schijven. Dit artikel richt zich op het uitleggen van de functies en voordelen van paginablobs.

Paginablobs zijn een verzameling van 512-byte pagina's, die de mogelijkheid bieden om willekeurige bereiken van bytes te lezen/schrijven. Daarom zijn paginablobs ideaal voor het opslaan van indexgebaseerde en schaarse gegevensstructuren zoals OS en gegevensschijven voor virtuele machines en databases. Azure SQL DB gebruikt bijvoorbeeld paginablobs als onderliggende permanente opslag voor de databases. Bovendien worden paginablobs ook vaak gebruikt voor bestanden met Range-Based updates.  

De belangrijkste functies van Azure-paginablobs zijn de REST-interface, de duurzaamheid van de onderliggende opslag en de naadloze migratiemogelijkheden naar Azure. Deze functies worden in het volgende gedeelte nader besproken. Bovendien worden Azure-paginablobs momenteel ondersteund op twee soorten opslag: Premium-opslag en standaardopslag. Premium Storage is speciaal ontworpen voor workloads die consistente hoge prestaties en lage latentie vereisen, waardoor premium paginablobs ideaal zijn voor krachtige opslagscenario's. Standaardopslagaccounts zijn kosteneffectiever voor het uitvoeren van latentie-ongevoelige workloads.

## <a name="sample-use-cases"></a>Voorbeeldgebruiksvoorbeelden

Laten we een paar use cases bespreken voor paginablobs, te beginnen met Azure IaaS-schijven. Azure-paginablobs vormen de ruggengraat van het virtuele schijven-platform voor Azure IaaS. Zowel Azure OS als dataschijven worden geïmplementeerd als virtuele schijven waar gegevens blijvend worden gehandhaafd in het Azure Storage-platform en vervolgens worden geleverd aan de virtuele machines voor maximale prestaties. Azure Disks worden in Hyper-V [VHD-indeling](https://technet.microsoft.com/library/dd979539.aspx) gehandhaafd en opgeslagen als [een paginablob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) in Azure Storage. Naast het gebruik van virtuele schijven voor Azure IaaS VM's, kunnen paginablobs ook PaaS- en DBaaS-scenario's inschakelen, zoals Azure SQL DB-service, die momenteel paginablobs gebruikt voor het opslaan van SQL-gegevens, waardoor snelle willekeurige leesbewerkingen voor de database mogelijk zijn. Een ander voorbeeld zou zijn als u een PaaS-service voor gedeelde media toegang voor collaboratieve videobewerking toepassingen, pagina blobs maken snelle toegang tot willekeurige locaties in de media. Het maakt het ook mogelijk om dezelfde media snel en efficiënt te bewerken en samen te voegen door meerdere gebruikers. 

Microsoft-services van eerste partij, zoals Azure Site Recovery, Azure Backup, evenals veel externe ontwikkelaars hebben toonaangevende innovaties geïmplementeerd met behulp van de REST-interface van de paginablob. Hieronder volgen enkele van de unieke scenario's die op Azure zijn geïmplementeerd: 

* Toepassingsgericht incrementeel momentopnamebeheer: toepassingen kunnen gebruikmaken van paginablobmomentopnamen en REST-API's voor het opslaan van de toepassingscontrolepunten zonder dat er kostbare dubbel gegevens nodig zijn. Azure Storage ondersteunt lokale momentopnamen voor paginablobs, waarvoor niet de hele blob hoeft te worden gekopieerd. Deze openbare snapshot API's maken ook toegang tot en kopiëren van delta's tussen snapshots mogelijk.
* Live migratie van toepassing en gegevens van on-premises naar de cloud: Kopieer de on-premises gegevens en gebruik REST API's om rechtstreeks naar een Azure-paginablob te schrijven terwijl de on-premises VM blijft draaien. Zodra het doel is ingehaald, u snel failover naar Azure VM met behulp van die gegevens. Op deze manier u uw VM's en virtuele schijven migreren van on-premises naar de cloud met minimale downtime, omdat de gegevensmigratie op de achtergrond plaatsvindt terwijl u de VM blijft gebruiken en de downtime die nodig is voor failover kort is (in minuten).
* [SAS-gebaseerde](../common/storage-sas-overview.md) gedeelde toegang, waarmee scenario's zoals meerdere lezers en single-writer met ondersteuning voor gelijktijdigheid controle mogelijk.

## <a name="page-blob-features"></a>Functies voor pagina-blobs

### <a name="rest-api"></a>REST API

Raadpleeg het volgende document om aan de slag te gaan met [het ontwikkelen met behulp van paginablobs.](storage-dotnet-how-to-use-blobs.md) Kijk bijvoorbeeld hoe u paginablobs openen met Opslagclientbibliotheek voor .NET. 

In het volgende diagram worden de algemene relaties tussen account-, containers- en paginablobs beschreven.

![Schermafbeelding van relaties tussen account-, containers- en paginablobs](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Een lege paginablob van een opgegeven grootte maken

Als u een paginablob wilt maken, maken we eerst een **CloudBlobClient-object,** waarbij de basis-URI voor toegang tot de blob-opslag voor uw opslagaccount *(pbaccount* in figuur 1) samen met het object **StorageCredentialsAccountAndKey,** zoals wordt weergegeven in het volgende voorbeeld. In het voorbeeld wordt vervolgens een verwijzing naar een **CloudBlobContainer-object** gemaakt en vervolgens de container *(testvhds)* gemaakt als deze nog niet bestaat. Maak vervolgens met het object **CloudBlobContainer** een verwijzing naar een **CloudPageBlob-object** door de paginablobnaam (os4.vhd) op te geven die u wilt openen. Als u de paginablob wilt maken, roept u [CloudPageBlob aan.Create](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create), waarbij de maximale grootte wordt doorgemaakt voor de blob die kan worden gemaakt. De *blobSize* moet een veelvoud van 512 bytes zijn.

```csharp
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

#### <a name="resizing-a-page-blob"></a>Het formaat van een paginablob wijzigen

Als u het formaat van een paginablob na het maken wilt wijzigen, gebruikt u de methode [Formaat wijzigen.](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize) De gevraagde grootte moet een veelvoud van 512 bytes zijn.

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

#### <a name="writing-pages-to-a-page-blob"></a>Pagina's schrijven naar een paginablob

Als u pagina's wilt schrijven, gebruikt u de methode [CloudPageBlob.WritePages.](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages)  Hiermee u een sequentiële set pagina's tot 4GB's schrijven. De offset die wordt geschreven, moet beginnen op een grens van 512 byte (startingOffset % 512 == 0) en eindigen op een grens van 512 - 1.  In het volgende codevoorbeeld ziet u hoe **u WritePages** voor een blob aanroept:

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

Zodra een schrijfaanvraag voor een sequentiële reeks pagina's slaagt in de blob-service en wordt gerepliceerd voor duurzaamheid en tolerantie, heeft het schrijven zich ertoe verbonden en wordt het succes teruggegeven aan de client.  

Het onderstaande diagram toont 2 afzonderlijke schrijfbewerkingen:

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Een schrijfbewerking die begint bij verschuiving 0 van lengte 1024 bytes 
2.  Een schrijfbewerking die begint bij offset 4096 van lengte 1024 

#### <a name="reading-pages-from-a-page-blob"></a>Pagina's lezen vanaf een paginablob

Als u pagina's wilt lezen, gebruikt u de methode [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) om een bereik van bytes van de paginablob te lezen. Hiermee u de volledige blob of het volledige bereik van bytes downloaden vanaf elke verschuiving in de blob. Bij het lezen hoeft de offset niet te beginnen op een veelvoud van 512. Bij het lezen van bytes vanaf een NUL-pagina retourneert de service nul bytes.

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

De volgende afbeelding toont een leesbewerking met een offset van 256 en een bereikgrootte van 4352. Geretourneerde gegevens worden oranje gemarkeerd. Nullen worden geretourneerd voor NUL-pagina's.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Als u een dunbevolkte blob hebt, u de geldige paginaregio's downloaden om te voorkomen dat u betaalt voor het overschrijden van nul bytes en de downloadlatentie te verminderen.  Als u wilt bepalen welke pagina's worden ondersteund door gegevens, gebruikt u [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges). U vervolgens de geretourneerde bereiken opsommen en de gegevens in elk bereik downloaden. 

```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the range size
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and
    // place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // Then use the buffer for the page range just read
}
```

#### <a name="leasing-a-page-blob"></a>Een paginablob leasen

Met de bewerking Blob lease wordt een vergrendeling op een blob voor schrijf- en verwijderingsbewerkingen in- en uitgevoerd. Deze bewerking is handig in scenario's waarin een paginablob wordt geopend vanuit meerdere clients om ervoor te zorgen dat slechts één client tegelijk naar de blob kan schrijven. Azure Disks maakt bijvoorbeeld gebruik van dit leasemechanisme om ervoor te zorgen dat de schijf slechts door één vm wordt beheerd. De vergrendelingsduur kan 15 tot 60 seconden zijn, of kan oneindig zijn. Zie de documentatie [hier](/rest/api/storageservices/lease-blob) voor meer details.

Naast de rijke REST API's bieden paginablobs ook gedeelde toegang, duurzaamheid en verbeterde beveiliging. We zullen deze voordelen in de volgende paragrafen nader behandelen. 

### <a name="concurrent-access"></a>Gelijktijdige toegang

De pagina blobs REST API en het leasemechanisme kunnen toepassingen toegang krijgen tot de pagina blob van meerdere clients. Stel dat u een gedistribueerde cloudservice moet bouwen die opslagobjecten met meerdere gebruikers deelt. Het kan een webapplicatie zijn die een grote verzameling afbeeldingen aan verschillende gebruikers bedient. Een optie voor de implementatie van dit is het gebruik van een VM met bijgevoegde schijven. Nadelen hiervan zijn onder andere de beperking dat een schijf alleen aan één VM kan worden gekoppeld, waardoor de schaalbaarheid, flexibiliteit en toenemende risico's worden beperkt. Als er een probleem is met de VM of de service die op de VM wordt uitgevoerd, is de afbeelding vanwege de lease niet toegankelijk totdat de lease verloopt of wordt verbroken; en (ii) Extra kosten van het hebben van een IaaS VM. 

Een alternatieve optie is om de paginablobs rechtstreeks via Azure Storage REST API's te gebruiken. Deze optie elimineert de noodzaak van dure IaaS VM's, biedt volledige flexibiliteit van directe toegang van meerdere clients, vereenvoudigt het klassieke implementatiemodel door de noodzaak om schijven te bevestigen/losmaken te elimineren en elimineert het risico op problemen op de VM. En, het biedt hetzelfde niveau van prestaties voor willekeurige lees / schrijf bewerkingen als een schijf

### <a name="durability-and-high-availability"></a>Duurzaamheid en hoge beschikbaarheid

Zowel standaard- als premium opslag zijn duurzame opslag waar de blobgegevens van de pagina altijd worden gerepliceerd om duurzaamheid en hoge beschikbaarheid te garanderen. Zie deze [documentatie](../common/storage-redundancy.md)voor meer informatie over azure storage redundantie. Azure heeft consequent enterprise-grade duurzaamheid geleverd voor IaaS-schijven en paginablobs, met een toonaangevende nul procent [annualized failure rate](https://en.wikipedia.org/wiki/Annualized_failure_rate).

### <a name="seamless-migration-to-azure"></a>Naadloze migratie naar Azure

Voor de klanten en ontwikkelaars die geïnteresseerd zijn in het implementeren van hun eigen aangepaste back-upoplossing, biedt Azure ook incrementele momentopnamen die alleen de delta's bevatten. Deze functie vermijdt de kosten van de eerste volledige kopie, die sterk verlaagt de back-up kosten. Samen met de mogelijkheid om differentiële gegevens efficiënt te lezen en te kopiëren, is dit een andere krachtige mogelijkheid die nog meer innovaties van ontwikkelaars mogelijk maakt, wat leidt tot een best-in-class back-up en disaster recovery (DR) ervaring op Azure. U uw eigen back-up- of DR-oplossing voor uw VM's op Azure instellen met [Blob Snapshot,](/rest/api/storageservices/snapshot-blob) samen met de API [Paginabereiken ophalen](/rest/api/storageservices/get-page-ranges) en de [Incrementele Api voor kopiëren](/rest/api/storageservices/incremental-copy-blob) blob, die u gebruiken voor het eenvoudig kopiëren van de incrementele gegevens voor DR. 

Bovendien hebben veel bedrijven kritieke workloads die al in on-premises datacenters worden uitgevoerd. Voor het migreren van de werkbelasting naar de cloud, zou een van de belangrijkste zorgen de hoeveelheid downtime die nodig is voor het kopiëren van de gegevens, en het risico van onvoorziene problemen na de omschakeling. In veel gevallen kan de downtime een showstopper zijn voor migratie naar de cloud. Met de REST API voor paginablobs lost Azure dit probleem op door cloudmigratie in te schakelen met minimale verstoring van kritieke workloads. 

Voor voorbeelden over het maken van een momentopname en het herstellen van een paginablob uit een momentopname, raadpleegt u het [instellen van een back-upproces met incrementele momentopnamen.](../../virtual-machines/windows/incremental-snapshots.md)
