---
title: Azure Cosmos DB-tabellen ontwerpen voor schalen en prestaties
description: 'Ontwerphandleiding azure table-opslag: schaalbare en performante tabellen in Azure Cosmos DB- en Azure Table-opslag'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: sakash279
ms.author: akshanka
ms.custom: seodec18
ms.openlocfilehash: 166076d366cbbf7bef24648772beaba9b3a88253
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246472"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Ontwerphandleiding azure table-opslagtabel: schaalbare en performante tabellen

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Als u schaalbare en beter bruikbare tabellen wilt ontwerpen, moet u rekening houden met verschillende factoren, waaronder kosten. Als u eerder schema's hebt ontworpen voor relationele databases, zullen deze aandachtspunten u bekend voorkomen. Hoewel er een aantal overeenkomsten zijn tussen Azure Table-opslag en relationele modellen, zijn er ook veel belangrijke verschillen. Deze verschillen leiden doorgaans tot verschillende ontwerpen, die misschien vreemd aandoen voor iemand die bekend is met relationele databases. De ontwerpen zijn echter zeer geschikt als u ontwerpt voor een archief op basis van NoSQL-sleutels/waarden, zoals Table-opslag.

Tabelopslag is ontworpen om toepassingen op cloudschaal te ondersteunen die miljarden entiteiten ('rijen' in relationele databaseterminologie) aan gegevens kunnen bevatten, of voor gegevenssets die hoge transactievolumes moeten ondersteunen. U moet daarom anders denken over hoe u uw gegevens opslaat en begrijpen hoe tabelopslag werkt. Een goed ontworpen NoSQL-gegevensarchief kan uw oplossing in staat stellen om veel verder te schalen (en tegen lagere kosten) dan een oplossing die een relationele database gebruikt. Deze gids helpt u met deze onderwerpen.  

## <a name="about-azure-table-storage"></a>Informatie over Azure Table-opslag
In dit gedeelte worden enkele van de belangrijkste functies van tabelopslag belicht die vooral relevant zijn voor het ontwerpen voor prestaties en schaalbaarheid. Zie [Inleiding tot Microsoft Azure Storage](../storage/common/storage-introduction.md) en Aan de slag met Azure [Table-opslag met .NET](table-storage-how-to-use-dotnet.md) voordat u de rest van dit artikel leest, als u nieuw bent in Azure Storage en Tabelopslag. Hoewel de focus van deze handleiding ligt op tabelopslag, bevat deze wel enige discussie over Azure Queue-opslag en Azure Blob-opslag en hoe u deze samen met Tabelopslag in een oplossing gebruiken.  

Tabelopslag gebruikt een tabelindeling om gegevens op te slaan. In de standaardterminologie vertegenwoordigt elke rij van de tabel een entiteit en slaan de kolommen de verschillende eigenschappen van die entiteit op. Elke entiteit heeft een paar sleutels om deze op unieke wijze te identificeren en een tijdstempelkolom die tabelopslag gebruikt om bij te houden wanneer de entiteit voor het laatst is bijgewerkt. Het tijdstempelveld wordt automatisch toegevoegd en u de tijdstempel niet handmatig overschrijven met een willekeurige waarde. Tabelopslag gebruikt deze laatst gewijzigde tijdstempel (LMT) om optimistische gelijktijdigheid te beheren.  

> [!NOTE]
> Api-bewerkingen voor tabelopslagrest leveren ook een `ETag` waarde terug die is afgeleid van het LMT. In dit document worden de termen ETag en LMT door elkaar gebruikt, omdat ze verwijzen naar dezelfde onderliggende gegevens.  
> 
> 

In het volgende voorbeeld wordt een eenvoudig tabelontwerp weergegeven om werknemers- en afdelingsentiteiten op te slaan. Veel van de voorbeelden die later in deze gids worden getoond, zijn gebaseerd op dit eenvoudige ontwerp.  

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
<td>Hall</td>
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
<td>Jun</td>
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
<th>Aantal werknemers</th>
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
<td>Ken Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Tot nu toe lijkt dit ontwerp op een tabel in een relationele database. De belangrijkste verschillen zijn de verplichte kolommen en de mogelijkheid om meerdere entiteitstypen in dezelfde tabel op te slaan. Bovendien heeft elk van de door de gebruiker gedefinieerde eigenschappen, zoals **FirstName** of **Age,** een gegevenstype, zoals geheel getal of tekenreeks, net als een kolom in een relationele database. Anders dan in een relationele database betekent de schemaloze aard van tabelopslag echter dat een eigenschap niet hetzelfde gegevenstype op elke entiteit hoeft te hebben. Als u complexe gegevenstypen in één eigenschap wilt opslaan, moet u een geserialiseerde indeling gebruiken, zoals JSON of XML. Zie [Gegevensmodel voor tabelopslaginformatie begrijpen](https://msdn.microsoft.com/library/azure/dd179338.aspx)voor meer informatie .

Uw keuze `PartitionKey` `RowKey` van en is van fundamenteel belang voor een goede tafel ontwerp. Elke entiteit die in een tabel `PartitionKey` is `RowKey`opgeslagen, moet een unieke combinatie hebben van en . Net als bij sleutels in een `PartitionKey` `RowKey` relationele databasetabel worden de waarden en waarden geïndexeerd om een geclusterde index te maken die snelle look-ups mogelijk maakt. Tabelopslag maakt echter geen secundaire indexen, dus dit zijn de enige twee geïndexeerde eigenschappen (sommige van de later beschreven patronen laten zien hoe u werken rond deze schijnbare beperking).  

Een tabel bestaat uit een of meer partities, en veel van de ontwerpbeslissingen `PartitionKey` `RowKey` die u neemt, zullen bestaan uit het kiezen van een geschikte en het optimaliseren van uw oplossing. Een oplossing kan bestaan uit slechts één tabel die al uw entiteiten bevat die zijn ingedeeld in partities, maar meestal heeft een oplossing meerdere tabellen. Tabellen helpen u om uw entiteiten logisch te organiseren en u te helpen de toegang tot de gegevens te beheren met behulp van toegangscontrolelijsten. U een hele tabel laten vallen met één opslagbewerking.  

### <a name="table-partitions"></a>Tabelpartities
De accountnaam, de `PartitionKey` tabelnaam en samen de partitie identificeren binnen de opslagservice waar tabelopslag de entiteit opslaat. Partities maken niet alleen deel uit van het adresseringsschema voor entiteiten, maar definiëren ook een ruimte voor transacties (zie de sectie later in dit artikel, [entiteitsgroepstransacties)](#entity-group-transactions)en vormen de basis van hoe tabelopslagschalen. Zie [Checklist Prestaties en schaalbaarheid voor tabelopslag voor](../storage/tables/storage-performance-checklist.md)meer informatie over tabelpartities.  

In tabelopslag worden een afzonderlijk knooppunt een of meer volledige partities en de serviceschalen geleverd door dynamisch verdelingspartities over knooppunten te laden. Als een knooppunt onder belasting staat, kan tabelopslag het bereik van partities die door dat knooppunt worden onderhouden, splitsen op verschillende knooppunten. Wanneer het verkeer afneemt, kan tabelopslag de partitievarieert van stille knooppunten terug naar één knooppunt samenvoegen.  

Zie [Microsoft Azure Storage: Een zeer beschikbare cloudopslagservice met sterke consistentie](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)voor meer informatie over de interne details van tabelopslag en in het bijzonder hoe deze partities beheert.  

### <a name="entity-group-transactions"></a>Entiteitsgroeptransacties
In tabelopslag zijn entiteitsgroepstransacties (EGT's) het enige ingebouwde mechanisme voor het uitvoeren van atoomupdates voor meerdere entiteiten. EGT's worden ook wel *batchtransacties*genoemd . EGT's kunnen alleen werken op entiteiten die in dezelfde partitie zijn opgeslagen (dezelfde partitiesleutel delen in een bepaalde tabel), dus op elk moment hebt u atoomtransactioneel gedrag nodig tussen meerdere entiteiten, om ervoor te zorgen dat deze entiteiten zich in dezelfde partitie bevinden. Dit is vaak een reden om meerdere entiteitstypen in dezelfde tabel (en partitie) te houden en geen meerdere tabellen te gebruiken voor verschillende entiteitstypen. Eén EGT kan maximaal 100 entiteiten uitvoeren.  Als u meerdere gelijktijdige ET's indient voor verwerking, is het belangrijk om ervoor te zorgen dat deze EGT's niet werken op entiteiten die veel voorkomen in ET's. Anders loopt u het risico de verwerking uit te stellen.

EGT's introduceren ook een mogelijke afweging voor u om te evalueren in uw ontwerp. Het gebruik van meer partities verhoogt de schaalbaarheid van uw toepassing, omdat Azure meer mogelijkheden heeft voor taakverdelingsaanvragen tussen knooppunten. Maar dit kan het vermogen van uw toepassing beperken om atoomtransacties uit te voeren en een sterke consistentie voor uw gegevens te behouden. Bovendien zijn er specifieke schaalbaarheidsdoelen op het niveau van een partitie die de doorvoer van transacties die u voor één knooppunt verwachten, kunnen beperken.

Zie [Schaalbaarheidsdoelen voor standaardopslagaccounts voor](../storage/common/scalability-targets-standard-account.md)meer informatie over schaalbaarheidsdoelen voor Azure-opslagaccounts. Zie [Schaalbaarheids- en prestatiedoelen voor tabelopslag voor](../storage/tables/scalability-targets.md)meer informatie over schaalbaarheidsdoelen voor tabelopslag. Latere secties van deze gids bespreken verschillende ontwerpstrategieën die u helpen trade-offs zoals deze te beheren en te bespreken hoe u uw partitiesleutel het beste kiezen op basis van de specifieke vereisten van uw clienttoepassing.  

### <a name="capacity-considerations"></a>Overwegingen bij capaciteitsbepaling
In de volgende tabel worden enkele van de belangrijkste waarden bevat waar u rekening mee moet houden wanneer u een oplossing voor tabelopslag ontwerpt:  

| Totale capaciteit van een Azure-opslagaccount | 500 TB |
| --- | --- |
| Aantal tabellen in een Azure-opslagaccount |Alleen beperkt door de capaciteit van het opslagaccount. |
| Aantal partities in een tabel |Alleen beperkt door de capaciteit van het opslagaccount. |
| Aantal entiteiten in een partitie |Alleen beperkt door de capaciteit van het opslagaccount. |
| Grootte van een afzonderlijke entiteit |Maximaal 1 MB, met een maximum van 255 eigenschappen (inclusief de `PartitionKey`, , `RowKey`en `Timestamp`). |
| Grootte van de`PartitionKey` |Een tekenreeks tot 1 KB groot. |
| Grootte van de`RowKey` |Een tekenreeks tot 1 KB groot. |
| Grootte van een entiteitsgroepstransactie |Een transactie kan maximaal 100 entiteiten omvatten en het laadvermogen moet minder dan 4 MB groot zijn. Een EGT kan een entiteit slechts één keer bijwerken. |

Zie [Het gegevensmodel tabelserviceservice begrijpen](https://msdn.microsoft.com/library/azure/dd179338.aspx)voor meer informatie .  

### <a name="cost-considerations"></a>Kostenoverwegingen
Tabelopslag is relatief goedkoop, maar u moet kostenramingen opnemen voor zowel het capaciteitsgebruik als de hoeveelheid transacties als onderdeel van uw evaluatie van een oplossing die tabelopslag gebruikt. In veel scenario's is het opslaan van gedenormaliseerde of dubbele gegevens om de prestaties of schaalbaarheid van uw oplossing te verbeteren echter een geldige aanpak. Zie [Azure Storage-prijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over prijzen.  

## <a name="guidelines-for-table-design"></a>Richtlijnen voor tabelontwerp
In deze lijsten worden enkele van de belangrijkste richtlijnen samengevat waarmee u rekening moet houden bij het ontwerpen van uw tabellen. Deze gids behandelt ze allemaal in meer detail later. Deze richtlijnen verschillen van de richtlijnen die u doorgaans zou volgen voor relationele databaseontwerp.  

Uw tafelopslag ontwerpen om efficiënt te *lezen:*

* **Ontwerp voor query's in leeszware toepassingen.** Wanneer u uw tabellen ontwerpt, moet u nadenken over de query's (vooral de latentiegevoelige query's) die u uitvoert voordat u nadenkt over hoe u uw entiteiten bijwerkt. Dit resulteert meestal in een efficiënte en performante oplossing.  
* **Geef `PartitionKey` beide `RowKey` en in uw query's op.** *Puntenquery's* zoals deze zijn de meest efficiënte query's voor tabelopslag.  
* **Overweeg dubbele kopieën van entiteiten op te slaan.** Tabelopslag is goedkoop, dus overweeg om dezelfde entiteit meerdere keren op te slaan (met verschillende sleutels), om efficiëntere query's mogelijk te maken.  
* **Overweeg uw gegevens te denormaliseren.** Tabelopslag is goedkoop, dus overweeg uw gegevens te denormaliseren. Sla bijvoorbeeld overzichtsentiteiten op, zodat query's voor geaggregeerde gegevens slechts toegang hoeven te krijgen tot één entiteit.  
* **Gebruik samengestelde sleutelwaarden.** De enige sleutels `PartitionKey` die `RowKey`je hebt zijn en. Gebruik bijvoorbeeld samengestelde sleutelwaarden om alternatieve hoofdtoegangspaden naar entiteiten in te schakelen.  
* **Queryprojectie gebruiken.** U de hoeveelheid gegevens die u via het netwerk overdraagt, verminderen door query's te gebruiken die alleen de velden selecteren die u nodig hebt.  

Uw tabelopslag zo ontwerpen dat u efficiënt *schrijft:*  

* **Maak geen hete partities.** Kies toetsen waarmee u uw aanvragen op elk gewenst moment over meerdere partities spreiden.  
* **Vermijd pieken in het verkeer.** Verdeel het verkeer over een redelijke periode en vermijd pieken in het verkeer.
* **Maak niet noodzakelijkerwijs een aparte tabel voor elk type entiteit.** Wanneer u atoomtransacties voor entiteitstypen nodig hebt, u deze meerdere entiteitstypen opslaan in dezelfde partitie in dezelfde tabel.
* **Houd rekening met de maximale doorvoer die u moet bereiken.** U moet zich bewust zijn van de schaalbaarheidsdoelen voor tabelopslag en ervoor zorgen dat uw ontwerp er niet toe zorgt dat u deze bereikt.  

Later in deze gids zie je voorbeelden die al deze principes in de praktijk brengen.  

## <a name="design-for-querying"></a>Ontwerp voor query's
Tafelopslag kan intensief worden gelezen, intensief schrijven of een mix van de twee. In deze sectie wordt het ontwerpen om leesbewerkingen efficiënt te ondersteunen. Een ontwerp dat leesbewerkingen efficiënt ondersteunt, is doorgaans ook efficiënt voor schrijfbewerkingen. Er zijn echter aanvullende overwegingen bij het ontwerpen om schrijfbewerkingen te ondersteunen. Deze worden besproken in de volgende sectie, [Ontwerp voor gegevenswijziging.](#design-for-data-modification)

Een goed uitgangspunt om u in staat te stellen gegevens efficiënt te lezen, is om te vragen "Welke query's moet mijn toepassing uitvoeren om de gegevens op te halen die het nodig heeft?"  

> [!NOTE]
> Met Tafelopslag is het belangrijk om het ontwerp van voren correct te krijgen, omdat het moeilijk en duur is om het later te wijzigen. In een relationele database is het bijvoorbeeld vaak mogelijk om prestatieproblemen op te lossen door simpelweg indexen toe te voegen aan een bestaande database. Dit is geen optie met tabelopslag.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>Hoe uw `PartitionKey` keuze `RowKey` van en beïnvloedt de queryprestaties
In de volgende voorbeelden wordt ervan uitgegaan dat tabelopslag werknemersentiteiten opslaat `Timestamp` met de volgende structuur (de meeste voorbeelden laten de eigenschap voor duidelijkheid weg):  

| Kolomnaam | Gegevenstype |
| --- | --- |
| `PartitionKey`(Afdelingsnaam) |Tekenreeks |
| `RowKey`(Werknemers-ID) |Tekenreeks |
| `FirstName` |Tekenreeks |
| `LastName` |Tekenreeks |
| `Age` |Geheel getal |
| `EmailAddress` |Tekenreeks |

Hier volgen enkele algemene richtlijnen voor het ontwerpen van query's voor tabelopslag. De filtersyntaxis die in de volgende voorbeelden wordt gebruikt, is afkomstig uit de API REST van tabelopslag. Zie [Queryentiteiten voor](https://msdn.microsoft.com/library/azure/dd179421.aspx)meer informatie .  

* Een *puntquery* is de meest efficiënte opzoeking om te gebruiken en wordt aanbevolen voor zoekopdrachten of zoekopdrachten met een hoog volume waarvoor de laagste latentie vereist is. Een dergelijke query kan de indexen gebruiken om een afzonderlijke `PartitionKey` `RowKey` entiteit efficiënt te lokaliseren door zowel de waarden als de waarden op te geven. Bijvoorbeeld: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* Tweede beste is een *bereik query*. Het gebruikt `PartitionKey`de , en `RowKey` filters op een bereik van waarden om meer dan één entiteit terug te sturen. De `PartitionKey` waarde identificeert een specifieke `RowKey` partitie en de waarden identificeren een subset van de entiteiten in die partitie. Bijvoorbeeld: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* Derde beste is een *partitie scan*. Het gebruikt `PartitionKey`de , en filters op een andere niet-key eigenschap en kan meer dan een entiteit retourneren. De `PartitionKey` waarde identificeert een specifieke partitie en de eigenschapswaarden selecteren voor een subset van de entiteiten in die partitie. Bijvoorbeeld: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* Een *tabelscan* bevat niet `PartitionKey`de , en is inefficiënt omdat het zoekt in alle partities die deel uitmaken van uw tabel voor alle overeenkomende entiteiten. Er wordt een tabelscan uitgevoerd, ongeacht of `RowKey`het filter de . Bijvoorbeeld: `$filter=LastName eq 'Jones'`.  
* Azure Table-opslagquery's die meerdere `PartitionKey` entiteiten retourneren, sorteren ze in en `RowKey` orde. Als u wilt voorkomen dat de entiteiten in de client worden gebruikt, kiest u een `RowKey` volgorde die de meest voorkomende sorteervolgorde definieert. Queryresultaten die worden geretourneerd door de Azure Table API in Azure Cosmos DB, worden niet gesorteerd op partitiesleutel of rijsleutel. Zie [verschillen tussen tabel-API in Azure Cosmos DB en Azure Table-opslag](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)voor een gedetailleerde lijst met functieverschillen.

Het gebruik**or**van een filter op `RowKey` basis van waarden resulteert in een partitiescan en wordt niet behandeld als een bereikquery. Vermijd daarom query's die filters `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`gebruiken, zoals: .  

Zie voor voorbeelden van clientcode die de opslagclientbibliotheek gebruikt om efficiënte query's uit te voeren:  

* [Een puntquery uitvoeren met de opslagclientbibliotheek](#run-a-point-query-by-using-the-storage-client-library)
* [Meerdere entiteiten ophalen met LINQ](#retrieve-multiple-entities-by-using-linq)
* [Projectie aan de serverzijde](#server-side-projection)  

Zie voor voorbeelden van client-side code die meerdere entiteitstypen kan verwerken die in dezelfde tabel zijn opgeslagen:  

* [Werken met heterogene entiteitstypen](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Kies een geschikte`PartitionKey`
Uw keuze `PartitionKey` moet de noodzaak om het gebruik van EGT's (om consistentie te garanderen) in evenwicht brengen met de vereiste om uw entiteiten over meerdere partities te verdelen (om een schaalbare oplossing te garanderen).  

In één keer u al uw entiteiten in één partitie opslaan. Dit kan echter de schaalbaarheid van uw oplossing beperken en zou voorkomen dat tabelopslag aanvragen voor laden en in balans kan laden. Aan het andere uiterste u één entiteit per partitie opslaan. Dit is zeer schaalbaar en stelt tabelopslag in staat om aanvragen voor het laden van saldo te laden, maar voorkomt dat u entiteitsgroeptransacties gebruiken.  

Een `PartitionKey` ideaal stelt u in staat om efficiënte query's te gebruiken en heeft voldoende partities om ervoor te zorgen dat uw oplossing schaalbaar is. Doorgaans zult u merken dat uw entiteiten een geschikte eigenschap hebben die uw entiteiten over voldoende partities verdeelt.

> [!NOTE]
> Bijvoorbeeld, in een systeem dat informatie over `UserID` gebruikers of `PartitionKey`werknemers opslaat, kan een goede zijn. Mogelijk hebt u verschillende entiteiten `UserID` die een bepaalde als partitiesleutel gebruiken. Elke entiteit die gegevens over een gebruiker opslaat, wordt gegroepeerd in één partitie. Deze entiteiten zijn toegankelijk via ET's, terwijl ze nog steeds zeer schaalbaar zijn.
> 
> 

Er zijn aanvullende overwegingen `PartitionKey` in uw keuze van die betrekking hebben op hoe u entiteiten invoegen, bijwerken en verwijderen. Zie [Ontwerpen voor gegevenswijziging](#design-for-data-modification) later in dit artikel voor meer informatie.  

### <a name="optimize-queries-for-table-storage"></a>Query's optimaliseren voor tabelopslag
Tabelopslag indexeert uw entiteiten automatisch `PartitionKey` `RowKey` met behulp van de waarden en waarden in één geclusterde index. Dit is de reden dat puntquery's het meest efficiënt zijn om te gebruiken. Er zijn echter geen andere indexen dan die `PartitionKey` op `RowKey`de geclusterde index op de en .

Veel ontwerpen moeten voldoen aan vereisten om het opzoeken van entiteiten op basis van meerdere criteria mogelijk te maken. Bijvoorbeeld het lokaliseren van werknemersentiteiten op basis van e-mail, werknemers-ID of achternaam. De volgende patronen in de sectie [Tabelontwerppatronen](#table-design-patterns) hebben betrekking op dit soort vereisten. De patronen beschrijven ook manieren om te werken rond het feit dat tabelopslag geen secundaire indexen biedt.  

* [Secundair indexpatroon binnen partitie:](#intra-partition-secondary-index-pattern)Sla meerdere kopieën `RowKey` van elke entiteit op met behulp van verschillende waarden (in dezelfde partitie). Dit maakt snelle en efficiënte lookups en `RowKey` alternatieve sorteerorders mogelijk met behulp van verschillende waarden.  
* [Secundair indexpatroon tussen partities](#inter-partition-secondary-index-pattern): Sla meerdere `RowKey` kopieën van elke entiteit op met behulp van verschillende waarden in afzonderlijke partities of in afzonderlijke tabellen. Dit maakt snelle en efficiënte lookups en `RowKey` alternatieve sorteerorders mogelijk met behulp van verschillende waarden.  
* [Patroon van indexentiteiten:](#index-entities-pattern)indexentiteiten onderhouden om efficiënte zoekopdrachten in te schakelen die lijsten met entiteiten retourneren.  

### <a name="sort-data-in-table-storage"></a>Gegevens sorteren in tabelopslag

Tabelopslag retourneert queryresultaten gesorteerd in `PartitionKey` oplopende `RowKey`volgorde, op basis van en vervolgens op .

> [!NOTE]
> Queryresultaten die worden geretourneerd door de Azure Table API in Azure Cosmos DB, worden niet gesorteerd op partitiesleutel of rijsleutel. Zie [verschillen tussen tabel-API in Azure Cosmos DB en Azure Table-opslag](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)voor een gedetailleerde lijst met functieverschillen.

Toetsen in tabelopslag zijn tekenreekswaarden. Om ervoor te zorgen dat numerieke waarden correct sorteren, moet u ze converteren naar een vaste lengte en ze met nullen vastzetten. Als u bijvoorbeeld de waarde van `RowKey` de werknemers-id die u als getalwaarde gebruikt, converteert, moet u de werknemers-id **123** converteren naar **00000123**. 

Veel toepassingen hebben vereisten voor het gebruik van gegevens die in verschillende orders worden gesorteerd: bijvoorbeeld het sorteren van werknemers op naam of op datum van toetreding. De volgende patronen in de [ontwerppatronen](#table-design-patterns) van de sectie Tabel hebben betrekking op het alternatieve sorteren van orders voor uw entiteiten:  

* [Secundair indexpatroon binnen partitie:](#intra-partition-secondary-index-pattern)Sla meerdere kopieën `RowKey` van elke entiteit op met behulp van verschillende waarden (in dezelfde partitie). Dit maakt snelle en efficiënte lookups en `RowKey` alternatieve sorteerorders mogelijk met behulp van verschillende waarden.  
* [Secundair indexpatroon tussen partities](#inter-partition-secondary-index-pattern): Sla meerdere `RowKey` kopieën van elke entiteit op met behulp van verschillende waarden in afzonderlijke partities in afzonderlijke tabellen. Dit maakt snelle en efficiënte lookups en `RowKey` alternatieve sorteerorders mogelijk met behulp van verschillende waarden.
* [Logboekstaartpatroon](#log-tail-pattern): Haal de *n-entiteiten* op die `RowKey` het meest recent aan een partitie zijn toegevoegd, met behulp van een waarde die wordt sorteert in omgekeerde datum en tijdvolgorde.  

## <a name="design-for-data-modification"></a>Ontwerp voor gegevenswijziging
In dit gedeelte wordt de ontwerpoverwegingen voor het optimaliseren van inserts, updates en deletes gericht. In sommige gevallen moet u de afweging tussen ontwerpen die optimaliseren voor het bevragen tegen ontwerpen die optimaliseren voor gegevenswijziging evalueren. Deze evaluatie is vergelijkbaar met wat u doet in ontwerpen voor relationele databases (hoewel de technieken voor het beheren van de ontwerpafwegingen verschillend zijn in een relationele database). De [ontwerppatronen](#table-design-patterns) van de sectie tabel beschrijft enkele gedetailleerde ontwerppatronen voor tabelopslag en belicht enkele van deze afwegingen. In de praktijk zult u merken dat veel ontwerpen die zijn geoptimaliseerd voor het opvragen van entiteiten ook goed werken voor het wijzigen van entiteiten.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>De prestaties van invoegbewerkingen optimaliseren, bijwerken en verwijderen
Als u een entiteit wilt bijwerken of verwijderen, `PartitionKey` moet `RowKey` u deze kunnen identificeren met behulp van de en de waarden. In dit opzicht moet `PartitionKey` `RowKey` uw keuze van en voor het wijzigen van entiteiten vergelijkbare criteria volgen als uw keuze om puntquery's te ondersteunen. U wilt entiteiten zo efficiënt mogelijk identificeren. U wilt geen inefficiënte partitie- of tabelscan gebruiken om een `PartitionKey` entiteit `RowKey` te vinden om de waarden te ontdekken die u moet bijwerken of verwijderen.  

De volgende patronen in de [ontwerppatronen](#table-design-patterns) van de sectie Tabel zijn gericht op het optimaliseren van de prestaties van uw bewerkingen voor invoegen, bijwerken en verwijderen:  

* [Patroon voor het verwijderen](#high-volume-delete-pattern)van een hoog volume: schakel het verwijderen van een groot aantal entiteiten in door alle entiteiten op te slaan voor gelijktijdige verwijdering in hun eigen afzonderlijke tabel. U verwijdert de entiteiten door de tabel te verwijderen.  
* [Patroon gegevensreeksen](#data-series-pattern): Sla volledige gegevensreeksen op in één entiteit om het aantal aanvragen dat u doet te minimaliseren.  
* [Breed entiteitspatroon:](#wide-entities-pattern)gebruik meerdere fysieke entiteiten om logische entiteiten met meer dan 252 eigenschappen op te slaan.  
* [Patroon grote entiteiten](#large-entities-pattern): Gebruik blobopslag om grote eigenschapswaarden op te slaan.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Zorgen voor consistentie in uw opgeslagen entiteiten
De andere belangrijke factor die uw keuze van sleutels voor het optimaliseren van gegevenswijzigingen beïnvloedt, is hoe u consistentie garanderen door atomaire transacties te gebruiken. U een EGT alleen gebruiken om te werken op entiteiten die in dezelfde partitie zijn opgeslagen.  

De volgende patronen in de [ontwerppatronen van](#table-design-patterns) de sectie Tabel hebben betrekking op het beheren van consistentie:  

* [Secundair indexpatroon binnen partitie:](#intra-partition-secondary-index-pattern)Sla meerdere kopieën `RowKey` van elke entiteit op met behulp van verschillende waarden (in dezelfde partitie). Dit maakt snelle en efficiënte lookups en `RowKey` alternatieve sorteerorders mogelijk met behulp van verschillende waarden.  
* [Secundair indexpatroon tussen partities](#inter-partition-secondary-index-pattern): Sla meerdere `RowKey` kopieën van elke entiteit op met behulp van verschillende waarden in afzonderlijke partities of in afzonderlijke tabellen. Dit maakt snelle en efficiënte lookups en `RowKey` alternatieve sorteerorders mogelijk met behulp van verschillende waarden.  
* [Uiteindelijk consistent transactiepatroon](#eventually-consistent-transactions-pattern): Uiteindelijk consistent gedrag inschakelen over de grenzen van de partitie of de grenzen van het opslagsysteem met Azure-wachtrijen.
* [Patroon van indexentiteiten:](#index-entities-pattern)indexentiteiten onderhouden om efficiënte zoekopdrachten in te schakelen die lijsten met entiteiten retourneren.  
* [Denormalisatiepatroon](#denormalization-pattern): Combineer gerelateerde gegevens samen in één entiteit, zodat u alle gegevens ophalen die u nodig hebt met één puntquery.  
* [Patroon van gegevensreeksen](#data-series-pattern): Sla volledige gegevensreeksen op in één entiteit om het aantal aanvragen dat u doet te minimaliseren.  

Zie [Entiteitsgroepstransacties](#entity-group-transactions) later in dit artikel voor meer informatie.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Zorg ervoor dat uw ontwerp voor efficiënte aanpassingen efficiënte vragen vergemakkelijkt
In veel gevallen resulteert een ontwerp voor efficiënt query's in efficiënte wijzigingen, maar u moet altijd evalueren of dit het geval is voor uw specifieke scenario. Sommige patronen in de [ontwerppatronen](#table-design-patterns) van de sectie Tabel evalueren expliciet afwegingen tussen query's en wijzigenvan entiteiten en u moet altijd rekening houden met het aantal van elk type bewerking.  

De volgende patronen in de sectie [Tabelontwerppatronen](#table-design-patterns) hebben betrekking op afwegingen tussen het ontwerpen voor efficiënte query's en het ontwerpen voor efficiënte gegevenswijziging:  

* [Samengestelde sleutelpatroon:](#compound-key-pattern) `RowKey` gebruik samengestelde waarden om een client in staat te stellen gerelateerde gegevens op te zoeken met één puntquery.  
* [Logboekstaartpatroon](#log-tail-pattern): Haal de *n-entiteiten* op die `RowKey` het meest recent aan een partitie zijn toegevoegd, met behulp van een waarde die wordt sorteert in omgekeerde datum en tijdvolgorde.  

## <a name="encrypt-table-data"></a>Tabelgegevens versleutelen
De clientbibliotheek .NET Azure Storage ondersteunt versleuteling van tekenreeksentiteiteigenschappen voor het invoegen en vervangen van bewerkingen. De versleutelde tekenreeksen worden opgeslagen op de service als binaire eigenschappen, en ze worden omgezet terug naar strings na decryptie.    

Voor tabellen moeten gebruikers naast het versleutelingsbeleid ook de eigenschappen opgeven die moeten worden versleuteld. Geef een `EncryptProperty` kenmerk op (voor POCO-entiteiten die afkomstig zijn van), `TableEntity`of geef een versleutelingsresolver op in aanvraagopties. Een versleutelingsresolver is een gemachtigde die een partitiesleutel, rijsleutel en eigendomsnaam neemt en een Booleaanse retourneert die aangeeft of die eigenschap moet worden versleuteld. Tijdens versleuteling gebruikt de clientbibliotheek deze informatie om te beslissen of een eigenschap moet worden versleuteld tijdens het schrijven naar de draad. De gemachtigde voorziet ook in de mogelijkheid van logica rond hoe eigenschappen worden versleuteld. (Als X bijvoorbeeld eigenschap A versleutelt; anders eigenschappen A en B versleutelen.) Het is niet nodig om deze informatie te verstrekken tijdens het lezen of opvragen van entiteiten.

Samenvoegen wordt momenteel niet ondersteund. Omdat een subset van eigenschappen mogelijk eerder is versleuteld met behulp van een andere sleutel, leidt het samenvoegen van de nieuwe eigenschappen en het bijwerken van de metagegevens tot gegevensverlies. Voor het samenvoegen van extra serviceoproepen moet u de reeds bestaande entiteit van de service lezen of een nieuwe sleutel per eigenschap gebruiken. Geen van beide is geschikt om prestatieredenen.     

Zie [Client-side encryptie en Azure Key Vault voor Microsoft Azure Storage voor](../storage/common/storage-client-side-encryption.md)informatie over het versleutelen van tabelgegevens.  

## <a name="model-relationships"></a>Modelrelaties
Het bouwen van domeinmodellen is een belangrijke stap in het ontwerp van complexe systemen. Meestal gebruikt u het modelleringsproces om entiteiten en de relaties tussen hen te identificeren, als een manier om het bedrijfsdomein te begrijpen en het ontwerp van uw systeem te informeren. In dit gedeelte wordt gefocusd op hoe u enkele van de gemeenschappelijke relatietypen in domeinmodellen vertalen naar ontwerpen voor tabelopslag. Het proces van toewijzing van een logisch gegevensmodel naar een fysiek NoSQL-gebaseerd gegevensmodel verschilt van het proces dat wordt gebruikt bij het ontwerpen van een relationele database. Relationele databases ontwerp gaat meestal uit van een data normalisatie proces geoptimaliseerd voor het minimaliseren van redundantie. Een dergelijk ontwerp gaat ook uit van een declaratieve querymogelijkheid die de implementatie van de werking van de database abstraheert.  

### <a name="one-to-many-relationships"></a>Een-op-veel relaties
Een-op-veel relaties tussen zakelijke domeinobjecten komen vaak voor: bijvoorbeeld één afdeling heeft veel werknemers. Er zijn verschillende manieren om een-op-veel relaties in tabelopslag te implementeren, elk met voor- en nadelen die relevant kunnen zijn voor het specifieke scenario.  

Denk aan het voorbeeld van een grote multinational met tienduizenden afdelingen en werknemersentiteiten. Elke afdeling heeft veel medewerkers en elke medewerker is gekoppeld aan één specifieke afdeling. Een benadering is het opslaan van afzonderlijke afdelingen en werknemersentiteiten, zoals:  

![Afbeelding met een afdelingsentiteit en een werknemerentiteit][1]

In dit voorbeeld wordt een impliciete één-op-één-relatie weergegeven tussen de typen, op basis van de `PartitionKey` waarde. Elke afdeling kan veel medewerkers hebben.  

In dit voorbeeld worden ook een afdelingsentiteit en de bijbehorende werknemersentiteiten in dezelfde partitie weergegeven. U ervoor kiezen om verschillende partities, tabellen of zelfs opslagaccounts te gebruiken voor de verschillende entiteitstypen.  

Een alternatieve benadering is om uw gegevens te denormaliseren en alleen werknemersentiteiten op te slaan met gedenormaliseerde afdelingsgegevens, zoals in het volgende voorbeeld wordt weergegeven. In dit specifieke scenario is deze gedenormaliseerde aanpak mogelijk niet de beste als u een vereiste hebt om de details van een afdelingsmanager te kunnen wijzigen. Om dit te doen, moet u elke werknemer in de afdeling bijwerken.  

![Afbeelding van de entiteit van de werknemer][2]

Zie het [denormalisatiepatroon](#denormalization-pattern) later in deze handleiding voor meer informatie.  

In de volgende tabel worden de voor- en nadelen van elk van de benaderingen voor het opslaan van werknemers- en afdelingsentiteiten die een één-op-éénrelatie hebben, samengevat. U moet ook overwegen hoe vaak u verwacht om verschillende bewerkingen uit te voeren. Het kan aanvaardbaar zijn om een ontwerp te hebben dat een dure bewerking bevat als die bewerking slechts zelden plaatsvindt.  

<table>
<tr>
<th>Methode</th>
<th>Voordelen</th>
<th>Nadelen</th>
</tr>
<tr>
<td>Afzonderlijke entiteitstypen, dezelfde partitie, dezelfde tabel</td>
<td>
<ul>
<li>U een afdelingsentiteit bijwerken met één bewerking.</li>
<li>U een EGT gebruiken om de consistentie te behouden als u een afdelingsentiteit moet wijzigen wanneer u een werknemerentiteit bijwerkt/invoegt/verwijdert. Als u bijvoorbeeld een afdelingsaantal medewerkers voor elke afdeling bijhoudt.</li>
</ul>
</td>
<td>
<ul>
<li>Mogelijk moet u zowel een werknemer als een afdelingsentiteit ophalen voor bepaalde clientactiviteiten.</li>
<li>Opslagbewerkingen vinden plaats in dezelfde partitie. Bij hoge transactievolumes kan dit resulteren in een hotspot.</li>
<li>U een werknemer niet verplaatsen naar een nieuwe afdeling met behulp van een EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Afzonderlijke entiteitstypen, verschillende partities of tabellen of opslagaccounts</td>
<td>
<ul>
<li>U een afdelingsentiteit of werknemerentiteit bijwerken met één bewerking.</li>
<li>Bij hoge transactievolumes kan dit helpen de belasting over meer partities te spreiden.</li>
</ul>
</td>
<td>
<ul>
<li>Mogelijk moet u zowel een werknemer als een afdelingsentiteit ophalen voor bepaalde clientactiviteiten.</li>
<li>U GEEN EGT's gebruiken om de consistentie te behouden wanneer u een werknemer bijwerkt/invoegt/verwijdert en een afdeling bijwerkt. Bijvoorbeeld het bijwerken van een aantal werknemers in een afdelingsentiteit.</li>
<li>U een werknemer niet verplaatsen naar een nieuwe afdeling met behulp van een EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormaliseren in één entiteitstype</td>
<td>
<ul>
<li>U alle informatie die u nodig hebt met een enkel verzoek ophalen.</li>
</ul>
</td>
<td>
<ul>
<li>Het kan duur zijn om consistentie te behouden als u de informatie van de afdeling moet bijwerken (dit vereist dat u alle werknemers in een afdeling bijwerkt).</li>
</ul>
</td>
</tr>
</table>

Hoe u kiest tussen deze opties, en welke van de voors en tegens het meest belangrijk zijn, is afhankelijk van uw specifieke toepassingsscenario's. Hoe vaak wijzigt u bijvoorbeeld afdelingsentiteiten? Hebben al uw personeelsvragen aanvullende afdelingsinformatie nodig? Hoe dicht bent u bij de schaalbaarheidslimieten voor uw partities of uw opslagaccount?  

### <a name="one-to-one-relationships"></a>Een-op-een relaties
Domeinmodellen kunnen een-op-een relaties tussen entiteiten bevatten. Als u een één-op-één relatie in tabelopslag moet implementeren, moet u ook kiezen hoe u de twee gerelateerde entiteiten koppelt wanneer u ze beide moet ophalen. Deze koppeling kan impliciet zijn, gebaseerd op een conventie in de kernwaarden, `PartitionKey` of `RowKey` expliciet, door een koppeling op te slaan in de vorm van en waarden in elke entiteit aan haar verbonden entiteit. Zie de sectie [Een-op-veel relaties](#one-to-many-relationships)voor een discussie over de vraag of u de gerelateerde entiteiten in dezelfde partitie moet opslaan.  

Er zijn ook implementatieoverwegingen die ertoe kunnen leiden dat u één-op-één relaties implementeert in tabelopslag:  

* Omgaan met grote entiteiten (zie [Patroon grote entiteiten](#large-entities-pattern)voor meer informatie).  
* Toegangsbesturingselementen implementeren (zie [Toegang beheren met gedeelde toegangshandtekeningen](#control-access-with-shared-access-signatures)voor meer informatie).  

### <a name="join-in-the-client"></a>Doe mee met de klant
Hoewel er manieren zijn om relaties in tabelopslag te modelleren, vergeet dan niet dat de twee belangrijkste redenen voor het gebruik van tabelopslag schaalbaarheid en prestaties zijn. Als u merkt dat u veel relaties modelleert die de prestaties en schaalbaarheid van uw oplossing in gevaar brengen, moet u zich afvragen of het nodig is om alle gegevensrelaties in uw tabelontwerp op te bouwen. U het ontwerp mogelijk vereenvoudigen en de schaalbaarheid en prestaties van uw oplossing verbeteren, als u uw clienttoepassing de benodigde joins laat uitvoeren.  

Als u bijvoorbeeld kleine tabellen hebt die gegevens bevatten die niet vaak worden gewijzigd, u deze gegevens één keer ophalen en op de cache opslaan op de client. Dit kan voorkomen dat herhaalde retouren om dezelfde gegevens op te halen. In de voorbeelden die we in deze gids hebben bekeken, is de set afdelingen in een kleine organisatie waarschijnlijk klein en verandert het zelden. Dit maakt het een goede kandidaat voor gegevens die een client applicatie kan een keer downloaden en cache als opzoekgegevens.  

### <a name="inheritance-relationships"></a>Overervingsrelaties
Als uw clienttoepassing een reeks klassen gebruikt die deel uitmaken van een overervingsrelatie om bedrijfsentiteiten te vertegenwoordigen, u deze entiteiten eenvoudig in tabelopslag blijven gebruiken. U bijvoorbeeld de volgende set klassen hebben gedefinieerd in `Person` uw clienttoepassing, waarbij een abstracte klasse is.

![Diagram van overervingsrelaties][3]

U instanties van de twee betonklassen in `Person` Tabelopslag blijven gebruiken met één tabel. Gebruik entiteiten die er als volgt uitzien:  

![Afbeelding met entiteit en werknemer][4]

Zie Later in deze handleiding [werken met heterogene entiteitstypen](#work-with-heterogeneous-entity-types) voor meer informatie over het werken met meerdere entiteitstypen in dezelfde tabel in clientcode. Dit geeft voorbeelden van hoe u het entiteitstype in clientcode herkennen.  

## <a name="table-design-patterns"></a>Tabelontwerppatronen
In eerdere secties hebt u geleerd hoe u het tabelontwerp optimaliseren voor zowel het ophalen van entiteitsgegevens met behulp van query's als voor het invoegen, bijwerken en verwijderen van entiteitsgegevens. In deze sectie worden enkele patronen beschreven die geschikt zijn voor gebruik met tabelopslag. Daarnaast zult u zien hoe u een aantal van de kwesties en trade-offs die eerder in deze gids aan de orde zijn gesteld, praktisch aanpakken. In het volgende diagram worden de relaties tussen de verschillende patronen samengevat:  

![Diagram met tabelontwerppatronen][5]

De patroonkaart markeert enkele relaties tussen patronen (blauw) en antipatronen (oranje) die in deze handleiding zijn gedocumenteerd. Er zijn natuurlijk veel andere patronen die het overwegen waard zijn. Een van de belangrijkste scenario's voor Tabelopslag is bijvoorbeeld het gebruik van het [gematerialiseerde weergavepatroon](https://msdn.microsoft.com/library/azure/dn589782.aspx) van het [segregatiepatroon voor de verantwoordelijkheid](https://msdn.microsoft.com/library/azure/jj554200.aspx) van de opdrachtquery.  

### <a name="intra-partition-secondary-index-pattern"></a>Secundair indexpatroon binnen partitie
Sla meerdere kopieën van `RowKey` elke entiteit op met behulp van verschillende waarden (in dezelfde partitie). Dit maakt snelle en efficiënte lookups en `RowKey` alternatieve sorteerorders mogelijk met behulp van verschillende waarden. Updates tussen kopieën kunnen consistent worden gehouden met behulp van EGT's.  

#### <a name="context-and-problem"></a>Context en probleem
Tabelopslag indexeert entiteiten automatisch `PartitionKey` met `RowKey` behulp van de en waarden. Hierdoor kan een clienttoepassing een entiteit efficiënt ophalen met behulp van deze waarden. Met de volgende tabelstructuur kan een clienttoepassing bijvoorbeeld een puntquery gebruiken om een afzonderlijke werknemersentiteit `PartitionKey` op `RowKey` te halen met behulp van de afdelingsnaam en de werknemers-id (de en waarden). Een client kan ook entiteiten ophalen die zijn gesorteerd op werknemers-id binnen elke afdeling.

![Afbeelding van de entiteit van de werknemer][6]

Als u ook een werknemerentiteit wilt vinden op basis van de waarde van een andere eigenschap, zoals e-mailadres, moet u een minder efficiënte partitiescan gebruiken om een overeenkomst te vinden. Dit komt omdat tabelopslag geen secundaire indexen biedt. Bovendien is er geen optie om een lijst van werknemers aan `RowKey` te vragen die in een andere volgorde zijn gesorteerd dan bestelling.  

#### <a name="solution"></a>Oplossing
Als u wilt werken aan het ontbreken van secundaire indexen, `RowKey` u meerdere kopieën van elke entiteit opslaan, waarbij elke kopie een andere waarde gebruikt. Als u een entiteit met de volgende structuren opslaat, u op efficiënte wijze werknemersentiteiten ophalen op basis van e-mailadres of werknemers-id. De voorvoegselwaarden `empid_`voor `email_` `RowKey`, en stellen u in staat om een zoekopdracht op te vragen voor één werknemer of een reeks werknemers, met behulp van een reeks e-mailadressen of werknemers-id's.  

![Afbeelding met werknemerentiteit met verschillende RowKey-waarden][7]

De volgende twee filtercriteria (één op zoek naar werknemers-ID en één op zoek naar een e-mailadres) geven beide puntquery's op:  

* $filter=(PartitionKey eq 'Sales') en (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') en (RowKey eq 'email_jonesj@contoso.com')  

Als u een zoekopdracht opvraagt voor een reeks werknemersentiteiten, u een bereik opgeven dat is gesorteerd in de volgorde van de werknemer-id of een bereik dat is gesorteerd in e-mailadresvolgorde. Query voor entiteiten met het `RowKey`juiste voorvoegsel in het .  

* Gebruik: $filter=(PartitionKey eq 'Sales') en (RowKey ge 'empid_000100') en (RowKey le 'empid_000199') om alle medewerkers in de afdeling Verkoop met een werknemers-ID in het bereik van 000100 tot 000199 te vinden.  
* Om alle medewerkers op de afdeling Verkoop te vinden met een e-mailadres te beginnen met de letter "a", gebruik dan: $filter=(PartitionKey eq 'Sales') en (RowKey ge 'email_a') en (RowKey lt 'email_b')  
  
De filtersyntaxis die in de voorgaande voorbeelden wordt gebruikt, is afkomstig uit de API REST van de tabelopslag. Zie [Queryentiteiten voor](https://msdn.microsoft.com/library/azure/dd179421.aspx)meer informatie .  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Tabelopslag is relatief goedkoop in gebruik, dus de kosten overhead van het opslaan van dubbele gegevens mag niet een grote zorg. U moet echter altijd de kosten van uw ontwerp evalueren op basis van uw verwachte opslagvereisten en alleen dubbele entiteiten toevoegen ter ondersteuning van de query's die uw clienttoepassing uitvoert.  
* Omdat de secundaire indexentiteiten zijn opgeslagen in dezelfde partitie als de oorspronkelijke entiteiten, moet u ervoor zorgen dat u de schaalbaarheidsdoelen voor een afzonderlijke partitie niet overschrijdt.  
* U uw dubbele entiteiten consistent met elkaar houden door EGT's te gebruiken om de twee kopieën van de entiteit atoombij te werken. Dit houdt in dat u alle kopieën van een entiteit in dezelfde partitie moet opslaan. Zie [Entiteitsgroeptransacties gebruiken](#entity-group-transactions)voor meer informatie .  
* De waarde die `RowKey` voor de moet uniek zijn voor elke entiteit. Overweeg samengestelde sleutelwaarden te gebruiken.  
* Opvulling van `RowKey` numerieke waarden in de (bijvoorbeeld de werknemer-id 000223) maakt het correct sorteren en filteren mogelijk op basis van boven- en ondergrenzen.  
* U hoeft niet per se alle eigenschappen van uw entiteit te dupliceren. Als de query's die de entiteiten opzoeken met behulp `RowKey` van het e-mailadres in de leeftijd van de werknemer nooit nodig hebben, kunnen deze entiteiten de volgende structuur hebben:

  ![Afbeelding van de entiteit van de werknemer][8]

* Meestal is het beter om dubbele gegevens op te slaan en ervoor te zorgen dat u alle gegevens ophalen die u nodig hebt met één query, dan om één query te gebruiken om een entiteit te vinden en een andere om de vereiste gegevens op te zoeken.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer:

- Uw clienttoepassing moet entiteiten ophalen met behulp van verschillende sleutels.
- Uw klant moet entiteiten ophalen in verschillende sorteerorders.
- U elke entiteit identificeren met behulp van verschillende unieke waarden.

Zorg er echter voor dat u de schaalbaarheidslimieten voor partities niet overschrijdt wanneer `RowKey` u entiteitsopzoekingen uitvoert met behulp van de verschillende waarden.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Secundair indexpatroon tussen partitie](#inter-partition-secondary-index-pattern)
* [Samengestelde sleutelpatroon](#compound-key-pattern)
* [Entiteitsgroeptransacties](#entity-group-transactions)
* [Werken met heterogene entiteitstypen](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Secundair indexpatroon tussen partitie
Sla meerdere kopieën van `RowKey` elke entiteit op met behulp van verschillende waarden in afzonderlijke partities of in afzonderlijke tabellen. Dit maakt snelle en efficiënte lookups en `RowKey` alternatieve sorteerorders mogelijk met behulp van verschillende waarden.  

#### <a name="context-and-problem"></a>Context en probleem
Tabelopslag indexeert entiteiten automatisch `PartitionKey` met `RowKey` behulp van de en waarden. Hierdoor kan een clienttoepassing een entiteit efficiënt ophalen met behulp van deze waarden. Met de volgende tabelstructuur kan een clienttoepassing bijvoorbeeld een puntquery gebruiken om een afzonderlijke werknemersentiteit `PartitionKey` op `RowKey` te halen met behulp van de afdelingsnaam en de werknemers-id (de en waarden). Een client kan ook entiteiten ophalen die zijn gesorteerd op werknemers-id binnen elke afdeling.  

![Afbeelding van de entiteit van de werknemer][9]

Als u ook een werknemer wilt kunnen vinden op basis van de waarde van een andere eigenschap, zoals e-mailadres, moet u een minder efficiënte partitiescan gebruiken om een overeenkomst te vinden. Dit komt omdat tabelopslag geen secundaire indexen biedt. Bovendien is er geen optie om een lijst van werknemers aan `RowKey` te vragen die in een andere volgorde zijn gesorteerd dan bestelling.  

U anticipeert op een groot aantal transacties ten opzichte van deze entiteiten en wilt het risico minimaliseren dat de opslagsnelheid van de tabel uw client beperkt.  

#### <a name="solution"></a>Oplossing
Als u wilt werken aan het ontbreken van secundaire indexen, kunt `PartitionKey` `RowKey` u meerdere kopieën van elke entiteit opslaan, waarbij elke kopie verschillende en waarden gebruikt. Als u een entiteit met de volgende structuren opslaat, u op efficiënte wijze werknemersentiteiten ophalen op basis van e-mailadres of werknemers-id. De voorvoegselwaarden `empid_`voor `email_` `PartitionKey`, en stellen u in staat om te bepalen welke index u wilt gebruiken voor een query.  

![Afbeelding met werknemerentiteit met primaire index en werknemerentiteit met secundaire index][10]

De volgende twee filtercriteria (één op zoek naar werknemers-ID en één op zoek naar een e-mailadres) geven beide puntquery's op:  

* $filter=(PartitionKey eq 'empid_Sales') en (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') en (RowKey eq 'jonesj@contoso.com')  

Als u een zoekopdracht opvraagt voor een reeks werknemersentiteiten, u een bereik opgeven dat is gesorteerd in de volgorde van de werknemer-id of een bereik dat is gesorteerd in e-mailadresvolgorde. Query voor entiteiten met het `RowKey`juiste voorvoegsel in het .  

* Voor het vinden van alle medewerkers in de afdeling Verkoop met een werknemers-ID in het bereik **000100** tot **000199**, gesorteerd in employee ID order, gebruik: $filter=(PartitionKey eq 'empid_Sales') en (RowKey ge '000100') en (RowKey le '000199')  
* Gebruik: $filter=(PartitionKey eq 'email_Sales') en (RowKey ge 'a') en (RowKey lt 'b') om alle medewerkers op de afdeling Verkoop te vinden met een e-mailadres dat begint met "a", gesorteerd in e-mailadresvolgorde.  

Houd er rekening mee dat de filtersyntaxis die in de voorgaande voorbeelden wordt gebruikt, afkomstig is uit de API REST van de tabelopslag. Zie [Queryentiteiten voor](https://msdn.microsoft.com/library/azure/dd179421.aspx)meer informatie .  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* U uw dubbele entiteiten uiteindelijk consistent met elkaar houden door het [patroon Uiteindelijk consistente transacties](#eventually-consistent-transactions-pattern) te gebruiken om de primaire en secundaire indexentiteiten te behouden.  
* Tabelopslag is relatief goedkoop in gebruik, dus de kosten overhead van het opslaan van dubbele gegevens mag niet een grote zorg. Evalueer echter altijd de kosten van uw ontwerp op basis van uw verwachte opslagvereisten en voeg alleen dubbele entiteiten toe ter ondersteuning van de query's die uw clienttoepassing uitvoert.  
* De waarde die `RowKey` voor de moet uniek zijn voor elke entiteit. Overweeg samengestelde sleutelwaarden te gebruiken.  
* Opvulling van `RowKey` numerieke waarden in de (bijvoorbeeld de werknemer-id 000223) maakt het correct sorteren en filteren mogelijk op basis van boven- en ondergrenzen.  
* U hoeft niet per se alle eigenschappen van uw entiteit te dupliceren. Als de query's die de entiteiten opzoeken met behulp `RowKey` van het e-mailadres in de leeftijd van de werknemer nooit nodig hebben, kunnen deze entiteiten de volgende structuur hebben:
  
  ![Afbeelding met werknemerentiteit met secundaire index][11]
* Meestal is het beter om dubbele gegevens op te slaan en ervoor te zorgen dat u alle gegevens ophalen die u nodig hebt met één query, dan om één query te gebruiken om een entiteit te lokaliseren met behulp van de secundaire index en een andere om de vereiste gegevens in de primaire index op te zoeken.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer:

- Uw clienttoepassing moet entiteiten ophalen met behulp van verschillende sleutels.
- Uw klant moet entiteiten ophalen in verschillende sorteerorders.
- U elke entiteit identificeren met behulp van verschillende unieke waarden.

Gebruik dit patroon wanneer u wilt voorkomen dat de schaalbaarheidslimieten voor partities `RowKey` worden overschreden wanneer u entiteitsopzoekingen uitvoert met behulp van de verschillende waarden.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Uiteindelijk consistent transactiepatroon](#eventually-consistent-transactions-pattern)  
* [Secundair indexpatroon binnen partitie](#intra-partition-secondary-index-pattern)  
* [Samengestelde sleutelpatroon](#compound-key-pattern)  
* [Entiteitsgroeptransacties](#entity-group-transactions)  
* [Werken met heterogene entiteitstypen](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Uiteindelijk consistent transactiepatroon
Maak uiteindelijk consistent gedrag mogelijk over de grenzen van de partitie of de grenzen van het opslagsysteem met Azure-wachtrijen.  

#### <a name="context-and-problem"></a>Context en probleem
EGT's maken atoomtransacties mogelijk tussen meerdere entiteiten die dezelfde partitiesleutel delen. Om prestatie- en schaalbaarheidsredenen u besluiten entiteiten met consistentievereisten op te slaan in afzonderlijke partities of in een afzonderlijk opslagsysteem. In een dergelijk scenario u geen EGT's gebruiken om de consistentie te behouden. U bijvoorbeeld een vereiste hebben om de uiteindelijke consistentie tussen:  

* Entiteiten die zijn opgeslagen in twee verschillende partities in dezelfde tabel, in verschillende tabellen of in verschillende opslagaccounts.  
* Een entiteit die is opgeslagen in tabelopslag en een blob die is opgeslagen in blobopslag.  
* Een entiteit die is opgeslagen in tabelopslag en een bestand in een bestandssysteem.  
* Een entiteit die is opgeslagen in tabelopslag, maar toch wordt geïndexeerd met Azure Cognitive Search.  

#### <a name="solution"></a>Oplossing
Door Azure-wachtrijen te gebruiken, u een oplossing implementeren die uiteindelijke consistentie biedt voor twee of meer partities of opslagsystemen.

Om deze aanpak te illustreren, gaat u ervan uit dat u een vereiste hebt om voormalige werknemersentiteiten te kunnen archiveren. Voormalige werknemersentiteiten worden zelden opgevraagd en moeten worden uitgesloten van activiteiten die betrekking hebben op huidige werknemers. Als u deze vereiste wilt implementeren, slaat u actieve werknemers op in de **tabel Huidige** en voormalige werknemers in de **tabel Archief.** Voor het archiveren van een werknemer moet u de entiteit uit de **tabel Huidige** verwijderen en de entiteit toevoegen aan de **archieftabel.**

Maar u geen EGT gebruiken om deze twee bewerkingen uit te voeren. Om het risico te vermijden dat een entiteit door een fout in beide of geen tabellen wordt weergegeven, moet de archiefbewerking uiteindelijk consistent zijn. In het volgende reeksdiagram worden de stappen in deze bewerking beschreven.  

![Oplossingsdiagram voor uiteindelijke consistentie][12]

Een client initieert de archiefbewerking door een bericht in een Azure-wachtrij te plaatsen (in dit voorbeeld om #456) te archiveren. Een werknemersrol geeft een enquête in de wachtrij voor nieuwe berichten; wanneer het vindt een, het leest het bericht en laat een verborgen kopie op de wachtrij. De werkrol haalt vervolgens een kopie van de entiteit uit de **tabel Huidige** op, voegt een kopie in de **tabel Archief** in en verwijdert vervolgens het origineel uit de **tabel Huidige.** Als er ten slotte geen fouten zijn gemaakt in de vorige stappen, verwijdert de werkrol het verborgen bericht uit de wachtrij.  

In dit voorbeeld voegt stap 4 in het diagram de werknemer in de **tabel Archief** in. Het kan de werknemer toevoegen aan een blob in Blob-opslag of een bestand in een bestandssysteem.  

#### <a name="recover-from-failures"></a>Herstellen van fouten
Het is belangrijk dat de bewerkingen in stap 4-5 in het diagram *idempotent* zijn voor het geval de werkrol de archiefbewerking opnieuw moet starten. Als u tabelopslag gebruikt, moet u voor stap 4 een bewerking 'invoegen of vervangen' gebruiken. voor stap 5 moet u een bewerking 'verwijderen als er bestaat' gebruiken in de clientbibliotheek die u gebruikt. Als u een ander opslagsysteem gebruikt, moet u een geschikte idempotente bewerking gebruiken.  

Als de werkrol stap 6 in het diagram nooit voltooit, wordt het bericht na een time-out opnieuw weergegeven in de wachtrij die klaar is voor de rol van de werknemer om te proberen het opnieuw te verwerken. De rol van de werknemer kan controleren hoe vaak een bericht in de wachtrij is gelezen en, indien nodig, markeren als een "gif" bericht voor onderzoek door het naar een aparte wachtrij te sturen. Zie Berichten ontvangen voor meer informatie over het lezen van wachtrijberichten en het controleren van het aantal [wachtrijen.](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

Sommige fouten uit tabelopslag en wachtrijopslag zijn tijdelijke fouten en uw clienttoepassing moet geschikte logica voor nieuwe apparaten bevatten om deze te verwerken.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Deze oplossing voorziet niet in transactieisolatie. Een client kan bijvoorbeeld de tabellen **Huidige** en **Archief** lezen wanneer de werknemerrol zich tussen stap 4-5 in het diagram bevond en een inconsistente weergave van de gegevens bekijken. De gegevens zullen uiteindelijk consistent zijn.  
* U moet er zeker van zijn dat stap 4-5 idempotent zijn om eventuele consistentie te garanderen.  
* U de oplossing schalen met meerdere wachtrijen en rolinstanties voor werknemers.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u eventuele consistentie wilt garanderen tussen entiteiten die in verschillende partities of tabellen bestaan. U dit patroon uitbreiden om eventuele consistentie te garanderen voor bewerkingen in tabelopslag en Blob-opslag en andere niet-Azure Storage-gegevensbronnen, zoals een database of het bestandssysteem.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Entiteitsgroeptransacties](#entity-group-transactions)  
* [Samenvoegen of vervangen](#merge-or-replace)  

> [!NOTE]
> Als transactieisolatie belangrijk is voor uw oplossing, u overwegen uw tabellen opnieuw te ontwerpen zodat u EGT's gebruiken.  
> 
> 

### <a name="index-entities-pattern"></a>Patroon van indexentiteiten
Indexentiteiten onderhouden om efficiënte zoekopdrachten in te schakelen die lijsten met entiteiten retourneren.  

#### <a name="context-and-problem"></a>Context en probleem
Tabelopslag indexeert entiteiten automatisch `PartitionKey` met `RowKey` behulp van de en waarden. Hierdoor kan een clienttoepassing een entiteit efficiënt ophalen met behulp van een puntquery. Met behulp van de volgende tabelstructuur kan een clienttoepassing bijvoorbeeld een afzonderlijke werknemersentiteit efficiënt `PartitionKey` ophalen `RowKey`met behulp van de afdelingsnaam en de werknemers-ID (de en).  

![Afbeelding van de entiteit van de werknemer][13]

Als u ook een lijst met werknemersentiteiten wilt kunnen ophalen op basis van de waarde van een andere niet-unieke eigenschap, zoals achternaam, moet u een minder efficiënte partitiescan gebruiken. Deze scan vindt overeenkomsten, in plaats van het gebruik van een index om ze direct op te zoeken. Dit komt omdat tabelopslag geen secundaire indexen biedt.  

#### <a name="solution"></a>Oplossing
Als u opzoeken op achternaam met de voorgaande entiteitsstructuur wilt inschakelen, moet u lijsten met werknemers-id's bijhouden. Als u de werknemersentiteiten met een bepaalde achternaam, zoals Jones, wilt ophalen, moet u eerst de lijst met werknemers-id's voor werknemers met Jones als achternaam vinden en vervolgens deze werknemersentiteiten ophalen. Er zijn drie belangrijke opties voor het opslaan van de lijsten met werknemers-geïdentificeerde gegevens:  

* Blob-opslag gebruiken.  
* Maak indexentiteiten in dezelfde partitie als de werknemersentiteiten.  
* Maak indexentiteiten in een afzonderlijke partitie of tabel.  

Optie 1: Blob-opslag gebruiken  

Maak een blob voor elke unieke achternaam en bewaar `PartitionKey` in elke `RowKey` blob een lijst met de waarden (afdelings) en (werknemers-ID) voor werknemers die die achternaam hebben. Wanneer u een werknemer toevoegt of verwijdert, moet u ervoor zorgen dat de inhoud van de relevante blob uiteindelijk consistent is met de werknemersentiteiten.  

Optie 2: Indexentiteiten in dezelfde partitie maken  

Gebruik indexentiteiten die de volgende gegevens opslaan:  

![Afbeelding met werknemerentiteit, met tekenreeks met een lijst met werknemers-iD's met dezelfde achternaam][14]

De `EmployeeIDs` eigenschap bevat een lijst met werknemers-id's voor `RowKey`werknemers met de achternaam die is opgeslagen in de .  

In de volgende stappen wordt het proces beschreven dat u moet volgen wanneer u een nieuwe werknemer toevoegt. In dit voorbeeld voegen we een werknemer met ID 000152 en achternaam Jones toe aan de afdeling Verkoop:  

1. Haal de indexentiteit `PartitionKey` op met een `RowKey` waarde 'Verkoop' en de waarde 'Jones'. Sla de ETag van deze entiteit op om te gebruiken in stap 2.  
2. Maak een entiteitsgroeptransactie (dat wil zeggen een batchbewerking)`PartitionKey` die de `RowKey` nieuwe werknemersentiteit invoegt (waarde 'Verkoop'`PartitionKey` en waarde '000152'), en de indexentiteit bijwerkt (waarde 'Verkoop' en `RowKey` waarde 'Jones'). De EGT doet dit door de nieuwe werknemers-ID toe te voegen aan de lijst in het veld EmployeeID's. Zie [Entiteitsgroepstransacties](#entity-group-transactions)voor meer informatie over EGT's .  
3. Als de EGT mislukt vanwege een optimistische gelijktijdigheidsfout (dat wil zeggen dat iemand anders de indexentiteit heeft gewijzigd), moet u opnieuw beginnen bij stap 1.  

U een vergelijkbare benadering gebruiken om een werknemer te verwijderen als u de tweede optie gebruikt. Het wijzigen van de achternaam van een werknemer is iets complexer, omdat u een EGT moet uitvoeren die drie entiteiten bijwerkt: de entiteit van de werknemer, de indexentiteit voor de oude achternaam en de indexentiteit voor de nieuwe achternaam. U moet elke entiteit ophalen voordat u wijzigingen aanbrengt om de ETag-waarden op te halen die u vervolgens gebruiken om de updates uit te voeren met behulp van optimistische gelijktijdigheid.  

In de volgende stappen wordt het proces beschreven dat u moet volgen wanneer u alle werknemers met een bepaalde achternaam in een afdeling moet opzoeken. In dit voorbeeld zoeken we alle medewerkers met achternaam Jones op in de afdeling Verkoop:  

1. Haal de indexentiteit `PartitionKey` op met een `RowKey` waarde 'Verkoop' en de waarde 'Jones'.  
2. Ontonder de lijst met werknemers-id's in het `EmployeeIDs` veld.  
3. Als u aanvullende informatie nodig hebt over elk van deze werknemers (zoals hun `PartitionKey` e-mailadressen), haalt u elk van de werknemersentiteiten op met behulp van waarde 'Verkoop' en `RowKey` waarden uit de lijst met werknemers die u in stap 2 hebt verkregen.  

Optie 3: Indexentiteiten maken in een afzonderlijke partitie of tabel  

Gebruik voor deze optie indexentiteiten die de volgende gegevens opslaan:  

![Afbeelding met werknemerentiteit, met tekenreeks met een lijst met werknemers-iD's met dezelfde achternaam][15]

De `EmployeeIDs` eigenschap bevat een lijst met werknemers-id's voor `RowKey`werknemers met de achternaam die is opgeslagen in de .  

U EGT's niet gebruiken om de consistentie te behouden, omdat de indexentiteiten zich in een afzonderlijke partitie van de werknemersentiteiten bevinden. Zorg ervoor dat de indexentiteiten uiteindelijk consistent zijn met de werknemersentiteiten.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Deze oplossing vereist ten minste twee query's om overeenkomende entiteiten op `RowKey` te halen: één om de indexentiteiten op te vragen om de lijst met waarden te verkrijgen en vervolgens query's op te vragen om elke entiteit in de lijst op te halen.  
* Omdat een afzonderlijke entiteit een maximale grootte van 1 MB heeft, gaan optie 2 en optie 3 in de oplossing ervan uit dat de lijst met werknemers-id's voor een bepaalde achternaam nooit meer dan 1 MB bedraagt. Als de lijst met werknemers-id's waarschijnlijk meer dan 1 MB groot is, gebruikt u optie 1 en slaat u de indexgegevens op in blob-opslag.  
* Als u optie 2 gebruikt (met BEHULP van EGT's om het toevoegen en verwijderen van werknemers te verwerken en de achternaam van een werknemer te wijzigen), moet u evalueren of het volume van transacties de schaalbaarheidslimieten in een bepaalde partitie zal benaderen. Als dit het geval is, moet u overwegen een uiteindelijk consistente oplossing (optie 1 of optie 3). Deze gebruiken wachtrijen om de updateaanvragen af te handelen en stellen u in staat om uw indexentiteiten op te slaan in een afzonderlijke partitie van de werknemersentiteiten.  
* Optie 2 in deze oplossing gaat ervan uit dat u wilt opzoeken op achternaam binnen een afdeling. U wilt bijvoorbeeld een lijst ophalen met werknemers met een achternaam Jones op de afdeling Verkoop. Als u alle werknemers met een achternaam Jones in de hele organisatie wilt kunnen opzoeken, gebruikt u optie 1 of optie 3.
* U een oplossing op basis van wachtrijen implementeren die uiteindelijkconsistentie oplevert. Zie voor meer informatie het [patroon Uiteindelijk consistente transacties](#eventually-consistent-transactions-pattern).  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u een set entiteiten wilt opzoeken die allemaal een gemeenschappelijke eigendomswaarde hebben, zoals alle werknemers met de achternaam Jones.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Samengestelde sleutelpatroon](#compound-key-pattern)  
* [Uiteindelijk consistent transactiepatroon](#eventually-consistent-transactions-pattern)  
* [Entiteitsgroeptransacties](#entity-group-transactions)  
* [Werken met heterogene entiteitstypen](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Denormalisatiepatroon
Combineer gerelateerde gegevens samen in één entiteit, zodat u alle gegevens ophalen die u nodig hebt met één puntquery.  

#### <a name="context-and-problem"></a>Context en probleem
In een relationele database normaliseert u doorgaans gegevens om duplicatie te verwijderen die optreedt wanneer query's gegevens uit meerdere tabellen ophalen. Als u uw gegevens normaliseert in Azure-tabellen, moet u meerdere retourvluchten van de client naar de server maken om uw gerelateerde gegevens op te halen. Met de volgende tabelstructuur hebt u bijvoorbeeld twee retourvluchten nodig om de details voor een afdeling op te halen. In één reis wordt de afdelingsentiteit opgehaald die de id van de manager bevat, en de tweede reis haalt de gegevens van de manager op in een werknemerentiteit.  

![Afbeelding van afdelingsentiteit en werknemersentiteit][16]

#### <a name="solution"></a>Oplossing
In plaats van de gegevens op te slaan in twee afzonderlijke entiteiten, denormaliseert u de gegevens en bewaart u een kopie van de gegevens van de manager in de afdelingsentiteit. Bijvoorbeeld:  

![Afbeelding van gedenormaliseerde en gecombineerde afdelingsentiteit][17]

Met afdelingsentiteiten die met deze eigenschappen zijn opgeslagen, u nu alle gegevens die u nodig hebt over een afdeling ophalen met behulp van een puntquery.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Er zijn kosten overhead in verband met het opslaan van een aantal gegevens twee keer. Het prestatievoordeel dat voortvloeit uit minder aanvragen voor tabelopslag weegt doorgaans op tegen de marginale stijging van de opslagkosten. Bovendien worden deze kosten gedeeltelijk gecompenseerd door een vermindering van het aantal transacties dat u nodig hebt om de gegevens van een afdeling op te halen.  
* U moet de consistentie behouden van de twee entiteiten die informatie over managers opslaan. U het consistentieprobleem afhandelen door EGT's te gebruiken om meerdere entiteiten in één atoomtransactie bij te werken. In dit geval worden de afdelingsentiteit en de werknemerentiteit voor de afdelingsmanager in dezelfde partitie opgeslagen.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u vaak gerelateerde informatie moet opzoeken. Dit patroon vermindert het aantal query's dat uw client moet maken om de benodigde gegevens op te halen.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Samengestelde sleutelpatroon](#compound-key-pattern)  
* [Entiteitsgroeptransacties](#entity-group-transactions)  
* [Werken met heterogene entiteitstypen](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Samengestelde sleutelpatroon
Gebruik `RowKey` samengestelde waarden om een client in staat te stellen gerelateerde gegevens op te zoeken met één puntquery.  

#### <a name="context-and-problem"></a>Context en probleem
In een relationele database is het natuurlijk om joins in query's te gebruiken om gerelateerde gegevens in één query naar de client terug te sturen. U bijvoorbeeld de werknemers-id gebruiken om een lijst op te zoeken met verwante entiteiten die prestatie- en controlegegevens voor die werknemer bevatten.  

Stel dat u werknemersentiteiten opslaat in tabelopslag met behulp van de volgende structuur:  

![Afbeelding van de entiteit van de werknemer][18]

U moet ook historische gegevens met betrekking tot beoordelingen en prestaties opslaan voor elk jaar dat de werknemer voor uw organisatie heeft gewerkt, en u moet toegang hebben tot deze informatie per jaar. Een optie is het maken van een andere tabel die entiteiten met de volgende structuur opslaat:  

![Afbeelding van de entiteit voor werknemersbeoordeling][19]

Met deze benadering u besluiten bepaalde informatie (zoals voor- en achternaam) in de nieuwe entiteit te dupliceren, zodat u uw gegevens met één verzoek ophalen. U echter geen sterke consistentie behouden omdat u een EGT niet gebruiken om de twee entiteiten atoombij te werken.  

#### <a name="solution"></a>Oplossing
Sla een nieuw entiteitstype op in de oorspronkelijke tabel met behulp van entiteiten met de volgende structuur:  

![Afbeelding van werknemerentiteit met samengestelde sleutel][20]

Merk op `RowKey` hoe het nu een samengestelde sleutel is, bestaande uit de werknemers-ID en het jaar van de controlegegevens. Hiermee u de prestaties van de werknemer ophalen en gegevens controleren met één aanvraag voor één entiteit.  

In het volgende voorbeeld wordt beschreven hoe u alle controlegegevens voor een bepaalde werknemer ophalen (zoals werknemer 000123 op de afdeling Verkoop):  

$filter=(PartitionKey eq 'Sales') en (RowKey ge 'empid_000123') en (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating, Peer Rating, Comments  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* U moet een geschikt scheidingsteken gebruiken waarmee u `RowKey` de waarde eenvoudig ontken: bijvoorbeeld **000123_2012**.  
* U slaat deze entiteit ook op in dezelfde partitie als andere entiteiten die gerelateerde gegevens voor dezelfde werknemer bevatten. Dit betekent dat u EGT's gebruiken om een sterke consistentie te behouden.
* U moet overwegen hoe vaak u de gegevens opvraagt om te bepalen of dit patroon geschikt is. Als u bijvoorbeeld zelden toegang krijgt tot de controlegegevens en de belangrijkste werknemersgegevens vaak, moet u deze als afzonderlijke entiteiten bewaren.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u een of meer verwante entiteiten moet opslaan die u regelmatig opvraagt.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Entiteitsgroeptransacties](#entity-group-transactions)  
* [Werken met heterogene entiteitstypen](#work-with-heterogeneous-entity-types)  
* [Uiteindelijk consistent transactiepatroon](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Het staartpatroon van het logboek
Haal de *n-entiteiten* op die onlangs `RowKey` aan een partitie zijn toegevoegd met behulp van een waarde die wordt sorteert in omgekeerde datum en tijdvolgorde.  

> [!NOTE]
> Queryresultaten die worden geretourneerd door de Azure Table API in Azure Cosmos DB, worden niet gesorteerd op partitiesleutel of rijsleutel. Hoewel dit patroon geschikt is voor tabelopslag, is het dus niet geschikt voor Azure Cosmos DB. Zie [verschillen tussen tabel-API in Azure Cosmos DB en Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)voor een gedetailleerde lijst met functieverschillen.

#### <a name="context-and-problem"></a>Context en probleem
Een veelvoorkomende vereiste is dat de meest recent gemaakte entiteiten kunnen worden opgehaald, bijvoorbeeld de tien meest recente onkostenclaims die door een werknemer zijn ingediend. Tabelquery's `$top` ondersteunen een querybewerking om de eerste *n-entiteiten* uit een set terug te sturen. Er is geen gelijkwaardige querybewerking *n* om de laatste n-entiteiten in een set terug te sturen.  

#### <a name="solution"></a>Oplossing
Sla de entiteiten `RowKey` op met behulp van een die van nature sorteert in omgekeerde datum / tijd volgorde, zodat de meest recente vermelding is altijd de eerste in de tabel.  

Als u bijvoorbeeld de tien meest recente onkostenclaims van een werknemer wilt ophalen, u een omgekeerde tekenwaarde gebruiken die is afgeleid van de huidige datum/tijd. In het volgende c#-codevoorbeeld wordt één manier weergegeven om `RowKey` een geschikte waarde voor omgekeerde teken te maken voor een code die sorteert van het meest recente tot de oudste:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

U teruggaan naar de datum/tijdwaarde met behulp van de volgende code:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

De tabelquery ziet er als volgt uit:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* U moet de omgekeerde tekenwaarde met voorloopnullen plaatsen om ervoor te zorgen dat de tekenreekswaarde sorteert zoals verwacht.  
* U moet zich bewust zijn van de schaalbaarheidsdoelen op het niveau van een partitie. Zorg ervoor dat u geen hot spot partities maakt.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u toegang moet krijgen tot entiteiten in omgekeerde datum/tijdvolgorde of wanneer u toegang moet krijgen tot de meest recent toegevoegde entiteiten.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Antipatroon voorbereiden / toevoegen](#prepend-append-anti-pattern)  
* [Entiteiten ophalen](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Patroon voor het verwijderen van hoge volumes
Schakel het verwijderen van een groot aantal entiteiten in door alle entiteiten op te slaan voor gelijktijdige verwijdering in hun eigen afzonderlijke tabel. U verwijdert de entiteiten door de tabel te verwijderen.  

#### <a name="context-and-problem"></a>Context en probleem
Veel toepassingen verwijderen oude gegevens die niet langer beschikbaar hoeven te zijn voor een clienttoepassing of die de toepassing heeft gearchiveerd naar een ander opslagmedium. U identificeert dergelijke gegevens meestal op een datum. U hebt bijvoorbeeld een vereiste om records te verwijderen van alle aanmeldingsaanvragen die meer dan 60 dagen oud zijn.  

Een mogelijk ontwerp is het gebruik van de datum `RowKey`en het tijdstip van de aanmeldingsaanvraag in de :  

![Afbeelding van de entiteit voor aanmeldingspogingen][21]

Deze aanpak voorkomt partitiehotspots, omdat de toepassing aanmeldingsentiteiten voor elke gebruiker in een afzonderlijke partitie kan invoegen en verwijderen. Deze aanpak kan echter kostbaar en tijdrovend zijn als u een groot aantal entiteiten hebt. Eerst moet u een tabelscan uitvoeren om alle entiteiten te identificeren die u wilt verwijderen en vervolgens moet u elke oude entiteit verwijderen. U het aantal retouren naar de server die nodig is om de oude entiteiten te verwijderen verminderen door meerdere verwijderingsaanvragen in ET's te batcheren.  

#### <a name="solution"></a>Oplossing
Gebruik een aparte tabel voor elke dag van aanmeldingspogingen. U het vorige entiteitsontwerp gebruiken om hotspots te vermijden wanneer u entiteiten invoegt. Het verwijderen van oude entiteiten is nu gewoon een kwestie van elke dag één tabel verwijderen (één opslagbewerking), in plaats van elke dag honderden en duizenden afzonderlijke aanmeldingsentiteiten te vinden en te verwijderen.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Ondersteunt uw ontwerp andere manieren waarop uw toepassing de gegevens gebruikt, zoals het opzoeken van specifieke entiteiten, het koppelen met andere gegevens of het genereren van geaggregeerde informatie?  
* Vermijdt uw ontwerp hotspots wanneer u nieuwe entiteiten invoegt?  
* Verwacht een vertraging als u dezelfde tabelnaam opnieuw wilt gebruiken nadat u deze hebt verwijderd. Het is beter om altijd unieke tafelnamen te gebruiken.  
* Verwacht enige tariefbeperking wanneer u een nieuwe tabel voor het eerst gebruikt, terwijl tabelopslag de toegangspatronen leert en de partities over knooppunten verdeelt. U moet overwegen hoe vaak u nieuwe tabellen moet maken.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u een groot aantal entiteiten hebt die u tegelijkertijd moet verwijderen.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Entiteitsgroeptransacties](#entity-group-transactions)
* [Entiteiten wijzigen](#modify-entities)  

### <a name="data-series-pattern"></a>Patroon van gegevensreeksen
Sla complete gegevensreeksen op in één entiteit om het aantal aanvragen dat u doet te minimaliseren.  

#### <a name="context-and-problem"></a>Context en probleem
Een veelvoorkomend scenario is dat een toepassing een reeks gegevens opslaat die deze doorgaans in één keer moet ophalen. Uw toepassing kan bijvoorbeeld registreren hoeveel chatberichten elke werknemer elk uur verzendt en deze informatie vervolgens gebruiken om uit te leggen hoeveel berichten elke gebruiker in de afgelopen 24 uur heeft verzonden. Een ontwerp kan zijn om 24 entiteiten op te slaan voor elke werknemer:  

![Afbeelding van de entiteit berichtstatistieken][22]

Met dit ontwerp u de entiteit eenvoudig vinden en bijwerken om voor elke werknemer bij te werken wanneer de toepassing de waarde voor het aantal berichten moet bijwerken. Als u echter de informatie wilt ophalen om een grafiek van de activiteit voor de voorgaande 24 uur te plotten, moet u 24 entiteiten ophalen.  

#### <a name="solution"></a>Oplossing
Gebruik het volgende ontwerp, met een aparte eigenschap om het aantal berichten voor elk uur op te slaan:  

![Afbeelding met de entiteit berichtstatistieken met gescheiden eigenschappen][23]

Met dit ontwerp u een samenvoegbewerking gebruiken om het aantal berichten voor een werknemer gedurende een bepaald uur bij te werken. U nu alle informatie ophalen die u nodig hebt om de grafiek te plotten met behulp van een aanvraag voor één entiteit.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Als uw volledige gegevensreeks niet in één entiteit past (een entiteit kan maximaal 252 eigenschappen hebben), gebruikt u een alternatief gegevensarchief zoals een blob.  
* Als u meerdere clients tegelijk een entiteit hebt, gebruikt u de **ETag** om optimistische gelijktijdigheid te implementeren. Als u veel klanten hebt, u een hoge twist ervaren.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u een gegevensreeks moet bijwerken en ophalen die is gekoppeld aan een afzonderlijke entiteit.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Patroon grote entiteiten](#large-entities-pattern)  
* [Samenvoegen of vervangen](#merge-or-replace)  
* [Uiteindelijk consistent transactiepatroon](#eventually-consistent-transactions-pattern) (als u de gegevensreeksen in een blob opslaat)  

### <a name="wide-entities-pattern"></a>Breed entiteitspatroon
Gebruik meerdere fysieke entiteiten om logische entiteiten met meer dan 252 eigenschappen op te slaan.  

#### <a name="context-and-problem"></a>Context en probleem
Een afzonderlijke entiteit kan niet meer dan 252 eigenschappen hebben (met uitzondering van de verplichte systeemeigenschappen) en mag in totaal niet meer dan 1 MB aan gegevens opslaan. In een relationele database werkt u meestal rond eventuele limieten voor de grootte van een rij door een nieuwe tabel toe te voegen en een 1-op-1-relatie tussen deze gegevens af te dwingen.  

#### <a name="solution"></a>Oplossing
Door Tabelopslag te gebruiken, u meerdere entiteiten opslaan om één groot bedrijfsobject met meer dan 252 eigenschappen weer te geven. Als u bijvoorbeeld een telling wilt opslaan van het aantal chatberichten dat elke werknemer de afgelopen 365 dagen heeft verzonden, u het volgende ontwerp gebruiken dat twee entiteiten met verschillende schema's gebruikt:  

![Afbeelding met de entiteit berichtstatistieken met Rowkey 01 en entiteit berichtstatistieken met Rowkey 02][24]

Als u een wijziging moet aanbrengen waarvoor beide entiteiten moeten worden bijgewerkt om ze met elkaar gesynchroniseerd te houden, u een EGT gebruiken. Anders u één samenvoegbewerking gebruiken om het aantal berichten voor een bepaalde dag bij te werken. Als u alle gegevens voor een individuele werknemer wilt ophalen, moet u beide entiteiten ophalen. U dit doen met twee `PartitionKey` efficiënte `RowKey` aanvragen die zowel een als een waarde gebruiken.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Houd rekening met het volgende punt bij de beslissing hoe u dit patroon implementeert:  

* Het ophalen van een volledige logische entiteit omvat ten minste twee opslagtransacties: één om elke fysieke entiteit op te halen.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u entiteiten moet opslaan waarvan de grootte of het aantal eigenschappen de limieten voor een afzonderlijke entiteit overschrijdt in tabelopslag.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Entiteitsgroeptransacties](#entity-group-transactions)
* [Samenvoegen of vervangen](#merge-or-replace)

### <a name="large-entities-pattern"></a>Patroon grote entiteiten
Gebruik Blob-opslag om grote eigenschapswaarden op te slaan.  

#### <a name="context-and-problem"></a>Context en probleem
Een afzonderlijke entiteit mag in totaal niet meer dan 1 MB aan gegevens opslaan. Als een of meer van uw eigenschappen waarden opslaan waardoor de totale grootte van uw entiteit deze waarde overschrijdt, u niet de hele entiteit opslaan in tabelopslag.  

#### <a name="solution"></a>Oplossing
Als uw entiteit meer dan 1 MB groot is omdat een of meer eigenschappen een grote hoeveelheid gegevens bevatten, u gegevens opslaan in Blob-opslag en vervolgens het adres van de blob opslaan in een eigenschap in de entiteit. U bijvoorbeeld de foto van een werknemer opslaan in blobopslag en `Photo` een koppeling naar de foto opslaan in de eigenschap van uw werknemerentiteit:  

![Afbeelding met werknemerentiteit met tekenreeks voor foto die naar Blob-opslag wijst][25]

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Als u de uiteindelijke consistentie wilt behouden tussen de entiteit in tabelopslag en de gegevens in Blob-opslag, gebruikt u het [patroon Uiteindelijk consistente transacties](#eventually-consistent-transactions-pattern) om uw entiteiten te behouden.
* Het ophalen van een volledige entiteit omvat ten minste twee opslagtransacties: één om de entiteit op te halen en één om de blobgegevens op te halen.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u entiteiten moet opslaan waarvan de grootte de limieten voor een afzonderlijke entiteit overschrijdt in tabelopslag.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Uiteindelijk consistent transactiepatroon](#eventually-consistent-transactions-pattern)  
* [Breed entiteitspatroon](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Antipatroon voorbereiden/toevoegen
Wanneer u een hoog volume aan inzetstukken hebt, vergroot u de schaalbaarheid door de wisselplaten over meerdere partities te spreiden.  

#### <a name="context-and-problem"></a>Context en probleem
Het vooraf gebruiken of toevoegen van entiteiten aan uw opgeslagen entiteiten resulteert meestal in de toepassing die nieuwe entiteiten toevoegt aan de eerste of laatste partitie van een reeks partities. In dit geval vinden alle wisselplaten op een bepaald moment plaats in dezelfde partitie, waardoor een hotspot wordt gemaakt. Dit voorkomt dat tabelopslag inserts voor meerdere knooppunten kan inladen en zorgt er mogelijk voor dat uw toepassing de schaalbaarheidsdoelen voor partitie bereikt. Houd bijvoorbeeld rekening met het geval van een toepassing die netwerk- en brontoegang door werknemers registreert. Een entiteitsstructuur zoals de volgende kan ertoe leiden dat de partitie van het huidige uur een hotspot wordt, als het volume van de transacties het schaalbaarheidsdoel voor een afzonderlijke partitie bereikt:  

![Afbeelding van de entiteit van de werknemer][26]

#### <a name="solution"></a>Oplossing
De volgende alternatieve entiteitsstructuur voorkomt een hotspot op een bepaalde partitie, omdat de toepassing gebeurtenissen registreert:  

![Afbeelding met werknemerentiteit met RowKey die de jaar-, maand-, dag-, uur- en gebeurtenis-id maakt][27]

Let met dit voorbeeld `PartitionKey` `RowKey` op hoe zowel de compound-toetsen als samengestelde sleutels zijn. De `PartitionKey` gebruikt zowel de afdeling als de werknemers-ID om de logboekregistratie over meerdere partities te distribueren.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Ondersteunt de alternatieve sleutelstructuur die het maken van hot partities op inserts efficiënt ondersteunt bij de query's die uw clienttoepassing maakt?  
* Betekent uw verwachte volume van transacties dat u waarschijnlijk de schaalbaarheidsdoelen voor een afzonderlijke partitie bereikt en wordt beperkt door tabelopslag?  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Vermijd het antipatroon voor het voorbereiden/toevoegen wanneer uw transactievolume waarschijnlijk zal resulteren in beperking van de snelheid door tabelopslag wanneer u een hete partitie opent.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Samengestelde sleutelpatroon](#compound-key-pattern)  
* [Het staartpatroon van het logboek](#log-tail-pattern)  
* [Entiteiten wijzigen](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Antipatroon van loggegevens
Doorgaans moet u blobopslag gebruiken in plaats van Tabelopslag om logboekgegevens op te slaan.  

#### <a name="context-and-problem"></a>Context en probleem
Een veelgebruikte use case voor loggegevens is het ophalen van een selectie logboekvermeldingen voor een specifiek datum-/tijdbereik. U wilt bijvoorbeeld alle fout- en kritieke berichten vinden die uw toepassing tussen 15:04 en 15:06 op een bepaalde datum heeft geregistreerd. U wilt de datum en tijd van het logboekbericht niet gebruiken om de partitie te bepalen waarop u logboekentiteiten opslaat. Dat resulteert in een hot partitie omdat op een bepaald moment `PartitionKey` alle logentiteiten dezelfde waarde delen (zie het [antipatroon prepend/toevoegen).](#prepend-append-anti-pattern) Het volgende entiteitsschema voor een logboekbericht resulteert bijvoorbeeld in een hot partitie, omdat de toepassing alle logboekberichten naar de partitie schrijft voor de huidige datum en het huidige uur:  

![Afbeelding van de entiteit logboekbericht][28]

In dit voorbeeld `RowKey` bevat het de datum en tijd van het logboekbericht om ervoor te zorgen dat logboekberichten worden gesorteerd in datum/tijdvolgorde. Het `RowKey` bevat ook een bericht-id, voor het geval meerdere logboekberichten dezelfde datum en tijd delen.  

Een andere benadering `PartitionKey` is het gebruik van een die ervoor zorgt dat de toepassing berichten schrijft over een reeks partities. Als de bron van het logboekbericht bijvoorbeeld een manier biedt om berichten over veel partities te distribueren, u het volgende entiteitsschema gebruiken:  

![Afbeelding van de entiteit logboekbericht][29]

Het probleem met dit schema is echter dat u elke partitie in de tabel moet doorzoeken om alle logboekberichten voor een bepaalde tijdspanne op te halen.

#### <a name="solution"></a>Oplossing
In het vorige gedeelte werd gewezen op het probleem van het gebruik van tabelopslag om logboekvermeldingen op te slaan en werden twee onbevredigende ontwerpen voorgesteld. Een oplossing leidde tot een hete partitie met het risico van slechte prestaties schrijven log berichten. De andere oplossing resulteerde in slechte queryprestaties, vanwege de vereiste om elke partitie in de tabel te scannen om logboekberichten op te halen voor een specifieke tijdspanne. Blob-opslag biedt een betere oplossing voor dit type scenario en zo worden azure storage-analyses de logboekgegevens opgeslagen die worden verzamelt.  

In dit gedeelte wordt beschreven hoe opslaganalyses logboekgegevens opslaan in Blob-opslag, als illustratie van deze benadering voor het opslaan van gegevens die u doorgaans per bereik opvraagt.  

Opslaganalyses slaan logboekberichten op in een afgebakende indeling in meerdere blobs. De afgebakende indeling maakt het gemakkelijk voor een clienttoepassing om de gegevens in het logboekbericht te ontindirecten.  

Storage analytics maakt gebruik van een naamgevingsconventie voor blobs waarmee u de blob (of blobs) vinden die de logboekberichten bevatten waarvoor u zoekt. Een blob met de naam 'wachtrij/2014/07/31/1800/000001.log' bevat bijvoorbeeld logboekberichten die betrekking hebben op de wachtrijservice voor het uur dat begint om 18:00 uur op 31 juli 2014. De "000001" geeft aan dat dit het eerste logboekbestand voor deze periode is. Opslaganalyses registreren ook de tijdstempels van de eerste en laatste logboekberichten die in het bestand zijn opgeslagen, als onderdeel van de metagegevens van de blob. Met de API voor Blob-opslag u blobs in een container vinden op basis van een naamvoorvoegsel. Als u alle blobs wilt vinden die wachtrijlogboekgegevens bevatten voor het uur dat om 18:00 uur begint, u het voorvoegsel queue/2014/07/31/1800 gebruiken.  

Opslaganalysebuffers registreren berichten intern en werken vervolgens periodiek de juiste blob bij of maken een nieuwe met de nieuwste batch logboekvermeldingen. Dit vermindert het aantal schrijfbewerkingen dat moet worden uitgevoerd naar Blob-opslag.  

Als u een vergelijkbare oplossing implementeert in uw eigen toepassing, bedenk dan hoe u de afweging tussen betrouwbaarheid en kosten en schaalbaarheid beheren. Met andere woorden, evalueer het effect van het schrijven van elke logboekvermelding in Blob-opslag als het gebeurt, in vergelijking met het bufferen van updates in uw toepassing en het schrijven ervan naar Blob-opslag in batches.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Houd rekening met de volgende punten bij het bepalen hoe logboekgegevens moeten worden opgeslagen:  

* Als u een tabelontwerp maakt dat potentiële hot partities vermijdt, u merken dat u uw logboekgegevens niet efficiënt openen.  
* Om logboekgegevens te verwerken, moet een client vaak veel records laden.  
* Hoewel logboekgegevens vaak gestructureerd zijn, is Blob-opslag mogelijk een betere oplossing.  

### <a name="implementation-considerations"></a>Afwegingen bij de implementatie
In dit gedeelte worden enkele overwegingen besproken om rekening mee te houden wanneer u de in de vorige secties beschreven patronen implementeert. De meeste van deze sectie maakt gebruik van voorbeelden geschreven in C# die gebruik maken van de Storage Client Library (versie 4.3.0 op het moment van schrijven).  

### <a name="retrieve-entities"></a>Entiteiten ophalen
Zoals besproken in de sectie [Ontwerp voor query's,](#design-for-querying)is de meest efficiënte query een puntquery. In sommige scenario's moet u echter mogelijk meerdere entiteiten ophalen. In deze sectie worden enkele veelvoorkomende benaderingen beschreven voor het ophalen van entiteiten met behulp van de opslagclientbibliotheek.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Een puntquery uitvoeren met de opslagclientbibliotheek
De eenvoudigste manier om een puntquery uit te voeren, is door de bewerking **Tabel ophalen** te gebruiken. Zoals in het volgende C#-codefragment wordt weergegeven, `PartitionKey` wordt met deze `RowKey` bewerking een entiteit met een waarde "Verkoop" en een waarde "212" opgehaald:  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Merk op hoe dit voorbeeld verwacht dat `EmployeeEntity`de entiteit die het ophaalt van het type is.  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Meerdere entiteiten ophalen met LINQ
U meerdere entiteiten ophalen door LINQ met opslagclientbibliotheek **where** te gebruiken en een query op te geven met een waar-clausule. Om een tabelscan te voorkomen, `PartitionKey` moet u altijd de waarde `RowKey` opnemen in de waar-clausule en, indien mogelijk, de waarde om tabel- en partitiescans te vermijden. Tabelopslag ondersteunt een beperkte reeks vergelijkingsoperatoren (groter dan, groter dan of gelijk, minder dan, kleiner dan of gelijk, gelijk en niet gelijk) om te gebruiken in de waar-clausule. In het volgende C#-codefragment worden alle werknemers gevonden waarvan `RowKey` de achternaam begint met 'B' `PartitionKey` (ervan uitgaande dat de achternaam wordt opgeslagen) in de afdeling Verkoop (uitgaande van de winkelnaam):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Merk op hoe de `RowKey` query `PartitionKey` zowel a als a opgeeft om betere prestaties te garanderen.  

Het volgende voorbeeld van code toont gelijkwaardige functionaliteit met behulp van de vloeiende API (voor meer informatie over vloeiende API's in het algemeen, zie [Aanbevolen procedures voor het ontwerpen van een vloeiende API):](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)  

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
> Het monster genest meerdere `CombineFilters` methoden om de drie filteromstandigheden op te nemen.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Grote aantallen entiteiten ophalen uit een query
Een optimale query retourneert `PartitionKey` een afzonderlijke `RowKey` entiteit op basis van een waarde en een waarde. In sommige scenario's u echter een vereiste hebben om veel entiteiten uit dezelfde partitie of zelfs van veel partities terug te sturen. U moet de prestaties van uw toepassing altijd volledig testen in dergelijke scenario's.  

Een query tegen tabelopslag kan maximaal 1.000 entiteiten tegelijk retourneren en maximaal vijf seconden uitvoeren. Tabelopslag retourneert een vervolgtoken om de clienttoepassing in staat te stellen de volgende set entiteiten aan te vragen, als een van de volgende entiteiten waar is:

- De resultatenset bevat meer dan 1.000 entiteiten.
- De query is niet binnen vijf seconden voltooid.
- De query overschrijdt de partitiegrens. 

Zie [Time-out en paginatie](https://msdn.microsoft.com/library/azure/dd135718.aspx)van query's voor meer informatie over hoe vervolgtokens werken.  

Als u de opslagclientbibliotheek gebruikt, kan deze automatisch vervolgtokens voor u verwerken omdat deze entiteiten uit tabelopslag retourneert. In het volgende c#-codevoorbeeld worden bijvoorbeeld automatisch vervolgtokens verwerkt als tabelopslag deze in een antwoord retourneert:  

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

De volgende C#-code verwerkt expliciet vervolgtokens:  

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

Door vervolgtokens expliciet te gebruiken, u bepalen wanneer uw toepassing het volgende segment van gegevens ophaalt. Als uw clienttoepassing gebruikers bijvoorbeeld in staat stelt om de entiteiten te bekijken die in een tabel zijn opgeslagen, kan een gebruiker besluiten om niet alle entiteiten te bekijken die door de query zijn opgehaald. Uw toepassing gebruikt alleen een vervolgtoken om het volgende segment op te halen wanneer de gebruiker klaar was met paging door alle entiteiten in het huidige segment. Deze aanpak heeft verschillende voordelen:  

* U de hoeveelheid gegevens die u uit tabelopslag moet ophalen en die u over het netwerk verplaatst, beperken.  
* U asynchrone I/O uitvoeren in .NET.  
* U het vervolgtoken serialiseren naar permanente opslag, zodat u doorgaan in het geval van een crash van een toepassing.  

> [!NOTE]
> Een vervolgtoken retourneert doorgaans een segment met 1.000 entiteiten, hoewel het er minder kan bevatten. Dit is ook het geval als u het aantal items dat een query retourneert, beperkt met **Take** om de eerste n-entiteiten terug te sturen die overeenkomen met uw opzoekcriteria. Tabelopslag kan een segment retourneren dat minder dan n entiteiten bevat, samen met een vervolgtoken om u in staat te stellen de resterende entiteiten op te halen.  
> 
> 

In de volgende C#-code ziet u hoe u het aantal entiteiten dat binnen een segment is geretourneerd, wijzigt:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projectie aan de serverzijde
Eén entiteit kan maximaal 255 eigenschappen hebben en maximaal 1 MB groot zijn. Wanneer u de tabel opvraagt en entiteiten ophaalt, hebt u mogelijk niet alle eigenschappen nodig en u voorkomen dat gegevens onnodig worden overgedragen (om de latentie en kosten te verminderen). U server-side projectie gebruiken om alleen de eigenschappen over te dragen die u nodig hebt. In het volgende voorbeeld `Email` wordt alleen `PartitionKey` `RowKey`de `Timestamp`eigenschap `ETag`(samen met , , en ) opgehaald uit de entiteiten die door de query zijn geselecteerd.  

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

Merk op `RowKey` hoe de waarde beschikbaar is, ook al is deze niet opgenomen in de lijst met eigenschappen die u wilt ophalen.  

### <a name="modify-entities"></a>Entiteiten wijzigen
Met de opslagclientbibliotheek u uw entiteiten wijzigen die zijn opgeslagen in tabelopslag door entiteiten in te voegen, te verwijderen en bij te werken. U EGT's gebruiken om meerdere inserts te batchn, bewerkingen samen bij te werken en te verwijderen, om het aantal vereiste retourvluchten te verminderen en de prestaties van uw oplossing te verbeteren.  

Uitzonderingen die worden gegenereerd wanneer een EGT in de opslagclientbibliotheek wordt uitgevoerd, bevatten doorgaans de index van de entiteit waardoor de batch is mislukt. Dit is handig wanneer u code debugging die EGTs gebruikt.  

U moet ook overwegen hoe uw ontwerp van invloed is op de manier waarop uw clienttoepassing omgaat met gelijktijdigheid en updatebewerkingen.  

#### <a name="managing-concurrency"></a>Gelijktijdigheid beheren
Tabelopslag implementeert standaard optimistische gelijktijdigheidscontroles op het niveau van afzonderlijke entiteiten voor het invoegen, samenvoegen en verwijderen van bewerkingen, hoewel het mogelijk is voor een client om tabelopslag te dwingen deze controles te omzeilen. Zie [Gelijktijdigheid beheren in Microsoft Azure Storage](../storage/common/storage-concurrency.md)voor meer informatie.  

#### <a name="merge-or-replace"></a>Samenvoegen of vervangen
De `Replace` methode `TableOperation` van de klasse vervangt altijd de volledige entiteit in tabelopslag. Als u een eigenschap niet opneemt in de aanvraag wanneer die eigenschap in de opgeslagen entiteit bestaat, wordt die eigenschap met de aanvraag verwijderd uit de opgeslagen entiteit. Tenzij u een eigenschap expliciet uit een opgeslagen entiteit wilt verwijderen, moet u elke eigenschap in het verzoek opnemen.  

U `Merge` de methode `TableOperation` van de klasse gebruiken om de hoeveelheid gegevens die u naar tabelopslag verzendt, te verminderen wanneer u een entiteit wilt bijwerken. De `Merge` methode vervangt alle eigenschappen in de opgeslagen entiteit door eigenschapswaarden van de entiteit die in de aanvraag is opgenomen. Met deze methode blijven alle eigenschappen in de opgeslagen entiteit intact die niet in de aanvraag zijn opgenomen. Dit is handig als u grote entiteiten hebt en slechts een klein aantal eigenschappen in een aanvraag hoeft bij te werken.  

> [!NOTE]
> De `*Replace` `Merge` methoden en methoden mislukken als de entiteit niet bestaat. Als alternatief u `InsertOrReplace` de `InsertOrMerge` methoden en methoden gebruiken die een nieuwe entiteit maken als deze niet bestaat.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Werken met heterogene entiteitstypen
Tabelopslag is een *tabelarchief zonder schema.* Dat betekent dat één tafel entiteiten van meerdere typen kan opslaan, wat een grote flexibiliteit biedt in uw ontwerp. In het volgende voorbeeld wordt een tabel geïllustreerd die zowel werknemers- als afdelingsentiteiten opslaat:  

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

Elke entiteit moet `PartitionKey` `RowKey`nog `Timestamp` steeds , en waarden hebben, maar kan elke set eigenschappen hebben. Bovendien is er niets dat het type entiteit aangeeft, tenzij u ervoor kiest om die informatie ergens op te slaan. Er zijn twee opties voor het identificeren van het entiteitstype:  

* Het entiteitstype voorbereiden `RowKey` op de `PartitionKey`(of eventueel de). Bijvoorbeeld, `EMPLOYEE_000123` of `DEPARTMENT_SALES` `RowKey` als waarden.  
* Gebruik een afzonderlijke eigenschap om het entiteitstype vast te leggen, zoals in de volgende tabel wordt weergegeven.  

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

De eerste optie, die afhankelijk `RowKey`is van het entiteitstype naar de , is handig als er een mogelijkheid bestaat dat twee entiteiten van verschillende typen dezelfde sleutelwaarde hebben. Het groepeert ook entiteiten van hetzelfde type samen in de partitie.  

De technieken die in deze sectie worden besproken zijn vooral relevant voor de discussie over[successierelaties.](#inheritance-relationships)  

> [!NOTE]
> Overweeg een versienummer op te nemen in de waarde van het entiteitstype, zodat clienttoepassingen POCO-objecten kunnen ontwikkelen en met verschillende versies kunnen werken.  
> 
> 

In de rest van deze sectie worden enkele functies in de opslagclientbibliotheek beschreven die het werken met meerdere entiteitstypen in dezelfde tabel vergemakkelijken.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Heterogene entiteitstypen ophalen
Als u de opslagclientbibliotheek gebruikt, hebt u drie opties voor het werken met meerdere entiteitstypen.  

Als u het type entiteit kent `RowKey` `PartitionKey` dat is opgeslagen met specifieke en waarden, u het entiteitstype opgeven wanneer u de entiteit ophaalt. U hebt dit gezien in de vorige twee `EmployeeEntity`voorbeelden die entiteiten van het type ophalen: [Voer een puntquery uit met de opslagclientbibliotheek](#run-a-point-query-by-using-the-storage-client-library) en [haal meerdere entiteiten op met LINQ](#retrieve-multiple-entities-by-using-linq).  

De tweede optie is `DynamicTableEntity` het type (een eigenschapzak) te gebruiken in plaats van een concreet POCO-entiteitstype. Deze optie kan ook de prestaties verbeteren, omdat het niet nodig is om de entiteit te serialiseren en te deserialiseren naar .NET-typen. Met de volgende C#-code worden mogelijk meerdere entiteiten van verschillende `DynamicTableEntity` typen uit de tabel opgehaald, maar worden alle entiteiten als instanties geretourneerd. Vervolgens wordt `EntityType` de eigenschap gebruikt om het type van elke entiteit te bepalen:  

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

Als u andere eigenschappen wilt `TryGetValue` ophalen, `Properties` moet `DynamicTableEntity` u de methode op de eigenschap van de klasse gebruiken.  

Een derde optie is `DynamicTableEntity` om te `EntityResolver` combineren met behulp van het type en een instantie. Hiermee u meerdere POCO-typen in dezelfde query oplossen. In dit voorbeeld `EntityResolver` gebruikt de `EntityType` gemachtigde de eigenschap om onderscheid te maken tussen de twee typen entiteiten die de query retourneert. De `Resolve` methode `resolver` gebruikt de `DynamicTableEntity` gemachtigde `TableEntity` om instanties op te lossen in instanties.  

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

#### <a name="modify-heterogeneous-entity-types"></a>Heterogene entiteitstypen wijzigen
U hoeft niet te weten welk type entiteit deze moet verwijderen en u weet altijd welk type entiteit deze invoegt wanneer u deze invoegt. U het `DynamicTableEntity` type echter gebruiken om een entiteit bij te werken zonder het type te kennen en zonder een POCO-entiteitsklasse te gebruiken. In het volgende codevoorbeeld wordt één entiteit `EmployeeCount` opgehaald en wordt gecontroleerd of de eigenschap bestaat voordat deze wordt bijgewerkt.  

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

### <a name="control-access-with-shared-access-signatures"></a>Toegang beheren met handtekeningen voor gedeelde toegang
U SAS-tokens (shared access signature) gebruiken om clienttoepassingen in staat te stellen entiteiten met een tabel rechtstreeks te wijzigen (en op te vragen), zonder dat u zich rechtstreeks hoeft te verifiëren met tabelopslag. Meestal zijn er drie belangrijke voordelen aan het gebruik van SAS in uw toepassing:  

* U hoeft uw opslagaccountsleutel niet te distribueren naar een onveilig platform (zoals een mobiel apparaat) om dat apparaat toegang te geven tot en entiteiten in tabelopslag te wijzigen.  
* U een deel van het werk dat web- en werknemersrollen uitvoeren bij het beheren van uw entiteiten, ontladen. U offloaden naar clientapparaten zoals computers van eindgebruikers en mobiele apparaten.  
* U een beperkte en tijdelijke set machtigingen toewijzen aan een client (zoals alleen-lezen toegang geven tot specifieke bronnen).  

Zie [Gedeelde toegangshandtekeningen (SAS) gebruiken](../storage/common/storage-dotnet-shared-access-signature-part-1.md)voor meer informatie over het gebruik van SAS-tokens met tabelopslag.  

U moet echter nog steeds de SAS-tokens genereren die een clienttoepassing verlenen aan de entiteiten in tabelopslag. Doe dit in een omgeving die veilige toegang heeft tot uw opslagaccountsleutels. Doorgaans gebruikt u een web- of werknemersrol om de SAS-tokens te genereren en deze te leveren aan de clienttoepassingen die toegang tot uw entiteiten nodig hebben. Omdat er nog steeds een overhead betrokken is bij het genereren en leveren van SAS-tokens aan klanten, moet u overwegen hoe u deze overhead het beste verminderen, vooral in scenario's met een hoog volume.  

Het is mogelijk om een SAS-token te genereren dat toegang verleent tot een subset van de entiteiten in een tabel. Standaard maakt u een SAS-token voor een hele tabel. Maar het is ook mogelijk om aan te geven dat `PartitionKey` het SAS-token `PartitionKey` `RowKey` toegang verleent tot een reeks waarden of een reeks waarden en waarden. U ervoor kiezen om SAS-tokens te genereren voor individuele gebruikers van uw systeem, zodat het SAS-token van elke gebruiker hen alleen toegang geeft tot hun eigen entiteiten in tabelopslag.  

### <a name="asynchronous-and-parallel-operations"></a>Asynchrone en parallelle bewerkingen
Op voorwaarde dat u uw aanvragen over meerdere partities verspreidt, u de doorvoer en de responsiviteit van de client verbeteren door asynchrone of parallelle query's te gebruiken.
U hebt bijvoorbeeld twee of meer exemplaren van de werkrol die toegang hebben tot uw tabellen parallel. U afzonderlijke werknemersrollen hebben die verantwoordelijk zijn voor bepaalde sets partities, of gewoon meerdere functie-exemplaren van werknemers hebben, die elk toegang hebben tot alle partities in een tabel.  

Binnen een clientinstantie u de doorvoer verbeteren door opslagbewerkingen asynchroon uit te voeren. De opslagclientbibliotheek maakt het eenvoudig om asynchrone query's en wijzigingen te schrijven. U bijvoorbeeld beginnen met de synchrone methode waarmee alle entiteiten in een partitie worden opgehaald, zoals wordt weergegeven in de volgende C#-code:  

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

U deze code eenvoudig zo wijzigen dat de query als volgt wordt uitgevoerd:  

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

In dit asynchrone voorbeeld ziet u de volgende wijzigingen van de synchrone versie:  

* De methodehandtekening bevat `async` nu de modifier en retourneert een `Task` instantie.  
* In plaats `ExecuteSegmented` van de methode aan te `ExecuteSegmentedAsync` roepen om resultaten op te halen, roept de methode nu de methode aan. De methode `await` gebruikt de modifier om de resultaten asynchroon op te halen.  

De clienttoepassing kan deze methode meerdere keren `department` aanroepen, met verschillende waarden voor de parameter. Elke query wordt uitgevoerd op een aparte thread.  

Er is geen asynchrone versie van de `Execute` methode in de `TableQuery` klasse, omdat de `IEnumerable` interface geen asynchrone opsomming ondersteunt.  

U entiteiten ook asynchroon invoegen, bijwerken en verwijderen. In het volgende c#-voorbeeld wordt een eenvoudige, synchrone methode weergegeven om een werknemerentiteit in te voegen of te vervangen:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

U deze code eenvoudig zo wijzigen dat de update als volgt wordt uitgevoerd:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

In dit asynchrone voorbeeld ziet u de volgende wijzigingen van de synchrone versie:  

* De methodehandtekening bevat `async` nu de modifier en retourneert een `Task` instantie.  
* In plaats `Execute` van de methode aan te roepen `ExecuteAsync` om de entiteit bij te werken, roept de methode nu de methode aan. De methode `await` gebruikt de modifier om de resultaten asynchroon op te halen.  

De clienttoepassing kan meerdere asynchrone methoden zoals deze aanroepen en elke methodeaanroep wordt uitgevoerd op een afzonderlijke thread.  


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

