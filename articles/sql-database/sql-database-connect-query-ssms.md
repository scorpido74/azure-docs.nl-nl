---
title: 'SSMS: verbinding maken en gegevens opvragen'
description: Ontdek hoe u verbinding maakt met SQL Database in Azure met behulp van SQL Server Management Studio (SSMS). Voer vervolgens T-SQL-instructies (Transact-SQL) uit om query's uit te voeren voor gegevens en om gegevens te bewerken.
keywords: verbinding maken met SQL-database,Sql Server Management Studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 69a6939cdbcc9b2c4e496c8d47aa20c9f6e85d57
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826937"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Snelstartgids: SQL Server Management Studio gebruiken om verbinding te maken met een Azure-SQL database

In deze Quick Start gebruikt u [SQL Server Management Studio][ssms-install-latest-84g] (SSMS) om verbinding te maken met een Azure SQL database. Vervolgens gaat u Transact-SQL-instructies uitvoeren om gegevens op te vragen, in te voegen, bij te werken en te verwijderen. U kunt SSMS gebruiken voor het beheren van een SQL-infrastructuur, van SQL Server tot SQL Database voor Microsoft Windows.  

## <a name="prerequisites"></a>Vereisten

Een Azure SQL-database. U kunt een van deze quickstarts gebruiken om een database te maken en vervolgens te configureren in Azure SQL Database:

  || Individuele database | Beheerd exemplaar |
  |:--- |:--- |:---|
  | Maken| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configureren | [IP-firewallregel op serverniveau](sql-database-server-level-firewall-rule.md)| [Connectiviteit vanaf een VM](sql-database-managed-instance-configure-vm.md)|
  |||[Connectiviteit vanaf locatie](sql-database-managed-instance-configure-p2s.md)
  |Gegevens laden|Adventure Works geladen volgens de quickstart|[Wide World Importers herstellen](sql-database-managed-instance-get-started-restore.md)
  |||Adventure Works herstellen of importeren vanuit [BACPAC](sql-database-import.md) -bestand vanuit [github](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|

  > [!IMPORTANT]
  > De scripts in dit artikel zijn geschreven voor gebruik met de Adventure Works-database. Met een beheerd exemplaar moet u de Adventure Works-database importeren in een exemplaardatabase of de scripts in dit artikel wijzigen voor gebruik van de Wide World Importers-database.

## <a name="install-the-latest-ssms"></a>De nieuwste SSMS installeren

Voordat u begint, moet u ervoor zorgen dat u de laatste [SSMS][ssms-install-latest-84g]hebt geïnstalleerd.

## <a name="get-sql-server-connection-information"></a>Verbindingsgegevens voor SQL Server ophalen

Haal de verbindingsgegevens op die u nodig hebt om verbinding te maken met de Azure SQL-database. U hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig voor de volgende procedures.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

2. Navigeer naar de pagina **SQL-database** of **Met SQL beheerde exemplaren**.

3. Bekijk op de pagina **Overzicht** de volledig gekwalificeerde servernaam naast **Servernaam** voor een individuele database, of de volledig gekwalificeerde servernaam naast **Host** voor een beheerd exemplaar. Als u de servernaam of hostnaam wilt kopiëren, plaatst u de muisaanwijzer erop en selecteert u het pictogram **Kopiëren**.

## <a name="connect-to-your-database"></a>Verbinding maken met uw database

In SMSS maakt u verbinding met uw Azure SQL Database-server.

> [!IMPORTANT]
> Een Azure SQL Database-server luistert naar poort 1433. Om verbinding te maken met een SQL Database-server achter een firewall van het bedrijf, moet de firewall voor deze poort zijn geopend.
>

1. Open SQL Server Management Studio. Het dialoogvenster **Verbinding maken met server** wordt geopend.

2. Voer de volgende informatie in:

   | Instelling      | Voorgestelde waarde    | Beschrijving |
   | ------------ | ------------------ | ----------- |
   | **Servertype** | Database-engine | Vereiste waarde. |
   | **Servernaam** | De volledig gekwalificeerde servernaam | Dit moet er ongeveer als volgt uitzien: **mijnnieuweserver20170313.database.windows.net**. |
   | **Verificatie** | SQL Server-verificatie | In deze zelfstudie wordt gebruik gemaakt van SQL-verificatie. |
   | **Aanmelding** | Gebruikers-id voor het beheerdersaccount voor de server | De gebruikers-id van het serverbeheerdersaccount dat wordt gebruikt voor het maken van de server. |
   | **Wachtwoord** | Het wachtwoord voor het serverbeheerdersaccount | Het wachtwoord van het serverbeheerdersaccount dat wordt gebruikt voor het maken van de server. |
   ||||

   ![verbinding maken met server](./media/sql-database-connect-query-ssms/connect.png)  

3. Selecteer **Opties** in het dialoogvenster **Verbinding maken met server**. In de vervolgkeuzelijst **Verbinding maken met database** selecteert u **mySampleDatabase**.

   ![verbinding maken met database op server](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Selecteer **Verbinden**. Het venster Objectverkenner wordt geopend.

5. Als u de objecten van de database wilt weergeven, vouwt u **Databases** uit en vouwt u vervolgens **mySampleDatabase** uit.

   ![mySampleDatabase-objecten](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Querygegevens

Voer deze Transact-SQL \[SELECT](https://msdn.microsoft.com/library/ms189499.aspx)-code uit om op categorie een query uit te voeren voor de twintig populairste producten.

1. Klik in Objectverkenner met de rechtermuisknop op **mySampleDatabase** en selecteer vervolgens **Nieuwe query**. Er wordt een nieuw queryvenster geopend dat is verbonden met uw database.

2. Plak deze SQL-query in het queryvenster.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Selecteer op de werkbalk **Uitvoeren** om gegevens op te halen uit de tabellen `Product` en `ProductCategory`.

    ![query om gegevens op te halen uit tabel product en ProductCategory](./media/sql-database-connect-query-ssms/query2.png)

## <a name="insert-data"></a>Gegevens invoegen

Voer deze Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)-code uit om een nieuw product te maken in de tabel `SalesLT.Product`.

1. Vervang de vorige query door de volgende.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Selecteer **Uitvoeren** om een nieuwe rij in te voegen in de tabel `Product`. In het deelvenster **Berichten** wordt **(1 rij beïnvloedt)** weergegeven.

## <a name="view-the-result"></a>Het resultaat weergeven

1. Vervang de vorige query door de volgende.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Selecteer **Uitvoeren**. Het volgende resultaat wordt weer gegeven.

   ![resultaat van query van product tabel](./media/sql-database-connect-query-ssms/result.png)

## <a name="update-data"></a>Gegevens bijwerken

Voer deze [Update](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-code uit om het nieuwe product te wijzigen.

1. Vervang de vorige query door de volgende.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Selecteer **Uitvoeren** om de opgegeven rij in de tabel `Product` bij te werken. In het deelvenster **Berichten** wordt **(1 rij beïnvloedt)** weergegeven.

## <a name="delete-data"></a>Gegevens verwijderen

Voer deze Transact-SQL [DELETE](https://msdn.microsoft.com/library/ms189835.aspx)-code uit om uw nieuwe product te verwijderen.

1. Vervang de vorige query door de volgende.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Selecteer **Uitvoeren** om de opgegeven rij in de tabel `Product` te verwijderen. In het deelvenster **Berichten** wordt **(1 rij beïnvloedt)** weergegeven.

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL Server Management Studio ](https://msdn.microsoft.com/library/ms174173.aspx) voor meer informatie over SSMS.
- Als u verbinding wilt maken en query's wilt uitvoeren met Azure Portal, raadpleegt u [Connect and query with the Azure Portal SQL query editor](sql-database-connect-query-portal.md) (Verbinding maken en query's uitvoeren met de SQL-query-editor in Azure Portal).
- Zie [Verbinding maken en query's uitvoeren met Visual Studio Code](sql-database-connect-query-vscode.md) als u verbinding wilt maken en query’s wilt uitvoeren met Visual Studio Code.
- Zie [Verbinding maken en query’s uitvoeren met .NET](sql-database-connect-query-dotnet.md) als u verbinding wilt maken en query’s wilt uitvoeren met .NET.
- Zie [Verbinding maken en query's uitvoeren met PHP](sql-database-connect-query-php.md) als u verbinding wilt maken en query's wilt uitvoeren met PHP.
- Zie [Verbinding maken en query's uitvoeren met Node.js](sql-database-connect-query-nodejs.md) als u verbinding wilt maken en query's wilt uitvoeren met Node.js.
- Zie [Verbinding maken en query's uitvoeren met Java](sql-database-connect-query-java.md) als u verbinding wilt maken en query's wilt uitvoeren met Java.
- Zie [Verbinding maken en query's uitvoeren met Python](sql-database-connect-query-python.md) als u verbinding wilt maken en query's wilt uitvoeren met Python.
- Zie [Verbinding maken en query's uitvoeren met Ruby](sql-database-connect-query-ruby.md) als u verbinding wilt maken en query's wilt uitvoeren met Ruby.

<!-- Article link references. -->

[ssms-install-latest-84g]: https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms
