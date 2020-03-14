---
title: Hive-tabellen maken en gegevens laden uit Blob storage - Team Data Science Process
description: Hive-query's te maken van Hive-tabellen laden van gegevens uit Azure blob-opslag gebruiken. Hive-tabellen partitioneren en de geoptimaliseerde rij kolommen (ORC) te verbeteren, opmaak gebruiken.
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
ms.openlocfilehash: 625d9d5c5ecf095d4acbff625754b2065f184536
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251659"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Hive-tabellen maken en gegevens laden uit Azure Blob Storage

In dit artikel geeft algemene Hive-query's die Hive-tabellen maken en gegevens laden vanuit Azure blob storage. Sommige richtlijnen zijn beschikbaar op de Hive-tabellen partitioneren en over het gebruik van de geoptimaliseerd rij kolommen (ORC) te verbeteren, opmaak.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u hebt:

* Er is een Azure Storage-account gemaakt. Zie [over Azure Storage-accounts](../../storage/common/storage-introduction.md)als u instructies nodig hebt.
* Een aangepaste Hadoop-cluster met de HDInsight-service wordt ingericht.  Zie [installatie clusters in HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)als u instructies nodig hebt.
* Ingeschakelde externe toegang tot het cluster, aangemeld en de Hadoop-opdrachtregelconsole geopend. Zie [Apache Hadoop-clusters beheren](../../hdinsight/hdinsight-administer-use-portal-linux.md)als u instructies nodig hebt.

## <a name="upload-data-to-azure-blob-storage"></a>Gegevens uploaden naar Azure blob-opslag
Als u een virtuele machine van Azure hebt gemaakt met behulp van de instructies in [een virtuele Azure-machine instellen voor geavanceerde analyse](../../machine-learning/data-science-virtual-machine/overview.md), moet dit script bestand zijn gedownload naar de map *C:\\gebruikers\\\<gebruikers naam\>\\documenten\\data Science-script* Directory op de virtuele machine. Deze Hive-query's vereisen alleen dat u een gegevens schema en Azure Blob-opslag configuratie opgeeft in de juiste velden die kunnen worden verzonden.

We gaan ervan uit dat de gegevens voor Hive-tabellen een **gedecomprimeerde** tabel indeling hebben en dat de gegevens zijn geüpload naar de standaard container van het opslag account dat wordt gebruikt door het Hadoop-cluster.

Als u wilt oefenen met de **NYC**, moet u het volgende doen:

* **down load** de 24 [NYCe taxi-reis gegevens](https://www.andresmh.com/nyctaxitrips) bestanden (12 retour bestanden en 12-ritbedrag bestanden),
* **pak** alle bestanden uit in CSV-bestanden en klik vervolgens
* **Upload** deze naar de standaard (of geschikte container) van het Azure Storage-account; opties voor een dergelijk account worden weer gegeven bij [gebruik Azure Storage met het onderwerp Azure HDInsight-clusters](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) . Het proces voor het uploaden van de CSV-bestanden naar de standaard container op het opslag account vindt u op deze [pagina](hive-walkthrough.md#upload).

## <a name="submit"></a>Hive-query's verzenden
Hive-query's kunnen worden verzonden met behulp van:

* [Hive-query's verzenden via Hadoop-opdracht regel in hoofd knooppunt van Hadoop-cluster](#headnode)
* [Hive-query's verzenden met de Hive-editor](#hive-editor)
* [Hive-query's met Azure PowerShell-opdrachten verzenden](#ps)

Hive-query's zijn SQL-achtige. Als u bekend bent met SQL, is het mogelijk dat het [onderdeel voor SQL-gebruikers Cheat Sheet](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) nuttig is.

Bij het indienen van een Hive-query, kunt u ook de bestemming van de uitvoer van Hive-query's, beheren, ongeacht of deze op het scherm of een lokaal bestand op het hoofdknooppunt van of naar een Azure-blob.

### <a name="headnode"></a>Hive-query's verzenden via Hadoop-opdracht regel in hoofd knooppunt van Hadoop-cluster
Als de Hive-query complex is, leidt verzendt, wordt deze rechtstreeks in het hoofdknooppunt van het Hadoop cluster meestal tot sneller omlooptijd dan verzendt, wordt deze met een Hive-Editor of Azure PowerShell-scripts.

Meld u aan bij het hoofd knooppunt van het Hadoop-cluster, open de Hadoop-opdracht regel op het bureau blad van het hoofd knooppunt en voer de opdracht `cd %hive_home%\bin`in.

Hebt u drie manieren om in te dienen Hive-query's in de Hadoop-opdrachtregel:

* rechtstreeks
* '. HQL-bestanden gebruiken
* met de opdrachtconsole Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Indienen van Hive-query's rechtstreeks in Hadoop vanaf de opdrachtregel.
U kunt de opdracht uitvoeren als `hive -e "<your hive query>;` om eenvoudige Hive-query's rechtstreeks te verzenden in de Hadoop-opdracht regel. Hier volgt een voorbeeld, waarbij een rood kader geeft een overzicht van de opdracht die is ingediend door de Hive-query en de groene vakje geeft een overzicht van de uitvoer van de Hive-query.

![Opdracht voor het indienen van Hive-query met de uitvoer van Hive-query](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Hive-query's in. HQL-bestanden verzenden
Wanneer de Hive-query gecompliceerder is en meerdere regels heeft, is het bewerken van query's in vanaf de opdrachtregel of in de opdrachtconsole Hive het niet praktisch. U kunt ook een tekst editor gebruiken in het hoofd knooppunt van het Hadoop-cluster om de Hive-query's in een. HQL-bestand op te slaan in een lokale map van het hoofd knooppunt. Vervolgens kan de Hive-query in het bestand '. HQL ' als volgt worden verzonden met behulp van het argument `-f`:

    hive -f "<path to the '.hql' file>"

![Hive-query in een. HQL-bestand](./media/move-hive-tables/run-hive-queries-3.png)

**Scherm afdruk van voortgangs status onderdrukken van Hive-query's**

Standaard nadat Hive-query wordt verzonden in Hadoop vanaf de opdrachtregel, wordt de voortgang van de taak voor toewijzen/verminderen afgedrukt op het scherm. Als u de scherm afdruk van de voortgang van de taak toewijzing/verminderen wilt onderdrukken, kunt u als volgt een argument `-S` ("S" in hoofd letters) in de opdracht regel gebruiken:

    hive -S -f "<path to the '.hql' file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Indienen van Hive-query's in de opdrachtconsole Hive.
U kunt ook eerst de Hive-opdracht console invoeren door de opdracht `hive` in Hadoop opdracht regel uit te voeren en vervolgens Hive-query's in Hive-opdracht console te verzenden. Hier volgt een voorbeeld. In dit voorbeeld Markeer de twee rode vakken de opdrachten gebruikt voor het invoeren van de opdrachtconsole Hive en de Hive-query verzonden in de opdrachtconsole Hive, respectievelijk. De groene vakje markeert de uitvoer van de Hive-query.

![Open de console van Hive-opdracht en voer de opdracht, Hive-query-uitvoer weergeven](./media/move-hive-tables/run-hive-queries-2.png)

De eerdere voorbeelden uitvoer rechtstreeks van de resultaten van de Hive-query op het scherm. U kunt de uitvoer ook schrijven naar een lokaal bestand op het hoofdknooppunt, of naar een Azure-blob. Vervolgens kunt u andere hulpprogramma's voor verdere analyse van de uitvoer van Hive-query's.

**Resultaten van de Hive-query van de uitvoer naar een lokaal bestand.**
Als u wilt uitvoeren van resultaten van Hive-query naar een lokale map op het hoofdknooppunt, hebt u om in te dienen de Hive-query in de Hadoop-opdrachtregel als volgt:

    hive -e "<hive query>" > <local path in the head node>

In het volgende voor beeld wordt de uitvoer van Hive-query geschreven naar een bestand `hivequeryoutput.txt` in Directory `C:\apps\temp`.

![Uitvoer van Hive-query](./media/move-hive-tables/output-hive-results-1.png)

**Resultaten van de query van de component uitvoer naar een Azure-Blob**

U kunt ook de resultaten van de Hive-query naar een Azure-blob, binnen de standaardcontainer van het Hadoop-cluster uitvoeren. De Hive-query voor deze is als volgt:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

In het volgende voor beeld wordt de uitvoer van Hive-query geschreven naar een BLOB-map `queryoutputdir` binnen de standaard container van het Hadoop-cluster. Hier, hoeft u alleen voor de naam van de map zonder naam van de blob. Er wordt een fout gegenereerd als u namen van mappen en blobs opgeeft, zoals `wasb:///queryoutputdir/queryoutput.txt`.

![Uitvoer van Hive-query](./media/move-hive-tables/output-hive-results-2.png)

Als u de standaardcontainer van het Hadoop-cluster met behulp van Azure Storage Explorer opent, ziet u de uitvoer van de Hive-query, zoals wordt weergegeven in de volgende afbeelding. U kunt het filter (gemarkeerd met een rood kader) toepassen om op te halen alleen de blob met de opgegeven letters in namen.

![Azure Storage Explorer met de uitvoer van de Hive-query](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a>Hive-query's verzenden met de Hive-editor
U kunt ook de query console (Hive-editor) gebruiken door een URL in te voeren van het formulier *https:\//\<Hadoop-cluster naam >. azurehdinsight. net/Home/HiveEditor* in een webbrowser. U moet zijn geregistreerd in de zien deze console en dus moet u uw Hadoop-clusterreferenties hier.

### <a name="ps"></a>Hive-query's met Azure PowerShell-opdrachten verzenden
U kunt ook PowerShell gebruiken om in te dienen Hive-query's. Zie [Hive-taken verzenden met Power shell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md)voor instructies.

## <a name="create-tables"></a>Hive-data base en-tabellen maken
De Hive-query's worden gedeeld in de [github-opslag plaats](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) en kunnen van daaruit worden gedownload.

Hier volgt de Hive-query die wordt gemaakt van een Hive-tabel.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Hier volgen de beschrijvingen van de velden die u nodig hebt om aan te sluiten en andere configuraties:

* **\<database naam\>** : de naam van de data base die u wilt maken. Als u alleen de standaard database wilt gebruiken, kan de query "*Data Base maken...* " worden wegge laten.
* **\<tabel naam\>** : de naam van de tabel die u in de opgegeven Data Base wilt maken. Als u de standaard database wilt gebruiken, kan de tabel rechtstreeks worden verwezen door *\<tabel naam\>* zonder \<database naam\>.
* **\<veld scheidings teken\>** : de schei ding van velden in het gegevens bestand die moeten worden geüpload naar de Hive-tabel.
* **\<regel scheidings teken\>** : de schei ding waarmee de regels in het gegevens bestand worden beperkt.
* **\<opslag locatie\>** : de locatie van de Azure Storage om de gegevens van Hive-tabellen op te slaan. Als u geen locatie opgeeft *\<opslag locatie\>* , worden de data base en de tabellen standaard in *Hive/Warehouse/* map opgeslagen in de standaard container van het Hive-cluster. Als u opgeven van de opslaglocatie wilt, heeft de opslaglocatie moet zich binnen de standaard-container voor de database en tabellen. Deze locatie moet worden aangeduid als locatie relatief ten opzichte van de standaard container van het cluster in de indeling *wasb:///\<Directory 1 >/'* of *' wasb:///\<Directory 1 >/\<Directory 2 >/'* , enzovoort. Nadat de query is uitgevoerd, worden de relatieve directory's in de standaard container gemaakt.
* **TBLPROPERTIES ("Skip. header. line. Count" = "1")** : als het gegevens bestand een header-regel bevat, moet u deze eigenschap toevoegen **aan het einde** van de query *Create Table* . Anders wordt de kopregel geladen als een record in de tabel. Als het gegevensbestand geen een kopregel heeft, kan deze configuratie worden weggelaten in de query.

## <a name="load-data"></a>Gegevens laden in Hive-tabellen
Hier volgt de Hive-query die gegevens in een Hive-tabel laadt.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **\<pad naar BLOB-gegevens\>** : als het blobbestand dat moet worden geüpload naar de Hive-tabel zich in de standaard container van het HDInsight Hadoop-cluster bevindt, moet het *\<pad naar de blob-gegevens\>* in de indeling *' wasb://\<-map in deze container >/\<BLOB-bestands naam > '* staan. De blob-bestand kan ook worden in een andere container van het HDInsight Hadoop-cluster. In dit geval moet *\<pad naar BLOB-gegevens\>* de volgende indeling hebben *: ' wasb://\<container name >\<naam van het opslag account >. blob. core. Windows. net/\<BLOB-bestands naam > '* .

  > [!NOTE]
  > De blob-gegevens worden geüpload naar de Hive-tabel heeft zich in de standaard- of extra container van het opslagaccount voor het Hadoop-cluster. Als dat niet het geval is, kan de query voor het *laden van gegevens* geen toegang krijgen tot de gegevens.
  >
  >

## <a name="partition-orc"></a>Geavanceerde onderwerpen: gepartitioneerde tabel-en opgeslagen Hive-gegevens in ORC-indeling
Als de gegevens te groot is, is het partitioneren van de tabel nuttig zijn voor query's die alleen moeten worden gescand van een aantal partities van de tabel. Het is bijvoorbeeld redelijk voor het partitioneren van de logboekgegevens van een website met datums.

Naast het Hive-tabellen partitioneren, is het ook nuttig voor het opslaan van de Hive-gegevens in de indeling geoptimaliseerd rij kolommen (ORC). Voor meer informatie over de ORC-indeling raadpleegt <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">u Orc-bestanden gebruiken om de prestaties te verbeteren wanneer de Hive gegevens leest, schrijft en verwerkt</a>.

### <a name="partitioned-table"></a>Gepartitioneerde tabel
Hier volgt de Hive-query die een gepartitioneerde tabel maakt en gegevens in het laadt.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Bij het uitvoeren van query's op gepartitioneerde tabellen wordt aanbevolen de partitie voorwaarde toe te voegen aan het **begin** van de component `where`, waardoor de efficiëntie van de zoek opdracht wordt verbeterd.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Hive-gegevens opslaan in de ORC-indeling
U laden geen gegevens rechtstreeks van blob-opslag in Hive-tabellen die zijn opgeslagen in het ORC-indeling. Hier volgen de stappen die u moet uitvoeren om te laden van gegevens van Azure-blobs met Hive-tabellen die zijn opgeslagen in ORC-indeling.

Een externe tabel maken **die is opgeslagen als TEXTFILE** en gegevens uit de Blob-opslag in de tabel laden.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Een interne tabel maken met hetzelfde schema als de externe tabel in stap 1, met hetzelfde veld scheidingsteken wordt gebruikt en het Hive-gegevens opslaan in het ORC-indeling.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Gegevens selecteren uit de externe tabel in stap 1 en invoegen in het ORC-tabel

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Als de TEXTFILE-tabel *\<database naam\>.\<externe TEXTFILE-tabel naam\>* partities heeft, selecteert u in stap 3, de `SELECT * FROM <database name>.<external textfile table name>`-opdracht de partitie variabele als een veld in de gegevensset die wordt geretourneerd. Het wordt ingevoegd in de naam van de *\<-data base\>.\<Orc-tabel naam\>* mislukt omdat *\<database naam\>.\<Orc-tabel naam\>* niet de partitie variabele als een veld in het tabel schema. In dit geval moet u de velden die moeten worden ingevoegd, in de *\<database naam selecteren\>.\<Orc-tabel naam\>* als volgt:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Het is veilig om de *\<naam van het externe tekst bestand* te verwijderen\>wanneer u de volgende query gebruikt nadat alle gegevens zijn ingevoegd in *\<database naam\>.\<Orc-tabel naam\>* :

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Na deze procedure uitvoert, moet u een tabel met gegevens in de klaar voor gebruik ORC-indeling hebben.  
