---
title: Controle lijst voor prestaties en schaal baarheid voor Table Storage-Azure Storage
description: Een controle lijst met bewezen procedures voor het gebruik van tabel opslag in het ontwikkelen van toepassingen met hoge prestaties.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: b36ed2cac7e5009a0581091252b36dcd5af81bd7
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389996"
---
# <a name="performance-and-scalability-checklist-for-table-storage"></a>Controle lijst voor prestaties en schaal baarheid voor tabel opslag

Micro soft heeft een aantal bewezen procedures ontwikkeld voor het ontwikkelen van toepassingen met hoge prestaties met tabel opslag. Deze controle lijst bevat de belangrijkste procedures die ontwikkel aars kunnen volgen om de prestaties te optimaliseren. Houd bij het ontwerpen van uw toepassing en tijdens het proces de volgende procedures in acht.

Azure Storage heeft schaal baarheid en prestatie doelen voor capaciteit, transactie snelheid en band breedte. Zie [Azure Storage schaal baarheid en prestatie doelen voor opslag accounts](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)voor meer informatie over Azure Storage schaalbaarheids doelen.

## <a name="checklist"></a>Controlelijst

In dit artikel worden bewezen procedures voor het uitvoeren van prestaties in een controle lijst georganiseerd die u kunt volgen tijdens het ontwikkelen van uw Table Storage-toepassing.

| Klaar | Category | Ontwerp overweging |
| --- | --- | --- |
| &nbsp; |Schaalbaarheids doelen |[Kunt u uw toepassing zo ontwerpen dat deze niet meer dan het maximale aantal opslag accounts gebruikt?](#maximum-number-of-storage-accounts) |
| &nbsp; |Schaalbaarheids doelen |[Vermijdt u benadering van capaciteits-en transactie limieten?](#capacity-and-transaction-targets) |
| &nbsp; |Networking |[Hebben apparaten aan de client zijde voldoende band breedte en lage latentie om de benodigde prestaties te verwezenlijken?](#throughput) |
| &nbsp; |Networking |[Hebben apparaten aan de client zijde een netwerk koppeling van hoge kwaliteit?](#link-quality) |
| &nbsp; |Networking |[Bevindt de client toepassing zich in dezelfde regio als het opslag account?](#location) |
| &nbsp; |Directe client toegang |[Maakt u gebruik van Shared Access signatures (SAS) en cross-Origin Resource Sharing (CORS) om direct toegang tot Azure Storage te bieden?](#sas-and-cors) |
| &nbsp; |Batchverwerking |[Worden de updates voor de toepassing batchgewijs bijgewerkt met behulp van entiteits groeps transacties?](#batch-transactions) |
| &nbsp; |.NET-configuratie |[Gebruikt u .NET Core 2,1 of hoger voor optimale prestaties?](#use-net-core) |
| &nbsp; |.NET-configuratie |[Hebt u uw client geconfigureerd voor het gebruik van een voldoende aantal gelijktijdige verbindingen?](#increase-default-connection-limit) |
| &nbsp; |.NET-configuratie |[Voor .NET-toepassingen hebt u .NET geconfigureerd voor het gebruik van een voldoende aantal threads?](#increase-minimum-number-of-threads) |
| &nbsp; |Parallelle uitvoering |[Hebt u gegarandeerd dat de parallelle kracht op de juiste wijze is gebonden, zodat u de mogelijkheden van uw client niet overbelastt of de schaalbaarheids doelen kunt benaderen?](#unbounded-parallelism) |
| &nbsp; |Tools |[Gebruikt u de nieuwste versies van door micro soft meegeleverde client bibliotheken en-hulpprogram ma's?](#client-libraries-and-tools) |
| &nbsp; |Nieuwe pogingen |[Gebruikt u een beleid voor opnieuw proberen met een exponentiële uitstel voor het beperken van fouten en time-outs?](#timeout-and-server-busy-errors) |
| &nbsp; |Nieuwe pogingen |[Voor komt uw toepassing nieuwe pogingen voor niet-herstel bare fouten?](#non-retryable-errors) |
| &nbsp; |Schaalbaarheids doelen |[Benadert u de schaalbaarheids doelen voor entiteiten per seconde?](#table-specific-scalability-targets) |
| &nbsp; |Configuratie |[Gebruikt u JSON voor uw tabel aanvragen?](#use-json) |
| &nbsp; |Configuratie |[Hebt u het Nagle-algoritme uitgeschakeld om de prestaties van kleine aanvragen te verbeteren?](#disable-nagle) |
| &nbsp; |Tabellen en partities |[Hebt u uw gegevens goed gepartitioneerd?](#schema) |
| &nbsp; |Dynamische partities |[Vermijdt u alleen toevoegen en alleen laten voorafgaan door patronen?](#append-only-and-prepend-only-patterns) |
| &nbsp; |Dynamische partities |[Worden uw toevoegingen/updates verspreid over verschillende partities?](#high-traffic-data) |
| &nbsp; |Querybereik |[Hebt u uw schema ontworpen zodat punt query's in de meeste gevallen kunnen worden gebruikt en kunnen tabel query's spaarzaam worden gebruikt?](#query-scope) |
| &nbsp; |Query densiteit |[Worden in uw query's doorgaans alleen de rijen gescand en geretourneerd die door uw toepassing worden gebruikt?](#query-density) |
| &nbsp; |De geretourneerde gegevens beperken |[Gebruikt u filteren om te voor komen dat entiteiten worden geretourneerd die niet nodig zijn?](#limiting-the-amount-of-data-returned) |
| &nbsp; |De geretourneerde gegevens beperken |[Maakt u gebruik van projectie om te voor komen dat eigenschappen worden geretourneerd die niet nodig zijn?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Denormalisatie |[Hebt u uw gegevens genormaliseerd, zodat u inefficiënte query's of meerdere Lees aanvragen voor het ophalen van gegevens wilt voor komen?](#denormalization) |
| &nbsp; |Invoegen, bijwerken en verwijderen |[Worden er aanvragen voor batch verwerking die transactioneel moeten zijn of tegelijkertijd kunnen worden uitgevoerd om retouren te verminderen?](#batching) |
| &nbsp; |Invoegen, bijwerken en verwijderen |[Vermijdt u het ophalen van een entiteit alleen om te bepalen of INSERT of update moet worden aangeroepen?](#upsert) |
| &nbsp; |Invoegen, bijwerken en verwijderen |[Moet u een reeks gegevens opslaan die regel matig worden opgehaald in één entiteit als eigenschappen in plaats van meerdere entiteiten?](#storing-data-series-in-a-single-entity) |
| &nbsp; |Invoegen, bijwerken en verwijderen |[Voor entiteiten die altijd samen worden opgehaald en kunnen worden geschreven in batches (bijvoorbeeld time series-gegevens), kunt u het gebruik van blobs in plaats van tabellen overwegen?](#storing-structured-data-in-blobs) |

## <a name="scalability-targets"></a>Schaalbaarheids doelen

Als uw toepassing een van de schaalbaarheids doelen benadert of overschrijdt, kunnen er meer trans acties of beperkingen optreden. Wanneer Azure Storage uw toepassing beperkt, begint de service met het retour neren van 503 (server bezet) of 500 (time-out voor bewerking). Als u deze fouten vermijdt door binnen de grenzen van de schaalbaarheids doelen te blijven, is een belang rijk onderdeel van het verbeteren van de prestaties van uw toepassing.

Zie [Azure Storage schaal baarheid en prestatie doelen voor opslag accounts](/azure/storage/common/storage-scalability-targets?toc=%2fazure%2fstorage%2ftables%2ftoc.json#azure-table-storage-scale-targets)voor meer informatie over de schaalbaarheids doelen voor de Table service.

### <a name="maximum-number-of-storage-accounts"></a>Maximum aantal opslag accounts

Als u het maximum aantal opslag accounts nadert dat is toegestaan voor een bepaalde combi natie van abonnement/regio, kunt u meerdere opslag accounts gebruiken om Shard te verg Roten, leiden, I/O-bewerkingen per seconde (IOPS) of capaciteit? In dit scenario raadt micro soft u aan gebruik te maken van verhoogde limieten voor opslag accounts om zo mogelijk het aantal opslag accounts te beperken dat vereist is voor uw werk belasting. Neem contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/options/) om verhoogde limieten voor uw opslag account aan te vragen. Zie voor meer informatie [aankondigen van grotere opslag accounts met hogere schaal](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Capaciteits-en transactie doelen

Als uw toepassing de schaalbaarheids doelen voor één opslag account nadert, kunt u een van de volgende benaderingen aannemen:  

- Bekijk de werk belasting die ervoor zorgt dat uw toepassing het schaalbaarheids doel benadert of overschrijdt. Kunt u het op verschillende manieren ontwerpen om minder band breedte of capaciteit of minder trans acties te gebruiken?
- Als uw toepassing een van de schaalbaarheids doelen moet overschrijden, maakt u meerdere opslag accounts en partitioneert u uw toepassings gegevens over deze meerdere opslag accounts. Als u dit patroon gebruikt, moet u ervoor zorgen dat u uw toepassing ontwerpt, zodat u in de toekomst meer opslag accounts kunt toevoegen voor taak verdeling. Opslag accounts zelf hebben geen andere kosten dan uw gebruik in termen van opgeslagen gegevens, gemaakte trans acties of overgedragen gegevens.
- Als uw toepassing de bandbreedte doelen nadert, kunt u overwegen om de gegevens aan de client zijde te comprimeren om de band breedte te verminderen die nodig is om de gegevens naar Azure Storage te verzenden.
    Tijdens het comprimeren van gegevens kan band breedte worden bespaard en de netwerk prestaties worden verbeterd, maar kan ook negatieve gevolgen voor de prestaties hebben. Evalueer de invloed op de prestaties van de extra verwerkings vereisten voor gegevens compressie en decompressie aan de client zijde. Houd er rekening mee dat het opslaan van gecomprimeerde gegevens lastigere problemen kan opleveren omdat het mogelijk moeilijker is om de gegevens te bekijken met behulp van standaard hulpprogramma's.
- Als uw toepassing de schaalbaarheids doelen nadert, moet u ervoor zorgen dat u een exponentiële uitstel gebruikt voor nieuwe pogingen. Het is raadzaam om te voor komen dat u de schaalbaarheids doelen bereikt door de aanbevelingen te implementeren die in dit artikel worden beschreven. Als u echter een exponentiële uitstel gebruikt voor nieuwe pogingen, kan uw toepassing niet snel opnieuw proberen, waardoor het beperken van de toepassingen kan worden verergerd. Zie voor meer informatie de sectie met de titel [time-out en server bezet](#timeout-and-server-busy-errors).

## <a name="table-specific-scalability-targets"></a>Tabel-specifieke schaalbaarheids doelen

Naast de beperkingen van de band breedte van een hele opslag account, hebben tabellen de volgende specifieke schaalbaarheids limiet. Het systeem houdt de taak verdeling uit naarmate het verkeer toeneemt, maar als uw verkeer plotselinge bursts heeft, kunt u dit volume van de door Voer niet direct ophalen. Als uw patroon bursts bevat, moet u rekening houden met bandbreedte beperking en/of time-outs tijdens de burst, omdat de opslag service automatisch uw tabel verdeelt. Een trage beveiliging heeft doorgaans betere resultaten naarmate de systeem tijd de taak verdeling op de juiste manier geeft.

## <a name="networking"></a>Networking

De beperkingen van het fysieke netwerk van de toepassing kunnen een grote invloed hebben op de prestaties. In de volgende secties worden enkele beperkingen beschreven die gebruikers kunnen tegen komen.  

### <a name="client-network-capability"></a>Client netwerk mogelijkheid

De band breedte en de kwaliteit van de netwerk koppeling spelen belang rijke rollen in de toepassings prestaties, zoals beschreven in de volgende secties.

#### <a name="throughput"></a>Doorvoer

Voor band breedte is het probleem vaak de mogelijkheden van de client. Grotere Azure-instanties hebben Nic's met een grotere capaciteit. u moet dus overwegen een grotere instantie of meer Vm's te gebruiken als u hogere netwerk limieten van één computer nodig hebt. Als u Azure Storage opent vanuit een on-premises toepassing, geldt dezelfde regel: inzicht in de netwerk mogelijkheden van het client apparaat en de netwerk verbinding met de Azure Storage locatie en verbeter deze indien nodig, of ontwerp uw toepassing om binnen hun mogelijkheden te werken.

#### <a name="link-quality"></a>Koppelings kwaliteit

Net als bij elk netwerk gebruik moet u er rekening mee houden dat de netwerk omstandigheden die fouten veroorzaken en pakket verlies leiden tot een trage door voer.  Het gebruik van WireShark of NetMon kan helpen bij het vaststellen van dit probleem.  

### <a name="location"></a>Locatie

In een gedistribueerde omgeving biedt de-client bij de server de beste prestaties. Voor toegang tot Azure Storage met de laagste latentie bevindt de beste locatie voor uw client zich in dezelfde Azure-regio. Als u bijvoorbeeld een Azure-web-app hebt die gebruikmaakt van Azure Storage, zoekt u deze binnen één regio, zoals vs-West of Azië-Zuidoost. Door co-locaties te plaatsen vermindert u de latentie en de kosten, omdat het bandbreedte gebruik binnen één regio gratis is.  

Als client toepassingen toegang krijgen tot Azure Storage maar niet worden gehost in azure, zoals apps voor mobiele apparaten of on-premises bedrijfs Services, kan de latentie worden verminderd door het opslag account in een regio in de buurt van die clients te zoeken. Als uw clients breed worden gedistribueerd (bijvoorbeeld sommige in Noord-Amerika en sommige in Europa), kunt u een opslag account per regio gebruiken. Deze methode is eenvoudiger te implementeren als de gegevens die de app opslaat, specifiek zijn voor afzonderlijke gebruikers en er geen replicatie van gegevens tussen opslag accounts nodig is.

## <a name="sas-and-cors"></a>SAS en CORS

Stel dat u code moet machtigen zoals Java script dat wordt uitgevoerd in de webbrowser van een gebruiker of in een mobiele telefoon-app om toegang te krijgen tot gegevens in Azure Storage. Een manier is om een service toepassing te bouwen die fungeert als een proxy. Het apparaat van de gebruiker wordt geverifieerd bij de service, die op zijn beurt toegang tot Azure Storage bronnen verleent. Op deze manier kunt u voor komen dat u de sleutels van uw opslag account op onveilige apparaten weergeeft. Met deze benadering wordt echter een aanzienlijke overhead op de service toepassing geplaatst, omdat alle gegevens die worden overgedragen tussen het apparaat van de gebruiker en Azure Storage door de service toepassing moeten worden door gegeven.

U kunt voor komen dat een service toepassing wordt gebruikt als proxy voor Azure Storage met behulp van Shared Access signatures (SAS). Met SAS kunt u het apparaat van uw gebruiker in staat stellen om aanvragen rechtstreeks naar Azure Storage te brengen met behulp van een beperkt toegangs token. Als een gebruiker bijvoorbeeld een foto wil uploaden naar uw toepassing, kan uw service toepassing een SAS genereren en deze verzenden naar het apparaat van de gebruiker. Het SAS-token kan toestemming geven om te schrijven naar een Azure Storage resource gedurende een opgegeven tijds interval, waarna de SAS-token verloopt. Zie voor meer informatie over SA'S [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](../common/storage-sas-overview.md).  

Normaal gesp roken staat een webbrowser geen Java script toe op een pagina die wordt gehost door een website op het ene domein om bepaalde bewerkingen, zoals schrijf bewerkingen, uit te voeren op een ander domein. Op basis van dit beleid wordt voor komen dat een schadelijk script op één pagina de toegang tot gegevens op een andere webpagina kan verkrijgen. Hetzelfde-Origin-beleid kan echter een beperking zijn bij het bouwen van een oplossing in de Cloud. Cross-Origin Resource Sharing (CORS) is een browser functie waarmee het doel domein kan communiceren met de browser waarmee aanvragen worden vertrouwd die afkomstig zijn van het bron domein.

Stel bijvoorbeeld dat een webtoepassing die in azure wordt uitgevoerd, een aanvraag voor een bron naar een Azure Storage-account maakt. De webtoepassing is het bron domein en het opslag account is het doel domein. U kunt CORS voor elk van de Azure Storage-services configureren om te communiceren met de webbrowser die aanvragen van het bron domein worden vertrouwd door Azure Storage. Zie [ondersteuning voor het gebruik van cors (cross-Origin Resource Sharing) voor Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services)voor meer informatie over cors.  
  
Zowel SAS als CORS kan u helpen om te voor komen dat uw webtoepassing onnodig wordt geladen.  

## <a name="batch-transactions"></a>Batch transacties

De Table service ondersteunt batch-trans acties op entiteiten die zich in dezelfde tabel bevinden en deel uitmaken van dezelfde partitie groep. Zie [trans acties voor entiteits groepen uitvoeren](/rest/api/storageservices/performing-entity-group-transactions)voor meer informatie.

## <a name="net-configuration"></a>.NET-configuratie

Als u de .NET Framework gebruikt, worden in deze sectie verschillende snelle configuratie-instellingen weer gegeven die u kunt gebruiken om belang rijke prestatie verbeteringen aan te brengen.  Als u andere talen gebruikt, controleert u of er soort gelijke concepten van toepassing zijn in de taal die u hebt gekozen.  

### <a name="use-net-core"></a>.NET core gebruiken

Ontwikkel uw Azure Storage-toepassingen met .NET Core 2,1 of hoger om te profiteren van de prestatie verbeteringen. .NET Core 3. x wordt aanbevolen als dat mogelijk is.

Voor meer informatie over prestatie verbeteringen in .NET core raadpleegt u de volgende blog berichten:

- [Prestatie verbeteringen in .NET Core 3,0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Prestatie verbeteringen in .NET Core 2,1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Standaard verbindings limiet verhogen

In .NET verhoogt de volgende code de standaard verbindings limiet (meestal 2 in een client omgeving of 10 in een server omgeving) tot 100. Normaal gesp roken moet u de waarde instellen op ongeveer het aantal threads dat door uw toepassing wordt gebruikt.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Stel de verbindings limiet in voordat u verbindingen opent.  

Raadpleeg de documentatie van die taal voor andere programmeer talen om te bepalen hoe de verbindings limiet moet worden ingesteld.  

Zie voor meer informatie het blog bericht [webservices: gelijktijdige verbindingen](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/).  

### <a name="increase-minimum-number-of-threads"></a>Minimum aantal threads verhogen

Als u synchrone aanroepen gebruikt in combi natie met asynchrone taken, wilt u mogelijk het aantal threads in de thread groep verhogen:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Zie de methode [thread pool. onSetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads) voor meer informatie.  

## <a name="unbounded-parallelism"></a>Niet-gebonden parallellisme

Parallelle uitvoering kan ideaal zijn voor prestaties, wees voorzichtig met het gebruik van een ongebonden parallelle afname, wat betekent dat er geen limiet wordt afgedwongen voor het aantal threads of parallelle aanvragen. Zorg ervoor dat u parallelle aanvragen beperkt tot het uploaden of downloaden van gegevens, om toegang te krijgen tot meerdere partities in hetzelfde opslag account of om toegang te krijgen tot meerdere items in dezelfde partitie. Als parallellisme niet is gebonden, kan uw toepassing de mogelijkheden van het client apparaat of de schaalbaarheids doelen van het opslag account overschrijden, wat resulteert in langere latenties en beperking.  

## <a name="client-libraries-and-tools"></a>Client bibliotheken en hulpprogram ma's

Gebruik altijd de nieuwste client bibliotheken en hulpprogram ma's van micro soft voor de beste prestaties. Azure Storage-client bibliotheken zijn beschikbaar voor verschillende talen. Azure Storage biedt ook ondersteuning voor Power shell en Azure CLI. Micro soft ontwikkelt deze client bibliotheken en hulpprogram ma's met het oog op de prestaties, houdt ze up-to-date met de nieuwste service versies en zorgt ervoor dat ze een groot aantal van de bewezen activiteiten intern verwerken. Zie de [documentatie over Azure Storage](/azure/storage/#reference)voor meer informatie.

## <a name="handle-service-errors"></a>Service fouten afhandelen

Azure Storage retourneert een fout wanneer de service een aanvraag niet kan verwerken. Informatie over de fouten die door Azure Storage in een bepaald scenario kunnen worden geretourneerd, is handig voor het optimaliseren van de prestaties.

### <a name="timeout-and-server-busy-errors"></a>Fouten bij time-out en server bezet

Azure Storage kan uw toepassing beperken als deze de schaalbaarheids limieten nadert. In sommige gevallen kan Azure Storage mogelijk geen aanvraag verwerken vanwege enige tijdelijke situatie. In beide gevallen kan de service een fout van 503 (server bezet) of 500 (Timeout) retour neren. Deze fouten kunnen zich ook voordoen als de service gegevens partities opnieuw moet verdelen voor een hogere door voer. De client toepassing moet meestal de bewerking opnieuw uitvoeren waardoor een van deze fouten wordt veroorzaakt. Als Azure Storage echter uw toepassing beperkt, omdat deze de schaalbaarheids doelen overschrijdt, of zelfs als de service om een andere reden niet kan worden uitgevoerd, kunnen agressieve pogingen het probleem erger maken. Het gebruik van een exponentiële beleid voor opnieuw proberen wordt aanbevolen en de client bibliotheken zijn standaard ingesteld op dit gedrag. Uw toepassing kan bijvoorbeeld na 2 seconden of 4 seconden, vervolgens 10 seconden, vervolgens 30 seconden, opnieuw worden geprobeerd en vervolgens volledig weer geven. Op deze manier vermindert uw toepassing de belasting van de service aanzienlijk, in plaats van exacerbating gedrag dat kan leiden tot het beperken van het probleem.  

Connectiviteits fouten kunnen onmiddellijk opnieuw worden geprobeerd, omdat ze niet het gevolg zijn van een beperking en naar verwachting tijdelijk zijn.  

### <a name="non-retryable-errors"></a>Niet-herstel bare fouten

De client bibliotheken voeren een nieuwe poging uit met een bewustzijn welke fouten opnieuw kunnen worden geprobeerd en wat niet mogelijk is. Als u echter de Azure Storage REST API rechtstreeks aanroept, zijn er enkele fouten die u niet opnieuw moet proberen. Bijvoorbeeld, een 400 (ongeldige aanvraag) geeft aan dat de client toepassing een aanvraag heeft verzonden die niet kan worden verwerkt omdat deze niet in de verwachte vorm werd weer gegeven. Als deze aanvraag opnieuw wordt verzonden, wordt dezelfde reactie elke keer uitgevoerd, zodat er geen punt in de nieuwe poging wordt gedaan. Als u de REST API Azure Storage rechtstreeks aanroept, moet u rekening houden met mogelijke fouten en of deze opnieuw moeten worden geprobeerd.

Zie [status-en fout codes](/rest/api/storageservices/status-and-error-codes2)voor meer informatie over Azure Storage fout codes.

## <a name="configuration"></a>Configuratie

In deze sectie vindt u verschillende snelle configuratie-instellingen die u kunt gebruiken om belang rijke prestatie verbeteringen in de Table service aan te brengen:

### <a name="use-json"></a>JSON gebruiken

Vanaf Storage Service versie 2013-08-15 ondersteunt de Table service het gebruik van JSON in plaats van de XML-AtomPub-indeling voor het overdragen van tabel gegevens. Het gebruik van JSON kan Payload-grootten tot 75% verminderen en kan de prestaties van uw toepassing aanzienlijk verbeteren.

Zie de tabel post [Microsoft Azure Tables: Inleiding tot JSON](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) -en [Payload-indeling voor Table service-bewerkingen](https://msdn.microsoft.com/library/azure/dn535600.aspx)voor meer informatie.

### <a name="disable-nagle"></a>Nagle uitschakelen

Het Nagle-algoritme is breed geïmplementeerd in TCP/IP-netwerken om de netwerk prestaties te verbeteren. Het is echter niet optimaal in alle omstandigheden (zoals zeer interactieve omgevingen). Het Nagle-algoritme heeft een negatieve invloed op de prestaties van aanvragen voor de Azure Table service en moet indien mogelijk worden uitgeschakeld.

## <a name="schema"></a>Schema

Hoe u uw gegevens opstelt en query's uitvoert, is de grootste enkele factor die van invloed is op de prestaties van de Table service. Hoewel elke toepassing verschilt, wordt in deze sectie een overzicht van enkele algemene bewezen procedures besproken die betrekking hebben op:

- Tabel ontwerp
- Efficiënte query's
- Efficiënte gegevens updates

### <a name="tables-and-partitions"></a>Tabellen en partities

Tabellen worden onderverdeeld in partities. Elke entiteit die is opgeslagen in een partitie deelt dezelfde partitie sleutel en heeft een unieke rijdefinitie om deze te identificeren binnen die partitie. Partities bieden voor delen, maar kunnen ook schaal baarheids limieten introduceren.

- Voor delen: u kunt entiteiten in dezelfde partitie bijwerken in één atomische, batch-trans actie die Maxi maal 100 afzonderlijke opslag bewerkingen bevat (limiet van 4 MB totale grootte). Uitgaande van het aantal entiteiten dat moet worden opgehaald, kunt u ook de gegevens binnen één partitie efficiënter opvragen dan gegevens die partities omspannen (hoewel er nog meer aanbevelingen worden gedaan voor het uitvoeren van query's in tabel gegevens).
- Schaal baarheids limiet: toegang tot entiteiten die zijn opgeslagen in één partitie, kan niet gelijkmatig worden verdeeld, omdat partities atomische batch transacties ondersteunen. Daarom is het schaalbaarheids doel voor een afzonderlijke tabel partitie lager dan voor de Table service als geheel.

Vanwege deze kenmerken van tabellen en partities moet u de volgende ontwerp principes aannemen:

- Zoek gegevens die uw client toepassing regel matig bijwerkt of query's in dezelfde logische eenheid van werken in dezelfde partitie. Zoek bijvoorbeeld naar gegevens in dezelfde partitie als uw toepassing bezig is met het samen voegen van schrijf bewerkingen of als u atomische batch bewerkingen uitvoert. Ook kunnen gegevens in één partitie efficiënter worden opgevraagd in één query dan gegevens tussen de verschillende partities.
- Zoek gegevens die uw client toepassing niet invoegt, bijwerkt of een query uitvoert in dezelfde logische werk eenheid (dat wil zeggen, in één query of batch-update) in afzonderlijke partities. Houd er rekening mee dat er geen limiet is voor het aantal partitie sleutels in één tabel, waardoor miljoenen partitie sleutels geen probleem zijn en niet van invloed zijn op de prestaties. Als uw toepassing bijvoorbeeld een populaire website is met gebruikers aanmelding, met behulp van de gebruikers-ID, kan de partitie sleutel een goede keuze zijn.

#### <a name="hot-partitions"></a>Dynamische partities

Een hot-partitie is een een niet-proportioneel percentage van het verkeer naar een account en kan niet worden gebalanceerd omdat het een enkele partitie is. In het algemeen worden er op een van de volgende twee manieren dynamische partities gemaakt:

#### <a name="append-only-and-prepend-only-patterns"></a>Alleen patronen voor alleen-en laten voorafgaan door toevoegen

Het patroon ' alleen toevoegen ' is een waarbij alle (of bijna alle) verkeer naar een bepaalde partitie sleutel toeneemt en afneemt op basis van de huidige tijd. Stel bijvoorbeeld dat uw toepassing de huidige datum als een partitie sleutel gebruikt voor logboek gegevens. Met dit ontwerp worden alle invoegingen naar de laatste partitie in de tabel verzonden en kan het systeem niet goed worden gebalanceerd. Als het volume van het verkeer naar die partitie het schaalbaarheids doel op partitie niveau overschrijdt, resulteert dit in een vertraging. Het is beter om ervoor te zorgen dat verkeer wordt verzonden naar meerdere partities om taak verdeling in te scha kelen voor de aanvragen in uw tabel.

#### <a name="high-traffic-data"></a>Gegevens met hoog verkeer

Als uw partitie schema resulteert in een enkele partitie die alleen gegevens bevat die veel meer worden gebruikt dan andere partities, ziet u mogelijk ook de vertraging wanneer die partitie het schaalbaarheids doel voor één partitie nadert. Het is beter om ervoor te zorgen dat uw partitie schema resulteert in geen enkele partitie die de schaalbaarheids doelen nadert.

### <a name="querying"></a>Uitvoeren van query's

In deze sectie worden bewezen procedures beschreven voor het uitvoeren van query's op de Table service.

#### <a name="query-scope"></a>Querybereik

Er zijn verschillende manieren om het bereik van entiteiten op te geven dat moet worden opgevraagd. In de volgende lijst worden de opties voor het query bereik beschreven.

- **Point-query's:** -een punt query haalt precies één entiteit op door zowel de partitie sleutel als de rij sleutel op te geven van de entiteit die moet worden opgehaald. Deze query's zijn efficiënt en u moet ze waar mogelijk gebruiken.
- **Partitie query's:** Een partitie query is een query die een set gegevens ophaalt die een gemeen schappelijke partitie sleutel deelt. Normaal gesp roken specificeert de query een reeks waarden voor de rij of een reeks waarden voor een bepaalde entiteits eigenschap naast een partitie sleutel. Deze query's zijn minder efficiënt dan punt query's en moeten spaarzaam worden gebruikt.
- **Tabel query's:** Een tabel query is een query waarmee een set entiteiten wordt opgehaald die geen gemeen schappelijke partitie sleutel delen. Deze query's zijn niet efficiënt en u moet ze indien mogelijk vermijden.

In het algemeen kunt u scans voor komen (query's die groter zijn dan één entiteit), maar als u moet scannen, kunt u proberen uw gegevens in te delen, zodat uw scans de gegevens ophalen die u nodig hebt zonder dat u grote hoeveel heden entiteiten hoeft te scannen of te retour neren die u niet nodig hebt.

#### <a name="query-density"></a>Query densiteit

Een andere sleutel factor in query efficiëntie is het aantal entiteiten dat wordt geretourneerd in vergelijking met het aantal entiteiten dat is gescand om de geretourneerde set te vinden. Als uw toepassing een tabel query uitvoert met een filter voor een eigenschaps waarde die slechts 1% van de gegevens shares bevat, scant de query 100 entiteiten voor elke entiteit die wordt geretourneerd. De tabel schaalbaarheids doelen die eerder zijn besproken, zijn allemaal gerelateerd aan het aantal gescande entiteiten en niet het aantal geretourneerde entiteiten: met een lage query dichtheid kan de Table service eenvoudig uw toepassing vertragen omdat deze zoveel entiteiten moet scannen om Haal de entiteit op die u zoekt. Zie de sectie ' [denormaliseren](#denormalization)' voor meer informatie over het voor komen van een beperking.

#### <a name="limiting-the-amount-of-data-returned"></a>De hoeveelheid geretourneerde gegevens beperken

Wanneer u weet dat een query entiteiten retourneert die u niet nodig hebt in de client toepassing, kunt u een filter gebruiken om de grootte van de geretourneerde set te reduceren. Hoewel de entiteiten die niet naar de client worden geretourneerd, nog steeds betrekking hebben op de limieten voor de schaal baarheid, worden de prestaties van uw toepassingen verbeterd vanwege de gereduceerde Payload-grootte van het netwerk en het beperkte aantal entiteiten dat uw client toepassing moet verwerken. Houd er rekening mee dat de schaal baarheids doelen te maken hebben met het aantal gescande entiteiten. een query die veel entiteiten uitfiltert, kan echter nog steeds worden beperkt, zelfs als er slechts enkele entiteiten worden geretourneerd. Zie de sectie ' [query-densiteit](#query-density)' voor meer informatie over het efficiënt maken van query's.

Als uw client toepassing slechts een beperkt aantal eigenschappen van de entiteiten in uw tabel nodig heeft, kunt u projectie gebruiken om de grootte van de geretourneerde gegevensset te beperken. Net als bij het filteren helpt projectie de belasting van het netwerk en de client verwerking te verminderen.

#### <a name="denormalization"></a>Denormalisatie

In tegens telling tot het werken met relationele data bases, de bewezen procedures voor het efficiënt uitvoeren van query's in tabel gegevens om uw gegevens te denormaliseren. Dat wil zeggen dat u dezelfde gegevens in meerdere entiteiten dupliceert (één voor elke sleutel die u kunt gebruiken om de gegevens te vinden) om zo het aantal entiteiten te beperken dat een query moet scannen om de gegevens te vinden die de client nodig heeft, in plaats van een groot aantal entiteiten te scannen om de gegevens van uw app te vinden lication-behoeften. Zo kunt u bijvoorbeeld in een e-commerce website een bestelling vinden op basis van de klant-ID (dit zijn de orders van deze klant) en op basis van de datum (Geef bestellingen op een datum). In Table Storage kunt u het beste de entiteit (of een verwijzing naar de) twee keer opslaan: eenmaal met tabel naam, PK en RK om het vinden van de klant-ID te vergemakkelijken, eenmaal om deze te vergemakkelijken op basis van de datum.  

### <a name="insert-update-and-delete"></a>Invoegen, bijwerken en verwijderen

In deze sectie worden bewezen procedures beschreven voor het wijzigen van entiteiten die zijn opgeslagen in de Table service.  

#### <a name="batching"></a>Batchverwerking

Batch transacties worden ook wel entiteits groeps transacties genoemd in Azure Storage. Alle bewerkingen binnen een entiteits groeps transactie moeten op één partitie in één tabel staan. Gebruik waar mogelijk entiteits groeps transacties om invoeg-, bijwerk-en verwijder bewerkingen uit te voeren in batches. Door het gebruik van entiteits groeps transacties wordt het aantal afrondingen van uw client toepassing naar de server verminderd, waardoor het aantal factureer bare trans acties (de trans actie van een entiteits groep wordt geteld als één trans actie voor facturerings doeleinden en Maxi maal 100 kan bevatten opslag bewerkingen) en schakelt u atomische updates in (alle bewerkingen slagen of mislukken in een entiteits groeps transactie). Omgevingen met hoge latentie, zoals mobiele apparaten, profiteren aanzienlijk van het gebruik van entiteits groeps transacties.  

#### <a name="upsert"></a>Upsert

Gebruik waar mogelijk tabel **Upsert** -bewerkingen. Er zijn twee soorten **Upsert**, die beide efficiënter kunnen zijn dan een traditionele **Insert** -en **Update** -bewerkingen:  

- **InsertOrMerge**: gebruik deze bewerking wanneer u een subset van de eigenschappen van de entiteit wilt uploaden, maar niet zeker weet of de entiteit al bestaat. Als de entiteit bestaat, worden de eigenschappen die zijn opgenomen in de **Upsert** -bewerking bijgewerkt en blijven alle bestaande eigenschappen ongewijzigd, als de entiteit niet bestaat, wordt de nieuwe entiteit ingevoegd. Dit is vergelijkbaar met het gebruik van projectie in een query, zodat u alleen de eigenschappen hoeft te uploaden die worden gewijzigd.
- **InsertOrReplace**: gebruik deze bewerking als u een geheel nieuwe entiteit wilt uploaden, maar u niet zeker weet of deze al bestaat. Gebruik deze bewerking wanneer u weet dat de zojuist geüploade entiteit volledig juist is, omdat deze de oude entiteit volledig overschrijft. U wilt bijvoorbeeld de entiteit bijwerken waarin de huidige locatie van een gebruiker wordt opgeslagen, ongeacht of de toepassing eerder opgeslagen locatie gegevens voor de gebruiker bevat. de nieuwe locatie-entiteit is voltooid en u hebt geen gegevens nodig van een vorige entiteit.

#### <a name="storing-data-series-in-a-single-entity"></a>Gegevens reeksen opslaan in één entiteit

Soms slaat een toepassing een reeks gegevens op die regel matig moet worden opgehaald: een toepassing kan bijvoorbeeld het CPU-gebruik in de loop van de tijd volgen om een doorlopende grafiek van de gegevens in de afgelopen 24 uur te zetten. Een manier is om één tabel entiteit per uur te hebben, waarbij elke entiteit een specifiek uur vertegenwoordigt en het CPU-gebruik voor dat uur opslaat. Voor het uitzetten van deze gegevens moet de toepassing de entiteiten ophalen die de gegevens van de 24 meest recente uren houden.  

Het is ook mogelijk dat uw toepassing het CPU-gebruik voor elk uur opslaat als een afzonderlijke eigenschap van één entiteit: om elk uur bij te werken, kan uw toepassing één **InsertOrMerge Upsert** -aanroep gebruiken om de waarde voor het meest recente uur bij te werken. Voor het uitzetten van de gegevens hoeft de toepassing slechts één entiteit op te halen in plaats van 24, waardoor een efficiënte query wordt gemaakt. Zie de sectie [query bereik](#query-scope)genaamd voor meer informatie over de efficiëntie van query's.

#### <a name="storing-structured-data-in-blobs"></a>Gestructureerde gegevens opslaan in blobs

Als u batch-invoeg acties uitvoert en vervolgens de bereiken van entiteiten samen ophaalt, kunt u gebruikmaken van blobs in plaats van tabellen. Een goed voor beeld hiervan is een logboek bestand. U kunt meerdere minuten aan logboeken vastleggen en deze invoegen en vervolgens meerdere minuten aan logboeken tegelijk ophalen. In dit geval is de prestaties beter als u blobs gebruikt in plaats van tabellen, omdat u het aantal objecten dat is geschreven of gelezen aanzienlijk kunt verminderen, en mogelijk ook het aantal aanvragen dat moet worden gedaan.  

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage schaalbaarheids-en prestatie doelen voor opslag accounts](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Status en fout codes](/rest/api/storageservices/Status-and-Error-Codes2)
