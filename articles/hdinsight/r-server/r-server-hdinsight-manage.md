---
title: ML Services-cluster beheren op HDInsight - Azure
description: Meer informatie over het beheren van verschillende taken in het ML Services-cluster in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: b2c16c27c0dfc0c30a99c52544cc4d2278eadfc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647727"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>ML Services-cluster beheren op Azure HDInsight

In dit artikel leert u hoe u een bestaand ML Services-cluster op Azure HDInsight beheert om taken uit te voeren, zoals het toevoegen van meerdere gelijktijdige gebruikers, het op afstand verbinden met een ML Services-cluster, het wijzigen van de compute context, enz.

## <a name="prerequisites"></a>Vereisten

* Een ML Services-cluster op HDInsight. Zie [Apache Hadoop-clusters maken met de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en SELECTEER **ML-services** voor **clustertype**.

* Een SSH-client (Secure Shell): er wordt een SSH-client gebruikt om extern verbinding te maken met het HDInsight-cluster en om opdrachten rechtstreeks uit te voeren op het cluster. Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie.

## <a name="enable-multiple-concurrent-users"></a>Meerdere gelijktijdige gebruikers inschakelen

U meerdere gelijktijdige gebruikers inschakelen voor het ML Services-cluster op HDInsight door meer gebruikers toe te voegen voor het randknooppunt waarop de RStudio-communityversie wordt uitgevoerd. Wanneer u een HDInsight-cluster maakt, moet u twee gebruikers opgeven, te weten een HTTP-gebruiker en een SSH-gebruiker:

![Inlogparameters voor HDI Azure-portalen](./media/r-server-hdinsight-manage/hdi-concurrent-users1.png)

- **Gebruikersnaam voor aanmelding cluster**: een HTTP-gebruiker voor verificatie via de HDInsight-gateway die wordt gebruikt voor het beveiligen van de HDInsight-clusters die u hebt gemaakt. Deze HTTP-gebruiker wordt gebruikt om toegang te krijgen tot de Apache Ambari UI, Apache Hadoop YARN UI, evenals andere UI-componenten.
- **Secure Shell (SSH)-gebruikersnaam**: een SSH-gebruiker voor toegang tot het cluster via Secure Shell. Deze gebruiker is een gebruiker in het Linux-systeem voor alle hoofdknooppunten, werkknooppunten en Edge-knooppunten. U kunt Secure Shell gebruiken voor toegang tot alle knooppunten in een extern cluster.

De R Studio Server Community-versie die wordt gebruikt in het ML Services-cluster op HDInsight accepteert alleen de Gebruikersnaam en het wachtwoord van Linux als aanmeldingsmechanisme. Doorgegeven tokens worden niet ondersteund. Dus als u voor het eerst toegang probeert te krijgen tot R Studio in een ML Services-cluster, moet u zich twee keer aanmelden.

- Meld u eerst aan met de HTTP-gebruikersreferenties via de HDInsight Gateway.

- Gebruik vervolgens de SSH-gebruikersreferenties om u aan te melden bij RStudio.
  
Op dit moment kan slechts één SSH gebruikersaccount worden gemaakt tijdens het inrichten van een HDInsight-cluster. Dus om meerdere gebruikers toegang te geven tot het ML Services-cluster op HDInsight, moet u extra gebruikers in het Linux-systeem maken.

Omdat RStudio op het randknooppunt van het cluster draait, zijn er verschillende stappen hier:

1. De bestaande SSH-gebruiker gebruiken om zich aan te melden bij het randknooppunt
2. Meer Linux-gebruikers toevoegen in Edge-knooppunt
3. RStudio Community-versie gebruiken met de gemaakte gebruiker

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Stap 1: De gemaakte SSH-gebruiker gebruiken om zich aan te melden bij het randknooppunt

Volg de instructies bij [Connect to HDInsight (Apache Hadoop) met SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) om toegang te krijgen tot het randknooppunt. Het randknooppuntadres voor het ML Services-cluster op HDInsight is `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Stap 2: meer Linux-gebruikers toevoegen in Edge-knooppunt

Om een gebruiker toe te voegen aan het Edge-knooppunt, voert u de volgende opdrachten uit:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

De volgende schermafbeelding toont de uitgangen.

![schermafbeelding uitvoer gelijktijdige gebruikers](./media/r-server-hdinsight-manage/hdi-concurrent-users2.png)

Wanneer u wordt gevraagd om 'Huidig Kerberos-wachtwoord:', drukt u op **Enter** om het te negeren. De `-m`-optie in de opdracht `useradd` geeft aan dat het systeem een basismap voor de gebruiker maakt die vereist is voor RStudio Community-versie.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Stap 3: RStudio Community-versie gebruiken met de gemaakte gebruiker

Toegang tot `https://CLUSTERNAME.azurehdinsight.net/rstudio/`RStudio vanaf . Als u zich voor het eerst aanmeldt nadat u het cluster hebt gemaakt, voert u de clusterbeheerdersreferenties in, gevolgd door de SSH-gebruikersreferenties die u hebt gemaakt. Als dit niet uw eerste aanmelding is, voert u alleen de referenties in voor de SSH-gebruiker die u hebt gemaakt.

U zich ook aanmelden met de oorspronkelijke referenties (standaard is het *sshuser)* gelijktijdig vanuit een ander browservenster.

U ziet ook dat de zojuist toegevoegde gebruikers geen hoofdmapbevoegdheden in het Linux-systeem hebben, maar wel dezelfde toegang tot alle bestanden in de externe HDFS- en WASB-opslag hebben.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Op afstand verbinding maken met Microsoft ML Services

U toegang tot de HDInsight Spark-compute-context instellen vanuit een externe instantie van ML-client die op uw bureaublad wordt uitgevoerd. Hiervoor moet u de opties opgeven (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches en sshProfileScript) bij het definiëren van de RxSpark-compute-context op uw bureaublad: Bijvoorbeeld:

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- '<sshuser>'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

Zie de sectie 'Microsoft Machine Learning Server gebruiken als Apache Hadoop-client' voor meer informatie in [Het gebruik van RevoScaleR in een Apache Spark-rekencontext](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

## <a name="use-a-compute-context"></a>Een compute-context gebruiken

Met een compute-context kunt u bepalen of een berekening lokaal op het Edge-knooppunt wordt uitgevoerd of wordt gedistribueerd naar de verschillende knooppunten in het HDInsight-cluster.  Zie [Een R-script uitvoeren op een ML Services-cluster in Azure HDInsight met RStudio Server](machine-learning-services-quickstart-job-rstudio.md)voor een voorbeeld van het instellen van een rekencontext met RStudio Server.

## <a name="distribute-r-code-to-multiple-nodes"></a>R-code distribueren naar meerdere knooppunten

Met ML Services op HDInsight u bestaande R-code gebruiken op `rxExec`meerdere knooppunten in het cluster. Deze functie is handig bij het uitvoeren van een parameteropschoning of van simulaties. Hier volgt een voorbeeldcode van het gebruik van `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Als u nog steeds de context Spark gebruikt, retourneert deze opdracht `(Sys.info()["nodename"])` de naamwaarde voor de werknemersknooppunten waarop de code wordt uitgevoerd. Op een cluster met vier node's verwacht u bijvoorbeeld uitvoer te ontvangen die vergelijkbaar is met het volgende fragment:

    $rxElem1
        nodename
    "wn3-mymlser"

    $rxElem2
        nodename
    "wn0-mymlser"

    $rxElem3
        nodename
    "wn3-mymlser"

    $rxElem4
        nodename
    "wn3-mymlser"

## <a name="access-data-in-apache-hive-and-parquet"></a>Toegang tot gegevens in Apache Hive en Parket

HDInsight ML Services biedt directe toegang tot gegevens in Hive en Parket voor gebruik door ScaleR-functies in de Spark compute-context. Deze mogelijkheden zijn beschikbaar via nieuwe functies voor ScaleR-gegevensbronnen (genaamd RxHiveData en RxParquetData) die gebruikmaken van Spark SQL om gegevens rechtstreeks in Spark DataFrame te laden voor analyse met ScaleR.

Hieronder ziet u code met voorbeeldcode bij het gebruik van de nieuwe functies:

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


Zie voor meer informatie over het gebruik van deze nieuwe `?RxHivedata` functies `?RxParquetData` de online help in ML Services door gebruik te maken van de en opdrachten.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Extra R-pakketten op het cluster installeren

### <a name="to-install-r-packages-on-the-edge-node"></a>R-pakketten installeren op het randknooppunt

Als u extra R-pakketten op het randknooppunt `install.packages()` wilt installeren, u rechtstreeks vanuit de R-console gebruiken, zodra u via SSH met het randknooppunt is verbonden. 

### <a name="to-install-r-packages-on-the-worker-node"></a>R-pakketten installeren op het werknemersknooppunt

Als u R-pakketten wilt installeren op de werknemersknooppunten van het cluster, moet u een Scriptactie gebruiken. Scriptacties zijn Bash-scripts die worden gebruikt om configuratiewijzigingen aan te brengen in het HDInsight-cluster of om extra software te installeren, zoals extra R-pakketten. 

> [!IMPORTANT]  
> U kunt scriptacties alleen gebruiken om extra R-pakketten te installeren nadat het cluster is gemaakt. Gebruik deze procedure niet tijdens het maken van het cluster, omdat het script afhankelijk is van ml-services die volledig zijn geconfigureerd.

1. Volg de stappen bij [Clusters aanpassen met Scriptactie](../hdinsight-hadoop-customize-cluster-linux.md).

3. Geef **voor Scriptactie verzenden**de volgende informatie op:

   * Selecteer **Aangepast** **voor scripttype**.

   * Geef voor **Naam**een naam op voor de scriptactie.

     * Voor Bash script `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh` **URI,** voer . Dit is het script dat extra R-pakketten op het werkknooppunt installeert

   * Schakel het selectievakje alleen voor **Werknemer in**.

   * **Parameters**: de R-pakketten die moeten worden geïnstalleerd. Bijvoorbeeld: `bitops stringr arules`

   * Schakel het selectievakje in om **deze scriptactie voort**te houden .  

   > [!NOTE]
   > 1. Standaard worden alle R-pakketten geïnstalleerd vanaf een momentopname van de Microsoft MRAN-repository die overeenkomt met de versie van ML Server die is geïnstalleerd. Als u nieuwere versies van pakketten wilt installeren, is er kans op incompatibiliteit. Dit soort installatie is echter mogelijk door `useCRAN` op te geven als het eerste element van de pakketlijst, bijvoorbeeld `useCRAN bitops, stringr, arules`.  
   > 2. Voor sommige R-pakketten zijn aanvullende Linux-systeembibliotheken vereist. Voor het gemak wordt de HDInsight ML Services vooraf geïnstalleerd met de afhankelijkheden die nodig zijn voor de top 100 populairste R-pakketten. Als voor de R-pakketten die u wilt installeren, meer bibliotheken zijn vereist, downloadt u het standaardscript dat hier wordt gebruikt, en voegt u stappen toe om de systeembibliotheken te installeren. Vervolgens moet u het gewijzigde script uploaden naar een openbare blobcontainer in Azure-opslag en het gewijzigde script gebruiken om de pakketten te installeren.
   >    Zie [Ontwikkeling van scriptacties](../hdinsight-hadoop-script-actions-linux.md) voor meer informatie over het ontwikkelen van scriptacties.

   ![Scriptactie azure portal verzenden](./media/r-server-hdinsight-manage/submit-script-action.png)

4. Selecteer **Maken** om het script uit te voeren. Nadat het script is voltooid, zijn de R-pakketten beschikbaar op alle werkknooppunten.

## <a name="next-steps"></a>Volgende stappen

* [ML Services-cluster operationeel maken in HDInsight](r-server-operationalize.md)
* [Contextopties berekenen voor HET ML-servicecluster op HDInsight](r-server-compute-contexts.md)
* [Opties voor Azure Storage voor ML Services-cluster in HDInsight](r-server-storage.md)
