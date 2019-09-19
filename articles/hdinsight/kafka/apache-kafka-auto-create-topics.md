---
title: Automatisch maken van een onderwerp in Apache Kafka-Azure HDInsight inschakelen
description: Meer informatie over het configureren van Apache Kafka op HDInsight om automatisch onderwerpen te maken. U kunt Kafka configureren door auto. Create. topics in te stellen op True via Ambari of tijdens het maken van clusters via Power shell of Resource Manager-sjablonen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/18/2018
ms.openlocfilehash: 393087f4d5c5e7a52fd2dd10d20362a045a0075b
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122671"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Apache Kafka op HDInsight configureren om automatisch onderwerpen te maken

Standaard wordt het automatisch maken van een onderwerp niet ingeschakeld door [Apache Kafka](https://kafka.apache.org/) op HDInsight. U kunt het automatisch maken van een onderwerp voor bestaande clusters inschakelen met [Apache Ambari](https://ambari.apache.org/). U kunt ook automatisch onderwerpen maken inschakelen wanneer u een nieuw Kafka-cluster maakt met behulp van een Azure Resource Manager sjabloon.

## <a name="apache-ambari-web-ui"></a>Apache Ambari-webgebruikersinterface

Gebruik de volgende stappen om het automatisch maken van een onderwerp in een bestaand cluster via de Ambari-webgebruikersinterface in te scha kelen:

1. Selecteer in de [Azure Portal](https://portal.azure.com)het Kafka-cluster.

2. Selecteer __cluster dashboard__in het __cluster overzicht__.

    ![Afbeelding van de portal waarop het cluster dashboard is geselecteerd](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Selecteer vervolgens het __HDInsight-cluster dashboard__. Verifieer, wanneer u hierom wordt gevraagd, met behulp van de aanmeldings referenties (admin) voor het cluster.

    ![Afbeelding van het dashboard item van het HDInsight-cluster](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Selecteer de Kafka-service in de lijst aan de linkerkant van de pagina.

    ![Tabblad Apache Ambari service List](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

4. Selecteer configuraties in het midden van de pagina.

    ![Tabblad Apache Ambari service-configuratie](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

5. Voer in het veld Filter een waarde van `auto.create`in.

    ![Veld Apache Ambari search filter](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Hiermee wordt de lijst met eigenschappen gefilterd `auto.create.topics.enable` en wordt de instelling weer gegeven.

6. Wijzig de waarde van `auto.create.topics.enable` in `true`en selecteer vervolgens opslaan. Voeg een notitie toe en selecteer vervolgens opslaan opnieuw.

    ![Afbeelding van het bestand auto. Create. topics. enable entry](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Selecteer de Kafka-service, selecteer __opnieuw opstarten__en selecteer vervolgens __alle betrokkenen opnieuw opstarten__. Selecteer __Bevestig opnieuw opstarten__als dit wordt gevraagd.

    ![Apache Ambari alle betrokken software opnieuw opstarten](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> U kunt Ambari-waarden ook instellen via de Ambari-REST API. Dit is over het algemeen moeilijker, omdat u meerdere REST-aanroepen moet maken om de huidige configuratie op te halen, deze te wijzigen, enzovoort. Zie [HDInsight-clusters beheren met het Apache Ambari rest API](../hdinsight-hadoop-manage-ambari-rest-api.md) -document voor meer informatie.

## <a name="resource-manager-templates"></a>Resource Manager-sjablonen

Wanneer u een Kafka-cluster maakt met behulp van een Azure Resource Manager- `auto.create.topics.enable` sjabloon, kunt u dit `kafka-broker`rechtstreeks instellen door het te voegen in een. In het volgende JSON-code fragment ziet u hoe u deze `true`waarde instelt op:

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u het automatisch maken van een onderwerp voor Apache Kafka op HDInsight inschakelt. Raadpleeg de volgende koppelingen voor meer informatie over het werken met Kafka:

* [Apache Kafka-logboeken analyseren](apache-kafka-log-analytics-operations-management.md)
* [Gegevens repliceren tussen Apache Kafka-clusters](apache-kafka-mirroring.md)
