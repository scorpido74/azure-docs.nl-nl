---
title: Apache Storm topologieën implementeren en beheren in azure HDInsight
description: Meer informatie over het implementeren, bewaken en beheren van Apache Storm topologieën met behulp van het Storm-dash board op HDInsight op basis van Linux. Gebruik Hadoop-hulpprogram ma's voor Visual Studio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: 1c219c85836eb4730fa90918385555c433a12449
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915098"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Apache Storm topologieën implementeren en beheren in azure HDInsight 

In dit document leert u de basis beginselen van het beheren en bewaken van [Apache Storm](https://storm.apache.org/) topologieën die worden uitgevoerd op Storm op HDInsight-clusters.

## <a name="prerequisites"></a>Vereisten

* Een Apache Storm cluster in HDInsight. Zie [Apache Hadoop clusters maken met behulp van de Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en selecteer **Storm** voor het **cluster type**.


* Beschrijving Vertrouwd met SSH en SCP: Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie.

* Beschrijving Visual Studio: Azure SDK 2.5.1 of nieuwer en de Data Lake-Hulpprogram Ma's voor Visual Studio. Zie [aan de slag met data Lake-Hulpprogram ma's voor Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)voor meer informatie.

    Een van de volgende versies van Visual Studio:

  * Visual Studio 2012 met update 4

  * Visual Studio 2013 met update 4 of [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (alle edities)

  * Visual Studio 2017 (willekeurige editie). Data Lake-Hulpprogram Ma's voor Visual Studio 2017 worden geïnstalleerd als onderdeel van de Azure-workload.

## <a name="submit-a-topology-visual-studio"></a>Een topologie verzenden: Visual Studio

De HDInsight-Hulpprogram Ma's kunnen worden gebruikt C# voor het verzenden of hybride topologieën naar uw Storm-cluster. In de volgende stappen wordt een voorbeeld toepassing gebruikt. Zie voor meer informatie over het maken van met de HDInsight-Hulpprogram Ma's [topologieën ontwikkelen C# met de Hdinsight-Hulpprogram Ma's voor Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

1. Als u de nieuwste versie van de Data Lake-hulpprogram ma's voor Visual Studio nog niet hebt geïnstalleerd, raadpleegt u [aan de slag met data Lake-Hulpprogram ma's voor Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > De Data Lake-Hulpprogram Ma's voor Visual Studio werden voorheen de HDInsight-Hulpprogram Ma's voor Visual Studio genoemd.
    >
    > Data Lake-Hulpprogram Ma's voor Visual Studio zijn opgenomen in de __Azure-workload__ voor visual studio 2017.

2. Open Visual Studio, selecteer **bestand** > **Nieuw** > **project**.

3. Vouw in het dialoog venster **Nieuw project** de optie **geïnstalleerde** > **sjablonen**uit en selecteer **HDInsight**. Selecteer in de lijst met sjablonen Storm-voor **beeld**. Typ onder in het dialoog venster een naam voor de toepassing.

    ![image](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

4. Klik in **Solution Explorer**met de rechter muisknop op het project en selecteer **verzenden naar Storm op HDInsight**.

   > [!NOTE]  
   > Voer de aanmeldings referenties voor uw Azure-abonnement in als u hierom wordt gevraagd. Als u meer dan één abonnement hebt, meldt u zich aan bij de versie die uw Storm in HDInsight-cluster bevat.

5. Selecteer uw Storm op HDInsight-cluster in de vervolg keuzelijst **Storm-cluster** en selecteer vervolgens **verzenden**. U kunt controleren of de verzen ding is geslaagd met behulp van het **uitvoer** venster.

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Een topologie verzenden: SSH en de Storm-opdracht

1. Gebruik SSH om verbinding te maken met het HDInsight-cluster. Vervang **username** door de naam van uw SSH-aanmelding. Vervang **clustername** door de naam van uw HDInsight-cluster:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie over het gebruik van SSH om verbinding te maken met uw HDInsight-cluster.

2. Gebruik de volgende opdracht om een voorbeeldtopologie te starten:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Met deze opdracht wordt de WordCount-voorbeeldtopologie gestart op het cluster. Deze topologie genereert wille keurig zinnen en telt vervolgens het exemplaar van elk woord in de zinnen.

   > [!NOTE]  
   > Bij het indienen van de topologie bij het cluster moet u eerst het jar-bestand met het cluster kopiëren voordat u de opdracht `storm` gebruikt. U kunt de `scp` opdracht gebruiken om het bestand naar het cluster te kopiëren. Bijvoorbeeld: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > Het WordCount-voorbeeld en andere Storm Starter-voorbeelden zijn al in uw cluster opgenomen in `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Een topologie verzenden: programmatisch

U kunt een topologie programmatisch implementeren met behulp van de Nimbus-service. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)biedt een voor beeld van een Java-toepassing die laat zien hoe u een topologie kunt implementeren en starten via de Nimbus-service.

## <a name="monitor-and-manage-visual-studio"></a>Controleren en beheren: Visual Studio

Wanneer een topologie wordt verzonden met behulp van Visual Studio, wordt de weer gave **Storm-topologieën** weer gegeven. Selecteer de topologie in de lijst om informatie over de actieve topologie weer te geven.

![monitor voor Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> U kunt Storm- **topologieën** ook bekijken vanuit **Server Explorer** door **Azure** > **HDInsight**uit te vouwen, vervolgens met de rechter muisknop op een storm op HDInsight-cluster te klikken en **Storm-topologieën weer geven**te selecteren.

Selecteer de vorm voor de spouts of bouten om informatie over deze onderdelen weer te geven. Er wordt een nieuw venster geopend voor elk geselecteerd item.

### <a name="deactivate-and-reactivate"></a>Deactiveren en opnieuw activeren

Als u een topologie deactiveert, wordt deze onderbroken totdat deze is afgebroken of opnieuw geactiveerd. Als u deze bewerkingen wilt uitvoeren, gebruikt u de knoppen __deactiveren__ en __opnieuw activeren__ boven aan het __topologie overzicht__.

### <a name="rebalance"></a>Opnieuw verdelen

Door een topologie te herverdelen kan het systeem de parallellisme van de topologie herzien. Als u bijvoorbeeld het formaat van het cluster hebt gewijzigd om meer notities toe te voegen, kunt u met herverdeling de nieuwe knoop punten weer geven met een topologie.

Als u een topologie wilt herverdelen, gebruikt u de knop opnieuw __verdelen__ boven aan het __topologie overzicht__.

> [!WARNING]  
> Als u een topologie herbalanceert, wordt de topologie eerst gedeactiveerd, waarna de werk nemers gelijkmatig over het cluster worden gedistribueerd. vervolgens wordt de topologie met de status weer gegeven voordat deze is opgebalanceerd. Dus als de topologie actief was, wordt deze weer actief. Als de service is gedeactiveerd, blijft deze gedeactiveerd.

### <a name="kill-a-topology"></a>Een topologie beëindigen

Storm-topologieën blijven actief totdat ze worden gestopt of het cluster wordt verwijderd. Als u een topologie wilt stoppen, gebruikt u de knop __Kill__ aan de bovenkant van het __topologie overzicht__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Controleren en beheren: SSH en de Storm-opdracht

Met `storm` het hulp programma kunt u met het uitvoeren van topologieën werken vanaf de opdracht regel. Gebruiken `storm -h` voor een volledige lijst met opdrachten.

### <a name="list-topologies"></a>Topologieën weer geven

Gebruik de volgende opdracht om een lijst met alle actieve topologieën weer te geven:

    storm list

Met deze opdracht wordt informatie geretourneerd die lijkt op de volgende tekst:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Deactiveren en opnieuw activeren

Als u een topologie deactiveert, wordt deze onderbroken totdat deze is afgebroken of opnieuw geactiveerd. Gebruik de volgende opdracht om te deactiveren en opnieuw te activeren:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Een actieve topologie beëindigen

Storm-topologieën, zodra het is gestart, blijven actief totdat het wordt gestopt. Gebruik de volgende opdracht om een topologie te stoppen:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Opnieuw verdelen

Door een topologie te herverdelen kan het systeem de parallellisme van de topologie herzien. Als u bijvoorbeeld het formaat van het cluster hebt gewijzigd om meer notities toe te voegen, kunt u met herverdeling de nieuwe knoop punten weer geven met een topologie.

> [!WARNING]  
> Als u een topologie herbalanceert, wordt de topologie eerst gedeactiveerd, waarna de werk nemers gelijkmatig over het cluster worden gedistribueerd. vervolgens wordt de topologie met de status weer gegeven voordat deze is opgebalanceerd. Dus als de topologie actief was, wordt deze weer actief. Als de service is gedeactiveerd, blijft deze gedeactiveerd.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Controleren en beheren: Storm-gebruikers interface

De Storm-gebruikersinterface biedt een webinterface voor het werken met actieve topologieën en is opgenomen in uw HDInsight-cluster. Als u de Storm-gebruikers interface wilt weer geven, gebruikt **https://CLUSTERNAME.azurehdinsight.net/stormui** u een webbrowser om te openen, waarbij **clustername** de naam van uw cluster is.

> [!NOTE]  
> Als u wordt gevraagd een gebruikersnaam en een wachtwoord op te geven, voert u de gegevens voor de clusterbeheerder (admin) en het wachtwoord in die u hebt gebruikt toen u het cluster maakte.

### <a name="main-page"></a>Hoofd pagina

De hoofd pagina van de Storm-gebruikers interface bevat de volgende informatie:

* **Cluster overzicht**: Basis informatie over het Storm-cluster.
* **Topologie overzicht**: Een lijst met actieve topologieën. Gebruik de koppelingen in deze sectie om meer informatie over specifieke topologieën te bekijken.
* **Overzicht Super Visor**: Informatie over de Storm-Super Visor.
* **Nimbus-configuratie**: Nimbus-configuratie voor het cluster.

### <a name="topology-summary"></a>Topologie samenvatting

Als u een koppeling selecteert in het gedeelte **topologie overzicht** , wordt de volgende informatie over de topologie weer gegeven:

* **Topologie overzicht**: Basis informatie over de topologie.
* **Topologie acties**: Beheer acties die u voor de topologie kunt uitvoeren.

  * **Activeren**: Hervat de verwerking van een gedeactiveerde topologie.
  * **Deactiveren**: Hiermee wordt een actieve topologie onderbroken.
  * **Herverdeling**: Hiermee past u de parallellisme van de topologie aan. Nadat u het aantal knooppunten in het cluster hebt gewijzigd, moet u actieve topologieën opnieuw verdelen. Met deze bewerking kan de topologie de parallellisme aanpassen om het toegenomen of kleinere aantal knoop punten in het cluster te compenseren.

    Zie <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">informatie over de parallellisme van een Apache Storm topologie</a>voor meer informatie.
  * **Afsluiten**: Hiermee wordt een storm-topologie beëindigd na de opgegeven time-out.
* **Topologie statistieken**: Statistieken over de topologie. Als u het tijds bestek voor de resterende vermeldingen op de pagina wilt instellen, gebruikt u de koppelingen in de kolom **venster** .
* **Spouts**: De spouts die wordt gebruikt door de topologie. Gebruik de koppelingen in deze sectie om meer informatie over specifieke spouts weer te geven.
* **Bouten**: De schichten die door de topologie worden gebruikt. Gebruik de koppelingen in deze sectie om meer informatie over specifieke schichten te bekijken.
* **Topologie configuratie**: De configuratie van de geselecteerde topologie.

### <a name="spout-and-bolt-summary"></a>Samen vatting van Spout en Schicht

Als u een Spout selecteert in de secties **Spouts** of **Bouts** , wordt de volgende informatie over het geselecteerde item weer gegeven:

* **Samen vatting van onderdelen**: Basis informatie over de Spout of bout.
* **Spout/flits statistieken**: Statistieken over de Spout of bout. Als u het tijds bestek voor de resterende vermeldingen op de pagina wilt instellen, gebruikt u de koppelingen in de kolom **venster** .
* **Invoer statistieken** (alleen flits): Informatie over de invoer stromen die door de schicht worden gebruikt.
* **Uitvoer statistieken**: Informatie over de stromen die worden verzonden door de Spout of bout.
* **Uitvoerendeers**: Informatie over de instanties van de Spout of bout. Selecteer de poort vermelding voor een specifieke **uitvoerder** om een logboek met diagnostische gegevens weer te geven die voor deze instantie zijn geproduceerd.
* **Fouten**: Eventuele fout gegevens voor de Spout of bout.

## <a name="monitor-and-manage-rest-api"></a>Controleren en beheren: REST-API

De Storm-gebruikers interface is gebaseerd op de REST API, zodat u vergelijk bare beheer-en controle functionaliteit kunt uitvoeren met behulp van de REST API. U kunt de REST API gebruiken om aangepaste hulp middelen te maken voor het beheren en bewaken van Storm-topologieën.

Zie [Apache Storm UI rest API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html)voor meer informatie. De volgende informatie is specifiek voor het gebruik van de REST API met Apache Storm op HDInsight.

> [!IMPORTANT]  
> De Storm-REST API is niet openbaar beschikbaar via internet en moet worden geopend met behulp van een SSH-tunnel naar het hoofd knooppunt van het HDInsight-cluster. Zie [ssh-tunneling gebruiken om toegang te krijgen tot Apache Ambari Web UI, Resource Manager, JobHistory, NameNode, Apache Oozie en andere web-UIs](../hdinsight-linux-ambari-ssh-tunnel.md)voor informatie over het maken en gebruiken van een SSH-tunnel.

### <a name="base-uri"></a>Basis-URI

De basis-URI voor de rest API op op Linux gebaseerde HDInsight-clusters is beschikbaar op het hoofd knooppunt op **https\/:/HEADNODEFQDN: 8744/API/v1/** . De domein naam van het hoofd knooppunt wordt gegenereerd tijdens het maken van het cluster en is niet statisch.

U kunt de Fully Qualified Domain Name (FQDN) voor het hoofd knooppunt van het cluster op verschillende manieren vinden:

* **Vanuit een SSH-sessie**: Gebruik de opdracht `headnode -f` van een SSH-sessie naar het cluster.
* **Van Ambari-Web**: Selecteer **Services** boven aan de pagina en selecteer vervolgens **Storm**. Selecteer op het tabblad **samen vatting** de optie **Storm-gebruikers interface Server**. De FQDN van het knoop punt dat als host fungeert voor de Storm-gebruikers interface en REST API wordt boven aan de pagina weer gegeven.
* **Van Ambari rest API**: Gebruik de opdracht `curl -u admin -G "https:\//CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` om informatie op te halen over het knoop punt waarop de Storm-gebruikers interface en de rest API worden uitgevoerd. Vervang **clustername** door de naam van het cluster. Wanneer u hierom wordt gevraagd, voert u het wacht woord in voor het account voor aanmelding (Administrator). In het antwoord bevat de vermelding ' host_name ' de FQDN-naam van het knoop punt.

### <a name="authentication"></a>Authentication

Aanvragen voor de REST API moeten **basis verificatie**gebruiken, dus u gebruikt de naam en het wacht woord van de HDInsight-cluster beheerder.

> [!NOTE]  
> Omdat basis verificatie wordt verzonden met behulp van Lees bare tekst, moet u **altijd** https gebruiken om de communicatie met het cluster te beveiligen.

### <a name="return-values"></a>Retour waarden

Informatie die wordt geretourneerd door de REST API, kan alleen worden gebruikt vanuit het cluster. Zo is de Fully Qualified Domain Name (FQDN) die voor [Apache ZooKeeper](https://zookeeper.apache.org/) servers wordt geretourneerd, niet toegankelijk vanaf internet.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [ontwikkelen van op Java gebaseerde topologieën met Apache Maven](apache-storm-develop-java-topology.md).

Zie [voorbeeld topologieën voor Apache Storm op HDInsight](apache-storm-example-topology.md)voor een lijst met voor beeld-topologieën.
