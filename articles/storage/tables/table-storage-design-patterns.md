---
title: Ontwerppatronen voor Azure-opslagtabellen | Microsoft Documenten
description: Gebruik patronen voor Azure table service-oplossingen.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: 5478163a6103bcc84b4f3608d7513c6e7cb11c01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529336"
---
# <a name="table-design-patterns"></a>Tabelontwerppatronen
In dit artikel worden enkele patronen beschreven die geschikt zijn voor gebruik met serviceoplossingen voor tabelservices. Ook zult u zien hoe u praktisch een aantal van de problemen en trade-offs besproken in andere tabel opslag ontwerp artikelen aan te pakken. In het volgende diagram worden de relaties tussen de verschillende patronen samengevat:  

![om gerelateerde gegevens op te zoeken](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


De patroonkaart hierboven markeert enkele relaties tussen patronen (blauw) en antipatronen (oranje) die in deze handleiding zijn gedocumenteerd. Er zijn van vele andere patronen die het overwegen waard zijn. Een van de belangrijkste scenario's voor Tabelservice is bijvoorbeeld het gebruik van het [gematerialiseerde weergavepatroon](https://msdn.microsoft.com/library/azure/dn589782.aspx) vanuit het [CQRS-patroon (Command Query Responsibility Segregation).](https://msdn.microsoft.com/library/azure/jj554200.aspx)  

## <a name="intra-partition-secondary-index-pattern"></a>Secundair indexpatroon binnen partitie
Sla meerdere kopieën van elke entiteit op met verschillende **RowKey-waarden** (in dezelfde partitie) om snelle en efficiënte opzoekingen en alternatieve sorteerorders in te schakelen met behulp van verschillende **RowKey-waarden.** Updates tussen kopieën kunnen consistent worden gehouden met behulp van EGT's.  

### <a name="context-and-problem"></a>Context en probleem
De tabelservice indexeert automatisch entiteiten met de waarden **PartitionKey** en **RowKey.** Hierdoor kan een clienttoepassing een entiteit efficiënt ophalen met behulp van deze waarden. Met behulp van de tabelstructuur die hieronder wordt weergegeven, kan een clienttoepassing bijvoorbeeld een puntquery gebruiken om een afzonderlijke werknemersentiteit op te halen met behulp van de afdelingsnaam en de werknemers-id (de **partitionkey-** en **rowkey-waarden).** Een client kan ook entiteiten ophalen die zijn gesorteerd op werknemers-id binnen elke afdeling.

![Afbeelding06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Als u ook een werknemer wilt kunnen vinden op basis van de waarde van een andere eigenschap, zoals e-mailadres, moet u een minder efficiënte partitiescan gebruiken om een overeenkomst te vinden. Dit komt omdat de tabelservice geen secundaire indexen biedt. Bovendien is er geen optie om een lijst met werknemers aan te vragen die in een andere volgorde zijn gesorteerd dan **RowKey-order.**  

### <a name="solution"></a>Oplossing
Als u wilt werken aan het ontbreken van secundaire indexen, u meerdere kopieën van elke entiteit bij elke kopie opslaan met behulp van een andere **RowKey-waarde.** Als u een entiteit opslaat met de onderstaande structuren, u op efficiënte wijze werknemersentiteiten ophalen op basis van e-mailadres of werknemers-id. Met de voorvoegselwaarden voor de **RowKey,** empid_ en email_ u een zoekopdracht uitvoeren voor één werknemer of een reeks werknemers met behulp van een reeks e-mailadressen of werknemers-id's.  

![Werknemersentiteiten](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

De volgende twee filtercriteria (één op zoek naar werknemers-ID en één op zoek naar e-mailadres) geven beide puntquery's op:  

* $filter=(PartitionKey eq 'Sales') en (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') en (RowKey eq 'email_jonesj@contoso.com')  

Als u een zoekopdracht opvraagt voor een reeks werknemersentiteiten, u een bereik opgeven dat is gesorteerd in de volgorde van de werknemer-id of een bereik dat is gesorteerd in e-mailadresvolgorde door te zoeken naar entiteiten met het juiste voorvoegsel in de **RowKey**.  

* Om alle medewerkers in de afdeling Verkoop te vinden met een werknemers-ID in het bereik 000100 tot 000199 gebruik: $filter=(PartitionKey eq 'Sales') en (RowKey ge 'empid_000100') en (RowKey le 'empid_000199')  
* Om alle medewerkers op de afdeling Verkoop te vinden met een e-mailadres te beginnen met de letter 'a' gebruik: $filter=(PartitionKey eq 'Sales') en (RowKey ge 'email_a') en (RowKey lt 'email_b')  
  
  De filtersyntaxis die in de bovenstaande voorbeelden wordt gebruikt, is afkomstig uit de API REST van de tabelservice, zie [Queryentiteiten](https://msdn.microsoft.com/library/azure/dd179421.aspx)voor meer informatie.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Tabelopslag is relatief goedkoop te gebruiken, zodat de kosten overhead van het opslaan van dubbele gegevens mag niet een grote zorg. U moet echter altijd de kosten van uw ontwerp evalueren op basis van uw verwachte opslagvereisten en alleen dubbele entiteiten toevoegen ter ondersteuning van de query's die uw clienttoepassing uitvoert.  
* Omdat de secundaire indexentiteiten zijn opgeslagen in dezelfde partitie als de oorspronkelijke entiteiten, moet u ervoor zorgen dat u de schaalbaarheidsdoelen voor een afzonderlijke partitie niet overschrijdt.  
* U uw dubbele entiteiten consistent met elkaar houden door EGT's te gebruiken om de twee kopieën van de entiteit atoombij te werken. Dit houdt in dat u alle kopieën van een entiteit in dezelfde partitie moet opslaan. Zie voor meer informatie de sectie [Entiteitsgroeptransacties gebruiken](table-storage-design.md#entity-group-transactions).  
* De waarde die voor de RowKey wordt **gebruikt,** moet uniek zijn voor elke entiteit. Overweeg samengestelde sleutelwaarden te gebruiken.  
* Opvulling van numerieke waarden in de **RowKey** (bijvoorbeeld de werknemers-ID 000223), maakt het correct sorteren en filteren mogelijk op basis van boven- en ondergrenzen.  
* U hoeft niet per se alle eigenschappen van uw entiteit te dupliceren. Als de query's die de entiteiten opzoeken met behulp van het e-mailadres in de **RowKey** bijvoorbeeld nooit de leeftijd van de werknemer nodig hebben, kunnen deze entiteiten de volgende structuur hebben:

   ![Entiteitsstructuur van werknemers](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Het is meestal beter om dubbele gegevens op te slaan en ervoor te zorgen dat u alle gegevens ophalen die u nodig hebt met één query, dan om één query te gebruiken om een entiteit te vinden en een andere om de vereiste gegevens op te zoeken.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer uw clienttoepassing entiteiten moet ophalen met behulp van verschillende sleutels, wanneer uw client entiteiten in verschillende sorteerorders moet ophalen en waar u elke entiteit identificeren met behulp van verschillende unieke waarden. U moet er echter zeker van zijn dat u de schaalbaarheidslimieten voor partities niet overschrijdt wanneer u entiteitsopzoekingen uitvoert met behulp van de verschillende **RowKey-waarden.**  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Secundair indexpatroon tussen partitie](#inter-partition-secondary-index-pattern)
* [Samengestelde sleutelpatroon](#compound-key-pattern)
* Entiteitgroeptransacties
* [Werken met heterogene entiteitstypen](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Secundair indexpatroon tussen partitie
Sla meerdere kopieën van elke entiteit op met verschillende **RowKey-waarden** in afzonderlijke partities of in afzonderlijke tabellen om snelle en efficiënte opzoekingen en alternatieve sorteerorders in te schakelen met behulp van verschillende **RowKey-waarden.**  

### <a name="context-and-problem"></a>Context en probleem
De tabelservice indexeert automatisch entiteiten met de waarden **PartitionKey** en **RowKey.** Hierdoor kan een clienttoepassing een entiteit efficiënt ophalen met behulp van deze waarden. Met behulp van de tabelstructuur die hieronder wordt weergegeven, kan een clienttoepassing bijvoorbeeld een puntquery gebruiken om een afzonderlijke werknemersentiteit op te halen met behulp van de afdelingsnaam en de werknemers-id (de **partitionkey-** en **rowkey-waarden).** Een client kan ook entiteiten ophalen die zijn gesorteerd op werknemers-id binnen elke afdeling.  

![Werknemer-id](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Als u ook een werknemer wilt kunnen vinden op basis van de waarde van een andere eigenschap, zoals e-mailadres, moet u een minder efficiënte partitiescan gebruiken om een overeenkomst te vinden. Dit komt omdat de tabelservice geen secundaire indexen biedt. Bovendien is er geen optie om een lijst met werknemers aan te vragen die in een andere volgorde zijn gesorteerd dan **RowKey-order.**  

U anticipeert op een groot aantal transacties ten opzichte van deze entiteiten en wilt het risico minimaliseren dat de tabelservice uw client beperkt.  

### <a name="solution"></a>Oplossing
Als u wilt werken aan het ontbreken van secundaire indexen, u meerdere kopieën van elke entiteit met elke kopie opslaan met behulp van verschillende **partitionkey-** en **rowkey-waarden.** Als u een entiteit opslaat met de onderstaande structuren, u op efficiënte wijze werknemersentiteiten ophalen op basis van e-mailadres of werknemers-id. Met de voorvoegselwaarden voor **partitionkey,** empid_ en email_ u bepalen welke index u voor een query wilt gebruiken.  

![Primaire index en secundaire index](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


De volgende twee filtercriteria (één op zoek naar werknemers-ID en één op zoek naar e-mailadres) geven beide puntquery's op:  

* $filter=(PartitionKey eq 'empid_Sales') en (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') en (RowKey eq 'jonesj@contoso.com')  

Als u een zoekopdracht opvraagt voor een reeks werknemersentiteiten, u een bereik opgeven dat is gesorteerd in de volgorde van de werknemer-id of een bereik dat is gesorteerd in e-mailadresvolgorde door te zoeken naar entiteiten met het juiste voorvoegsel in de **RowKey**.  

* Alle medewerkers in de afdeling Verkoop met een werknemers-ID in het bereik **000100** tot **000199** gesorteerd in gebruik van employee ID-order: $filter=(PartitionKey eq 'empid_Sales') en (RowKey ge '000100') en (RowKey le '000199')  
* Alle medewerkers op de afdeling Verkoop vinden met een e-mailadres dat begint met 'a' gesorteerd in gebruik van e-mailadressen: $filter=(PartitionKey eq 'email_Sales') en (RowKey ge 'a') en (RowKey lt 'b')  

De filtersyntaxis die in de bovenstaande voorbeelden wordt gebruikt, is afkomstig uit de API REST van de tabelservice, zie [Queryentiteiten](https://msdn.microsoft.com/library/azure/dd179421.aspx)voor meer informatie.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* U uw dubbele entiteiten uiteindelijk consistent met elkaar houden door het [patroon Uiteindelijk consistente transacties](#eventually-consistent-transactions-pattern) te gebruiken om de primaire en secundaire indexentiteiten te behouden.  
* Tabelopslag is relatief goedkoop te gebruiken, zodat de kosten overhead van het opslaan van dubbele gegevens mag niet een grote zorg. U moet echter altijd de kosten van uw ontwerp evalueren op basis van uw verwachte opslagvereisten en alleen dubbele entiteiten toevoegen ter ondersteuning van de query's die uw clienttoepassing uitvoert.  
* De waarde die voor de RowKey wordt **gebruikt,** moet uniek zijn voor elke entiteit. Overweeg samengestelde sleutelwaarden te gebruiken.  
* Opvulling van numerieke waarden in de **RowKey** (bijvoorbeeld de werknemers-ID 000223), maakt het correct sorteren en filteren mogelijk op basis van boven- en ondergrenzen.  
* U hoeft niet per se alle eigenschappen van uw entiteit te dupliceren. Als de query's die de entiteiten opzoeken met behulp van het e-mailadres in de **RowKey** bijvoorbeeld nooit de leeftijd van de werknemer nodig hebben, kunnen deze entiteiten de volgende structuur hebben:
  
   ![Werknemerentiteit (secundaire index)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Het is meestal beter om dubbele gegevens op te slaan en ervoor te zorgen dat u alle gegevens die u nodig hebt met één query ophalen dan om één query te gebruiken om een entiteit te vinden met behulp van de secundaire index en een andere om de vereiste gegevens in de primaire index op te zoeken.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer uw clienttoepassing entiteiten moet ophalen met behulp van verschillende sleutels, wanneer uw client entiteiten in verschillende sorteerorders moet ophalen en waar u elke entiteit identificeren met behulp van verschillende unieke waarden. Gebruik dit patroon wanneer u wilt voorkomen dat de schaalbaarheidslimieten voor partities worden overschreden wanneer u entiteitsopzoekingen uitvoert met de verschillende **RowKey-waarden.**  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Uiteindelijk consistent transactiepatroon](#eventually-consistent-transactions-pattern)  
* [Secundair indexpatroon binnen partitie](#intra-partition-secondary-index-pattern)  
* [Samengestelde sleutelpatroon](#compound-key-pattern)  
* Entiteitgroeptransacties  
* [Werken met heterogene entiteitstypen](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Uiteindelijk consistent transactiepatroon
Maak uiteindelijk consistent gedrag mogelijk over de grenzen van de partitie of de grenzen van het opslagsysteem met Azure-wachtrijen.  

### <a name="context-and-problem"></a>Context en probleem
EGT's maken atoomtransacties mogelijk tussen meerdere entiteiten die dezelfde partitiesleutel delen. Om prestatie- en schaalbaarheidsredenen u besluiten entiteiten met consistentievereisten op te slaan in afzonderlijke partities of in een afzonderlijk opslagsysteem: in een dergelijk scenario u geen EGT's gebruiken om consistentie te behouden. U bijvoorbeeld een vereiste hebben om de uiteindelijke consistentie tussen:  

* Entiteiten die zijn opgeslagen in twee verschillende partities in dezelfde tabel, in verschillende tabellen of in verschillende opslagaccounts.  
* Een entiteit die is opgeslagen in de tabelservice en een blob die is opgeslagen in de Blob-service.  
* Een entiteit die is opgeslagen in de tabelservice en een bestand in een bestandssysteem.  
* Een entiteit die is opgeslagen in de tabelservice en nog is geïndexeerd met de Azure Cognitive Search-service.  

### <a name="solution"></a>Oplossing
Door Azure-wachtrijen te gebruiken, u een oplossing implementeren die uiteindelijke consistentie biedt voor twee of meer partities of opslagsystemen.
Om deze aanpak te illustreren, gaat u ervan uit dat u een vereiste hebt om oude werknemersentiteiten te kunnen archiveren. Oude werknemersentiteiten worden zelden opgevraagd en moeten worden uitgesloten van activiteiten die betrekking hebben op huidige werknemers. Als u deze vereiste wilt implementeren, slaat u actieve medewerkers op in de **tabel Huidige** en oude werknemers in de **tabel Archief.** Voor het archiveren van een werknemer moet u de entiteit uit de **tabel Huidige** verwijderen en de entiteit toevoegen aan de **archieftabel,** maar u een EGT niet gebruiken om deze twee bewerkingen uit te voeren. Om het risico te vermijden dat een entiteit door een fout in beide of geen tabellen wordt weergegeven, moet de archiefbewerking uiteindelijk consistent zijn. In het volgende reeksdiagram worden de stappen in deze bewerking beschreven. Meer details is beschikbaar voor uitzonderingspaden in de volgende tekst.  

![Azure-wachtrijoplossing](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Een client initieert de archiefbewerking door een bericht in een Azure-wachtrij te plaatsen, in dit voorbeeld om #456 te archiveren. Een werknemersrol geeft een enquête in de wachtrij voor nieuwe berichten; wanneer het vindt een, het leest het bericht en laat een verborgen kopie op de wachtrij. De werkrol haalt vervolgens een kopie van de entiteit uit de **tabel Huidige** op, voegt een kopie in de **tabel Archief** in en verwijdert vervolgens het origineel uit de **tabel Huidige.** Als er ten slotte geen fouten zijn gemaakt in de vorige stappen, verwijdert de werkrol het verborgen bericht uit de wachtrij.  

In dit voorbeeld voegt stap 4 de werknemer in de **tabel Archief** in. Het kan de werknemer toevoegen aan een blob in de Blob-service of een bestand in een bestandssysteem.  

### <a name="recovering-from-failures"></a>Herstellen van storingen
Het is belangrijk dat de bewerkingen in stap **4** en **5** *idempotent* zijn voor het geval de rol van de werknemer de archiefbewerking opnieuw moet starten. Als u de tabelservice gebruikt, moet u voor stap **4** een bewerking 'invoegen of vervangen' gebruiken. voor stap **5** moet u een bewerking 'verwijderen als er bestaat' gebruiken in de clientbibliotheek die u gebruikt. Als u een ander opslagsysteem gebruikt, moet u een geschikte idempotente bewerking gebruiken.  

Als de werkrol stap **6**nooit voltooit, wordt het bericht na een time-out opnieuw weergegeven in de wachtrij die klaar is voor de rol van de werknemer om te proberen het opnieuw te verwerken. De rol van de werknemer kan controleren hoe vaak een bericht in de wachtrij is gelezen en, indien nodig, markeren is het een "gif" bericht voor onderzoek door het naar een aparte wachtrij te sturen. Zie Berichten ontvangen voor meer informatie over het lezen van wachtrijberichten en het controleren van het aantal [wachtrijen.](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

Sommige fouten uit de tabel- en wachtrijservices zijn tijdelijke fouten en uw clienttoepassing moet geschikte logica voor nieuwe toepassingen bevatten om deze te verwerken.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Deze oplossing voorziet niet in transactieisolatie. Een client kan bijvoorbeeld de tabellen **Huidige** en **Archief** lezen wanneer de werknemerrol zich tussen stap **4** en **5**bevond en een inconsistente weergave van de gegevens bekijken. De gegevens zullen uiteindelijk consistent zijn.  
* U moet er zeker van zijn dat stap 4 en 5 idempotent zijn om uiteindelijke consistentie te garanderen.  
* U de oplossing schalen met meerdere wachtrijen en rolinstanties voor werknemers.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u eventuele consistentie wilt garanderen tussen entiteiten die in verschillende partities of tabellen bestaan. U dit patroon uitbreiden om eventuele consistentie te garanderen voor bewerkingen in de tabelservice en de Blob-service en andere niet-Azure Storage-gegevensbronnen, zoals database of het bestandssysteem.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* Entiteitgroeptransacties  
* [Samenvoegen of vervangen](#merge-or-replace)  

> [!NOTE]
> Als transactieisolatie belangrijk is voor uw oplossing, moet u overwegen uw tabellen opnieuw te ontwerpen zodat u EGT's gebruiken.  
> 
> 

## <a name="index-entities-pattern"></a>Patroon van indexentiteiten
Indexentiteiten onderhouden om efficiënte zoekopdrachten in te schakelen die lijsten met entiteiten retourneren.  

### <a name="context-and-problem"></a>Context en probleem
De tabelservice indexeert automatisch entiteiten met de waarden **PartitionKey** en **RowKey.** Hierdoor kan een clienttoepassing een entiteit efficiënt ophalen met behulp van een puntquery. Met behulp van de onderstaande tabelstructuur kan een clienttoepassing bijvoorbeeld een afzonderlijke werknemersentiteit efficiënt ophalen met behulp van de afdelingsnaam en de werknemers-id (de **PartitionKey** en **RowKey).**  

![Werknemerentiteit](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Als u ook een lijst met werknemersentiteiten wilt kunnen ophalen op basis van de waarde van een andere niet-unieke eigenschap, zoals hun achternaam, moet u een minder efficiënte partitiescan gebruiken om overeenkomsten te vinden in plaats van een index te gebruiken om ze rechtstreeks op te zoeken. Dit komt omdat de tabelservice geen secundaire indexen biedt.  

### <a name="solution"></a>Oplossing
Als u opzoeken op achternaam wilt inschakelen met de bovenstaande entiteitsstructuur, moet u lijsten met werknemers-id's bijhouden. Als u de werknemersentiteiten met een bepaalde achternaam, zoals Jones, wilt ophalen, moet u eerst de lijst met werknemers-id's voor werknemers met Jones als achternaam vinden en vervolgens deze werknemersentiteiten ophalen. Er zijn drie belangrijke opties voor het opslaan van de lijsten met werknemers-geïdentificeerde gegevens:  

* Gebruik blobopslag.  
* Maak indexentiteiten in dezelfde partitie als de werknemersentiteiten.  
* Maak indexentiteiten in een afzonderlijke partitie of tabel.  

<u>Optie #1: Blob-opslag gebruiken</u>  

Voor de eerste optie maakt u een blob voor elke unieke achternaam en in elke blob slaat u een lijst op met de waarden **PartitionKey** (afdeling) en **RowKey** (employee ID) voor werknemers met die achternaam. Wanneer u een werknemer toevoegt of verwijdert, moet u ervoor zorgen dat de inhoud van de relevante blob uiteindelijk consistent is met de werknemersentiteiten.  

<u>Optie #2:</u> Indexentiteiten in dezelfde partitie maken  

Gebruik voor de tweede optie indexentiteiten die de volgende gegevens opslaan:  

![Entiteit werknemersindex](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

De eigenschap **EmployeeID's** bevat een lijst met werknemers-id's voor werknemers met de achternaam die is opgeslagen in de **RowKey.**  

In de volgende stappen wordt het proces beschreven dat u moet volgen wanneer u een nieuwe werknemer toevoegt als u de tweede optie gebruikt. In dit voorbeeld voegen we een werknemer toe met ID 000152 en een achternaam Jones op de afdeling Verkoop:  

1. Haal de indexentiteit op met een **partitionkey-waarde** 'Verkoop' en de waarde 'Jones' van **RowKey.** Sla de ETag van deze entiteit op om te gebruiken in stap 2.  
2. Maak een entiteitsgroeptransactie (dat wil zeggen een batchbewerking) die de nieuwe werknemersentiteit **(PartitionKey-waarde** 'Verkoop' en **RowKey-waarde** '000152') invoegt en de indexentiteit **(PartitionKey-waarde** 'Verkoop' en **RowKey-waarde** 'Jones') bijwerkt door de nieuwe werknemers-id toe te voegen aan de lijst in het veld EmployeeIDs. Zie Entiteitgroeptransacties voor meer informatie over entiteitsgroepstransacties.  
3. Als de entiteitsgroeptransactie mislukt vanwege een optimistische gelijktijdigheidsfout (iemand anders heeft zojuist de indexentiteit gewijzigd), moet u opnieuw beginnen bij stap 1.  

U een vergelijkbare benadering gebruiken om een werknemer te verwijderen als u de tweede optie gebruikt. Het wijzigen van de achternaam van een werknemer is iets complexer omdat u een entiteitsgroeptransactie moet uitvoeren die drie entiteiten bijwerkt: de werknemerentiteit, de indexentiteit voor de oude achternaam en de indexentiteit voor de nieuwe achternaam. U moet elke entiteit ophalen voordat u wijzigingen aanbrengt om de ETag-waarden op te halen die u vervolgens gebruiken om de updates uit te voeren met optimistische gelijktijdigheid.  

In de volgende stappen wordt het proces beschreven dat u moet volgen wanneer u alle werknemers met een bepaalde achternaam in een afdeling moet opzoeken als u de tweede optie gebruikt. In dit voorbeeld zoeken we alle medewerkers met achternaam Jones op in de afdeling Verkoop:  

1. Haal de indexentiteit op met een **partitionkey-waarde** 'Verkoop' en de waarde 'Jones' van **RowKey.**  
2. Ontwende de lijst met werknemers-id's in het veld EmployeeID's.  
3. Als u aanvullende informatie nodig hebt over elk van deze werknemers (zoals hun e-mailadressen), haalt u elk van de werknemersentiteiten op met **partitionkey-waarde** 'Verkoop' en **RowKey-waarden** uit de lijst met werknemers die u in stap 2 hebt verkregen.  

<u>Optie #3:</u> Indexentiteiten maken in een afzonderlijke partitie of tabel  

Gebruik voor de derde optie indexentiteiten die de volgende gegevens opslaan:  

![Entiteit van werknemersindex in een afzonderlijke partitie](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


De eigenschap **EmployeeID's** bevat een lijst met werknemers-id's voor werknemers met de achternaam die is opgeslagen in de **RowKey.**  

Met de derde optie u GEEN EGT's gebruiken om de consistentie te behouden omdat de indexentiteiten zich in een afzonderlijke partitie van de werknemersentiteiten bevinden. Zorg ervoor dat de indexentiteiten uiteindelijk consistent zijn met de werknemersentiteiten.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Deze oplossing vereist ten minste twee query's om overeenkomende entiteiten op te halen: één om de indexentiteiten op te vragen om de lijst met **RowKey-waarden** te verkrijgen en vervolgens query's op te vragen om elke entiteit in de lijst op te halen.  
* Aangezien een afzonderlijke entiteit een maximale grootte van 1 MB heeft, gaan de optie #2 en optie #3 in de oplossing ervan uit dat de lijst met werknemers-id's voor een bepaalde achternaam nooit groter is dan 1 MB. Als de lijst met werknemers-id's waarschijnlijk groter is dan 1 MB, gebruikt u de optie #1 en slaat u de indexgegevens op in blobopslag.  
* Als u optie#2 gebruikt (met BEHULP van EGT's om het toevoegen en verwijderen van werknemers te verwerken en de achternaam van een werknemer te wijzigen), moet u evalueren of het volume van transacties de schaalbaarheidslimieten in een bepaalde partitie zal benaderen. Als dit het geval is, moet u een uiteindelijk consistente oplossing overwegen (optie #1 of optie #3) die wachtrijen gebruikt om de updateaanvragen af te handelen en waarmee u uw indexentiteiten opslaan in een afzonderlijke partitie van de werknemersentiteiten.  
* Optie #2 in deze oplossing ervan uitgaat dat u op achternaam wilt opzoeken binnen een afdeling: u wilt bijvoorbeeld een lijst met werknemers met een achternaam Jones ophalen op de afdeling Verkoop. Als u alle werknemers met een achternaam Jones in de hele organisatie wilt kunnen opzoeken, gebruikt u optie #1 of optie #3.
* U een oplossing op basis van wachtrijen implementeren die uiteindelijkconsistentie oplevert (zie het [patroon Uiteindelijk consistente transacties](#eventually-consistent-transactions-pattern) voor meer details).  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u een set entiteiten wilt opzoeken die allemaal een gemeenschappelijke eigendomswaarde hebben, zoals alle werknemers met de achternaam Jones.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Samengestelde sleutelpatroon](#compound-key-pattern)  
* [Uiteindelijk consistent transactiepatroon](#eventually-consistent-transactions-pattern)  
* Entiteitgroeptransacties  
* [Werken met heterogene entiteitstypen](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Denormalisatiepatroon
Combineer gerelateerde gegevens samen in één entiteit, zodat u alle gegevens ophalen die u nodig hebt met één puntquery.  

### <a name="context-and-problem"></a>Context en probleem
In een relationele database normaliseert u doorgaans gegevens om duplicatie te verwijderen, wat resulteert in query's die gegevens uit meerdere tabellen ophalen. Als u uw gegevens normaliseert in Azure-tabellen, moet u meerdere retourvluchten van de client naar de server maken om uw gerelateerde gegevens op te halen. Met de onderstaande tabelstructuur hebt u bijvoorbeeld twee retourvluchten nodig om de details voor een afdeling op te halen: een om de afdelingsentiteit op te halen die de id van de manager bevat, en vervolgens een ander verzoek om de gegevens van de manager in een werknemerentiteit op te halen.  

![Afdelingsentiteit en werknemerentiteit](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Oplossing
In plaats van de gegevens op te slaan in twee afzonderlijke entiteiten, denormaliseert u de gegevens en bewaart u een kopie van de gegevens van de manager in de afdelingsentiteit. Bijvoorbeeld:  

![Afdelingsentiteit](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Met afdelingsentiteiten die met deze eigenschappen zijn opgeslagen, u nu alle gegevens die u nodig hebt over een afdeling ophalen met behulp van een puntquery.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Er zijn kosten overhead in verband met het opslaan van een aantal gegevens twee keer. Het prestatievoordeel (als gevolg van minder aanvragen voor de opslagservice) weegt doorgaans op tegen de marginale stijging van de opslagkosten (en deze kosten worden gedeeltelijk gecompenseerd door een vermindering van het aantal transacties dat u nodig hebt om de gegevens van een afdeling op te halen ).  
* U moet de consistentie behouden van de twee entiteiten die informatie over managers opslaan. U het consistentieprobleem afhandelen door EGT's te gebruiken om meerdere entiteiten in één atoomtransactie bij te werken: in dit geval worden de afdelingsentiteit en de werknemerentiteit voor de afdelingsmanager in dezelfde partitie opgeslagen.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u vaak gerelateerde informatie moet opzoeken. Dit patroon vermindert het aantal query's dat uw client moet maken om de benodigde gegevens op te halen.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Samengestelde sleutelpatroon](#compound-key-pattern)  
* Entiteitgroeptransacties  
* [Werken met heterogene entiteitstypen](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Samengestelde sleutelpatroon
Gebruik samengestelde **RowKey-waarden** om een client in staat te stellen gerelateerde gegevens op te zoeken met één puntquery.  

### <a name="context-and-problem"></a>Context en probleem
In een relationele database is het natuurlijk om joins in query's te gebruiken om gerelateerde gegevens in één query terug te sturen naar de client. U bijvoorbeeld de werknemers-id gebruiken om een lijst op te zoeken met verwante entiteiten die prestatie- en controlegegevens voor die werknemer bevatten.  

Stel dat u werknemersentiteiten opslaat in de tabelservice met behulp van de volgende structuur:  

![Entiteitsstructuur van werknemers](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

U moet ook historische gegevens met betrekking tot beoordelingen en prestaties opslaan voor elk jaar dat de werknemer voor uw organisatie heeft gewerkt en u moet toegang hebben tot deze informatie per jaar. Een optie is het maken van een andere tabel die entiteiten met de volgende structuur opslaat:  

![Alternatieve entiteitsstructuur van werknemers](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Merk op dat u met deze aanpak besluiten om bepaalde informatie (zoals voor- en achternaam) in de nieuwe entiteit te dupliceren, zodat u uw gegevens met één verzoek ophalen. U echter geen sterke consistentie behouden omdat u een EGT niet gebruiken om de twee entiteiten atoomtewijs bij te werken.  

### <a name="solution"></a>Oplossing
Sla een nieuw entiteitstype op in de oorspronkelijke tabel met entiteiten met de volgende structuur:  

![Oplossing voor de structuur van de entiteit van werknemers](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Merk op hoe de **RowKey** nu een samengestelde sleutel is die bestaat uit de werknemers-ID en het jaar van de controlegegevens waarmee u de prestaties van de werknemer ophalen en gegevens controleren met één verzoek voor één entiteit.  

In het volgende voorbeeld wordt beschreven hoe u alle controlegegevens voor een bepaalde werknemer ophalen (zoals werknemer 000123 op de afdeling Verkoop):  

$filter=(PartitionKey eq 'Sales') en (RowKey ge 'empid_000123') en (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating, Peer Rating, Comments  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* U moet een geschikt scheidingsteken gebruiken waarmee u de **RowKey-waarde** eenvoudig ontken: bijvoorbeeld **000123_2012**.  
* U slaat deze entiteit ook op in dezelfde partitie als andere entiteiten die gerelateerde gegevens voor dezelfde werknemer bevatten, wat betekent dat u EGT's gebruiken om een sterke consistentie te behouden.
* U moet overwegen hoe vaak u de gegevens opvraagt om te bepalen of dit patroon geschikt is.  Als u bijvoorbeeld zelden toegang krijgt tot de controlegegevens en de belangrijkste werknemersgegevens, moet u deze vaak als afzonderlijke entiteiten bewaren.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u een of meer verwante entiteiten moet opslaan die u regelmatig opvraagt.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* Entiteitgroeptransacties  
* [Werken met heterogene entiteitstypen](#working-with-heterogeneous-entity-types)  
* [Uiteindelijk consistent transactiepatroon](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Het staartpatroon van het logboek
Haal *n* de n-entiteiten op die onlangs aan een partitie zijn toegevoegd met behulp van een **RowKey-waarde** die wordt sorteert in omgekeerde datum- en tijdvolgorde.  

### <a name="context-and-problem"></a>Context en probleem
Een gemeenschappelijke vereiste is in staat geweest om de meest recent gemaakte entiteiten op te halen, bijvoorbeeld de 10 meest recente onkostenclaims die door een werknemer zijn ingediend. Tabelquery's ondersteunen een **$top** querybewerking om de eerste *n-entiteiten* uit een set terug te sturen: er is geen gelijkwaardige querybewerking om de laatste n-entiteiten in een set terug te sturen.  

### <a name="solution"></a>Oplossing
Sla de entiteiten op met behulp van een **RowKey** die van nature sorteert in omgekeerde datum/ tijdvolgorde met behulp van, zodat de meest recente vermelding altijd de eerste in de tabel is.  

Als u bijvoorbeeld de 10 meest recente onkostenclaims van een werknemer wilt ophalen, u een omgekeerde tekenwaarde gebruiken die is afgeleid van de huidige datum/tijd. In het volgende c#-codevoorbeeld wordt één manier weergegeven om een geschikte waarde 'omgekeerde teken' te maken voor een **RowKey** die sorteert van de meest recente tot de oudste:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

U teruggaan naar de datumtijdwaarde met behulp van de volgende code:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

De tabelquery ziet er als volgt uit:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* U moet de omgekeerde tekenwaarde met voorloopnullen plaatsen om ervoor te zorgen dat de tekenreekswaarde sorteert zoals verwacht.  
* U moet zich bewust zijn van de schaalbaarheidsdoelen op het niveau van een partitie. Zorg ervoor dat u geen hot spot-partities maakt.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u toegang moet krijgen tot entiteiten in omgekeerde datum/tijdvolgorde of wanneer u toegang moet krijgen tot de meest recent toegevoegde entiteiten.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Antipatroon voorbereiden / toevoegen](#prepend-append-anti-pattern)  
* [Entiteiten ophalen](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Patroon voor het verwijderen van hoge volumes
Een groot aantal entiteiten kunnen verwijderen door alle entiteiten op te slaan voor gelijktijdige verwijdering in hun eigen afzonderlijke tabel; u de entiteiten verwijdert door de tabel te verwijderen.  

### <a name="context-and-problem"></a>Context en probleem
Veel toepassingen verwijderen oude gegevens die niet langer beschikbaar hoeven te zijn voor een clienttoepassing of die de toepassing heeft gearchiveerd naar een ander opslagmedium. U identificeert dergelijke gegevens meestal op een datum: u hebt bijvoorbeeld een verplichting om records te verwijderen van alle aanmeldingsverzoeken die meer dan 60 dagen oud zijn.  

Een mogelijk ontwerp is het gebruik van de datum en het tijdstip van de aanmeldingsaanvraag in de **RowKey:**  

![Datum en tijd van inlogpoging](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Deze aanpak voorkomt partitiehotspots omdat de toepassing inlogentiteiten voor elke gebruiker in een afzonderlijke partitie kan invoegen en verwijderen. Deze aanpak kan echter kostbaar en tijdrovend zijn als u een groot aantal entiteiten hebt, omdat u eerst een tabelscan moet uitvoeren om alle entiteiten te identificeren die moeten worden verwijderd en vervolgens elke oude entiteit moet verwijderen. U het aantal retouren naar de server die nodig is om de oude entiteiten te verwijderen verminderen door meerdere verwijderingsaanvragen in ET's te batcheren.  

### <a name="solution"></a>Oplossing
Gebruik een aparte tabel voor elke dag van inlogpogingen. U het bovenstaande entiteitsontwerp gebruiken om hotspots te vermijden wanneer u entiteiten invoegt, en het verwijderen van oude entiteiten is nu gewoon een kwestie van elke dag één tabel verwijderen (één opslagbewerking) in plaats van honderden en duizenden afzonderlijke inloggen entiteiten elke dag.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Ondersteunt uw ontwerp andere manieren waarop uw toepassing de gegevens gebruikt, zoals het opzoeken van specifieke entiteiten, het koppelen met andere gegevens of het genereren van geaggregeerde informatie?  
* Vermijdt uw ontwerp hotspots wanneer u nieuwe entiteiten invoegt?  
* Verwacht een vertraging als u dezelfde tabelnaam opnieuw wilt gebruiken nadat u deze hebt verwijderd. Het is beter om altijd unieke tafelnamen te gebruiken.  
* Verwacht enige beperking wanneer u voor het eerst een nieuwe tabel gebruikt terwijl de tabelservice de toegangspatronen leert en de partities over knooppunten verdeelt. U moet overwegen hoe vaak u nieuwe tabellen moet maken.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u een groot aantal entiteiten hebt die u tegelijkertijd moet verwijderen.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* Entiteitgroeptransacties
* [Entiteiten wijzigen](#modifying-entities)  

## <a name="data-series-pattern"></a>Patroon van gegevensreeksen
Sla complete gegevensreeksen op in één entiteit om het aantal aanvragen dat u doet te minimaliseren.  

### <a name="context-and-problem"></a>Context en probleem
Een veelvoorkomend scenario is dat een toepassing een reeks gegevens opslaat die deze doorgaans in één keer moet ophalen. Uw toepassing kan bijvoorbeeld registreren hoeveel chatberichten elke werknemer elk uur verzendt en deze informatie vervolgens gebruiken om uit te leggen hoeveel berichten elke gebruiker in de afgelopen 24 uur heeft verzonden. Een ontwerp kan zijn om 24 entiteiten op te slaan voor elke werknemer:  

![24 entiteiten opslaan voor elke werknemer](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Met dit ontwerp u de entiteit eenvoudig vinden en bijwerken om voor elke werknemer bij te werken wanneer de toepassing de waarde voor het aantal berichten moet bijwerken. Als u echter de informatie wilt ophalen om een grafiek van de activiteit voor de voorgaande 24 uur te plotten, moet u 24 entiteiten ophalen.  

### <a name="solution"></a>Oplossing
Gebruik het volgende ontwerp met een afzonderlijke eigenschap om het aantal berichten voor elk uur op te slaan:  

![Entiteit berichtstatistieken](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Met dit ontwerp u een samenvoegbewerking gebruiken om het aantal berichten voor een werknemer gedurende een bepaald uur bij te werken. U nu alle informatie ophalen die u nodig hebt om de grafiek te plotten met een aanvraag voor één entiteit.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Als uw volledige gegevensreeks niet in één entiteit past (een entiteit kan maximaal 252 eigenschappen hebben), gebruikt u een alternatief gegevensarchief zoals een blob.  
* Als u meerdere clients tegelijk een entiteit hebt, moet u de **ETag** gebruiken om optimistische gelijktijdigheid te implementeren. Als u veel klanten hebt, u ervaren hoge twist.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u een gegevensreeks moet bijwerken en ophalen die is gekoppeld aan een afzonderlijke entiteit.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Patroon grote entiteiten](#large-entities-pattern)  
* [Samenvoegen of vervangen](#merge-or-replace)  
* [Uiteindelijk consistent transactiepatroon](#eventually-consistent-transactions-pattern) (als u de gegevensreeksen in een blob opslaat)  

## <a name="wide-entities-pattern"></a>Breed entiteitspatroon
Gebruik meerdere fysieke entiteiten om logische entiteiten met meer dan 252 eigenschappen op te slaan.  

### <a name="context-and-problem"></a>Context en probleem
Een afzonderlijke entiteit mag niet meer dan 252 eigenschappen hebben (met uitzondering van de verplichte systeemeigenschappen) en mag in totaal niet meer dan 1 MB aan gegevens opslaan. In een relationele database krijgt u doorgaans alle limieten voor de grootte van een rij omzeilen door een nieuwe tabel toe te voegen en een 1-op-1-relatie tussen deze gegevens af te dwingen.  

### <a name="solution"></a>Oplossing
Met de tabelservice u meerdere entiteiten opslaan om één groot bedrijfsobject met meer dan 252 eigenschappen weer te geven. Als u bijvoorbeeld een telling wilt opslaan van het aantal chatberichten dat elke werknemer de afgelopen 365 dagen heeft verzonden, u het volgende ontwerp gebruiken dat twee entiteiten met verschillende schema's gebruikt:  

![Meerdere entiteiten](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Als u een wijziging moet aanbrengen waarvoor beide entiteiten moeten worden bijgewerkt om ze met elkaar gesynchroniseerd te houden, u een EGT gebruiken. Anders u één samenvoegbewerking gebruiken om het aantal berichten voor een bepaalde dag bij te werken. Als u alle gegevens voor een individuele werknemer wilt ophalen, moet u beide entiteiten ophalen, wat u doen met twee efficiënte aanvragen die zowel een **PartitionKey-** als een **RowKey-waarde** gebruiken.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Het ophalen van een volledige logische entiteit omvat ten minste twee opslagtransacties: één om elke fysieke entiteit op te halen.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u entiteiten moet opslaan waarvan de grootte of het aantal eigenschappen de limieten voor een afzonderlijke entiteit in de tabelservice overschrijdt.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* Entiteitgroeptransacties
* [Samenvoegen of vervangen](#merge-or-replace)

## <a name="large-entities-pattern"></a>Patroon grote entiteiten
Gebruik blobopslag om grote eigenschapswaarden op te slaan.  

### <a name="context-and-problem"></a>Context en probleem
Een afzonderlijke entiteit mag in totaal niet meer dan 1 MB aan gegevens opslaan. Als een of meer van uw eigenschappen waarden opslaan waardoor de totale grootte van uw entiteit deze waarde overschrijdt, u de volledige entiteit niet opslaan in de tabelservice.  

### <a name="solution"></a>Oplossing
Als uw entiteit meer dan 1 MB groot is omdat een of meer eigenschappen een grote hoeveelheid gegevens bevatten, u gegevens opslaan in de Blob-service en vervolgens het adres van de blob opslaan in een eigenschap in de entiteit. U bijvoorbeeld de foto van een werknemer opslaan in blobopslag en een koppeling naar de foto opslaan in de eigenschap **Foto** van uw werknemerentiteit:  

![Foto, eigenschap](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Als u de uiteindelijke consistentie tussen de entiteit in de tabelservice en de gegevens in de Blob-service wilt behouden, gebruikt u het [patroon Uiteindelijk consistente transacties](#eventually-consistent-transactions-pattern) om uw entiteiten te behouden.
* Het ophalen van een volledige entiteit omvat ten minste twee opslagtransacties: één om de entiteit op te halen en één om de blobgegevens op te halen.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u entiteiten moet opslaan waarvan de grootte de limieten voor een afzonderlijke entiteit in de tabelservice overschrijdt.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Uiteindelijk consistent transactiepatroon](#eventually-consistent-transactions-pattern)  
* [Breed entiteitspatroon](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Antipatroon voorbereiden/toevoegen
Vergroot de schaalbaarheid wanneer u een hoog volume aan wisselplaten hebt door de inserts over meerdere partities te spreiden.  

### <a name="context-and-problem"></a>Context en probleem
Het vooraf gebruiken of toevoegen van entiteiten aan uw opgeslagen entiteiten resulteert meestal in de toepassing die nieuwe entiteiten toevoegt aan de eerste of laatste partitie van een reeks partities. In dit geval vinden alle inserts op een bepaald moment plaats in dezelfde partitie, waardoor een hotspot wordt gemaakt die voorkomt dat de tabelservice inserts over meerdere knooppunten laadt en mogelijk de schaalbaarheidsdoelen voor Partitie. Als u bijvoorbeeld een toepassing hebt die netwerk- en resourcetoegang door werknemers registreert, kan een entiteitsstructuur zoals hieronder worden weergegeven, ertoe leiden dat de partitie van het huidige uur een hotspot wordt als het volume van transacties het schaalbaarheidsdoel voor een afzonderlijke partitie:  

![Entiteitsstructuur](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Oplossing
De volgende alternatieve entiteitsstructuur voorkomt een hotspot op een bepaalde partitie terwijl de toepassing gebeurtenissen registreert:  

![Alternatieve entiteitsstructuur](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Let met dit voorbeeld op hoe zowel de **PartitionKey** als **RowKey** samengestelde sleutels zijn. De **PartitionKey** gebruikt zowel de afdeling als de werknemers-id om de logboekregistratie over meerdere partities te distribueren.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Ondersteunt de alternatieve sleutelstructuur die het maken van hot partities op inserts efficiënt ondersteunt bij de query's die uw clienttoepassing maakt?  
* Betekent uw verwachte volume van transacties dat u waarschijnlijk de schaalbaarheidsdoelstellingen voor een afzonderlijke partitie bereikt en wordt beperkt door de opslagservice?  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Vermijd het antipatroon voor het voorbereiden/toevoegen wanneer uw volume van transacties waarschijnlijk zal leiden tot beperking door de opslagservice wanneer u een hot partitie opent.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Samengestelde sleutelpatroon](#compound-key-pattern)  
* [Het staartpatroon van het logboek](#log-tail-pattern)  
* [Entiteiten wijzigen](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Antipatroon van loggegevens
Doorgaans moet u de Blob-service gebruiken in plaats van de tabelservice om logboekgegevens op te slaan.  

### <a name="context-and-problem"></a>Context en probleem
Een veelgebruikte use case voor loggegevens is het ophalen van een selectie logboekvermeldingen voor een specifiek datum-/tijdbereik: u wilt bijvoorbeeld alle fout- en kritieke berichten vinden die uw toepassing tussen 15:04 en 15:06 op een specifieke datum heeft geregistreerd. U wilt de datum en tijd van het logboekbericht niet gebruiken om de partitie te bepalen waarop u logboekentiteiten opslaat: dat resulteert in een hete partitie, omdat op een gegeven moment alle logentiteiten dezelfde **partitionkey-waarde** delen (zie de sectie [Anti-patroon prepend/toevoegen).](#prepend-append-anti-pattern) Het volgende entiteitsschema voor een logboekbericht resulteert bijvoorbeeld in een hot partitie omdat de toepassing alle logboekberichten naar de partitie schrijft voor de huidige datum en het huidige uur:  

![Entiteit voor logboekberichten](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

In dit voorbeeld bevat de **RowKey** de datum en tijd van het logboekbericht om ervoor te zorgen dat logboekberichten worden opgeslagen gesorteerd in datum/tijdvolgorde en bevat een bericht-id voor het geval meerdere logboekberichten dezelfde datum en tijd delen.  

Een andere benadering is het gebruik van een **PartitionKey** die ervoor zorgt dat de toepassing berichten schrijft over een reeks partities. Als de bron van het logboekbericht bijvoorbeeld een manier biedt om berichten over veel partities te distribueren, u het volgende entiteitsschema gebruiken:  

![Entiteit alternatief logboekbericht](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

Het probleem met dit schema is echter dat u elke partitie in de tabel moet doorzoeken om alle logboekberichten voor een bepaalde tijdspanne op te halen.

### <a name="solution"></a>Oplossing
In het vorige gedeelte werd gewezen op het probleem van het gebruik van de tabelservice om logboekvermeldingen op te slaan en werden twee, onbevredigende ontwerpen voorgesteld. Een oplossing leidde tot een hete partitie met het risico van slechte prestaties schrijven log berichten; de andere oplossing resulteerde in slechte queryprestaties vanwege de vereiste om elke partitie in de tabel te scannen om logboekberichten op te halen voor een specifieke tijdspanne. Blob-opslag biedt een betere oplossing voor dit type scenario en zo slaat Azure Storage Analytics de logboekgegevens op die het verzamelt.  

In dit gedeelte wordt beschreven hoe Storage Analytics loggegevens opslaat in blob-opslag als illustratie van deze benadering voor het opslaan van gegevens die u doorgaans per bereik opvraagt.  

Storage Analytics slaat logboekberichten op in een afgebakende indeling in meerdere blobs. De afgebakende indeling maakt het gemakkelijk voor een clienttoepassing om de gegevens in het logboekbericht te ontindirecten.  

Storage Analytics gebruikt een naamgevingsconventie voor blobs waarmee u de blob (of blobs) vinden die de logboekberichten bevatten waarvoor u zoekt. Een blob met de naam 'wachtrij/2014/07/31/1800/000001.log' bevat bijvoorbeeld logboekberichten die betrekking hebben op de wachtrijservice voor het uur dat begint om 18:00 uur op 31 juli 2014. De "000001" geeft aan dat dit het eerste logboekbestand voor deze periode is. Storage Analytics registreert ook de tijdstempels van de eerste en laatste logboekberichten die in het bestand zijn opgeslagen als onderdeel van de metagegevens van de blob. Met de API voor blobopslag u blobs in een container vinden op basis van een naamvoorvoegsel: om alle blobs te vinden die wachtrijlogboekgegevens bevatten voor het uur dat om 18:00 uur begint, u het voorvoegsel 'queue/2014/07/31/1800' gebruiken.  

Storage Analytics buffert logberichten intern en werkt vervolgens periodiek de juiste blob bij of maakt een nieuwe met de nieuwste batch logboekvermeldingen. Dit vermindert het aantal schrijfbewerkingen dat moet worden uitgevoerd naar de blobservice.  

Als u een soortgelijke oplossing implementeert in uw eigen toepassing, moet u overwegen hoe u de afweging beheren tussen betrouwbaarheid (het schrijven van elke logboekvermelding voor blob-opslag als het gebeurt) en kosten en schaalbaarheid (het bufferen van updates in uw toepassing en schrijven deze blob opslag in batches).  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Houd rekening met de volgende punten bij het bepalen hoe logboekgegevens moeten worden opgeslagen:  

* Als u een tabelontwerp maakt dat potentiële hot partities vermijdt, u merken dat u uw logboekgegevens niet efficiënt openen.  
* Om logboekgegevens te verwerken, moet een client vaak veel records laden.  
* Hoewel logboekgegevens vaak gestructureerd zijn, kan blobopslag een betere oplossing zijn.  

## <a name="implementation-considerations"></a>Afwegingen bij de implementatie
In dit gedeelte worden enkele overwegingen besproken om rekening mee te houden wanneer u de in de vorige secties beschreven patronen implementeert. De meeste van deze sectie maakt gebruik van voorbeelden geschreven in C# die gebruik maken van de Storage Client Library (versie 4.3.0 op het moment van schrijven).  

## <a name="retrieving-entities"></a>Entiteiten ophalen
Zoals besproken in de sectie Ontwerp voor query's, is de meest efficiënte query een puntquery. In sommige scenario's moet u echter mogelijk meerdere entiteiten ophalen. In deze sectie worden enkele veelvoorkomende benaderingen beschreven voor het ophalen van entiteiten met behulp van de opslagclientbibliotheek.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Een puntquery uitvoeren met de opslagclientbibliotheek
De eenvoudigste manier om een puntquery uit te voeren, is door de bewerking **Tabel ophalen** te gebruiken zoals weergegeven in het volgende C#-codefragment waarmee een entiteit met een **partitionkey** van waarde 'Verkoop' en een **RowKey** van waarde "212" wordt opgehaald:  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Merk op hoe dit voorbeeld verwacht dat de entiteit die het ophaalt van het type **EmployeeEntity**is.  

### <a name="retrieving-multiple-entities-using-linq"></a>Meerdere entiteiten ophalen met LINQ
U LINQ gebruiken om meerdere entiteiten uit de tabelservice op te halen wanneer u werkt met microsoft Azure Cosmos Table Standard Library. 

```azurecli
dotnet add package Microsoft.Azure.Cosmos.Table
```

Als u de onderstaande voorbeelden wilt laten werken, moet u naamruimten opnemen:

```csharp
using System.Linq;
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Azure.Cosmos.Table.Queryable;
```

De employeeTable is een CloudTable-object\<waarmee een methode CreateQuery ITableEntity\<>() wordt geïmplementeerd, waarmee een TableQuery ITableEntity> wordt geretourneerd. Objecten van dit type implementeren een IQueryable en maken het gebruik van zowel LINQ Query Expressions en dot notatie syntax.

Het ophalen van meerdere entiteiten en worden bereikt door het opgeven van een query met een **waar** clausule. Om een tabelscan te voorkomen, moet u altijd de **partitionkey-waarde** opnemen in de waar-clausule en, indien mogelijk, de **RowKey-waarde** om tabel- en partitiescans te voorkomen. De tabelservice ondersteunt een beperkt aantal vergelijkingsoperatoren (groter dan, groter dan of gelijk, minder dan, kleiner dan of gelijk, gelijk en niet gelijk) om te gebruiken in de waar-clausule. 

In het volgende C#-codefragment worden alle werknemers gevonden waarvan de achternaam begint met 'B' (ervan uitgaande dat de **RowKey** de achternaam opslaat) in de verkoopafdeling (uitgaande van de **partitionkey** slaat de afdelingsnaam op):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
            
var employees = query.Execute();  
```

Let op hoe de query zowel een **RowKey** als een **partitionkey** opgeeft om betere prestaties te garanderen.  

In het volgende codevoorbeeld wordt gelijkwaardige functionaliteit weergegeven zonder de syntaxis van LINQ te gebruiken:  

```csharp
TableQuery<EmployeeEntity> employeeQuery = 
    new TableQuery<EmployeeEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B")),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")));
            
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> Het voorbeeld genest meerdere **CombineFilters-methoden** om de drie filtervoorwaarden op te nemen.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Grote aantallen entiteiten uit een query ophalen
Een optimale query retourneert een afzonderlijke entiteit op basis van een **PartitionKey-waarde** en een **RowKey-waarde.** In sommige scenario's u echter een vereiste hebben om veel entiteiten terug te sturen van dezelfde partitie of zelfs van veel partities.  

U moet de prestaties van uw toepassing altijd volledig testen in dergelijke scenario's.  

Een query ten opzichte van de tabelservice kan maximaal 1.000 entiteiten tegelijk retourneren en maximaal vijf seconden worden uitgevoerd. Als de resultaatset meer dan 1.000 entiteiten bevat, als de query niet binnen vijf seconden is voltooid of als de query de partitiegrens overschrijdt, retourneert de tabelservice een vervolgtoken om de clienttoepassing in staat te stellen de volgende set entiteiten aan te vragen. Zie [Time-out voor query's en paginatie](https://msdn.microsoft.com/library/azure/dd135718.aspx)voor meer informatie over hoe vervolgtokens werken.  

Als u de opslagclientbibliotheek gebruikt, kan deze automatisch vervolgtokens voor u verwerken omdat entiteiten uit de tabelservice worden geretourneerd. In het volgende c-codevoorbeeld met de opslagclientbibliotheek worden automatisch vervolgtokens verwerkt als de tabelservice deze in een reactie retourneert:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
    // ...
}  
```

De volgende C#-code verwerkt expliciet vervolgtokens:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;
do
{
    var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
        // ...
    }
    
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Door vervolgtokens expliciet te gebruiken, u bepalen wanneer uw toepassing het volgende segment van gegevens ophaalt. Als uw clienttoepassing gebruikers bijvoorbeeld in staat stelt om de entiteiten te bekijken die in een tabel zijn opgeslagen, kan een gebruiker besluiten om niet door alle entiteiten te pagina's die door de query zijn opgehaald, zodat uw toepassing alleen een vervolgtoken gebruikt om het volgende segment op te halen wanneer de gebruiker was klaar met paging door alle entiteiten in het huidige segment. Deze aanpak heeft verschillende voordelen:  

* Hiermee u de hoeveelheid gegevens beperken die u uit de tabelservice ophalen en die u over het netwerk verplaatst.  
* Hiermee u asynchrone IO uitvoeren in .NET.  
* Hiermee u het vervolgtoken serialiseren naar permanente opslag, zodat u doorgaan in het geval van een crash van een toepassing.  

> [!NOTE]
> Een vervolgtoken retourneert doorgaans een segment met 1.000 entiteiten, hoewel dit mogelijk minder is. Dit is ook het geval als u het aantal vermeldingen van een query retourneert met **Take** om de eerste n-entiteiten terug te sturen die overeenkomen met uw opzoekcriteria: de tabelservice kan een segment met minder dan n-entiteiten retourneren, samen met een vervolgtoken, zodat u de resterende entiteiten ophalen.  
> 
> 

In de volgende C#-code ziet u hoe u het aantal entiteiten dat binnen een segment is geretourneerd, wijzigt:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Projectie aan de serverzijde
Eén entiteit kan maximaal 255 eigenschappen hebben en maximaal 1 MB groot zijn. Wanneer u de tabel opvraagt en entiteiten ophaalt, hebt u mogelijk niet alle eigenschappen nodig en u voorkomen dat gegevens onnodig worden overgedragen (om de latentie en kosten te verminderen). U server-side projectie gebruiken om alleen de eigenschappen over te dragen die u nodig hebt. In het volgende voorbeeld wordt alleen de eigenschap **E-mail** opgehaald (samen met **PartitionKey,** **RowKey**, **Timestamp**en **ETag)** uit de entiteiten die door de query zijn geselecteerd.  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
    new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
    Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Merk op hoe de **RowKey-waarde** beschikbaar is, ook al is deze niet opgenomen in de lijst met eigenschappen die u wilt ophalen.  

## <a name="modifying-entities"></a>Entiteiten wijzigen
Met de opslagclientbibliotheek u uw entiteiten wijzigen die zijn opgeslagen in de tabelservice door entiteiten in te voegen, te verwijderen en bij te werken. U EGT's gebruiken om meerdere inserts te batchn, bewerkingen samen te werken en te verwijderen om het aantal vereiste retourvluchten te verminderen en de prestaties van uw oplossing te verbeteren.  

Uitzonderingen die worden gegenereerd wanneer de opslagclientbibliotheek een EGT uitvoert, bevatten doorgaans de index van de entiteit waardoor de batch is mislukt. Dit is handig wanneer u code debugging die EGTs gebruikt.  

U moet ook overwegen hoe uw ontwerp van invloed is op de manier waarop uw clienttoepassing omgaat met gelijktijdigheid en updatebewerkingen.  

### <a name="managing-concurrency"></a>Gelijktijdigheid beheren
Standaard implementeert de tabelservice optimistische gelijktijdigheidscontroles op het niveau van afzonderlijke entiteiten voor **bewerkingen invoegen,** **Samenvoegen**en **Verwijderen,** hoewel het mogelijk is voor een client om de tabelservice te dwingen deze controles te omzeilen. Zie [Gelijktijdigheid beheren in Microsoft Azure Storage](../../storage/common/storage-concurrency.md)voor meer informatie over hoe de tabelservice gelijktijdigheid beheert.  

### <a name="merge-or-replace"></a>Samenvoegen of vervangen
De methode **Vervangen** van de klasse **TableOperation** vervangt altijd de volledige entiteit in de tabelservice. Als u een eigenschap niet opneemt in de aanvraag wanneer die eigenschap in de opgeslagen entiteit bestaat, verwijdert de aanvraag die eigenschap uit de opgeslagen entiteit. Tenzij u een eigenschap expliciet uit een opgeslagen entiteit wilt verwijderen, moet u elke eigenschap in het verzoek opnemen.  

U de methode **Samenvoegen** van de klasse **TableOperation** gebruiken om de hoeveelheid gegevens die u naar de tabelservice verzendt, te verminderen wanneer u een entiteit wilt bijwerken. De **methode Samenvoegen** vervangt alle eigenschappen in de opgeslagen entiteit door eigenschapswaarden van de entiteit die in de aanvraag is opgenomen, maar laat alle eigenschappen in de opgeslagen entiteit intact die niet in de aanvraag zijn opgenomen. Dit is handig als u grote entiteiten hebt en slechts een klein aantal eigenschappen in een aanvraag hoeft bij te werken.  

> [!NOTE]
> De methoden **Vervangen** en **samenvoegen** mislukken als de entiteit niet bestaat. Als alternatief u de methoden **InsertOrReplace** en **InsertOrMerge** gebruiken die een nieuwe entiteit maken als deze niet bestaat.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Werken met heterogene entiteitstypen
De tabelservice is een *tabelarchief zonder schema* dat betekent dat één tabel entiteiten van meerdere typen kan opslaan die een grote flexibiliteit in uw ontwerp bieden. In het volgende voorbeeld wordt een tabel geïllustreerd die zowel werknemers- als afdelingsentiteiten opslaat:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Tijdstempel</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Leeftijd</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Leeftijd</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>Aantal werknemers</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Leeftijd</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Elke entiteit moet nog steeds **partitionkey-,** **rowkey-** en **tijdstempelwaarden** hebben, maar kan een set eigenschappen hebben. Bovendien is er niets dat het type entiteit aangeeft, tenzij u ervoor kiest om die informatie ergens op te slaan. Er zijn twee opties voor het identificeren van het entiteitstype:  

* Het entiteitstype voorbereiden op de **RowKey** (of mogelijk de **PartitionKey).** Bijvoorbeeld **EMPLOYEE_000123** of **DEPARTMENT_SALES** als **RowKey-waarden.**  
* Gebruik een afzonderlijke eigenschap om het entiteitstype vast te leggen zoals weergegeven in de onderstaande tabel.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Tijdstempel</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Leeftijd</th>
<th>Email</th>
</tr>
<tr>
<td>Werknemer</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Leeftijd</th>
<th>Email</th>
</tr>
<tr>
<td>Werknemer</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>Aantal werknemers</th>
</tr>
<tr>
<td>Afdeling</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Leeftijd</th>
<th>Email</th>
</tr>
<tr>
<td>Werknemer</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

De eerste optie, die afhankelijk is van het entiteitstype naar de **RowKey,** is handig als er een mogelijkheid is dat twee entiteiten van verschillende typen dezelfde sleutelwaarde hebben. Het groepeert ook entiteiten van hetzelfde type samen in de partitie.  

De technieken besproken in deze sectie zijn vooral relevant voor de discussie [Erfenis relaties](table-storage-design-modeling.md#inheritance-relationships) eerder in deze gids in het artikel [Modellering relaties](table-storage-design-modeling.md).  

> [!NOTE]
> U moet overwegen een versienummer op te nemen in de waarde van het entiteitstype om clienttoepassingen in staat te stellen POCO-objecten te ontwikkelen en met verschillende versies te werken.  
> 
> 

In de rest van deze sectie worden enkele functies in de opslagclientbibliotheek beschreven die het werken met meerdere entiteitstypen in dezelfde tabel vergemakkelijken.  

### <a name="retrieving-heterogeneous-entity-types"></a>Heterogene entiteitstypen ophalen
Als u de opslagclientbibliotheek gebruikt, hebt u drie opties voor het werken met meerdere entiteitstypen.  

Als u het type entiteit kent dat is opgeslagen met een specifieke **RowKey-** en **PartitionKey-waarden,** u het entiteitstype opgeven wanneer u de entiteit ophaalt zoals weergegeven in de vorige twee voorbeelden die entiteiten van type **EmployeeEntity**ophalen: [Een puntquery uitvoeren met de opslagclientbibliotheek](#executing-a-point-query-using-the-storage-client-library) en [meerdere entiteiten ophalen met LINQ](#retrieving-multiple-entities-using-linq).  

De tweede optie is het **dynamictableentitytype** (een eigenschappenzak) te gebruiken in plaats van een concreet POCO-entiteitstype (deze optie kan ook de prestaties verbeteren omdat het niet nodig is de entiteit te serialiseren en te deserialiseren naar .NET-typen). Met de volgende C#-code worden mogelijk meerdere entiteiten van verschillende typen uit de tabel opgehaald, maar worden alle entiteiten geretourneerd als **DynamicTableEntity-instanties.** Vervolgens wordt de eigenschap **EntityType** gebruikt om het type van elke entiteit te bepalen:  

```csharp
string filter =
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
        TableOperators.And,
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "F")));
        
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
    
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
    EntityProperty entityTypeProperty;
    if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
    {
        if (entityTypeProperty.StringValue == "Employee")
        {
            // use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Als u andere eigenschappen wilt ophalen, moet u de methode **TryGetValue** gebruiken op de eigenschap **Properties** van de klasse **DynamicTableEntity.**  

Een derde optie is het combineren met het type **DynamicTableEntity** en een instantie **EntityResolver.** Hiermee u meerdere POCO-typen in dezelfde query oplossen. In dit voorbeeld gebruikt de **delegate EntityResolver** de eigenschap **EntityType** om onderscheid te maken tussen de twee typen entiteiten die de query retourneert. De methode **Oplossen** gebruikt de **resolver-gemachtigde** om de instanties **van DynamicTableEntity** op te lossen in **tabelentiteitinstanties.**  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{
    TableEntity resolvedEntity = null;
    if (props["EntityType"].StringValue == "Department")
    {
        resolvedEntity = new DepartmentEntity();
    }
    else if (props["EntityType"].StringValue == "Employee")
    {
        resolvedEntity = new EmployeeEntity();
    }
    else 
    {
        throw new ArgumentException("Unrecognized entity", "props");
    }

    resolvedEntity.PartitionKey = pk;
    resolvedEntity.RowKey = rk;
    resolvedEntity.Timestamp = ts;
    resolvedEntity.ETag = etag;
    resolvedEntity.ReadEntity(props, null);
    return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
        
TableQuery<DynamicTableEntity> entityQuery = new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
    if (e is DepartmentEntity)
    {
        // ...
    }
    else if (e is EmployeeEntity)
    {
        // ...
    }
}  
```

### <a name="modifying-heterogeneous-entity-types"></a>Heterogene entiteitstypen wijzigen
U hoeft niet te weten welk type entiteit deze moet verwijderen en u weet altijd het type entiteit wanneer u deze invoegt. U **dynamictableentity-type** echter gebruiken om een entiteit bij te werken zonder het type ervan te kennen en zonder een POCO-entiteitsklasse te gebruiken. In het volgende codevoorbeeld wordt één entiteit opgehaald en wordt gecontroleerd of de eigenschap **EmployeeCount** bestaat voordat deze wordt bijgewerkt.  

```csharp
TableResult result = employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;
if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
    throw new InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}

countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));
```

## <a name="controlling-access-with-shared-access-signatures"></a>Toegang beheren met gedeelde toegangshandtekeningen
U SAS-tokens (Shared Access Signature) gebruiken om clienttoepassingen in staat te stellen entiteiten (en query's) te wijzigen zonder dat u uw opslagaccountsleutel in uw code hoeft op te nemen. Meestal zijn er drie belangrijke voordelen aan het gebruik van SAS in uw toepassing:  

* U hoeft uw opslagaccountsleutel niet te distribueren naar een onveilig platform (zoals een mobiel apparaat) om dat apparaat toegang te geven tot entiteiten in de tabelservice en deze te wijzigen.  
* U een deel van het werk dat web- en werknemersrollen uitvoeren bij het beheren van uw entiteiten naar clientapparaten zoals computers van eindgebruikers en mobiele apparaten, ontladen.  
* U een beperkte en tijdbeperkte set machtigingen toewijzen aan een client (zoals alleen-lezen toegang geven tot specifieke bronnen).  

Zie [Gedeelde toegangshandtekeningen (SAS) gebruiken voor](../../storage/common/storage-sas-overview.md)meer informatie over het gebruik van SAS-tokens met de tabelservice.  

U moet echter nog steeds de SAS-tokens genereren die een clienttoepassing verlenen aan de entiteiten in de tabelservice: u moet dit doen in een omgeving die beveiligde toegang heeft tot uw opslagaccountsleutels. Doorgaans gebruikt u een web- of werknemersrol om de SAS-tokens te genereren en deze te leveren aan de clienttoepassingen die toegang tot uw entiteiten nodig hebben. Omdat er nog steeds een overhead betrokken is bij het genereren en leveren van SAS-tokens aan klanten, moet u overwegen hoe u deze overhead het beste verminderen, vooral in scenario's met een hoog volume.  

Het is mogelijk om een SAS-token te genereren dat toegang verleent tot een subset van de entiteiten in een tabel. Standaard maakt u een SAS-token voor een hele tabel, maar het is ook mogelijk om op te geven dat het SAS-token toegang verleent tot een reeks **PartitionKey-waarden** of een reeks **partitionkey-** en **rowkey-waarden.** U ervoor kiezen om SAS-tokens te genereren voor individuele gebruikers van uw systeem, zodat het SAS-token van elke gebruiker hen alleen toegang geeft tot hun eigen entiteiten in de tabelservice.  

## <a name="asynchronous-and-parallel-operations"></a>Asynchrone en parallelle bewerkingen
Op voorwaarde dat u uw aanvragen over meerdere partities verspreidt, u de doorvoer en de responsiviteit van de client verbeteren door asynchrone of parallelle query's te gebruiken.
U hebt bijvoorbeeld twee of meer exemplaren van de werkrol die toegang hebben tot uw tabellen parallel. U afzonderlijke werknemersrollen hebben die verantwoordelijk zijn voor bepaalde sets partities, of gewoon meerdere functie-exemplaren van werknemers hebben, die elk toegang hebben tot alle partities in een tabel.  

Binnen een clientinstantie u de doorvoer verbeteren door opslagbewerkingen asynchroon uit te voeren. De opslagclientbibliotheek maakt het eenvoudig om asynchrone query's en wijzigingen te schrijven. U bijvoorbeeld beginnen met de synchrone methode waarmee alle entiteiten in een partitie worden opgehaald, zoals weergegeven in de volgende C#-code:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

    TableContinuationToken continuationToken = null;
    do
    {
        var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
        
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

U deze code eenvoudig wijzigen zodat de query als volgt asynchroon wordt uitgevoerd:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);
    
    TableContinuationToken continuationToken = null;
    do
    {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
    
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

In dit asynchrone voorbeeld ziet u de volgende wijzigingen van de synchrone versie:  

* De methodehandtekening bevat nu de **async-modifier** en retourneert een **taakinstantie.**  
* In plaats van de **methode ExecuteSegmented** aan te roepen om resultaten op te halen, roept de methode nu de methode **ExecuteSegmentedAsync** aan en gebruikt de **wachtmodifier** om resultaten asynchroon op te halen.  

De clienttoepassing kan deze methode meerdere keren aanroepen (met verschillende waarden voor de **afdelingsparameter)** en elke query wordt uitgevoerd op een afzonderlijke thread.  

Er is geen asynchrone versie van de methode **Uitvoeren** in de klasse **TabelQuery** omdat de **IEnumerable-interface** geen asynchrone opsomming ondersteunt.  

U entiteiten ook asynchroon invoegen, bijwerken en verwijderen. In het volgende c#-voorbeeld wordt een eenvoudige, synchrone methode weergegeven om een werknemerentiteit in te voegen of te vervangen:  

```csharp
private static void SimpleEmployeeUpsert(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = employeeTable.Execute(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

U deze code eenvoudig wijzigen zodat de update als volgt wordt uitgevoerd:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = await employeeTable.ExecuteAsync(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

In dit asynchrone voorbeeld ziet u de volgende wijzigingen van de synchrone versie:  

* De methodehandtekening bevat nu de **async-modifier** en retourneert een **taakinstantie.**  
* In plaats van de methode **Uitvoeren** aan te roepen om de entiteit bij te werken, roept de methode **executeasync** nu aan en gebruikt de **wachtmodifier** om resultaten asynchroon op te halen.  

De clienttoepassing kan meerdere asynchrone methoden zoals deze aanroepen en elke methodeaanroep wordt uitgevoerd op een afzonderlijke thread.  

## <a name="next-steps"></a>Volgende stappen

- [Relaties modelleren](table-storage-design-modeling.md)
- [Ontwerp voor query's](table-storage-design-for-query.md)
- [Tabelgegevens versleutelen](table-storage-design-encrypt-data.md)
- [Ontwerp voor gegevenswijziging](table-storage-design-for-modification.md)
