---
title: Replicatie naar Azure SQL Edge configureren (preview-versie)
description: Meer informatie over het configureren van replicatie naar Azure SQL Edge (preview-versie)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 6249d72ba43bf59a2862595f40adf2d1ac5a6346
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235167"
---
# <a name="configure-replication-to-azure-sql-edge-preview"></a>Replicatie naar Azure SQL Edge configureren (preview-versie) 

Een exemplaar van Azure SQL Edge kan worden geconfigureerd als push-abonnee voor eenrichtings transactionele replicatie of momentopname replicatie. Een exemplaar van Azure SQL Edge kan niet fungeren als de uitgever of de Distributor voor een transactionele replicatie Configuratie. Samenvoeg replicatie, P2P-replicatie, Oracle-publicatie wordt niet ondersteund met Azure SQL Edge.

## <a name="supported-configurations"></a>**Ondersteunde configuraties**:
  
- Een exemplaar van Azure SQL Edge moet een push-abonnee zijn voor een uitgever.
- De uitgever en de Distributor kunnen
   - Een exemplaar van SQL Server dat on-premises of een exemplaar van SQL Server wordt uitgevoerd op een virtuele machine van Azure. Zie [SQL Server op Azure virtual machines Overview](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/)voor meer informatie. SQL Server exemplaren moeten een versie hebben die groter is dan SQL Server 2016.
   - Een exemplaar van een door Azure SQL beheerd exemplaar. Een beheerd exemplaar kan Publisher-, Distributor-en Subscriber-data bases hosten. Zie [replicatie met SQL database Managed instance](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/)voor meer informatie.

- De distributie database en de replicatie agenten kunnen niet worden geplaatst op een Azure SQL Edge-exemplaar.  

> [!NOTE]
> Poging tot het configureren van een replicatie met een niet-ondersteunde versie kan leiden tot een fout nummer MSSQL_REPL20084 (het proces kan geen verbinding maken met de abonnee.) en MSSQL_REPL40532 (kan de server \<name> die door de aanmelding is aangevraagd, niet openen. De aanmelding is mislukt.)  

Als u alle functies van Azure SQL Edge wilt gebruiken, moet u de nieuwste versies van [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) en [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)gebruiken.  

## <a name="remarks"></a>Opmerkingen

- Replicatie kan worden geconfigureerd met behulp van [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) of door Transact-SQL-instructies uit te voeren op de Publisher met behulp van SQL Server Management Studio of [Azure data base Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
- Replicatie kan alleen SQL Server verificatie aanmeldingen gebruiken om verbinding te maken met een Azure SQL Edge-exemplaar.
- Gerepliceerde tabellen moeten een primaire sleutel hebben.
- Eén publicatie op SQL Server kan zowel Azure SQL-Edge als SQL Server (on-premises en SQL Server in een virtuele machine van Azure) ondersteunen.  
- Replicatie beheer, bewaking en probleem oplossing moeten worden uitgevoerd vanuit het SQL Server-exemplaar.  
- Alleen push-abonnementen naar Azure SQL Edge worden ondersteund.  
- `@subscriber_type = 0`Wordt alleen ondersteund in **Sp_addsubscription** voor Azure SQL Edge.  
- Azure SQL Edge biedt geen ondersteuning voor bidirectionele, onmiddellijke, bijwerk bare of peer-to-peer-replicatie.
- Azure SQL Edge ondersteunt alleen een subset van functies die beschikbaar zijn in SQL Server of een door SQL beheerd exemplaar, omdat een poging om een Data Base (of objecten in de data base) te repliceren die een of meer niet-ondersteunde functies bevatten, een fout veroorzaakt. Als u bijvoorbeeld een Data Base probeert te repliceren die objecten bevat met ruimtelijke gegevens typen, resulteert dit in een fout. Zie [ondersteunde functies van Azure SQL Edge](features.md)voor meer informatie over functies die door Azure SQL Edge worden ondersteund.

## <a name="scenarios"></a>Scenario's  

### <a name="initializing-reference-data-on-an-edge-instance"></a>Referentie gegevens voor een Edge-instantie initialiseren

Een veelvoorkomend scenario waarbij replicatie nuttig kan zijn wanneer het Edge-exemplaar moet worden geïnitialiseerd met referentie gegevens die na verloop van tijd worden gewijzigd. U kunt bijvoorbeeld ML-modellen bijwerken op het rand exemplaar nadat ze zijn getraind op een SQL Server-exemplaar.

1. Een transactionele replicatie publicatie maken op een SQL Server Data Base.  
2. Gebruik op het SQL Server-exemplaar de **wizard Nieuw abonnement** of de Transact-SQL-instructies om een push te maken voor een abonnement op Azure SQL Edge.  
3. De gerepliceerde Data Base op Azure SQL Edge kan worden geïnitialiseerd door gebruik te maken van een moment opname die is gegenereerd door de momentopname agent en die wordt gedistribueerd en geleverd door de distributie agent of een back-up van de data base van de uitgever. Als de back-up van de data base objecten/onderdelen bevat die niet worden ondersteund door Azure SQL Edge, mislukt de herstel bewerking.

## <a name="limitations"></a>Beperkingen

De volgende opties worden niet ondersteund voor Azure SQL Edge-abonnementen:

- Koppeling bestands groepen kopiëren  
- Schema's voor het partitioneren van tabellen kopiëren  
- Schema's voor het partitioneren van indexen kopiëren  
- Door de gebruiker gedefinieerde statistieken kopiëren  
- Standaard bindingen kopiëren  
- Regel bindingen kopiëren  
- Volledige-tekst indexen kopiëren  
- XSD van kopiëren XML  
- XML-indexen kopiëren  
- Machtigingen kopiëren  
- Ruimtelijke indexen kopiëren  
- Gefilterde indexen kopiëren  
- Gegevens compressie kenmerk kopiëren  
- Sparse kolom kenmerk kopiëren  
- Kopieer FileStream-, hierarchyid-of ruimtelijke gegevens typen.
- Hierarchyid naar MAX-gegevens typen converteren  
- Ruimtelijke naar maximum aantal gegevens typen converteren  
- Uitgebreide eigenschappen kopiëren  
- Machtigingen kopiëren  

## <a name="examples"></a>Voorbeelden

Maak een publicatie en een push-abonnement. Zie voor meer informatie:
  
- [Een publicatie maken](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Maak een push-abonnement](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) met behulp van de naam/IP van de Azure SQL Edge-server als de abonnee (bijvoorbeeld **myEdgeinstance, 1433**) en een database naam op het exemplaar van de Azure SQL-Edge als de doel database (bijvoorbeeld **AdventureWorks**).  

## <a name="see-also"></a>Zie ook  

- [Een publicatie maken](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Een push-abonnement maken](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typen replicatie](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Bewaking (replicatie)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Een abonnement initialiseren](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


