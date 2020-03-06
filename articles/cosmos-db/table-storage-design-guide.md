---
title: Azure Cosmos DB tabellen ontwerpen voor schalen en prestaties
description: 'Ontwerp handleiding voor Azure Table Storage: schaal bare en uitvoerende tabellen in Azure Cosmos DB en Azure-tabel opslag'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: sakash279
ms.author: akshanka
ms.custom: seodec18
ms.openlocfilehash: 166076d366cbbf7bef24648772beaba9b3a88253
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395646"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Ontwerp handleiding voor de Azure Table Storage-tabel: schaal bare en uitvoerende tabellen

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Als u schaalbare en beter bruikbare tabellen wilt ontwerpen, moet u rekening houden met verschillende factoren, waaronder kosten. Als u eerder schema's hebt ontworpen voor relationele databases, zullen deze aandachtspunten u bekend voorkomen. Hoewel er een aantal overeenkomsten zijn tussen Azure Table-opslag en relationele modellen, zijn er ook veel belangrijke verschillen. Deze verschillen leiden doorgaans tot verschillende ontwerpen, die misschien vreemd aandoen voor iemand die bekend is met relationele databases. De ontwerpen zijn echter zeer geschikt als u ontwerpt voor een archief op basis van NoSQL-sleutels/waarden, zoals Table-opslag.

Table Storage is ontworpen ter ondersteuning van toepassingen in de cloud die miljarden entiteiten (' rijen ' in relationele data base-terminologie) van gegevens kunnen bevatten, of voor gegevens sets die hoogwaardige volumes moeten ondersteunen. Daarom moet u nadenken over de manier waarop u uw gegevens opslaat, en begrijpen hoe tabel opslag werkt. Met een goed ontworpen NoSQL-gegevens archief kan uw oplossing veel verder worden geschaald (en tegen lagere kosten) dan een oplossing die gebruikmaakt van een relationele data base. Deze handleiding helpt u bij deze onderwerpen.  

## <a name="about-azure-table-storage"></a>Over Azure Table Storage
In deze sectie worden enkele van de belangrijkste functies van tabel opslag uitgelegd die vooral relevant zijn voor het ontwerpen van prestaties en schaal baarheid. Als u niet bekend bent met Azure Storage en tabel opslag, raadpleegt u [Inleiding tot Microsoft Azure Storage](../storage/common/storage-introduction.md) en aan de [slag met Azure Table Storage met behulp van .net voordat u](table-storage-how-to-use-dotnet.md) de rest van dit artikel leest. Hoewel deze hand leiding zich in de tabel opslag bevindt, bevat deze een discussie van Azure Queue Storage en Azure Blob-opslag, en hoe u deze kunt gebruiken samen met tabel opslag in een oplossing.  

Tabel opslag maakt gebruik van een tabel indeling om gegevens op te slaan. Staat elke rij van de tabel voor een entiteit in de standard-terminologie en de kolommen opslaan van de verschillende eigenschappen van die entiteit. Elke entiteit heeft een paar sleutels om deze uniek te identificeren en een time stamp-kolom die wordt gebruikt om bij te houden wanneer de entiteit voor het laatst is bijgewerkt. Het tijds tempel veld wordt automatisch toegevoegd en u kunt de tijds tempel niet hand matig overschrijven met een wille keurige waarde. De tabel opslag maakt gebruik van deze laatst gewijzigde tijds tempel (LMT) om optimistische gelijktijdigheid te beheren.  

> [!NOTE]
> REST API bewerkingen voor tabel opslag retour neren ook een `ETag` waarde die wordt afgeleid van de LMT. In dit document worden de termen ETag en LMT door elkaar gebruikt, omdat ze verwijzen naar dezelfde onderliggende gegevens.  
> 
> 

Het volgende voorbeeld ziet het ontwerp van een eenvoudige tabel om op te slaan en de werknemers en afdelingen entiteiten. Veel van de voorbeelden verderop in deze handleiding zijn gebaseerd op dit eenvoudige ontwerp.  

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
<td>jun</td>
<td>CaO</td>
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


Tot nu toe lijkt dit ontwerp op een tabel in een relationele data base. De belangrijkste verschillen zijn de verplichte kolommen en de mogelijkheid om meerdere entiteits typen op te slaan in dezelfde tabel. Daarnaast heeft elk van de door de gebruiker gedefinieerde eigenschappen, zoals voor **naam** of **leeftijd**, een gegevens type, zoals geheel getal of teken reeks, net als een kolom in een relationele data base. In tegens telling tot een relationele data base betekent het schema niet-minder aard van tabel opslag dat een eigenschap niet hetzelfde gegevens type hoeft te hebben voor elke entiteit. Voor het opslaan van complexe gegevenstypen in één eigenschap, moet u een geserialiseerde indeling zoals JSON of XML. Zie voor meer informatie wat is de [Table Storage-gegevens model](https://msdn.microsoft.com/library/azure/dd179338.aspx).

De keuze van `PartitionKey` en `RowKey` is fundamenteel voor een goed ontwerp van de tabel. Elke entiteit die is opgeslagen in een tabel moet een unieke combi natie van `PartitionKey` en `RowKey`hebben. Net als bij sleutels in een relationele-database tabel worden de waarden `PartitionKey` en `RowKey` geïndexeerd om een geclusterde index te maken die snelle weer gave-ups mogelijk maakt. In tabel opslag worden echter geen secundaire indexen gemaakt. Dit zijn dus de enige twee geïndexeerde eigenschappen (sommige van de patronen die verderop worden beschreven, laten zien hoe u deze zicht bare beperking kunt omzeilen).  

Een tabel bestaat uit een of meer partities, en veel van de ontwerp beslissingen die u aanbrengt, zijn een geschikte `PartitionKey` en `RowKey` om uw oplossing te optimaliseren. Een oplossing kan bestaan uit slechts één tabel die al uw entiteiten bevat, ingedeeld in partities, maar een oplossing heeft meestal meerdere tabellen. Met tabellen kunt u uw entiteiten logisch indelen en kunt u de toegang tot de gegevens beheren met behulp van toegangs beheer lijsten. U kunt een hele tabel verwijderen door één opslag bewerking te gebruiken.  

### <a name="table-partitions"></a>Tabelpartities
De account naam, tabel naam en `PartitionKey` samen identificeren de partitie in de opslag service waar tabel opslag de entiteit opslaat. En als onderdeel van het adresserings schema voor entiteiten definiëren partities een bereik voor trans acties (Zie de sectie verderop in dit artikel, [trans acties van entiteits groepen](#entity-group-transactions)) en vormen de basis van hoe tabel opslag wordt geschaald. Zie [prestaties en schaal baarheid controle lijst voor tabel opslag](../storage/tables/storage-performance-checklist.md)voor meer informatie over tabel partities.  

In tabel opslag, een afzonderlijk knoop punt Services, een of meer volledige partities, en de service wordt geschaald door dynamische taak verdeling van partities tussen knoop punten. Als een knoop punt wordt geladen, kan de tabel opslag het bereik van partities dat door dat knoop punt wordt verwerkt, splitsen op verschillende knoop punten. Wanneer er verkeer wordt afgedeeld, kan de partitielay-out van de partitie reeksen van de Stille knoop punten weer worden samengevoegd op één knoop punt.  

Zie [Microsoft Azure Storage: een Maxi maal beschik bare service voor Cloud opslag met sterke consistentie](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)voor meer informatie over de interne Details van tabel opslag, en met name hoe het beheert van partities.  

### <a name="entity-group-transactions"></a>Trans acties van entiteits groep
In tabel opslag zijn EGTs (entity Group trans Actions) het enige ingebouwde mechanisme voor het uitvoeren van atomische updates op meerdere entiteiten. EGTs worden ook wel batch- *trans acties*genoemd. EGTs kan alleen worden uitgevoerd op entiteiten die zijn opgeslagen in dezelfde partitie (het delen van dezelfde partitie sleutel in een bepaalde tabel), dus wanneer u een Atomic-transactioneel gedrag voor meerdere entiteiten nodig hebt, moet u ervoor zorgen dat deze entiteiten zich in dezelfde partitie bevinden. Dit is vaak een reden om meerdere entiteits typen in dezelfde tabel (en partitie) te bewaren en niet meerdere tabellen te gebruiken voor verschillende entiteits typen. Een enkele EGT kan worden uitgevoerd op maximaal 100 entiteiten.  Als u meerdere gelijktijdige EGTs voor verwerking verzendt, is het belang rijk om ervoor te zorgen dat deze EGTs niet worden uitgevoerd op entiteiten die gemeen schappelijk zijn voor EGTs. Anders wordt de verwerking van Risico's vertraagd.

Met EGTs kunt u ook een mogelijke trans actie in uw ontwerp introduceren. Door meer partities te gebruiken, wordt de schaal baarheid van uw toepassing verhoogd, omdat Azure meer mogelijkheden heeft voor taak verdelings aanvragen op verschillende knoop punten. Maar dit kan de mogelijkheid van uw toepassing om atomische trans acties uit te voeren, beperken en een sterke consistentie voor uw gegevens garanderen. Daarnaast zijn er specifieke schaalbaarheids doelen op het niveau van een partitie die de door Voer van trans acties die u voor één knoop punt kan verwachten, kunnen beperken.

Zie [schaalbaarheids doelen voor standaard opslag accounts](../storage/common/scalability-targets-standard-account.md)voor meer informatie over schaalbaarheids doelen voor Azure Storage-accounts. Zie [schaalbaarheids-en prestatie doelen voor Table Storage](../storage/tables/scalability-targets.md)voor meer informatie over schaalbaarheids doelen voor Table Storage. Latere secties van deze handleiding bespreekt verschillende ontwerp strategieën die u helpen beheren wisselwerking zoals deze, en bespreken hoe het beste aan uw op basis van de specifieke vereisten van uw clienttoepassing partitiesleutel kiezen.  

### <a name="capacity-considerations"></a>Overwegingen voor capaciteit
De volgende tabel bevat enkele van de belangrijkste waarden waarvan u rekening moet houden bij het ontwerpen van een tabel opslag oplossing:  

| Totale capaciteit van een Azure storage-account | 500 TB |
| --- | --- |
| Aantal tabellen in een Azure storage-account |Alleen beperkt door de capaciteit van het opslag account. |
| Aantal partities in een tabel |Alleen beperkt door de capaciteit van het opslag account. |
| Aantal entiteiten in een partitie |Alleen beperkt door de capaciteit van het opslag account. |
| Grootte van een afzonderlijke entiteit |Maxi maal 1 MB, met een maximum van 255 eigenschappen (inclusief de `PartitionKey`, `RowKey`en `Timestamp`). |
| Grootte van de `PartitionKey` |Een teken reeks met een grootte van Maxi maal 1 KB. |
| Grootte van de `RowKey` |Een teken reeks met een grootte van Maxi maal 1 KB. |
| Grootte van de trans actie van een entiteits groep |Een trans actie kan Maxi maal 100 entiteiten bevatten en de payload moet minder dan 4 MB groot zijn. Een entiteit kunt slechts één keer bijwerken door een EGT. |

Zie [Wat is het Table service-gegevens model](https://msdn.microsoft.com/library/azure/dd179338.aspx)? voor meer informatie.  

### <a name="cost-considerations"></a>Kostenoverwegingen
Table Storage is relatief goed koop, maar u moet kosten ramingen voor zowel het capaciteits gebruik als het aantal trans acties opnemen als onderdeel van de evaluatie van een oplossing die gebruikmaakt van Table-opslag. In veel gevallen is het echter wel mogelijk om gedenormaliseerde of dubbele gegevens op te slaan om de prestaties of schaal baarheid van uw oplossing te verbeteren. Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over prijzen.  

## <a name="guidelines-for-table-design"></a>Richtlijnen voor tabelontwerp
In deze lijsten vindt u een overzicht van enkele belang rijke richt lijnen die u moet onthouden wanneer u uw tabellen ontwerpt. In deze hand leiding worden deze allemaal in meer detail beschreven. Deze richt lijnen verschillen van de richt lijnen die u normaal gesp roken volgt voor het ontwerpen van relationele data bases.  

De opslag van de tabel ontwerpen voor een efficiënte *Lees bewerking* :

* **Ontwerpen voor het uitvoeren van query's in lees-en zware toepassingen.** Wanneer u uw tabellen ontwerpt, moet u nadenken over de query's (met name de latentie gevoelige waarden) die u wilt uitvoeren voordat u nadenkt over hoe u uw entiteiten bijwerkt. Dit leidt meestal tot een efficiënte en krachtige oplossing.  
* **Geef zowel `PartitionKey` als `RowKey` op in uw query's.** *Punt query's* zoals dit zijn de meest efficiënte tabel opslag query's.  
* **Overweeg dubbele exemplaren van entiteiten op te slaan.** Table Storage is goedkope, dus overweeg om dezelfde entiteit meerdere keren (met verschillende sleutels) op te slaan om efficiëntere query's mogelijk te maken.  
* **Overweeg uw gegevens te denormaliseren.** Table Storage is goedkope, dus overweeg om uw gegevens te denormaliseren. Bijvoorbeeld, opslaan, samenvatting entiteiten zodat query's voor statistische gegevens alleen nodig voor toegang tot één entiteit.  
* **Samengestelde sleutel waarden gebruiken.** De enige sleutels die u hebt, zijn `PartitionKey` en `RowKey`. Gebruik bijvoorbeeld samengestelde sleutelwaarden zodat alternatieve versleutelde Toegangspaden naar entiteiten.  
* **Query projectie gebruiken.** U kunt verminderen de hoeveelheid gegevens die u via het netwerk worden overgebracht met behulp van query's die u selecteert alleen de velden die u nodig hebt.  

Het ontwerpen van de tabel opslag om te *schrijven* naar een efficiënte manier:  

* **Maak geen dynamische partities.** Kies de sleutels waarmee u kunt uw verzoeken, verdeeld over meerdere partities op elk moment.  
* **Vermijd pieken in verkeer.** Distribueer het verkeer over een redelijke periode en Vermijd pieken in het verkeer.
* **Maak niet noodzakelijkerwijs een afzonderlijke tabel voor elk type entiteit.** Wanneer u atomische transacties via Entiteitstypen vereist, kunt u deze meerdere Entiteitstypen opslaan in dezelfde partitie in dezelfde tabel.
* **Houd rekening met de maximale door Voer die u moet door nemen.** U moet rekening houden met de schaalbaarheids doelen voor Table Storage en ervoor zorgen dat uw ontwerp deze niet overschrijdt.  

Verderop in deze hand leiding ziet u voor beelden waarin al deze principes in de praktijk worden geplaatst.  

## <a name="design-for-querying"></a>Ontwerp voor query's
Tabel opslag kan worden gelezen op intensieve, schrijf intensief of een combi natie van beide. In deze sectie wordt gekeken om Lees bewerkingen efficiënt te ondersteunen. Een ontwerp dat ondersteunt bewerkingen efficiënt lezen is meestal ook efficiënt voor schrijfbewerkingen. Er zijn echter aanvullende overwegingen bij het ontwerpen ter ondersteuning van schrijf bewerkingen. Deze worden beschreven in de volgende sectie, [ontwerpen voor het wijzigen van gegevens](#design-for-data-modification).

Een goed uitgangs punt om gegevens efficiënt te kunnen lezen is het stellen van ' welke query's moet mijn toepassing worden uitgevoerd om de benodigde gegevens op te halen? '  

> [!NOTE]
> Met tabel opslag is het belang rijk om het ontwerp van de voor grond te herstellen, omdat het moeilijk en kostbaar is om dit later te wijzigen. Zo kunt u in een relationele data base vaak prestatie problemen verhelpen door indexen toe te voegen aan een bestaande data base. Dit is geen optie voor tabel opslag.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>De manier waarop uw keuze van `PartitionKey` en `RowKey` invloed heeft op de prestaties van query's
In de volgende voor beelden wordt ervan uitgegaan dat er in tabel opslag werk nemers worden opgeslagen met de volgende structuur (de meeste voor beelden laten de `Timestamp` eigenschap voor duidelijkheid weg):  

| Kolom naam | Gegevenstype |
| --- | --- |
| `PartitionKey` (afdelings naam) |Tekenreeks |
| `RowKey` (werk nemer-ID) |Tekenreeks |
| `FirstName` |Tekenreeks |
| `LastName` |Tekenreeks |
| `Age` |Geheel getal |
| `EmailAddress` |Tekenreeks |

Hier volgen enkele algemene richt lijnen voor het ontwerpen van Table-opslag query's. De filter syntaxis die in de volgende voor beelden wordt gebruikt, is afkomstig uit de tabel opslag REST API. Zie [query-entiteiten](https://msdn.microsoft.com/library/azure/dd179421.aspx)voor meer informatie.  

* Een *Point-query* is de meest efficiënte zoek opdracht en wordt aanbevolen voor Zoek opdrachten met hoge volumes of lookups waarvoor de laagste latentie is vereist. Een dergelijke query kan de indexen gebruiken om een afzonderlijke entiteit efficiënt te vinden door zowel de `PartitionKey` als de `RowKey` waarden op te geven. Bijvoorbeeld: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* Tweede beste is een *bereik query*. Het `PartitionKey`wordt gebruikt en filtert op een bereik van `RowKey` waarden om meer dan één entiteit te retour neren. Met de `PartitionKey` waarde wordt een specifieke partitie geïdentificeerd en de `RowKey` waarden geven een subset van de entiteiten in die partitie aan. Bijvoorbeeld: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* Derde beste is een *partitie scan*. De `PartitionKey`wordt gebruikt en filters worden gefilterd op een andere niet-sleutel eigenschap en kunnen meer dan één entiteit retour neren. De `PartitionKey` waarde identificeert een specifieke partitie en de eigenschapwaarden worden geselecteerd voor een subset van de entiteiten in die partitie. Bijvoorbeeld: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* Een *tabel scan* bevat niet de `PartitionKey`, en is inefficiënt omdat alle partities die de tabel vormen, worden doorzocht op overeenkomende entiteiten. Er wordt een tabel scan uitgevoerd, ongeacht of het filter gebruikmaakt van de `RowKey`. Bijvoorbeeld: `$filter=LastName eq 'Jones'`.  
* Azure Table Storage-query's waarmee meerdere entiteiten worden geretourneerd, worden in `PartitionKey` en `RowKey` volgorde gesorteerd. Kies een `RowKey` die de meest voorkomende sorteer volgorde definieert om te voor komen dat de entiteiten in de client worden gebruikt. Query resultaten die door de Azure-Table-API in Azure Cosmos DB zijn geretourneerd, worden niet gesorteerd op partitie sleutel of rij-sleutel. Zie [verschillen tussen Table-API in azure Cosmos DB en Azure-tabel opslag](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)voor een gedetailleerde lijst met functie verschillen.

Het gebruik van een '**or**' om een filter op te geven op basis van `RowKey` waarden resulteert in een partitie scan en wordt niet behandeld als een bereik query. Vermijd daarom query's die gebruikmaken van filters zoals: `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`.  

Zie voor voor beelden van code aan de client zijde die de Storage-client bibliotheek gebruikt voor het uitvoeren van efficiënte query's:  

* [Een punt query uitvoeren met de Storage-client bibliotheek](#run-a-point-query-by-using-the-storage-client-library)
* [Meerdere entiteiten ophalen met behulp van LINQ](#retrieve-multiple-entities-by-using-linq)
* [Projectie aan server zijde](#server-side-projection)  

Zie voor voorbeelden van de client-side-code die meerdere Entiteitstypen die zijn opgeslagen in dezelfde tabel kunnen worden verwerkt:  

* [Werken met heterogene entiteits typen](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Kies een geschikte `PartitionKey`
Uw keuze van `PartitionKey` moet de nood zaak van het gebruik van EGTs (om consistentie te garanderen) afwegen tegen de vereiste om uw entiteiten over meerdere partities te verdelen (om te zorgen voor een schaal bare oplossing).  

U kunt op een extreem punt al uw entiteiten opslaan in één partitie. Maar dit kan de schaal baarheid van uw oplossing beperken en voor komen dat tabel opslag taak verdeling kan aanvragen. In het andere extreem kunt u één entiteit per partitie opslaan. Dit is zeer schaalbaar en maakt tabel opslag mogelijk voor het laden van aanvragen, maar voor komt u dat u entiteits groeps transacties gebruikt.  

Een ideale `PartitionKey` biedt u de mogelijkheid om efficiënte query's te gebruiken en beschikt over voldoende partities om ervoor te zorgen dat uw oplossing schaalbaar is. Normaal gesp roken zult u zien dat uw entiteiten een geschikte eigenschap hebben die uw entiteiten distribueert over voldoende partities.

> [!NOTE]
> Een voor beeld: in een systeem dat informatie over gebruikers of werk nemers opslaat, kan `UserID` een goede `PartitionKey`zijn. Mogelijk hebt u verschillende entiteiten die een bepaalde `UserID` gebruiken als de partitie sleutel. Elke entiteit die gegevens over een gebruiker opslaat, wordt in één partitie gegroepeerd. Deze entiteiten zijn toegankelijk via EGTs, terwijl ze toch uiterst schaalbaar zijn.
> 
> 

Er zijn aanvullende overwegingen in uw keuze van `PartitionKey` die betrekking hebben op het invoegen, bijwerken en verwijderen van entiteiten. Zie voor meer informatie [ontwerp voor het wijzigen van gegevens](#design-for-data-modification) verderop in dit artikel.  

### <a name="optimize-queries-for-table-storage"></a>Query's voor tabel opslag optimaliseren
Met tabel opslag worden uw entiteiten automatisch geïndexeerd met behulp van de `PartitionKey` en `RowKey` waarden in één geclusterde index. Dit is de reden dat punt query's het meest efficiënt zijn om te gebruiken. Er zijn echter geen andere indexen dan die op de geclusterde index op de `PartitionKey` en `RowKey`.

Veel ontwerpen moeten voldoen aan de vereisten voor het opzoeken van de entiteiten op basis van meerdere criteria inschakelen. U kunt bijvoorbeeld werknemers entiteiten zoeken op basis van e-mail, werk nemer-ID of achternaam. De volgende patronen in de [ontwerp patronen](#table-design-patterns) van de sectie tabel hebben betrekking op deze typen vereisten. De patronen beschrijven ook manieren om te werken met het feit dat tabel opslag geen secundaire indexen biedt.  

* [Intra-Partition secundair index patroon](#intra-partition-secondary-index-pattern): meerdere exemplaren van elke entiteit opslaan met behulp van verschillende `RowKey` waarden (in dezelfde partitie). Dit maakt snelle en efficiënte zoek acties mogelijk en alternatieve Sorteer volgorden met behulp van verschillende `RowKey` waarden.  
* [Secundair index patroon tussen partities](#inter-partition-secondary-index-pattern): meerdere exemplaren van elke entiteit opslaan met behulp van verschillende `RowKey` waarden in afzonderlijke partities of in afzonderlijke tabellen. Dit maakt snelle en efficiënte zoek acties mogelijk en alternatieve Sorteer volgorden met behulp van verschillende `RowKey` waarden.  
* [Patroon van index entiteiten](#index-entities-pattern): behoud index entiteiten om efficiënte Zoek opdrachten in te scha kelen waarmee lijsten met entiteiten worden geretourneerd.  

### <a name="sort-data-in-table-storage"></a>Gegevens in tabel opslag sorteren

Tabel opslag retourneert query resultaten die in oplopende volg orde zijn gesorteerd, op basis van `PartitionKey` en vervolgens op `RowKey`.

> [!NOTE]
> Query resultaten die door de Azure-Table-API in Azure Cosmos DB zijn geretourneerd, worden niet gesorteerd op partitie sleutel of rij-sleutel. Zie [verschillen tussen Table-API in azure Cosmos DB en Azure-tabel opslag](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)voor een gedetailleerde lijst met functie verschillen.

Sleutels in tabel opslag zijn teken reeks waarden. Om ervoor te zorgen dat numerieke waarden correct worden gesorteerd, moet u ze converteren naar een vaste lengte en ze met nullen aanvullen. Als de waarde van de werk nemer-ID die u als `RowKey` gebruikt, bijvoorbeeld een geheel getal is, moet u werk nemer-ID **123** converteren naar **00000123**. 

Veel toepassingen zijn vereisten voor het gebruik van de gegevens gesorteerd op verschillende plekken: bijvoorbeeld werknemers sorteren op naam, of lid wordt van datum. De volgende patronen in de sectie [tabel ontwerp patronen](#table-design-patterns) zijn een alternatief voor het sorteren van de sorteer volgorde voor uw entiteiten:  

* [Intra-Partition secundair index patroon](#intra-partition-secondary-index-pattern): meerdere exemplaren van elke entiteit opslaan met behulp van verschillende `RowKey` waarden (in dezelfde partitie). Dit maakt snelle en efficiënte zoek acties mogelijk en alternatieve Sorteer volgorden met behulp van verschillende `RowKey` waarden.  
* [Secundair index patroon tussen partities](#inter-partition-secondary-index-pattern): meerdere exemplaren van elke entiteit opslaan met behulp van verschillende `RowKey` waarden in afzonderlijke partities in afzonderlijke tabellen. Dit maakt snelle en efficiënte zoek acties mogelijk en alternatieve Sorteer volgorden met behulp van verschillende `RowKey` waarden.
* [Eind patroon van logboek](#log-tail-pattern): Haal de *n* -entiteiten die het laatst zijn toegevoegd aan een partitie op met behulp van een `RowKey` waarde die in omgekeerde datum-en tijd volgorde wordt gesorteerd.  

## <a name="design-for-data-modification"></a>Ontwerp voor gegevenswijziging
In deze sectie richt zich op de ontwerpoverwegingen voor het optimaliseren van toevoegingen, updates, en wordt verwijderd. In sommige gevallen moet u de afweging evalueren tussen de ontwerpen die worden geoptimaliseerd voor het uitvoeren van query's op ontwerpen die worden geoptimaliseerd voor het wijzigen van gegevens. Deze evaluatie is vergelijkbaar met wat u in ontwerpen voor relationele data bases doet (hoewel de technieken voor het beheer van de ontwerp traden verschillen in een relationele data base). In de sectie [tabel ontwerp patronen](#table-design-patterns) worden enkele gedetailleerde ontwerp patronen voor tabel opslag beschreven en worden enkele van deze trans acties gemarkeerd. In de praktijk zult u merken dat veel modellen die zijn geoptimaliseerd voor het uitvoeren van query's, ook geschikt zijn voor het wijzigen van entiteiten.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>De prestaties van INSERT-, update-en delete-bewerkingen optimaliseren
Als u een entiteit wilt bijwerken of verwijderen, moet u deze kunnen identificeren met behulp van de `PartitionKey` en `RowKey` waarden. In dit opzicht moeten uw keuze van `PartitionKey` en `RowKey` voor het wijzigen van entiteiten vergelijk bare criteria volgen op uw keuze om Point-query's te ondersteunen. U wilt entiteiten zo efficiënt mogelijk identificeren. U wilt geen inefficiënte partitie of tabel Scan gebruiken om een entiteit te zoeken om de `PartitionKey` en `RowKey` waarden te vinden die u nodig hebt om deze bij te werken of te verwijderen.  

De volgende patronen in het [ontwerp patroon](#table-design-patterns) van de sectie tabel maken het optimaliseren van de prestaties van uw insert-, update-en delete-bewerkingen.  

* [Patroon voor het verwijderen van hoog volume](#high-volume-delete-pattern): Schakel het verwijderen van een groot aantal entiteiten in door alle entiteiten voor gelijktijdige verwijdering in hun eigen afzonderlijke tabel op te slaan. U verwijdert de entiteiten door de tabel te verwijderen.  
* [Patroon van de gegevens reeks](#data-series-pattern): Sla volledige gegevens reeksen op in één entiteit om het aantal aanvragen dat u maakt, te minimaliseren.  
* [Patroon voor grote entiteiten](#wide-entities-pattern): gebruik meerdere fysieke entiteiten om logische entiteiten met meer dan 252 eigenschappen op te slaan.  
* [Patroon van grote entiteiten](#large-entities-pattern): gebruik Blob Storage voor het opslaan van grote eigenschaps waarden.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Consistentie in uw opgeslagen entiteiten garanderen
De andere belangrijke factoren die van invloed op de keuze van sleutels voor het optimaliseren van aanpassingen wordt beschreven hoe u zorgen voor consistentie met behulp van atomic-transacties. U kunt alleen een EGT gebruiken om te worden uitgevoerd op entiteiten die zijn opgeslagen in dezelfde partitie.  

De volgende patronen in de sectie [tabel ontwerp patronen](#table-design-patterns) adres consistentie beheer:  

* [Intra-Partition secundair index patroon](#intra-partition-secondary-index-pattern): meerdere exemplaren van elke entiteit opslaan met behulp van verschillende `RowKey` waarden (in dezelfde partitie). Dit maakt snelle en efficiënte zoek acties mogelijk en alternatieve Sorteer volgorden met behulp van verschillende `RowKey` waarden.  
* [Secundair index patroon tussen partities](#inter-partition-secondary-index-pattern): meerdere exemplaren van elke entiteit opslaan met behulp van verschillende `RowKey` waarden in afzonderlijke partities of in afzonderlijke tabellen. Dit maakt snelle en efficiënte zoek acties mogelijk en alternatieve Sorteer volgorden met behulp van verschillende `RowKey` waarden.  
* [Uiteindelijk consistent transactie patroon](#eventually-consistent-transactions-pattern): Schakel uiteindelijk consistent gedrag in voor de partitie grenzen of de grenzen van het opslag systeem met behulp van Azure-wacht rijen.
* [Patroon van index entiteiten](#index-entities-pattern): behoud index entiteiten om efficiënte Zoek opdrachten in te scha kelen waarmee lijsten met entiteiten worden geretourneerd.  
* [Denormalisatie patroon](#denormalization-pattern): gerelateerde gegevens combi neren in één entiteit, zodat u alle gegevens kunt ophalen die u nodig hebt met één punt query.  
* [Patroon van de gegevens reeks](#data-series-pattern): Sla volledige gegevens reeksen op in één entiteit om het aantal aanvragen dat u maakt, zo klein mogelijk te houden.  

Zie [trans acties voor entiteits groepen](#entity-group-transactions) verderop in dit artikel voor meer informatie.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Zorg ervoor dat uw ontwerp voor efficiënte wijzigingen efficiënte query's vereenvoudigt
In veel gevallen moet altijd een ontwerp voor efficiënte query resultaten in een efficiënte wijzigingen, maar u evalueren of dit het geval is bij uw specifieke scenario. Sommige van de patronen in de sectie [tabel ontwerp patronen](#table-design-patterns) evalueren expliciet de traden tussen query's en het wijzigen van entiteiten, en u moet altijd rekening houden met het aantal van elk type bewerking.  

De volgende patronen in de [ontwerp patronen](#table-design-patterns) van de sectie tabel adresseren de trans acties tussen het ontwerpen voor efficiënte query's en het ontwerpen voor efficiënte gegevens aanpassing:  

* [Samengesteld sleutel patroon](#compound-key-pattern): gebruik samengestelde `RowKey` waarden om ervoor te zorgen dat een client gerelateerde gegevens opzoekt met één punt query.  
* [Eind patroon van logboek](#log-tail-pattern): Haal de *n* -entiteiten die het laatst zijn toegevoegd aan een partitie op met behulp van een `RowKey` waarde die in omgekeerde datum-en tijd volgorde wordt gesorteerd.  

## <a name="encrypt-table-data"></a>Tabel gegevens versleutelen
De client bibliotheek van .NET Azure Storage ondersteunt versleuteling van eigenschappen van teken reeks entiteiten voor INSERT-en Replace-bewerkingen. De versleutelde teken reeksen worden als binaire eigenschappen opgeslagen op de service en worden teruggeconverteerd naar teken reeksen na ontsleuteling.    

Gebruikers moeten de eigenschappen moeten worden versleuteld opgeven voor tabellen, naast het coderingsbeleid. Geef een `EncryptProperty` kenmerk op (voor POCO-entiteiten die zijn afgeleid van `TableEntity`) of geef een versleutelings conflict op in de aanvraag opties. Een coderings omzetter is een gemachtigde die een partitie sleutel, een rij en een eigenschaps naam gebruikt en een Booleaanse waarde retourneert die aangeeft of die eigenschap moet worden versleuteld. Tijdens de versleuteling gebruikt de client bibliotheek deze informatie om te bepalen of een eigenschap moet worden versleuteld tijdens het schrijven naar de kabel. De gemachtigde biedt ook de mogelijkheid van logica over hoe eigenschappen zijn gecodeerd. (Bijvoorbeeld als X, vervolgens versleutelings eigenschap A; anders eigenschappen A en B versleutelen.) Het is niet nodig deze informatie te verstrekken tijdens het lezen of doorzoeken van entiteiten.

Samen voegen wordt momenteel niet ondersteund. Omdat een subset van eigenschappen mogelijk eerder is versleuteld met behulp van een andere sleutel, kunt u eenvoudig de nieuwe eigenschappen samen voegen en de meta gegevens bijwerken, waardoor er gegevens verloren gaan. Voor samen voegen moet u extra service aanroepen maken om de bestaande entiteit van de service te lezen of een nieuwe sleutel per eigenschap te gebruiken. Geen van deze zijn geschikt om prestatie redenen.     

Zie [versleuteling aan client zijde en Azure Key Vault voor Microsoft Azure Storage](../storage/common/storage-client-side-encryption.md)voor meer informatie over het versleutelen van tabel gegevens.  

## <a name="model-relationships"></a>Model relaties
Het ontwikkelen van domeinmodellen is een belangrijke stap in het ontwerp van complexe systemen. Normaal gesp roken gebruikt u het model proces om entiteiten en de relaties tussen hen te identificeren, zodat u inzicht krijgt in het bedrijfs domein en het ontwerp van uw systeem kunt indelen. In deze sectie wordt uitgelegd hoe u een aantal algemene relatie typen in domein modellen kunt vertalen naar ontwerpen voor tabel opslag. Het toewijzings proces van een logisch gegevens model aan een fysiek NoSQL gegevens model wijkt af van het beleid dat wordt gebruikt bij het ontwerpen van een relationele data base. Het ontwerp van relationele data bases gaat doorgaans uit van een proces voor gegevens normalisatie dat is geoptimaliseerd voor het minimaliseren van redundantie. Dit ontwerp gaat ook uit van een declaratieve query functie waarbij de implementatie van de werking van de data base wordt abstract.  

### <a name="one-to-many-relationships"></a>Een-op-veel-relaties
Een-op-veel-relaties tussen zakelijke domeinobjecten vaak optreden: één afdeling heeft bijvoorbeeld veel werknemers. Er zijn verschillende manieren voor het implementeren van een-op-veel-relaties in tabel opslag, elk met voor-en nadelen die relevant kunnen zijn voor het specifieke scenario.  

Bekijk het voor beeld van een grote Multi-National onderneming met tien duizenden afdelingen en werknemers entiteiten. Elke afdeling heeft veel werk nemers en elke werk nemer is gekoppeld aan één specifieke afdeling. Een manier is om afzonderlijke afdelings-en werknemers entiteiten op te slaan, zoals de volgende:  

![Afbeelding van een afdelings entiteit en een werknemers entiteit][1]

In dit voor beeld ziet u een impliciete een-op-veel-relatie tussen de typen, op basis van de `PartitionKey` waarde. Elke afdeling kan veel werknemers hebben.  

In dit voorbeeld toont ook een entiteit afdeling en de gerelateerde werknemer-entiteiten in dezelfde partitie. U kunt ervoor kiezen om verschillende partities, tabellen of zelfs opslag accounts te gebruiken voor de verschillende entiteits typen.  

Een alternatieve methode is het denormaliseren van uw gegevens en alleen werk nemers entiteiten met Gedenormaliseerde gegevens van de afdeling op te slaan, zoals wordt weer gegeven in het volgende voor beeld. In dit specifieke scenario is deze gedenormaliseerde aanpak mogelijk niet het beste als u een vereiste hebt om de details van een Afdelings Manager te kunnen wijzigen. Als u dit wilt doen, moet u elke werk nemer in de afdeling bijwerken.  

![Afbeelding van entiteit van werk nemer][2]

Zie voor meer informatie het [denormalisatie patroon](#denormalization-pattern) verderop in deze hand leiding.  

De volgende tabel bevat een overzicht van de voor-en nadelen van de benaderingen voor het opslaan van werk nemers en afdelings entiteiten met een een-op-veel-relatie. U moet ook overwegen hoe vaak u verwacht om verschillende bewerkingen uit te voeren. Het kan acceptabel zijn om een ontwerp te hebben dat een dure bewerking bevat als deze bewerking alleen zelden plaatsvindt.  

<table>
<tr>
<th>Methode</th>
<th>Professionals</th>
<th>Nadelen</th>
</tr>
<tr>
<td>Afzonderlijke Entiteitstypen, dezelfde partitie, dezelfde tabel</td>
<td>
<ul>
<li>U kunt een entiteit afdeling bijwerken met een eenmalige bewerking.</li>
<li>U kunt een EGT gebruiken voor het handhaven van de consistentie hebt u een vereiste voor het wijzigen van een entiteit afdeling wanneer u update/insert/verwijderen een werknemer-entiteit. Bijvoorbeeld, als u een aantal afdelingen medewerkers voor elke afdeling onderhouden.</li>
</ul>
</td>
<td>
<ul>
<li>Mogelijk moet u zowel een werk nemer als een afdelings entiteit voor sommige client activiteiten ophalen.</li>
<li>Opslagbewerkingen gebeuren in dezelfde partitie. Op volumes met een hoge trans actie kan dit resulteren in een hot spot.</li>
<li>U kunt een werk nemer niet naar een nieuwe afdeling verplaatsen met behulp van een EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Afzonderlijke Entiteitstypen, verschillende partities of tabellen of storage-accounts</td>
<td>
<ul>
<li>U kunt een entiteit van de afdeling of de werknemer entiteit bijwerken met een eenmalige bewerking.</li>
<li>Op volumes met een hoge trans actie kan dit helpen de belasting over meer partities te verdelen.</li>
</ul>
</td>
<td>
<ul>
<li>Mogelijk moet u zowel een werk nemer als een afdelings entiteit voor sommige client activiteiten ophalen.</li>
<li>U kunt EGTs niet gebruiken om consistentie te behouden wanneer u een werk nemer bijwerkt of invoegt/verwijdert en een afdeling bijwerkt. Bijvoorbeeld, een aantal medewerkers in een entiteit afdeling worden bijgewerkt.</li>
<li>U kunt een werk nemer niet naar een nieuwe afdeling verplaatsen met behulp van een EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormaliseren in één entiteitstype</td>
<td>
<ul>
<li>U kunt alle informatie die u nodig hebt met een enkele aanvraag ophalen.</li>
</ul>
</td>
<td>
<ul>
<li>Het kan duur zijn om de consistentie te hand haven als u gegevens van de afdeling moet bijwerken (hiervoor moet u alle werk nemers op een afdeling bijwerken).</li>
</ul>
</td>
</tr>
</table>

Hoe u een van deze opties kiest, en welke van de voor delen en nadelen het belangrijkst zijn, is afhankelijk van uw specifieke toepassings scenario's. Hoe vaak worden er bijvoorbeeld afdelings entiteiten gewijzigd? Moeten al uw werknemers query's de aanvullende afdelings gegevens hebben? Hoe dicht zijn de schaalbaarheids limieten voor uw partities of uw opslag account?  

### <a name="one-to-one-relationships"></a>-Op-een-relaties
Domein modellen kunnen een-op-een-relaties tussen entiteiten bevatten. Als u een een-op-een-relatie in Table Storage wilt implementeren, moet u ook kiezen hoe u de twee gerelateerde entiteiten wilt koppelen wanneer u deze beide moet ophalen. Deze koppeling kan impliciet zijn, op basis van een Conventie in de sleutel waarden, of expliciet, door een koppeling op te slaan in de vorm van `PartitionKey` en `RowKey` waarden in elke entiteit te koppelen aan de gerelateerde entiteit. Zie de sectie [een-op-veel-relaties](#one-to-many-relationships)voor een bespreking van de vraag of u de gerelateerde entiteiten in dezelfde partitie moet opslaan.  

Er zijn ook implementatie overwegingen die kunnen leiden tot het implementeren van een-op-een-relaties in tabel opslag:  

* Verwerken van grote entiteiten (Zie het [patroon grote entiteiten](#large-entities-pattern)voor meer informatie).  
* Toegangs beheer implementeren (Zie [toegang beheren met hand tekeningen voor gedeelde toegang](#control-access-with-shared-access-signatures)) voor meer informatie.  

### <a name="join-in-the-client"></a>Deelnemen aan de client
Hoewel er manieren zijn om relaties in tabel opslag te model leren, moet u niet verg eten dat de twee belangrijkste redenen voor het gebruik van tabel opslag schaal baarheid en prestaties zijn. Als u een model maakt van veel relaties die de prestaties en schaal baarheid van uw oplossing in de hand hebben, kunt u het beste zelf bepalen of u de gegevens relaties in uw tabel ontwerp nodig hebt. U kunt het ontwerp mogelijk vereenvoudigen en de schaal baarheid en prestaties van uw oplossing verbeteren, als u uw client toepassing de benodigde deelname wilt laten uitvoeren.  

Als u bijvoorbeeld kleine tabellen hebt die gegevens bevatten die niet vaak veranderen, kunt u deze gegevens één keer ophalen en opslaan in de cache op de client. Dit kunt herhaalde interactie om op te halen van dezelfde gegevens voorkomen. In de voor beelden in deze hand leiding is de set van afdelingen in een kleine organisatie waarschijnlijk klein en niet regel matig gewijzigd. Dit maakt het een goede kandidaat voor gegevens die een client toepassing kan downloaden en opslaan als opzoek gegevens.  

### <a name="inheritance-relationships"></a>Overname van relaties
Als uw client toepassing gebruikmaakt van een set klassen die deel uitmaken van een overname relatie om bedrijfs entiteiten weer te geven, kunt u deze entiteiten eenvoudig persistent maken in tabel opslag. Stel dat u de volgende set klassen hebt gedefinieerd in uw client toepassing, waarbij `Person` een abstracte klasse is.

![Diagram van overname relaties][3]

U kunt exemplaren van de twee concrete klassen in tabel opslag persistent maken met behulp van één `Person` tabel. Gebruik entiteiten die er ongeveer als volgt uitzien:  

![Afbeelding van de entiteit van de klant en de entiteit van de werk nemer][4]

Zie voor meer informatie over het werken met meerdere entiteits typen in dezelfde tabel in client code [werken met heterogene entiteits typen](#work-with-heterogeneous-entity-types) verderop in deze hand leiding. Dit bevat voorbeelden van het entiteitstype in clientcode herkennen.  

## <a name="table-design-patterns"></a>Tabelontwerppatronen
In de vorige secties hebt u geleerd hoe u het tabel ontwerp optimaliseert voor het ophalen van entiteits gegevens met behulp van query's en voor het invoegen, bijwerken en verwijderen van entiteits gegevens. In deze sectie worden enkele patronen beschreven die geschikt zijn voor gebruik met tabel opslag. Daarnaast ziet u hoe u een aantal van de problemen en berekenings taken die eerder in deze hand leiding zijn opgetreden, nagenoeg kunt aanpakken. In het volgende diagram ziet u een overzicht van de relaties tussen de verschillende patronen:  

![Diagram van tabel ontwerp patronen][5]

De patroon kaart markeert enkele relaties tussen patronen (blauw) en anti-patronen (oranje) die in deze hand leiding worden beschreven. Er zijn natuurlijk veel andere patronen die moeten overwogen worden. Een van de belangrijkste scenario's voor tabel opslag is bijvoorbeeld het gebruik van het [patroon gerealiseerde weer gave](https://msdn.microsoft.com/library/azure/dn589782.aspx) van het scheidings patroon van de [opdracht query-verantwoordelijkheid](https://msdn.microsoft.com/library/azure/jj554200.aspx) .  

### <a name="intra-partition-secondary-index-pattern"></a>Intra-partitie secundaire index patroon
Sla meerdere exemplaren van elke entiteit op met behulp van verschillende `RowKey` waarden (in dezelfde partitie). Dit maakt snelle en efficiënte zoek acties mogelijk en alternatieve Sorteer volgorden met behulp van verschillende `RowKey` waarden. Updates tussen kopieën kunnen consistent worden gehouden met behulp van EGTs.  

#### <a name="context-and-problem"></a>Context en probleem
Met tabel opslag worden entiteiten automatisch geïndexeerd met behulp van de waarden `PartitionKey` en `RowKey`. Hiermee kan een client toepassing een entiteit efficiënt ophalen met behulp van deze waarden. Als u bijvoorbeeld de volgende tabel structuur gebruikt, kan een client toepassing een punt query gebruiken om een afzonderlijke werknemers entiteit op te halen met behulp van de afdelings naam en de werk nemer-ID (de `PartitionKey` en `RowKey` waarden). Een client kan ook entiteiten ophalen die zijn gesorteerd op werk nemer-ID binnen elke afdeling.

![Afbeelding van entiteit van werk nemer][6]

Als u ook een werknemers entiteit wilt zoeken op basis van de waarde van een andere eigenschap, zoals e-mail adres, moet u een minder efficiënte partitie Scan gebruiken om een overeenkomst te vinden. Dit komt doordat tabel opslag geen secundaire indexen biedt. Daarnaast is er geen optie voor het aanvragen van een lijst met werk nemers die in een andere volg orde zijn gesorteerd dan `RowKey` order.  

#### <a name="solution"></a>Oplossing
Om het ontbreken van secundaire indexen te omzeilen, kunt u meerdere exemplaren van elke entiteit opslaan, waarbij elke kopie een andere `RowKey` waarde gebruikt. Als u een entiteit met de volgende structuren opslaat, kunt u op efficiënte wijze werknemers entiteiten ophalen op basis van het e-mail adres of de werk nemer-ID. Met de voorvoegsel waarden voor `RowKey`, `empid_`en `email_` kunt u een query uitvoeren voor één werk nemer of een bereik van werk nemers, met behulp van een reeks e-mail adressen of werk nemer-Id's.  

![Afbeelding van entiteit van werk nemer met verschillende RowKey-waarden][7]

De volgende twee filter criteria (één op te geven op basis van de werk nemer-ID en één op basis van een e-mail adres) opgeven punt query's:  

* $filter = (PartitionKey eq 'Verkoop') en (RowKey eq 'empid_000223')  
* $filter = (PartitionKey EQ ' Sales ') en (RowKey EQ 'email_jonesj@contoso.com')  

Als u een query uitvoert voor een bereik van werk nemers, kunt u een bereik opgeven, gesorteerd op werk nemer-ID-order of een bereik dat is gesorteerd in de e-mailadres volgorde. Zoek naar entiteiten met het juiste voor voegsel in de `RowKey`.  

* Als u wilt zoeken naar alle werk nemers van de afdeling verkoop met een werk nemer-ID in het bereik 000100 tot 000199, gebruikt u: $filter = (PartitionKey EQ ' Sales ') en (RowKey ge ' empid_000100 ') en (RowKey Le ' empid_000199 ')  
* Als u wilt zoeken naar alle werk nemers van de afdeling verkoop met een e-mail adres dat begint met de letter ' a ', gebruikt u: $filter = (PartitionKey EQ ' Sales ') en (RowKey ge ' email_a ') en (RowKey lt ' email_b ')  
  
De filter syntaxis die in de voor gaande voor beelden wordt gebruikt, is afkomstig uit de tabel opslag REST API. Zie [query-entiteiten](https://msdn.microsoft.com/library/azure/dd179421.aspx)voor meer informatie.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Table Storage is relatief goed koop om te gebruiken, dus de kosten besparing van het opslaan van dubbele gegevens mag geen grote bezorgdheid zijn. U moet de kosten van uw ontwerp echter altijd evalueren op basis van uw verwachte opslag vereisten en alleen dubbele entiteiten toevoegen ter ondersteuning van de query's die door uw client toepassing worden uitgevoerd.  
* Omdat de secundaire index entiteiten worden opgeslagen in dezelfde partitie als de oorspronkelijke entiteiten, moet u ervoor zorgen dat u de schaalbaarheids doelen voor een afzonderlijke partitie niet overschrijdt.  
* U kunt uw dubbele entiteiten consistent zijn met elkaar houden met behulp van EGTs atomisch bijwerken van de twee kopieën van de entiteit. Dit betekent dat u alle exemplaren van een entiteit moet opslaan in dezelfde partitie. Zie [entiteits groeps transacties gebruiken](#entity-group-transactions)voor meer informatie.  
* De waarde die wordt gebruikt voor de `RowKey` moet uniek zijn voor elke entiteit. Overweeg het gebruik van de samengestelde sleutel waarden.  
* Met de opvullings numerieke waarden in de `RowKey` (bijvoorbeeld de werk nemer-ID 000223) kunnen sorteren en filteren op basis van de boven-en ondergrenzen worden gesorteerd.  
* U hoeft niet noodzakelijkerwijs alle eigenschappen van uw entiteit te dupliceren. Als de query's die de entiteiten opzoeken met behulp van het e-mail adres in de `RowKey` nooit de leeftijd van de werk nemer nodig hebben, kunnen deze entiteiten de volgende structuur hebben:

  ![Afbeelding van entiteit van werk nemer][8]

* Normaal gesp roken is het beter om dubbele gegevens op te slaan en ervoor te zorgen dat u alle gegevens kunt ophalen die u nodig hebt met één query, dan om één query te gebruiken om een entiteit te zoeken en een andere om de vereiste gegevens op te zoeken.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer:

- Uw client toepassing moet entiteiten ophalen met behulp van verschillende sleutels.
- Uw client moet entiteiten in verschillende sorteer volgorden ophalen.
- U kunt elke entiteit identificeren met behulp van verschillende unieke waarden.

Zorg er echter voor dat u de schaal baarheids limieten van de partitie niet overschrijdt wanneer u entiteits lookups uitvoert met behulp van de verschillende `RowKey` waarden.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Secundair index patroon tussen partities](#inter-partition-secondary-index-pattern)
* [Samengesteld sleutel patroon](#compound-key-pattern)
* [Trans acties van entiteits groep](#entity-group-transactions)
* [Werken met heterogene entiteits typen](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Secundaire index tussen partitie-patroon
Meerdere exemplaren van elke entiteit opslaan door gebruik te maken van verschillende `RowKey` waarden in afzonderlijke partities of in afzonderlijke tabellen. Dit maakt snelle en efficiënte zoek acties mogelijk en alternatieve Sorteer volgorden met behulp van verschillende `RowKey` waarden.  

#### <a name="context-and-problem"></a>Context en probleem
Met tabel opslag worden entiteiten automatisch geïndexeerd met behulp van de waarden `PartitionKey` en `RowKey`. Hiermee kan een client toepassing een entiteit efficiënt ophalen met behulp van deze waarden. Als u bijvoorbeeld de volgende tabel structuur gebruikt, kan een client toepassing een punt query gebruiken om een afzonderlijke werknemers entiteit op te halen met behulp van de afdelings naam en de werk nemer-ID (de `PartitionKey` en `RowKey` waarden). Een client kan ook entiteiten ophalen die zijn gesorteerd op werk nemer-ID binnen elke afdeling.  

![Afbeelding van entiteit van werk nemer][9]

Als u ook wilt kunnen vinden van een werknemer-entiteit op basis van de waarde van een ander domein, zoals e-mailadres, moet u een minder efficiënt partitie scan gebruiken om een overeenkomst te vinden. Dit komt doordat tabel opslag geen secundaire indexen biedt. Daarnaast is er geen optie voor het aanvragen van een lijst met werk nemers die in een andere volg orde zijn gesorteerd dan `RowKey` order.  

U verwacht een groot aantal trans acties tegen deze entiteiten en wilt het risico van de tabel opslag beperken die uw client beperkt.  

#### <a name="solution"></a>Oplossing
Om het ontbreken van secundaire indexen te omzeilen, kunt u meerdere exemplaren van elke entiteit opslaan, waarbij elk exemplaar wordt gebruikt met verschillende `PartitionKey` en `RowKey` waarden. Als u een entiteit met de volgende structuren opslaat, kunt u op efficiënte wijze werknemers entiteiten ophalen op basis van het e-mail adres of de werk nemer-ID. Met de voorvoegsel waarden voor `PartitionKey`, `empid_`en `email_` kunt u bepalen welke index u wilt gebruiken voor een query.  

![Afbeelding van entiteit van werk nemer met primaire index en werknemers entiteit met secundaire index][10]

De volgende twee filter criteria (één op te geven op basis van de werk nemer-ID en één op basis van een e-mail adres) opgeven punt query's:  

* $filter = (PartitionKey eq ' empid_Sales') en (RowKey eq '000223')
* $filter = (PartitionKey EQ ' email_Sales ') en (RowKey EQ 'jonesj@contoso.com')  

Als u een query uitvoert voor een bereik van werk nemers, kunt u een bereik opgeven, gesorteerd op werk nemer-ID-order of een bereik dat is gesorteerd in de e-mailadres volgorde. Zoek naar entiteiten met het juiste voor voegsel in de `RowKey`.  

* Als u wilt zoeken naar alle werk nemers van de afdeling verkoop met een werk nemer-ID in het bereik **000100** tot **000199**, gesorteerd in de volg orde van de werk nemer-id, gebruikt u: $filter = (PartitionKey EQ ' empid_Sales ') en (RowKey ge 000100 ') en (RowKey Le ' 000199 ')  
* Als u wilt zoeken naar alle werk nemers van de afdeling verkoop met een e-mail adres dat begint met ' a ', gesorteerd in de e-mailadres volgorde, gebruikt u: $filter = (PartitionKey EQ ' email_Sales ') en (RowKey ge ' a ') en (RowKey lt ' b ')  

Houd er rekening mee dat de filter syntaxis die in de voor gaande voor beelden wordt gebruikt, afkomstig is uit de tabel opslag REST API. Zie [query-entiteiten](https://msdn.microsoft.com/library/azure/dd179421.aspx)voor meer informatie.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* U kunt ervoor zorgen dat uw dubbele entiteiten uiteindelijk consistent zijn met elkaar met behulp van het [patroon voor uiteindelijk consistente trans acties](#eventually-consistent-transactions-pattern) voor het onderhouden van de primaire en secundaire index entiteiten.  
* Table Storage is relatief goedkope gebruik, dus de kosten besparing van het opslaan van dubbele gegevens mag geen grote bezorgdheid zijn. U kunt de kosten van uw ontwerp echter altijd evalueren op basis van uw verwachte opslag vereisten en alleen dubbele entiteiten toevoegen ter ondersteuning van de query's die door uw client toepassing worden uitgevoerd.  
* De waarde die wordt gebruikt voor de `RowKey` moet uniek zijn voor elke entiteit. Overweeg het gebruik van de samengestelde sleutel waarden.  
* Met de opvullings numerieke waarden in de `RowKey` (bijvoorbeeld de werk nemer-ID 000223) kunnen sorteren en filteren op basis van de boven-en ondergrenzen worden gesorteerd.  
* U hoeft niet noodzakelijkerwijs alle eigenschappen van uw entiteit te dupliceren. Als de query's die de entiteiten opzoeken met behulp van het e-mail adres in de `RowKey` nooit de leeftijd van de werk nemer nodig hebben, kunnen deze entiteiten de volgende structuur hebben:
  
  ![Afbeelding met werk nemers entiteit met secundaire index][11]
* Normaal gesp roken is het beter om dubbele gegevens op te slaan en ervoor te zorgen dat u alle gegevens kunt ophalen die u nodig hebt met één query, dan om één query te gebruiken om een entiteit te zoeken met behulp van de secundaire index en een andere om de vereiste gegevens in de primaire index op te zoeken.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer:

- Uw client toepassing moet entiteiten ophalen met behulp van verschillende sleutels.
- Uw client moet entiteiten in verschillende sorteer volgorden ophalen.
- U kunt elke entiteit identificeren met behulp van verschillende unieke waarden.

Gebruik dit patroon wanneer u wilt voor komen dat de schaal limieten voor partities worden overschreden wanneer u entiteits lookups uitvoert met behulp van de verschillende `RowKey` waarden.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Patroon uiteindelijk consistent trans acties](#eventually-consistent-transactions-pattern)  
* [Secundair index patroon voor de intra partitie](#intra-partition-secondary-index-pattern)  
* [Samengesteld sleutel patroon](#compound-key-pattern)  
* [Trans acties van entiteits groep](#entity-group-transactions)  
* [Werken met heterogene entiteits typen](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Uiteindelijk consistent transacties patroon
Uiteindelijk consistent gedrag partitiegrenzen of wanneer de grenzen van de storage-systeem inschakelen met behulp van Azure-wachtrijen.  

#### <a name="context-and-problem"></a>Context en probleem
Atomische transacties inschakelen EGTs voor meerdere entiteiten die dezelfde partitiesleutel delen. Om redenen van prestaties en schaal baarheid kunt u ervoor kiezen entiteiten op te slaan die consistentie vereisten hebben in afzonderlijke partities of in een afzonderlijk opslag systeem. In een dergelijk scenario kunt u EGTs niet gebruiken om consistentie te hand haven. Bijvoorbeeld, mogelijk hebt u een vereiste voor het handhaven van de uiteindelijke consistentie tussen:  

* Entiteiten die zijn opgeslagen in twee verschillende partities in dezelfde tabel, in verschillende tabellen of in verschillende opslagaccounts.  
* Een entiteit die is opgeslagen in tabel opslag en een blob die is opgeslagen in Blob Storage.  
* Een entiteit die is opgeslagen in tabel opslag en een bestand in een bestands systeem.  
* Een entiteit die is opgeslagen in tabel opslag, die is geïndexeerd met behulp van Azure Cognitive Search.  

#### <a name="solution"></a>Oplossing
Met behulp van Azure-wachtrijen, kunt u een oplossing die zorgt voor uiteindelijke consistentie tussen twee of meer partities of opslagsystemen implementeren.

Om deze aanpak te illustreren, moet u ervan uitgaan dat u een vereiste hebt om voormalige werknemers entiteiten te archiveren. Voormalige werknemers entiteiten worden zelden gequeryeerd en moeten worden uitgesloten van activiteiten die worden uitgevoerd op huidige werk nemers. U kunt deze vereiste alleen implementeren als u actieve werk nemers in de **huidige** tabel en voormalige werk nemers in de **Archief** tabel opslaat. Voor het archiveren van een werk nemer moet u de entiteit uit de **huidige** tabel verwijderen en de entiteit toevoegen aan de **Archief** tabel.

Maar u kunt geen EGT gebruiken om deze twee bewerkingen uit te voeren. Om te voorkomen dat het risico dat een fout veroorzaakt een entiteit wordt weergegeven in beide of geen van beide tabellen, moet de archiveringsbewerking uiteindelijk consistent. De volgende reeksdiagram geeft een overzicht van de stappen in deze bewerking.  

![Oplossingsdiagram voor uiteindelijke consistentie][12]

Een client initieert de archief bewerking door een bericht te plaatsen in een Azure-wachtrij (in dit voor beeld om werk nemers te archiveren #456). Een werkrol, peilt de wachtrij voor nieuwe berichten; Wanneer er een wordt gevonden, leest het bericht en een verborgen kopie van de wachtrij verlaat. Met de rol werk rollen volgende wordt een kopie van de entiteit opgehaald uit de **huidige** tabel, wordt een kopie ingevoegd in de **Archief** tabel en wordt vervolgens het origineel uit de **huidige** tabel verwijderd. Ten slotte, als er geen fouten uit de vorige stap zijn, de werkrol verborgen wordt het bericht verwijderd uit de wachtrij.  

In dit voor beeld voegt stap 4 in het diagram de werk nemer in de **Archief** tabel in. De werk nemer kan worden toegevoegd aan een BLOB in Blob Storage of een bestand in een bestands systeem.  

#### <a name="recover-from-failures"></a>Herstellen van fouten
Het is belang rijk dat de bewerkingen in stap 4-5 in het diagram worden *idempotent* in het geval de werk rollen de archief bewerking opnieuw moeten starten. Als u tabel opslag gebruikt, moet u voor stap 4 de bewerking ' invoegen of vervangen ' gebruiken. voor stap 5 moet u een bewerking delete if exists gebruiken in de client bibliotheek die u gebruikt. Als u een ander opslag systeem gebruikt, moet u een geschikte idempotent-bewerking gebruiken.  

Als de rol werk nemer de stap 6 nooit in het diagram voltooit, wordt het bericht na een time-out opnieuw weer gegeven in de wachtrij voor de werk rollen om het opnieuw te verwerken. De rol werk nemer kan controleren hoe vaak een bericht in de wachtrij is gelezen en, indien nodig, markeren als een ' verontreinigd ' bericht voor onderzoek door dit naar een afzonderlijke wachtrij te verzenden. Zie [berichten ophalen](https://msdn.microsoft.com/library/azure/dd179474.aspx)voor meer informatie over het lezen van wachtrij berichten en het controleren van het aantal in de wachtrij.  

Sommige fouten van tabel opslag en wachtrij opslag zijn tijdelijke fouten en de client toepassing moet voldoende pogings logica bevatten om ze te kunnen verwerken.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Deze oplossing biedt geen transactie isolatie. Een client kan bijvoorbeeld de **huidige** en **Archief** tabellen lezen wanneer de rol van de werk nemer tussen stap 4-5 in het diagram ligt en een inconsistente weer gave van de gegevens te zien krijgt. De gegevens worden uiteindelijk consistent.  
* U moet er zeker van zijn dat de stappen 4-5 idempotent zijn om te zorgen voor een uiteindelijke consistentie.  
* U kunt de oplossing schalen met behulp van meerdere wachtrijen en instanties van de werkrol.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u een garantie voor de uiteindelijke consistentie tussen entiteiten die aanwezig zijn in andere partities of tabellen wilt maken. U kunt dit patroon uitbreiden om te zorgen voor mogelijke consistentie voor bewerkingen in tabel opslag en Blob-opslag, en andere niet-Azure Storage gegevens bronnen, zoals een Data Base of het bestands systeem.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Trans acties van entiteits groep](#entity-group-transactions)  
* [Samen voegen of vervangen](#merge-or-replace)  

> [!NOTE]
> Als de transactie isolatie belang rijk is voor uw oplossing, kunt u het beste uw tabellen opnieuw ontwerpen zodat u EGTs kunt gebruiken.  
> 
> 

### <a name="index-entities-pattern"></a>Patroon van index entiteiten
Onderhouden index entiteiten om in te schakelen van efficiënte zoekopdrachten die een lijst met entiteiten retourneren.  

#### <a name="context-and-problem"></a>Context en probleem
Met tabel opslag worden entiteiten automatisch geïndexeerd met behulp van de waarden `PartitionKey` en `RowKey`. Hiermee kan een client toepassing een entiteit efficiënt ophalen met behulp van een Point-query. Als u bijvoorbeeld de volgende tabel structuur gebruikt, kan een client toepassing een afzonderlijke werknemers entiteit efficiënt ophalen met behulp van de afdelings naam en de werk nemer-ID (`PartitionKey` en `RowKey`).  

![Afbeelding van entiteit van werk nemer][13]

Als u ook een lijst met werknemers entiteiten wilt kunnen ophalen op basis van de waarde van een andere niet-unieke eigenschap, zoals achternaam, moet u een minder efficiënte partitie Scan gebruiken. Met deze scan vindt u overeenkomsten, in plaats van een index te gebruiken om ze direct te bekijken. Dit komt doordat tabel opslag geen secundaire indexen biedt.  

#### <a name="solution"></a>Oplossing
Als u zoeken op achternaam wilt inschakelen met de voor gaande entiteits structuur, moet u lijsten met werk nemer-Id's onderhouden. Als u de entiteiten van werk nemers met een bepaalde achternaam wilt ophalen, bijvoorbeeld Jansen, moet u eerst de lijst met werk nemer-Id's voor werk nemers met Jansen als achternaam vinden en vervolgens die entiteiten van de werk nemer ophalen. Er zijn drie belang rijke opties voor het opslaan van de lijsten met werk nemer-Id's:  

* Gebruik Blob Storage.  
* Index entiteiten maken in dezelfde partitie als de werknemer-entiteiten.  
* Index entiteiten maken in een afzonderlijke partitie of een tabel.  

Optie 1: Blob Storage gebruiken  

Maak een BLOB voor elke unieke achternaam en sla in elke Blob een lijst op met de waarden van de `PartitionKey` (Department) en `RowKey` (werk nemer-ID) voor werk nemers die deze achternaam hebben. Wanneer u een werk nemer toevoegt of verwijdert, moet u ervoor zorgen dat de inhoud van de relevante BLOB uiteindelijk consistent is met de entiteiten van de werk nemer.  

Optie 2: index entiteiten in dezelfde partitie maken  

Gebruik index entiteiten waarin de volgende gegevens zijn opgeslagen:  

![Afbeelding van entiteit van werk nemer, met teken reeks met een lijst met werk nemer-Id's met dezelfde achternaam][14]

De eigenschap `EmployeeIDs` bevat een lijst met werk nemer-Id's voor werk nemers met de achternaam die is opgeslagen in de `RowKey`.  

In de volgende stappen wordt beschreven hoe u het proces moet volgen wanneer u een nieuwe werk nemer wilt toevoegen. In dit voor beeld voegen we een werk nemer met de ID 000152 en de laatste naam Jansen toe op de verkoop afdeling:  

1. Haal de index entiteit op met de `PartitionKey` waarde verkoop en de `RowKey` waarde Jansen. Sla de ETag van deze entiteit voor gebruik in stap 2.  
2. Maak een trans actie voor een entiteits groep (dat wil zeggen, een batch bewerking) waarmee de nieuwe werknemers entiteit (`PartitionKey` waarde "Sales" en `RowKey` waarde "000152") wordt ingevoegd en de index entiteit (`PartitionKey` waarde "Sales" en `RowKey` waarde Jansen ") wordt bijgewerkt. De EGT doet dit door de nieuwe werk nemer-ID toe te voegen aan de lijst in het veld EmployeeIDs. Zie [entiteits groeps transacties](#entity-group-transactions)voor meer informatie over EGTs.  
3. Als de EGT mislukt als gevolg van een optimistische gelijktijdigheids fout (dat wil zeggen dat iemand anders de index entiteit heeft gewijzigd), moet u beginnen met stap 1.  

U kunt een soort gelijke aanpak gebruiken om een werk nemer te verwijderen als u de tweede optie gebruikt. Het wijzigen van de achternaam van een werk nemer is iets ingewik kelder, omdat u een EGT moet uitvoeren dat drie entiteiten bijwerkt: de werknemers entiteit, de index entiteit voor de oude achternaam en de index entiteit voor de nieuwe achternaam. U moet elke entiteit ophalen voordat u wijzigingen aanbrengt, om de ETag-waarden op te halen die u vervolgens kunt gebruiken om de updates uit te voeren met behulp van optimistische gelijktijdigheid.  

De volgende stappen geven een overzicht van het proces dat u moet volgen wanneer u alle werk nemers met een bepaalde achternaam in een afdeling moet opzoeken. In dit voor beeld worden alle werk nemers met de naam Jansen op de verkoop afdeling opzoeken:  

1. Haal de index entiteit op met de `PartitionKey` waarde verkoop en de `RowKey` waarde Jansen.  
2. De lijst met Id's van werk nemers in het veld `EmployeeIDs` parseren.  
3. Als u meer informatie nodig hebt over elk van deze werk nemers (zoals hun e-mail adressen), haalt u elk van de werknemers entiteiten op met behulp van `PartitionKey` waarde ' verkoop ' en `RowKey` u waarden op uit de lijst met werk nemers die u in stap 2 hebt verkregen.  

Optie 3: index entiteiten in een afzonderlijke partitie of tabel maken  

Gebruik voor deze optie index entiteiten die de volgende gegevens bevatten:  

![Afbeelding van entiteit van werk nemer, met teken reeks met een lijst met werk nemer-Id's met dezelfde achternaam][15]

De eigenschap `EmployeeIDs` bevat een lijst met werk nemer-Id's voor werk nemers met de achternaam die is opgeslagen in de `RowKey`.  

U kunt EGTs niet gebruiken om consistentie te behouden, omdat de index entiteiten zich in een afzonderlijke partitie bevinden van de entiteiten van de werk nemer. Zorg ervoor dat de index entiteiten uiteindelijk consistent zijn met de entiteiten van de werk nemer.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Deze oplossing vereist ten minste twee query's voor het ophalen van overeenkomende entiteiten: een om de index entiteiten op te vragen om de lijst met `RowKey` waarden te verkrijgen, en vervolgens query's om elke entiteit in de lijst op te halen.  
* Omdat een afzonderlijke entiteit een maximale grootte van 1 MB heeft, is voor optie 2 en optie 3 in de oplossing aangenomen dat de lijst met werk nemer-Id's voor een bepaalde achternaam nooit meer dan 1 MB is. Als de lijst met werk nemer-Id's waarschijnlijk meer dan 1 MB groot is, gebruikt u optie 1 en slaat u de index gegevens op in Blob Storage.  
* Als u optie 2 gebruikt (met behulp van EGTs om werk nemers toe te voegen en te verwijderen en de achternaam van een werk nemer te wijzigen), moet u evalueren of het volume van de trans acties de schaalbaarheids limieten in een bepaalde partitie zal belasten. Als dit het geval is, moet u rekening houden met een uiteindelijk consistente oplossing (optie 1 of optie 3). Deze wacht rijen gebruiken om de update aanvragen af te handelen en stellen u in staat om uw index entiteiten op te slaan in een afzonderlijke partitie van de entiteiten van de werk nemer.  
* Bij optie 2 in deze oplossing wordt ervan uitgegaan dat u in een afdeling op achternaam wilt zoeken. U wilt bijvoorbeeld een lijst met werk nemers ophalen met een achternaam Jansen op de verkoop afdeling. Als u alle werk nemers met een achternaam Jansen wilt kunnen opzoeken in de hele organisatie, gebruikt u optie 1 of optie 3.
* U kunt een op een wachtrij gebaseerde oplossing implementeren die uiteindelijke consistentie biedt. Zie het [patroon uiteindelijk consistent trans acties](#eventually-consistent-transactions-pattern)voor meer informatie.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u een set entiteiten wilt opzoeken die alle een gemeen schappelijke eigenschaps waarde delen, zoals alle werk nemers met de achternaam Jansen.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Samengesteld sleutel patroon](#compound-key-pattern)  
* [Patroon uiteindelijk consistent trans acties](#eventually-consistent-transactions-pattern)  
* [Trans acties van entiteits groep](#entity-group-transactions)  
* [Werken met heterogene entiteits typen](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Denormalisatie patroon
Gerelateerde gegevens samenvoegen in één enkele entiteit waarmee u kunt om op te halen van alle gegevens die u nodig hebt met een single point-query.  

#### <a name="context-and-problem"></a>Context en probleem
In een relationele data base normaliseren we doorgaans gegevens om duplicatie te verwijderen die optreedt wanneer query's gegevens uit meerdere tabellen ophalen. Als u uw gegevens in Azure-tabellen normaliseren, moet u meerdere retouren van de client naar de server om de gerelateerde gegevens te halen. Met de volgende tabel structuur hebt u bijvoorbeeld twee retour retouren nodig om de details van een afdeling op te halen. Met één reis wordt de afdelings entiteit opgehaald die de ID van de Manager bevat, en de tweede reis haalt de details van de Manager op in een werknemers entiteit.  

![Afbeelding van afdelings entiteit en werknemers entiteit][16]

#### <a name="solution"></a>Oplossing
In plaats van het opslaan van gegevens in twee afzonderlijke entiteiten, de gegevens denormaliseren en bewaar een kopie van de manager van de gegevens in de entiteit afdeling. Bijvoorbeeld:  

![Afbeelding van de entiteit gedenormaliseerde en gecombineerde afdeling][17]

Met afdelings entiteiten die met deze eigenschappen zijn opgeslagen, kunt u nu alle informatie die u nodig hebt over een afdeling ophalen met behulp van een Point-query.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Er is enige overhead die is gekoppeld aan het opslaan van sommige gegevens twee keer kosten. Het prestatie voordeel dat voortkomt uit minder aanvragen voor tabel opslag, verlaagt doorgaans de marginale toename van de opslag kosten. Verder wordt deze kosten gedeeltelijk verrekend met een verlaging van het aantal trans acties dat u nodig hebt om de details van een afdeling op te halen.  
* U moet de consistentie van de twee entiteiten die opslaan van informatie over managers behouden. U kunt het consistentie probleem afhandelen met behulp van EGTs om meerdere entiteiten in één atomische trans actie bij te werken. In dit geval worden de afdelings-entiteit en de werknemers entiteit voor de afdelings manager in dezelfde partitie opgeslagen.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u vaak nodig hebt om te controleren of gerelateerde informatie. Dit patroon vermindert het aantal query's die de client aanbrengen moet in de gegevens die moet worden opgehaald.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Samengesteld sleutel patroon](#compound-key-pattern)  
* [Trans acties van entiteits groep](#entity-group-transactions)  
* [Werken met heterogene entiteits typen](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Samengestelde sleutel patroon
Gebruik samengestelde `RowKey` waarden om ervoor te zorgen dat een client gerelateerde gegevens opzoekt met een single point-query.  

#### <a name="context-and-problem"></a>Context en probleem
In een relationele data base is het natuurlijk om samen voegingen in query's te gebruiken om gerelateerde gegevens items te retour neren naar de client in één query. U kunt bijvoorbeeld de werk nemer-ID gebruiken om een lijst op te zoeken met gerelateerde entiteiten die de prestaties en de beoordelings gegevens voor die werk nemer bevatten.  

Stel dat u entiteiten van werk nemers opslaat in Table Storage met behulp van de volgende structuur:  

![Afbeelding van entiteit van werk nemer][18]

U moet ook historische gegevens over beoordelingen en prestaties opslaan voor elk jaar dat de werk nemer heeft gewerkt aan uw organisatie, en u moet per jaar toegang hebben tot deze informatie. Een optie is om te maken van een andere tabel waarin entiteiten met de volgende structuur:  

![Afbeelding van entiteit beoordeling van werk nemer][19]

Met deze methode kunt u ervoor kiezen om bepaalde gegevens (zoals de voor naam en achternaam) in de nieuwe entiteit te dupliceren, zodat u uw gegevens kunt ophalen met één aanvraag. U kunt echter geen sterke consistentie behouden omdat u geen EGT kunt gebruiken om de twee entiteiten atomisch bij te werken.  

#### <a name="solution"></a>Oplossing
Een nieuw entiteits type opslaan in de oorspronkelijke tabel door gebruik te maken van entiteiten met de volgende structuur:  

![Afbeelding van werknemers entiteit met samengestelde sleutel][20]

U ziet hoe de `RowKey` nu een samengestelde sleutel is, bestaande uit de werk nemer-ID en het jaar van de beoordelings gegevens. Zo kunt u de prestaties van de werk nemer ophalen en gegevens controleren met één aanvraag voor één entiteit.  

Het volgende voorbeeld bevat een overzicht van hoe u alle controle-gegevens kunt ophalen voor een bepaalde werknemer (zoals werknemer 000123 op de afdeling verkoop):  

$filter = (PartitionKey eq 'Verkoop') en (RowKey ge 'empid_000123') en (RowKey lt 'empid_000124') & $select = RowKey, Manager classificatie, classificatie van de Peer, opmerkingen  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* U moet een geschikt scheidings teken gebruiken waarmee u de `RowKey` waarde eenvoudig kunt parseren: bijvoorbeeld **000123_2012**.  
* U slaat deze entiteit ook op in dezelfde partitie als andere entiteiten die gerelateerde gegevens bevatten voor dezelfde werk nemer. Dit betekent dat u EGTs kunt gebruiken om sterke consistentie te hand haven.
* U kunt overwegen hoe vaak u de gegevens wilt opvragen om te bepalen of dit patroon geschikt is. Als u bijvoorbeeld in een regel matig toegang hebt tot de gegevens van de controle en de belangrijkste gegevens van werk nemers, moet u deze ook als afzonderlijke entiteiten houden.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u nodig hebt voor het opslaan van een of meer entiteiten die u ophaalt vaak gerelateerde.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Trans acties van entiteits groep](#entity-group-transactions)  
* [Werken met heterogene entiteits typen](#work-with-heterogeneous-entity-types)  
* [Patroon uiteindelijk consistent trans acties](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Logboek tail-patroon
Haal de *n* -entiteiten die het laatst zijn toegevoegd aan een partitie op met behulp van een `RowKey` waarde die in omgekeerde datum-en tijd volgorde wordt gesorteerd.  

> [!NOTE]
> Query resultaten die door de Azure-Table-API in Azure Cosmos DB zijn geretourneerd, worden niet gesorteerd op partitie sleutel of rij-sleutel. Dit patroon is dus geschikt voor tabel opslag en is daarom niet geschikt voor Azure Cosmos DB. Zie [verschillen tussen Table-API in azure Cosmos DB en Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)voor een gedetailleerde lijst met functie verschillen.

#### <a name="context-and-problem"></a>Context en probleem
Een algemene vereiste is om te kunnen worden opgehaald van de meest recent gemaakte entiteiten, bijvoorbeeld de tien meest recente onkosten-claims verzonden door een werknemer. Tabel query's bieden ondersteuning voor een `$top` query bewerking om de eerste *n* entiteiten van een set te retour neren. Er is geen equivalente query bewerking voor het retour neren van de laatste *n* entiteiten in een set.  

#### <a name="solution"></a>Oplossing
Sla de entiteiten op met behulp van een `RowKey` die op natuurlijke wijze in omgekeerde datum-en tijd volgorde wordt gesorteerd, zodat de meest recente vermelding altijd de eerste is in de tabel.  

Bijvoorbeeld, om te kunnen ophalen van de tien meest recente onkosten claims verzonden door een werknemer, kunt u de waarde van een omgekeerde maatstreepjes afgeleid van de huidige datum en tijd. In het C# volgende code voorbeeld ziet u één manier om een geschikte ' omgekeerde Ticks '-waarde te maken voor een `RowKey` die van de meest recente naar de oudste sorteert:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

U kunt teruggaan naar de datum/tijd-waarde met behulp van de volgende code:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

De query uitvoeren op tabel er zo uit:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* U moet de omgekeerde Tick-waarde met voorloop nullen aanvullen om ervoor te zorgen dat de teken reeks waarde op de verwachte manier wordt gesorteerd.  
* U moet rekening houden met de schaalbaarheidsdoelen op het niveau van een partitie. Wees voorzichtig met het maken van geen hot spot-partities.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u entiteiten in omgekeerde datum/tijd-volg orde wilt gebruiken of wanneer u toegang wilt krijgen tot de meest recent toegevoegde entiteiten.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Anti-patroon laten voorafgaan door/toevoegen](#prepend-append-anti-pattern)  
* [Entiteiten ophalen](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Patroon van hoge volumes verwijderen
Schakel het verwijderen van een groot aantal entiteiten in door alle entiteiten voor gelijktijdige verwijdering in hun eigen afzonderlijke tabel op te slaan. U verwijdert de entiteiten door de tabel te verwijderen.  

#### <a name="context-and-problem"></a>Context en probleem
Veel toepassingen verwijderen oude gegevens die niet langer moet beschikbaar zijn voor een clienttoepassing, of die de toepassing naar een ander opslagmedium is gearchiveerd. Normaal gesp roken identificeert u gegevens op een datum. U hebt bijvoorbeeld een vereiste om records te verwijderen van alle aanmeldings aanvragen die meer dan 60 dagen oud zijn.  

Een mogelijk ontwerp is het gebruik van de datum en tijd van de aanmeldings aanvraag in de `RowKey`:  

![Afbeelding van de entiteit aanmeldings poging][21]

Deze aanpak voor komt het partitioneren van HOTS pots, omdat de toepassing aanmeldings entiteiten kan invoegen en verwijderen voor elke gebruiker in een afzonderlijke partitie. Deze benadering kan echter kostbaar en tijdrovend zijn als u een groot aantal entiteiten hebt. Eerst moet u een tabel scan uitvoeren om te bepalen welke entiteiten moeten worden verwijderd en vervolgens moet u elke oude entiteit verwijderen. U kunt het aantal retouren naar de server die is vereist voor het verwijderen van de oude entiteiten van meerdere delete-aanvragen in EGTs batchverwerking verminderen.  

#### <a name="solution"></a>Oplossing
Een afzonderlijke tabel gebruiken voor elke dag van aanmeldingspogingen. U kunt het voor gaande entiteits ontwerp gebruiken om HOTS pots te voor komen wanneer u entiteiten invoegt. Het verwijderen van oude entiteiten is nu gewoon een kwestie van het verwijderen van één tabel elke dag (één opslag bewerking), in plaats van honderden en duizenden afzonderlijke aanmeldings entiteiten elke dag te zoeken en te verwijderen.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Ondersteunt uw ontwerp andere manieren waarop uw toepassing de gegevens gaat gebruiken, zoals het opzoeken van specifieke entiteiten, het koppelen met andere gegevens of het genereren van geaggregeerde gegevens?  
* Uw ontwerp dat hotspots voorkomen wanneer u nieuwe entiteiten wilt invoegen?  
* Verwacht een vertraging optreden als u de naam van de dezelfde tabel gebruiken wilt na het verwijderen. Is het beter om altijd te gebruiken unieke tabelnamen.  
* Er wordt een aantal frequentie beperkingen verwacht wanneer u voor het eerst een nieuwe tabel gebruikt, terwijl tabel opslag de toegangs patronen verdeelt en de partities verspreid over de knoop punten. U moet rekening houden met hoe vaak moet u nieuwe tabellen maken.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer er een groot aantal entiteiten die u op hetzelfde moment moet verwijderen.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Trans acties van entiteits groep](#entity-group-transactions)
* [Entiteiten wijzigen](#modify-entities)  

### <a name="data-series-pattern"></a>Patroon voor gegevens uit de serie
Store volledige gegevensreeks in één enkele entiteit te minimaliseren van het aantal aanvragen die u aanbrengt.  

#### <a name="context-and-problem"></a>Context en probleem
Een veelvoorkomend scenario is voor een toepassing voor het opslaan van een reeks gegevens die normaal gesproken nodig is om op te halen in één keer. Uw toepassing kan bijvoorbeeld hoeveel IM-berichten die elke werknemer elk uur verzendt vastleggen en vervolgens deze informatie gebruiken om te tekenen het aantal berichten verzonden via de voorgaande 24 uur van elke gebruiker. Een ontwerp kan voor het opslaan van 24 entiteiten voor elke werknemer zijn:  

![Afbeelding van entiteit voor bericht statistieken][22]

Met dit ontwerp kunt u eenvoudig vinden en bijwerken van de entiteit bij te werken voor elke werknemer wanneer de toepassing moet de waarde voor aantal bijwerken. Als u wilt ophalen van de informatie voor het tekenen van een grafiek van de activiteit in de voorgaande 24 uur, moet u echter 24 entiteiten ophalen.  

#### <a name="solution"></a>Oplossing
Gebruik het volgende ontwerp, met een afzonderlijke eigenschap voor het opslaan van het aantal berichten voor elk uur:  

![Afbeelding van de entiteit bericht statistieken met gescheiden eigenschappen][23]

Met dit ontwerp kunt u een samenvoeging voor het bijwerken van het aantal berichten voor een werknemer voor een bepaald uur. U kunt nu alle informatie ophalen die u nodig hebt om de grafiek uit te zetten met behulp van een aanvraag voor één entiteit.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Als uw volledige gegevens reeks niet in één entiteit past (een entiteit kan Maxi maal 252 eigenschappen hebben), gebruikt u een alternatief gegevens archief, zoals een blob.  
* Als u meerdere clients tegelijk een entiteit bijwerkt, gebruikt u **ETAG** voor het implementeren van optimistische gelijktijdigheid. Als u veel clients hebt, is het mogelijk dat u veel conflicten ondervindt.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u nodig hebt om te werken en een gegevensreeks die zijn gekoppeld aan een afzonderlijke entiteit ophalen.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Patroon voor grote entiteiten](#large-entities-pattern)  
* [Samen voegen of vervangen](#merge-or-replace)  
* [Uiteindelijk consistent transactie patroon](#eventually-consistent-transactions-pattern) (als u de gegevens reeksen in een BLOB opslaat)  

### <a name="wide-entities-pattern"></a>Breed entiteiten patroon
Meerdere fysieke entiteiten gebruiken voor het opslaan van logische entiteiten met meer dan 252 eigenschappen.  

#### <a name="context-and-problem"></a>Context en probleem
Een afzonderlijke entiteit kan niet meer dan 252 eigenschappen hebben (met uitzonde ring van de verplichte systeem eigenschappen) en kan niet meer dan 1 MB aan gegevens in totaal opslaan. In een relationele data base zou u normaal gesp roken de limieten voor de grootte van een rij omzeilen door een nieuwe tabel toe te voegen en een 1-op-1-relatie tussen hen af te dwingen.  

#### <a name="solution"></a>Oplossing
Met behulp van tabel opslag kunt u meerdere entiteiten opslaan om één groot zakelijk object met meer dan 252 eigenschappen aan te duiden. Als u bijvoorbeeld een telling wilt opslaan van het aantal IM-berichten dat elke werk nemer heeft verzonden gedurende de afgelopen 365 dagen, kunt u het volgende ontwerp gebruiken dat twee entiteiten met verschillende schema's gebruikt:  

![Afbeelding van de entiteit bericht statistieken met Rowkey 01 en de entiteit bericht statistieken met Rowkey 02][24]

Als u een wijziging aanbrengt die moet worden bijgewerkt van beide entiteiten om te voorkomen dat ze met elkaar worden gesynchroniseerd wilt, kunt u een EGT gebruiken. Anders kunt u één samenvoegbewerking bijwerken van het aantal berichten voor een specifieke dag. Als u alle gegevens voor een afzonderlijke werk nemer wilt ophalen, moet u beide entiteiten ophalen. U kunt dit doen met twee efficiënte aanvragen waarbij zowel een `PartitionKey` als een `RowKey` waarde worden gebruikt.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Houd rekening met het volgende punt bij het bepalen van het implementeren van dit patroon:  

* Bij het ophalen van een volledige logische entiteit bestaat uit ten minste twee opslagtransacties: één voor elke fysieke entiteit ophalen.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u entiteiten wilt opslaan waarvan de grootte of het aantal eigenschappen groter is dan de limieten voor een afzonderlijke entiteit in de tabel opslag.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Trans acties van entiteits groep](#entity-group-transactions)
* [Samen voegen of vervangen](#merge-or-replace)

### <a name="large-entities-pattern"></a>Patroon voor grote instanties
Gebruik Blob Storage voor het opslaan van grote eigenschaps waarden.  

#### <a name="context-and-problem"></a>Context en probleem
Een afzonderlijke entiteit kan niet meer dan 1 MB aan gegevens in totaal opslaan. Als een of meer van uw eigenschappen waarden opslaan die ervoor zorgen dat de totale grootte van uw entiteit deze waarde overschrijdt, kunt u de hele entiteit niet opslaan in de tabel opslag.  

#### <a name="solution"></a>Oplossing
Als uw entiteit groter is dan 1 MB, omdat een of meer eigenschappen een grote hoeveelheid gegevens bevatten, kunt u gegevens opslaan in Blob Storage en het adres van de BLOB vervolgens opslaan in een eigenschap in de entiteit. U kunt bijvoorbeeld de foto van een werk nemer opslaan in Blob Storage en een koppeling naar de foto opslaan in de eigenschap `Photo` van uw werknemers entiteit:  

![Afbeelding van entiteit van werk nemer met teken reeks voor foto die verwijst naar Blob Storage][25]

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Als u de uiteindelijke consistentie tussen de entiteit in tabel opslag en de gegevens in Blob Storage wilt behouden, gebruikt u het patroon met de [uiteindelijk consistente trans acties](#eventually-consistent-transactions-pattern) om uw entiteiten te onderhouden.
* Bij het ophalen van een volledige entiteit bestaat uit ten minste twee opslagtransacties: één om op te halen van de entiteit en één om op te halen van de blob-gegevens.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u entiteiten wilt opslaan waarvan de grootte de limieten voor een afzonderlijke entiteit in de tabel opslag overschrijdt.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Patroon uiteindelijk consistent trans acties](#eventually-consistent-transactions-pattern)  
* [Patroon voor grote entiteiten](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Anti-patroon toevoegen/toevoegen
Wanneer u een groot aantal toevoegingen hebt, verg root u de schaal baarheid door de toevoegingen over meerdere partities te spreiden.  

#### <a name="context-and-problem"></a>Context en probleem
Begin of u normaal gesproken entiteiten toevoegt aan uw opgeslagen entiteiten resulteert in de toepassing toe te voegen nieuwe entiteiten op de eerste of laatste partitie van een reeks van partities. In dit geval worden alle ingevoegde gegevens op een bepaald moment in dezelfde partitie geplaatst, waardoor er een hotspot wordt gemaakt. Hiermee wordt voor komen dat tabel opslag wordt toegevoegd aan taak verdeling voor meerdere knoop punten, waardoor uw toepassing mogelijk de schaalbaarheids doelen voor de partitie bereikt. Denk bijvoorbeeld aan het geval van een toepassing die netwerk-en bron toegang registreert door werk nemers. Een entiteits structuur, zoals de volgende, kan resulteren in de partitie van het huidige uur als een hotspot, als het volume van de trans acties het schaalbaarheids doel voor een afzonderlijke partitie bereikt:  

![Afbeelding van entiteit van werk nemer][26]

#### <a name="solution"></a>Oplossing
De volgende alternatieve entiteits structuur voor komt een hotspot op een bepaalde partitie, wanneer de toepassing gebeurtenissen registreert:  

![Afbeelding van de entiteit Employee met RowKey die het jaar, de maand, de dag, het uur en de gebeurtenis-ID heeft samengesteld][27]

In dit voor beeld ziet u hoe zowel de `PartitionKey` als `RowKey` samengestelde sleutels zijn. De `PartitionKey` gebruikt zowel de afdeling als de werk nemer-ID om de logboek registratie over meerdere partities te verdelen.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Biedt ondersteuning voor de alternatieve sleutel-structuur die voorkomt het maken van hot-partities op voegt efficiënt query's kunt u uw clienttoepassing?  
* Betekent uw verwachte volume aan trans acties dat u waarschijnlijk de schaalbaarheids doelen voor een afzonderlijke partitie zult bereiken en dat de tabel opslag wordt beperkt.  

#### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Vermijd het Anti-patroon laten voorafgaan door/Append wanneer uw volume aan trans acties waarschijnlijk de frequentie limiet per tabel opslag kan opleveren wanneer u een hot-partitie opent.  

#### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Samengesteld sleutel patroon](#compound-key-pattern)  
* [Eind patroon van logboek](#log-tail-pattern)  
* [Entiteiten wijzigen](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Logboek gegevens anti-patroon
Normaal gesp roken moet u Blob Storage gebruiken in plaats van tabel opslag om logboek gegevens op te slaan.  

#### <a name="context-and-problem"></a>Context en probleem
Een veelvoorkomende use-case voor logboek gegevens is het ophalen van een selectie logboek vermeldingen voor een specifiek datum-en tijds bereik. U wilt bijvoorbeeld alle fout-en kritieke berichten zoeken die uw toepassing heeft geregistreerd tussen 15:04 en 15:06 op een specifieke datum. U wilt de datum en tijd van het logboek bericht niet gebruiken om te bepalen met welke partitie u logboek entiteiten opslaat. Dit resulteert in een hot partitie, omdat alle logboek entiteiten op een bepaald moment dezelfde `PartitionKey` waarde delen (Zie het [Anti-patroon laten voorafgaan door/Append](#prepend-append-anti-pattern)). Het volgende entiteits schema voor een logboek bericht resulteert bijvoorbeeld in een warme partitie, omdat de toepassing alle logboek berichten voor de huidige datum en tijd naar de partitie schrijft:  

![Afbeelding van entiteit van het logboek bericht][28]

In dit voor beeld bevat de `RowKey` de datum en tijd van het logboek bericht om ervoor te zorgen dat logboek berichten worden gesorteerd in de volg orde van datum/tijd. Het `RowKey` bevat ook een bericht-ID, in het geval dat meerdere logboek berichten dezelfde datum en tijd delen.  

Een andere benadering is het gebruik van een `PartitionKey` die ervoor zorgt dat de toepassing berichten schrijft over diverse partities. Als de bron van het logboek bericht bijvoorbeeld een manier biedt om berichten over meerdere partities te verdelen, kunt u het volgende entiteits schema gebruiken:  

![Afbeelding van entiteit van het logboek bericht][29]

Het probleem met dit schema is echter dat om alle logboek berichten op te halen voor een specifieke tijds Panne, u moet elke partitie in de tabel doorzoeken.

#### <a name="solution"></a>Oplossing
In de vorige sectie is het probleem gemarkeerd met het gebruik van Table-opslag voor het opslaan van logboek vermeldingen en suggesties voor twee onbevredigende ontwerpen. Een oplossing heeft geleid tot een hot partitie met het risico op slechte prestaties logboek berichten te schrijven. De andere oplossing resulteerde in slechtere query prestaties vanwege de vereiste om elke partitie in de tabel te scannen om logboek berichten op te halen voor een bepaalde periode. Blob Storage biedt een betere oplossing voor dit type scenario. Dit is de manier waarop Azure Storage Analytics de logboek gegevens opslaat die worden verzameld.  

In deze sectie wordt beschreven hoe opslag analyse logboek gegevens opslaat in Blob Storage, als illustratie van deze benadering om gegevens op te slaan die u doorgaans doorzoekt op bereik.  

Opslag analyse slaat logboek berichten op in een indeling met scheidings tekens in meerdere blobs. De indeling scheidingstekens kunt eenvoudig een clienttoepassing parseren van de gegevens in het logboekbericht.  

Storage Analytics maakt gebruik van een naamgevings Conventie voor blobs waarmee u de BLOB (of blobs) kunt vinden die de logboek berichten bevat waarvoor u zoekt. Een blob met de naam Queue/2014/07/31/1800/000001. log bevat bijvoorbeeld logboek berichten die betrekking hebben op de wachtrij service voor het uur, beginnend bij 18:00 op 31 juli 2014. De '000001' geeft aan dat dit het eerste logboekbestand voor deze periode. Storage Analytics registreert ook de tijds tempels van de eerste en laatste logboek berichten die in het bestand zijn opgeslagen als onderdeel van de meta gegevens van de blob. Met de API voor Blob Storage kunt u blobs in een container vinden op basis van een naam voorvoegsel. U kunt het voor voegsel ' queue/2014/07/31/1800 ' gebruiken om alle blobs te vinden die wachtrij logboek gegevens bevatten voor het uur vanaf 18:00.  

Opslag analyse buffers registreert intern logboek berichten, werkt de juiste BLOB regel matig bij of maakt een nieuwe met de meest recente batch logboek vermeldingen. Dit beperkt het aantal schrijf bewerkingen dat moet worden uitgevoerd op de Blob-opslag.  

Als u een soort gelijke oplossing in uw eigen toepassing implementeert, kunt u overwegen hoe u de afweging tussen betrouw baarheid en kosten en schaal baarheid beheert. Met andere woorden, Controleer het effect van het schrijven van elke logboek vermelding naar Blob-opslag, vergeleken met het bufferen van updates in uw toepassing en het schrijven naar Blob Storage in batches.  

#### <a name="issues-and-considerations"></a>Problemen en overwegingen
Houd rekening met de volgende punten bij het bepalen hoe gegevens worden opgeslagen:  

* Als u een tabel ontwerp maakt waarmee mogelijke Hot-partities worden voor komen, is het mogelijk dat u geen efficiënt toegang hebt tot uw logboek gegevens.  
* Voor het verwerken van logboekgegevens, moet een client vaak veel records laden.  
* Hoewel logboek gegevens vaak worden gestructureerd, is het mogelijk dat Blob Storage een betere oplossing is.  

### <a name="implementation-considerations"></a>Afwegingen bij de implementatie
In deze sectie worden enkele van de overwegingen op moet letten wanneer u de patronen die worden beschreven in de vorige secties implementeert beschreven. De meeste van deze sectie bevat voorbeelden die zijn geschreven in C# en die gebruikmaken van de Storage-clientbibliotheek (versie 4.3.0 op het moment van schrijven).  

### <a name="retrieve-entities"></a>Entiteiten ophalen
Zoals beschreven in het sectie [ontwerp voor het uitvoeren van query's](#design-for-querying), is de meest efficiënte query een Point-query. In sommige scenario's moet u echter mogelijk meerdere entiteiten ophalen. In deze sectie worden enkele veelvoorkomende benaderingen beschreven om entiteiten op te halen met behulp van de Storage-client bibliotheek.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Een punt query uitvoeren met de Storage-client bibliotheek
De eenvoudigste manier om een punt query uit te voeren, is met behulp van de bewerking tabel **ophalen** . Zoals weer gegeven in het C# volgende code fragment, haalt deze bewerking een entiteit op met een `PartitionKey` van de waarde ' Sales ' en een `RowKey` van de waarde ' 212 ':  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

U ziet hoe in dit voor beeld wordt verwacht dat de entiteit die wordt opgehaald, van het type `EmployeeEntity`is.  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Meerdere entiteiten ophalen met behulp van LINQ
U kunt meerdere entiteiten ophalen door gebruik te maken van LINQ met Storage-client bibliotheek en een query op te geven met een **where** -component. Als u een tabel scan wilt voor komen, moet u altijd de `PartitionKey` waarde in de WHERE-component toevoegen, en indien mogelijk de `RowKey` waarde om te voor komen dat er tabellen en partities worden gescand. Tabel opslag ondersteunt een beperkt aantal vergelijkings operatoren (groter dan, groter dan of gelijk aan, kleiner dan, kleiner dan of gelijk aan, gelijk aan en niet gelijk aan) voor gebruik in de component WHERE. In het C# volgende code fragment vindt u alle werk nemers waarvan de achternaam begint met ' B ' (ervan uitgaande dat de `RowKey` de achternaam opslaat) op de verkoop afdeling (ervan uitgaande dat de `PartitionKey` de naam van de afdeling opslaat):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

U ziet hoe in de query zowel een `RowKey` als een `PartitionKey` wordt opgegeven om de prestaties te verbeteren.  

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
> Het voor beeld nest meerdere `CombineFilters` methoden om de drie filter voorwaarden op te nemen.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Grote aantallen entiteiten ophalen uit een query
Een optimale query retourneert een afzonderlijke entiteit op basis van een `PartitionKey` waarde en een `RowKey` waarde. In sommige gevallen kan het echter nodig zijn om veel entiteiten uit dezelfde partitie te retour neren, of zelfs van een groot aantal partities. U moet de prestaties van uw toepassing altijd volledig hebt getest in dergelijke scenario's.  

Een query op tabel opslag kan Maxi maal 1.000 entiteiten tegelijk retour neren, en gedurende een periode van Maxi maal vijf seconden worden uitgevoerd. Tabel opslag retourneert een vervolg token om de client toepassing in staat te stellen de volgende set entiteiten aan te vragen, als een van de volgende voor waarden waar is:

- De resultatenset bevat meer dan 1.000 entiteiten.
- De query is niet binnen vijf seconden voltooid.
- De query snijdt de partitie grens. 

Zie querytime [-out en paginering](https://msdn.microsoft.com/library/azure/dd135718.aspx)voor meer informatie over de werking van vervolg tokens.  

Als u de Storage-client bibliotheek gebruikt, kan het automatisch vervolg tokens voor u afhandelen terwijl er entiteiten uit de tabel opslag worden geretourneerd. Het volgende C# code voorbeeld verwerkt bijvoorbeeld automatisch vervolg tokens als de tabel opslag deze retourneert in een antwoord:  

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

De volgende C#-code wordt expliciet voortzetting van tokens verwerkt:  

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

Met behulp van expliciet voortzetting van tokens, kunt u bepalen wanneer het volgende segment van de gegevens in uw toepassing worden opgehaald. Als uw client toepassing bijvoorbeeld gebruikers in staat stelt om de entiteiten die zijn opgeslagen in een tabel te bladeren, kan een gebruiker besluiten niet alle entiteiten die door de query zijn opgehaald door te bladeren. Uw toepassing gebruikt alleen een vervolg token om het volgende segment op te halen wanneer de gebruiker klaar was met het pagineren van alle entiteiten in het huidige segment. Deze benadering heeft verschillende voordelen:  

* U kunt de hoeveelheid gegevens die moet worden opgehaald uit de tabel opslag beperken en die u via het netwerk verplaatst.  
* U kunt asynchrone I/O's uitvoeren in .NET.  
* U kunt het vervolg token serialiseren naar permanente opslag, zodat u kunt door gaan in het geval een toepassing vastloopt.  

> [!NOTE]
> Een vervolg token retourneert meestal een segment met 1.000 entiteiten, hoewel het minder kan bevatten. Dit is ook het geval als u het aantal vermeldingen dat een query retourneert, beperkt door **gebruik te maken van** de eerste n entiteiten die overeenkomen met uw zoek criteria. Tabel opslag retourneert mogelijk een segment met minder dan n entiteiten, samen met een vervolg token om u in staat te stellen de resterende entiteiten op te halen.  
> 
> 

De volgende C#-code laat zien hoe het aantal entiteiten geretourneerd binnen een segment wijzigen:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Server-side-projectie
Een enkele entiteit kan maximaal 255 eigenschappen hebben en maximaal 1 MB in grootte. Wanneer u een query uitvoert op de tabel en entiteiten ophaalt, hebt u mogelijk niet alle eigenschappen nodig en kunt u voor komen dat gegevens onnodig worden overgedragen (om latentie en kosten te beperken). U kunt de serverzijde projectie gebruiken om over te dragen alleen de eigenschappen die u nodig hebt. In het volgende voor beeld wordt alleen de eigenschap `Email` (samen met `PartitionKey`, `RowKey`, `Timestamp`en `ETag`) opgehaald uit de entiteiten die zijn geselecteerd door de query.  

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

U ziet hoe de `RowKey` waarde beschikbaar is, hoewel deze niet is opgenomen in de lijst met eigenschappen die moeten worden opgehaald.  

### <a name="modify-entities"></a>Entiteiten wijzigen
Met de Storage-client bibliotheek kunt u uw entiteiten die zijn opgeslagen in tabel opslag, wijzigen door entiteiten in te voegen, te verwijderen en bij te werken. U kunt EGTs gebruiken om meerdere invoeg-, bijwerk-en verwijder bewerkingen tegelijk uit te kunnen drukken, om het aantal benodigde retouren te verminderen en de prestaties van uw oplossing te verbeteren.  

Uitzonde ringen die worden gegenereerd wanneer de Storage-client bibliotheek een EGT uitvoert, bevatten doorgaans de index van de entiteit die de oorzaak van de batch heeft veroorzaakt. Dit is handig wanneer u de code die gebruikmaakt van EGTs foutopsporing.  

U moet ook overwegen hoe uw ontwerp is van invloed op hoe uw clienttoepassing omgaat met gelijktijdigheid en update-bewerkingen.  

#### <a name="managing-concurrency"></a>Gelijktijdigheid beheren
Standaard worden door tabel opslag optimistische gelijktijdigheid controles geïmplementeerd op het niveau van afzonderlijke entiteiten voor INSERT-, merge-en delete-bewerkingen, hoewel het mogelijk is dat een client tabel opslag geforceerd afdwingt om deze controles over te slaan. Zie voor meer informatie [gelijktijdigheid beheren in Microsoft Azure Storage](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Samenvoegen of vervangen
De methode `Replace` van de klasse `TableOperation` vervangt altijd de volledige entiteit in tabel opslag. Als u in de aanvraag geen eigenschap opneemt wanneer die eigenschap in de opgeslagen entiteit bestaat, wordt die eigenschap door de aanvraag uit de opgeslagen entiteit verwijderd. Tenzij u expliciet verwijderen van een eigenschap van een opgeslagen entiteit wilt, moet u elke eigenschap opnemen in de aanvraag.  

U kunt de methode `Merge` van de klasse `TableOperation` gebruiken om de hoeveelheid gegevens die u naar de tabel opslag verzendt, te verminderen wanneer u een entiteit wilt bijwerken. De `Merge`-methode vervangt alle eigenschappen in de opgeslagen entiteit door eigenschaps waarden van de entiteit die in de aanvraag is opgenomen. Deze methode zorgt ervoor dat alle eigenschappen in de opgeslagen entiteit intact blijven die niet zijn opgenomen in de aanvraag. Dit is handig als u grote entiteiten hebt en alleen een klein aantal eigenschappen in een aanvraag hoeft bij te werken.  

> [!NOTE]
> De methoden `*Replace` en `Merge` mislukken als de entiteit niet bestaat. Als alternatief kunt u de methoden `InsertOrReplace` en `InsertOrMerge` gebruiken om een nieuwe entiteit te maken als deze niet bestaat.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Werken met heterogene entiteits typen
Table Storage is een *schema-less Table-* archief. Dit betekent dat een enkele tabel entiteiten van meerdere typen kan opslaan, waardoor uw ontwerp een grote flexibiliteit biedt. Het volgende voorbeeld wordt een tabel zowel werknemers als afdeling entiteiten opslaan:  

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

Elke entiteit moet nog steeds `PartitionKey`, `RowKey`en `Timestamp` waarden bevatten, maar kan een wille keurige set eigenschappen hebben. Bovendien is er niets om het type van een entiteit aan te geven, tenzij u ervoor kiest om die informatie ergens op te slaan. Er zijn twee opties voor het identificeren van het entiteitstype:  

* Laten voorafgaan door het entiteits type naar het `RowKey` (of mogelijk de `PartitionKey`). Bijvoorbeeld `EMPLOYEE_000123` of `DEPARTMENT_SALES` als `RowKey` waarden.  
* Gebruik een afzonderlijke eigenschap voor het vastleggen van het entiteits type, zoals weer gegeven in de volgende tabel.  

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

De eerste optie, in afwachting van het entiteits type voor de `RowKey`, is handig als er sprake is van een mogelijkheid dat twee entiteiten van verschillende typen dezelfde sleutel waarde hebben. Deze groepen ook entiteiten van hetzelfde type samen in de partitie.  

De technieken die in deze sectie worden beschreven, zijn vooral relevant voor de discussie over[overname relaties](#inheritance-relationships).  

> [!NOTE]
> Overweeg het opnemen van een versie nummer in de waarde van het entiteits type om client toepassingen in staat te stellen POCO-objecten te ontwikkelen en te werken met andere versies.  
> 
> 

De rest van deze sectie beschrijft een aantal van de functies in de Storage-clientbibliotheek werken met meerdere Entiteitstypen in dezelfde tabel kunnen.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Heterogene entiteits typen ophalen
Als u de Storage-client bibliotheek gebruikt, hebt u drie opties voor het werken met meerdere entiteits typen.  

Als u weet welk type entiteit is opgeslagen met specifieke `RowKey` en `PartitionKey` waarden, kunt u het entiteits type opgeven wanneer u de entiteit ophaalt. U hebt dit gezien in de vorige twee voor beelden waarmee entiteiten van het type `EmployeeEntity`worden opgehaald: [Voer een punt query uit met behulp van de Storage-client bibliotheek](#run-a-point-query-by-using-the-storage-client-library) en [Haal meerdere entiteiten op met behulp van LINQ](#retrieve-multiple-entities-by-using-linq).  

De tweede optie is het `DynamicTableEntity` type (een eigenschappen verzameling) te gebruiken in plaats van een concreet POCO-entiteits type. Deze optie kan ook de prestaties verbeteren, omdat u de entiteit niet hoeft te serialiseren en deserialiseren naar .NET-typen. Met de C# volgende code worden mogelijk meerdere entiteiten van verschillende typen uit de tabel opgehaald, maar worden alle entiteiten als `DynamicTableEntity` instanties geretourneerd. Vervolgens wordt de eigenschap `EntityType` gebruikt om het type van elke entiteit te bepalen:  

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

Als u andere eigenschappen wilt ophalen, moet u de methode `TryGetValue` gebruiken voor de eigenschap `Properties` van de `DynamicTableEntity`-klasse.  

Een derde optie is om te combi neren met behulp van het `DynamicTableEntity` type en een `EntityResolver`-exemplaar. Hiermee kunt u omzetten in meerdere POCO-typen in dezelfde query. In dit voor beeld gebruikt de gemachtigde van de `EntityResolver` de `EntityType`-eigenschap om onderscheid te maken tussen de twee typen entiteiten die door de query worden geretourneerd. De `Resolve` methode maakt gebruik van de `resolver` gemachtigde om `DynamicTableEntity` instanties te herleiden tot `TableEntity` exemplaren.  

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

#### <a name="modify-heterogeneous-entity-types"></a>Heterogene entiteits typen wijzigen
U hoeft niet te weten welk type entiteit u wilt verwijderen en u weet altijd het type van een entiteit wanneer u deze invoegt. U kunt echter het `DynamicTableEntity` type gebruiken om een entiteit bij te werken zonder het type te weten en zonder een POCO-entiteits klasse te gebruiken. Met het volgende code voorbeeld wordt één entiteit opgehaald en wordt gecontroleerd of de eigenschap `EmployeeCount` bestaat voordat deze wordt bijgewerkt.  

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

### <a name="control-access-with-shared-access-signatures"></a>Toegang beheren met hand tekeningen voor gedeelde toegang
U kunt SAS-tokens (Shared Access Signature) gebruiken om client toepassingen toe te staan om tabel entiteiten rechtstreeks te wijzigen (en query), zonder dat u rechtstreeks hoeft te verifiëren met tabel opslag. Er zijn drie belangrijke voordelen voor het gebruik van SAS in uw toepassing:  

* U hoeft uw opslag account niet te distribueren naar een niet-beveiligd platform (zoals een mobiel apparaat) om het apparaat in tabel opslag toegang te geven tot entiteiten en deze te wijzigen.  
* U kunt een deel van het werk dat web-en werk rollen uitvoeren in uw entiteiten beheren. U kunt offloaden naar client apparaten zoals computers en mobiele apparaten van eind gebruikers.  
* U kunt een beperkte en tijdgebonden set machtigingen toewijzen aan een client (zoals alleen-lezen toegang toestaan voor specifieke resources).  

Zie [using Shared Access signatures (SAS) (Engelstalig)](../storage/common/storage-dotnet-shared-access-signature-part-1.md)voor meer informatie over het gebruik van SAS-tokens met table-opslag.  

U moet echter nog steeds de SAS-tokens genereren die een client toepassing verlenen aan de entiteiten in tabel opslag. Doe dit in een omgeving met beveiligde toegang tot de sleutels van uw opslag account. Meestal gebruikt u een web- of worker-rol voor het genereren van de SAS-tokens en levert u deze aan de clienttoepassingen die toegang nodig tot uw entiteiten. Aangezien er nog steeds een overhead die betrokken zijn bij het genereren en het leveren van SAS-tokens aan clients, kunt u overwegen het beste aan deze overhead, met name in scenario's met hoge volumes verminderen.  

Het is mogelijk om een SAS-token te genereren dat toegang verleent tot een subset van de entiteiten in een tabel. Standaard maakt u een SAS-token voor een volledige tabel. Het is echter ook mogelijk om op te geven dat het SAS-token toegang verleent tot een bereik van `PartitionKey` waarden, of een bereik van `PartitionKey` en `RowKey` waarden. U kunt ervoor kiezen om SAS-tokens te genereren voor afzonderlijke gebruikers van uw systeem, zodat de SAS-token van elke gebruiker alleen toegang tot hun eigen entiteiten in de tabel opslag toestaat.  

### <a name="asynchronous-and-parallel-operations"></a>Asynchrone en parallelle bewerkingen
Indien u uw aanvragen over meerdere partities spreiden bent, kunt u de doorvoer en client reactiesnelheid met behulp van asynchrone of parallelle query's verbeteren.
Bijvoorbeeld, mogelijk hebt u twee of meer instanties van de werkrol toegang krijgen tot uw tabellen parallel. U kunt afzonderlijke worker-rollen hebben die verantwoordelijk zijn voor bepaalde sets van partities of alleen meerdere rolinstanties hebben, die elk toegang hebben tot alle partities in een tabel.  

Binnen een client exemplaar kunt u de door Voer verbeteren door opslag bewerkingen asynchroon uit te voeren. De Storage-clientbibliotheek kunt eenvoudig schrijven asynchrone query's en wijzigingen. U kunt bijvoorbeeld beginnen met de synchrone methode waarmee alle entiteiten in een partitie worden opgehaald, zoals wordt weer gegeven in de volgende C# code:  

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

U kunt deze code eenvoudig aanpassen zodat de query asynchroon wordt uitgevoerd, als volgt:  

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

In dit voorbeeld asynchrone ziet u de volgende wijzigingen van de synchrone versie:  

* De methode handtekening bevat nu de `async` modificator en retourneert een `Task` exemplaar.  
* In plaats van de `ExecuteSegmented`-methode aan te roepen om de resultaten op te halen, roept de methode nu de `ExecuteSegmentedAsync`-methode aan. De methode maakt gebruik van de `await` modificator om resultaten asynchroon op te halen.  

De client toepassing kan deze methode meerdere keren aanroepen met verschillende waarden voor de para meter `department`. Elke query wordt uitgevoerd op een afzonderlijke thread.  

Er is geen asynchrone versie van de `Execute` methode in de `TableQuery`-klasse, omdat de `IEnumerable` interface geen ondersteuning biedt voor asynchrone inventarisatie.  

U kunt ook invoegen, bijwerken en verwijderen van entiteiten asynchroon. De volgende C#-voorbeeld ziet u een eenvoudige, synchrone methode voor het invoegen of vervangen van een werknemer entiteit:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

U kunt deze code eenvoudig wijzigen zodat de update asynchroon wordt uitgevoerd, als volgt:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

In dit voorbeeld asynchrone ziet u de volgende wijzigingen van de synchrone versie:  

* De methode handtekening bevat nu de `async` modificator en retourneert een `Task` exemplaar.  
* In plaats van de `Execute`-methode aan te roepen om de entiteit bij te werken, roept de methode nu de `ExecuteAsync`-methode aan. De methode maakt gebruik van de `await` modificator om resultaten asynchroon op te halen.  

De client toepassing kan meerdere asynchrone methoden, zoals deze, aanroepen en elke methode aanroep wordt uitgevoerd in een afzonderlijke thread.  


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

