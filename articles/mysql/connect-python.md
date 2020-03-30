---
title: Verbinding maken met Python - Azure Database voor MySQL
description: Deze snelstartgids bevat enkele voorbeelden van Python-code die u kunt gebruiken om verbinding te maken met en gegevens op te vragen uit Azure Database voor MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
ms.devlang: python
ms.topic: quickstart
ms.date: 01/09/2020
ms.openlocfilehash: c9ea155f3cc71dd961a3780e3b188a6d062606bc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067904"
---
# <a name="quickstart-use-python-to-connect-and-query-data-with-azure-database-for-mysql"></a>Snelstart: Python gebruiken om gegevens te verbinden en op te vragen met Azure Database voor MySQL

In deze quickstart maakt u verbinding met een Azure Database voor MySQL met Python. Vervolgens gebruikt u SQL-instructies om gegevens in de database op te vragen, in te voegen, bij te werken en te verwijderen van Mac-, Ubuntu Linux- en Windows-platforms. 

In dit onderwerp wordt ervan uitgegaan dat u bekend bent met het ontwikkelen van python, maar dat u nieuw bent in het werken met Azure Database voor MySQL.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een Azure-database voor MySQL-server. [Maak een Azure Database voor MySQL-server met Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) of [Maak een Azure Database voor MySQL-server met Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="install-python-and-the-mysql-connector"></a>Python en de MySQL-connector installeren

Installeer Python en de MySQL-connector voor Python op uw computer met de volgende stappen: 

> [!NOTE]
> Deze quickstart maakt gebruik van een raw SQL-querybenadering om verbinding te maken met MySQL. Als u een webframework gebruikt, gebruikt u de aanbevolen connector voor het framework, bijvoorbeeld [mysqlclient](https://pypi.org/project/mysqlclient/) voor Django.

1. Download en installeer [Python 3.7 of hoger](https://www.python.org/downloads/) voor je besturingssysteem. Zorg ervoor dat u `PATH`Python toevoegt aan uw , omdat de MySQL-connector dat vereist.
   
1. Open een opdrachtprompt of `bash` shell en controleer `python -V` uw Python-versie door met de hoofdletterv-schakelaar te worden uitgevoerd.
   
1. De `pip` pakketinstaller is opgenomen in de nieuwste versies van Python. Update `pip` naar de nieuwste `pip install -U pip`versie door het uitvoeren van . 
   
   Als `pip` deze niet is geïnstalleerd, `get-pip.py`u deze downloaden en installeren met. Zie [Installatie](https://pip.pypa.io/en/stable/installing/)voor meer informatie. 
   
1. Gebruik `pip` de MySQL-connector voor Python en de bijbehorende afhankelijkheden:
   
   ```bash
   pip install mysql-connector-python
   ```
   
   U de Python-connector voor MySQL ook installeren vanuit [mysql.com.](https://dev.mysql.com/downloads/connector/python/) Zie de [MySQL Connector/Python Developer Guide](https://dev.mysql.com/doc/connector-python/en/)voor meer informatie over de MySQL Connector voor Python. 

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen

Haal de verbindingsgegevens die u nodig hebt om verbinding te maken met Azure Database voor MySQL op uit de Azure-portal. U hebt de servernaam, databasenaam en inloggegevens nodig.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
   
1. Zoek in de zoekbalk van de portal naar en selecteer de Azure Database voor MySQL-server die u hebt gemaakt, zoals **mydemoserver.**
   
   ![Servernaam van Azure Database for MySQL](./media/connect-python/1_server-overview-name-login.png)
   
1. Noteer op de **pagina Overzicht** van de server de **inlognaam** **serveren** serverbeheerder . Als u uw wachtwoord bent vergeten, u het wachtwoord ook opnieuw instellen vanaf deze pagina.
   
   ![Servernaam van Azure Database for MySQL](./media/connect-python/azure-database-for-mysql-server-overview-name-login.png)

## <a name="run-the-python-examples"></a>De Voorbeelden van Python uitvoeren

Voor elk codevoorbeeld in dit artikel:

1. Een nieuw bestand maken in een teksteditor.
1. Voeg het codevoorbeeld toe aan het bestand. Vervang in de `<mydemoserver>`code `<myadmin>` `<mypassword>`de `<mydatabase>` , , en tijdelijke aanduidingen door de waarden voor uw MySQL-server en -database.
1. Sla het bestand op in een projectmap met een *.py-extensie,* zoals *C:\pythonmysql\createtable.py* of */home/username/pythonmysql/createtable.py*.
1. Als u de code wilt `bash` uitvoeren, opent u bijvoorbeeld `cd pythonmysql`een opdrachtprompt of shell en wijzigt u de map in uw projectmap. Typ `python` bijvoorbeeld `python createtable.py`de opdracht gevolgd door de bestandsnaam en druk op Enter. 
   
   > [!NOTE]
   > Als *python.exe* in Windows niet wordt gevonden, moet u mogelijk het Python-pad toevoegen aan uw PATH-omgevingsvariabele of het volledige pad naar *python.exe*bieden, bijvoorbeeld `C:\python27\python.exe createtable.py`.

## <a name="create-a-table-and-insert-data"></a>Een tabel maken en gegevens invoegen

Gebruik de volgende code om verbinding te maken met de server en database, een tabel te maken en gegevens te laden met behulp van een **SQL-instructie INVOEGEN.** 

De code importeert de mysql.connector-bibliotheek en gebruikt de functie [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) om verbinding te maken met Azure Database voor MySQL met behulp van de [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in de config-verzameling. De code gebruikt een cursor op de verbinding en de methode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) voert de SQL-query uit tegen de MySQL-database. 

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

De code importeert de mysql.connector-bibliotheek en gebruikt de functie [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) om verbinding te maken met Azure Database voor MySQL met behulp van de [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in de config-verzameling. De code gebruikt een cursor op de verbinding en de methode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) voert de SQL-query uit tegen de MySQL-database. 

De code leest de gegevensrijen met de methode [fetchall(),](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) houdt het `for` resultaat in een verzamelingsrij en gebruikt een iterator om over de rijen te lussen.

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

De code importeert de mysql.connector-bibliotheek en gebruikt de functie [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) om verbinding te maken met Azure Database voor MySQL met behulp van de [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in de config-verzameling. De code gebruikt een cursor op de verbinding en de methode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) voert de SQL-query uit tegen de MySQL-database. 

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

De code importeert de mysql.connector-bibliotheek en gebruikt de functie [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) om verbinding te maken met Azure Database voor MySQL met behulp van de [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in de config-verzameling. De code gebruikt een cursor op de verbinding en de methode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) voert de SQL-query uit tegen de MySQL-database. 

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
