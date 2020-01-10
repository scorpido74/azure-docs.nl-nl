---
title: 'Zelf studie: uw SQLite-data base migreren naar Azure SQL Database Serverloos'
description: Meer informatie over het uitvoeren van een offline migratie vanuit SQLite naar Azure SQL Database Serverloos met behulp van Azure Data Factory.
services: sql-database
author: joplum
ms.author: joplum
manager: prda
ms.service: sql-database
ms.workload: data-services
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c718daa4bc99bffd6fcfeb084299bed6682fe884
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780507"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Uw SQLite-data base naar Azure SQL Database Server migreren
Voor veel mensen biedt SQLite hun eerste ervaring met data bases en SQL-programmering. Het is opgenomen in veel besturings systemen en populaire toepassingen maakt SQLite één de meest uitgebreide en gebruikte data base-engines in de wereld. En omdat het waarschijnlijk de eerste data base-engine is die veel mensen gebruiken, kan deze vaak worden beëindigd als een centraal deel van projecten of toepassingen. In dergelijke gevallen, wanneer het project of de toepassing de eerste SQLite-implementatie uitbreidt, moeten ontwikkel aars hun gegevens mogelijk migreren naar een betrouw bare, gecentraliseerde gegevens opslag.

Azure SQL Database serverloos is een compute-laag voor afzonderlijke data bases waarmee de berekening automatisch wordt geschaald op basis van de werkbelasting vraag en facturen voor de hoeveelheid reken kracht die per seconde wordt gebruikt. De compute-laag zonder server onderbreekt ook automatisch data bases tijdens inactieve Peri Oden wanneer alleen opslag wordt gefactureerd en automatisch data bases hervat wanneer de activiteit wordt geretourneerd.

Als u de onderstaande stappen hebt gevolgd, wordt uw Data Base naar Azure SQL Database Serverloos gemigreerd, zodat u uw data base beschikbaar kunt stellen aan andere gebruikers of toepassingen in de Cloud en alleen betaalt voor wat u gebruikt, met minimale wijzigingen in de toepassings code.

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement
- SQLite2 of SQLite3-data base die u wilt migreren
- Een Windows-omgeving
  - Als u geen lokale Windows-omgeving hebt, kunt u een virtuele Windows-machine in azure gebruiken voor de migratie. Verplaats en maak uw SQLite-database bestand beschikbaar op de VM met behulp van Azure Files en Storage Explorer.

## <a name="steps"></a>Stappen

1. Richt een nieuwe Azure SQL Database in op de Serverloze Compute-laag.

    ![scherm afbeelding van Azure Portal waarin het voorzienings voorbeeld voor Azure SQL data base serverloos wordt weer gegeven](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/provision-serverless.png)

2. Zorg ervoor dat uw SQLite-database bestand beschikbaar is in uw Windows-omgeving. Installeer een SQLite ODBC-stuur programma als u er nog geen hebt (er zijn veel beschikbaar in open source), bijvoorbeeld http://www.ch-werner.de/sqliteodbc/).

3. Maak een systeem-DSN voor de data base. Zorg ervoor dat u de toepassing voor gegevens bron beheer gebruikt die overeenkomt met uw systeem architectuur (32-bits VS 64-bits). U kunt zien welke versie u gebruikt in de systeem instellingen.

    - Open ODBC-gegevens bron beheer in uw omgeving.
    - Klik op het tabblad systeem-DSN en klik op toevoegen.
    - Selecteer de SQLite ODBC-connector die u hebt geïnstalleerd en geef de verbinding een beschrijvende naam, bijvoorbeeld sqlitemigrationsource
    - De database naam instellen op het db-bestand
    - Opslaan en afsluiten

4. Down load en installeer de zelf-hostende Integration runtime. De eenvoudigste manier om dit te doen is de optie voor snelle installatie, zoals beschreven in de documentatie. Als u kiest voor een hand matige installatie, moet u de toepassing voorzien van een verificatie sleutel die zich in uw Data Factory-exemplaar kan bevinden door:

    - Opstarten van ADF (ontwerpen en controleren vanuit de service in de Azure Portal)
    - Klik op het tabblad ' Auteur ' (blauw potlood) aan de linkerkant
    - Klik op verbindingen (linksonder) en vervolgens op Integration Runtimes
    - Voeg nieuwe zelf-Hostende Integration Runtime toe, geef een naam op en selecteer *optie 2*.

5. Maak een nieuwe gekoppelde service voor de bron-SQLite-data base in uw Data Factory.

    ![scherm opname van lege gekoppelde services-Blade in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create.png)

6. Klik in verbindingen onder gekoppelde service op nieuw

7. Zoek en selecteer de ODBC-Connector


    ![scherm opname van het logo van de ODBC-connector op de Blade gekoppelde services in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-odbc.png)

8. Geef de gekoppelde service een duidelijke naam, bijvoorbeeld "sqlite_odbc". Selecteer de Integration runtime in de vervolg keuzelijst verbinding maken via Integration runtime. Voer het volgende in het connection string in en vervang de eerste catalogus variabele door het bestandspad voor het. db-bestand en de DSN met de naam van de systeem-DSN-verbinding: 

    ```
    Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Het verificatie type instellen op anoniem

10. De verbinding testen

    ![scherm opname met een geslaagde verbinding in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-test-successful.png)

11. Maak een andere gekoppelde service voor uw Serverloze SQL-doel. Selecteer de data base met behulp van de wizard gekoppelde service en geef de referenties voor de SQL-verificatie op.

    ![scherm opname met Azure SQL Database geselecteerd in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create-target.png)

12. Pak de CREATE TABLE-instructies uit in de SQLite-data base. U kunt dit doen door het onderstaande python-script uit te voeren in uw database bestand.

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. Maak de landings tabellen in uw Serverloze SQL-doel omgeving door de CREATE TABLE-instructies te kopiëren uit het bestand CreateTables. SQL en de SQL-instructies uit te voeren in de query-editor in het Azure Portal.

14. Ga terug naar het Start scherm van uw Data Factory en klik op Gegevens kopiëren om de wizard voor het maken van de taak uit te voeren.

    ![scherm opname van het logo van de Gegevens kopiëren wizard in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/copy-data.png)

15. Selecteer alle tabellen uit de bron-SQLite-data base met behulp van de selectie vakjes en wijs deze toe aan de doel tabellen in Azure SQL. Zodra de taak is uitgevoerd, hebt u uw gegevens van SQLite naar Azure SQL gemigreerd.

## <a name="next-steps"></a>Volgende stappen

- Zie [Quick Start: een enkele data base maken in Azure SQL database met behulp van de Azure Portal](sql-database-single-database-get-started.md)om aan de slag te gaan.
- Zie [resource limieten voor serverloze Compute-lagen](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)voor resource limieten.
