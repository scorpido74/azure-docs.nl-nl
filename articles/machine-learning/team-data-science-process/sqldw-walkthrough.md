---
title: Een model bouwen en implementeren met Azure Synapse Analytics - Team Data Science Process
description: Een machine learning-model bouwen en implementeren met Azure Synapse Analytics met een openbaar beschikbare gegevensset.
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
ms.openlocfilehash: 96d0a5b2fb59e4612107d8ccbf7285fff7576585
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128395"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>Het Team Data Science-proces in actie: Azure Synapse Analytics gebruiken
In deze zelfstudie nemen we je mee door het bouwen en implementeren van een machine learning-model met Azure Synapse Analytics voor een openbaar beschikbare gegevensset- de [NYC Taxi Trips-gegevensset.](https://www.andresmh.com/nyctaxitrips/) Het binaire classificatiemodel dat is geconstrueerd, voorspelt of een fooi wordt betaald voor een reis.  Modellen omvatten meerklassenclassificatie (al dan niet een tip) en regressie (de verdeling voor de betaalde tipbedragen).

De procedure volgt de [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) workflow. We laten zien hoe u een data science-omgeving instelt, hoe u de gegevens in Azure Synapse Analytics laadt en hoe u Azure Synapse Analytics of een IPython-notitieblok gebruiken om de gegevens en functies voor het ontwerpen te verkennen. Vervolgens laten we zien hoe je een model bouwen en implementeren met Azure Machine Learning.

## <a name="the-nyc-taxi-trips-dataset"></a><a name="dataset"></a>De NYC Taxi Trips dataset
De NYC Taxi Trip gegevens bestaat uit ongeveer 20 GB van gecomprimeerde CSV-bestanden (~ 48 GB ongecomprimeerd), het opnemen van meer dan 173 miljoen individuele reizen en de tarieven betaald voor elke reis. Elke reis record bevat de pick-up en dropoff locaties en tijden, geanonimiseerde hack (driver's) licentienummer, en het medaillon (unieke ID taxi's) nummer. De gegevens hebben betrekking op alle reizen in het jaar 2013 en worden verstrekt in de volgende twee gegevenssets voor elke maand:

1. Het **bestand trip_data.csv** bevat reisgegevens, zoals het aantal passagiers, ophaal- en inleverpunten, reisduur en reisduur. Hier zijn een paar voorbeeldrecords:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Het **bestand trip_fare.csv** bevat details over het tarief dat voor elke reis is betaald, zoals het betalingstype, het tariefbedrag, toeslagen en belastingen, tips en tolgelden en het totale betaalde bedrag. Hier zijn een paar voorbeeldrecords:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

De **unieke sleutel** die\_wordt\_gebruikt om deel te nemen aan reisgegevens en reistarieven bestaat uit de volgende drie velden:

* Medaillon
* hack\_licentie en
* ophaaldatum.\_

## <a name="address-three-types-of-prediction-tasks"></a><a name="mltasks"></a>Drie soorten voorspellingstaken aanpakken
We formuleren drie voorspellingsproblemen op basis van het *tipbedrag\_* om drie soorten modelleringstaken te illustreren:

1. **Binaire classificatie**: Om te voorspellen of een fooi is betaald voor een reis, dat wil zeggen, een *tip\_bedrag* dat groter is dan $0 is een positief voorbeeld, terwijl een tip *\_bedrag* van $0 is een negatief voorbeeld.
2. **Classificatie van meerdere klassen**: Om het bereik van de fooi te voorspellen die voor de reis is betaald. We verdelen het *tipbedrag\_* in vijf bakken of klassen:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Regressietaak:** Het bedrag van de fooi voor een reis voorspellen.

## <a name="set-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>De Azure-datascience-omgeving instellen voor geavanceerde analyses
Voer de volgende stappen uit om uw Azure Data Science-omgeving in te stellen.

**Uw eigen Azure Blob-opslagaccount maken**

* Wanneer u uw eigen Azure blob-opslag indient, kiest u een geolocatie voor uw Azure blob-opslag in of zo dicht mogelijk bij **South Central US**, waar de NYC-taxigegevens worden opgeslagen. De gegevens worden gekopieerd met AzCopy van de openbare blobopslagcontainer naar een container in uw eigen opslagaccount. Hoe dichter uw Azure blob-opslag zich bij South Central US bevindt, hoe sneller deze taak (stap 4) wordt voltooid.
* Als u uw eigen Azure Storage-account wilt maken, voert u de stappen uit die zijn beschreven op [Azure Storage-accounts.](../../storage/common/storage-create-storage-account.md) Zorg ervoor dat u notities maakt over de waarden voor het volgen van opslagaccountreferenties, omdat deze later in deze walkthrough nodig zullen zijn.

  * **Naam van opslagaccount**
  * **Opslagaccountsleutel**
  * **Containernaam** (waarvan u wilt dat de gegevens worden opgeslagen in de Azure blob-opslag)

**Informeer uw Azure Synapse Analytics-exemplaar in.**
Volg de documentatie bij [Maken en query een Azure SQL Data Warehouse in de Azure portal](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) om een Azure Synapse Analytics-exemplaar te voorzien. Zorg ervoor dat u aantekeningen maakt op de volgende Azure Synapse Analytics-referenties die in latere stappen worden gebruikt.

* **Servernaam:** \<servernaam>.database.windows.net
* **SQLDW (Database) Naam**
* **Gebruikersnaam**
* **Wachtwoord**

**Installeer Visual Studio en SQL Server Data Tools.** Zie Aan [de slag met Visual Studio 2019 voor SQL Data Warehouse voor](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-install-visual-studio.md)instructies.

**Maak verbinding met uw Azure Synapse Analytics met Visual Studio.** Zie stappen 1 & 2 in [Connect to Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-connect-overview.md)voor instructies.

> [!NOTE]
> Voer de volgende SQL-query uit op de database die u hebt gemaakt in uw Azure Synapse Analytics (in plaats van de query die in stap 3 van het verbindingsonderwerp wordt geleverd) om **een hoofdsleutel**te maken.
>
>

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Maak een Azure Machine Learning-werkruimte onder uw Azure-abonnement.** Zie Een [Azure Machine Learning-werkruimte maken](../studio/create-workspace.md)voor instructies.

## <a name="load-the-data-into-azure-synapse-analytics"></a><a name="getdata"></a>De gegevens laden in Azure Synapse Analytics
Open een Windows PowerShell-opdrachtconsole. Voer de volgende PowerShell-opdrachten uit om het voorbeeld SQL-scriptbestanden die we met u delen op GitHub te downloaden naar een lokale map die u opgeeft met de parameter *DestDir.* U de waarde van parameter *DestDir* wijzigen in een lokale map. Als *-DestDir* niet bestaat, wordt het gemaakt door het PowerShell-script.

> [!NOTE]
> Mogelijk moet **u als administrator worden uitgevoerd** wanneer u het volgende PowerShell-script uitvoert als uw *DestDir-map* beheerdersbevoegdheden nodig heeft om deze te maken of te schrijven.
>
>

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Na een succesvolle uitvoering wordt uw huidige werkmap gewijzigd in *-DestDir*. Je moet in staat zijn om het scherm te zien, zoals hieronder:

![Huidige werkmapwijzigingen][19]

Voer in *uw -DestDir*het volgende PowerShell-script uit in de beheerdersmodus:

    ./SQLDW_Data_Import.ps1

Wanneer het PowerShell-script voor de eerste keer wordt uitgevoerd, wordt u gevraagd de gegevens van uw Azure Synapse Analytics en uw Azure blob-opslagaccount in te voeren. Wanneer dit PowerShell-script voor de eerste keer is voltooid, worden de referenties die u invoert, naar een configuratiebestand SQLDW.conf in de huidige werkmap geschreven. De toekomstige uitvoering van dit PowerShell-scriptbestand heeft de mogelijkheid om alle benodigde parameters uit dit configuratiebestand te lezen. Als u bepaalde parameters moet wijzigen, u ervoor kiezen om de parameters op het scherm op te vragen in te voeren door dit configuratiebestand te verwijderen en de parameterswaarden in te voeren zoals gevraagd of om de parameterwaarden te wijzigen door het SQLDW.conf-bestand in uw *map -DestDir* te bewerken.

> [!NOTE]
> Om schemanaamconflicten te voorkomen met die in uw Azure Synapse Analytics in Azure, wordt bij het rechtstreeks lezen van parameters rechtstreeks uit het SQLDW.conf-bestand een willekeurig getal van 3 cijfers toegevoegd aan de schemanaam van het SQLDW.conf-bestand als het standaardschema naam voor elke run. Het PowerShell-script kan u om een schemanaam vragen: de naam kan naar eigen inzicht van de gebruiker worden opgegeven.
>
>

Met dit **PowerShell-scriptbestand** worden de volgende taken voltooid:

* **Downloads en installeert AzCopy,** als AzCopy nog niet is geïnstalleerd

        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
               Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }
* **Kopieert gegevens naar uw privéblobopslagaccount** van de openbare blob met AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* **Laadt gegevens met Polybase (door LoadDataToSQLDW.sql uit te voeren) naar uw Azure Synapse Analytics** vanuit uw privéblob-opslagaccount met de volgende opdrachten.

  * Een schema maken

          EXEC (''CREATE SCHEMA {schemaname};'');
  * Een databasescopereferentie maken

          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Een externe gegevensbron maken voor een Azure Storage-blob

          CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;

          CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;
  * Maak een externe bestandsindeling voor een csv-bestand. Gegevens worden niet gecomprimeerd en velden worden gescheiden met het pipe-teken.

          CREATE EXTERNAL FILE FORMAT {csv_file_format}
          WITH
          (
              FORMAT_TYPE = DELIMITEDTEXT,
              FORMAT_OPTIONS
              (
                  FIELD_TERMINATOR ='','',
                  USE_TYPE_DEFAULT = TRUE
              )
          )
          ;
  * Externe tarief- en reistabellen maken voor nyc-taxigegevensset in Azure blob-opslag.

          CREATE EXTERNAL TABLE {external_nyctaxi_fare}
          (
              medallion varchar(50) not null,
              hack_license varchar(50) not null,
              vendor_id char(3),
              pickup_datetime datetime not null,
              payment_type char(3),
              fare_amount float,
              surcharge float,
              mta_tax float,
              tip_amount float,
              tolls_amount float,
              total_amount float
          )
          with (
              LOCATION    = ''/nyctaxifare/'',
              DATA_SOURCE = {nyctaxi_fare_storage},
              FILE_FORMAT = {csv_file_format},
              REJECT_TYPE = VALUE,
              REJECT_VALUE = 12
          )

            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                   medallion varchar(50) not null,
                   hack_license varchar(50)  not null,
                   vendor_id char(3),
                   rate_code char(3),
                   store_and_fwd_flag char(3),
                   pickup_datetime datetime  not null,
                   dropoff_datetime datetime,
                   passenger_count int,
                   trip_time_in_secs bigint,
                   trip_distance float,
                   pickup_longitude varchar(30),
                   pickup_latitude varchar(30),
                   dropoff_longitude varchar(30),
                   dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12
            )

    - Gegevens uit externe tabellen in Azure blob-opslag laden naar Azure Synapse Analytics

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - Maak een voorbeeldgegevenstabel (NYCTaxi_Sample) en voeg er gegevens in door SQL-query's op de reis- en tarieftabellen te selecteren. (Sommige stappen van deze walkthrough moeten deze voorbeeldtabel gebruiken.)

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

De geografische locatie van uw opslagaccounts is van invloed op de laadtijden.

> [!NOTE]
> Afhankelijk van de geografische locatie van uw privéblobopslagaccount, kan het kopiëren van gegevens van een openbare blob naar uw privé-opslagaccount ongeveer 15 minuten of zelfs langer duren en het proces van het laden van gegevens van uw opslagaccount naar uw Azure Azure Synapse Analytics kan 20 minuten of langer duren.
>
>

U moet beslissen wat doen als u dubbele bron- en bestemmingsbestanden hebt.

> [!NOTE]
> Als de CSV-bestanden die moeten worden gekopieerd van de openbare blobopslag naar uw privéblobopslagaccount al bestaan in uw privéblobopslagaccount, vraagt AzCopy u of u ze wilt overschrijven. Als u ze niet wilt overschrijven, voert u **n** in wanneer u daarom wordt gevraagd. Als u ze **allemaal** wilt overschrijven, voert u **een** in wanneer u daarom wordt gevraagd. U ook **y** invoeren om CSV-bestanden afzonderlijk te overschrijven.
>
>

![Uitvoer van AzCopy][21]

U uw eigen gegevens gebruiken. Als uw gegevens zich in uw on-premises machine bevinden in uw echte toepassing, u AzCopy nog steeds gebruiken om on-premises gegevens te uploaden naar uw privé Azure blob-opslag. U hoeft alleen de locatie `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"` **Bron** te wijzigen in de opdracht AzCopy van het PowerShell-scriptbestand in de lokale map die uw gegevens bevat.

> [!TIP]
> Als uw gegevens zich al in uw eigen Azure blob-opslag bevinden in uw echte toepassing, u de azcopy-stap in het PowerShell-script overslaan en de gegevens rechtstreeks uploaden naar Azure Azure Synapse Analytics. Hiervoor zijn aanvullende bewerkingen van het script nodig om het aan te passen aan het formaat van uw gegevens.
>
>

Dit PowerShell-script sluit ook de Azure Synapse Analytics-informatie aan op de voorbeeldbestanden voor gegevensverkenning SQLDW_Explorations.sql, SQLDW_Explorations.ipynb en SQLDW_Explorations_Scripts.py, zodat deze drie bestanden klaar zijn om uit te proberen direct nadat het PowerShell-script is voltooid.

Na een succesvolle uitvoering ziet u het scherm zoals hieronder:

![Uitvoer van een geslaagde scriptuitvoering][20]

## <a name="data-exploration-and-feature-engineering-in-azure-synapse-analytics"></a><a name="dbexplore"></a>Gegevensverkenning en functie-engineering in Azure Synapse Analytics
In deze sectie voeren we gegevensverkenning en het genereren van functies uit door SQL-query's rechtstreeks uit te voeren tegen Azure Synapse Analytics met Behulp van **Visual Studio Data Tools.** Alle SQL-query's die in deze sectie worden gebruikt, zijn te vinden in het voorbeeldscript met de naam *SQLDW_Explorations.sql*. Dit bestand is al gedownload naar uw lokale directory door het PowerShell-script. U het ook ophalen van [GitHub.](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql) Maar het bestand in GitHub heeft niet de Azure Synapse Analytics-informatie aangesloten.

Maak verbinding met uw Azure Synapse Analytics met Visual Studio met de inlognaam en wachtwoord van Azure Synapse Analytics en open de **SQL Object Explorer** om te bevestigen dat de database en tabellen zijn geïmporteerd. Haal het *SQLDW_Explorations.sql-bestand op.*

> [!NOTE]
> Als u een pdw-queryeditor (Parallel Data Warehouse) wilt openen, gebruikt u de opdracht **Nieuwe query** terwijl uw PDW is geselecteerd in de SQL **Object Explorer.** De standaard SQL-queryeditor wordt niet ondersteund door PDW.
>
>

Dit zijn de typen taken voor het verkennen van gegevens en het genereren van functies in deze sectie:

* Bekijk gegevensdistributies van een paar velden in verschillende tijdvensters.
* Onderzoek de gegevenskwaliteit van de lengte- en breedtevelden.
* Genereer binaire en meerklassenclassificatielabels op basis van het **tipbedrag.\_**
* Genereer functies en bereken/vergelijk reisafstanden.
* Sluit je aan bij de twee tabellen en pak een willekeurig voorbeeld dat wordt gebruikt om modellen te bouwen.

### <a name="data-import-verification"></a>Verificatie van gegevensinvoer
Deze query's bieden een snelle verificatie van het aantal rijen en kolommen in de eerder gevulde tabellen met behulp van de parallelle bulkimport van Polybase,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Uitvoer:** U krijgt 173.179.759 rijen en 14 kolommen.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploratie: De distributie van de reis door medaillon
In dit voorbeeldquery worden de medaillons (taxinummers) geïdentificeerd die binnen een bepaalde periode meer dan 100 ritten hebben voltooid. De query zou profiteren van de partitie tabel toegang, omdat het wordt geconditioneerd door de partitie regeling van **pick-up\_datum**. Het opvragen van de volledige gegevensset maakt ook gebruik van de partitietabel en/of indexscan.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Uitvoer:** De query moet een tabel met rijen retourneren waarin de 13.369 medaillons (taxi's) en het aantal voltooide ritten in 2013 worden opgegeven. De laatste kolom bevat de telling van het aantal voltooide reizen.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Exploratie: Reisverdeling per medaillon en hack_license
In dit voorbeeld worden de medaillons (taxinummers) en hack_license nummers (chauffeurs) geïdentificeerd die binnen een bepaalde periode meer dan 100 ritten hebben voltooid.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Uitvoer:** De query moet een tabel met 13.369 rijen retourneren met de 13.369 auto-id's die meer dan 100 ritten in 2013 hebben voltooid. De laatste kolom bevat de telling van het aantal voltooide reizen.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Beoordeling van de gegevenskwaliteit: records verifiëren met onjuiste lengtegraad en/of breedtegraad
In dit voorbeeld wordt onderzocht of een van de lengte- en/of breedtevelden een ongeldige waarde bevat (stralingsgraden moeten tussen -90 en 90 liggen) of (0, 0) coördinaten hebben.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Uitvoer:** De query retourneert 837.467 reizen met ongeldige lengte- en/of breedtevelden.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploratie: Getipt vs. niet getipt reizen distributie
In dit voorbeeld wordt het aantal reizen gevonden dat is getipt ten opzichte van het aantal dat niet is getipt in een bepaalde periode (of in de volledige gegevensset als het het volledige jaar bestrijkt zoals het hier is ingesteld). Deze verdeling weerspiegelt de binaire labelverdeling die later moet worden gebruikt voor binaire classificatiemodellering.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Uitvoer:** De query moet de volgende tipfrequenties voor het jaar 2013 retourneren: 90.447.622 getipt en 82.264.709 niet getipt.

### <a name="exploration-tip-classrange-distribution"></a>Verkenning: Tipklasse/bereikverdeling
In dit voorbeeld wordt de verdeling van tipbereiken in een bepaalde periode berekend (of in de volledige gegevensset als deze betrekking heeft op het volledige jaar). Deze verdeling van labelklassen wordt later gebruikt voor classificatiemodellering met meerdere klassen.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**Output:**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Verkenning: Reisafstand berekenen en vergelijken
In dit voorbeeld worden de lengte van de pick-up en dropoff en de breedtegraad omgezet in SQL-geografische punten, wordt de reisafstand berekend met sql-geografische puntenverschil en wordt een willekeurige steekproef van de resultaten geretourneerd voor vergelijking. Het voorbeeld beperkt de resultaten tot geldige coördinaten alleen met behulp van de eerder behandelde beoordeling van de gegevenskwaliteit.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>Feature engineering met SQL-functies
Soms kunnen SQL-functies een efficiënte optie zijn voor functieengineering. In deze walkthrough hebben we een SQL-functie gedefinieerd om de directe afstand tussen de ophaal- en dropofflocaties te berekenen. U de volgende SQL-scripts uitvoeren in **Visual Studio Data Tools.**

Hier is het SQL-script dat de afstandsfunctie definieert.

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

Hier volgt een voorbeeld om deze functie aan te roepen om functies in uw SQL-query te genereren:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Uitvoer:** Deze query genereert een tabel (met 2.803.538 rijen) met ophaal- en dropoffbreedtes en lengtegraden en de bijbehorende directe afstanden in mijlen. Hier zijn de resultaten voor de eerste drie rijen:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | Directe afstand |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Gegevens voorbereiden voor modelbuilding
De volgende query voegt zich bij de **nyctaxi-reis\_** en **\_nyctaxi-tarieftabellen,** genereert een binair classificatielabel **met een kopwaarde**voor classificatieclassificatie **\_** en haalt een voorbeeld uit de volledige samengevoegde gegevensset. De bemonstering wordt gedaan door het ophalen van een subset van de reizen op basis van ophaaltijd.  Deze query kan vervolgens rechtstreeks worden gekopieerd in de [Azure Machine Learning Studio (klassieke)](https://studio.azureml.net) [importgegevensmodule] [importeren voor]directe gegevensopname uit de SQL-database-instantie in Azure. De query sluit records met onjuiste (0, 0) coördinaten uit.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Wanneer u klaar bent om over te gaan tot Azure Machine Learning, u het:

1. Sla de uiteindelijke SQL-query op om de gegevens te extraheren en te samplen en de query rechtstreeks te kopiëren in een[importgegevensmodule importeren]] in Azure Machine Learning, of
2. Blijf vasthouden aan de gesamplede en gemanipuleerde gegevens die u van plan bent te gebruiken voor modelbuilding in een nieuwe Azure Synapse[Analytics-tabel] en gebruik de nieuwe tabel in de [importgegevensmodule importeren van gegevens importeren]in Azure Machine Learning. Het PowerShell-script in een eerdere stap heeft deze taak voor u uitgevoerd. U rechtstreeks uit deze tabel lezen in de module Gegevens importeren.

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Gegevensverkenning en functie-engineering in IPython-notitieblok
In deze sectie zullen we gegevensverkenning en het genereren van functies uitvoeren met behulp van zowel Python- als SQL-query's tegen de Azure Synapse Analytics die eerder is gemaakt. Een voorbeeld van iPython-notitieblok met de naam **SQLDW_Explorations.ipynb** en een Python-scriptbestand **SQLDW_Explorations_Scripts.py** zijn gedownload naar uw lokale directory. Ze zijn ook beschikbaar op [GitHub.](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW) Deze twee bestanden zijn identiek in Python-scripts. Het Python-scriptbestand wordt aan u geleverd voor het geval u geen IPython Notebook-server hebt. Deze twee voorbeeldpythonbestanden zijn ontworpen onder **Python 2.7**.

De benodigde Azure Synapse Analytics-informatie in het voorbeeld IPython-notitieblok en het Python-scriptbestand dat naar uw lokale machine is gedownload, zijn eerder door het PowerShell-script aangesloten. Ze zijn uitvoerbaar zonder enige wijziging.

Als u al een Azure Machine Learning-werkruimte hebt ingesteld, u het voorbeeld IPython-notitieblok rechtstreeks uploaden naar de AzureML IPython-notitieblokservice en deze starten. Hier volgen de stappen die u wilt uploaden naar de AzureML IPython Notebook-service:

1. Meld u aan bij uw Azure Machine Learning-werkruimte, klik bovenaan op **Studio** en klik op **NOTITIEBLOKKEN** aan de linkerkant van de webpagina.

    ![Klik op Studio en vervolgens op NOTITIEBLOKKEN][22]
2. Klik op **NIEUW** in de linker benedenhoek van de webpagina en selecteer **Python 2**. Geef vervolgens een naam aan het notitieblok op en klik op het vinkje om het nieuwe lege IPython-notitieblok te maken.

    ![Klik op NIEUW en selecteer Python 2][23]
3. Klik op het **Jupyter-symbool** in de linkerbovenhoek van het nieuwe IPython-notitieblok.

    ![Klik op het symbool Jupyter][24]
4. Sleep en plaats het voorbeeld IPython-notitieblok naar de **boompagina** van uw AzureML IPython-notitieblokservice en klik op **Uploaden**. Vervolgens wordt het voorbeeld iPython-notitieblok geüpload naar de AzureML IPython-notitieblokservice.

    ![Klik op Uploaden][25]

Om het voorbeeld IPython Notebook of het Python-scriptbestand uit te voeren, zijn de volgende Python-pakketten nodig. Als u de AzureML IPython Notebook-service gebruikt, zijn deze pakketten vooraf geïnstalleerd.

- panda's
- Numpy
- matplotlib
- pyodbc
- Pytables

Bij het bouwen van geavanceerde analytische oplossingen op Azure Machine Learning met grote gegevens, hier is de aanbevolen volgorde:

* Lees in een kleine steekproef van de gegevens in een in-memory dataframe.
* Voer enkele visualisaties en verkenningen uit met behulp van de gesamplede gegevens.
* Experimenteer met feature engineering met behulp van de gesamplede gegevens.
* Gebruik Python om SQL Queries rechtstreeks uit te geven tegen de Azure Synapse Analytics voor grotere gegevensverkenning, gegevensmanipulatie en functieengineering.
* Bepaal de steekproefgrootte die geschikt is voor Azure Machine Learning-modelbouw.

De volgende zijn een paar gegevensverkenning, datavisualisatie en voorbeelden van functieengineering. Meer gegevensverkenningen zijn te vinden in het voorbeeld IPython Notebook en het voorbeeld Python-scriptbestand.

### <a name="initialize-database-credentials"></a>Databasereferenties initialiseren
Initialiseer de instellingen voor databaseverbindingen in de volgende variabelen:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Databaseverbinding maken
Hier is de verbindingstekenreeks die de verbinding met de database maakt.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Aantal rijen en kolommen in tabel rapporteren <nyctaxi_trip>
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Totaal aantal rijen = 173179759
* Totaal aantal kolommen = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>Aantal rijen en kolommen in tabel <nyctaxi_fare> rapporteren
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Totaal aantal rijen = 173179759
* Totaal aantal kolommen = 11

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Lees een klein gegevensvoorbeeld uit de Azure Synapse Analytics-database
    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

De tijd om de voorbeeldtabel te lezen is 14,096495 seconden.
Aantal opgehaalde rijen en kolommen = (1000, 21).

### <a name="descriptive-statistics"></a>Beschrijvende statistieken
Nu bent u klaar om de gesamplede gegevens te verkennen. We beginnen met het bekijken van een aantal beschrijvende statistieken voor de **reisafstand\_** (of andere velden die u wilt opgeven).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Visualisatie: voorbeeld van kaderplot
Vervolgens kijken we naar de doos plot voor de reis afstand om de quantiles visualiseren.

    df1.boxplot(column='trip_distance',return_type='dict')

![Uitvoer van de kaderplot][1]

### <a name="visualization-distribution-plot-example"></a>Visualisatie: voorbeeld van distributieplot
Plots die de verdeling en een histogram visualiseren voor de bemonsterde reisafstanden.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Distributieplotoutput][2]

### <a name="visualization-bar-and-line-plots"></a>Visualisatie: balk- en regelplots
In dit voorbeeld stoppen we de reisafstand in vijf opslaglocaties en visualiseren we de binningresultaten.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

We kunnen de bovenstaande opslaglocatieverdeling in een balk of lijnplot plotten met:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Uitvoer van het staafperceel][3]

en

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Plotuitvoer van lijn][4]

### <a name="visualization-scatterplot-examples"></a>Visualisatie: voorbeelden van Scatterplot
We tonen spreidingplot tussen **reistijd\_\_in\_seconden** en **reisafstand\_** om te zien of er een correlatie is

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Scatterplot-uitvoer van relatie tussen tijd en afstand][6]

Op dezelfde manier kunnen we de relatie tussen **tariefcode\_** en **reisafstand\_** controleren.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Scatterplot-uitvoer van relatie tussen code en afstand][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Gegevensverkenning op gesamplede gegevens met SQL-query's in IPython-notitieblok
In deze sectie verkennen we gegevensdistributies met behulp van de gesamplede gegevens die blijven bestaan in de nieuwe tabel die we hierboven hebben gemaakt. Vergelijkbare verkenningen kunnen worden uitgevoerd met behulp van de oorspronkelijke tabellen.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Verkenning: aantal rijen en kolommen in de opgenomen tabel rapporteren
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Exploratie: Getipt / niet struikelde Distributie
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Verkenning: Verdeling van de tipklasse
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Verkenning: Plot de tipverdeling per klasse
    tip_class_dist['tip_freq'].plot(kind='bar')

![Plot #26][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Exploratie: Dagelijkse distributie van reizen
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploratie: Reisverdeling per medaillon
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Exploratie: Trip distributie door medaillon en hack licentie
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Verkenning: Reistijdverdeling
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Exploratie: Verdeling van de afstand van de reis
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Verkenning: distributie van betalingstype
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Controleer de uiteindelijke vorm van de featurized tabel
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="build-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Modellen bouwen in Azure Machine Learning
We zijn nu klaar om over te gaan tot modelbuilding en modelimplementatie in [Azure Machine Learning.](https://studio.azureml.net) De gegevens zijn klaar om te worden gebruikt in een van de eerder geïdentificeerde voorspellingsproblemen, namelijk:

1. **Binaire classificatie**: Om te voorspellen of er al dan niet een fooi is betaald voor een reis.
2. **Classificatie van meerdere klassen**: Om het bereik van de betaalde fooi te voorspellen, volgens de eerder gedefinieerde klassen.
3. **Regressietaak:** Het bedrag van de fooi voor een reis voorspellen.

Als u wilt beginnen met de modelleringsoefening, meldt u zich aan bij uw **Azure Machine Learning -werkruimte (klassieke)** werkruimte. Zie Een werkruimte voor Azure Machine [Learning Studio (klassieke) werkruimte maken](../studio/create-workspace.md)als u nog geen werkruimte voor machine learning hebt gemaakt.

1. Zie [Wat is Azure Machine Learning Studio (klassiek)om](../studio/what-is-ml-studio.md) aan de slag te gaan met Azure Machine Learning?
2. Meld u aan bij [Azure Machine Learning Studio (klassiek).](https://studio.azureml.net)
3. De Machine Learning Studio (klassieke) startpagina biedt een schat aan informatie, video's, tutorials, links naar de Modules Reference, en andere bronnen. Zie het Azure Machine Learning [Documentation Center](https://azure.microsoft.com/documentation/services/machine-learning/)voor meer informatie over Azure Machine Learning.

Een typisch trainingsexperiment bestaat uit de volgende stappen:

1. Maak een **+NIEUW** experiment.
2. Haal de gegevens in Azure Machine Learning Studio (klassiek).
3. Pre-proces, transformeren en manipuleren van de gegevens als dat nodig is.
4. Genereer functies waar nodig.
5. Splits de gegevens op in trainings-/validatie/testgegevenssets (of heb afzonderlijke gegevenssets voor elk).
6. Selecteer een of meer machine learning-algoritmen, afhankelijk van het leerprobleem dat moet worden opgelost. Binaire classificatie, meerklassenclassificatie, regressie.
7. Train een of meer modellen met behulp van de trainingsgegevensset.
8. De validatiegegevensset scoremet het getrainde model(en).
9. Evalueer het model(en) om de relevante statistieken voor het leerprobleem te berekenen.
10. Stem het model(en) af en selecteer het beste model dat u wilt implementeren.

In deze oefening hebben we de gegevens in Azure Synapse Analytics al onderzocht en ontworpen en besloten we de steekproefgrootte in te nemen in Azure Machine Learning Studio (klassiek). Hier is de procedure om een of meer van de voorspellingsmodellen te bouwen:

1. Haal de gegevens in Azure Machine Learning Studio (klassiek) met behulp van de[importgegevensmodule Gegevens] [importeren,]beschikbaar in de sectie **Gegevensinvoer en Uitvoer.** Zie de referentiepagina[importgegevensmodule] [importeren]voor meer informatie.

    ![Azure ML-importgegevens][17]
2. Selecteer **Azure SQL Database** als **gegevensbron** in het deelvenster **Eigenschappen.**
3. Voer de DNS-naam van de database in het veld **Databaseservernaam** in. Formaat:`tcp:<your_virtual_machine_DNS_name>,1433`
4. Voer de **naam van** de database in het bijbehorende veld in.
5. Voer de *SQL-gebruikersnaam* in de naam van het **serveraccount**en het *wachtwoord* in het wachtwoord van het **serveraccount in**.
7. Plak in het tekstgebied **Databasequery** bewerken de query waarmee de benodigde databasevelden worden geëxtraheerd (inclusief berekende velden zoals de labels) en de gegevens naar beneden naar de gewenste steekproefgrootte.

Een voorbeeld van een experiment met binaire classificatie dat rechtstreeks uit de Azure Synapse Analytics-database wordt gelezen, vindt u in de onderstaande afbeelding (vergeet niet de tabelnamen te vervangen nyctaxi_trip en nyctaxi_fare door de schemanaam en de tabelnamen die u in uw walkthrough). Vergelijkbare experimenten kunnen worden gebouwd voor multiclass classificatie- en regressieproblemen.

![Azure ML-trein][10]

> [!IMPORTANT]
> In de voorbeelden van gegevensextractie en bemonsteringsquery's in vorige secties **worden alle labels voor de drie modelleringsoefeningen opgenomen in de query**. Een belangrijke (vereiste) stap in elk van de modellering oefeningen is het **uitsluiten** van de onnodige labels voor de andere twee problemen, en alle andere **doel lekken**. Gebruik bijvoorbeeld bij het gebruik van binaire classificatie het label **met de kop en** sluit de **tipklasse\_** velden, het **tipbedrag\_** en het totale **\_bedrag uit**. De laatste zijn doel lekken, omdat ze impliceren de tip betaald.
>
> Als u onnodige kolommen of doellekken wilt uitsluiten, u de module [Kolommen selecteren in gegevensset][select-columns] of de [metagegevens bewerken][edit-metadata]gebruiken. Zie [Kolommen selecteren in gegevensset][select-columns] en [Metagegevens bewerken][edit-metadata] voor meer informatie.
>
>

## <a name="deploy-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Modellen implementeren in Azure Machine Learning
Wanneer uw model klaar is, u het eenvoudig implementeren als een webservice rechtstreeks vanuit het experiment. Zie [Een Azure Machine Learning-webservice implementeren](../studio/deploy-a-machine-learning-web-service.md)voor meer informatie over het implementeren van Azure ML-webservices.

Als u een nieuwe webservice wilt implementeren, moet u het volgende doen:

1. Maak een scoreexperiment.
2. Implementeer de webservice.

Als u een scoreexperiment wilt maken op **een voltooid** trainingsexperiment, klikt u op **SCORINGsEXPERIMENT MAKEN** op de onderste actiebalk.

![Azure-score][18]

Azure Machine Learning probeert een scoreexperiment te maken op basis van de onderdelen van het trainingsexperiment. In het bijzonder zal het:

1. Sla het getrainde model op en verwijder de modeltrainingsmodules.
2. Een logische **invoerpoort** identificeren om het schema voor verwachte invoergegevens weer te geven.
3. Een logische **uitvoerpoort** identificeren om het verwachte webserviceuitvoerschema weer te geven.

Wanneer het scoreexperiment wordt gemaakt, bekijkt u de resultaten en past u indien nodig aan. Een typische aanpassing is om de invoerset of query te vervangen door een query die labelvelden uitsluit, omdat deze labelvelden niet worden toegewezen aan het schema wanneer u de service aanroept. Het is ook een goede gewoonte om de grootte van de invoergegevensset en/of query te reduceren tot een paar records, genoeg om het invoerschema aan te geven. Voor de uitvoerpoort is het gebruikelijk om alle invoervelden uit te sluiten en alleen de **gescoorde labels** en **gescoorde waarschijnlijkheden** in de uitvoer op te nemen met de module [Kolommen selecteren in gegevensset.][select-columns]

Een voorbeeld score experiment is voorzien in de onderstaande figuur. Wanneer u klaar bent om te worden geïmplementeerd, klikt u op de knop **WEBSERVICE PUBLICEREN** op de onderste actiebalk.

![Azure ML publiceren][11]

## <a name="summary"></a>Samenvatting
Om samen te vatten wat we hebben gedaan in deze walkthrough-zelfstudie, hebt u een Azure-gegevenswetenschapsomgeving gemaakt, gewerkt met een grote openbare gegevensset, deze door het Team Data Science-proces geleid, van gegevensverwerving tot modeltraining en vervolgens naar de implementatie van een Azure Machine Learning-webservice.

### <a name="license-information"></a>Licentiegegevens
Deze voorbeeld-walkthrough en de bijbehorende scripts en IPython-notitieblokken worden door Microsoft gedeeld onder de MIT-licentie. Controleer het bestand LICENSE.txt in de map van de voorbeeldcode op GitHub voor meer informatie.

## <a name="references"></a>Verwijzingen
- [Andrés Monroy NYC Taxi Trips Download Pagina](https://www.andresmh.com/nyctaxitrips/)
- [FOILing NYC's Taxi Trip Data door Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [NYC Taxi en Limousine Commissie Onderzoek en Statistieken](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sqldw-walkthrough/azuremltrain.png
[11]: ./media/sqldw-walkthrough/azuremlpublish.png
[12]: ./media/sqldw-walkthrough/ssmsconnect.png
[13]: ./media/sqldw-walkthrough/executescript.png
[14]: ./media/sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/sqldw-walkthrough/bulkimport.png
[17]: ./media/sqldw-walkthrough/amlreader.png
[18]: ./media/sqldw-walkthrough/amlscoring.png
[19]: ./media/sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/sqldw-walkthrough/ps_load_data.png
[21]: ./media/sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
