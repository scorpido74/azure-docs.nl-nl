---
title: Gelijktijdigheid beheren
titleSuffix: Azure Storage
description: Meer informatie over het beheren van gelijktijdigheid voor de blob-, wachtrij-, tabel-en bestands Services.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9879f98e72e22fc0745a9e91f29216cbe74ab8fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79255299"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Gelijktijdigheid beheren in Microsoft Azure Storage

Moderne op internet gebaseerde toepassingen hebben doorgaans meerdere gebruikers tegelijk de gegevens weer geven en bijwerken. Hiervoor moeten ontwikkel aars van toepassingen zorgvuldig nadenken hoe ze een voorspel bare ervaring kunnen bieden aan hun eind gebruikers, met name voor scenario's waarin meerdere gebruikers dezelfde gegevens kunnen bijwerken. Er zijn drie hoofd strategieën voor het gelijktijdigheid van gegevens die ontwikkel aars doorgaans overwegen:  

1. Optimistische gelijktijdigheid: een toepassing die een update uitvoert, wordt beschouwd als onderdeel van de update Controleer of de gegevens zijn gewijzigd sinds de laatste keer dat de toepassing de gegevens heeft gelezen. Als bijvoorbeeld twee gebruikers die een wikipagina bekijken, een update op dezelfde pagina maken, moet het wiki-platform ervoor zorgen dat de tweede update de eerste update niet overschrijft, en dat beide gebruikers begrijpen of hun update is geslaagd of niet. Deze strategie wordt meestal gebruikt in webtoepassingen.
2. Pessimistische gelijktijdigheid: een toepassing die een update wil uitvoeren, kan een object vergren delen voor komen dat andere gebruikers de gegevens kunnen bijwerken tot de vergren deling is opgeheven. Bijvoorbeeld: in een scenario met een hoofd-en onderliggend gegevens replicatie waarbij alleen de Master updates gaat uitvoeren, wordt doorgaans een exclusieve vergren deling voor een lange periode op de gegevens gehouden om ervoor te zorgen dat niemand anders deze kan bijwerken.
3. Laatste schrijver WINS: een aanpak waarmee update bewerkingen kunnen door gaan zonder te verifiëren of een andere toepassing de gegevens heeft bijgewerkt, omdat de toepassing de gegevens voor het eerst heeft gelezen. Deze strategie (of het ontbreken van een formele strategie) wordt meestal gebruikt voor het partitioneren van gegevens op een zodanige manier dat er geen kans bestaat dat meerdere gebruikers toegang hebben tot dezelfde gegevens. Het kan ook nuttig zijn wanneer er verwerkte gegevens stromen worden verwerkt.  

Dit artikel bevat een overzicht van de manier waarop het Azure Storage-platform de ontwikkeling vereenvoudigt door de eerste klasse ondersteuning te bieden voor alle drie deze gelijktijdigheids strategieën.  

## <a name="azure-storage-simplifies-cloud-development"></a>Azure Storage vereenvoudigt de ontwikkeling van Clouds

De Azure Storage-service biedt ondersteuning voor alle drie de strategieën, hoewel het een onderscheidende mogelijkheid is om volledige ondersteuning te bieden voor optimistische en pessimistische gelijktijdigheid, omdat deze is ontworpen om te voorzien in een krachtig consistentie model dat waarborgt dat wanneer de opslag service een gegevens invoegt of bijwerkt, de meest recente update wordt weer gegeven. Opslag platforms die gebruikmaken van een afwijkend consistentie model hebben een vertraging tussen het moment dat een schrijf bewerking wordt uitgevoerd door één gebruiker en wanneer de bijgewerkte gegevens door andere gebruikers worden gezien, waardoor de ontwikkeling van client toepassingen kan worden voor komen om inconsistenties van invloed te laten zijn op eind gebruikers.  

Naast het selecteren van een passende gelijktijdigheids strategie ontwikkel aars moet er ook rekening mee worden gehouden met de manier waarop een opslag platform wijzigingen kan isoleren: met name wijzigingen in hetzelfde object in trans acties. De Azure Storage-service maakt gebruik van snap shot-isolatie zodat Lees bewerkingen gelijktijdig met schrijf bewerkingen binnen één partitie kunnen plaatsvinden. In tegens telling tot andere isolatie niveaus zorgt de snap shot-isolatie ervoor dat alle Lees bewerkingen een consistente moment opname van de gegevens zien, zelfs wanneer er updates worden uitgevoerd. de laatste vastgelegde waarden worden geretourneerd tijdens het verwerken van een update transactie.  

## <a name="managing-concurrency-in-blob-storage"></a>Gelijktijdigheid beheren in Blob Storage

U kunt ervoor kiezen om een optimistische of pessimistische gelijktijdigheids modellen te gebruiken voor het beheren van de toegang tot blobs en containers in de Blob service. Als u geen expliciete strategie opgeeft, is WINS de standaard instelling.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Optimistische gelijktijdigheid voor blobs en containers

De opslag service wijst een id toe aan elk opgeslagen object. Deze id wordt bijgewerkt wanneer een update bewerking wordt uitgevoerd op een object. De id wordt geretourneerd naar de client als onderdeel van een HTTP GET-antwoord met de ETag-header (entiteits code) die is gedefinieerd in het HTTP-protocol. Een gebruiker die een update uitvoert op een dergelijk object, kan in de oorspronkelijke ETag samen met een voorwaardelijke koptekst verzenden om ervoor te zorgen dat een update alleen wordt uitgevoerd als aan een bepaalde voor waarde is voldaan. in dit geval is de voor waarde een if-match-header, waarvoor de opslag service vereist dat de waarde van de ETag die is opgegeven in de update aanvraag gelijk is aan die in de opslag  

Het overzicht van dit proces is als volgt:  

1. Een BLOB ophalen uit de opslag service, het antwoord bevat een HTTP ETag-header waarde die de huidige versie van het object in de opslag service aanduidt.
2. Wanneer u de BLOB bijwerkt, neemt u de ETag-waarde op die u in stap 1 in de **if-match-** voorwaardelijke header van de aanvraag die u naar de service hebt ontvangen.
3. De service vergelijkt de ETag-waarde in de aanvraag met de huidige ETag-waarde van de blob.
4. Als de huidige ETag-waarde van de BLOB een andere versie is dan de ETag in de voorwaardelijke koptekst in de **if-match-** header in de aanvraag, retourneert de service een 412-fout naar de client. Dit geeft aan de client door dat een ander proces de BLOB heeft bijgewerkt sinds de client deze heeft opgehaald.
5. Als de huidige ETag-waarde van de BLOB dezelfde versie is als de ETag in de voorwaardelijke koptekst in de **if-match-** header in de aanvraag, wordt de aangevraagde bewerking door de service uitgevoerd en wordt de huidige ETAG-waarde van de BLOB bijgewerkt om aan te tonen dat er een nieuwe versie is gemaakt.  

Het volgende C#-fragment (met behulp van de client opslag bibliotheek 4.2.0) toont een eenvoudig voor beeld van het samen stellen van een **if-match AccessCondition** op basis van de ETAG-waarde die wordt geopend vanuit de eigenschappen van een blob die eerder is opgehaald of ingevoegd. Vervolgens wordt het **AccessCondition** -object gebruikt bij het bijwerken van de blob: het **AccessCondition** -object voegt de **if-match-** header toe aan de aanvraag. Als een ander proces de BLOB heeft bijgewerkt, retourneert de Blob service het status bericht HTTP 412 (voor waarde is mislukt). U kunt het volledige voor beeld hier downloaden: [gelijktijdigheid beheren met behulp van Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

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

Azure Storage biedt ook ondersteuning voor aanvullende voorwaardelijke kopteksten, zoals **If-Modified-sinds**, **als-ongewijzigd-sinds** en **als-None-Match** , evenals combi Naties daarvan. Zie [voorwaardelijke headers opgeven voor BLOB-service bewerkingen](https://msdn.microsoft.com/library/azure/dd179371.aspx)voor meer informatie.  

De volgende tabel bevat een overzicht van de container bewerkingen die voorwaardelijke kopteksten accepteren, zoals **if-match** in de aanvraag en die een ETag-waarde Retour neren in het antwoord.  

| Bewerking | Retourneert container ETag-waarde | Accepteert voorwaardelijke kopteksten |
|:--- |:--- |:--- |
| Container maken |Ja |Nee |
| Container eigenschappen ophalen |Ja |Nee |
| Meta gegevens van container ophalen |Ja |Nee |
| Meta gegevens van container instellen |Ja |Ja |
| Container-ACL ophalen |Ja |Nee |
| Container-ACL instellen |Ja |Ja (*) |
| Container verwijderen |Nee |Ja |
| Lease-container |Ja |Ja |
| Blobs weer geven |Nee |Nee |

(*) De machtigingen die zijn gedefinieerd door SetContainerACL, worden in de cache opgeslagen en updates van deze machtigingen nemen 30 seconden in beslag, waarbij periode-updates niet gegarandeerd consistent zijn.  

De volgende tabel bevat een overzicht van de BLOB-bewerkingen die voorwaardelijke kopteksten accepteren, zoals **if-match** in de aanvraag en die een ETag-waarde Retour neren in het antwoord.

| Bewerking | Retourneert ETag-waarde | Accepteert voorwaardelijke kopteksten |
|:--- |:--- |:--- |
| BLOB plaatsen |Ja |Ja |
| BLOB ophalen |Ja |Ja |
| Get Blob Properties (Blob-eigenschappen ophalen) |Ja |Ja |
| BLOB-eigenschappen instellen |Ja |Ja |
| BLOB-meta gegevens ophalen |Ja |Ja |
| BLOB-meta gegevens instellen |Ja |Ja |
| Lease-BLOB (*) |Ja |Ja |
| Moment opname-BLOB |Ja |Ja |
| BLOB kopiëren |Ja |Ja (voor de bron-en doel-blob) |
| Kopiëren van BLOB afbreken |Nee |Nee |
| Blob verwijderen |Nee |Ja |
| Blok keren |Nee |Nee |
| Blokkerings lijst plaatsen |Ja |Ja |
| Blokkerings lijst ophalen |Ja |Nee |
| Pagina plaatsen |Ja |Ja |
| Paginabereiken ophalen |Ja |Ja |

(*) De lease-BLOB wijzigt de ETag op een BLOB niet.  

### <a name="pessimistic-concurrency-for-blobs"></a>Pessimistische gelijktijdigheid voor blobs

Als u een BLOB wilt vergren delen voor exclusief gebruik, kunt u er een [lease](https://msdn.microsoft.com/library/azure/ee691972.aspx) op verkrijgen. Wanneer u een lease aanschaft, geeft u op hoelang u de lease nodig hebt: dit kan tussen 15 en 60 seconden of oneindig zijn, wat een exclusieve vergren deling is. U kunt een beperkte lease vernieuwen om deze uit te breiden en u kunt elke lease vrijgeven wanneer u er klaar mee bent. Wanneer de Blob service afloopt, worden er automatisch beperkte leases vrijgegeven.  

Met leases kunnen verschillende synchronisatie strategieën worden ondersteund, waaronder exclusieve schrijf-en gedeelde Lees-, exclusieve write/exclusieve Lees-en gedeelde write/exclusieve Lees bewerking. Als er een lease bestaat, wordt door de opslag service exclusieve schrijf bewerkingen (put, set en Delete) afgedwongen, maar moet de ontwikkelaar ervoor zorgen dat alle client toepassingen een lease-ID gebruiken en dat slechts één client tegelijk een geldige lease-ID heeft. Lees bewerkingen die geen lease-ID bevatten, resulteren in gedeelde Lees bewerkingen.  

In het volgende C#-fragment ziet u een voor beeld van het ophalen van een exclusieve lease gedurende 30 seconden op een blob, het bijwerken van de inhoud van de BLOB en het vrijgeven van de lease. Als er al een geldige lease in de blob is wanneer u een nieuwe lease wilt verkrijgen, retourneert de Blob service het status resultaat ' HTTP (409) '. In het volgende code fragment wordt een **AccessCondition** -object gebruikt om de lease gegevens op te slaan wanneer er een aanvraag wordt gemaakt om de BLOB in de opslag service bij te werken.  U kunt het volledige voor beeld hier downloaden: [gelijktijdigheid beheren met behulp van Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Als u een schrijf bewerking probeert uit te voeren op een geleasde BLOB zonder de lease-ID door te geven, mislukt de aanvraag met een 412-fout. Houd er rekening mee dat als de lease verloopt voordat u de **UploadText** -methode aanroept, maar u de lease-id nog steeds doorgeeft, mislukt de aanvraag ook met een **412** -fout. Voor meer informatie over het beheren van lease verloop tijden en lease-Id's raadpleegt u de informatie over de [lease-BLOB](https://msdn.microsoft.com/library/azure/ee691972.aspx) rest.  

De volgende BLOB-bewerkingen kunnen leases gebruiken voor het beheren van pessimistische gelijktijdigheid:  

* BLOB plaatsen
* BLOB ophalen
* Get Blob Properties (Blob-eigenschappen ophalen)
* BLOB-eigenschappen instellen
* BLOB-meta gegevens ophalen
* BLOB-meta gegevens instellen
* Blob verwijderen
* Blok keren
* Blokkerings lijst plaatsen
* Blokkerings lijst ophalen
* Pagina plaatsen
* Paginabereiken ophalen
* Moment opname-BLOB-lease-ID optioneel als er een lease bestaat
* Kopiëren van BLOB-lease-ID vereist als er een lease bestaat op de doel-BLOB
* Kopiëren van BLOB afbreken-lease-ID vereist als er een oneindige lease bestaat op de doel-BLOB
* Lease Blob  

### <a name="pessimistic-concurrency-for-containers"></a>Pessimistische gelijktijdigheid voor containers

Met leases in containers kunnen dezelfde synchronisatie strategieën worden ondersteund als op blobs (exclusief schrijven/gedeeld lezen, exclusieve write/exclusieve Lees-en gedeelde write/exclusieve Lees bewerking). in tegens telling tot blobs dwingt de opslag service alleen de exclusiviteit af voor verwijderings bewerkingen. Als u een container met een actieve lease wilt verwijderen, moet een client de actieve lease-ID met de aanvraag verwijderen bevatten. Alle andere container bewerkingen slagen op een geleasde container zonder de lease-ID op te nemen, in welk geval ze gedeelde bewerkingen zijn. Als de meeste updates (put of set) of lees bewerkingen zijn vereist, moeten ontwikkel aars ervoor zorgen dat alle clients een lease-ID gebruiken en dat slechts één client tegelijk een geldige lease-ID heeft.  

De volgende container bewerkingen kunnen leases gebruiken voor het beheren van pessimistische gelijktijdigheid:  

* Container verwijderen
* Container eigenschappen ophalen
* Meta gegevens van container ophalen
* Meta gegevens van container instellen
* Container-ACL ophalen
* Container-ACL instellen
* Lease-container  

Zie voor meer informatie:  

* [Specifying Conditional Headers for Blob Service Operations (Voorwaardelijke headers opgeven voor bewerkingen van de blob-service)](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Lease-container](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Lease-blob](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-table-storage"></a>Gelijktijdigheid beheren in tabel opslag

In het Table service worden optimistische gelijktijdigheid controles gebruikt als het standaard gedrag bij het werken met entiteiten, in tegens telling tot de Blob service waar u expliciet moet kiezen om optimistische gelijktijdigheids controles uit te voeren. Het andere verschil tussen de Table-en BLOB-Services is dat u alleen het gelijktijdigheids gedrag van entiteiten kunt beheren, terwijl u met de Blob service u de gelijktijdigheid van zowel containers als blobs kunt beheren.  

Als u optimistische gelijktijdigheid wilt gebruiken en wilt controleren of een ander proces een entiteit heeft gewijzigd sinds u deze hebt opgehaald uit de Table Storage-service, kunt u de ETag-waarde gebruiken die u ontvangt wanneer de tabel service een entiteit retourneert. Het overzicht van dit proces is als volgt:  

1. Een entiteit ophalen uit de Table Storage-service, het antwoord bevat een ETag-waarde die de huidige ID identificeert die is gekoppeld aan die entiteit in de opslag service.
2. Wanneer u de entiteit bijwerkt, neemt u de ETag-waarde op die u in stap 1 hebt ontvangen in de verplichte **if-match-** header van de aanvraag die u naar de service verzendt.
3. De service vergelijkt de ETag-waarde in de aanvraag met de huidige ETag-waarde van de entiteit.
4. Als de huidige ETag-waarde van de entiteit verschilt van de ETag in de verplichte koptekst **if-match** in de aanvraag, retourneert de service een 412-fout naar de client. Dit geeft aan de client door dat een andere proces de entiteit heeft bijgewerkt sinds de client deze heeft opgehaald.
5. Als de huidige ETag-waarde van de entiteit hetzelfde is als de ETag in de verplichte koptekst **if-** match in de aanvraag of als de header **if-match** het Joker teken (*) bevat, wordt de aangevraagde bewerking door de service uitgevoerd en wordt de huidige ETAG-waarde van de entiteit bijgewerkt om aan te geven dat deze is bijgewerkt.  

In tegens telling tot de Blob service, moet de client in de tabel service een **if-match-** header in update aanvragen bevatten. Het is echter wel mogelijk om een onvoorwaardelijke update (de laatste WINS-strategie van de schrijver) af te dwingen en gelijktijdigheids controles over te slaan als de client de header **if-match** instelt op het Joker teken (*) in de aanvraag.  

In het volgende C#-code fragment wordt een klant entiteit weer gegeven die eerder is gemaakt of opgehaald bij het bijwerken van het e-mail adres. Met de eerste INSERT of Retrieve-bewerking wordt de ETag-waarde opgeslagen in het klant-object, en omdat het voor beeld hetzelfde object exemplaar gebruikt wanneer het de Vervang bewerking uitvoert, stuurt het automatisch de ETag-waarde terug naar de Table-service, waardoor de service kan controleren op gelijktijdigheids schendingen. Als een ander proces de entiteit heeft bijgewerkt in tabel opslag, retourneert de service het status bericht HTTP 412 (voor waarde is mislukt).  U kunt het volledige voor beeld hier downloaden: [gelijktijdigheid beheren met behulp van Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Als u de gelijktijdigheids controle expliciet wilt uitschakelen, moet u de **ETAG** -eigenschap van het **werknemers** object instellen op ' * ' voordat u de Vervang bewerking uitvoert.  

```csharp
customer.ETag = "*";  
```

De volgende tabel bevat een overzicht van hoe de tabel entiteit bewerkingen ETag-waarden gebruiken:

| Bewerking | Retourneert ETag-waarde | Vereist if-match-aanvraag header |
|:--- |:--- |:--- |
| Entiteiten opvragen |Ja |Nee |
| Entiteit invoegen |Ja |Nee |
| Entiteit bijwerken |Ja |Ja |
| Entiteit samen voegen |Ja |Ja |
| Entiteit verwijderen |Nee |Ja |
| Entiteit invoegen of vervangen |Ja |Nee |
| Entiteit invoegen of samen voegen |Ja |Nee |

Houd er rekening mee dat de bewerkingen **entiteit invoegen of vervangen** en **entiteit invoegen of samen voegen** *geen* Gelijktijdigheids controles uitvoeren omdat ze geen ETag-waarde naar de Table-service verzenden.  

In algemene ontwikkel aars die gebruikmaken van tabellen, moeten gebruikmaken van optimistische gelijktijdigheid bij het ontwikkelen van schaal bare toepassingen. Als pessimistische vergren deling vereist is, kunnen ontwikkel aars van het openen van tabellen een aangewezen BLOB toewijzen voor elke tabel en proberen een lease te maken op de BLOB voordat deze op de tabel wordt uitgevoerd. Voor deze benadering moet de toepassing ervoor zorgen dat alle gegevens toegangs paden de lease verkrijgen voordat ze op de tabel worden uitgevoerd. Houd er rekening mee dat de minimale lease tijd 15 seconden is. dit vereist zorgvuldige aandacht voor schaal baarheid.  

Zie voor meer informatie:  

* [Bewerkingen op entiteiten](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Gelijktijdigheid beheren in de Queue-service

Een scenario waarbij gelijktijdig gebruik wordt gedaan van een probleem in de wachtrij service, is dat meerdere clients berichten uit een wachtrij ophalen. Wanneer een bericht uit de wachtrij wordt opgehaald, bevat het antwoord het bericht en een pop-ontvangst waarde, die vereist is voor het verwijderen van het bericht. Het bericht wordt niet automatisch verwijderd uit de wachtrij, maar nadat het is opgehaald, is het niet zichtbaar voor andere clients voor het tijds interval dat is opgegeven door de para meter visibilitytimeout. De client die het bericht haalt, wordt verwacht het bericht te verwijderen nadat het is verwerkt en vóór de tijd die is opgegeven door het TimeNextVisible-element van het antwoord, dat wordt berekend op basis van de waarde van de para meter visibilitytimeout. De waarde van visibilitytimeout wordt toegevoegd aan het tijdstip waarop het bericht wordt opgehaald om de waarde van TimeNextVisible te bepalen.  

De Queue-service biedt geen ondersteuning voor optimistische of pessimistische gelijktijdigheid en daarom moeten clients die berichten ophalen die zijn opgehaald uit een wachtrij ervoor zorgen dat berichten op een idempotente manier worden verwerkt. Een laatste WINS-strategie van schrijver wordt gebruikt voor update bewerkingen, zoals SetQueueServiceProperties, SetQueueMetaData, SetQueueACL en UpdateMessage.  

Zie voor meer informatie:  

* [Wachtrijservice REST API](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Berichten ophalen](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-azure-files"></a>Gelijktijdigheid beheren in Azure Files

De bestands service kan worden geopend met twee verschillende protocol eindpunten: SMB en REST. De REST-service biedt geen ondersteuning voor optimistische vergren deling of pessimistische vergren deling, en alle updates volgen een WINS-strategie van de laatste schrijver. SMB-clients die bestands shares koppelen, kunnen gebruikmaken van mechanismen voor bestandssysteem vergrendeling om de toegang tot gedeelde bestanden te beheren, met inbegrip van de mogelijkheid om pessimistische vergren deling uit te voeren. Wanneer een SMB-client een bestand opent, wordt de bestands toegang en de deel modus. Als u een optie voor bestands toegang van ' schrijven ' of ' lezen/schrijven ' instelt, samen met de bestands share modus ' geen ', wordt het bestand vergrendeld door een SMB-client totdat het bestand is gesloten. Als er een REST-bewerking wordt uitgevoerd voor een bestand waarbij een SMB-client het bestand heeft vergrendeld, retourneert de REST-service de status code 409 (conflict) met de fout code SharingViolation.  

Wanneer een SMB-client een bestand opent om te verwijderen, wordt het bestand gemarkeerd als verwijderen in behandeling totdat alle andere SMB-client open ingangen voor dat bestand worden gesloten. Terwijl een bestand is gemarkeerd als verwijderen in behandeling, retourneert elke REST-bewerking in dat bestand de status code 409 (conflict) met de fout code SMBDeletePending. Status code 404 (niet gevonden) wordt niet geretourneerd, omdat het mogelijk is dat de SMB-client de markering voor verwijdering in behandeling verwijdert voordat het bestand wordt gesloten. Met andere woorden, de status code 404 (niet gevonden) wordt alleen verwacht wanneer het bestand is verwijderd. Houd er rekening mee dat als een bestand een SMB in behandeling heeft verwijderings status, dit niet wordt opgenomen in de resultaten van de lijst bestanden. Houd er ook rekening mee dat het REST verwijderen van bestanden en REST verwijderen Directory-bewerkingen op een andere manier wordt doorgevoerd en niet resulteert in een verwijderings status in behandeling.  

Zie voor meer informatie:  

* [Bestands vergrendelingen beheren](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="next-steps"></a>Volgende stappen

Voor de volledige voorbeeld toepassing waarnaar wordt verwezen in deze blog:  

* [Gelijktijdigheid beheren met Azure Storage-voorbeeld toepassing](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Voor meer informatie over Azure Storage raadpleegt u:  

* [Start pagina van Microsoft Azure Storage](https://azure.microsoft.com/services/storage/)
* [Kennismaking met Azure Storage](storage-introduction.md)
* Opslag aan de slag voor [BLOB](../blobs/storage-dotnet-how-to-use-blobs.md), [Table](../../cosmos-db/table-storage-how-to-use-dotnet.md), [Queues](../storage-dotnet-how-to-use-queues.md)en [files](../storage-dotnet-how-to-use-files.md)
* Opslag architectuur – [Azure Storage: een Maxi maal beschik bare Cloud opslag service met sterke consistentie](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

