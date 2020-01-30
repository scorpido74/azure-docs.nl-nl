---
title: Python gebruiken om een query uit te zoeken op een Data Base
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
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768574"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Snelstart: Python gebruiken om een query uit te voeren voor een Azure SQL-database

In deze Quick Start gebruikt u python om verbinding te maken met een Azure-SQL database en kunt u T-SQL-instructies gebruiken om gegevens op te vragen.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Een [Azure-SQL database](sql-database-single-database-get-started.md)
- [Python](https://python.org/downloads) 3 en verwante software

  # <a name="macostabmacos"></a>[MacOS](#tab/macos)

  Als u homebrew en Python, het ODBC-stuur programma en SQLCMD en het python-stuur programma voor SQL Server wilt installeren, gebruikt u stap **1,2**, **1,3**en **2,1** in [create python apps met behulp van SQL Server in macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  Zie voor meer informatie [micro soft ODBC-stuur programma op macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  Gebruik `sudo apt-get install python python-pip gcc g++ build-essential`om python en andere vereiste pakketten te installeren.

  Zie [Configure a Environment for Pyodbc python Development](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)om het ODBC-stuur programma, Sqlcmd en het python-stuur programma voor SQL Server te installeren.

  Zie [micro soft ODBC-stuur programma op Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)voor meer informatie.

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  Zie [Configure a Environment for Pyodbc python Development](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)om python, het ODBC-stuur programma en Sqlcmd, en het python-stuur programma voor SQL Server te installeren.

  Zie [micro soft ODBC-stuur programma](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server)voor meer informatie.

---

> [!IMPORTANT]
> De scripts in dit artikel zijn geschreven om de **Adventure Works** -data base te gebruiken.

> [!NOTE]
> U kunt desgewenst kiezen voor het gebruik van een Azure SQL Managed instance.
>
> Als u de [Azure Portal](sql-database-managed-instance-get-started.md), [Power shell](scripts/sql-database-create-configure-managed-instance-powershell.md)of [cli](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)wilt maken en configureren, gebruikt u de [on-site-](sql-database-managed-instance-configure-p2s.md) of [VM](sql-database-managed-instance-configure-vm.md) -verbinding instellen.
>
> Als u gegevens wilt laden, raadpleegt u [Restore with BACPAC](sql-database-import.md) with the [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) file of raadpleegt u [de Data Base voor Wide World Importers herstellen](sql-database-managed-instance-get-started-restore.md).

Zie [azure SQL database libraries voor python](/python/api/overview/azure/sql), de [pyodbc-opslag plaats](https://github.com/mkleehammer/pyodbc/wiki/)en een pyodbc-voor [beeld](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)om python en Azure SQL database verder te verkennen.

## <a name="get-sql-server-connection-information"></a>SQL Server-verbindingsgegevens ophalen

Haal de verbindingsgegevens op die u nodig hebt om verbinding te maken met de Azure SQL-database. U hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig voor de volgende procedures.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

2. Ga naar de pagina **SQL-data bases** of **SQL Managed instances** .

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

