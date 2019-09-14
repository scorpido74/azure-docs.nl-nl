---
title: Tint met Hadoop op HDInsight Linux-gebaseerde clusters-Azure
description: Meer informatie over het installeren van tint op HDInsight-clusters en het gebruik van tunneling om de aanvragen naar tinten te sturen. Gebruik tint om door de opslag te bladeren en Hive of Pig uit te voeren.
keywords: kleur Toon Hadoop
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: hrasheed
ms.openlocfilehash: 67f338b583ef428b8dd04e859a5204fd708ce434
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962008"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Tint op HDInsight Hadoop-clusters installeren en gebruiken

Meer informatie over het installeren van tint op HDInsight-clusters en het gebruik van tunneling om de aanvragen naar tinten te sturen.

## <a name="what-is-hue"></a>Wat is tint?
Tint is een set webtoepassingen die worden gebruikt voor interactie met een Apache Hadoop cluster. U kunt tint gebruiken om te bladeren door de opslag die is gekoppeld aan een Hadoop-cluster (WASB, in het geval van HDInsight-clusters), Hive-taken en Pig-scripts, enzovoort, uit te voeren. De volgende onderdelen zijn beschikbaar voor tint installaties op een HDInsight Hadoop-cluster.

* Beeswax-Hive-editor
* Apache Pig
* Meta Store-beheer
* Apache Oozie
* FileBrowser (die praat met de WASB-standaard container)
* Taak browser

> [!WARNING]  
> Onderdelen die worden meegeleverd met het HDInsight-cluster, worden volledig ondersteund en Microsoft Ondersteuning helpt bij het isoleren en oplossen van problemen met betrekking tot deze onderdelen.
>
> Aangepaste onderdelen ontvangen commercieel redelijke ondersteuning om u te helpen het probleem verder op te lossen. Dit kan leiden tot het oplossen van het probleem of het vragen om beschik bare kanalen te benaderen voor de open source-technologieën waar diep gaande expertise voor die technologie wordt gevonden. Er zijn bijvoorbeeld veel community-sites die kunnen worden gebruikt, zoals: [MSDN-forum voor HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Ook Apache-projecten hebben project sites [https://apache.org](https://apache.org)op, bijvoorbeeld: [Hadoop](https://hadoop.apache.org/).
>
>

## <a name="install-hue-using-script-actions"></a>Tint installeren met script acties

Het script voor de installatie van tint op een HDInsight-cluster op basis van https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh Linux is beschikbaar op. U kunt dit script gebruiken om tinten te installeren in clusters met Azure Storage-blobs (WASB) of Azure Data Lake Storage als standaard opslag.

In deze sectie vindt u instructies voor het gebruik van het script bij het inrichten van het cluster met behulp van de Azure Portal.

> [!NOTE]  
> Azure PowerShell, de klassieke Azure-CLI, de HDInsight .NET SDK of Azure Resource Manager sjablonen kunnen ook worden gebruikt voor het Toep assen van script acties. U kunt ook script acties Toep assen op reeds actieve clusters. Zie [HDInsight-clusters aanpassen met script acties](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie.
>
>

1. Begin met het inrichten van een cluster met behulp van de stappen bij het [inrichten van HDInsight-clusters in Linux](hdinsight-hadoop-provision-linux-clusters.md), maar het inrichten niet volt ooien.

   > [!NOTE]  
   > Voor het installeren van tint op HDInsight-clusters is de aanbevolen hoofd knooppunt-grootte ten minste A4 (8 kernen, 14 GB geheugen).
   >
   >
2. Selecteer op de Blade **optionele configuratie** de optie **script acties**en geef de gegevens op zoals hieronder wordt weer gegeven:

    ![Script actie parameters opgeven voor tint](./media/hdinsight-hadoop-hue-linux/hdi-hue-script-action.png "Script actie parameters opgeven voor tint")

   * **NAAM**: Voer een beschrijvende naam in voor de script actie.
   * **SCRIPT-URI**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **KOP**: Schakel deze optie in.
   * **WERK NEMER**: Laat dit leeg.
   * **ZOOKEEPER**: Laat dit leeg.
   * **PARA METERS**: Laat dit leeg.
3. Gebruik de knop **selecteren** aan de onderkant van de **script acties**om de configuratie op te slaan. Gebruik tot slot de knop **selecteren** onder aan de Blade **optionele configuratie** om de optionele configuratie gegevens op te slaan.
4. Ga door met het inrichten van het cluster zoals beschreven in [HDInsight-clusters inrichten op Linux](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="use-hue-with-hdinsight-clusters"></a>Tint gebruiken met HDInsight-clusters

SSH-Tunneling is de enige manier om toegang te krijgen tot de tint op het cluster zodra deze actief is. Door middel van tunneling via SSH kan het verkeer rechtstreeks naar de hoofd knooppunt van het cluster gaan waarop tint wordt uitgevoerd. Wanneer het inrichten van het cluster is voltooid, gebruikt u de volgende stappen om tint te gebruiken op een HDInsight Linux-cluster.

> [!NOTE]  
> U wordt aangeraden om de volgende instructies te volgen met Firefox-webbrowser.
>
>

1. Gebruik de informatie in [ssh-tunneling gebruiken om toegang te krijgen tot Apache Ambari Web UI, Resource Manager, JobHistory, NameNode, Oozie en andere WEBgebruikersinterfaces](hdinsight-linux-ambari-ssh-tunnel.md) om een SSH-tunnel van uw client systeem te maken met het HDInsight-cluster, en configureer vervolgens uw webbrowser voor het gebruik van de SSH-tunnel als een proxy.

2. Nadat u een SSH-tunnel hebt gemaakt en uw browser hebt geconfigureerd voor proxy verkeer via deze, moet u de hostnaam van het primaire hoofd knooppunt vinden. U kunt dit doen door verbinding te maken met het cluster via SSH op poort 22. Bijvoorbeeld, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` waarbij **username** de gebruikers naam van SSH is en **clustername** de naam van uw cluster is.

    Zie [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie.

3. Als de verbinding tot stand is gebracht, gebruikt u de volgende opdracht om de Fully Qualified Domain Name van de primaire hoofd knooppunt te verkrijgen:

        hostname -f

    Dit retourneert een naam die er ongeveer als volgt uitziet:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Dit is de hostnaam van de primaire hoofd knooppunt waar de website tint zich bevindt.
4. Gebruik de browser om de tint portal te openen op http\/:/hostname: 8888. Vervang HOSTNAME door de naam die u in de vorige stap hebt verkregen.

   > [!NOTE]  
   > Wanneer u zich voor de eerste keer aanmeldt, wordt u gevraagd een account te maken om u aan te melden bij de tint Portal. De referenties die u hier opgeeft, zijn beperkt tot de portal en zijn niet gerelateerd aan de beheerders-of SSH-gebruikers referenties die u hebt opgegeven tijdens het inrichten van het cluster.
   >
   >

    ![Meld u aan bij de portal voor kleur Toon](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Referenties opgeven voor de Toon van de portal")

### <a name="run-a-hive-query"></a>Een Hive-query uitvoeren
1. Klik in de tint Portal op **query-editors**en klik vervolgens op **Hive** om de Hive-editor te openen.

    ![Hive gebruiken](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Hive gebruiken")
2. Op het tabblad **assistent** , onder **Data Base**, ziet u **hivesampletable**. Dit is een voorbeeld tabel die wordt geleverd met alle Hadoop-clusters in HDInsight. Voer een voorbeeld query in het rechterdeel venster in en Bekijk de uitvoer op het tabblad **resultaten** in het onderstaande deel venster, zoals wordt weer gegeven in de scherm opname.

    ![Hive-query uitvoeren](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Hive-query uitvoeren")

    U kunt ook het tabblad **grafiek** gebruiken om een visuele weer gave van het resultaat te bekijken.

### <a name="browse-the-cluster-storage"></a>Bladeren door de cluster opslag
1. Klik in de rechter bovenhoek van de menu balk van de tint Portal op **bestands browser** .
2. De bestands browser wordt standaard geopend in de **/User/myuser** -map. Klik op de slash direct vóór de gebruikers lijst in het pad om naar de hoofdmap van de Azure storage-container te gaan die aan het cluster is gekoppeld.

    ![Bestands browser gebruiken](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Bestands browser gebruiken")
3. Klik met de rechter muisknop op een bestand of map om de beschik bare bewerkingen weer te geven. Gebruik de knop **uploaden** in de rechter bovenhoek om bestanden te uploaden naar de huidige map. Gebruik de knop **Nieuw** om nieuwe bestanden of mappen te maken.

> [!NOTE]  
> De bestands browser tinten kan alleen de inhoud weer geven van de standaard container die is gekoppeld aan het HDInsight-cluster. Alle extra opslag accounts/containers die u aan het cluster hebt gekoppeld, zijn niet toegankelijk via de bestands browser. De extra containers die zijn gekoppeld aan het cluster, zijn echter altijd toegankelijk voor de Hive-taken. Als u bijvoorbeeld de opdracht `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` in de Hive-editor invoert, ziet u ook de inhoud van extra containers. In deze opdracht is **newcontainer** niet de standaard container die aan een cluster is gekoppeld.
>
>

## <a name="important-considerations"></a>Belang rijke overwegingen
1. Het script dat wordt gebruikt om de kleur Toon te installeren, installeert dit alleen op de primaire hoofd knooppunt van het cluster.

2. Tijdens de installatie worden meerdere Hadoop-Services (HDFS, GARENs, MR2, Oozie) opnieuw gestart voor het bijwerken van de configuratie. Nadat het script is voltooid, kan het enige tijd duren voordat andere Hadoop-services worden opgestart. Dit kan in eerste instantie van invloed zijn op de prestaties van de kleur. Zodra alle services zijn opgestart, is de kleur Toon volledig functioneel.
3. Kleur Toon biedt geen inzicht in Apache TEZ-taken. Dit is de huidige standaard waarde voor Hive. Als u MapReduce wilt gebruiken als de engine voor het uitvoeren van de Hive, werkt u het script bij voor het gebruik van de volgende opdracht in uw script:

        set hive.execution.engine=mr;

4. Met Linux-clusters kunt u een scenario hebben waarin uw services worden uitgevoerd op de primaire hoofd knooppunt terwijl de Resource Manager kan worden uitgevoerd op de secundaire server. Dit scenario kan leiden tot fouten (zie hieronder) als u de details van actieve taken op het cluster wilt weer geven met behulp van tint. U kunt de taak Details echter bekijken wanneer de taak is voltooid.

   ![Fout met tint Portal](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Fout met tint Portal")

   Dit is te wijten aan een bekend probleem. Als tijdelijke oplossing wijzigt u Ambari zodat de actieve Resource Manager ook wordt uitgevoerd op de primaire hoofd knooppunt.
5. Kleur Toon begrijpt WebHDFS terwijl HDInsight-clusters Azure Storage `wasb://`gebruiken. Het aangepaste script dat wordt gebruikt met script actie installeert dus WebWasb, een WebHDFS-compatibele service voor het praten met WASB. Dit betekent dat, zelfs als de kleur Toon, HDFS op locatie (bijvoorbeeld wanneer u de muis over de **bestands browser**beweegt) wordt geïnterpreteerd als WASB.

## <a name="next-steps"></a>Volgende stappen
* [Installeer Apache Giraph op HDInsight-clusters](hdinsight-hadoop-giraph-install-linux.md). Gebruik cluster aanpassing om Giraph te installeren op HDInsight Hadoop-clusters. Met Giraph kunt u grafiek verwerking uitvoeren met behulp van Hadoop en kan deze worden gebruikt met Azure HDInsight.
* [Installeer R op HDInsight-clusters](hdinsight-hadoop-r-scripts-linux.md). Gebruik cluster aanpassing om R-op HDInsight Hadoop-clusters te installeren. R is een open source-taal en-omgeving voor statistische computing. Het biedt honderden ingebouwde statistische functies en een eigen programmeer taal waarmee aspecten van functionele en objectgeoriënteerd Program meren worden gecombineerd. Het biedt ook uitgebreide grafische mogelijkheden.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
