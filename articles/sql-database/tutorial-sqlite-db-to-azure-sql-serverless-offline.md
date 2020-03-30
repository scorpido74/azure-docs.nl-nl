---
title: 'Zelfstudie: Uw SQLite-database migreren naar Azure SQL Database Serverless'
description: Leer een offline migratie uitvoeren van SQLite naar Azure SQL Database Serverless met Azure Data Factory.
services: sql-database
author: joplum
ms.author: joplum
manager: prda
ms.service: sql-database
ms.workload: data-services
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c718daa4bc99bffd6fcfeb084299bed6682fe884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780507"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Uw SQLite-database migreren naar Azure SQL Database Serverless
Voor veel mensen biedt SQLite hun eerste ervaring met databases en SQL-programmering. Het is opname in veel besturingssystemen en populaire toepassingen maakt SQLite een van de meest ingezet en gebruikte database motoren in de wereld. En omdat het waarschijnlijk de eerste database-engine is die veel mensen gebruiken, kan het vaak eindigen als een centraal onderdeel van projecten of toepassingen. In dergelijke gevallen waarin het project of de toepassing de eerste SQLite-implementatie ontgroeit, moeten ontwikkelaars hun gegevens mogelijk migreren naar een betrouwbaar, gecentraliseerd gegevensarchief.

Azure SQL Database serverless is een compute tier voor afzonderlijke databases die automatisch compute schalen op basis van de workloadvraag en facturen voor de hoeveelheid compute die per seconde wordt gebruikt. De serverless compute-laag pauzeert ook automatisch databases tijdens inactieve perioden wanneer alleen opslag wordt gefactureerd en hervat automatisch databases wanneer de activiteit terugkeert.

Zodra u de onderstaande stappen hebt gevolgd, wordt uw database gemigreerd naar Azure SQL Database Serverless, zodat u uw database beschikbaar maken voor andere gebruikers of toepassingen in de cloud en alleen betalen voor wat u gebruikt, met minimale wijzigingen in de toepassingscode.

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement
- SQLite2- of SQLite3-database die u wilt migreren
- Een Windows-omgeving
  - Als u geen lokale Windows-omgeving hebt, u een Windows-vm in Azure gebruiken voor de migratie. Verplaats uw SQLite-databasebestand en maak uw SQLite-databasebestand beschikbaar op de VM met Azure Files en Storage Explorer.

## <a name="steps"></a>Stappen

1. Een nieuwe Azure SQL-database inrichten in de compute-laag Serverless.

    ![schermafbeelding van Azure-portal met inrichtingsvoorbeeld voor azure sql-databaseserverloos](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/provision-serverless.png)

2. Zorg ervoor dat uw SQLite-databasebestand beschikbaar is in uw Windows-omgeving. Installeer een SQLite ODBC Driver als je er nog geen hebt (er http://www.ch-werner.de/sqliteodbc/)zijn er veel beschikbaar in Open Source, bijvoorbeeld.

3. Maak een Systeem DSN voor de database. Zorg ervoor dat u de toepassing Gegevensbronbeheerder gebruikt die overeenkomt met uw systeemarchitectuur (32-bits versus 64-bits). Welke versie u uitvoert, vindt u in uw systeeminstellingen.

    - Open ODBC-gegevensbronbeheerder in uw omgeving.
    - Klik op het tabblad DSN van het systeem en klik op 'Toevoegen'.
    - Selecteer de DOOR u geïnstalleerde SQLite ODBC-connector en geef de verbinding een betekenisvolle naam, bijvoorbeeld sqlitemigrationsource
    - De databasenaam instellen op het .db-bestand
    - Opslaan en afsluiten

4. Download en installeer de zelf gehoste runtime voor integratie. De eenvoudigste manier om dit te doen is de Express installatie optie, zoals beschreven in de documentatie. Als u kiest voor een handmatige installatie, moet u de toepassing voorzien van een verificatiesleutel, die zich in uw instantie Data Factory kan bevinden door:

    - ADF opstarten (auteur en monitor vanuit de service in de Azure-portal)
    - Klik op het tabblad 'Auteur' (Blauw potlood) aan de linkerkant
    - Klik op Verbindingen (linksonder) en vervolgens op De runtimes van integratie
    - Voeg nieuwe Self-Hosted Integration Runtime toe, geef deze een naam, selecteer *Optie 2*.

5. Maak een nieuwe gekoppelde service voor de bron SQLite-database in uw datafabriek.

    ![schermafbeelding met leeg gekoppeld serviceblad in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create.png)

6. Klik in Verbindingen onder Gekoppelde service op Nieuw

7. Zoeken naar en selecteer de ODBC-connector


    ![schermafbeelding met odbc-connectorlogo in het gekoppelde servicesblad in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-odbc.png)

8. Geef de gekoppelde service een betekenisvolle naam, bijvoorbeeld "sqlite_odbc". Selecteer de runtime van uw integratie in de vervolgkeuzelijst 'Verbinding maken via integratieruntime'. Voer de onderstaande in de verbindingstekenreeks in en vervang de variabele Initiële catalogus door het bestandspad voor het DB-bestand en het DSN door de naam van de Systeem DSN-verbinding: 

    ```
    Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Het verificatietype instellen op Anoniem

10. De verbinding testen

    ![schermafbeelding met een succesvolle verbinding in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-test-successful.png)

11. Maak een andere gekoppelde service voor uw SERVERless SQL-doel. Selecteer de database met de wizard Gekoppelde service en geef de SQL-verificatiereferenties op.

    ![schermafbeelding met Azure SQL Database geselecteerd in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create-target.png)

12. Haal de TABEL-instructies MAKEN uit uw SQLite-database. U dit doen door het onderstaande Python-script in uw databasebestand uit te voeren.

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

13. Maak de landtabellen in uw SERVERless SQL-doelomgeving door de tabelinstructies MAKEN uit het CreateTables.sql-bestand te kopiëren en de SQL-instructies in de Queryeditor in de Azure-portal uit te voeren.

14. Ga terug naar het startscherm van uw gegevensfabriek en klik op 'Gegevens kopiëren' om door de wizard Voor het maken van banen te lopen.

    ![schermafbeelding met het wizard-logo Gegevens kopiëren in Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/copy-data.png)

15. Selecteer alle tabellen uit de bron-SQLite-database met behulp van de selectievakjes en wijs ze toe aan de doeltabellen in Azure SQL. Zodra de taak is uitgevoerd, hebt u uw gegevens met succes gemigreerd van SQLite naar Azure SQL!

## <a name="next-steps"></a>Volgende stappen

- Zie [Snelstart: Eén database maken in Azure SQL Database met de Azure-portal](sql-database-single-database-get-started.md)om aan de slag te gaan.
- Zie [Serverless compute tier resource limits](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)voor resourcelimieten voor resources.
