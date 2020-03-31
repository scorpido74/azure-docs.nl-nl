---
title: Compute resource voor SQL-groep beheren
description: Meer informatie over de mogelijkheden voor het uitschalen van prestaties in een Azure Synapse Analytics SQL-groep. Schaal uit door DBO's aan te passen of verlaag de kosten door het gegevensmagazijn te pauzeren.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4c6c4d97282387fbcee1d7e8b55b95c01e3dded5
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351602"
---
# <a name="manage-compute-in-azure-synapse-analytics-data-warehouse"></a>Compute beheren in Azure Synapse Analytics-gegevensmagazijn

Meer informatie over het beheren van compute resources in Azure Synapse Analytics SQL-groep. Verlaag de kosten door de SQL-groep te pauzeren of schaal het gegevensmagazijn om aan prestatievereisten te voldoen. 

## <a name="what-is-compute-management"></a>Wat is compute management?

De architectuur van het datawarehouse scheidt opslag en rekenkracht, waardoor elk afzonderlijk kan worden geschaald. Daardoor kunt u de rekenkracht aanpassen om aan prestatievereisten te voldoen zonder dat dit consequenties heeft voor de opslag van gegevens. U kunt ook rekenresources pauzeren en hervatten. Een natuurlijk gevolg van deze architectuur is dat [facturering](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) voor compute en storage gescheiden is. U kunt op de kosten voor rekenuren besparen door de berekeningen te onderbreken als u uw datawarehouse een tijdje niet nodig hebt. 

## <a name="scaling-compute"></a>Compute schalen

U de rekenkracht uitschalen of terugschalen door de instelling [voor gegevensmagazijnen](what-is-a-data-warehouse-unit-dwu-cdwu.md) voor uw SQL-groep aan te passen. De prestaties voor het laden en voor query's kunnen lineair toenemen als u meer datawarehouse-eenheden wilt toevoegen. 

Zie de [Azure-portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md)of [T-SQL](quickstart-scale-compute-tsql.md) snelstarts voor schaalstappen. U ook scale-outbewerkingen uitvoeren met een [REST API.](sql-data-warehouse-manage-compute-rest-api.md#scale-compute)

Als u een schaalbewerking wilt uitvoeren, doodt SQL-groep eerst alle binnenkomende query's en rolt vervolgens transacties terug om een consistente status te garanderen. Het aanpassen van de schaal vindt alleen plaats als de transactie is teruggedraaid. Voor een schaalbewerking ontkoppelt het systeem de opslaglaag van de compute-knooppunten, voegt het compute-knooppunten toe en voegt het de opslaglaag opnieuw toe aan de compute-laag. Elke SQL-groep wordt opgeslagen als 60 distributies, die gelijkmatig worden gedistribueerd naar de compute-knooppunten. Als u meer compute nodes toevoegt, wordt meer rekenkracht toegevoegd. Naarmate het aantal compute nodes toeneemt, neemt het aantal distributies per compute node af, waardoor uw query's meer rekenkracht krijgen. Ook het verminderen van gegevensmagazijneenheden vermindert het aantal compute nodes, waardoor de rekenbronnen voor query's worden verminderd.

In de volgende tabel ziet u hoe het aantal distributies per Compute-knooppunt verandert naarmate de gegevensmagazijnen veranderen.  DW30000c biedt 60 Compute-knooppunten en bereikt veel hogere queryprestaties dan DW100c. 

| Datawarehouse-eenheden  | \#van compute nodes | \#van de distributies per knooppunt |
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


## <a name="finding-the-right-size-of-data-warehouse-units"></a>De juiste grootte van gegevensmagazijneenheden zoeken

Als u de prestatievoordelen wilt zien van uitschalen, met name voor grotere gegevensmagazijnen, wilt u ten minste een gegevensset van 1 TB gebruiken. Als u het beste aantal gegevensmagazijneenheden voor uw SQL-groep wilt vinden, probeert u op en neer te schalen. Voer een paar query's uit met verschillende aantallen gegevensmagazijneenheden na het laden van uw gegevens. Omdat schalen snel is, u verschillende prestatieniveaus in een uur of minder proberen. 

Aanbevelingen voor het vinden van het beste aantal datawarehouse-eenheden:

- Voor een SQL-pool in ontwikkeling, begin met het selecteren van een kleiner aantal gegevensmagazijneenheden.  Een goed uitgangspunt is DW400c of DW200c.
- Controleer de prestaties van uw toepassing en observeer het aantal geselecteerde gegevensmagazijneenheden in vergelijking met de prestaties die u waarneemt.
- Ga uit van een lineaire schaal en bepaal hoeveel u nodig hebt om de eenheden voor gegevensmagazijnen te verhogen of te verkleinen. 
- Blijf aanpassingen maken totdat u een optimaal prestatieniveau voor uw bedrijfsbehoeften hebt bereikt.

## <a name="when-to-scale-out"></a>Wanneer moet u uitschalen

Het uitschalen van datawarehouse-eenheden heeft invloed op deze aspecten van prestaties:

- Verbetert lineair de prestaties van het systeem voor scans, aggregaties en CTAS-instructies.
- Verhoogt het aantal lezers en schrijvers voor het laden van gegevens.
- Maximaal aantal gelijktijdige query's en gelijktijdige sleuven.

Aanbevelingen voor wanneer gegevensmagazijnen moeten worden uitschalen:

- Voordat u een zware bewerking voor het laden of transformeren van gegevens uitvoert, schaalt u deze uit om de gegevens sneller beschikbaar te maken.
- Schaal tijdens piekuren uit om grotere aantallen gelijktijdige query's aan te kunnen. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Wat gebeurt er als uitschaling de prestaties niet verbetert?

Het toevoegen van data warehouse units verhogen van de parallellisme. Als het werk gelijkmatig wordt verdeeld over de Compute-knooppunten, verbetert het extra parallellisme de queryprestaties. Als uitschalen uw prestaties niet verandert, zijn er enkele redenen waarom dit zou kunnen gebeuren. Uw gegevens kunnen over de distributies worden scheefgetrokken of query's introduceren mogelijk een grote hoeveelheid gegevensverplaatsing. Zie Problemen met prestaties [uitvoeren](sql-data-warehouse-troubleshoot.md#performance)voor problemen met queryprestaties. 

## <a name="pausing-and-resuming-compute"></a>Onderbreken en hervatten van rekenactiviteiten

Als u de compute pauzeert, wordt de opslaglaag losgekoppeld van de Compute-knooppunten. De compute resources worden vrijgegeven van uw account. U wordt niet in rekening gebracht voor compute terwijl de compute wordt onderbroken. Als u de compute hervat, wordt de opslag opnieuw gekoppeld aan de Compute-knooppunten en worden de kosten voor Compute hervat. Wanneer u een SQL-groep pauzeert:

* Reken- en geheugenbronnen worden geretourneerd naar de groep beschikbare resources in het datacenter
* Kosten voor de eenheid van gegevensmagazijnen zijn nul voor de duur van de pauze.
* Gegevensopslag wordt niet beïnvloed en uw gegevens blijven intact. 
* Alle bewerkingen voor het uitvoeren of in de wachtrij worden geannuleerd.

Wanneer u een SQL-groep hervat:

* De SQL-groep krijgt reken- en geheugenbronnen voor de instelling van gegevensmagazijnen.
* Rekenkosten voor uw gegevensmagazijnen worden hervat.
* Uw gegevens worden beschikbaar.
* Nadat de SQL-groep online is, moet u uw workloadquery's opnieuw starten.

Als u altijd wilt dat uw SQL-groep toegankelijk is, u deze verkleinen tot de kleinste grootte in plaats van onderbreken. 

Zie de [Azure-portal](pause-and-resume-compute-portal.md)of [PowerShell](pause-and-resume-compute-powershell.md) snel start voor het onderbreken en hervatten van stappen. U ook de [pause REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) of de RESUME REST [API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute)gebruiken.

## <a name="drain-transactions-before-pausing-or-scaling"></a>Transacties stoppen voor onderbreken of schalen

We raden u aan bestaande transacties te laten voltooien voordat u een pauze- of schaalbewerking start.

Wanneer u uw SQL-pool pauzeert of schaalt, worden uw query's achter de schermen geannuleerd wanneer u de pauze- of schaalaanvraag initieert. Een eenvoudige SELECT-query annuleren is een snelle bewerking en heeft zo goed als geen invloed op de duur van het onderbreken of schalen van uw instantie.  Maar transactiequery’s, die uw gegevens of de structuur van uw gegevens wijzigen, kunnen mogelijk niet snel worden stopgezet. **Transactiequery’s moeten per definitie volledig worden voltooid of hun wijzigingen volledig terugdraaien.** Het kan even lang of langer duren om het werk dat door een transactiequery is voltooid, terug te draaien, als het uitvoeren van de oorspronkelijke opdracht van de query. Als u bijvoorbeeld een query annuleert voor het verwijderen van rijen die al een uur wordt uitgevoerd, kan het systeem er een uur over doen om de verwijderde rijen terug te plaatsen. Als u onderbreken of schalen uitvoert terwijl er transacties bezig zijn, kan het schalen of onderbreken lang lijken te duren omdat het schalen of onderbreken moet wachten op het terugdraaien van de transacties voordat het kan worden voortgezet.

Zie ook [Inzicht in transacties](sql-data-warehouse-develop-transactions.md)en Het optimaliseren van [transacties](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Compute management automatiseren

Zie Compute beheren met [Azure-functies](manage-compute-with-azure-functions.md)voor het automatiseren van de compute management-bewerkingen.

Elk van de scale-out-, pauze- en hervattingsbewerkingen kan enkele minuten duren. Als u automatisch schaalt, pauzeert of hervat, raden we u aan logica te implementeren om ervoor te zorgen dat bepaalde bewerkingen zijn voltooid voordat u doorgaat met een andere actie. Als u de SQL-poolstatus controleert via verschillende eindpunten, u de automatisering van dergelijke bewerkingen correct implementeren. 

Zie de [QuickStart powershell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) of [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) om de SQL-poolstatus te controleren. U ook de SQL-poolstatus controleren met een [REST-API.](sql-data-warehouse-manage-compute-rest-api.md#check-database-state)


## <a name="permissions"></a>Machtigingen

Voor het schalen van de SQL-groep zijn de machtigingen vereist die zijn beschreven in [ALTER DATABASE.](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)  Voor Onderbreken en hervatten is de SQL [DB Contributor-toestemming](../../role-based-access-control/built-in-roles.md#sql-db-contributor) vereist, met name Microsoft.Sql/servers/databases/action.


## <a name="next-steps"></a>Volgende stappen
Zie de handleiding voor [het beheren van compute](manage-compute-with-azure-functions.md) Een ander aspect van het beheren van compute resources is het toewijzen van verschillende compute resources voor afzonderlijke query's. Zie [Resourceklassen voor werkbelastingbeheer voor](resource-classes-for-workload-management.md)meer informatie .
