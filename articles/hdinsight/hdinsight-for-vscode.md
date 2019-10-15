---
title: Azure HDInsight voor Visual Studio code
description: Meer informatie over het gebruik van de Spark-& Hive-Hulpprogram Ma's (Azure HDInsight) voor Visual Studio code voor het maken en verzenden van query's en scripts.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 03d0d26a21e710c07019d3ffcb13a1482a96af50
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311734"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Gebruik de Spark-& Hive-Hulpprogram Ma's voor Visual Studio code

Meer informatie over het gebruik van Spark-& Hive-Hulpprogram Ma's voor Visual Studio code voor het maken en verzenden van Apache Hive batch-taken, interactieve Hive-query's en PySpark-scripts voor Apache Spark. Eerst wordt beschreven hoe u Spark-& Hive-Hulpprogram Ma's installeert in Visual Studio code. vervolgens wordt uitgelegd hoe u taken kunt verzenden naar Spark-& Hive-Hulpprogram Ma's.  

Spark & Hive-Hulpprogram Ma's kunnen worden geïnstalleerd op platforms die worden ondersteund door Visual Studio code, waaronder Windows, Linux en macOS. Houd rekening met de volgende vereisten voor verschillende platforms.

## <a name="prerequisites"></a>Vereisten

De volgende items zijn vereist voor het volt ooien van de stappen in dit artikel:

- Een Azure HDInsight-cluster. Zie aan de [slag met HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)om een cluster te maken. Of gebruik een Spark-en Hive-cluster dat ondersteuning biedt voor een Apache livy-eind punt.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono is alleen vereist voor Linux en macOS.
- [Een PySpark Interactive Environment voor Visual Studio code](set-up-pyspark-interactive-environment.md).
- Een lokale map. In dit artikel wordt gebruikgemaakt van **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>& Hive-Hulpprogram Ma's voor Spark installeren

Nadat u aan de vereisten hebt voldaan, kunt u Spark-& Hive-Hulpprogram Ma's voor Visual Studio code installeren door de volgende stappen uit te voeren:

1. Open Visual Studio Code.

2. Navigeer in de menu balk om  > -**extensies** **weer te geven**.

3. Voer in het zoekvak **Spark &-Hive**in.

4. Selecteer **Spark & Hive-Hulpprogram ma's** uit de zoek resultaten en selecteer vervolgens **installeren**:

   ![De Spark-& Hive voor Visual Studio code python installeren](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Selecteer **opnieuw laden** wanneer dit nodig is.

## <a name="open-a-work-folder"></a>Een werkmap openen

Voer de volgende stappen uit om een werkmap te openen en een bestand te maken in Visual Studio code:

1. Navigeer in de menu balk naar **bestand** > **geopende map...**  > **C:\HD\HDexample**en selecteer vervolgens de knop **map selecteren** . De map wordt aan de linkerkant weer gegeven in de weer gave van de **Verkenner** .

2. Selecteer in de **Verkenner** -weer gave de map **HDexample** en selecteer vervolgens het pictogram **nieuw bestand** naast de werkmap:

   ![pictogram Visual Studio code nieuw bestand](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Geef het nieuwe bestand een naam met behulp van de `.hql` (Hive-query's) of de bestands extensie van het `.py`-bestand (Spark-script). In dit voor beeld wordt **HelloWorld. HQL**gebruikt.

## <a name="set-the-azure-environment"></a>De Azure-omgeving instellen

Voor een nationale Cloud gebruiker voert u de volgende stappen uit om eerst de Azure-omgeving in te stellen en vervolgens de **Azure: Sign in** -opdracht te gebruiken om u aan te melden bij Azure:

1. Navigeer naar **bestands** > **voor keuren** > **instellingen**.
2. Zoek naar de volgende teken reeks: **Azure: Cloud**.
3. Selecteer de nationale Cloud in de lijst:

   ![Configuratie van standaard aanmeldings vermelding instellen](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Verbinding maken met een Azure-account

Voordat u scripts kunt verzenden naar uw clusters vanuit Visual Studio code, moet u verbinding maken met uw Azure-account of een cluster koppelen (met Apache Ambari-gebruikers naam en wachtwoord referenties of een domein account). Volg deze stappen om verbinding te maken met Azure:

1. Ga in de menu balk naar **weer gave** > **opdracht palet...** en voer **Azure in: aanmelden**

    ![Component Hulpprogramma's van Spark & voor Visual Studio code login](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Volg de aanmeldings instructies om u aan te melden bij Azure. Nadat u verbinding hebt gemaakt, wordt de naam van uw Azure-account weer gegeven op de status balk onder aan het venster Visual Studio code.  

## <a name="link-a-cluster"></a>Een cluster koppelen

### <a name="link-azure-hdinsight"></a>Koppeling: Azure HDInsight

U kunt een normaal cluster koppelen met behulp van een door [Apache Ambari](https://ambari.apache.org/)beheerde gebruikers naam, of u kunt een beveiligd Hadoop-cluster van het ondernemings beveiligings pakket koppelen met behulp van een domein gebruikers naam (zoals: `user1@contoso.com`).

1. Ga in de menu balk naar **weer gave** > **opdracht palet...** , en voer **Spark/Hive in: koppel een cluster**.

   ![Opdracht palet koppeling cluster opdracht](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Selecteer het gekoppelde cluster type **Azure HDInsight**.

3. Voer de URL van het HDInsight-cluster in.

4. Voer uw Ambari-gebruikers naam in; de standaard instelling is **admin**.

5. Voer uw Ambari-wacht woord in.

6. Selecteer het cluster type.

7. Stel de weergave naam van het cluster in (optioneel).

8. Bekijk de **uitvoer** weergave voor verificatie.

   > [!NOTE]  
   > De gekoppelde gebruikers naam en het bijbehorende wacht woord worden gebruikt als het cluster beide zijn aangemeld bij het Azure-abonnement en een cluster heeft gekoppeld.  

### <a name="link-generic-livy-endpoint"></a>Koppeling: algemeen livy-eind punt

1. Ga in de menu balk naar **weer gave** > **opdracht palet...** , en voer **Spark/Hive in: koppel een cluster**.

2. Selecteer het type van het **algemene livy-eind punt**voor het gekoppelde cluster.

3. Voer het algemene livy-eind punt in. Bijvoorbeeld: http @ no__t-0//10.172.41.42:18080.

4. Selecteer autorisatie type **Basic** of **none**.  Als u **Basic**selecteert:  
    &emsp;a. Voer uw Ambari-gebruikers naam in; de standaard instelling is **admin**.  
    &emsp;b. Voer uw Ambari-wacht woord in.

5. Bekijk de **uitvoer** weergave voor verificatie.

## <a name="list-clusters"></a>Clusters weer geven

1. Ga in de menu balk naar **weer gave** > **opdracht palet...** en voer **Spark/Hive: List cluster**.

2. Selecteer het gewenste abonnement.

3. Bekijk de **uitvoer** weergave. In deze weer gave worden uw gekoppelde cluster (of clusters) en alle clusters onder uw Azure-abonnement weer gegeven:

    ![Een standaard cluster configuratie instellen](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Het standaard cluster instellen

1. Open de **HDexample** -map die [eerder](#open-a-work-folder)werd beschreven, indien deze is gesloten.  

2. Selecteer het bestand **HelloWorld. HQL** dat u [eerder](#open-a-work-folder)hebt gemaakt. Deze wordt geopend in de Script Editor.

3. Klik met de rechter muisknop op de script editor en selecteer **Spark/Hive: standaard cluster instellen**.  

4. [Maak verbinding](#connect-to-an-azure-account) met uw Azure-account of koppel een cluster als u dit nog niet hebt gedaan.

5. Selecteer een cluster als het standaard cluster voor het huidige script bestand. De hulpprogram ma's worden automatisch bijgewerkt **. VSCode\settings.json** -configuratie bestand:

   ![Standaard cluster configuratie instellen](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Interactieve Hive-query's en Hive batch scripts verzenden

Met Spark & Hive-Hulpprogram Ma's voor Visual Studio code kunt u interactieve Hive-query's en Hive-batch scripts verzenden naar uw clusters.

1. Open de **HDexample** -map die [eerder](#open-a-work-folder)werd beschreven, indien deze is gesloten.  

2. Selecteer het bestand **HelloWorld. HQL** dat u [eerder](#open-a-work-folder)hebt gemaakt. Deze wordt geopend in de Script Editor.

3. Kopieer en plak de volgende code in het Hive-bestand en sla het op:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Maak verbinding](#connect-to-an-azure-account) met uw Azure-account of koppel een cluster als u dit nog niet hebt gedaan.

5. Klik met de rechter muisknop op de script editor en selecteer **Hive: Interactive** om de query te verzenden, of gebruik de sneltoets CTRL + ALT + I.  Selecteer **Hive: batch** om het script in te dienen, of gebruik de sneltoets CTRL + ALT + H.  

6. Als u geen standaard cluster hebt opgegeven, selecteert u een cluster. Met de hulpprogram ma's kunt u ook een blok code verzenden in plaats van het hele script bestand met behulp van het context menu. Na enkele ogen blikken worden de query resultaten weer gegeven op een nieuw tabblad:

   ![Resultaat van interactieve Apache Hive query](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **Resultaten** paneel: u kunt het hele resultaat opslaan als een CSV-, JSON-of Excel-bestand naar een lokaal pad of door alleen meerdere regels te selecteren.

    - **Berichten** paneel: wanneer u een **regel** nummer selecteert, springt dit naar de eerste regel van het script dat wordt uitgevoerd.

## <a name="submit-interactive-pyspark-queries"></a>Interactieve PySpark-query's verzenden

Voer de volgende stappen uit om interactieve PySpark-query's te verzenden:

1. Open de **HDexample** -map die [eerder](#open-a-work-folder)werd beschreven, indien deze is gesloten.  

2. Maak een nieuw **HelloWorld.py** -bestand door de [eerdere](#open-a-work-folder) stappen te volgen.

3. Kopieer en plak de volgende code in het script bestand:

   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

4. [Maak verbinding](#connect-to-an-azure-account) met uw Azure-account of koppel een cluster als u dit nog niet hebt gedaan.

5. Selecteer alle code, klik met de rechter muisknop op de script editor en selecteer **Spark: PySpark Interactive** om de query te verzenden. Of gebruik de sneltoets CTRL + ALT + I.

   ![context menu van pyspark Interactive](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Selecteer het cluster als u geen standaard cluster hebt opgegeven. Na enkele ogen blikken worden de **python-interactieve** resultaten weer gegeven op een nieuw tabblad. Met de hulpprogram ma's kunt u ook een blok code verzenden in plaats van het hele script bestand met behulp van het snelmenu:

   ![interactief pyspark Interactive python-venster](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. Voer **%% info**in en druk op SHIFT + ENTER om de taak gegevens weer te geven (optioneel):

   ![pyspark interactieve weergave taak gegevens](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Het hulp programma biedt ook ondersteuning voor de **Spark SQL** -query:

   ![Resultaat van interactieve weer gave Pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   De status van de verzen ding wordt weer gegeven aan de linkerkant van de onderste status balk wanneer query's worden uitgevoerd. Verzend geen andere query's wanneer de status **PySpark kernel (bezet)** is.  

   > [!NOTE]
   >
   > Als de **uitbrei ding python is ingeschakeld** in de instellingen (standaard geselecteerd), wordt het oude venster gebruikt door de pyspark-interactie resultaten:
   >
   > ![pyspark Interactive python-uitbrei ding uitgeschakeld](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>Batch taak PySpark verzenden

1. Open de **HDexample** -map die u [eerder](#open-a-work-folder)hebt besproken als deze is gesloten.  

2. Maak een nieuw **BatchFile.py** -bestand door de [eerdere](#open-a-work-folder) stappen te volgen.

3. Kopieer en plak de volgende code in het script bestand:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```

4. [Maak verbinding](#connect-to-an-azure-account) met uw Azure-account of koppel een cluster als u dit nog niet hebt gedaan.

5. Klik met de rechter muisknop op de Script Editor, selecteer **Spark: PySpark batch**of gebruik de sneltoets CTRL + ALT + H.

6. Selecteer een cluster waar u uw PySpark-taak naar wilt verzenden:

   ![Python-taak resultaat uitvoer verzenden](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Nadat u een python-taak hebt verzonden, worden de inzendings logboeken weer gegeven in het **uitvoer** venster van Visual Studio code. De url's van de Spark-gebruikers interface en de URL van de garen-interface worden ook weer gegeven. U kunt de URL in een webbrowser openen om de taak status bij te houden.

## <a name="apache-livy-configuration"></a>Apache livy-configuratie

De [Apache livy](https://livy.incubator.apache.org/) -configuratie wordt ondersteund. U kunt deze configureren in de **. VSCode\settings.json** -bestand in de map van de werk ruimte. Momenteel biedt livy-configuratie alleen ondersteuning voor python-script. Zie [LIVY README](https://github.com/cloudera/livy/blob/master/README.rst )(Engelstalig) voor meer informatie.

<a id="triggerlivyconf"></a>**Livy-configuratie activeren**

Methode 1  
1. Navigeer in de menu balk naar **bestands** > **voor keuren** > **instellingen**.
2. Voer in het vak **Zoek instellingen** **HDInsight-taak inzending in: livy conf**.  
3. Selecteer **bewerken in instellingen. json** voor het relevante Zoek resultaat.

Methode 2 een bestand verzenden en u ziet dat de map. vscode automatisch wordt toegevoegd aan de werkmap. U kunt de livy-configuratie bekijken door **. vscode\settings.json**te selecteren.

+ De project instellingen:

    ![Configuratie van HDInsight Apache livy](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Stel de waarde en eenheid in voor de instellingen **driverMemory** en **executorMemory** . Bijvoorbeeld: 1g of 1024m.

+ Ondersteunde livy-configuraties:

    **/Batches plaatsen** Aanvraag tekst

    | name | description | type |
    | :- | :- | :- |
    | Profiler | Bestand met de toepassing die moet worden uitgevoerd | Pad (vereist) |
    | proxyUser | Gebruiker die moet worden geïmiteerd bij het uitvoeren van de taak | Tekenreeks |
    | className | Hoofd klasse java/Spark-toepassing | Tekenreeks |
    | argumenten | Opdracht regel argumenten voor de toepassing | Lijst met teken reeksen |
    | Pott | Potten die in deze sessie moeten worden gebruikt | Lijst met teken reeksen | 
    | pyFiles | Python-bestanden die moeten worden gebruikt in deze sessie | Lijst met teken reeksen |
    | logbestanden | Bestanden die moeten worden gebruikt in deze sessie | Lijst met teken reeksen |
    | driverMemory | Hoeveelheid geheugen die moet worden gebruikt voor het stuur programma | Tekenreeks |
    | driverCores | Het aantal kernen dat moet worden gebruikt voor het stuur programma | integer |
    | executorMemory | Hoeveelheid geheugen die per bewerkings proces moet worden gebruikt | Tekenreeks |
    | executorCores | Aantal kernen dat voor elke uitvoerder moet worden gebruikt | integer |
    | numExecutors | Aantal actieve uitvoerendeers dat voor deze sessie wordt gestart | integer |
    | archieven | Archief archieven die moeten worden gebruikt in deze sessie | Lijst met teken reeksen |
    | wachtrij | De naam van de wachtrij met GARENs die moet worden ingediend| Tekenreeks |
    | name | De naam van deze sessie | Tekenreeks |
    | belangen | Eigenschappen van Spark-configuratie | Toewijzing van sleutel = val |

    Antwoord tekst het gemaakte batch-object.

    | name | description | type |
    | :- | :- | :- |
    | id | Sessie-id | integer |
    | appId | Toepassings-id van deze sessie | Tekenreeks |
    | appInfo | Gedetailleerde toepassings informatie | Toewijzing van sleutel = val |
    | logbestand | Logboek regels | Lijst met teken reeksen |
    | state |Batch status | Tekenreeks |

    > [!NOTE]
    > De toegewezen livy-configuratie wordt weer gegeven in het deel venster uitvoer wanneer u het script verzendt.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integreren met Azure HDInsight vanuit Explorer

U kunt de Hive-tabel in uw clusters rechtstreeks bekijken via de **Azure HDInsight** Explorer:

1. [Maak verbinding](#connect-to-an-azure-account) met uw Azure-account als u dit nog niet hebt gedaan.

2. Selecteer het pictogram van **Azure** in de kolom uiterst links.

3. Vouw in het linkerdeel venster **Azure: HDINSIGHT**uit. De beschik bare abonnementen en clusters worden weer gegeven.

4. Vouw het cluster uit om de meta gegevens database en het tabel schema van de Hive weer te geven.

5. Klik met de rechter muisknop op de Hive-tabel. Bijvoorbeeld: **hivesampletable**. Selecteer **voor beeld**.

   ![Component tabel van Spark & voor Visual Studio code-Preview](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Het venster **preview-resultaten** wordt geopend:

   ![Het &-onderdeel Spark voor Visual Studio code-voorbeeld resultaten](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- Paneel resultaten

   U kunt het hele resultaat opslaan als een CSV-, JSON-of Excel-bestand naar een lokaal pad, of u hoeft alleen maar meerdere regels te selecteren.

- BERICHTEN paneel
   1. Wanneer het aantal rijen in de tabel groter is dan 100, ziet u het volgende bericht: "de eerste 100 rijen worden weer gegeven voor de Hive-tabel."
   2. Wanneer het aantal rijen in de tabel kleiner is dan of gelijk is aan 100, ziet u een bericht zoals het volgende: "60 rijen worden weer gegeven voor Hive-tabel".
   3. Wanneer de tabel geen inhoud bevat, ziet u het volgende bericht: 0 rijen worden weer gegeven voor Hive-tabel.

        >[!NOTE]
        >
        >Installeer xclip in Linux om Kopieer tabel gegevens in te scha kelen.
        >
        >![Spark-& Hive voor Visual Studio code in Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Aanvullende functies

De Spark-& Hive voor Visual Studio code biedt ook ondersteuning voor de volgende functies:

- **Automatisch aanvullen IntelliSense**. Suggesties worden pop-ups gemaakt voor tref woorden, methoden, variabelen en andere programmeer elementen. Verschillende pictogrammen vertegenwoordigen verschillende typen objecten:

    ![Hulp middelen voor Spark &-Hive voor Visual Studio code IntelliSense-objecten](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **Fout markering voor IntelliSense**. De taal service onderstreept het bewerken van fouten in het Hive-script.     
- **Syntaxis punten**. De taal service gebruikt verschillende kleuren om variabelen, tref woorden, gegevens typen, functies en andere programmeer elementen te onderscheiden:

    ![De belangrijkste aandachtspunten voor de Visual Studio-code voor het onderdeel Spark &](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Alleen-lezen rol

Gebruikers aan wie de rol alleen lezer voor het cluster is toegewezen, kunnen geen taken meer verzenden naar het HDInsight-cluster, en kunnen de Hive-data base niet weer geven. Neem contact op met de Cluster beheerder om uw rol bij te werken naar de [**HDInsight-cluster operator**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) in de [Azure Portal](https://ms.portal.azure.com/). Als u geldige Ambari-referenties hebt, kunt u het cluster hand matig koppelen met behulp van de volgende richt lijnen.

### <a name="browse-the-hdinsight-cluster"></a>Bladeren in het HDInsight-cluster  

Wanneer u de Azure HDInsight Explorer selecteert om een HDInsight-cluster uit te breiden, wordt u gevraagd het cluster te koppelen als u de rol alleen-lezen hebt voor het cluster. Gebruik de volgende methode om een koppeling naar het cluster te maken met behulp van uw Ambari-referenties.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>De taak verzenden naar het HDInsight-cluster

Bij het verzenden van een taak naar een HDInsight-cluster wordt u gevraagd het cluster te koppelen als u de rol alleen-lezen voor het cluster gebruikt. Gebruik de volgende stappen om een koppeling naar het cluster te maken met behulp van Ambari-referenties.

### <a name="link-to-the-cluster"></a>Koppeling naar het cluster

1. Geef een geldige Ambari-gebruikers naam op.
2. Voer een geldig wacht woord in.

   ![De gebruikers naam van de & voor de Visual Studio-code van Spark-Hulpprogram Ma's](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Onderdeel Hulpprogramma's van Spark & voor Visual Studio code-wacht woord](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >U kunt `Spark / Hive: List Cluster` gebruiken om het gekoppelde cluster te controleren:
  >
  >![Koppeling naar het onderdeel Hulpprogramma's van Spark & voor Visual Studio code Reader](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Bladeren in een Data Lake Storage Gen2-account

Wanneer u de Azure HDInsight Explorer selecteert om een Data Lake Storage Gen2 account uit te vouwen, wordt u gevraagd om de toegangs sleutel voor opslag in te voeren als uw Azure-account geen toegang heeft tot Gen2-opslag. Nadat de toegangs sleutel is gevalideerd, wordt het Data Lake Storage Gen2-account automatisch uitgevouwen.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Taken verzenden naar een HDInsight-cluster met Data Lake Storage Gen2

Wanneer u een taak verzendt naar een HDInsight-cluster met behulp van Data Lake Storage Gen2, wordt u gevraagd om de toegangs sleutel voor opslag in te voeren als uw Azure-account geen schrijf toegang heeft tot Gen2-opslag. Nadat de toegangs sleutel is gevalideerd, wordt de taak verzonden.

![Spark-& Hive-Hulpprogram Ma's voor Visual Studio code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> U kunt de toegangs sleutel voor het opslag account ophalen van de Azure Portal. Zie [toegangs sleutels weer geven en kopiëren](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys)voor meer informatie.

## <a name="unlink-cluster"></a>Cluster ontkoppelen

1. Ga in de menu balk naar **weer gave** > **opdracht palet**en voer **Spark/Hive: ontkoppelen van een cluster**.  

2. Selecteer een cluster om te ontkoppelen.  

3. Raadpleeg de **uitvoer** weergave voor verificatie.  

## <a name="sign-out"></a>Afmelden  

Ga in de menu balk naar **weer gave** > **opdracht palet**en voer Azure in **: Meld**u aan.

## <a name="next-steps"></a>Volgende stappen

Zie voor een video die demonstreert met behulp van Spark-& Hive voor Visual Studio code [spark & Hive voor Visual Studio code](https://go.microsoft.com/fwlink/?linkid=858706).
