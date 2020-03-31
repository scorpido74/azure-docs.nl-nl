---
title: Schaalbare data science met Azure Data Lake - Team Data Science Process
description: Azure Data Lake gebruiken om gegevensverkennings- en binaire classificatietaken op een gegevensset uit te voeren.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9409f14b20684afa1a39d45e663ff316f405cc97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717923"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Schaalbare gegevenswetenschap met Azure Data Lake: een end-to-end walkthrough
Deze walkthrough laat zien hoe u Azure Data Lake gebruiken om gegevensverkenning seinen en binaire classificatietaken uit te voeren op een voorbeeld van de NYC-taxirit en tariefgegevensset om te voorspellen of een fooi al dan niet wordt betaald door een tarief. Het leidt u door de stappen van het [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), end-to-end, van gegevensverwerving tot modeltraining en vervolgens naar de implementatie van een webservice die het model publiceert.

## <a name="technologies"></a>Technologieën

Deze technologieën worden gebruikt in deze walkthrough.
* Azure Data Lake Analytics
* U-SQL en Visual Studio
* Python
* Azure Machine Learning
* Scripts


### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
Het [Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) beschikt over alle mogelijkheden die nodig zijn om het voor gegevenswetenschappers gemakkelijk te maken om gegevens van elke grootte, vorm en snelheid op te slaan en gegevensverwerking, geavanceerde analyses en machine learning-modellering met hoge schaalbaarheid op een kosteneffectieve manier uit te voeren.   U betaalt per functie, alleen wanneer gegevens daadwerkelijk worden verwerkt. Azure Data Lake Analytics bevat U-SQL, een taal die de declaratieve aard van SQL combineert met de expressieve kracht van C# om schaalbare gedistribueerde querymogelijkheden te bieden. Hiermee u ongestructureerde gegevens verwerken door schema's toe te passen op lezen, aangepaste logica en door de gebruiker gedefinieerde functies (UDF's) toe te voegen en u uitbreidbaarheid opnemen om fijnkorrelige controle over hoe op schaal uit te voeren mogelijk te maken. Zie [Visual Studio-blogpost](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)voor meer informatie over de ontwerpfilosofie achter U-SQL.

Data Lake Analytics is ook een belangrijk onderdeel van Cortana Analytics Suite en werkt met Azure SQL Data Warehouse, Power BI en Data Factory. Deze combinatie biedt u een compleet cloud big data en geavanceerd analytics platform.

Deze walkthrough begint met een beschrijving van hoe u de vereisten en resources installeren die nodig zijn om gegevenswetenschapsprocestaken te voltooien. Vervolgens worden de stappen voor gegevensverwerking beschreven met U-SQL en wordt afgesloten door te laten zien hoe python en hive met Azure Machine Learning Studio (klassiek) kunnen worden gebruikt om de voorspellende modellen te bouwen en te implementeren.

### <a name="u-sql-and-visual-studio"></a>U-SQL en Visual Studio
Deze walkthrough raadt aan om Visual Studio te gebruiken om U-SQL-scripts te bewerken om de gegevensset te verwerken. De U-SQL scripts worden hier beschreven en geleverd in een apart bestand. Het proces omvat het innemen, verkennen en samplen van de gegevens. Het toont ook hoe u een U-SQL-scripted-taak uitvoert vanuit de Azure-portal. Hive-tabellen worden gemaakt voor de gegevens in een bijbehorend HDInsight-cluster om het bouwen en implementeren van een binair classificatiemodel in Azure Machine Learning Studio te vergemakkelijken.

### <a name="python"></a>Python
Deze walkthrough bevat ook een sectie die laat zien hoe je een voorspellend model bouwen en implementeren met Python met Azure Machine Learning Studio. Het biedt een Jupyter notebook met de Python scripts voor de stappen in dit proces. Het notitieblok bevat code voor een aantal extra functie engineering stappen en modellen constructie, zoals multiclass classificatie en regressie modellering in aanvulling op de binaire classificatie model hier beschreven. De regressietaak is het voorspellen van de hoeveelheid tip op basis van andere tipfuncties.

### <a name="azure-machine-learning"></a>Azure Machine Learning 
Azure Machine Learning Studio (klassiek) wordt gebruikt voor het bouwen en implementeren van de voorspellende modellen met behulp van twee benaderingen: eerst met Python-scripts en vervolgens met Hive-tabellen op een HDInsight -cluster (Hadoop).

### <a name="scripts"></a>Scripts
Alleen de belangrijkste stappen worden beschreven in deze walkthrough. U het volledige **U-SQL-script** en **Jupyter-notitieblok** downloaden van [GitHub.](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough)

## <a name="prerequisites"></a>Vereisten
Voordat u met deze onderwerpen begint, moet u het volgende hebben:

* Een Azure-abonnement. Zie [Gratis proefversie van Azure,](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)als u er nog geen hebt.
* [Aanbevolen] Visual Studio 2013 of hoger. Als u nog niet een van deze versies hebt geïnstalleerd, u een gratis communityversie downloaden van [Visual Studio Community.](https://www.visualstudio.com/vs/community/)

> [!NOTE]
> In plaats van Visual Studio u de Azure-portal ook gebruiken om Azure Data Lake-query's in te dienen. Instructies worden gegeven over hoe dit te doen, zowel met Visual Studio en op de portal in de sectie getiteld **Procesgegevens met U-SQL**.
>
>


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Data science-omgeving voorbereiden voor Azure Data Lake
Maak de volgende bronnen om de data science-omgeving voor te bereiden op deze walkthrough:

* Azure Data Lake Storage (ADLS)
* Azure Data Lake Analytics (ADLA)
* Azure Blob-opslagaccount
* Azure Machine Learning Studio (klassiek) account
* Azure Data Lake-hulpprogramma's voor Visual Studio (aanbevolen)

In deze sectie vindt u instructies over het maken van elk van deze bronnen. Als u ervoor kiest om Hive-tabellen te gebruiken met Azure Machine Learning, in plaats van Python, om een model te bouwen, moet u ook een HDInsight -cluster (Hadoop) inrichten. Deze alternatieve procedure beschreven in de optie 2 sectie.


> [!NOTE]
> De **Azure Data Lake Store** kan afzonderlijk worden gemaakt of wanneer u de Azure Data Lake **Analytics** maakt als standaardopslag. Er wordt verwezen naar instructies voor het afzonderlijk maken van elk van deze bronnen, maar het Data Lake-opslagaccount hoeft niet afzonderlijk te worden gemaakt.
>
>

### <a name="create-an-azure-data-lake-storage"></a>Een Azure Data Lake-opslag maken


Maak een ADLS vanuit de [Azure-portal.](https://portal.azure.com) Zie Een [HDInsight-cluster maken met Data Lake Store met Azure-portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)voor meer informatie. Zorg ervoor dat u de cluster-AAD-identiteit instelt in het **DataSource-blad** van het daar beschreven **optionele configuratieblad.**

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Een Azure Data Lake Analytics-account maken
Maak een ADLA-account vanuit de [Azure-portal.](https://portal.azure.com) Zie [Zelfstudie: aan de slag met Azure Data Lake Analytics met Azure Portal](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Een Azure Blob-opslagaccount maken
Maak een Azure Blob-opslagaccount vanuit de [Azure-portal](https://portal.azure.com). Zie de sectie Een opslagaccount maken in [Over Azure Storage-accounts](../../storage/common/storage-create-storage-account.md)voor meer informatie.

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-classic-account"></a>Een Azure Machine Learning Studio-account (klassiek) instellen
Meld u aan/t u aanmeldt voor Azure Machine Learning Studio (klassiek) vanaf de studiopagina van [Azure Machine Learning.](https://azure.microsoft.com/services/machine-learning/) Klik op de knop **Nu aan de slag** en kies vervolgens een 'Vrije werkruimte' of 'Standaardwerkruimte'. Nu bent u klaar om experimenten te maken in Azure Machine Learning studio.

### <a name="install-azure-data-lake-tools-recommended"></a>Azure Data Lake-hulpprogramma's installeren [Aanbevolen]
Installeer Azure Data Lake Tools voor uw versie van Visual Studio vanuit [Azure Data Lake Tools voor Visual Studio.](https://www.microsoft.com/download/details.aspx?id=49504)

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Nadat de installatie is voltooid, opent u Visual Studio. U ziet het tabblad Data Lake bovenaan het menu. Uw Azure-bronnen moeten in het linkerdeelvenster worden weergegeven wanneer u zich aanmeldt bij uw Azure-account.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>De NYC Taxi Trips dataset
De hier gebruikte gegevensset is een openbaar beschikbare gegevensset - de [NYC Taxi Trips-gegevensset](https://www.andresmh.com/nyctaxitrips/). De NYC Taxi Trip gegevens bestaat uit ongeveer 20 GB van gecomprimeerde CSV-bestanden (~ 48 GB ongecomprimeerd), het opnemen van meer dan 173 miljoen individuele reizen en de tarieven betaald voor elke reis. Elke reis record bevat de pick-up en dropoff locaties en tijden, geanonimiseerde hack (driver's) licentienummer, en het medaillon (unieke ID taxi's) nummer. De gegevens hebben betrekking op alle reizen in het jaar 2013 en worden verstrekt in de volgende twee gegevenssets voor elke maand:

De CSV 'trip_data' bevat reisgegevens, zoals het aantal passagiers, ophaal- en inleverpunten, reisduur en reislengte. Hier zijn een paar voorbeeldrecords:

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



De 'trip_fare' CSV bevat details over het betaalde tarief voor elke reis, zoals het betalingstype, het tariefbedrag, toeslagen en belastingen, tips en tolgelden en het totale betaalde bedrag. Hier zijn een paar voorbeeldrecords:

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

De unieke sleutel\_om deel\_te nemen aan reisgegevens en reistarief\_bestaat\_uit de volgende drie velden: medaillon, hack licentie en pick-up datetime. De raw CSV-bestanden zijn toegankelijk vanaf een Azure Storage-blob. Het U-SQL-script voor deze join bevindt zich in de sectie [Deelnemen aan reis- en tarieftabellen.](#join)

## <a name="process-data-with-u-sql"></a>Gegevens verwerken met U-SQL
De gegevensverwerkingstaken die in deze sectie worden geïllustreerd, omvatten het innemen, controleren van de kwaliteit, het verkennen en het bemonsteren van de gegevens. Hoe u deelneemt aan reis- en tarieftabellen wordt ook weergegeven. In het laatste gedeelte wordt een U-SQL-scripttaken uitgevoerd vanuit de Azure-portal. Hier zijn links naar elke onderafdeling:

* [Gegevensopname: lees in gegevens van openbare blob](#ingest)
* [Kwaliteitscontroles van gegevens](#quality)
* [Gegevensverkenning](#explore)
* [Deelnemen aan reis- en tarieftabellen](#join)
* [Gegevensbemonstering](#sample)
* [U-SQL-taken uitvoeren](#run)

De U-SQL scripts worden hier beschreven en geleverd in een apart bestand. U de volledige **U-SQL-scripts** downloaden van [GitHub.](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough)

Als u U-SQL wilt uitvoeren, Visual Studio openen, klikt u op **Bestand --> Nieuw --> Project,** kiest **u-SQL-project**, naam en slaat u deze op in een map.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> Het is mogelijk om de Azure Portal te gebruiken om U-SQL uit te voeren in plaats van Visual Studio. U naar de Azure Data Lake Analytics-bron op de portal navigeren en query's rechtstreeks indienen zoals geïllustreerd in de volgende afbeelding:
>
>

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="data-ingestion-read-in-data-from-public-blob"></a><a name="ingest"></a>Gegevensopname: lees in gegevens van openbare blob

De locatie van de gegevens in de Azure-blob wordt aangeduid als **\_wasb://container naam\@blob\_opslagaccount\_\_name.blob.core.windows.net/blob_name** en kan worden geëxtraheerd met **Extractors.Csv()**. Vervang uw eigen containernaam en opslagaccountnaam\_\@in\_\_volgende\_scripts voor de naam van het blobaccount voor containernamen in het wasb-adres. Aangezien de bestandsnamen in dezelfde indeling zijn, is het mogelijk om **\_reisgegevens\_\{\*\}.csv** te gebruiken om in alle 12 reisbestanden te lezen.

    ///Read in Trip data
    @trip0 =
        EXTRACT
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

Aangezien er kopteksten in de eerste rij zijn, moet u de kopteksten verwijderen en kolomtypen in geschikte typen wijzigen. U de verwerkte gegevens opslaan in Azure Data Lake Storage met **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ of naar Azure Blob-opslagaccount met **wasb://container_name\@blob_storage_account_name.blob.core.windows.net/blob_name**.

    // change data types
    @trip =
        SELECT
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        DateTime.Parse(pickup_datetime) AS pickup_datetime,
        DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
        Int32.Parse(passenger_count) AS passenger_count,
        Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
        Double.Parse(trip_distance) AS trip_distance,
        (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
        (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
        (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
        (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

    ////output data to ADL
    OUTPUT @trip
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
    USING Outputters.Csv();

    ////Output data to blob
    OUTPUT @trip
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
    USING Outputters.Csv();

Op dezelfde manier u lezen in de tariefgegevenssets. Klik met de rechtermuisknop op Azure Data Lake Storage en u ervoor kiezen om uw gegevens in **Azure-portal te bekijken --> Data Explorer** of **Verkenner** in Visual Studio.

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="data-quality-checks"></a><a name="quality"></a>Kwaliteitscontroles van gegevens
Nadat reis- en tarieftabellen zijn ingelezen, kunnen de kwaliteitscontroles van gegevens op de volgende manier worden uitgevoerd. De resulterende CSV-bestanden kunnen worden uitgevoerd naar Azure Blob-opslag of Azure Data Lake Storage.

Vind het aantal medaillons en het unieke aantal medaillons:

    ///check the number of medallions and unique number of medallions
    @trip2 =
        SELECT
        medallion,
        vendor_id,
        pickup_datetime.Month AS pickup_month
        FROM @trip;

    @ex_1 =
        SELECT
        pickup_month,
        COUNT(medallion) AS cnt_medallion,
        COUNT(DISTINCT(medallion)) AS unique_medallion
        FROM @trip2
        GROUP BY pickup_month;
        OUTPUT @ex_1
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
    USING Outputters.Csv();

Vind die medaillons die meer dan 100 reizen hadden:

    ///find those medallions that had more than 100 trips
    @ex_2 =
        SELECT medallion,
               COUNT(medallion) AS cnt_medallion
        FROM @trip2
        //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
        GROUP BY medallion
        HAVING COUNT(medallion) > 100;
        OUTPUT @ex_2
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
    USING Outputters.Csv();

Zoek deze ongeldige records in termen van pickup_longitude:

    ///find those invalid records in terms of pickup_longitude
    @ex_3 =
        SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
        FROM @trip
        WHERE
        pickup_longitude <- 90 OR pickup_longitude > 90;
        OUTPUT @ex_3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
    USING Outputters.Csv();

Zoek ontbrekende waarden voor sommige variabelen:

    //check missing values
    @res =
        SELECT *,
               (medallion == null? 1 : 0) AS missing_medallion
        FROM @trip;

    @trip_summary6 =
        SELECT
            vendor_id,
        SUM(missing_medallion) AS medallion_empty,
        COUNT(medallion) AS medallion_total,
        COUNT(DISTINCT(medallion)) AS medallion_total_unique
        FROM @res
        GROUP BY vendor_id;
    OUTPUT @trip_summary6
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
    USING Outputters.Csv();



### <a name="data-exploration"></a><a name="explore"></a>Gegevensverkenning
Doe wat gegevensverkenning met de volgende scripts om een beter begrip van de gegevens te krijgen.

Zoek de verdeling van getipte en niet-getipte reizen:

    ///tipped vs. not tipped distribution
    @tip_or_not =
        SELECT *,
               (tip_amount > 0 ? 1: 0) AS tipped
        FROM @fare;

    @ex_4 =
        SELECT tipped,
               COUNT(*) AS tip_freq
        FROM @tip_or_not
        GROUP BY tipped;
        OUTPUT @ex_4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
    USING Outputters.Csv();

Vind de verdeling van het tipbedrag met cut-off waarden: 0, 5, 10 en 20 dollar.

    //tip class/range distribution
    @tip_class =
        SELECT *,
               (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
        FROM @fare;
    @ex_5 =
        SELECT tip_class,
               COUNT(*) AS tip_freq
        FROM @tip_class
        GROUP BY tip_class;
        OUTPUT @ex_5
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
    USING Outputters.Csv();

Vind basisstatistieken van de reisafstand:

    // find basic statistics for trip_distance
    @trip_summary4 =
        SELECT
            vendor_id,
            COUNT(*) AS cnt_row,
            MIN(trip_distance) AS min_trip_distance,
            MAX(trip_distance) AS max_trip_distance,
            AVG(trip_distance) AS avg_trip_distance
        FROM @trip
        GROUP BY vendor_id;
    OUTPUT @trip_summary4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
    USING Outputters.Csv();

Zoek de percentiels van de reisafstand:

    // find percentiles of trip_distance
    @trip_summary3 =
        SELECT DISTINCT vendor_id AS vendor,
                        PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
        FROM @trip;
       // group by vendor_id;
    OUTPUT @trip_summary3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
    USING Outputters.Csv();


### <a name="join-trip-and-fare-tables"></a><a name="join"></a>Deelnemen aan reis- en tarieftabellen
Reis- en tarieftafels kunnen worden samengevoegd door medaillon, hack_license en pickup_time.

    //join trip and fare table

    @model_data_full =
    SELECT t.*,
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

    //// output to blob
    OUTPUT @model_data_full
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
    USING Outputters.Csv();

    ////output data to ADL
    OUTPUT @model_data_full
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
    USING Outputters.Csv();


Bereken voor elk niveau van het aantal passagiers het aantal records, het gemiddelde tipbedrag, de variantie van het tipbedrag, het percentage fooien.

    // contingency table
    @trip_summary8 =
        SELECT passenger_count,
               COUNT(*) AS cnt,
               AVG(tip_amount) AS avg_tip_amount,
               VAR(tip_amount) AS var_tip_amount,
               SUM(tipped) AS cnt_tipped,
               (float)SUM(tipped)/COUNT(*) AS pct_tipped
        FROM @model_data_full
        GROUP BY passenger_count;
        OUTPUT @trip_summary8
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
    USING Outputters.Csv();


### <a name="data-sampling"></a><a name="sample"></a>Gegevensbemonstering
Selecteer eerst willekeurig 0,1% van de gegevens uit de samengevoegde tabel:

    //random select 1/1000 data for modeling purpose
    @addrownumberres_randomsample =
    SELECT *,
            ROW_NUMBER() OVER() AS rownum
    FROM @model_data_full;

    @model_data_random_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_randomsample
    WHERE rownum % 1000 == 0;

    OUTPUT @model_data_random_sample_1_1000
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
    USING Outputters.Csv();

Dan doen gestratificeerde bemonstering door binaire variabele tip_class:

    //stratified random select 1/1000 data for modeling purpose
    @addrownumberres_stratifiedsample =
    SELECT *,
            ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
    FROM @model_data_full;

    @model_data_stratified_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_stratifiedsample
    WHERE rownum % 1000 == 0;
    //// output to blob
    OUTPUT @model_data_stratified_sample_1_1000
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv();
    ////output data to ADL
    OUTPUT @model_data_stratified_sample_1_1000
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv();


### <a name="run-u-sql-jobs"></a><a name="run"></a>U-SQL-taken uitvoeren
Nadat u U-SQL-scripts hebt bewerkt, u deze naar de server verzenden met uw Azure Data Lake Analytics-account. Klik op **Data Lake,** **Job verzenden,** selecteer uw **Analytics-account,** kies **Parallelisme**en klik op **Verzenden.**

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Wanneer de taak is uitgevoerd, wordt de status van uw taak weergegeven in Visual Studio voor bewaking. Nadat de taak is voltooid, u zelfs het taakuitvoeringsproces opnieuw afspelen en de knelpuntstappen vinden om uw taakefficiëntie te verbeteren. U ook naar Azure-portal gaan om de status van uw U-SQL-taken te controleren.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Nu u de uitvoerbestanden controleren in Azure Blob-opslag of Azure-portal. Gebruik de gestratificeerde voorbeeldgegevens voor onze modellering in de volgende stap.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Modellen bouwen en implementeren in Azure Machine Learning
Er zijn twee opties beschikbaar om gegevens in Azure Machine Learning te verzamelen om

* In de eerste optie gebruikt u de gesamplede gegevens die naar een Azure Blob zijn geschreven (in de bovenstaande stap **Gegevensbemonstering)** en gebruikt u Python om modellen te bouwen en te implementeren vanuit Azure Machine Learning.
* In de tweede optie bevraagt u de gegevens in Azure Data Lake rechtstreeks met behulp van een Hive-query. Deze optie vereist dat u een nieuw HDInsight-cluster maakt of een bestaand HDInsight-cluster gebruikt waarbij de Hive-tabellen wijzen op de NY Taxi-gegevens in Azure Data Lake Storage.  Beide opties worden in de volgende secties besproken.

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Optie 1: Python gebruiken om machine learning-modellen te bouwen en te implementeren
Als u machine learning-modellen wilt bouwen en implementeren met Python, maakt u een Jupyter-notitieblok op uw lokale machine of in Azure Machine Learning Studio. De Jupyter Notebook op [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) bevat de volledige code om gegevens, functieengineering, modellering en implementatie te verkennen, te visualiseren. In dit artikel worden alleen de modellering en implementatie behandeld.

### <a name="import-python-libraries"></a>Python-bibliotheken importeren
Om het voorbeeld Jupyter Notebook of het Python-scriptbestand uit te voeren, zijn de volgende Python-pakketten nodig. Als u de Azure Machine Learning Notebook-service gebruikt, zijn deze pakketten vooraf geïnstalleerd.

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random
    import sklearn
    from sklearn.linear_model import LogisticRegression
    from sklearn.cross_validation import train_test_split
    from sklearn import metrics
    from __future__ import division
    from sklearn import linear_model
    from azureml import services


### <a name="read-in-the-data-from-blob"></a>Lees in de gegevens van blob
* Verbindingsreeks

        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* Inlezen als tekst

        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)
* Kolomnamen en afzonderlijke kolommen toevoegen

        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* Sommige kolommen wijzigen in numeriek

        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Machine learning-modellen bouwen
Hier bouwt u een binair classificatiemodel om te voorspellen of een reis wordt getipt of niet. In de Jupyter Notebook vindt u nog twee modellen: meerklassenclassificatie en regressiemodellen.

* Eerst moet je dummy variabelen die kunnen worden gebruikt in scikit-learn modellen te maken

        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* Gegevensframe maken voor de modellering

        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])

        X = data.iloc[:,1:]
        Y = data.tipped
* Training en testen 60-40 split

        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* Logistieke regressie in trainingsset

        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)

       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Gegevensset scoretesten

        Y_test_pred = logit_fit.predict(X_test)
* Evaluatiestatistieken berekenen

        fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
        print fpr_train, tpr_train, thresholds_train

        fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred)
        print fpr_test, tpr_test, thresholds_test

        #AUC
        print metrics.auc(fpr_train,tpr_train)
        print metrics.auc(fpr_test,tpr_test)

        #Confusion Matrix
        print metrics.confusion_matrix(Y_train,Y_train_pred)
        print metrics.confusion_matrix(Y_test,Y_test_pred)

       ![c2](./media/data-lake-walkthrough/c2-py-logit-evaluation.PNG)

### <a name="build-web-service-api-and-consume-it-in-python"></a>Web Service API bouwen en deze gebruiken in Python
U wilt het machine learning-model operationeel maken nadat het is gebouwd. Het binaire logistieke model wordt hier als voorbeeld gebruikt. Zorg ervoor dat de scikit-learn-versie in uw lokale machine 0.15.1 is (Azure Machine Learning Studio bevindt zich al op deze versie).

* Zoek uw werkruimtereferenties via de instellingen van Azure Machine Learning Studio (klassieke) instellingen. Klik in Azure Machine Learning Studio op **Instellingen** --> **naamautorisatietokens****Name** --> .

    ![c3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

* Webservice maken

        @services.publish(workspaceid, auth_token)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)
* Webservicereferenties ophalen

        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key

        print url
        print api_key

        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass
* Call Web service API. Wacht doorgaans 5-10 seconden na de vorige stap.

        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Optie 2: Modellen rechtstreeks maken en implementeren in Azure Machine Learning
Azure Machine Learning Studio (klassiek) kan gegevens rechtstreeks uit Azure Data Lake Storage lezen en vervolgens worden gebruikt om modellen te maken en te implementeren. Deze benadering maakt gebruik van een Hive-tabel die verwijst naar de Azure Data Lake Storage. Er moet een apart Azure HDInsight-cluster worden ingericht voor de Hive-tabel. 

### <a name="create-an-hdinsight-linux-cluster"></a>Een HDInsight Linux-cluster maken
Maak een HDInsight Cluster (Linux) vanuit de [Azure-portal.](https://portal.azure.com) Zie **het cluster Een HDInsight maken met toegang tot azure data lake-opslag** in Een [HDInsight-cluster maken met Data Lake Store met Azure-portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)voor meer informatie.

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Hive-tabel maken in HDInsight
Nu maakt u Hive-tabellen die in Azure Machine Learning Studio (klassiek) in het HDInsight-cluster kunnen worden gebruikt met behulp van de gegevens die in de vorige stap zijn opgeslagen in Azure Data Lake Storage. Ga naar het HDInsight-cluster dat is gemaakt. Klik op **Instellingen** --> **Cluster** --> **AAD Identity** --> **ADLS Access**, controleer of uw Azure Data Lake Storage-account in de lijst wordt toegevoegd met lees-, schrijf- en uitvoerrechten.

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Klik vervolgens op **Dashboard** naast de knop **Instellingen** en er verschijnt een venster. Klik **op Bijenkorfweergave** in de rechterbovenhoek van de pagina en u ziet de **queryeditor**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Plak in de volgende Hive-scripts om een tabel te maken. De locatie van de gegevensbron bevindt zich op deze manier in de referentie van Azure Data Lake Storage: **adl://data_lake_store_name.azuredatalakestore.net:443/folder_name/file_name**.

    CREATE EXTERNAL TABLE nyc_stratified_sample
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string,
      payment_type string,
      fare_amount string,
      surcharge string,
      mta_tax string,
      tolls_amount string,
      total_amount string,
      tip_amount string,
      tipped string,
      tip_class string,
      rownum string
      )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';


Wanneer de query is voltooid, ziet u de resultaten als volgt:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Modellen bouwen en implementeren in Azure Machine Learning Studio
U bent nu klaar om een model te bouwen en te implementeren dat voorspelt of een fooi wordt betaald met Azure Machine Learning. De gestratificeerde voorbeeldgegevens zijn klaar om te worden gebruikt in dit binaire classificatieprobleem (tip of niet). De voorspellende modellen met behulp van multiclass classificatie (tip_class) en regressie (tip_amount) kunnen ook worden gebouwd en geïmplementeerd met Azure Machine Learning Studio, maar hier wordt alleen getoond hoe de behuizing te behandelen met behulp van het binaire classificatiemodel.

1. Haal de gegevens in Azure Machine Learning Studio (klassiek) met behulp van de module **Gegevens importeren,** beschikbaar in de sectie **Gegevensinvoer en -uitvoer.** Zie de [referentiepagina van](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) de module Gegevens importeren voor meer informatie.
2. Selecteer **Bijenkorfquery** als **gegevensbron** in het deelvenster **Eigenschappen.**
3. Het volgende Hive-script plakken in de queryeditor van de **Hive-database**

        select * from nyc_stratified_sample;
4. Voer de URI van HDInsight-cluster in (deze URI is te vinden in Azure-portal), Hadoop-referenties, locatie van uitvoergegevens en de naam van het Azure Storage-account/de naam van de sleutel/container.

   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)

Een voorbeeld van een binaire classificatie-experiment waarin gegevens uit de tabel Hive worden gelezen, wordt weergegeven in de volgende afbeelding:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

Nadat het experiment is gemaakt, klikt u op **Webservice** --> **Voorspellende webservice** instellen

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Voer het automatisch gemaakte scoringsexperiment uit, klik op **Webservice implementeren** wanneer het is voltooid

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Het dashboard van de webservice wordt binnenkort weergegeven:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Samenvatting
Door deze walkthrough te voltooien, hebt u een data science-omgeving gemaakt voor het bouwen van schaalbare end-to-end oplossingen in Azure Data Lake. Deze omgeving werd gebruikt om een grote openbare gegevensset te analyseren, waarbij deze wordt genomen door de canonieke stappen van het Data Science-proces, van gegevensverwerving via modeltraining en vervolgens tot de implementatie van het model als webservice. U-SQL werd gebruikt voor het verwerken, verkennen en samplen van de gegevens. Python en Hive werden gebruikt met Azure Machine Learning Studio (klassiek) om voorspellende modellen te bouwen en te implementeren.

## <a name="whats-next"></a>Volgende stappen
Het leerpad voor het [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) biedt koppelingen naar onderwerpen die elke stap in het geavanceerde analyseproces beschrijven. Er zijn een reeks walkthroughs die zijn gespecificeerd op de pagina [Walkthroughs van](walkthroughs.md) het Team Data Science Process die laten zien hoe u resources en services gebruiken in verschillende scenario's voor voorspellende analyses:

* [Het Team Data Science-proces in actie: SQL Data Warehouse gebruiken](sqldw-walkthrough.md)
* [Het Team Data Science Process in actie: hdinsight Hadoop clusters gebruiken](hive-walkthrough.md)
* [Het Team Data Science-proces: SQL Server gebruiken](sql-walkthrough.md)
* [Overzicht van het Data Science-proces met Spark op Azure HDInsight](spark-overview.md)
