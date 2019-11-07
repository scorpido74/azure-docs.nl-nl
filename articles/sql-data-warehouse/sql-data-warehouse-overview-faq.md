---
title: Veelgestelde vragen over Azure Synapse Analytics (voorheen SQL DW)
description: In dit artikel vindt u een lijst met veelgestelde vragen over Azure Synapse Analytics (voorheen SQL DW) van klanten en ontwikkel aars
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: f7b4f926bb9611d87c67276f754a6b596850b59d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645590"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Veelgestelde vragen over Azure Synapse Analytics (voorheen SQL DW)

## <a name="general"></a>Algemeen

V. Wat is Azure Synapse?

A. Azure Synapse is een oneindig analyse service waarmee gegevens-en Big data-analyses worden gecombineerd. Het biedt u de vrijheid om gegevens op uw voor waarden op te vragen met behulp van serverloze on-demand of ingerichte resources op schaal. Azure Synapse brengt deze twee werelden samen met een uniforme ervaring om gegevens op te nemen, voor te bereiden, te beheren en te verwerken voor direct BI en machine learning behoeften. Zie [Wat is Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)? voor meer informatie.

V. Wat is er gebeurd met Azure SQL Data Warehouse?

A. Azure Synapse is Azure SQL Data Warehouse (SQL DW) ontwikkeld. We hebben hetzelfde toonaangevende data warehouse gebruikt voor een geheel nieuw niveau van prestaties en mogelijkheden. U kunt nu door gaan met het uitvoeren van uw bestaande Data Warehouse-workloads in productie met Azure Synapse en automatisch profiteren van de nieuwe mogelijkheden die in Preview zijn. Zie [Wat is Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)? voor meer informatie.

V. Wat is SQL Analytics?

A. SQL Analytics verwijst naar de functies voor Enter prise data warehousing die algemeen beschikbaar zijn met Azure Synapse. Zie [Wat is Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)? voor meer informatie.

V. Hoe kan ik aan de slag met Azure Synapse?

A. U kunt aan de slag met een [gratis Azure-account](https://azure.microsoft.com/free/sql-data-warehouse/) of [contact opnemen met de verkoop voor meer informatie](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html). 

V. Wat biedt Azure Synapse voor de beveiliging van gegevens?

A. Azure Synapse biedt verschillende oplossingen voor het beveiligen van gegevens, zoals TDE en controle. Zie [beveiliging]voor meer informatie.

V. Waar kan ik zien welke juridische of zakelijke standaarden Azure Synapse compatibel is met?

A. Ga naar de [Naleving van micro soft] pagina voor diverse nalevings aanbiedingen per product zoals Soc en ISO. Kies eerst op nalevings titel en vouw vervolgens Azure uit in de sectie micro soft in-Scope Cloud Services aan de rechter kant van de pagina om te zien welke Services compatibel zijn met Azure Synapse.

V. Kan ik Power BI verbinding maken?

A. Ja. Hoewel Power BI directe query met Azure Synapse ondersteunt, is het niet bedoeld voor een groot aantal gebruikers of realtime-gegevens. Als u de prestaties van Power BI verder wilt optimaliseren, kunt u Power BI gebruiken boven op Azure Analysis Services of Analysis Service IaaS.

V. Wat zijn de capaciteits limieten van SQL Analytics?

A. Bekijk onze huidige [capaciteits limieten] pagina. 

V. Waarom duurt mijn schaal/onderbreken/hervatten?

A. Verschillende factoren kunnen invloed hebben op de tijd voor reken beheer bewerkingen. Een veelvoorkomend geval voor langlopende bewerkingen is transactioneel terugdraaien. Wanneer een schaal-of onderbrekings bewerking wordt gestart, worden alle binnenkomende sessies geblokkeerd en worden query's leeg gemaakt. Om ervoor te zorgen dat het systeem stabiel blijft, moeten trans acties worden teruggedraaid voordat een bewerking kan worden begonnen. Hoe groter het aantal en hoe groter de logboek grootte van trans acties, hoe langer de bewerking het systeem kan herstellen naar een stabiele status.

## <a name="user-support"></a>Gebruikers ondersteuning

V. Ik heb een functie verzoek, waar kan ik het verzenden?

A. Als u een functie aanvraag hebt, moet u deze verzenden op onze [UserVoice] -pagina

V. Hoe kan ik x doen?

A. Voor hulp bij het ontwikkelen met Azure Synapse kunt u vragen stellen op onze [stack overflow] -pagina. 

V. Hoe kan ik een ondersteunings ticket verzenden?

A. [Ondersteunings tickets] kunnen via Azure Portal worden ingediend.

## <a name="sql-languagefeature-support"></a>Ondersteuning voor SQL-taal/-functie 

V. Welke gegevens typen worden ondersteund?

A. Zie [gegevens typen].

V. Welke tabel functies worden ondersteund?

A. Veel functies worden ondersteund, de onderdelen die niet worden ondersteund en die in niet- [Niet-ondersteunde tabel functies]worden beschreven.

## <a name="tooling-and-administration"></a>Hulp middelen en beheer

V. Biedt ondersteuning voor database projecten in Visual Studio.

A. Data base-projecten worden momenteel niet ondersteund in Visual Studio. Als u een stemming wilt casten om deze functie te downloaden, gaat u naar onze [Functie aanvraag voor database projecten]voor gebruikers voice-database projecten.

V. Ondersteunt SQL Analytics REST Api's?

A. Ja. De meeste REST-functionaliteit die met SQL Database kan worden gebruikt, is ook beschikbaar met SQL Analytics. U vindt API-informatie in REST-documentatie pagina's of [MSDN].


## <a name="loading"></a>Bezig met laden

V. Welke client Stuur Programma's worden ondersteund?

A. Stuur programma-ondersteuning voor DW vindt u op de pagina [Verbindingsreeksen]

V: welke bestands indelingen worden ondersteund door poly base?

A: Orc, RC, Parquet en platte tekst met scheidings tekens

V: met welke gegevens bronnen kan ik verbinding maken met poly base? 

A: [Azure data Lake Store] -en [Azure Storage blobs]

V: is de reken pushdown mogelijk bij het verbinden met Azure Storage blobs of ADLS? 

A: Nee, poly base werkt alleen met de opslag onderdelen. 

V: kan ik verbinding maken met HDI?

A: HDI kan ADLS of WASB als de HDFS-laag gebruiken. Als u de laag HDFS hebt, kunt u die gegevens laden in SQL DW. U kunt echter geen pushdown berekening genereren voor het HDI-exemplaar. 

## <a name="next-steps"></a>Volgende stappen
Zie onze pagina [overzicht] voor meer informatie over Azure Synapse als geheel.


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Verbindingsreeksen]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Ondersteunings tickets]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Beveiliging]: ./sql-data-warehouse-overview-manage-security.md
[Naleving van micro soft]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[capaciteits limieten]: ./sql-data-warehouse-service-capacity-limits.md
[gegevens typen]: ./sql-data-warehouse-tables-data-types.md
[Niet-ondersteunde tabel functies]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Azure Storage blobs]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Functie aanvraag voor database projecten]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[Overzicht]: ./sql-data-warehouse-overview-faq.md
