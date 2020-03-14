---
title: Bouwen en implementeren van een model in een SQL Server-VM - Team Data Science Process
description: Bouw en implementeer een machine learning-model met behulp van SQL Server op een Azure-VM met een openbaar beschikbare gegevensset.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251581"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Het Team Data Science Process in actie: met behulp van SQL Server
In deze zelf studie doorloopt u het proces van het bouwen en implementeren van een machine learning model met behulp van SQL Server en een openbaar beschik bare gegevensset, de NYC-gegevensset voor de [taxi-trips](https://www.andresmh.com/nyctaxitrips/) . De procedure volgt een standaard gegevenswetenschapwerkstroom: opnemen en Verken de gegevens, functies, dingen en vervolgens bouwen en implementeren van een model bouwen.

## <a name="dataset"></a>Beschrijving van gegevensset voor NYCe taxi trips
De NYC-gegevens over de taxi zijn ongeveer 20 GB aan gecomprimeerde CSV-bestanden (~ 48 GB niet-gecomprimeerd), bestaande uit meer dan 173.000.000 afzonderlijke reizen en de voor elke reis betaalde tarieven. Elke record van de fietstocht bevat de locatie van ophalen en dropoff en tijd, geanonimiseerde hack (van het stuurprogramma) licentienummer en straten (unieke id van taxi) getal. De gegevens bevat informatie over alle gegevens in het jaar 2013 en is beschikbaar in de volgende twee gegevenssets voor elke maand:

1. Trip_data CSV bevat reis details, zoals het aantal personen, ophalen en dropoff punten, duur van de tocht en lengte van de fietstocht. Hier volgen enkele voorbeeldrecords:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. De 'trip_fare' CSV bevat details van het tarief voor elke reis, zoals betalingstype, fare bedrag, toeslag en belastingen, tips en tolwegen, betaald en de totale hoeveelheid betaald. Hier volgen enkele voorbeeldrecords:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

De unieke sleutel voor deelname aan de reis\_gegevens en reis\_ritbedrag bestaat uit de velden: Medallion, Hack\_Licensing en pickup\_DateTime.

## <a name="mltasks"></a>Voor beelden van voorspellings taken
We zullen drie Voorspellings problemen formuleren op basis van het *aantal fooien\_* , te weten:

* Binaire classificatie: er wordt voor speld dat er al dan niet een tip voor een reis is betaald, dat wil zeggen een *tip\_bedrag* dat groter is dan $0 een positief voor beeld is, terwijl een *Tip\_bedrag* van $0 een negatief voor beeld is.
* Multiklassen classificatie: om te voorspellen van het bereik van de tip betaald voor de reis. De *tip\_hoeveelheid* wordt verdeeld over vijf bakken of klassen:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
* Regressie taak: om te voorspellen van de hoeveelheid tip voor een reis betaald.  

## <a name="setup"></a>De Azure data Science-omgeving instellen voor geavanceerde analyse
Zoals u kunt zien in de hand leiding voor het [plannen van uw omgeving](plan-your-environment.md) , zijn er verschillende opties voor het werken met de gegevensset NYCe taxi trips in Azure:

* Werken met de gegevens in Azure-blobs en vervolgens in Azure Machine Learning-model
* Laad de gegevens in een SQL Server-database en het model in Azure Machine Learning

In deze zelf studie wordt gebruikgemaakt van de parallelle bulkimportbewerking van de gegevens tot een SQL Server, het verkennen van functies, het samen stellen van onderdelen en het bemonsteren van SQL Server Management Studio en het gebruik van een IPython-notebook. [Voorbeeld scripts](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) en [IPython-notebooks](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) worden gedeeld in github. Een voorbeeld IPython notebook wilt werken met de gegevens in Azure-blobs is ook beschikbaar in dezelfde locatie.

Uw Azure Data Science-omgeving instellen:

1. [Een opslagaccount maken](../../storage/common/storage-account-create.md)
2. [Een Azure Machine Learning-werk ruimte maken](../studio/create-workspace.md)
3. [Richt een Data Science virtual machine](../data-science-virtual-machine/setup-sql-server-virtual-machine.md)in dat een SQL Server en een IPython notebook server biedt.
   
   > [!NOTE]
   > De voorbeeldscripts en IPython-notitieblokken worden gedownload naar uw Data Science virtual machine tijdens de installatie. Wanneer de VM-script voor na de installatie is voltooid, worden de voorbeelden in de bibliotheek documenten van de virtuele machine:  
   > 
   > * Voorbeeld scripts: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Voor beelden van IPython-notebooks: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   waarbij `<user_name>` de Windows-aanmeldings naam van uw virtuele machine is. We verwijzen naar de voorbeeld mappen als **voorbeeld scripts** en voor **beelden van IPython-notebooks**.
   > 
   > 

Dit scenario is gebaseerd op de grootte van de gegevensset, de locatie van de gegevens bron en de geselecteerde Azure-doel omgeving en is vergelijkbaar met het [scenario \#5: grote gegevensset in een lokale bestanden, doel-SQL Server in azure VM](plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>De gegevens ophalen uit de open bare bron
Als u de [NYC](https://www.andresmh.com/nyctaxitrips/) van een taxi wilt ophalen uit de open bare locatie, kunt u een van de methoden die worden beschreven in [gegevens verplaatsen van en naar Azure Blob Storage](move-azure-blob.md) gebruiken om de gegevens naar de nieuwe virtuele machine te kopiëren.

Om te kopiëren van gegevens met AzCopy:

1. Meld u aan bij uw virtuele machine (VM)
2. Een nieuwe map maken in de gegevens schijf van de virtuele machine (Opmerking: gebruik niet de tijdelijke schijf die als een gegevens schijf wordt geleverd bij de virtuele machine).
3. Voer de volgende Azcopy-opdrachtregel, < path_to_data_folder > vervangen door de gegevensmap van uw in (2) gemaakt in een opdrachtpromptvenster:
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    Wanneer de AzCopy is voltooid, moet een totaal van 24 gecomprimeerde CSV-bestanden (12 voor reis\_gegevens en 12 voor reis\_ritbedrag) zich in de gegevensmap bevinden.
4. Pak het gedownloade bestanden. Houd er rekening mee de map waar de niet-gecomprimeerde bestanden zich bevinden. In deze map wordt het < pad genoemd\_naar\_data\_bestanden\>.

## <a name="dbload"></a>Gegevens bulksgewijs importeren in SQL Server Data Base
De prestaties van het laden/overbrengen van grote hoeveel heden gegevens naar een SQL Database en volgende query's kunnen worden verbeterd door *gepartitioneerde tabellen en weer gaven*te gebruiken. In deze sectie volgen we de instructies die worden beschreven in [parallelle bulksgewijze gegevens import met behulp van SQL-partitie tabellen](parallel-load-sql-partitioned-tables.md) om een nieuwe Data Base te maken en de gegevens parallel in gepartitioneerde tabellen te laden.

1. Start **SQL Server Management Studio**als u bent aangemeld bij uw VM.
2. Verbinding maken met behulp van Windows-verificatie.
   
    ![SSMS verbinden][12]
3. Als u de SQL Server-verificatie modus nog niet hebt gewijzigd en een nieuwe SQL-aanmeldings gebruiker hebt gemaakt, opent u het script bestand met de naam **change\_auth. SQL** in de map **voorbeeld scripts** . De standaard-gebruikersnaam en het wachtwoord wijzigen. Klik op **uitvoeren** in de werk balk om het script uit te voeren.
   
    ![Script uitvoeren][13]
4. Controleren en/of de SQL Server-database en logboekbestanden standaardmappen om ervoor te zorgen dat zojuist gemaakte databases worden opgeslagen in een gegevensschijf te wijzigen. De SQL Server VM-installatie kopie die is geoptimaliseerd voor het laden van gegevens opslag is vooraf geconfigureerd met gegevens-en logboek schijven. Als uw virtuele machine een gegevensschijf niet bevat en u nieuwe virtuele harde schijven tijdens het installatieproces van de virtuele machine toegevoegd, wijzigt u de standaard-mappen als volgt:
   
   * Klik met de rechter muisknop op de naam van de SQL Server in het linkerdeel venster en klik op **Eigenschappen**.
     
       ![Eigenschappen van SQL Server][14]
   * Selecteer **Data Base-instellingen** in de lijst **Selecteer een pagina** aan de linkerkant.
   * Controleer en/of wijzig de **standaard locaties van de data base** naar de locatie van de **gegevens schijf** van uw keuze. Deze locatie is waar nieuwe data bases zich bevinden als ze worden gemaakt met de standaard instellingen.
     
       ![Standaardinstellingen voor SQL Database][15]  
5. Als u een nieuwe data base en een set bestands groepen wilt maken voor de gepartitioneerde tabellen, opent u het voorbeeld script **create\_db\_default. SQL**. Met het script wordt een nieuwe data base gemaakt met de naam **TaxiNYC** en 12 bestands groepen in de standaard locatie van gegevens. Elke bestands groep zal één maand reis bevatten\_gegevens en reis\_ritbedrag-gegevens. Indien gewenst, kunt u de naam van de database wijzigen. Klik op **uitvoeren** om het script uit te voeren.
6. Maak vervolgens twee partitie tabellen, één voor de reis\_gegevens en een voor de reis\_tarief. Open het voorbeeld script **create\_gepartitioneerd\_Table. SQL.** dit zal:
   
   * Maak een partitiefunctie voor het splitsen van de gegevens per maand.
   * Maak een partitieschema van elke maand om gegevens te koppelen aan een andere bestandsgroep.
   * Maak twee gepartitioneerde tabellen die zijn toegewezen aan het partitie schema: **nyctaxi\_trip** houdt de reis\_gegevens en **nyctaxi\_ritbedrag** de reis\_gegevens over het ritbedrag bevat.
     
     Klik op **uitvoeren** om het script uit te voeren en de gepartitioneerde tabellen te maken.
7. In de map **voorbeeld scripts** zijn er twee voor beelden van Power shell-scripts voor het demonstreren van parallelle bulk invoer van gegevens aan SQL Server tabellen.
   
   * **bcp\_parallel\_algemeen. ps1** is een algemeen script voor het parallel importeren van gegevens in een tabel. Met dit script om in te stellen van de invoer- en doel-variabelen, zoals aangegeven in de opmerkingen in het script wijzigen.
   * **bcp\_parallel\_nyctaxi. ps1** is een vooraf geconfigureerde versie van het algemene script en kan worden gebruikt om beide tabellen te laden voor de gegevens van de NYC taxi-reizen.  
8. Klik met de rechter muisknop op de script naam **bcp\_parallel\_nyctaxi. ps1** en klik op **bewerken** om deze te openen in Power shell. Controleer de vooraf ingestelde variabelen en wijzig deze op basis van de geselecteerde database naam, invoer gegevensbestand, doelmap voor het doel logboek en de paden naar de voorbeeld bestanden **nyctaxi_trip. XML** en **nyctaxi\_ritbedrag. XML** (opgegeven in de map **voorbeeld scripts** ).
   
    ![Gegevens voor bulksgewijs importeren][16]
   
    U kunt ook de verificatiemodus selecteren, standaard Windows-verificatie. Klik op de groene pijl in de werkbalk om uit te voeren. Het script wordt gestart, 24 bulksgewijze importbewerkingen in parallelle, 12 voor elke gepartitioneerde tabel. U kunt de voortgang gegevens importeren door het openen van de standaardgegevensmap voor SQL Server als hierboven.
9. Het PowerShell-script rapporteert de begin- en eindtijd. Wanneer alle bulksgewijs importeren is voltooid, wordt de eindtijd gerapporteerd. Controleer de doelmap van het logboek om te controleren of de bulk invoer is geslaagd, dat wil zeggen dat er geen fouten worden gerapporteerd in de doelmap van het logboek.
10. Uw database is nu gereed voor verkennen, feature-engineering en andere bewerkingen naar wens. Omdat de tabellen zijn gepartitioneerd op basis van het veld **datum/tijd van de ophaal\_** , bevatten query's met de voor waarden voor **ophalen\_datum/tijd** in de **where** -component voor deel van het partitie schema.
11. Bekijk in **SQL Server Management Studio**het voor beeld van het voorbeeld script **\_query's. SQL**. Als u een van de voorbeeld query's wilt uitvoeren, markeert u de query regels en klikt u op in de werk balk op **uitvoeren** .
12. De NYC Taxi Trips-gegevens zijn geladen uit twee verschillende tabellen. Ter verbetering van join-bewerkingen, is het raadzaam om te indexeren van de tabellen. Het voorbeeld script **maakt\_gepartitioneerde\_index. SQL** maakt gepartitioneerde indexen voor de samengestelde samenvoegings sleutel **medallion, Hack\_license en pickup\_datetime**.

## <a name="dbexplore"></a>Het verkennen van gegevens en functies in SQL Server
In deze sectie wordt het verkennen van gegevens en het genereren van onderdelen uitgevoerd door SQL-query's rechtstreeks uit te voeren in het **SQL Server Management Studio** met behulp van de SQL Server-Data Base die u eerder hebt gemaakt. Een voorbeeld script met de naam voor **beeld-\_query's. SQL** is te zien in de map **voorbeeld scripts** . Wijzig het script om de naam van de data base te wijzigen als deze afwijkt van de standaard waarde: **TaxiNYC**.

In deze oefening we het volgende doen:

* Maak verbinding met **SQL Server Management Studio** met behulp van Windows-verificatie of met behulp van SQL-verificatie en de SQL-aanmeldings naam en het wacht woord.
* Verken gegevens distributies van enkele velden in verschillende tijdvensters.
* Onderzoek de kwaliteit van de gegevens van de velden breedtegraad en lengtegraad.
* Genereer binaire en classificatie labels voor multi klassen op basis van het **aantal fooien\_** .
* Functies genereren en reis afstanden compute/vergelijken.
* Voeg de twee tabellen en ophalen van een steekproef die worden gebruikt om modellen te bouwen.

Wanneer u klaar om door te gaan met Azure Machine Learning bent, kunt u een van:  

1. Sla de laatste SQL-query op om de gegevens te extra heren en voor te bereiden en kopieer de query rechtstreeks naar een [import gegevens][import-data] module in azure machine learning, of
2. Behoud de bemonsterde en ontworpen gegevens die u wilt gebruiken voor het maken van modellen in een nieuwe database tabel en gebruik de nieuwe tabel in de module [gegevens importeren][import-data] in azure machine learning.

In deze sectie wordt de laatste query opgeslagen om de gegevens op te halen en voor te bereiden. De tweede methode wordt gedemonstreerd in het gedeelte [voor het verkennen van gegevens en functies in IPython notebook](#ipnb) .

Voor een snelle controle van het aantal rijen en kolommen in de tabellen die eerder met behulp van parallelle bulkimport, ingevuld

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Verkennen: Verdeling reis straten
In dit voorbeeld wordt de straten (taxi getallen) geïdentificeerd met meer dan 100 trips binnen een bepaalde periode. De query zou profiteren van de gepartitioneerde tabel toegang, omdat deze wordt voor bereid op het partitie schema van **pickup\_datetime**. Uitvoeren van query's de volledige gegevensset maakt ook gebruik van de gepartitioneerde tabel en/of index-scan.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Verkennen: Reis distributie per straten en hack_license
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Gegevens van kwaliteit-beoordeling: Controleer of records met onjuiste lengtegraad en/of breedtegraad
In dit voorbeeld onderzoekt het probleem als een van de velden breedtegraad en/of breedtegraad een ongeldige waarde bevatten (radiaal graden moet tussen-90 en 90), of (0, 0) coördinaten.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Verkennen: gekanteld versus zonder gekantelde trips
In dit voorbeeld wordt gezocht naar het nummer van de gegevens die zijn punt versus geen punt in een bepaald moment periode (of in de volledige gegevensset als die betrekking hebben op het gehele jaar). Deze verdeling weerspiegelt de binaire labeldistributie moet later worden gebruikt voor binaire classificatie-modellen.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Verkennen: Tip-klasse/bereik distributie
In dit voorbeeld berekent de verdeling van de tip bereiken in een bepaalde periode (of in de volledige gegevensset als die betrekking hebben op het gehele jaar). Deze verdeling van de label klassen wordt later gebruikt voor het model leren van een classificatie met multi klassen.

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

#### <a name="exploration-compute-and-compare-trip-distance"></a>Verkennen: Compute en reis afstand vergelijken
In dit voorbeeld zet de ophalen en dropoff lengtegraad en breedtegraad in SQL-geo verwijst, de reis afstand met behulp van SQL Geografie punten verschil berekent en retourneert een steekproef van de resultaten voor vergelijking. Het voorbeeld de resultaten beperkt tot geldig coördinaten alleen met behulp van de kwaliteit evaluatie van de query die eerder besproken.

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

#### <a name="feature-engineering-in-sql-queries"></a>Feature-Engineering in SQL-query 's
De label generatie Geografie conversie verkennen query's en kunnen ook worden gebruikt voor het genereren van labels en-functies door de tellen deel te verwijderen. Aanvullende technische SQL-voor beelden voor functies vindt u in de sectie [gegevens verkennen en functie in IPython notebook](#ipnb) . Het is efficiënter om de query's voor het genereren van functies uit te voeren op de volledige gegevensset of een grote subset hiervan met behulp van SQL-query's die rechtstreeks worden uitgevoerd op het SQL Server Data Base-exemplaar. De query's kunnen worden uitgevoerd in **SQL Server Management Studio**, IPython notebook of een ontwikkel hulpprogramma of omgeving dat lokaal of op afstand toegang heeft tot de data base.

#### <a name="preparing-data-for-model-building"></a>Voorbereiden van gegevens voor het Model bouwen
Met de volgende query wordt de **nyctaxi-\_reis** -en **nyctaxi\_-ritbedrag** gegenereerd, wordt er een binaire classificatie label **gekanteld**, een classificatie label met meerdere klassen **\_klasse**, en wordt een wille keurig voor beeld van 1% geëxtraheerd uit de verzameling met volledige joins. Deze query kan worden gekopieerd en vervolgens rechtstreeks in de module [Azure machine learning Studio](https://studio.azureml.net) [gegevens importeren][import-data] worden geplakt voor directe gegevens opname vanuit het SQL Server Data Base-exemplaar in Azure. De query niet van toepassing op records met onjuiste (0, 0) coördinaten.

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


## <a name="ipnb"></a>Gegevens exploratie en functie techniek in IPython notebook
In deze sectie gaat uitvoeren en gegevens verkennen en functie te genereren met behulp van Python en SQL-query's op de SQL Server-database eerder hebt gemaakt. Een voor beeld van een IPython-notebook met de naam **machine learning-data-Science-process-SQL-Story. ipynb** vindt u in de voor **beeld-IPython-notebooks** . Dit notitie blok is ook beschikbaar op [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

Wanneer u met big data werkt, volgt u deze aanbevolen procedure:

* Lees in een voorbeeld van de gegevens in het kader van een in-memory-gegevens.
* Sommige visualisaties en explorations met behulp van de samplinggegevens uitvoeren.
* Experimenteer met feature-engineering met behulp van de samplinggegevens.
* Voor grotere gegevensverkenning, gegevens manipuleren en feature-engineering, Python te gebruiken om uit te geven van SQL-query's rechtstreeks op de SQL Server-database in de Azure-VM.
* Bepaal de grootte van de steekproef te gebruiken voor het bouwen van Azure Machine Learning-model.

Wanneer u klaar bent om door te gaan met Azure Machine Learning, kunt u een van:  

1. Sla de laatste SQL-query op om de gegevens te extra heren en voor te bereiden en kopieer de query rechtstreeks naar een [import gegevens][import-data] module in azure machine learning. Deze methode wordt gedemonstreerd in de sectie [buil ding modellen in azure machine learning](#mlmodel) .    
2. Behoud de voorbeeld gegevens die u wilt gebruiken voor het maken van modellen in een nieuwe database tabel en gebruik vervolgens de nieuwe tabel in de module [gegevens importeren][import-data] .

Hier volgen enkele gegevens verkennen, gegevensvisualisatie en functie-engineering-voorbeelden. Zie voor meer voor beelden het voor beeld van een SQL IPython-notebook in de map **IPython notebooks** .

#### <a name="initialize-database-credentials"></a>Databasereferenties initialiseren
Initialiseren van de instellingen van uw database-verbinding in de volgende variabelen:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Maak verbinding met Database
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

* Totale aantal rijen 173179759 =  
* Totale aantal kolommen = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Lees in een kleine steekproef van de SQL Server-Database
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

Tijd voor het lezen van dat de tabel is 6.492000 seconden  
Aantal rijen en kolommen opgehaald = (84952, 21)

#### <a name="descriptive-statistics"></a>Beschrijvende statistieken
U bent er nu klaar om de sample gegevens te verkennen. We beginnen met het bekijken van de beschrijvende statistieken voor de **reis\_afstand** (of een andere) veld (en):

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Visualisatie: Voorbeeld van Plot
Nu we eens kijken naar de BoxPlot voor de reis-afstand tot de quantiles visualiseren

    df1.boxplot(column='trip_distance',return_type='dict')

![Tekenen van #1][1]

#### <a name="visualization-distribution-plot-example"></a>Visualisatie: Voorbeeld van de distributie tekengebied
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Tekenen van #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Visualisatie: De balk en de regel grafieken
In dit voorbeeld we de afstand reis naar vijf opslaglocaties bin en het binning resultaten te visualiseren.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

We kunnen tekenen van de bovenstaande bin distributie in een staaf of lijn diagram als hieronder

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Tekenen van #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![#4 tekenen][4]

#### <a name="visualization-scatterplot-example"></a>Visualisatie: Voorbeeld van de Teststappen
We tonen het spreidings diagram tussen **reis\_tijd\_in\_seconden** en **reis\_afstand** om te zien of er een correlatie is

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![#6 tekenen][6]

Op dezelfde manier kunnen we de relatie controleren tussen het **tarief\_code** en **reis\_afstand**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![#8 tekenen][8]

### <a name="sub-sampling-the-data-in-sql"></a>De gegevens in SQL steekproeven subplan
Wanneer u gegevens voorbereidt voor het maken van modellen in [Azure machine learning Studio](https://studio.azureml.net), kunt u ervoor kiezen **om de SQL-query rechtstreeks te gebruiken in de module gegevens importeren** of de ontworpen en voorbeeld gegevens in een nieuwe tabel te bewaren, die u in de module [gegevens importeren][import-data] kunt gebruiken met een eenvoudige **SELECT * van < uw\_nieuwe\_tabel\_naam >** .

In deze sectie maakt u een nieuwe tabel voor de bemonsterde en engineerde gegevens. Een voor beeld van een rechtstreekse SQL-query voor het maken van modellen is te vinden in de sectie [voor het verkennen van gegevens en functies in SQL Server](#dbexplore) .

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Maken van een voorbeeld van een tabel en vullen met 1% van de gekoppelde tabellen. Verwijderen in de eerste tabel als deze bestaat.
In deze sectie voegen we de tabellen **nyctaxi\_trip** en **nyctaxi\_ritbedrag**toe, extraheert u een wille keurige steek proef van 1% en bewaart u de voorbeeld gegevens in een nieuwe tabel naam **nyctaxi\_één\_percentage**:

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

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Gegevens verkennen met behulp van SQL-query's in IPython Notebook
In deze sectie verkennen we gegevens distributies met behulp van de 1% voorbeeld gegevens die zijn opgeslagen in de nieuwe tabel die hierboven is gemaakt. Vergelijk bare exploratie kan worden uitgevoerd met behulp van de oorspronkelijke tabellen, eventueel met behulp van **TABLESAMPLE** om het voorbereidings voorbeeld te beperken of door de resultaten te beperken tot een bepaalde periode met behulp van de **ophaal\_datetime** -partities, zoals wordt geïllustreerd in het gedeelte over het [verkennen van gegevens en functies in SQL Server](#dbexplore) .

#### <a name="exploration-daily-distribution-of-trips"></a>Verkennen: Dagelijkse distributie van trips
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Verkennen: Reis-distributie per straten
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Functie te genereren met behulp van SQL-query's in IPython Notebook
In deze sectie er nieuwe labels worden gegenereerd en functies die rechtstreeks met behulp van SQL-query's, op de tabel % 1 wordt gemaakt in de vorige sectie.

#### <a name="label-generation-generate-class-labels"></a>Label-generatie: Labels van de klasse te genereren
In het volgende voorbeeld wordt er twee sets met labels moet worden gebruikt voor het maken van modellering gegenereerd:

1. Binaire klassen labels worden **gekanteld** (voor speld als er een tip wordt gegeven)
2. **Tip\_klasse** voor het labelen van labels (voor speling van de tip-bin of-bereik)
   
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

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Feature-Engineering: Aantal functies voor Categorische kolommen
In dit voorbeeld transformeert een categorische veld naar een numeriek veld door elke categorie vervangen door het aantal van de instanties in de gegevens.

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

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Feature-Engineering: Bin functies voor numerieke kolommen
In dit voor beeld wordt een doorlopend numeriek veld getransformeerd naar een vooraf ingestelde categorie bereik, dat wil zeggen numeriek veld transformeren naar een categorische-veld.

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

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Feature-Engineering: Locatie functies ophalen uit de Decimal breedtegraad/lengtegraad
In dit voor beeld wordt de decimale weer gave van een breedte-en/of lengte veld verdeeld in meerdere regio velden van verschillende granulariteit, zoals land/regio, plaats, steen, blok, enzovoort. De nieuwe geo-velden zijn niet toegewezen aan de werkelijke locaties. Zie voor meer informatie over het toewijzen van Geocode-locaties [Bing Maps rest-Services](https://msdn.microsoft.com/library/ff701710.aspx).

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

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Controleer of de laatste vorm van de tabel boommodel
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

U kunt nu door gaan met het model leren van het bouwen en model implementeren in [Azure machine learning](https://studio.azureml.net). De gegevens zijn gereed voor de voorspelling problemen geïdentificeerd lager, namelijk:

1. Binaire classificatie: om te voorspellen of een tip is betaald voor een reis.
2. Multiklassen classificatie: om te voorspellen van het bereik van de tip betaald, op basis van de eerder gedefinieerde klassen.
3. Regressie taak: om te voorspellen van de hoeveelheid tip voor een reis betaald.  

## <a name="mlmodel"></a>Modellen bouwen in Azure Machine Learning
Als u wilt de oefening modellen, moet u zich aanmelden bij uw Azure Machine Learning-werkruimte. Als u nog geen machine learning-werk ruimte hebt gemaakt, raadpleegt u [een Azure machine learning-werk ruimte maken](../studio/create-workspace.md).

1. Zie [Wat is er Azure machine learning Studio](../studio/what-is-ml-studio.md) om aan de slag te gaan met Azure machine learning?
2. Meld u aan bij [Azure machine learning Studio](https://studio.azureml.net).
3. De startpagina van Studio biedt een schat aan informatie, video's, zelfstudies en koppelingen naar de Modules-verwijzing en andere bronnen. Raadpleeg het [Azure machine learning Documentation Center](https://azure.microsoft.com/documentation/services/machine-learning/)voor meer informatie over Azure machine learning.

Een typische trainingsexperiment bestaat uit de volgende stappen uit:

1. Maak een **Nieuw** experiment.
2. Krijg de gegevens voor Azure Machine Learning.
3. De gegevens vooraf verwerken, transformeren en manipuleren als dat nodig is.
4. Functies genereren indien nodig.
5. De gegevens splitsen in gegevenssets training/validatie/testen (of afzonderlijke gegevenssets hebben voor elk).
6. Selecteer een of meer machine learning-algoritmen, afhankelijk van het leerprobleem om op te lossen. Bijvoorbeeld binaire classificatie, classificatie met meer klassen, regressie.
7. Een of meer modellen met behulp van de gegevensset training trainen.
8. De validatie-gegevensset met behulp van het getrainde modellen te beoordelen.
9. De modellen voor het berekenen van de relevante metrische gegevens voor het leerprobleem evalueren.
10. Stem de model (len) af en selecteer het beste model om te implementeren.

In deze oefening hebben we al verkend en ontworpen van de gegevens in SQL Server en op de grootte van de steekproef besloten om op te nemen in Azure Machine Learning. We hebben besloten om een of meer van de Voorspellings modellen te bouwen:

1. De gegevens van Azure Machine Learning ophalen met behulp van [de module gegevens][import-data] **invoer en-uitvoer** die beschikbaar is. Zie de pagina referentie gegevens module [importeren][import-data] voor meer informatie.
   
    ![Gegevens van Azure Machine Learning importeren][17]
2. Selecteer **Azure SQL database** als **gegevens bron** in het deel venster **Eigenschappen** .
3. Voer de naam van de data base-DNS in het veld **database server naam** in. Indeling: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Voer de **database naam** in het bijbehorende veld in.
5. Voer de **SQL-gebruikers naam** in de naam van de **Server gebruikers account**en het **wacht woord** in het **wacht woord van de server gebruikers account**in.
7. Plak in het tekst gebied **database query** bewerken de query waarmee de benodigde database velden worden geëxtraheerd (met inbegrip van berekende velden zoals de labels) en druk op voor beelden van de gegevens naar de gewenste steekproef grootte.

Een voorbeeld van een binaire classificatie-experiment lezen van gegevens rechtstreeks vanuit de SQL Server-database is in de afbeelding hieronder. Vergelijkbare experimenten kunnen worden samengesteld voor multiklassen classificatie en regressie problemen.

![Azure Machine Learning Train][10]

> [!IMPORTANT]
> In de voor beelden van model gegevens extractie en bemonsterings query's in de vorige secties **zijn alle labels voor de drie model oefeningen opgenomen in de query**. Een belang rijke (vereiste) stap in elk van de modellerings oefeningen is het **uitsluiten** van de overbodige labels voor de andere twee problemen en eventuele andere **doel lekkages**. Als u bijvoorbeeld een binaire classificatie gebruikt, gebruikt u het label dat wordt **gekanteld** en sluit u de velden **Tip\_klasse**, **tip\_hoeveelheid**en **totale\_bedrag**. De laatste zijn doel lekken omdat ze de tip impliceren betaald.
> 
> Als u onnodige kolommen en/of doel lekkages wilt uitsluiten, kunt u de module [kolommen selecteren in gegevensset][select-columns] of de [meta gegevens bewerken][edit-metadata]gebruiken. Zie [kolommen selecteren in gegevensset][select-columns] en referentie pagina's voor [meta gegevens bewerken][edit-metadata] voor meer informatie.
> 
> 

## <a name="mldeploy"></a>Modellen implementeren in Azure Machine Learning
Als uw model klaar is, kunt u deze eenvoudig implementeren als een webservice rechtstreeks vanuit het experiment. Zie [een Azure machine learning-webservice implementeren](../studio/deploy-a-machine-learning-web-service.md)voor meer informatie over het implementeren van Azure machine learning-webservices.

Als u wilt een nieuwe webservice implementeert, moet u naar:

1. Een scoring-experiment maken.
2. De webservice implementeren.

Als u een score experiment wilt maken op basis van een **voltooid** trainings experiment, klikt u op Score voor punten **maken** in de onderste actie balk.

![Azure scoren][18]

Azure Machine Learning wordt geprobeerd te maken van een scoring-experiment op basis van de onderdelen van het trainingsexperiment. In het bijzonder wordt:

1. Opslaan van het getrainde model en het verwijderen van de training model modules.
2. Identificeer een logische **invoer poort** om het verwachte invoer gegevens schema weer te geven.
3. Identificeer een logische **uitvoer poort** om het verwachte uitvoer schema van de webservice weer te geven.

Als u het scoring experiment maakt, beoordelen en naar wens aanpassen. Een typische aanpassing is het vervangen van de invoer-gegevensset en/of de query met één die label velden uitsluit omdat deze labels niet beschikbaar zijn in het schema wanneer de service wordt aangeroepen. Het is ook een goed idee om de grootte van de invoer-gegevensset en/of de query te verkleinen naar enkele records, genoeg om het invoer schema aan te duiden. Voor de uitvoer poort is het gebruikelijk om alle invoer **velden uit te sluiten en alleen** de **gescoorde labels** op te nemen in de uitvoer met behulp van de module [select columns in dataset][select-columns] .

Er is een voorbeeld van een experiment scores in de afbeelding hieronder. Wanneer u klaar bent om te implementeren, klikt u op de knop **PUBLISH web service** in de onderste actie balk.

![Azure Machine Learning publiceren][11]

Als u wilt samenvatting in deze zelfstudie scenario kunt u een Azure data science-omgeving heeft gewerkt met een grote openbare gegevensset van gegevens ophalen om modellen te trainen en implementeren van een Azure Machine Learning-webservice hebt gemaakt.

### <a name="license-information"></a>Licentie-informatie
In dit voorbeeld scenario en de bijbehorende scripts en IPython notebook(s) worden gedeeld door Microsoft onder de MIT-licentie. Controleer het bestand LICENSE. txt in de map van de voorbeeld code op GitHub voor meer informatie.

### <a name="references"></a>Verwijzingen
• [Download pagina voor Andrés Monroy NYCe taxi](https://www.andresmh.com/nyctaxitrips/)  
• [De taxi gegevens van NYC door Chris Whong te folie](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC van de taxi en limousine](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page) van de Commissie

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
