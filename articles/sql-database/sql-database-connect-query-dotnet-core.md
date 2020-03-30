---
title: .NET-kern gebruiken om op te vragen
description: In dit onderwerp wordt beschreven hoe u .NET Core gebruikt om een programma te maken waarmee u verbinding maakt met een Azure SQL-database en waarmee u query's voor deze database uitvoert met behulp van Transact-SQL-instructies.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/29/2019
ms.openlocfilehash: 369c708fd3181076c6deb9d7ac9134c57a18f819
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73827090"
---
# <a name="quickstart-use-net-core-c-to-query-an-azure-sql-database"></a>Snelstart: .NET Core (C#) gebruiken om een query uit te voeren voor een Azure SQL-database

In deze quickstart gebruikt u [.NET Core](https://www.microsoft.com/net/) en C# code om verbinding te maken met een Azure SQL-database. Vervolgens moet u een Transact-SQL-instructie uitvoeren om een query op gegevens uit te voeren.

> [!TIP]
> Met de volgende Microsoft Learn-module u gratis leren hoe u [een ASP.NET-toepassing ontwikkelen en configureren die een Azure SQL-database opvraagt](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

- Een Azure SQL-database. U kunt een van deze quickstarts gebruiken om een database te maken en vervolgens te configureren in Azure SQL Database:

  || Individuele database | Beheerd exemplaar |
  |:--- |:--- |:---|
  | Maken| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [Powershell](scripts/sql-database-create-and-configure-database-powershell.md) | [Powershell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configureren | [IP-firewallregel op serverniveau](sql-database-server-level-firewall-rule.md)| [Connectiviteit vanaf een VM](sql-database-managed-instance-configure-vm.md)|
  |||[Connectiviteit vanaf locatie](sql-database-managed-instance-configure-p2s.md)
  |Gegevens laden|Adventure Works geladen volgens de quickstart|[Wide World Importers herstellen](sql-database-managed-instance-get-started-restore.md)
  |||Adventure Works uit [BACPAC-bestand](sql-database-import.md) herstellen of importeren van [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > De scripts in dit artikel zijn geschreven voor gebruik met de Adventure Works-database. Met een beheerd exemplaar moet u de Adventure Works-database importeren in een exemplaardatabase of de scripts in dit artikel wijzigen zodat deze de Wide World Importers-database gebruiken.

- [.NET Core voor uw besturingssysteem](https://www.microsoft.com/net/core) moet zijn geïnstalleerd.

> [!NOTE]
> In deze snelstartgids wordt gebruik gemaakt van de *mySampleDatabase*-database. Als u een andere database wilt gebruiken, moet u de databaseverwijzingen wijzigen en de `SELECT`-query aanpassen in de C#-code.

## <a name="get-sql-server-connection-information"></a>SQL Server-verbindingsgegevens ophalen

Haal de verbindingsgegevens op die u nodig hebt om verbinding te maken met de Azure SQL-database. U hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig voor de volgende procedures.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Navigeer naar de pagina **SQL-database** of **Met SQL beheerde exemplaren**.

3. Bekijk op de pagina **Overzicht** de volledig gekwalificeerde servernaam naast **Servernaam** voor een individuele database, of de volledig gekwalificeerde servernaam naast **Host** voor een beheerd exemplaar. Als u de servernaam of hostnaam wilt kopiëren, plaatst u de muisaanwijzer erboven en selecteert u het pictogram **Kopiëren**.

## <a name="get-adonet-connection-information-optional"></a>ADO.NET-verbindingsgegevens ophalen (optioneel)

1. Navigeer naar de pagina **mySampleDatabase** en selecteer vervolgens onder **Instellingen** de optie **Verbindingsreeksen**.

2. Bekijk de volledige **ADO.NET**-verbindingsreeks.

    ![ADO.NET-verbindingsreeks](./media/sql-database-connect-query-dotnet/adonet-connection-string2.png)

3. Kopieer de **ADO.NET**-verbindingsreeks als u van plan bent om deze te gebruiken.
  
## <a name="create-a-new-net-core-project"></a>Een nieuw .NET Core-project maken

1. Open een opdrachtprompt en maak een map met de naam **sqltest**. Navigeer naar deze map en voer deze opdracht uit.

    ```cmd
    dotnet new console
    ```
    Met deze opdracht maakt u nieuwe app-projectbestanden, waaronder een eerste C#-codebestand (**Program.cs**), een XML-configuratiebestand (**sqltest.csproj**) en de benodigde binaire bestanden.

2. Open **sqltest.csproj** in een teksteditor en plak de volgende XML-code tussen de `<Project>`-tags. Met deze XML-code voegt u `System.Data.SqlClient` toe als een afhankelijkheid.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>Code invoegen om een query uit te voeren voor een SQL-database

1. Open **Program.cs** in een teksteditor.

2. Vervang de inhoud door de volgende code en voeg de juiste waarden toe voor de server, de database, de gebruikersnaam en het wachtwoord.

> [!NOTE]
> Als u een ADO.NET-verbindingsreeks wilt gebruiken, vervangt u de vier regels in de code waarmee de server, de database, de gebruikersnaam en het wachtwoord worden ingesteld door de onderstaande regel. Stel in de verbindingsreeks uw gebruikersnaam en wachtwoord in.
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

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

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                    sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                    sb.Append("JOIN [SalesLT].[Product] p ");
                    sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                    String sql = sb.ToString();

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
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
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>De code uitvoeren

1. Voer in de prompt de volgende opdrachten uit.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. Controleer of de bovenste twintig rijen worden geretourneerd.

   ```text
   Query data example:
   =========================================

   Road Frames HL Road Frame - Black, 58
   Road Frames HL Road Frame - Red, 58
   Helmets Sport-100 Helmet, Red
   Helmets Sport-100 Helmet, Black
   Socks Mountain Bike Socks, M
   Socks Mountain Bike Socks, L
   Helmets Sport-100 Helmet, Blue
   Caps AWC Logo Cap
   Jerseys Long-Sleeve Logo Jersey, S
   Jerseys Long-Sleeve Logo Jersey, M
   Jerseys Long-Sleeve Logo Jersey, L
   Jerseys Long-Sleeve Logo Jersey, XL
   Road Frames HL Road Frame - Red, 62
   Road Frames HL Road Frame - Red, 44
   Road Frames HL Road Frame - Red, 48
   Road Frames HL Road Frame - Red, 52
   Road Frames HL Road Frame - Red, 56
   Road Frames LL Road Frame - Black, 58
   Road Frames LL Road Frame - Black, 60
   Road Frames LL Road Frame - Black, 62

   Done. Press enter.
   ```
3. Kies **Enter** om het toepassingsvenster te sluiten.

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met .NET Core in Windows/Linux/macOS met behulp van de opdrachtregel ](/dotnet/core/tutorials/using-with-xplat-cli).
- Meer informatie over [verbinding maken met en een query uitvoeren voor een Azure SQL-database met behulp van het .NET Framework en Visual Studio](sql-database-connect-query-dotnet-visual-studio.md).  
- Meer informatie over [Uw eerste Azure SQL-database ontwerpen met behulp van SSMS](sql-database-design-first-database.md) of [Een Azure SQL-database ontwerpen en hiermee verbinding maken met behulp van C# en ADO.NET](sql-database-design-first-database-csharp.md).
- Raadpleeg de [.NET-documentatie](https://docs.microsoft.com/dotnet/) voor meer informatie over .NET.
