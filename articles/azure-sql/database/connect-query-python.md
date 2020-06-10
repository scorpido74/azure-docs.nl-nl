---
title: Python gebruiken om een query uit te voeren op een database
description: In dit onderwerp ziet u hoe u Python gebruikt om een programma te maken dat is verbonden met een database in Azure SQL Database, en hoe u een query voor deze database uitvoert met behulp van Transact-SQL-instructies.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2 
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 1d1d4ca6d3ccd20d851f0a88d1879f9e9a8175c4
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267321"
---
# <a name="quickstart-use-python-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Quickstart: Python gebruiken om een query uit te voeren op een database in Azure SQL Database of Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

In deze snelstart gebruikt u Python om verbinding te maken met Azure SQL Database of Azure SQL Managed Instance gebruikt u Transact-SQL-instructies gebruikt om een query uit te voeren op gegevens.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze quickstart te voltooien:

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

  || SQL Database | SQL Managed Instance | SQL Server op virtuele Azure-machine |
  |:--- |:--- |:---|:---|
  | Maken| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Configureren | [IP-firewallregel op serverniveau](firewall-create-server-level-portal-quickstart.md)| [Connectiviteit vanaf een VM](../managed-instance/connect-vm-instance-configure.md)|
  |||[Connectiviteit vanaf on-premises](../managed-instance/point-to-site-p2s-configure.md) | [Verbinding maken met een SQL Server-exemplaar](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Gegevens laden|Adventure Works geladen volgens de quickstart|[Wide World Importers herstellen](../managed-instance/restore-sample-database-quickstart.md) | [Wide World Importers herstellen](../managed-instance/restore-sample-database-quickstart.md) |
  |||Adventure Works herstellen of importeren vanuit een [BACPAC](database-import.md)-bestand vanaf [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Adventure Works herstellen of importeren vanuit een [BACPAC](database-import.md)-bestand vanaf [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

- [Python](https://python.org/downloads) 3 en gerelateerde software

  # <a name="macos"></a>[MacOS](#tab/macos)

  Als u Homebrew en Python, het ODBC-stuurprogramma en SQLCMD, en het Python-stuurprogramma voor SQL Server wilt installeren, gebruikt u de stappen **1.2**, **1.3** en **2.1** in [Python-apps maken met SQL Server op macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  Zie [ODBC-stuurprogramma van Microsoft op macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server) voor meer informatie.

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Gebruik `sudo apt-get install python python-pip gcc g++ build-essential` om Python en andere vereiste pakketten te installeren.

  Zie [Een omgeving configureren voor pyodbc Python-ontwikkeling](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux) als u het ODBC-stuurprogramma, SQLCMD en het Python-stuurprogramma voor SQL Server wilt installeren.

  Zie [ODBC-stuurprogramma van Microsoft op Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server) voor meer informatie.

  # <a name="windows"></a>[Windows](#tab/windows)

  Zie [Een omgeving configureren voor pyodbc Python-ontwikkeling](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows) als u Python, het ODBC-stuurprogramma en SQLCMD, en het Python-stuurprogramma voor SQL Server wilt installeren.

  Zie [ODBC-stuurprogramma van Microsoft](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server) voor meer informatie.

---

> [!IMPORTANT]
> De scripts in dit artikel zijn geschreven voor gebruik met de **Adventure Works**-database.

> [!NOTE]
> U kunt desgewenst kiezen voor het gebruik van een met Azure SQL beheerd exemplaar.
>
> Als u wilt maken en configureren, gebruikt u de [Azure-portal](../managed-instance/instance-create-quickstart.md), [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md)of de [CLI-](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) en stelt u vervolgens een [on-premises](../managed-instance/point-to-site-p2s-configure.md) of [VM](../managed-instance/connect-vm-instance-configure.md)-verbinding in.
>
> Als u gegevens wilt laden, raadpleegt u [Herstellen met BACPAC](database-import.md) met het [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)-bestand of raadpleegt u [De Wide World Imports-database herstellen](../managed-instance/restore-sample-database-quickstart.md).

Als u Python en de database in Azure SQL Database verder wilt verkennen, raadpleegt u [Azure SQL Database-bibliotheken voor Python](/python/api/overview/azure/sql), de [pyodbc-opslagplaats](https://github.com/mkleehammer/pyodbc/wiki/) en een [pyodbc-voorbeeld](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-server-connection-information"></a>SQL Server-verbindingsgegevens ophalen

Haal de verbindingsgegevens op die u nodig hebt om verbinding te maken met de database in Azure SQL Database. U hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig voor de volgende procedures.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Ga naar de pagina **SQL-databases** of **Met SQL beheerde exemplaren**.

3. Bekijk op de pagina **Overzicht** de volledig gekwalificeerde servernaam naast **Servernaam** voor de database in Azure SQL Database, of de volledig gekwalificeerde servernaam (of het IP-adres) naast **Host** voor een met Azure SQL beheerd exemplaar of SQL Server op virtuele Azure-machine. Als u de servernaam of hostnaam wilt kopiëren, plaatst u de muisaanwijzer erboven en selecteert u het pictogram **Kopiëren**.

> [!NOTE]
> Zie [Verbinding met een SQL Server-exemplaar](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server) op Azure VM voor meer informatie over de verbinding van SQL Server op virtuele Azure-machine.

## <a name="create-code-to-query-your-database"></a>Code maken om query's uit te voeren op uw database 

1. Maak in een teksteditor een nieuw bestand met de naam *sqltest.py*.  
   
1. Voeg de volgende code toe. Vervang \<server>, \<database>, \<username> en \<password> door uw eigen waarden.
   
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

- [Uw eerste database ontwerpen in Azure SQL Database](design-first-database-tutorial.md)
- [Microsoft Python-stuurprogramma's voor SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python-ontwikkelaarscentrum](https://azure.microsoft.com/develop/python/?v=17.23h)

