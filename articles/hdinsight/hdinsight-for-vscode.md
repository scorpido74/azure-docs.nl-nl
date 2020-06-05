---
title: Azure HDInsight voor Visual Studio code
description: Meer informatie over het gebruik van de Spark-& Hive-Hulpprogram Ma's (Azure HDInsight) voor Visual Studio code. Gebruik de hulpprogram ma's om query's en scripts te maken en in te dienen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: d99b9103a597442a5a98fa842eef3e98b2aad086
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417335"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Gebruik de Spark-& Hive-Hulpprogram Ma's voor Visual Studio code

Meer informatie over het gebruik van Apache Spark & Hive-Hulpprogram Ma's voor Visual Studio code. Gebruik de hulpprogram ma's om Apache Hive batch taken, interactieve Hive-query's en PySpark-scripts te maken en in te dienen voor Apache Spark. Eerst wordt beschreven hoe u Spark-& Hive-Hulpprogram Ma's installeert in Visual Studio code. Vervolgens wordt uitgelegd hoe u taken kunt verzenden naar Spark-& Hive-Hulpprogram Ma's.  

Spark-& Hive-Hulpprogram Ma's kunnen worden geïnstalleerd op platforms die worden ondersteund door Visual Studio code. Houd rekening met de volgende vereisten voor verschillende platforms.

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

2. Ga in de menu balk naar extensies **weer geven**  >  **Extensions**.

3. Voer in het zoekvak **Spark &-Hive**in.

4. Selecteer **Spark & Hive-Hulpprogram ma's** uit de zoek resultaten en selecteer vervolgens **installeren**:

   ![De Spark-& Hive voor Visual Studio code python installeren](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Selecteer **opnieuw laden** wanneer dit nodig is.

## <a name="open-a-work-folder"></a>Een werkmap openen

Voer de volgende stappen uit om een werkmap te openen en een bestand te maken in Visual Studio code:

1. Ga in de menu balk naar **bestand**  >  **openen map...**  >  **C:\HD\HDexample**en selecteer vervolgens de knop **map selecteren** . De map wordt aan de linkerkant weer gegeven in de weer gave van de **Verkenner** .

2. Selecteer in de **Verkenner** -weer gave de map **HDexample** en selecteer vervolgens het pictogram **nieuw bestand** naast de werkmap:

   ![pictogram Visual Studio code nieuw bestand](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Geef het nieuwe bestand een naam met behulp van de `.hql` (Hive-query's) of de `.py` bestands extensie (Spark-script). In dit voor beeld wordt **HelloWorld. HQL**gebruikt.

## <a name="set-the-azure-environment"></a>De Azure-omgeving instellen

Voor een nationale Cloud gebruiker voert u de volgende stappen uit om eerst de Azure-omgeving in te stellen en vervolgens de **Azure: Sign in** -opdracht te gebruiken om u aan te melden bij Azure:

1. Navigeer naar **File**  >  **instellingen voor bestands voorkeuren**  >  **Settings**.
2. Zoek naar de volgende teken reeks: **Azure: Cloud**.
3. Selecteer de nationale Cloud in de lijst:

   ![Configuratie van standaard aanmeldings vermelding instellen](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Verbinding maken met een Azure-account

Voordat u scripts kunt verzenden naar uw clusters vanuit Visual Studio code, moet u verbinding maken met uw Azure-account of een cluster koppelen. Gebruik de Apache Ambari-gebruikers naam en wachtwoord referenties of een account dat lid is van het domein. Volg deze stappen om verbinding te maken met Azure:

1. Navigeer in de menu balk naar het **weer geven**van het  >  **opdracht palet...** en voer **Azure: aanmelden in**:

    ![Component Hulpprogramma's van Spark & voor Visual Studio code login](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Volg de aanmeldings instructies om u aan te melden bij Azure. Nadat u verbinding hebt gemaakt, ziet u de naam van uw Azure-account op de status balk onder aan het venster Visual Studio code.  

## <a name="link-a-cluster"></a>Een cluster koppelen

### <a name="link-azure-hdinsight"></a>Koppeling: Azure HDInsight

U kunt een normaal cluster koppelen met behulp van een door [Apache Ambari](https://ambari.apache.org/)beheerde gebruikers naam, of u kunt een beveiligd Hadoop-cluster van het ondernemings beveiligings pakket koppelen met behulp van een domein gebruikers naam (zoals: `user1@contoso.com` ).

1. Navigeer in de menu balk naar het **weer geven**van het  >  **opdracht palet...**, en voer **Spark/Hive: koppelen aan een cluster**.

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

1. Navigeer in de menu balk naar het **weer geven**van het  >  **opdracht palet...**, en voer **Spark/Hive: koppelen aan een cluster**.

2. Selecteer het type van het **algemene livy-eind punt**voor het gekoppelde cluster.

3. Voer het algemene livy-eind punt in. Bijvoorbeeld: http \: //10.172.41.42:18080.

4. Selecteer autorisatie type **Basic** of **none**.  Als u **Basic**selecteert:  
    &emsp;één. Voer uw Ambari-gebruikers naam in; de standaard instelling is **admin**.  
    &emsp;b. Voer uw Ambari-wacht woord in.

5. Bekijk de **uitvoer** weergave voor verificatie.

## <a name="list-clusters"></a>Clusters weer geven

1. Navigeer in de menu balk naar opdracht palet **weer geven**  >  **...** en voer **Spark/Hive: List cluster**in.

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

Gebruikers kunnen PySpark interactief uitvoeren op de volgende manieren:

### <a name="using-the-pyspark-interactive-command-in-py-file"></a>De PySpark Interactive opdracht in PY File gebruiken
Voer de volgende stappen uit met de interactieve opdracht PySpark om de query's te verzenden:

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

4. De prompt voor het installeren van PySpark kernel wordt weer gegeven in de rechter benedenhoek van het venster. U kunt op de knop **installeren** klikken om door te gaan met de PySpark-installaties. u kunt ook op de knop **overs Laan** klikken om deze stap over te slaan.

   ![pyspark-kernel installeren](./media/hdinsight-for-vscode/install-the-pyspark-kernel.png)

5. Als u het later opnieuw moet installeren, kunt u naar de instellingen van de **Bestands**  >  **voorkeur**navigeren  >  **Settings**en vervolgens **Hdinsight uitschakelen: Schakel overs Laan Pyspark-installatie** in de instellingen in. 
    
    ![pyspark-kernel installeren](./media/hdinsight-for-vscode/enable-skip-pyspark-installation.png)

6. Als de installatie is geslaagd in stap 4, wordt het bericht venster ' PySpark geïnstalleerd ' weer gegeven in de rechter benedenhoek van het venster. Klik op de knop **opnieuw laden** om het venster opnieuw te laden.
    ![pyspark is geïnstalleerd](./media/hdinsight-for-vscode/pyspark-kernel-installed-successfully.png)

7. [Maak verbinding](#connect-to-an-azure-account) met uw Azure-account of koppel een cluster als u dit nog niet hebt gedaan.

8. Selecteer alle code, klik met de rechter muisknop op de script editor en selecteer **Spark: PySpark Interactive** om de query te verzenden. Of gebruik de sneltoets CTRL + ALT + I.

    ![context menu van pyspark Interactive](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

9. Selecteer het cluster als u geen standaard cluster hebt opgegeven. Na enkele ogen blikken worden de **python-interactieve** resultaten weer gegeven op een nieuw tabblad. Klik op PySpark om de kernel over te scha kelen naar **PySpark**en de code wordt uitgevoerd. Met de hulpprogram ma's kunt u ook een blok code verzenden in plaats van het hele script bestand met behulp van het snelmenu:

   ![interactief pyspark Interactive python-venster](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

10. Voer **%% info**in en druk op SHIFT + ENTER om de taak gegevens weer te geven (optioneel):

    ![pyspark interactieve weergave taak gegevens](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

Het hulp programma biedt ook ondersteuning voor de **Spark SQL** -query:

   ![resultaat van interactieve weer gave pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)


### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Interactieve query's uitvoeren in een PY-bestand met behulp van een opmerking #%%

1. Voeg **#%%** vóór de py-code toe om de notitieblok ervaring op te halen.

    ![#%% toevoegen](./media/hdinsight-for-vscode/run-cell.png)

2. Klik op de **cel run**. Na enkele ogen blikken worden de python-interactieve resultaten weer gegeven op een nieuw tabblad.

   ![resultaten van de rij uitvoeren](./media/hdinsight-for-vscode/run-cell-get-results.png)

   > [!NOTE]  
   > Wanneer de kernel of de instellingen up-to-out zijn, gebruikt u het bestand **python: Select interpreter om de opdracht Jupyter server te starten** en start u de **IPython-kernel opnieuw**, waarna u de VSCode opnieuw kunt gebruiken.

## <a name="leverage-ipynb-support-from-python-extension"></a>Gebruik IPYNB-ondersteuning van python-extensie

1. U kunt een Jupyter Notebook maken met behulp van het opdracht palet of door een nieuw. ipynb-bestand te maken in uw werk ruimte. Zie [werken met Jupyter-notebooks in Visual Studio code](https://code.visualstudio.com/docs/python/jupyter-support) voor meer informatie.

2. Klik op PySpark om de kernel over te scha kelen naar **PySpark**, en klik vervolgens op **cel uitvoeren**, na enige tijd, het resultaat wordt weer gegeven.

   ![ipynb-resultaten uitvoeren](./media/hdinsight-for-vscode/run-ipynb-file-results.png)


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
1. Navigeer in de menu balk naar instellingen voor **Bestands**  >  **Voorkeuren**  >  **Settings**.
2. Voer in het vak **Zoek instellingen** **HDInsight-taak inzending in: livy conf**.  
3. Selecteer **bewerken in instellingen. json** voor het relevante Zoek resultaat.

Methode 2 een bestand verzenden en u ziet dat de `.vscode` map automatisch wordt toegevoegd aan de werkmap. U kunt de livy-configuratie bekijken door **. vscode\settings.json**te selecteren.

+ De project instellingen:

    ![Configuratie van HDInsight Apache livy](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Stel de waarde en eenheid in voor de instellingen **driverMemory** en **executorMemory** . Bijvoorbeeld: 1g of 1024m.

+ Ondersteunde livy-configuraties:

    **/Batches plaatsen** Aanvraag tekst

    | naam | description | type |
    | --- | --- | --- |
    | file | Bestand met de toepassing die moet worden uitgevoerd | Pad (vereist) |
    | proxyUser | Gebruiker die moet worden geïmiteerd bij het uitvoeren van de taak | Tekenreeks |
    | className | Hoofd klasse java/Spark-toepassing | Tekenreeks |
    | argumenten | Opdracht regel argumenten voor de toepassing | Lijst met teken reeksen |
    | Pott | Potten die in deze sessie moeten worden gebruikt | Lijst met teken reeksen | 
    | pyFiles | Python-bestanden die moeten worden gebruikt in deze sessie | Lijst met teken reeksen |
    | bestanden | Bestanden die moeten worden gebruikt in deze sessie | Lijst met teken reeksen |
    | driverMemory | Hoeveelheid geheugen die moet worden gebruikt voor het stuur programma | Tekenreeks |
    | driverCores | Het aantal kernen dat moet worden gebruikt voor het stuur programma | Int |
    | executorMemory | Hoeveelheid geheugen die per bewerkings proces moet worden gebruikt | Tekenreeks |
    | executorCores | Aantal kernen dat voor elke uitvoerder moet worden gebruikt | Int |
    | numExecutors | Aantal actieve uitvoerendeers dat voor deze sessie wordt gestart | Int |
    | archieven | Archief archieven die moeten worden gebruikt in deze sessie | Lijst met teken reeksen |
    | wachtrij | De naam van de wachtrij met GARENs die moet worden ingediend| Tekenreeks |
    | naam | De naam van deze sessie | Tekenreeks |
    | belangen | Eigenschappen van Spark-configuratie | Toewijzing van sleutel = val |

    Antwoord tekst het gemaakte batch-object.

    | naam | description | type |
    | --- | ---| --- |
    | Id | Sessie-id | Int |
    | appId | Toepassings-ID van deze sessie | Tekenreeks |
    | appInfo | Gedetailleerde toepassings informatie | Toewijzing van sleutel = val |
    | logboek | Logboek regels | Lijst met teken reeksen |
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
   2. Wanneer het aantal rijen in de tabel kleiner is dan of gelijk is aan 100, ziet u het volgende bericht: "60 rijen worden weer gegeven voor Hive-tabel".
   3. Wanneer er geen inhoud in de tabel is, wordt het volgende bericht weer gegeven: " `0 rows are displayed for Hive table.` "

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

Gebruikers aan wie de rol alleen lezer voor het cluster is toegewezen, kunnen geen taken verzenden naar het HDInsight-cluster, noch de Hive-Data Base weer geven. Neem contact op met de Cluster beheerder om uw rol bij te werken naar de [**HDInsight-cluster operator**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) in de [Azure Portal](https://ms.portal.azure.com/). Als u geldige Ambari-referenties hebt, kunt u het cluster hand matig koppelen met behulp van de volgende richt lijnen.

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
  >U kunt gebruiken `Spark / Hive: List Cluster` om het gekoppelde cluster te controleren:
  >
  >![Koppeling naar het onderdeel Hulpprogramma's van Spark & voor Visual Studio code Reader](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Bladeren in een Data Lake Storage Gen2-account

Selecteer de Azure HDInsight Explorer om een Data Lake Storage Gen2 account uit te vouwen. U wordt gevraagd om de toegangs sleutel voor opslag in te voeren als uw Azure-account geen toegang heeft tot Gen2-opslag. Nadat de toegangs sleutel is gevalideerd, wordt het Data Lake Storage Gen2-account automatisch uitgevouwen.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Taken verzenden naar een HDInsight-cluster met Data Lake Storage Gen2

Een taak verzenden naar een HDInsight-cluster met behulp van Data Lake Storage Gen2. U wordt gevraagd om de toegangs sleutel voor opslag in te voeren als uw Azure-account geen schrijf toegang heeft tot Gen2-opslag. Nadat de toegangs sleutel is gevalideerd, wordt de taak verzonden.

![Spark-& Hive-Hulpprogram Ma's voor Visual Studio code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> U kunt de toegangs sleutel voor het opslag account ophalen van de Azure Portal. Zie [toegangs sleutels voor opslag accounts beheren](../storage/common/storage-account-keys-manage.md)voor meer informatie.

## <a name="unlink-cluster"></a>Cluster ontkoppelen

1. Ga in de menu balk naar het **View**  >  **opdracht palet**weer geven en voer **Spark/Hive: ontkoppelen van een cluster**.  

2. Selecteer een cluster om te ontkoppelen.  

3. Raadpleeg de **uitvoer** weergave voor verificatie.  

## <a name="sign-out"></a>Afmelden  

Ga in de menu balk naar het **View**  >  **opdracht palet**weer geven en voer **Azure in: Meld**u aan.

## <a name="known-issues"></a>Bekende problemen
### <a name="python-on-2020580290-version-is-not-supported-on-this-extention"></a>Python op 2020.5.80290-versie wordt niet ondersteund voor deze uitbrei ding 

Kan geen verbinding maken met Jupyter notebook. is een bekend probleem voor python-versie 2020.5.80290. Gebruikers wordt aangeraden de **2020.4.76186** -versie van MS-python te gebruiken om dit probleem te voor komen.

![bekende problemen](./media/hdinsight-for-vscode/known-issue.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor een video die demonstreert met behulp van Spark-& Hive voor Visual Studio code [spark & Hive voor Visual Studio code](https://go.microsoft.com/fwlink/?linkid=858706).
