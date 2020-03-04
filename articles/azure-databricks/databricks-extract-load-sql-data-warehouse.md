---
title: Zelf studie-ETL-bewerkingen uitvoeren met Azure Databricks
description: In deze zelf studie leert u hoe u gegevens extraheert van Data Lake Storage Gen2 naar Azure Databricks, hoe u de gegevens transformeert en vervolgens de gegevens in Azure SQL Data Warehouse laadt.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: 9cab78e85b8644f29bfcd067b104b1b5c10c2266
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249840"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Zelf studie: gegevens extra heren, transformeren en laden met behulp van Azure Databricks

In deze zelfstudie voert u een ETL-bewerking (Extraction, Transformation, and Loading) uit met behulp van Azure Databricks. U haalt gegevens op uit Azure Data Lake Storage Gen2 naar Azure Databricks, voert trans formaties uit op de gegevens in Azure Databricks en laadt de getransformeerde gegevens in Azure SQL Data Warehouse.

Voor de stappen in deze zelfstudie wordt gebruik gemaakt van de SQL Data Warehouse-connector voor Azure Databricks om gegevens over te dragen naar Azure Databricks. Op zijn beurt gebruikt deze connector Azure Blob Storage als tijdelijke opslag voor de gegevens die worden overgebracht tussen een Azure Databricks-cluster en Azure SQL Data Warehouse.

In de volgende afbeelding wordt de stroom van de toepassing weergegeven:

![Azure Databricks met Data Lake Store en SQL Data Warehouse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks met Data Lake Store en SQL Data Warehouse")

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een Azure Databricks-service maken.
> * Een Apache Spark-cluster in Azure Databricks maken.
> * Een bestandssysteem in uw Data Lake Storage Gen2-account maken.
> * Voorbeeldgegevens in het Azure Data Lake Storage Gen2-account uploaden.
> * Een service-principal maken.
> * Gegevens uit het Azure Data Lake Storage Gen2-account extraheren.
> * Gegevens transformeren in Azure Databricks.
> * Gegevens laden in Azure SQL Data Warehouse.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!Note]
> Deze zelf studie kan niet worden uitgevoerd met een **gratis proef abonnement van Azure**.
> Als u een gratis account hebt, gaat u naar uw profiel en wijzigt u uw abonnement in **betalen per gebruik**. Zie [Gratis Azure-account](https://azure.microsoft.com/free/) voor meer informatie. Vervolgens [verwijdert u de bestedings limiet](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)en [vraagt u een quotum toename](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) aan voor vcpu's in uw regio. Wanneer u uw Azure Databricks-werk ruimte maakt, kunt u de prijs categorie **Trial (Premium-14-dagen gratis dbu's)** selecteren om de werk ruimte gedurende 14 dagen toegang te geven tot gratis premium Azure Databricks dbu's.
     
## <a name="prerequisites"></a>Vereisten

Voltooi deze taken voordat u aan deze zelfstudie begint:

* Maak een Azure SQL Data Warehouse, maak een firewall regel op server niveau en maak verbinding met de server als server beheerder. Zie [Quick Start: een Azure SQL-Data Warehouse maken en er een query op uitvoeren in de Azure Portal](../sql-data-warehouse/create-data-warehouse-portal.md).

* Maak een hoofd sleutel voor Azure SQL Data Warehouse. Zie [Een databasehoofdsleutel maken](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Maak een Azure Blob-opslagaccount met daarin een container. Haal ook de toegangssleutel op voor toegang tot het opslagaccount. Zie [Quick Start: blobs uploaden, downloaden en vermelden met de Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

* Een Azure Data Lake Storage Gen2-opslagaccount maken. Zie [Quick Start: een Azure data Lake Storage Gen2-opslag account maken](../storage/blobs/data-lake-storage-quickstart-create-account.md).

* Een service-principal maken. Zie [How to: de portal gebruiken om een Azure AD-toepassing en Service-Principal te maken die toegang hebben tot resources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   Er zijn een paar specifieke zaken die u moet doen terwijl u de stappen in het artikel uitvoert.

   * Bij het uitvoeren van de stappen in de sectie [de toepassing toewijzen aan een rol](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) van het artikel, moet u ervoor zorgen dat u de rol voor **blobgegevens voor gegevens opslag** toewijst aan de Service-Principal in het bereik van het data Lake Storage Gen2-account. Als u de rol toewijst aan de bovenliggende resource groep of het abonnement, ontvangt u aan machtigingen gerelateerde fouten tot deze roltoewijzingen worden door gegeven aan het opslag account.

      Als u liever een toegangs beheer lijst (ACL) wilt gebruiken om de service-principal te koppelen aan een specifiek bestand of een specifieke directory, verwijst u naar het [toegangs beheer in azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

   * Bij het uitvoeren van de stappen in de sectie [waarden ophalen voor ondertekening in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) het artikel plakt u de Tenant-id, app-id en geheime waarden in een tekst bestand. U hebt deze binnenkort nodig.

* Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="gather-the-information-that-you-need"></a>Verzamel de benodigde informatie

Zorg dat u over alle vereisten voor deze zelfstudie beschikt.

   Voor u begint, moet u de volgende gegevens hebben:

   : heavy_check_mark: de naam van de data base, de naam van de database server, de gebruikers naam en het wacht woord van uw Azure SQL Data Warehouse.

   : heavy_check_mark: de toegangs sleutel van uw Blob Storage-account.

   : heavy_check_mark: de naam van uw Data Lake Storage Gen2 Storage-account.

   : heavy_check_mark: de Tenant-ID van uw abonnement.

   : heavy_check_mark: de toepassings-ID van de app die u hebt geregistreerd bij Azure Active Directory (Azure AD).

   : heavy_check_mark: de verificatie sleutel voor de app die u hebt geregistreerd bij Azure AD.

## <a name="create-an-azure-databricks-service"></a>Een Azure Databricks-service maken

In dit gedeelte gaat u een Azure Databricks-service maken met behulp van de Azure-portal.

1. Selecteer in het menu Azure Portal de optie **een resource maken**.

    ![Een resource maken op Azure Portal](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png)

    Selecteer vervolgens **Analytics** > **Azure Databricks**.

    ![Azure Databricks maken op Azure Portal](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-resource-create.png)



2. Geef bij **Azure Databricks Service** de volgende waarden op voor het maken van een Databricks-service:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |**Werkruimtenaam**     | Geef een naam op voor de Databricks-werkruimte.        |
    |**Abonnement**     | Selecteer uw Azure-abonnement in de vervolgkeuzelijst.        |
    |**Resourcegroep**     | Geef aan of u een nieuwe resourcegroep wilt maken of een bestaande groep wilt gebruiken. Een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. Zie [Overzicht van Azure Resource Manager](../azure-resource-manager/management/overview.md) voor meer informatie. |
    |**Locatie**     | Selecteer **VS - west 2**.  Zie [Producten beschikbaar per regio](https://azure.microsoft.com/regions/services/) voor andere beschikbare regio's.      |
    |**Prijscategorie**     |  selecteer **Standaard**.     |

3. Het duurt enkele minuten om het account te maken. Bekijk de voortgangsbalk bovenaan om de bewerkingsstatus te volgen.

4. Selecteer **Vastmaken aan dashboard** en selecteer **Maken**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Een Apache Spark-cluster in Azure Databricks maken

1. Ga in de Azure-portal naar de Databricks-service die u hebt gemaakt en selecteer **Werkruimte starten**.

2. U wordt omgeleid naar de Azure Databricks-portal. Klik in de portal op **Cluster**.

    ![Databricks op Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Databricks op Azure")

3. Op de pagina **Nieuw cluster** geeft u de waarden op waarmee een nieuw cluster wordt gemaakt.

    ![Een Databricks Spark-cluster maken in azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Een Databricks Spark-cluster maken in azure")

4. Vul de waarden voor de volgende velden in (en laat bij de overige velden de standaardwaarden staan):

    * Voer een naam in voor het cluster.

    * Zorg ervoor dat u het selectievakje **Beëindigen na \_\_ minuten van inactiviteit** inschakelt. Geef een duur (in minuten) op waarna het cluster moet worden beëindigd als het niet wordt gebruikt.

    * Selecteer **Cluster maken**. Als het cluster wordt uitgevoerd, kunt u notitieblokken koppelen aan het cluster en Apache Spark-taken uitvoeren.

## <a name="create-a-file-system-in-the-azure-data-lake-storage-gen2-account"></a>Een bestandssysteem in uw Azure Data Lake Storage Gen2-account maken

In deze sectie maakt u een notebook in de Azure Databricks-werkruimte en voert u vervolgens codefragmenten uit om het opslagaccount te configureren

1. Ga in de [Azure-portal](https://portal.azure.com) naar de Azure Databricks-service die u hebt gemaakt en selecteer **Werkruimte starten**.

2. Selecteer aan de linkerkant **Werkruimte**. Selecteer in de **Werkruimte**-vervolgkeuzelijst, **Notitieblok** > **maken**.

    ![Een notitie blok maken in Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Een notitie blok maken in Databricks")

3. Voer in het dialoogvenster **Notitieblok maken** een naam voor het notitieblok in. Selecteer **Scala** als taal en selecteer het Spark-cluster dat u eerder hebt gemaakt.

    ![Details opgeven voor een notitie blok in Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Details opgeven voor een notitie blok in Databricks")

4. Selecteer **Maken**.

5. In het volgende code blok worden de standaard referenties voor de Service-Principal ingesteld voor elk ADLS gen 2-account dat in de Spark-sessie wordt geopend. Het tweede code blok voegt de account naam toe aan de instelling om referenties op te geven voor een specifiek ADLS gen 2-account.  Kopieer en plak een van beide code blokken in de eerste cel van uw Azure Databricks notitie blok.

   **Sessie configuratie**

   ```scala
   val appID = "<appID>"
   val password = "<password>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   ```

   **Accountconfiguratie**

   ```scala
   val storageAccountName = "<storage-account-name>"
   val appID = "<app-id>"
   val secret = "<secret>"
   val fileSystemName = "<file-system-name>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type." + storageAccountName + ".dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type." + storageAccountName + ".dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id." + storageAccountName + ".dfs.core.windows.net", "" + appID + "")
   spark.conf.set("fs.azure.account.oauth2.client.secret." + storageAccountName + ".dfs.core.windows.net", "" + secret + "")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint." + storageAccountName + ".dfs.core.windows.net", "https://login.microsoftonline.com/" + tenantID + "/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://" + fileSystemName  + "@" + storageAccountName + ".dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
   ```

6. In dit codeblok vervangt u de tijdelijke aanduidingen `<app-id>`, `<secret>`, `<tenant-id>` en `<storage-account-name>` door de waarden die u hebt verzameld bij het uitvoeren van de vereiste stappen voor deze zelfstudie. Vervang de tijdelijke aanduiding `<file-system-name>` door de naam die u het bestandssysteem wilt geven.

   * De tijdelijke aanduidingen `<app-id>` en `<secret>` zijn afkomstig uit de app die u bij Active Directory hebt geregistreerd tijdens het maken van een service-principal.

   * De tijdelijke aanduiding `<tenant-id>` is afkomstig van uw abonnement.

   * De tijdelijke aanduiding `<storage-account-name>` is de naam van uw Azure Data Lake Storage Gen2-opslagaccount.

7. Druk op de toetsen **Shift + Enter** om de code in dit blok uit te voeren.

## <a name="ingest-sample-data-into-the-azure-data-lake-storage-gen2-account"></a>Voorbeeldgegevens in het Azure Data Lake Storage Gen2-account opnemen

Voordat u met deze sectie begint, moet u eerst aan de volgende vereisten voldoen:

Voer de volgende code in een notitieblokcel in:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

Druk in de cel op **SHIFT+ENTER** om de code uit te voeren.

Voer nu in een cel onder deze cel de volgende code in, en vervang de waarden tussen haakjes door dezelfde waarden die u eerder hebt gebruikt:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://" + fileSystemName + "@" + storageAccountName + ".dfs.core.windows.net/")

Druk in de cel op **SHIFT+ENTER** om de code uit te voeren.

## <a name="extract-data-from-the-azure-data-lake-storage-gen2-account"></a>Gegevens uit het Azure Data Lake Storage Gen2-account extraheren

1. U kunt nu het JSON-voorbeeldbestand laden als een dataframe in Azure Databricks. Plak de volgende code in de nieuwe cel. Vervang de tijdelijke aanduidingen tussen haken door uw eigen waarden.

   ```scala
   val df = spark.read.json("abfss://" + fileSystemName + "@" + storageAccountName + ".dfs.core.windows.net/small_radio_json.json")
   ```
2. Druk op de toetsen **Shift + Enter** om de code in dit blok uit te voeren.

3. Voer de volgende code uit om de inhoud van het dataframe weer te geven:

    ```scala
    df.show()
    ```
   Het volgende (of een vergelijkbaar) codefragment wordt weergegeven:

   ```output
   +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
   |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
   +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
   | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
   | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
   | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
   ...
   ...
   ```

   U hebt de gegevens nu geëxtraheerd uit Azure Data Lake Store Gen2 en geladen in Azure Databricks.

## <a name="transform-data-in-azure-databricks"></a>Gegevens transformeren in Azure Databricks

Het bestand **small_radio_json.json** met de onbewerkte voorbeeldgegevensset legt de doelgroep voor een radiozender vast en bestaat uit een aantal kolommen. In deze sectie gaat u de gegevens zodanig transformeren dat alleen bepaalde kolommen uit de gegevensset worden opgehaald.

1. Haal eerst alleen de kolommen **firstName**, **lastName**, **gender**, **location** en **level** op uit het dataframe dat u hebt gemaakt.

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
   ```

   De uitvoer die u ontvangt, wordt weergegeven in het volgende codefragment:

   ```output
   +---------+----------+------+--------------------+-----+
   |firstname|  lastname|gender|            location|level|
   +---------+----------+------+--------------------+-----+
   | Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |     Tess|  Townsend|     F|Nashville-Davidso...| free|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
   |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Tess|  Townsend|     F|Nashville-Davidso...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   +---------+----------+------+--------------------+-----+
   ```

2. U kunt deze gegevens nog verder transformeren door de naam van de kolom **level** te wijzigen in **subscription_type**.

   ```scala
   val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
   renamedColumnsDF.show()
   ```

   De uitvoer die u ontvangt, wordt weergegeven in het volgende codefragment.

   ```output
   +---------+----------+------+--------------------+-----------------+
   |firstname|  lastname|gender|            location|subscription_type|
   +---------+----------+------+--------------------+-----------------+
   | Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
   |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   +---------+----------+------+--------------------+-----------------+
   ```

## <a name="load-data-into-azure-sql-data-warehouse"></a>Gegevens laden in Azure SQL Data Warehouse

In deze sectie uploadt u de getransformeerde gegevens naar Azure SQL Data Warehouse. Gebruik de Azure SQL Data Warehouse-connector voor Azure Databricks om een dataframe rechtstreeks als een tabel in een SQL-datawarehouse te uploaden.

Zoals eerder vermeld, maakt de SQL Data Warehouse-connector gebruik van Azure Blob Storage als tijdelijke opslag voor het uploaden van gegevens tussen Azure Databricks en Azure SQL Data Warehouse. U begint met het opgeven van de configuratie om verbinding te maken met het opslagaccount. U moet het account al hebben gemaakt als onderdeel van de vereisten voor dit artikel.

1. Geef de configuratie op voor toegang tot het Azure Storage-account vanuit Azure Databricks.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Geef een tijdelijke map op die wordt gebruikt tijdens het verplaatsen van gegevens tussen Azure Databricks en Azure SQL Data Warehouse.

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. Voer het volgende codefragment uit om toegangssleutels voor Azure Blob-opslag op te slaan in de configuratie. Deze actie zorgt ervoor dat u de toegangssleutel niet als gewone tekst in het notebook hoeft te bewaren.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
   ```

4. Geef de waarden op om verbinding te maken met de Azure SQL Data Warehouse-instantie. U moet als vereiste een SQL-datawarehouse hebben gemaakt. Gebruik de volledig gekwalificeerde server naam voor **dwServer**. Bijvoorbeeld `<servername>.database.windows.net`.

   ```scala
   //SQL Data Warehouse related settings
   val dwDatabase = "<database-name>"
   val dwServer = "<database-server-name>"
   val dwUser = "<user-name>"
   val dwPass = "<password>"
   val dwJdbcPort =  "1433"
   val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
   ```

5. Voer het volgende codefragment uit om het getransformeerde dataframe, **renamedColumnsDF**, als een tabel te laden in een SQL-datawarehouse. Met dit fragment wordt een tabel met de naam **SampleTable** gemaakt in de SQL-database.

   ```scala
   spark.conf.set(
       "spark.sql.parquet.writeLegacyFormat",
       "true")

   renamedColumnsDF.write.format("com.databricks.spark.sqldw").option("url", sqlDwUrlSmall).option("dbtable", "SampleTable")       .option( "forward_spark_azure_storage_credentials","True").option("tempdir", tempDir).mode("overwrite").save()
   ```

   > [!NOTE]
   > In dit voor beeld wordt gebruikgemaakt van de `forward_spark_azure_storage_credentials` vlag, waarmee SQL Data Warehouse toegang krijgt tot gegevens uit de Blob-opslag met behulp van een toegangs sleutel. Dit is de enige ondersteunde verificatie methode.
   >
   > Als uw Azure-Blob Storage is beperkt tot het selecteren van virtuele netwerken, heeft SQL Data Warehouse [Managed Service Identity vereist in plaats van toegangs sleutels](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Dit leidt ertoe dat de fout ' deze aanvraag is niet gemachtigd om deze bewerking uit te voeren. '

6. Maak verbinding met de SQL-database en controleer of u de database **SampleTable** ziet.

   ![De voorbeeld tabel verifiëren](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Voorbeeld tabel verifiëren")

7. Voer een Select-query uit om de inhoud van de tabel te controleren. De tabel moet dezelfde gegevens bevatten als het dataframe **renamedColumnsDF**.

    ![De inhoud van de voorbeeld tabel controleren](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "De inhoud van de voorbeeld tabel controleren")

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de zelfstudie hebt voltooid, kunt u het cluster beëindigen. Selecteer links **Clusters** vanuit de Azure Databricks-werkruimte. Als u het cluster wilt beëindigen, wijst u onder **Acties** het beletselteken (...) aan en selecteert u het pictogram **Beëindigen**.

![Een Databricks-cluster stoppen](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Een Databricks-cluster stoppen")

Als u het cluster niet handmatig beëindigt, stopt het cluster automatisch, op voorwaarde dat het selectievakje **Beëindigen na \_\_ minuten van inactiviteit** is ingeschakeld tijdens het maken van het cluster. In dat geval stopt het cluster automatisch als het gedurende de opgegeven tijd inactief is geweest.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een Azure Databricks-service maken
> * Een Apache Spark-cluster in Azure Databricks maken
> * Een notitieblok maken in Azure Databricks
> * Gegevens extraheren uit een Data Lake Storage Gen2-account
> * Gegevens transformeren in Azure Databricks
> * Gegevens laden in Azure SQL Data Warehouse

Ga naar de volgende zelfstudie voor informatie over het streamen van realtime gegevens naar Azure Databricks met behulp van Azure Event Hubs.

> [!div class="nextstepaction"]
>[Gegevens streamen naar Azure Databricks met behulp van Event Hubs](databricks-stream-from-eventhubs.md)
