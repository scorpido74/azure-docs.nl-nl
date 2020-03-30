---
title: Vacatures indienen bij R Tools voor Visual Studio - Azure HDInsight
description: Verzend R-taken van uw lokale Visual Studio-machine in een HDInsight-cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 73d1478ec2d6c90428f22a30ec82634df115d2f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435256"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Taken verzenden vanuit R-hulpprogramma's voor Visual Studio

[R Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) is een gratis, open-source extensie voor de Community (gratis), Professional en Enterprise edities van zowel Visual Studio [2017](https://www.visualstudio.com/downloads/)als [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129) of hoger. RTVS is niet beschikbaar voor [Visual Studio 2019](https://docs.microsoft.com/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?view=vs-2019).

RTVS verbetert uw R-workflow door tools aan te bieden zoals het [R Interactive-venster](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), intellisense (codevoltooiing), [plotvisualisatie](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) via R-bibliotheken zoals ggplot2 en ggviz, [R-code debugging](https://docs.microsoft.com/visualstudio/rtvs/debugging)en meer.

## <a name="set-up-your-environment"></a>Uw omgeving instellen

1. [R-hulpprogramma's voor Visual Studio installeren](/visualstudio/rtvs/installing-r-tools-for-visual-studio).

    ![RTVS installeren in Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Selecteer de werkbelasting *gegevenswetenschap en analytische toepassingen* en selecteer vervolgens de **R-taalondersteuning,** **Runtime-ondersteuning voor R-ontwikkeling**en **Microsoft R-clientopties.**

3. U moet openbare en privésleutels hebben voor SSH-verificatie.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Installeer [ML Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) op uw machine. ML Server [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) biedt `RxSpark` de functies en functies.

5. Installeer [PuTTY](https://www.putty.org/) om een rekencontext te bieden om functies van uw lokale client naar uw HDInsight-cluster uit te voeren. `RevoScaleR`

6. U hebt de mogelijkheid om de Instellingen voor gegevenswetenschap toe te passen op uw Visual Studio-omgeving, die een nieuwe indeling biedt voor uw werkruimte voor de R-gereedschappen.
   1. Als u uw huidige Instellingen voor Visual Studio wilt opslaan, gebruikt u de opdracht **Extra > Instellingen importeren en exporteren,** selecteert u Geselecteerde **omgevingsinstellingen exporteren** en een bestandsnaam opgeven. Als u deze instellingen wilt herstellen, gebruikt u dezelfde opdracht en selecteert **u Geselecteerde omgevingsinstellingen importeren**.

   2. Ga naar het **menu-item R-hulpprogramma's** en selecteer **vervolgens Gegevenswetenschapsinstellingen...**.

       ![Instellingen voor Visual Studio Data Science](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Met behulp van de aanpak in stap 1 u ook uw persoonlijke lay-out van gegevenswetenschapper opslaan en herstellen, in plaats van de opdracht **Gegevenswetenschapsinstellingen** te herhalen.

## <a name="execute-local-r-methods"></a>Lokale R-methoden uitvoeren

1. Maak uw HDInsight ML Services-cluster.
2. Installeer de [RTVS-extensie](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Download de [monsters zip-bestand](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Open `examples/Examples.sln` om de oplossing te lanceren in Visual Studio.
5. Open `1-Getting Started with R.R` het bestand `A first look at R` in de oplossingsmap.
6. Druk vanaf boven aan het bestand op Ctrl+Enter om elke regel één voor één naar het interactieve r-venster te verzenden. Sommige regels kunnen een tijdje duren als ze pakketten installeren.
    * U ook alle regels in het R-bestand selecteren (Ctrl+A), vervolgens alle regels uitvoeren (Ctrl+Enter) of het pictogram Interactief uitvoeren op de werkbalk selecteren.

        ![Visual Studio voert interactief uit](./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png)

7. Nadat u alle regels in het script hebt uitgevoerd, ziet u een uitvoer die vergelijkbaar is met deze:

    ![Hulpmiddelen voor Visual Studio workspace R](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Taken verzenden naar een HDInsight ML Services-cluster

Met behulp van een Microsoft ML Server/Microsoft R-client vanaf een Windows-computer die is `RevoScaleR` uitgerust met PuTTY, u een rekencontext maken die gedistribueerde functies van uw lokale client naar uw HDInsight-cluster uitvoert. Gebruik `RxSpark` om de rekencontext te maken, waarbij uw gebruikersnaam wordt opgegeven, het randknooppunt van het Cluster Apache Hadoop, SSH-switches enzovoort.

1. Het adres van het ML Services-randknooppunt op HDInsight is `CLUSTERNAME-ed-ssh.azurehdinsight.net` de plaats waar `CLUSTERNAME` de naam van uw ML Services-cluster is.

1. Plak de volgende code in het interactieve R-venster in Visual Studio en wijzig de waarden van de instelvariabelen die overeenkomen met uw omgeving.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
          sshHostname = mySshHostname,
          sshSwitches = mySshSwitches,
          sshProfileScript = mySshProfileScript,
          consoleOutput = TRUE,
          hdfsShareDir = myHdfsShareDir,
          shareDir = myShareDir,
          sshClientDir = mySshClientDir
    )

    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```

   ![apache spark de context instellen](./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png)

1. Voer de volgende opdrachten uit in het interactieve R-venster:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

    ![Succesvolle rx-opdrachtuitvoering](./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png) a
1. Controleer of `rxHadoopCopy` het bestand `people.json` met succes is gekopieerd van `/user/RevoShare/newUser` de voorbeeldgegevensmap naar de nieuw gemaakte map:

    1. Selecteer **opslagaccounts** in het linkermenu in het clustervenster HDInsight ML Services in Azure.

        ![Azure HDInsight-opslagaccounts](./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png)

    2. Selecteer het standaardopslagaccount voor uw cluster en noteer de naam container/directory.

    3. Selecteer **Containers** in het linkermenu in het deelvenster Opslagaccount.

        ![Azure HDInsight-opslagcontainers](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. Selecteer de containernaam van uw cluster, blader naar de **gebruikersmap** (mogelijk moet u onder aan de lijst *op Laden* klikken) en vervolgens *RevoShare*selecteren en vervolgens **de nieuweGebruiker**. Het `people.json` bestand moet worden `newUser` weergegeven in de map.

        ![LOCATIE van de gekopieerde bestandsmap van HDInsight](./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png)

1. Nadat u klaar bent met het gebruik van de huidige Apache Spark-context, moet u deze stoppen. U niet meerdere contexten tegelijk uitvoeren.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Volgende stappen

* [Contextopties berekenen voor ML Services op HDInsight](r-server-compute-contexts.md)
* [Het combineren van ScaleR en SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) is een voorbeeld van voorspellingen over vliegtuigvertraging.
