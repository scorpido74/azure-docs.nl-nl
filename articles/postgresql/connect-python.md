---
title: Verbinding maken met python-Azure Database for PostgreSQL-één server
description: In deze Quick Start vindt u voor beelden van python-code die u kunt gebruiken om verbinding te maken en gegevens op te vragen van Azure Database for PostgreSQL-één server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: python
ms.topic: quickstart
ms.date: 11/07/2019
ms.openlocfilehash: 3694c0b74393068538a0c8f496444a1541d88fee
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76769066"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Snelstartgids: python gebruiken om verbinding te maken en gegevens op te vragen in Azure Database for PostgreSQL-één server

In deze Quick Start werkt u met een Azure Database for PostgreSQL met behulp van python in macOS, Ubuntu Linux of Windows. De Quick Start laat zien hoe u verbinding maakt met de data base en hoe u SQL-instructies gebruikt om gegevens te zoeken, in te voegen, bij te werken en te verwijderen. In het artikel wordt ervan uitgegaan dat u bekend bent met python, maar geen ervaring hebt met het werken met Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Volt ooien van [Quick Start: een Azure database for postgresql-server maken in azure Portal](quickstart-create-server-database-portal.md) of [Quick start: een Azure database for PostgreSQL maken met behulp van de Azure cli](quickstart-create-server-database-azure-cli.md).
  
- [Python](https://www.python.org/downloads/) 2.7.9 + of 3.4 +.
  
- Laatste [PIP](https://pip.pypa.io/en/stable/installing/) -pakket installatie programma.

## <a name="install-the-python-libraries-for-postgresql"></a>De python-bibliotheken voor PostgreSQL installeren
De [psycopg2](https://pypi.python.org/pypi/psycopg2/) -module maakt het mogelijk om verbinding te maken met en query's uit te stellen op een postgresql-data base en is beschikbaar als Linux-, macOS-of Windows- [wiel](https://pythonwheels.com/) pakket. Installeer de binaire versie van de module, inclusief alle afhankelijkheden. Zie installatie voor meer `psycopg2` informatie over de installatie en [Installation](http://initd.org/psycopg/docs/install.html)vereisten. 

Als u `psycopg2`wilt installeren, opent u een Terminal of opdracht prompt en `pip install psycopg2`voert u de opdracht uit.

## <a name="get-database-connection-information"></a>Verbindings gegevens voor data base ophalen
Voor het maken van verbinding met een Azure Database for PostgreSQL-Data Base zijn de volledig gekwalificeerde server naam en aanmeldings referenties vereist. U kunt deze informatie ophalen van de Azure Portal.

1. Zoek en selecteer in de [Azure Portal](https://portal.azure.com/)de naam van de Azure database for postgresql-server. 
1. Op de **overzichts** pagina van de server kopieert u de volledig gekwalificeerde **Server naam** en de **gebruikers naam**van de beheerder. De volledig gekwalificeerde **Server naam** is altijd van het formulier * \<mijn-server naam>. postgres.database.Azure.com*, en de **gebruikers naam** van de beheerder is altijd de indeling * \<mijn-beheerder-gebruikers naam\<> @ mijn-server naam>*. 
   
   U hebt ook uw beheerders wachtwoord nodig. Als u het verg eten bent, kunt u deze opnieuw instellen op deze pagina. 
   
   ![Servernaam Azure Database for PostgreSQL](./media/connect-python/1-connection-string.png)

## <a name="how-to-run-the-python-examples"></a>De python-voor beelden uitvoeren

Voor elk code voorbeeld in dit artikel:

1. Maak een nieuw bestand in een tekst editor. 
   
1. Voeg het code voorbeeld toe aan het bestand. Vervang in de code:
   - `<server-name>`en `<admin-username>` met de waarden die u hebt gekopieerd uit het Azure Portal.
   - `<admin-password>`met uw server wachtwoord.
   - `<database-name>`met de naam van uw Azure Database for PostgreSQL-data base. Er is automatisch een standaard database met de naam *post gres* gemaakt toen u de server maakte. U kunt de naam van de data base wijzigen of een nieuwe data base maken met behulp van SQL-opdrachten. 
   
1. Sla het bestand in de projectmap op met de extensie *. py* , zoals *postgres-insert.py*. Voor Windows moet u ervoor zorgen dat UTF-8-code ring is geselecteerd wanneer u het bestand opslaat. 
   
1. Als u het bestand wilt uitvoeren, gaat u naar de projectmap in de opdracht regel interface en typt `python` u, gevolgd door de bestands naam `python postgres-insert.py`, bijvoorbeeld.

## <a name="create-a-table-and-insert-data"></a>Een tabel maken en gegevens invoegen
In het volgende code voorbeeld wordt verbinding gemaakt met uw Azure Database for PostgreSQL-data base met behulp van de functie [psycopg2. Connect](http://initd.org/psycopg/docs/connection.html) en worden gegevens geladen met een SQL **Insert** -instructie. De functie [cursor. Execute](http://initd.org/psycopg/docs/cursor.html#execute) voert de SQL-query uit op de data base. 

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

Wanneer de code wordt uitgevoerd, wordt de volgende uitvoer gegenereerd:

![Opdrachtregeluitvoer](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Gegevens lezen
Het volgende code voorbeeld maakt verbinding met uw Azure Database for PostgreSQL-data base en gebruikt [cursor. Execute](http://initd.org/psycopg/docs/cursor.html#execute) met de SQL **Select** -instructie om gegevens te lezen. Deze functie accepteert een query en retourneert een resultatenset om over te lopen met behulp van [cursor. fetchall ()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). 

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
Het volgende code voorbeeld maakt verbinding met uw Azure Database for PostgreSQL-data base en gebruikt [cursor. Execute](http://initd.org/psycopg/docs/cursor.html#execute) met de instructie SQL **Update** om gegevens bij te werken. 

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
Het volgende code voorbeeld maakt verbinding met uw Azure Database for PostgreSQL-data base en gebruikt [cursor. Execute](http://initd.org/psycopg/docs/cursor.html#execute) met de instructie SQL **Delete** om een inventaris item te verwijderen dat u eerder hebt ingevoegd. 

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
