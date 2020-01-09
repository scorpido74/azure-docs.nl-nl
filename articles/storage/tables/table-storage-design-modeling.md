---
title: Model relaties in azure Table Storage-ontwerp | Microsoft Docs
description: Inzicht in het model proces bij het ontwerpen van uw oplossing voor tabel opslag.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 25082c107fbc0feeb533aa2b4fc56cff960e778d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457558"
---
# <a name="modeling-relationships"></a>Relaties modelleren
In dit artikel wordt het modelleer proces beschreven om u te helpen bij het ontwerpen van uw Azure Table Storage-oplossingen.

Het ontwikkelen van domeinmodellen is een belangrijke stap in het ontwerp van complexe systemen. Meestal gebruikt u het modelleringsproces om entiteiten en de relaties tussen deze als een manier om te begrijpen van het bedrijfsdomein en kennis van het ontwerp van uw systeem te identificeren. Deze sectie richt zich op hoe u enkele van de algemene relatietypen gevonden in de domeinmodellen te ontwerpen voor de Table-service kunt vertalen. Het proces van de toewijzing van een logische gegevensmodel naar een fysieke op NoSQL gebaseerde gegevens-model verschilt van die bij het ontwerpen van een relationele database gebruikt. Relationele databases ontwerp wordt meestal ervan uitgegaan dat een gegevens-normalisatieproces die is geoptimaliseerd voor het minimaliseren van redundantie – en een declaratieve query-functie die isoleert de implementatie van hoe u de database de werking van.  

## <a name="one-to-many-relationships"></a>Een-op-veel-relaties
Een-op-veel-relaties tussen zakelijke domeinobjecten vaak optreden: één afdeling heeft bijvoorbeeld veel werknemers. Er zijn verschillende manieren voor het implementeren van een-op-veel-relaties in de tabelservice elke met voor- en nadelen die mogelijk relevant zijn voor het opgegeven scenario.  

Bekijk het voorbeeld van een grote onderneming met meerdere nationale met tienduizenden afdelingen en werknemer entiteiten waarbij elke afdeling heeft veel werknemers en elke werknemer als die zijn gekoppeld aan een bepaalde afdeling. Eén aanpak is het opslaan van afzonderlijke afdeling en werknemer entiteiten zoals deze:  


![Afzonderlijke afdelingen en entiteiten van werk nemers opslaan](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

In dit voorbeeld ziet u een impliciete een-op-veel-relatie tussen de typen die op basis van de **PartitionKey** waarde. Elke afdeling kan veel werknemers hebben.  

In dit voorbeeld toont ook een entiteit afdeling en de gerelateerde werknemer-entiteiten in dezelfde partitie. U kunt verschillende partities, tabellen of zelfs storage-accounts gebruiken voor de verschillende Entiteitstypen.  

Een alternatieve methode is het uw gegevens denormaliseren en op te slaan alleen werknemer entiteiten met gedenormaliseerde afdelingsgegevens zoals wordt weergegeven in het volgende voorbeeld. In dit specifieke scenario deze gedenormaliseerde benadering mogelijk niet de beste hebt u een vereiste om de details van een afdelingsmanager niet wijzigen omdat hiervoor moet u bijwerken van alle werknemers van de afdeling te kunnen.  

![Werknemer-entiteit](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Zie voor meer informatie de [denormalisatie patroon](table-storage-design-patterns.md#denormalization-pattern) verderop in deze handleiding.  

De volgende tabel geeft een overzicht van de voor- en nadelen van elk van de methoden die hierboven worden beschreven voor het opslaan van werknemers en afdeling entiteiten waarvoor een een-op-veel-relatie. U moet ook overwegen hoe vaak verwacht u dat verschillende bewerkingen uit te voeren: kan het zijn aanvaardbaar is voor een ontwerp met een dure bewerking als die voor deze bewerking alleen zelden gebeurt hebben.  

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
<li>Mogelijk moet u een entiteit van de afdeling voor sommige activiteiten van de client en van een werknemer worden opgehaald.</li>
<li>Opslagbewerkingen gebeuren in dezelfde partitie. Op hoog transactie volumes, kan dit resulteren in een hotspot.</li>
<li>U kunt een werknemer niet verplaatsen naar een nieuwe afdeling met behulp van een EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Afzonderlijke entiteits typen, verschillende partities of tabellen of opslag accounts</td>
<td>
<ul>
<li>U kunt een entiteit van de afdeling of de werknemer entiteit bijwerken met een eenmalige bewerking.</li>
<li>Op hoog transactie volumes, kan dit helpen bij de belasting over meer partities verdelen.</li>
</ul>
</td>
<td>
<ul>
<li>Mogelijk moet u een entiteit van de afdeling voor sommige activiteiten van de client en van een werknemer worden opgehaald.</li>
<li>U kunt EGTs niet gebruiken voor het handhaven van de consistentie wanneer u update/insert/verwijderen van een werknemer en update een afdeling. Bijvoorbeeld, een aantal medewerkers in een entiteit afdeling worden bijgewerkt.</li>
<li>U kunt een werknemer niet verplaatsen naar een nieuwe afdeling met behulp van een EGT.</li>
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
<li>Kan het zijn duur in het handhaven van de consistentie als u nodig hebt om afdeling informatie (dit moet u bij het bijwerken van de werknemers van een afdeling) te werken.</li>
</ul>
</td>
</tr>
</table>

Hoe u kiezen tussen deze opties, en welke van de voor- en nadelen zijn de meest significante, is afhankelijk van uw specifieke scenario's. Bijvoorbeeld, hoe vaak u Wijzig afdeling entiteiten; moeten alle werknemers-query's de aanvullende afdelingen informatie; hoe dicht weet u naar de schaalbaarheidslimieten van de partities of uw storage-account?  

## <a name="one-to-one-relationships"></a>-Op-een-relaties
Domeinmodellen kunnen-op-een-relaties tussen entiteiten bevatten. Als u nodig hebt voor het implementeren van een-op-een-relatie in de Table-service, moet u ook de twee gerelateerde entiteiten koppelen wanneer u nodig hebt om op te halen ze allebei. Deze koppeling kan impliciet, op basis van een overeenkomst in de sleutelwaarden of expliciet worden doordat een koppeling in de vorm van **PartitionKey** en **RowKey** waarden in elke entiteit naar de gerelateerde entiteit. Zie de sectie voor een bespreking van of u de gerelateerde entiteiten in dezelfde partitie moet opslaan, [een-op-veel relaties](#one-to-many-relationships).  

Er zijn ook overwegingen bij de implementatie die kunnen leiden bij de implementatie-op-een-relaties in de Table-service:  

* Verwerken van grote entiteiten (Zie voor meer informatie, [grote entiteiten patroon](table-storage-design-patterns.md#large-entities-pattern)).  
* Toegangs beheer implementeren (Zie toegang beheren met hand tekeningen voor gedeelde toegang) voor meer informatie.  

## <a name="join-in-the-client"></a>Deelnemen aan de client
Hoewel er manieren grafiekverwerking om relaties in de tabelservice zijn, moet u niet vergeet dat de twee belangrijkste redenen voor het gebruik van de Table-service schaalbaarheid en prestaties zijn. Als u dat u veel relaties die een bedreiging vormen van de prestaties en schaalbaarheid van uw oplossing modelleert vindt, vraagt u uzelf als is het nodig zijn om te maken van alle de relaties tussen de gegevens in het tabelontwerp van de. U mogelijk het ontwerp vereenvoudigen en verbeteren van de schaalbaarheid en prestaties van uw oplossing als u toestaat dat uw clienttoepassing uitvoeren die nodig zijn joins.  

Bijvoorbeeld als u kleine tabellen met gegevens die niet vaak wijzigen hebt, kunt klikt u vervolgens u deze gegevens eenmaal ophalen en opslaan in cache deze op de client. Dit kunt herhaalde interactie om op te halen van dezelfde gegevens voorkomen. In de voor beelden die we in deze hand leiding hebben bekeken, is de set van afdelingen in een kleine organisatie waarschijnlijk klein en is het niet vaak een goede kandidaat te maken voor gegevens die client toepassing kan downloaden en opslaan als opzoek gegevens.  

## <a name="inheritance-relationships"></a>Overname van relaties
Als de clienttoepassing gebruikmaakt van een set klassen die deel van een overnamerelatie uitmaken voor bedrijfsentiteiten, kunt u eenvoudig deze entiteiten in de Table-service behouden. Bijvoorbeeld, u mogelijk de volgende set klassen gedefinieerd in uw clienttoepassing waar **persoon** is een abstracte klasse.

![Abstracte persoons klasse](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

U kunt zich blijven voordoen exemplaren van de twee concrete klassen in de tabel-service met behulp van één personentabel met behulp van entiteiten in dat er als volgt:  

![Tabel persoon](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Zie de sectie werken met heterogene entiteits typen verderop in deze hand leiding voor meer informatie over het werken met meerdere entiteits typen in dezelfde tabel in client code. Dit bevat voorbeelden van het entiteitstype in clientcode herkennen.  


## <a name="next-steps"></a>Volgende stappen

- [Tabel ontwerp patronen](table-storage-design-patterns.md)
- [Ontwerpen voor het uitvoeren van query's](table-storage-design-for-query.md)
- [Tabel gegevens versleutelen](table-storage-design-encrypt-data.md)
- [Ontwerp voor gegevens aanpassing](table-storage-design-for-modification.md)
