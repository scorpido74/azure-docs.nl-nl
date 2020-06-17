---
title: 'Zelfstudie: Aan de slag met Azure Synapse Analytics'
description: Stappen om snel inzicht te krijgen in de basisconcepten in Azure Synapse
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 05/19/2020
ms.openlocfilehash: 00f93086fec62c08c5241d868fc5104a1197cff3
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84605405"
---
# <a name="getting-started-with-azure-synapse-analytics"></a>Aan de slag met Azure Synapse Analytics

Dit document leidt u door alle basisstappen die nodig zijn om Azure Synapse Analytics in te stellen en te gebruiken.

## <a name="prepare-a-storage-account-for-use-with-a-synapse-workspace"></a>Een opslagaccount voorbereiden voor gebruik met een Synapse-werkruimte

* Open de [Azure-portal](https://portal.azure.com)
* Maak een nieuw opslagaccount met de volgende instellingen:

    |Tabblad|Instelling | Voorgestelde waarde | Beschrijving |
    |---|---|---|---|
    |Basisbeginselen|**Naam van opslagaccount**| U kunt een willekeurige naam opgeven.|In dit document heeft het de naam `contosolake`.|
    |Basisbeginselen|**Type account**|Moet worden ingesteld op `StorageV2`||
    |Basisbeginselen|**Locatie**|U kunt elke willekeurige locatie kiezen| Het wordt aangeraden dat uw Synapse-werkruimte en Azure Data Lake Storage (ADLS) Gen2-account zich in dezelfde regio bevinden.|
    |Geavanceerd|**Data Lake Storage Gen2**|`Enabled`| Azure Synapse werkt alleen met opslagaccounts waarvoor deze instelling is ingeschakeld.|

1. Nadat het opslagaccount is gemaakt, selecteert u in het linkernavigatievenster **Toegangsbeheer (IAM)** . Wijs vervolgens de volgende rollen toe of zorg ervoor dat deze al zijn toegewezen. 

    a. * Wijs uzelf de rol **Eigenaar** toe in het opslagaccount b. * Wijs uzelf de rol **Eigenaar van opslagblobgegevens** toe in het opslagaccount

1. Selecteer in het navigatievenster links de optie **Containers** en maak een container. U kunt een willekeurige naam opgeven. Accepteer het standaard **Openbaar toegangsniveau**. In dit document heeft de container de naam `users`. Selecteer **Maken**. 

In de volgende stap configureert u de Synapse-werkruimte voor het gebruik van dit opslagaccount als primair opslagaccount en de container voor het opslaan van werkruimtegegevens. In de werkruimte worden gegevens opgeslagen in Apache Spark-tabellen en Spark-toepassingslogboeken in dit account in een map met de naam `/synapse/workspacename`.

## <a name="create-a-synapse-workspace"></a>Een Synapse-werkruimte maken

* Open [Azure Portal](https://portal.azure.com) en zoek bovenin naar `Synapse`.
* Selecteer in de zoekresultaten onder **Services** de optie **Azure Synapse Analytics (voorbeeld van werkruimten)**
* Selecteer **+ Toevoegen** om een werkruimte te maken met behulp van deze instellingen

    |Tabblad|Instelling | Voorgestelde waarde | Beschrijving |
    |---|---|---|---|
    |Basisbeginselen|**Werkruimtenaam**|U kunt deze werkruimte elke naam geven die u wilt.| In dit document gebruiken we `myworkspace`|
    |Basisbeginselen|**Regio**|Laat de regio van het opslagaccount overeenkomen|

1. Selecteer onder **Data Lake Storage Gen 2 selecteren** het account en de container die u eerder hebt gemaakt.

1. Selecteer **Controleren + maken**. Selecteer **Maken**. Uw werkruimte is binnen een paar minuten klaar.

## <a name="verify-the-synapse-workspace-msi-has-access-to-the-storage-account"></a>Controleren of de MSI van de Synapse-werkruimte toegang heeft tot het opslagaccount

Dit is mogelijk al voor u gedaan. U moet het in elk geval controleren.

1. Open [Azure Portal](https://portal.azure.com) en open het primaire opslagaccount dat u hebt gekozen voor uw werkruimte.
1. Selecteer **Toegangsbeheer (IAM)** in de linkernavigatie. Wijs vervolgens de volgende rollen toe of zorg ervoor dat deze al zijn toegewezen. 
    a. Wijs de werkruimte-id toe aan de rol **Bijdrager voor opslagblobgegevens** op het opslagaccount. De werkruimte-id heeft dezelfde naam als de werkruimte. In dit document is de naam van de werkruimte `myworkspace`, dus de identiteit van de werkruimte is `myworkspaced`
1. Selecteer **Opslaan**.
    
## <a name="launch-synapse-studio"></a>Synapse Studio starten

Wanneer uw Synapse-werkruimte is gemaakt, kunt u Synapse Studio op twee manieren openen:
* Open uw Synapse-werkruimte in [Azure Portal](https://portal.azure.com) en selecteer bovenaan de sectie **Overzicht** de optie **Synapse Studio starten**
* Ga rechtstreeks naar https://web.azuresynapse.net en meld u aan bij uw werkruimte.

## <a name="create-a-sql-pool"></a>Een SQL-pool maken

1. Selecteer in Synapse Studio in de navigatie aan de linkerkant **Beheren > SQL-pools**
1. Selecteer **+ Nieuw** en voer deze instellingen in:

    |Instelling | Voorgestelde waarde | 
    |---|---|
    |**Naam van SQL-pool**| `SQLDB1`|
    |**Prestatieniveau**|`DW100C`|

1. Selecteer **Bekijken en maken** en selecteer vervolgens **Maken**.
1. Uw SQL-pool is binnen een paar minuten klaar. Wanneer uw SQL-pool is gemaakt, wordt deze gekoppeld aan een SQL-pooldatabase, ook wel **SQLDB1**genoemd.

Een SQL-pool verbruikt factureerbare resources zolang deze worden uitgevoerd. U kunt de pool later onderbreken om de kosten te verlagen.

## <a name="create-an-apache-spark-pool"></a>Een Apache Spark-pool maken

1. Selecteer in Synapse Studio aan de linkerkant **Beheren > Apache Spark-pools**
1. Selecteer **+ Nieuw** en voer deze instellingen in:

    |Instelling | Voorgestelde waarde | 
    |---|---|
    |**Naam van Apache Spark-pool**|`Spark1`
    |**Knooppuntgrootte**| `Small`|
    |**Aantal knooppunten**| Stel het minimum in op 3 en het maximum op 3|

1. Selecteer **Bekijken en maken** en selecteer vervolgens **Maken**.
1. Uw Apache Spark-pool is binnen een paar seconden gereed.

> [!NOTE]
> Ondanks de naam is een Apache Spark-pool niet hetzelfde als een SQL-pool. Dit zijn slechts enkele basismetagegevens die u gebruikt om de Synapse-werkruimte te vertellen hoe met Apache Spark moet worden gewerkt. 

Omdat ze uit metagegevens bestaan, kunnen Apache Spark-pools niet worden gestart of gestopt. 

Wanneer u een Apache Spark-activiteit uitvoert in Synapse, geeft u een Apache Spark-pool op die moet worden gebruikt. De pool informeert Synapse hoeveel Apache Spark-resources moeten worden gebruikt. U betaalt alleen voor de resources die worden gebruikt. Wanneer u het gebruik van de pool actief stopt, treedt er automatisch een time-out op in de resources en worden ze gerecycled.

> [!NOTE]
> Apache Spark-databases worden onafhankelijk van Apache Spark-pools gemaakt. Een werkruimte heeft altijd een Apache Spark-database met de naam **standaard** en u kunt extra Spark-databases maken.

## <a name="the-sql-on-demand-pool"></a>De SQL on-demand-pool

Elke werkruimte wordt geleverd met een vooraf gemaakte en niet-verwijderbare pool met de naam **SQL on-demand**. Met de SQL on-demand-pool kunt u met SQL werken zonder dat u een Synapse SQL-pool hoeft te maken of te beheren. In tegenstelling tot de andere soorten pools is facturering voor SQL on-demand gebaseerd op de hoeveelheid gegevens die zijn gescand om de query uit te voeren en niet op het aantal resources dat wordt gebruikt om de query uit te voeren.

* SQL on-demand heeft ook zijn eigen SQL on-demand-databases die onafhankelijk van elke SQL on-demand-pool bestaan.
* Momenteel heeft een werkruimte altijd precies één SQL on-demand-pool met de naam **SQL on-demand**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>De gegevens van het NYC-taxivoorbeeld laden in de SQLDB1-database

1. Selecteer in Synapse Studio in het bovenste blauwe menu het pictogram **?** .
1. Selecteer **Aan de slag > Aan de slag-hub**
1. Selecteer in de kaart **Query uitvoeren op voorbeeldgegevens** de SQL-pool met de naam `SQLDB1`
1. Selecteer **Querygegevens**. U ziet de melding Voorbeeldgegevens laden, die vervolgens verdwijnt.
1. U ziet een lichtblauwe meldingsbalk boven aan Synapse Studio, waarmee wordt aangegeven dat de gegevens in SQLDB1 worden geladen. Wacht totdat deze groen is en sluit het vervolgens.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>De NYC-taxigegevens in de SQL-pool verkennen

1. Ga in Synapse Studio naar de hub **Gegevens**
1. Ga naar **SQLDB1 > Tabellen**. U ziet dat er meerdere tabellen zijn geladen.
1. Klik met de rechtermuisknop op de tabel **dbo.Trip** en selecteer **Nieuw SQL-script > Eerste 100 rijen selecteren**
1. Er wordt een nieuw SQL-script gemaakt, dat automatisch wordt uitgevoerd.
1. U ziet dat bovenaan het SQL-script **Verbinding maken met** automatisch is ingesteld op de SQL-pool met de naam `SQLDB1`.
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

1. Deze query laat zien op welke manier de totale reisafstanden en de gemiddelde reisafstand betrekking hebben op het aantal reizigers
1. In het resultatenvenster van het SQL-script wijzigt u de **Weergave** in **Grafiek** om een visualisatie van de resultaten weer te geven als een lijndiagram

## <a name="load-the-nyc-taxi-sample-data-into-the-spark-nyctaxi-database"></a>De gegevens van het NYC-taxivoorbeeld laden in de Apache Spark-database nyctaxi

Er zijn gegevens beschikbaar in een tabel in `SQLDB1`. We laden deze nu in een Spark-database met de naam `nyctaxi`.

1. Ga in Synapse Studio naar de hub **Ontwikkelen**
1. Selecteer **+** en selecteer **Notebook**
1. Stel bovenaan de notebook de waarde **Koppelen aan** in op `Spark1`
1. Selecteer **Code toevoegen** om een notebookcodecel toe te voegen en plak de onderstaande tekst:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Ga naar de hub **Data**, klik met de rechtermuisknop op **Databases** en selecteer **Vernieuwen**.
1. U ziet nu deze databases:
    - SQLDB1 (SQL-pool)
    - nyctaxi (Apache Spark)
      
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>De gegevens over de NYC-taxi met behulp van Apache Spark en notebooks analyseren

1. Ga terug naar uw notebook
1. Maak een nieuwe codecel, voer de onderstaande tekst in en voer de cel uit om een voorbeeld te geven van de NYC-taxigegevens die we in de Spark-database `nyctaxi` hebben geladen.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Voer de volgende code uit om dezelfde analyse uit te voeren die we eerder met de SQL-pool `SQLDB1` hebben uitgevoerd. Met deze code worden ook de resultaten van de analyse opgeslagen in een tabel met de naam `nyctaxi.passengercountstats` en worden de resultaten gevisualiseerd.

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

1. Selecteer **Grafiek** in de celresultaten om de gevisualiseerde gegevens weer te geven
 
## <a name="customize-data-visualization-data-with-spark-and-notebooks"></a>Gegevens over gegevensvisualisatie aanpassen met Apache Spark en notebooks

Met notebooks kunt u bepalen hoe grafieken worden weergegeven. De volgende code toont een eenvoudig voorbeeld met behulp van de populaire bibliotheken `matplotlib` en `seaborn`. Er wordt hetzelfde soort lijndiagram weergegeven dat u ook zag toen u de SQL-query's eerder uitvoerde.

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

Eerdere hebben we gegevens gekopieerd vanuit een SQL-pooltabel `SQLDB1.dbo.Trip` naar een Apache Spark-tabel `nyctaxi.trip`. Vervolgens hebben we Apache Spark gebruikt om de gegevens te aggregeren in de Apache Spark-tabel `nyctaxi.passengercountstats`. We gaan nu de gegevens van `nyctaxi.passengercountstats` kopiëren naar een SQL-pooltabel met de naam `SQLDB1.dbo.PassengerCountStats`. 

Voer de volgende cel uit in uw notebook. Hiermee wordt de geaggregeerde Apache Spark-tabel weer gekopieerd naar de SQL-pooltabel.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>NYC-taxigegevens analyseren in Apache Spark-databases met behulp van SQL on-demand 

Tabellen in Apache Spark-databases zijn automatisch zichtbaar en kunnen worden doorzocht door SQL on-demand.

1. Ga in Synapse Studio naar de hub **Ontwikkelen** en maak een nieuw SQL-script
1. Stel **Verbinding maken met** in op **SQL on-demand** 
1. Plak de volgende tekst in het script en voer het script uit.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```
    > [!NOTE]
    > De eerste keer dat u een query uitvoert die SQL on-demand gebruikt, duurt het ongeveer tien seconden tot SQL on-demand de SQL-resources heeft verzameld die nodig zijn om uw query's uit te voeren. Voor volgende query's is niet zoveel tijd nodig en die zijn dan ook veel sneller.
  
## <a name="orchestrate-activities-with-pipelines"></a>Activiteiten organiseren met pijplijnen

U kunt een groot aantal verschillende taken in Azure Synapse organiseren. In deze sectie ziet u hoe eenvoudig dat is.

1. Ga in Synapse Studio naar de hub **Organiseren**.
1. Selecteer **+** en selecteer **Pijplijn**. Er wordt een nieuwe pijplijn gemaakt.
1. Ga naar de hub Ontwikkelen en zoek de notebook op die u eerder hebt gemaakt.
1. Sleep de notebook naar de pijplijn.
1. Selecteer in de pijplijn **Trigger toevoegen > Nieuw/bewerken**.
1. Selecteer in **Trigger kiezen** **Nieuw** en stel vervolgens in terugkeerpatroon de trigger in om elk uur te worden uitgevoerd.
1. Selecteer **OK**.
1. Selecteer **Alles publiceren** en de pijplijn wordt elk uur uitgevoerd.
1. Als u de pijplijn nu wilt uitvoeren zonder te wachten op het volgende uur, selecteert u **Trigger toevoegen > Nieuw/bewerken**.

## <a name="working-with-data-in-a-storage-account"></a>Werken met gegevens in een opslagaccount

Tot nu toe zijn scenario's beschreven waarin gegevens in databases staan die zijn opgenomen in de werkruimte. U leert nu werken met bestanden in opslagaccounts. In dit scenario wordt het primaire opslagaccount van de werkruimte en de container die we hebben opgegeven bij het maken van de werkruimte gebruikt.

* De naam van het opslagaccount: `contosolake`
* De naam van de container in het opslagaccount: `users`

### <a name="creating-csv-and-parquet-files-in-your-storage-account"></a>CSV- en Parquet-bestanden maken in uw opslagaccount

Voer de volgende code uit in een notebook. Er wordt een CSV-bestand en een Parquet-bestand gemaakt in het opslagaccount

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyzing-data-in-a-storage-account"></a>Gegevens analyseren in een opslagaccount

1. Ga in Synapse Studio naar de hub **Gegevens**
1. Selecteer **Gekoppeld**
1. Navigeer naar **Opslagaccounts > myworkspace (Primary - contosolake)**
1. Selecteer **gebruikers (Primair)**
1. U ziet een map met de naam `NYCTaxi`. In deze map ziet u de twee mappen `PassengerCountStats.csv` en `PassengerCountStats.parquet`.
1. Ga naar de map `PassengerCountStats.parquet`.
1. Klik met de rechtermuisknop op het `.parquet`-bestand en selecteer **Nieuwe notebook**. Er wordt nu een notebook gemaakt met een cel die er als volgt uitziet:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Voer de cel uit.
1. Klik met de rechtermuisknop op het parquet-bestand en selecteer **Nieuw SQL-script > Eerste 100 rijen selecteren**. Er wordt een SQL-script als dit gemaakt:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```
    
1. In het script wordt het veld **Koppelen aan** ingesteld op **SQL on-demand**.
1. Voer het script uit.

## <a name="visualize-data-with-power-bi"></a>Gegevens visualiseren met Power BI

Aan de hand van de NYC-taxigegevens hebben we geaggregeerde gegevenssets gemaakt in twee tabellen:
* `nyctaxi.passengercountstats`
* `SQLDB1.dbo.PassengerCountStats`

U kunt een Power BI-werkruimte koppelen aan uw Synapse-werkruimte. Zo kunt u eenvoudig gegevens in uw Power BI-werkruimte krijgen en kunt u uw Power BI-rapporten rechtstreeks in uw Synapse-werkruimte bewerken.

### <a name="create-a-power-bi-workspace"></a>Een Power BI-werkruimte maken

1. Meld u aan bij [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Maak een nieuwe Power BI-werkruimte met de naam `NYCTaxiWorkspace1`.

### <a name="link-your-synapse-workspace-to-your-new-power-bi-workspace"></a>Uw Synapse-werkruimte koppelen aan uw nieuwe Power BI-werkruimte

1. Ga in Synapse Studio naar **Beheren > Gekoppelde services**.
1. Selecteer **+ Nieuw** en selecteer **Verbinding maken met Power BI** en stel deze velden in:

    |Instelling | Voorgestelde waarde | 
    |---|---|
    |**Naam**|`NYCTaxiWorkspace1`|
    |**Werkruimtenaam**|`NYCTaxiWorkspace1`|
        
1. Selecteer **Maken**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-synapse-workspace"></a>Een Power BI-gegevensset maken die gebruikmaakt van gegevens in uw Synapse-werkruimte

1. Ga in Synapse Studio naar **Ontwikkelen > Power BI**.
1. Ga naar **NYCTaxiWorkspace1 > Power BI-gegevenssets** en selecteer **Nieuwe Power BI-gegevensset**.
1. Beweeg de muisaanwijzer over de database `SQLDB1` en selecteer **.pbids-bestand downloaden**.
1. Open het gedownloade bestand `.pbids`. 
1. Hiermee wordt Power BI-bureaublad gestart en wordt het automatisch verbonden met `SQLDB1` in uw Synapse-werkruimte.
1. Als er een dialoogvenster wordt weergegeven met de naam **SQL Server-database**: a. Selecteer **Microsoft-account**. 
    b. Selecteer **Aanmelden** en meld u aan.
    c. Selecteer **Verbinden**.
1. Het dialoogvenster **Navigator** wordt geopend. Als dat is gebeurd, controleert u de tabel **PassengerCountStats** en selecteert u **Laden**.
1. Het dialoogvenster **Verbindingsinstellingen** wordt weergegeven. Selecteer **DirectQuery** en selecteer **OK**
1. Selecteer de knop **Rapport** aan de linkerkant.
1. Voeg **Lijndiagram** toe aan uw rapport.
    a. Sleep de kolom **PasssengerCount** naar **Visualisaties > As** b. Sleep de kolommen **SumTripDistance** en **AvgTripDistance** naar **Visualisaties > Waarden**.
1. Selecteer op het tabblad **Start** de optie **Publiceren**.
1. U wordt gevraagd of u uw wijzigingen wilt opslaan. Selecteer **Opslaan**.
1. U wordt gevraagd een bestandsnaam te kiezen. Kies `PassengerAnalysis.pbix` en selecteer **Opslaan**.
1. U moet**Een doel selecteren** en vervolgens selecteert u `NYCTaxiWorkspace1` en **Selecteren**.
1. Wacht tot het publiceren is voltooid.

### <a name="configure-authentication-for-your-dataset"></a>Verificatie configureren voor uw gegevensset

1. Open [powerbi.microsoft.com](https://powerbi.microsoft.com/) en kies **Aanmelden**
1. Selecteer aan de linkerkant onder **Werkruimten** de werkruimte `NYCTaxiWorkspace1`.
1. In die werkruimte ziet u een gegevensset met de naam `Passenger Analysis` en een rapport met de naam `Passenger Analysis`.
1. Beweeg de muisaanwijzer over de gegevensset `PassengerAnalysis`, selecteer het pictogram met de drie puntjes en selecteer **Instellingen**.
1. Stel in **Referenties voor de gegevensbron** de **Verificatiemethode** in op **OAuth2** en selecteer **Aanmelden**.

### <a name="edit-a-report-in-synapse-studio"></a>Een rapport bewerken in Synapse Studio

1. Ga terug naar Synapse Studio en selecteer **Sluiten en vernieuwen** 
1. Ga naar de hub **Ontwikkelen** 
1. Beweeg de muisaanwijzer over **Power BI** en klik op het knooppunt **Power BI-rapporten** om te vernieuwen.
1. Onder **Power BI** ziet u nu: a. * Onder **NYCTaxiWorkspace1 > Power BI-gegevenssets**, een nieuwe gegevensset met de naam **PassengerAnalysis**.
    b. * Onder **NYCTaxiWorkspace1 > Power BI-rapporten**, een nieuw rapport met de naam **PassengerAnalysis**.
1. Selecteer het rapport **PassengerAnalysis**. 
1. Het rapport wordt geopend en u kunt het rapport nu rechtstreeks bewerken in Synapse Studio.

## <a name="monitor-activities"></a>Activiteiten controleren

1. Ga in Synapse Studio naar de monitorhub.
1. Op deze locatie ziet u een geschiedenis van alle activiteiten die worden uitgevoerd in de werkruimte en welke nu actief zijn.
1. Bestudeer de **Pijplijnuitvoeringen**, **Apache Spark-toepassingen**en **SQL-aanvragen** en u kunt zien wat u al hebt gedaan in de werkruimte.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure Synapse Analytics (preview)](overview-what-is.md)

