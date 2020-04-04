---
title: Workloadurgentie
description: Richtlijnen voor het instellen van het belang voor Synapse SQL-poolquery's in Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 2c8617cffaa81da6423011a494b8dbc82c42d218
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632460"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Azure Synapse Analytics-workloadbelang

In dit artikel wordt uitgelegd hoe het werkbelastingbelang de volgorde van uitvoering voor Synapse SQL-poolaanvragen in Azure Synapse kan beïnvloeden.

## <a name="importance"></a>Urgentie

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Voor bedrijfsbehoeften kunnen workloads voor gegevensopslag belangrijker zijn dan andere.  Overweeg een scenario waarin bedrijfskritieke verkoopgegevens worden geladen voordat de fiscale periode wordt gesloten.  Gegevensbelastingen voor andere bronnen, zoals weergegevens, hebben geen strikte SLA's. Als u een aanvraag instelt om verkoopgegevens te laden en een laag belang voor een verzoek om weergegevens te laden, zorgt u ervoor dat de verkoopgegevensbelasting de eerste toegang tot resources krijgt en sneller wordt voltooid.

## <a name="importance-levels"></a>Belangniveaus

Er zijn vijf niveaus van belang: laag, below_normal, normaal, above_normal en hoog.  Aanvragen die geen belang instellen, krijgen het standaardniveau van normaal toegewezen. Aanvragen met hetzelfde belangrijkniveau hebben hetzelfde planningsgedrag dat vandaag bestaat.

## <a name="importance-scenarios"></a>Belangscenario's

Naast het hierboven beschreven basisscenario met verkoop- en weergegevens, zijn er andere scenario's waarin het belang van werkbelasting helpt te voldoen aan de behoeften van gegevensverwerking en query's.

### <a name="locking"></a>Vergrendelen

Toegang tot sloten voor lees- en schrijfactiviteit is een gebied van natuurlijke twist. Activiteiten zoals [partitieschakelen](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) of [OBJECT RENAME](/sql/t-sql/statements/rename-transact-sql?view=azure-sqldw-latest) vereisen verhoogde vergrendelingen.  Zonder het belang van de werkbelasting optimaliseert Synapse SQL-pool in Azure Synapse voor doorvoer. Optimaliseren voor doorvoer betekent dat wanneer uitvoerende en in de wachtrij staande aanvragen dezelfde vergrendelingsbehoeften hebben en dat resources beschikbaar zijn, de wachtrijaanvragen aanvragen kunnen omzeilen met hogere vergrendelingsbehoeften die eerder in de aanvraagwachtrij zijn aangekomen. Zodra het werkbelastingbelang wordt toegepast op aanvragen met hogere vergrendelingsbehoeften. Verzoek met een hoger belang zal worden uitgevoerd voor de aanvraag met een lager belang.

Kijk een naar het volgende voorbeeld:

- Q1 is actief bezig met het uitvoeren en selecteren van gegevens van SalesFact.
- Q2 staat in de wachtrij in de rij om te wachten tot Q1 is voltooid.  Het werd ingediend om 9 uur en probeert om nieuwe gegevens over te schakelen naar SalesFact.
- Q3 wordt om 9:01 uur ingediend en wil gegevens selecteren van SalesFact.

Als Q2 en Q3 hetzelfde belang hebben en Q1 nog steeds wordt uitgevoerd, zal Q3 beginnen met uitvoeren. Q2 blijft wachten op een exclusief slot op SalesFact.  Als Q2 belangrijker is dan Q3, zal Q3 wachten tot Q2 klaar is voordat het kan beginnen met de uitvoering.

### <a name="non-uniform-requests"></a>Niet-uniforme verzoeken

Een ander scenario waarin belangrijk kan helpen voldoen aan query-eisen is wanneer aanvragen met verschillende resourceklassen worden ingediend.  Zoals eerder vermeld, onder hetzelfde belang, synapse SQL pool in Azure Synapse optimaliseert voor doorvoer. Wanneer aanvragen voor gemengde grootte (zoals smallrc of mediumrc) in de wachtrij staan, kiest Synapse SQL-groep de vroegste binnenkomende aanvraag die binnen de beschikbare resources past. Als het werkbelastingbelang wordt toegepast, wordt vervolgens de aanvraag voor het hoogste belang gepland.
  
Overweeg het volgende voorbeeld op DW500c:

- Q1, Q2, Q3 en Q4 worden uitgevoerd smallrc query's.
- Q5 wordt ingediend met de mediumrc resource klasse om 9 uur.
- Q6 wordt om 9:01 uur ingediend met de smallrc resourceklasse.

Omdat Q5 mediumrc is, vereist het twee gelijktijdigheidssleuven. Q5 moet wachten tot twee van de lopende query's zijn voltooid.  Wanneer een van de query's (Q1-Q4) is voltooid, wordt Q6 echter onmiddellijk gepland omdat de resources bestaan om de query uit te voeren.  Als Q5 belangrijker is dan Q6, wacht Q6 tot Q5 wordt uitgevoerd voordat het kan beginnen met uitvoeren.

## <a name="next-steps"></a>Volgende stappen

- Zie de [KLASSER (Transact-SQL) maken voor](/sql/t-sql/statements/create-workload-classifier-transact-sql)meer informatie over het maken van een classificatie.  
- Zie [Workloadclassificatie](sql-data-warehouse-workload-classification.md)voor meer informatie over workloadclassificatie.  
- Zie de classificatie Snelstart [Werkbelasting maken voor](quickstart-create-a-workload-classifier-tsql.md) het maken van een classificatie voor werkbelasting.
- Bekijk de how-to-artikelen om [het belang van workloads](sql-data-warehouse-how-to-configure-workload-importance.md) te configureren en hoe [u Workload Management beheren en bewaken.](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
- Zie [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) om query's en het toegewezen belang weer te geven.
