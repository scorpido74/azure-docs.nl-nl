---
title: Reken resource beheren
description: Meer informatie over de mogelijkheden voor het uitbreiden van prestaties in Azure SQL Data Warehouse. Uitschalen door Dwu's aan te passen of door de kosten te verlagen door het Data Warehouse te onderbreken.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/12/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 936d92d085420e1386e29a924470b9bac9200d43
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039086"
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Compute in Azure SQL Data Warehouse beheren
Meer informatie over het beheren van reken bronnen in Azure SQL Data Warehouse. Lagere kosten door het Data Warehouse te onderbreken of het Data Warehouse te schalen om te voldoen aan de prestatie vereisten. 

## <a name="what-is-compute-management"></a>Wat is reken beheer?
De architectuur van SQL Data Warehouse scheidt opslag en reken kracht, waardoor elk afzonderlijk kan worden geschaald. Hierdoor kunt u de reken kracht schalen om te voldoen aan de prestatie vereisten, onafhankelijk van de gegevens opslag. U kunt ook de reken resources onderbreken en hervatten. Een natuurlijk gevolg van deze architectuur is dat de [facturering](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) voor reken capaciteit en opslag gescheiden is. Als u uw data warehouse niet enige tijd hoeft te gebruiken, kunt u de reken kosten besparen door Compute te onderbreken. 

## <a name="scaling-compute"></a>Berekening schalen
U kunt de schaal omhoog of omlaag schalen door de instellingen voor het [Data Warehouse-eenheden](what-is-a-data-warehouse-unit-dwu-cdwu.md) voor uw data warehouse aan te passen. Het laden en de query prestaties kunnen lineair toenemen naarmate u meer data warehouse-eenheden toevoegt. 

Zie [Azure Portal](quickstart-scale-compute-portal.md), [Power shell](quickstart-scale-compute-powershell.md)of [T-SQL](quickstart-scale-compute-tsql.md) Quick starts (Engelstalig) voor stapsgewijze instructies. U kunt ook uitschaal bewerkingen uitvoeren met een [rest API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Als u een schaal bewerking wilt uitvoeren, wordt door SQL Data Warehouse eerst alle binnenkomende query's af te breken en worden vervolgens de trans acties teruggedraaid om een consistente status te krijgen. Schalen wordt alleen uitgevoerd wanneer het terugdraaien van de trans actie is voltooid. Voor een schaal bewerking koppelt het systeem de opslaglaag van de reken knooppunten en voegt reken knooppunten toe en koppelt de opslaglaag vervolgens opnieuw aan de compute-laag. Elk data warehouse wordt opgeslagen als 60-distributies, die gelijkmatig worden gedistribueerd naar de reken knooppunten. Het toevoegen van meer reken knooppunten voegt meer reken kracht toe. Naarmate het aantal reken knooppunten toeneemt, neemt het aantal verdelingen per reken knooppunt af, waardoor er meer reken kracht is voor uw query's. Evenzo verlagen Data Warehouse-eenheden het aantal reken knooppunten, waardoor de reken resources voor query's worden verminderd.

De volgende tabel laat zien hoe het aantal distributies per Compute-knoop punt verandert naarmate de Data Warehouse-eenheden worden gewijzigd.  DWU6000 biedt 60 Compute-knoop punten en behaalt veel hogere query prestaties dan DWU100. 

| Datawarehouse-eenheden  | \# van reken knooppunten | \# van distributies per knoop punt |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
| DW500c   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>De juiste grootte van de Data Warehouse-eenheden zoeken

Voor een overzicht van de prestatie voordelen van uitschalen, met name voor grotere Data Warehouse-eenheden, wilt u ten minste één data set van 1 TB gebruiken. Als u het beste aantal data warehouse-eenheden voor uw data warehouse wilt zoeken, kunt u omhoog en omlaag schalen. Voer enkele query's uit met verschillende aantallen Data Warehouse-eenheden na het laden van uw gegevens. Omdat schalen snel is, kunt u verschillende prestatie niveaus uitproberen in een uur of minder. 

Aanbevelingen voor het zoeken naar het beste aantal data warehouse-eenheden:

- Voor een Data Warehouse in ontwikkeling begint u met het selecteren van een kleiner aantal data warehouse-eenheden.  Een goed uitgangs punt is DW400 of DW200.
- Bewaak de prestaties van uw toepassing, waarbij het aantal geselecteerde data warehouse-eenheden wordt geobserveerd vergeleken met de prestaties die u ziet.
- Stel dat er een lineaire schaal is en bepaal hoeveel u nodig hebt om de Data Warehouse-eenheden te verg Roten of te verkleinen. 
- Blijf aanpassingen aanbrengen totdat u een optimaal prestatie niveau bereikt voor uw bedrijfs vereisten.

## <a name="when-to-scale-out"></a>Wanneer uitschalen
Het uitschalen van data warehouse-eenheden heeft gevolgen voor de volgende aspecten van prestaties:

- Zorgt voor een lineaire verbetering van de prestaties van het systeem voor scans, aggregaties en CTAS-instructies.
- Verhoogt het aantal lezers en schrijvers voor het laden van gegevens.
- Maximum aantal gelijktijdige query's en gelijktijdigheids sleuven.

Aanbevelingen voor het schalen van data warehouse-eenheden:

- Voordat u een zware gegevens laad-of transformatie bewerking uitvoert, kunt u uitschalen om de gegevens sneller beschikbaar te maken.
- Uitschalen tijdens de piek uren om grotere aantallen gelijktijdige query's toe te passen. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Wat gebeurt er als de prestaties niet worden verbeterd door het uitschalen?

Het toevoegen van data warehouse-eenheden verhoogt de parallelle factor. Als het werk gelijkmatig wordt verdeeld tussen de reken knooppunten, verbetert de extra parallelle uitvoering query prestaties. Als u de prestaties niet wijzigt, zijn er enkele redenen waarom dit kan gebeuren. Het kan zijn dat uw gegevens over de distributies worden schuingetrokken, of query's kunnen een grote hoeveelheid gegevens verplaatsing introduceren. Zie [prestaties oplossen](sql-data-warehouse-troubleshoot.md#performance)voor meer informatie over het onderzoeken van prestatie problemen. 

## <a name="pausing-and-resuming-compute"></a>De berekening onderbreken en hervatten
Als u de reken kracht onderbreekt, ontkoppelt u de opslaglaag van de reken knooppunten. De reken resources worden vrijgegeven uit uw account. Er worden geen kosten in rekening gebracht voor de reken kracht terwijl de reken kracht wordt onderbroken. Als u de compute hervat, wordt de opslag opnieuw gekoppeld aan de reken knooppunten en worden de kosten voor Compute hervat. Wanneer u een Data Warehouse onderbreekt:

* Reken-en geheugen bronnen worden geretourneerd naar de groep beschik bare resources in het Data Center
* De kosten voor de eenheid van het Data Warehouse zijn nul voor de duur van de onderbreking.
* Gegevens opslag wordt niet beïnvloed en uw gegevens blijven intact. 
* SQL Data Warehouse annuleert alle actieve of in de wachtrij geplaatste bewerkingen.

Wanneer u een Data Warehouse hervat:

* SQL Data Warehouse worden reken-en geheugen resources opgehaald voor de instellingen van uw data warehouse-eenheden.
* Reken kosten voor uw data warehouse-eenheden hervatten.
* Uw gegevens worden nu beschikbaar.
* Nadat het Data Warehouse online is, moet u de query's voor de werk belasting opnieuw opstarten.

Als u uw data warehouse altijd toegankelijk wilt maken, kunt u overwegen om het te schalen naar de kleinste grootte in plaats van te onderbreken. 

Raadpleeg de [Azure Portal](pause-and-resume-compute-portal.md)of [Power shell](pause-and-resume-compute-powershell.md) -Quick starts voor instructies voor onderbreken en hervatten. U kunt ook de [pause-rest API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) of de [hervattings rest API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute)gebruiken.

## <a name="drain-transactions-before-pausing-or-scaling"></a>Transacties stoppen voor onderbreken of schalen
We raden aan om bestaande trans acties te volt ooien voordat u een pauze of schaal bewerking initieert.

Wanneer u uw SQL Data Warehouse onderbreekt of schaalt, worden uw query’s achter de schermen geannuleerd zodra u het onderbreek- of schaalverzoek start.  Een eenvoudige SELECT-query annuleren is een snelle bewerking en heeft zo goed als geen invloed op de duur van het onderbreken of schalen van uw instantie.  Maar transactiequery’s, die uw gegevens of de structuur van uw gegevens wijzigen, kunnen mogelijk niet snel worden stopgezet.  **Transactiequery’s moeten per definitie volledig worden voltooid of hun wijzigingen volledig terugdraaien.**  Het kan even lang of langer duren om het werk dat door een transactiequery is voltooid, terug te draaien, als het uitvoeren van de oorspronkelijke opdracht van de query.  Als u bijvoorbeeld een query annuleert voor het verwijderen van rijen die al een uur wordt uitgevoerd, kan het systeem er een uur over doen om de verwijderde rijen terug te plaatsen.  Als u onderbreken of schalen uitvoert terwijl er transacties bezig zijn, kan het schalen of onderbreken lang lijken te duren omdat het schalen of onderbreken moet wachten op het terugdraaien van de transacties voordat het kan worden voortgezet.

Zie ook [informatie over trans acties](sql-data-warehouse-develop-transactions.md)en het [optimaliseren van trans acties](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Reken beheer automatiseren
Zie [Compute-functies beheren met Azure functions](manage-compute-with-azure-functions.md)voor het automatiseren van de bewerkingen voor Compute management.

Het kan enkele minuten duren voordat elk van de bewerkingen scale-out, Pause en resume is voltooid. Als u automatisch wilt schalen, onderbreken of hervatten, wordt u aangeraden logica te implementeren om ervoor te zorgen dat bepaalde bewerkingen zijn voltooid voordat u doorgaat met een andere actie. Door de status van het Data Warehouse via verschillende eind punten te controleren, kunt u automatisering van dergelijke bewerkingen op de juiste manier implementeren. 

Zie de Snelstartgids [Power shell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) of [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) om de status van het Data Warehouse te controleren. U kunt ook de status van het Data Warehouse controleren met een [rest API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Machtigingen

Voor het schalen van het Data Warehouse zijn de machtigingen vereist die worden beschreven in [ALTER data base](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse).  Voor onderbreken en hervatten is de machtiging [SQL DB-Inzender](../role-based-access-control/built-in-roles.md#sql-db-contributor) vereist, met name micro soft. SQL/servers/data bases/action.


## <a name="next-steps"></a>Volgende stappen
Zie de hand leiding voor het [beheren van reken kracht](manage-compute-with-azure-functions.md) een ander aspect van het beheren van reken resources is het toewijzen van verschillende reken bronnen voor afzonderlijke query's. Zie [resource klassen voor workload Management](resource-classes-for-workload-management.md)voor meer informatie.
