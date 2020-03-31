---
title: Hue met Hadoop op HDInsight Linux-gebaseerde clusters - Azure
description: Meer informatie over het installeren van Hue op HDInsight-clusters en het gebruik van tunneling om de aanvragen naar Hue te routeren. Gebruik Hue om door opslag te bladeren en Hive of Pig uit te voeren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 69acfd4f2edab9be1b1dcfbb52eafbd00aec712f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934567"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Hue installeren en gebruiken op HDInsight Hadoop-clusters

Meer informatie over het installeren van Hue op HDInsight-clusters en het gebruik van tunneling om de aanvragen naar Hue te routeren.

## <a name="what-is-hue"></a>Wat is Hue?

Hue is een set webtoepassingen die worden gebruikt om te communiceren met een Apache Hadoop-cluster. U Hue gebruiken om door de opslag te bladeren die is gekoppeld aan een Hadoop-cluster (WASB, in het geval van HDInsight-clusters), Hive-taken en Varkensscripts uit te voeren, enzovoort. De volgende componenten zijn beschikbaar met Hue-installaties op een HDInsight Hadoop-cluster.

* Beeswax Hive Editor
* Apache Varken
* Metastore manager
* Apache Oozie
* FileBrowser (die praat met WASB standaard container)
* Jobbrowser

> [!WARNING]  
> Onderdelen die zijn geleverd met het HDInsight-cluster worden volledig ondersteund en Microsoft Support helpt bij het isoleren en oplossen van problemen met betrekking tot deze componenten.
>
> Aangepaste componenten ontvangen commercieel redelijke ondersteuning om u te helpen het probleem verder op te lossen. Dit kan resulteren in het oplossen van het probleem of u vragen om beschikbare kanalen in te schakelen voor de open source-technologieën waar diepgaande expertise voor die technologie wordt gevonden. Er zijn bijvoorbeeld veel communitysites die kunnen worden gebruikt, zoals: [https://stackoverflow.com](https://stackoverflow.com) [MSDN-forum voor HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), . Ook Apache projecten hebben [https://apache.org](https://apache.org)projectsites op , bijvoorbeeld: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Hue installeren met Scriptacties

Gebruik de informatie in de onderstaande tabel voor uw scriptactie. Zie [HDInsight-clusters aanpassen met Scriptacties](hdinsight-hadoop-customize-cluster-linux.md) voor specifieke instructies over het gebruik van Scriptacties.

> [!NOTE]  
> Om Hue op HDInsight-clusters te installeren, is de aanbevolen headnodegrootte ten minste A4 (8 cores, 14 GB geheugen).

|Eigenschap |Waarde |
|---|---|
|Scripttype:|- Aangepast|
|Name|Hue installeren|
|Bash script URI|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|Knooppunttype(s):|Head|

## <a name="use-hue-with-hdinsight-clusters"></a>Hue gebruiken met HDInsight-clusters

SSH Tunneling is de enige manier om toegang te krijgen tot Hue op het cluster zodra deze wordt uitgevoerd. Tunneling via SSH maakt het verkeer direct naar de headnode van het cluster waar Hue draait. Nadat het cluster is ingericht, gebruikt u de volgende stappen om Hue te gebruiken op een HDInsight-cluster.

> [!NOTE]  
> We raden u aan de webbrowser Firefox te gebruiken om de onderstaande instructies te volgen.

1. Gebruik de informatie in [SSH-tunneling gebruiken om toegang te krijgen tot apache Ambari web ui, ResourceManager, JobHistory, NameNode, Oozie en andere web-uI's](hdinsight-linux-ambari-ssh-tunnel.md) om een SSH-tunnel te maken van uw clientsysteem naar het HDInsight-cluster en configureer vervolgens uw webbrowser om de SSH-tunnel als proxy te gebruiken.

1. Gebruik [de ssh-opdracht](./hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Gebruik na verbinding de volgende opdracht om de volledig gekwalificeerde domeinnaam van de primaire headnode te verkrijgen:

    ```bash
    hostname -f
    ```

    Hiermee wordt een naam als volgt teruggegeven:

        myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Dit is de hostnaam van de primaire headnode waar de Hue-website zich bevindt.

1. Gebruik de browser om de `http://HOSTNAME:8888`Hue-portal te openen op . Vervang HOSTNAME door de naam die u in de vorige stap hebt verkregen.

   > [!NOTE]  
   > Wanneer je je voor de eerste keer aanmeldt, wordt je gevraagd een account aan te maken om je aan te melden bij de Hue-portal. De referenties die u hier opgeeft, zijn beperkt tot de portal en zijn niet gerelateerd aan de beheerders- of SSH-gebruikersreferenties die u hebt opgegeven tijdens het inrichten van het cluster.

    ![HDInsight hue portal login window HDInsight hue portal login window HDInsight hue portal login window HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Referenties opgeven voor Hue-portal")

### <a name="run-a-hive-query"></a>Een Hive-query uitvoeren

1. Selecteer **queryeditors**in de Hue-portal en selecteer **Vervolgens Hive** om de Hive-editor te openen.

    ![HDInsight hue portal gebruik hive editor](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Hive gebruiken")

2. Op het tabblad **Assist,** onder **Database**, ziet u **hivesampletable**. Dit is een voorbeeldtabel die wordt verzonden met alle Hadoop-clusters op HDInsight. Voer een voorbeeldquery in het rechterdeelvenster in en zie de uitvoer op het tabblad **Resultaten** in het deelvenster hieronder, zoals weergegeven in de schermopname.

    ![HDInsight hue portal hive query](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Hive-query uitvoeren")

    U ook het tabblad **Grafiek** gebruiken om een visuele weergave van het resultaat te zien.

### <a name="browse-the-cluster-storage"></a>Blader door de clusteropslag

1. Selecteer in de Hue-portal De optie **Bestandsbrowser** in de rechterbovenhoek van de menubalk.
2. Standaard wordt de bestandsbrowser geopend in de **map /user/myuser.** Selecteer de slash doorsturen vlak voordat de gebruikersmap in het pad naar de hoofdmap gaat van de Azure-opslagcontainer die aan het cluster is gekoppeld.

    ![HDInsight hue portal-bestandsbrowser](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Bestandsbrowser gebruiken")

3. Klik met de rechtermuisknop op een bestand of map om de beschikbare bewerkingen te bekijken. Gebruik de knop **Uploaden** in de rechterhoek om bestanden naar de huidige map te uploaden. Gebruik de knop **Nieuw** om nieuwe bestanden of mappen te maken.

> [!NOTE]  
> De Hue-bestandsbrowser kan alleen de inhoud weergeven van de standaardcontainer die is gekoppeld aan het HDInsight-cluster. Eventuele extra opslagaccounts/containers die u aan het cluster hebt gekoppeld, zijn niet toegankelijk via de bestandsbrowser. De extra containers die aan het cluster zijn gekoppeld, zijn echter altijd toegankelijk voor de Hive-taken. Als u bijvoorbeeld de `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` opdracht invoert in de Hive-editor, u ook de inhoud van extra containers zien. In deze opdracht is **newcontainer** niet de standaardcontainer die is gekoppeld aan een cluster.

## <a name="important-considerations"></a>Belangrijke overwegingen

1. Het script dat wordt gebruikt om Hue te installeren, installeert het alleen op de primaire headnode van het cluster.

1. Tijdens de installatie worden meerdere Hadoop-services (HDFS, YARN, MR2, Oozie) opnieuw opgestart voor het bijwerken van de configuratie. Nadat het script is voltooid met het installeren van Hue, kan het enige tijd duren voordat andere Hadoop-services zijn gestart. Dit kan in eerste instantie van invloed zijn op de prestaties van Hue. Zodra alle services zijn gestart, is Hue volledig functioneel.

1. Hue begrijpt apache Tez-taken niet, wat de huidige standaardwaarde is voor Hive. Als u MapReduce wilt gebruiken als uitvoeringsengine voor Hive, werkt u het script bij om de volgende opdracht in uw script te gebruiken:

        set hive.execution.engine=mr;

1. Met Linux-clusters u een scenario hebben waarin uw services worden uitgevoerd op de primaire headnode terwijl de Resource Manager mogelijk op de secundaire kop wordt uitgevoerd. Een dergelijk scenario kan leiden tot fouten (zie hieronder) wanneer u Hue gebruikt om details van LOPENDE taken in het cluster weer te geven. U echter de taakgegevens bekijken wanneer de taak is voltooid.

   ![Voorbeeldbericht hue-portalfout](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Hue-portalfout")

   Dit is te wijten aan een bekend probleem. Wijzig als tijdelijke oplossing Ambari zodat de actieve Resource Manager ook op de primaire headnode wordt uitgevoerd.

1. Hue begrijpt WebHDFS, terwijl HDInsight-clusters Azure Storage gebruiken met behulp van `wasbs://`. Dus, de aangepaste script gebruikt met script actie installeert WebWasb, dat is een WebHDFS-compatibele service voor het praten met WASB. Dus, ook al is de Hue-portal zegt HDFS op plaatsen (zoals wanneer u uw muis over de **bestandsbrowser**beweegt), moet het worden geïnterpreteerd als WASB.

## <a name="next-steps"></a>Volgende stappen

[Installeer R op HDInsight-clusters.](hdinsight-hadoop-r-scripts-linux.md) Gebruik clusteraanpassingen om R te installeren op HDInsight Hadoop-clusters. R is een open-source taal en omgeving voor statistische computing. Het biedt honderden ingebouwde statistische functies en een eigen programmeertaal die aspecten van functionele en objectgeoriënteerde programmering combineert. Het biedt ook uitgebreide grafische mogelijkheden.
