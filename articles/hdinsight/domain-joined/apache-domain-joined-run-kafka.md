---
title: Zelfstudie - Apache Kafka & Enterprise Security - Azure HDInsight
description: Zelfstudie - Lees hoe u Apache Ranger-beleidsregels voor Kafka configureert in Azure HDInsight met enterprise security package.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: 58c5b3bdd6d50f2e512cccffe78bd4e70805d729
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78204732"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Zelfstudie: Apache Kafka-beleidsregels configureren in HDInsight met Enterprise Security Package (preview)

Leer hoe u Apache Ranger-beleidsregels configureert voor Apache Kafka-clusters met Enterprise Security Package (ESP). ESP-clusters worden verbonden met een domein zodat gebruikers zich kunnen verifiëren met domeinreferenties. In deze zelfstudie maakt u twee Ranger-beleidsregels om de toegang tot de onderwerpen `sales` en `marketingspend` te beperken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Domeingebruikers maken
> * Ranger-beleidsregels maken
> * Onderwerpen in een Kafka-cluster maken
> * Ranger-beleidsregels testen

## <a name="prerequisite"></a>Vereiste

Een [HDInsight Kafka-cluster met Enterprise Security Package](./apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Verbinding maken met de beheerinterface van Apache Ranger

1. Maak vanuit een browser verbinding met de beheerinterface van Ranger met behulp van de URL `https://ClusterName.azurehdinsight.net/Ranger/`. Vergeet niet om `ClusterName` te wijzigen in de naam van uw Kafka-cluster. Ranger-referenties zijn niet hetzelfde als referenties van een Hadoop-cluster. Gebruik een nieuw InPrivate-browservenster om verbinding te maken met de beheerinterface van Ranger om te voorkomen dat browsers Hadoop-referenties in de cache gebruiken.

2. Meld u aan met uw beheerdersreferenties voor Azure Active Directory (AD). Deze referenties zijn niet hetzelfde als de referenties voor het HDInsight-cluster of de SSH-referenties voor het Linux HDInsight knooppunt.

   ![HDInsight Apache Ranger Admin UI](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Domeingebruikers maken

Ga naar [Een HDInsight-cluster maken met Enterprise Security Package](./apache-domain-joined-configure-using-azure-adds.md) voor informatie over het maken van de domeingebruikers **sales_user** en **marketing_user**. In een productiescenario zijn de domeingebruikers afkomstig uit uw Active Directory-tenant.

## <a name="create-ranger-policy"></a>Ranger-beleid maken

Maak een Ranger-beleid voor **sales_user** en **marketing_user**.

1. Open de **beheerinterface van Ranger**.

2. Selecteer ** \<ClusterNaam->_kafka** onder **Kafka**. Er kan één vooraf geconfigureerd beleid worden weergegeven.

3. Selecteer **Nieuw beleid toevoegen** en voer de volgende waarden in:

   |Instelling  |Voorgestelde waarde  |
   |---------|---------|
   |Policy Name  |  hdi sales* policy   |
   |Onderwerp   |  sales* |
   |Select User  |  sales_user1 |
   |Machtigingen  | Publish, Consume, Create |

   U kunt de volgende jokertekens gebruiken in de onderwerpnaam:

   * Gebruik '*' om nul of meer exemplaren van tekens aan te geven.
   * Gebruik '?' om één teken aan te geven.

   ![Apache Ranger Admin UI Beleid maken1](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)

   Als er niet automatisch een domeingebruiker wordt ingevuld bij **Select User**, wacht u even totdat Ranger is gesynchroniseerd met AAD.

4. Selecteer **Toevoegen** om het beleid op te slaan.

5. Selecteer **Nieuw beleid toevoegen** en voer de volgende waarden in:

   |Instelling  |Voorgestelde waarde  |
   |---------|---------|
   |Policy Name  |  hdi marketing policy   |
   |Onderwerp   |  marketingspend |
   |Select User  |  marketing_user1 |
   |Machtigingen  | Publish, Consume, Create |

   ![Apache Ranger Admin UI Beleid maken2](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Selecteer **Toevoegen** om het beleid op te slaan.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Onderwerpen maken in een Kafka-cluster met ESP

Twee onderwerpen, `salesevents` en `marketingspend`, maken:

1. Gebruik de volgende opdracht om een SSH-verbinding met het cluster op te zetten:

   ```cmd
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Vervang `DOMAINADMIN` door de beheerder van het cluster dat u hebt geconfigureerd tijdens het [maken van het cluster](./apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp) en vervang `CLUSTERNAME` door de naam van uw cluster. Voer het wachtwoord voor het beheerdersaccount in wanneer hierom wordt gevraagd. Zie [SSH gebruiken met HDInsight](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie over het gebruik van `SSH` met HDInsight.

2. Gebruik de volgende opdrachten om de clusternaam op te slaan in een variabele en een hulpprogramma voor het parseren van JSON (`jq`) te installeren. Wanneer u daarom wordt gevraagd, geeft u de naam van het Kafka-cluster op.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Gebruik de volgende opdrachten om de Kafka-brokerhosts op te vragen. Geef desgevraagd het wachtwoord voor het beheerdersaccounts voor het cluster op.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   Voordat u verder gaat, moet u mogelijk uw ontwikkelomgeving instellen als u dit nog niet hebt gedaan. Je hebt componenten nodig zoals de Java JDK, Apache Maven en een SSH-client met scp. Zie [installatie-instructies](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer)voor meer informatie .

1. Download de [voorbeelden voor aan een domein gekoppelde Apache Kafka voor producer/consumer](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Volg stap 2 en 3 onder **Het voorbeeld compileren en implementeren** in [Zelfstudie: Werken met de Producer- en Consumer-API's van Apache Kafka](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example)

1. Voer de volgende opdrachten uit:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Ranger-beleidsregels testen

Op basis van de geconfigureerde Ranger-beleidsregels kan **sales_user** het onderwerp `salesevents` produceren/verbruiken, maar niet het onderwerp `marketingspend`. Omgekeerd kan **marketing_user** het onderwerp `marketingspend` produceren/verbruiken, maar niet het onderwerp `salesevents`.

1. Open een nieuwe SSH-verbinding met het cluster. Gebruik de volgende opdracht om u als **sales_user1** aan te melden:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Voer de volgende opdracht uit:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Gebruik de brokernamen uit de vorige sectie om de volgende omgevingsvariabele in te stellen:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Voorbeeld: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. Volg stap 3 onder **Bouwen en implementeer het voorbeeld** in [Zelfstudie: Gebruik de Apache Kafka-producenten en consumenten-API's](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example) om ervoor te zorgen dat deze `kafka-producer-consumer.jar` ook beschikbaar zijn voor **sales_user**.

**Opmerking: Gebruik voor deze zelfstudie de kafka-producer-consumer.jar onder het project "DomainJoined-Producer-Consumer" (niet het project 'DomainJoined-Producer-Consumer', dat is voor niet-domeingebonden scenario's).**

5. Controleer of **sales_user1** het onderwerp `salesevents` kan produceren. Voer hiervoor de volgende opdracht uit:

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. Voer de volgende opdracht uit om gebeurtenissen uit het onderwerp `salesevents` te verbruiken:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Controleer of u de berichten kunt lezen.

7. Controleer of **sales_user1** het onderwerp `marketingspend` niet kan produceren. Voer hiervoor het volgende uit in hetzelfde SSH-venster:

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Er treedt een autorisatiefout op, maar die kunt u negeren.

8. U ziet dat **marketing_user1** geen gebeurtenissen van het onderwerp `salesevents` kan verbruiken.

   Herhaal stappen 1-4 hierboven, maar dit keer als **marketing_user1**.

   Voer de volgende opdracht uit om gebeurtenissen uit het onderwerp `salesevents` te verbruiken:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Eerdere berichten kunnen niet worden weergegeven.

9. Bekijk de controlegebeurtenissen voor toegang vanuit de gebruikersinterface van Ranger.

   ![Toegangsgebeurtenissen voor de controle van ranger-gebruikersinterface-beleid ](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet meer wilt gebruiken, verwijdert u het Kafka-cluster dat u met de volgende stappen hebt gemaakt:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Typ **HDInsight** in het **Zoekvak** bovenaan.
1. Selecteer onder **Services** de optie **HDInsight-clusters**.
1. Klik in de lijst met HDInsight-clusters die wordt weergegeven, op de **...** naast het cluster dat u voor deze zelfstudie hebt gemaakt. 
1. Klik **op Verwijderen**. Klik **op Ja**.

## <a name="troubleshooting"></a>Problemen oplossen
Als kafka-producer-consumer.jar niet werkt in een domein samengevoegd cluster, zorg er dan voor dat u de kafka-producer-consumer.jar gebruikt onder "DomainJoined-Producer-Consumer" project (niet het project onder Producer-Consumer, dat is voor niet-domein geword-scenario's).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Schijfversleuteling met behulp van door klant beheerde sleutel](../disk-encryption.md)
