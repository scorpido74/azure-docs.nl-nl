---
title: Azure HDInsight voor Visual Studio-code
description: Meer informatie over het gebruik van de Spark & Hive Tools (Azure HDInsight) voor Visual Studio Code voor het maken en verzenden van query's en scripts.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 9a81868d678b4c0277e904e879c73185a378bf70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435685"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Spark & Hive-hulpprogramma's gebruiken voor visual studiocode

Meer informatie over het gebruik van Spark-& Hive-hulpprogramma's voor Visual Studio-code om Apache Hive-batchtaken, interactieve Hive-query's en PySpark-scripts voor Apache Spark te maken en in te dienen. Eerst beschrijven we hoe we Spark-& Hive Tools in Visual Studio Code kunnen installeren, en daarna doorlopen we hoe we vacatures kunnen indienen bij Spark & Hive Tools.  

Spark & Hive Tools kunnen worden geïnstalleerd op platforms die worden ondersteund door Visual Studio Code, waaronder Windows, Linux en macOS. Let op de volgende voorwaarden voor verschillende platforms.

## <a name="prerequisites"></a>Vereisten

De volgende items zijn vereist voor het voltooien van de stappen in dit artikel:

- Een Azure HDInsight-cluster. Zie Aan de [slag met HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)om een cluster te maken. Of gebruik een Spark- en Hive-cluster dat een Apache Livy-eindpunt ondersteunt.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono is alleen vereist voor Linux en macOS.
- [Een interactieve PySpark-omgeving voor Visual Studio Code.](set-up-pyspark-interactive-environment.md)
- Een lokale directory. In dit artikel wordt **C:\HD\HDexample gebruikt.**

## <a name="install-spark--hive-tools"></a>Spark & Hive-hulpprogramma's installeren

Nadat u aan de vereisten hebt voldoen, u Spark & Hive Tools voor Visual Studio Code installeren door de volgende stappen te volgen:

1. Open Visual Studio Code.

2. Navigeer in de menubalk naar**Extensies** **weergeven.** > 

3. Voer in het zoekvak **Spark & Hive**in.

4. Selecteer **Spark & Hive-hulpprogramma's** in de zoekresultaten en selecteer **Vervolgens Installeren:**

   ![Spark & Hive voor Visual Studio Code Python installeren](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Selecteer **Opnieuw laden** indien nodig.

## <a name="open-a-work-folder"></a>Een werkmap openen

Voer de volgende stappen uit om een werkmap te openen en een bestand te maken in Visual Studio Code:

1. Navigeer in de menubalk naar **Map Openen bestand...** > **Open Folder...**  >  **C:\HD\HDexample**en selecteer vervolgens de knop **Map selecteren.** De map wordt links in de **Explorer-weergave** weergegeven.

2. Selecteer in de **Explorer-weergave** de map **HDexample** en selecteer vervolgens het pictogram **Nieuw bestand** naast de werkmap:

   ![nieuw bestandspictogram voor visuele studiocode](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Geef het nieuwe bestand `.hql` een naam met behulp `.py` van de (Hive queries) of de (Spark script) bestandsextensie. In dit voorbeeld wordt **HelloWorld.hql gebruikt.**

## <a name="set-the-azure-environment"></a>De Azure-omgeving instellen

Voer voor een nationale cloudgebruiker de volgende stappen uit om eerst de Azure-omgeving in te stellen en vervolgens de opdracht **Azure: Aanmelden** gebruiken om u aan te melden bij Azure:

1. Navigeer naar**Instellingen** **voor bestandsvoorkeuren** > **Preferences** > .
2. Zoeken op de volgende tekenreeks: **Azure: Cloud**.
3. Selecteer de nationale cloud in de lijst:

   ![Standaardconfiguratie voor inloginvoer instellen](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Verbinding maken met een Azure-account

Voordat u scripts indienen naar uw clusters vanuit Visual Studio Code, moet u verbinding maken met uw Azure-account of een cluster koppelen (met behulp van gebruikers- en wachtwoordreferenties van Apache Ambari of een account dat is verbonden met een domein). Volg de volgende stappen om verbinding te maken met Azure:

1. Navigeer in de menubalk naar **Opdrachtpalet weergeven...** > **Command Palette...** en voer **Azure in: Aanmelden:**

    ![Spark & Hive-hulpprogramma's voor het inloggen met visual studiocode](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Volg de aanmeldingsinstructies om u aan te melden bij Azure. Nadat u bent verbonden, wordt uw Azure-accountnaam weergegeven op de statusbalk onder aan het venster Visual Studio Code.  

## <a name="link-a-cluster"></a>Een cluster koppelen

### <a name="link-azure-hdinsight"></a>Koppeling: Azure HDInsight

U een normaal cluster koppelen met behulp van een [door Apache Ambari](https://ambari.apache.org/)beheerde gebruikersnaam, of u kunt `user1@contoso.com`een Secure Hadoop-cluster van het Enterprise Security Pack koppelen met behulp van een domeingebruikersnaam (zoals: ).

1. Navigeer in de menubalk naar**Opdrachtpalet** **weergeven...** > en voer **Spark / Hive: Een cluster koppelen.**

   ![Opdrachtpaletkoppelingcluster, opdracht](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Selecteer gekoppeld clustertype **Azure HDInsight**.

3. Voer de URL van het HDInsight-cluster in.

4. Voer uw Ambari-gebruikersnaam in; de standaardinstelling is **beheerder**.

5. Voer uw Ambari-wachtwoord in.

6. Selecteer het clustertype.

7. De weergavenaam van het cluster instellen (optioneel).

8. **Uitvoerweergave** controleren voor verificatie.

   > [!NOTE]  
   > De gekoppelde gebruikersnaam en wachtwoord worden gebruikt als het cluster zowel is aangemeld bij het Azure-abonnement als een cluster heeft gekoppeld.  

### <a name="link-generic-livy-endpoint"></a>Link: Generic Livy endpoint

1. Navigeer in de menubalk naar**Opdrachtpalet** **weergeven...** > en voer **Spark / Hive: Een cluster koppelen.**

2. Selecteer gekoppeld clustertype **Algemeen Livy-eindpunt**.

3. Voer het algemene Livy-eindpunt in. Bijvoorbeeld: http\:-10.172.41.42:18080.

4. Selecteer autorisatietype **Basic** of **Geen**.  Als u **Basic**selecteert:  
    &emsp;a. Voer uw Ambari-gebruikersnaam in; de standaardinstelling is **beheerder**.  
    &emsp;B. Voer uw Ambari-wachtwoord in.

5. **Uitvoerweergave** controleren voor verificatie.

## <a name="list-clusters"></a>Lijstclusters

1. Navigeer in de menubalk naar**Opdrachtpalet** **weergeven...** > en voer **Spark / Hive: List Cluster in.**

2. Selecteer het gewenste abonnement.

3. Bekijk de **uitvoerweergave.** In deze weergave worden uw gekoppelde cluster (of clusters) en alle clusters onder uw Azure-abonnement weergegeven:

    ![Een standaardclusterconfiguratie instellen](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Het standaardcluster instellen

1. Open de **map HDexample** die [eerder](#open-a-work-folder)is besproken, indien gesloten.  

2. Selecteer het **helloworld.hql-bestand** dat [eerder](#open-a-work-folder)is gemaakt. Het wordt geopend in de scripteditor.

3. Klik met de rechtermuisknop op de scripteditor en selecteer **Vervolgens Spark / Hive: Standaardcluster instellen**.  

4. [Maak verbinding met](#connect-to-an-azure-account) uw Azure-account of koppel een cluster als u dit nog niet hebt gedaan.

5. Selecteer een cluster als het standaardcluster voor het huidige scriptbestand. De hulpprogramma's werken de **. VSCode\settings.json** configuratiebestand:

   ![Standaardclusterconfiguratie instellen](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Interactieve Hive-query's en Hive-batchscripts verzenden

Met Spark & Hive Tools voor Visual Studio Code u interactieve Hive-query's en Hive-batchscripts indienen bij uw clusters.

1. Open de **map HDexample** die [eerder](#open-a-work-folder)is besproken, indien gesloten.  

2. Selecteer het **helloworld.hql-bestand** dat [eerder](#open-a-work-folder)is gemaakt. Het wordt geopend in de scripteditor.

3. Kopieer en plak de volgende code in uw Hive-bestand en sla deze op:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Maak verbinding met](#connect-to-an-azure-account) uw Azure-account of koppel een cluster als u dit nog niet hebt gedaan.

5. Klik met de rechtermuisknop op de scripteditor en selecteer **Hive: Interactief** om de query in te dienen of gebruik de sneltoets Ctrl+Alt+I.  Selecteer **Hive: Batch** om het script in te dienen of gebruik de sneltoets Ctrl+Alt+H.  

6. Als u geen standaardcluster hebt opgegeven, selecteert u een cluster. Met de hulpprogramma's u ook een codeblok indienen in plaats van het hele scriptbestand met behulp van het contextmenu. Na enkele ogenblikken worden de queryresultaten weergegeven in een nieuw tabblad:

   ![Resultaat interactieve Apache Hive-query](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **Deelvenster RESULTATEN:** U het hele resultaat opslaan als CSV-, JSON- of Excel-bestand op een lokaal pad of gewoon meerdere regels selecteren.

    - **DEELVENSTER BERICHTEN:** Wanneer u een **regelnummer** selecteert, springt het naar de eerste regel van het lopende script.

## <a name="submit-interactive-pyspark-queries"></a>Interactieve PySpark-query's verzenden

Voer de volgende stappen uit om interactieve PySpark-query's in te dienen:

1. Open de **map HDexample** die [eerder](#open-a-work-folder)is besproken, indien gesloten.  

2. Maak een nieuw **HelloWorld.py** bestand, na de [eerdere](#open-a-work-folder) stappen.

3. Kopieer en plak de volgende code in het scriptbestand:

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

4. [Maak verbinding met](#connect-to-an-azure-account) uw Azure-account of koppel een cluster als u dit nog niet hebt gedaan.

5. Selecteer alle code, klik met de rechtermuisknop op de scripteditor en selecteer **Spark: PySpark Interactive** om de query in te dienen. Of gebruik de snelkoppeling Ctrl+Alt+I.

   ![pyspark interactieve contextmenu](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Selecteer het cluster als u geen standaardcluster hebt opgegeven. Na een paar ogenblikken worden de resultaten **van Python Interactive** weergegeven in een nieuw tabblad. Met de hulpprogramma's u ook een codeblok indienen in plaats van het hele scriptbestand met behulp van het contextmenu:

   ![pyspark interactieve python interactief venster](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. Typ **%%info**en druk op Shift+Enter om de taakgegevens weer te geven (optioneel):

   ![pyspark interactieve baaninformatie bekijken](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. De tool ondersteunt ook de **Spark SQL-query:**

   ![Pyspark Interactive bekijk resultaat](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   De indieningsstatus wordt links van de onderste statusbalk weergegeven wanneer u query's uitvoert. Dien geen andere query's in wanneer de status **PySpark Kernel (bezet)** is.  

   > [!NOTE]
   >
   > Wanneer **Python Extension Enabled** is gewist in de instellingen (deze is standaard geselecteerd), gebruiken de ingediende pyspark-interactieresultaten het oude venster:
   >
   > ![pyspark interactieve python extensie uitgeschakeld](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>PySpark-batchtaak verzenden

1. Open de **map HDexample** die u [eerder](#open-a-work-folder)hebt besproken, indien gesloten.  

2. Maak een nieuw **BatchFile.py** bestand door de [eerdere](#open-a-work-folder) stappen te volgen.

3. Kopieer en plak de volgende code in het scriptbestand:

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

4. [Maak verbinding met](#connect-to-an-azure-account) uw Azure-account of koppel een cluster als u dit nog niet hebt gedaan.

5. Klik met de rechtermuisknop op de scripteditor en selecteer **Spark: PySpark Batch**of gebruik de sneltoets Ctrl+Alt+H.

6. Selecteer een cluster om uw PySpark-taak in te dienen:

   ![Python-taakresultaatuitvoer verzenden](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Nadat u een Python-taak hebt verzonden, worden logboeken voor indiening weergegeven in **het** uitvoervenster in Visual Studio Code. De URL van de Spark UI en de URL van de garengebruikers worden ook weergegeven. U de URL openen in een webbrowser om de taakstatus bij te houden.

## <a name="apache-livy-configuration"></a>Apache Livy-configuratie

[Apache Livy](https://livy.incubator.apache.org/) configuratie wordt ondersteund. U het configureren in de **. VSCode\settings.json-bestand** in de werkruimtemap. Momenteel ondersteunt Livy-configuratie alleen Python-script. Zie [Livy README voor](https://github.com/cloudera/livy/blob/master/README.rst )meer informatie.

<a id="triggerlivyconf"></a>**Hoe livy-configuratie te activeren**

Methode 1  
1. Navigeer in de menubalk naar**Instellingen** **voor bestandsvoorkeuren** > **Preferences** > .
2. Typ **HDInsight Job Submission: Livy Conf**in het vak **Zoekinstellingen.**  
3. Selecteer **Bewerken in settings.json** voor het relevante zoekresultaat.

Methode 2 Een bestand verzenden en u zien dat de map .vscode automatisch wordt toegevoegd aan de werkmap. U de Livy-configuratie bekijken door **.vscode\settings.json**te selecteren.

+ De projectinstellingen:

    ![HDInsight Apache Livy configuratie](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Stel voor de instellingen **driverMemory** en **executorMemory** de waarde en eenheid in. Bijvoorbeeld: 1g of 1024m.

+ Ondersteunde Livy-configuraties:

    **POST /batches** Aanvraaginstantie

    | name | description | type |
    | :- | :- | :- |
    | file | Bestand met de uit te voeren toepassing | Pad (vereist) |
    | proxyGebruiker | Gebruiker die zich voordoet bij het uitvoeren van de taak | Tekenreeks |
    | Classname | Toepassing Java/Spark hoofdklasse | Tekenreeks |
    | argumenten | Opdrachtregelargumenten voor de toepassing | Lijst met tekenreeksen |
    | Potten | Potten te gebruiken in deze sessie | Lijst met tekenreeksen | 
    | pyFiles | Python-bestanden die in deze sessie moeten worden gebruikt | Lijst met tekenreeksen |
    | bestanden | Bestanden die in deze sessie moeten worden gebruikt | Lijst met tekenreeksen |
    | driverMemory | Hoeveelheid geheugen die moet worden gebruikt voor het stuurprogrammaproces | Tekenreeks |
    | driverCores | Aantal te gebruiken cores voor het stuurprogrammaproces | Int |
    | executorMemory | Hoeveelheid geheugen die per executeurproces moet worden gebruikt | Tekenreeks |
    | executeurCores | Aantal te gebruiken cores voor elke executeur | Int |
    | numExecutors | Aantal uitvoerders dat voor deze sessie moet worden gestart | Int |
    | Archieven | Archieven die in deze sessie kunnen worden gebruikt | Lijst met tekenreeksen |
    | wachtrij | Naam van de GARENwachtrij die moet worden ingediend bij| Tekenreeks |
    | name | Naam van deze sessie | Tekenreeks |
    | Conf | Spark-configuratie-eigenschappen | Kaart van key=val |

    Reactielichaam Het gemaakte batchobject.

    | name | description | type |
    | :- | :- | :- |
    | id | Sessie-id | Int |
    | appId | Toepassings-id van deze sessie | Tekenreeks |
    | appInfo | Gedetailleerde toepassingsgegevens | Kaart van key=val |
    | logboek | Logboekregels | Lijst met tekenreeksen |
    | state |Batchstatus | Tekenreeks |

    > [!NOTE]
    > De toegewezen Livy-config wordt weergegeven in het uitvoervenster wanneer u het script verzendt.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integreren met Azure HDInsight vanuit Explorer

U de Hive Table in uw clusters rechtstreeks bekijken via de **Azure HDInsight-verkenner:**

1. [Maak verbinding met](#connect-to-an-azure-account) uw Azure-account als u dit nog niet hebt gedaan.

2. Selecteer het **Azure-pictogram** in de meest linkse kolom.

3. Vouw **AZURE: HDINSIGHT**in het linkerdeelvenster uit. De beschikbare abonnementen en clusters worden weergegeven.

4. Vouw het cluster uit om de database met metagegevens van Hive en het tabelschema weer te geven.

5. Klik met de rechtermuisknop op de Hive-tabel. Bijvoorbeeld: **hivesampletable**. Selecteer **Voorbeeld**.

   ![Spark & Hive for Visual Studio Code preview hive table](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Het venster **Voorbeeldresultaten** wordt geopend:

   ![Voorbeeldvenster Spark & Hive voor Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- Deelvenster RESULTATEN

   U het hele resultaat opslaan als csv-, JSON- of Excel-bestand op een lokaal pad of gewoon meerdere regels selecteren.

- Deelvenster BERICHTEN
   1. Wanneer het aantal rijen in de tabel groter is dan 100, ziet u het volgende bericht: 'De eerste 100 rijen worden weergegeven voor de tabel Hive.'
   2. Wanneer het aantal rijen in de tabel kleiner is dan of gelijk is aan 100, ziet u een bericht als volgt: '60 rijen worden weergegeven voor de tabel Hive'.
   3. Als er geen inhoud in de tabel staat, ziet u het volgende bericht: '0 rijen worden weergegeven voor de tabel Hive'.

        >[!NOTE]
        >
        >Installeer in Linux xclip om kopieertabelgegevens in te schakelen.
        >
        >![Spark & Hive voor Visual Studio-code in Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Extra functies

Spark & Hive for Visual Studio Code ondersteunt ook de volgende functies:

- **IntelliSense automatisch aanvullen**. Er verschijnen suggesties voor trefwoorden, methoden, variabelen en andere programmeerelementen. Verschillende pictogrammen vertegenwoordigen verschillende soorten objecten:

    ![Spark & Hive-hulpprogramma's voor IntelliSense-objecten met visual studiocode](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **Foutmarkering IntelliSense**. De taalservice onderstreept bewerkingsfouten in het Hive-script.     
- **Syntaxishoogtepunten**. De taalservice gebruikt verschillende kleuren om variabelen, trefwoorden, gegevenstypen, functies en andere programmeerelementen te onderscheiden:

    ![Spark & Hive Tools for Visual Studio Code syntaxis highlights](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Rol met alleen voor lezers

Gebruikers die de rol met alleen-reader voor het cluster toegewezen krijgen, kunnen geen taken meer indienen bij het HDInsight-cluster en kunnen de Hive-database ook niet bekijken. Neem contact op met de clusterbeheerder om uw rol te upgraden naar [**HDInsight Cluster Operator**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) in de [Azure-portal.](https://ms.portal.azure.com/) Als u geldige Ambari-referenties hebt, u het cluster handmatig koppelen aan de hand van de volgende richtlijnen.

### <a name="browse-the-hdinsight-cluster"></a>Blader door het HDInsight-cluster  

Wanneer u de Azure HDInsight-verkenner selecteert om een HDInsight-cluster uit te vouwen, wordt u gevraagd het cluster te koppelen als u de rol hebt die alleen voor de lezer geldt voor het cluster. Gebruik de volgende methode om een koppeling te maken naar het cluster met behulp van uw Ambari-referenties.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>De taak verzenden naar het HDInsight-cluster

Wanneer u taak indient bij een HDInsight-cluster, wordt u gevraagd het cluster te koppelen als u zich in de rol met alleen voor het lezen voor het cluster bevindt. Gebruik de volgende stappen om een koppeling te maken naar het cluster met Ambari-referenties.

### <a name="link-to-the-cluster"></a>Koppeling naar het cluster

1. Voer een geldige Ambari-gebruikersnaam in.
2. Voer een geldig wachtwoord in.

   ![Spark & Hive-hulpprogramma's voor gebruikersnaam van visual studiocode](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Spark & Hive-hulpprogramma's voor het wachtwoord van de Visual Studio-code](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >U kunt `Spark / Hive: List Cluster` het gekoppelde cluster controleren:
  >
  >![Spark & Hive Tools voor Visual Studio Code Reader Gekoppeld](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Blader door een Data Lake Storage Gen2-account

Wanneer u de Azure HDInsight explorer selecteert om een Data Lake Storage Gen2-account uit te breiden, wordt u gevraagd de opslagtoegangssleutel in te voeren als uw Azure-account geen toegang heeft tot Gen2-opslag. Nadat de toegangssleutel is gevalideerd, wordt het Data Lake Storage Gen2-account automatisch uitgebreid.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Taken verzenden naar een HDInsight-cluster met Data Lake Storage Gen2

Wanneer u een taak indient bij een HDInsight-cluster met Data Lake Storage Gen2, wordt u gevraagd de opslagtoegangssleutel in te voeren als uw Azure-account geen schrijftoegang heeft tot Gen2-opslag. Nadat de toegangssleutel is gevalideerd, wordt de taak met succes ingediend.

![Spark & Hive-hulpprogramma's voor AccessKey voor Visual Studio-code](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> U de toegangssleutel voor het opslagaccount ophalen via de Azure-portal. Zie [Toegangssleutels voor opslagaccount beheren](../storage/common/storage-account-keys-manage.md)voor meer informatie .

## <a name="unlink-cluster"></a>Cluster ontkoppelen

1. Ga in de menubalk naar**Opdrachtpalet** **weergeven** > en voer **Vervolgens Spark / Hive: Koppel een cluster los.**  

2. Selecteer een cluster om de koppeling los te koppelen.  

3. Zie de **uitvoerweergave** voor verificatie.  

## <a name="sign-out"></a>Afmelden  

Ga in de menubalk naar**Opdrachtpalet** **weergeven** > en voer **Azure in: Afmelden**.

## <a name="next-steps"></a>Volgende stappen

Zie Spark & Hive voor Visual Studio Code voor een video die spark & Hive voor Visual Studio Code [gebruikt.](https://go.microsoft.com/fwlink/?linkid=858706)
