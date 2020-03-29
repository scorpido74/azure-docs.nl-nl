---
title: Modelleringsrelaties in Azure Table-opslagontwerp | Microsoft Documenten
description: Inzicht in het modelleringsproces bij het ontwerpen van uw oplossing voor tafelopslag.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 25082c107fbc0feeb533aa2b4fc56cff960e778d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457558"
---
# <a name="modeling-relationships"></a>Relaties modelleren
In dit artikel wordt het modelleringsproces besproken waarmee u uw Azure Table-opslagoplossingen ontwerpen.

Het bouwen van domeinmodellen is een belangrijke stap in het ontwerp van complexe systemen. Meestal gebruikt u het modelleringsproces om entiteiten en de relaties tussen hen te identificeren als een manier om het bedrijfsdomein te begrijpen en het ontwerp van uw systeem te informeren. In dit gedeelte wordt gefocusd op hoe u enkele van de gemeenschappelijke relatietypen in domeinmodellen vertalen naar ontwerpen voor de tabelservice. Het proces van toewijzing van een logisch gegevensmodel naar een fysiek NoSQL-gebaseerd gegevensmodel verschilt van het proces dat wordt gebruikt bij het ontwerpen van een relationele database. Relationele databases ontwerp gaat meestal uit van een data normalisatie proces geoptimaliseerd voor het minimaliseren van redundantie - en een declaratieve querying vermogen dat abstracts hoe de uitvoering van hoe de database werkt.  

## <a name="one-to-many-relationships"></a>Een-op-veel relaties
Een-op-veel relaties tussen zakelijke domeinobjecten komen vaak voor: bijvoorbeeld één afdeling heeft veel werknemers. Er zijn verschillende manieren om een-op-veel relaties in de tabelservice te implementeren met voor- en nadelen die relevant kunnen zijn voor het specifieke scenario.  

Denk aan het voorbeeld van een groot multi-nationaal bedrijf met tienduizenden afdelingen en werknemersentiteiten waar elke afdeling veel werknemers en elke werknemer heeft als gekoppeld aan één specifieke afdeling. Een benadering is het opslaan van afzonderlijke afdelingen en werknemers entiteiten zoals deze:  


![Afzonderlijke afdelings- en werknemersentiteiten opslaan](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

In dit voorbeeld wordt een impliciete één-op-één-relatie weergegeven tussen de typen op basis van de **partitionkey-waarde.** Elke afdeling kan veel medewerkers hebben.  

In dit voorbeeld worden ook een afdelingsentiteit en de bijbehorende werknemersentiteiten in dezelfde partitie weergegeven. U ervoor kiezen om verschillende partities, tabellen of zelfs opslagaccounts te gebruiken voor de verschillende entiteitstypen.  

Een alternatieve benadering is om uw gegevens te denormaliseren en alleen werknemersentiteiten op te slaan met gedenormaliseerde afdelingsgegevens zoals weergegeven in het volgende voorbeeld. In dit specifieke scenario is deze gedenormaliseerde aanpak mogelijk niet de beste als u een vereiste hebt om de details van een afdelingsmanager te kunnen wijzigen, omdat u om dit te doen elke werknemer op de afdeling moet bijwerken.  

![Werknemerentiteit](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Zie het [denormalisatiepatroon](table-storage-design-patterns.md#denormalization-pattern) later in deze handleiding voor meer informatie.  

In de volgende tabel worden de voor- en nadelen van elk van de hierboven beschreven benaderingen samengevat voor het opslaan van werknemers- en afdelingsentiteiten die een één-op-één-relatie hebben. U moet ook overwegen hoe vaak u verwacht verschillende bewerkingen uit te voeren: het kan aanvaardbaar zijn om een ontwerp te hebben dat een dure bewerking bevat als die bewerking slechts zelden plaatsvindt.  

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
<li>Het kan duur zijn om consistentie te behouden als u informatie over de afdeling moet bijwerken (dit vereist dat u alle werknemers in een afdeling bijwerkt).</li>
</ul>
</td>
</tr>
</table>

Hoe u kiest tussen deze opties en welke van de voors en tegens het belangrijkst zijn, is afhankelijk van uw specifieke toepassingsscenario's. Hoe vaak wijzigt u bijvoorbeeld afdelingsentiteiten; doen al uw werknemer query's moeten de extra afdelingsinformatie; hoe dicht bent u bij de schaalbaarheidslimieten voor uw partities of uw opslagaccount?  

## <a name="one-to-one-relationships"></a>Een-op-een relaties
Domeinmodellen kunnen een-op-een relaties tussen entiteiten bevatten. Als u een één-op-één relatie in de tabelservice moet implementeren, moet u ook kiezen hoe u de twee gerelateerde entiteiten koppelt wanneer u ze beide moet ophalen. Deze koppeling kan impliciet zijn, gebaseerd op een conventie in de kernwaarden, of expliciet door een koppeling op te slaan in de vorm van **PartitionKey-** en **RowKey-waarden** in elke entiteit naar de bijbehorende entiteit. Zie de sectie [Een-op-veel relaties](#one-to-many-relationships)voor een discussie over de vraag of u de gerelateerde entiteiten in dezelfde partitie moet opslaan.  

Er zijn ook implementatieoverwegingen die ertoe kunnen leiden dat u één-op-één relaties implementeert in de tabelservice:  

* Omgaan met grote entiteiten (zie [Patroon grote entiteiten](table-storage-design-patterns.md#large-entities-pattern)voor meer informatie).  
* Toegangsbesturingselementen implementeren (zie Toegang beheren met gedeelde toegangshandtekeningen voor meer informatie).  

## <a name="join-in-the-client"></a>Doe mee met de klant
Hoewel er manieren zijn om relaties in de tabelservice te modelleren, mag u niet vergeten dat de twee belangrijkste redenen voor het gebruik van de tabelservice schaalbaarheid en prestaties zijn. Als u merkt dat u veel relaties modelleert die de prestaties en schaalbaarheid van uw oplossing in gevaar brengen, moet u zich afvragen of het nodig is om alle gegevensrelaties in uw tabelontwerp op te bouwen. Mogelijk u het ontwerp vereenvoudigen en de schaalbaarheid en prestaties van uw oplossing verbeteren als u uw clienttoepassing de nodige joins laat uitvoeren.  

Als u bijvoorbeeld kleine tabellen hebt die gegevens bevatten die niet vaak worden gewijzigd, u deze gegevens één keer ophalen en op de cache opslaan op de client. Dit kan voorkomen dat herhaalde retouren om dezelfde gegevens op te halen. In de voorbeelden die we hebben bekeken in deze gids, de set van afdelingen in een kleine organisatie is waarschijnlijk klein en veranderen zelden waardoor het een goede kandidaat voor gegevens die client applicatie kan downloaden een keer en cache als opzoeken van gegevens.  

## <a name="inheritance-relationships"></a>Overervingsrelaties
Als uw clienttoepassing een reeks klassen gebruikt die deel uitmaken van een overervingsrelatie om bedrijfsentiteiten te vertegenwoordigen, u deze entiteiten in de tabelservice eenvoudig blijven gebruiken. U bijvoorbeeld de volgende set klassen hebben gedefinieerd in uw clienttoepassing waarbij **Persoon** een abstracte klasse is.

![Abstracte persoonsklasse](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

U instanties van de twee concrete klassen in de tabelservice blijven gebruiken met één persoon met behulp van entiteiten die er als volgt uitzien:  

![Tabel Persoon](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Zie de sectie Werken met heterogene entiteitstypen later in deze handleiding voor meer informatie over het werken met meerdere entiteitstypen in dezelfde tabel in clientcode. Dit geeft voorbeelden van hoe u het entiteitstype in clientcode herkennen.  


## <a name="next-steps"></a>Volgende stappen

- [Tabelontwerppatronen](table-storage-design-patterns.md)
- [Ontwerp voor query's](table-storage-design-for-query.md)
- [Tabelgegevens versleutelen](table-storage-design-encrypt-data.md)
- [Ontwerp voor gegevenswijziging](table-storage-design-for-modification.md)
