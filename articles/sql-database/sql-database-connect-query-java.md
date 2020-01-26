---
title: Java gebruiken om een query uit te zoeken op een Data Base
description: Laat zien hoe u Java gebruikt om een programma te maken dat is verbonden met een Azure SQL-database, en hoe u deze database doorzoekt met behulp van T-SQL-instructies.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-java-july2019. seo-java-august2019
ms.openlocfilehash: e23f7d165a09a3730019cd9e2d279a01d2fa1e49
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758509"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Snelstart: Java gebruiken om een query uit te voeren voor een Azure SQL-database

In deze Quick Start gebruikt u Java om verbinding te maken met een Azure-SQL database en T-SQL-instructies te gebruiken om gegevens op te vragen.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Een [Azure-SQL database](sql-database-single-database-get-started.md)
  
- [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)-gerelateerde software

  # <a name="macostabmacos"></a>[MacOS](#tab/macos)

  Installeer homebrew en Java en installeer maven met behulp van de stappen **1,2** en **1,3** in [Java-Apps maken met behulp van SQL Server op macOS](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  Installeer Java, de Java Development Kit en installeer vervolgens maven met behulp van de stappen **1,2**, **1,3**en **1,4** in [Create Java-Apps met behulp van SQL Server op Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  Installeer Java en installeer maven met behulp van de stappen **1,2** en **1,3** in [Java-Apps maken met behulp van SQL Server in Windows](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

  ---

> [!IMPORTANT]
> De scripts in dit artikel zijn geschreven om de **Adventure Works** -data base te gebruiken.

> [!NOTE]
> U kunt desgewenst kiezen voor het gebruik van een Azure SQL Managed instance.
>
> Als u de [Azure Portal](sql-database-managed-instance-get-started.md), [Power shell](scripts/sql-database-create-configure-managed-instance-powershell.md)of [cli](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)wilt maken en configureren, gebruikt u de [on-site-](sql-database-managed-instance-configure-p2s.md) of [VM](sql-database-managed-instance-configure-vm.md) -verbinding instellen.
>
> Als u gegevens wilt laden, raadpleegt u [Restore with BACPAC](sql-database-import.md) with the [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) file of raadpleegt u [de Data Base voor Wide World Importers herstellen](sql-database-managed-instance-get-started-restore.md).

## <a name="get-sql-server-connection-information"></a>SQL Server-verbindingsgegevens ophalen

Haal de verbindingsgegevens op die u nodig hebt om verbinding te maken met de Azure SQL-database. U hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig voor de volgende procedures.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

2. Selecteer **SQL-data bases** of open de pagina **SQL Managed instances** .

3. Bekijk op de pagina **Overzicht** de volledig gekwalificeerde servernaam naast **Servernaam** voor een individuele database, of de volledig gekwalificeerde servernaam naast **Host** voor een beheerd exemplaar. Als u de servernaam of hostnaam wilt kopiëren, plaatst u de muisaanwijzer erboven en selecteert u het pictogram **Kopiëren**. 

## <a name="create-the-project"></a>Het project maken

1. Maak vanaf de opdrachtregel een nieuw Maven-project met de naam *sqltest*.

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. Wijzig de map in *sqltest* en open *pom.xml* met uw favoriete teksteditor. Voeg het **Microsoft JDBC-stuurprogramma voor SQL Server** toe aan de afhankelijkheden van het project met behulp van de volgende code:

    ```xml
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>7.0.0.jre8</version>
    </dependency>
    ```

1. Voeg, ook in *pom.xml*, de volgende eigenschappen toe aan uw project. Als u geen sectie met eigenschappen hebt, kunt u deze toevoegen na de afhankelijkheden.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Sla *pom.xml* op en sluit het af.

## <a name="add-code-to-query-database"></a>Code toevoegen om een query uit te voeren op de database

1. U hebt al een bestand met de naam *App.java* in uw Maven-project dat zich bevindt in:

   *..\sqltest\src\main\java\com\sqldbsamples\App.java*

1. Open het bestand en vervang de inhoud door de volgende code. Voeg vervolgens de juiste waarden toe voor uw server, database, gebruiker en wachtwoord.

    ```java
    package com.sqldbsamples;

    import java.sql.Connection;
    import java.sql.Statement;
    import java.sql.PreparedStatement;
    import java.sql.ResultSet;
    import java.sql.DriverManager;

    public class App {

        public static void main(String[] args) {

            // Connect to database
            String hostName = "your_server.database.windows.net"; // update me
            String dbName = "your_database"; // update me
            String user = "your_username"; // update me
            String password = "your_password"; // update me
            String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;"
                + "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
            Connection connection = null;

            try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName "
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";

                try (Statement statement = connection.createStatement();
                ResultSet resultSet = statement.executeQuery(selectSql)) {

                    // Print results from select statement
                    System.out.println("Top 20 categories:");
                    while (resultSet.next())
                    {
                        System.out.println(resultSet.getString(1) + " "
                            + resultSet.getString(2));
                    }
                    connection.close();
                }
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
    ```

   > [!NOTE]
   > In het codevoorbeeld wordt de voorbeelddatabase **AdventureWorksLT** voor Azure SQL gebruikt.

## <a name="run-the-code"></a>De code uitvoeren

1. Voer de app uit vanaf de opdrachtprompt.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Controleer de bovenste 20 rijen die worden geretourneerd en sluit het app-venster.

## <a name="next-steps"></a>Volgende stappen

- [Uw eerste Azure SQL-database ontwerpen](sql-database-design-first-database.md)  

- [Microsoft JDBC-stuurprogramma voor SQL Server](https://github.com/microsoft/mssql-jdbc)  

- [Problemen melden/vragen stellen](https://github.com/microsoft/mssql-jdbc/issues)  
