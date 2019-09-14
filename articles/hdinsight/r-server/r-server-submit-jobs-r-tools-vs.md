---
title: Taken verzenden vanuit R-Hulpprogram Ma's voor Visual Studio-Azure HDInsight
description: Verzend R-taken van uw lokale Visual Studio-machine naar een HDInsight-cluster.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 2d53f1bfc6eade535bfb1b3bb07d5115ffe5fc80
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967883"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Taken verzenden vanuit R-hulpprogramma's voor Visual Studio

[R-Hulpprogram ma's voor Visual Studio](https://www.visualstudio.com/vs/rtvs/) (RTVS) is een gratis open-source uitbrei ding voor de Community (gratis), Professional en Enter prise-edities van zowel [Visual studio 2017](https://www.visualstudio.com/downloads/)als [Visual Studio 2015 update 3](https://go.microsoft.com/fwlink/?LinkId=691129) of hoger. RTVS is niet beschikbaar voor [Visual Studio 2019](https://docs.microsoft.com/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?view=vs-2019).

RTVS breidt uw R-werk stroom uit door hulpprogram ma's te bieden zoals het [R Interactive Window](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (repl), IntelliSense (code completion), de [visualisatie te tekenen](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) via R-bibliotheken zoals Ggplot2 en ggviz, [r-code fout opsporing](https://docs.microsoft.com/visualstudio/rtvs/debugging)en meer.

## <a name="set-up-your-environment"></a>Uw omgeving instellen

1. Installeer [R-Hulpprogram ma's voor Visual Studio](https://docs.microsoft.com/visualstudio/rtvs/installation).

    ![RTVS installeren in Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Selecteer de werk belasting *Data Science en analytische toepassingen* en selecteer vervolgens de **ondersteuning voor r-taal**, runtime- **ondersteuning voor r-ontwikkeling**en **micro soft R-client** opties.

3. U moet open bare en persoonlijke sleutels hebben voor SSH-verificatie.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Installeer [ml server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) op de computer. ML server biedt de [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) functies `RxSpark` en.

5. Installeer [putty](https://www.putty.org/) om een compute-context te `RevoScaleR` bieden voor het uitvoeren van functies van uw lokale client naar uw HDInsight-cluster.

6. U hebt de optie om de data Science-instellingen toe te passen op uw Visual Studio-omgeving, die een nieuwe indeling biedt voor uw werk ruimte voor de R-hulpprogram ma's.
   1. Als u de huidige Visual Studio-instellingen wilt opslaan, gebruikt u de opdracht **Hulpprogram ma's > instellingen voor importeren en exporteren** en selecteert u **geselecteerde omgevings instellingen exporteren** en geeft u een bestands naam op. Als u deze instellingen wilt herstellen, gebruikt u dezelfde opdracht en selecteert u **geselecteerde omgevings instellingen importeren**.

   2. Ga naar het menu-item **R tools** en selecteer **Data Science Settings...** .

       ![Instellingen voor data wetenschappen...](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Met de aanpak in stap 1 kunt u ook uw persoonlijke gegevens wetenschapper-indeling opslaan en herstellen, in plaats van de opdracht **Data Science Settings** te herhalen.

## <a name="execute-local-r-methods"></a>Lokale R-methoden uitvoeren

1. Maak uw cluster met HDInsight ML-Services.
2. Installeer de [RTVS-extensie](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Down load het [zip-bestand voor beelden](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Open `examples/Examples.sln` om de oplossing in Visual Studio te starten.
5. Open het `1-Getting Started with R.R` bestand in de `A first look at R` map Solution.
6. Vanaf de bovenkant van het bestand, drukt u op CTRL + ENTER om elke regel, één per keer, naar het venster R Interactive te verzenden. Sommige regels kunnen even duren als ze pakketten installeren.
    * U kunt ook alle regels in het R-bestand selecteren (CTRL + A) en vervolgens alle uitvoeren (CTRL + ENTER) of het pictogram voor het uitvoeren van een interactieve taak op de werk balk selecteren.

        ![Interactief uitvoeren](./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png)

7. Nadat u alle regels in het script hebt uitgevoerd, ziet u een uitvoer die er ongeveer als volgt uitzien:

    ![Werkruimte](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Taken verzenden naar een HDInsight ML Services-cluster

Als u een micro soft ml server/micro soft R-client gebruikt vanaf een Windows-computer met Putty, kunt u een COMPUTE `RevoScaleR` -context maken waarmee gedistribueerde functies van uw lokale client naar uw HDInsight-cluster worden uitgevoerd. Gebruik `RxSpark` om de compute-context te maken, waarbij u uw gebruikers naam opgeeft, het Edge-knoop punt van het Apache Hadoop cluster, SSH-switches, enzovoort.

1. Het knooppunt adres van de ml van het service `CLUSTERNAME-ed-ssh.azurehdinsight.net` - `CLUSTERNAME` Edge op HDInsight is de naam van uw cluster met ml-Services.

1. Plak de volgende code in het venster R Interactive in Visual Studio en wijzig de waarden van de installatie variabelen zodat deze overeenkomen met uw omgeving.

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

   ![De Spark-context instellen](./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png)

1. Voer de volgende opdrachten uit in het venster R Interactive:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

    ![Geslaagde RX-](./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png) opdracht voor het uitvoeren van een
1. Controleer of het `people.json` bestand `/user/RevoShare/newUser` is gekopieerd van de map voorbeeld gegevens naar de zojuist gemaakte map: `rxHadoopCopy`

    1. Selecteer in het deel venster cluster van HDInsight ML-Services in azure **opslag accounts** in het menu links.

        ![Opslagaccounts](./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png)

    2. Selecteer het standaard opslag account voor uw cluster en noteer de naam van de container/map.

    3. Selecteer **containers** in het menu links in het deel venster opslag account.

        ![Containers](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. Selecteer de container naam van uw cluster, blader naar de map **gebruiker** (mogelijk moet u op *extra laden* onder aan de lijst) en selecteer vervolgens *RevoShare*en **newuser**. Het `people.json` bestand moet in de `newUser` map worden weer gegeven.

        ![Gekopieerd bestand](./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png)

1. Wanneer u klaar bent met het gebruik van de huidige Apache Spark context, moet u deze stoppen. U kunt niet meerdere contexten tegelijk uitvoeren.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Volgende stappen

* [Opties voor Compute-context voor MILLILITERs Services in HDInsight](r-server-compute-contexts.md)
* De [combi natie van Scaler en sparkr](../hdinsight-hadoop-r-scaler-sparkr.md) biedt een voor beeld van vertraagde voor spellingen voor de vliegtuig vlucht.

