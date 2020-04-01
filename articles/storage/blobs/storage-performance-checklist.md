---
title: Checklist voor prestaties en schaalbaarheid voor Blob-opslag - Azure Storage
description: Een checklist van beproefde praktijken voor gebruik met Blob-opslag bij het ontwikkelen van krachtige toepassingen.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b94725d4d3eb9fd6f13a39d00486b4ab085b9ef9
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473932"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Checklist voor prestaties en schaalbaarheid voor Blob-opslag

Microsoft heeft een aantal beproefde praktijken ontwikkeld voor het ontwikkelen van krachtige toepassingen met Blob-opslag. Deze checklist identificeert belangrijke praktijken die ontwikkelaars kunnen volgen om de prestaties te optimaliseren. Houd deze praktijken in gedachten tijdens het ontwerpen van uw toepassing en gedurende het hele proces.

Azure Storage heeft schaalbaarheids- en prestatiedoelstellingen voor capaciteit, transactiesnelheid en bandbreedte. Zie [Schaalbaarheids- en prestatiedoelen voor standaardopslagaccounts en](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) [schaalbaarheids- en prestatiedoelen voor Blob-opslag](scalability-targets.md)voor meer informatie over azure-opslagdoelen.

## <a name="checklist"></a>Controlelijst

In dit artikel worden bewezen praktijken voor prestaties georganiseerd in een checklist die u volgen tijdens het ontwikkelen van uw Blob-opslagtoepassing.

| Gereed | Categorie | Ontwerpoverweging |
| --- | --- | --- |
| &nbsp; |Schaalbaarheidsdoelen |[u uw toepassing zo ontwerpen dat deze niet meer gebruikt dan het maximum aantal opslagaccounts?](#maximum-number-of-storage-accounts) |
| &nbsp; |Schaalbaarheidsdoelen |[Vermijdt u naderende capaciteits- en transactielimieten?](#capacity-and-transaction-targets) |
| &nbsp; |Schaalbaarheidsdoelen |[Hebben een groot aantal clients gelijktijdig toegang tot één blob?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |Schaalbaarheidsdoelen |[Blijft uw toepassing binnen de schaalbaarheidsdoelstellingen voor één blob?](#bandwidth-and-operations-per-blob) |
| &nbsp; |Partitionering |[Is uw naamgevingsconventie ontworpen om een betere load-balancing mogelijk te maken?](#partitioning) |
| &nbsp; |Netwerken |[Hebben client-side apparaten voldoende hoge bandbreedte en lage latentie om de benodigde prestaties te bereiken?](#throughput) |
| &nbsp; |Netwerken |[Hebben client-side apparaten een netwerkkoppeling van hoge kwaliteit?](#link-quality) |
| &nbsp; |Netwerken |[Is de clienttoepassing in dezelfde regio als het opslagaccount?](#location) |
| &nbsp; |Directe toegang tot de client |[Gebruikt u shared access signatures (SAS) en cross-origin resource sharing (CORS) om directe toegang tot Azure Storage mogelijk te maken?](#sas-and-cors) |
| &nbsp; |Caching |[Worden gegevens in cache van uw toepassing in cache opgenomen en zelden gewijzigd?](#reading-data) |
| &nbsp; |Caching |[Is uw toepassing batching updates door ze caching op de client en vervolgens uploaden ze in grotere sets?](#uploading-data-in-batches) |
| &nbsp; |.NET-configuratie |[Gebruikt u .NET Core 2.1 of hoger voor optimale prestaties?](#use-net-core) |
| &nbsp; |.NET-configuratie |[Hebt u uw client geconfigureerd om voldoende gelijktijdige verbindingen te gebruiken?](#increase-default-connection-limit) |
| &nbsp; |.NET-configuratie |[Heeft u voor .NET-toepassingen .NET geconfigureerd om een voldoende aantal threads te gebruiken?](#increase-minimum-number-of-threads) |
| &nbsp; |Parallellisme |[Hebt u ervoor gezorgd dat parallellisme op de juiste manier wordt begrensd, zodat u de mogelijkheden van uw klant niet overbelast of de schaalbaarheidsdoelstellingen benadert?](#unbounded-parallelism) |
| &nbsp; |Hulpprogramma's |[Gebruikt u de nieuwste versies van door Microsoft geleverde clientbibliotheken en -hulpprogramma's?](#client-libraries-and-tools) |
| &nbsp; |Nieuwe pogingen |[Gebruikt u een herprobeerbeleid met een exponentiële back-off voor beperking van fouten en time-outs?](#timeout-and-server-busy-errors) |
| &nbsp; |Nieuwe pogingen |[Is uw toepassing het vermijden van nieuwe tries voor niet-herprobeerbare fouten?](#non-retryable-errors) |
| &nbsp; |Blobs kopiëren |[Kopieert u blobs op de meest efficiënte manier?](#blob-copy-apis) |
| &nbsp; |Blobs kopiëren |[Gebruikt u de nieuwste versie van AzCopy voor bulkcopybewerkingen?](#use-azcopy) |
| &nbsp; |Blobs kopiëren |[Gebruikt u de Azure Data Box-familie voor het importeren van grote hoeveelheden gegevens?](#use-azure-data-box) |
| &nbsp; |Inhoudsdistributie |[Gebruikt u een CDN voor contentdistributie?](#content-distribution) |
| &nbsp; |Metagegevens gebruiken |[Slaat u veelgebruikte metadata over blobs op in hun metadata?](#use-metadata) |
| &nbsp; |Snel uploaden |[Wanneer u probeert om een blob snel te uploaden, upload je blokken parallel?](#upload-one-large-blob-quickly) |
| &nbsp; |Snel uploaden |[Wanneer u probeert om veel blobs snel te uploaden, upload je blobs parallel?](#upload-many-blobs-quickly) |
| &nbsp; |Blob-type |[Gebruikt u paginablobs of blokkeert u blobs indien nodig?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>Schaalbaarheidsdoelen

Als uw toepassing een van de schaalbaarheidsdoelen benadert of overschrijdt, kan deze worden geconfronteerd met verhoogde transactielatencies of beperking. Wanneer Azure Storage uw toepassing beperkt, begint de service met het retourneren van 503 (Server bezet) of 500 (Time-out van bewerking). Het vermijden van deze fouten door binnen de grenzen van de schaalbaarheidsdoelstellingen te blijven, is een belangrijk onderdeel van het verbeteren van de prestaties van uw toepassing.

Zie [Schaalbaarheids- en prestatiedoelen](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage)voor Azure Storage voor meer informatie over schaalbaarheidsdoelen voor de wachtrijservice.

### <a name="maximum-number-of-storage-accounts"></a>Maximum aantal opslagaccounts

Als u het maximum aantal opslagaccounts nadert dat is toegestaan voor een bepaalde combinatie van abonnement/regio, evalueert u uw scenario en bepaalt u of een van de volgende voorwaarden van toepassing is:

- Gebruikt u opslagaccounts om onbeheerde schijven op te slaan en deze schijven toe te voegen aan uw virtuele machines (VM's)? Voor dit scenario raadt Microsoft aan beheerde schijven te gebruiken. Beheerde schijven schalen automatisch en zonder de noodzaak om afzonderlijke opslagaccounts te maken en te beheren. Zie [Inleiding tot door Azure beheerde schijven voor](../../virtual-machines/windows/managed-disks-overview.md) meer informatie
- Gebruikt u één opslagaccount per klant, met het oog op gegevensisolatie? Voor dit scenario raadt Microsoft aan om voor elke klant een blobcontainer te gebruiken in plaats van een volledig opslagaccount. Met Azure Storage u nu RBAC-rollen (role-based access control) per container toewijzen. Zie [Toegang verlenen tot Azure blob- en wachtrijgegevens met RBAC in de Azure-portal](../common/storage-auth-aad-rbac-portal.md)voor meer informatie.
- Gebruikt u meerdere opslagaccounts om de invallen, de uitgang, de I/O-bewerkingen per seconde (IOPS) of de capaciteit te vergroten? In dit scenario raadt Microsoft u aan gebruik te maken van hogere limieten voor opslagaccounts om het aantal opslagaccounts te verminderen dat nodig is voor uw werkbelasting, indien mogelijk. Neem contact op met [Azure Support](https://azure.microsoft.com/support/options/) om hogere limieten voor uw opslagaccount aan te vragen. Zie [Grotere opslagaccounts op](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)grotere schaal aankondigen voor meer informatie .

### <a name="capacity-and-transaction-targets"></a>Capaciteits- en transactiedoelstellingen

Als uw toepassing de schaalbaarheidsdoelen voor één opslagaccount nadert, u overwegen een van de volgende benaderingen te kiezen:  

- Als uw toepassing het transactiedoel bereikt, u overwegen blokblobopslagaccounts te gebruiken, die zijn geoptimaliseerd voor hoge transactiesnelheden en lage en consistente latentie. Zie [Overzicht van Azure-opslagaccount](../common/storage-account-overview.md) voor meer informatie.
- Heroverweeg de werkbelasting die ervoor zorgt dat uw toepassing de schaalbaarheidsdoelstelling benadert of overschrijdt. u het anders ontwerpen om minder bandbreedte of capaciteit te gebruiken, of minder transacties?
- Als uw toepassing een van de schaalbaarheidsdoelen moet overschrijden, maakt u meerdere opslagaccounts en partitiet u uw toepassingsgegevens over die meerdere opslagaccounts. Als u dit patroon gebruikt, moet u uw toepassing zo ontwerpen dat u in de toekomst meer opslagaccounts toevoegen voor taakverdeling. Opslagaccounts zelf hebben geen andere kosten dan uw gebruik in termen van opgeslagen gegevens, transacties of overgedragen gegevens.
- Als uw toepassing de bandbreedtedoelen nadert, u overwegen gegevens aan de clientzijde te comprimeren om de bandbreedte te verminderen die nodig is om de gegevens naar Azure Storage te verzenden.
    Hoewel het comprimeren van gegevens bandbreedte kan besparen en de netwerkprestaties kan verbeteren, kan dit ook negatieve effecten hebben op de prestaties. Evalueer de prestatie-impact van de aanvullende verwerkingsvereisten voor gegevenscompressie en decompressie aan de clientzijde. Houd er rekening mee dat het opslaan van gecomprimeerde gegevens het oplossen van problemen moeilijker kan maken, omdat het mogelijk moeilijker is om de gegevens te bekijken met behulp van standaardhulpprogramma's.
- Als uw toepassing de schaalbaarheidsdoelen nadert, zorg er dan voor dat u een exponentiële back-off gebruikt voor nieuwe pogingen. Het is het beste om te proberen te voorkomen dat het bereiken van de schaalbaarheidsdoelstellingen door de uitvoering van de aanbevelingen beschreven in dit artikel. Echter, met behulp van een exponentiële backoff voor nieuwe pogingen zal voorkomen dat uw toepassing van snel opnieuw proberen, die kan maken throttling erger. Zie voor meer informatie de sectie met de titel [Timeout and Server Busy errors](#timeout-and-server-busy-errors).

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>Meerdere clients die gelijktijdig toegang hebben tot één blob

Als u een groot aantal clients gelijktijdig toegang hebt tot één blob, moet u rekening houden met zowel de schaalbaarheidsdoelen per blob als per opslagaccount. Het exacte aantal clients dat toegang heeft tot één blob, is afhankelijk van factoren zoals het aantal clients dat de blob tegelijkertijd aanvraagt, de grootte van de blob en netwerkvoorwaarden.

Als de blob kan worden verspreid via een CDN, zoals afbeeldingen of video's die vanaf een website worden weergegeven, u een CDN gebruiken. Zie voor meer informatie de sectie met de titel [Inhoudsdistributie](#content-distribution).

In andere scenario's, zoals wetenschappelijke simulaties waarbij de gegevens vertrouwelijk zijn, hebt u twee opties. De eerste is om de toegang van uw workload zodanig te spreiden dat de blob gedurende een bepaalde periode wordt geopend vs tegelijkertijd toegankelijk te zijn. U de blob ook tijdelijk kopiëren naar meerdere opslagaccounts om het totale IOPS per blob en voor opslagaccounts te verhogen. De resultaten variëren afhankelijk van het gedrag van uw toepassing, dus zorg ervoor dat u gelijktijdigheidspatronen test tijdens het ontwerp.

### <a name="bandwidth-and-operations-per-blob"></a>Bandbreedte en bewerkingen per blob

Eén blob ondersteunt maximaal 500 aanvragen per seconde. Als u meerdere clients hebt die dezelfde blob moeten lezen en u deze limiet mogelijk overschrijdt, u overwegen een blokblobopslagaccount te gebruiken. Een blokblobopslagaccount biedt een hoger aanvraagpercentage of I/O-bewerkingen per seconde (IOPS).

U ook een CDN (Content Delivery Network) zoals Azure CDN gebruiken om bewerkingen op de blob te distribueren. Zie [Azure CDN-overzicht](../../cdn/cdn-overview.md)voor meer informatie over Azure CDN.  

## <a name="partitioning"></a>Partitionering

Inzicht in hoe Azure Storage-partities van uw blobgegevens nuttig zijn om de prestaties te verbeteren. Azure Storage kan gegevens sneller in één partitie weergeven dan gegevens die meerdere partities omvatten. Door uw blobs de juiste naam te bieden, u de efficiëntie van leesverzoeken verbeteren.

Blob-opslag maakt gebruik van een op bereik gebaseerd partitieschema voor schalen en taakverdeling. Elke blob heeft een partitiesleutel die bestaat uit de volledige blobnaam (account+container+blob). De partitiesleutel wordt gebruikt om blobgegevens in bereiken te verdelen. De bereiken worden vervolgens load-balanced over Blob-opslag.

Op bereik gebaseerde partitionering betekent dat naamgevingsconventies die lexicale volgorde gebruiken (bijvoorbeeld *mypayroll,* *myperformance,* *myemployees,* etc.) of timestamps *(log20160101*, *log20160102*, *log20160102,* enz.) eerder zullen resulteren in de co-locatie van de partities op dezelfde partitieserver. , totdat een verhoogde belasting vereist dat ze worden opgesplitst in kleinere bereiken. Het co-lokaliseren van blobs op dezelfde partitieserver verbetert de prestaties, dus een belangrijk onderdeel van prestatieverbetering bestaat uit het benoemen van blobs op een manier die ze het meest effectief organiseert.

Alle blobs in een container kunnen bijvoorbeeld door één server worden bediend totdat de belasting op deze blobs verdere herbalancering van de partitiebereiken vereist. Op dezelfde manier kan een groep licht geladen accounts met hun namen in lexicale volgorde door één server worden bediend totdat de belasting op een of al deze accounts vereist dat ze worden gesplitst over meerdere partitieservers.

Elke load-balancing operatie kan invloed hebben op de latentie van opslagaanroepen tijdens de bewerking. De mogelijkheid van de service om een plotselinge uitbarsting van verkeer naar een partitie te verwerken, wordt beperkt door de schaalbaarheid van een enkele partitieserver totdat de taakverdelingsbewerking wordt gestart en het bereik van de partitiesleutel opnieuw in evenwicht brengt.

U een aantal aanbevolen procedures volgen om de frequentie van dergelijke bewerkingen te verminderen.  

- Gebruik indien mogelijk blob- of blokformaten groter dan 4 MiB voor standaardopslagaccounts en meer dan 256 KiB voor premium opslagaccounts. Grotere blob- of blokgroottes activeren automatisch blobs met hoge doorvoer. Blobs met hoge doorvoer bieden krachtige inname die niet wordt beïnvloed door partitienaamgeving.
- Controleer de naamgevingsconventie die u gebruikt voor accounts, containers, blobs, tabellen en wachtrijen. Overweeg account-, container- of blobnamen vooraf op te stellen met een hash van drie cijfers met behulp van een hashing-functie die het beste bij uw behoeften past.
- Als u uw gegevens organiseert met behulp van tijdstempels of numerieke id's, moet u ervoor zorgen dat u geen append-only (of prepend-only) verkeerspatroon gebruikt. Deze patronen zijn niet geschikt voor een op bereik gebaseerd partitioneringssysteem. Deze patronen kunnen ertoe leiden dat al het verkeer naar één partitie gaat en het systeem beperkt van effectief balanceren.

    Als u bijvoorbeeld dagelijkse bewerkingen hebt die een blob met een tijdstempel gebruiken, zoals *yyyymmdd,* wordt al het verkeer voor die dagelijkse bewerking naar één blob geleid, die wordt bediend door één partitieserver. Overweeg of de limieten per blob en limieten per partitie aan uw behoeften voldoen en overweeg deze bewerking indien nodig in meerdere blobs op te splitsen. Als u tijdreeksgegevens opslaat in uw tabellen, kan al het verkeer worden doorverwezen naar het laatste deel van de sleutelnaamruimte. Als u numerieke id's gebruikt, moet u de ID vooraf fixeren met een hash van drie cijfers. Als u tijdstempels gebruikt, moet u de tijdstempel met de secondenwaarde voorzetten, bijvoorbeeld *ssyyyymmdd.* Als uw toepassing routinematig aanbiedings- en querybewerkingen uitvoert, kiest u een hashing-functie die het aantal query's beperkt. In sommige gevallen kan een willekeurig voorvoegsel voldoende zijn.
  
- Zie [Azure Storage: Een zeer beschikbare cloudopslagservice met sterke consistentie voor](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf)meer informatie over het partitieschema dat wordt gebruikt in Azure Storage.

## <a name="networking"></a>Netwerken

De fysieke netwerkbeperkingen van de toepassing kunnen een aanzienlijke invloed hebben op de prestaties. In de volgende secties worden enkele beperkingen beschreven die gebruikers kunnen tegenkomen.  

### <a name="client-network-capability"></a>Clientnetwerkmogelijkheid

Bandbreedte en de kwaliteit van de netwerkkoppeling spelen een belangrijke rol in de prestaties van toepassingen, zoals beschreven in de volgende secties.

#### <a name="throughput"></a>Doorvoer

Voor bandbreedte is het probleem vaak de mogelijkheden van de client. Grotere Azure-exemplaren hebben NIC's met een grotere capaciteit, dus u moet overwegen een grotere instantie of meer VM's te gebruiken als u hogere netwerklimieten van één machine nodig hebt. Als u azure-opslag opent vanuit een on-premises toepassing, geldt dezelfde regel: begrijp de netwerkmogelijkheden van het clientapparaat en de netwerkconnectiviteit met de Azure Storage-locatie en verbeter deze indien nodig of ontwerp uw toepassing om binnen hun mogelijkheden te werken.

#### <a name="link-quality"></a>Koppelingskwaliteit

Zoals bij elk netwerkgebruik, houd er rekening mee dat netwerkomstandigheden die resulteren in fouten en pakketverlies de effectieve doorvoer zullen vertragen.  Het gebruik van WireShark of NetMon kan helpen bij het diagnosticeren van dit probleem.  

### <a name="location"></a>Locatie

In elke gedistribueerde omgeving levert het plaatsen van de client in de buurt van de server de beste prestaties. Voor toegang tot Azure Storage met de laagste latentie bevindt de beste locatie voor uw client zich binnen dezelfde Azure-regio. Als u bijvoorbeeld een Azure-webapp hebt die Azure Storage gebruikt, zoekt u deze vervolgens binnen één regio, zoals US West of Asia Southeast. Co-locating resources vermindert de latentie en de kosten, omdat bandbreedtegebruik binnen één regio gratis is.  

Als clienttoepassingen toegang krijgen tot Azure Storage, maar niet worden gehost in Azure, zoals apps voor mobiele apparaten of on-premises bedrijfsservices, kan het lokaliseren van het opslagaccount in een regio in de buurt van die clients de latentie verminderen. Als uw klanten breed verdeeld zijn (bijvoorbeeld sommige in Noord-Amerika en sommige in Europa), u overwegen één opslagaccount per regio te gebruiken. Deze aanpak is gemakkelijker te implementeren als de gegevens die de toepassing opslaat specifiek zijn voor individuele gebruikers en niet vereist dat gegevens tussen opslagaccounts worden gerepliceerd.

Gebruik een netwerk voor het leveren van inhoud, zoals Azure CDN, voor een brede distributie van blob-inhoud. Zie Azure CDN voor meer informatie over Azure [CDN.](../../cdn/cdn-overview.md)  

## <a name="sas-and-cors"></a>SAS en CORS

Stel dat u code zoals JavaScript die wordt uitgevoerd in de webbrowser van een gebruiker of in een mobiele telefoon-app moet autoriseren om toegang te krijgen tot gegevens in Azure Storage. Een benadering is het bouwen van een service-applicatie die fungeert als een proxy. Het apparaat van de gebruiker verifieert met de service, die op zijn beurt de toegang tot Azure Storage-bronnen autoriseert. Op deze manier u voorkomen dat uw opslagaccountsleutels op onveilige apparaten worden blootgesteld. Deze benadering plaatst echter een aanzienlijke overhead voor de servicetoepassing, omdat alle gegevens die tussen het apparaat van de gebruiker en Azure Storage worden overgedragen, door de servicetoepassing moeten worden doorgegeven.

U voorkomen dat u een servicetoepassing gebruikt als proxy voor Azure Storage met behulp van sas (Shared Access Signatures). Met SAS u het apparaat van uw gebruiker in staat stellen om rechtstreeks aanvragen in Azure Storage in te dienen met behulp van een beperkt toegangstoken. Als een gebruiker bijvoorbeeld een foto naar uw toepassing wil uploaden, kan uw servicetoepassing een SAS genereren en naar het apparaat van de gebruiker verzenden. Het SAS-token kan toestemming verlenen om voor een bepaald tijdsinterval naar een Azure Storage-bron te schrijven, waarna het SAS-token verloopt. Zie Beperkte toegang tot [Azure Storage-bronnen verlenen met behulp van gedeelde toegangshandtekeningen (SAS)](../common/storage-sas-overview.md)voor meer informatie over SAS.  

Doorgaans staat een webbrowser JavaScript niet toe op een pagina die wordt gehost door een website op een domein om bepaalde bewerkingen, zoals schrijfbewerkingen, naar een ander domein uit te voeren. Dit beleid, dat bekend staat als het beleid van dezelfde oorsprong, voorkomt dat een kwaadaardig script op de ene pagina toegang krijgt tot gegevens op een andere webpagina. Het beleid van dezelfde oorsprong kan echter een beperking zijn bij het bouwen van een oplossing in de cloud. Cross-origin resource sharing (CORS) is een browserfunctie waarmee het doeldomein kan communiceren met de browser die het vertrouwt op aanvragen die afkomstig zijn uit het brondomein.

Stel dat een webtoepassing die wordt uitgevoerd in Azure een aanvraag voor een bron naar een Azure Storage-account maakt. De webtoepassing is het brondomein en het opslagaccount is het doeldomein. U CORS configureren voor een van de Azure Storage-services om te communiceren met de webbrowser dat aanvragen van het brondomein worden vertrouwd door Azure Storage. Zie [CORS-ondersteuning (Cross-origin resource sharing) voor Azure Storage voor](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services)meer informatie over CORS.  
  
Zowel SAS als CORS kunnen u helpen onnodige belasting op uw webapplicatie te voorkomen.  

## <a name="caching"></a>Caching

Caching speelt een belangrijke rol in de prestaties. In de volgende secties worden de aanbevolen procedures voor caching besproken.

### <a name="reading-data"></a>Gegevens lezen

In het algemeen is het lezen van gegevens eenmaal de voorkeur boven het twee keer lezen. Overweeg het voorbeeld van een webtoepassing waarmee een 50 MiB-blob uit de Azure-opslag is opgehaald om als inhoud voor een gebruiker te dienen. Idealiter cachet de toepassing de blob lokaal naar de schijf en haalt vervolgens de in de cache opgeslagen versie op voor volgende gebruikersverzoeken.

Een manier om te voorkomen dat het ophalen van een blob als het niet is gewijzigd sinds het in de cache is opgeslagen, is door de GET-bewerking te kwalificeren met een voorwaardelijke koptekst voor wijzigingstijd. Als de laatste gewijzigde tijd is na de tijd dat de blob in de cache is opgeslagen, wordt de blob opgehaald en opnieuw in de cache opgeslagen. Anders wordt de blob in de cache opgehaald voor optimale prestaties.

U ook besluiten om uw toepassing te ontwerpen om ervan uit te gaan dat de blob gedurende een korte periode ongewijzigd blijft nadat u deze hebt opgehaald. In dit geval hoeft de toepassing niet te controleren of de blob tijdens dat interval is gewijzigd.

Configuratiegegevens, opzoekgegevens en andere gegevens die vaak door de toepassing worden gebruikt, zijn goede kandidaten voor caching.  

Zie [Voorwaardelijke kopteksten opgeven voor Blob-servicebewerkingen voor](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)meer informatie over het gebruik van voorwaardelijke kopteksten.  

### <a name="uploading-data-in-batches"></a>Gegevens uploaden in batches

In sommige scenario's u gegevens lokaal samenvoegen en deze vervolgens periodiek uploaden in een batch in plaats van elk stuk gegevens onmiddellijk te uploaden. Stel dat een webtoepassing een logboekbestand met activiteiten bijhoudt. De toepassing kan details van elke activiteit uploaden als het gebeurt met een tabel (waarvoor veel opslagbewerkingen nodig zijn), of het kan activiteitsgegevens opslaan in een lokaal logboekbestand en vervolgens periodiek alle activiteitsdetails uploaden als een afgebakend bestand naar een blob. Als elke logboekvermelding 1 KB groot is, u duizenden vermeldingen in één transactie uploaden. Een enkele transactie ondersteunt het uploaden van een blob van maximaal 64 MiB in grootte. De ontwikkelaar van de toepassing moet ontwerpen voor de mogelijkheid van clientapparaat of uploadfouten. Als de activiteitsgegevens moeten worden gedownload voor een tijdsinterval in plaats van voor één activiteit, wordt het gebruik van Blob-opslag aanbevolen via tabelopslag.

## <a name="net-configuration"></a>.NET-configuratie

Als u het .NET Framework gebruikt, worden in deze sectie een aantal snelle configuratie-instellingen weergegeven die u gebruiken om aanzienlijke prestatieverbeteringen aan te brengen.  Als u andere talen gebruikt, controleert u of vergelijkbare concepten van toepassing zijn in de door u gekozen taal.  

### <a name="use-net-core"></a>Gebruik .NET Core

Ontwikkel uw Azure Storage-toepassingen met .NET Core 2.1 of hoger om te profiteren van prestatieverbeteringen. Het gebruik van .NET Core 3.x wordt aanbevolen wanneer dat mogelijk is.

Zie de volgende blogberichten voor meer informatie over prestatieverbeteringen in .NET Core:

- [Prestatieverbeteringen in .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Prestatieverbeteringen in .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Standaardverbindingslimiet verhogen

In .NET verhoogt de volgende code de standaardverbindingslimiet (die meestal twee is in een clientomgeving of tien in een serveromgeving) tot 100. Doorgaans moet u de waarde instellen op ongeveer het aantal threads dat door uw toepassing wordt gebruikt. Stel de verbindingslimiet in voordat u verbindingen opent.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Zie de documentatie voor andere programmeertalen om te bepalen hoe u de verbindingslimiet instelt.  

Zie de blogpost Web [Services: Concurrent Connections voor](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/)meer informatie.  

### <a name="increase-minimum-number-of-threads"></a>Minimumaantal threads verhogen

Als u synchrone aanroepen samen met asynchrone taken gebruikt, u het aantal threads in de threadgroep verhogen:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Zie de methode [ThreadPool.SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads) voor meer informatie.  

## <a name="unbounded-parallelism"></a>Onbegrensd eerwerk

Hoewel parallellisme geweldig kan zijn voor prestaties, wees voorzichtig met het gebruik van onbegrensd eerwerk, wat betekent dat er geen limiet wordt afgedwongen op het aantal threads of parallelle aanvragen. Zorg ervoor dat u parallelle aanvragen voor het uploaden of downloaden van gegevens beperkt, toegang krijgt tot meerdere partities in hetzelfde opslagaccount of toegang krijgt tot meerdere items in dezelfde partitie. Als parallellisme niet wordt begrensd, kan uw toepassing de mogelijkheden van het clientapparaat of de schaalbaarheidsdoelen van het opslagaccount overschrijden, wat resulteert in langere latencies en beperking.  

## <a name="client-libraries-and-tools"></a>Clientbibliotheken en -hulpprogramma's

Gebruik altijd de nieuwste clientbibliotheken en hulpprogramma's van Microsoft voor de beste prestaties. Azure Storage-clientbibliotheken zijn beschikbaar voor verschillende talen. Azure Storage ondersteunt ook PowerShell en Azure CLI. Microsoft ontwikkelt deze clientbibliotheken en -tools actief met het oog op prestaties, houdt ze up-to-date met de nieuwste serviceversies en zorgt ervoor dat ze veel van de beproefde prestatiepraktijken intern afhandelen. Zie de [naslagdocumentatie azure storage voor](/azure/storage/#reference)meer informatie .

## <a name="handle-service-errors"></a>Servicefouten verwerken

Azure Storage retourneert een fout wanneer de service een aanvraag niet kan verwerken. Inzicht in de fouten die in een bepaald scenario door Azure Storage kunnen worden geretourneerd, is handig voor het optimaliseren van de prestaties.

### <a name="timeout-and-server-busy-errors"></a>Time-out en serverdrukke fouten

Azure Storage kan uw toepassing beperken als de schaalbaarheidslimieten worden beperkt. In sommige gevallen kan Azure Storage een aanvraag mogelijk niet verwerken vanwege een tijdelijke voorwaarde. In beide gevallen kan de service een fout van 503 (Server bezet) of 500 (Time-out) retourneren. Deze fouten kunnen ook optreden als de service gegevenspartities opnieuw in evenwicht brengt om een hogere doorvoer mogelijk te maken. De clienttoepassing moet doorgaans de bewerking opnieuw proberen die een van deze fouten veroorzaakt. Als Azure Storage uw toepassing echter verzwaren omdat deze de schaalbaarheidsdoelen overschrijdt, of zelfs als de service om een andere reden niet in staat is om de aanvraag te dienen, kunnen agressieve pogingen het probleem verergeren. Het gebruik van een exponentieel back-off retry-beleid wordt aanbevolen en de clientbibliotheken worden standaard gebruikt voor dit gedrag. Bijvoorbeeld, uw toepassing kan opnieuw proberen na 2 seconden, dan 4 seconden, dan 10 seconden, dan 30 seconden, en dan volledig opgeven. Op deze manier vermindert uw toepassing de belasting van de service aanzienlijk, in plaats van gedrag te verergeren dat kan leiden tot beperking.  

Verbindingsfouten kunnen onmiddellijk opnieuw worden geprobeerd, omdat ze niet het gevolg zijn van beperking en naar verwachting van voorbijgaande aard zijn.  

### <a name="non-retryable-errors"></a>Niet-opnieuw probeerbare fouten

De clientbibliotheken behandelen nieuwe pogingen met het besef welke fouten opnieuw kunnen worden geprobeerd en welke niet. Als u echter de Azure Storage REST API rechtstreeks aanroept, zijn er enkele fouten die u niet opnieuw moet proberen. Een fout van 400 (Slechte aanvraag) geeft bijvoorbeeld aan dat de clienttoepassing een aanvraag heeft verzonden die niet kon worden verwerkt omdat deze niet in het verwachte formulier stond. Het opnieuw verzenden van deze aanvraag resulteert elke keer hetzelfde antwoord, dus het heeft geen zin om het opnieuw te proberen. Als u de Azure Storage REST API rechtstreeks aanroept, moet u zich bewust zijn van mogelijke fouten en of deze opnieuw moeten worden geprobeerd.

Zie [Status- en foutcodes](/rest/api/storageservices/status-and-error-codes2)voor meer informatie over foutcodes voor Azure Storage.

## <a name="copying-and-moving-blobs"></a>Blobs kopiëren en verplaatsen

Azure Storage biedt een aantal oplossingen voor het kopiëren en verplaatsen van blobs binnen een opslagaccount, tussen opslagaccounts en tussen on-premises systemen en de cloud. In dit gedeelte worden enkele van deze opties beschreven in termen van hun effecten op de prestaties. Zie [Een Azure-oplossing kiezen voor gegevensoverdracht voor](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)informatie over het efficiënt overbrengen van gegevens van of naar Blob-opslag.

### <a name="blob-copy-apis"></a>Blob-kopieer-API's

Als u blobs wilt kopiëren in opslagaccounts, gebruikt u de [bewerking Blokkeren van URL plaatsen.](/rest/api/storageservices/put-block-from-url) Met deze bewerking worden gegevens synchroon van elke URL-bron naar een blokblob gekopieerd. Het `Put Block from URL` gebruik van de bewerking kan de vereiste bandbreedte aanzienlijk verminderen wanneer u gegevens migreert in opslagaccounts. Omdat de kopieerbewerking plaatsvindt aan de servicezijde, hoeft u de gegevens niet te downloaden en opnieuw te uploaden.

Als u gegevens binnen hetzelfde opslagaccount wilt kopiëren, gebruikt u de bewerking [Blob kopiëren.](/rest/api/storageservices/Copy-Blob) Het kopiëren van gegevens binnen hetzelfde opslagaccount wordt doorgaans snel voltooid.  

### <a name="use-azcopy"></a>AzCopy gebruiken

Het AzCopy-opdrachtregelhulpprogramma is een eenvoudige en efficiënte optie voor bulkoverdracht van blobs naar, van en tussen opslagaccounts. AzCopy is geoptimaliseerd voor dit scenario en kan hoge overdrachtssnelheden bereiken. AzCopy versie 10 `Put Block From URL` gebruikt de bewerking om blobgegevens te kopiëren over opslagaccounts. Zie [Gegevens kopiëren of verplaatsen naar Azure Storage met AzCopy v10](/azure/storage/common/storage-use-azcopy-v10)voor meer informatie.  

### <a name="use-azure-data-box"></a>Azure-gegevensvak gebruiken

Als u grote hoeveelheden gegevens wilt importeren in Blob-opslag, u overwegen de Azure Data Box-familie te gebruiken voor offline overdrachten. Door Microsoft geleverde Data Box-apparaten zijn een goede keuze voor het verplaatsen van grote hoeveelheden gegevens naar Azure wanneer u beperkt bent door tijd, beschikbaarheid van het netwerk of kosten. Zie de Azure [DataBox-documentatie](/azure/databox/)voor meer informatie .

## <a name="content-distribution"></a>Inhoudsdistributie

Soms moet een toepassing dezelfde inhoud aan veel gebruikers aanbieden (bijvoorbeeld een productdemovideo die wordt gebruikt op de startpagina van een website), zich in dezelfde of meerdere regio's. Gebruik in dit scenario een CDN (Content Delivery Network), zoals Azure CDN, om blob-inhoud geografisch te distribueren. In tegenstelling tot een Azure Storage-account dat in één regio bestaat en dat geen inhoud met lage latentie kan leveren aan andere regio's, gebruikt Azure CDN servers in meerdere datacenters over de hele wereld. Bovendien kan een CDN doorgaans veel hogere uitgangslimieten ondersteunen dan een enkel opslagaccount.  

Zie Azure CDN voor meer informatie over Azure [CDN.](../../cdn/cdn-overview.md)

## <a name="use-metadata"></a>Metagegevens gebruiken

De Blob-service ondersteunt HEAD-aanvragen, die blob-eigenschappen of metagegevens kunnen bevatten. Als uw toepassing bijvoorbeeld de exif-gegevens (exchangable image format) van een foto nodig heeft, kan deze de foto ophalen en extraheren. Om bandbreedte te besparen en de prestaties te verbeteren, kan uw toepassing de Exif-gegevens opslaan in de metagegevens van de blob wanneer de toepassing de foto uploadt. U vervolgens de Exif-gegevens in metagegevens ophalen met alleen een HEAD-verzoek. Het ophalen van alleen metagegevens en niet de volledige inhoud van de blob bespaart aanzienlijke bandbreedte en vermindert de verwerkingstijd die nodig is om de Exif-gegevens te extraheren. Houd er rekening mee dat 8 KiB metagegevens per blob kunnen worden opgeslagen.  

## <a name="upload-blobs-quickly"></a>Blobs snel uploaden

Als u blobs snel wilt uploaden, bepaalt u eerst of u één blob of veel uploadt. Gebruik de onderstaande richtlijnen om de juiste methode te bepalen die u wilt gebruiken, afhankelijk van uw scenario.  

### <a name="upload-one-large-blob-quickly"></a>Eén grote blob snel uploaden

Als u één grote blob snel wilt uploaden, kan een clienttoepassing de blokken of pagina's parallel uploaden, rekening houdend met de schaalbaarheidsdoelen voor afzonderlijke blobs en het opslagaccount als geheel. De Azure Storage-clientbibliotheken ondersteunen het gelijktijdig uploaden. U bijvoorbeeld de volgende eigenschappen gebruiken om het aantal gelijktijdige aanvragen op te geven dat is toegestaan in .NET of Java. Clientbibliotheken voor andere ondersteunde talen bieden vergelijkbare opties.

- Stel voor .NET de eigenschap [BlobRequestOptions.ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount) in.
- Voor Java/Android belt u de methode [BlobRequestOptions.setConcurrentRequestCount (laatste integer concurrentRequestCount).](/java/api/com.microsoft.azure.storage.blob.blobrequestoptions.setconcurrentrequestcount)

### <a name="upload-many-blobs-quickly"></a>Upload snel veel blobs

Als u veel blobs snel wilt uploaden, uploadt u blobs parallel. Parallel uploaden is sneller dan het uploaden van enkele blobs tegelijk met parallelle blokuploads, omdat het de upload over meerdere partities van de opslagservice verspreidt. AzCopy voert standaard uploads parallel uit en wordt aanbevolen voor dit scenario. Zie [Aan de slag met AzCopy](../common/storage-use-azcopy-v10.md)voor meer informatie.  

## <a name="choose-the-correct-type-of-blob"></a>Het juiste type blob kiezen

Azure Storage ondersteunt blokblobs, toevoegen blobs en paginablobs. Voor een bepaald gebruiksscenario heeft uw keuze van blobtype invloed op de prestaties en schaalbaarheid van uw oplossing.

Blokblobs zijn geschikt wanneer u grote hoeveelheden gegevens efficiënt wilt uploaden. Een clienttoepassing die bijvoorbeeld foto's of video's uploadt naar Blob-opslag, is gericht op blokblobs.

Blobs toevoegen zijn vergelijkbaar met blobs blokkeren omdat ze uit blokken bestaan. Wanneer u een toevoegende blob wijzigt, worden alleen blokken toegevoegd aan het einde van de blob. Blobs toevoegen is handig voor scenario's zoals logboekregistratie, wanneer een toepassing gegevens moet toevoegen aan een bestaande blob.

Paginablobs zijn geschikt als de toepassing willekeurige schrijfbewerkingen op de gegevens moet uitvoeren. Azure-schijven voor virtuele machines worden bijvoorbeeld opgeslagen als paginablobs. Zie [Blobs voor blokken begrijpen, blobs toevoegen en paginablobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)voor meer informatie.  

## <a name="next-steps"></a>Volgende stappen

- [Schaalbaarheid en prestatiedoelen voor Blob-opslag](scalability-targets.md)
- [Schaalbaarheid en prestatiedoelen voor standaardopslagaccounts](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Status- en foutcodes](/rest/api/storageservices/Status-and-Error-Codes2)
