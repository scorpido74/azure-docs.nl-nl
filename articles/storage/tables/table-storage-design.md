---
title: Ontwerp schaal bare en uitvoerende tabellen in azure Table Storage. | Microsoft Docs
description: Ontwerp schaal bare en uitvoerende tabellen in azure Table Storage.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 03/09/2020
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 8df639eea757c374554fa19e57c43cef79308e98
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255143"
---
# <a name="design-scalable-and-performant-tables"></a>Schaalbare en beter bruikbare tabellen ontwerpen

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Als u schaal bare en uitvoerende tabellen wilt ontwerpen, moet u rekening houden met factoren zoals prestaties, schaal baarheid en kosten. Als u eerder schema's hebt ontworpen voor relationele data bases, zijn deze overwegingen bekend, maar hoewel er wat overeenkomsten zijn tussen het Azure Table service-opslag model en relationele modellen, zijn er ook belang rijke verschillen. Deze verschillen leiden doorgaans tot verschillende ontwerpen waarvan het prestatie meter item intuïtief of onjuist kan zijn voor iemand die vertrouwd is met relationele data bases, maar duidelijk is als u ontwerpt voor een NoSQL sleutel/waarde-archief zoals Azure Table service. Veel van uw ontwerp verschillen geven aan dat de Table service is ontworpen ter ondersteuning van Cloud toepassingen die miljarden entiteiten (of rijen in relationele data base terminologie) van gegevens bevatten of voor gegevens sets die hoge trans acties moeten ondersteunen omvang. Daarom moet u nadenken over de manier waarop u uw gegevens opslaat en begrijpt hoe de Table service werkt. Met een goed ontworpen NoSQL-gegevens archief kan uw oplossing veel verder worden geschaald en tegen lagere kosten dan een oplossing die gebruikmaakt van een relationele data base. Deze handleiding helpt u bij deze onderwerpen.  

## <a name="about-the-azure-table-service"></a>Over de Azure Table-service
Deze sectie worden enkele van de belangrijkste functies van de Table-service die vooral relevant zijn voor het ontwerpen voor prestaties en schaalbaarheid. Als u geen ervaring hebt met Azure Storage en de Table service, lees dan eerst [Inleiding tot Microsoft Azure Storage](../../storage/common/storage-introduction.md) en aan de [slag met Azure Table Storage met .net voordat u](../../cosmos-db/table-storage-how-to-use-dotnet.md) de rest van dit artikel leest. Hoewel de focus van deze hand leiding zich op het Table service bevindt, bevat het een bespreking van de Azure-wachtrij en BLOB-Services, en hoe u deze kunt gebruiken met de Table service.  

Wat is de Table-service? Als u van de naam verwacht, de tabelservice tabelvorm gebruikt voor het opslaan van gegevens. Staat elke rij van de tabel voor een entiteit in de standard-terminologie en de kolommen opslaan van de verschillende eigenschappen van die entiteit. Elke entiteit heeft een paar sleutels om deze uniek te identificeren en een time stamp-kolom die de Table service gebruikt om bij te houden wanneer de entiteit voor het laatst is bijgewerkt. De tijds tempel wordt automatisch toegepast en u kunt de tijds tempel niet hand matig overschrijven met een wille keurige waarde. De Table-service maakt gebruik van deze timestamp laatst gewijzigd (LMT) voor het beheren van optimistische gelijktijdigheid.  

> [!NOTE]
> De Table service-REST API bewerkingen retour neren ook een **ETAG** -waarde die is afgeleid van de LMT. In dit document worden de termen ETag en LMT door elkaar gebruikt omdat ze naar dezelfde onderliggende gegevens verwijzen.  
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


Tot nu toe worden deze gegevens weer gegeven zoals in een tabel in een relationele data base, met de belangrijkste verschillen tussen de verplichte kolommen en de mogelijkheid om meerdere entiteits typen op te slaan in dezelfde tabel. Daarnaast heeft elk van de door de gebruiker gedefinieerde eigenschappen, zoals **FirstName** of **Age** , een gegevens type, zoals geheel getal of teken reeks, net als een kolom in een relationele data base. Hoewel in tegenstelling tot in een relationele database, de zonder schema aard van de Table-service betekent dat een eigenschap niet hetzelfde gegevenstype voor elke entiteit moet hebben. Voor het opslaan van complexe gegevenstypen in één eigenschap, moet u een geserialiseerde indeling zoals JSON of XML. Zie [inzicht in het tabel service gegevens model](https://msdn.microsoft.com/library/azure/dd179338.aspx)voor meer informatie over de tabel service, zoals ondersteunde gegevens typen, ondersteunde datumbereiken, naamgevings regels en grootte beperkingen.

Uw keuze van **PartitionKey** en **RowKey** is fundamenteel voor een goed ontwerp van een tabel. Elke entiteit die is opgeslagen in een tabel moet een unieke combi natie van **PartitionKey** en **RowKey**hebben. Net als bij sleutels in een relationele-database tabel worden de waarden **PartitionKey** en **RowKey** geïndexeerd voor het maken van een geclusterde index voor het inschakelen van snelle vormgeving. De Table service maakt echter geen secundaire indexen, dus **PartitionKey** en **RowKey** zijn de enige geïndexeerde eigenschappen. Sommige van de patronen die in [tabel ontwerp patronen](table-storage-design-patterns.md) worden beschreven, illustreren hoe u deze zicht bare beperking kunt omzeilen.  

Een tabel bestaat uit een of meer partities, en veel van de ontwerp beslissingen die u aanbrengt, zijn een geschikte **PartitionKey** en **RowKey** om uw oplossing te optimaliseren. Een oplossing kan bestaan uit één tabel die al uw entiteiten bevat, ingedeeld in partities, maar een oplossing heeft meestal meerdere tabellen. Tabellen kunt u logisch ordenen van uw entiteiten, helpen bij het beheren van toegang tot de gegevens met behulp van toegangsbeheerlijsten en u kunt een hele tabel met behulp van een enkele opslagbewerking neerzetten.  

## <a name="table-partitions"></a>Tabelpartities
De account naam, tabel naam en **PartitionKey** bepalen samen de partitie in de opslag service waar de tabel service de entiteit opslaat. En als onderdeel van het adresserings schema voor entiteiten definiëren partities een bereik voor [trans acties (zie hieronder](#entity-group-transactions) ) en vormen de basis van de manier waarop de tabel service wordt geschaald. Zie voor meer informatie over partities de [controle lijst voor prestaties en schaal baarheid voor tabel opslag](storage-performance-checklist.md).  

In de Table service, een afzonderlijk knoop punt Services een of meer volledige partities en de service wordt geschaald door dynamische Load Balancing-partities tussen knoop punten. Als een knoop punt wordt geladen, kan de Table-service het bereik van partities dat door dat knoop punt wordt verwerkt, *splitsen* op verschillende knoop punten. Wanneer er verkeer wordt ondergebracht, kan de-service de partitielay-bereiken van de Stille knoop punten weer *samen voegen* op een enkel knoop punt.  

Zie het artikel [Microsoft Azure Storage: een Maxi maal beschik bare Cloud opslag service met sterke consistentie](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)voor meer informatie over de interne Details van de Table service, met name hoe de service partities beheert.  

## <a name="entity-group-transactions"></a>Entiteit-groepstransacties
Entiteit-groepstransacties (EGTs) zijn in de tabel-service, het enige ingebouwde mechanisme voor het uitvoeren van atomic updates voor meerdere entiteiten. EGTs worden soms ook wel batch- *trans acties*genoemd. EGTs kan alleen worden gebruikt voor entiteiten die zijn opgeslagen in dezelfde partitie (dat wil zeggen, delen dezelfde partitie sleutel in een bepaalde tabel). U moet er dus altijd voor zorgen dat deze entiteiten zich in dezelfde partitie bevinden, zodat u een Atomic-transactioneel gedrag voor meerdere entiteiten nodig hebt. Dit is vaak een reden voor het bewaren van meerdere Entiteitstypen in dezelfde tabel (en de partitie) en meerdere tabellen voor verschillende Entiteitstypen niet gebruiken. Een enkele EGT kan worden uitgevoerd op maximaal 100 entiteiten.  Als u meerdere gelijktijdige EGTs voor verwerking verzendt, is het belang rijk om ervoor te zorgen dat deze EGTs niet worden uitgevoerd op entiteiten die gemeen schappelijk zijn voor EGTs; anders kan de verwerking worden vertraagd.

Met EGTs kunt u ook een mogelijke trans actie in uw ontwerp introduceren. Dat wil zeggen dat het gebruik van meer partities de schaal baarheid van uw toepassing verg root, omdat Azure meer mogelijkheden heeft voor taak verdeling van aanvragen op verschillende knoop punten. Maar het gebruik van meer partities kan de mogelijkheid van uw toepassing beperken tot het uitvoeren van atomische trans acties en het behouden van sterke consistentie voor uw gegevens. Daarnaast zijn er specifieke schaalbaarheids doelen op het niveau van een partitie die de door Voer van trans acties die u voor één knoop punt kan verwachten, kunnen beperken. Zie [schaalbaarheids doelen voor standaard opslag accounts](../common/scalability-targets-standard-account.md)voor meer informatie over de schaalbaarheids doelen voor Azure Standard-opslag accounts. Zie [schaalbaarheids-en prestatie doelen voor Table Storage](scalability-targets.md)voor meer informatie over de schaalbaarheids doelen voor de Table service.

## <a name="capacity-considerations"></a>Overwegingen voor capaciteit

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="cost-considerations"></a>Kostenoverwegingen
Table Storage is relatief goed koop, maar u moet kosten ramingen voor zowel het capaciteits gebruik als het aantal trans acties opnemen als onderdeel van de evaluatie van een Table service oplossing. In veel gevallen is het opslaan van gedenormaliseerde of dubbele gegevens voor het verbeteren van de prestaties of schaal baarheid van uw oplossing een geldige benadering. Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over prijzen.  

## <a name="next-steps"></a>Volgende stappen

- [Tabel ontwerp patronen](table-storage-design-patterns.md)
- [Model relaties](table-storage-design-modeling.md)
- [Ontwerpen voor het uitvoeren van query's](table-storage-design-for-query.md)
- [Tabel gegevens versleutelen](table-storage-design-encrypt-data.md)
- [Ontwerp voor gegevens aanpassing](table-storage-design-for-modification.md)
