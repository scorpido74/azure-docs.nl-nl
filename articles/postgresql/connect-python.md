---
title: Verbinding maken met Python - Azure Database voor PostgreSQL - Single Server
description: Deze quickstart biedt Python-codevoorbeelden die u gebruiken om gegevens uit Azure Database voor PostgreSQL - Single Server te verbinden en op te vragen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: python
ms.topic: quickstart
ms.date: 11/07/2019
ms.openlocfilehash: 3694c0b74393068538a0c8f496444a1541d88fee
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76769066"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Snelstart: Python gebruiken om gegevens te verbinden en op te vragen in Azure Database voor PostgreSQL - Single Server

In deze quickstart werkt u met een Azure Database voor PostgreSQL met Python op macOS, Ubuntu Linux of Windows. De quickstart laat zien hoe u verbinding maakt met de database en SQL-instructies gebruikt om gegevens op te vragen, in te voegen, bij te werken en te verwijderen. In het artikel wordt ervan uitgegaan dat u bekend bent met Python, maar nieuw voor het werken met Azure Database voor PostgreSQL.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

- Voltooiing van [Quickstart: maak een Azure-database voor PostgreSQL-server in de Azure-portal](quickstart-create-server-database-portal.md) of [Quickstart: maak een Azure-database voor PostgreSQL met behulp van de Azure CLI](quickstart-create-server-database-azure-cli.md).
  
- [Python](https://www.python.org/downloads/) 2.7.9+ of 3.4+.
  
- Laatste [pip](https://pip.pypa.io/en/stable/installing/) pakket installateur.

## <a name="install-the-python-libraries-for-postgresql"></a>De Python-bibliotheken voor PostgreSQL installeren
De [psycopg2-module](https://pypi.python.org/pypi/psycopg2/) maakt het mogelijk om verbinding te maken met en een PostgreSQL-database op te vragen en is beschikbaar als een Linux-, macOS- of [Windows-wielpakket.](https://pythonwheels.com/) Installeer de binaire versie van de module, inclusief alle afhankelijkheden. Zie Installatie `psycopg2` voor meer informatie [Installation](http://initd.org/psycopg/docs/install.html)over installatie en vereisten. 

Als `psycopg2`u wilt installeren, opent u `pip install psycopg2`een terminal- of opdrachtprompt en voert u de opdracht uit .

## <a name="get-database-connection-information"></a>Databaseverbindingsgegevens verzamelen
Voor verbinding met een Azure Database voor PostgreSQL-database zijn de volledig gekwalificeerde servernamen en inloggegevens vereist. U deze informatie ophalen via de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)naar en selecteer uw Azure Database voor PostgreSQL-servernaam. 
1. Kopieer op de pagina **Overzicht** van de server de volledig gekwalificeerde **servernaam** en de **gebruikersnaam beheerder.** De volledig gekwalificeerde **servernaam** is altijd van het formulier * \<mijn-server-naam>.postgres.database.azure.com*, en de **gebruikersnaam admin** is altijd van het formulier * \<mijn-admin-gebruikersnaam>@\<mijn-server-naam>*. 
   
   Je hebt ook je beheerderswachtwoord nodig. Als je het vergeet, kun je het resetten vanaf deze pagina. 
   
   ![Servernaam Azure Database for PostgreSQL](./media/connect-python/1-connection-string.png)

## <a name="how-to-run-the-python-examples"></a>De Voorbeelden van Python uitvoeren

Voor elk codevoorbeeld in dit artikel:

1. Een nieuw bestand maken in een teksteditor. 
   
1. Voeg het codevoorbeeld toe aan het bestand. Vervang in de code:
   - `<server-name>`en `<admin-username>` met de waarden die u hebt gekopieerd van de Azure-portal.
   - `<admin-password>`met uw serverwachtwoord.
   - `<database-name>`met de naam van uw Azure Database for PostgreSQL-database. Er is automatisch een standaarddatabase met de naam *postgres* gemaakt toen u uw server maakte. U de naam van die database wijzigen of een nieuwe database maken met SQL-opdrachten. 
   
1. Sla het bestand op in uw projectmap met een *.py-extensie,* zoals *postgres-insert.py*. Controleer voor Windows of UTF-8-codering is geselecteerd wanneer u het bestand opslaat. 
   
1. Als u het bestand wilt uitvoeren, wijzigt u de `python` projectmap in een `python postgres-insert.py`opdrachtregelinterface en typt u de bestandsnaam, bijvoorbeeld .

## <a name="create-a-table-and-insert-data"></a>Een tabel maken en gegevens invoegen
In het volgende codevoorbeeld wordt verbinding gemaakt met uw Azure Database for PostgreSQL-database met de functie [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) en worden gegevens geladen met een SQL **INSERT-instructie.** Met de functie [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) wordt de SQL-query uitgevoerd tegen de database. 

```Python
import psycopg2

# Update connection string information 
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")

# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")

# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")

# Clean up
conn.commit()
cursor.close()
conn.close()
```

Wanneer de code succesvol wordt uitgevoerd, produceert deze de volgende uitvoer:

![Opdrachtregeluitvoer](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Gegevens lezen
In het volgende codevoorbeeld wordt verbinding maakt met uw Azure Database voor PostgreSQL-database en wordt [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) gebruikt met de SQL **SELECT-instructie** om gegevens te lezen. Deze functie accepteert een query en retourneert een resultaat dat is ingesteld om te herhalen met [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Gegevens bijwerken
In het volgende codevoorbeeld wordt verbinding maakt met uw Azure Database voor PostgreSQL-database en wordt [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) gebruikt met de SQL **UPDATE-instructie** om gegevens bij te werken. 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Gegevens verwijderen
In het volgende codevoorbeeld wordt verbinding gemaakt met uw Azure Database voor PostgreSQL-database en wordt [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) met de SQL **DELETE-instructie** gebruikt om een voorraaditem te verwijderen dat u eerder hebt ingevoegd. 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Een database migreren met behulp van Exporteren en importeren](./howto-migrate-using-export-and-import.md)
