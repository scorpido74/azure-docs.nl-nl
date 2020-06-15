---
title: Verbinding maken met Python - Azure Database for PostgreSQL - individuele server
description: Deze quickstart bevat een voorbeeld van Python-code dat u kunt gebruiken om verbinding te maken met en gegevens op te vragen uit een Azure Database for PostgreSQL - individuele server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter, tracking-python
ms.devlang: python
ms.topic: quickstart
ms.date: 11/07/2019
ms.openlocfilehash: 174c11ba65ccba6389bf3e62d233b1ee56943b97
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560941"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Quickstart: Python gebruiken om verbinding te maken en gegevens op te vragen in Azure Database for PostgreSQL - individuele server

In deze quickstart werkt u met een Azure Database for PostgreSQL-exemplaar met behulp van Python in macOS, Ubuntu Linux of Windows. In de quickstart ziet u hoe u verbinding kunt maken met de database en SQL-instructies kunt gebruiken om gegevens in de database op te vragen, in te voegen, bij te werken en te verwijderen. In dit artikel wordt ervan uitgegaan dat u bekend bent met Python, maar geen ervaring hebt met het werken met Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

- Voltooiing van [quickstart: Een Azure Database for PostgreSQL-server maken in Azure Portal](quickstart-create-server-database-portal.md) of [quickstart: Een Azure Database for PostgreSQL-exemplaar maken met Azure CLI](quickstart-create-server-database-azure-cli.md).
  
- [Python](https://www.python.org/downloads/) 2.7.9+ of 3.4+.
  
- Nieuwste [PIP](https://pip.pypa.io/en/stable/installing/)-pakketinstallatieprogramma.

## <a name="install-the-python-libraries-for-postgresql"></a>De Python-bibliotheken voor PostgreSQL installeren
Met de module [psycopg2](https://pypi.python.org/pypi/psycopg2/) kunt u verbinding maken met en query's uitvoeren op een PostgreSQL-database. Deze module is beschikbaar als [wheel](https://pythonwheels.com/)-pakket voor Linux, macOS of Windows. Installeer de binaire versie van de module, inclusief alle afhankelijkheden. Zie [Installatie](http://initd.org/psycopg/docs/install.html) voor meer informatie over de installatie en vereisten van `psycopg2`. 

Als u `psycopg2` wilt installeren, opent u een terminal of opdrachtprompt en voert u de opdracht `pip install psycopg2` uit.

## <a name="get-database-connection-information"></a>De verbindingsgegevens voor de database ophalen
Voor het maken van verbinding met een Azure Database for PostgreSQL-database zijn de volledig gekwalificeerde servernaam en aanmeldingsreferenties vereist. U kunt deze informatie ophalen uit Azure Portal.

1. Zoek en selecteer in [Azure Portal](https://portal.azure.com/) uw Azure Database for PostgreSQL-servernaam. 
1. Kopieer op de pagina **Overzicht** van de server de volledig gekwalificeerde **servernaam** en de **gebruikersnaam met beheerdersrechten**. De volledig gekwalificeerde **servernaam** heeft altijd de vorm *\<my-server-name>.postgres.database.azure.com* en de **gebruikersnaam met beheerdersrechten** heeft altijd van de vorm *\<my-admin-username>@\<my-server-name>* . 
   
   U hebt ook uw beheerderswachtwoord nodig. Als u deze vergeten bent, kunt u deze opnieuw instellen op deze pagina. 
   
   ![Servernaam Azure Database for PostgreSQL](./media/connect-python/1-connection-string.png)

## <a name="how-to-run-the-python-examples"></a>De Python-voorbeelden uitvoeren

Voor elk codevoorbeeld in dit artikel:

1. Maak een nieuw bestand in een teksteditor. 
   
1. Voeg het codevoorbeeld toe aan het bestand. Vervang in de code:
   - `<server-name>` en `<admin-username>` door de waarden die u hebt gekopieerd uit Azure Portal.
   - `<admin-password>` door uw serverwachtwoord.
   - `<database-name>` door de naam van uw Azure Database for PostgreSQL-database. Er is automatisch een standaarddatabase met de naam *postgres* gemaakt toen u uw server maakte. U kunt de naam van de database wijzigen of een nieuwe database maken met behulp van SQL-opdrachten. 
   
1. Sla het bestand op in uw projectmap met een *.py*-extensie, zoals *postgres-insert.py*. In Windows moet u ervoor zorgen dat UTF-8-codering is geselecteerd wanneer u het bestand opslaat. 
   
1. Als u het bestand wilt uitvoeren, gaat u naar uw projectmap in een opdrachtregelinterface en typt u `python` gevolgd door de bestandsnaam, bijvoorbeeld `python postgres-insert.py`.

## <a name="create-a-table-and-insert-data"></a>Een tabel maken en gegevens invoegen
In het volgende codevoorbeeld wordt verbinding gemaakt met uw Azure Database for PostgreSQL-database met behulp van de functie [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) en worden gegevens geladen met de SQL-instructie **INSERT**. Met de functie [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) wordt de SQL-query uitgevoerd op de database. 

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
In het volgende codevoorbeeld wordt verbinding gemaakt met uw Azure Database for PostgreSQL-database en wordt [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) gebruikt met de SQL-instructie **SELECT** om gegevens te lezen. Deze functie accepteert een query en retourneert een resultatenset die met behulp van [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) kan worden herhaald. 

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
In het volgende codevoorbeeld wordt verbinding gemaakt met uw Azure Database for PostgreSQL-database en wordt [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) gebruikt met de SQL-instructie **UPDATE** om gegevens bij te werken. 

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
In het volgende codevoorbeeld wordt verbinding gemaakt met uw Azure Database for PostgreSQL-database en wordt [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) gebruikt met de SQL-instructie **DELETE** om een door u eerder ingevoegd inventarisitem te verwijderen. 

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
