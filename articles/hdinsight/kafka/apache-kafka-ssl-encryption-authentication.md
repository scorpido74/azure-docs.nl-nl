---
title: Apache Kafka SSL-versleuteling & verificatie - Azure HDInsight
description: Ssl-versleuteling instellen voor communicatie tussen Kafka-klanten en Kafka-makelaars en tussen Kafka-makelaars. SSL-verificatie van clients instellen.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 4a363caf61046cf39c31ae2d5f35622b7b9109f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130011"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Ssl-versleuteling en -verificatie (Secure Sockets Layer) instellen voor Apache Kafka in Azure HDInsight

In dit artikel ziet u hoe u SSL-versleuteling instellen tussen Apache Kafka-clients en Apache Kafka-makelaars. Het toont u ook hoe u verificatie van clients instelt (soms aangeduid als tweerichtingsSSL).

> [!Important]
> Er zijn twee clients die u gebruiken voor Kafka-toepassingen: een Java-client en een consoleclient. Alleen de `ProducerConsumer.java` Java-client kan SSL gebruiken voor zowel produceren als consumeren. De client `console-producer.sh` van de consoleproducent werkt niet met SSL.

> [!Note] 
> HDInsight Kafka console producent met versie 1.1 ondersteunt geen SSL.
## <a name="apache-kafka-broker-setup"></a>Apache Kafka makelaar setup

De instelling voor Kafka SSL-broker gebruikt vier HDInsight-clustervm's op de volgende manier:

* headnode 0 - Certificaatautoriteit (CA)
* worker node 0, 1 en 2 - brokers

> [!Note] 
> Deze handleiding maakt gebruik van zelfondertekende certificaten, maar de meest veilige oplossing is het gebruik van certificaten die zijn uitgegeven door vertrouwde CA's.

De samenvatting van het systeemproces voor makelaars is als volgt:

1. De volgende stappen worden herhaald op elk van de drie werknemersknooppunten:

    1. Genereer een certificaat.
    1. Maak een certificaatondertekeningsaanvraag.
    1. Stuur het certificaatondertekeningsverzoek naar de Certificeringsinstantie (CA).
    1. Meld u aan bij de CA en onderteken het verzoek.
    1. SCP het ondertekende certificaat terug naar het werkknooppunt.
    1. SCP het openbare certificaat van de CA naar het werkknooppunt.

1. Zodra u alle certificaten hebt, zet de certs in de cert opslag.
1. Ga naar Ambari en wijzig de configuraties.

Gebruik de volgende gedetailleerde instructies om de installatie van de makelaar te voltooien:

> [!Important]
> In de volgende codefragmenten is wnX een afkorting voor een van `wn0`de `wn1` `wn2` drie werkknooppunten en moet worden vervangen door , of, indien van toepassing. `WorkerNode0_Name`en `HeadNode0_Name` moet worden vervangen door de namen van de respectieve machines.

1. Voer de eerste installatie uit op hoofdknooppunt 0, waarmee voor HDInsight de rol van de Certificaatautoriteit (CA) wordt ingevuld.

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Voer dezelfde initiële instelling uit op elk van de brokers (werknemersknooppunten 0, 1 en 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Voer op elk van de werkknooppunten de volgende stappen uit met het onderstaande codefragment.
    1. Maak een keystore en vul deze in met een nieuw privécertificaat.
    1. Maak een certificaatondertekeningsaanvraag.
    1. SCP de certificaatondertekeningsaanvraag voor de CA (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Voer op de CA-machine de volgende opdracht uit om ca-cert- en ca-key-bestanden te maken:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Wijzig naar de CA-machine en teken alle ontvangen cert-ondertekeningsaanvragen:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Stuur de ondertekende certificaten terug naar de werknemersknooppunten van de CA (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Stuur het openbare certificaat van de CA naar elk werknemersknooppunt.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Voeg op elk werknemersknooppunt het openbare certificaat van de CV's toe aan de truststore en de keystore. Voeg vervolgens het ondertekende certificaat van het werknemersknooppunt toe aan de keystore

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Kafka-configuratie bijwerken om SSL te gebruiken en brokers opnieuw op te starten

U hebt nu elke Kafka-broker ingesteld met een keystore en truststore en de juiste certificaten geïmporteerd. Wijzig vervolgens gerelateerde Kafka-configuratie-eigenschappen met Ambari en start vervolgens de Kafka-brokers opnieuw.

Ga als volgt te werk om de configuratiewijziging te voltooien:

1. Meld u aan bij de Azure-portal en selecteer uw Azure HDInsight Apache Kafka-cluster.
1. Ga naar de Ambari-gebruikersinterface door te klikken op **Ambari-huis** onder **Clusterdashboards**.
1. Onder **Kafka Broker** stel je het **luisteraars-pand** in op`PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. Onder **Advanced kafka-broker** stelt u de eigenschap **security.inter.broker.protocol** in op`SSL`

    ![Kafka ssl-configuratie-eigenschappen bewerken in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Onder **Custom kafka-broker** stel de eigenschap `required` **ssl.client.auth** in op . Deze stap is alleen vereist als u verificatie en versleuteling instelt.

    ![Kafka ssl-configuratie-eigenschappen bewerken in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Voeg nieuwe configuratie-eigenschappen toe aan het bestand server.properties.

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Ga naar de gebruikersinterface van de Ambari-configuratie en controleer of de nieuwe eigenschappen worden weergegeven onder **Advanced kafka-env** en de eigenschap **kafka-env-sjabloon.**

    Voor HDI-versie 3.6:

    ![Kafka-env-sjabloon bewerken, eigenschap in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    Voor HDI-versie 4.0:

     ![Kafka-env-sjabloon bewerken, eigenschap in Ambari vier](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)   

1. Herstart alle Kafka-makelaars.
1. Start de admin client met producent en consument opties om te controleren of zowel producenten als consumenten werken aan poort 9093.

## <a name="client-setup-without-authentication"></a>Clientinstelling (zonder verificatie)

Als u geen verificatie nodig hebt, is de samenvatting van de stappen om alleen SSL-versleuteling in te stellen:

1. Meld u aan bij de CA (actief hoofdknooppunt).
1. Kopieer het CA-cert naar de clientmachine vanuit de CA-machine (wn0).
1. Meld u aan bij de clientmachine (hn1) en navigeer naar de `~/ssl` map.
1. Importeer het CA-cert naar de truststore.
1. Importeer het CA-cert naar de sleutelopslag.

Deze stappen worden beschreven in de volgende codefragmenten.

1. Meld u aan bij het CA-knooppunt.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. Het ca-cert naar de clientmachine kopiëren

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Meld u aan bij de clientmachine (stand-by hoofdknooppunt).

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Importeer het CA-certificaat in de truststore.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Importeer het CA-cert in keystore.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Maak het `client-ssl-auth.properties`bestand . Zij moet de volgende regels hebben:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

## <a name="client-setup-with-authentication"></a>Clientinstelling (met verificatie)

> [!Note]
> De volgende stappen zijn alleen vereist als **and** u zowel SSL-versleuteling als verificatie instelt. Als u alleen versleuteling instelt, raadpleegt u [Clientsetup zonder verificatie.](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

In de volgende vier stappen worden de taken samengevat die nodig zijn om de clientinstelling te voltooien:

1. Meld u aan bij de clientmachine (stand-by hoofdknooppunt).
1. Maak een java keystore en ontvang een ondertekend certificaat voor de makelaar. Kopieer het certificaat vervolgens naar de VM waar de CA wordt uitgevoerd.
1. Schakel over naar de CA-machine (actief hoofdknooppunt) om het clientcertificaat te ondertekenen.
1. Ga naar de clientmachine (stand-by hoofdknooppunt) en navigeer naar de `~/ssl` map. Kopieer het ondertekende cert naar de clientmachine.

De details van elke stap worden hieronder gegeven.

1. Meld u aan bij de clientmachine (stand-by hoofdknooppunt).

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Verwijder een bestaande ssl directory.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Maak een java keystore en maak een certificaatondertekeningsaanvraag. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. De certificaatondertekeningsaanvraag naar de CA kopiëren

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Schakel over naar de CA-machine (actief hoofdknooppunt) en teken het clientcertificaat.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Het ondertekende clientcert kopiëren van de CA (actief hoofdknooppunt) naar de clientmachine.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. Het ca-cert naar de clientmachine kopiëren

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Maak clientwinkel met ondertekend cert en importeer ca-cert in de keystore en truststore:

    ```bash
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    ```

1. Een bestand `client-ssl-auth.properties`maken . Zij moet de volgende regels hebben:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Verificatie

> [!Note]
> Als HDInsight 4.0 en Kafka 2.1 zijn geïnstalleerd, u de consoleproducent/consumenten gebruiken om uw installatie te verifiëren. Zo niet, voer dan de Kafka-producent uit op poort 9092 en stuur berichten naar het onderwerp en gebruik vervolgens de Kafka-consument op poort 9093 die SSL gebruikt.

### <a name="kafka-21-or-above"></a>Kafka 2.1 of hoger

1. Maak een onderwerp als het nog niet bestaat.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1.  Start console producent en `client-ssl-auth.properties` geef het pad naar als een configuratiebestand voor de producent.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1.  Open een andere ssh-verbinding met clientmachine en `client-ssl-auth.properties` start consoleconsumenten en geef het pad naar als configuratiebestand voor de consument.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1.1

1. Maak een onderwerp als het nog niet bestaat.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1.  Start consoleproducent en geef het pad naar client-ssl-auth.properties als configuratiebestand voor de producent.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

3.  Open een andere ssh-verbinding met clientmachine en `client-ssl-auth.properties` start consoleconsumenten en geef het pad naar als configuratiebestand voor de consument.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Volgende stappen

* [Wat is Apache Kafka in HDInsight?](apache-kafka-introduction.md)
