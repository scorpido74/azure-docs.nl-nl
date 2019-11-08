---
title: Een geografisch gedistribueerde oplossing implementeren
description: Meer informatie over het configureren van uw Azure SQL database en-toepassing voor failover naar een gerepliceerde data base en de testfailover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 51380d312c778380602c64cac766b050511cf994
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73810927"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Zelf studie: een geografisch gedistribueerde data base implementeren

Configureer een Azure-SQL database en-toepassing voor failover naar een externe regio en test een failover-plan. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> - Een [failovergroep](sql-database-auto-failover-group.md) maken
> - Een Java-toepassing uitvoeren om een query uit te voeren op een Azure-SQL database
> - Testfailover

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek.

Als u de zelf studie wilt volt ooien, moet u ervoor zorgen dat u de volgende items hebt geïnstalleerd:

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- Een enkele data base in Azure SQL Database. Als u één gebruik wilt maken,
  - [Portal](sql-database-single-database-get-started.md)
  - [CLI](sql-database-cli-samples.md)
  - [PowerShell](sql-database-powershell-samples.md)

  > [!NOTE]
  > In deze zelf studie wordt gebruikgemaakt van de voorbeeld database *AdventureWorksLT* .

- Java en Maven, Zie [een app bouwen met SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), **Java** markeren en uw omgeving selecteren en de stappen volgen.

> [!IMPORTANT]
> Zorg ervoor dat u firewall regels instelt voor het gebruik van het open bare IP-adres van de computer waarop u de stappen in deze zelf studie uitvoert. Firewall regels op database niveau worden automatisch gerepliceerd naar de secundaire server.
>
> Zie voor meer informatie [een firewall regel op database niveau maken](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) of om het IP-adres te bepalen dat wordt gebruikt voor de firewall regel op server niveau voor uw computer Zie [een firewall op server niveau maken](sql-database-server-level-firewall-rule.md).  

## <a name="create-a-failover-group"></a>Een failovergroep maken

Gebruik Azure PowerShell om [failover-groepen](sql-database-auto-failover-group.md) te maken tussen een bestaande Azure SQL-Server en een nieuwe Azure SQL-Server in een andere regio. Voeg vervolgens de voorbeeld database toe aan de failovergroep.

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

Voer het volgende script uit om een failovergroep te maken:

   ```powershell
    # Set variables for your server and database
    $adminlogin = "<your admin>"
    $password = "<your password>"
    $myresourcegroupname = "<your resource group name>"
    $mylocation = "<your resource group location>"
    $myservername = "<your existing server name>"
    $mydatabasename = "<your database name>"
    $mydrlocation = "<your disaster recovery location>"
    $mydrservername = "<your disaster recovery server name>"
    $myfailovergroupname = "<your globally unique failover group name>"

    # Create a backup server in the failover region
    New-AzSqlServer -ResourceGroupName $myresourcegroupname `
       -ServerName $mydrservername `
       -Location $mydrlocation `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
          -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

    # Create a failover group between the servers
    New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -PartnerServerName $mydrservername  `
       –FailoverGroupName $myfailovergroupname `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2

    # Add the database to the failover group
    Get-AzSqlDatabase `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -DatabaseName $mydatabasename | `
     Add-AzSqlDatabaseToFailoverGroup `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -FailoverGroupName $myfailovergroupname
   ```

Geo-replicatie-instellingen kunnen ook worden gewijzigd in de Azure Portal door uw data base te selecteren en vervolgens **instellingen** > **geo-replicatie**.

![Geo-replicatie-instellingen](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>Het voorbeeld project uitvoeren

1. Maak in de-console een Maven-project met de volgende opdracht:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Typ **Y** en druk op **Enter**.

1. Wijzig de mappen in het nieuwe project.

   ```bash
   cd SqlDbSample
   ```

1. Open met uw favoriete editor het bestand *pom. XML* in de projectmap.

1. Voeg het micro soft JDBC-stuur programma voor SQL Server afhankelijkheid toe door de volgende `dependency` sectie toe te voegen. De afhankelijkheid moet in de sectie groter `dependencies` worden geplakt.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Geef de Java-versie op door de sectie `properties` toe te voegen na de sectie `dependencies`:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Ondersteuning voor manifest bestanden door de sectie `build` toe te voegen na de sectie `properties`:

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. Sla het bestand *pom. XML* op en sluit het.

1. Open het bestand *app. java* dat zich bevindt in.. \SqlDbSample\src\main\java\com\sqldbsamples en vervang de inhoud door de volgende code:

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. Sla het bestand *app. java* op en sluit het.

1. Voer in de opdracht console de volgende opdracht uit:

   ```bash
   mvn package
   ```

1. Start de toepassing die ongeveer 1 uur wordt uitgevoerd totdat deze hand matig wordt gestopt, waardoor u tijd hebt om de testfailover uit te voeren.

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>Testfailover

Voer de volgende scripts uit om een failover te simuleren en Bekijk de resultaten van de toepassing. U ziet dat sommige invoegen en selecteren mislukken tijdens de database migratie.

U kunt ook de rol van de server voor herstel na nood gevallen tijdens de test controleren met de volgende opdracht:

   ```powershell
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername).ReplicationRole
   ```

Een failover testen:

1. Een hand matige failover van de failovergroep starten:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -FailoverGroupName $myfailovergroupname
   ```

1. Herstel de failovergroep terug naar de primaire server:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   ```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Azure-SQL database en-toepassing geconfigureerd voor failover naar een externe regio en een failover-plan getest. U hebt geleerd hoe u:

> [!div class="checklist"]
> - Een failover-groep met geo-replicatie maken
> - Een Java-toepassing uitvoeren om een query uit te voeren op een Azure-SQL database
> - Testfailover

Ga verder met de volgende zelf studie over het migreren met behulp van DMS.

> [!div class="nextstepaction"]
> [SQL Server migreren naar een beheerd exemplaar van Azure SQL database met behulp van DMS](../dms/tutorial-sql-server-to-managed-instance.md)
