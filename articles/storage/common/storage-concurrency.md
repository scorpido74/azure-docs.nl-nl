---
title: Gelijktijdigheid beheren
titleSuffix: Azure Storage
description: Meer informatie over het beheren van gelijktijdigheid voor de services Blob, Queue, Table en File.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9879f98e72e22fc0745a9e91f29216cbe74ab8fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255299"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Gelijktijdigheid beheren in Microsoft Azure Storage

Moderne internettoepassingen hebben doorgaans meerdere gebruikers die gegevens tegelijkertijd bekijken en bijwerken. Dit vereist dat ontwikkelaars van toepassingen goed nadenken over hoe ze hun eindgebruikers een voorspelbare ervaring kunnen bieden, met name voor scenario's waarin meerdere gebruikers dezelfde gegevens kunnen bijwerken. Er zijn drie belangrijke strategieën voor gegevensgelijktijdigvaluta die ontwikkelaars doorgaans overwegen:  

1. Optimistische gelijktijdigheid : een toepassing die een update uitvoert, controleert als onderdeel van de update of de gegevens zijn gewijzigd sinds de toepassing die gegevens voor het laatst heeft gelezen. Als twee gebruikers die een wikipagina bekijken bijvoorbeeld een update naar dezelfde pagina maken, moet het wikiplatform ervoor zorgen dat de tweede update de eerste update niet overschrijft en dat beide gebruikers begrijpen of hun update succesvol was of niet. Deze strategie wordt meestal gebruikt in webapplicaties.
2. Pessimistische gelijktijdigheid - Een toepassing die een update wil uitvoeren, neemt een vergrendeling op een object dat andere gebruikers verhindert de gegevens bij te werken totdat het slot is vrijgegeven. In een master/ondergeschikt gegevensreplicatiescenario waarbij alleen de stramien updates uitvoert, houdt de stramien doorgaans een exclusief slot voor een langere periode op de gegevens om ervoor te zorgen dat niemand anders deze kan bijwerken.
3. Laatste schrijver wint - Een aanpak waarmee alle updatebewerkingen kunnen doorgaan zonder te controleren of een andere toepassing de gegevens heeft bijgewerkt sinds de toepassing de gegevens voor het eerst heeft gelezen. Deze strategie (of het ontbreken van een formele strategie) wordt meestal gebruikt wanneer gegevens zodanig worden verdeeld dat het niet waarschijnlijk is dat meerdere gebruikers toegang krijgen tot dezelfde gegevens. Het kan ook handig zijn wanneer kortstondige gegevensstromen worden verwerkt.  

In dit artikel vindt u een overzicht van hoe het Azure Storage-platform de ontwikkeling vereenvoudigt door eersteklas ondersteuning te bieden voor alle drie deze gelijktijdigheidsstrategieën.  

## <a name="azure-storage-simplifies-cloud-development"></a>Azure Storage vereenvoudigt cloudontwikkeling

De Azure-opslagservice ondersteunt alle drie de strategieën, hoewel het onderscheidend is in zijn vermogen om volledige ondersteuning te bieden voor optimistische en pessimistische gelijktijdigheid, omdat het is ontworpen om een sterk consistentiemodel te omarmen dat garandeert dat wanneer de Opslagservice verbindt een gegevensinvoeging of updatebewerking alle verdere toegangen tot die gegevens zullen de nieuwste update zien. Opslagplatforms die een eventueel consistentiemodel gebruiken, hebben een vertraging tussen wanneer een schrijven door één gebruiker wordt uitgevoerd en wanneer de bijgewerkte gegevens door andere gebruikers kunnen worden gezien en aldus de ontwikkeling van clienttoepassingen compliceren om inconsistenties te voorkomen eindgebruikers.  

Naast het selecteren van een geschikte gelijktijdigheidsstrategie moeten ontwikkelaars zich ook bewust zijn van hoe een opslagplatform wijzigingen isoleert - met name wijzigingen in hetzelfde object tussen transacties. De Azure-opslagservice maakt gebruik van momentopnameisolatie om leesbewerkingen gelijktijdig te laten plaatsvinden met schrijfbewerkingen binnen één partitie. In tegenstelling tot andere isolatieniveaus garandeert momentopnameisolatie dat alle reads een consistente momentopname van de gegevens zien, zelfs wanneer er updates plaatsvinden , voornamelijk door de laatst vastgelegde waarden terug te sturen terwijl een updatetransactie wordt verwerkt.  

## <a name="managing-concurrency-in-blob-storage"></a>Gelijktijdigheid beheren in Blob-opslag

U ervoor kiezen om optimistische of pessimistische gelijktijdigheidsmodellen te gebruiken om de toegang tot blobs en containers in de Blob-service te beheren. Als u niet expliciet een strategie opgeeft die laatst wint, is de standaardinstelling.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Optimistische gelijktijdigheid voor blobs en containers

De opslagservice wijst een id toe aan elk opgeslagen object. Deze id wordt bijgewerkt telkens wanneer een updatebewerking op een object wordt uitgevoerd. De id wordt teruggestuurd naar de client als onderdeel van een HTTP GET-antwoord met behulp van de etag-header (entity tag) die is gedefinieerd in het HTTP-protocol. Een gebruiker die een update uitvoert op een dergelijk object kan de oorspronkelijke ETag verzenden, samen met een voorwaardelijke koptekst om ervoor te zorgen dat een update alleen plaatsvindt als aan een bepaalde voorwaarde is voldaan - in dit geval is de voorwaarde een "If-Match"-header, waarvoor de opslagservice vereist is om ervoor te zorgen dat de waarde van de ETag die in de updateaanvraag is opgegeven, dezelfde is als die welke is opgeslagen in de opslagservice.  

De contouren van dit proces zijn als volgt:  

1. Haal een blob op uit de opslagservice, het antwoord bevat een HTTP ETag-headerwaarde die de huidige versie van het object in de opslagservice identificeert.
2. Wanneer u de blob bijwerkt, neemt u de ETag-waarde op die u in stap 1 hebt ontvangen in de voorwaardelijke **kop-inhoud als overeenkomen** van het verzoek dat u naar de service verzendt.
3. De service vergelijkt de ETag-waarde in de aanvraag met de huidige ETag-waarde van de blob.
4. Als de huidige ETag-waarde van de blob een andere versie is dan de ETag in de **voorwaardelijke koptekst If-Match** in de aanvraag, retourneert de service een fout van 412 naar de client. Dit geeft aan de client aan dat een ander proces de blob heeft bijgewerkt sinds de client deze heeft opgehaald.
5. Als de huidige ETag-waarde van de blob dezelfde versie is als de ETag in de **voorwaardelijke koptekst Als-Match** in de aanvraag, voert de service de gevraagde bewerking uit en werkt de huidige ETag-waarde van de blob bij om aan te geven dat deze een nieuwe versie heeft gemaakt.  

In het volgende C#-fragment (met behulp van de clientopslagbibliotheek 4.2.0) wordt een eenvoudig voorbeeld weergegeven van het maken van een **If-Match AccessCondition** op basis van de ETag-waarde die wordt geopend vanuit de eigenschappen van een blob die eerder is opgehaald of ingevoegd. Vervolgens wordt het object **AccessCondition** gebruikt wanneer de blob wordt bijgewerkt: het object **AccessCondition** voegt de **koptekst If-Match** toe aan de aanvraag. Als een ander proces de blob heeft bijgewerkt, retourneert de Blob-service een statusbericht VAN HTTP 412 (Voorwaarde mislukt). U het volledige voorbeeld hier downloaden: [Samendraaiing beheren met Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage Blob service which returns the ETag in response.
string originalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No ETag provided so original blob is overwritten (thus generating a new ETag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the original ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using original ETag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(originalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's original ETag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}  
```

Azure Storage bevat ook ondersteuning voor extra voorwaardelijke kopteksten zoals **If-Modified-Since,** **If-Unmodified-Since** en **If-None-Match,** evenals combinaties daarvan. Zie [Voorwaardelijke kopteksten opgeven voor Klodderservicebewerkingen voor](https://msdn.microsoft.com/library/azure/dd179371.aspx)meer informatie .  

In de volgende tabel worden de containerbewerkingen samengevat die voorwaardelijke kopteksten accepteren, zoals **If-Match** in de aanvraag en die een ETag-waarde in het antwoord retourneren.  

| Bewerking | Retourneert containeretagwaarde | Voorwaardelijke kopteksten accepteren |
|:--- |:--- |:--- |
| Container maken |Ja |Nee |
| Containereigenschappen opdoen |Ja |Nee |
| Containermetagegevens metagegevens ophalen |Ja |Nee |
| Containermetagegevens instellen |Ja |Ja |
| Ontvang Container ACL |Ja |Nee |
| Container ACL instellen |Ja |Ja (*) |
| Container verwijderen |Nee |Ja |
| Leasecontainer |Ja |Ja |
| Blobs weergeven |Nee |Nee |

(*) De door SetContainerACL gedefinieerde machtigingen worden in de cache opgeslagen en het kan 30 seconden duren voordat updates voor deze machtigingen worden doorgevoerd gedurende welke periode updates niet consistent zijn.  

In de volgende tabel worden de blobbewerkingen samengevat die voorwaardelijke kopteksten accepteren, zoals **If-Match** in de aanvraag en die een ETag-waarde in het antwoord retourneren.

| Bewerking | Geeft als resultaat de waarde van ETag | Voorwaardelijke kopteksten accepteren |
|:--- |:--- |:--- |
| Blob plaatsen |Ja |Ja |
| Get Blob |Ja |Ja |
| Get Blob Properties (Blob-eigenschappen ophalen) |Ja |Ja |
| Blob-eigenschappen instellen |Ja |Ja |
| Blob-metagegevens oppakken |Ja |Ja |
| Blob-metagegevens instellen |Ja |Ja |
| Blob leasen (*) |Ja |Ja |
| Momentopnameblob |Ja |Ja |
| Blob kopiëren |Ja |Ja (voor bron- en doelblob) |
| Blob kopiëren afbreken |Nee |Nee |
| Blob verwijderen |Nee |Ja |
| Zet blok |Nee |Nee |
| Lijst met blokkeringplaatsen plaatsen |Ja |Ja |
| Bloklijst opdoen |Ja |Nee |
| Pagina plaatsen |Ja |Ja |
| Paginabereiken ophalen |Ja |Ja |

(*) Lease Blob verandert de ETag op een blob niet.  

### <a name="pessimistic-concurrency-for-blobs"></a>Pessimistische gelijktijdigheid voor blobs

Als u een blob wilt vergrendelen voor exclusief gebruik, u er een [lease-overeenkomst](https://msdn.microsoft.com/library/azure/ee691972.aspx) op aanschaffen. Wanneer u een lease-overeenkomst verwerft, geeft u aan hoe lang u de lease nodig hebt: dit kan tussen de 15 en 60 seconden of oneindig zijn, wat neerkomt op een exclusief slot. U een eindige lease verlengen om deze uit te breiden, en u elke lease vrijgeven wanneer u ermee klaar bent. De Blob-service brengt automatisch eindige leases vrij wanneer deze verlopen.  

Leases maken het mogelijk om verschillende synchronisatiestrategieën te ondersteunen, waaronder exclusief schrijven / gedeeld lezen, exclusief schrijven / exclusief lezen en gedeeld schrijven / exclusief lezen. Wanneer een lease bestaat, dwingt de opslagservice exclusieve schrijft (put, set en delete operations) maar het waarborgen van exclusiviteit voor leesbewerkingen vereist dat de ontwikkelaar ervoor zorgt dat alle clienttoepassingen een lease-ID gebruiken en dat slechts één client tegelijk een geldig lease-id. Lees bewerkingen die geen lease-ID-resultaat bevatten in gedeelde reads.  

Het volgende C#-fragment toont een voorbeeld van het verkrijgen van een exclusieve lease voor 30 seconden op een blob, het bijwerken van de inhoud van de blob en het vrijgeven van de lease. Als er al een geldige lease op de blob is wanneer u een nieuwe lease probeert te verkrijgen, retourneert de Blob-service een statusresultaat 'HTTP (409) Conflict'. In het volgende fragment wordt een **object AccessCondition** gebruikt om de leasegegevens in te kapselen wanneer het een verzoek indient om de blob in de opslagservice bij te werken.  U het volledige voorbeeld hier downloaden: [Samendraaiing beheren met Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Acquire lease for 15 seconds
string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

// Update blob using lease. This operation will succeed
const string helloText = "Blob updated";
var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
blockBlob.UploadText(helloText, accessCondition: accessCondition);
Console.WriteLine("Blob updated using an exclusive lease");

//Simulate third party update to blob without lease
try
{
    // Below operation will fail as no valid lease provided
    Console.WriteLine("Trying to update blob without valid lease");
    blockBlob.UploadText("Update without lease, will fail");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
    else
        throw;
}  
```

Als u een schrijfbewerking op een gehuurde blob probeert zonder de lease-id door te geven, mislukt de aanvraag met een fout van 412. Houd er rekening mee dat als de lease verloopt voordat u de **UploadText-methode** aanroept, maar u nog steeds de lease-id doorgeeft, het verzoek ook mislukt met een fout van **412.** Zie de [LEASE Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx) REST-documentatie voor meer informatie over het beheren van lease-verlopen en lease-id's.  

De volgende blobbewerkingen kunnen leases gebruiken om pessimistische gelijktijdigheid te beheren:  

* Blob plaatsen
* Get Blob
* Get Blob Properties (Blob-eigenschappen ophalen)
* Blob-eigenschappen instellen
* Blob-metagegevens oppakken
* Blob-metagegevens instellen
* Blob verwijderen
* Zet blok
* Lijst met blokkeringplaatsen plaatsen
* Bloklijst opdoen
* Pagina plaatsen
* Paginabereiken ophalen
* Snapshot Blob - lease-ID optioneel als er een lease-overeenkomst bestaat
* Blob kopiëren - lease-id vereist als er een lease-overeenkomst bestaat op de doelblob
* Blob kopiëren afbreken - lease-id vereist als er een oneindige lease bestaat op de doelblob
* Lease Blob  

### <a name="pessimistic-concurrency-for-containers"></a>Pessimistische gelijktijdigheid voor containers

Leases op containers maken het mogelijk om dezelfde synchronisatiestrategieën te ondersteunen als op blobs (exclusief schrijven / gedeeld lezen, exclusief schrijven / exclusief lezen en gedeeld schrijven / exclusief lezen), maar in tegenstelling tot blobs dwingt de opslagservice alleen exclusiviteit af bij het verwijderen van bewerkingen. Als u een container met een actieve lease wilt verwijderen, moet een klant de active lease-id bij de verwijderingsaanvraag opnemen. Alle andere containeractiviteiten slagen op een gehuurde container zonder de lease-ID op te nemen in welk geval het gedeelde activiteiten zijn. Als exclusiviteit van update (put of set) of leesbewerkingen vereist is, moeten ontwikkelaars ervoor zorgen dat alle clients een lease-ID gebruiken en dat slechts één klant tegelijk een geldig lease-id heeft.  

De volgende containerbewerkingen kunnen leases gebruiken om pessimistische gelijktijdigheid te beheren:  

* Container verwijderen
* Containereigenschappen opdoen
* Containermetagegevens metagegevens ophalen
* Containermetagegevens instellen
* Ontvang Container ACL
* Container ACL instellen
* Leasecontainer  

Zie voor meer informatie:  

* [Specifying Conditional Headers for Blob Service Operations (Voorwaardelijke headers opgeven voor bewerkingen van de blob-service)](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Leasecontainer](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Lease-blob](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-table-storage"></a>Gelijktijdigheid beheren in tabelopslag

De tabelservice gebruikt optimistische gelijktijdigheidscontroles als standaardgedrag wanneer u met entiteiten werkt, in tegenstelling tot de Blob-service waarbij u expliciet moet kiezen voor optimistische gelijktijdigheidscontroles. Het andere verschil tussen de tabel en blobservices is dat u alleen het gelijktijdigheidsgedrag van entiteiten beheren, terwijl u met de Blob-service de gelijktijdigheid van zowel containers als blobs beheren.  

Als u optimistische gelijktijdigheid wilt gebruiken en wilt controleren of een ander proces een entiteit heeft gewijzigd sinds u deze hebt opgehaald uit de tabelopslagservice, u de ETag-waarde gebruiken die u ontvangt wanneer de tabelservice een entiteit retourneert. De contouren van dit proces zijn als volgt:  

1. Haal een entiteit op uit de tabelopslagservice, het antwoord bevat een ETag-waarde die de huidige id identificeert die aan die entiteit is gekoppeld in de opslagservice.
2. Wanneer u de entiteit bijwerkt, neemt u de ETag-waarde op die u in stap 1 hebt ontvangen, in de verplichte **If-Match-header** van het verzoek dat u naar de service verzendt.
3. De service vergelijkt de ETag-waarde in de aanvraag met de huidige ETag-waarde van de entiteit.
4. Als de huidige ETag-waarde van de entiteit anders is dan de ETag in de verplichte **Koptekst If-Match** in de aanvraag, retourneert de service een fout van 412 naar de client. Dit geeft aan de client aan dat een ander proces de entiteit heeft bijgewerkt sinds de client deze heeft opgehaald.
5. Als de huidige ETag-waarde van de entiteit dezelfde is als de ETag in de verplichte **Koptekst If-Match** in de aanvraag of de **koptekst If-Match** het jokerteken (*), voert de service de gevraagde bewerking uit en werkt de huidige ETag-waarde van de entiteit bij om aan te geven dat deze is bijgewerkt.  

Houd er rekening mee dat de tabelservice in tegenstelling tot de Blob-service vereist dat de client een **Als-Match-header** opneemt in updateaanvragen. Het is echter mogelijk om een onvoorwaardelijke update (laatste schrijver wint strategie) af te dwingen en gelijktijdigheidscontroles te omzeilen als de client de **Kop-inhoud als** het jokerteken (*) in de aanvraag instelt.  

In het volgende C#-fragment wordt een entiteit van een klant weergegeven die eerder is gemaakt of opgehaald met een bijgewerkte e-mailadres. De initiële invoeg- of ophalensbewerking slaat de ETag-waarde op in het klantobject en omdat het voorbeeld dezelfde objectinstantie gebruikt wanneer de vervangingsbewerking wordt uitgevoerd, wordt de ETag-waarde automatisch teruggestuurd naar de tabelservice, zodat de service controleren op overtredingen van gelijktijdigheid. Als een ander proces de entiteit in tabelopslag heeft bijgewerkt, retourneert de service een statusbericht HTTP 412 (Voorwaarde mislukt).  U het volledige voorbeeld hier downloaden: [Samendraaiing beheren met Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
try
{
    customer.Email = "updatedEmail@contoso.org";
    TableOperation replaceCustomer = TableOperation.Replace(customer);
    customerTable.Execute(replaceCustomer);
    Console.WriteLine("Replace operation succeeded.");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == 412)
        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
    else
        throw;
}  
```

Als u de gelijktijdigheidscontrole expliciet wilt uitschakelen, moet u de eigenschap **ETag** van het **werknemerobject** instellen op '*' voordat u de vervangende bewerking uitvoert.  

```csharp
customer.ETag = "*";  
```

In de volgende tabel wordt samengevat hoe de tabelentiteitbewerkingen ETag-waarden gebruiken:

| Bewerking | Geeft als resultaat de waarde van ETag | Vereist als-match aanvraag header |
|:--- |:--- |:--- |
| Queryentiteiten |Ja |Nee |
| Entiteit invoegen |Ja |Nee |
| Entiteit bijwerken |Ja |Ja |
| Entiteit samenvoegen |Ja |Ja |
| Entiteit verwijderen |Nee |Ja |
| Entiteit invoegen of vervangen |Ja |Nee |
| Entiteit invoegen of samenvoegen |Ja |Nee |

Houd er rekening mee dat de **bewerkingen Entiteit invoegen of vervangen** **of entiteit invoegen of samenvoegen** geen gelijktijdigheidscontroles uitvoeren omdat ze geen ETag-waarde naar de tabelservice verzenden. *not*  

Over het algemeen moeten ontwikkelaars die tabellen gebruiken, vertrouwen op optimistische gelijktijdigheid bij het ontwikkelen van schaalbare toepassingen. Als pessimistische vergrendeling nodig is, kunnen ontwikkelaars één benadering volgen bij het openen van tabellen om een aangewezen blob toe te wijzen voor elke tabel en te proberen een lease op de blob te nemen voordat ze op de tabel werken. Deze aanpak vereist wel dat de toepassing ervoor zorgt dat alle gegevenstoegangspaden de lease verkrijgen voordat ze op de tabel worden opgenomen. U moet er ook rekening mee houden dat de minimale leasetijd 15 seconden is, wat een zorgvuldige afweging voor schaalbaarheid vereist.  

Zie voor meer informatie:  

* [Bewerkingen op entiteiten](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Gelijktijdigheid beheren in de wachtrijservice

Een scenario waarin gelijktijdigheid een punt van zorg is in de wachtrijservice, is waarbij meerdere clients berichten uit een wachtrij ophalen. Wanneer een bericht uit de wachtrij wordt opgehaald, bevat het antwoord het bericht en een popontvangstwaarde, die nodig is om het bericht te verwijderen. Het bericht wordt niet automatisch uit de wachtrij verwijderd, maar nadat het is opgehaald, is het niet zichtbaar voor andere clients voor het tijdsinterval dat is opgegeven door de parameter zichtbaarheidstime-out. De client die het bericht ophaalt, wordt verwacht dat het bericht wordt verwijderd nadat het is verwerkt, en vóór de tijd die is opgegeven door het element TimeNextVisible van het antwoord, dat wordt berekend op basis van de waarde van de parameter zichtbaarheidstime-out. De waarde van een time-out voor zichtbaarheid wordt toegevoegd aan het tijdstip waarop het bericht wordt opgehaald om de waarde van TimeNextVisible te bepalen.  

De wachtrijservice heeft geen ondersteuning voor optimistische of pessimistische gelijktijdigheid en daarom moeten clients die berichten verwerken die uit een wachtrij zijn opgehaald, ervoor zorgen dat berichten op een idempotente manier worden verwerkt. Een laatste writer wins-strategie wordt gebruikt voor updatebewerkingen zoals SetQueueServiceProperties, SetQueueMetaData, SetQueueACL en UpdateMessage.  

Zie voor meer informatie:  

* [Wachtrijservice REST API](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Berichten ontvangen](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-azure-files"></a>Gelijktijdigheid beheren in Azure-bestanden

De bestandsservice is toegankelijk via twee verschillende protocoleindpunten: SMB en REST. De REST-service heeft geen ondersteuning voor optimistische vergrendeling of pessimistische vergrendeling en alle updates zullen volgen een laatste schrijver wint strategie. Mkb-clients die bestandsshares monteren, kunnen gebruikmaken van vergrendelingsmechanismen voor bestandssystemen om de toegang tot gedeelde bestanden te beheren, inclusief de mogelijkheid om pessimistische vergrendeling uit te voeren. Wanneer een SMB-client een bestand opent, geeft dit zowel de bestandstoegangs- als de deelmodus op. Als u de optie Bestandstoegang instelt van 'Schrijven' of 'Lezen/schrijven' samen met de modus Bestandsshare van 'Geen' wordt het bestand vergrendeld door een SMB-client totdat het bestand is gesloten. Als de restbewerking wordt geprobeerd in een bestand waarin een SMB-client het bestand heeft vergrendeld, retourneert de REST-service statuscode 409 (Conflict) met foutcode SharingViolation.  

Wanneer een SMB-client een bestand opent voor verwijdering, markeert het bestand als in behandeling verwijderen totdat alle andere SMB-clientgrepen in dat bestand zijn gesloten. Terwijl een bestand is gemarkeerd als in behandeling te verwijderen, retourneert elke REST-bewerking in dat bestand statuscode 409 (Conflict) met foutcode SMBDeletePending. Statuscode 404 (Niet gevonden) wordt niet geretourneerd omdat het mogelijk is voor de SMB-client om de in behandeling zijnde verwijderingsvlag te verwijderen voordat het bestand wordt gesloten. Met andere woorden, statuscode 404 (Niet gevonden) wordt alleen verwacht wanneer het bestand is verwijderd. Houd er rekening mee dat een bestand in een smb-status in behandeling is, maar niet wordt opgenomen in de resultaten van lijstbestanden. Houd er ook rekening mee dat de rest delete-bestands- en REST-mapbewerkingen atoomzijn gepleegd en niet resulteren in een in behandeling zijnde verwijderstatus.  

Zie voor meer informatie:  

* [Bestandsvergrendelingen beheren](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="next-steps"></a>Volgende stappen

Voor de volledige voorbeeldtoepassing waarnaar in deze blog wordt verwezen:  

* [Gelijktijdigheid beheren met Azure Storage - Voorbeeldtoepassing](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Zie voor meer informatie over Azure Storage:  

* [Startpagina van Microsoft Azure Storage](https://azure.microsoft.com/services/storage/)
* [Kennismaking met Azure Storage](storage-introduction.md)
* Opslag aan de slag voor [Blob,](../blobs/storage-dotnet-how-to-use-blobs.md) [Tabel,](../../cosmos-db/table-storage-how-to-use-dotnet.md) [Wachtrijen](../storage-dotnet-how-to-use-queues.md)en [bestanden](../storage-dotnet-how-to-use-files.md)
* Opslagarchitectuur – [Azure Storage: een zeer beschikbare cloudopslagservice met sterke consistentie](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

