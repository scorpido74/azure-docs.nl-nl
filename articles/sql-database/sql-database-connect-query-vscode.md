---
title: VS code gebruiken om verbinding te maken en query's uit te zoeken
description: Ontdek hoe u verbinding maakt met SQL Database in Azure met behulp van Visual Studio Code. Voer daarna Transact-SQL-instructies (T-SQL) uit om query's uit te voeren voor gegevens en om gegevens te bewerken.
keywords: verbinding maken met sql-database
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
ms.openlocfilehash: 28b35f273783b2e4d0b8f59c5bc5be384b933ba2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826894"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query-an-azure-sql-database"></a>Snelstartgids: Visual Studio code gebruiken om verbinding te maken en een Azure SQL Database te zoeken

[Visual Studio Code](https://code.visualstudio.com/docs) is een grafische code-editor voor Linux, macOS en Windows. Het biedt ondersteuning voor extensies, zoals de [mssql-extensie](https://aka.ms/mssql-marketplace) voor het uitvoeren van query's in Microsoft SQL Server, Azure SQL Database en SQL Data Warehouse. In deze snelstart gaat u Visual Studio Code gebruiken om verbinding te maken met een Azure SQL-database en vervolgens Transact-SQL-instructies uitvoeren om query's op gegevens in de database uit te voeren, gegevens in te voegen, bij te werken en te verwijderen.

## <a name="prerequisites"></a>Vereisten

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
  > De scripts in dit artikel zijn geschreven voor gebruik met de Adventure Works-database. Met een beheerd exemplaar moet u de Adventure Works-database importeren in een exemplaardatabase of de scripts in dit artikel wijzigen voor gebruik van de Wide World Importers-database.

## <a name="install-visual-studio-code"></a>Visual Studio Code installeren

Zorg ervoor dat u de nieuwste versie van [Visual Studio Code](https://code.visualstudio.com/Download) hebt geïnstalleerd en de [mssql-extensie](https://aka.ms/mssql-marketplace) hebt geladen. Zie [VS-code installeren](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) en [mssql voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) voor hulp bij het installeren van de mssql-extensie.

## <a name="configure-visual-studio-code"></a>Visual Studio Code configureren

### <a name="mac-os"></a>**Mac OS**

Voor macOS moet u OpenSSL installeren. Dit is een vereiste voor .NET core die wordt gebruikt door de MSSQL-extensie. Open de terminal en voer de volgende opdrachten in om **brew** en **OpenSSL** te installeren.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Er is geen speciale configuratie vereist.

### <a name="windows"></a>**Windows**

Er is geen speciale configuratie vereist.

## <a name="get-sql-server-connection-information"></a>Verbindingsgegevens voor SQL Server ophalen

Haal de verbindingsgegevens op die u nodig hebt om verbinding te maken met de Azure SQL-database. U hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig voor de volgende procedures.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

2. Navigeer naar de pagina **SQL-database** of **Met SQL beheerde exemplaren**.

3. Bekijk op de pagina **Overzicht** de volledig gekwalificeerde servernaam naast **Servernaam** voor een individuele database, of de volledig gekwalificeerde servernaam naast **Host** voor een beheerd exemplaar. Als u de servernaam of hostnaam wilt kopiëren, plaatst u de muisaanwijzer erop en selecteert u het pictogram **Kopiëren**.

## <a name="set-language-mode-to-sql"></a>Taalmodus instellen op SQL

Stel de taalmodus in Visual Studio Code in op **SQL** om mssql-opdrachten en T-SQL IntelliSense in te schakelen.

1. Open een nieuw Visual Studio Code venster.

2. Druk op **Ctrl**+**N**. Hiermee wordt een nieuw bestand met tekst zonder opmaak geopend.

3. Selecteer **Tekst zonder opmaak** in de rechterbenedenhoek van de statusbalk.

4. Selecteer in de vervolgkeuzelijst **Taalmodus selecteren** die wordt geopend de optie **SQL**.

## <a name="connect-to-your-database"></a>Verbinding maken met uw database

Gebruik Visual Studio Code om verbinding te maken met uw Azure SQL Database-server.

> [!IMPORTANT]
> Zorg ervoor dat u de server- en aanmeldingsgegevens bij de hand hebt, voordat u verdergaat. Wanneer u de verbindingsprofielgegevens gaat invoeren, moet u ervoor zorgen dat de focus op Visual Studio Code blijft, omdat u anders opnieuw moet beginnen met het maken van het verbindingsprofiel.

1. Druk in Visual Studio Code op **Ctrl+Shift+P** (of **F1**) om het opdrachtenpalet te openen.

2. Selecteer **MS SQL:Connect** en kies **Enter**.

3. Selecteer **Verbindingsprofiel maken**.

4. Volg de aanwijzingen op om de verbindingseigenschappen voor het nieuwe profiel op te geven. Wanneer u een waarde hebt opgegeven, kiest u **Enter** om door te gaan.

   | Eigenschap       | Voorgestelde waarde | Beschrijving |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Servernaam** | De volledig gekwalificeerde servernaam | Dit moet er ongeveer als volgt uitzien: **mijnnieuweserver20170313.database.windows.net**. |
   | **Databasenaam** | mySampleDatabase | De database waarmee verbinding moet worden gemaakt. |
   | **Verificatie** | SQL-aanmelding| In deze zelfstudie wordt gebruik gemaakt van SQL-verificatie. |
   | **Gebruikersnaam** | Gebruikersnaam | De gebruikersnaam van het serverbeheerdersaccount dat wordt gebruikt voor het maken van de server. |
   | **Wachtwoord (SQL-aanmelding)** | Wachtwoord | Het wachtwoord van het serverbeheerdersaccount dat wordt gebruikt voor het maken van de server. |
   | **Wachtwoord opslaan?** | Ja of nee | Selecteer **Ja** als u het wachtwoord niet elke keer wilt invoeren. |
   | **Voer een naam in voor dit profiel** | Een profielnaam, zoals **mySampleProfile** | Een opgeslagen profiel zorgt ervoor dat de verbinding sneller tot stand komt bij toekomstige aanmeldingen. |

   Wanneer uw profiel is gemaakt, wordt er een melding weergegeven dat het profiel is gemaakt en verbonden is.

## <a name="query-data"></a>Querygegevens

Voer de volgende Transact-SQL-[SELECT](https://msdn.microsoft.com/library/ms189499.aspx)-instructie uit om op categorie een query uit te voeren voor de 20 populairste producten.

1. Plak in het editorvenster de volgende SQL-query.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Druk op **Ctrl**+**Shift**+**E** om de query uit te voeren en de resultaten uit de tabellen `Product` en `ProductCategory` weer te geven.

    ![Query voor het ophalen van gegevens uit twee tabellen](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Gegevens invoegen

Voer de volgende Transact-SQL-[INSERT](https://msdn.microsoft.com/library/ms174335.aspx)-instructie uit om een nieuw product toe te voegen aan de tabel `SalesLT.Product`.

1. Vervang de vorige query door de volgende.

   ```sql
   INSERT INTO [SalesLT].[Product]
        ( [Name]
        , [ProductNumber]
        , [Color]
        , [ProductCategoryID]
        , [StandardCost]
        , [ListPrice]
        , [SellStartDate]
        )
     VALUES
        ('myNewProduct'
        ,123456789
        ,'NewColor'
        ,1
         ,100
         ,100
         ,GETDATE() );
   ```

2. Druk op **Ctrl**+**Shift**+**E** om een nieuwe rij in de tabel `Product` in te voegen.

## <a name="update-data"></a>Gegevens bijwerken

Voer de volgende Transact-SQL-[UPDATE](https://msdn.microsoft.com/library/ms177523.aspx)-instructie uit om het toegevoegde product bij te werken.

1. Vervang de vorige query door de volgende:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Druk op **Ctrl**+**Shift**+**E** om de opgegeven rij in de tabel `Product` bij te werken.

## <a name="delete-data"></a>Gegevens verwijderen

Voer de volgende Transact-SQL-[DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql)-instructie uit om het nieuwe product te verwijderen.

1. Vervang de vorige query door de volgende:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Druk op **Ctrl**+**Shift**+**E** om de opgegeven rij in de tabel `Product` te verwijderen.

## <a name="next-steps"></a>Volgende stappen

- Als u verbinding wilt maken en query's wilt uitvoeren met SQL Server Management Studio, raadpleegt u [Quick Start: gebruik SQL Server Management Studio om verbinding te maken met een Azure SQL database en query gegevens](sql-database-connect-query-ssms.md).
- Als u verbinding wilt maken en query's wilt uitvoeren met behulp van de Azure Portal, raadpleegt u [Quick Start: gebruik de SQL-query editor in de Azure Portal om verbinding te maken en gegevens te zoeken](sql-database-connect-query-portal.md)
- Zie het blogbericht [Create a database IDE with MSSQL extension](https://msdn.microsoft.com/magazine/mt809115) voor een MSDN-artikel over het gebruik van Visual Studio Code.
