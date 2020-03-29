---
title: Data science met Spark op Azure HDInsight - Team Data Science Process
description: De Spark MLlib toolkit biedt aanzienlijke machine learning modelleringsmogelijkheden aan de gedistribueerde HDInsight-omgeving.
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
ms.openlocfilehash: 63148b99e65a5ccc49d54d4ae6c58adebc72c6d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718511"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Overzicht van gegevenswetenschap met Spark op Azure HDInsight

Deze reeks onderwerpen laat zien hoe u HDInsight Spark gebruiken om algemene gegevenswetenschappelijke taken te voltooien, zoals gegevensopname, functieengineering, modellering en modelevaluatie. De gebruikte gegevens is een voorbeeld van de 2013 NYC taxi reis en tarief dataset. De modellen gebouwd omvatten logistieke en lineaire regressie, willekeurige bossen, en gradiënt gestimuleerd bomen. De onderwerpen laten ook zien hoe u deze modellen opslaan in Azure blob-opslag (WASB) en hoe u hun voorspellende prestaties scoren en evalueren. Meer geavanceerde onderwerpen gaan over hoe modellen kunnen worden getraind met behulp van cross-validatie en hyper-parameter vegen. In dit overzichtsonderwerp wordt ook verwezen naar de onderwerpen waarin wordt beschreven hoe u het Spark-cluster instelt dat u nodig hebt om de stappen in de opgegeven walkthroughs uit te voeren.

## <a name="spark-and-mllib"></a>Vonk en MLlib
[Spark](https://spark.apache.org/) is een open-source parallel verwerkingsframework dat in-memory processing ondersteunt om de prestaties van big-data analytische toepassingen te verbeteren. De Spark processing engine is gebouwd voor snelheid, gebruiksgemak en geavanceerde analyses. Spark's in-memory gedistribueerde rekenmogelijkheden maken het een goede keuze voor de iteratieve algoritmen die worden gebruikt in machine learning en grafiekberekeningen. [MLlib](https://spark.apache.org/mllib/) is spark's schaalbare machine learning-bibliotheek die de algoritmische modelleringsmogelijkheden naar deze gedistribueerde omgeving brengt.

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) is het azure-gehoste aanbod van open-source Spark. Het bevat ook ondersteuning voor **Jupyter PySpark-notitieblokken** op het Spark-cluster waarmee interactieve Spark SQL-query's kunnen worden uitgevoerd voor het transformeren, filteren en visualiseren van gegevens die zijn opgeslagen in Azure Blobs (WASB). PySpark is de Python API voor Spark. De codefragmenten die de oplossingen bieden en de relevante plots weergeven om de gegevens hier te visualiseren, worden uitgevoerd in Jupyter-notitieblokken die zijn geïnstalleerd op de Spark-clusters. De modelleringsstappen in deze onderwerpen bevatten code die laat zien hoe u elk type model traint, evalueert, opslaat en verbruikt.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Setup: Spark-clusters en Jupyter-notitieblokken
Setup stappen en code zijn voorzien in deze walkthrough voor het gebruik van een HDInsight Spark 1.6. Maar Jupyter-laptops zijn beschikbaar voor zowel HDInsight Spark 1.6- als Spark 2.0-clusters. Een beschrijving van de notitieblokken en links naar hen zijn voorzien in de [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) voor de GitHub repository met hen. Bovendien is de code hier en in de gekoppelde notitieblokken generiek en moet het werken op een Spark-cluster. Als u HDInsight Spark niet gebruikt, kunnen de clusterinstellingen en beheerstappen enigszins afwijken van wat hier wordt weergegeven. Voor het gemak, hier zijn de links naar de Jupyter notebooks voor Spark 1.6 (te draaien in de pySpark kernel van de Jupyter Notebook server) en Spark 2.0 (worden uitgevoerd in de pySpark3 kernel van de Jupyter Notebook server):

### <a name="spark-16-notebooks"></a>Spark 1.6-notitieblokken
Deze notebooks worden uitgevoerd in de pySpark-kernel van jupyter-laptopserver.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb)Biedt informatie over het uitvoeren van gegevensverkenning, modellering en scoren met verschillende algoritmen.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Bevat onderwerpen in notebook #1 en modelontwikkeling met behulp van hyperparameter tuning en cross-validatie.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): Laat zien hoe je een opgeslagen model operationaliseren met Python op HDInsight-clusters.

### <a name="spark-20-notebooks"></a>Spark 2.0-notitieblokken
Deze notebooks worden uitgevoerd in de pySpark3-kernel van Jupyter-laptopserver.

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)Dit bestand biedt informatie over het uitvoeren van gegevensverkenning, modellering en scoren in Spark 2.0-clusters met behulp van de NYC Taxi-reis en tariefgegevensset [die hier](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data)worden beschreven. Deze notebook kan een goed uitgangspunt zijn voor het snel verkennen van de code die we hebben verstrekt voor Spark 2.0. Voor een meer gedetailleerde notebook analyseert de NYC Taxi gegevens, zie de volgende notebook in deze lijst. Bekijk de notities die deze lijst volgen en die deze notitieblokken vergelijkt.
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Dit bestand laat zien hoe je data wrangling (Spark SQL en dataframe operaties), exploratie, modellering en scoren met behulp van de NYC Taxi reis en tarief data-set [hier](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data)beschreven uit te voeren .
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Dit bestand laat zien hoe je datawrangling (Spark SQL en dataframe-bewerkingen) uitvoeren, verkenning, modellering en scoren met behulp van de bekende Airline On-time vertrekdataset uit 2011 en 2012. We hebben de gegevensset van de luchtvaartmaatschappij geïntegreerd met de weergegevens van de luchthaven (bijvoorbeeld windsnelheid, temperatuur, hoogte enz.) voorafgaand aan het modelleren, zodat deze weerfuncties in het model kunnen worden opgenomen.

<!-- -->

> [!NOTE]
> De gegevensset van de luchtvaartmaatschappij is toegevoegd aan de Spark 2.0-notitieblokken om het gebruik van classificatiealgoritmen beter te illustreren. Zie de volgende links voor informatie over de vluchtgegevensset voor vertrek van luchtvaartmaatschappijen en de gegevensset weer:
> 
> - Vluchtgegevens op tijd:[https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Luchthaven weergegevens:[https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> De Spark 2.0-laptops op de gegevenssets voor vertraging van de nyc-taxi en luchtvaartmaatschappij kunnen 10 minuten of meer duren (afhankelijk van de grootte van uw HDI-cluster). De eerste notebook in de bovenstaande lijst toont vele aspecten van de data-exploratie, visualisatie en ML model training in een notebook die minder tijd kost om te draaien met down-sampled NYC dataset, waarin de taxi en tarief bestanden zijn vooraf verbonden: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb). Deze notebook duurt een veel kortere tijd om te voltooien (2-3 minuten) en kan een goed uitgangspunt voor het snel verkennen van de code die we hebben verstrekt voor Spark 2.0.

<!-- -->

Zie het [Spark 1.6-document over het verbruik](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) voor een voorbeeld waarin de vereiste stappen worden beschreven voor richtlijnen voor de operationalisering van een Spark 2.0-model en modelverbruik voor het scoren. Als u dit voorbeeld wilt gebruiken op Spark 2.0, vervangt u het Python-codebestand door [dit bestand](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Vereisten

De volgende procedures zijn gerelateerd aan Spark 1.6. Gebruik voor de Spark 2.0-versie de notitieblokken die eerder zijn beschreven en gekoppeld.

1. U hebt een abonnement op Azure nodig. Zie [Gratis proefversie van Azure,](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)als u er nog geen hebt.

2. Je hebt een Spark 1.6-cluster nodig om deze walkthrough te voltooien. Zie de instructies in Aan de slag om er een te [maken: Maak Apache Spark op Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Het clustertype en de versie is opgegeven in het menu **Clustertype selecteren.**

![Cluster configureren](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Zie de [Data Science met Scala met Spark op Azure](scala-walkthrough.md)voor een onderwerp dat laat zien hoe u Scala in plaats van Python gebruiken om taken te voltooien voor een end-to-end data science-proces.
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>De NYC 2013 Taxi gegevens
De NYC Taxi Trip gegevens is ongeveer 20 GB van gecomprimeerde komma-gescheiden waarden (CSV) bestanden (~ 48 GB ongecomprimeerd), bestaande uit meer dan 173 miljoen individuele reizen en de tarieven betaald voor elke reis. Elke reis record omvat de pick-up en dropoff locatie en tijd, geanonimiseerde hack (driver's) licentienummer en medaillon (unieke id taxi's) nummer. De gegevens hebben betrekking op alle reizen in het jaar 2013 en worden verstrekt in de volgende twee gegevenssets voor elke maand:

1. De CSV-bestanden 'trip_data' bevatten reisgegevens, zoals het aantal passagiers, ophaal- en afleverpunten, reisduur en reisduur. Hier zijn een paar voorbeeldrecords:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. De CSV-bestanden 'trip_fare' bevatten details over het tarief dat voor elke reis is betaald, zoals het betalingstype, het tariefbedrag, de toeslag en belastingen, tips en tolgelden en het totale betaalde bedrag. Hier zijn een paar voorbeeldrecords:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

We hebben een voorbeeld van 0,1% van\_deze bestanden\_genomen en zijn samengevoegd met de reisgegevens en cvs-bestanden voor reistarieven in één gegevensset om te gebruiken als invoergegevensset voor deze walkthrough. De unieke sleutel\_om deel\_te nemen reisgegevens en reistarief\_bestaat\_uit de velden: medaillon, hack licentie en pick-up datetime. Elke record van de gegevensset bevat de volgende kenmerken die een NYC-taxirit vertegenwoordigen:

| Veld | Korte beschrijving |
| --- | --- |
| Medaillon |Geanonimiseerdtaximedaillon (unieke taxi-id) |
| hack_license |Geanonimiseerd hackney carriage licentienummer |
| vendor_id |Taxiverkoper-id |
| rate_code |NYC taxitarief van tarief |
| store_and_fwd_flag |Vlag opslaan en doorsturen |
| pickup_datetime |Ophaaldatum & tijd |
| dropoff_datetime |Afgiftedatum & tijd |
| pickup_hour |Ophaaluur |
| pickup_week |Afhaalweek van het jaar |
| Weekdag |Weekdag (bereik 1-7) |
| passenger_count |Aantal passagiers in een taxirit |
| trip_time_in_secs |Reistijd in seconden |
| trip_distance |Reisafstand afgelegd in mijlen |
| pickup_longitude |Pick-up longitude |
| pickup_latitude |Latitude ophalen |
| dropoff_longitude |Dropoff-verlenging |
| dropoff_latitude |Afgiftebreedte |
| direct_distance |Directe afstand tussen ophaal- en afleverlocaties |
| payment_type |Betalingstype (contant, creditcard enz.) |
| fare_amount |Tariefbedrag in |
| Toeslag |Toeslag |
| mta_tax |MTA Metro Transport belasting |
| tip_amount |Tipbedrag |
| tolls_amount |Tolbedrag |
| total_amount |Totaalbedrag |
| Getipt |Getipt (0/1 voor nee of ja) |
| tip_class |Tip klasse (0: $0, 1: $0-5, 2: $6-10, 3: $11-20, 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Code uitvoeren vanuit een Jupyter-notitieblok op het Spark-cluster
U de Jupyter-notitieblok starten vanuit de Azure-portal. Zoek uw Spark-cluster op uw dashboard en klik erop om de beheerpagina voor uw cluster in te voeren. Als u het notitieblok wilt openen dat is gekoppeld aan het Spark-cluster, klikt u op **Clusterdashboards** -> **Jupyter-notitieblok**.

![Clusterdashboards](./media/spark-overview/spark-jupyter-on-portal.png)

U ook ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** zoeken naar de Jupyter-notitieblokken. Vervang het CLUSTERNAME-gedeelte van deze URL door de naam van uw eigen cluster. Je hebt het wachtwoord nodig voor je beheerdersaccount om toegang te krijgen tot de notitieblokken.

![Blader door Jupyter-notitieblokken](./media/spark-overview/spark-jupyter-notebook.png)

Selecteer PySpark om een map te zien die een paar voorbeelden bevat van voorverpakte notitieblokken die de PySpark API gebruiken. De notitieblokken die de codevoorbeelden voor deze reeks Spark-onderwerpen bevatten, zijn beschikbaar op [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

U de notitieblokken rechtstreeks van [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) uploaden naar de Jupyter-notebookserver op uw Spark-cluster. Klik op de startpagina van uw Jupyter op de knop **Uploaden** aan de rechterkant van het scherm. Het opent een verkenner. Hier u de URL van GitHub (ruwe inhoud) van het notitieblok plakken en op **Openen**klikken.

U ziet de bestandsnaam op uw Jupyter-bestandslijst met opnieuw **een knop Uploaden.** Klik **op deze knop Uploaden.** Nu hebt u het notitieblok geïmporteerd. Herhaal deze stappen om de andere notitieblokken uit deze walkthrough te uploaden.

> [!TIP]
> U met de rechtermuisknop op de koppelingen in uw browser klikken en **Koppeling kopiëren** selecteren om de URL van de GitHub-raw-inhoud te krijgen. U deze URL plakken in het dialoogvenster Verkenner van Jupyter Upload.
> 
> 

U kunt nu:

* Bekijk de code door op het notitieblok te klikken.
* Voer elke cel uit door op **SHIFT-ENTER**te drukken .
* Voer het hele notitieblok uit door op **Cell** -> **Run**te klikken.
* Gebruik de automatische visualisatie van query's.

> [!TIP]
> De PySpark-kernel visualiseert automatisch de uitvoer van SQL -query's (HiveQL). U krijgt de optie om verschillende typen visualisaties (tabel, cirkel, lijn, gebied of balk) te selecteren met behulp van de menuknoppen **Type** in het notitieblok:
>
>

![Logistieke regressie ROC curve voor generieke aanpak](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Volgende stappen
Nu u bent ingesteld met een HDInsight Spark-cluster en de Jupyter-notitieblokken hebt geüpload, bent u klaar om de onderwerpen te doorlopen die overeenkomen met de drie PySpark-notitieblokken. Ze laten zien hoe u uw gegevens verkennen en vervolgens hoe u modellen maken en consumeren. De geavanceerde gegevens exploratie en modellering notebook laat zien hoe cross-validatie, hyper-parameter vegen, en model evaluatie op te nemen.

**Gegevensverkenning en modellering met Spark:** Verken de gegevensset en maak, beoordeel en evalueer de machine learning-modellen door te werken via de [binaire classificatie- en regressiemodellen maken voor gegevens met het Spark MLlib-toolkitonderwerp.](spark-data-exploration-modeling.md)

**Modelverbruik:** Zie [Spark-built machine learning-modellen voor](spark-model-consumption.md)meer informatie over het scoren van de classificatie- en regressiemodellen die in dit onderwerp zijn gemaakt.

**Cross-validatie en hyperparameter vegen:** Zie [Geavanceerde gegevens exploratie en modellering met Spark](spark-advanced-data-exploration-modeling.md) over hoe modellen kunnen worden getraind met behulp van cross-validatie en hyper-parameter vegen

