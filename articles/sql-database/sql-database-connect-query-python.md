---
title: Python gebruiken om een database op te vragen
description: In dit onderwerp ziet u hoe u Python gebruikt om een programma te maken dat is verbonden met een Azure SQL-database, en hoe u een query voor deze database uitvoert met behulp van Transact-SQL-instructies.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: e82f8feae0096202e48a58296dd2e9d21bb61885
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768574"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Snelstart: Python gebruiken om een query uit te voeren voor een Azure SQL-database

In deze quickstart gebruikt u Python om verbinding te maken met een Azure SQL-database en T-SQL-instructies te gebruiken om gegevens op te vragen.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een [Azure SQL-database](sql-database-single-database-get-started.md)
- [Python](https://python.org/downloads) 3 en aanverwante software

  # <a name="macos"></a>[Macos](#tab/macos)

  Als u Homebrew en Python, het ODBC-stuurprogramma en SQLCMD en het Python-stuurprogramma voor SQL Server wilt installeren, gebruikt u stappen **1.2,** **1.3**en **2.1 om Python-apps** [te maken met SQL Server op macOS.](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)

  Zie [Microsoft ODBC Driver op macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)voor meer informatie.

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Als u Python en andere `sudo apt-get install python python-pip gcc g++ build-essential`vereiste pakketten wilt installeren, gebruikt u .

  Zie [een omgeving configureren voor pyodbc Python-ontwikkeling](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)als u het ODBC-stuurprogramma, SQLCMD en het Python-stuurprogramma voor SQL Server wilt installeren.

  Zie [Microsoft ODBC Driver op Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)voor meer informatie.

  # <a name="windows"></a>[Windows](#tab/windows)

  Zie [Een omgeving configureren voor pyodbc Python-ontwikkeling](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)als u Python, het ODBC-stuurprogramma en SQLCMD en het Python-stuurprogramma voor SQL Server wilt installeren.

  Zie [Microsoft ODBC Driver voor](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server)meer informatie.

---

> [!IMPORTANT]
> De scripts in dit artikel zijn geschreven om gebruik te maken van de **Adventure Works** database.

> [!NOTE]
> U er optioneel voor kiezen om een Azure SQL-beheerde instantie te gebruiken.
>
> Als u wilt maken en configureren, gebruikt u de [Azure Portal,](sql-database-managed-instance-get-started.md) [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)of [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)en vervolgens [on-site](sql-database-managed-instance-configure-p2s.md) of [VM-connectiviteit.](sql-database-managed-instance-configure-vm.md)
>
> Zie herstellen met het bestand Adventure Works of zie de database met Wide World Importers herstellen als u gegevens wilt laden, zie [herstellen met BACPAC](sql-database-import.md) met het bestand [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) of de database van Wide World [Importers herstellen.](sql-database-managed-instance-get-started-restore.md)

Zie [Azure SQL-databasebibliotheken voor Python,](/python/api/overview/azure/sql)de [pyodbc-repository](https://github.com/mkleehammer/pyodbc/wiki/)en een [pyodbc-voorbeeld](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)om Python en de Azure SQL-database verder te verkennen.

## <a name="get-sql-server-connection-information"></a>SQL Server-verbindingsgegevens ophalen

Haal de verbindingsgegevens op die u nodig hebt om verbinding te maken met de Azure SQL-database. U hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig voor de volgende procedures.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Ga naar de pagina **SQL-databases** of **SQL managed instances.**

3. Bekijk op de pagina **Overzicht** de volledig gekwalificeerde servernaam naast **Servernaam** voor een individuele database, of de volledig gekwalificeerde servernaam naast **Host** voor een beheerd exemplaar. Als u de servernaam of hostnaam wilt kopiëren, plaatst u de muisaanwijzer erboven en selecteert u het pictogram **Kopiëren**.

## <a name="create-code-to-query-your-sql-database"></a>Code maken om query's uit te voeren op uw SQL-database 

1. Maak in een teksteditor een nieuw bestand met de naam *sqltest.py*.  
   
1. Voeg de volgende code toe. Gebruik uw eigen waarden voor \<server >, \<database >, \<gebruikersnaam > en \<wachtwoord >.
   
   >[!IMPORTANT]
   >Voor de code in dit voorbeeld worden de voorbeeldgegevens gebruikt van AdventureWorksLT, die u als bron kunt kiezen bij het maken van uw database. Als in uw database andere gegevens staan, kunt u tabellen uit uw eigen database gebruiken in de SELECT-query. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
   cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
   row = cursor.fetchone()
   while row:
       print (str(row[0]) + " " + str(row[1]))
       row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>De code uitvoeren

1. Voer de volgende opdracht uit op een opdrachtprompt:

   ```cmd
   python sqltest.py
   ```

1. Controleer of de bovenste twintig rijen Categorie/Product worden geretourneerd en sluit vervolgens het opdrachtvenster.

## <a name="next-steps"></a>Volgende stappen

- [Uw eerste Azure SQL-database ontwerpen](sql-database-design-first-database.md)
- [Microsoft Python-stuurprogramma's voor SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python-ontwikkelaarscentrum](https://azure.microsoft.com/develop/python/?v=17.23h)

