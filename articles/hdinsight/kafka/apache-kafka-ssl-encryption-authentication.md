---
title: SSL-versleutelings & authenticatie Apache Kafka-Azure HDInsight
description: SSL-versleuteling instellen voor communicatie tussen Kafka-clients en Kafka-Brokers, en tussen Kafka-brokers. SSL-verificatie van clients instellen.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 5dd698b28a01ed251492cf34e9da2dda4d0c2580
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241998"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Versleuteling en verificatie van Secure Sockets Layer (SSL) instellen voor Apache Kafka in azure HDInsight

In dit artikel wordt beschreven hoe u SSL-versleuteling instelt tussen Apache Kafka-clients en Apache Kafka brokers. U kunt ook zien hoe u verificatie van clients instelt (ook wel SSL (Two-eenrichtings) genoemd).

> [!Important]
> Er zijn twee clients die u kunt gebruiken voor Kafka-toepassingen: een Java-client en een-console-client. Alleen de Java-client `ProducerConsumer.java` kan SSL gebruiken voor zowel produceren als verbruiken. De console producer-client `console-producer.sh` werkt niet met SSL.

## <a name="apache-kafka-broker-setup"></a>Setup van Apache Kafka Broker

Het installatie programma voor Kafka SSL Broker maakt op de volgende manier gebruik van vier HDInsight-cluster-Vm's:

* hoofd knooppunt 0-certificerings instantie (CA)
* worker-knoop punt 0, 1 en 2-Brokers

> [!Note] 
> In deze hand leiding worden zelfondertekende certificaten gebruikt, maar de veiligste oplossing is het gebruik van certificaten die zijn uitgegeven door vertrouwde certificerings instanties.

De samen vatting van het installatie proces van de Broker is als volgt:

1. De volgende stappen worden herhaald op elk van de drie worker-knoop punten:

    1. Genereer een certificaat.
    1. Maak een aanvraag voor certificaat ondertekening.
    1. Verzend de aanvraag voor certificaat ondertekening naar de certificerings instantie (CA).
    1. Meld u aan bij de CA en onderteken de aanvraag.
    1. SCP het ondertekende certificaat terug naar het worker-knoop punt.
    1. SCP het open bare certificaat van de certificerings instantie naar het worker-knoop punt.

1. Als u alle certificaten hebt, plaatst u de Certificaats in het certificaat archief.
1. Ga naar Ambari en wijzig de configuraties.

Gebruik de volgende gedetailleerde instructies om de Broker-installatie te volt ooien:

> [!Important]
> In de volgende code fragmenten wnX is een afkorting voor een van de drie worker-knoop punten en moet worden vervangen door `wn0`, `wn1` of `wn2` als dat nodig is. `WorkerNode0_Name` en `HeadNode0_Name` moeten worden vervangen door de namen van de respectieve computers, zoals `wn0-abcxyz` of `hn0-abcxyz`.

1. Voer de eerste installatie uit op het hoofd knooppunt 0, dat voor HDInsight de rol van de certificerings instantie (CA) zal vullen.

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Voer dezelfde initiële installatie uit voor elk van de Brokers (worker-knoop punten 0, 1 en 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Voer op elk van de worker-knoop punten de volgende stappen uit met behulp van het code fragment hieronder.
    1. Maak een-opslag en vul deze in met een nieuw persoonlijk certificaat.
    1. Maak een aanvraag voor certificaat ondertekening.
    1. SCP de aanvraag voor certificaat ondertekening aan de CA (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Voer op de CA-computer de volgende opdracht uit om CA-certificaat-en CA-sleutel bestanden te maken:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Ga naar de CA-computer en Onderteken alle ontvangen aanvragen voor certificaat ondertekening:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Stuur de ondertekende certificaten terug naar de worker-knoop punten van de CA (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Verzend het open bare certificaat van de certificerings instantie naar elk worker-knoop punt.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Voeg op elk worker-knoop punt het open bare CAs-certificaat toe aan de truststore en de-opslag. Voeg vervolgens een eigen ondertekend certificaat toe aan de opslag van het werk knooppunt

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Kafka-configuratie bijwerken voor het gebruik van SSL en het opnieuw opstarten van Brokers

U hebt nu elke Kafka-Broker met een Store en truststore ingesteld en de juiste certificaten geïmporteerd. Wijzig vervolgens de eigenschappen van gerelateerde Kafka-configuratie met behulp van Ambari en start vervolgens de Kafka-Brokers opnieuw.

Voer de volgende stappen uit om de configuratie wijziging te volt ooien:

1. Meld u aan bij de Azure Portal en selecteer uw Azure HDInsight Apache Kafka-cluster.
1. Ga naar de Ambari-gebruikers interface door te klikken op **Ambari start** onder **cluster dashboards**.
1. Stel de eigenschap **listeners** onder **Kafka Broker** in op `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. Onder **Geavanceerd Kafka-Broker** stelt u de eigenschap **Security. intercompany Broker. protocol** in op `SSL`

    ![Kafka SSL-configuratie-eigenschappen bewerken in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Stel de eigenschap **SSL. client. auth** onder **aangepast Kafka-broker** in op `required`. Deze stap is alleen vereist als u verificatie en versleuteling instelt.

    ![Kafka SSL-configuratie-eigenschappen bewerken in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Onder **Geavanceerde Kafka-env** voegt u de volgende regels toe aan het einde van de **Kafka-envelop-sjabloon** eigenschap.

    ```config
    # Needed to configure IP address advertising
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

    ![De eigenschap Kafka-env-sjabloon bewerken in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

1. Start alle Kafka-Brokers opnieuw op.
1. Start de admin-client met producer-en consumenten opties om te controleren of zowel producenten als consumenten op poort 9093 werken.

## <a name="client-setup-with-authentication"></a>Client installatie (met verificatie)

> [!Note]
> De volgende stappen zijn alleen vereist als u SSL-versleuteling **en** verificatie instelt. Als u alleen versleuteling wilt instellen, kunt u door gaan naar de [client installatie zonder verificatie](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

Voer de volgende stappen uit om de client installatie te volt ooien:

1. Meld u aan bij de client computer (HN1).
1. Een Java-opslag archief maken en een ondertekend certificaat voor de Broker ophalen. Kopieer vervolgens het certificaat naar de virtuele machine waarop de CA wordt uitgevoerd.
1. Schakel over naar de CA-computer (hn0) om het client certificaat te ondertekenen.
1. Ga naar de client machine (HN1) en ga naar de map `~/ssl`. Kopieer het ondertekende certificaat naar de client computer.

```bash
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123"

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (hn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:MyServerPassword123

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert from the CA (hn0) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Bekijk ten slotte het bestand `client-ssl-auth.properties` met de opdracht `cat client-ssl-auth.properties`. Deze moet de volgende regels bevatten:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>Client installatie (zonder verificatie)

Als u geen verificatie nodig hebt, zijn de stappen voor het instellen van alleen SSL-versleuteling:

1. Meld u aan bij de client computer (HN1) en navigeer naar de map `~/ssl`
1. Kopieer het ondertekende certificaat naar de client computer vanaf de CA-computer (wn0).
1. Importeer het CA-certificaat naar de truststore
1. Het CA-certificaat importeren in de-opslag

Deze stappen worden weer gegeven in het volgende code fragment.

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Ten slotte bekijkt u het bestand `client-ssl-auth.properties` met de opdracht `cat client-ssl-auth.properties`. Deze moet de volgende regels bevatten:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>Volgende stappen

* [Wat is Apache Kafka op HDInsight?](apache-kafka-introduction.md)
