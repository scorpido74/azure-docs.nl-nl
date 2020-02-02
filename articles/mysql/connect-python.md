---
title: Verbinding maken met behulp van python-Azure Database for MySQL
description: Deze snelstartgids bevat enkele voorbeelden van Python-code die u kunt gebruiken om verbinding te maken met en gegevens op te vragen uit Azure Database voor MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: seo-python-october2019
ms.devlang: python
ms.topic: quickstart
ms.date: 01/09/2020
ms.openlocfilehash: 96296a4ecea8dd1d546adf2292291746bd37507d
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938934"
---
# <a name="quickstart-use-python-to-connect-and-query-data-with-azure-database-for-mysql"></a>Snelstartgids: python gebruiken om verbinding te maken en gegevens op te vragen met Azure Database for MySQL

In deze Quick Start maakt u verbinding met een Azure Database for MySQL met behulp van python. Vervolgens gebruikt u SQL-instructies om gegevens in de-data base te zoeken, in te voegen, bij te werken en te verwijderen vanaf een Mac-, Ubuntu Linux-en Windows-platform. 

In dit onderwerp wordt ervan uitgegaan dat u bekend bent met het ontwikkelen met behulp van python, maar nog geen ervaring hebt met het werken met Azure Database for MySQL.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Een Azure Database for MySQL-server. [Maak een Azure database for mysql server met behulp van Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) of [Maak een Azure database for mysql server met behulp van Azure cli](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="install-python-and-the-mysql-connector"></a>Python en de MySQL-connector installeren

Installeer Python en de MySQL-Connector voor python op uw computer met behulp van de volgende stappen: 

> [!NOTE]
> In deze Quick Start wordt gebruikgemaakt van een onbewerkte SQL-query benadering om verbinding te maken met MySQL. Als u een webframework gebruikt, gebruikt u de aanbevolen connector voor het Framework, bijvoorbeeld [mysqlclient](https://pypi.org/project/mysqlclient/) voor django.

1. Down load en Installeer [Python 3,7 of hoger](https://www.python.org/downloads/) voor uw besturings systeem. Zorg ervoor dat u python toevoegt aan uw `PATH`, omdat de MySQL-Connector dat vereist.
   
1. Open een opdracht prompt of `bash` shell en controleer uw python-versie door `python -V` uit te voeren met de schakel optie hoofd letter V.
   
1. Het installatie programma voor het `pip`-pakket is opgenomen in de meest recente versies van python. Werk `pip` naar de nieuwste versie door `pip install -U pip`uit te voeren. 
   
   Als `pip` niet is geïnstalleerd, kunt u het downloaden en installeren met `get-pip.py`. Zie [installatie](https://pip.pypa.io/en/stable/installing/)voor meer informatie. 
   
1. Gebruik `pip` om de MySQL-Connector voor python en de bijbehorende afhankelijkheden te installeren:
   
   ```bash
   pip install mysql-connector-python
   ```
   
   U kunt ook de python-connector voor MySQL installeren vanuit [mysql.com](https://dev.mysql.com/downloads/connector/python/). Zie voor meer informatie over de MySQL-Connector voor python de [mysql-connector/python-ontwikkelaars handleiding](https://dev.mysql.com/doc/connector-python/en/). 

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen

Haal de verbindings gegevens op die u nodig hebt om verbinding te maken met Azure Database for MySQL vanuit de Azure Portal. U hebt de server naam, database naam en aanmeldings referenties nodig.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
   
1. Zoek en selecteer de Azure Database for MySQL server die u hebt gemaakt in de zoek balk van de portal, zoals **mydemoserver**.
   
   ![Servernaam van Azure Database for MySQL](./media/connect-python/1_server-overview-name-login.png)
   
1. Noteer de **Server naam** en de **aanmeldings naam van de server beheerder**op de pagina **overzicht** van de server. Als u uw wacht woord vergeet, kunt u het wacht woord ook opnieuw instellen op deze pagina.
   
   ![Servernaam van Azure Database for MySQL](./media/connect-python/azure-database-for-mysql-server-overview-name-login.png)

## <a name="run-the-python-examples"></a>De python-voor beelden uitvoeren

Voor elk code voorbeeld in dit artikel:

1. Maak een nieuw bestand in een tekst editor.
1. Voeg het code voorbeeld toe aan het bestand. Vervang in de code de tijdelijke aanduidingen `<mydemoserver>`, `<myadmin>`, `<mypassword>`en `<mydatabase>` door de waarden voor uw MySQL-server en-data base.
1. Sla het bestand op in een projectmap met de extensie *. py* , zoals *C:\pythonmysql\createtable.py* of */Home/username/pythonmysql/createtable.py*.
1. Als u de code wilt uitvoeren, opent u een opdracht prompt of `bash` shell en wijzigt u de map in de projectmap, bijvoorbeeld `cd pythonmysql`. Typ de `python` opdracht, gevolgd door de bestands naam, bijvoorbeeld `python createtable.py`, en druk op ENTER. 
   
   > [!NOTE]
   > Als *python. exe* niet wordt gevonden in Windows, moet u mogelijk het python-pad toevoegen aan de omgevings variabele PATH of het volledige pad naar *python. exe*opgeven, bijvoorbeeld `C:\python27\python.exe createtable.py`.

## <a name="create-a-table-and-insert-data"></a>Een tabel maken en gegevens invoegen

Gebruik de volgende code om verbinding te maken met de server en de data base, een tabel en gegevens te laden met behulp van een **Insert** SQL-instructie. 

Met de code wordt de MySQL. connector-bibliotheek geïmporteerd en wordt de functie [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) gebruikt om verbinding te maken met Azure database for MySQL met behulp van de [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in de configuratie verzameling. De code gebruikt een cursor voor de verbinding en de methode [cursor. Execute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) voert de SQL-query uit op de MySQL-data base. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="read-data"></a>Gegevens lezen

Gebruik de volgende code om verbinding te maken en de gegevens te lezen met behulp van de SQL-instructie **SELECT**. 

Met de code wordt de MySQL. connector-bibliotheek geïmporteerd en wordt de functie [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) gebruikt om verbinding te maken met Azure database for MySQL met behulp van de [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in de configuratie verzameling. De code gebruikt een cursor voor de verbinding en de methode [cursor. Execute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) voert de SQL-query uit op de MySQL-data base. 

De code leest de gegevens rijen met de methode [fetchall ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) , behoudt de resultatenset in een rij verzameling en gebruikt een `for` iterator om de rijen te herhalen.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="update-data"></a>Gegevens bijwerken

Gebruik de volgende code om verbinding te maken en de gegevens bij te werken met behulp van de SQL-instructie **UPDATE**. 

Met de code wordt de MySQL. connector-bibliotheek geïmporteerd en wordt de functie [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) gebruikt om verbinding te maken met Azure database for MySQL met behulp van de [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in de configuratie verzameling. De code gebruikt een cursor voor de verbinding en de methode [cursor. Execute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) voert de SQL-query uit op de MySQL-data base. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="delete-data"></a>Gegevens verwijderen

Gebruik de volgende code om verbinding te maken en de gegevens te verwijderen met behulp van de SQL-instructie **DELETE**. 

Met de code wordt de MySQL. connector-bibliotheek geïmporteerd en wordt de functie [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) gebruikt om verbinding te maken met Azure database for MySQL met behulp van de [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in de configuratie verzameling. De code gebruikt een cursor voor de verbinding en de methode [cursor. Execute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) voert de SQL-query uit op de MySQL-data base. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established.")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password.")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist.")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een database migreren met behulp van Exporteren en importeren](./concepts-migrate-import-export.md)
