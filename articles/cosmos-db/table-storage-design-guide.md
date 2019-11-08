---
title: Azure Cosmos DB tabellen ontwerpen ter ondersteuning van schaal baarheid en prestaties
description: 'Ontwerp handleiding voor Azure Storage: het ontwerpen van schaal bare en uitvoerende tabellen in Azure Cosmos DB en Azure Storage tabel'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: wmengmsft
ms.author: wmeng
ms.custom: seodec18
ms.openlocfilehash: 499ac3a394339ebb07c36abeaaa761de22927941
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827779"
---
# <a name="azure-storage-table-design-guide-designing-scalable-and-performant-tables"></a>Ontwerp handleiding voor Azure Storage tabellen: schaal bare en uitvoerende tabellen ontwerpen

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Voor het ontwerpen van schaal bare en uitvoerende tabellen moet u rekening houden met een aantal factoren zoals prestaties, schaal baarheid en kosten. Als u eerder schema's hebt ontworpen voor relationele data bases, zijn deze overwegingen bekend voor u, maar hoewel er wat overeenkomsten zijn tussen het Azure Table service-opslag model en relationele modellen, zijn er ook veel belang rijke verschillen. Deze verschillen leiden doorgaans tot verschillende ontwerpen waarvan de teller intuïtief of onjuist kan worden weer geven aan iemand die vertrouwd is met relationele data bases, maar dat is wel handig als u ontwerpt voor een NoSQL sleutel/waarde-archief zoals Azure Table service. Veel van uw ontwerp verschillen zijn het feit dat de Table service is ontworpen voor de ondersteuning van toepassingen op Cloud schaal die miljarden entiteiten kunnen bevatten (rijen in relationele data base terminologie) van gegevens of voor gegevens sets die hoge trans acties moeten ondersteunen volumes: daarom moet u anders nadenken over hoe u uw gegevens opslaat en weet hoe de Table service werkt. Een goed ontworpen NoSQL-gegevens archief kan ervoor zorgen dat uw oplossing veel verder kan schalen (en tegen lagere kosten) dan een oplossing die gebruikmaakt van een relationele data base. Deze hand leiding helpt u bij deze onderwerpen.  

## <a name="about-the-azure-table-service"></a>Over de Azure Table service
Deze sectie bevat een overzicht van enkele belang rijke functies van de Table service die vooral relevant zijn voor het ontwerpen van prestaties en schaal baarheid. Als u nog niet bekend bent met Azure Storage en de Table service, lees dan eerst [Inleiding tot Microsoft Azure Storage](../storage/common/storage-introduction.md) en aan de [slag met Azure Table Storage met behulp van .net voordat u](table-storage-how-to-use-dotnet.md) de rest van dit artikel leest. Hoewel de focus van deze hand leiding zich op het Table service bevindt, bevat deze een discussie van de Azure-wachtrij en BLOB-Services, en hoe u deze kunt gebruiken samen met de Table service in een oplossing.  

Wat is de Table service? Zoals u kunt verwachten van de naam, gebruikt het Table service een tabel indeling om gegevens op te slaan. In de standaard terminologie vertegenwoordigt elke rij van de tabel een entiteit, en in de kolommen worden de verschillende eigenschappen van die entiteit opgeslagen. Elke entiteit heeft een paar sleutels om deze uniek te identificeren en een time stamp-kolom die de Table service gebruikt om bij te houden wanneer de entiteit voor het laatst is bijgewerkt (het tijds tempel veld wordt automatisch toegevoegd en u kunt de tijds tempel niet hand matig overschrijven met een wille keurige waarde). De Table service maakt gebruik van deze laatst gewijzigde tijds tempel (LMT) om optimistische gelijktijdigheid te beheren.  

> [!NOTE]
> De Table service-REST API bewerkingen retour neren ook een **ETAG** -waarde die is afgeleid van de tijds tempel van de laatste wijziging (LMT). In dit document ziet u de termen ETag en LMT door elkaar, omdat ze verwijzen naar dezelfde onderliggende gegevens.  
> 
> 

In het volgende voor beeld ziet u een eenvoudig tabel ontwerp voor het opslaan van werk nemers en afdelings entiteiten. Veel van de voor beelden die verderop in deze hand leiding worden weer gegeven, zijn gebaseerd op dit eenvoudige ontwerp.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Tijdstempel</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Leeftijd</th>
<th>Email</th>
</tr>
<tr>
<td>Don</td>
<td>Huis</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Leeftijd</th>
<th>Email</th>
</tr>
<tr>
<td>Juni</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>Afdeling</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Verkoop</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Leeftijd</th>
<th>Email</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Tot nu toe ziet dit ontwerp er ongeveer uit als een tabel in een relationele data base met de belangrijkste verschillen tussen de verplichte kolommen en de mogelijkheid om meerdere entiteits typen op te slaan in dezelfde tabel. Daarnaast heeft elk van de door de gebruiker gedefinieerde eigenschappen, zoals **FirstName** of **Age** , een gegevens type, zoals geheel getal of teken reeks, net als een kolom in een relationele data base. Hoewel in tegens telling tot een relationele data base, betekent het schema-minder aard van het Table service dat een eigenschap niet hetzelfde gegevens type hoeft te hebben voor elke entiteit. Als u complexe gegevens typen in één eigenschap wilt opslaan, moet u een serialisatie-indeling gebruiken, zoals JSON of XML. Zie [inzicht in het tabel service gegevens model](https://msdn.microsoft.com/library/azure/dd179338.aspx)voor meer informatie over de tabel service, zoals ondersteunde gegevens typen, ondersteunde datumbereiken, naamgevings regels en grootte beperkingen.

Zoals u ziet, is uw keuze van **PartitionKey** en **RowKey** cruciaal voor een goede tabel ontwerp. Elke entiteit die is opgeslagen in een tabel moet een unieke combi natie van **PartitionKey** en **RowKey**hebben. Net als bij sleutels in een relationele-database tabel worden de waarden **PartitionKey** en **RowKey** geïndexeerd om een geclusterde index te maken die snelle zoek-ups mogelijk maakt. de Table service maakt echter geen secundaire indexen, zodat dit de enige twee geïndexeerde eigenschappen zijn (een aantal van de patronen die verderop worden beschreven, laten zien hoe u deze zicht bare beperking kunt omzeilen).  

Een tabel bestaat uit een of meer partities, en zoals u ziet, zijn veel van de ontwerp beslissingen die u aanbrengt, een geschikte **PartitionKey** en **RowKey** om uw oplossing te optimaliseren. Een oplossing kan bestaan uit slechts één tabel die al uw entiteiten bevat, ingedeeld in partities, maar een oplossing heeft meestal meerdere tabellen. Met tabellen kunt u uw entiteiten logisch organiseren, de toegang tot de gegevens beheren met behulp van toegangs beheer lijsten en een volledige tabel verwijderen met één opslag bewerking.  

### <a name="table-partitions"></a>Tabelpartities
De account naam, tabel naam en **PartitionKey** bepalen samen de partitie in de opslag service waar de tabel service de entiteit opslaat. En als onderdeel van het adresserings schema voor entiteiten definiëren partities een bereik voor [trans acties (zie hieronder](#entity-group-transactions) ) en vormen de basis van de manier waarop de tabel service wordt geschaald. Zie [Azure Storage schaalbaarheids-en prestatie doelen](../storage/common/storage-scalability-targets.md)voor meer informatie over partities.  

In de Table service, een afzonderlijk knoop punt Services, een of meer volledige partities en de service wordt geschaald door dynamische taak verdeling van partities tussen knoop punten. Als een knoop punt wordt geladen, kan de Table-service het bereik van partities dat door dat knoop punt wordt verwerkt, *splitsen* op verschillende knoop punten. Wanneer er verkeer wordt ondergebracht, kan de-service de partitielay-bereiken van de Stille knoop punten weer *samen voegen* op een enkel knoop punt.  

Zie het artikel [Microsoft Azure Storage: een Maxi maal beschik bare Cloud opslag service met sterke consistentie](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)voor meer informatie over de interne Details van de Table service, met name hoe de service partities beheert.  

### <a name="entity-group-transactions"></a>Trans acties van entiteits groep
In de Table service zijn EGTs (trans acties voor entiteits groepen) het enige ingebouwde mechanisme voor het uitvoeren van atomische updates op meerdere entiteiten. EGTs worden in sommige documentatie ook wel *batch-trans acties* genoemd. EGTs kan alleen worden uitgevoerd op entiteiten die zijn opgeslagen in dezelfde partitie (delen dezelfde partitie sleutel in een bepaalde tabel), dus wanneer u een Atomic-transactioneel gedrag voor meerdere entiteiten nodig hebt, moet u ervoor zorgen dat deze entiteiten zich in dezelfde partitie bevinden. Dit is vaak een reden om meerdere entiteits typen in dezelfde tabel (en partitie) te bewaren en niet meerdere tabellen te gebruiken voor verschillende entiteits typen. Eén EGT kan aan Maxi maal 100 entiteiten worden gebruikt.  Als u meerdere gelijktijdige EGTs voor verwerking indient, is het belang rijk om ervoor te zorgen dat deze EGTs niet worden uitgevoerd op entiteiten die gemeen schappelijk zijn voor EGTs, anders kan de verwerking worden vertraagd.

EGTs maakt ook een mogelijke trans actie mogelijk die u kunt evalueren in uw ontwerp: door meer partities te gebruiken, wordt de schaal baarheid van uw toepassing verhoogd, omdat Azure meer mogelijkheden heeft voor taak verdeling van aanvragen op verschillende knoop punten, maar dit kan de mogelijkheid van uw toepassing om atomische trans acties uit te voeren en sterke consistentie voor uw gegevens te behouden. Daarnaast zijn er specifieke schaalbaarheids doelen op het niveau van een partitie die de door Voer van trans acties die voor één knoop punt kunnen worden beperkt, kan beperken: Zie voor meer informatie over de schaalbaarheids doelen voor Azure Storage-accounts en de tabel service. [Azure Storage schaal baarheid en prestatie doelen](../storage/common/storage-scalability-targets.md). In latere secties van deze hand leiding worden verschillende ontwerp strategieën besproken die u helpen bij het beheren van de verhandelingen, zoals deze, en wordt uitgelegd hoe u de partitie sleutel kunt kiezen op basis van de specifieke vereisten van uw client toepassing.  

### <a name="capacity-considerations"></a>Capaciteits overwegingen
De volgende tabel bevat enkele van de sleutel waarden waarvan u rekening moet houden bij het ontwerpen van een Table service oplossing:  

| Totale capaciteit van een Azure-opslag account | 500 TB |
| --- | --- |
| Aantal tabellen in een Azure-opslag account |Alleen beperkt door de capaciteit van het opslag account |
| Aantal partities in een tabel |Alleen beperkt door de capaciteit van het opslag account |
| Aantal entiteiten in een partitie |Alleen beperkt door de capaciteit van het opslag account |
| Grootte van een afzonderlijke entiteit |Maxi maal 1 MB met Maxi maal 255 eigenschappen (inclusief de **PartitionKey**, **RowKey**en **tijds tempel**) |
| Grootte van de **PartitionKey** |Een teken reeks met een grootte van Maxi maal 1 KB |
| Grootte van de **RowKey** |Een teken reeks met een grootte van Maxi maal 1 KB |
| Grootte van de trans actie van een entiteits groep |Een trans actie kan Maxi maal 100 entiteiten bevatten en de payload moet minder dan 4 MB groot zijn. Een EGT kan een entiteit slechts één keer bijwerken. |

Zie [Het gegevensmodel van de tabelservice](https://msdn.microsoft.com/library/azure/dd179338.aspx) voor meer informatie.  

### <a name="cost-considerations"></a>Kostenoverwegingen
Table Storage is relatief goed koop, maar u moet kosten ramingen voor zowel het capaciteits gebruik als het aantal trans acties opnemen als onderdeel van de evaluatie van een oplossing die gebruikmaakt van de Table service. In veel gevallen waarin het uitvoeren van gedenormaliseerde of dubbele gegevens is vereist om de prestaties of schaal baarheid van uw oplossing te verbeteren, is een geldige aanpak. Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over prijzen.  

## <a name="guidelines-for-table-design"></a>Richtlijnen voor tabelontwerp
In deze lijsten vindt u een overzicht van enkele belang rijke richt lijnen die u moet onthouden wanneer u uw tabellen ontwerpt. in deze hand leiding worden deze allemaal uitgebreid beschreven in. Deze richt lijnen verschillen van de richt lijnen die u normaal gesp roken volgt voor het ontwerpen van relationele data bases.  

Het ontwerpen van uw Table service-oplossing om efficiënt te *lezen* :

* ***Ontwerpen voor het uitvoeren van query's in lees-en zware toepassingen.*** Wanneer u uw tabellen ontwerpt, moet u nadenken over de query's (met name de latentie gevoelige waarden) die u wilt uitvoeren voordat u denkt dat u uw entiteiten wilt bijwerken. Dit resulteert doorgaans in een efficiënte en beste oplossing.  
* ***Geef zowel PartitionKey als RowKey op in uw query's.*** *Punt query's* zoals dit zijn de meest efficiënte query's voor de tabel service.  
* ***Overweeg dubbele exemplaren van entiteiten op te slaan.*** Table Storage is goedkope, dus overweeg om dezelfde entiteit meerdere keren (met verschillende sleutels) op te slaan om efficiëntere query's mogelijk te maken.  
* ***Overweeg uw gegevens te denormaliseren.*** Table Storage is goedkope manier om uw gegevens te denormaliseren. Zo kunt u bijvoorbeeld overzichts entiteiten opslaan zodat query's voor statistische gegevens alleen toegang hebben tot één entiteit.  
* ***Samengestelde sleutel waarden gebruiken.*** De enige sleutels die u hebt, zijn **PartitionKey** en **RowKey**. Gebruik bijvoorbeeld samengestelde sleutel waarden om alternatieve toegangs paden naar entiteiten in te scha kelen.  
* ***Query projectie gebruiken.*** U kunt de hoeveelheid gegevens die u via het netwerk overdraagt verminderen met behulp van query's waarmee u alleen de gewenste velden selecteert.  

Ontwerp uw Table service oplossing om te *schrijven* naar een efficiënte manier:  

* ***Maak geen dynamische partities.*** Kies sleutels waarmee u op elk gewenst moment uw aanvragen kunt verspreiden over meerdere partities.  
* ***Vermijd pieken in verkeer.*** Het verkeer gedurende een redelijke periode vloeiend te maken en pieken in het verkeer te voor komen.
* ***Maak niet noodzakelijkerwijs een afzonderlijke tabel voor elk type entiteit.*** Wanneer u atomische trans acties tussen entiteits typen nodig hebt, kunt u deze typen meerdere entiteiten opslaan in dezelfde partitie in dezelfde tabel.
* ***Houd rekening met de maximale door Voer die u moet door nemen.*** U moet rekening houden met de schaalbaarheids doelen voor de Table service en ervoor te zorgen dat uw ontwerp deze niet overschrijdt.  

Als u deze hand leiding leest, ziet u voor beelden waarin al deze principes in de praktijk worden geplaatst.  

## <a name="design-for-querying"></a>Ontwerp voor query's
Table service oplossingen kunnen intensieve, schrijf intensief of een combi natie van beide worden gelezen. In deze sectie vindt u informatie over de zaken die u moet overwegen wanneer u uw Table service ontwerpt om Lees bewerkingen efficiënt te ondersteunen. Normaal gesp roken is een ontwerp dat lees bewerkingen efficiënt ondersteunt ook efficiënt voor schrijf bewerkingen. Er zijn echter extra aandachtspunten bij het ontwerpen ter ondersteuning van schrijf bewerkingen, zoals beschreven in de volgende sectie, [ontwerpen voor het wijzigen van gegevens](#design-for-data-modification).

Een goed uitgangs punt voor het ontwerpen van uw Table service-oplossing om gegevens efficiënt te kunnen lezen is het stellen van ' welke query's moet mijn toepassing worden uitgevoerd om de benodigde gegevens van de Table service op te halen? '  

> [!NOTE]
> Met de Table service is het belang rijk om het ontwerp correct te laten beginnen, omdat het moeilijk en kostbaar is om het later te wijzigen. In een relationele data base is het vaak mogelijk om prestatie problemen op te lossen door indexen toe te voegen aan een bestaande Data Base: dit is geen optie met de Table service.  
> 
> 

Deze sectie richt zich op de belangrijkste problemen die u moet aanpakken wanneer u uw tabellen ontwerpt voor het uitvoeren van query's. De onderwerpen in deze sectie zijn onder andere:

* [Hoe uw keuze van PartitionKey en RowKey gevolgen heeft voor de query prestaties](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Een geschikte PartitionKey kiezen](#choosing-an-appropriate-partitionkey)
* [Query's optimaliseren voor de Table service](#optimizing-queries-for-the-table-service)
* [Gegevens sorteren in de Table service](#sorting-data-in-the-table-service)

### <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Hoe uw keuze van PartitionKey en RowKey gevolgen heeft voor de query prestaties
In de volgende voor beelden wordt ervan uitgegaan dat de tabel service werk nemers-entiteiten opslaat met de volgende structuur (in de meeste voor beelden wordt de **Time Stamp** -eigenschap voor duidelijkheid wegge laten):  

| *Kolom naam* | *Gegevenstype* |
| --- | --- |
| **PartitionKey** (afdelings naam) |Tekenreeks |
| **RowKey** (werk nemer-id) |Tekenreeks |
| **Voor** |Tekenreeks |
| **LastName** |Tekenreeks |
| **Leeftijd** |Geheel getal |
| **EmailAddress** |Tekenreeks |

In de vorige sectie van Azure Table service Overview worden enkele van de belangrijkste functies van de Azure-Table service beschreven die een directe invloed hebben op het ontwerpen van query's. Dit resulteert in de volgende algemene richt lijnen voor het ontwerpen van Table service query's. De filter syntaxis die in de onderstaande voor beelden wordt gebruikt, is afkomstig uit de Table service REST API. Zie [query-entiteiten](https://msdn.microsoft.com/library/azure/dd179421.aspx)voor meer informatie.  

* Een ***Point-query*** is de meest efficiënte zoek actie die moet worden gebruikt en wordt aanbevolen voor gebruik voor Zoek opdrachten met hoge volumes of lookups waarvoor een laagste latentie is vereist. Een dergelijke query kan de indexen gebruiken om een afzonderlijke entiteit efficiënt te vinden door de waarden voor **PartitionKey** en **RowKey** op te geven. Bijvoorbeeld: $filter = (PartitionKey EQ ' Sales ') en (RowKey EQ ' 2 ')  
* Seconde best is een ***bereik query*** die gebruikmaakt van de **PartitionKey** en filters voor een bereik van **RowKey** -waarden om meer dan één entiteit te retour neren. De waarde **PartitionKey** identificeert een specifieke partitie en de **RowKey** -waarden identificeren een subset van de entiteiten in die partitie. Bijvoorbeeld: $filter = PartitionKey EQ ' Sales ' en RowKey ge ' en RowKey lt 'T '  
* De derde beste is een ***partitie scan*** die gebruikmaakt van de **PartitionKey** en filters op een andere niet-sleutel eigenschap en die mogelijk meer dan één entiteit retourneert. De waarde **PartitionKey** identificeert een specifieke partitie en de eigenschapwaarden worden geselecteerd voor een subset van de entiteiten in die partitie. Bijvoorbeeld: $filter = PartitionKey EQ ' Sales ' en LastName EQ ' Smit '  
* Een ***tabel scan*** bevat niet de **PartitionKey** en is inefficiënt omdat alle partities die de tabel vormen, op zijn beurt worden doorzocht op alle overeenkomende entiteiten. Er wordt een tabel scan uitgevoerd, ongeacht of het filter gebruikmaakt van de **RowKey**. Bijvoorbeeld: $filter = LastName EQ ' Jansen '  
* Azure Table Storage Query's waarmee meerdere entiteiten worden geretourneerd, retour neren ze in **PartitionKey** -en **RowKey** -volg orde. Kies een **RowKey** die de meest voorkomende sorteer volgorde definieert om te voor komen dat de entiteiten in de client worden gebruikt. Query resultaten die door de Azure-Table-API in Azure Cosmos DB worden geretourneerd, worden niet gesorteerd op partitie sleutel of rij-sleutel. Zie [verschillen tussen Table-API in azure Cosmos DB en Azure-tabel opslag](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)voor een gedetailleerde lijst met functie verschillen.

Het gebruik van een '**or**' om een filter op te geven op basis van **RowKey** -waarden resulteert in een partitie scan en wordt niet behandeld als een bereik query. U moet daarom query's voor komen die gebruikmaken van filters zoals: $filter = PartitionKey EQ ' Sales ' en (RowKey EQ ' 121 ' of RowKey EQ ' 322 ')  

Zie voor voor beelden van code aan client zijde die de Storage-client bibliotheek gebruikt voor het uitvoeren van efficiënte query's:  

* [Een Point-query uitvoeren met de Storage-client bibliotheek](#executing-a-point-query-using-the-storage-client-library)
* [Ophalen van meerdere entiteiten met behulp van LINQ](#retrieving-multiple-entities-using-linq)
* [Projectie aan server zijde](#server-side-projection)  

Zie voor voor beelden van code aan client zijde die meerdere entiteits typen kan verwerken die zijn opgeslagen in dezelfde tabel:  

* [Werken met heterogene entiteits typen](#working-with-heterogeneous-entity-types)  

### <a name="choosing-an-appropriate-partitionkey"></a>Een geschikte PartitionKey kiezen
Uw keuze van **PartitionKey** moet de nood zaak hebben om het gebruik van EGTs (om consistentie te garanderen) in te stellen op basis van de vereiste om uw entiteiten over meerdere partities te verdelen (om ervoor te zorgen dat er een schaal bare oplossing wordt).  

U kunt op een extreem punt al uw entiteiten opslaan in één partitie, maar dit kan de schaal baarheid van uw oplossing beperken en voor komen dat de Table-service taak verdeling kan aanvragen. In het andere geval kunt u één entiteit per partitie opslaan, die zeer schaalbaar is en waardoor de Table-service taak verdeling kan aanvragen, maar dat voor komt dat u trans acties van entiteits groepen gebruikt.  

Een ideale **PartitionKey** maakt het mogelijk om efficiënte query's te gebruiken en met voldoende partities om ervoor te zorgen dat uw oplossing schaalbaar is. Normaal gesp roken zult u zien dat uw entiteiten een geschikte eigenschap hebben die uw entiteiten distribueert over voldoende partities.

> [!NOTE]
> Een voor beeld: in een systeem dat informatie over gebruikers of werk nemers opslaat, kan UserID een goede PartitionKey zijn. Mogelijk hebt u verschillende entiteiten die een bepaalde gebruikers-id gebruiken als de partitie sleutel. Elke entiteit die gegevens over een gebruiker opslaat, wordt gegroepeerd in één partitie, zodat deze entiteiten toegankelijk zijn via entiteits transacties, terwijl ze toch uiterst schaalbaar zijn.
> 
> 

Er zijn aanvullende overwegingen in uw keuze van **PartitionKey** die te maken hebben met het invoegen, bijwerken en verwijderen van entiteiten: Zie het sectie [ontwerp voor het wijzigen van de gegevens](#design-for-data-modification) hieronder.  

### <a name="optimizing-queries-for-the-table-service"></a>Query's optimaliseren voor de Table service
De Table service indexeert automatisch uw entiteiten met behulp van de waarden **PartitionKey** en **RowKey** in één geclusterde index. de reden hiervoor is dat het punt query's het meest efficiënt zijn om te gebruiken. Er zijn echter geen andere indexen dan die op de geclusterde index op de **PartitionKey** en **RowKey**.

Veel modellen moeten voldoen aan de vereisten om het opzoeken van entiteiten op basis van meerdere criteria mogelijk te maken. U kunt bijvoorbeeld werknemers entiteiten zoeken op basis van e-mail, werk nemer-id of achternaam. De volgende patronen in de [ontwerp patronen](#table-design-patterns) van de sectie tabel geven een oplossing voor deze typen vereisten en beschrijven manieren om het feit te omzeilen dat de Table service geen secundaire indexen levert:  

* [Intra-Partition secundair index patroon](#intra-partition-secondary-index-pattern) : Sla meerdere kopieën van elke entiteit op met behulp van verschillende **RowKey** -waarden (in dezelfde partitie) om snelle en efficiënte zoek acties en alternatieve Sorteer volgorden mogelijk te maken met behulp van verschillende **RowKey** -waarden.  
* [Secundair index patroon tussen partities](#inter-partition-secondary-index-pattern) : Sla meerdere kopieën van elke entiteit op met behulp van verschillende **RowKey** -waarden in afzonderlijke partities of in afzonderlijke tabellen om snelle en efficiënte zoek acties en alternatieve Sorteer volgorden mogelijk te maken met behulp van verschillende  **RowKey** waarden.  
* [Patroon van index entiteiten](#index-entities-pattern) : behoud index entiteiten om efficiënte Zoek opdrachten in te scha kelen waarmee lijsten met entiteiten worden geretourneerd.  

### <a name="sorting-data-in-the-table-service"></a>Gegevens sorteren in de Table service

Query resultaten die door de Table service worden geretourneerd, worden in oplopende volg orde gesorteerd op basis van **PartitionKey** en vervolgens op **RowKey**.

> [!NOTE]
> Query resultaten die door de Azure-Table-API in Azure Cosmos DB worden geretourneerd, worden niet gesorteerd op partitie sleutel of rij-sleutel. Zie [verschillen tussen Table-API in azure Cosmos DB en Azure-tabel opslag](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)voor een gedetailleerde lijst met functie verschillen.

Sleutels in Azure Storage tabel zijn teken reeks waarden en om ervoor te zorgen dat numerieke waarden correct worden gesorteerd, moet u ze converteren naar een vaste lengte en deze met nullen aanvullen. Als de waarde van de werk nemer-id die u als **RowKey** gebruikt, bijvoorbeeld een geheel getal is, moet u werk nemer-id **123** converteren naar **00000123**. 

Veel toepassingen hebben vereisten voor het gebruik van gegevens die in verschillende orders zijn gesorteerd: bijvoorbeeld het sorteren van werk nemers op naam of het samen voegen van de datum. De volgende patronen in de sectie [tabel ontwerp patronen](#table-design-patterns) zijn een alternatief voor het sorteren van de sorteer volgorde voor uw entiteiten:  

* [Intra-Partition secundair index patroon](#intra-partition-secondary-index-pattern) : Sla meerdere kopieën van elke entiteit op met behulp van verschillende RowKey-waarden (in dezelfde partitie) om snelle en efficiënte zoek acties en alternatieve Sorteer volgorden mogelijk te maken met behulp van verschillende RowKey-waarden.  
* [Secundair index patroon tussen partities](#inter-partition-secondary-index-pattern) : Sla meerdere kopieën van elke entiteit op met behulp van verschillende RowKey-waarden in afzonderlijke partities in afzonderlijke tabellen om snelle en efficiënte zoek acties en alternatieve Sorteer volgorden mogelijk te maken met behulp van verschillende RowKey-waarden.
* [Eind patroon van logboek](#log-tail-pattern) : Haal de *n* -entiteiten op die het laatst aan een partitie zijn toegevoegd met behulp van een **RowKey** -waarde die in omgekeerde datum-en tijd volgorde wordt gesorteerd.  

## <a name="design-for-data-modification"></a>Ontwerp voor gegevenswijziging
Deze sectie richt zich op de ontwerp overwegingen voor het optimaliseren van invoegingen, updates en verwijderingen. In sommige gevallen moet u de afweging evalueren tussen de ontwerpen die worden geoptimaliseerd voor het uitvoeren van query's op ontwerpen die worden geoptimaliseerd voor het wijzigen van gegevens, net zoals bij ontwerpen voor relationele data bases (hoewel de technieken voor het beheer van de ontwerp traden echter anders in een relationele data base). In de sectie [tabel ontwerp patronen](#table-design-patterns) worden een aantal gedetailleerde ontwerp patronen voor de Table service beschreven en worden enkele van deze trans acties gemarkeerd. In de praktijk zult u merken dat veel modellen die zijn geoptimaliseerd voor het uitvoeren van query's, ook geschikt zijn voor het wijzigen van entiteiten.  

### <a name="optimizing-the-performance-of-insert-update-and-delete-operations"></a>De prestaties van INSERT-, update-en delete-bewerkingen optimaliseren
Als u een entiteit wilt bijwerken of verwijderen, moet u deze kunnen identificeren met behulp van de waarden **PartitionKey** en **RowKey** . In dit opzicht moeten uw keuze van **PartitionKey** en **RowKey** voor het wijzigen van entiteiten vergelijk bare criteria volgen op uw keuze om punt query's te ondersteunen, omdat u zo efficiënt mogelijk entiteiten wilt identificeren. U wilt geen inefficiënte partitie of tabel Scan gebruiken om een entiteit te vinden om de **PartitionKey** -en **RowKey** -waarden te ontdekken die u moet bijwerken of verwijderen.  

De volgende patronen in het [ontwerp patroon](#table-design-patterns) van de sectie tabel maken het optimaliseren van de prestaties of uw insert-, update-en delete-bewerkingen:  

* [Patroon voor grote volumes verwijderen](#high-volume-delete-pattern) : Hiermee wordt het verwijderen van een groot aantal entiteiten ingeschakeld door alle entiteiten voor gelijktijdige verwijdering in hun eigen afzonderlijke tabel op te slaan. u verwijdert de entiteiten door de tabel te verwijderen.  
* [Patroon van de gegevens reeks](#data-series-pattern) : Sla volledige gegevens reeksen op in één entiteit om het aantal aanvragen dat u maakt, te minimaliseren.  
* [Patroon voor grote entiteiten](#wide-entities-pattern) : gebruik meerdere fysieke entiteiten om logische entiteiten met meer dan 252 eigenschappen op te slaan.  
* [Patroon voor grote entiteiten](#large-entities-pattern) : gebruik Blob Storage om grote eigenschaps waarden op te slaan.  

### <a name="ensuring-consistency-in-your-stored-entities"></a>Consistentie in uw opgeslagen entiteiten garanderen
De andere sleutel factor die van invloed is op uw keuze van sleutels voor het optimaliseren van gegevens wijzigingen is het garanderen van consistentie met behulp van atomische trans acties. U kunt alleen een EGT gebruiken om te werken aan entiteiten die zijn opgeslagen in dezelfde partitie.  

De volgende patronen in de sectie [tabel ontwerp patronen](#table-design-patterns) adres consistentie beheer:  

* [Intra-Partition secundair index patroon](#intra-partition-secondary-index-pattern) : Sla meerdere kopieën van elke entiteit op met behulp van verschillende **RowKey** -waarden (in dezelfde partitie) om snelle en efficiënte zoek acties en alternatieve Sorteer volgorden mogelijk te maken met behulp van verschillende **RowKey** -waarden.  
* [Secundair index patroon tussen partities](#inter-partition-secondary-index-pattern) : Sla meerdere kopieën van elke entiteit op met behulp van verschillende RowKey-waarden in afzonderlijke partities of in afzonderlijke tabellen om snelle en efficiënte zoek acties en alternatieve Sorteer volgorden mogelijk te maken met behulp van verschillende **RowKey** gegevens.  
* [Patroon uiteindelijk consistent trans acties](#eventually-consistent-transactions-pattern) : Schakel uiteindelijk consistent gedrag in voor de partitie grenzen of de grenzen van het opslag systeem met behulp van Azure-wacht rijen.
* [Patroon van index entiteiten](#index-entities-pattern) : behoud index entiteiten om efficiënte Zoek opdrachten in te scha kelen waarmee lijsten met entiteiten worden geretourneerd.  
* [Denormalisatie patroon](#denormalization-pattern) : gerelateerde gegevens samen in één entiteit combi neren zodat u alle gegevens kunt ophalen die u nodig hebt met één punt query.  
* [Patroon van de gegevens reeks](#data-series-pattern) : Sla volledige gegevens reeksen op in één entiteit om het aantal aanvragen dat u maakt, te minimaliseren.  

Zie de sectie [trans acties voor entiteits](#entity-group-transactions)groepen voor meer informatie over entiteits transacties.  

### <a name="ensuring-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Het waarborgen van het ontwerp voor efficiënte wijzigingen vereenvoudigt efficiënte query's
In veel gevallen kan een ontwerp voor efficiënte query resultaten leiden tot efficiënte wijzigingen, maar moet u altijd evalueren of dit het geval is voor uw specifieke scenario. Sommige van de patronen in de sectie [tabel ontwerp patronen](#table-design-patterns) evalueren expliciet de traden tussen query's en het wijzigen van entiteiten, en u moet altijd rekening houden met het aantal van elk type bewerking.  

De volgende patronen in de [ontwerp patronen](#table-design-patterns) van de sectie tabel adresseren de trans acties tussen het ontwerpen voor efficiënte query's en het ontwerpen voor efficiënte gegevens aanpassing:  

* [Samengesteld sleutel patroon](#compound-key-pattern) : gebruik samengestelde **RowKey** -waarden om een client in staat te stellen gerelateerde gegevens op te zoeken met een single point-query.  
* [Eind patroon van logboek](#log-tail-pattern) : Haal de *n* -entiteiten op die het laatst aan een partitie zijn toegevoegd met behulp van een **RowKey** -waarde die in omgekeerde datum-en tijd volgorde wordt gesorteerd.  

## <a name="encrypting-table-data"></a>Tabel gegevens versleutelen
De client bibliotheek van .NET Azure Storage ondersteunt versleuteling van eigenschappen van teken reeks entiteiten voor INSERT-en Replace-bewerkingen. De versleutelde teken reeksen worden als binaire eigenschappen opgeslagen op de service en worden teruggeconverteerd naar teken reeksen na ontsleuteling.    

Voor tabellen, naast het versleutelings beleid, moeten gebruikers de eigenschappen opgeven die moeten worden versleuteld. U kunt dit doen door ofwel een [EncryptProperty]-kenmerk op te geven (voor POCO-entiteiten die zijn afgeleid van TableEntity) of een Encryption resolver in de aanvraag opties. Een Encryption resolver is een gemachtigde die een partitie sleutel, een rij-en een eigenschaps naam gebruikt en een Booleaanse waarde retourneert die aangeeft of die eigenschap moet worden versleuteld. Tijdens de versleuteling gebruikt de client bibliotheek deze gegevens om te bepalen of een eigenschap moet worden versleuteld tijdens het schrijven naar de kabel. De gemachtigde biedt ook de mogelijkheid van logica om te bepalen hoe eigenschappen worden versleuteld. (Bijvoorbeeld als X, vervolgens versleutelings eigenschap A; anders eigenschappen A en B versleutelen.) Het is niet nodig om deze informatie op te geven tijdens het lezen of doorzoeken van entiteiten.

Samen voegen wordt momenteel niet ondersteund. Omdat een subset van eigenschappen mogelijk eerder is versleuteld met een andere sleutel, worden de nieuwe eigenschappen gebundeld en worden de meta gegevens bijgewerkt als gevolg van gegevens verlies. Samen voegen vereist het maken van extra service aanroepen om de vooraf bestaande entiteit van de service te lezen of een nieuwe sleutel per eigenschap te gebruiken, die beide niet geschikt zijn om prestatie redenen.     

Zie [versleuteling aan client zijde en Azure Key Vault voor Microsoft Azure Storage](../storage/common/storage-client-side-encryption.md)voor meer informatie over het versleutelen van tabel gegevens.  

## <a name="modeling-relationships"></a>Relaties modelleren
Het bouwen van domein modellen is een belang rijke stap in het ontwerp van complexe systemen. Normaal gesp roken gebruikt u het model proces om entiteiten en de relaties daartussen te identificeren als een manier om inzicht te krijgen in het bedrijfs domein en het ontwerp van uw systeem te informeren. In deze sectie wordt uitgelegd hoe u een aantal algemene relatie typen in domein modellen kunt vertalen naar ontwerpen voor de Table service. Het toewijzings proces van een logisch gegevens model naar een fysiek NoSQL gebaseerd gegevens model wijkt af van het beleid dat wordt gebruikt bij het ontwerpen van een relationele data base. In het ontwerp van relationele data bases wordt doorgaans uitgegaan van een gegevensnormalisatieproces dat is geoptimaliseerd voor het minimaliseren van redundantie, en een declaratieve query functie waarmee wordt uitgelegd hoe de implementatie van de data base werkt.  

### <a name="one-to-many-relationships"></a>Een-op-veel-relaties
Een-op-veel-relaties tussen bedrijfs domein objecten komen vaak voor: bijvoorbeeld één afdeling heeft veel werk nemers. Er zijn verschillende manieren voor het implementeren van een-op-veel-relaties in de Table service elk met voor-en nadelen die relevant kunnen zijn voor het specifieke scenario.  

Bekijk het voor beeld van een grote Multi-National Corporation met tien duizenden afdelingen en werk nemers, waarbij elke afdeling veel werk nemers en elke werk nemer heeft die aan één specifieke afdeling zijn gekoppeld. Een manier is om afzonderlijke afdelings-en werknemers entiteiten zoals deze te bewaren:  

![Afdeling en entiteit van werk nemer][1]

In dit voor beeld ziet u een impliciete een-op-veel-relatie tussen de typen op basis van de waarde **PartitionKey** . Elke afdeling kan veel werk nemers hebben.  

In dit voor beeld ziet u ook een afdelings entiteit en de gerelateerde werknemers entiteiten in dezelfde partitie. U kunt ervoor kiezen om verschillende partities, tabellen of zelfs opslag accounts te gebruiken voor de verschillende entiteits typen.  

Een alternatieve methode is om uw gegevens te denormaliseren en alleen werk nemers te slaan met Gedenormaliseerde gegevens van de afdeling, zoals wordt weer gegeven in het volgende voor beeld. In dit specifieke scenario is deze gedenormaliseerde aanpak mogelijk niet het beste als u een vereiste hebt om de details van een Afdelings Manager te wijzigen. Als u dit wilt doen, moet u elke werk nemer in de afdeling bijwerken.  

![Entiteit werk nemer][2]

Zie voor meer informatie het [denormalisatie patroon](#denormalization-pattern) verderop in deze hand leiding.  

De volgende tabel bevat een overzicht van de voor-en nadelen van elk van de benaderingen die hierboven worden beschreven voor het opslaan van werk nemers en afdelings entiteiten die een een-op-veel-relatie hebben. U moet ook rekening houden met het aantal keren dat u verwacht verschillende bewerkingen uit te voeren: het kan acceptabel zijn om een ontwerp te hebben dat een dure bewerking bevat als deze bewerking alleen zelden plaatsvindt.  

<table>
<tr>
<th>Hanter</th>
<th>-Professionals</th>
<th>Nadelen</th>
</tr>
<tr>
<td>Afzonderlijke entiteits typen, dezelfde partitie, dezelfde tabel</td>
<td>
<ul>
<li>U kunt een afdelings entiteit bijwerken met één bewerking.</li>
<li>U kunt een EGT gebruiken om de consistentie te behouden als u een vereiste hebt om een afdelings entiteit te wijzigen wanneer u een werknemers entiteit bijwerkt of invoegt of verwijdert. Als u bijvoorbeeld het aantal werk nemers per afdeling beheert.</li>
</ul>
</td>
<td>
<ul>
<li>Mogelijk moet u zowel een werk nemer als een afdelings entiteit voor sommige client activiteiten ophalen.</li>
<li>Opslag bewerkingen vinden plaats in dezelfde partitie. Op volumes met een hoge trans actie kan dit leiden tot een hotspot.</li>
<li>U kunt een werk nemer niet met een EGT verplaatsen naar een nieuwe afdeling.</li>
</ul>
</td>
</tr>
<tr>
<td>Afzonderlijke entiteits typen, verschillende partities of tabellen of opslag accounts</td>
<td>
<ul>
<li>U kunt een afdelings-of werknemers entiteit bijwerken met één bewerking.</li>
<li>Op volumes met een hoge trans actie kan dit de belasting over meer partities verdelen.</li>
</ul>
</td>
<td>
<ul>
<li>Mogelijk moet u zowel een werk nemer als een afdelings entiteit voor sommige client activiteiten ophalen.</li>
<li>U kunt EGTs niet gebruiken om consistentie te behouden wanneer u een werk nemer bijwerkt of invoegt/verwijdert en een afdeling bijwerkt. U kunt bijvoorbeeld het aantal werk nemers in een afdelings entiteit bijwerken.</li>
<li>U kunt een werk nemer niet met een EGT verplaatsen naar een nieuwe afdeling.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormaliseren in één entiteits type</td>
<td>
<ul>
<li>U kunt alle informatie die u nodig hebt, ophalen met één aanvraag.</li>
</ul>
</td>
<td>
<ul>
<li>Het kan duur zijn om de consistentie te hand haven als u de gegevens van de afdeling moet bijwerken (hiervoor moet u alle werk nemers in een afdeling bijwerken).</li>
</ul>
</td>
</tr>
</table>

Hoe u kiest tussen deze opties en welke van de voor-en nadelen het belangrijkst zijn, is afhankelijk van uw specifieke toepassings scenario's. Hoe vaak u bijvoorbeeld afdelings entiteiten wijzigt. moeten al uw werknemers query's de aanvullende informatie over de afdeling hebben. hoe dicht zijn de schaalbaarheids limieten voor uw partities of uw opslag account?  

### <a name="one-to-one-relationships"></a>Een-op-een-relaties
Domein modellen kunnen een-op-een-relaties tussen entiteiten bevatten. Als u een een-op-een-relatie wilt implementeren in de Table service, moet u ook kiezen hoe u de twee gerelateerde entiteiten wilt koppelen wanneer u deze beide moet ophalen. Deze koppeling kan impliciet zijn, op basis van een Conventie in de sleutel waarden of expliciet door een koppeling op te slaan in de vorm van **PartitionKey** -en **RowKey** -waarden in elke entiteit naar de gerelateerde entiteit. Zie de sectie [een-op-veel-relaties](#one-to-many-relationships)voor een bespreking van de vraag of u de gerelateerde entiteiten in dezelfde partitie moet opslaan.  

Er zijn ook implementatie overwegingen die kunnen leiden tot het implementeren van een-op-een-relaties in de Table service:  

* Verwerken van grote entiteiten (Zie het [patroon grote entiteiten](#large-entities-pattern)voor meer informatie).  
* Toegangs beheer implementeren (Zie [toegang beheren met hand tekeningen voor gedeelde toegang](#controlling-access-with-shared-access-signatures)) voor meer informatie.  

### <a name="join-in-the-client"></a>Toevoegen aan de client
Hoewel er manieren zijn om relaties te model leren in de Table service, moet u niet verg eten dat de twee voornaamste redenen voor het gebruik van de Table service schaal baarheid en prestaties zijn. Als u een model maakt van veel relaties die de prestaties en schaal baarheid van uw oplossing in de hand hebben, kunt u het beste zelf bepalen of u alle gegevens relaties in uw tabel ontwerp wilt maken. U kunt het ontwerp mogelijk vereenvoudigen en de schaal baarheid en prestaties van uw oplossing verbeteren als u uw client toepassing de benodigde deelname wilt laten uitvoeren.  

Als u bijvoorbeeld kleine tabellen hebt die gegevens bevatten die niet vaak veranderen, kunt u deze gegevens eenmaal ophalen en opslaan in de cache op de client. Dit kan voor komen dat herhaalde retours dezelfde gegevens ophalen. In de voor beelden die we in deze hand leiding hebben bekeken, is de set van afdelingen in een kleine organisatie waarschijnlijk klein en is het niet vaak een goede kandidaat te maken voor gegevens die client toepassing kan downloaden als opzoek gegevens.  

### <a name="inheritance-relationships"></a>Overname relaties
Als uw client toepassing een set klassen gebruikt die deel uitmaken van een overname relatie om bedrijfs entiteiten weer te geven, kunt u deze entiteiten gemakkelijk persistent maken in de Table service. U kunt bijvoorbeeld de volgende set klassen in uw client toepassing hebben gedefinieerd, waarbij **persoon** een abstracte klasse is.

![Er diagram van overname relaties][3]

U kunt exemplaren van de twee concrete klassen in het Table service persistent maken met behulp van een tabel met één persoon, met behulp van entiteiten in die er als volgt uitzien:  

![Diagram van de entiteit van de klant en de werk nemer][4]

Zie de sectie [werken met heterogene entiteits typen](#working-with-heterogeneous-entity-types) verderop in deze hand leiding voor meer informatie over het werken met meerdere entiteits typen in dezelfde tabel in client code. Dit biedt voor beelden van het herkennen van het entiteits type in client code.  

## <a name="table-design-patterns"></a>Tabel ontwerp patronen
In de vorige secties hebt u enkele gedetailleerde discussies gezien over het optimaliseren van het tabel ontwerp voor het ophalen van entiteits gegevens met behulp van query's en voor het invoegen, bijwerken en verwijderen van entiteits gegevens. In deze sectie worden enkele patronen beschreven die geschikt zijn voor gebruik met Table service oplossingen. Daarnaast kunt u zien hoe u een aantal van de problemen en trans acties die eerder in deze hand leiding is opgetreden, nagenoeg zou kunnen aanpakken. In het volgende diagram ziet u een overzicht van de relaties tussen de verschillende patronen:  

![Afbeelding van tabel ontwerp patronen][5]

In het patroon schema hierboven ziet u een aantal relaties tussen patronen (blauw) en anti patronen (oranje) die in deze hand leiding worden beschreven. Er zijn veel andere patronen die in overweging worden nemen. Een van de belangrijkste scenario's voor Table service is bijvoorbeeld het gebruik van het [patroon gerealiseerde weer gave](https://msdn.microsoft.com/library/azure/dn589782.aspx) van het [CQRS-patroon (Command query](https://msdn.microsoft.com/library/azure/jj554200.aspx) promaterialing).  

### <a name="intra-partition-secondary-index-pattern"></a>Secundair index patroon voor de intra partitie
Sla meerdere exemplaren van elke entiteit op met behulp van verschillende **RowKey** -waarden (in dezelfde partitie) om snelle en efficiënte zoek acties en alternatieve Sorteer volgorden mogelijk te maken met behulp van verschillende **RowKey** -waarden. Updates tussen kopieën kunnen consistent worden gehouden met behulp van EGTs.  

#### <a name="context-and-problem"></a>Context en probleem
De Table service indexeert automatisch entiteiten met behulp van de waarden **PartitionKey** en **RowKey** . Hiermee kan een client toepassing een entiteit efficiënt ophalen met behulp van deze waarden. Met de tabel structuur die hieronder wordt weer gegeven, kan een client toepassing bijvoorbeeld een punt query gebruiken om een afzonderlijke werknemers entiteit op te halen met behulp van de afdelings naam en de werk nemer-id (de waarden **PartitionKey** en **RowKey** ). Een client kan ook entiteiten ophalen die zijn gesorteerd op werk nemer-id binnen elke afdeling.

![Entiteit werk nemer][6]

Als u ook een werknemers entiteit wilt kunnen vinden op basis van de waarde van een andere eigenschap, zoals e-mail adres, moet u een minder efficiënte partitie Scan gebruiken om een overeenkomst te vinden. Dit komt doordat de Table service geen secundaire indexen biedt. Daarnaast is er geen optie voor het aanvragen van een lijst met werk nemers die in een andere volg orde zijn gesorteerd dan **RowKey** order.  

#### <a name="solution"></a>Oplossing
Om het ontbreken van secundaire indexen te omzeilen, kunt u meerdere exemplaren van elke entiteit met elk exemplaar opslaan met behulp van een andere **RowKey** -waarde. Als u een entiteit met de hieronder weer gegeven structuren opslaat, kunt u op efficiënte wijze werknemers entiteiten ophalen op basis van het e-mail adres of de werk nemer-id. Met de voorvoegsel waarden voor de **RowKey**, empid_ en email_ kunt u een query uitvoeren voor één werk nemer of een bereik van werk nemers met behulp van een reeks e-mail adressen of werk nemer-id's.  

![Entiteit van werk nemer met verschillende RowKey-waarden][7]

De volgende twee filter criteria (één op te geven op basis van de werk nemer-id en één op te zoeken op basis van een e-mail adres) beide opgeven punt query's:  

* $filter = (PartitionKey EQ ' Sales ') en (RowKey EQ ' empid_000223 ')  
* $filter = (PartitionKey EQ ' Sales ') en (RowKey EQ 'email_jonesj@contoso.com')  

Als u een query uitvoert voor een bereik van werk nemers, kunt u een bereik opgeven dat is gesorteerd in de volg orde van de werk nemer-id of een bereik dat in de e-mail adres volgorde is gesorteerd door een query uit te sturen naar entiteiten met het juiste voor voegsel in de **RowKey**.  

* Als u wilt zoeken naar alle werk nemers van de afdeling verkoop met een werk nemer-id in het bereik 000100 tot 000199 gebruikt u: $filter = (PartitionKey EQ ' Sales ') en (RowKey ge ' empid_000100 ') en (RowKey Le ' empid_000199 ')  
* Als u wilt zoeken naar alle werk nemers van de afdeling verkoop met een e-mail adres dat begint met de letter ' a ' gebruiken: $filter = (PartitionKey EQ ' Sales ') en (RowKey ge ' email_a ') en (RowKey lt ' email_b ')  
  
  De filter syntaxis die in de bovenstaande voor beelden wordt gebruikt, is afkomstig uit de Table service REST API, Zie [query-entiteiten](https://msdn.microsoft.com/library/azure/dd179421.aspx)voor meer informatie.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Table Storage is relatief goed koop om te gebruiken, dus de kosten overhead van het opslaan van dubbele gegevens mag geen grote bezorgdheid zijn. U moet de kosten van uw ontwerp echter altijd evalueren op basis van uw verwachte opslag vereisten en alleen dubbele entiteiten toevoegen ter ondersteuning van de query's die door uw client toepassing worden uitgevoerd.  
* Omdat de secundaire index entiteiten worden opgeslagen in dezelfde partitie als de oorspronkelijke entiteiten, moet u ervoor zorgen dat u de schaalbaarheids doelen voor een afzonderlijke partitie niet overschrijdt.  
* U kunt ervoor zorgen dat uw dubbele entiteiten consistent blijven door gebruik te maken van EGTs om de twee exemplaren van de entiteit atomisch bij te werken. Dit betekent dat u alle kopieën van een entiteit in dezelfde partitie moet opslaan. Zie de sectie [trans acties voor entiteits groepen gebruiken](#entity-group-transactions)voor meer informatie.  
* De waarde die wordt gebruikt voor de **RowKey** moet uniek zijn voor elke entiteit. Overweeg het gebruik van samengestelde sleutel waarden.  
* Met de opvullings numerieke waarden in de **RowKey** (bijvoorbeeld de werk nemer-id 000223) kunt u de sorteer-en filter functie op basis van de boven-en ondergrenzen sorteren.  
* U hoeft niet noodzakelijkerwijs alle eigenschappen van uw entiteit te dupliceren. Als de query's voor het opzoeken van de entiteiten die gebruikmaken van het e-mail adres in de **RowKey** nooit de leeftijd van de werk nemer nodig hebben, kunnen deze entiteiten de volgende structuur hebben:

![Entiteit werk nemer][8]

* Het is doorgaans beter om dubbele gegevens op te slaan en ervoor te zorgen dat u alle gegevens kunt ophalen die u nodig hebt met één query, dan om één query te gebruiken om een entiteit te zoeken en een andere om de vereiste gegevens op te zoeken.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer uw client toepassing entiteiten moet ophalen met behulp van verschillende sleutels, wanneer uw client entiteiten in verschillende sorteer volgorden moet ophalen en waar u elke entiteit kunt identificeren met behulp van verschillende unieke waarden. U moet er echter zeker van zijn dat u de schaal baarheids limieten van de partitie niet overschrijdt wanneer u entiteiten lookups uitvoert met behulp van de verschillende **RowKey** -waarden.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Secundair index patroon tussen partities](#inter-partition-secondary-index-pattern)
* [Samengesteld sleutel patroon](#compound-key-pattern)
* [Trans acties van entiteits groep](#entity-group-transactions)
* [Werken met heterogene entiteits typen](#working-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Secundair index patroon tussen partities
Sla meerdere exemplaren van elke entiteit op met behulp van verschillende **RowKey** -waarden in afzonderlijke partities of in afzonderlijke tabellen om snelle en efficiënte zoek acties en alternatieve Sorteer volgorden mogelijk te maken met behulp van verschillende **RowKey** -waarden.  

#### <a name="context-and-problem"></a>Context en probleem
De Table service indexeert automatisch entiteiten met behulp van de waarden **PartitionKey** en **RowKey** . Hiermee kan een client toepassing een entiteit efficiënt ophalen met behulp van deze waarden. Met de tabel structuur die hieronder wordt weer gegeven, kan een client toepassing bijvoorbeeld een punt query gebruiken om een afzonderlijke werknemers entiteit op te halen met behulp van de afdelings naam en de werk nemer-id (de waarden **PartitionKey** en **RowKey** ). Een client kan ook entiteiten ophalen die zijn gesorteerd op werk nemer-id binnen elke afdeling.  

![Entiteit werk nemer][9]

Als u ook een werknemers entiteit wilt kunnen vinden op basis van de waarde van een andere eigenschap, zoals e-mail adres, moet u een minder efficiënte partitie Scan gebruiken om een overeenkomst te vinden. Dit komt doordat de Table service geen secundaire indexen biedt. Daarnaast is er geen optie voor het aanvragen van een lijst met werk nemers die in een andere volg orde zijn gesorteerd dan **RowKey** order.  

U verwacht een groot aantal trans acties voor deze entiteiten en wilt het risico van de Table serviceings frequentie die uw client beperkt beperken.  

#### <a name="solution"></a>Oplossing
Om het ontbreken van secundaire indexen te omzeilen, kunt u meerdere exemplaren van elke entiteit met elk exemplaar opslaan met behulp van verschillende **PartitionKey** -en **RowKey** -waarden. Als u een entiteit met de hieronder weer gegeven structuren opslaat, kunt u op efficiënte wijze werknemers entiteiten ophalen op basis van het e-mail adres of de werk nemer-id. Met de voorvoegsel waarden voor de **PartitionKey**, empid_ en email_ kunt u bepalen welke index u wilt gebruiken voor een query.  

![Werknemers entiteit met primaire index en werknemers entiteit met secundaire index][10]

De volgende twee filter criteria (één op te geven op basis van de werk nemer-id en één op te zoeken op basis van een e-mail adres) beide opgeven punt query's:  

* $filter = (PartitionKey EQ ' empid_Sales ') en (RowKey EQ ' 000223 ')
* $filter = (PartitionKey EQ ' email_Sales ') en (RowKey EQ 'jonesj@contoso.com')  

Als u een query uitvoert voor een bereik van werk nemers, kunt u een bereik opgeven dat is gesorteerd in de volg orde van de werk nemer-id of een bereik dat in de e-mail adres volgorde is gesorteerd door een query uit te sturen naar entiteiten met het juiste voor voegsel in de **RowKey**.  

* Als u wilt zoeken naar alle werk nemers van de afdeling verkoop met een werk nemer-id in het bereik **000100** tot **000199** , gesorteerd op werk nemer-id order gebruik: $filter = (PartitionKey EQ ' empid_Sales ') en (RowKey ge 000100 ') en (RowKey Le ' 000199 ')  
* Als u wilt zoeken naar alle werk nemers van de afdeling verkoop met een e-mail adres dat begint met ' a ' gesorteerd op e-mail adres order gebruiken: $filter = (PartitionKey EQ ' email_Sales ') en (RowKey ge ' a ') en (RowKey lt ' b ')  

Houd er rekening mee dat de filter syntaxis die in de bovenstaande voor beelden wordt gebruikt, afkomstig is uit het Table service REST API, Zie [query-entiteiten](https://msdn.microsoft.com/library/azure/dd179421.aspx)voor meer informatie.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* U kunt ervoor zorgen dat uw dubbele entiteiten uiteindelijk consistent zijn met elkaar met behulp van het [patroon voor uiteindelijk consistente trans acties](#eventually-consistent-transactions-pattern) voor het onderhouden van de primaire en secundaire index entiteiten.  
* Table Storage is relatief goed koop om te gebruiken, dus de kosten overhead van het opslaan van dubbele gegevens mag geen grote bezorgdheid zijn. U moet de kosten van uw ontwerp echter altijd evalueren op basis van uw verwachte opslag vereisten en alleen dubbele entiteiten toevoegen ter ondersteuning van de query's die door uw client toepassing worden uitgevoerd.  
* De waarde die wordt gebruikt voor de **RowKey** moet uniek zijn voor elke entiteit. Overweeg het gebruik van samengestelde sleutel waarden.  
* Met de opvullings numerieke waarden in de **RowKey** (bijvoorbeeld de werk nemer-id 000223) kunt u de sorteer-en filter functie op basis van de boven-en ondergrenzen sorteren.  
* U hoeft niet noodzakelijkerwijs alle eigenschappen van uw entiteit te dupliceren. Als bijvoorbeeld de query's die de entiteiten gebruiken die het e-mail adres in het **RowKey** hebben, de leeftijd van de werk nemer nooit nodig hebben, kunnen deze entiteiten de volgende structuur hebben:
  
  ![Werknemers entiteit met secundaire index][11]
* Het is doorgaans beter om dubbele gegevens op te slaan en ervoor te zorgen dat u alle gegevens kunt ophalen die u nodig hebt met één query dan om één query te gebruiken om een entiteit te zoeken met behulp van de secundaire index en een andere voor het opzoeken van de vereiste gegevens in de primaire index.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer uw client toepassing entiteiten moet ophalen met behulp van verschillende sleutels, wanneer uw client entiteiten in verschillende sorteer volgorden moet ophalen en waar u elke entiteit kunt identificeren met behulp van verschillende unieke waarden. Gebruik dit patroon wanneer u wilt voor komen dat de schaal limieten voor partities worden overschreden wanneer u entiteits lookups uitvoert met behulp van de verschillende **RowKey** -waarden.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Patroon uiteindelijk consistent trans acties](#eventually-consistent-transactions-pattern)  
* [Secundair index patroon voor de intra partitie](#intra-partition-secondary-index-pattern)  
* [Samengesteld sleutel patroon](#compound-key-pattern)  
* [Trans acties van entiteits groep](#entity-group-transactions)  
* [Werken met heterogene entiteits typen](#working-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Patroon uiteindelijk consistent trans acties
Schakel uiteindelijk consistent gedrag in voor de partitie grenzen of de grenzen van het opslag systeem met behulp van Azure-wacht rijen.  

#### <a name="context-and-problem"></a>Context en probleem
EGTs schakelt atomische trans acties in voor meerdere entiteiten die dezelfde partitie sleutel delen. Om redenen van prestaties en schaal baarheid kunt u ervoor kiezen om entiteiten op te slaan die consistentie vereisten hebben in afzonderlijke partities of in een afzonderlijk opslag systeem: in dit scenario kunt u EGTs niet gebruiken om consistentie te hand haven. U hebt bijvoorbeeld een vereiste voor het onderhouden van mogelijke consistentie tussen:  

* Entiteiten die zijn opgeslagen in twee verschillende partities in dezelfde tabel, in verschillende tabellen of in verschillende opslag accounts.  
* Een entiteit die is opgeslagen in de Table service en een blob die is opgeslagen in de Blob service.  
* Een entiteit die is opgeslagen in de Table service en een bestand in een bestands systeem.  
* Een entiteits archief in de Table service nog geïndexeerd met behulp van de Azure Cognitive Search-service.  

#### <a name="solution"></a>Oplossing
Met behulp van Azure queues kunt u een oplossing implementeren die uiteindelijke consistentie biedt in twee of meer partities of opslag systemen.
Om deze aanpak te illustreren, moet u ervan uitgaan dat u een vereiste hebt om oude werknemers entiteiten te archiveren. Oude werknemers entiteiten worden zelden gequeryeerd en moeten worden uitgesloten van activiteiten die worden uitgevoerd op huidige werk nemers. Voor het implementeren van deze vereiste moet u actieve werk nemers in de **huidige** tabel en de oude werk nemers in de **Archief** tabel opslaan. Voor het archiveren van een werk nemer moet u de entiteit uit de **huidige** tabel verwijderen en de entiteit toevoegen aan de **Archief** tabel, maar u kunt geen EGT gebruiken om deze twee bewerkingen uit te voeren. Om het risico te voor komen dat een entiteit in beide of geen van beide tabellen wordt weer gegeven, moet de archief bewerking uiteindelijk consistent zijn. In het volgende sequentie diagram ziet u een overzicht van de stappen in deze bewerking. Meer gedetailleerde informatie over uitzonderings paden vindt u in de volgende tekst.  

![Oplossings diagram voor uiteindelijke consistentie][12]

Een client initieert de archief bewerking door een bericht in een Azure-wachtrij te plaatsen, in dit voor beeld om werk nemers te archiveren #456. Een werk rollen pollt de wachtrij voor nieuwe berichten; Wanneer er een wordt gevonden, wordt het bericht gelezen en wordt er een verborgen kopie in de wachtrij geplaatst. Met de rol werk rollen volgende wordt een kopie van de entiteit opgehaald uit de **huidige** tabel, wordt een kopie ingevoegd in de **Archief** tabel en wordt vervolgens het origineel uit de **huidige** tabel verwijderd. Ten slotte, als er geen fouten zijn opgetreden uit de vorige stappen, verwijdert de rol van de werk nemer het verborgen bericht uit de wachtrij.  

In dit voor beeld voegt stap 4 de werk nemer in de **Archief** tabel in. De werk nemer kan worden toegevoegd aan een BLOB in de Blob service of een bestand in een bestands systeem.  

#### <a name="recovering-from-failures"></a>Herstellen van fouten
Het is belang rijk dat de bewerkingen in stap **4** en **5** moeten worden *idempotent* voor het geval de werk rollen de archief bewerking opnieuw moeten starten. Als u de Table service gebruikt, moet u voor stap **4** de bewerking ' invoegen of vervangen ' gebruiken. voor stap **5** moet u een bewerking delete if exists gebruiken in de client bibliotheek die u gebruikt. Als u een ander opslag systeem gebruikt, moet u een geschikte idempotent-bewerking gebruiken.  

Als de rol werk nemer nooit stap **6**voltooit, wordt na een time-out het bericht opnieuw in de wachtrij geplaatst om de werk rollen opnieuw te kunnen verwerken. De rol van de werk nemer kan controleren hoe vaak een bericht in de wachtrij is gelezen en, indien nodig, een ' verontreinigd ' bericht voor onderzoek worden verzonden door het naar een afzonderlijke wachtrij te verzenden. Zie [berichten ophalen](https://msdn.microsoft.com/library/azure/dd179474.aspx)voor meer informatie over het lezen van wachtrij berichten en het controleren van het aantal in de wachtrij.  

Aantal fouten in de tabel en wachtrij-services zijn tijdelijke fouten en de clienttoepassing moet bevatten geschikt Pogingslogica om deze te verwerken.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Deze oplossing biedt geen transactie isolatie. Een client kan bijvoorbeeld de **huidige** en **Archief** tabellen lezen wanneer de rol van werk nemers tussen stap **4** en **5**was en een inconsistente weer gave van de gegevens te zien krijgt. De gegevens zullen uiteindelijk consistent zijn.  
* U moet er zeker van zijn dat stap 4 en 5 idempotent zijn om te zorgen voor een uiteindelijke consistentie.  
* U kunt de oplossing schalen door meerdere wacht rijen en worker-instanties te gebruiken.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u de uiteindelijke consistentie tussen entiteiten die in verschillende partities of tabellen bestaan, wilt garanderen. U kunt dit patroon uitbreiden om te zorgen voor mogelijke consistentie voor bewerkingen in het Table service en de Blob service en andere gegevens bronnen die niet Azure Storage zijn, zoals data base of het bestands systeem.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Trans acties van entiteits groep](#entity-group-transactions)  
* [Samen voegen of vervangen](#merge-or-replace)  

> [!NOTE]
> Als de transactie isolatie belang rijk is voor uw oplossing, kunt u het beste uw tabellen opnieuw ontwerpen zodat u EGTs kunt gebruiken.  
> 
> 

### <a name="index-entities-pattern"></a>Patroon van index entiteiten
Behoud index entiteiten om efficiënte Zoek opdrachten in te scha kelen waarmee lijsten met entiteiten worden geretourneerd.  

#### <a name="context-and-problem"></a>Context en probleem
De Table service indexeert automatisch entiteiten met behulp van de waarden **PartitionKey** en **RowKey** . Hiermee kan een client toepassing een entiteit efficiënt ophalen met behulp van een Point-query. Met de tabel structuur die hieronder wordt weer gegeven, kan een client toepassing bijvoorbeeld een afzonderlijke werknemers entiteit ophalen met behulp van de afdelings naam en de werk nemer-id (de **PartitionKey** en **RowKey**).  

![Entiteit werk nemer][13]

Als u ook een lijst met werknemers entiteiten wilt kunnen ophalen op basis van de waarde van een andere niet-unieke eigenschap, zoals de achternaam, moet u een minder efficiënte partitie Scan gebruiken om overeenkomsten te vinden in plaats van een index te gebruiken om deze direct te zoeken. Dit komt doordat de Table service geen secundaire indexen biedt.  

#### <a name="solution"></a>Oplossing
Als u zoeken op achternaam wilt inschakelen met de entiteits structuur die hierboven wordt weer gegeven, moet u lijsten met werk nemer-id's onderhouden. Als u de entiteiten van werk nemers met een bepaalde achternaam wilt ophalen, bijvoorbeeld Jansen, moet u eerst de lijst met werk nemer-id's voor werk nemers met Jansen als achternaam vinden en vervolgens die entiteiten van de werk nemer ophalen. Er zijn drie belang rijke opties voor het opslaan van de lijsten met werk nemer-id's:  

* Gebruik Blob Storage.  
* Maak index entiteiten in dezelfde partitie als de entiteiten van de werk nemer.  
* Maak index entiteiten in een afzonderlijke partitie of tabel.  

<u>Optie #1: Blob Storage gebruiken</u>  

Voor de eerste optie maakt u een BLOB voor elke unieke achternaam en slaat u in elke Blob een lijst op met de waarden van de **PartitionKey** (Department) en **RowKey** (werk nemer-id) voor werk nemers die deze achternaam hebben. Wanneer u een werk nemer toevoegt of verwijdert, moet u ervoor zorgen dat de inhoud van de relevante BLOB uiteindelijk consistent is met de entiteiten van de werk nemer.  

<u>Optie #2:</u> Index entiteiten in dezelfde partitie maken  

Voor de tweede optie gebruikt u index entiteiten waarin de volgende gegevens zijn opgeslagen:  

![Werknemers entiteit met een teken reeks met een lijst met werk nemer-Id's met dezelfde achternaam][14]

De eigenschap **EmployeeIDs** bevat een lijst met werk nemer-id's voor werk nemers met de achternaam die is opgeslagen in de **RowKey**.  

De volgende stappen beschrijven het proces dat u moet volgen wanneer u een nieuwe werk nemer toevoegt als u de tweede optie gebruikt. In dit voor beeld voegen we een werk nemer met de id 000152 en een achternaam Jansen toe op de verkoop afdeling:  

1. Haal de index entiteit op met de **PartitionKey** -waarde "Sales" en de **RowKey** -waarde "Jansen". Sla de ETag van deze entiteit op die u in stap 2 wilt gebruiken.  
2. Maak een trans actie voor een entiteits groep (dat wil zeggen, een batch bewerking) die de nieuwe werknemers entiteit (**PartitionKey** waarde "Sales" en **RowKey** value "000152") invoegt en de index entiteit bijwerkt (**PartitionKey** -waarde "Sales" en **RowKey** waarde "Jansen") door de nieuwe werk nemer-id toe te voegen aan de lijst in het veld EmployeeIDs. Zie [trans acties voor entiteits](#entity-group-transactions)groepen voor meer informatie over entiteits groeps transacties.  
3. Als de trans actie van de entiteits groep mislukt vanwege een optimistische gelijktijdigheids fout (iemand anders de index entiteit heeft gewijzigd), moet u opnieuw beginnen met stap 1.  

U kunt een soort gelijke aanpak gebruiken om een werk nemer te verwijderen als u de tweede optie gebruikt. Het wijzigen van de achternaam van een werk nemer is iets ingewik kelder omdat u de trans actie van een entiteits groep moet uitvoeren die drie entiteiten bijwerkt: de werknemers entiteit, de index entiteit voor de oude achternaam en de index entiteit voor de nieuwe achternaam. U moet elke entiteit ophalen voordat u wijzigingen aanbrengt om de ETag-waarden op te halen die u vervolgens kunt gebruiken om de updates uit te voeren met optimistische gelijktijdigheid.  

De volgende stappen geven een overzicht van het proces dat u moet volgen wanneer u alle werk nemers met een bepaalde achternaam in een afdeling moet opzoeken als u de tweede optie gebruikt. In dit voor beeld worden alle werk nemers met de naam Jansen op de verkoop afdeling opzoeken:  

1. Haal de index entiteit op met de **PartitionKey** -waarde "Sales" en de **RowKey** -waarde "Jansen".  
2. De lijst met werk nemer-Id's parseren in het veld EmployeeIDs.  
3. Als u meer informatie nodig hebt over elk van deze werk nemers (zoals hun e-mail adressen), haalt u elk van de werk nemers op met behulp van de **PartitionKey** -waarde "Sales" en **RowKey** -waarden uit de lijst met werk nemers die u in stap 2 hebt verkregen.  

<u>Optie #3:</u> Index entiteiten in een afzonderlijke partitie of tabel maken  

Voor de derde optie gebruikt u index entiteiten waarin de volgende gegevens worden opgeslagen:  

![Werknemers entiteit met een teken reeks met een lijst met werk nemer-Id's met dezelfde achternaam][15]

De eigenschap **EmployeeIDs** bevat een lijst met werk nemer-id's voor werk nemers met de achternaam die is opgeslagen in de **RowKey**.  

Met de derde optie kunt u EGTs niet gebruiken om consistentie te behouden, omdat de index entiteiten zich in een afzonderlijke partitie van de entiteiten van de werk nemer bevinden. Zorg ervoor dat de index entiteiten uiteindelijk consistent zijn met de entiteiten van de werk nemer.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Deze oplossing vereist ten minste twee query's voor het ophalen van overeenkomende entiteiten: een om de index entiteiten te doorzoeken om de lijst met **RowKey** -waarden te verkrijgen, en vervolgens query's om elke entiteit in de lijst op te halen.  
* Omdat een individuele entiteit een maximum grootte van 1 MB, optie #2 en optie #3 in de oplossing heeft, wordt ervan uitgegaan dat de lijst met werk nemer-id's voor een gegeven achternaam nooit groter is dan 1 MB. Als de lijst met werk nemer-id's waarschijnlijk groter dan 1 MB groot is, gebruikt u de optie #1 en slaat u de index gegevens op in Blob Storage.  
* Als u de optie #2 gebruikt (met behulp van EGTs om werk nemers toe te voegen en te verwijderen, en de achternaam van een werk nemer te wijzigen), moet u evalueren of het volume van de trans acties de schaalbaarheids limieten in een bepaalde partitie zal belasten. Als dit het geval is, moet u rekening houden met een uiteindelijk consistente oplossing (optie #1 of optie #3) die wacht rijen gebruikt voor het verwerken van de update aanvragen en u in staat stelt uw index entiteiten op te slaan in een afzonderlijke partitie van de entiteiten van de werk nemer.  
* In de optie #2 in deze oplossing wordt ervan uitgegaan dat u wilt zoeken op achternaam binnen een afdeling: u wilt bijvoorbeeld een lijst met werk nemers ophalen met een achternaam Jansen op de verkoop afdeling. Als u alle werk nemers met een achternaam Jansen wilt kunnen opzoeken in de hele organisatie, gebruikt u een van de opties #1 of optie #3.
* U kunt een op een wachtrij gebaseerde oplossing implementeren die uiteindelijke consistentie biedt (Zie het [patroon uiteindelijk consistent trans acties](#eventually-consistent-transactions-pattern) voor meer informatie).  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u een set entiteiten wilt opzoeken die alle een gemeen schappelijke eigenschaps waarde delen, zoals alle werk nemers met de achternaam Jansen.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Samengesteld sleutel patroon](#compound-key-pattern)  
* [Patroon uiteindelijk consistent trans acties](#eventually-consistent-transactions-pattern)  
* [Trans acties van entiteits groep](#entity-group-transactions)  
* [Werken met heterogene entiteits typen](#working-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Het denormalisatie patroon
Gerelateerde gegevens samen voegen in één entiteit zodat u alle gegevens kunt ophalen die u nodig hebt met één punt query.  

#### <a name="context-and-problem"></a>Context en probleem
In een relationele data base normaliseren we doorgaans gegevens om duplicatie te verwijderen, wat resulteert in query's die gegevens uit meerdere tabellen ophalen. Als u uw gegevens in azure-tabellen normaliseert, moet u meerdere round trips van de client naar de server maken om de gerelateerde gegevens op te halen. Met de tabel structuur die hieronder wordt weer gegeven, hebt u bijvoorbeeld twee retour retouren nodig om de details van een afdeling op te halen: een voor het ophalen van de afdelings entiteit die de manager-id bevat en vervolgens een andere aanvraag om de details van de Manager op te halen in een entiteit van een werk nemer.  

![Afdelings-en werknemers entiteit][16]

#### <a name="solution"></a>Oplossing
In plaats van de gegevens in twee afzonderlijke entiteiten op te slaan, moet u de gegevens denormaliseren en een kopie van de details van de manager in de entiteit afdeling bewaren. Bijvoorbeeld:  

![Gedenormaliseerde en gecombineerde afdelings entiteit][17]

Als afdelings entiteiten met deze eigenschappen zijn opgeslagen, kunt u nu alle Details ophalen die u nodig hebt over een afdeling met behulp van een Point-query.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Er zijn kosten overhead gekoppeld aan het opslaan van een aantal gegevens twee maal. Het prestatie voordeel (als gevolg van minder aanvragen voor de opslag service) verlaagt doorgaans de marginale toename van de opslag kosten (en deze kosten worden deels gecompenseerd door een verlaging van het aantal trans acties dat u nodig hebt om de details van een afdeling op te halen ).  
* U moet de consistentie van de twee entiteiten onderhouden die informatie over managers opslaan. U kunt het consistentie probleem afhandelen met behulp van EGTs om meerdere entiteiten in één atomische trans actie bij te werken. in dit geval worden de afdelings entiteit en de werknemers entiteit voor de afdelings manager in dezelfde partitie opgeslagen.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u vaak verwante informatie wilt opzoeken. Dit patroon vermindert het aantal query's dat door de client moet worden gemaakt om de benodigde gegevens op te halen.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Samengesteld sleutel patroon](#compound-key-pattern)  
* [Trans acties van entiteits groep](#entity-group-transactions)  
* [Werken met heterogene entiteits typen](#working-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Samengesteld sleutel patroon
Gebruik samengestelde **RowKey** -waarden om een client in staat te stellen gerelateerde gegevens te zoeken met een single point-query.  

#### <a name="context-and-problem"></a>Context en probleem
In een relationele data base is het natuurlijk om samen voegingen in query's te gebruiken om gerelateerde gegevens items te retour neren naar de client in één query. U kunt bijvoorbeeld de werk nemer-id gebruiken om een lijst op te zoeken met gerelateerde entiteiten die de prestaties en de beoordelings gegevens voor die werk nemer bevatten.  

Stel dat u werknemers entiteiten opslaat in de Table service met behulp van de volgende structuur:  

![Entiteit werk nemer][18]

U moet ook historische gegevens over beoordelingen en prestaties opslaan voor elk jaar dat de werk nemer heeft gewerkt aan uw organisatie en u deze gegevens per jaar moet kunnen openen. U kunt ook een andere tabel maken waarin entiteiten worden opgeslagen met de volgende structuur:  

![Entiteit beoordeling van werk nemer][19]

Met deze aanpak kunt u ervoor kiezen om bepaalde gegevens (zoals de voor naam en achternaam) in de nieuwe entiteit te dupliceren, zodat u uw gegevens kunt ophalen met één aanvraag. U kunt echter geen sterke consistentie behouden omdat u geen EGT kunt gebruiken om de twee entiteiten atomisch bij te werken.  

#### <a name="solution"></a>Oplossing
Sla een nieuw entiteits type op in de oorspronkelijke tabel met behulp van entiteiten met de volgende structuur:  

![Entiteit van werk nemer met samengestelde sleutel][20]

U ziet dat de **RowKey** nu een samengestelde sleutel is die bestaat uit de werk nemer-id en het jaar van de beoordelings gegevens waarmee u de prestaties van de werk nemer kunt ophalen en gegevens met één enkele aanvraag voor één entiteit kan controleren.  

In het volgende voor beeld wordt beschreven hoe u alle controle gegevens voor een bepaalde werk nemer kunt ophalen (zoals werk nemer 000123 van de afdeling verkoop):  

$filter = (PartitionKey EQ ' Sales ') en (RowKey ge ' empid_000123 ') en (RowKey lt ' empid_000124 ') & $select = RowKey, Manager-classificatie, peer rating, opmerkingen  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* U moet een geschikt scheidings teken gebruiken waarmee u de **RowKey** -waarde eenvoudig kunt parseren: bijvoorbeeld **000123_2012**.  
* U slaat deze entiteit ook op in dezelfde partitie als andere entiteiten die gerelateerde gegevens bevatten voor dezelfde werk nemer. Dit betekent dat u EGTs kunt gebruiken om sterke consistentie te hand haven.
* U kunt overwegen hoe vaak u de gegevens wilt opvragen om te bepalen of dit patroon geschikt is.  Bijvoorbeeld, als u de gegevens over de controle en de belangrijkste gegevens van werk nemers vaker opent, moet u deze als afzonderlijke entiteiten houden.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u een of meer gerelateerde entiteiten wilt opslaan die u regel matig doorzoekt.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Trans acties van entiteits groep](#entity-group-transactions)  
* [Werken met heterogene entiteits typen](#working-with-heterogeneous-entity-types)  
* [Patroon uiteindelijk consistent trans acties](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Eind patroon van logboek
Haal de *n* -entiteiten die het laatst zijn toegevoegd aan een partitie op met behulp van een **RowKey** -waarde die wordt gesorteerd in omgekeerde datum-en tijd volgorde.  

> [!NOTE]
> Query resultaten die door de Azure-Table-API in Azure Cosmos DB zijn geretourneerd, worden niet gesorteerd op partitie sleutel of rij-sleutel. Dit patroon is dus geschikt voor Azure Table Storage en niet Azure Cosmos DB. Zie [verschillen tussen Table-API in azure Cosmos DB en Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)voor een gedetailleerde lijst met functie verschillen.

#### <a name="context-and-problem"></a>Context en probleem
Een algemene vereiste is om de meest recent gemaakte entiteiten op te halen, bijvoorbeeld de tien meest recente onkosten claims die zijn ingediend door een werk nemer. Tabel query's bieden ondersteuning voor een **$Top** query bewerking voor het retour neren van de eerste *n* entiteiten uit een set: er is geen equivalente query bewerking om de laatste n-entiteiten in een set te retour neren.  

#### <a name="solution"></a>Oplossing
Sla de entiteiten op met behulp van een **RowKey** die op natuurlijke wijze in omgekeerde datum/tijd wordt gesorteerd door gebruik te maken van de meest recente vermelding altijd het eerste item in de tabel.  

Als u bijvoorbeeld de tien meest recente onkosten claims wilt ophalen die door een werk nemer zijn ingediend, kunt u een omgekeerde maat waarde gebruiken die is afgeleid van de huidige datum/tijd. In het C# volgende code voorbeeld ziet u één manier om een geschikte ' omgekeerde Ticks '-waarde te maken voor een **RowKey** die van de meest recente naar de oudste sorteert:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

U kunt teruggaan naar de waarde datum en tijd met de volgende code:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

De tabel query ziet er als volgt uit:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* U moet de omgekeerde Tick-waarde met voorloop nullen aanvullen om ervoor te zorgen dat de teken reeks waarde op de verwachte manier wordt gesorteerd.  
* U moet rekening houden met de schaalbaarheids doelen op het niveau van een partitie. Wees voorzichtig met het maken van hot spot-partities.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u entiteiten in omgekeerde datum/tijd-volg orde wilt gebruiken of wanneer u toegang wilt krijgen tot de meest recent toegevoegde entiteiten.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Anti-patroon laten voorafgaan door/toevoegen](#prepend-append-anti-pattern)  
* [Entiteiten ophalen](#retrieving-entities)  

### <a name="high-volume-delete-pattern"></a>Patroon voor hoog volume verwijderen
Schakel het verwijderen van een groot aantal entiteiten in door alle entiteiten voor gelijktijdige verwijdering in hun eigen afzonderlijke tabel op te slaan. u verwijdert de entiteiten door de tabel te verwijderen.  

#### <a name="context-and-problem"></a>Context en probleem
Veel toepassingen verwijderen oude gegevens die niet meer beschikbaar zijn voor een client toepassing of die de toepassing heeft gearchiveerd naar een ander opslag medium. Normaal gesp roken identificeert u gegevens met een datum: u hebt bijvoorbeeld de vereiste om records te verwijderen van alle aanmeldings aanvragen die meer dan 60 dagen oud zijn.  

Een mogelijk ontwerp is het gebruik van de datum en tijd van de aanmeldings aanvraag in de **RowKey**:  

![Entiteit voor aanmeldings poging][21]

Deze aanpak voor komt het partitioneren van HOTS pots, omdat de toepassing aanmeldings entiteiten voor elke gebruiker in een afzonderlijke partitie kan invoegen en verwijderen. Deze aanpak kan echter kosten en tijd in beslag nemen als u een groot aantal entiteiten hebt, omdat u eerst een tabel scan moet uitvoeren om alle entiteiten te identificeren die moeten worden verwijderd. vervolgens moet u elke oude entiteit verwijderen. U kunt het aantal Retouren naar de server verminderen dat nodig is voor het verwijderen van de oude entiteiten door meerdere verwijderings aanvragen in batch op te nemen in EGTs.  

#### <a name="solution"></a>Oplossing
Gebruik een afzonderlijke tabel voor elke dag van de aanmeldings pogingen. U kunt het entiteits ontwerp hierboven gebruiken om HOTS pots te voor komen bij het invoegen van entiteiten, en het verwijderen van oude entiteiten is nu gewoon een kwestie van het verwijderen van één tabel elke dag (één opslag bewerking) in plaats van honderden en duizenden afzonderlijke items te zoeken en te verwijderen meldt elke dag aan bij de entiteiten.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Ondersteunt uw ontwerp andere manieren waarop uw toepassing de gegevens gebruikt, zoals het opzoeken van specifieke entiteiten, het koppelen met andere gegevens of het genereren van geaggregeerde gegevens?  
* Vermijdt uw ontwerp hot spots wanneer u nieuwe entiteiten invoegt?  
* Er wordt een vertraging verwacht als u dezelfde tabel naam wilt gebruiken nadat u deze hebt verwijderd. Het is beter om altijd unieke tabel namen te gebruiken.  
* Er wordt een aantal beperkingen verwacht wanneer u voor het eerst een nieuwe tabel gebruikt terwijl de Table service de toegangs patronen leert en de partities over verschillende knoop punten distribueert. U kunt overwegen hoe vaak nieuwe tabellen moeten worden gemaakt.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u een groot aantal entiteiten hebt dat tegelijk moet worden verwijderd.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Trans acties van entiteits groep](#entity-group-transactions)
* [Entiteiten wijzigen](#modifying-entities)  

### <a name="data-series-pattern"></a>Patroon van de gegevens reeks
Sla volledige gegevens reeksen op in één entiteit om het aantal aanvragen dat u maakt, te minimaliseren.  

#### <a name="context-and-problem"></a>Context en probleem
Een veelvoorkomend scenario is voor een toepassing om een reeks gegevens op te slaan die normaal gesp roken allemaal tegelijk moeten worden opgehaald. Uw toepassing kan bijvoorbeeld registreren hoeveel berichten elke werk nemer elk uur verzendt, en vervolgens deze gegevens gebruiken om te tekenen hoe vaak elke gebruiker de voor gaande 24 uur heeft verzonden. Het kan zijn dat u voor elke werk nemer 24 entiteiten opslaat:  

![Entiteit voor bericht statistieken][22]

Met dit ontwerp kunt u de entiteit die u wilt bijwerken voor elke werk nemer eenvoudig vinden en bijwerken wanneer de toepassing de waarde voor het aantal berichten moet bijwerken. Als u echter de informatie wilt ophalen om een grafiek van de activiteit voor de voor gaande 24 uur te tekenen, moet u 24 entiteiten ophalen.  

#### <a name="solution"></a>Oplossing
Gebruik het volgende ontwerp met een afzonderlijke eigenschap om het aantal berichten voor elk uur op te slaan:  

![Entiteit voor bericht statistieken met gescheiden eigenschappen][23]

Met dit ontwerp kunt u een samenvoeg bewerking gebruiken om het aantal berichten voor een werk nemer voor een bepaald uur bij te werken. U kunt nu alle informatie ophalen die u nodig hebt om de grafiek uit te zetten met behulp van een aanvraag voor één entiteit.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Als uw volledige gegevens reeks niet in één entiteit past (een entiteit kan Maxi maal 252 eigenschappen hebben), gebruikt u een alternatief gegevens archief, zoals een blob.  
* Als u meerdere clients tegelijk een entiteit bijwerkt, moet u de **ETAG** gebruiken voor het implementeren van optimistische gelijktijdigheid. Als u veel clients hebt, kunt u veel conflicten ondervinden.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u een gegevens reeks die aan een afzonderlijke entiteit is gekoppeld, moet bijwerken en ophalen.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Patroon voor grote entiteiten](#large-entities-pattern)  
* [Samen voegen of vervangen](#merge-or-replace)  
* [Uiteindelijk consistent transactie patroon](#eventually-consistent-transactions-pattern) (als u de gegevens reeksen opslaat in een blob)  

### <a name="wide-entities-pattern"></a>Patroon voor grote entiteiten
Gebruik meerdere fysieke entiteiten om logische entiteiten met meer dan 252 eigenschappen op te slaan.  

#### <a name="context-and-problem"></a>Context en probleem
Een afzonderlijke entiteit kan niet meer dan 252 eigenschappen hebben (met uitzonde ring van de verplichte systeem eigenschappen) en kan niet meer dan 1 MB aan gegevens in totaal opslaan. In een relationele data base krijgt u normaal gesp roken een afronding van de limieten voor de grootte van een rij door een nieuwe tabel toe te voegen en een 1-op-1-relatie tussen hen af te dwingen.  

#### <a name="solution"></a>Oplossing
Met behulp van de Table service kunt u meerdere entiteiten opslaan om één groot zakelijk object met meer dan 252 eigenschappen aan te duiden. Als u bijvoorbeeld een telling wilt opslaan van het aantal IM-berichten dat elke werk nemer heeft verzonden voor de afgelopen 365 dagen, kunt u het volgende ontwerp gebruiken dat twee entiteiten met verschillende schema's gebruikt:  

![Entiteit voor bericht statistieken met Rowkey 01 en de entiteit bericht status met Rowkey 02][24]

Als u een wijziging wilt aanbrengen waarvoor beide entiteiten moeten worden bijgewerkt zodat ze met elkaar gesynchroniseerd blijven, kunt u een EGT gebruiken. U kunt ook één samenvoeg bewerking gebruiken om het aantal berichten voor een specifieke dag bij te werken. Als u alle gegevens voor een individuele werk nemer wilt ophalen, moet u beide entiteiten ophalen, die u kunt doen met twee efficiënte aanvragen die zowel een **PartitionKey** als een **RowKey** -waarde gebruiken.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Het ophalen van een volledige logische entiteit omvat ten minste twee opslag transacties: één om elke fysieke entiteit op te halen.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u entiteiten wilt opslaan waarvan de grootte of het aantal eigenschappen groter is dan de limieten voor een afzonderlijke entiteit in de Table service.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Trans acties van entiteits groep](#entity-group-transactions)
* [Samen voegen of vervangen](#merge-or-replace)

### <a name="large-entities-pattern"></a>Patroon voor grote entiteiten
Gebruik Blob Storage voor het opslaan van grote eigenschaps waarden.  

#### <a name="context-and-problem"></a>Context en probleem
Een afzonderlijke entiteit kan niet meer dan 1 MB aan gegevens in totaal opslaan. Als een of meer van uw eigenschappen waarden opslaan die ervoor zorgen dat de totale grootte van uw entiteit deze waarde overschrijdt, kunt u de hele entiteit niet opslaan in de Table service.  

#### <a name="solution"></a>Oplossing
Als uw entiteit groter is dan 1 MB, omdat een of meer eigenschappen een grote hoeveelheid gegevens bevatten, kunt u gegevens opslaan in de Blob service en het adres van de BLOB vervolgens opslaan in een eigenschap in de entiteit. U kunt bijvoorbeeld de foto van een werk nemer opslaan in Blob Storage en een koppeling naar de foto opslaan in de eigenschap **foto** van uw werknemers entiteit:  

![Werknemers entiteit met een teken reeks voor een foto die verwijst naar Blob Storage][25]

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Als u de uiteindelijke consistentie tussen de entiteit in het Table service en de gegevens in de Blob service wilt behouden, gebruikt u het patroon met de [uiteindelijk consistente trans acties](#eventually-consistent-transactions-pattern) om uw entiteiten te onderhouden.
* Het ophalen van een volledige entiteit omvat ten minste twee opslag transacties: een om de entiteit op te halen en één om de BLOB-gegevens op te halen.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u entiteiten wilt opslaan waarvan de grootte de limieten voor een afzonderlijke entiteit in de Table service overschrijdt.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Patroon uiteindelijk consistent trans acties](#eventually-consistent-transactions-pattern)  
* [Patroon voor grote entiteiten](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Anti-patroon laten voorafgaan door/toevoegen
Verg root de schaal baarheid wanneer u een groot aantal toevoegingen hebt door de toevoegingen over meerdere partities te spreiden.  

#### <a name="context-and-problem"></a>Context en probleem
In afwachting van de in behandeling zijnde of toegevoegde entiteiten aan uw opgeslagen entiteiten resulteert doorgaans in de toepassing door nieuwe entiteiten toe te voegen aan de eerste of laatste partitie van een reeks partities. In dit geval worden alle invoegingen op elk gewenst moment in dezelfde partitie geplaatst, waardoor er een hotspot wordt gemaakt waarmee wordt voor komen dat de tabel service van taak verdeling wordt ingevoegd op meerdere knoop punten, waardoor uw toepassing mogelijk de schaalbaarheids doelen voor partitie. Als u bijvoorbeeld een toepassing hebt die netwerk-en bron toegang door werk nemers registreert, kan een entiteits structuur, zoals hieronder wordt weer gegeven, ertoe leiden dat de partitie van het huidige uur een hotspot wordt als het volume van trans acties het schaalbaarheids doel bereikt voor een afzonderlijke partitie:  

![Entiteit werk nemer][26]

#### <a name="solution"></a>Oplossing
De volgende alternatieve entiteits structuur voor komt een hotspot op een bepaalde partitie als de toepassing gebeurtenissen registreert:  

![Entiteit van werk nemers met RowKey die het jaar, de maand, de dag, het uur en de gebeurtenis-ID hebben samengesteld][27]

In dit voor beeld ziet u hoe zowel de **PartitionKey** als de **RowKey** samengestelde sleutels zijn. De **PartitionKey** gebruikt zowel de afdeling als de werk nemer-id om de logboek registratie over meerdere partities te verdelen.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Biedt de alternatieve sleutel structuur waarmee wordt voor komen dat u Hot partitioneert op invoegingen de query's die uw client toepassing maakt, efficiënt worden ondersteund?  
* Betekent uw verwachte volume aan trans acties dat u waarschijnlijk de schaalbaarheids doelen voor een afzonderlijke partitie zult bereiken en dat deze wordt beperkt door de opslag service?  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Vermijd het Anti-patroon laten voorafgaan door/Append wanneer uw volume aan trans acties waarschijnlijk de frequentie limiet van de opslag service kan opleveren wanneer u een hete partitie opent.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Samengesteld sleutel patroon](#compound-key-pattern)  
* [Eind patroon van logboek](#log-tail-pattern)  
* [Entiteiten wijzigen](#modifying-entities)  

### <a name="log-data-anti-pattern"></a>Anti-patroon van logboek gegevens
Normaal gesp roken moet u de Blob service gebruiken in plaats van de Table service om logboek gegevens op te slaan.  

#### <a name="context-and-problem"></a>Context en probleem
Een veelvoorkomende use-case voor logboek gegevens is het ophalen van een selectie logboek vermeldingen voor een specifiek datum/tijd bereik: u wilt bijvoorbeeld alle fout-en kritieke berichten zoeken die uw toepassing heeft geregistreerd tussen 15:04 en 15:06 op een specifieke datum. U wilt de datum en tijd van het logboek bericht niet gebruiken om te bepalen met welke partitie u logboek entiteiten opslaat: op een bepaald moment worden alle logboek entiteiten met dezelfde **PartitionKey** -waarde gedeeld (Zie de sectie [laten voorafgaan door/ Anti-patroon toevoegen](#prepend-append-anti-pattern)). Het volgende entiteits schema voor een logboek bericht resulteert bijvoorbeeld in een hete partitie, omdat de toepassing alle logboek berichten naar de partitie schrijft voor de huidige datum en tijd:  

![Entiteit van het logboek bericht][28]

In dit voor beeld bevat de **RowKey** de datum en tijd van het logboek bericht om er zeker van te zijn dat logboek berichten worden opgeslagen in de volg orde van datum/tijd en een bericht-id bevat als meerdere logboek berichten dezelfde datum en tijd delen.  

Een andere benadering is het gebruik van een **PartitionKey** die ervoor zorgt dat de toepassing berichten schrijft over diverse partities. Als de bron van het logboek bericht bijvoorbeeld een manier biedt om berichten over meerdere partities te verdelen, kunt u het volgende entiteits schema gebruiken:  

![Entiteit van het logboek bericht][29]

Het probleem met dit schema is echter dat alle logboek berichten moeten worden opgehaald voor een specifieke periode die u moet doorzoeken op elke partitie in de tabel.

#### <a name="solution"></a>Oplossing
In de vorige sectie is het probleem gemarkeerd dat probeert de Table service te gebruiken voor het opslaan van logboek vermeldingen en voorgestelde twee, niet-bevredigende ontwerpen. Een oplossing heeft geleid tot een hot partitie met het risico op slechte prestaties logboek berichten te schrijven. de andere oplossing resulteerde in slechtere query prestaties vanwege de vereiste om elke partitie in de tabel te scannen om logboek berichten op te halen voor een bepaalde periode. Blob Storage biedt een betere oplossing voor dit type scenario. Dit is de manier waarop Azure Opslaganalyse de logboek gegevens opslaat die worden verzameld.  

In deze sectie wordt beschreven hoe Opslaganalyse logboek gegevens opslaat in Blob Storage als een illustratie van deze benadering om gegevens op te slaan die u doorgaans doorzoekt op bereik.  

Opslaganalyse slaat logboek berichten op in een indeling met scheidings tekens in meerdere blobs. De indeling met scheidings tekens maakt het eenvoudig voor een client toepassing om de gegevens in het logboek bericht te parseren.  

Opslaganalyse gebruikt een naamgevings Conventie voor blobs waarmee u de BLOB (of blobs) kunt vinden die de logboek berichten bevat waarvoor u zoekt. Een blob met de naam Queue/2014/07/31/1800/000001. log bevat bijvoorbeeld logboek berichten die betrekking hebben op de wachtrij service voor het uur, te beginnen bij 18:00 op 31 juli 2014. De ' 000001 ' geeft aan dat dit het eerste logboek bestand voor deze periode is. Opslaganalyse registreert ook de tijds tempels van de eerste en laatste logboek berichten die in het bestand zijn opgeslagen als onderdeel van de meta gegevens van de blob. Met de API voor Blob Storage kunt u blobs vinden in een container op basis van een naam voorvoegsel: voor het zoeken van alle blobs die wachtrij logboek gegevens bevatten voor het uur vanaf 18:00, gebruikt u het voor voegsel ' queue/2014/07/31/1800 '.  

Opslaganalyse buffers logboek berichten intern en vervolgens regel matig de juiste BLOB bijwerken of een nieuw item met de meest recente batch logboek vermeldingen maken. Dit beperkt het aantal schrijf bewerkingen dat moet worden uitgevoerd naar de BLOB-service.  

Als u een soort gelijke oplossing in uw eigen toepassing implementeert, moet u overwegen hoe u de afweging tussen de betrouw baarheid (het schrijven van elke logboek vermelding naar Blob-opslag wanneer deze wordt uitgevoerd) en de kosten en schaal baarheid (het bufferen van updates in uw toepassing en schrijven ze naar Blob-opslag in batches).  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Houd rekening met de volgende punten wanneer u bepaalt hoe u logboek gegevens wilt opslaan:  

* Als u een tabel ontwerp maakt waarmee mogelijke Hot-partities worden voor komen, is het mogelijk dat u geen efficiënt toegang hebt tot uw logboek gegevens.  
* Voor het verwerken van logboek gegevens moet een client vaak veel records laden.  
* Hoewel logboek gegevens vaak worden gestructureerd, is het mogelijk dat Blob Storage een betere oplossing is.  

### <a name="implementation-considerations"></a>Afwegingen bij de implementatie
In deze sectie worden enkele aandachtspunten besproken wanneer u de in de vorige secties beschreven patronen implementeert. In de meeste van deze sectie worden voor C# beelden gebruikt die zijn geschreven in die gebruikmaken van de Storage-client bibliotheek (versie 4.3.0 op het moment van schrijven).  

### <a name="retrieving-entities"></a>Entiteiten ophalen
Zoals beschreven in het sectie [ontwerp voor het uitvoeren van query's](#design-for-querying), is de meest efficiënte query een Point-query. In sommige scenario's is het echter mogelijk dat u meerdere entiteiten moet ophalen. In deze sectie worden enkele veelvoorkomende benaderingen beschreven om entiteiten op te halen met behulp van de Storage-client bibliotheek.  

#### <a name="executing-a-point-query-using-the-storage-client-library"></a>Een Point-query uitvoeren met de Storage-client bibliotheek
De eenvoudigste manier om een punt query uit te voeren, is door de bewerking tabel **ophalen** te gebruiken, C# zoals wordt weer gegeven in het volgende code fragment waarmee een entiteit wordt opgehaald met een **PartitionKey** van de waarde "Sales" en een **RowKey** van de waarde "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

U ziet hoe in dit voor beeld wordt verwacht dat de entiteit die wordt opgehaald, van het type **EmployeeEntity**is.  

#### <a name="retrieving-multiple-entities-using-linq"></a>Ophalen van meerdere entiteiten met behulp van LINQ
U kunt meerdere entiteiten ophalen door gebruik te maken van LINQ met Storage-client bibliotheek en een query met een **where** -component op te geven. Als u een tabel scan wilt voor komen, moet u altijd de waarde **PartitionKey** in de component WHERE toevoegen en indien mogelijk de **RowKey** -waarde om te voor komen dat er tabel-en partitie scans worden uitgevoerd. De tabel service ondersteunt een beperkt aantal vergelijkings operatoren (groter dan, groter dan of gelijk aan, kleiner dan, kleiner dan of gelijk aan, gelijk aan en niet gelijk aan) die worden gebruikt in de component WHERE. In het C# volgende code fragment vindt u alle werk nemers waarvan de achternaam begint met ' B ' (ervan uitgaande dat de **RowKey** de achternaam opslaat) op de verkoop afdeling (ervan uitgaande dat de **PartitionKey** de naam van de afdeling opslaat):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

U ziet hoe de query zowel een **RowKey** als een **PartitionKey** opgeeft om betere prestaties te garanderen.  

In het volgende code voorbeeld wordt equivalente functionaliteit weer gegeven met behulp van de fluent API (Zie [Aanbevolen procedures voor het ontwerpen van een Fluent API](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)voor meer informatie over Fluent api's in het algemeen):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> Het voor beeld nest meerdere **CombineFilters** -methoden om de drie filter voorwaarden op te nemen.  
> 
> 

#### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Grote aantallen entiteiten ophalen uit een query
Een optimale query retourneert een afzonderlijke entiteit op basis van een **PartitionKey** -waarde en een **RowKey** -waarde. In sommige scenario's is het echter mogelijk dat u een groot aantal entiteiten van dezelfde partitie of zelfs van een groot aantal partities moet retour neren.  

In dergelijke scenario's moet u de prestaties van uw toepassing altijd volledig testen.  

Een query voor de tabel service kan Maxi maal 1.000 entiteiten tegelijk retour neren en kan Maxi maal vijf seconden worden uitgevoerd. Als de resultatenset meer dan 1.000 entiteiten bevat, als de query niet binnen vijf seconden is voltooid, of als de query de grens van de partitie overschrijdt, retourneert de Table service een vervolg token om de client toepassing in staat te stellen de volgende set entiteiten aan te vragen. Zie querytime [-out en paginering](https://msdn.microsoft.com/library/azure/dd135718.aspx)voor meer informatie over de werking van vervolg tokens.  

Als u de Storage-client bibliotheek gebruikt, kan het automatisch vervolg tokens voor u afhandelen wanneer het entiteiten van de Table service retourneert. In het C# volgende code voorbeeld met de Storage-client bibliotheek worden vervolg tokens automatisch verwerkt als de Table-service deze retourneert in een antwoord:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

De volgende C# code verwerkt vervolg tokens expliciet:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Door vervolg tokens expliciet te gebruiken, kunt u bepalen wanneer uw toepassing het volgende segment met gegevens ophaalt. Als uw client toepassing bijvoorbeeld gebruikers in staat stelt om de entiteiten die zijn opgeslagen in een tabel te bladeren, kan een gebruiker besluiten niet alle entiteiten die zijn opgehaald door de query, zodat uw toepassing alleen een vervolg token zou gebruiken om het volgende segment op te halen wanneer de gebruiker heeft de paginering voltooid van alle entiteiten in het huidige segment. Deze benadering heeft verschillende voor delen:  

* Hiermee kunt u de hoeveelheid gegevens die moet worden opgehaald van de Table service, en die u via het netwerk verplaatst, beperken.  
* Hiermee kunt u asynchrone i/o uitvoeren in .NET.  
* U kunt hiermee het vervolg token serialiseren naar permanente opslag, zodat u kunt door gaan in het geval een toepassing vastloopt.  

> [!NOTE]
> Een vervolg token retourneert meestal een segment met 1.000 entiteiten, maar dit kan minder zijn. Dit is ook het geval als u het aantal vermeldingen dat een query retourneert, beperkt door **gebruik te maken van** de eerste n entiteiten die overeenkomen met uw zoek criteria: de Table-service retourneert een segment met minder dan n entiteiten samen met een vervolg token naar Hiermee kunt u de resterende entiteiten ophalen.  
> 
> 

De volgende C# code laat zien hoe u het aantal entiteiten wijzigt dat binnen een segment wordt geretourneerd:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projectie aan server zijde
Eén entiteit kan Maxi maal 255 eigenschappen hebben en kan Maxi maal 1 MB groot zijn. Wanneer u een query uitvoert op de tabel en entiteiten ophaalt, hebt u mogelijk niet alle eigenschappen nodig en kunt u voor komen dat gegevens onnodig worden overgedragen (om latentie en kosten te beperken). U kunt projectie aan server zijde gebruiken om alleen de eigenschappen over te dragen die u nodig hebt. In het volgende voor beeld worden alleen de **e-mail** eigenschap (samen met **PartitionKey**, **RowKey**, **Time Stamp**en **ETAG**) opgehaald uit de entiteiten die zijn geselecteerd door de query.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

U ziet dat de waarde **RowKey** beschikbaar is, ook al is deze niet opgenomen in de lijst met eigenschappen die moeten worden opgehaald.  

### <a name="modifying-entities"></a>Entiteiten wijzigen
Met de Storage-client bibliotheek kunt u uw entiteiten die in de tabel service zijn opgeslagen, wijzigen door entiteiten in te voegen, te verwijderen en bij te werken. U kunt EGTs gebruiken om meerdere insert-, update-en delete-bewerkingen samen te nemen om het aantal benodigde retouren te verminderen en de prestaties van uw oplossing te verbeteren.  

Uitzonde ringen die worden gegenereerd wanneer de Storage-client bibliotheek een EGT uitvoert, bevatten doorgaans de index van de entiteit die de oorzaak van de batch heeft veroorzaakt. Dit is handig bij het opsporen van fouten in code die gebruikmaakt van EGTs.  

U moet ook overwegen hoe uw ontwerp van invloed is op de manier waarop uw client toepassing gelijktijdig valuta's en update bewerkingen verwerkt.  

#### <a name="managing-concurrency"></a>Gelijktijdigheid beheren
De tabel service implementeert standaard optimistische gelijktijdigheid controles op het niveau van afzonderlijke entiteiten voor **Insert**-, **Merge**-en **Delete** -bewerkingen, hoewel het mogelijk is dat een client de Table-service afdwingt deze te omzeilen plaats. Zie voor meer informatie over hoe de tabel service gelijktijdigheid beheert, [gelijktijdigheid beheren in Microsoft Azure Storage](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Samen voegen of vervangen
De methode **replace** van de klasse **TableOperation** vervangt altijd de volledige entiteit in de Table service. Als u in de aanvraag geen eigenschap opneemt wanneer die eigenschap in de opgeslagen entiteit bestaat, wordt die eigenschap door de aanvraag uit de opgeslagen entiteit verwijderd. Tenzij u een eigenschap expliciet van een opgeslagen entiteit wilt verwijderen, moet u elke eigenschap in de aanvraag toevoegen.  

U kunt de methode **Merge** van de klasse **TableOperation** gebruiken om de hoeveelheid gegevens te verminderen die u naar de Table service stuurt wanneer u een entiteit wilt bijwerken. De methode **Merge** vervangt alle eigenschappen in de opgeslagen entiteit door eigenschaps waarden van de entiteit die in de aanvraag is opgenomen, maar laat alle eigenschappen in de opgeslagen entiteit intact die niet zijn opgenomen in de aanvraag. Dit is handig als u grote entiteiten hebt en alleen een klein aantal eigenschappen in een aanvraag hoeft bij te werken.  

> [!NOTE]
> De methoden **vervangen** en **samen voegen** mislukken als de entiteit niet bestaat. Als alternatief kunt u de methoden **InsertOrReplace** en **InsertOrMerge** gebruiken om een nieuwe entiteit te maken als deze niet bestaat.  
> 
> 

### <a name="working-with-heterogeneous-entity-types"></a>Werken met heterogene entiteits typen
De Table-service is een *zonder schema* tabelarchief betekent dit dat één tabel entiteiten van meerdere typen bieden hoge mate van flexibiliteit bij het ontwerpen kunt opslaan. In het volgende voor beeld ziet u een tabel waarin zowel werk nemer-als afdelings entiteiten worden opgeslagen:  

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
<th>EmployeeCount</th>
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

Elke entiteit moet nog steeds waarden voor **PartitionKey**, **RowKey**en **Time Stamp** hebben, maar kan een set eigenschappen hebben. Bovendien is er niets om het type van een entiteit aan te geven, tenzij u ervoor kiest om die informatie ergens op te slaan. Er zijn twee opties voor het identificeren van het entiteits type:  

* Laten voorafgaan door het entiteits type naar de **RowKey** (of mogelijk de **PartitionKey**). Bijvoorbeeld **EMPLOYEE_000123** of **DEPARTMENT_SALES** als **RowKey** -waarden.  
* Gebruik een afzonderlijke eigenschap om het entiteits type vast te leggen, zoals wordt weer gegeven in de onderstaande tabel.  

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
<td>Werknemerrecords</td>
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
<td>Werknemerrecords</td>
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
<th>EmployeeCount</th>
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
<td>Werknemerrecords</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

De eerste optie, in afwachting van het entiteits type op de **RowKey**, is handig als er sprake is van een mogelijkheid dat twee entiteiten van verschillende typen dezelfde sleutel waarde hebben. Het groepeert ook entiteiten van hetzelfde type samen in de partitie.  

De technieken die in deze sectie worden beschreven, zijn met name relevant voor de relaties van de discussie [overname](#inheritance-relationships) eerder in deze hand leiding in de sectie model relaties.  

> [!NOTE]
> U moet overwegen een versie nummer in de waarde entiteits type op te nemen om client toepassingen in staat te stellen POCO-objecten te ontwikkelen en te werken met andere versies.  
> 
> 

In de rest van deze sectie worden enkele van de functies in de Storage-client bibliotheek beschreven, waarmee u in dezelfde tabel met meerdere entiteits typen kan werken.  

#### <a name="retrieving-heterogeneous-entity-types"></a>Heterogene entiteits typen ophalen
Als u de Storage-client bibliotheek gebruikt, hebt u drie opties voor het werken met meerdere entiteits typen.  

Als u het type entiteit weet dat is opgeslagen met een specifieke **RowKey** -en **PartitionKey** -waarden, kunt u het entiteits type opgeven wanneer u de entiteit ophaalt, zoals wordt weer gegeven in de vorige twee voor beelden waarin entiteiten van het type EmployeeEntity worden opgehaald: [Het uitvoeren van een Point-query met de Storage-client bibliotheek](#executing-a-point-query-using-the-storage-client-library) en [het ophalen van meerdere entiteiten met behulp van LINQ](#retrieving-multiple-entities-using-linq).  

De tweede optie is het gebruik van het type **DynamicTableEntity** (een eigenschappen verzameling) in plaats van een concreet poco entiteits type (deze optie kan ook de prestaties verbeteren omdat het niet nodig is om de entiteit te serialiseren en deserialiseren naar .net-typen). Met de C# volgende code worden mogelijk meerdere entiteiten van verschillende typen uit de tabel opgehaald, maar worden alle entiteiten als **DynamicTableEntity** -instanties geretourneerd. Vervolgens wordt de eigenschap **EntityType** gebruikt om het type van elke entiteit te bepalen:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
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
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Als u andere eigenschappen wilt ophalen, moet u de methode **TryGetValue** gebruiken voor de eigenschap **Properties** van de klasse **DynamicTableEntity** .  

Een derde optie is om te combi neren met behulp van het type **DynamicTableEntity** en een **EntityResolver** -exemplaar. Zo kunt u omzetten in meerdere POCO-typen in dezelfde query. In dit voor beeld gebruikt de gemachtigde van de **EntityResolver** de eigenschap **EntityType** om onderscheid te maken tussen de twee typen entiteiten die door de query worden geretourneerd. De methode **Resolve** gebruikt de **resolver** -gemachtigde voor het omzetten van **DynamicTableEntity** -instanties in **TableEntity** -exemplaren.  

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
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

#### <a name="modifying-heterogeneous-entity-types"></a>Heterogene entiteits typen wijzigen
U hoeft niet te weten welk type entiteit u wilt verwijderen en u weet altijd het type van een entiteit wanneer u deze invoegt. U kunt echter **DynamicTableEntity** type gebruiken om een entiteit bij te werken zonder het type ervan te weten en zonder een poco-entiteits klasse te gebruiken. Met het volgende code voorbeeld wordt één entiteit opgehaald en wordt gecontroleerd of de eigenschap **EmployeeCount** bestaat voordat deze wordt bijgewerkt.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

### <a name="controlling-access-with-shared-access-signatures"></a>Toegang beheren met hand tekeningen voor gedeelde toegang
U kunt Shared Access Signature-tokens (SAS) gebruiken om client toepassingen toe te staan om tabel entiteiten rechtstreeks te wijzigen (en query) zonder dat u rechtstreeks hoeft te verifiëren met de tabel service. Normaal gesp roken zijn er drie belang rijke voor delen van het gebruik van SAS in uw toepassing:  

* U hoeft de sleutel van uw opslag account niet te distribueren naar een niet-beveiligd platform (zoals een mobiel apparaat) om het apparaat in de Table service toe te staan om entiteiten te openen en te wijzigen.  
* U kunt een deel van het werk dat web-en werk rollen uitvoeren, gebruiken om uw entiteiten te beheren op client apparaten, zoals computers en mobiele apparaten voor eind gebruikers.  
* U kunt een beperkte en tijdgebonden set machtigingen toewijzen aan een client (zoals alleen-lezen toegang toestaan voor specifieke resources).  

Zie [using Shared Access signatures (SAS) (Engelstalig)](../storage/common/storage-dotnet-shared-access-signature-part-1.md)voor meer informatie over het gebruik van SAS-tokens met de Table service.  

U moet echter nog steeds de SAS-tokens genereren die een client toepassing verlenen aan de entiteiten in de Table-service: doe dit in een omgeving met beveiligde toegang tot uw opslag account sleutels. Normaal gesp roken gebruikt u een web-of worker-rol voor het genereren van de SAS-tokens en levert u deze aan de client toepassingen die toegang nodig hebben tot uw entiteiten. Omdat er nog steeds overhead is betrokken bij het genereren en leveren van SAS-tokens aan clients, moet u rekening houden met het beste om deze overhead te reduceren, met name in scenario's met grote volumes.  

Het is mogelijk om een SAS-token te genereren dat toegang verleent tot een subset van de entiteiten in een tabel. Standaard maakt u een SAS-token voor een hele tabel, maar het is ook mogelijk om op te geven dat het SAS-token toegang verleent tot een reeks **PartitionKey** -waarden of een bereik van **PartitionKey** -en **RowKey** -waarden. U kunt ervoor kiezen om SAS-tokens te genereren voor afzonderlijke gebruikers van uw systeem, zodat de SAS-token van elke gebruiker alleen toegang tot hun eigen entiteiten in de tabel service toestaat.  

### <a name="asynchronous-and-parallel-operations"></a>Asynchrone en parallelle bewerkingen
Als u uw aanvragen verspreidt over meerdere partities, kunt u de reactie snelheid van de door Voer en de client verbeteren met behulp van asynchrone of parallelle query's.
Stel dat u twee of meer worker-instanties tegelijk toegang hebt tot uw tabellen. U kunt afzonderlijke worker-rollen hebben die verantwoordelijk zijn voor bepaalde sets van partities of alleen meerdere rolinstanties hebben, die elk toegang hebben tot alle partities in een tabel.  

Binnen een client exemplaar kunt u de door Voer verbeteren door opslag bewerkingen asynchroon uit te voeren. Met de Storage-client bibliotheek kunt u eenvoudig asynchrone query's en wijzigingen schrijven. U kunt bijvoorbeeld beginnen met de synchrone methode waarmee alle entiteiten in een partitie worden opgehaald, zoals wordt weer gegeven in de C# volgende code:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

U kunt deze code eenvoudig wijzigen zodat de query asynchroon wordt uitgevoerd:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

In dit asynchrone voor beeld ziet u de volgende wijzigingen ten opzichte van de synchrone versie:  

* De methode handtekening bevat nu de **async** -modificator en retourneert een **taak** exemplaar.  
* In plaats van de **ExecuteSegmented** -methode aan te roepen om de resultaten op te halen, roept de methode nu de **ExecuteSegmentedAsync** -methode aan en gebruikt de wijzigings functie **await** om resultaten asynchroon op te halen.  

De client toepassing kan deze methode meerdere keren aanroepen (met verschillende waarden voor de para meter **afdeling** ) en elke query wordt uitgevoerd in een afzonderlijke thread.  

Er is geen asynchrone versie van de methode **Execute** in de klasse **TableQuery** , omdat de **IEnumerable** -interface geen ondersteuning biedt voor asynchrone inventarisatie.  

U kunt ook instanties asynchroon invoegen, bijwerken en verwijderen. In het C# volgende voor beeld ziet u een eenvoudige, synchrone methode voor het invoegen of vervangen van een werknemers entiteit:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

U kunt deze code eenvoudig aanpassen zodat de update op de volgende manier asynchroon wordt uitgevoerd:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

In dit asynchrone voor beeld ziet u de volgende wijzigingen ten opzichte van de synchrone versie:  

* De methode handtekening bevat nu de **async** -modificator en retourneert een **taak** exemplaar.  
* In plaats van de **Execute** -methode aan te roepen om de entiteit bij te werken, roept de methode nu de methode **ExecuteAsync** aan en gebruikt de wijzigings functie **await** om resultaten asynchroon op te halen.  

De client toepassing kan meerdere asynchrone methoden, zoals deze, aanroepen en elke methode aanroep wordt uitgevoerd op een afzonderlijke thread.  

### <a name="credits"></a>Tegoeden
We willen graag de volgende leden van het Azure-team bedanken voor hun bijdragen: Dominic Betts, Jason Hogg, Jean Ghanem, Jai Haridas, Jeff Irwin, Vamshidhar Kommineni, Vinay Shah en Serdar Ozler, evenals Tom Hollander van micro soft DX. 

We willen ook de volgende micro soft Mvp's voor hun waardevolle feedback in de loop van de beoordelings cycli bedanken: Igor Papirov en Edward Bakker.

[1]: ./media/storage-table-design-guide/storage-table-design-IMAGE01.png
[2]: ./media/storage-table-design-guide/storage-table-design-IMAGE02.png
[3]: ./media/storage-table-design-guide/storage-table-design-IMAGE03.png
[4]: ./media/storage-table-design-guide/storage-table-design-IMAGE04.png
[5]: ./media/storage-table-design-guide/storage-table-design-IMAGE05.png
[6]: ./media/storage-table-design-guide/storage-table-design-IMAGE06.png
[7]: ./media/storage-table-design-guide/storage-table-design-IMAGE07.png
[8]: ./media/storage-table-design-guide/storage-table-design-IMAGE08.png
[9]: ./media/storage-table-design-guide/storage-table-design-IMAGE09.png
[10]: ./media/storage-table-design-guide/storage-table-design-IMAGE10.png
[11]: ./media/storage-table-design-guide/storage-table-design-IMAGE11.png
[12]: ./media/storage-table-design-guide/storage-table-design-IMAGE12.png
[13]: ./media/storage-table-design-guide/storage-table-design-IMAGE13.png
[14]: ./media/storage-table-design-guide/storage-table-design-IMAGE14.png
[15]: ./media/storage-table-design-guide/storage-table-design-IMAGE15.png
[16]: ./media/storage-table-design-guide/storage-table-design-IMAGE16.png
[17]: ./media/storage-table-design-guide/storage-table-design-IMAGE17.png
[18]: ./media/storage-table-design-guide/storage-table-design-IMAGE18.png
[19]: ./media/storage-table-design-guide/storage-table-design-IMAGE19.png
[20]: ./media/storage-table-design-guide/storage-table-design-IMAGE20.png
[21]: ./media/storage-table-design-guide/storage-table-design-IMAGE21.png
[22]: ./media/storage-table-design-guide/storage-table-design-IMAGE22.png
[23]: ./media/storage-table-design-guide/storage-table-design-IMAGE23.png
[24]: ./media/storage-table-design-guide/storage-table-design-IMAGE24.png
[25]: ./media/storage-table-design-guide/storage-table-design-IMAGE25.png
[26]: ./media/storage-table-design-guide/storage-table-design-IMAGE26.png
[27]: ./media/storage-table-design-guide/storage-table-design-IMAGE27.png
[28]: ./media/storage-table-design-guide/storage-table-design-IMAGE28.png
[29]: ./media/storage-table-design-guide/storage-table-design-IMAGE29.png

