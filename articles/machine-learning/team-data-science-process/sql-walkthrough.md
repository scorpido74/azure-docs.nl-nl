---
title: Een model bouwen en implementeren in een SQL Server VM - Team Data Science Process
description: Een machine learning-model bouwen en implementeren met SQL Server op een Azure VM met een openbaar beschikbare gegevensset.
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
ms.openlocfilehash: a47f30cf00624faf098c8b605534cf355eacadee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251581"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Het Team Data Science-proces in actie: SQL Server gebruiken
In deze zelfstudie doorloopt u het proces van het bouwen en implementeren van een machine learning-model met SQL Server en een openbaar beschikbare gegevensset - de NYC Taxi Trips-gegevensset. [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) De procedure volgt een standaard data science workflow: de gegevens innemen en verkennen, functies ontwikkelen om het leren te vergemakkelijken, vervolgens een model bouwen en implementeren.

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>NYC Taxi Trips Dataset Beschrijving
De NYC Taxi Trip gegevens is ongeveer 20 GB van gecomprimeerde CSV-bestanden (~ 48 GB ongecomprimeerd), bestaande uit meer dan 173 miljoen individuele reizen en de tarieven betaald voor elke reis. Elke reis record omvat de pick-up en dropoff locatie en tijd, geanonimiseerde hack (driver's) licentienummer en medaillon (unieke id taxi's) nummer. De gegevens hebben betrekking op alle reizen in het jaar 2013 en worden verstrekt in de volgende twee gegevenssets voor elke maand:

1. De CSV 'trip_data' bevat reisgegevens, zoals het aantal passagiers, ophaal- en inleverpunten, reisduur en reislengte. Hier zijn een paar voorbeeldrecords:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. De 'trip_fare' CSV bevat details over het betaalde tarief voor elke reis, zoals het betalingstype, het tariefbedrag, toeslagen en belastingen, tips en tolgelden en het totale betaalde bedrag. Hier zijn een paar voorbeeldrecords:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

De unieke sleutel\_om deel\_te nemen reisgegevens en reistarief\_bestaat\_uit de velden: medaillon, hack licentie en pick-up datetime.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Voorbeelden van voorspellingstaken
We zullen drie voorspellingsproblemen formuleren op basis van het *tipbedrag,\_* namelijk:

* Binaire classificatie: Voorspel of een fooi is betaald voor een reis, dat wil zeggen, een *tipbedrag\_* dat groter is dan $ 0 is een positief voorbeeld, terwijl een *tipbedrag\_* van $0 een negatief voorbeeld is.
* Classificatie van meerdere klassen: het bereik van de fooi en de reis voorspellen. We verdelen het *tipbedrag\_* in vijf bakken of klassen:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
* Regressietaak: Het bedrag van de fooi voor een reis voorspellen.  

## <a name="setting-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>De Azure-datascience-omgeving instellen voor geavanceerde analyses
Zoals u zien in de gids [Uw omgeving plannen,](plan-your-environment.md) zijn er verschillende opties om te werken met de nyc-taxireisgegevensin Azure:

* Werken met de gegevens in Azure-blobs en vervolgens modelleren in Azure Machine Learning
* De gegevens in een SQL Server-database laden en vervolgens modelleren in Azure Machine Learning

In deze zelfstudie tonen we parallelle bulkimport van de gegevens naar een SQL Server, gegevensverkenning, functieengineering en down sampling met SQL Server Management Studio en met IPython Notebook. [Voorbeeldscripts](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) en [IPython-notitieblokken](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) worden gedeeld in GitHub. Een voorbeeld van iPython-notitieblok om met de gegevens in Azure-blobs te werken, is ook beschikbaar op dezelfde locatie.

Ga als u uw Azure Data Science-omgeving in:

1. [Een opslagaccount maken](../../storage/common/storage-account-create.md)
2. [Een Azure Machine Learning-werkruimte maken](../studio/create-workspace.md)
3. [Een Data Science Virtual Machine inrichten,](../data-science-virtual-machine/setup-sql-server-virtual-machine.md)die een SQL Server en een IPython Notebook-server biedt.
   
   > [!NOTE]
   > De voorbeeldscripts en IPython-notitieblokken worden tijdens het installatieproces gedownload naar uw virtuele machine van Data Science. Wanneer het script voor de installatie van de VM is voltooid, worden de voorbeelden in de documentenbibliotheek van uw vm weergegeven:  
   > 
   > * Voorbeeldscripts:`C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Voorbeeld van iPython-notitieblokken:`C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   waar `<user_name>` is de Windows-inlognaam van uw VM. We verwijzen naar de voorbeeldmappen als **Voorbeeldscripts** en **VoorbeeldiPython-notitieblokken.**
   > 
   > 

Op basis van de grootte van de gegevensset, de locatie van de gegevensbron en de geselecteerde Azure-doelomgeving is dit scenario vergelijkbaar met [Scenario \#5: Grote gegevensset in een lokale bestanden, target SQL Server in Azure VM](plan-sample-scenarios.md#largelocaltodb).

## <a name="get-the-data-from-public-source"></a><a name="getdata"></a>Haal de gegevens uit openbare bron
Als u de gegevensset [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) vanaf de openbare locatie wilt ophalen, u een van de methoden gebruiken die zijn beschreven in Gegevens verplaatsen van en naar Azure Blob [Storage](move-azure-blob.md) om de gegevens naar uw nieuwe virtuele machine te kopiëren.

Ga als een kopie van de gegevens met AzCopy:

1. Inloggen bij uw virtuele machine (VM)
2. Maak een nieuwe map in de gegevensschijf van de VM (Opmerking: gebruik de tijdelijke schijf die bij de VM als gegevensschijf wordt geleverd niet).
3. Voer in een opdrachtpromptvenster de volgende opdrachtregel Azcopy uit en vervang <path_to_data_folder> door uw gegevensmap die is gemaakt in (2):
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    Wanneer de AzCopy is voltooid, moeten in totaal 24\_csv-bestanden met\_rits (12 voor ritgegevens en 12 voor rittarief) in de gegevensmap staan.
4. Rits de gedownloade bestanden uit. Let op de map waar de niet-gecomprimeerde bestanden zich bevinden. Deze map wordt het <\_pad\_\_naar\>gegevensbestanden genoemd.

## <a name="bulk-import-data-into-sql-server-database"></a><a name="dbload"></a>Bulkimportgegevens in SQL Server-database
De prestaties van het laden/overbrengen van grote hoeveelheden gegevens naar een SQL-database en volgende query's kunnen worden verbeterd met behulp van *partitietabellen en weergaven.* In deze sectie volgen we de instructies die zijn beschreven in [Parallel Bulk Data Import met SQL-partitietabellen](parallel-load-sql-partitioned-tables.md) om een nieuwe database te maken en de gegevens parallel in partitietabellen te laden.

1. Als u bent ingelogd op uw VM, start u **SQL Server Management Studio**.
2. Maak verbinding met Windows-verificatie.
   
    ![SSMS Connect][12]
3. Als u de SQL Server-verificatiemodus nog niet hebt gewijzigd en een nieuwe SQL-inloggebruiker hebt gemaakt, opent u het scriptbestand met de naam **change\_auth.sql** in de map **Voorbeeldscripts.** Wijzig de standaardgebruikersnaam en het standaardwachtwoord. Klik **op Uitvoeren** op de werkbalk om het script uit te voeren.
   
    ![Script uitvoeren][13]
4. Controleer en/of wijzig de standaarddatabase en logboekmappen van SQL Server om ervoor te zorgen dat nieuw gemaakte databases worden opgeslagen in een gegevensschijf. De SQL Server VM-afbeelding die is geoptimaliseerd voor het belasting en lasten van gegevensopslag, is vooraf geconfigureerd met gegevens en logboekschijven. Als uw vm geen gegevensschijf heeft opgenomen en u tijdens het installatieproces van virtuele virtuele schijven nieuwe virtuele harde schijven hebt toegevoegd, wijzigt u de standaardmappen als volgt:
   
   * Klik met de rechtermuisknop op de SQL Server-naam in het linkerdeelvenster en klik op **Eigenschappen**.
     
       ![SQL Server-eigenschappen][14]
   * Selecteer **Database-instellingen** in de lijst **Een pagina** selecteren aan de linkerkant.
   * Controleer en/of wijzig de **standaardlocaties van** de database in de locaties van de **gegevensschijf** van uw keuze. Op deze locatie bevinden zich nieuwe databases als deze zijn gemaakt met de standaardinstellingen.
     
       ![Standaardinstellingen voor SQL-database][15]  
5. Als u een nieuwe database en een set bestandsgroepen wilt maken om de partitietabellen vast te houden, opent u het voorbeeldscript **door db\_\_default.sql te maken.** Het script maakt een nieuwe database met de naam **TaxiNYC** en 12 bestandsgroepen in de standaardgegevenslocatie. Elke filegroep houdt een\_maand aan\_reisgegevens en rittariefgegevens in. Wijzig desgewenst de naam van de database. Klik **op Uitvoeren** om het script uit te voeren.
6. Maak vervolgens twee partitietabellen, een\_voor de reisgegevens en een andere voor het rittarief.\_ Open het voorbeeldscript **maak partitietable.sql\_\_**, wat:
   
   * Maak een partitiefunctie om de gegevens per maand te splitsen.
   * Maak een partitieschema om de gegevens van elke maand aan een andere bestandsgroep toe te leiden.
   * Maak twee verdeelde tabellen toegewezen aan de partitie regeling:\_ **\_nyctaxi reis** zal de\_reis gegevens te houden en **nyctaxi\_tarief** zal de reis tarief gegevens te houden.
     
     Klik **op Uitvoeren** om het script uit te voeren en de partitietabellen te maken.
7. In de map **Voorbeeldscripts** zijn twee voorbeeld-PowerShell-scripts beschikbaar om parallelle bulkinvoer van gegevens naar SQL Server-tabellen aan te tonen.
   
   * **bcp\_\_parallel generic.ps1** is een algemeen script voor parallelle bulkimportgegevens in een tabel. Wijzig dit script om de invoer- en doelvariabelen in te stellen zoals aangegeven in de commentaarregels in het script.
   * **bcp\_\_parallel nyctaxi.ps1** is een vooraf geconfigureerde versie van het generieke script en kan worden gebruikt om beide tabellen te laden voor de NYC Taxi Trips gegevens.  
8. Klik met de rechtermuisknop op de naam van het **bcp\_parallel\_nyctaxi.ps1-script** en klik op **Bewerken** om deze in PowerShell te openen. Controleer de vooraf ingestelde variabelen en wijzig op basis van de geselecteerde databasenaam, invoergegevensmap, doellogboekmap en paden naar de voorbeeldindelingsbestanden **nyctaxi_trip.xml** en **\_nyctaxi fare.xml** (in de map **Voorbeeldscripts).**
   
    ![Gegevens voor het importeren van bulk][16]
   
    U ook de verificatiemodus selecteren, standaard is Windows-verificatie. Klik op de groene pijl op de werkbalk om uit te voeren. Het script start 24 bulkimportbewerkingen parallel, 12 voor elke partitietabel. U de voortgang van het importeren van gegevens controleren door de standaardgegevensmap van SQL Server te openen zoals hierboven ingesteld.
9. Het PowerShell-script rapporteert de begin- en eindtijden. Wanneer alle bulkinvoer is voltooid, wordt de eindtijd gerapporteerd. Controleer de map van het doellogboek om te controleren of de bulkinvoer is gelukt, dat wil zeggen dat er geen fouten zijn gerapporteerd in de map met doellogboeken.
10. Uw database is nu klaar voor exploratie, feature engineering en andere bewerkingen zoals gewenst. Aangezien de tabellen zijn verdeeld volgens het veld **ophaaldatum,\_** profiteren query's die de **datumvoorwaarden voor ophalen\_** in de **WHERE-component** bevatten, van het partitieschema.
11. Bekijk in **SQL Server Management Studio**de meegeleverde voorbeeldscriptvoorbeeldquery's.sql **\_**. Als u een van de voorbeeldquery's wilt uitvoeren, markeert u de queryregels en klikt u op **Uitvoeren** op de werkbalk.
12. De NYC Taxi Trips-gegevens worden geladen in twee afzonderlijke tabellen. Om de join-bewerkingen te verbeteren, is het ten zeerste aan te raden om de tabellen te indexeren. Het voorbeeldscript **\_maakt\_partitieindex.sql** maakt partitieindexen op de samengestelde join key **medaillon, hacklicentie\_en pick-updatum.\_**

## <a name="data-exploration-and-feature-engineering-in-sql-server"></a><a name="dbexplore"></a>Gegevensverkenning en functie-engineering in SQL Server
In deze sectie voeren we gegevensverkenning en functiegeneratie uit door SQL-query's rechtstreeks in de **SQL Server Management Studio** uit te voeren met behulp van de SQL Server-database die eerder is gemaakt. In de map **Voorbeeldscripts** wordt een voorbeeldscript met de naam **voorbeeldquery's.sql\_** weergegeven. Wijzig het script om de databasenaam te wijzigen, als deze verschilt van de standaard: **TaxiNYC**.

In deze oefening zullen we:

* Maak verbinding met **SQL Server Management Studio** met behulp van Windows Authentication of met SQL Authentication en de SQL-inlognaam en -wachtwoord.
* Bekijk gegevensdistributies van een paar velden in verschillende tijdvensters.
* Onderzoek de gegevenskwaliteit van de lengte- en breedtevelden.
* Genereer binaire en meerklassenclassificatielabels op basis van het **tipbedrag.\_**
* Genereer functies en bereken/vergelijk reisafstanden.
* Sluit je aan bij de twee tabellen en pak een willekeurig voorbeeld dat wordt gebruikt om modellen te bouwen.

Wanneer u klaar bent om over te gaan tot Azure Machine Learning, u het:  

1. Sla de uiteindelijke SQL-query op om de gegevens te extraheren en te samplen en de query rechtstreeks te kopiëren naar een [module Gegevens importeren][import-data] in Azure Machine Learning, of
2. Blijf vasthouden aan de gesamplede en gemanipuleerde gegevens die u van plan bent te gebruiken voor modelbuilding in een nieuwe databasetabel en gebruik de nieuwe tabel in de module [Gegevens importeren][import-data] in Azure Machine Learning.

In deze sectie slaan we de uiteindelijke query op om de gegevens te extraheren en te samplen. De tweede methode wordt gedemonstreerd in de sectie [Data Exploration and Feature Engineering in IPython Notebook.](#ipnb)

Voor een snelle controle van het aantal rijen en kolommen in de eerder gevulde tabellen met behulp van parallelle bulkimport,

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Exploratie: De distributie van de reis door medaillon
In dit voorbeeld wordt het medaillon (taxinummers) geïdentificeerd met meer dan 100 ritten binnen een bepaalde periode. De query zou profiteren van de partitie tabel toegang, omdat het wordt geconditioneerd door de partitie regeling van **pick-up\_datum**. Het opvragen van de volledige gegevensset maakt ook gebruik van de partitietabel en/of indexscan.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Exploratie: Reisverdeling per medaillon en hack_license
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Beoordeling van de gegevenskwaliteit: records verifiëren met onjuiste lengtegraad en/of breedtegraad
In dit voorbeeld wordt onderzocht of een van de lengte- en/of breedtevelden een ongeldige waarde bevat (stralingsgraden moeten tussen -90 en 90 liggen) of (0, 0) coördinaten hebben.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploratie: Getipt vs. Niet Getipt Trips distributie
In dit voorbeeld wordt het aantal reizen gevonden dat in een bepaalde periode is getipt vs. niet getipt (of in de volledige gegevensset als deze betrekking heeft op het volledige jaar). Deze verdeling weerspiegelt de binaire labelverdeling die later moet worden gebruikt voor binaire classificatiemodellering.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Verkenning: Tipklasse/bereikverdeling
In dit voorbeeld wordt de verdeling van tipbereiken in een bepaalde periode berekend (of in de volledige gegevensset als deze betrekking heeft op het volledige jaar). Deze verdeling van de labelklassen wordt later gebruikt voor classificatiemodellering met meerdere klassen.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

#### <a name="exploration-compute-and-compare-trip-distance"></a>Verkenning: Reisafstand berekenen en vergelijken
In dit voorbeeld worden de lengte van de pick-up en dropoff en de breedtegraad omgezet in SQL-geografische punten, wordt de reisafstand berekend met sql-geografische puntenverschil en wordt een willekeurige steekproef van de resultaten geretourneerd voor vergelijking. Het voorbeeld beperkt de resultaten tot geldige coördinaten alleen met behulp van de eerder behandelde beoordeling van de gegevenskwaliteit.

    SELECT
    pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
    ,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
    ,trip_distance
    ,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
    FROM nyctaxi_trip
    tablesample(0.01 percent)
    WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### <a name="feature-engineering-in-sql-queries"></a>Feature Engineering in SQL-query's
De query's voor het genereren van labels en geografische conversiekunnen ook worden gebruikt om labels/functies te genereren door het tellende onderdeel te verwijderen. Aanvullende FEATURE Engineering SQL-voorbeelden worden weergegeven in de sectie [Gegevensverkenning en Functie-engineering in IPython Notebook.](#ipnb) Het is efficiënter om de query's voor het genereren van functies uit te voeren op de volledige gegevensset of een grote subset ervan met SQL-query's die rechtstreeks op de SQL Server-database-instantie worden uitgevoerd. De query's kunnen worden uitgevoerd in **SQL Server Management Studio,** IPython Notebook of een ontwikkelingstool of omgeving die lokaal of op afstand toegang heeft tot de database.

#### <a name="preparing-data-for-model-building"></a>Gegevens voorbereiden voor modelbouw
De volgende query voegt zich bij de **\_nyctaxi reis** en **nyctaxi\_tarief** tabellen, genereert een binaire classificatie label **getipt**, een multi-class classificatie label tip **\_klasse**, en haalt een 1% willekeurige steekproef uit de volledige samengevoegde dataset. Deze query kan vervolgens rechtstreeks worden gekopieerd in de [Azure Machine Learning Studio](https://studio.azureml.net) Import [Data-module][import-data] voor directe gegevensopname uit de SQL Server-database-instantie in Azure. De query sluit records met onjuiste (0, 0) coördinaten uit.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 percent)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Gegevensverkenning en functie-engineering in IPython-notitieblok
In deze sectie zullen we gegevensverkenning en het genereren van functies uitvoeren met behulp van zowel Python- als SQL-query's tegen de eerder gemaakte SQL Server-database. Een voorbeeld van IPython-notebook met de naam **machine-Learning-data-science-process-sql-story.ipynb** wordt aangeboden in de map **Voorbeeld iPython-notitieblokken.** Deze notebook is ook beschikbaar op [GitHub.](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)

Wanneer u met big data werkt, volgt u deze aanbevolen volgorde:

* Lees in een kleine steekproef van de gegevens in een in-memory dataframe.
* Voer enkele visualisaties en verkenningen uit met behulp van de gesamplede gegevens.
* Experimenteer met feature engineering met behulp van de gesamplede gegevens.
* Gebruik Python om SQL Queries rechtstreeks uit te geven tegen de SQL Server-database in de Azure VM voor grotere gegevensverkenning, gegevensmanipulatie en functieengineering.
* Bepaal de steekproefgrootte die moet worden gebruikt voor Azure Machine Learning-modelbuilding.

Wanneer u klaar bent om over te gaan tot Azure Machine Learning, u het:  

1. Sla de uiteindelijke SQL-query op om de gegevens te extraheren en te samplen en de query rechtstreeks te kopiëren naar een [module Gegevens importeren][import-data] in Azure Machine Learning. Deze methode wordt gedemonstreerd in de sectie [Bouwmodellen in Azure Machine Learning.](#mlmodel)    
2. Houd de gesamplede en gemanipuleerde gegevens die u van plan bent te gebruiken voor modelbouw in een nieuwe databasetabel vol en gebruik vervolgens de nieuwe tabel in de module [Gegevens importeren.][import-data]

Hieronder volgen enkele voorbeelden van gegevensverkenning, gegevensvisualisatie en functieengineering. Zie het voorbeeld SQL IPython-notitieblok in de map **Voorbeeld iPython-notitieblokken voor** meer voorbeelden.

#### <a name="initialize-database-credentials"></a>Databasereferenties initialiseren
Initialiseer de instellingen voor databaseverbindingen in de volgende variabelen:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Databaseverbinding maken
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Rapport aantal rijen en kolommen in tabel nyctaxi_trip
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('nyctaxi_trip')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('nyctaxi_trip')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Totaal aantal rijen = 173179759  
* Totaal aantal kolommen = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Een klein gegevensvoorbeeld uit de SQL Server-database lezen
    t0 = time.time()

    query = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (0.05 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Tijd om de voorbeeldtabel te lezen is 6,492000 seconden  
Aantal opgehaalde rijen en kolommen = (84952, 21)

#### <a name="descriptive-statistics"></a>Beschrijvende statistieken
Nu zijn klaar om de gesamplede gegevens te verkennen. We beginnen met het bekijken van beschrijvende statistieken voor het **veld\_** (of een ander) veld(en):

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Visualisatie: voorbeeld van vakplot
Vervolgens kijken we naar de doos plot voor de reis afstand om de quantiles visualiseren

    df1.boxplot(column='trip_distance',return_type='dict')

![Plot #1][1]

#### <a name="visualization-distribution-plot-example"></a>Visualisatie: voorbeeld van distributieplot
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Plot #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Visualisatie: balk- en regelplots
In dit voorbeeld stoppen we de reisafstand in vijf opslaglocaties en visualiseren we de binningresultaten.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

We kunnen de bovenstaande bin distributie in een bar of lijn plot als hieronder plot

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Plot #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Plot #4][4]

#### <a name="visualization-scatterplot-example"></a>Visualisatie: voorbeeld van Scatterplot
We tonen spreidingplot tussen **reistijd\_\_in\_seconden** en **reisafstand\_** om te zien of er een correlatie is

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Plot #6][6]

Op dezelfde manier kunnen we de relatie tussen **tariefcode\_** en **reisafstand\_** controleren.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Plot #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Sub-Sampling van de gegevens in SQL
Bij het voorbereiden van gegevens voor modelbuilding in [Azure Machine Learning Studio](https://studio.azureml.net)u ofwel besluiten dat de **SQL-query rechtstreeks in de module Gegevens importeren gebruikt** of de gemanipuleerde en gesamplede gegevens in een nieuwe tabel blijven gebruiken, die u gebruiken in de module Gegevens [importeren][import-data] met een eenvoudige SELECT * UIT <**uw\_\_nieuwe tabelnaam\_>**.

In deze sectie maken we een nieuwe tabel om de gesamplede en gemanipuleerde gegevens vast te houden. Een voorbeeld van een directe SQL-query voor modelbuilding wordt weergegeven in de sectie [Gegevensverkenning en Functie-engineering in SQL Server.](#dbexplore)

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Maak een voorbeeldtabel en vul met 1% van de samengevoegde tabellen. Drop Table First als deze bestaat.
In deze sectie, we toetreden tot de tabellen **\_nyctaxi reis** en **nyctaxi\_tarief,** extract een 1% willekeurige steekproef, en blijven de steekproef gegevens in een nieuwe tabel naam **nyctaxi\_\_een procent:**

    cursor = conn.cursor()

    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''

    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
        INTO nyctaxi_one_percent
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (1 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
        AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''

    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Gegevensverkenning met SQL-query's in IPython-notitieblok
In deze sectie verkennen we gegevensdistributies met behulp van de 1% gesamplede gegevens die blijven bestaan in de nieuwe tabel die we hierboven hebben gemaakt. Vergelijkbare verkenningen kunnen worden uitgevoerd met behulp van de oorspronkelijke tabellen, optioneel met behulp van **TABLESAMPLE** om het verkenningsvoorbeeld te beperken of door de resultaten te beperken tot een bepaalde periode met behulp van de **datumpartities\_** voor ophalen, zoals geïllustreerd in de sectie [Gegevensverkenning en Functie-engineering in SQL Server.](#dbexplore)

#### <a name="exploration-daily-distribution-of-trips"></a>Exploratie: Dagelijkse distributie van reizen
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploratie: Reisverdeling per medaillon
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Functiegeneratie met SQL-query's in IPython-notitieblok
In deze sectie genereren we nieuwe labels en functies rechtstreeks met SQL-query's, die werken op de 1% voorbeeldtabel die we in de vorige sectie hebben gemaakt.

#### <a name="label-generation-generate-class-labels"></a>Labelgeneratie: klasselabels genereren
In het volgende voorbeeld genereren we twee sets labels die we kunnen gebruiken voor modellering:

1. Binaire klasselabels **getipt** (voorspellen of er een tip wordt gegeven)
2. Tipklasse labels met meerdere klassen (het voorspellen van de tipopslag of het bereik) **\_**
   
        nyctaxi_one_percent_add_col = '''
            ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
        '''
   
        cursor.execute(nyctaxi_one_percent_add_col)
        cursor.commit()
   
        nyctaxi_one_percent_update_col = '''
            UPDATE nyctaxi_one_percent
            SET
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''
   
        cursor.execute(nyctaxi_one_percent_update_col)
        cursor.commit()

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Feature Engineering: Functie-functies voor categorische kolommen
In dit voorbeeld wordt een categorisch veld omgezet in een numeriek veld door elke categorie te vervangen door het aantal exemplaren in de gegevens.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B AS
        (
            SELECT medallion, hack_license,
                SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
                SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
            FROM nyctaxi_one_percent
            GROUP BY medallion, hack_license
        )

        UPDATE nyctaxi_one_percent
        SET nyctaxi_one_percent.cmt_count = B.cmt_count,
            nyctaxi_one_percent.vts_count = B.vts_count
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion AND A.hack_license = B.hack_license
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Feature Engineering: Bin-functies voor numerieke kolommen
In dit voorbeeld wordt een continu numeriek veld omgezet in vooraf ingestelde categoriebereiken, dat wil zeggen het numerieke veld omzetten in een categorisch veld.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
        (
            SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
            NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
        )

        UPDATE nyctaxi_one_percent
        SET trip_time_bin = B.BinNumber
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion
        AND A.hack_license = B.hack_license
        AND A.pickup_datetime = B.pickup_datetime
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Feature Engineering: Locatiefuncties extraheren uit decimale breedte/lengte
In dit voorbeeld wordt de decimale weergave van een breedte- en/of lengteveld opgesplitst in meerdere regiovelden met verschillende granulariteit, zoals land/regio, stad, stad, blok, enz. De nieuwe geovelden worden niet toegewezen aan werkelijke locaties. Zie [Bing Maps REST Services](https://msdn.microsoft.com/library/ff701710.aspx)voor informatie over het toewijzen van geocodelocaties.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent
        ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
            l5 varchar(3), l6 varchar(3), l7 varchar(3)
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET l1=round(pickup_longitude,0)
            , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
            , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
            , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
            , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
            , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
            , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Controleer de uiteindelijke vorm van de featurized tabel
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

We zijn nu klaar om over te gaan tot modelbuilding en modelimplementatie in [Azure Machine Learning.](https://studio.azureml.net) De gegevens zijn klaar voor een van de eerder geïdentificeerde voorspellingsproblemen, namelijk:

1. Binaire classificatie: Om te voorspellen of er al dan niet een fooi is betaald voor een reis.
2. Classificatie van meerdere klassen: het bereik van de betaalde fooi en volgens de eerder gedefinieerde klassen te voorspellen.
3. Regressietaak: Het bedrag van de fooi voor een reis voorspellen.  

## <a name="building-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Modellen bouwen in Azure Machine Learning
Als u wilt beginnen met de modelleringsoefening, meldt u zich aan bij uw Azure Machine Learning-werkruimte. Zie [Een Azure Machine Learning-werkruimte maken](../studio/create-workspace.md)als u nog geen werkruimte voor machine learning hebt gemaakt.

1. Zie Wat is Azure Machine Learning Studio om aan de slag te gaan met Azure Machine [Learning?](../studio/what-is-ml-studio.md)
2. Meld u aan bij [Azure Machine Learning Studio](https://studio.azureml.net).
3. De startpagina van Studio biedt een schat aan informatie, video's, tutorials, links naar de modulesreferentie en andere bronnen. Raadpleeg het Azure Machine Learning [Documentation Center](https://azure.microsoft.com/documentation/services/machine-learning/)voor meer informatie over Azure Machine Learning.

Een typisch trainingsexperiment bestaat uit de volgende stappen:

1. Maak een **+NIEUW** experiment.
2. Download de gegevens naar Azure Machine Learning.
3. Pre-proces, transformeren en manipuleren van de gegevens als dat nodig is.
4. Genereer functies waar nodig.
5. Splits de gegevens op in trainings-/validatie/testgegevenssets (of heb afzonderlijke gegevenssets voor elk).
6. Selecteer een of meer machine learning-algoritmen, afhankelijk van het leerprobleem dat moet worden opgelost. Binaire classificatie, meerklassenclassificatie, regressie.
7. Train een of meer modellen met behulp van de trainingsgegevensset.
8. De validatiegegevensset scoremet het getrainde model(en).
9. Evalueer het model(en) om de relevante statistieken voor het leerprobleem te berekenen.
10. Stem het model(en) af en selecteer het beste model dat u wilt implementeren.

In deze oefening hebben we de gegevens in SQL Server al onderzocht en ontworpen en besloten we de steekproefgrootte in te nemen in Azure Machine Learning. Om een of meer van de voorspellingsmodellen te bouwen, hebben we besloten:

1. Download de gegevens naar Azure Machine Learning met de module [Gegevens importeren,][import-data] beschikbaar in de sectie **Gegevensinvoer en -uitvoer.** Zie de [referentiepagina van][import-data] de module Gegevens importeren voor meer informatie.
   
    ![Azure Machine Learning Importgegevens][17]
2. Selecteer **Azure SQL Database** als **gegevensbron** in het deelvenster **Eigenschappen.**
3. Voer de DNS-naam van de database in het veld **Databaseservernaam** in. Formaat:`tcp:<your_virtual_machine_DNS_name>,1433`
4. Voer de **naam van** de database in het bijbehorende veld in.
5. Voer de **SQL-gebruikersnaam** in de naam van het **serveraccount**en het **wachtwoord** in het wachtwoord van het **serveraccount in**.
7. Plak in het tekstgebied **Databasequery** bewerken de query waarmee de benodigde databasevelden worden geëxtraheerd (inclusief berekende velden zoals de labels) en de gegevens naar beneden naar de gewenste steekproefgrootte.

Een voorbeeld van een binair classificatie-experiment dat gegevens rechtstreeks uit de SQL Server-database leest, vindt u in de onderstaande afbeelding. Vergelijkbare experimenten kunnen worden gebouwd voor multiclass classificatie- en regressieproblemen.

![Azure Machine Learning-trein][10]

> [!IMPORTANT]
> In de voorbeelden van gegevensextractie en bemonsteringsquery's in vorige secties **worden alle labels voor de drie modelleringsoefeningen opgenomen in de query**. Een belangrijke (vereiste) stap in elk van de modellering oefeningen is het **uitsluiten** van de onnodige labels voor de andere twee problemen, en alle andere **doel lekken**. Gebruik bijvoorbeeld bij het gebruik van binaire classificatie het label **met de kop en** sluit de **tipklasse\_** velden, het **tipbedrag\_** en het totale **\_bedrag uit**. De laatste zijn doel lekken, omdat ze impliceren de tip betaald.
> 
> Als u onnodige kolommen en/of doellekken wilt uitsluiten, u de module [Kolommen selecteren in gegevensset][select-columns] of de [metagegevens bewerken][edit-metadata]gebruiken. Zie [Kolommen selecteren in gegevensset][select-columns] en [Metagegevens bewerken][edit-metadata] voor meer informatie.
> 
> 

## <a name="deploying-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Modellen implementeren in Azure Machine Learning
Wanneer uw model klaar is, u het eenvoudig implementeren als een webservice rechtstreeks vanuit het experiment. Zie [Een Webservice Azure Machine Learning implementeren](../studio/deploy-a-machine-learning-web-service.md)voor meer informatie over het implementeren van Azure Machine Learning-webservices.

Als u een nieuwe webservice wilt implementeren, moet u het volgende doen:

1. Maak een scoreexperiment.
2. Implementeer de webservice.

Als u een scoreexperiment wilt maken op **een voltooid** trainingsexperiment, klikt u op **SCORINGsEXPERIMENT MAKEN** op de onderste actiebalk.

![Azure-score][18]

Azure Machine Learning probeert een scoreexperiment te maken op basis van de onderdelen van het trainingsexperiment. In het bijzonder zal het:

1. Sla het getrainde model op en verwijder de modeltrainingsmodules.
2. Een logische **invoerpoort** identificeren om het schema voor verwachte invoergegevens weer te geven.
3. Een logische **uitvoerpoort** identificeren om het verwachte webserviceuitvoerschema weer te geven.

Wanneer het scoreexperiment wordt gemaakt, bekijkt u het en past u het aan indien nodig. Een typische aanpassing is om de invoergegevensset en/of query te vervangen door een gegevensset die labelvelden uitsluit, omdat deze labels niet beschikbaar zijn in het schema wanneer de service wordt aangeroepen. Het is ook een goede gewoonte om de grootte van de invoergegevensset en/of query te reduceren tot een paar records, genoeg om het invoerschema aan te geven. Voor de uitvoerpoort is het gebruikelijk om alle invoervelden uit te sluiten en alleen de **gescoorde labels** en **gescoorde waarschijnlijkheden** in de uitvoer op te nemen met de module [Kolommen selecteren in gegevensset.][select-columns]

Een voorbeeld score experiment is in de figuur hieronder. Wanneer u klaar bent om te worden geïmplementeerd, klikt u op de knop **WEBSERVICE PUBLICEREN** op de onderste actiebalk.

![Azure Machine Learning publiceren][11]

Om samen te vatten, hebt u in deze walkthrough-zelfstudie een Azure-gegevenswetenschapsomgeving gemaakt, gewerkt met een grote openbare gegevensset, van gegevensverzameling tot modeltraining en implementatie van een Azure Machine Learning-webservice.

### <a name="license-information"></a>Licentiegegevens
Deze voorbeeld-walkthrough en de bijbehorende scripts en IPython-notitieblokken worden door Microsoft gedeeld onder de MIT-licentie. Controleer het bestand LICENSE.txt in de map van de voorbeeldcode op GitHub voor meer informatie.

### <a name="references"></a>Verwijzingen
• [Andrés Monroy NYC Taxi Trips Download Pagina](https://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC's Taxi Trip Data door Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi en Limousine Commissie Onderzoek en Statistieken](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sql-walkthrough/azuremltrain.png
[11]: ./media/sql-walkthrough/azuremlpublish.png
[12]: ./media/sql-walkthrough/ssmsconnect.png
[13]: ./media/sql-walkthrough/executescript.png
[14]: ./media/sql-walkthrough/sqlserverproperties.png
[15]: ./media/sql-walkthrough/sqldefaultdirs.png
[16]: ./media/sql-walkthrough/bulkimport.png
[17]: ./media/sql-walkthrough/amlreader.png
[18]: ./media/sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
