---
title: 'Snelstartgids: Apache Kafka instellen op HDInsight met behulp van Azure Portal'
description: In deze quickstart leert u hoe u met Azure Portal een Apache Kafka-cluster maakt in Azure HDInsight. Er wordt ook aandacht besteed aan Kafka-onderwerpen, -abonnees en -consumenten.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/29/2020
ms.openlocfilehash: 0f6e1a7b8ac4dce0504fca5f03090588a75ae1ef
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82732438"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-azure-portal"></a>Snelstartgids: Apache Kafka cluster maken in azure HDInsight met behulp van Azure Portal

[Apache Kafka](./apache-kafka-introduction.md) is een open-source, gedistribueerd streamingplatform. Het wordt vaak gebruikt als een berichtenbroker, omdat het een functionaliteit biedt die vergelijkbaar is met een publicatie-/abonnementswachtrij voor berichten.

In deze Quick Start leert u hoe u een Apache Kafka cluster maakt met behulp van de Azure Portal. U leert ook hoe u de inbegrepen hulpprogramma's gebruikt voor het verzenden en ontvangen van berichten via Apache Kafka. Zie [clusters instellen in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)voor uitgebreide uitleg van de beschik bare configuraties. Zie [clusters maken in de portal](../hdinsight-hadoop-create-linux-clusters-portal.md)voor meer informatie over het gebruik van de portal om clusters te maken.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

De Apache Kafka-API is alleen toegankelijk voor resources binnen hetzelfde virtuele netwerk. In deze Quick Start hebt u rechtstreeks toegang tot het cluster via SSH. Als u andere services, netwerken of virtuele machines wilt verbinden met Apache Kafka, moet u eerst een virtueel netwerk maken en vervolgens de resources maken in het netwerk. Zie het document [Verbinding maken met Apache Kafka via een virtueel netwerk](apache-kafka-connect-vpn-gateway.md) voor meer informatie. Zie [een virtueel netwerk plannen voor Azure hdinsight](../hdinsight-plan-virtual-network-deployment.md)voor meer algemene informatie over het plannen van virtuele netwerken voor hdinsight.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka-cluster maken

Voer de volgende stappen uit om een Apache Kafka cluster te maken op HDInsight:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer in het bovenste menu de optie **+ een resource maken**.

    ![Azure Portal bron-HDInsight maken](./media/apache-kafka-get-started/azure-portal-create-resource.png)

1. Selecteer **Analytics** > **Azure HDInsight** om naar de pagina **HDInsight-cluster maken** te gaan.

1. Geef op **het tabblad basis beginselen** de volgende informatie op:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Abonnement    |  Selecteer in de vervolg keuzelijst het Azure-abonnement dat wordt gebruikt voor het cluster. |
    |Resourcegroep     | Maak een resourcegroep of selecteer een bestaande resourcegroep.  Een resourcegroep is een container met Azure-onderdelen.  In dit geval bevat de resourcegroep het HDInsight-cluster en het afhankelijke Azure Storage-account. |
    |Clusternaam   | Voer een wereldwijd unieke naam in. De naam mag Maxi maal 59 tekens bevatten, inclusief letters, cijfers en afbreek streepjes. De eerste en laatste tekens van de naam mogen geen streepjes zijn. |
    |Regio    | Selecteer in de vervolg keuzelijst een regio waar het cluster wordt gemaakt.  Kies een regio dichter bij u voor betere prestaties. |
    |Cluster type| Selecteer **cluster type selecteren** om een lijst te openen. Selecteer in de lijst **Kafka** als het cluster type.|
    |Versie|De standaard versie voor het cluster type wordt opgegeven. Selecteer in de vervolg keuzelijst de optie als u een andere versie wilt opgeven.|
    |Gebruikersnaam/Wachtwoord voor clusteraanmeldgegevens    | De standaard aanmeldings naam is **admin**. Het wacht woord moet uit minstens tien tekens bestaan en moet ten minste één cijfer, één hoofd letter en één kleine letters, één niet-alfanumeriek teken (behalve de tekens ' "') bevatten \). Zorg ervoor dat u **geen makkelijk te raden** wachtwoorden gebruikt, zoals 'Pass@word1'.|
    |SSH-gebruikersnaam (Secure Shell) | De standaardgebruikersnaam is **sshuser**.  U kunt hier echter een andere naam opgeven als u dat wilt. |
    |Wacht woord voor cluster aanmelding gebruiken voor SSH| Schakel dit selectie vakje in om hetzelfde wacht woord voor SSH-gebruiker te gebruiken als de gebruiker die u hebt opgegeven voor de aanmelding van het cluster.|

   ![Basis beginselen van het cluster Azure Portal maken](./media/apache-kafka-get-started/azure-portal-cluster-basics.png)

    Elke Azure-regio (locatie) heeft _foutdomeinen_. Een foutdomein is een logische groepering van de onderliggende hardware in een Azure-datacenter. Elk foutdomein deelt een algemene voedingsbron en netwerkswitch. De virtuele machines en beheerde schijven die de knooppunten in een HDInsight-cluster implementeren zijn verdeeld over deze foutdomeinen. Deze architectuur beperkt de potentiële impact van problemen met de fysieke hardware.

    Voor hoge beschikbaarheid van gegevens wordt u geadviseerd om een regio (locatie) te selecteren die __drie foutdomeinen__ heeft. Raadpleeg het document [Beschikbaarheid van virtuele Linux-machines](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) voor informatie over het aantal foutdomeinen in een regio.

    Selecteer het tabblad **volgende: opslag >>** om door te gaan naar de opslag instellingen.

1. Geef op het tabblad **opslag** de volgende waarden op:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Type van primaire opslag|Gebruik de standaard waarde **Azure Storage**.|
    |Selectiemethode|Gebruik de standaard waarde **uit de lijst**.|
    |Primair opslagaccount|Gebruik de vervolg keuzelijst om een bestaand opslag account te selecteren of selecteer **nieuwe maken**. Als u een nieuw account maakt, moet de naam tussen de 3 en 24 tekens lang zijn en mag alleen cijfers en kleine letters bevatten|
    |Container|De automatisch ingevulde waarde gebruiken.|

    ![HDInsight Linux aan de slag waarden voor cluster opslag opgeven](./media/apache-kafka-get-started/azure-portal-cluster-storage.png "Opslag waarden opgeven voor het maken van een HDInsight-cluster")

    Selecteer het tabblad **beveiliging en netwerk** .

1. Voor deze Quick start moet u de standaard beveiligings instellingen behouden. Ga naar [Een HDInsight-cluster configureren met Enterprise Security Package met behulp van Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md) voor meer informatie over Enterprise Security Package. Ga voor meer informatie over het gebruik van uw eigen sleutel voor Apache Kafka schijf versleuteling naar door de [klant beheerde sleutel schijf versleuteling](../disk-encryption.md)

   Als u uw cluster verbinding wilt laten maken met een virtueel netwerk, selecteert u een virtueel netwerk in de vervolgkeuzelijst **Virtueel netwerk**.

   ![Cluster toevoegen aan virtueel netwerk](./media/apache-kafka-get-started/azure-portal-cluster-security-networking-kafka-vnet.png)

    Selecteer het tabblad **configuratie + prijzen** .

1. Als u de beschik baarheid van Apache Kafka op HDInsight wilt garanderen, moet u het __aantal knoop punten__ invoeren voor **worker-knoop punt** instellen op 3 of hoger. De standaardwaarde is 4.

    Met de **standaard schijven per worker-knooppunt** vermelding wordt de schaal baarheid van Apache Kafka op HDInsight geconfigureerd. Apache Kafka in HDInsight gebruikt de lokale schijf van de virtuele machines in het cluster voor het opslaan van gegevens. Omdat Apache Kafka veel gebruikmaakt van invoer/uitvoer, wordt [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) gebruikt voor een hoge doorvoer en meer opslag per knooppunt. Het type beheerde schijf is __Standaard__ (HDD) of __Premium__ (SSD). Het type schijf is afhankelijk van de VM-grootte die wordt gebruikt door de werkknooppunten (Apache Kafka-brokers). Premium-schijven worden automatisch gebruikt met VM's uit de DS- en GS-serie. Alle andere VM-typen gebruiken standaardschijven.

   ![De Apache Kafka-clustergrootte instellen](./media/apache-kafka-get-started/azure-portal-cluster-configuration-pricing-kafka.png)

    Selecteer het tabblad **controleren + maken** .

1. Controleer de configuratie voor het cluster. Wijzig de instellingen die onjuist zijn. Selecteer ten slotte **maken** om het cluster te maken.

    ![samen vatting van Kafka-cluster configuratie](./media/apache-kafka-get-started/azure-portal-cluster-review-create-kafka.png)

    Het kan tot 20 minuten duren om het cluster te maken.

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

1. Gebruik de [SSH-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer vervolgens de volgende opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Voer het wachtwoord voor de SSH-gebruiker in wanneer hierom wordt gevraagd.

    Zodra er verbinding is gemaakt, ziet u informatie die er ongeveer als volgt uitziet:

    ```output
    Authorized uses only. All activity may be monitored and reported.
    Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    83 packages can be updated.
    37 updates are security updates.


    Welcome to Apache Kafka on HDInsight.

    Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
    ```

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Informatie over de Apache Zookeeper- en Broker-hosts ophalen

Als u met Kafka werkt, moet u de *Zookeeper*- en *Broker*-hosts kennen. Deze hosts worden gebruikt met de Apache Kafka-API en veel van de hulpprogramma's die bij Kafka worden meegeleverd.

In deze sectie vraagt u de hostgegevens op uit de Apache Ambari REST API in het cluster.

1. Installeer [JQ](https://stedolan.github.io/jq/), een JSON-processor op de opdracht regel. Dit hulp programma wordt gebruikt voor het parseren van JSON-documenten en is handig bij het parseren van de hostgegevens. Voer bij de open SSH-verbinding de volgende opdracht in `jq`die u wilt installeren:

    ```bash
    sudo apt -y install jq
    ```

1. Wachtwoord variabele instellen. Vervang `PASSWORD` door het wacht woord voor cluster aanmelding en voer de volgende opdracht in:

    ```bash
    export password='PASSWORD'
    ```

1. Pak de naam van het gecasete cluster uit. De daad werkelijke behuizing van de cluster naam kan anders zijn dan verwacht, afhankelijk van hoe het cluster is gemaakt. Met deze opdracht wordt de daad werkelijke behuizing opgehaald en opgeslagen in een variabele. Voer de volgende opdracht in:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Als u dit proces van buiten het cluster uitvoert, is er een andere procedure voor het opslaan van de cluster naam. Haal de cluster naam op in kleine letters van de Azure Portal. Vervang vervolgens de cluster naam door `<clustername>` in de volgende opdracht en voer deze uit: `export clusterName='<clustername>'`.


1. Als u een omgevings variabele met Zookeeper-hostgegevens wilt instellen, gebruikt u de onderstaande opdracht. Met de opdracht worden alle Zookeeper-hosts opgehaald en worden alleen de eerste twee vermeldingen geretourneerd. De reden hiervoor is dat u een bepaalde mate van redundantie wilt voor het geval één host onbereikbaar is.

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Voor deze opdracht is toegang tot Ambari vereist. Als uw cluster zich achter een NSG bevindt, voert u deze opdracht uit vanaf een computer die toegang heeft tot Ambari.

1. Gebruik de volgende opdracht om te controleren of de omgevingsvariabele juist is ingesteld:

    ```bash
    echo $KAFKAZKHOSTS
    ```

    Met deze opdracht wordt informatie geretourneerd die lijkt op de volgende tekst:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

1. Gebruik de volgende opdracht om een omgevingsvariabele in te stellen met brokerhostinformatie van Apache Kafka:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Voor deze opdracht is toegang tot Ambari vereist. Als uw cluster zich achter een NSG bevindt, voert u deze opdracht uit vanaf een computer die toegang heeft tot Ambari.

1. Gebruik de volgende opdracht om te controleren of de omgevingsvariabele juist is ingesteld:

    ```bash
    echo $KAFKABROKERS
    ```

    Met deze opdracht wordt informatie geretourneerd die lijkt op de volgende tekst:

    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Apache Kafka-onderwerpen beheren

Kafka slaat gegevensstromen op in zogenaamde *onderwerpen (topics)*. U kunt het hulpprogramma `kafka-topics.sh` gebruiken om onderwerpen te beheren.

* **Als u een onderwerp wilt maken**, gebruikt u de volgende opdracht in de SSH-verbinding:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Met deze opdracht brengt u een verbinding met Zookeeper tot stand met behulp van de hostinformatie die is opgeslagen in `$KAFKAZKHOSTS`. Vervolgens wordt er een Apache Kafka-onderwerp gemaakt met de naam **test**.

    * Gegevens die zijn opgeslagen in dit onderwerp worden gepartitioneerd in acht partities.

    * Elke partitie wordt gerepliceerd naar drie werkknooppunten in het cluster.

        * Als u het cluster hebt gemaakt in een Azure-regio met drie foutdomeinen, gebruikt u een replicatiefactor van drie. Gebruik anders een replicatiefactor van vier.
        
        * In regio's met drie foutdomeinen zorgt een replicatiefactor van drie ervoor dat replica's worden verdeeld over de foutdomeinen. In regio's met twee foutdomeinen zorgt een replicatiefactor van vier ervoor dat replica's worden verdeeld over de domeinen.
        
        * Raadpleeg het document [Beschikbaarheid van virtuele Linux-machines](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) voor informatie over het aantal foutdomeinen in een regio.

        * Apache Kafka kan niet overweg met Azure-foutdomeinen. Bij het maken van partitiereplica's voor onderwerpen worden replica's mogelijk niet goed gedistribueerd voor hoge beschikbaarheid.

        * Gebruik het [partitieherverdelingsprogramma van Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools) voor gegarandeerde hoge beschikbaarheid. Dit hulpprogramma moet vanuit een SSH-verbinding naar het hoofdknooppunt van het Apache Kafka-cluster worden uitgevoerd.

        * Om de hoogst mogelijke beschikbaarheid van uw Apache Kafka-gegevens te waarborgen, moet u de partitiereplica's voor uw onderwerp opnieuw indelen wanneer:

            * U een nieuw onderwerp of nieuwe partitie maakt

            * U een cluster omhoog schaalt

* Gebruik de volgende opdracht om **een lijst met onderwerpen op te vragen**:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Met deze opdracht worden de onderwerpen weergegeven die beschikbaar zijn in het Apache Kafka-cluster.

* Gebruik de volgende opdracht om **een onderwerp te verwijderen**:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    Met deze opdracht verwijdert u het onderwerp met de naam `topicname`.

    > [!WARNING]  
    > Als u het onderwerp `test` verwijdert dat u eerder hebt gemaakt, moet u het opnieuw maken. Het onderwerp wordt namelijk gebruikt in stappen verderop in dit document.

Gebruik de volgende opdracht voor meer informatie over de opdrachten die beschikbaar zijn met het hulpprogramma `kafka-topics.sh`:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Records maken en gebruiken

Kafka slaat *records* op in onderwerpen. Records worden geproduceerd door *producenten* en worden gebruikt door *consumenten*. Producenten en consumenten communiceren met de *Kafka-brokerservice*. Elk werkknooppunt in uw HDInsight-cluster is een Apache Kafka-brokerhost.

Gebruik de volgende stappen om records op te slaan in het testonderwerp dat u eerder hebt gemaakt. Lees deze vervolgens met behulp van een verbruiker:

1. Als u records wilt wegschrijven naar het onderwerp, gebruikt u het hulpprogramma `kafka-console-producer.sh` vanuit de SSH-verbinding:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```

    Na deze opdracht komt u aan bij een lege regel.

2. Typ een tekstbericht op de lege regel en druk op Enter. Voer op deze manier enkele berichten in en gebruik vervolgens **Ctrl+C** om terug te keren naar de normale prompt. Elke regel wordt als afzonderlijke record naar het Apache Kafka-onderwerp verzonden.

3. Als u records wilt lezen uit het onderwerp, gebruikt u het hulpprogramma `kafka-console-consumer.sh` vanuit de SSH-verbinding:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```

    Met deze opdracht haalt u de records van het onderwerp op en geeft u deze weer. Met `--from-beginning` wordt de consument gevraagd om bij het begin van de stream te beginnen, zodat alle records worden opgehaald.

    Vervang `--bootstrap-server $KAFKABROKERS` door `--zookeeper $KAFKAZKHOSTS` als u een oudere versie van Kafka gebruikt.

4. Gebruik __Ctrl+C__ om de consument te stoppen.

U kunt ook programmatisch producenten en consumenten maken. Zie het document [Producer and Consumer API van Apache Kafka met HDInsight](apache-kafka-producer-consumer-api.md) voor een voorbeeld van het gebruik van deze API.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die in deze snelstart zijn gemaakt, wilt opschonen, verwijdert u de resourcegroep. Als u de resourcegroep verwijdert, worden ook het bijbehorende HDInsight-cluster en eventuele andere resources die aan de resourcegroep zijn gekoppeld, verwijderd.

Ga als volgt te werk om de resourcegroep te verwijderen in Azure Portal:

1. Vouw het menu aan de linkerkant in Azure Portal uit om het menu met services te openen en kies __Resourcegroepen__ om de lijst met resourcegroepen weer te geven.
2. Zoek de resourcegroep die u wilt verwijderen en klik met de rechtermuisknop op de knop __Meer__ (... ) aan de rechterkant van de vermelding.
3. Selecteer __Resourcegroep verwijderen__ en bevestig dit.

> [!WARNING]  
> Als u een Apache Kafka cluster op HDInsight verwijdert, worden alle gegevens die zijn opgeslagen in Kafka, verwijderd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Apache Spark gebruiken met Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
