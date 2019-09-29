---
title: Controle lijst voor prestaties en schaal baarheid Azure Storage | Microsoft Docs
description: Een controle lijst met bewezen procedures voor het gebruik van Azure Storage bij het ontwikkelen van toepassingen met een goede uitvoering.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e700cf04123bf02c1014aa418189221fbbb0b812
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71670929"
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Controle lijst voor prestaties en schaal baarheid Microsoft Azure Storage

Sinds de release van de Microsoft Azure Storage services heeft micro soft een aantal bewezen procedures ontwikkeld voor het gebruik van deze services op een goede manier, en dit artikel is bedoeld om het belangrijkste te consolideren in een lijst met checklist stijlen. De bedoeling van dit artikel is om toepassings ontwikkelaars te helpen controleren of ze bewezen procedures gebruiken met Azure Storage en om hen te helpen bij het identificeren van andere bewezen prak tijken die ze moeten overwegen. In dit artikel wordt niet elke mogelijke Optima Lise ring van prestaties en schaal baarheid behandeld, maar worden deze uitgesloten die klein zijn of niet breed van toepassing zijn. Voor zover het gedrag van de toepassing tijdens het ontwerpen kan worden voor speld, is het handig om deze in een vroeg stadium te houden om te voor komen dat ontwerpen die in prestatie problemen worden uitgevoerd.  

Elke toepassings ontwikkelaar die gebruikmaakt van Azure Storage moet de tijd nemen om dit artikel te lezen en te controleren of de toepassing de onderstaande bewezen procedures volgt.  

## <a name="checklist"></a>Controlelijst

In dit artikel worden de bewezen procedures in de volgende groepen ingedeeld. Bewezen procedures die van toepassing zijn op:  

* Alle Azure Storage services (blobs, tabellen, wacht rijen en bestanden)
* Blobs
* Tabellen
* Wachtrijen  

| Gereed | Onderwerp | Category | Vraag |
| --- | --- | --- | --- |
| &nbsp; | Alle services |Schaalbaarheids doelen |[Is uw toepassing ontworpen om te voor komen dat de schaalbaarheids doelen worden bereikt?](#subheading1) |
| &nbsp; | Alle services |Schaalbaarheids doelen |[Is uw naamgevings Conventie ontworpen om een betere taak verdeling te bieden?](#subheading47) |
| &nbsp; | Alle services |Netwerken |[Hebben apparaten aan de client zijde voldoende band breedte en lage latentie om de benodigde prestaties te verwezenlijken?](#subheading2) |
| &nbsp; | Alle services |Netwerken |[Hebben apparaten aan de client zijde een hoge mate van kwaliteit?](#subheading3) |
| &nbsp; | Alle services |Netwerken |[Bevindt de client toepassing zich in de buurt van het opslag account?](#subheading4) |
| &nbsp; | Alle services |Inhoudsdistributie |[Gebruikt u een CDN voor inhouds distributie?](#subheading5) |
| &nbsp; | Alle services |Directe client toegang |[Maakt u gebruik van SAS en CORS voor directe toegang tot opslag ruimte in plaats van proxy?](#subheading6) |
| &nbsp; | Alle services |Caching |[Worden de gegevens in de toepassing opgeslagen die herhaaldelijk worden gebruikt en worden deze zelden gewijzigd?](#subheading7) |
| &nbsp; | Alle services |Caching |[Worden uw toepassings batch updates (in de cache opgeslagen en vervolgens in grotere sets geüpload)?](#subheading8) |
| &nbsp; | Alle services |.NET-configuratie |[Hebt u uw client geconfigureerd voor het gebruik van een voldoende aantal gelijktijdige verbindingen?](#subheading9) |
| &nbsp; | Alle services |.NET-configuratie |[Hebt u .NET geconfigureerd voor het gebruik van een voldoende aantal threads?](#subheading10) |
| &nbsp; | Alle services |.NET-configuratie |[Gebruikt u .NET 4,5 of hoger, wat een verbeterde garbagecollection heeft?](#subheading11) |
| &nbsp; | Alle services |Parallelle uitvoering |[Hebt u gegarandeerd dat de parallelle kracht op de juiste wijze is gebonden, zodat u de client mogelijkheden of de schaalbaarheids doelen niet overbelast?](#subheading12) |
| &nbsp; | Alle services |Hulpprogramma's |[Gebruikt u de nieuwste versie van micro soft-client bibliotheken en-hulpprogram ma's?](#subheading13) |
| &nbsp; | Alle services |Nieuwe pogingen |[Gebruikt u een exponentiële uitstel-beleid voor opnieuw proberen voor het beperken van fouten en time-outs?](#subheading14) |
| &nbsp; | Alle services |Nieuwe pogingen |[Voor komt uw toepassing nieuwe pogingen voor niet-herstel bare fouten?](#subheading15) |
| &nbsp; | Blobs |Schaalbaarheids doelen |[Hebt u een groot aantal clients gelijktijdig toegang tot één object?](#subheading46) |
| &nbsp; | Blobs |Schaalbaarheids doelen |[Bevindt uw toepassing zich binnen de band breedte of het schaalbaarheids doel voor bewerkingen voor één BLOB?](#subheading16) |
| &nbsp; | Blobs |Blobs kopiëren |[Kopieert u blobs op een efficiënte manier?](#subheading17) |
| &nbsp; | Blobs |Blobs kopiëren |[Gebruikt u AzCopy voor bulk kopieën van blobs?](#subheading18) |
| &nbsp; | Blobs |Blobs kopiëren |[Gebruikt u Azure import/export om grote hoeveel heden gegevens over te dragen?](#subheading19) |
| &nbsp; | Blobs |Meta gegevens gebruiken |[Slaat u veelgebruikte meta gegevens over blobs op in hun meta gegevens?](#subheading20) |
| &nbsp; | Blobs |Snel uploaden |[Als u probeert een BLOB snel te uploaden, kunt u blokken parallel uploaden?](#subheading21) |
| &nbsp; | Blobs |Snel uploaden |[Kunt u blobs parallel uploaden wanneer u een groot aantal blobs wilt uploaden?](#subheading22) |
| &nbsp; | Blobs |Juiste BLOB-type |[Gebruikt u pagina-blobs of blok-blobs wanneer dit van toepassing is?](#subheading23) |
| &nbsp; | Tabellen |Schaalbaarheids doelen |[Benadert u de schaalbaarheids doelen voor entiteiten per seconde?](#subheading24) |
| &nbsp; | Tabellen |Configuratie |[Gebruikt u JSON voor uw tabel aanvragen?](#subheading25) |
| &nbsp; | Tabellen |Configuratie |[Hebt u Nagle uitgeschakeld om de prestaties van kleine aanvragen te verbeteren?](#subheading26) |
| &nbsp; | Tabellen |Tabellen en partities |[Hebt u uw gegevens goed gepartitioneerd?](#subheading27) |
| &nbsp; | Tabellen |Dynamische partities |[Vermijdt u alleen toevoegen en alleen laten voorafgaan door patronen?](#subheading28) |
| &nbsp; | Tabellen |Dynamische partities |[Worden uw toevoegingen/updates verspreid over verschillende partities?](#subheading29) |
| &nbsp; | Tabellen |Query bereik |[Hebt u uw schema ontworpen zodat punt query's in de meeste gevallen kunnen worden gebruikt en kunnen tabel query's spaarzaam worden gebruikt?](#subheading30) |
| &nbsp; | Tabellen |Query densiteit |[Worden in uw query's doorgaans alleen de rijen gescand en geretourneerd die door uw toepassing worden gebruikt?](#subheading31) |
| &nbsp; | Tabellen |De geretourneerde gegevens beperken |[Gebruikt u filteren om te voor komen dat entiteiten worden geretourneerd die niet nodig zijn?](#subheading32) |
| &nbsp; | Tabellen |De geretourneerde gegevens beperken |[Maakt u gebruik van projectie om te voor komen dat eigenschappen worden geretourneerd die niet nodig zijn?](#subheading33) |
| &nbsp; | Tabellen |Denormalisatie |[Hebt u uw gegevens genormaliseerd, zodat u inefficiënte query's of meerdere Lees aanvragen voor het ophalen van gegevens wilt voor komen?](#subheading34) |
| &nbsp; | Tabellen |Invoegen/bijwerken/verwijderen |[Worden er aanvragen voor batch verwerking die transactioneel moeten zijn of tegelijkertijd kunnen worden uitgevoerd om retouren te verminderen?](#subheading35) |
| &nbsp; | Tabellen |Invoegen/bijwerken/verwijderen |[Vermijdt u het ophalen van een entiteit alleen om te bepalen of INSERT of update moet worden aangeroepen?](#subheading36) |
| &nbsp; | Tabellen |Invoegen/bijwerken/verwijderen |[Moet u een reeks gegevens opslaan die regel matig worden opgehaald in één entiteit als eigenschappen in plaats van meerdere entiteiten?](#subheading37) |
| &nbsp; | Tabellen |Invoegen/bijwerken/verwijderen |[Voor entiteiten die altijd samen worden opgehaald en kunnen worden geschreven in batches (bijvoorbeeld time series-gegevens), kunt u het gebruik van blobs in plaats van tabellen overwegen?](#subheading38) |
| &nbsp; | Wachtrijen |Schaalbaarheids doelen |[Benadert u de schaalbaarheids doelen voor berichten per seconde?](#subheading39) |
| &nbsp; | Wachtrijen |Configuratie |[Hebt u Nagle uitgeschakeld om de prestaties van kleine aanvragen te verbeteren?](#subheading40) |
| &nbsp; | Wachtrijen |Berichtgrootte |[Worden uw berichten gecomprimeerd om de prestaties van de wachtrij te verbeteren?](#subheading41) |
| &nbsp; | Wachtrijen |Bulksgewijs ophalen |[Haalt u meerdere berichten op in één ' Get-bewerking '?](#subheading42) |
| &nbsp; | Wachtrijen |Polling frequentie |[Zoekt u regel matig genoeg om de waargenomen latentie van uw toepassing te verminderen?](#subheading43) |
| &nbsp; | Wachtrijen |Bericht bijwerken |[Gebruikt u UpdateMessage om de voortgang in de verwerking van berichten op te slaan, zodat het hele bericht niet opnieuw moet worden verwerkt als er een fout optreedt?](#subheading44) |
| &nbsp; | Wachtrijen |Architectuur |[Gebruikt u wacht rijen om uw volledige toepassing schaalbaar te maken door langlopende workloads uit het kritieke pad te houden en vervolgens onafhankelijk te schalen?](#subheading45) |

## <a name="allservices"></a>Alle services

In deze sectie vindt u bewezen procedures die van toepassing zijn op het gebruik van een van de Azure Storage services (blobs, tabellen, wacht rijen of bestanden).  

### <a name="subheading1"></a>Schaalbaarheids doelen

Azure Storage zichzelf heeft een limiet van 250 opslag accounts per regio per abonnement. Als u de limiet bereikt, kunt u geen opslagaccounts meer maken voor de betreffende combinatie van abonnement en regio.

Elk van de Azure Storage services heeft schaalbaarheids doelen voor capaciteit (GB), transactie frequentie en band breedte. Als uw toepassing een van de schaalbaarheids doelen benadert of overschrijdt, kunnen er meer trans acties of beperkingen optreden. Wanneer een opslag service uw toepassing beperkt, begint de service met het retour neren van de fout codes 503 Server bezet of time-out 500-bewerking voor sommige opslag transacties. In deze sectie wordt beschreven hoe u de algemene benadering kunt volgen om te omgaan met schaal baarheids doelen en met name bandbreedte schaalbaarheids doelen. Latere secties die betrekking hebben op afzonderlijke opslag Services, bespreken de schaalbaarheids doelen in de context van die specifieke service:  

* [BLOB-band breedte en-aanvragen per seconde](#subheading16)
* [Tabel entiteiten per seconde](#subheading24)
* [Wachtrij berichten per seconde](#subheading39)  

#### <a name="sub1bandwidth"></a>Doel van bandbreedte schaal baarheid voor alle services

Op het moment van schrijven, zijn de bandbreedte doelen in de VS voor een GRS-account (geo-redundante opslag) 10 gigabits per seconde (Gbps) voor inkomend verkeer (gegevens die worden verzonden naar het opslag account) en 20 Gbps voor uitgaand verkeer (gegevens die vanuit het opslag account worden verzonden). Voor een lokaal redundante opslag account (LRS) zijn de limieten hoger – 20 Gbps voor ingangs-en 30 Gbps voor uitgaand verkeer.  De limieten voor internationale band breedte zijn mogelijk lager en kunnen worden gevonden op onze [pagina schaalbaarheids doelen](https://msdn.microsoft.com/library/azure/dn249410.aspx).  Zie de koppelingen in nuttige bronnen hieronder voor meer informatie over de opties voor opslag redundantie.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>Wat u moet doen wanneer u een schaalbaarheids doel nadert

Als u de limiet hebt bereikt voor opslag accounts die u in een bepaalde combi natie van abonnement/regio kunt hebben, evalueert u uw toepassing en gebruik van opslag accounts en bepaalt u of er een van deze voor waarden van toepassing is.

* Opslag accounts gebruiken als niet-beheerde schijven en deze schijven toevoegen aan uw virtuele machines. In dit scenario raden we u aan [Managed disks](../../virtual-machines/windows/managed-disks-overview.md)te gebruiken, omdat ze schaal baarheid van opslag schijven voor u afhandelen zonder dat u afzonderlijke opslag accounts hoeft te maken en te beheren.
* Met behulp van één opslag account per klant, voor het doel van gegevens isolatie. In dit scenario raden we u aan opslag containers voor elke klant te gebruiken in plaats van een hele opslag account. Met Azure Storage kunt u nu op rollen gebaseerd toegangs beheer op basis van per [container](storage-auth-aad-rbac-portal.md)opgeven.
* Het gebruik van meerdere opslag accounts om te Shard voor een grotere schaal baarheid van binnenkomend/uitgaand/IOPS/capaciteit. In dit scenario raden we u aan de [verhoogde limieten](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) van standaard opslag accounts te gebruiken om het aantal opslag accounts te beperken dat vereist is voor uw werk belasting.

Als uw toepassing de schaalbaarheids doelen voor één opslag account nadert, kunt u een van de volgende benaderingen aannemen:  

* Bekijk de werk belasting die ervoor zorgt dat uw toepassing het schaalbaarheids doel benadert of overschrijdt. Kunt u het op verschillende manieren ontwerpen om minder band breedte of capaciteit of minder trans acties te gebruiken?
* Als een toepassing een van de schaalbaarheids doelen moet overschrijden, moet u meerdere opslag accounts maken en uw toepassings gegevens in deze meerdere opslag accounts partitioneren. Als u dit patroon gebruikt, moet u ervoor zorgen dat u uw toepassing ontwerpt, zodat u in de toekomst meer opslag accounts kunt toevoegen voor taak verdeling. Op het moment van schrijven kan elk Azure-abonnement Maxi maal 250 opslag accounts per regio bevatten (wanneer dit wordt geïmplementeerd met het Azure Resource Manager model).  Opslag accounts hebben ook geen andere kosten dan uw gebruik in termen van opgeslagen gegevens, gemaakte trans acties of overgedragen gegevens.
* Als uw toepassing de bandbreedte doelen heeft, kunt u overwegen gegevens in de client te comprimeren om de band breedte te verminderen die nodig is om de gegevens naar de opslag service te verzenden.  Dit kan band breedte besparen en de netwerk prestaties verbeteren, maar kan ook enkele negatieve gevolgen hebben.  U moet de gevolgen van de prestaties evalueren door de extra verwerkings vereisten voor het comprimeren en decomprimeren van gegevens in de client. Daarnaast kan het opslaan van gecomprimeerde gegevens lastiger zijn om problemen op te lossen, omdat het moeilijker is om opgeslagen gegevens weer te geven met behulp van standaard hulpprogramma's.
* Als uw toepassing de schaalbaarheids doelen bevindt, moet u ervoor zorgen dat u een exponentiële uitstel gebruikt voor nieuwe pogingen (Zie [nieuwe pogingen](#subheading14)).  Het is beter om ervoor te zorgen dat u de schaalbaarheids doelen nooit aanpakt (door gebruik te maken van een van de bovenstaande methoden), maar dit zorgt ervoor dat uw toepassing niet alleen een snelle poging doet, waardoor de beperking verergerd wordt.  

#### <a name="useful-resources"></a>Nuttige informatie

De volgende koppelingen bieden aanvullende informatie over de schaalbaarheids doelen:

* Zie [Azure Storage schaalbaarheids-en prestatie doelen](storage-scalability-targets.md) voor informatie over schaalbaarheids doelen.
* Zie [Azure storage replicatie](storage-redundancy.md) en het blog bericht [Azure Storage redundantie opties en lees toegang geografisch redundante opslag](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) voor informatie over opties voor opslag redundantie.
* Zie [Azure-prijzen](https://azure.microsoft.com/pricing/overview/)voor actuele informatie over prijzen voor Azure-Services.  

### <a name="subheading47"></a>Naamgevings regels voor partities

Azure Storage gebruikt een op bereik gebaseerd partitie schema om het systeem te schalen en te verdelen. De partitie sleutel (account + container + blob) wordt gebruikt voor het partitioneren van gegevens in bereiken en deze bereiken worden verdeeld over het hele systeem. Dit betekent dat naam conventies zoals lexicale ordening (bijvoorbeeld *mypayroll*, *myperformance*, *myemployees*enzovoort) of het gebruik van tijds tempels (*log20160101*, *log20160102*, *log20160102*, enzovoort) worden uitgeleend de partities kunnen zich op dezelfde partitie server bevinden, totdat een bewerking voor taak verdeling in kleinere bereiken wordt gesplitst. Alle blobs in een container kunnen bijvoorbeeld worden bediend door één server totdat de belasting van deze blobs verdere herverdeling van de partitielay-bereiken vereist. Op dezelfde manier kan een groep met zeer bewaarde accounts met hun namen die zijn gerangschikt in lexicale volg orde worden bediend door één server totdat de belasting van een of meer van deze accounts moet worden verdeeld over meerdere partities servers. Elke bewerking voor taak verdeling kan van invloed zijn op de latentie van opslag aanroepen tijdens de bewerking. De mogelijkheid om een plotselinge hoeveelheid verkeer naar een partitie af te handelen, wordt beperkt door de schaal baarheid van een enkele partitie server totdat de taak verdeling wordt geactiveerd en het partitie sleutel bereik opnieuw wordt gebalanceerd.

U kunt een aantal aanbevolen procedures volgen om de frequentie van dergelijke bewerkingen te verminderen.  

* Gebruik, indien mogelijk, een grotere put-BLOB of plaatsings blok formaten (meer dan 4 MiB voor standaard accounts en groter dan 256 KiB voor Premium-accounts) om een blok-blob met hoge door Voer (HTBB) te activeren. HTBB biedt hoge prestatie opname die niet wordt beïnvloed door de naam van een partitie.
* Bekijk de naam Conventie die u gebruikt voor accounts, containers, blobs, tabellen en wacht rijen. Overweeg het vooraf bepalen van namen van accounts, containers of blobs met een hash van drie cijfers met een hash-functie die het beste bij uw behoeften past.  
* Als u uw gegevens indeelt met behulp van tijds tempels of numerieke id's, moet u ervoor zorgen dat u geen gebruik maakt van een verkeers patroon voor alleen toevoegen (of alleen laten voorafgaan door). Deze patronen zijn niet geschikt voor een partitie systeem op basis van een bereik en kunnen leiden tot al het verkeer naar één partitie en het systeem beperken tegen het effectief laden van taken. Als u bijvoorbeeld dagelijkse bewerkingen hebt die gebruikmaken van een blob-object met een tijds tempel zoals *jjjmmdd*, wordt al het verkeer voor die dagelijkse bewerking omgeleid naar één object, dat door één partitie server wordt geleverd. Kijk of de limieten per Blob en per partitie Maxi maal aan uw behoeften voldoen en overweeg deze bewerking te verbreken in meerdere blobs, indien nodig. En als u tijdreeks gegevens in uw tabellen opslaat, kan al het verkeer worden omgeleid naar het laatste deel van de sleutel naam ruimte. Als u tijds tempels of numerieke Id's moet gebruiken, maakt u een voor voegsel van de ID met een hash van drie cijfers, of in het geval van het voor voegsel van tijds tempels het seconden gedeelte van de tijd, zoals *ssyyyymmdd*. Als er regel matig een lijst-en query bewerking wordt uitgevoerd, kiest u een hash-functie die uw aantal query's beperkt. In andere gevallen kan een wille keurig voor voegsel voldoende zijn.  
* Voor meer informatie over het partitie schema dat wordt gebruikt in azure Storage raadpleegt [u Azure Storage: Een Maxi maal beschik bare Cloud opslag service](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf)met sterke consistentie.

### <a name="networking"></a>Netwerken

Hoewel de API aanroept, hebben vaak de fysieke netwerk beperkingen van de toepassing een aanzienlijke invloed op de prestaties. Hieronder worden enkele beperkingen beschreven die gebruikers kunnen tegen komen.  

#### <a name="client-network-capability"></a>Client netwerk mogelijkheid

##### <a name="subheading2"></a>Vracht

Voor band breedte is het probleem vaak de mogelijkheden van de client. Een voor beeld: Hoewel één opslag account 10 Gbps of meer van inkomend verkeer kan verwerken (Zie [bandbreedte schaal baarheids doelen](#sub1bandwidth)), is de netwerk snelheid in een ' kleine ' Azure worker-rolinstantie alleen geschikt voor ongeveer 100 Mbps. Grotere Azure-instanties hebben Nic's met een grotere capaciteit. u moet dus overwegen een grotere instantie of meer Vm's te gebruiken als u hogere netwerk limieten van één computer nodig hebt. Als u toegang hebt tot een opslag service vanuit een on-premises toepassing, geldt dezelfde regel: inzicht in de netwerk mogelijkheden van het client apparaat en de netwerk verbinding met de Azure Storage locatie en verbeter deze indien nodig of ontwerp uw toepassing om binnen hun mogelijkheden te werken.  

##### <a name="subheading3"></a>Koppelings kwaliteit

Net als bij elk netwerk gebruik moet u er rekening mee houden dat de netwerk voorwaarden die fouten veroorzaken en pakket verlies leiden tot een trage door voer.  Het gebruik van WireShark of NetMon kan helpen bij het vaststellen van dit probleem.  

##### <a name="useful-resources"></a>Nuttige informatie

Zie [Windows VM](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) -grootten of- [VM](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)-grootten voor meer informatie over de grootte van virtuele machines en de toegewezen band breedte.  

#### <a name="subheading4"></a>Locatie

In een gedistribueerde omgeving biedt de-client bij de server de beste prestaties. Voor toegang tot Azure Storage met de laagste latentie bevindt de beste locatie voor uw client zich in dezelfde Azure-regio. Als u bijvoorbeeld een Azure-website hebt die gebruikmaakt van Azure Storage, moet u deze binnen één regio (bijvoorbeeld VS-West of Azië-Zuidoost) zoeken. Dit vermindert de latentie en de kosten: op het moment van schrijven is het bandbreedte gebruik in één regio gratis.  

Als uw client toepassingen niet worden gehost in azure (zoals apps voor mobiele apparaten of on-premises Enter prise Services), wordt het opslag account in een regio in de buurt van de apparaten die toegang krijgen, in het algemeen minder latentie. Als uw clients breed worden gedistribueerd (bijvoorbeeld sommige in Noord-Amerika en sommige in Europa), kunt u overwegen meerdere opslag accounts te gebruiken: één in een regio Noord-Amerikaanse en één in een Europese regio. Dit helpt de latentie te verlagen voor gebruikers in beide regio's. Deze methode is eenvoudiger te implementeren als de gegevens die de app opslaat, specifiek zijn voor afzonderlijke gebruikers en er geen replicatie van gegevens tussen opslag accounts nodig is.  Voor een brede inhouds distributie wordt een CDN aanbevolen. Zie de volgende sectie voor meer informatie.  

### <a name="subheading5"></a>Inhouds distributie

Soms moet een toepassing dezelfde inhoud op veel gebruikers aanbieden (bijvoorbeeld een product demo video die wordt gebruikt op de start pagina van een website), die zich in dezelfde of meerdere regio's bevindt. In dit scenario moet u een Content Delivery Network (CDN) gebruiken, zoals Azure CDN, en de CDN gebruikt Azure Storage als oorsprong van de gegevens. In tegens telling tot een Azure Storage-account dat bestaat in één regio en die geen inhoud met lage latentie kan leveren aan andere regio's, Azure CDN servers gebruiken in meerdere data centers over de hele wereld. Daarnaast kan een CDN veel hogere uitwijkings limieten ondersteunen dan één opslag account.  

Zie [Azure CDN](https://azure.microsoft.com/services/cdn/)voor meer informatie over Azure CDN.  

### <a name="subheading6"></a>SAS en CORS gebruiken

Wanneer u code, zoals Java script in de webbrowser van een gebruiker of een mobiele telefoon-app, wilt machtigen om toegang te krijgen tot gegevens in Azure Storage, is het gebruik van een toepassing in een ondernemingsrol als een proxy: het apparaat van de gebruiker wordt geverifieerd met de webrol , die op zijn beurt toestemming verleent voor toegang tot opslag bronnen. Op deze manier kunt u voor komen dat u de sleutels van uw opslag account op onveilige apparaten weergeeft. Dit plaatst echter een aanzienlijke overhead op de webrol, omdat alle gegevens die tussen het apparaat van de gebruiker en de opslag service worden overgedragen via de webrole moeten passeren. U kunt voor komen dat een webrol als proxy voor de opslag service wordt gebruikt met behulp van Shared Access signatures (SAS), soms in combi natie met cross-Origin resource sharing headers (CORS). Met SAS kunt u toestaan dat het apparaat van uw gebruiker aanvragen rechtstreeks naar een opslag service doet door middel van een beperkt toegangs token. Als een gebruiker bijvoorbeeld een foto wil uploaden naar uw toepassing, kan uw webrol een SAS-token genereren en verzenden naar het apparaat van de gebruiker. Dit geeft de volgende 30 minuten toestemming om te schrijven naar een specifieke BLOB of container (waarna het SAS-token verloopt).

Normaal gesp roken staat een browser geen Java script toe op een pagina die wordt gehost door een website op het ene domein om specifieke bewerkingen, zoals een ' PUT ', uit te voeren op een ander domein. Als u bijvoorbeeld als host fungeert voor een webrole op ' contosomarketing.cloudapp.net ' en Java script aan de client zijde wilt gebruiken om een BLOB te uploaden naar uw opslag account in ' contosoproducts.blob.core.windows.net ', wordt deze bewerking door de browser ' hetzelfde Origin-beleid ' niet meer ondersteund. CORS is een browser functie waarmee het doel domein (in dit geval het opslag account) kan communiceren met de browser waarmee aanvragen worden vertrouwd die afkomstig zijn van het bron domein (in dit geval de webfunctie).  

Beide technologieën kunnen u helpen bij het voor komen van onnodige belasting (en knel punten) in uw webtoepassing.  

#### <a name="useful-resources"></a>Nuttige informatie

Zie voor meer informatie over SA'S [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](storage-sas-overview.md).  

Zie [ondersteuning voor het gebruik van cors (cross-Origin Resource Sharing) voor de Azure Storage services](https://msdn.microsoft.com/library/azure/dn535601.aspx)voor meer informatie over cors.  

### <a name="caching"></a>Caching

#### <a name="subheading7"></a>Gegevens ophalen

Over het algemeen is het ophalen van gegevens uit een service een betere plaats dan twee keer. Bekijk het voor beeld van een MVC-webtoepassing die wordt uitgevoerd in een webrole die al een 50-MB-blob van de opslag service heeft opgehaald om als inhoud aan een gebruiker te dienen. De toepassing kan vervolgens dezelfde BLOB ophalen telkens wanneer een gebruiker deze aanvraagt, of deze lokaal opslaan in de schijf en de versie in de cache voor volgende gebruikers aanvragen opnieuw gebruiken. Wanneer een gebruiker de gegevens aanvraagt, kan de toepassing bovendien een voorwaardelijke header ontvangen voor wijzigings tijd, waardoor de gehele BLOB niet meer kan worden opgehaald. U kunt dit patroon Toep assen om met tabel entiteiten te werken.  

In sommige gevallen kunt u besluiten dat uw toepassing kan aannemen dat de BLOB geldig blijft gedurende een korte periode nadat deze is opgehaald, en dat tijdens deze periode niet moet worden gecontroleerd of de blob is gewijzigd.

Configuratie, zoek acties en andere gegevens die altijd door de toepassing worden gebruikt, zijn uitstekende kandidaten voor het opslaan in de cache.  

Zie [voorwaardelijke kopteksten voor BLOB service bewerkingen opgeven](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)voor meer informatie over voorwaardelijke down loads.  

#### <a name="subheading8"></a>Gegevens uploaden in batches

In sommige toepassings scenario's kunt u gegevens lokaal samen voegen, en deze vervolgens periodiek uploaden in een batch in plaats van elk stukje gegevens onmiddellijk te uploaden. Zo kan een webtoepassing een logboek bestand van activiteiten bewaren: de toepassing kan gegevens van elke activiteit uploaden als een tabel entiteit (waarvoor veel opslag bewerkingen zijn vereist), of de gegevens van de activiteit kunnen worden opgeslagen in een lokaal logboek bestand en vervolgens Upload regel matig alle activiteiten Details als een bestand met scheidings tekens naar een blob. Als elke logboek vermelding 1 KB groot is, kunt u duizend tallen uploaden in één put-BLOB-trans actie (u kunt een BLOB van Maxi maal 64 MB in één trans actie uploaden). Als de lokale computer vastloopt voordat het uploaden is geslaagd, verliest u mogelijk enkele logboek gegevens: de ontwikkelaar van de toepassing moet ontwerpen voor de mogelijkheid van client apparaten of upload fouten.  Als de gegevens van de activiteit moeten worden gedownload voor TimeSpans (niet alleen voor één activiteit), worden blobs aanbevolen voor tabellen.

### <a name="net-configuration"></a>.NET-configuratie

Als u de .NET Framework gebruikt, worden in deze sectie verschillende snelle configuratie-instellingen weer gegeven die u kunt gebruiken om belang rijke prestatie verbeteringen aan te brengen.  Als u andere talen gebruikt, controleert u of er soort gelijke concepten van toepassing zijn in de taal die u hebt gekozen.  

#### <a name="subheading9"></a>Standaard verbindings limiet verhogen

In .NET verhoogt de volgende code de standaard verbindings limiet (meestal 2 in een client omgeving of 10 in een server omgeving) tot 100. Normaal gesp roken moet u de waarde instellen op ongeveer het aantal threads dat door uw toepassing wordt gebruikt.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

U moet de verbindings limiet instellen voordat u verbindingen opent.  

Raadpleeg de documentatie van die taal voor andere programmeer talen om te bepalen hoe de verbindings limiet moet worden ingesteld.  

Zie voor meer informatie het blog bericht [webservices: Gelijktijdige verbindingen](https://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx).  

#### <a name="subheading10"></a>Minimum aantal threads verhogen als synchrone code wordt gebruikt met asynchrone taken

Met deze code wordt het minimum aantal threads in de thread groep verhoogd:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Zie de [methode thread pool. onSetMinThreads](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx)voor meer informatie.  

#### <a name="subheading11"></a>Profiteer van .NET 4,5 en een hogere Garbage Collection

Gebruik .NET 4,5 of hoger voor de client toepassing om te profiteren van de prestatie verbeteringen in de garbagecollection van de server.

Zie het artikel [een overzicht van prestatie verbeteringen in .net 4,5](https://msdn.microsoft.com/magazine/hh882452.aspx)voor meer informatie.  

### <a name="subheading12"></a>Niet-gebonden parallellisme

Parallelle uitvoering kan ideaal zijn voor prestaties, wees voorzichtig met het gebruik van een ongebonden parallelle verbinding (geen limiet voor het aantal threads en/of parallelle aanvragen) voor het uploaden of downloaden van gegevens, waarbij meerdere werk rollen worden gebruikt voor toegang tot meerdere partities (containers, wacht rijen of tabel partities) in hetzelfde opslag account of om toegang te krijgen tot meerdere items in dezelfde partitie. Als de parallellisme niet is gebonden, kan uw toepassing de mogelijkheden van het client apparaat overschrijden of de schaalbaarheids doelen van het opslag account, wat resulteert in langere latentie en beperking.  

### <a name="subheading13"></a>Client bibliotheken en hulpprogram ma's voor opslag

Gebruik altijd de nieuwste door micro soft meegeleverde client bibliotheken en hulpprogram ma's. Op het moment van schrijven zijn er client bibliotheken beschikbaar voor .NET, Windows Phone, Windows Runtime, Java en C++, evenals voor beelden van bibliotheken voor andere talen. Daarnaast heeft micro soft power shell-cmdlets en Azure CLI-opdrachten uitgebracht voor het werken met Azure Storage. Micro soft ontwikkelt deze hulpprogram ma's actief met het oog op de prestaties, houdt ze up-to-date met de nieuwste service versies en zorgt ervoor dat ze een groot aantal van de bewezen beproefde procedures intern verwerken.  

### <a name="retries"></a>Nieuwe pogingen

#### <a name="subheading14"></a>Problemen met beperking en server bezet

In sommige gevallen kan het voor komen dat de opslag service uw toepassing kan vertragen of dat het niet mogelijk is om de aanvraag te verzenden vanwege enige tijdelijke voor waarde en het bericht ' 503 Server bezet ' of ' 500 timeout ' te retour neren.  Dit kan gebeuren als uw toepassing een van de schaalbaarheids doelen nadert of als het systeem de gepartitioneerde gegevens opnieuw onderverdelen zodat deze sneller kunnen worden door gegeven.  De client toepassing moet meestal opnieuw proberen de bewerking uit te voeren die een dergelijke fout veroorzaakt: als u de aanvraag later opnieuw kunt proberen. Als de opslag service echter uw toepassing beperkt, omdat deze de schaalbaarheids doelen overschrijdt, of zelfs als de service om een andere reden niet in staat is om de aanvraag te verwerken, is het probleem doorgaans erger. Daarom moet u een exponentiële back-up gebruiken (de client bibliotheken zijn standaard ingesteld voor dit gedrag). Uw toepassing kan bijvoorbeeld na 2 seconden of 4 seconden, vervolgens 10 seconden, vervolgens 30 seconden, opnieuw worden geprobeerd en vervolgens volledig weer geven. Dit gedrag leidt ertoe dat uw toepassing de belasting van de service aanzienlijk verlaagt in plaats van problemen op te lossen.  

Connectiviteits fouten kunnen onmiddellijk opnieuw worden geprobeerd, omdat ze niet het gevolg zijn van een beperking en naar verwachting tijdelijk zijn.  

#### <a name="subheading15"></a>Niet-herstel bare fouten

De client bibliotheken zijn op de hoogte van de fouten die opnieuw kunnen worden uitgevoerd en niet. Als u echter uw eigen code schrijft op basis van de opslag REST API, moet u er rekening mee houden dat er fouten zijn die u niet opnieuw moet proberen: bijvoorbeeld een 400 (ongeldige aanvraag) geeft aan dat de client toepassing een aanvraag heeft verzonden die niet kan worden verwerkt omdat deze niet in een verwachte vorm. Als deze aanvraag opnieuw wordt verzonden, wordt elke keer hetzelfde antwoord geretourneerd, zodat er geen punt in de nieuwe poging wordt gedaan. Als u uw eigen code op basis van de opslag REST API schrijft, moet u zich bewust zijn van de betekenis van de fout codes en de juiste manier om een nieuwe poging te doen (of niet).  

#### <a name="useful-resources"></a>Nuttige informatie

Zie [status-en fout codes](https://msdn.microsoft.com/library/azure/dd179382.aspx) op de Microsoft Azure website voor meer informatie over opslag fout codes.  

## <a name="blobs"></a>Blobs

Naast de bewezen procedures voor [alle services](#allservices) die eerder zijn beschreven, zijn de volgende bewezen procedures specifiek van toepassing op de BLOB service.  

### <a name="blob-specific-scalability-targets"></a>BLOB-specifieke schaalbaarheids doelen

#### <a name="subheading46"></a>Meerdere clients hebben gelijktijdig toegang tot één object

Als u een groot aantal clients tegelijkertijd toegang tot één object wilt geven, moet u overwegen per object en de schaalbaarheids doelen voor het opslag account. Het exacte aantal clients dat toegang heeft tot een enkel object, is afhankelijk van factoren zoals het aantal clients tegelijk dat het object aanvraagt, de grootte van het object, de netwerk omstandigheden, enzovoort.

Als het object kan worden gedistribueerd via een CDN, zoals afbeeldingen of Video's van een website, moet u een CDN gebruiken. [Hier](#subheading5)weer geven.

In andere scenario's, zoals weten schappelijke simulaties waarin de gegevens vertrouwelijk zijn, hebt u twee opties. De eerste is om de toegang van uw werk belasting te spreiden, zodat het object gedurende een bepaalde tijd toegankelijk is en tegelijkertijd wordt geopend. U kunt het object ook tijdelijk kopiëren naar meerdere opslag accounts, waardoor het totale aantal IOPS per object en tussen opslag accounts niet groter wordt. In beperkte tests hebben we vastgesteld dat ongeveer 25 Vm's tegelijkertijd een BLOB van 100 GB tegelijk kunnen downloaden (elke virtuele machine heeft gelijktijdig de down load met 32-threads). Als u 100-clients hebt die toegang nodig hebben tot het object, kopieert u het eerst naar een tweede opslag account en hebben de eerste 50 Vm's toegang tot de eerste Blob en de tweede 50 Vm's toegang tot de tweede blob. De resultaten variëren, afhankelijk van het gedrag van uw toepassingen, zodat u dit tijdens het ontwerpen moet testen. 

#### <a name="subheading16"></a>Band breedte en bewerkingen per BLOB

U kunt lezen van of schrijven naar één blob met een maximum van 60 MB/seconde (dit is ongeveer 480 Mbps, wat de mogelijkheden van veel client-side netwerken overschrijdt (inclusief de fysieke NIC op het client apparaat). Daarnaast ondersteunt één BLOB Maxi maal 500 aanvragen per seconde. Als u meerdere clients hebt die dezelfde BLOB moeten lezen en u deze limieten mogelijk overschrijdt, kunt u overwegen om een CDN te gebruiken voor het distribueren van de blob.  

Zie [Azure Storage schaal baarheid en prestatie doelen](storage-scalability-targets.md)voor meer informatie over de doel doorvoer voor blobs.  

### <a name="copying-and-moving-blobs"></a>Blobs kopiëren en verplaatsen

#### <a name="subheading17"></a>BLOB kopiëren

De opslag REST API versie 2012-02-12 heeft de handige mogelijkheid voor het kopiëren van Blobs over accounts geïntroduceerd: een client toepassing kan de opslag service de instructie geven om een BLOB uit een andere bron te kopiëren (mogelijk in een ander opslag account) en de service vervolgens laten uitvoeren de kopie wordt asynchroon gekopieerd. Hierdoor kan de band breedte die nodig is voor de toepassing aanzienlijk verminderen wanneer u gegevens migreert vanuit andere opslag accounts, omdat u de gegevens niet hoeft te downloaden en te uploaden.  

Het is echter wel zo dat bij het kopiëren tussen opslag accounts geen garantie is op het moment dat de kopie wordt voltooid. Als uw toepassing snel een BLOB-kopie moet volt ooien onder uw besturings element, is het wellicht beter om de BLOB te kopiëren door deze te downloaden naar een virtuele machine en deze vervolgens te uploaden naar de bestemming.  Zorg ervoor dat de kopie wordt uitgevoerd door een virtuele machine die wordt uitgevoerd in dezelfde Azure-regio, anders is het mogelijk dat het kopiëren van de Kopieer prestaties van invloed is op de volledige voorspel baarheid.  Daarnaast kunt u de voortgang van een asynchroon kopiëren via een programma bewaken.  

Kopieën binnen hetzelfde opslag account zelf worden meestal snel voltooid.  

Zie [BLOB kopiëren](https://msdn.microsoft.com/library/azure/dd894037.aspx)voor meer informatie.  

#### <a name="subheading18"></a>AzCopy gebruiken

Het Azure Storage-team heeft een opdracht regel programma ' AzCopy ' uitgebracht dat is bedoeld om te helpen bij het bulksgewijs overdragen van veel blobs naar, van en tussen opslag accounts.  Dit hulp programma is geoptimaliseerd voor dit scenario en kan hoge overdrachts snelheden bezorgen.  Het gebruik ervan wordt aangemoedigd voor het bulksgewijs uploaden, downloaden en kopiëren van scenario's. Zie [gegevens overdragen met het AzCopy-opdracht regel programma](storage-use-azcopy.md)voor meer informatie over IT en downloaden.  

#### <a name="subheading19"></a>Azure import/export-service

Voor grote hoeveel heden gegevens (meer dan 1 TB) biedt de Azure Storage de import/export-service, waarmee het uploaden en downloaden van Blob-opslag mogelijk is via het verzenden van harde schijven.  U kunt uw gegevens op een harde schijf plaatsen en deze naar micro soft verzenden voor het uploaden, of een lege harde schijf naar micro soft verzenden om gegevens te downloaden.  Zie [De Microsoft Azure Import/Export-service gebruiken om gegevens over te brengen naar Blob Storage](../storage-import-export-service.md) voor meer informatie.  Dit kan veel efficiënter zijn dan het uploaden/downloaden van dit gegevens volume via het netwerk.  

### <a name="subheading20"></a>Meta gegevens gebruiken

Het Blob service ondersteunt Head-aanvragen, die meta gegevens over de BLOB kunnen bevatten. Als uw toepassing bijvoorbeeld de EXIF-gegevens uit een foto nodig heeft, kan deze de foto ophalen en uitpakken. Om band breedte te besparen en de prestaties te verbeteren, kan uw toepassing de EXIF-gegevens in de meta gegevens van de BLOB opslaan wanneer de toepassing de foto uploadt: u kunt de EXIF-gegevens in de meta gegevens ophalen met behulp van een HEAD-aanvraag, een aanzienlijke band breedte opslaan en de de verwerkings tijd die nodig is om de EXIF-gegevens te extra heren wanneer de BLOB wordt gelezen. Dit is handig in scenario's waarin u alleen de meta gegevens nodig hebt, en niet de volledige inhoud van een blob.  Er kunnen Maxi maal 8 KB aan meta gegevens per BLOB worden opgeslagen (de service accepteert geen aanvragen om meer dan dat op te slaan), dus als de gegevens niet binnen die grootte passen, kunt u deze methode mogelijk niet gebruiken.  

### <a name="rapid-uploading"></a>Snel uploaden

Als u de blobs snel wilt uploaden, is de eerste vraag die moet worden beantwoord: bent u een BLOB of een aantal?  Gebruik de onderstaande richt lijnen om te bepalen welke methode het meest geschikt is voor uw scenario.  

#### <a name="subheading21"></a>Snel één grote BLOB uploaden

Als u snel één grote BLOB wilt uploaden, moet uw client toepassing zijn blokken of pagina's parallel uploaden (mindful van de schaalbaarheids doelen voor afzonderlijke blobs en het opslag account als geheel).  De officiële door micro soft verschafte RTM-opslag-client bibliotheken (.NET, Java) hebben de mogelijkheid om dit te doen.  Gebruik voor elk van de bibliotheken het onder opgegeven object/eigenschap om het niveau van gelijktijdigheid in te stellen:  

* .NET: Stel ParallelOperationThreadCount in op een BlobRequestOptions-object dat moet worden gebruikt.
* Java/Android: Use BlobRequestOptions.setConcurrentRequestCount()
* Node. js: Gebruik parallelOperationThreadCount op de aanvraag opties of op de Blob service.
* C++: Gebruik de methode blob_request_options:: set_parallelism_factor.

#### <a name="subheading22"></a>Snel veel blobs uploaden

Als u snel een groot aantal blobs wilt uploaden, uploadt u blobs parallel. Dit is sneller dan het uploaden van één BLOB tegelijk met parallelle blok uploads, omdat het uploaden over meerdere partities van de opslag service wordt verdeeld. Eén BLOB ondersteunt alleen een door Voer van 60 MB/seconde (ongeveer 480 Mbps). Op het moment van schrijven ondersteunt een LRS-account tot 20 Gbps ingang, wat veel meer is dan de door Voer die door een afzonderlijke BLOB wordt ondersteund.  [AzCopy](#subheading18) voert standaard gelijktijdig uploads uit en wordt aanbevolen voor dit scenario.  

### <a name="subheading23"></a>Het juiste type BLOB kiezen

Azure Storage ondersteunt twee typen blobs: *pagina* -blobs en *blok* -blobs. Voor een bepaald gebruiks scenario is uw keuze van het type blob van invloed op de prestaties en schaal baarheid van uw oplossing. Blok-blobs zijn geschikt wanneer u grote hoeveel heden gegevens efficiënt wilt uploaden: een client toepassing moet bijvoorbeeld mogelijk Foto's of Video's uploaden naar Blob Storage. Pagina-blobs zijn geschikt als de toepassing wille keurige schrijf bewerkingen moet uitvoeren op de gegevens: Azure-Vhd's worden bijvoorbeeld opgeslagen als pagina-blobs.  

Zie voor meer informatie [over blok-blobs, toevoeg-blobs en pagina](https://msdn.microsoft.com/library/azure/ee691964.aspx)-blobs.  

## <a name="tables"></a>Tabellen

Naast de bewezen procedures voor [alle services](#allservices) die eerder zijn beschreven, zijn de volgende bewezen procedures specifiek van toepassing op de tabel service.  

### <a name="subheading24"></a>Tabel-specifieke schaalbaarheids doelen

Naast de beperkingen van de band breedte van een hele opslag account, hebben tabellen de volgende specifieke schaalbaarheids limiet.  Het systeem houdt de taak verdeling uit naarmate het verkeer toeneemt, maar als uw verkeer plotselinge bursts heeft, kunt u dit volume van de door Voer niet direct ophalen.  Als uw patroon bursts bevat, moet u rekening houden met bandbreedte beperking en/of time-outs tijdens de burst, omdat de opslag service automatisch uw tabel verdeelt.  Een trage beveiliging heeft doorgaans betere resultaten naarmate de systeem tijd de taak verdeling op de juiste manier geeft.  

#### <a name="entities-per-second-storage-account"></a>Entiteiten per seconde (opslag account)

De schaalbaarheids limiet voor het openen van tabellen is Maxi maal 20.000 entiteiten (1 KB elk) per seconde voor een account.  In het algemeen geldt dat elke entiteit die wordt ingevoegd, bijgewerkt, verwijderd of gescand telt naar dit doel.  Een batch invoegen die 100 entiteiten bevat, telt dus als 100 entiteiten.  Een query die 1000 entiteiten scant en vijf keer als 1000 entiteiten retourneert.  

#### <a name="entities-per-second-partition"></a>Entiteiten per seconde (partitie)

Binnen één partitie zijn de schaalbaarheids doelen voor het openen van tabellen 2.000 entiteiten (elke 1 KB per seconde), met dezelfde tellingen zoals beschreven in de vorige sectie.  

### <a name="configuration"></a>Configuratie

In deze sectie vindt u verschillende snelle configuratie-instellingen die u kunt gebruiken om belang rijke prestatie verbeteringen in de Table-service aan te brengen:  

#### <a name="subheading25"></a>JSON gebruiken

Vanaf de Storage Service versie 2013-08-15 ondersteunt de Table-service het gebruik van JSON in plaats van de XML-AtomPub-indeling voor het overdragen van tabel gegevens. Dit kan Payload-grootten tot 75% verminderen en kan de prestaties van uw toepassing aanzienlijk verbeteren.

Zie voor meer informatie de post [Microsoft Azure-tabellen: Introductie van](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) de [indeling JSON en payload voor Table service-bewerkingen](https://msdn.microsoft.com/library/azure/dn535600.aspx).

#### <a name="subheading26"></a>Nagle uitschakelen

Het Nagle-algoritme is breed geïmplementeerd in TCP/IP-netwerken om de netwerk prestaties te verbeteren. Het is echter niet optimaal in alle omstandigheden (zoals zeer interactieve omgevingen). Voor Azure Storage heeft Nagle het algoritme een negatieve invloed op de prestaties van aanvragen voor de tabel-en wachtrij Services, en moet u dit indien mogelijk uitschakelen.

### <a name="schema"></a>Schema

Hoe u uw gegevens opstelt en query's uitvoert, is de grootste enkele factor die van invloed is op de prestaties van de tabel service. Hoewel elke toepassing verschilt, wordt in deze sectie een overzicht van enkele algemene bewezen procedures besproken die betrekking hebben op:  

* Tabel ontwerp
* Efficiënte query's
* Efficiënte gegevens updates  

#### <a name="subheading27"></a>Tabellen en partities

Tabellen worden onderverdeeld in partities. Elke entiteit die is opgeslagen in een partitie deelt dezelfde partitie sleutel en heeft een unieke rijdefinitie om deze te identificeren binnen die partitie. Partities bieden voor delen, maar kunnen ook schaal baarheids limieten introduceren.  

* Voordelen: U kunt entiteiten in dezelfde partitie bijwerken in één atomische, batch-trans actie die Maxi maal 100 afzonderlijke opslag bewerkingen bevat (limiet van 4 MB totale grootte). Uitgaande van het aantal entiteiten dat moet worden opgehaald, kunt u ook de gegevens binnen één partitie efficiënter opvragen dan gegevens die partities omspannen (hoewel er nog meer aanbevelingen worden gedaan voor het uitvoeren van query's in tabel gegevens).
* Schaal baarheids limiet: De toegang tot entiteiten die zijn opgeslagen in één partitie, kan niet gelijkmatig worden verdeeld, omdat partities atomische batch transacties ondersteunen. Daarom is het schaalbaarheids doel voor een afzonderlijke tabel partitie lager dan voor de tabel service als geheel.  

Vanwege deze kenmerken van tabellen en partities moet u de volgende ontwerp principes aannemen:  

* Gegevens die door uw client toepassing regel matig worden bijgewerkt of in dezelfde logische werk eenheid worden opgevraagd, moeten zich in dezelfde partitie bevinden.  Dit komt mogelijk doordat uw toepassing de schrijf bewerkingen samenvoegt of omdat u gebruik wilt maken van atomische batch bewerkingen.  Ook kunnen gegevens in één partitie efficiënter worden opgevraagd in één query dan gegevens tussen de verschillende partities.
* Gegevens die uw client toepassing niet invoegt/bijwerkt of een query uitvoert in dezelfde logische werk eenheid (één query of batch-update), moeten zich bevinden in afzonderlijke partities.  Een belang rijke opmerking is dat er geen limiet is voor het aantal partitie sleutels in één tabel, waardoor miljoenen partitie sleutels geen probleem zijn en niet van invloed zijn op de prestaties.  Als uw toepassing bijvoorbeeld een populaire website is met gebruikers aanmelding, met behulp van de gebruikers-ID, kan de partitie sleutel een goede keuze zijn.  

#### <a name="hot-partitions"></a>Dynamische partities

Een hot-partitie is een een niet-proportioneel percentage van het verkeer naar een account en kan niet worden gebalanceerd omdat het een enkele partitie is.  In het algemeen worden er op een van de volgende twee manieren dynamische partities gemaakt:  

##### <a name="subheading28"></a>Alleen patronen voor alleen-en laten voorafgaan door toevoegen

Het patroon ' alleen toevoegen ' is een waarbij alle (of bijna alle) verkeer naar een gegeven PK toeneemt en afneemt op basis van de huidige tijd.  Een voor beeld hiervan is als uw toepassing de huidige datum gebruikt als partitie sleutel voor logboek gegevens.  Dit resulteert in alle invoegingen van de laatste partitie in de tabel en het systeem kan het saldo niet laden omdat alle schrijf bewerkingen naar het einde van de tabel gaan.  Als het volume van het verkeer naar die partitie het schaalbaarheids doel op partitie niveau overschrijdt, resulteert dit in een vertraging.  Het is beter om ervoor te zorgen dat verkeer wordt verzonden naar meerdere partities om taak verdeling in te scha kelen voor de aanvragen in uw tabel.  

##### <a name="subheading29"></a>Gegevens met hoog verkeer

Als uw partitie schema resulteert in een enkele partitie die alleen gegevens bevat die veel meer worden gebruikt dan andere partities, ziet u mogelijk ook de vertraging wanneer die partitie het schaalbaarheids doel voor één partitie nadert.  Het is beter om ervoor te zorgen dat uw partitie schema resulteert in geen enkele partitie die de schaalbaarheids doelen nadert.  

#### <a name="querying"></a>Uitvoeren van query's

In deze sectie worden bewezen procedures beschreven voor het uitvoeren van query's in de tabel service.  

##### <a name="subheading30"></a>Query bereik

Er zijn verschillende manieren om het bereik van entiteiten op te geven dat moet worden opgevraagd.  Hier volgt een bespreking van het gebruik van elke.  

In het algemeen kunt u scans voor komen (query's die groter zijn dan één entiteit), maar als u moet scannen, kunt u proberen uw gegevens in te delen, zodat uw scans de gegevens ophalen die u nodig hebt zonder dat u grote hoeveel heden entiteiten hoeft te scannen of te retour neren die u niet nodig hebt.  

###### <a name="point-queries"></a>Punt query's

Een Point-query haalt precies één entiteit op. Dit gebeurt door de partitie sleutel en de rij sleutel op te geven van de entiteit die moet worden opgehaald. Deze query's zijn efficiënt en u moet ze waar mogelijk gebruiken.  

###### <a name="partition-queries"></a>Partitie query's

Een partitie query is een query die een set gegevens ophaalt die een gemeen schappelijke partitie sleutel deelt. Normaal gesp roken specificeert de query een reeks waarden voor de rij of een reeks waarden voor een bepaalde entiteits eigenschap naast een partitie sleutel. Dit zijn minder efficiënt dan punt query's en moeten spaarzaam worden gebruikt.  

###### <a name="table-queries"></a>Tabel query's

Een tabel query is een query waarmee een set entiteiten wordt opgehaald die geen gemeen schappelijke partitie sleutel delen. Deze query's zijn niet efficiënt en u moet ze indien mogelijk vermijden.  

##### <a name="subheading31"></a>Query densiteit

Een andere sleutel factor in query efficiëntie is het aantal entiteiten dat wordt geretourneerd in vergelijking met het aantal entiteiten dat is gescand om de geretourneerde set te vinden. Als uw toepassing een tabel query uitvoert met een filter voor een eigenschaps waarde die slechts 1% van de gegevens shares bevat, scant de query 100 entiteiten voor elke entiteit die wordt geretourneerd. De tabel schaalbaarheids doelen die eerder zijn besproken, zijn allemaal gerelateerd aan het aantal gescande entiteiten en niet het aantal geretourneerde entiteiten: met een lage query dichtheid kan de tabel service de toepassing eenvoudig vertragen omdat er zoveel entiteiten moeten worden gescand om Haal de entiteit op die u zoekt.  Zie de sectie hieronder over [](#subheading34) het denormaliseren voor meer informatie over hoe u dit kunt voor komen.  

##### <a name="limiting-the-amount-of-data-returned"></a>De hoeveelheid geretourneerde gegevens beperken

###### <a name="subheading32"></a>Filtrati

Wanneer u weet dat een query entiteiten retourneert die u niet nodig hebt in de client toepassing, kunt u een filter gebruiken om de grootte van de geretourneerde set te reduceren. Hoewel de entiteiten die niet naar de client worden geretourneerd, nog steeds betrekking hebben op de limieten voor de schaal baarheid, worden de prestaties van uw toepassingen verbeterd vanwege de gereduceerde Payload-grootte van het netwerk en het beperkte aantal entiteiten dat uw client toepassing moet verwerken.  Zie boven opmerking over [query-densiteit](#subheading31). de schaal baarheids doelen zijn echter gerelateerd aan het aantal gescande entiteiten. een query die veel entiteiten uitfiltert, kan dus nog steeds worden beperkt, zelfs als er slechts enkele entiteiten worden geretourneerd.  

###### <a name="subheading33"></a>Projection

Als uw client toepassing slechts een beperkt aantal eigenschappen van de entiteiten in uw tabel nodig heeft, kunt u projectie gebruiken om de grootte van de geretourneerde gegevensset te beperken. Net als bij het filteren helpt dit bij het verminderen van de netwerk belasting en de client verwerking.  

##### <a name="subheading34"></a>Denormalisatie

In tegens telling tot het werken met relationele data bases, de bewezen procedures voor het efficiënt uitvoeren van query's in tabel gegevens om uw gegevens te denormaliseren. Dat wil zeggen dat u dezelfde gegevens in meerdere entiteiten dupliceert (één voor elke sleutel die u kunt gebruiken om de gegevens te vinden) om zo het aantal entiteiten te beperken dat een query moet scannen om de gegevens te vinden die de client nodig heeft, in plaats van een groot aantal entiteiten te scannen om de gegevens van uw app te vinden lication-behoeften.  Zo kunt u bijvoorbeeld in een e-commerce website een bestelling vinden op basis van de klant-ID (dit zijn de orders van deze klant) en op basis van de datum (Geef bestellingen op een datum).  In Table Storage kunt u het beste de entiteit (of een verwijzing naar de) twee keer opslaan: eenmaal met tabel naam, PK en RK om het vinden van de klant-ID te vergemakkelijken, eenmaal om deze te vergemakkelijken op basis van de datum.  

#### <a name="insertupdatedelete"></a>Invoegen/bijwerken/verwijderen

In deze sectie worden bewezen procedures beschreven voor het wijzigen van entiteiten die zijn opgeslagen in de tabel service.  

##### <a name="subheading35"></a>Batch verwerking

Batch transacties worden ETG (trans acties voor entiteits groepen) in Azure Storage genoemd. alle bewerkingen binnen een ETG moeten op één partitie in één tabel staan. Indien mogelijk gebruikt u ETGs om invoeg-, bijwerk-en verwijder bewerkingen uit te voeren in batches. Dit reduceert het aantal afrondingen van uw client toepassing naar de server, vermindert het aantal factureer bare trans acties (een ETG telt als één trans actie voor facturerings doeleinden en kan Maxi maal 100 opslag bewerkingen bevatten) en schakelt atomische updates in (alle de bewerkingen slagen of mislukken in een ETG). Omgevingen met hoge latentie, zoals mobiele apparaten, profiteren aanzienlijk van het gebruik van ETGs.  

##### <a name="subheading36"></a>Upsert

Gebruik waar mogelijk tabel **Upsert** -bewerkingen. Er zijn twee soorten **Upsert**, die beide efficiënter kunnen zijn dan een traditionele **Insert** -en **Update** -bewerkingen:  

* **InsertOrMerge**: Gebruik deze instelling als u een subset van de eigenschappen van de entiteit wilt uploaden, maar niet zeker weet of de entiteit al bestaat. Als de entiteit bestaat, worden de eigenschappen die zijn opgenomen in de **Upsert** -bewerking bijgewerkt en blijven alle bestaande eigenschappen ongewijzigd, als de entiteit niet bestaat, wordt de nieuwe entiteit ingevoegd. Dit is vergelijkbaar met het gebruik van projectie in een query, zodat u alleen de eigenschappen hoeft te uploaden die worden gewijzigd.
* **InsertOrReplace**: Gebruik deze functie als u een geheel nieuwe entiteit wilt uploaden, maar u niet zeker weet of deze al bestaat. U moet dit alleen gebruiken wanneer u weet dat de zojuist geüploade entiteit volledig correct is, omdat deze de oude entiteit volledig overschrijft. U wilt bijvoorbeeld de entiteit bijwerken waarin de huidige locatie van een gebruiker wordt opgeslagen, ongeacht of de toepassing eerder opgeslagen locatie gegevens voor de gebruiker bevat. de nieuwe locatie-entiteit is voltooid en u hebt geen gegevens nodig van een vorige entiteit.

##### <a name="subheading37"></a>Gegevens reeksen opslaan in één entiteit

Soms slaat een toepassing een reeks gegevens op die regel matig moet worden opgehaald: een toepassing kan bijvoorbeeld het CPU-gebruik in de loop van de tijd volgen om een doorlopende grafiek van de gegevens in de afgelopen 24 uur te zetten. Een manier is om één tabel entiteit per uur te hebben, waarbij elke entiteit een specifiek uur vertegenwoordigt en het CPU-gebruik voor dat uur opslaat. Voor het uitzetten van deze gegevens moet de toepassing de entiteiten ophalen die de gegevens van de 24 meest recente uren houden.  

Het is ook mogelijk dat uw toepassing het CPU-gebruik voor elk uur opslaat als een afzonderlijke eigenschap van één entiteit: om elk uur bij te werken, kan uw toepassing één **InsertOrMerge Upsert** -aanroep gebruiken om de waarde voor het meest recente uur bij te werken. Voor het uitzetten van de gegevens hoeft de toepassing slechts één entiteit op te halen in plaats van 24, waardoor een efficiënte query wordt gemaakt (Zie de bovenstaande discussie over het bereik van de [query](#subheading30)).

##### <a name="subheading38"></a>Gestructureerde gegevens opslaan in blobs

Soms lijkt het alsof gestructureerde gegevens in tabellen worden geplaatst, maar bereiken van entiteiten worden altijd samen opgehaald en kunnen ze batchgewijs worden ingevoegd.  Een goed voor beeld hiervan is een logboek bestand.  In dit geval kunt u meerdere minuten aan logboeken vastleggen, ze invoegen en vervolgens altijd enkele minuten aan logboeken tegelijk ophalen.  In dit geval is het beter om blobs te gebruiken in plaats van tabellen, omdat u het aantal geschreven/geretourneerde objecten aanzienlijk kunt verminderen, evenals het aantal aanvragen dat moet worden gedaan.  

## <a name="queues"></a>Wachtrijen

Naast de bewezen procedures voor [alle services](#allservices) die eerder zijn beschreven, zijn de volgende bewezen procedures specifiek van toepassing op de Queue-service.  

### <a name="subheading39"></a>Schaalbaarheids limieten

Eén wachtrij kan ongeveer 2.000 berichten (1 KB elk) per seconde verwerken (elke AddMessage, GetMessage en DeleteMessage tellen als een bericht). Als dit niet voldoende is voor uw toepassing, moet u meerdere wacht rijen gebruiken en de berichten over de toepassingen verdelen.  

Huidige schaalbaarheids doelen weer geven bij [Azure Storage schaal baarheid en prestatie doelen](storage-scalability-targets.md).  

### <a name="subheading40"></a>Nagle uitschakelen

Zie de sectie over de tabel configuratie die de Nagle-algoritme beschrijft. het Nagle-algoritme is doorgaans ongeldig voor de prestaties van wachtrij aanvragen en u moet dit uitschakelen.  

### <a name="subheading41"></a>Bericht grootte

De prestaties en schaal baarheid van de wachtrij nemen af naarmate de bericht grootte toeneemt. Plaats alleen de informatie die de ontvanger nodig heeft in een bericht.  

### <a name="subheading42"></a>Batch ophalen

U kunt Maxi maal 32 berichten uit een wachtrij in één bewerking ophalen. Hierdoor kan het aantal retours van de client toepassing worden verminderd. Dit is vooral nuttig voor omgevingen, zoals mobiele apparaten, met een hoge latentie.  

### <a name="subheading43"></a>Polling-interval voor de wachtrij

De meeste toepassingen controleren op berichten uit een wachtrij, die een van de grootste bronnen aan trans acties voor die toepassing kunnen zijn. Uw polling-interval zo goed mogelijk selecteren: polling wordt te vaak gebruikt om ervoor te zorgen dat uw toepassing de schaalbaarheids doelen voor de wachtrij nadert. Bij 200.000 trans acties voor $0,01 (op het moment van schrijven) is er echter een eenmalige polling per seconde voor een maand van minder dan 15 euro. de kosten zijn dus niet doorgaans een factor die van invloed is op uw gekozen polling-interval.  

Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/)voor actuele informatie over de kosten.  

### <a name="subheading44"></a>Bericht bijwerken

U kunt de bewerking **bericht bijwerken** gebruiken om de time-out voor onzichtbaarheid te verhogen of om status informatie van een bericht bij te werken. Hoewel dit krachtig is, moet u er rekening mee houden dat elke **Update bericht** bewerking het doel van de schaal baarheid telt. Dit kan echter een veel efficiëntere benadering zijn dan het gebruik van een werk stroom die een taak van de ene wachtrij naar de volgende stuurt, wanneer elke stap van de taak is voltooid. Met de bewerking **bericht bijwerken** kan uw toepassing de taak status opslaan in het bericht en vervolgens blijven werken, in plaats van het bericht voor de volgende stap van de taak telkens opnieuw in de wachtrij te plaatsen wanneer een stap is voltooid.  

Zie voor meer informatie het artikel [How to: Wijzig de inhoud van een bericht](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message)in de wachtrij.  

### <a name="subheading45"></a>Toepassings architectuur

U moet wacht rijen gebruiken om uw toepassings architectuur schaalbaar te maken. Hieronder vindt u enkele manieren waarop u wacht rijen kunt gebruiken om uw toepassing beter te schaalbaar te maken:  

* U kunt wacht rijen gebruiken om werk belastingen te maken voor het verwerken en uitzetten van workloads in uw toepassing. U kunt bijvoorbeeld aanvragen van gebruikers in de wachtrij plaatsen voor het uitvoeren van processorintensieve werk, zoals het wijzigen van de grootte van geüploade afbeeldingen.
* U kunt wacht rijen gebruiken om delen van uw toepassing te scheiden, zodat u ze onafhankelijk van elkaar kunt schalen. Een web-front-end kan bijvoorbeeld enquête resultaten van gebruikers in een wachtrij plaatsen voor latere analyse en opslag. U kunt meer werk rollen instanties toevoegen om de wachtrij gegevens naar behoefte te verwerken.  

## <a name="conclusion"></a>Conclusie

In dit artikel worden enkele van de meest voorkomende, bewezen prak tijken besproken voor het optimaliseren van de prestaties bij het gebruik van Azure Storage. Wij raden elke toepassingsontwikkelaar aan om toepassingen te beoordelen aan de hand van elk van de bovenstaande procedures en de aanbevelingen te overwegen voor het verkrijgen van uitstekende prestaties voor toepassingen die gebruikmaken van Azure Storage.
