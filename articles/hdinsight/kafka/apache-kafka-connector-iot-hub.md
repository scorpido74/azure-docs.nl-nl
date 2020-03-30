---
title: Apache Kafka gebruiken op HDInsight met Azure IoT Hub
description: Meer informatie over het gebruik van Apache Kafka op HDInsight met Azure IoT Hub. Het Kafka Connect Azure IoT Hub-project biedt een bron- en sinkconnector voor Kafka. De bronconnector kan gegevens van IoT Hub lezen en de sink-connector wordt naar IoT Hub geschreven.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: 48a72b5ba3819712b9e1d2536ae2dd3a06eaf3f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238823"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Apache Kafka gebruiken op HDInsight met Azure IoT Hub

Meer informatie over het gebruik van de [Apache Kafka Connect Azure IoT Hub-connector](https://github.com/Azure/toketi-kafka-connect-iothub) om gegevens te verplaatsen tussen Apache Kafka op HDInsight en Azure IoT Hub. In dit document leert u hoe u de IoT Hub-connector uitvoert vanaf een randknooppunt in het cluster.

Met de Kafka Connect API u connectors implementeren die continu gegevens in Kafka ophalen of gegevens van Kafka naar een ander systeem pushen. De [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) is een connector die gegevens van Azure IoT Hub naar Kafka haalt. Het kan ook gegevens van Kafka naar de IoT Hub pushen.

Wanneer u uit de IoT-hub trekt, gebruikt u een __bronconnector.__ Wanneer u naar IoT Hub duwt, gebruikt u een __gootsteenconnector.__ De IoT Hub-connector biedt zowel de bron- als de gootsteenconnectoren.

In het volgende diagram wordt de gegevensstroom tussen Azure IoT Hub en Kafka op HDInsight weergegeven wanneer u de connector gebruikt.

![Afbeelding met gegevens die van IoT Hub naar Kafka via de connector stromen](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Zie [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect)voor meer informatie over de Connect API .

## <a name="prerequisites"></a>Vereisten

* Een Apache Kafka cluster op HDInsight. Zie de [snelstart voor Kafka in HDInsight](apache-kafka-get-started.md) voor meer informatie.

* Een randknooppunt in het Kafka-cluster. Zie het document [Randknooppunten gebruiken met HDInsight](../hdinsight-apps-use-edge-node.md) voor meer informatie.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Een Azure IoT-hub en -apparaat. Voor dit artikel u overwegen de online simulator Connect Raspberry Pi te gebruiken [voor Azure IoT Hub.](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started)

* [Scala build tool](https://www.scala-sbt.org/).

## <a name="build-the-connector"></a>De connector bouwen

1. Download de bron voor [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) de connector van uw lokale omgeving.

2. Navigeer vanuit een opdrachtprompt `toketi-kafka-connect-iothub-master` naar de map. Gebruik vervolgens de volgende opdracht om het project te bouwen en te verpakken:

    ```cmd
    sbt assembly
    ```

    De bouw duurt een paar minuten om te voltooien. Met de opdracht `kafka-connect-iothub-assembly_2.11-0.7.0.jar` wordt `toketi-kafka-connect-iothub-master\target\scala-2.11` een bestand gemaakt met de naam in de map voor het project.

## <a name="install-the-connector"></a>De connector installeren

1. Upload het .jar-bestand naar het randknooppunt van uw Kafka op HDInsight-cluster. Bewerk de onderstaande `CLUSTERNAME` opdracht door te vervangen door de werkelijke naam van uw cluster. De standaardwaarden voor het SSH-gebruikersaccount en de naam van [het randknooppunt](../hdinsight-apps-use-edge-node.md#access-an-edge-node) worden hieronder gebruikt en worden indien nodig gewijzigd.

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Zodra de bestandskopie is voltooid, maakt u verbinding met het randknooppunt met SSH:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Als u de connector `libs` in de Kafka-map wilt installeren, gebruikt u de volgende opdracht:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

Houd uw SSH-verbinding actief voor de resterende stappen.

## <a name="configure-apache-kafka"></a>Apache Kafka configureren

Gebruik de volgende stappen om Kafka te configureren om de connector in de zelfstandige modus uit te voeren, van uw SSH-verbinding tot het randknooppunt:

1. Wachtwoordvariabele instellen. Vervang WACHTWOORD door het wachtwoord voor het inloggen van het cluster en voer de opdracht in:

    ```bash
    export password='PASSWORD'
    ```

1. Installeer het [jq-hulpprogramma.](https://stedolan.github.io/jq/) jq maakt het eenvoudiger om JSON-documenten te verwerken die zijn geretourneerd uit Ambari-query's. Voer de volgende opdracht in:

    ```bash
    sudo apt -y install jq
    ```

1. Krijg het adres van de Kafka makelaars. Er kunnen veel makelaars in uw cluster, maar je hoeft alleen maar te verwijzen naar een of twee. Als u het adres van twee brokerhosts wilt krijgen, gebruikt u de volgende opdracht:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Kopieer de waarden voor later gebruik. De geretourneerde waarde ziet er ongeveer zo uit:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Haal het adres van de Apache Zookeeper knooppunten. Er zijn verschillende Zookeeper-knooppunten in het cluster, maar u hoeft slechts naar een of twee te verwijzen. Gebruik de volgende opdracht om de adressen `KAFKAZKHOSTS`in de variabele op te slaan:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. Wanneer u de connector in `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` de zelfstandige modus uitvoert, wordt het bestand gebruikt om te communiceren met de Kafka-makelaars. Als u `connect-standalone.properties` het bestand wilt bewerken, gebruikt u de volgende opdracht:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. Breng de volgende bewerkingen uit:

    |Huidige waarde |Nieuwe waarde | Opmerking |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|De `localhost:9092` waarde vervangen door de brokerhosts uit de vorige stap|Hiermee configureert u de zelfstandige configuratie voor het randknooppunt om de Kafka-makelaars te vinden.|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|Met deze wijziging u testen met behulp van de consoleproducent die bij Kafka is meegeleverd. Het kan zijn dat u verschillende converters nodig hebt voor andere producenten en consumenten. Zie voor [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)informatie over het gebruik van andere converterwaarden .|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|Hetzelfde als hierboven.|
    |N.v.t.|`consumer.max.poll.records=10`|Toevoegen aan het einde van het bestand. Deze wijziging is om time-outs in de gootsteenconnector te voorkomen door deze te beperken tot 10 records tegelijk. Zie voor meer [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)informatie .|

1. Als u het bestand wilt opslaan, gebruikt u __Ctrl + X,__ __Y__en voert u __enter .__

1. Als u de onderwerpen wilt maken die door de connector worden gebruikt, gebruikt u de volgende opdrachten:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Gebruik de `iotin` volgende `iotout` opdracht om te controleren of de onderwerpen en onderwerpen bestaan:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Het `iotin` onderwerp wordt gebruikt om berichten van IoT Hub te ontvangen. Het `iotout` onderwerp wordt gebruikt om berichten naar IoT Hub te verzenden.

## <a name="get-iot-hub-connection-information"></a>Informatie over de verbinding met IoT Hub

Als u iot-hubgegevens wilt ophalen die door de connector worden gebruikt, gebruikt u de volgende stappen:

1. Download het endpoint- en Event Hub-compatibele eindpuntnaam voor uw IoT-hub. Gebruik een van de volgende methoden om deze informatie te krijgen:

   * Gebruik de volgende stappen via __de [Azure-portal:](https://portal.azure.com/)__

     1. Navigeer naar uw IoT-hub en selecteer __Eindpunten.__
     2. Selecteer __Gebeurtenissen__in __ingebouwde eindpunten__.
     3. Kopieer de waarde van de volgende velden uit __eigenschappen:__

         * __Naam die compatibel is met gebeurtenishub__
         * __Eindpunt met gebeurtenishub__
         * __Partities__

        > [!IMPORTANT]  
        > De eindpuntwaarde van de portal kan extra tekst bevatten die in dit voorbeeld niet nodig is. De tekst extraheren die `sb://<randomnamespace>.servicebus.windows.net/`overeenkomt met dit patroon .

   * Gebruik de volgende opdracht vanuit __Azure [CLI:](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__

       ```azurecli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Vervang `myhubname` de naam van uw IoT-hub. Het antwoord is vergelijkbaar met de volgende tekst:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Ontvang het __beleid voor gedeelde toegang__ en de __sleutel__. Gebruik in dit voorbeeld de __servicesleutel.__ Gebruik een van de volgende methoden om deze informatie te krijgen:

    * Gebruik de volgende stappen via __de [Azure-portal:](https://portal.azure.com/)__

        1. Selecteer __Beleid voor gedeelde toegang__en selecteer __service__.
        2. Kopieer de waarde __primaire sleutel.__
        3. Kopieer de __tekenreeks Verbinding- primaire sleutel.__

    * Gebruik de volgende opdracht vanuit __Azure [CLI:](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__

        1. Als u de primaire sleutelwaarde wilt krijgen, gebruikt u de volgende opdracht:

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Vervang `myhubname` de naam van uw IoT-hub. Het antwoord is de `service` primaire sleutel tot het beleid voor deze hub.

        2. Als u de verbindingstekenreeks voor het `service` beleid wilt opvragen, gebruikt u de volgende opdracht:

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Vervang `myhubname` de naam van uw IoT-hub. Het antwoord is de `service` verbindingstekenreeks voor het beleid.

## <a name="configure-the-source-connection"></a>De bronverbinding configureren

Als u de bron wilt configureren om met uw IoT-hub te werken, voert u de volgende acties uit vanaf een SSH-verbinding met het randknooppunt:

1. Maak een kopie `connect-iot-source.properties` van `/usr/hdp/current/kafka-broker/config/` het bestand in de map. Als u het bestand wilt downloaden van het toketi-kafka-connect-iothub-project, gebruikt u de volgende opdracht:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. Als u `connect-iot-source.properties` het bestand wilt bewerken en de IoT-hubgegevens wilt toevoegen, gebruikt u de volgende opdracht:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. Zoek en wijzig in de editor de volgende vermeldingen:

    |Huidige waarde |Bewerken|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|Vervang `PLACEHOLDER` door `iotin`. Berichten ontvangen van IoT hub `iotin` worden geplaatst in het onderwerp.|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|Vervang `PLACEHOLDER` door de naam die compatibel is met de gebeurtenishub.|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|Vervang `PLACEHOLDER` door het eindpunt die compatibel is met de gebeurtenishub.|
    |`IotHub.AccessKeyName=PLACEHOLDER`|Vervang `PLACEHOLDER` door `service`.|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|Vervang `PLACEHOLDER` door de primaire `service` sleutel van het beleid.|
    |`IotHub.Partitions=PLACEHOLDER`|Vervang `PLACEHOLDER` het aantal partities uit de vorige stappen.|
    |`IotHub.StartTime=PLACEHOLDER`|Vervang `PLACEHOLDER` door een UTC-datum. Deze datum is wanneer de connector begint te controleren op berichten. De datumnotatie is `yyyy-mm-ddThh:mm:ssZ`.|
    |`BatchSize=100`|Vervang `100` door `5`. Deze wijziging zorgt ervoor dat de connector berichten in Kafka leest zodra er vijf nieuwe berichten in de IoT-hub zijn.|

    Zie Kafka Connect [Source Connector voor Azure IoT Hub voor](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md)een voorbeeldconfiguratie.

1. Als u wijzigingen wilt opslaan, gebruikt u __Ctrl + X,__ __Y__en voert u __Enter in__.

Zie [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md)voor meer informatie over het configureren van de connectorbron .

## <a name="configure-the-sink-connection"></a>De sinkverbinding configureren

Als u de sink-verbinding wilt configureren om met uw IoT-hub te werken, voert u de volgende acties uit vanaf een SSH-verbinding met het randknooppunt:

1. Maak een kopie `connect-iothub-sink.properties` van `/usr/hdp/current/kafka-broker/config/` het bestand in de map. Als u het bestand wilt downloaden van het toketi-kafka-connect-iothub-project, gebruikt u de volgende opdracht:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. Als u `connect-iothub-sink.properties` het bestand wilt bewerken en de IoT-hubgegevens wilt toevoegen, gebruikt u de volgende opdracht:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. Zoek en wijzig in de editor de volgende vermeldingen:

    |Huidige waarde |Bewerken|
    |---|---|
    |`topics=PLACEHOLDER`|Vervang `PLACEHOLDER` door `iotout`. Berichten die `iotout` naar het onderwerp zijn geschreven, worden doorgestuurd naar de IoT-hub.|
    |`IotHub.ConnectionString=PLACEHOLDER`|Vervang `PLACEHOLDER` de verbindingstekenreeks `service` voor het beleid.|

    Zie Kafka Connect [Sink Connector voor Azure IoT Hub voor](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)een voorbeeldconfiguratie voor een voorbeeldconfiguratie.

1. Als u wijzigingen wilt opslaan, gebruikt u __Ctrl + X,__ __Y__en voert u __Enter in__.

Zie [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)voor meer informatie over het configureren van de connectorsink .

## <a name="start-the-source-connector"></a>De bronconnector starten

1. Als u de bronconnector wilt starten, gebruikt u de volgende opdracht vanaf een SSH-verbinding met het randknooppunt:

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    Zodra de connector wordt gestart, stuurt u berichten naar de IoT-hub vanaf uw apparaat(en). Terwijl de connector berichten van de IoT-hub leest en deze opslaat in het Kafka-onderwerp, worden informatie op de console opgeslagen:

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > Mogelijk ziet u verschillende waarschuwingen wanneer de connector wordt gestart. Deze waarschuwingen veroorzaken geen problemen met het ontvangen van berichten van de IoT-hub.

1. Stop de connector na een paar minuten met **Ctrl + C** twee keer. Het duurt een paar minuten voordat de connector stopt.

## <a name="start-the-sink-connector"></a>De gootsteenconnector starten

Gebruik de volgende opdracht om de gootsteenconnector in de zelfstandige modus te starten, van een SSH-verbinding naar het randknooppunt:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Als de connector wordt uitgevoerd, wordt informatie weergegeven die vergelijkbaar is met de volgende tekst:

```output
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Mogelijk ziet u verschillende waarschuwingen als de connector wordt gestart. U kunt deze gewoon negeren.

## <a name="send-messages"></a>Berichten verzenden

Als u berichten via de connector wilt verzenden, gebruikt u de volgende stappen:

1. Open *een tweede* SSH-sessie voor het Kafka-cluster:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Krijg het adres van de Kafka-makelaars voor de nieuwe ssh-sessie. Vervang WACHTWOORD door het wachtwoord voor het inloggen van het cluster en voer de opdracht in:

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. Als u berichten `iotout` naar het onderwerp wilt verzenden, gebruikt u de volgende opdracht:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Met deze opdracht wordt u niet teruggegeven aan de normale Bash-prompt. In plaats daarvan stuurt `iotout` het toetsenbord invoer naar het onderwerp.

1. Als u een bericht naar uw apparaat wilt verzenden, plakt u een JSON-document in de SSH-sessie voor de `kafka-console-producer`.

    > [!IMPORTANT]  
    > U moet de waarde `"deviceId"` van de vermelding van de id van uw apparaat instellen. In het volgende voorbeeld wordt `myDeviceId`het apparaat genoemd:

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    Het schema voor dit JSON-document [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)wordt nader beschreven op .

    Als u het gesimuleerde Raspberry Pi-apparaat gebruikt en het wordt uitgevoerd, wordt het volgende bericht door het apparaat geregistreerd:

    ```output
    Receive message: Turn On
    ```

    Verzend het JSON-document opnieuw, maar `"message"` wijzig de waarde van de vermelding. De nieuwe waarde wordt geregistreerd door het apparaat.

Zie [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)voor meer informatie over het gebruik van de gootsteenconnector .

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u de Apache Kafka Connect API gebruiken om de IoT Kafka Connector op HDInsight te starten. Gebruik de volgende koppelingen om andere manieren te ontdekken om met Kafka te werken:

* [Apache Spark gebruiken met Apache Kafka op HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Gebruik Apache Storm met Apache Kafka op HDInsight](../hdinsight-apache-storm-with-kafka.md)
