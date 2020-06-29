---
title: 'Zelfstudie: Aan de slag met Azure Synapse Analytics'
description: In deze zelfstudie leert u de basisstappen voor het instellen en gebruiken van Azure Synapse Analytics.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: daa8b594b06203c7de9a9b462be469dd71ed2e49
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791857"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Aan de slag met Azure Synapse Analytics

In deze zelfstudie wordt u begeleid bij de basisstappen voor het instellen en gebruiken van Azure Synapse Analytics.

## <a name="prepare-a-storage-account"></a>Een opslagaccount voorbereiden

1. Open de [Azure Portal](https://portal.azure.com).
1. Maak een nieuw opslagaccount met de volgende instellingen:

    |Tabblad|Instelling | Voorgestelde waarde | Beschrijving |
    |---|---|---|---|
    |Basisbeginselen|**Naam van opslagaccount**| U kunt een willekeurige naam opgeven.|In dit document heeft het de naam **contosolake**.|
    |Basisbeginselen|**Type account**|Moet worden ingesteld op **StorageV2**.||
    |Basisbeginselen|**Locatie**|Kies een willekeurige locatie.| We raden u aan uw Azure Synapse Analytics-werkruimte en Azure Data Lake Storage Gen2-account in dezelfde regio te bewaren.|
    |Geavanceerd|**Data Lake Storage Gen2**|**Ingeschakeld**| Azure Synapse werkt alleen met opslagaccounts waarvoor deze instelling is ingeschakeld.|
    |||||

1. Nadat u het opslagaccount hebt gemaakt, selecteert u **Toegangsbeheer (IAM)** in het linkerdeelvenster. Wijs vervolgens de volgende rollen toe of zorg ervoor dat deze al zijn toegewezen:
    1. Wijs uzelf toe aan de rol **Eigenaar**.
    1. Wijs uzelf toe aan de rol **Eigenaar van opslagblobgegevens**.
1. Selecteer in het navigatievenster links de optie **Containers** en maak een container.
1. U kunt de container een willekeurige naam geven. In dit document noemen we de container **users**.
1. Accepteer de standaardinstelling **Openbaar toegangsniveau** en selecteer vervolgens **Maken**.

In de volgende stap configureert u de Azure Synapse-werkruimte voor het gebruik van dit opslagaccount als primair opslagaccount en de container voor het opslaan van werkruimtegegevens. In de werkruimte worden gegevens opgeslagen in Apache Spark-tabellen. Het slaat Apache Spark-toepassingslogboeken op in een map met de naam **/synapse/workspacename**.

## <a name="create-a-synapse-workspace"></a>Een Synapse-werkruimte maken

1. Open de [Azure-portal](https://portal.azure.com) en zoek bovenin naar **Synapse**.
1. Selecteer in de zoekresultaten onder **Services** de optie **Azure Synapse Analytics (voorbeeld van werkruimten)** .
1. Selecteer **Toevoegen** om een werkruimte te maken met behulp van deze instellingen:

    |Tabblad|Instelling | Voorgestelde waarde | Beschrijving |
    |---|---|---|---|
    |Basisbeginselen|**Werkruimtenaam**|U kunt deze werkruimte elke naam geven die u wilt.| In dit document gebruiken we **myworkspace**.|
    |Basisbeginselen|**Regio**|Maak deze gelijk aan de regio van het opslagaccount.|

1. Selecteer onder **Data Lake Storage Gen 2 selecteren** het account en de container die u eerder hebt gemaakt.
1. Selecteer **Beoordelen en maken** > **Maken**. Uw werkruimte is binnen een paar minuten klaar.

## <a name="verify-access-to-the-storage-account"></a>De toegang tot het opslagaccount controleren

Beheerde identiteiten voor uw Azure Synapse-werkruimte hebben mogelijk al toegang tot het opslagaccount. Voer de volgende stappen uit om dat te controleren:

1. Open de [Azure-portal](https://portal.azure.com) en open het primaire opslagaccount dat u hebt gekozen voor uw werkruimte.
1. Selecteer **Toegangsbeheer (IAM)** in het linkerdeelvenster.
1. Wijs vervolgens de volgende rollen toe of controleer of ze al zijn toegewezen. We gebruiken dezelfde naam voor de werkruimte-id en de werkruimtenaam.
    1. Wijs voor de rol **Bijdrager voor opslagblobgegevens** in het opslagaccount **myworkspace** toe als werkruimte-id.
    1. Wijs **myworkspace** als werkruimtenaam toe.

1. Selecteer **Opslaan**.

## <a name="open-synapse-studio"></a>Synapse Studio openen

Wanneer uw Azure Synapse-werkruimte is gemaakt, kunt u Synapse Studio op twee manieren openen:

* Open de Synapse-werkruimte in de [Azure-portal](https://portal.azure.com). Selecteer bovenaan de sectie **Overzicht** de optie **Synapse Studio starten**.
* Ga naar https://web.azuresynapse.net en meld u aan bij uw werkruimte.

## <a name="create-a-sql-pool"></a>Een SQL-pool maken

1. Selecteer in Synapse Studio in het linkerdeelvenster de optie **Beheren** > **SQL-pools**.
1. Selecteer **Nieuw** en voer deze instellingen in:

    |Instelling | Voorgestelde waarde | 
    |---|---|---|
    |**Naam van SQL-pool**| **SQLDB1**|
    |**Prestatieniveau**|**DW100C**|
    |||

1. Selecteer **Beoordelen en maken** > **Maken**. Uw SQL-pool is binnen een paar minuten klaar. Uw SQL-pool is gekoppeld aan een SQL-pooldatabase die ook **SQLDB1** heet.

Een SQL-pool verbruikt factureerbare resources zolang deze worden uitgevoerd. U kunt de pool later onderbreken om de kosten te verlagen.

## <a name="create-an-apache-spark-pool"></a>Een Apache Spark-pool maken

1. Selecteer in Synapse Studio in het linkerdeelvenster **Beheren** > **Apache Spark-pools**.
1. Selecteer **Nieuw** en voer deze instellingen in:

    |Instelling | Voorgestelde waarde | 
    |---|---|---|
    |**Naam van Apache Spark-pool**|**Spark1**
    |**Knooppuntgrootte**| **Klein**|
    |**Aantal knooppunten**| Stel het minimum in op 3 en het maximum op 3|

1. Selecteer **Beoordelen en maken** > **Maken**. Uw Apache Spark-pool is binnen een paar seconden gereed.

> [!NOTE]
> Ondanks de naam is een Apache Spark-pool niet hetzelfde als een SQL-pool. Dit zijn slechts enkele basismetagegevens die u gebruikt om de Azure Synapse-werkruimte te vertellen hoe met Apache Spark moet worden gewerkt.

Omdat ze uit metagegevens bestaan, kunnen Spark-pools niet worden gestart of gestopt.

Wanneer u een Apache Spark-activiteit uitvoert in Azure Synapse, geeft u een Spark-pool op die moet worden gebruikt. De pool informeert Azure Synapse hoeveel Apache Spark-resources moeten worden gebruikt. U betaalt alleen voor de resources die u gebruikt. Wanneer u de pool niet meer actief gebruikt, treedt er automatisch een time-out op in de resources en worden ze gerecycled.

> [!NOTE]
> Apache Spark-databases worden onafhankelijk van Apache Spark-pools gemaakt. Een werkruimte heeft altijd een Apache Spark-database met de naam **standaard**. U kunt extra Apache Spark-databases maken.

## <a name="the-sql-on-demand-pool"></a>De SQL on-demand-pool

Elke werkruimte wordt geleverd met een vooraf gemaakte pool met de naam **SQL on-demand**. Deze pool kan niet worden verwijderd. Met de pool SQL on-demand kunt u met SQL werken zonder dat u een SQL-pool hoeft te maken of beheren in Azure Synapse.

In tegenstelling tot de andere soorten pools is facturering voor SQL on-demand gebaseerd op de hoeveelheid gegevens die zijn gescand om de query uit te voeren en niet op het aantal resources dat wordt gebruikt om de query uit te voeren.

* SQL on-demand heeft ook zijn eigen SQL on-demand-databases die onafhankelijk van elke SQL on-demand-pool bestaan.
* Een werkruimte heeft altijd precies één SQL on-demand-pool met de naam **SQL on-demand**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>De gegevens van het NYC-taxivoorbeeld laden in de SQLDB1-database

1. Selecteer in Synapse Studio in het bovenste blauwe menu het pictogram **?** .
1. Selecteer **Aan de slag** > **Aan de slag-hub**.
1. Selecteer in de kaart **Query uitvoeren op voorbeeldgegevens** de SQL-pool met de naam **SQLDB1**.
1. Selecteer **Querygegevens**. Er wordt kort een melding over het laden van voorbeeldgegevens weergegeven. Een lichtblauwe statusbalk boven in Synapse Studio geeft aan dat gegevens in SQLDB1 worden geladen.
1. Nadat de statusbalk groen wordt, sluit u deze.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>De NYC-taxigegevens in de SQL-pool verkennen

1. Ga in Synapse Studio naar de hub **Gegevens**.
1. Ga naar **SQLDB1** > **Tabellen**. Er wordt een aantal tabellen geladen.
1. Klik met de rechtermuisknop op de tabel **dbo.Trip** en selecteer **Nieuw SQL-script** > **Eerste 100 rijen selecteren**.
1. Wacht tot er een nieuw SQL-script wordt gemaakt en uitgevoerd.
1. U ziet dat bovenaan het SQL-script **Verbinding maken met** automatisch is ingesteld op de SQL-pool met de naam **SQLDB1**.
1. Vervang de tekst van het SQL-script door deze code en voer deze uit.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    Deze query laat zien op welke manier de totale reisafstanden en de gemiddelde reisafstand betrekking hebben op het aantal reizigers.
1. In het resultatenvenster van het SQL-script wijzigt u de **Weergave** in **Grafiek** om een visualisatie van de resultaten weer te geven als een lijndiagram.

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>De gegevens van NYC-taxi laden in de Apache Spark-database nyctaxi

Er zijn gegevens beschikbaar in een tabel in **SQLDB1**. Laad deze in een Apache Spark-database met de naam **nyctaxi**.

1. Ga in Synapse Studio naar de hub **Ontwikkelen**.
1. Selecteer **+**  > **Notebook**.
1. Stel bovenaan de notebook de waarde voor **Koppelen aan** in op **Spark1**.
1. Selecteer **Code toevoegen** om een notebookcodecel toe te voegen en plak de volgende tekst:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Ga naar de hub **Data**, klik met de rechtermuisknop op **Databases** en selecteer **Vernieuwen**. U zou deze databases moeten zien:

    - **SQLDB1** (SQL-pool)
    - **nyctaxi** (Apache Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>De gegevens over de NYC-taxi met behulp van Apache Spark en notebooks analyseren

1. Ga terug naar uw notebook.
1. Maak een nieuwe codecel en voer de volgende tekst in. Voer vervolgens de cel uit om de NYC-taxigegevens weer te geven die zijn geladen in de Apache Spark-database **nyctaxi**.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Voer de volgende code uit om dezelfde analyse uit te voeren die we eerder met de SQL-pool **SQLDB1** hebben uitgevoerd. Met deze code worden ook de resultaten van de analyse opgeslagen in een tabel met de naam **nyctaxi.passengercountstats** en worden de resultaten gevisualiseerd.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Selecteer **Grafiek** in de celresultaten om de gevisualiseerde gegevens weer te geven.

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Gegevensvisualisatie aanpassen met Apache Spark en notebooks

U kunt bepalen hoe grafieken worden weergegeven met behulp van notebooks. De volgende code toont een eenvoudig voorbeeld. De code maakt gebruik van de populaire bibliotheken **matplotlib** en **seaborn**. De code geeft hetzelfde soort lijndiagram weer als de SQL-query's die u eerder hebt uitgevoerd.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```

## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Gegevens uit een Apache Spark-tabel laden in een SQL-pooltabel

Eerder kopieerde u gegevens uit de tabel met SQL-pools **SQLDB1.dbo.Trip** naar de Apache Spark-tabel **nyctaxi.trip**. Vervolgens aggregeerde u de gegevens in de Apache Spark-tabel **nyctaxi.passengercountstats** met behulp van Apache Spark. Nu gaat u de gegevens uit **nyctaxi.passengercountstats** kopiëren naar een SQL-pooltabel met de naam **SQLDB1.dbo.PassengerCountStats**.

Voer de volgende cel uit in uw notebook. Hiermee wordt de geaggregeerde Apache Spark-tabel weer gekopieerd naar de SQL-pooltabel.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>NYC-taxigegevens analyseren in Apache Spark-databases met behulp van SQL on-demand

Tabellen in Apache Spark-databases zijn automatisch zichtbaar en er kunnen query's op worden uitgevoerd met SQL on-demand.

1. Ga in Synapse Studio naar de hub **Ontwikkelen** en maak een nieuw SQL-script.
1. Stel **Verbinding maken met** in op **SQL on-demand**.
1. Plak de volgende tekst in het script en voer het script uit.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > De eerste keer dat u een query uitvoert die SQL on-demand gebruikt, duurt het ongeveer tien seconden tot SQL on-demand de SQL-resources heeft verzameld die nodig zijn om uw query's uit te voeren. Daaropvolgende query's worden veel sneller uitgevoerd.
  
## <a name="orchestrate-activities-with-pipelines"></a>Activiteiten organiseren met pijplijnen

U kunt een groot aantal verschillende taken in Azure Synapse organiseren.

1. Ga in Synapse Studio naar de hub **Organiseren**.
1. Selecteer **+**  > **Pijplijn** om een nieuwe pijplijn te maken.
1. Ga naar de hub **Ontwikkelen** en zoek de notebook op die u eerder hebt gemaakt.
1. Sleep de notebook naar de pijplijn.
1. Selecteer in de pijplijn **Trigger toevoegen** > **Nieuw/bewerken**.
1. Selecteer bij **Trigger kiezen** **Nieuw** in en stel vervolgens bij **terugkeerpatroon** de trigger in om elk uur te worden uitgevoerd.
1. Selecteer **OK**.
1. Selecteer **Alles publiceren**. De pijplijn wordt elk uur uitgevoerd.
1. Als u de pijplijn nu wilt uitvoeren zonder te wachten op het volgende uur, selecteert u **Trigger toevoegen** > **Nieuw/bewerken**.

## <a name="work-with-data-in-a-storage-account"></a>Werken met gegevens in een opslagaccount

Tot nu toe zijn scenario's beschreven waarin gegevens in databases staan die zijn opgenomen in de werkruimte. U leert nu werken met bestanden in opslagaccounts. In dit scenario wordt het primaire opslagaccount van de werkruimte en de container die we hebben opgegeven bij het maken van de werkruimte gebruikt.

* De naam van het opslagaccount: **contosolake**
* De naam van de container in het opslagaccount: **users**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>CSV- en Parquet-bestanden maken in uw opslagaccount

Voer de volgende code uit in een notebook. Er wordt een CSV-bestand en een Parquet-bestand gemaakt in het opslagaccount.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>Gegevens analyseren in een opslagaccount

1. Ga in Synapse Studio naar de hub **Gegevens** en selecteer vervolgens **Gekoppeld**.
1. Navigeer naar **Opslagaccounts** > **myworkspace (Primary - contosolake)** .
1. Selecteer **users (Primary)"** . De map **NYCTaxi** moet worden weergegeven. Hierin ziet u twee mappen, **PassengerCountStats.csv** en **PassengerCountStats.parquet**.
1. Open de map **PassengerCountStats.parquet**. Hierin staat een Parquet-bestand met een naam als *part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet*.
1. Klik met de rechtermuisknop op **.parquet** en selecteer vervolgens **Nieuwe notebook**. Er wordt een notebook gemaakt met een cel als deze:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Voer de cel uit.
1. Klik met de rechtermuisknop op het Parquet-bestand en selecteer **Nieuw SQL-script** > **Eerste 100 rijen selecteren**. Er wordt een SQL-script als deze gemaakt:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

     In het script wordt het veld **Koppelen aan** ingesteld op **SQL on-demand**.

1. Voer het script uit.

## <a name="visualize-data-with-power-bi"></a>Gegevens visualiseren met Power BI

Aan de hand van de NYC-taxigegevens hebben we geaggregeerde gegevenssets gemaakt in twee tabellen:
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

U kunt een Power BI-werkruimte koppelen aan uw Azure Synapse-werkruimte. Zo kunt u eenvoudig gegevens in uw Power BI-werkruimte ophalen. U kunt uw Power BI-rapporten rechtstreeks in uw Azure Synapse-werkruimte bewerken.

### <a name="create-a-power-bi-workspace"></a>Een Power BI-werkruimte maken

1. Meld u aan bij [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Maak een nieuwe Power BI-werkruimte met de naam **NYCTaxiWorkspace1**.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Uw Azure Synapse-werkruimte koppelen aan uw nieuwe Power BI-werkruimte

1. Ga in Synapse Studio naar **Beheren** > **Gekoppelde services**.
1. Selecteer **Nieuw** > **Verbinding maken met Power BI** en stel deze velden in:

    |Instelling | Voorgestelde waarde | 
    |---|---|
    |**Naam**|**NYCTaxiWorkspace1**|
    |**Werkruimtenaam**|**NYCTaxiWorkspace1**|

1. Selecteer **Maken**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Een Power BI-gegevensset maken die gebruikmaakt van gegevens in uw Azure Synapse-werkruimte

1. Ga in Synapse Studio naar **Ontwikkelen** > **Power BI**.
1. Ga naar **NYCTaxiWorkspace1** > **Power BI-gegevenssets** en selecteer **Nieuwe Power BI-gegevensset**.
1. Beweeg de muisaanwijzer over de database **SQLDB1** en selecteer **.pbids-bestand downloaden**.
1. Open het gedownloade **.pbids**-bestand. Power BI Desktop opent en maakt automatisch verbinding met **SQLDB1** in uw Azure Synapse-werkruimte.
1. Als er een dialoogvenster wordt weergegeven met de naam **SQL Server-database**:
    1. Selecteer **Microsoft-account**.
    1. Selecteer **Aanmelden** en meld u aan bij uw account.
    1. Selecteer **Verbinden**.
1. Wanneer het dialoogvenster **Navigator** is geopend, schakelt u de tabel **PassengerCountStats** in en selecteert u **Laden**.
1. Wanneer het dialoogvenster **Verbindingsinstellingen** wordt weergegeven, selecteert u **DirectQuery** > **OK**.
1. Selecteer de knop **Rapport** aan de linkerkant.
1. Voeg **Lijndiagram** toe aan uw rapport.
    1. Sleep de kolom **PasssengerCount** naar **Visualisaties** > **As**.
    1. Sleep de kolommen **SumTripDistance** en **AvgTripDistance** naar **Visualisaties** > **Waarden**.
1. Selecteer op het tabblad **Start** de optie **Publiceren**.
1. Selecteer **Opslaan** om uw wijzigingen op te slaan.
1. Kies de bestandsnaam **PassengerAnalysis.pbix** en selecteer **Opslaan**.
1. Kies bij **Een doel selecteren** de optie **NYCTaxiWorkspace1** en klik vervolgens op **Selecteren**.
1. Wacht tot het publiceren is voltooid.

### <a name="configure-authentication-for-your-dataset"></a>Verificatie configureren voor uw gegevensset

1. Open [powerbi.microsoft.com](https://powerbi.microsoft.com/) en kies **Aanmelden**.
1. Selecteer aan de linkerkant onder **Werkruimten** de werkruimte **NYCTaxiWorkspace1**.
1. Zoek in die werkruimte een gegevensset met de naam **Passenger Analysis** en een rapport met de naam **Passenger Analysis**.
1. Beweeg de muisaanwijzer over de gegevensset **PassengerAnalysis**, selecteer de knop met het weglatingsteken (...) en selecteer vervolgens **Instellingen**.
1. Stel in **Referenties voor de gegevensbron** de **Verificatiemethode** in op **OAuth2** en selecteer **Aanmelden**.

### <a name="edit-a-report-in-synapse-studio"></a>Een rapport bewerken in Synapse Studio

1. Ga terug naar Synapse Studio en selecteer **Sluiten en vernieuwen**.
1. Ga naar de hub **Ontwikkelen**.
1. Beweeg de muisaanwijzer over **Power BI** en selecteer het knooppunt voor **Power BI-rapporten** vernieuwen.
1. Onder **Power BI** zou het volgende moeten staan:
    1. Onder **NYCTaxiWorkspace1** > **Power BI-gegevenssets**, een nieuwe gegevensset met de naam **PassengerAnalysis**.
    1. Onder **NYCTaxiWorkspace1Power** > **Power BI-rapporten**, een nieuw rapport met de naam **PassengerAnalysis**.
1. Selecteer het rapport **PassengerAnalysis**. Het rapport wordt geopend en u kunt het rechtstreeks in Synapse Studio bewerken.

## <a name="monitor-activities"></a>Activiteiten controleren

1. Ga in Synapse Studio naar de hub **bewaken**.
1. Op deze locatie ziet u een geschiedenis van alle activiteiten die worden uitgevoerd in de werkruimte en welke nu actief zijn.
1. Bestudeer de **Pijplijnuitvoeringen**, **Apache Spark-toepassingen** en **SQL-aanvragen** om te zien wat u al hebt gedaan in de werkruimte.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure Synapse Analytics (voorbeeld van werkruimten)](overview-what-is.md).

