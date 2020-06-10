---
title: Verbinding maken met Python - Azure Database for MySQL
description: Deze snelstartgids bevat enkele voorbeelden van Python-code die u kunt gebruiken om verbinding te maken met en gegevens op te vragen uit Azure Database voor MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
- tracking-python
ms.devlang: python
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 5e9729ef2a95a840d0e994c44ecfcf0b61444234
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561068"
---
# <a name="quickstart-use-python-to-connect-and-query-data-with-azure-database-for-mysql"></a>Quickstart: Python gebruiken om verbinding te maken en gegevens op te vragen met Azure Database for MySQL

In deze quickstart maakt u verbinding met een Azure Database for MySQL met behulp van Python. U gebruikt vervolgens SQL-instructies om gegevens op te vragen, in te voegen, bij te werken en te verwijderen in de database vanaf Mac-, Ubuntu Linux- en Windows-platforms. 

In dit onderwerp wordt ervan uitgegaan dat u bekend bent met het ontwikkelen met behulp van Python, maar geen ervaring hebt met het werken met Azure Database for MySQL.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een Azure Database for MySQL-server. [Een Azure Database for MySQL-server maken met behulp van de Azure-portal](quickstart-create-mysql-server-database-using-azure-portal.md) of [Een Azure Database for MySQL-server maken met behulp van Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!IMPORTANT] 
> Controleer of het IP-adres waarmee u verbinding maakt aan de firewallregels van de server is toegevoegd met de [Azure-portal](./howto-manage-firewall-using-portal.md) of [Azure CLI](./howto-manage-firewall-using-cli.md)

## <a name="install-python-and-the-mysql-connector"></a>Python en de MySQL-connector installeren

Installeer Python en de MySQL-connector voor Python op uw computer met behulp van de volgende stappen: 

> [!NOTE]
> In deze snelstart gebruikt u een onbewerkte SQL-query om verbinding te maken met MySQL. Als u een webframework gebruikt, moet u de aanbevolen connector voor deze frameworks gebruiken, zoals [mysqlclient](https://pypi.org/project/mysqlclient/) voor Django.

1. Download en installeer [Python 3.7 of hoger](https://www.python.org/downloads/) voor uw besturingssysteem. Zorg ervoor dat u Python toevoegt aan uw `PATH`, omdat de MySQL-connector dat vereist.
   
1. Open een opdrachtprompt of `bash` shell en controleer uw Python-versie door `python -V` uit te voeren met de schakeloptie voor hoofdletter V.
   
1. Het installatieprogramma voor het `pip`-pakket is opgenomen in de meest recente versies van Python. Werk `pip` bij naar de nieuwste versie door `pip install -U pip` uit te voeren. 
   
   Als `pip` niet is geïnstalleerd, kunt u het downloaden en installeren met `get-pip.py`. Zie [Installatie](https://pip.pypa.io/en/stable/installing/) voor meer informatie. 
   
1. Gebruik `pip` om de MySQL-connector voor Python en de bijbehorende afhankelijkheden te installeren:
   
   ```bash
   pip install mysql-connector-python
   ```
   
   U kunt ook de Python-connector voor MySQL installeren vanuit [mysql.com](https://dev.mysql.com/downloads/connector/python/). Voor meer informatie over de MySQL-connector voor python raadpleegt u de [ontwikkelaarshandleiding voor MySQL/Python](https://dev.mysql.com/doc/connector-python/en/). 

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen

Haal de verbindingsgegevens op die nodig zijn om verbinding te maken met de Azure Database for MySQL van de Azure-portal. U hebt de servernaam, databasenaam en aanmeldingsreferenties nodig.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
   
1. Zoek en selecteer in de portalzoekbalk de Azure Database for MySQL server die u hebt gemaakt, zoals **mydemoserver**.
   
   ![Servernaam van Azure Database for MySQL](./media/connect-python/1_server-overview-name-login.png)
   
1. Ga naar de pagina **Overzicht** van de server en noteer de **Servernaam** en de **Aanmeldingsnaam van de serverbeheerder**. Als u uw wachtwoord vergeet, kunt u het wachtwoord op deze pagina opnieuw instellen.
   
   ![Servernaam van Azure Database for MySQL](./media/connect-python/azure-database-for-mysql-server-overview-name-login.png)

## <a name="run-the-python-examples"></a>De Python-voorbeelden uitvoeren

Voor elk codevoorbeeld in dit artikel:

1. Maak een nieuw bestand in een teksteditor.
1. Voeg het codevoorbeeld toe aan het bestand. Vervang in de code de tijdelijke aanduidingen `<mydemoserver>`, `<myadmin>`, `<mypassword>`en `<mydatabase>` door de waarden voor uw MySQL-server en -database.
1. Sla het bestand op in een projectmap met de extensie *.py*, zoals *C:\pythonmysql\createtable.py* of */home/username/pythonmysql/createtable.py*.
1. Als u de code wilt uitvoeren, opent u een opdrachtprompt of `bash`-shell en wijzigt u vervolgens de map in uw projectmap, bijvoorbeeld `cd pythonmysql`. Typ de `python`-opdracht, gevolgd door de bestandsnaam (bijvoorbeeld `python createtable.py`) en druk op Enter. 
   
   > [!NOTE]
   > Als *python.exe* niet wordt gevonden, moet u in Windows mogelijk het volledige Python-pad naar de PATH-omgevingsvariabale opgeven, of het volledige pad naar *python.exe* opgeven, zoals `C:\python27\python.exe createtable.py`.

## <a name="create-a-table-and-insert-data"></a>Een tabel maken en gegevens invoegen

Gebruik de volgende code om verbinding te maken met de server en database, een tabel te maken en gegevens te laden met behulp van de SQL-instructie **INSERT**. 

De code importeert de bibliotheek mysql.connector en gebruikt de functie [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) om verbinding te maken met Azure Database for MySQL met behulp van de [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in de config-verzameling. De code gebruikt een cursor voor de verbinding en de methode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) voert de SQL-query uit op de MySQL-database. 

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

De code importeert de bibliotheek mysql.connector en gebruikt de functie [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) om verbinding te maken met Azure Database for MySQL met behulp van de [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in de config-verzameling. De code gebruikt een cursor voor de verbinding en de methode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) voert de SQL-query uit op de MySQL-database. 

De code leest de gegevensrijen met behulp van de methode [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html), bewaart de resultatenset in een verzamelingrij en gebruikt een `for`-iterator om de rijen te doorlopen.

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

De code importeert de bibliotheek mysql.connector en gebruikt de functie [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) om verbinding te maken met Azure Database for MySQL met behulp van de [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in de config-verzameling. De code gebruikt een cursor voor de verbinding en de methode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) voert de SQL-query uit op de MySQL-database. 

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

De code importeert de bibliotheek mysql.connector en gebruikt de functie [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) om verbinding te maken met Azure Database for MySQL met behulp van de [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in de config-verzameling. De code gebruikt een cursor voor de verbinding en de methode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) voert de SQL-query uit op de MySQL-database. 

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
