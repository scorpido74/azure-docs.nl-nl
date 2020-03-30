---
title: Ontwerp schaalbare en performante tabellen in Azure-tabelopslag. | Microsoft Docs
description: Ontwerp schaalbare en performante tabellen in Azure-tabelopslag.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 03/09/2020
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 8df639eea757c374554fa19e57c43cef79308e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255143"
---
# <a name="design-scalable-and-performant-tables"></a>Schaalbare en beter bruikbare tabellen ontwerpen

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Als u schaalbare en performante tabellen wilt ontwerpen, moet u rekening houden met factoren zoals prestaties, schaalbaarheid en kosten. Als u eerder schema's voor relationele databases hebt ontworpen, zijn deze overwegingen bekend, maar hoewel er enkele overeenkomsten zijn tussen het Azure Table-serviceopslagmodel en relationele modellen, zijn er ook belangrijke verschillen. Deze verschillen leiden meestal tot verschillende ontwerpen die er contra-intuïtief of fout uitzien voor iemand die bekend is met relationele databases, maar zinvol zijn als u ontwerpt voor een NoSQL-sleutel/waardearchief, zoals de Azure Table-service. Veel van uw ontwerpverschillen weerspiegelen het feit dat de Table-service is ontworpen om cloudtoepassingen te ondersteunen die miljarden entiteiten (of rijen in relationele databaseterminologie) van gegevens kunnen bevatten of voor gegevenssets die hoge transacties moeten ondersteunen Volumes. Daarom moet u anders denken over hoe u uw gegevens opslaat en begrijpen hoe de Table-service werkt. Een goed ontworpen NoSQL-gegevensarchief kan uw oplossing in staat stellen om veel verder en tegen lagere kosten te schalen dan een oplossing die een relationele database gebruikt. Deze gids helpt u met deze onderwerpen.  

## <a name="about-the-azure-table-service"></a>Informatie over de Azure Table-service
In dit gedeelte worden enkele van de belangrijkste functies van de tabelservice belicht die vooral relevant zijn voor het ontwerpen voor prestaties en schaalbaarheid. Als u nieuw bent in Azure Storage en de Tabelservice, leest u eerst [Inleiding tot Microsoft Azure Storage](../../storage/common/storage-introduction.md) en gaat u aan de slag met Azure Table Storage met behulp van [.NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) voordat u de rest van dit artikel leest. Hoewel de focus van deze handleiding ligt op de tabelservice, bevat deze discussie over de Azure Queue- en Blob-services en hoe u deze gebruiken met de tabelservice.  

Wat is de table service? Zoals u van de naam mag verwachten, gebruikt de tabelservice een tabelindeling om gegevens op te slaan. In de standaardterminologie vertegenwoordigt elke rij van de tabel een entiteit en slaan de kolommen de verschillende eigenschappen van die entiteit op. Elke entiteit heeft een paar sleutels om deze op unieke wijze te identificeren en een tijdstempelkolom die de tabelservice gebruikt om bij te houden wanneer de entiteit voor het laatst is bijgewerkt. De tijdstempel wordt automatisch toegepast en u de tijdstempel niet handmatig overschrijven met een willekeurige waarde. De tabelservice gebruikt deze laatst gewijzigde tijdstempel (LMT) om optimistische gelijktijdigheid te beheren.  

> [!NOTE]
> De API-bewerkingen van de tabelservice REST leveren ook een **ETag-waarde** op die is afgeleid van het LMT. Dit document gebruikt de termen ETag en LMT door elkaar omdat ze verwijzen naar dezelfde onderliggende gegevens.  
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


Tot nu toe lijken deze gegevens op een tabel in een relationele database, waarbij de belangrijkste verschillen de verplichte kolommen zijn en de mogelijkheid om meerdere entiteitstypen in dezelfde tabel op te slaan. Elk van de door de gebruiker gedefinieerde eigenschappen zoals **FirstName** of **Age** heeft ook een gegevenstype, zoals geheel getal of tekenreeks, net als een kolom in een relationele database. Hoewel in tegenstelling tot een relationele database, betekent de schemaloze aard van de tabelservice dat een eigenschap niet hetzelfde gegevenstype op elke entiteit hoeft te hebben. Als u complexe gegevenstypen in één eigenschap wilt opslaan, moet u een geserialiseerde indeling gebruiken, zoals JSON of XML. Zie [Het tabelservicegegevensmodel begrijpen](https://msdn.microsoft.com/library/azure/dd179338.aspx)voor meer informatie over de tabelservice, zoals ondersteunde gegevenstypen, ondersteunde datumbereiken, naamgevingsregels en groottebeperkingen.

Uw keuze van **PartitionKey** en **RowKey** is van fundamenteel belang voor een goed tafelontwerp. Elke entiteit die in een tabel is opgeslagen, moet een unieke combinatie van **PartitionKey** en **RowKey**hebben. Net als bij sleutels in een relationele databasetabel worden de **partitionkey-** en **rowkey-waarden** geïndexeerd om een geclusterde index te maken om snelle look-ups mogelijk te maken. De tabelservice maakt echter geen secundaire indexen, dus **PartitionKey** en **RowKey** zijn de enige geïndexeerde eigenschappen. Sommige van de patronen beschreven in [tabel ontwerppatronen](table-storage-design-patterns.md) illustreren hoe u werken rond deze schijnbare beperking.  

Een tabel bestaat uit een of meer partities, en veel van de ontwerpbeslissingen die u neemt, zullen gaan over het kiezen van een geschikte **PartitionKey** en **RowKey** om uw oplossing te optimaliseren. Een oplossing kan bestaan uit één tabel die al uw entiteiten bevat die zijn ingedeeld in partities, maar meestal heeft een oplossing meerdere tabellen. Tabellen helpen u om uw entiteiten logisch te organiseren, u te helpen de toegang tot de gegevens te beheren met behulp van toegangscontrolelijsten en u een hele tabel laten vallen met één opslagbewerking.  

## <a name="table-partitions"></a>Tabelpartities
De accountnaam, tabelnaam en **PartitionKey** identificeren samen de partitie binnen de opslagservice waar de tabelservice de entiteit opslaat. Partities maken niet alleen deel uit van het adresseringsschema voor entiteiten, maar definiëren ook een ruimte voor transacties (zie [Entiteitsgroeptransacties](#entity-group-transactions) hieronder) en vormen de basis van de manier waarop de tabelservice wordt geschaald. Zie checklist Prestaties en [schaalbaarheid voor tabelopslag voor](storage-performance-checklist.md)meer informatie over partities.  

In de tabelservice worden een afzonderlijk knooppunt een of meer volledige partities en de serviceschalen door dynamisch verdelingspartities over knooppunten te schalen. Als een knooppunt onder belasting staat, kan de tabelservice het bereik van partities die door dat knooppunt worden onderhouden, *splitsen* op verschillende knooppunten; wanneer het verkeer afneemt, *kan* de service de partitiereeksen samenvoegen van stille knooppunten terug naar één knooppunt.  

Zie het document [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)voor meer informatie over de interne details van de tabelservice en in het bijzonder hoe de service partities beheert.  

## <a name="entity-group-transactions"></a>Entiteitgroeptransacties
In de tabelservice zijn Entiteitsgroepstransacties (EGT's) het enige ingebouwde mechanisme voor het uitvoeren van atoomupdates voor meerdere entiteiten. EGT's worden soms ook wel *batchtransacties*genoemd . EGT's kunnen alleen werken op entiteiten die in dezelfde partitie zijn opgeslagen (dat wil zeggen dezelfde partitiesleutel in een bepaalde tabel delen). Dus wanneer u atoomtransactiegedrag nodig hebt tussen meerdere entiteiten, moet u ervoor zorgen dat deze entiteiten zich in dezelfde partitie bevinden. Dit is vaak een reden om meerdere entiteitstypen in dezelfde tabel (en partitie) te houden en geen meerdere tabellen te gebruiken voor verschillende entiteitstypen. Eén EGT kan maximaal 100 entiteiten uitvoeren.  Als u meerdere gelijktijdige ET's indient voor verwerking, is het belangrijk om ervoor te zorgen dat deze EGT's niet werken op entiteiten die gemeenschappelijk zijn voor ALLE-EGT's; anders kan de verwerking worden vertraagd.

EGT's introduceren ook een mogelijke afweging voor u om te evalueren in uw ontwerp. Dat wil zeggen dat het gebruik van meer partities de schaalbaarheid van uw toepassing verhoogt, omdat Azure meer mogelijkheden heeft voor taakverdelingsaanvragen tussen knooppunten. Maar het gebruik van meer partities kan het vermogen van uw toepassing beperken om atomaire transacties uit te voeren en een sterke consistentie voor uw gegevens te behouden. Bovendien zijn er specifieke schaalbaarheidsdoelen op het niveau van een partitie die de doorvoer van transacties die u voor één knooppunt verwachten, kunnen beperken. Zie [Schaalbaarheidsdoelen voor standaardopslagaccounts voor](../common/scalability-targets-standard-account.md)Azure voor meer informatie over schaalbaarheidsdoelen voor Azure-standaardopslagaccounts. Zie [Schaalbaarheids- en prestatiedoelen voor tabelopslag voor](scalability-targets.md)meer informatie over schaalbaarheidsdoelen voor de tabelservice.

## <a name="capacity-considerations"></a>Overwegingen bij capaciteitsbepaling

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="cost-considerations"></a>Kostenoverwegingen
Tabelopslag is relatief goedkoop, maar u moet kostenramingen opnemen voor zowel het capaciteitsgebruik als de hoeveelheid transacties als onderdeel van uw evaluatie van een tabelserviceoplossing. In veel scenario's is het opslaan van gedenormaliseerde of dubbele gegevens om de prestaties of schaalbaarheid van uw oplossing te verbeteren echter een geldige aanpak. Zie [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over prijzen.  

## <a name="next-steps"></a>Volgende stappen

- [Tabelontwerppatronen](table-storage-design-patterns.md)
- [Relaties modelleren](table-storage-design-modeling.md)
- [Ontwerp voor query's](table-storage-design-for-query.md)
- [Tabelgegevens versleutelen](table-storage-design-encrypt-data.md)
- [Ontwerp voor gegevenswijziging](table-storage-design-for-modification.md)
