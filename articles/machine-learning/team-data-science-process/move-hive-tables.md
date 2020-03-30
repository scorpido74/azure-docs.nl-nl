---
title: Hive-tabellen maken en gegevens laden vanuit Blob-opslag - Team Data Science Process
description: Gebruik Hive-query's om Hive-tabellen te maken en gegevens uit Azure blob-opslag te laden. Partitiehivetabellen en gebruik de opmaak van Optimized Row Columnar (ORC) om de queryprestaties te verbeteren.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251659"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Hive-tabellen maken en gegevens laden vanuit Azure Blob Storage

In dit artikel worden algemene Hive-query's gepresenteerd die Hive-tabellen maken en gegevens uit Azure blob-opslag laden. Er worden ook enkele richtlijnen gegeven voor het partitioneren van Hive-tabellen en over het gebruik van de opmaak van Optimized Row Columnar (ORC) om de queryprestaties te verbeteren.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u:

* Een Azure Storage-account maken. Zie Over Azure [Storage-accounts](../../storage/common/storage-introduction.md)als u instructies nodig hebt.
* Ingericht een aangepaste Hadoop cluster met de HDInsight service.  Zie Setup Clusters [in HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)als u instructies nodig hebt.
* Ingeschakelde externe toegang tot het cluster, ingelogd en opende de Hadoop Command-Line console. Als u instructies nodig hebt, [raadpleegt u Apache Hadoop-clusters beheren.](../../hdinsight/hdinsight-administer-use-portal-linux.md)

## <a name="upload-data-to-azure-blob-storage"></a>Gegevens uploaden naar Azure blob-opslag
Als u een virtuele Azure-machine hebt gemaakt door de instructies te volgen die zijn opgenomen in [Het instellen van een virtuele Azure-machine voor geavanceerde analyses,](../../machine-learning/data-science-virtual-machine/overview.md)moet dit scriptbestand zijn gedownload naar de map *C:\\\\\<\>\\Gebruikers gebruikersnaam Documents\\Data Science Scripts* op de virtuele machine. Deze Hive-query's vereisen alleen dat u een gegevensschema en Azure blob-opslagconfiguratie opgeeft in de juiste velden om klaar te zijn voor indiening.

We gaan ervan uit dat de gegevens voor Hive-tabellen een **niet-gecomprimeerde** tabelindeling hebben en dat de gegevens zijn geüpload naar de standaardcontainer (of naar een extra) container van het opslagaccount dat wordt gebruikt door het Hadoop-cluster.

Als u wilt oefenen op de **NYC Taxi Trip Data,** moet u:

* **download** de 24 [NYC Taxi Trip Data](https://www.andresmh.com/nyctaxitrips) bestanden (12 Trip bestanden en 12 Fare bestanden),
* alle bestanden **uitritsen** in CSV-bestanden en vervolgens
* **upload** ze naar de standaard (of geschikte container) van het Azure Storage-account; opties voor een dergelijk account worden weergegeven in het onderwerp [Azure Storage gebruiken met Azure HDInsight-clusters.](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) Het proces om de CSV-bestanden te uploaden naar de standaardcontainer op het opslagaccount is te vinden op deze [pagina.](hive-walkthrough.md#upload)

## <a name="how-to-submit-hive-queries"></a><a name="submit"></a>Hive-query's indienen
Hive-query's kunnen worden ingediend met behulp van:

* [Hive-query's indienen via Hadoop Command Line in headnode van hadoop-cluster](#headnode)
* [Hive-query's verzenden met de Hive Editor](#hive-editor)
* [Hive-query's verzenden met Azure PowerShell-opdrachten](#ps)

Hive-query's zijn SQL-achtig. Als u bekend bent met SQL, u de [Hive for SQL-gebruikers Cheat Sheet](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) nuttig vinden.

Wanneer u een Hive-query indient, u ook de bestemming van de uitvoer van Hive-query's beheren, of deze nu op het scherm is of naar een lokaal bestand op het hoofdknooppunt of naar een Azure-blob.

### <a name="submit-hive-queries-through-hadoop-command-line-in-headnode-of-hadoop-cluster"></a><a name="headnode"></a>Hive-query's indienen via Hadoop Command Line in headnode van hadoop-cluster
Als de Hive-query complex is, leidt het rechtstreeks indienen van de query rechtstreeks in het hoofdknooppunt van het Hadoop-cluster meestal tot een snellere ommekeer dan het indienen ervan met een Hive Editor- of Azure PowerShell-scripts.

Log in op het hoofdknooppunt van het Hadoop-cluster, open de Hadoop-opdrachtregel `cd %hive_home%\bin`op het bureaublad van het hoofdknooppunt en voer de opdracht in.

U Hive-query's indienen in de Hadoop-opdrachtregel:

* Direct
* met behulp van '.hql'-bestanden
* met de Hive-opdrachtconsole

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Verzend Hive-query's rechtstreeks in hadoop-opdrachtregel.
U opdracht `hive -e "<your hive query>;` zoals uitvoeren om eenvoudige Hive-query's rechtstreeks in hadoop-opdrachtregel in te dienen. Hier is een voorbeeld, waarbij het rode vak de opdracht die de Hive-query indient, schetst en het groene vak de uitvoer van de Hive-query omschrijft.

![Opdracht om Hive-query in te dienen met uitvoer uit Hive-query](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Hive-query's indienen in '.hql'-bestanden
Wanneer de Hive-query ingewikkelder is en meerdere regels heeft, is het bewerken van query's in opdrachtregel of Hive-opdrachtconsole niet praktisch. Een alternatief is het gebruik van een teksteditor in het hoofdknooppunt van het Hadoop-cluster om de Hive-query's op te slaan in een bestand '.hql' in een lokale map van het hoofdknooppunt. Vervolgens kan de Hive-query in het bestand '.hql' worden ingediend met behulp van het `-f` argument als volgt:

    hive -f "<path to the '.hql' file>"

![Hive query in een '.hql' bestand](./media/move-hive-tables/run-hive-queries-3.png)

**Schermafdruk van hive-query's voor voortgangsstatus onderdrukken**

Nadat Hive-query is ingediend in Hadoop Command Line, wordt de voortgang van de taak Kaart/Vermindering standaard afgedrukt op het scherm. Als u de schermafdruk van de taakvoortgang kaart/vermindering wilt onderdrukken, u een argument `-S` ('S' in hoofdletters) in de opdrachtregel als volgt gebruiken:

    hive -S -f "<path to the '.hql' file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Hive-query's verzenden in de opdrachtconsole Hive.
U ook eerst de opdrachtconsole `hive` Hive invoeren door de opdracht in hadoop-opdrachtregel uit te voeren en vervolgens Hive-query's in de opdrachtconsole Hive in te dienen. Hier volgt een voorbeeld. In dit voorbeeld markeren de twee rode vakken de opdrachten die zijn gebruikt om respectievelijk de opdrachtconsole Hive in te voeren en de Hive-query die is ingediend in de opdrachtconsole Hive. In het groene vak wordt de uitvoer van de Bijenkorf-query weergegeven.

![Hive-opdrachtconsole openen en opdracht invoeren, uitvoer van Bijenkorf-query weergeven](./media/move-hive-tables/run-hive-queries-2.png)

In de vorige voorbeelden worden de resultaten van de Hive-query rechtstreeks op het scherm uitgevoerd. U de uitvoer ook schrijven naar een lokaal bestand op het hoofdknooppunt of naar een Azure-blob. Vervolgens u andere hulpprogramma's gebruiken om de uitvoer van Hive-query's verder te analyseren.

**Resultaten van uitvoerbijenkorfquery's naar een lokaal bestand.**
Als u de resultaten van Hive-query's wilt uitvoeren naar een lokale map op het hoofdknooppunt, moet u de Hive-query in de Hadoop-opdrachtregel als volgt indienen:

    hive -e "<hive query>" > <local path in the head node>

In het volgende voorbeeld wordt de uitvoer van `hivequeryoutput.txt` Hive-query in een bestand in map `C:\apps\temp`geschreven.

![Uitvoer van Hive-query](./media/move-hive-tables/output-hive-results-1.png)

**Resultaten van Hive-query's uitvoeren naar een Azure-blob**

U de resultaten van de Hive-query ook uitvoeren naar een Azure-blob, in de standaardcontainer van het Hadoop-cluster. De Hive-query hiervoor is als volgt:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

In het volgende voorbeeld wordt de uitvoer van `queryoutputdir` Hive-query naar een blobmap in de standaardcontainer van het Hadoop-cluster geschreven. Hier hoeft u alleen de naam van de map op te geven, zonder de naam blob. Er wordt een fout gegenereerd als u zowel `wasb:///queryoutputdir/queryoutput.txt`directory- als blobnamen opgeeft, zoals .

![Uitvoer van Hive-query](./media/move-hive-tables/output-hive-results-2.png)

Als u de standaardcontainer van het Hadoop-cluster opent met Azure Storage Explorer, ziet u de uitvoer van de Hive-query zoals weergegeven in de volgende afbeelding. U het filter (gemarkeerd met rood vak) toepassen om alleen de blob met opgegeven letters in namen op te halen.

![Azure Storage Explorer met uitvoer van de Hive-query](./media/move-hive-tables/output-hive-results-3.png)

### <a name="submit-hive-queries-with-the-hive-editor"></a><a name="hive-editor"></a>Hive-query's verzenden met de Hive Editor
U de Query Console (Hive Editor) ook gebruiken door een URL van het formulier https in te *voeren:\//\<Hadoop-clusternaam>.azurehdinsight.net/Home/HiveEditor* in een webbrowser. Je moet ingelogd zijn om deze console te bekijken en dus ook deze te bekijken.

### <a name="submit-hive-queries-with-azure-powershell-commands"></a><a name="ps"></a>Hive-query's verzenden met Azure PowerShell-opdrachten
U PowerShell ook gebruiken om Hive-query's in te dienen. Zie [Hive-taken verzenden met PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md)voor instructies .

## <a name="create-hive-database-and-tables"></a><a name="create-tables"></a>Hive-database en -tabellen maken
De Hive-query's worden gedeeld in de [GitHub-repository](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) en kunnen vanaf daar worden gedownload.

Hier is de Hive-query die een Hive-tabel maakt.

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

Hier volgen de beschrijvingen van de velden die u moet aansluiten en andere configuraties:

* **databasenaam:\>de naam van de database die u wilt maken. \<** Als u alleen de standaarddatabase wilt gebruiken, kan de query "*database maken...*" worden weggelaten.
* **tabelnaam:\>de naam van de tabel die u wilt maken in de opgegeven database. \<** Als u de standaarddatabase wilt gebruiken, kan de tabel \<rechtstreeks\>worden aangeduid met * \<tabelnaam\> * zonder databasenaam.
* **veldscheidingsteken:\>de scheidingsteken die velden in het gegevensbestand dat moet worden geüpload naar de Hive-tabel, aflegt. \<**
* **lijnscheidingsteken:\>de scheidingsteken die regels in het gegevensbestand afgebakt. \<**
* **opslaglocatie:\>de Azure Storage-locatie om de gegevens van Hive-tabellen op te slaan. \<** If you do not specify *LOCATION \<storage location\>*, the database and the tables are stored in *hive/warehouse/* directory in the default container of the Hive cluster by default. Als u de opslaglocatie wilt opgeven, moet de opslaglocatie zich binnen de standaardcontainer voor de database en tabellen bevinden. Deze locatie moet worden aangeduid als locatie ten opzichte van de standaardcontainer van het cluster in de indeling *'wasb:///\<directory 1>/'* of *\<'wasb:/// directory 1>/directory\<2>/'*, enz. Nadat de query is uitgevoerd, worden de relatieve mappen gemaakt binnen de standaardcontainer.
* **TBLPROPERTIES("skip.header.line.count"="1")**: Als het gegevensbestand een koptekstregel heeft, moet u deze eigenschap toevoegen **aan het einde** van de *tabelquery maken.* Anders wordt de koptekstregel als record naar de tabel geladen. Als het gegevensbestand geen koptekstregel heeft, kan deze configuratie worden weggelaten in de query.

## <a name="load-data-to-hive-tables"></a><a name="load-data"></a>Gegevens laden naar Hive-tabellen
Hier is de Hive-query die gegevens laadt in een Hive-tabel.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* pad **naar\>blobgegevens: Als het blobbestand dat moet worden geüpload naar de Hive-tabel zich in de standaardcontainer van het HDInsight Hadoop-cluster bevindt, moet het pad naar blobgegevens in de indeling 'wasb:// map in deze container>/ blobbestandsnaam>' staan. \<** * \<\> * *\<\<* Het blobbestand kan zich ook in een extra container van het HDInsight Hadoop-cluster bevinden. In dit geval moet * \<pad naar\> blobgegevens* in de indeling *'wasb://\<containernaam>\<de naam van het opslagaccount>.blob.core.windows.net/\<blobbestandsnaam>'*.

  > [!NOTE]
  > De blobgegevens die moeten worden geüpload naar de tabel Hive, moeten zich in de standaard- of extra container van het opslagaccount voor het Hadoop-cluster bevinden. Anders wordt de query *GEGEVENS laden* mislukt als er wordt geklaagd dat deze geen toegang heeft tot de gegevens.
  >
  >

## <a name="advanced-topics-partitioned-table-and-store-hive-data-in-orc-format"></a><a name="partition-orc"></a>Geavanceerde onderwerpen: partitietabel en hive-gegevens opslaan in ORC-indeling
Als de gegevens groot zijn, is het partitioneren van de tabel gunstig voor query's die slechts een paar partities van de tabel hoeven te scannen. Het is bijvoorbeeld redelijk om de loggegevens van een website te verdelen op datums.

Naast het partitioneren van Hive-tabellen is het ook handig om de Hive-gegevens op te slaan in de indeling Optimized Row Columnar (ORC). Zie <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">ORC-bestanden gebruiken verbetert de prestaties wanneer Hive gegevens leest, schrijft en verwerkt</a>voor meer informatie over ORC-opmaak.

### <a name="partitioned-table"></a>Verdeelde tabel
Hier is de Hive-query die een partitietabel maakt en er gegevens in laadt.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Bij het opvragen van partitietabellen wordt aanbevolen om de partitievoorwaarde toe te voegen aan het **begin** van de `where` clausule, wat de zoekefficiëntie verbetert.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="store-hive-data-in-orc-format"></a><a name="orc"></a>Hive-gegevens opslaan in ORC-indeling
U gegevens uit blobopslag niet rechtstreeks laden in Hive-tabellen die zijn opgeslagen in de ORC-indeling. Dit zijn de stappen die u moet nemen om gegevens van Azure-blobs te laden naar Hive-tabellen die zijn opgeslagen in ORC-indeling.

Maak een externe tabel **DIE IS OPGESLAGEN ALS TEXTFILE** en laad gegevens van blobopslag naar de tabel.

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

Maak een interne tabel met hetzelfde schema als de externe tabel in stap 1, met dezelfde veldscheidingsteken, en sla de Hive-gegevens op in de ORC-indeling.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Gegevens uit de externe tabel selecteren in stap 1 en invoegen in de ORC-tabel

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Als de naam van de * \<tekstfile-tabeldatabase\>\< . externe tekstbestandstabelnaam\> * heeft partities, selecteert `SELECT * FROM <database name>.<external textfile table name>` de opdracht in STAP 3 de partitievariabele als een veld in de geretourneerde gegevensset. Het invoegen in de * \<databasenaam\>.\< ORC-tabelnaam\> * mislukt sinds * \<de naam\>van de database .\< Orc-tabelnaam\> * heeft de partitievariabele niet als veld in het tabelschema. In dit geval moet u specifiek de velden selecteren die moeten worden ingevoegd in * \<databasenaam.\>\< ORC-tabelnaam\> * als volgt:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Het is veilig om de * \<naam\> van de externe tekstbestandstabel* te laten vallen wanneer u de volgende query gebruikt nadat alle gegevens in * \<\>databasenaam zijn ingevoegd.\< ORC-tabelnaam\>*:

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Na het volgen van deze procedure moet u een tabel met gegevens in de ORC-indeling klaar hebben voor gebruik.  
