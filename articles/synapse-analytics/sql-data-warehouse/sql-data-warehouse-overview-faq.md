---
title: Azure Synapse Analytics (voorheen SQL DW) veelgestelde vragen
description: In dit artikel worden veelgestelde vragen over Azure Synapse Analytics (voorheen SQL DW) van klanten en ontwikkelaars weergegeven
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 766615a92e616a254cca4b2b3ce1ccde40118275
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350255"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure Synapse Analytics (voorheen SQL DW) Veelgestelde vragen

## <a name="general"></a>Algemeen

V. Wat is Azure Synapse?

A. Azure Synapse is een onbegrensde analyseservice die datawarehousing en Big Data-analyses samenbrengt. Het geeft u de vrijheid om gegevens op te vragen over uw voorwaarden, met behulp van serverloze on-demand of ingerichte bronnen - op schaal. Azure Synapse brengt deze twee werelden samen met een uniforme ervaring om gegevens in te nemen, voor te bereiden, te beheren en te leveren voor onmiddellijke BI- en machine learning-behoeften. Zie [Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)voor meer informatie.

V. Wat is er gebeurd met Azure SQL Data Warehouse?

A. Azure Synapse is Azure SQL Data Warehouse (SQL DW) geëvolueerd. We hebben hetzelfde toonaangevende datawarehouse naar een geheel nieuw niveau van prestaties en mogelijkheden gebracht. U uw bestaande datawarehouseworkloads in productie blijven uitvoeren met Azure Synapse en automatisch profiteren van de nieuwe mogelijkheden, die in voorbeeldzijn. Zie [Wat is Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)voor meer informatie.

V. Wat is SQL Analytics?

A. SQL Analytics verwijst naar de enterprise data warehousing-functies die algemeen beschikbaar zijn met Azure Synapse. Zie [Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)voor meer informatie.

V. Hoe ga ik aan de slag met Azure Synapse?

A. U aan de slag met een [gratis Azure-account](https://azure.microsoft.com/free/sql-data-warehouse/) of [contact opnemen met verkoop voor meer informatie.](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html) 

V. Wat biedt Azure Synapse voor gegevensbeveiliging?

A. Azure Synapse biedt verschillende oplossingen voor het beschermen van gegevens zoals TDE en auditing. Zie [Beveiliging voor](sql-data-warehouse-overview-manage-security.md)meer informatie.

V. Waar kan ik erachter komen met welke wettelijke of zakelijke standaarden Azure Synapse voldoet?

A. Ga naar de [Microsoft Compliance-pagina](https://www.microsoft.com/trustcenter/compliance/complianceofferings) voor verschillende compliance-aanbiedingen per product zoals SOC en ISO. Kies eerst op nalevingstitel. Vouw Azure vervolgens uit in de sectie Microsoft in-scope cloudservices aan de rechterkant van de pagina om te zien welke services Azure Synapse compatibel zijn.

V. Kan ik Power BI aansluiten?

A. Ja. Hoewel Power BI directe query's ondersteunt met Azure Synapse, is het niet bedoeld voor een groot aantal gebruikers of realtime gegevens. Als u de Prestaties van Power BI verder wilt optimaliseren, u overwegen Power BI te gebruiken bovenop Azure Analysis Services of Analysis Service IaaS.

V. Wat zijn SQL Analytics-capaciteitslimieten?

A. Zie onze pagina met huidige [capaciteitslimieten.](sql-data-warehouse-service-capacity-limits.md) 

V. Waarom duurt het zo lang met mijn schaal/pauze/cv?

A. Verschillende factoren kunnen de tijd voor compute management operations beïnvloeden. Een veelvoorkomend geval voor langlopende bewerkingen is transactionele terugdraaiing. Wanneer een schaal- of pauzebewerking wordt gestart, worden alle binnenkomende sessies geblokkeerd en worden query's verwijderd. Om het systeem in een stabiele toestand te laten, moeten transacties worden teruggedraaid voordat een bewerking kan beginnen. Hoe groter het aantal en hoe groter de logboekgrootte van transacties, hoe langer de bewerking wordt vastgelopen door het systeem in een stabiele toestand te herstellen.

## <a name="user-support"></a>Gebruikersondersteuning

V. Ik heb een functieaanvraag, waar kan ik deze indienen?

A. Als je een functieaanvraag hebt, dien je deze in op onze [UserVoice-pagina](https://feedback.azure.com/forums/307516-sql-data-warehouse)

V. Hoe kan ik x doen?

A. Voor hulp bij het ontwikkelen met Azure Synapse u vragen stellen op onze [Stack Overflow-pagina.](https://stackoverflow.com/questions/tagged/azure-sqldw) 

V. Hoe dien ik een ondersteuningsticket in?

A. [Ondersteuningstickets](sql-data-warehouse-get-started-create-support-ticket.md) kunnen worden ingediend via azure portal.

## <a name="sql-languagefeature-support"></a>ONDERSTEUNING voor SQL-taal/functie 

V. Welke gegevenstypen worden ondersteund?

A. Zie [gegevenstypen](sql-data-warehouse-tables-data-types.md).

V. Welke tafelfuncties ondersteunt u?

A. Veel functies worden ondersteund. Functies die niet worden ondersteund, zijn te vinden in [niet-ondersteunde tabelfuncties.](sql-data-warehouse-tables-data-types.md)

## <a name="tooling-and-administration"></a>Tooling en administratie

V. Ondersteunt SQL Analytics REST API's?

A. Ja. De meeste REST-functionaliteit die kan worden gebruikt met SQL Database is ook beschikbaar met SQL Analytics. U API-informatie vinden op REST-documentatiepagina's of [MSDN.](https://msdn.microsoft.com/library/azure/mt163685.aspx)


## <a name="loading"></a>Laden

V. Welke client drivers ondersteunt u?

A. Driver-ondersteuning voor SQL Analytics is te vinden op de pagina [Verbindingstekenreeksen](sql-data-warehouse-connection-strings.md)

V: Welke bestandsindelingen worden ondersteund door PolyBase?

A: Orc, RC, Parket en platte afgebakende tekst

V: Met welke gegevensbronnen kan ik verbinding maken met PolyBase? 

A: [Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) en Azure Storage [Blobs](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

V: Is berekening pushdown mogelijk bij het maken van verbinding met Azure Storage Blobs of ADLS? 

A: Nee, PolyBase werkt alleen samen met de opslagcomponenten. 

V: Kan ik verbinding maken met HDI?

A: HDI kan ADLS of WASB gebruiken als HDFS-laag. Als u een hdfs-laag hebt, u die gegevens in een SQL Analytics-gegevensmagazijn laden. U echter geen pushdown-berekening genereren naar de HDI-instantie. 

## <a name="next-steps"></a>Volgende stappen
Zie onze [overzichtspagina](sql-data-warehouse-overview-faq.md) voor meer informatie over Azure Synapse als geheel.
