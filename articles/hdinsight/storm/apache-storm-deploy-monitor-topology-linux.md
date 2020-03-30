---
title: Apache Storm-topologieën implementeren en beheren op Azure HDInsight
description: Meer informatie over het implementeren, monitoren en beheren van Apache Storm-topologieën met behulp van het Storm Dashboard op Linux-gebaseerde HDInsight. Gebruik Hadoop-tools voor Visual Studio.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: e890289230b3215bd102d8c5a78dca4f1b7b90f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271900"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Apache Storm-topologieën implementeren en beheren op Azure HDInsight

Leer in dit document de basisprincipes van het beheren en monitoren van [Apache Storm-topologieën](https://storm.apache.org/) die worden uitgevoerd op Storm op HDInsight-clusters.

## <a name="prerequisites"></a>Vereisten

* Een Apache Storm cluster op HDInsight. Zie [Apache Hadoop-clusters maken met de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en Selecteer **Storm** voor **clustertype**.

* (Optioneel) Vertrouwdheid met Secure Shell (SSH) en Secure Copy (SCP). Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Optioneel) Visual Studio, Azure SDK 2.5.1 of nieuwer en de Data Lake Tools voor Visual Studio. Zie [Apache Hadoop & Visual Studio Data Lake Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)voor meer informatie.

## <a name="submit-a-topology-using-visual-studio"></a>Een topologie verzenden met Visual Studio

U de Data Lake-hulpprogramma's voor Visual Studio gebruiken om C# of hybride topologieën in te dienen bij uw Storm-cluster. In de volgende stappen wordt een voorbeeldtoepassing gebruikt. Zie [Apache Storm-topologieën met Visual Studio en C#](apache-storm-develop-csharp-visual-studio-topology.md)voor informatie over het maken van topologie met behulp van de Data Lake-hulpprogramma's.

1. Zie [Data Lake Tools voor Visual Studio gebruiken](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)als u de nieuwste versie van de Data Lake-tools voor Visual Studio nog niet hebt geïnstalleerd.

    > [!NOTE]  
    > De Azure Data Lake en Stream Analytics Tools werden voorheen de HDInsight Tools voor Visual Studio genoemd.
    >
    > Azure Data Lake en Stream Analytics Tools voor Visual Studio zijn opgenomen in de **Azure-ontwikkelworkload** voor Visual Studio 2019.

1. Start Visual Studio.

1. Selecteer **in** het venster Start de optie **Een nieuw project maken**.

1. Selecteer in **het venster Een nieuw project** maken `Storm`het zoekvak en voer . Kies vervolgens **Stormvoorbeeld** in de resultatenlijst en selecteer **Volgende**.

1. Voer in het **venster Uw nieuwe project configureren** een **projectnaam**in en ga naar of maak een **locatie** om het nieuwe project op te slaan. Selecteer vervolgens **Maken**.

    ![Uw nieuwe projectvenster configureren, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

1. Klik vanuit **Server Explorer**met de rechtermuisknop op **Azure** en selecteer Verbinding maken met **Microsoft Azure-abonnement...** en voltooi het aanmeldingsproces.

1. Klik vanuit **Solution Explorer**met de rechtermuisknop op het project en kies Verzenden naar Storm **op HDInsight**.

    > [!NOTE]  
    > Voer de inloggegevens voor uw Azure-abonnement in als u daarom wordt gevraagd. Als u meer dan één abonnement hebt, meldt u zich aan bij het abonnement dat uw Cluster Storm op HDInsight bevat.

1. Kies in het dialoogvenster **Topologie verzenden** onder de vervolgkeuzelijst **Stormcluster** de optie Storm op het cluster HDInsight en selecteer **Vervolgens Verzenden**. U controleren of de indiening is gelukt door het deelvenster **Uitvoer te** bekijken.

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Een topologie verzenden met ssh en de opdracht Storm

1. Gebruik [de ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Gebruik vanuit uw ssh-sessie de volgende opdracht om de **WordCount-voorbeeldtopologie** te starten:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    Met deze opdracht wordt de WordCount-voorbeeldtopologie gestart op het cluster. Deze topologie genereert willekeurig zinnen en telt vervolgens het optreden van elk woord in de zinnen.

    > [!NOTE]  
    > Wanneer u topologie inhet cluster indient, moet u eerst het `storm` .jar-bestand met het cluster kopiëren voordat u de opdracht gebruikt. Als u het bestand naar het `scp` cluster wilt kopiëren, u de opdracht gebruiken. Geef bijvoorbeeld `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar` op.
    >
    > Het voorbeeld *WordCount* en andere voorbeelden van stormstarteren `/usr/hdp/current/storm-client/contrib/storm-starter/`zijn al opgenomen in uw cluster op .

## <a name="submit-a-topology-programmatically"></a>Een topologie programmatisch verzenden

U een topologie programmatisch implementeren met behulp van de Nimbus-service. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)biedt een voorbeeld Java-toepassing die laat zien hoe u een topologie implementeert en start via de Nimbus-service.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Een topologie bewaken en beheren in Visual Studio

Wanneer u een topologie indient met Visual Studio, wordt het venster **Weergave Stormtopologieën** weergegeven. Selecteer de topologie in de lijst om informatie over de hardlooptopologie weer te geven.

![Monitor topologie, Storm Topologieën Weergavevenster, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> U **stormtopologieën** ook bekijken vanuit **Server Explorer.**  > Azure **Azure****HDInsight uitvouwen,** met de rechtermuisknop op een cluster Storm op HDInsight klikken en vervolgens **Stormtopologieën weergeven.**

Selecteer de vorm voor de uitstespuiten of bouten om informatie over deze onderdelen weer te geven. Er wordt een knopinfo met componentgegevens weergegeven voor het geselecteerde item.

### <a name="deactivate-and-reactivate-a-topology"></a>Een topologie deactiveren en opnieuw activeren

Als u een topologie deactiveert, wordt deze onderbroken totdat de topologie wordt gedood of opnieuw wordt geactiveerd. Als u deze bewerkingen wilt uitvoeren, gebruikt u de knoppen **Deactiveren** en **Opnieuw activeren** in het gebied **Acties** boven aan het venster **Weergave Stormtopologieën.**

### <a name="rebalance-a-topology"></a>Een topologie opnieuw in evenwicht brengen

Door een topologie opnieuw in evenwicht te brengen, kan het systeem het parallellisme van de topologie herzien. Als u bijvoorbeeld het formaat van het cluster hebt gewijzigd om meer notities toe te voegen, kan een topologie opnieuw worden gebalanceerd om de nieuwe knooppunten te zien.

Als u een topologie opnieuw in evenwicht wilt brengen, gebruikt u de knop **Opnieuw inevenwicht** in het gebied **Acties** van het venster **Weergave Stormtopologieën.**

> [!WARNING]  
> Als u een topologie opnieuw in evenwicht brengt, wordt de topologie gedeactiveerd, worden werknemers gelijkmatig over het cluster verdeeld en wordt de topologie geretourneerd naar de status waarin deze zich bevond voordat opnieuw wordt gebalanceerd. Als de topologie actief was, wordt deze weer actief. Als de topologie is gedeactiveerd, blijft deze gedeactiveerd.

### <a name="kill-a-running-topology"></a>Dood een lopende topologie

Stormtopologieën blijven actief totdat ze zijn gestopt of het cluster is verwijderd. Als u een topologie wilt stoppen, gebruikt u de knop **Kill** in het gebied **Acties.**

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Een topologie bewaken en beheren met de opdracht SSH en de storm

Met `storm` het hulpprogramma u vanaf de opdrachtregel met het uitvoeren van topologieën werken. Gebruik `storm -h` voor een volledige lijst met opdrachten.

### <a name="list-topologies"></a>Lijsttopologieën

Gebruik de volgende opdracht om alle lopende topologieën weer te geven:

```shell
storm list
```

Met deze opdracht wordt informatie geretourneerd die lijkt op de volgende tekst:

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Een topologie deactiveren en opnieuw activeren

Als u een topologie deactiveert, wordt deze onderbroken totdat de topologie wordt gedood of opnieuw wordt geactiveerd. Gebruik de volgende opdrachten om deactiveren of opnieuw activeren:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Dood een lopende topologie

Storm topologieën, eenmaal begonnen, blijven lopen tot gestopt. Als u een topologie wilt stoppen, gebruikt u de volgende opdracht:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Een topologie opnieuw in evenwicht brengen

Door een topologie opnieuw in evenwicht te brengen, kan het systeem het parallellisme van de topologie herzien. Als u bijvoorbeeld het formaat van het cluster hebt gewijzigd om meer notities toe te voegen, kan een topologie opnieuw worden gebalanceerd om de nieuwe knooppunten te zien.

> [!WARNING]  
> Als u een topologie opnieuw in evenwicht brengt, wordt de topologie gedeactiveerd, worden werknemers gelijkmatig over het cluster verdeeld en wordt de topologie geretourneerd naar de status waarin deze zich bevond voordat opnieuw wordt gebalanceerd. Als de topologie actief was, wordt deze weer actief. Als het gedeactiveerd is, blijft het gedeactiveerd.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Een topologie bewaken en beheren met behulp van de gebruikersinterface van Storm

De Storm UI biedt een webinterface voor het werken met het uitvoeren van topologieën, en het is opgenomen op uw HDInsight-cluster. Als u de gebruikersinterface van Storm `https://CLUSTERNAME.azurehdinsight.net/stormui`wilt weergeven, gebruikt u een webbrowser om te openen , waarbij *CLUSTERNAME* de naam van uw cluster is.

> [!NOTE]  
> Als u wordt gevraagd een gebruikersnaam en wachtwoord op te geven, voert u de gebruikersnaam en het wachtwoord van de clusterbeheerder in die u hebt gebruikt bij het maken van het cluster.

### <a name="storm-ui-main-page"></a>Hoofdpagina van stormgebruikersinterface

De hoofdpagina van de Storm UI geeft de volgende informatie:

| Sectie | Beschrijving |
| --- | --- |
| Clusteroverzicht| Basisinformatie over het cluster Storm. |
| Nimbus samenvatting | Een lijst van basisNimbus informatie. |
| Samenvatting topologie | Een lijst van lopende topologieën. Als u meer informatie over een specifieke topologie wilt weergeven, selecteert u de koppeling in de kolom **Naam.** |
| Samenvatting van de supervisor | Informatie over de Storm supervisor. Als u de werknemersresources wilt zien die zijn gekoppeld aan een specifieke supervisor, selecteert u de koppeling in de kolom **Host** of **Id.** |
| Nimbus-configuratie | Nimbus-configuratie voor het cluster. |

De hoofdpagina van de Storm UI lijkt op deze webpagina:

![Hoofdpagina, Storm UI, Apache Storm topologieën, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>Samenvatting topologie

Als u een koppeling selecteert in de sectie **Topologie-overzicht,** wordt de volgende informatie over de topologie weergegeven:

| Sectie | Beschrijving |
| --- | --- |
| Samenvatting topologie | Basisinformatie over de topologie. |
| Topologieacties| Beheeracties die u doen voor de topologie. De beschikbare acties worden later in deze sectie beschreven. |
| Topologiestatistieken | Statistieken over de topologie. Als u het tijdsbestek voor een item in deze sectie wilt instellen, selecteert u de koppeling in de **kolom Venster.** |
| Spouts *(tijdsbestek)* | De tuiten gebruikt door de topologie. Als u meer informatie wilt weergeven over een specifieke uitloop, selecteert u de koppeling in de kolom **Id.** |
| Bouten *(tijdsbestek)* | De bouten gebruikt door de topologie. Als u meer informatie wilt weergeven over een specifieke bout, selecteert u de koppeling in de kolom **Id.** |
| Werknemersresources | Een lijst met werknemersbronnen. Als u meer informatie wilt weergeven over een specifieke werknemerbron, selecteert u de koppeling in de kolom **Host.** |
| Topologievisualisatie | Een knop **Visualisatie weergeven** met een visualisatie van de topologie. |
| Topologieconfiguratie | De configuratie van de geselecteerde topologie. |

De overzichtspagina stormtopologie lijkt op deze webpagina:

![Overzichtspagina topologie, Storm UI, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

In de sectie **Acties van de Topologie** u de volgende knoppen selecteren om een actie uit te voeren:

| Knop | Beschrijving |
| --- | --- |
| Activeren | Hervat de verwerking van een gedeactiveerde topologie. |
| Deactiveren | Hiermee wordt een hardlooptopologie onderbroken. |
| Opnieuw in evenwicht brengen | Hiermee past u het parallellisme van de topologie aan. U moet het uitvoeren van topologieën opnieuw in evenwicht brengen nadat u het aantal knooppunten in het cluster hebt gewijzigd. Met deze bewerking kan de topologie het parallellisme aanpassen om het extra of verminderde aantal knooppunten in het cluster te compenseren.<br/><br/>Zie <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Het begrijpen van de parallellisme van een Apache Storm-topologie</a>voor meer informatie.
| Doden | Beëindigt een stormtopologie na de opgegeven time-out. |
| Fouten opsporen | Hiermee begint u een foutopsporingssessie voor de hardlooptopologie. |
| Foutopsporing stoppen | Beëindigt de foutopsporingssessie voor de hardlooptopologie. |
| Logboekniveau wijzigen | Hiermee wijzigt u het foutopsporingslogboekniveau. |

##### <a name="spout-and-bolt-summary"></a>Samenvatting van tuit en bout

Als u een uitloop selecteert in de secties **Spouts** of **Bolts,** wordt de volgende informatie over het geselecteerde item weergegeven:

| Sectie | Beschrijving |
| --- | --- |
| Componentoverzicht | Basisinformatie over de tuit of bout. |
| Componentacties | **Foutopsporings-** en **Stop-foutopsporingsknoppen.** |
| Spout statistieken of bout stats | Statistieken over de tuit of bout. Als u het tijdsbestek voor een item in deze sectie wilt instellen, selecteert u de koppeling in de **kolom Venster.** |
| (Bolt-only)<br/>Invoerstatistieken *(tijdsbestek)* | Informatie over de invoerstromen die door de bout worden verbruikt. |
| Uitvoerstatistieken *(tijdsbestek)* | Informatie over de stromen die door de tuit of bout worden uitgezonden. |
| Profilering en foutopsporing | Besturingselementen voor het profileren en debuggen van de componenten op deze pagina. U de waarde **Status / Timeout (Minuten)** instellen en u knoppen selecteren voor **JStack,** **Werknemer opnieuw starten**en **Heap**. |
| Uitvoerders *(tijdsbestek)* | Informatie over de exemplaren van de tuit of bout. Als u een logboek met diagnostische informatie wilt weergeven dat voor dit exemplaar is geproduceerd, selecteert u de **poortvermelding** voor een specifieke executor. U ook de werknemersbronnen zien die zijn gekoppeld aan een specifieke uitvoerder door de koppeling in de kolom **Host te** selecteren. |
| Fouten | Eventuele foutinformatie voor de tuit of bout. |

De Storm bout samenvatting pagina ziet er vergelijkbaar met deze webpagina:

![Samenvattingspagina van de bout, Storm UI, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>De topologie bewaken en beheren met behulp van de REST API

De Storm-gebruikersinterface is gebouwd bovenop de REST API, zodat u vergelijkbare beheer- en bewakingstaken uitvoeren met behulp van de REST API. U de REST API gebruiken om aangepaste hulpprogramma's te maken voor het beheren en bewaken van Storm-topologieën.

Zie [De Apache Storm UI REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html)voor meer informatie . De volgende informatie is specifiek voor het gebruik van de REST API met Apache Storm op HDInsight.

> [!IMPORTANT]  
> De Storm REST API is niet openbaar beschikbaar via het internet. Het moet worden benaderd met behulp van een SSH-tunnel naar de HDInsight cluster kop knooppunt. Zie SSH-tunneling gebruiken voor informatie over het maken en gebruiken [van Een SSH-tunnel om toegang te krijgen tot Azure HDInsight.](../hdinsight-linux-ambari-ssh-tunnel.md)

### <a name="base-uri"></a>Basis URI

De basis URI voor de REST API op Linux-gebaseerde HDInsight clusters is beschikbaar op URL-adres, `https://HEADNODEFQDN:8744/api/v1/`waar u *HEADNODEFQDN* vervangt door het hoofdknooppunt. De domeinnaam van het hoofdknooppunt wordt gegenereerd tijdens het maken van het cluster en is niet statisch.

U de volledig gekwalificeerde domeinnaam (FQDN) voor het clusterhoofdknooppunt op verschillende manieren vinden:

| FQDN-detectiemethode | Beschrijving |
| --- | --- |
| SSH-sessie | Gebruik de `headnode -f` opdracht van een SSH-sessie naar het cluster. |
| Ambari Web | Selecteer Services boven aan de`https://CLUSTERNAME.azurehdinsight.net`pagina **Services** op de webpagina van het Ambari-cluster ( ) en selecteer **Vervolgens Storm**. Selecteer op het tabblad **Overzicht** de optie **Storm-gebruikersinterface .** De FQDN van het knooppunt dat de Storm UI en REST API host, wordt boven aan de pagina weergegeven. |
| Ambari REST API | Gebruik de `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` opdracht om informatie op te halen over het knooppunt waarop de gebruikersinterface van Storm en REST worden uitgevoerd. Vervang de twee exemplaren van *CLUSTERNAME* door de clusternaam. Wanneer u wordt gevraagd, voert u het wachtwoord in voor het gebruikersaccount (beheerder). In het antwoord bevat de "host_name" vermelding van de JSON-uitvoer de FQDN van het knooppunt. |

### <a name="authentication"></a>Authentication

Aanvragen voor de REST API moeten *basisverificatie*gebruiken, dus u moet de beheerdersnaam en het wachtwoord voor het HDInsight-cluster gebruiken.

> [!NOTE]  
> Omdat basisverificatie wordt verzonden met behulp van duidelijke tekst, moet u *altijd* HTTPS gebruiken om de communicatie met het cluster te beveiligen.

### <a name="return-values"></a>Retourwaarden

Informatie die wordt geretourneerd uit de REST API kan alleen bruikbaar zijn vanuit het cluster. De volledig gekwalificeerde domeinnaam (FQDN) die is geretourneerd voor [Apache ZooKeeper-servers](https://zookeeper.apache.org/) is bijvoorbeeld niet toegankelijk vanaf het internet.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [ontwikkelen van Java-gebaseerde topologieën met Apache Maven.](apache-storm-develop-java-topology.md)

Zie [Voorbeeld van Apache Storm-topologieën in Azure HDInsight](apache-storm-example-topology.md)voor een lijst met voorbeeldtopologieën.
