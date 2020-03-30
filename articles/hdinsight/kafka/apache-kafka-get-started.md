---
title: 'Snelstart: Apache Kafka instellen op HDInsight met Azure-portal'
description: In deze quickstart leert u hoe u met Azure Portal een Apache Kafka-cluster maakt in Azure HDInsight. Er wordt ook aandacht besteed aan Kafka-onderwerpen, -abonnees en -consumenten.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/24/2020
ms.openlocfilehash: 90f7010970f70379c8adecc4214c44d896a1beaf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80130270"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-azure-portal"></a>Snelstart: Apache Kafka-cluster maken in Azure HDInsight met Azure-portal

[Apache Kafka](./apache-kafka-introduction.md) is een open-source, gedistribueerd streamingplatform. Het wordt vaak gebruikt als een berichtenbroker, omdat het een functionaliteit biedt die vergelijkbaar is met een publicatie-/abonnementswachtrij voor berichten.

In deze snelstart leert u hoe u met Azure Portal een Apache Kafka-cluster maakt. U leert ook hoe u de inbegrepen hulpprogramma's gebruikt voor het verzenden en ontvangen van berichten via Apache Kafka. Zie [Clusters instellen in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)voor uitgebreide uitleg over beschikbare configuraties. Zie [Clusters maken in de portal](../hdinsight-hadoop-create-linux-clusters-portal.md)voor meer informatie over het gebruik van de portal om clusters te maken.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

De Apache Kafka-API is alleen toegankelijk voor resources binnen hetzelfde virtuele netwerk. In deze snelstart benadert u het cluster rechtstreeks via SSH. Als u andere services, netwerken of virtuele machines wilt verbinden met Apache Kafka, moet u eerst een virtueel netwerk maken en vervolgens de resources maken in het netwerk. Zie het document [Verbinding maken met Apache Kafka via een virtueel netwerk](apache-kafka-connect-vpn-gateway.md) voor meer informatie.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka-cluster maken

Als u een Apache Kafka-cluster op HDInsight wilt maken, gebruikt u de volgende stappen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer in het bovenste menu **+ Een resource maken.**

    ![Azure-portal maakt bron HDInsight](./media/apache-kafka-get-started/azure-portal-create-resource.png)

1. Selecteer **Analytics** > **Azure HDInsight** om naar de **clusterpagina HDInsight maken** te gaan.

1. Geef op het tabblad **Basisbeginselen** de volgende informatie op:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Abonnement    |  Selecteer in de vervolgkeuzelijst het Azure-abonnement dat voor het cluster wordt gebruikt. |
    |Resourcegroep     | Maak een resourcegroep of selecteer een bestaande resourcegroep.  Een resourcegroep is een container met Azure-onderdelen.  In dit geval bevat de resourcegroep het HDInsight-cluster en het afhankelijke Azure Storage-account. |
    |Clusternaam   | Voer een wereldwijd unieke naam in. De naam kan bestaan uit maximaal 59 tekens, waaronder letters, cijfers en koppeltekens. De eerste en laatste tekens van de naam mogen geen streepjes zijn. |
    |Regio    | Selecteer in de vervolgkeuzelijst een gebied waar het cluster wordt gemaakt.  Kies een regio dichter bij u voor betere prestaties. |
    |Clustertype| Selecteer **Clustertype selecteren** om een lijst te openen. Selecteer In de lijst **Kafka** als clustertype.|
    |Versie|De standaardversie voor het clustertype wordt opgegeven. Kies in de vervolgkeuzelijst als u een andere versie wilt opgeven.|
    |Gebruikersnaam/Wachtwoord voor clusteraanmeldgegevens    | De standaardinlognaam is **beheerder**. Het wachtwoord moet ten minste tien tekens lang zijn en moet ten minste één cijfer, één hoofdletter en één kleine \)letter bevatten, één niet-alfanumeriek teken (met uitzondering van tekens " " . Zorg ervoor dat u **geen makkelijk te raden** wachtwoorden gebruikt, zoals 'Pass@word1'.|
    |SSH-gebruikersnaam (Secure Shell) | De standaardgebruikersnaam is **sshuser**.  U kunt hier echter een andere naam opgeven als u dat wilt. |
    |Wachtwoord voor clusteraanmelding voor SSH gebruiken| Schakel dit selectievakje in om hetzelfde wachtwoord te gebruiken voor SSH-gebruiker als het wachtwoord dat u hebt opgegeven voor de gebruikers van het clusteraanmelding.|

   ![Azure-portal maakt clusterbasisbeginselen](./media/apache-kafka-get-started/azure-portal-cluster-basics.png)

    Elke Azure-regio (locatie) heeft _foutdomeinen_. Een foutdomein is een logische groepering van de onderliggende hardware in een Azure-datacenter. Elk foutdomein deelt een algemene voedingsbron en netwerkswitch. De virtuele machines en beheerde schijven die de knooppunten in een HDInsight-cluster implementeren zijn verdeeld over deze foutdomeinen. Deze architectuur beperkt de potentiële impact van problemen met de fysieke hardware.

    Voor hoge beschikbaarheid van gegevens wordt u geadviseerd om een regio (locatie) te selecteren die __drie foutdomeinen__ heeft. Raadpleeg het document [Beschikbaarheid van virtuele Linux-machines](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) voor informatie over het aantal foutdomeinen in een regio.

    Selecteer het tabblad **Volgende: Opslag >>** om door te gaan naar de opslaginstellingen.

1. Geef op het tabblad **Opslag** de volgende waarden op:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Type van primaire opslag|Gebruik de standaardwaarde **Azure Storage**.|
    |Selectiemethode|Gebruik de standaardwaarde **Selecteren uit lijst**.|
    |Primair opslagaccount|Gebruik de vervolgkeuzelijst om een bestaand opslagaccount te selecteren of selecteer **Nieuw maken**. Als u een nieuw account maakt, moet de naam tussen de 3 en 24 tekens lang zijn en kunnen er alleen cijfers en kleine letters worden vermeld|
    |Container|Gebruik de automatisch ingevulde waarde.|

    ![HDInsight Linux aan de slag bieden cluster opslag waarden](./media/apache-kafka-get-started/azure-portal-cluster-storage.png "Opslagwaarden opgeven voor het maken van een HDInsight-cluster")

    Selecteer het tabblad **Beveiliging + netwerken.**

1. Laat voor deze snelstart de standaardbeveiligingsinstellingen staan. Ga naar [Een HDInsight-cluster configureren met Enterprise Security Package met behulp van Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md) voor meer informatie over Enterprise Security Package. Ga naar [klantbeheer sleutelschijfversleuteling](../disk-encryption.md) voor meer informatie over het gebruik van uw eigen sleutel voor Apache Kafka-schijfversleuteling

   Als u uw cluster verbinding wilt laten maken met een virtueel netwerk, selecteert u een virtueel netwerk in de vervolgkeuzelijst **Virtueel netwerk**.

   ![Cluster toevoegen aan virtueel netwerk](./media/apache-kafka-get-started/azure-portal-cluster-security-networking-kafka-vnet.png)

    Selecteer het tabblad **Configuratie + prijzen.**

1. Om de beschikbaarheid van Apache Kafka op HDInsight te garanderen, moet het __aantal knooppunten__ dat wordt ingesteld voor **worker node** worden ingesteld op 3 of hoger. De standaardwaarde is 4.

    De **standaardschijven per schijfknooppuntinvoer** configureren de schaalbaarheid van Apache Kafka op HDInsight. Apache Kafka in HDInsight gebruikt de lokale schijf van de virtuele machines in het cluster voor het opslaan van gegevens. Omdat Apache Kafka veel gebruikmaakt van invoer/uitvoer, wordt [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) gebruikt voor een hoge doorvoer en meer opslag per knooppunt. Het type beheerde schijf is __Standaard__ (HDD) of __Premium__ (SSD). Het type schijf is afhankelijk van de VM-grootte die wordt gebruikt door de werkknooppunten (Apache Kafka-brokers). Premium-schijven worden automatisch gebruikt met VM's uit de DS- en GS-serie. Alle andere VM-typen gebruiken standaardschijven.

   ![De Apache Kafka-clustergrootte instellen](./media/apache-kafka-get-started/azure-portal-cluster-configuration-pricing-kafka.png)

    Selecteer het tabblad **Controleren + maken.**

1. Controleer de configuratie voor het cluster. Wijzig alle instellingen die onjuist zijn. Selecteer tot slot **Maken** om het cluster te maken.

    ![kafka-clusterconfiguratieoverzicht](./media/apache-kafka-get-started/azure-portal-cluster-review-create-kafka.png)

    Het kan tot 20 minuten duren om het cluster te maken.

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

1. Gebruik [de ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer de opdracht in:

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

1. Installeer [jq](https://stedolan.github.io/jq/), een command-line JSON processor. Dit hulpprogramma wordt gebruikt om JSON-documenten te ontleden en is handig bij het ontleden van de hostinformatie. Voer vanaf de geopende SSH-verbinding de volgende opdracht in om te installeren: `jq`

    ```bash
    sudo apt -y install jq
    ```

1. Wachtwoordvariabele instellen. Vervang `PASSWORD` het wachtwoord voor het inloggen van het cluster en voer de opdracht in:

    ```bash
    export password='PASSWORD'
    ```

1. Haal de correct ingeschreven clusternaam eruit. De werkelijke behuizing van de clusternaam kan anders zijn dan u verwacht, afhankelijk van hoe het cluster is gemaakt. Deze opdracht krijgt de werkelijke behuizing en slaat deze vervolgens op in een variabele. Voer de volgende opdracht in:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Als u dit proces van buiten het cluster doet, is er een andere procedure voor het opslaan van de clusternaam. Haal de clusternaam in kleine letters uit de Azure-portal. Vervang vervolgens de clusternaam voor `<clustername>` in de `export clusterName='<clustername>'`volgende opdracht en voer deze uit: .


1. Als u een omgevingsvariabele wilt instellen met hostgegevens van Zookeeper, gebruikt u de onderstaande opdracht. De opdracht haalt alle Zookeeper-hosts op en retourneert vervolgens alleen de eerste twee vermeldingen. De reden hiervoor is dat u een bepaalde mate van redundantie wilt voor het geval één host onbereikbaar is.

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Deze opdracht vereist Toegang tot Ambari. Als uw cluster zich achter een NSG bevindt, voert u deze opdracht uit vanaf een machine die toegang heeft tot Ambari. 

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
    > Deze opdracht vereist Toegang tot Ambari. Als uw cluster zich achter een NSG bevindt, voert u deze opdracht uit vanaf een machine die toegang heeft tot Ambari. 

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

        Als u het cluster hebt gemaakt in een Azure-regio met drie foutdomeinen, gebruikt u een replicatiefactor van drie. Gebruik anders een replicatiefactor van vier.
        
        In regio's met drie foutdomeinen zorgt een replicatiefactor van drie ervoor dat replica's worden verdeeld over de foutdomeinen. In regio's met twee foutdomeinen zorgt een replicatiefactor van vier ervoor dat replica's worden verdeeld over de domeinen.
        
        Raadpleeg het document [Beschikbaarheid van virtuele Linux-machines](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) voor informatie over het aantal foutdomeinen in een regio.

        Apache Kafka kan niet overweg met Azure-foutdomeinen. Bij het maken van partitiereplica's voor onderwerpen worden replica's mogelijk niet goed gedistribueerd voor hoge beschikbaarheid.

        Gebruik het [partitieherverdelingsprogramma van Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools) voor gegarandeerde hoge beschikbaarheid. Dit hulpprogramma moet vanuit een SSH-verbinding naar het hoofdknooppunt van het Apache Kafka-cluster worden uitgevoerd.

        Om de hoogst mogelijke beschikbaarheid van uw Apache Kafka-gegevens te waarborgen, moet u de partitiereplica's voor uw onderwerp opnieuw indelen wanneer:

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
> Als u een Apache Kafka-cluster verwijdert op HDInsight, worden alle gegevens die in Kafka zijn opgeslagen, verwijderd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Apache Spark gebruiken met Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
