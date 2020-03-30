---
title: Aan de slag met query's met verschillende gegevens
description: elastische databasequery gebruiken met verticaal verdeelde databases
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: af93035766eaf1afa12d124b8379ee55c5567260
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823801"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Aan de slag met cross-database query's (verticale partitionering) (voorbeeld)

Met elastische databasequery 's voor Azure SQL Database u T-SQL-query's uitvoeren die meerdere databases omvatten met één verbindingspunt. Dit artikel is van toepassing op [verticaal verdeelde databases](sql-database-elastic-query-vertical-partitioning.md).  

Wanneer u klaar bent, leest u hoe u een Azure SQL-database configureert en gebruikt om query's uit te voeren die meerdere gerelateerde databases omvatten.

Zie het overzicht van [elastische databasequery's](sql-database-elastic-query-overview.md)in Azure SQL Database voor meer informatie over de functie elastische databasequery.

## <a name="prerequisites"></a>Vereisten

WIJZIG ELKE externe gegevensbrontoestemming is vereist. Deze toestemming is opgenomen in de toestemming van ALTER DATABASE. WIJZIG ALLE externe gegevensbronmachtigingen zijn nodig om naar de onderliggende gegevensbron te verwijzen.

## <a name="create-the-sample-databases"></a>De voorbeelddatabases maken

Maak om te beginnen twee databases, **klanten** en **orders,** in dezelfde of verschillende SQL Database-servers.

Voer de volgende query's uit in de database **Orders** om de tabel **Orderinformatie** te maken en de voorbeeldgegevens in te voeren.

    CREATE TABLE [dbo].[OrderInformation](
        [OrderID] [int] NOT NULL,
        [CustomerID] [int] NOT NULL
        )
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)

Voer nu de volgende query uit in de database **Klanten** om de tabel **CustomerInformation** te maken en de voorbeeldgegevens in te voeren.

    CREATE TABLE [dbo].[CustomerInformation](
        [CustomerID] [int] NOT NULL,
        [CustomerName] [varchar](50) NULL,
        [Company] [varchar](50) NULL
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
    )
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO')

## <a name="create-database-objects"></a>Databaseobjecten maken

### <a name="database-scoped-master-key-and-credentials"></a>Hoofdsleutel en referenties met databasebereik

1. Open SQL Server Management Studio of SQL Server Data Tools in Visual Studio.
2. Maak verbinding met de database Orders en voer de volgende T-SQL-opdrachten uit:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';  

    De "gebruikersnaam" en "wachtwoord" moet de gebruikersnaam en het wachtwoord worden gebruikt om in te loggen in de klanten database.
    Verificatie met Azure Active Directory met elastische query's wordt momenteel niet ondersteund.

### <a name="external-data-sources"></a>Externe gegevensbronnen

Als u een externe gegevensbron wilt maken, voert u de volgende opdracht uit in de database Orders:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
        (TYPE = RDBMS,
        LOCATION = '<server_name>.database.windows.net',
        DATABASE_NAME = 'Customers',
        CREDENTIAL = ElasticDBQueryCred,
    ) ;

### <a name="external-tables"></a>Externe tabellen

Maak een externe tabel in de database Orders, die overeenkomt met de definitie van de tabel CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
    ( [CustomerID] [int] NOT NULL,
      [CustomerName] [varchar](50) NOT NULL,
      [Company] [varchar](50) NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc)

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Een voorbeeld van een elastische database T-SQL-query uitvoeren

Zodra u uw externe gegevensbron en uw externe tabellen hebt gedefinieerd, u nu T-SQL gebruiken om uw externe tabellen op te vragen. Voer deze query uit in de database Orders:

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
    FROM OrderInformation
    INNER JOIN CustomerInformation
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID

## <a name="cost"></a>Kosten

Momenteel is de functie elastische databasequery opgenomen in de kosten van uw Azure SQL Database.  

Zie [SQL Database Pricing](https://azure.microsoft.com/pricing/details/sql-database)voor prijsinformatie .

## <a name="next-steps"></a>Volgende stappen

* Zie Overzicht van elastische query's voor een overzicht van elastische [query's](sql-database-elastic-query-overview.md).
* Zie [Verticaal verdeelde gegevens opvragen voor](sql-database-elastic-query-vertical-partitioning.md) syntaxis- en voorbeeldquery's voor verticaal verdeelde gegevens)
* Zie [Aan de slag met elastische query voor horizontale partitionering (sharding) voor](sql-database-elastic-query-getting-started.md)een horizontale zelfstudie voor het partitioneren (sharden).
* Zie [Horizontaal gepartitioneerde gegevens opvragen voor](sql-database-elastic-query-horizontal-partitioning.md) syntaxis- en voorbeeldquery's voor horizontaal verdeelde gegevens)
* Zie [\_sp \_remote uitvoeren](https://msdn.microsoft.com/library/mt703714) voor een opgeslagen procedure die een Transact-SQL-instructie uitvoert op één externe Azure SQL-database of set databases die als shards dienen in een horizontaal partitioneringsschema.
