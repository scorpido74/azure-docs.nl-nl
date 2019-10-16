---
title: 'Zelf studie: toegang tot Azure Data Lake Storage Gen2 gegevens met Azure Databricks met Spark | Microsoft Docs'
description: Deze zelf studie laat zien hoe u Spark-query's uitvoert op een Azure Databricks cluster om toegang te krijgen tot gegevens in een Azure Data Lake Storage Gen2 Storage-account.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 0607c2b848a486e24654081bd7937cb734394e58
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331841"
---
# <a name="tutorial-access-data-lake-storage-gen2-data-with-azure-databricks-using-spark"></a>Zelf studie: toegang tot Data Lake Storage Gen2 gegevens met Azure Databricks met Spark

In deze zelfstudie ziet u hoe u een Azure Databricks-cluster kunt verbinden met gegevens die zijn opgeslagen in een Azure-opslagaccount waarvoor Azure Data Lake Storage Gen2 is ingeschakeld. Deze verbinding stelt u in staat om systeemeigen query’s en analyses van uw cluster uit te voeren op uw gegevens.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Databricks-cluster maken
> * Niet-gestructureerde gegevens opnemen in een opslagaccount
> * Analyse uitvoeren op gegevens in Blob-opslag

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Azure Data Lake Storage Gen2-account maken.

  Zie [een Azure data Lake Storage Gen2-account maken](data-lake-storage-quickstart-create-account.md).

* Zorg ervoor dat aan uw gebruikersaccount de [rol van Gegevensbijdrager voor opslagblob](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) is toegewezen.

* Installeer AzCopy v10. Zie [Gegevens overdragen met AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

* Een service-principal maken. Zie [How to: de portal gebruiken om een Azure AD-toepassing en Service-Principal te maken die toegang hebben tot resources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

  Er zijn een paar specifieke zaken die u moet doen terwijl u de stappen in het artikel uitvoert.

  : heavy_check_mark: bij het uitvoeren van de stappen in de sectie [toepassing toewijzen aan een rol](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) van het artikel, moet u ervoor zorgen dat u de rol van **BLOB voor gegevens opslag** aan de Service-Principal toewijst.

  > [!IMPORTANT]
  > Zorg ervoor dat u de rol toewijst in het bereik van het Data Lake Storage Gen2-opslagaccount. U kunt een rol toewijzen aan de bovenliggende resourcegroep of het bovenliggende abonnement, maar u ontvangt machtigingsgerelateerde fouten tot die roltoewijzingen zijn doorgegeven aan het opslagaccount.

  : heavy_check_mark: als u de stappen in de sectie [waarden ophalen voor ondertekening in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) het artikel uitvoert, plakt u de Tenant-id, app-id en wachtwoord waarden in een tekst bestand. U hebt deze binnenkort nodig.

### <a name="download-the-flight-data"></a>De vluchtgegevens downloaden

In deze zelfstudie worden vluchtgegevens van het Bureau of Transport Statistics gebruikt om te laten zien hoe u een ETL-bewerking kunt uitvoeren. U moet deze gegevens downloaden om de zelfstudie te kunnen voltooien.

1. Ga naar [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Schakel het selectievakje **Prezipped file** in om alle gegevensvelden te selecteren.

3. Selecteer de knop **Download** en sla de resultaten op uw computer op. 

4. Pak de inhoud van het zipbestand uit en noteer de bestandsnaam en het pad van het bestand. U hebt deze informatie in een latere stap nodig.

## <a name="create-an-azure-databricks-service"></a>Een Azure Databricks-service maken

In dit gedeelte gaat u een Azure Databricks-service maken met behulp van de Azure-portal.

1. Selecteer in Azure Portal **Een resource maken** > **Analyse** > **Azure Databricks**.

    ![Databricks in Azure Portal](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Databricks in Azure Portal")

2. Geef bij **Azure Databricks Service** de volgende waarden op voor het maken van een Databricks-service:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |**Werkruimtenaam**     | Geef een naam op voor de Databricks-werkruimte.  |
    |**Abonnement**     | Selecteer uw Azure-abonnement in de vervolgkeuzelijst.        |
    |**Resourcegroep**     | Geef aan of u een nieuwe resourcegroep wilt maken of een bestaande groep wilt gebruiken. Een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. Zie [Overzicht van Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) voor meer informatie. |
    |**Locatie**     | Selecteer **US - west 2**. Zie [Producten beschikbaar per regio](https://azure.microsoft.com/regions/services/) voor andere beschikbare regio's.       |
    |**Prijscategorie**     |  Selecteer **standaard**.     |

    ![Een Azure Databricks-werkruimte maken](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Een Azure Databricks-service maken")

3. Het duurt enkele minuten om het account te maken. Bekijk de voortgangsbalk bovenaan om de bewerkingsstatus te volgen.

4. Selecteer **Vastmaken aan dashboard** en selecteer **Maken**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Een Apache Spark-cluster in Azure Databricks maken

1. Ga in de Azure-portal naar de Databricks-service die u hebt gemaakt en selecteer **Werkruimte starten**.

2. U wordt omgeleid naar de Azure Databricks-portal. Klik in de portal op **Cluster**.

    ![Databricks in Azure](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Databricks in Azure")

3. Op de pagina **Nieuw cluster** geeft u de waarden op waarmee een nieuw cluster wordt gemaakt.

    ![Een Databricks Spark-cluster maken in Azure](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Een Databricks Spark-cluster maken in Azure")

    Vul de waarden voor de volgende velden in (en laat bij de overige velden de standaardwaarden staan):

    - Voer een naam in voor het cluster.
     
    - Zorg ervoor dat u het selectievakje **Beëindigen na 120 minuten van inactiviteit** inschakelt. Geef een duur (in minuten) op waarna het cluster moet worden beëindigd als het niet wordt gebruikt.

4. Selecteer **Cluster maken**. Als het cluster wordt uitgevoerd, kunt u notitieblokken koppelen aan het cluster en Apache Spark-taken uitvoeren.

## <a name="ingest-data"></a>Gegevens opnemen

### <a name="copy-source-data-into-the-storage-account"></a>Brongegevens kopiëren naar het opslagaccount

Gebruik AzCopy om gegevens uit uw *csv*-bestand te kopiëren naar uw Data Lake Storage Gen2-account.

1. Open een opdrachtpromptvenster en voer de volgende opdracht in om u aan te melden bij uw opslagaccount.

   ```bash
   azcopy login
   ```

   Volg de instructies die worden weer gegeven in het opdracht prompt venster om uw gebruikers account te verifiëren.

2. Voer de volgende opdracht in om gegevens kopiëren vanuit het *csv*-account.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<container-name>/folder1/On_Time.csv
   ```

   * Vervang de tijdelijke aanduiding voor @no__t 0 door het pad naar het *CSV* -bestand.

   * Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

   * Vervang de tijdelijke aanduiding voor @no__t 0 door een wille keurige naam die u wilt toewijzen aan de container.

## <a name="create-a-container-and-mount-it"></a>Een container maken en koppelen

In deze sectie maakt u een container en een map in uw opslag account.

1. Ga in de [Azure-portal](https://portal.azure.com) naar de Azure Databricks-service die u hebt gemaakt en selecteer **Werkruimte starten**.

2. Selecteer aan de linkerkant **Werkruimte**. Selecteer in de **Werkruimte**-vervolgkeuzelijst, **Notitieblok** > **maken**.

    ![Een notitieblok maken in Databricks](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Een notitieblok maken in Databricks")

3. Voer in het dialoogvenster **Notitieblok maken** een naam voor het notitieblok in. Selecteer **Python** als taal en selecteer vervolgens het Apache Spark-cluster dat u eerder hebt gemaakt.

4. Selecteer **Maken**.

5. Kopieer en plak het volgende codeblok in de eerste cel, maar voer deze code nog niet uit.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<appId>",
           "fs.azure.account.oauth2.client.secret": "<password>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. In dit codeblok vervangt u de tijdelijke aanduidingen `appId`, `password`, `tenant` en `storage-account-name` door de waarden die u hebt verzameld bij het uitvoeren van de vereiste stappen voor deze zelfstudie. Vervang de waarde van de tijdelijke aanduiding @no__t 0 door de naam die u in de vorige stap aan de container hebt door gegeven.

Gebruik deze waarden om de vermelde tijdelijke aanduidingen te vervangen.

   * De tijdelijke aanduidingen `appId` en `password` zijn afkomstig uit de app die u bij Active Directory hebt geregistreerd tijdens het maken van een service-principal.

   * De tijdelijke aanduiding `tenant-id` is afkomstig van uw abonnement.

   * De tijdelijke aanduiding `storage-account-name` is de naam van uw Azure Data Lake Storage Gen2-opslagaccount.

   * Vervang de tijdelijke aanduiding voor @no__t 0 door een wille keurige naam die u wilt toewijzen aan de container.

   > [!NOTE]
   > In een productie-instelling kunt u uw wacht woord opslaan in Azure Databricks. Voeg vervolgens een zoek sleutel toe aan uw code blok in plaats van het wacht woord. Nadat u deze quickstart hebt afgerond, raadpleegt u het artikel [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) (Engelstalig) op de website van Azure Databricks voor voorbeelden van deze methode.

19. Druk op de toetsen **Shift + Enter** om de code in dit blok uit te voeren.

   Houd dit notitieblok open, want u gaat er later opdrachten aan toevoegen.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Databricks Notebook gebruiken om CSV te converteren naar Parquet

Voeg aan het notitieblok dat u eerder hebt gemaakt een nieuwe cel toe en plak de volgende code in die cel. 

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>Gegevens verkennen

Plak de volgende code in een nieuwe cel om een lijst op te halen met CSV-bestanden die zijn geüpload via AzCopy.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata"))
```

Als u een nieuw bestand en een nieuwe lijst met bestanden wilt maken in de map *parquet/flights*, voert u dit script uit:

```python
dbutils.fs.put("/mnt/flightdata/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/parquet/flights")
```

Met deze codevoorbeelden hebt u de hiërarchische aard van HDFS verkend met behulp van gegevens die zijn opgeslagen in een opslagaccount met Azure Data Lake Storage Gen2.

## <a name="query-the-data"></a>Query’s uitvoeren voor de gegevens

Hierna kunt u beginnen met het doorzoeken van de gegevens die u hebt geüpload in het opslagaccount. Voer elk van de volgende codeblokken in **Cmd 1** in en druk op **Cmd + Enter** om het Python-script uit te voeren.

Als u dataframes wilt maken voor uw gegevensbronnen, voert u het volgende script uit:

* Vervang de tijdelijke aanduiding voor @no__t 0 door het pad naar het *CSV* -bestand.

```python
# Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/On_Time.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

# read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(
    header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

# print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

# print the flight database size
print("Number of flights in the database: ", flightDF.count())

# show the first 20 rows (20 is the default)
# to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

# Display to run visualizations
# preferably run this in a separate cmd cell
display(flightDF)
```

Voer dit script uit om enkele basisquery's op de gegevens uit te voeren.

```python
# Run each of these queries, preferably in a separate cmd cell for separate analysis
# create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

# create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

# using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights = out2.count()
print("Jan 2016: ", NumJan2016Flights, " Feb 2016: ", NumFeb2016Flights)
Total = NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql(
    "SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'")
print('Airports in Texas: ', out.show(100))

# find all airlines that fly from Texas
out1 = spark.sql(
    "SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep en alle gerelateerde resources, wanneer u deze niet meer nodig hebt. Hiervoor selecteert u de resourcegroep voor het opslagaccount en selecteert u **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Gegevens uitpakken, transformeren en laden met Apache Hive in Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)
