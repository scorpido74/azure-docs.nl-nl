---
title: Spiegel Apache Kafka onderwerpen - Azure HDInsight
description: Meer informatie over het gebruik van de spiegelfunctie van Apache Kafka om een replica van een Kafka op HDInsight-cluster te onderhouden door onderwerpen te spiegelen aan een secundair cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 45977f52226fac0a3e23455ce9457a721947a8cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425881"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>MirrorMaker gebruiken om Apache Kafka-onderwerpen te repliceren met Kafka in HDInsight

Meer informatie over het gebruik van de spiegelfunctie van Apache Kafka om onderwerpen te repliceren naar een secundair cluster. Mirroring kan worden uitgevoerd als een continu proces, of met tussenpozen worden gebruikt als een methode voor het migreren van gegevens van het ene cluster naar het andere.

In dit voorbeeld wordt spiegelen gebruikt om onderwerpen tussen twee HDInsight-clusters te repliceren. Beide clusters bevinden zich in verschillende virtuele netwerken in verschillende datacenters.

> [!WARNING]  
> Spiegelen mag niet worden beschouwd als een middel om fouttolerantie te bereiken. De verschuiving naar items binnen een onderwerp is verschillend tussen de primaire en secundaire clusters, zodat clients de twee niet door elkaar kunnen gebruiken.
>
> Als u zich zorgen maakt over fouttolerantie, moet u replicatie instellen voor de onderwerpen binnen uw cluster. Zie [Aan de slag met Apache Kafka op HDInsight](apache-kafka-get-started.md)voor meer informatie.

## <a name="how-apache-kafka-mirroring-works"></a>Hoe Apache Kafka spiegelen werkt

Mirroring werkt met het gereedschap [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (onderdeel van Apache Kafka) om records van onderwerpen op het primaire cluster te consumeren en vervolgens een lokale kopie op het secundaire cluster te maken. MirrorMaker maakt gebruik van een (of meer) *consumenten* die lezen uit het primaire cluster, en een *producent* die schrijft naar de lokale (secundaire) cluster.

De meest nuttige mirroring setup voor disaster recovery maakt gebruik van Kafka clusters in verschillende Azure regio's. Om dit te bereiken, worden de virtuele netwerken waar de clusters zich bevinden samengekeken.

Het volgende diagram illustreert het spiegelingsproces en hoe de communicatie tussen clusters stroomt:

![Diagram van het spiegelproces](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

De primaire en secundaire clusters kunnen verschillen in het aantal knooppunten en partities, en verschuivingen binnen de onderwerpen zijn ook verschillend. Mirroring behoudt de sleutelwaarde die wordt gebruikt voor partitionering, zodat de recordvolgorde per sleutel wordt bewaard.

### <a name="mirroring-across-network-boundaries"></a>Spiegelen over netwerkgrenzen heen

Als u moet spiegelen tussen Kafka-clusters in verschillende netwerken, zijn er de volgende aanvullende overwegingen:

* **Gateways**: De netwerken moeten kunnen communiceren op TCP/IP-niveau.

* **Serveradressering:** U ervoor kiezen om uw clusterknooppunten aan te pakken met hun IP-adressen of volledig gekwalificeerde domeinnamen.

    * **IP-adressen:** Als u uw Kafka-clusters configureert om IP-adresadvertenties te gebruiken, u doorgaan met de mirroring-instelling met behulp van de IP-adressen van de brokerknooppunten en zookeeper-knooppunten.
    
    * **Domeinnamen:** Als u uw Kafka-clusters niet configureert voor IP-adresadvertenties, moeten de clusters verbinding met elkaar kunnen maken met behulp van FQDN's (Fully Qualified Domain Names). Hiervoor is een DNS-server (Domain Name System) in elk netwerk nodig die is geconfigureerd om aanvragen naar de andere netwerken door te sturen. Bij het maken van een Azure Virtual Network moet u in plaats van de automatische DNS die bij het netwerk is geleverd, een aangepaste DNS-server en het IP-adres voor de server opgeven. Nadat het virtuele netwerk is gemaakt, moet u vervolgens een Azure Virtual Machine maken die dat IP-adres gebruikt en vervolgens DNS-software erop installeren en configureren.

    > [!WARNING]  
    > Maak en configureer de aangepaste DNS-server voordat u HDInsight installeert in het virtuele netwerk. Er is geen extra configuratie vereist voor HDInsight om de DNS-server te gebruiken die is geconfigureerd voor het virtuele netwerk.

Zie [Een VNet-naar-VNet-verbinding configureren](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)voor meer informatie over het verbinden van twee Azure Virtual Networks.

## <a name="mirroring-architecture"></a>Architectuur spiegelen

Deze architectuur bevat twee clusters in verschillende brongroepen en virtuele netwerken: een **primaire** en **secundaire**.

### <a name="creation-steps"></a>Stappen maken

1. Maak twee nieuwe resourcegroepen:

    |Resourcegroep | Locatie |
    |---|---|
    | kafka-primary-rg | VS - centraal |
    | kafka-secundair-rg | VS - noord-centraal |

1. Maak een nieuw virtueel netwerk **kafka-primary-vnet** in **kafka-primary-rg**. Laat de standaardinstellingen achter.
1. Maak een nieuw virtueel netwerk **kafka-secondary-vnet** in **kafka-secondary-rg**, ook met standaardinstellingen.

1. Maak twee nieuwe Kafka-clusters:

    | Clusternaam | Resourcegroep | Virtual Network | Opslagaccount |
    |---|---|---|---|
    | kafka-primair-cluster | kafka-primary-rg | kafka-primary-vnet | kafkaprimarystorage |
    | kafka-secundair-cluster | kafka-secundair-rg | kafka-secundair-vnet | kafkasecondarystorage |

1. Maak virtuele netwerkpeerings. Deze stap creëert twee peerings: een van **kafka-primary-vnet** tot **kafka-secondary-vnet** en een terug van **kafka-secondary-vnet** naar **kafka-primary-vnet**.
    1. Selecteer het virtuele **kafka-primaire-vnet-netwerk.**
    1. Selecteer **Peerings** onder **Instellingen**.
    1. Selecteer **Toevoegen**.
    1. Voer op het scherm **Peering toevoegen** de details in zoals in de onderstaande schermafbeelding.

        ![HDInsight Kafka toevoegen vnet peering](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

### <a name="configure-ip-advertising"></a>IP-reclame configureren

Configureer IP-advertenties om een client in staat te stellen verbinding te maken met IP-adressen van broker in plaats van domeinnamen.

1. Ga naar het Ambari-dashboard `https://PRIMARYCLUSTERNAME.azurehdinsight.net`voor het primaire cluster: .
1. Selecteer **Services** > **Kafka**. CliSelectck het tabblad **Configs.**
1. Voeg de volgende config-lijnen toe aan de sectie onderste **kafka-env-sjabloon.** Selecteer **Opslaan**.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Voer een notitie in op het scherm **Configuratie opslaan** en klik op **Opslaan**.
1. Als u wordt gevraagd om configuratiewaarschuwing, klikt u **op Toch doorgaan**.
1. Selecteer **Ok** op de **configuratiewijzigingen opslaan**.
1. Selecteer**Alle betrokkenen opnieuw** **starten** > in de melding **Vereist opnieuw starten.** Selecteer **Alles opnieuw starten bevestigen**.

    ![Apache Ambari herstart alle getroffenen](./media/apache-kafka-mirroring/ambari-restart-notification.png)

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>Configureer Kafka om te luisteren op alle netwerkinterfaces.
    
1. Blijf op het tabblad **Configs** onder **Services** > **Kafka**. Stel in de sectie Kafka `PLAINTEXT://0.0.0.0:9092` **Broker** de eigenschap **luisteraars** in op .
1. Selecteer **Opslaan**.
1. Selecteer **Opnieuw starten**en Alles opnieuw starten **bevestigen**.

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>Ip-adressen van Record Broker en Zookeeper-adressen voor het primaire cluster.

1. Selecteer **Hosts** op het Ambari-dashboard.
1. Noteer de IP-adressen voor de makelaars en dierenverzorgers. De broker knooppunten hebben **wn** als de eerste twee letters van de host naam, en de zookeeper knooppunten hebben **zk** als de eerste twee letters van de host naam.

    ![Apache Ambari bekijk knooppunt ip-adressen](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Herhaal de vorige drie stappen voor het tweede cluster **kafka-secundair-cluster:** configureer IP-advertenties, stel luisteraars in en noteer de IP-adressen van de broker en zookeeper.

## <a name="create-topics"></a>Onderwerpen maken

1. Maak verbinding met het **primaire** cluster met SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Vervang **sshuser** door de SSH-gebruikersnaam die wordt gebruikt bij het maken van het cluster. Vervang **PRIMARYCLUSTER** door de basisnaam die wordt gebruikt bij het maken van het cluster.

    Zie [SSH-sleutels gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie.

1. Gebruik de volgende opdracht om een variabele te maken met de Apache Zookeeper-hosts voor het primaire cluster. De tekenreeksen moeten `ZOOKEEPER_IP_ADDRESS1` worden vervangen door de werkelijke IP-adressen die eerder zijn geregistreerd, zoals `10.23.0.11` en `10.23.0.7`. Als u FQDN-resolutie gebruikt met een aangepaste DNS-server, voert u [de volgende stappen](apache-kafka-get-started.md#getkafkainfo) uit om namen van tusseneffecten en dierenverzorgers te krijgen.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Als u een `testtopic`onderwerp met de naam wilt maken, gebruikt u de volgende opdracht:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. Gebruik de volgende opdracht om te controleren of het onderwerp is gemaakt:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    Het antwoord `testtopic`bevat .

1. Gebruik het volgende om de hostgegevens van Zookeeper voor dit **(primaire)** cluster weer te geven:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Hiermee worden informatie geretourneerd die vergelijkbaar is met de volgende tekst:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Sla deze informatie op. Het wordt gebruikt in de volgende sectie.

## <a name="configure-mirroring"></a>Mirroring configureren

1. Maak verbinding met het **secundaire** cluster via een andere SSH-sessie:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Vervang **sshuser** door de SSH-gebruikersnaam die wordt gebruikt bij het maken van het cluster. Vervang **SECONDARYCLUSTER** door de naam die wordt gebruikt bij het maken van het cluster.

    Zie [SSH-sleutels gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie.

1. Een `consumer.properties` bestand wordt gebruikt om de communicatie met het **primaire** cluster te configureren. Als u het bestand wilt maken, gebruikt u de volgende opdracht:

    ```bash
    nano consumer.properties
    ```

    Gebruik de volgende tekst als `consumer.properties` de inhoud van het bestand:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Vervang **PRIMARY_ZKHOSTS** door de IP-adressen van Zookeeper uit het **primaire** cluster.

    Dit bestand beschrijft de consumenteninformatie die u moet gebruiken bij het lezen van het primaire Kafka-cluster. Zie [Consumer Configs](https://kafka.apache.org/documentation#consumerconfigs) op kafka.apache.org voor meer informatie over de configuratie van consumenten.

    Als u het bestand wilt opslaan, gebruikt u **Ctrl + X,** **Y**en voert u **enter .**

1. Voordat u de producent configureert die communiceert met het secundaire cluster, stelt u een variabele in voor de IP-adressen van de broker van het **secundaire** cluster. Gebruik de volgende opdrachten om deze variabele te maken:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    De `echo $SECONDARY_BROKERHOSTS` opdracht moet informatie retourneren die vergelijkbaar is met de volgende tekst:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. Een `producer.properties` bestand wordt gebruikt om het **secundaire** cluster te communiceren. Als u het bestand wilt maken, gebruikt u de volgende opdracht:

    ```bash
    nano producer.properties
    ```

    Gebruik de volgende tekst als `producer.properties` de inhoud van het bestand:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Vervang **SECONDARY_BROKERHOSTS** door de IP-adressen van de makelaar die in de vorige stap zijn gebruikt.

    Zie [Producent Configs](https://kafka.apache.org/documentation#producerconfigs) op kafka.apache.org voor meer informatie over de configuratie van de producent.

1. Gebruik de volgende opdrachten om een omgevingsvariabele te maken met de IP-adressen van de Zookeeper-hosts voor het secundaire cluster:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. De standaardconfiguratie voor Kafka op HDInsight staat het automatisch maken van onderwerpen niet toe. U moet een van de volgende opties gebruiken voordat u het mirroringproces start:

    * **De onderwerpen op het secundaire cluster maken:** met deze optie u ook het aantal partities en de replicatiefactor instellen.

        U onderwerpen van tevoren maken met de volgende opdracht:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Vervang `testtopic` de naam van het te maken onderwerp.

    * **Het cluster configureren voor het automatisch maken**van onderwerpen: met deze optie kan MirrorMaker automatisch onderwerpen maken, maar het kan ze maken met een ander aantal partities of replicatiefactor dan het primaire onderwerp.

        Voer de volgende stappen uit om het secundaire cluster te configureren om automatisch onderwerpen te maken:

        1. Ga naar het Ambari-dashboard `https://SECONDARYCLUSTERNAME.azurehdinsight.net`voor het secundaire cluster: .
        1. Klik **op Services** > **Kafka**. Klik op het tabblad **Configs.**
        1. Voer __Filter__ in het veld Filter `auto.create`een waarde in van . Hiermee filtert u de `auto.create.topics.enable` lijst met eigenschappen en wordt de instelling weergegeven.
        1. Wijzig de `auto.create.topics.enable` waarde van true en selecteer __Opslaan__. Voeg een notitie toe en selecteer opnieuw __opslaan.__
        1. Selecteer de __Kafka-service,__ selecteer __Opnieuw starten__en selecteer __Alle betrokkenen opnieuw starten__. Wanneer u daarom wordt gevraagd, selecteert __u Alles opnieuw starten bevestigen__.

        ![kafka automatisch maken van onderwerpen inschakelen](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>MirrorMaker starten

1. Gebruik de volgende opdracht om het MirrorMaker-proces te starten vanaf de SSH-verbinding met het **secundaire** cluster:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    De parameters die in dit voorbeeld worden gebruikt zijn:

    |Parameter |Beschrijving |
    |---|---|
    |--consumer.config|Hiermee geeft u het bestand op dat consumenteneigenschappen bevat. Deze eigenschappen worden gebruikt om een consument te maken die leest uit het *primaire* Kafka-cluster.|
    |--producent.config|Hiermee geeft u het bestand op dat producenteneigenschappen bevat. Deze eigenschappen worden gebruikt om een producent te maken die naar het *secundaire Kafka-cluster* schrijft.|
    |--whitelist|Een lijst met onderwerpen die MirrorMaker repliceert van het primaire cluster naar het secundaire.|
    |--num.streams|Het aantal consumententhreads dat moet worden gemaakt.|

    De consument op het secundaire knooppunt wacht nu op berichten.

2. Gebruik de volgende opdracht om een producent te starten en berichten naar het onderwerp te verzenden, van de SSH-verbinding naar het **primaire** cluster:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Wanneer u met een cursor op een lege lijn aankomt, typt u een paar tekstberichten in. De berichten worden verzonden naar het onderwerp op het **primaire** cluster. Gebruik Ctrl **+ C** om het productieproces te beëindigen wanneer u klaar bent.

3. Gebruik **Ctrl + C** om het MirrorMaker-proces te beëindigen van de SSH-verbinding naar het **secundaire** cluster. Het kan enkele seconden duren om het proces te beëindigen. Als u wilt controleren of de berichten zijn gerepliceerd naar het secundaire, gebruikt u de volgende opdracht:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    De lijst met `testtopic`onderwerpen bevat nu , die wordt gemaakt wanneer MirrorMaster het onderwerp weerspiegelt van het primaire cluster naar het secundaire. De berichten die uit het onderwerp worden opgehaald, zijn dezelfde als de berichten die u in het primaire cluster hebt ingevoerd.

## <a name="delete-the-cluster"></a>Het cluster verwijderen

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

De stappen in dit document hebben clusters gemaakt in verschillende Azure-brongroepen. Als u alle gemaakte resources wilt verwijderen, u de twee resourcegroepen verwijderen die zijn gemaakt: **kafka-primary-rg** en **kafka-secondary_rg**. Als u de brongroepen verwijdert, worden alle resources verwijderd die zijn gemaakt door dit document te volgen, inclusief clusters, virtuele netwerken en opslagaccounts.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) gebruiken om een replica van een [Apache Kafka-cluster](https://kafka.apache.org/) te maken. Gebruik de volgende koppelingen om andere manieren te ontdekken om met Kafka te werken:

* [Apache Kafka MirrorMaker documentatie](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) op cwiki.apache.org.
* [Beste praktijken van Kafka Mirror Maker](https://community.cloudera.com/t5/Community-Articles/Kafka-Mirror-Maker-Best-Practices/ta-p/249269)
* [Aan de slag met Apache Kafka op HDInsight](apache-kafka-get-started.md)
* [Apache Spark gebruiken met Apache Kafka op HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Verbinding maken met Apache Kafka via een Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)
