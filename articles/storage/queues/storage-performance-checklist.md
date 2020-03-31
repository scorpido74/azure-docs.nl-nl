---
title: Checklist prestaties en schaalbaarheid voor wachtrijopslag - Azure Storage
description: Een checklist van beproefde praktijken voor gebruik met queue storage bij het ontwikkelen van krachtige toepassingen.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: eb1821537e6e25b05dfdca3107729eecf4c6e1bf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75750508"
---
# <a name="performance-and-scalability-checklist-for-queue-storage"></a>Checklist voor prestaties en schaalbaarheid voor wachtrijopslag

Microsoft heeft een aantal beproefde praktijken ontwikkeld voor het ontwikkelen van krachtige toepassingen met Queue-opslag. Deze checklist identificeert belangrijke praktijken die ontwikkelaars kunnen volgen om de prestaties te optimaliseren. Houd deze praktijken in gedachten tijdens het ontwerpen van uw toepassing en gedurende het hele proces.

Azure Storage heeft schaalbaarheids- en prestatiedoelstellingen voor capaciteit, transactiesnelheid en bandbreedte. Zie [Schaalbaarheids- en prestatiedoelen voor standaardopslagaccounts](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) en [schaalbaarheids- en prestatiedoelen voor wachtrijopslag voor](scalability-targets.md)meer informatie over azure-opslagdoelen.

## <a name="checklist"></a>Controlelijst

In dit artikel worden bewezen praktijken voor prestaties georganiseerd in een checklist die u volgen tijdens het ontwikkelen van uw wachtrijopslagtoepassing.

| Gereed | Categorie | Ontwerpoverweging |
| --- | --- | --- |
| &nbsp; |Schaalbaarheidsdoelen |[u uw toepassing zo ontwerpen dat deze niet meer gebruikt dan het maximum aantal opslagaccounts?](#maximum-number-of-storage-accounts) |
| &nbsp; |Schaalbaarheidsdoelen |[Vermijdt u naderende capaciteits- en transactielimieten?](#capacity-and-transaction-targets) |
| &nbsp; |Netwerken |[Hebben client-side apparaten voldoende hoge bandbreedte en lage latentie om de benodigde prestaties te bereiken?](#throughput) |
| &nbsp; |Netwerken |[Hebben client-side apparaten een netwerkkoppeling van hoge kwaliteit?](#link-quality) |
| &nbsp; |Netwerken |[Is de clienttoepassing in dezelfde regio als het opslagaccount?](#location) |
| &nbsp; |Directe clienttoegang |[Gebruikt u shared access signatures (SAS) en cross-origin resource sharing (CORS) om directe toegang tot Azure Storage mogelijk te maken?](#sas-and-cors) |
| &nbsp; |.NET-configuratie |[Gebruikt u .NET Core 2.1 of hoger voor optimale prestaties?](#use-net-core) |
| &nbsp; |.NET-configuratie |[Hebt u uw client geconfigureerd om voldoende gelijktijdige verbindingen te gebruiken?](#increase-default-connection-limit) |
| &nbsp; |.NET-configuratie |[Heeft u voor .NET-toepassingen .NET geconfigureerd om een voldoende aantal threads te gebruiken?](#increase-minimum-number-of-threads) |
| &nbsp; |Parallellisme |[Hebt u ervoor gezorgd dat parallellisme op de juiste manier wordt begrensd, zodat u de mogelijkheden van uw klant niet overbelast of de schaalbaarheidsdoelstellingen benadert?](#unbounded-parallelism) |
| &nbsp; |Hulpprogramma's |[Gebruikt u de nieuwste versies van door Microsoft geleverde clientbibliotheken en -hulpprogramma's?](#client-libraries-and-tools) |
| &nbsp; |Nieuwe pogingen |[Gebruikt u een herprobeerbeleid met een exponentiële back-off voor beperking van fouten en time-outs?](#timeout-and-server-busy-errors) |
| &nbsp; |Nieuwe pogingen |[Is uw toepassing het vermijden van nieuwe tries voor niet-herprobeerbare fouten?](#non-retryable-errors) |
| &nbsp; |Configuratie |[Heb je het Nagle-algoritme uitgeschakeld om de prestaties van kleine aanvragen te verbeteren?](#disable-nagle) |
| &nbsp; |Berichtgrootte |[Zijn uw berichten compact om de prestaties van de wachtrij te verbeteren?](#message-size) |
| &nbsp; |Ophalen van bulk |[Haalt u meerdere berichten op in één GET-bewerking?](#batch-retrieval) |
| &nbsp; |Polling frequentie |[Bent u polling vaak genoeg om de waargenomen latentie van uw toepassing te verminderen?](#queue-polling-interval) |
| &nbsp; |Bericht bijwerken |[Gebruikt u de bewerking Bericht bijwerken om voortgang bij het verwerken van berichten op te slaan, zodat u voorkomen dat u het hele bericht opnieuw moet verwerken als er een fout optreedt?](#use-update-message) |
| &nbsp; |Architectuur |[Gebruikt u wachtrijen om uw hele toepassing schaalbaarder te maken door langlopende workloads uit het kritieke pad te houden en vervolgens onafhankelijk van elkaar te schalen?](#application-architecture) |

## <a name="scalability-targets"></a>Schaalbaarheidsdoelen

Als uw toepassing een van de schaalbaarheidsdoelen benadert of overschrijdt, kan deze worden geconfronteerd met verhoogde transactielatencies of beperking. Wanneer Azure Storage uw toepassing beperkt, begint de service met het retourneren van 503 (Server bezet) of 500 (Time-out van bewerking). Het vermijden van deze fouten door binnen de grenzen van de schaalbaarheidsdoelstellingen te blijven, is een belangrijk onderdeel van het verbeteren van de prestaties van uw toepassing.

Zie [Schaalbaarheids- en prestatiedoelen](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage)voor Azure Storage voor meer informatie over schaalbaarheidsdoelen voor de wachtrijservice.

### <a name="maximum-number-of-storage-accounts"></a>Maximum aantal opslagaccounts

Als u het maximum aantal opslagaccounts nadert dat is toegestaan voor een bepaalde abonnements-/regiocombinatie, gebruikt u dan meerdere opslagaccounts om de invallen, de uitgang, de I/O-bewerkingen per seconde (IOPS) of de capaciteit te vergroten? In dit scenario raadt Microsoft u aan gebruik te maken van hogere limieten voor opslagaccounts om het aantal opslagaccounts te verminderen dat nodig is voor uw werkbelasting, indien mogelijk. Neem contact op met [Azure Support](https://azure.microsoft.com/support/options/) om hogere limieten voor uw opslagaccount aan te vragen. Zie [Grotere opslagaccounts op](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)grotere schaal aankondigen voor meer informatie .

### <a name="capacity-and-transaction-targets"></a>Capaciteits- en transactiedoelstellingen

Als uw toepassing de schaalbaarheidsdoelen voor één opslagaccount nadert, u overwegen een van de volgende benaderingen te kiezen:  

- Als de schaalbaarheidsdoelen voor wachtrijen onvoldoende zijn voor uw toepassing, gebruikt u meerdere wachtrijen en distribueert u berichten over deze wachtrijen.
- Heroverweeg de werkbelasting die ervoor zorgt dat uw toepassing de schaalbaarheidsdoelstelling benadert of overschrijdt. u het anders ontwerpen om minder bandbreedte of capaciteit te gebruiken, of minder transacties?
- Als uw toepassing een van de schaalbaarheidsdoelen moet overschrijden, maakt u meerdere opslagaccounts en partitiet u uw toepassingsgegevens over die meerdere opslagaccounts. Als u dit patroon gebruikt, moet u uw toepassing zo ontwerpen dat u in de toekomst meer opslagaccounts toevoegen voor taakverdeling. Opslagaccounts zelf hebben geen andere kosten dan uw gebruik in termen van opgeslagen gegevens, transacties of overgedragen gegevens.
- Als uw toepassing de bandbreedtedoelen nadert, u overwegen gegevens aan de clientzijde te comprimeren om de bandbreedte te verminderen die nodig is om de gegevens naar Azure Storage te verzenden.
    Hoewel het comprimeren van gegevens bandbreedte kan besparen en de netwerkprestaties kan verbeteren, kan dit ook negatieve effecten hebben op de prestaties. Evalueer de prestatie-impact van de aanvullende verwerkingsvereisten voor gegevenscompressie en decompressie aan de clientzijde. Houd er rekening mee dat het opslaan van gecomprimeerde gegevens het oplossen van problemen moeilijker kan maken, omdat het mogelijk moeilijker is om de gegevens te bekijken met behulp van standaardhulpprogramma's.
- Als uw toepassing de schaalbaarheidsdoelen nadert, zorg er dan voor dat u een exponentiële back-off gebruikt voor nieuwe pogingen. Het is het beste om te proberen te voorkomen dat het bereiken van de schaalbaarheidsdoelstellingen door de uitvoering van de aanbevelingen beschreven in dit artikel. Echter, met behulp van een exponentiële backoff voor nieuwe pogingen zal voorkomen dat uw toepassing van snel opnieuw proberen, die kan maken throttling erger. Zie voor meer informatie de sectie met de titel [Timeout and Server Busy errors](#timeout-and-server-busy-errors).

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

## <a name="disable-nagle"></a>Nagle uitschakelen

Het algoritme van Nagle wordt op grote schaal geïmplementeerd in TCP/IP-netwerken als een middel om de netwerkprestaties te verbeteren. Het is echter niet optimaal in alle omstandigheden (zoals zeer interactieve omgevingen). Het algoritme van Nagle heeft een negatieve invloed op de prestaties van aanvragen voor de Azure Table-service en u moet het indien mogelijk uitschakelen.

## <a name="message-size"></a>Berichtgrootte

De prestaties en schaalbaarheid van de wachtrij nemen af naarmate de berichtgrootte toeneemt. Zet alleen de informatie die de ontvanger nodig heeft in een bericht.  

## <a name="batch-retrieval"></a>Batch ophalen

U maximaal 32 berichten ophalen uit een wachtrij in één bewerking. Batch retrieval kan het aantal retourvluchten van de clienttoepassing verminderen, wat vooral handig is voor omgevingen, zoals mobiele apparaten, met een hoge latentie.  

## <a name="queue-polling-interval"></a>Wachtrijpolling-interval

De meeste toepassingen poll voor berichten uit een wachtrij, die een van de grootste bronnen van transacties voor die toepassing kan zijn. Selecteer uw polling-interval verstandig: polling te vaak kan ertoe leiden dat uw toepassing om de schaalbaarheid doelen voor de wachtrij te benaderen. Echter, op 200.000 transacties voor $ 0,01 (op het moment van schrijven), een enkele processor polling een keer per seconde voor een maand zou minder dan 15 cent kosten, zodat de kosten is meestal niet een factor die uw keuze van polling interval beïnvloedt.  

Zie [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/)voor actuele kosteninformatie.  

## <a name="use-update-message"></a>Bericht bijwerken gebruiken

U de bewerking **Bericht bijwerken** gebruiken om de time-out voor onzichtbaarheid te vergroten of om de statusgegevens van een bericht bij te werken. Het gebruik **van Update-bericht** kan een efficiëntere aanpak zijn dan een werkstroom die een taak van de ene wachtrij naar de volgende doorgeeft, omdat elke stap van de taak is voltooid. Uw toepassing kan de taakstatus opslaan in het bericht en vervolgens blijven werken, in plaats van het bericht voor de volgende stap van de taak opnieuw in het rijrijzet. Houd er rekening mee dat elke bewerking **Van het updatebericht** meetelt voor het schaalbaarheidsdoel.

## <a name="application-architecture"></a>Toepassingsarchitectuur

Gebruik wachtrijen om uw toepassingsarchitectuur schaalbaar te maken. In de volgende lijsten worden enkele manieren weergegeven waarop u wachtrijen gebruiken om uw toepassing schaalbaarder te maken:  

- U wachtrijen gebruiken om werkachterstanden te maken voor het verwerken en probleemmeten van workloads in uw toepassing. U bijvoorbeeld aanvragen van gebruikers in de wachtrij plaatsen om intensief processorwerk uit te voeren, zoals het wijzigen van het formaat van geüploade afbeeldingen.
- U wachtrijen gebruiken om delen van uw toepassing los te koppelen, zodat u ze onafhankelijk schalen. Een webfrontend kan bijvoorbeeld enquêteresultaten van gebruikers in een wachtrij plaatsen voor latere analyse en opslag. U meer exemplaren van werkrollen toevoegen om de wachtrijgegevens naar behoefte te verwerken.  

## <a name="next-steps"></a>Volgende stappen

- [Schaalbaarheid en prestatiedoelen voor wachtrijopslag](scalability-targets.md)
- [Schaalbaarheid en prestatiedoelen voor standaardopslagaccounts](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Status- en foutcodes](/rest/api/storageservices/Status-and-Error-Codes2)
