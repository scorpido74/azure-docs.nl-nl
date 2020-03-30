---
title: Checklist voor prestaties en schaalbaarheid voor tabelopslag - Azure Storage
description: Een checklist van beproefde praktijken voor gebruik met tafelopslag bij het ontwikkelen van krachtige toepassingen.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: 89581c8ae2fbdbb55a2abfbd527c8fdcf4b65761
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75749545"
---
# <a name="performance-and-scalability-checklist-for-table-storage"></a>Checklist voor prestaties en schaalbaarheid voor tabelopslag

Microsoft heeft een aantal beproefde praktijken ontwikkeld voor het ontwikkelen van krachtige toepassingen met tabelopslag. Deze checklist identificeert belangrijke praktijken die ontwikkelaars kunnen volgen om de prestaties te optimaliseren. Houd deze praktijken in gedachten tijdens het ontwerpen van uw toepassing en gedurende het hele proces.

Azure Storage heeft schaalbaarheids- en prestatiedoelstellingen voor capaciteit, transactiesnelheid en bandbreedte. Zie [Schaalbaarheids- en prestatiedoelen voor standaardopslagaccounts](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json) en [schaalbaarheids- en prestatiedoelen voor tabelopslag voor](scalability-targets.md)meer informatie over azure-opslagdoelen.

## <a name="checklist"></a>Controlelijst

Dit artikel organiseert bewezen praktijken voor prestaties in een checklist die u volgen tijdens het ontwikkelen van uw tabelopslagtoepassing.

| Gereed | Categorie | Ontwerpoverweging |
| --- | --- | --- |
| &nbsp; |Schaalbaarheidsdoelen |[u uw toepassing zo ontwerpen dat deze niet meer gebruikt dan het maximum aantal opslagaccounts?](#maximum-number-of-storage-accounts) |
| &nbsp; |Schaalbaarheidsdoelen |[Vermijdt u naderende capaciteits- en transactielimieten?](#capacity-and-transaction-targets) |
| &nbsp; |Schaalbaarheidsdoelen |[Nadert u de schaalbaarheidsdoelstellingen voor entiteiten per seconde?](#targets-for-data-operations) |
| &nbsp; |Netwerken |[Hebben client-side apparaten voldoende hoge bandbreedte en lage latentie om de benodigde prestaties te bereiken?](#throughput) |
| &nbsp; |Netwerken |[Hebben client-side apparaten een netwerkkoppeling van hoge kwaliteit?](#link-quality) |
| &nbsp; |Netwerken |[Is de clienttoepassing in dezelfde regio als het opslagaccount?](#location) |
| &nbsp; |Directe clienttoegang |[Gebruikt u shared access signatures (SAS) en cross-origin resource sharing (CORS) om directe toegang tot Azure Storage mogelijk te maken?](#sas-and-cors) |
| &nbsp; |Batchverwerking |[Worden uw toepassingsbatching-updates door entiteitsgroeptransacties te gebruiken?](#batch-transactions) |
| &nbsp; |.NET-configuratie |[Gebruikt u .NET Core 2.1 of hoger voor optimale prestaties?](#use-net-core) |
| &nbsp; |.NET-configuratie |[Hebt u uw client geconfigureerd om voldoende gelijktijdige verbindingen te gebruiken?](#increase-default-connection-limit) |
| &nbsp; |.NET-configuratie |[Heeft u voor .NET-toepassingen .NET geconfigureerd om een voldoende aantal threads te gebruiken?](#increase-minimum-number-of-threads) |
| &nbsp; |Parallellisme |[Hebt u ervoor gezorgd dat parallellisme op de juiste manier wordt begrensd, zodat u de mogelijkheden van uw klant niet overbelast of de schaalbaarheidsdoelstellingen benadert?](#unbounded-parallelism) |
| &nbsp; |Hulpprogramma's |[Gebruikt u de nieuwste versies van door Microsoft geleverde clientbibliotheken en -hulpprogramma's?](#client-libraries-and-tools) |
| &nbsp; |Nieuwe pogingen |[Gebruikt u een herprobeerbeleid met een exponentiële back-off voor beperking van fouten en time-outs?](#timeout-and-server-busy-errors) |
| &nbsp; |Nieuwe pogingen |[Is uw toepassing het vermijden van nieuwe tries voor niet-herprobeerbare fouten?](#non-retryable-errors) |
| &nbsp; |Configuratie |[Gebruikt u JSON voor uw tafelaanvragen?](#use-json) |
| &nbsp; |Configuratie |[Heb je het Nagle-algoritme uitgeschakeld om de prestaties van kleine aanvragen te verbeteren?](#disable-nagle) |
| &nbsp; |Tabellen en partities |[Heb je je gegevens goed verdeeld?](#schema) |
| &nbsp; |Hete partities |[Vermijd tu append-only en prepend-only patronen?](#append-only-and-prepend-only-patterns) |
| &nbsp; |Hete partities |[Zijn uw inserts / updates verspreid over vele partities?](#high-traffic-data) |
| &nbsp; |Querybereik |[Hebt u uw schema zo ontworpen dat puntquery's in de meeste gevallen kunnen worden gebruikt en tabelquery's spaarzaam kunnen worden gebruikt?](#query-scope) |
| &nbsp; |Querydichtheid |[Scannen en retourneren uw query's meestal alleen rijen die uw toepassing gebruikt?](#query-density) |
| &nbsp; |Geretourneerde gegevens beperken |[Gebruikt u filtering om terugkerende entiteiten te voorkomen die niet nodig zijn?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Geretourneerde gegevens beperken |[Gebruikt u projectie om terugkerende eigenschappen te voorkomen die niet nodig zijn?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Denormalisatie |[Hebt u uw gegevens zodanig gedenormaliseerd dat u inefficiënte query's of meerdere leesverzoeken vermijdt wanneer u gegevens probeert te krijgen?](#denormalization) |
| &nbsp; |Invoegen, bijwerken en verwijderen |[Bent u batching verzoeken die moeten worden transactioneel of kan worden gedaan op hetzelfde moment om retouren te verminderen?](#batching) |
| &nbsp; |Invoegen, bijwerken en verwijderen |[Vermijdt u het ophalen van een entiteit alleen maar om te bepalen of u invoegen of bijwerken wilt bellen?](#upsert) |
| &nbsp; |Invoegen, bijwerken en verwijderen |[Hebt u overwogen reeksen gegevens op te slaan die vaak samen worden opgehaald in één entiteit als eigenschappen in plaats van meerdere entiteiten?](#storing-data-series-in-a-single-entity) |
| &nbsp; |Invoegen, bijwerken en verwijderen |[Voor entiteiten die altijd samen worden opgehaald en in batches kunnen worden geschreven (bijvoorbeeld tijdreeksgegevens), hebt u overwogen blobs te gebruiken in plaats van tabellen?](#storing-structured-data-in-blobs) |

## <a name="scalability-targets"></a>Schaalbaarheidsdoelen

Als uw toepassing een van de schaalbaarheidsdoelen benadert of overschrijdt, kan deze worden geconfronteerd met verhoogde transactielatencies of beperking. Wanneer Azure Storage uw toepassing beperkt, begint de service met het retourneren van 503 (Server bezet) of 500 (Time-out van bewerking). Het vermijden van deze fouten door binnen de grenzen van de schaalbaarheidsdoelstellingen te blijven, is een belangrijk onderdeel van het verbeteren van de prestaties van uw toepassing.

Zie [Schaalbaarheids- en prestatiedoelen voor tabelopslag voor](scalability-targets.md)meer informatie over schaalbaarheidsdoelen voor de tabelservice.

### <a name="maximum-number-of-storage-accounts"></a>Maximum aantal opslagaccounts

Als u het maximum aantal opslagaccounts nadert dat is toegestaan voor een bepaalde abonnements-/regiocombinatie, gebruikt u dan meerdere opslagaccounts om de invallen, de uitgang, de I/O-bewerkingen per seconde (IOPS) of de capaciteit te vergroten? In dit scenario raadt Microsoft u aan gebruik te maken van hogere limieten voor opslagaccounts om het aantal opslagaccounts te verminderen dat nodig is voor uw werkbelasting, indien mogelijk. Neem contact op met [Azure Support](https://azure.microsoft.com/support/options/) om hogere limieten voor uw opslagaccount aan te vragen. Zie [Grotere opslagaccounts op](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)grotere schaal aankondigen voor meer informatie .

### <a name="capacity-and-transaction-targets"></a>Capaciteits- en transactiedoelstellingen

Als uw toepassing de schaalbaarheidsdoelen voor één opslagaccount nadert, u overwegen een van de volgende benaderingen te kiezen:  

- Heroverweeg de werkbelasting die ervoor zorgt dat uw toepassing de schaalbaarheidsdoelstelling benadert of overschrijdt. u het anders ontwerpen om minder bandbreedte of capaciteit te gebruiken, of minder transacties?
- Als uw toepassing een van de schaalbaarheidsdoelen moet overschrijden, maakt u meerdere opslagaccounts en partitiet u uw toepassingsgegevens over die meerdere opslagaccounts. Als u dit patroon gebruikt, moet u uw toepassing zo ontwerpen dat u in de toekomst meer opslagaccounts toevoegen voor taakverdeling. Opslagaccounts zelf hebben geen andere kosten dan uw gebruik in termen van opgeslagen gegevens, transacties of overgedragen gegevens.
- Als uw toepassing de bandbreedtedoelen nadert, u overwegen gegevens aan de clientzijde te comprimeren om de bandbreedte te verminderen die nodig is om de gegevens naar Azure Storage te verzenden.
    Hoewel het comprimeren van gegevens bandbreedte kan besparen en de netwerkprestaties kan verbeteren, kan dit ook negatieve effecten hebben op de prestaties. Evalueer de prestatie-impact van de aanvullende verwerkingsvereisten voor gegevenscompressie en decompressie aan de clientzijde. Houd er rekening mee dat het opslaan van gecomprimeerde gegevens het oplossen van problemen moeilijker kan maken, omdat het mogelijk moeilijker is om de gegevens te bekijken met behulp van standaardhulpprogramma's.
- Als uw toepassing de schaalbaarheidsdoelen nadert, zorg er dan voor dat u een exponentiële back-off gebruikt voor nieuwe pogingen. Het is het beste om te proberen te voorkomen dat het bereiken van de schaalbaarheidsdoelstellingen door de uitvoering van de aanbevelingen beschreven in dit artikel. Echter, met behulp van een exponentiële backoff voor nieuwe pogingen zal voorkomen dat uw toepassing van snel opnieuw proberen, die kan maken throttling erger. Zie voor meer informatie de sectie met de titel [Timeout and Server Busy errors](#timeout-and-server-busy-errors).

### <a name="targets-for-data-operations"></a>Doelen voor gegevensbewerkingen

Azure Storage load balances as the traffic to your storage account increases, but if the traffic exhibits sudden bursts, you may not be able to get this volume of throughput immediately. Verwacht beperking en/of time-outs tijdens de burst, omdat Azure Storage uw tabel automatisch laadt. Opvoeren langzaam over het algemeen levert betere resultaten, als het systeem heeft de tijd om de balans op de juiste manier te laden.

#### <a name="entities-per-second-storage-account"></a>Entiteiten per seconde (opslagaccount)

De schaalbaarheidslimiet voor toegang tot tabellen is maximaal 20.000 entiteiten (1 KB per seconde) per seconde voor een account. Over het algemeen telt elke entiteit die wordt ingevoegd, bijgewerkt, verwijderd of gescand, mee naar dit doel. Dus een batch insert die 100 entiteiten bevat zou tellen als 100 entiteiten. Een query die 1000 entiteiten scant en 5 retourneert, telt als 1000 entiteiten.

#### <a name="entities-per-second-partition"></a>Entiteiten per seconde (partitie)

Binnen één partitie is het schaalbaarheidsdoel voor toegang tot tabellen 2.000 entiteiten (elk 1 KB) per seconde, waarbij dezelfde telling wordt gebruikt als in de vorige sectie.

## <a name="networking"></a>Netwerken

De fysieke netwerkbeperkingen van de toepassing kunnen een aanzienlijke invloed hebben op de prestaties. In de volgende secties worden enkele beperkingen beschreven die gebruikers kunnen tegenkomen.  

### <a name="client-network-capability"></a>Clientnetwerkmogelijkheid

Bandbreedte en de kwaliteit van de netwerkkoppeling spelen een belangrijke rol in de prestaties van toepassingen, zoals beschreven in de volgende secties.

#### <a name="throughput"></a>Doorvoer

Voor bandbreedte is het probleem vaak de mogelijkheden van de client. Grotere Azure-exemplaren hebben NIC's met een grotere capaciteit, dus u moet overwegen een grotere instantie of meer VM's te gebruiken als u hogere netwerklimieten van één machine nodig hebt. Als u azure-opslag opent vanuit een on-premises toepassing, geldt dezelfde regel: inzicht krijgen in de netwerkmogelijkheden van het clientapparaat en de netwerkconnectiviteit met de Azure Storage-locatie en deze indien nodig verbeteren of uw ontwerp om binnen hun mogelijkheden te werken.

#### <a name="link-quality"></a>Koppelingskwaliteit

Zoals bij elk netwerkgebruik, houd er rekening mee dat netwerkomstandigheden die resulteren in fouten en pakketverlies de effectieve doorvoer zullen vertragen.  Het gebruik van WireShark of NetMon kan helpen bij het diagnosticeren van dit probleem.  

### <a name="location"></a>Locatie

In elke gedistribueerde omgeving levert het plaatsen van de client in de buurt van de server de beste prestaties. Voor toegang tot Azure Storage met de laagste latentie bevindt de beste locatie voor uw client zich binnen dezelfde Azure-regio. Als u bijvoorbeeld een Azure-webapp hebt die Azure Storage gebruikt, zoekt u deze vervolgens binnen één regio, zoals US West of Asia Southeast. Co-locating resources vermindert de latentie en de kosten, omdat bandbreedtegebruik binnen één regio gratis is.  

Als clienttoepassingen toegang krijgen tot Azure Storage, maar niet worden gehost in Azure, zoals apps voor mobiele apparaten of on-premises bedrijfsservices, kan het lokaliseren van het opslagaccount in een regio in de buurt van die clients de latentie verminderen. Als uw klanten breed verdeeld zijn (bijvoorbeeld sommige in Noord-Amerika en sommige in Europa), u overwegen één opslagaccount per regio te gebruiken. Deze aanpak is gemakkelijker te implementeren als de gegevens die de toepassing opslaat specifiek zijn voor individuele gebruikers en niet vereist dat gegevens tussen opslagaccounts worden gerepliceerd.

## <a name="sas-and-cors"></a>SAS en CORS

Stel dat u code zoals JavaScript die wordt uitgevoerd in de webbrowser van een gebruiker of in een mobiele telefoon-app moet autoriseren om toegang te krijgen tot gegevens in Azure Storage. Een benadering is het bouwen van een service-applicatie die fungeert als een proxy. Het apparaat van de gebruiker verifieert met de service, die op zijn beurt de toegang tot Azure Storage-bronnen autoriseert. Op deze manier u voorkomen dat uw opslagaccountsleutels op onveilige apparaten worden blootgesteld. Deze benadering plaatst echter een aanzienlijke overhead voor de servicetoepassing, omdat alle gegevens die tussen het apparaat van de gebruiker en Azure Storage worden overgedragen, door de servicetoepassing moeten worden doorgegeven.

U voorkomen dat u een servicetoepassing gebruikt als proxy voor Azure Storage met behulp van sas (Shared Access Signatures). Met SAS u het apparaat van uw gebruiker in staat stellen om rechtstreeks aanvragen in Azure Storage in te dienen met behulp van een beperkt toegangstoken. Als een gebruiker bijvoorbeeld een foto naar uw toepassing wil uploaden, kan uw servicetoepassing een SAS genereren en naar het apparaat van de gebruiker verzenden. Het SAS-token kan toestemming verlenen om voor een bepaald tijdsinterval naar een Azure Storage-bron te schrijven, waarna het SAS-token verloopt. Zie Beperkte toegang tot [Azure Storage-bronnen verlenen met behulp van gedeelde toegangshandtekeningen (SAS)](../common/storage-sas-overview.md)voor meer informatie over SAS.  

Doorgaans staat een webbrowser JavaScript niet toe op een pagina die wordt gehost door een website op een domein om bepaalde bewerkingen, zoals schrijfbewerkingen, naar een ander domein uit te voeren. Dit beleid, dat bekend staat als het beleid van dezelfde oorsprong, voorkomt dat een kwaadaardig script op de ene pagina toegang krijgt tot gegevens op een andere webpagina. Het beleid van dezelfde oorsprong kan echter een beperking zijn bij het bouwen van een oplossing in de cloud. Cross-origin resource sharing (CORS) is een browserfunctie waarmee het doeldomein kan communiceren met de browser die het vertrouwt op aanvragen die afkomstig zijn uit het brondomein.

Stel dat een webtoepassing die wordt uitgevoerd in Azure een aanvraag voor een bron naar een Azure Storage-account maakt. De webtoepassing is het brondomein en het opslagaccount is het doeldomein. U CORS configureren voor een van de Azure Storage-services om te communiceren met de webbrowser dat aanvragen van het brondomein worden vertrouwd door Azure Storage. Zie [CORS-ondersteuning (Cross-origin resource sharing) voor Azure Storage voor](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services)meer informatie over CORS.  
  
Zowel SAS als CORS kunnen u helpen onnodige belasting op uw webapplicatie te voorkomen.  

## <a name="batch-transactions"></a>Batchtransacties

De tabelservice ondersteunt batchtransacties op entiteiten die zich in dezelfde tabel bevinden en tot dezelfde partitiegroep behoren. Zie [Transacties met entiteitsgroepen uitvoeren](/rest/api/storageservices/performing-entity-group-transactions)voor meer informatie .

## <a name="net-configuration"></a>.NET-configuratie

Als u het .NET Framework gebruikt, worden in deze sectie een aantal snelle configuratie-instellingen weergegeven die u gebruiken om aanzienlijke prestatieverbeteringen aan te brengen.  Als u andere talen gebruikt, controleert u of vergelijkbare concepten van toepassing zijn in de door u gekozen taal.  

### <a name="use-net-core"></a>Gebruik .NET Core

Ontwikkel uw Azure Storage-toepassingen met .NET Core 2.1 of hoger om te profiteren van prestatieverbeteringen. Het gebruik van .NET Core 3.x wordt aanbevolen wanneer dat mogelijk is.

Zie de volgende blogberichten voor meer informatie over prestatieverbeteringen in .NET Core:

- [Prestatieverbeteringen in .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Prestatieverbeteringen in .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Standaardverbindingslimiet verhogen

In .NET verhoogt de volgende code de standaardverbindingslimiet (die meestal 2 is in een clientomgeving of 10 in een serveromgeving) tot 100. Doorgaans moet u de waarde instellen op ongeveer het aantal threads dat door uw toepassing wordt gebruikt.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Stel de verbindingslimiet in voordat u verbindingen opent.  

Zie voor andere programmeertalen de documentatie van die taal om te bepalen hoe u de verbindingslimiet instelt.  

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

## <a name="configuration"></a>Configuratie

In deze sectie worden verschillende snelle configuratie-instellingen weergegeven die u gebruiken om belangrijke prestatieverbeteringen aan te brengen in de tabelservice:

### <a name="use-json"></a>Json gebruiken

Vanaf de opslagserviceversie 2013-08-15 ondersteunt de tabelservice het gebruik van JSON in plaats van de op XML gebaseerde AtomPub-indeling voor het overbrengen van tabelgegevens. Het gebruik van JSON kan de laadgrootte met maar liefst 75% verminderen en kan de prestaties van uw toepassing aanzienlijk verbeteren.

Zie voor meer informatie het bericht [Microsoft Azure Tables: Introducing JSON](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) and [Payload Format for Table Service Operations](https://msdn.microsoft.com/library/azure/dn535600.aspx).

### <a name="disable-nagle"></a>Nagle uitschakelen

Het algoritme van Nagle wordt op grote schaal geïmplementeerd in TCP/IP-netwerken als een middel om de netwerkprestaties te verbeteren. Het is echter niet optimaal in alle omstandigheden (zoals zeer interactieve omgevingen). Het algoritme van Nagle heeft een negatieve invloed op de prestaties van aanvragen voor de Azure Table-service en u moet het indien mogelijk uitschakelen.

## <a name="schema"></a>Schema

Hoe u uw gegevens weergeeft en opvraagt, is de grootste factor die van invloed is op de prestaties van de tabelservice. Hoewel elke toepassing anders is, schetst deze sectie enkele algemene bewezen praktijken die betrekking hebben op:

- Tabelontwerp
- Efficiënte query's
- Efficiënte gegevensupdates

### <a name="tables-and-partitions"></a>Tabellen en partities

Tabellen zijn onderverdeeld in partities. Elke entiteit die in een partitie is opgeslagen, deelt dezelfde partitiesleutel en heeft een unieke rijsleutel om deze binnen die partitie te identificeren. Partities bieden voordelen, maar introduceren ook schaalbaarheidslimieten.

- Voordelen: U entiteiten in dezelfde partitie bijwerken in één, atomaire batchtransactie die maximaal 100 afzonderlijke opslagbewerkingen bevat (limiet van 4 MB totale grootte). Ervan uitgaande dat hetzelfde aantal entiteiten moet worden opgehaald, u ook gegevens binnen één partitie efficiënter opvragen dan gegevens die partities overspannen (hoewel u verder leest voor verdere aanbevelingen over het opvragen van tabelgegevens).
- Schaalbaarheidslimiet: toegang tot entiteiten die in één partitie zijn opgeslagen, kan niet worden belast, omdat partities atomaire batchtransacties ondersteunen. Daarom is de schaalbaarheidsdoelstelling voor een afzonderlijke tabelpartitie lager dan voor de tabelservice als geheel.

Vanwege deze kenmerken van tabellen en partities moet u de volgende ontwerpprincipes aannemen:

- Zoek gegevens die uw clienttoepassing vaak bijwerkt of query's in dezelfde logische eenheid van het werk in dezelfde partitie. Zoek bijvoorbeeld gegevens in dezelfde partitie als uw toepassing schrijft aggregeren of als u atomaire batchbewerkingen uitvoert. Gegevens in één partitie kunnen ook efficiënter worden opgevraagd in één query dan gegevens tussen partities.
- Gegevens zoeken die uw clienttoepassing niet invoegt, bijwerkt of opvraagt in dezelfde logische werkeenheid (dat wil zeggen in één query of batch-update) in afzonderlijke partities. Houd er rekening mee dat er geen limiet is aan het aantal partitiesleutels in één tabel, dus het hebben van miljoenen partitiesleutels is geen probleem en heeft geen invloed op de prestaties. Als uw toepassing bijvoorbeeld een populaire website is met gebruikersinlogen, kan het gebruik van de gebruikersnaam als partitiesleutel een goede keuze zijn.

#### <a name="hot-partitions"></a>Hete partities

Een hot partitie is er een die een onevenredig percentage van het verkeer naar een account ontvangt en niet kan worden geladen omdat het een enkele partitie is. In het algemeen worden hot partities op twee manieren gemaakt:

#### <a name="append-only-and-prepend-only-patterns"></a>Alleen toevoegen en alleen voorbereiden

Het patroon 'Alleen toevoegen' is er een waarbij alle (of bijna alle) verkeer naar een bepaalde partitiesleutel toeneemt en afneemt afhankelijk van de huidige tijd. Stel dat uw toepassing de huidige datum gebruikt als partitiesleutel voor logboekgegevens. Dit ontwerp resulteert in alle inserts naar de laatste partitie in uw tabel, en het systeem kan niet goed laden evenwicht. Als het volume van het verkeer naar die partitie hoger is dan het schaalbaarheidsdoel op partitieniveau, leidt dit tot beperking. Het is beter om ervoor te zorgen dat verkeer naar meerdere partities wordt verzonden, zodat de aanvragen in uw tabel in evenwicht kunnen worden gebracht met de belastingsbalans.

#### <a name="high-traffic-data"></a>Gegevens met veel verkeer

Als uw partitieschema resulteert in één partitie met alleen gegevens die veel meer worden gebruikt dan andere partities, u ook beperking zien als die partitie het schaalbaarheidsdoel voor één partitie nadert. Het is beter om ervoor te zorgen dat uw partitieschema resulteert in geen enkele partitie die de schaalbaarheidsdoelen nadert.

### <a name="querying"></a>Uitvoeren van query's

In deze sectie worden beproefde praktijken beschreven voor het bevragen van de tabelservice.

#### <a name="query-scope"></a>Querybereik

Er zijn verschillende manieren om het bereik van entiteiten op te geven dat moet worden opgevraagd. In de volgende lijst wordt elke optie voor querybereik beschreven.

- **Puntquery's:**- Een puntquery haalt precies één entiteit op door zowel de partitiesleutel als de rijsleutel van de entiteit op te geven die moet worden opgehaald. Deze query's zijn efficiënt en u moet ze waar mogelijk gebruiken.
- **Partitiequery's:** Een partitiequery is een query die een set gegevens ophaalt waarmee een algemene partitiesleutel wordt gedeeld. Doorgaans geeft de query naast een partitiesleutel een bereik met rijsleutelwaarden of een bereik van waarden voor een eigenschap van een entiteit op. Deze query's zijn minder efficiënt dan puntquery's en moeten spaarzaam worden gebruikt.
- **Tabelquery's:** Een tabelquery is een query waarmee een set entiteiten wordt opgehaald die geen algemene partitiesleutel deelt. Deze query's zijn niet efficiënt en je moet ze vermijden indien mogelijk.

Over het algemeen u scans vermijden (query's die groter zijn dan één entiteit), maar als u moet scannen, probeert u uw gegevens zo te ordenen dat uw scans de gegevens ophalen die u nodig hebt zonder aanzienlijke hoeveelheden entiteiten te scannen of terug te sturen die u niet nodig hebt.

#### <a name="query-density"></a>Querydichtheid

Een andere belangrijke factor in query-efficiëntie is het aantal entiteiten dat is geretourneerd in vergelijking met het aantal entiteiten dat is gescand om de geretourneerde set te vinden. Als uw toepassing een tabelquery uitvoert met een filter voor een eigenschapswaarde die slechts 1% van de gegevensdeelt, scant de query 100 entiteiten voor elke entiteit die wordt geretourneerd. De eerder besproken tabelschaalbaarheidsdoelen hebben allemaal betrekking op het aantal gescande entiteiten en niet op het aantal geretourneerde entiteiten: een lage querydichtheid kan er gemakkelijk voor zorgen dat de tabelservice uw toepassing beperkt omdat deze zoveel entiteiten moet scannen om de entiteit op te halen die u zoekt. Zie de sectie getiteld [Denormalisatie](#denormalization)voor meer informatie over het voorkomen van beperking.

#### <a name="limiting-the-amount-of-data-returned"></a>De hoeveelheid geretourneerde gegevens beperken

Wanneer u weet dat een query entiteiten retourneert die u niet nodig hebt in de clienttoepassing, u overwegen een filter te gebruiken om de grootte van de geretourneerde set te verkleinen. Hoewel de entiteiten die niet naar de client worden teruggestuurd nog steeds meetellen voor de schaalbaarheidslimieten, worden de prestaties van uw toepassingen verbeterd vanwege de verminderde netwerkpayloadgrootte en het verminderde aantal entiteiten dat uw clienttoepassing moet verwerken. Houd er rekening mee dat de schaalbaarheidsdoelen betrekking hebben op het aantal gescande entiteiten, dus een query die veel entiteiten filtert, kan nog steeds leiden tot beperking, zelfs als er weinig entiteiten worden geretourneerd. Zie de sectie met [querydichtheid](#query-density)voor meer informatie over het efficiënt maken van query's.

Als uw clienttoepassing slechts een beperkte set eigenschappen van de entiteiten in uw tabel nodig heeft, u projectie gebruiken om de grootte van de geretourneerde gegevensset te beperken. Net als bij filteren helpt projectie de netwerkbelasting en clientverwerking te verminderen.

#### <a name="denormalization"></a>Denormalisatie

In tegenstelling tot het werken met relationele databases, leiden de beproefde praktijken voor het efficiënt opvragen van tabelgegevens tot het denormaliseren van uw gegevens. Dat wil zeggen, het dupliceren van dezelfde gegevens in meerdere entiteiten (een voor elke sleutel die u gebruiken om de gegevens te vinden) om het aantal entiteiten dat een query moet scannen om de gegevens die de client nodig heeft te minimaliseren, in plaats van grote aantallen entiteiten te scannen om de gegevens te vinden uw toepassingsbehoeften. Bijvoorbeeld, in een e-commerce website, wilt u misschien een bestelling te vinden, zowel door de klant-ID (geef me de bestellingen van deze klant) en op de datum (geef me bestellingen op een datum). In Tabelopslag u de entiteit (of een verwijzing ernaar) het beste twee keer opslaan, één keer met tabelnaam, PK en RK om het vinden per klant-ID te vergemakkelijken, één keer om het vinden ervan op de datum te vergemakkelijken.  

### <a name="insert-update-and-delete"></a>Invoegen, bijwerken en verwijderen

In deze sectie worden beproefde praktijken beschreven voor het wijzigen van entiteiten die zijn opgeslagen in de tabelservice.  

#### <a name="batching"></a>Batchverwerking

Batchtransacties worden entiteitsgroeptransacties genoemd in Azure Storage. Alle bewerkingen binnen een entiteitsgroeptransactie moeten op één partitie in één tabel staan. Gebruik waar mogelijk entiteitsgroeptransacties om inserts, updates en verwijderingen in batches uit te voeren. Het gebruik van entiteitsgroeptransacties vermindert het aantal retourvluchten van uw clienttoepassing naar de server, vermindert het aantal factureerbare transacties (een entiteitsgroeptransactie telt als één transactie voor factureringsdoeleinden en kan maximaal 100 opslagbewerkingen) en maakt atoomupdates mogelijk (alle bewerkingen slagen of mislukken allemaal binnen een entiteitsgroepstransactie). Omgevingen met hoge latencies zoals mobiele apparaten zullen veel baat hebben bij het gebruik van entiteitsgroepstransacties.  

#### <a name="upsert"></a>Upsert

Gebruik tafel **Upsert** operaties waar mogelijk. Er zijn twee soorten **Upsert,** die beide efficiënter kunnen zijn dan een traditionele **invoeg-** en **updatebewerking:**  

- **InsertOrMerge:** gebruik deze bewerking wanneer u een subset van de eigenschappen van de entiteit wilt uploaden, maar niet zeker weet of de entiteit al bestaat. Als de entiteit bestaat, worden de eigenschappen van de **upsert-bewerking** bijgewerkt en worden alle bestaande eigenschappen zoals deze entiteit niet bestaat, wordt de nieuwe entiteit ingevoegd. Dit is vergelijkbaar met het gebruik van projectie in een query, in die zin dat u alleen de eigenschappen hoeft te uploaden die worden gewijzigd.
- **InsertOrReplace**: Gebruik deze bewerking wanneer u een geheel nieuwe entiteit wilt uploaden, maar u weet niet zeker of deze al bestaat. Gebruik deze bewerking wanneer u weet dat de nieuw geüploade entiteit volledig correct is omdat deze de oude entiteit volledig overschrijft. U wilt bijvoorbeeld de entiteit bijwerken die de huidige locatie van een gebruiker opslaat, ongeacht of de toepassing eerder locatiegegevens voor de gebruiker heeft opgeslagen. de nieuwe locatieentiteit is voltooid en u hebt geen informatie van een vorige entiteit nodig.

#### <a name="storing-data-series-in-a-single-entity"></a>Gegevensreeksen opslaan in één entiteit

Soms slaat een toepassing een reeks gegevens op die vaak in één keer moet worden opgehaald: een toepassing kan bijvoorbeeld het CPU-gebruik in de loop van de tijd volgen om een rollende grafiek van de gegevens van de afgelopen 24 uur in kaart te brengen. Een benadering is om één tabelentiteit per uur te hebben, waarbij elke entiteit een specifiek uur vertegenwoordigt en het CPU-gebruik voor dat uur opslaat. Om deze gegevens in kaart te brengen, moet de toepassing de entiteiten ophalen die de gegevens uit de 24 meest recente uren bewaren.  

Als alternatief kan uw toepassing het CPU-gebruik voor elk uur opslaan als een afzonderlijke eigenschap van één entiteit: om elk uur bij te werken, kan uw toepassing één **insertormerge-upsert-oproep** gebruiken om de waarde voor het meest recente uur bij te werken. Als u de gegevens wilt plotten, hoeft de toepassing slechts één entiteit op te halen in plaats van 24, wat zorgt voor een efficiënte query. Zie de sectie met de titel [Querybereik](#query-scope)voor meer informatie over query-efficiëntie).

#### <a name="storing-structured-data-in-blobs"></a>Gestructureerde gegevens opslaan in blobs

Als u batchinserts uitvoert en vervolgens reeksen entiteiten samen ophaalt, u overwegen blobs te gebruiken in plaats van tabellen. Een goed voorbeeld is een logbestand. U meerdere minuten logboeken batchen en deze invoegen en vervolgens enkele minuten logboeken tegelijk ophalen. In dit geval zijn de prestaties beter als u blobs gebruikt in plaats van tabellen, omdat u het aantal objecten dat naar of gelezen is geschreven of gelezen aanzienlijk verminderen, en mogelijk ook het aantal aanvragen dat moet worden ingediend.  

## <a name="next-steps"></a>Volgende stappen

- [Schaalbaarheid en prestatiedoelen voor tabelopslag](scalability-targets.md)
- [Schaalbaarheid en prestatiedoelen voor standaardopslagaccounts](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Status- en foutcodes](/rest/api/storageservices/Status-and-Error-Codes2)
