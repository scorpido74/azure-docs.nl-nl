---
title: ML-services op HDInsight operationaliseren - Azure
description: Meer informatie over het operationaliseren van uw gegevensmodel om voorspellingen te doen met ML Services in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: a05bcdef2b7456fbab852e9728c156e57f847f57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71123571"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>ML Services-cluster operationaliseren op Azure HDInsight

Nadat u het ML Services-cluster in HDInsight hebt gebruikt om uw gegevensmodellering te voltooien, u het model operationeel maken om voorspellingen te doen. In dit artikel vindt u instructies over het uitvoeren van deze taak.

## <a name="prerequisites"></a>Vereisten

* Een ML Services-cluster op HDInsight. Zie [Apache Hadoop-clusters maken met de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en SELECTEER **ML-services** voor **clustertype**.

* Een SSH-client (Secure Shell): er wordt een SSH-client gebruikt om extern verbinding te maken met het HDInsight-cluster en om opdrachten rechtstreeks uit te voeren op het cluster. Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie.

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>ML Services-cluster operationaliseren met configuratie met één doos

> [!NOTE]  
> De onderstaande stappen zijn van toepassing op R Server 9.0 en ML Server 9.1. Raadpleeg voor ML Server 9.3 het [beheerhulpprogramma gebruiken om de configuratie van de operationalisatie te beheren.](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch)

1. SSH op het Edge-knooppunt.

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)voor instructies over het gebruik van SSH met Azure HDInsight.

1. Directory wijzigen voor de desbetreffende versie en sudo de dot net dll: 

    - Voor Microsoft ML Server 9.1:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Voor Microsoft R Server 9.0:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. U krijgt de opties om uit te kiezen. Kies de eerste optie, zoals in de volgende schermafbeelding wordt weergegeven, om **ML Server voor operationalisatie**te configureren.

    ![R-serverbeheerhulpprogramma selecteren](./media/r-server-operationalize/admin-util-one-box-1.png)

1. U krijgt nu de optie om te kiezen hoe u ML Server wilt operationaliseren. Kies uit de gepresenteerde opties de eerste door **A**in te voeren.

    ![R-server Beheer hulpprogramma operationaliseren](./media/r-server-operationalize/admin-util-one-box-2.png)

1. Voer het wachtwoord in en voer het wachtwoord voor een lokale beheerdersgebruiker in en voer deze opnieuw in.

1. U ziet uitvoergegevens die suggereren dat de bewerking is geslaagd. U wordt ook gevraagd om een andere optie in het menu te selecteren. Selecteer E om terug te gaan naar het hoofdmenu.

    ![R server Administratie utility succes](./media/r-server-operationalize/admin-util-one-box-3.png)

1. Optioneel u diagnostische controles uitvoeren door als volgt een diagnostische test uit te voeren:

    a. Selecteer **6** om diagnostische tests uit te voeren in het hoofdmenu.

    ![R-server Beheer hulpprogramma diagnostisch](./media/r-server-operationalize/hdinsight-diagnostic1.png)

    b. Selecteer **A**in het menu Diagnostische tests . Voer desgevraagd het wachtwoord in dat u hebt opgegeven voor de lokale beheerder.

    ![Hulpprogrammatest Voor R-serverbeheer](./media/r-server-operationalize/hdinsight-diagnostic2.png)

    c. Controleer of de uitvoer aangeeft dat de algehele status een pas is.

    ![R-server Beheer hulpprogrammapas](./media/r-server-operationalize/hdinsight-diagnostic3.png)

    d. Voer in de gepresenteerde menuopties **E** in om terug te keren naar het hoofdmenu en vervolgens **8** in te voeren om het beheerhulpprogramma af te sluiten.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Lange vertragingen bij het consumeren van webservice op Apache Spark

Als u lange vertragingen ondervindt wanneer u een webservice probeert te gebruiken die is gemaakt met mrsdeploy-functies in een Apache Spark-compute-context, moet u mogelijk enkele ontbrekende mappen toevoegen. De Spark-toepassing is van een gebruiker met de naam *rserve2* wanneer de toepassing wordt aangeroepen vanuit een webservice met behulp van mrsdeploy-functies. Dit probleem omzeilen:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


In dit stadium is de configuratie voor uitoefening voltooid. Nu u `mrsdeploy` het pakket op uw RClient gebruiken om verbinding te maken met de operationalisatie op randknooppunt en de functies zoals [externe uitvoering](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) en [webservices](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)te gebruiken. Afhankelijk van of het cluster is ingesteld in een virtueel netwerk of niet, moet u mogelijk forward tunneling via SSH-aanmelding instellen voor de poort. In de volgende secties wordt uitgelegd hoe u deze tunnel instelt.

### <a name="ml-services-cluster-on-virtual-network"></a>ML Services-cluster op virtueel netwerk

Zorg ervoor dat u verkeer via poort 12800 naar het Edge-knooppunt toestaat. Op deze manier kunt u het Edge-knooppunt gebruiken om verbinding te maken met de functie Uitoefening.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Als de `remoteLogin()` geen verbinding kan maken met het Edge-knooppunt maar als u wel verbinding hebt via SSH, moet u controleren of de regel op basis waarvan verkeer via poort 12800 is toegestaan, juist is ingesteld of niet. Als dit probleem zich blijft voordoen, kunt u een tijdelijke oplossing gebruiken door forward tunneling via SSH in te stellen voor de poort. Zie voor instructies de volgende sectie:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>ML Services-cluster niet ingesteld op virtueel netwerk

Als het cluster niet is ingesteld in het virtuele netwerk vnet of als u problemen ondervindt met de connectiviteit via dit netwerk, kunt u forward tunneling via SSH instellen voor de poort:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Zodra uw SSH-sessie actief is, wordt het verkeer van de poort 12800 van uw lokale machine doorgestuurd naar de poort van het randknooppunt 12800 via de SSH-sessie. Zorg ervoor dat u `127.0.0.1:12800` gebruikt in de `remoteLogin()`-methode. Dit logt in op de operationalisatie van het randknooppunt via port forwarding.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Geoperationaliseerde compute nodes schalen op HDInsight-workernodes

Als u de compute-knooppunten wilt schalen, ontmantelt u eerst de werkknooppunten en configureert u vervolgens rekenknooppunten op de buiten bedrijf genomen werkknooppunten.

### <a name="step-1-decommission-the-worker-nodes"></a>Stap 1: De werknemersknooppunten buiten werking stellen

ML Services cluster wordt niet beheerd via [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Als de werknemersknooppunten niet worden ontmanteld, werkt de YARN Resource Manager niet zoals verwacht omdat het niet op de hoogte is van de resources die door de server worden opgenomen. Om deze sitatie te voorkomen raden we u aan de werkknooppunten uit bedrijf te nemen voordat u de rekenknooppunten uitschaalt.

Volg de volgende stappen om werknemersknooppunten te ontmantelen:

1. Meld u aan bij de Ambari-console van het cluster en klik op het tabblad **Hosts.**

1. Selecteer werkknooppunten (die moeten worden ontmanteld).

1. Klik **op Geselecteerde hosts** > **Geselecteerde** > **hosts** > **Schakel de onderhoudsmodus in**. In de volgende afbeelding zijn bijvoorbeeld wk3 en wk4 geselecteerd om uit bedrijf te worden genomen.  

   ![Apache Ambari inschakelen onderhoudsmodus](./media/r-server-operationalize/get-started-operationalization.png)  

* Selecteer **Geselecteerde acties** > **Hosts** > **DataNodes** > klik op **Buitengebruik .**
* Selecteer **Acties** > **Geselecteerde hosts** > **NodeManagers** > klik op **Buitengebruik .**
* Selecteer **Geselecteerde acties** > **Hosts** > **DataNodes** > klik op **Stoppen**.
* Selecteer **Geselecteerde** > **hosts-nodemanagers** **Selected Hosts** > > klik op **Stoppen**.
* Selecteer **Geselecteerde acties** > **Hosts** > **hosts** > klik op Alle onderdelen **stoppen**.
* Hef de selectie van de worker-knooppunten op en selecteer de hoofdknooppunten.
* Selecteer **geselecteerde acties** > **>** hosts "**Hosts** > **alle onderdelen opnieuw opstarten**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Stap 2: Compute-knooppunten configureren op elk buiten bedrijf genomen werkknooppunt(en)

1. SSH op elk uit bedrijf genomen werkknooppunt.

1. Voer beheerhulpprogramma's uit met behulp van de relevante DLL voor het ML Services-cluster dat u hebt. Voer voor ML Server 9.1 het volgende uit:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Voer **1** in om de optie **ML-server configureren voor operationalisatie configureren te**selecteren.

1. Voer **C** in `C. Compute node`om de optie te selecteren . Hiermee configureert u het rekenknooppunt op het werkknooppunt.

1. Sluit het beheerprogramma.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Stap 3: Details van compute nodes toevoegen aan webknooppunt

Zodra alle services buiten bedrijf zijn gesteld om compute node uit te voeren, komt u terug op het randknooppunt en voegt u de IP-adressen van de ontmantelde werknemer toe aan de configuratie van het ML Server-webknooppunt:

1. SSH op het Edge-knooppunt.

1. Voer `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json` uit.

1. Zoek naar de sectie 'Uris' en voeg ip- en poortdetails van het werknemersknooppunt toe.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>Volgende stappen

* [ML Services-cluster beheren in HDInsight](r-server-hdinsight-manage.md)
* [Opties voor compute-context voor ML Services-cluster in HDInsight](r-server-compute-contexts.md)
* [Opties voor Azure Storage voor ML Services-cluster in HDInsight](r-server-storage.md)
