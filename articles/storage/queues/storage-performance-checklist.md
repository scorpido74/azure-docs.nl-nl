---
title: Controle lijst voor prestaties en schaal baarheid voor wachtrij opslag-Azure Storage
description: Een controle lijst met beproefde procedures voor het gebruik van wachtrij opslag bij het ontwikkelen van toepassingen met hoge prestaties.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: 02196a4df71bef767f8d1921f704907ad06e77b4
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311152"
---
# <a name="performance-and-scalability-checklist-for-queue-storage"></a>Controle lijst voor prestaties en schaal baarheid voor wachtrij opslag

Micro soft heeft een aantal bewezen procedures ontwikkeld voor het ontwikkelen van toepassingen met hoge prestaties met wachtrij opslag. Deze controle lijst bevat de belangrijkste procedures die ontwikkel aars kunnen volgen om de prestaties te optimaliseren. Houd bij het ontwerpen van uw toepassing en tijdens het proces de volgende procedures in acht.

Azure Storage heeft schaal baarheid en prestatie doelen voor capaciteit, transactie snelheid en band breedte. Zie [Azure Storage schaal baarheid en prestatie doelen voor opslag accounts](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)voor meer informatie over Azure Storage schaalbaarheids doelen.

## <a name="checklist"></a>Controlelijst

In dit artikel worden bewezen procedures voor het uitvoeren van prestaties in een controle lijst georganiseerd die u kunt volgen tijdens het ontwikkelen van uw wachtrij opslag toepassing.

| Klaar | Category | Ontwerp overweging |
| --- | --- | --- |
| &nbsp; |Schaalbaarheids doelen |[Kunt u uw toepassing zo ontwerpen dat deze niet meer dan het maximale aantal opslag accounts gebruikt?](#maximum-number-of-storage-accounts) |
| &nbsp; |Schaalbaarheids doelen |[Vermijdt u benadering van capaciteits-en transactie limieten?](#capacity-and-transaction-targets) |
| &nbsp; |Networking |[Hebben apparaten aan de client zijde voldoende band breedte en lage latentie om de benodigde prestaties te verwezenlijken?](#throughput) |
| &nbsp; |Networking |[Hebben apparaten aan de client zijde een netwerk koppeling van hoge kwaliteit?](#link-quality) |
| &nbsp; |Networking |[Bevindt de client toepassing zich in dezelfde regio als het opslag account?](#location) |
| &nbsp; |Directe client toegang |[Maakt u gebruik van Shared Access signatures (SAS) en cross-Origin Resource Sharing (CORS) om direct toegang tot Azure Storage te bieden?](#sas-and-cors) |
| &nbsp; |.NET-configuratie |[Gebruikt u .NET Core 2,1 of hoger voor optimale prestaties?](#use-net-core) |
| &nbsp; |.NET-configuratie |[Hebt u uw client geconfigureerd voor het gebruik van een voldoende aantal gelijktijdige verbindingen?](#increase-default-connection-limit) |
| &nbsp; |.NET-configuratie |[Voor .NET-toepassingen hebt u .NET geconfigureerd voor het gebruik van een voldoende aantal threads?](#increase-minimum-number-of-threads) |
| &nbsp; |Parallelle uitvoering |[Hebt u gegarandeerd dat de parallelle kracht op de juiste wijze is gebonden, zodat u de mogelijkheden van uw client niet overbelastt of de schaalbaarheids doelen kunt benaderen?](#unbounded-parallelism) |
| &nbsp; |Tools |[Gebruikt u de nieuwste versies van door micro soft meegeleverde client bibliotheken en-hulpprogram ma's?](#client-libraries-and-tools) |
| &nbsp; |Nieuwe pogingen |[Gebruikt u een beleid voor opnieuw proberen met een exponentiële uitstel voor het beperken van fouten en time-outs?](#timeout-and-server-busy-errors) |
| &nbsp; |Nieuwe pogingen |[Voor komt uw toepassing nieuwe pogingen voor niet-herstel bare fouten?](#non-retryable-errors) |
| &nbsp; |Configuratie |[Hebt u het Nagle-algoritme uitgeschakeld om de prestaties van kleine aanvragen te verbeteren?](#disable-nagle) |
| &nbsp; |Berichtgrootte |[Worden uw berichten gecomprimeerd om de prestaties van de wachtrij te verbeteren?](#message-size) |
| &nbsp; |Bulksgewijs ophalen |[Haalt u meerdere berichten op in één GET-bewerking?](#batch-retrieval) |
| &nbsp; |Polling frequentie |[Zoekt u regel matig genoeg om de waargenomen latentie van uw toepassing te verminderen?](#queue-polling-interval) |
| &nbsp; |Bericht bijwerken |[Gebruikt u de update bericht bewerking voor het opslaan van de voortgang van de verwerking van berichten, zodat u het hele bericht niet opnieuw hoeft te verwerken als er een fout optreedt?](#use-update-message) |
| &nbsp; |Architectuur |[Gebruikt u wacht rijen om uw volledige toepassing schaalbaar te maken door langlopende workloads uit het kritieke pad te houden en vervolgens onafhankelijk te schalen?](#application-architecture) |

## <a name="scalability-targets"></a>Schaalbaarheids doelen

Als uw toepassing een van de schaalbaarheids doelen benadert of overschrijdt, kunnen er meer trans acties of beperkingen optreden. Wanneer Azure Storage uw toepassing beperkt, begint de service met het retour neren van 503 (server bezet) of 500 (time-out voor bewerking). Als u deze fouten vermijdt door binnen de grenzen van de schaalbaarheids doelen te blijven, is een belang rijk onderdeel van het verbeteren van de prestaties van uw toepassing.

Zie [Azure Storage schaal baarheid en prestatie doelen](/azure/storage/common/storage-scalability-targets?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#azure-queue-storage-scale-targets)voor meer informatie over de schaalbaarheids doelen voor de Queue-service.

### <a name="maximum-number-of-storage-accounts"></a>Maximum aantal opslag accounts

Als u het maximum aantal opslag accounts nadert dat is toegestaan voor een bepaalde combi natie van abonnement/regio, kunt u meerdere opslag accounts gebruiken om Shard te verg Roten, leiden, I/O-bewerkingen per seconde (IOPS) of capaciteit? In dit scenario raadt micro soft u aan gebruik te maken van verhoogde limieten voor standaard opslag accounts om zo mogelijk het aantal opslag accounts te beperken dat vereist is voor uw werk belasting. Neem contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/options/) om verhoogde limieten voor uw opslag account aan te vragen. Zie voor meer informatie [aankondigen van grotere opslag accounts met hogere schaal](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Capaciteits-en transactie doelen

Als uw toepassing de schaalbaarheids doelen voor één opslag account nadert, kunt u een van de volgende benaderingen aannemen:  

- Als de schaalbaarheids doelen voor wacht rijen ontoereikend zijn voor uw toepassing, gebruikt u meerdere wacht rijen en distribueert u berichten over hen.
- Bekijk de werk belasting die ervoor zorgt dat uw toepassing het schaalbaarheids doel benadert of overschrijdt. Kunt u het op verschillende manieren ontwerpen om minder band breedte of capaciteit of minder trans acties te gebruiken?
- Als uw toepassing een van de schaalbaarheids doelen moet overschrijden, maakt u meerdere opslag accounts en partitioneert u uw toepassings gegevens over deze meerdere opslag accounts. Als u dit patroon gebruikt, moet u ervoor zorgen dat u uw toepassing ontwerpt, zodat u in de toekomst meer opslag accounts kunt toevoegen voor taak verdeling. Opslag accounts zelf hebben geen andere kosten dan uw gebruik in termen van opgeslagen gegevens, gemaakte trans acties of overgedragen gegevens.
- Als uw toepassing de bandbreedte doelen nadert, kunt u overwegen om de gegevens aan de client zijde te comprimeren om de band breedte te verminderen die nodig is om de gegevens naar Azure Storage te verzenden.
    Tijdens het comprimeren van gegevens kan band breedte worden bespaard en de netwerk prestaties worden verbeterd, maar kan ook negatieve gevolgen voor de prestaties hebben. Evalueer de invloed op de prestaties van de extra verwerkings vereisten voor gegevens compressie en decompressie aan de client zijde. Houd er rekening mee dat het opslaan van gecomprimeerde gegevens lastigere problemen kan opleveren omdat het mogelijk moeilijker is om de gegevens te bekijken met behulp van standaard hulpprogramma's.
- Als uw toepassing de schaalbaarheids doelen nadert, moet u ervoor zorgen dat u een exponentiële uitstel gebruikt voor nieuwe pogingen. Het is raadzaam om te voor komen dat u de schaalbaarheids doelen bereikt door de aanbevelingen te implementeren die in dit artikel worden beschreven. Als u echter een exponentiële uitstel gebruikt voor nieuwe pogingen, kan uw toepassing niet snel opnieuw proberen, waardoor het beperken van de toepassingen kan worden verergerd. Zie voor meer informatie de sectie met de titel [time-out en server bezet](#timeout-and-server-busy-errors).

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

## <a name="disable-nagle"></a>Nagle uitschakelen

Het Nagle-algoritme is breed geïmplementeerd in TCP/IP-netwerken om de netwerk prestaties te verbeteren. Het is echter niet optimaal in alle omstandigheden (zoals zeer interactieve omgevingen). Het Nagle-algoritme heeft een negatieve invloed op de prestaties van aanvragen voor de Azure Table service en moet indien mogelijk worden uitgeschakeld.

## <a name="message-size"></a>Berichtgrootte

De prestaties en schaal baarheid van de wachtrij nemen af naarmate de bericht grootte toeneemt. Plaats alleen de informatie die de ontvanger nodig heeft in een bericht.  

## <a name="batch-retrieval"></a>Batch ophalen

U kunt Maxi maal 32 berichten uit een wachtrij in één bewerking ophalen. Bij het ophalen van batches kan het aantal retour aanname van de client toepassing worden verminderd. Dit is vooral nuttig voor omgevingen, zoals mobiele apparaten, met een hoge latentie.  

## <a name="queue-polling-interval"></a>Polling-interval voor de wachtrij

De meeste toepassingen controleren op berichten uit een wachtrij, die een van de grootste bronnen aan trans acties voor die toepassing kunnen zijn. Uw polling-interval zo goed mogelijk selecteren: polling wordt te vaak gebruikt om ervoor te zorgen dat uw toepassing de schaalbaarheids doelen voor de wachtrij nadert. Bij 200.000 trans acties voor $0,01 (op het moment van schrijven) is er echter een eenmalige polling per seconde voor een maand van minder dan 15 euro. de kosten zijn dus niet doorgaans een factor die van invloed is op uw gekozen polling-interval.  

Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/)voor actuele informatie over de kosten.  

## <a name="use-update-message"></a>Update bericht gebruiken

U kunt de bewerking **bericht bijwerken** gebruiken om de time-out voor onzichtbaarheid te verhogen of om de status informatie van een bericht bij te werken. Het gebruik van **update berichten** kan een efficiëntere benadering zijn dan een werk stroom die een taak van de ene wachtrij naar de volgende stuurt, wanneer elke stap van de taak is voltooid. Uw toepassing kan de taak status opslaan in het bericht en vervolgens blijven werken, in plaats van het bericht voor de volgende stap van de taak telkens opnieuw in de wachtrij te plaatsen wanneer een stap is voltooid. Houd er bij elke **Update bericht** bewerking mee dat het doel van de schaal baarheid wordt geteld.

## <a name="application-architecture"></a>Toepassingsarchitectuur

Gebruik wacht rijen om uw toepassings architectuur schaalbaar te maken. Hieronder vindt u enkele manieren waarop u wacht rijen kunt gebruiken om uw toepassing beter te schaalbaar te maken:  

- U kunt wacht rijen gebruiken om werk belastingen te maken voor het verwerken en uitzetten van workloads in uw toepassing. U kunt bijvoorbeeld aanvragen van gebruikers in de wachtrij plaatsen voor het uitvoeren van processorintensieve werk, zoals het wijzigen van de grootte van geüploade afbeeldingen.
- U kunt wacht rijen gebruiken om delen van uw toepassing te scheiden, zodat u ze onafhankelijk van elkaar kunt schalen. Een web-front-end kan bijvoorbeeld enquête resultaten van gebruikers in een wachtrij plaatsen voor latere analyse en opslag. U kunt meer werk rollen instanties toevoegen om de wachtrij gegevens naar behoefte te verwerken.  

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage schaalbaarheids-en prestatie doelen voor opslag accounts](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Controle lijst voor prestaties en schaal baarheid voor Blob Storage](../blobs/storage-performance-checklist.md)
- [Controle lijst voor prestaties en schaal baarheid voor tabel opslag](../tables/storage-performance-checklist.md)
- [Status en fout codes](/rest/api/storageservices/Status-and-Error-Codes2)
