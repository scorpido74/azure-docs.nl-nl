---
title: Een geogedistribueerde oplossing implementeren
description: Lees of u uw Azure SQL-database en -toepassing wilt configureren voor failover naar een gerepliceerde database en test failover.
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
ms.openlocfilehash: 58d5bd4a7f3087e11056354f7534c3c9dbebca3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067293"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Zelfstudie: Een geogedistribueerde database implementeren

Configureer een Azure SQL-database en -toepassing voor failover naar een extern gebied en test een failoverplan. Procedures voor:

> [!div class="checklist"]
> - Een [failovergroep maken](sql-database-auto-failover-group.md)
> - Een Java-toepassing uitvoeren om een Azure SQL-database op te vragen
> - Testfailover

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek.

Als u de zelfstudie wilt voltooien, controleert u of u de volgende items hebt geïnstalleerd:

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- Eén database in Azure SQL Database. Als u één gebruik wilt maken,
  - [Portal](sql-database-single-database-get-started.md)
  - [CLI](sql-database-cli-samples.md)
  - [PowerShell](sql-database-powershell-samples.md)

  > [!NOTE]
  > De zelfstudie maakt gebruik van de *AdventureWorksLT-voorbeelddatabase.*

- Java en Maven, zie [Een app bouwen met SQL Server,](https://www.microsoft.com/sql-server/developer-get-started/) **Java** markeren en uw omgeving selecteren en vervolgens de stappen volgen.

> [!IMPORTANT]
> Zorg ervoor dat u firewallregels instelt om het openbare IP-adres te gebruiken van de computer waarop u de stappen uitvoert in deze zelfstudie. Firewallregels op databaseniveau worden automatisch gerepliceerd naar de secundaire server.
>
> Zie [Een firewallregel op serverniveau maken](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) voor informatie of het IP-adres bepalen dat wordt gebruikt voor de firewallregel op serverniveau voor uw computer: Een firewall op [serverniveau maken](sql-database-server-level-firewall-rule.md).  

## <a name="create-a-failover-group"></a>Een failovergroep maken

Maak met Azure PowerShell [failovergroepen](sql-database-auto-failover-group.md) tussen een bestaande Azure SQL-server en een nieuwe Azure SQL-server in een andere regio. Voeg vervolgens de voorbeelddatabase toe aan de failovergroep.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

Als u een failovergroep wilt maken, voert u het volgende script uit:

```powershell
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>"
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
New-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $drServer `
    -Location $drLocation -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
    -ArgumentList $admin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# create a failover group between the servers
New-AzSqlDatabaseFailoverGroup –ResourceGroupName $resourceGroup -ServerName $server `
    -PartnerServerName $drServer –FailoverGroupName $failoverGroup –FailoverPolicy Automatic -GracePeriodWithDataLossHours 2

# add the database to the failover group
Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $server -DatabaseName $database | `
    Add-AzSqlDatabaseToFailoverGroup -ResourceGroupName $resourceGroup -ServerName $server -FailoverGroupName $failoverGroup
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

> [!IMPORTANT]
> Voer `az login` uit om u aan te melden bij Azure.

```azurecli
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>" # must be different then $location
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
az sql server create --admin-password $password --admin-user $admin `
    --name $drServer --resource-group $resourceGroup --location $drLocation

# create a failover group between the servers
az sql failover-group create --name $failoverGroup --partner-server $drServer `
    --resource-group $resourceGroup --server $server --add-db $database `
    --failover-policy Automatic --grace-period 2
```

* * *

Georeplicatie-instellingen kunnen ook worden gewijzigd in de**Azure-portal**door uw database te selecteren en vervolgens **Geo-replicatie instellingen** > in te zetten.

![Instellingen voor georeplicatie](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>Het voorbeeldproject uitvoeren

1. Maak in de console een Maven-project met de volgende opdracht:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Typ **Y** en druk op **Enter**.

1. Verander mappen in het nieuwe project.

   ```bash
   cd SqlDbSample
   ```

1. Open het *pom.xml-bestand* in uw projectmap met behulp van uw favoriete editor.

1. Voeg het Microsoft JDBC-stuurprogramma toe voor `dependency` SQL Server-afhankelijkheid door de volgende sectie toe te voegen. De afhankelijkheid moet worden geplakt `dependencies` binnen de grotere sectie.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Geef de Java-versie `properties` op `dependencies` door de sectie na de sectie toe te voegen:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Ondersteuning voor manifestbestanden `build` door `properties` de sectie na de sectie toe te voegen:

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

1. Sla het bestand *pom.xml* op en sluit het.

1. Open het *App.java-bestand* in .. \SqlDbSample\src\main\java\com\sqldbsamples en vervang de inhoud door de volgende code:

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

1. Sla het *app.java-bestand* op en sluit deze af.

1. Voer in de opdrachtconsole de volgende opdracht uit:

   ```bash
   mvn package
   ```

1. Start de toepassing die ongeveer 1 uur wordt uitgevoerd totdat u handmatig wordt gestopt, zodat u de tijd hebt om de failovertest uit te voeren.

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

Voer de volgende scripts uit om een failover te simuleren en de toepassingsresultaten te observeren. Merk op hoe sommige inserts en selecties mislukken tijdens de databasemigratie.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U de rol van de rampherstelserver tijdens de test controleren met de volgende opdracht:

```powershell
(Get-AzSqlDatabaseFailoverGroup -FailoverGroupName $failoverGroup `
    -ResourceGroupName $resourceGroup -ServerName $drServer).ReplicationRole
```

Ga als lid van het werk om een failover te testen:

1. Start een handmatige failover van de failovergroep:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $myresourcegroupname `
    -ServerName $drServer -FailoverGroupName $failoverGroup
   ```

1. Failovergroep terugzetten naar de primaire server:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $server -FailoverGroupName $failoverGroup
   ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U de rol van de rampherstelserver tijdens de test controleren met de volgende opdracht:

```azurecli
az sql failover-group show --name $failoverGroup --resource-group $resourceGroup --server $drServer
```

Ga als lid van het werk om een failover te testen:

1. Start een handmatige failover van de failovergroep:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $drServer
   ```

1. Failovergroep terugzetten naar de primaire server:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   ```

* * *

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure SQL-database en -toepassing geconfigureerd voor failover naar een extern gebied en een failoverplan getest. U hebt geleerd hoe u:

> [!div class="checklist"]
> - Een failover-groep met geo-replicatie maken
> - Een Java-toepassing uitvoeren om een Azure SQL-database op te vragen
> - Testfailover

Ga door naar de volgende zelfstudie over het migreren met Behulp van DMS.

> [!div class="nextstepaction"]
> [SQL Server migreren naar Azure SQL-databasebeheerde instantie met DMS](../dms/tutorial-sql-server-to-managed-instance.md)
