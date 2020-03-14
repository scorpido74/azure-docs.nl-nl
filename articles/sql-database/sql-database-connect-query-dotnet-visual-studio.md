---
title: Visual Studio met .NET gebruiken en C# query's uitvoeren
description: Gebruik Visual Studio om een C#-toepassing te maken die is verbonden met een Azure SQL-database en query's uitvoert op deze database met behulp van Transact-SQL-instructies.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/31/2019
ms.openlocfilehash: 75f84f87fc59a870a0c88fd24cb1b1b8894b3e2a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79209577"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Snelstartgids: .NET en C# in Visual Studio gebruiken om verbinding te maken met een Azure-SQL database en een query uit te zoeken

In deze Quick start ziet u hoe [.NET Framework](https://www.microsoft.com/net/) u de C# .NET Framework en code in Visual Studio kunt gebruiken om een query uit te voeren op een Azure SQL database met Transact-SQL-instructies.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze snelstartgids te voltooien:

- Een Azure SQL-database. U kunt een van deze quickstarts gebruiken om een database te maken en vervolgens te configureren in Azure SQL Database:

  || Individuele database | Beheerd exemplaar |
  |:--- |:--- |:---|
  | Maken| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configureren | [IP-firewallregel op serverniveau](sql-database-server-level-firewall-rule.md)| [Connectiviteit vanaf een VM](sql-database-managed-instance-configure-vm.md)|
  |||[Connectiviteit vanaf locatie](sql-database-managed-instance-configure-p2s.md)
  |Gegevens laden|Adventure Works geladen volgens de quickstart|[Wide World Importers herstellen](sql-database-managed-instance-get-started-restore.md)
  |||Adventure Works herstellen of importeren vanuit [BACPAC](sql-database-import.md) -bestand vanuit [github](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > De scripts in dit artikel zijn geschreven voor gebruik met de Adventure Works-database. Met een beheerd exemplaar moet u de Adventure Works-database in een exemplaardatabase importeren of de scripts in dit artikel wijzigen zodat deze de Wide World Importers-database gebruiken.

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community, Professional of ENTER prise Edition.

## <a name="get-sql-server-connection-information"></a>Verbindingsgegevens voor SQL Server ophalen

Haal de verbindingsgegevens op die u nodig hebt om verbinding te maken met de Azure SQL-database. U hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig voor de volgende procedures.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Navigeer naar de pagina **SQL-database** of **Met SQL beheerde exemplaren**.

3. Bekijk op de pagina **Overzicht** de volledig gekwalificeerde servernaam naast **Servernaam** voor een individuele database, of de volledig gekwalificeerde servernaam naast **Host** voor een beheerd exemplaar. Als u de servernaam of hostnaam wilt kopiëren, plaatst u de muisaanwijzer erop en selecteert u het pictogram **Kopiëren**. 

## <a name="create-code-to-query-the-sql-database"></a>Code maken om query's uit te voeren op de SQL-database

1. Selecteer in Visual Studio **Bestand** > **Nieuw** > **Project**. 
   
1. Selecteer in het dialoogvenster **Nieuw project** achtereenvolgens **Visual C#** en **Consoletoepassing (.NET Framework)** .
   
1. Voer *sqltest* in voor de projectnaam en selecteer vervolgens **OK**. Het nieuwe project wordt gemaakt. 
   
1. Selecteer **Project** > **NuGet-pakketten beheren**. 
   
1. Selecteer in **NuGet Package Manager** het tabblad **Bladeren** en zoek en selecteer **System.Data.SqlClient**.
   
1. Selecteer op de pagina **System.Data.SqlClient** de optie **Installeren**. 
   - Selecteer **OK** om door te gaan met de installatie. 
   - Als een venster voor **akkoord gaan met de licentie** wordt weergegeven, selecteert u **Ik ga akkoord**.
   
1. Wanneer de installatie is voltooid, kunt u **NuGet Package Manager** sluiten. 
   
1. Vervang in de code-editor de inhoud **Program.cs** door de volgende code. Vervang de waarden voor `<server>`, `<username>`, `<password>`en `<database>`.
   
   >[!IMPORTANT]
   >Voor de code in dit voorbeeld worden de voorbeeldgegevens gebruikt van AdventureWorksLT, die u als bron kunt kiezen bij het maken van uw database. Als in uw database andere gegevens staan, kunt u tabellen uit uw eigen database gebruiken in de SELECT-query. 
   
   ```csharp
   using System;
   using System.Data.SqlClient;
   using System.Text;
   
   namespace sqltest
   {
       class Program
       {
           static void Main(string[] args)
           {
               try 
               { 
                   SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                   builder.DataSource = "<server>.database.windows.net"; 
                   builder.UserID = "<username>";            
                   builder.Password = "<password>";     
                   builder.InitialCatalog = "<database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
                           using (SqlDataReader reader = command.ExecuteReader())
                           {
                               while (reader.Read())
                               {
                                   Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                               }
                           }
                       }                    
                   }
               }
               catch (SqlException e)
               {
                   Console.WriteLine(e.ToString());
               }
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>De code uitvoeren

1. Om de app uit te voeren, selecteert u **Fouten opsporen** > **Foutopsporing starten** of selecteert u **Start** op de werkbalk of drukt u op **F5**.
1. Controleer of de bovenste twintig rijen Categorie/Product in uw database worden geretourneerd en sluit vervolgens het appvenster.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [verbinding maken met en een query uitvoeren op een Azure SQL-database met behulp van .NET Core](sql-database-connect-query-dotnet-core.md) in Windows/Linux/macOS.  
- Meer informatie over [Aan de slag met .NET Core in Windows/Linux/macOS met behulp van de opdrachtregel](/dotnet/core/tutorials/using-with-xplat-cli).
- Meer informatie over [Uw eerste Azure SQL-database ontwerpen met behulp van SSMS](sql-database-design-first-database.md) of [Uw eerste Azure SQL-database ontwerpen met behulp van .NET](sql-database-design-first-database-csharp.md).
- Raadpleeg de [.NET-documentatie](https://docs.microsoft.com/dotnet/) voor meer informatie over .NET.
- Voor beeld van een logische poging: [Maak robuuste verbinding met SQL met ADO.net][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

