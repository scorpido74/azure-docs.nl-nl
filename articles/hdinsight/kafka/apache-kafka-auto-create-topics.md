---
title: Automatische onderwerpcreatie inschakelen in Apache Kafka - Azure HDInsight
description: Meer informatie over het configureren van Apache Kafka op HDInsight om automatisch onderwerpen te maken. U Kafka configureren door auto.create.topics.enable in te stellen om true te doen via Ambari of tijdens het maken van het cluster via PowerShell- of Resource Manager-sjablonen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 7ec7d15806927306b12624962facbafddf2ce08b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242358"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Apache Kafka configureren op HDInsight om automatisch onderwerpen te maken

[Apache Kafka](https://kafka.apache.org/) op HDInsight maakt standaard geen automatische onderwerpcreatie mogelijk. U het maken van automatische onderwerpen voor bestaande clusters inschakelen met [Apache Ambari](https://ambari.apache.org/). U ook het maken van automatische onderwerpen inschakelen bij het maken van een nieuw Kafka-cluster met behulp van een Azure Resource Manager-sjabloon.

## <a name="apache-ambari-web-ui"></a>Apache Ambari Web UI

Als u automatische onderwerpcreatie op een bestaand cluster wilt inschakelen via de Ambari Web UI, gebruikt u de volgende stappen:

1. Selecteer in de [Azure-portal](https://portal.azure.com)uw Kafka-cluster.

1. Selecteer **Ambari home**in **clusterdashboards**.

    ![Afbeelding van de portal met het gekozen clusterdashboard](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    Wanneer u daarom wordt gevraagd, verifieert u de aanmeldingsgegevens (beheerders) voor het cluster. U ook rechtstreeks verbinding maken `https://CLUSTERNAME.azurehdinsight.net/` met `CLUSTERNAME` Amabri van waar de naam van uw Kafka-cluster is.

1. Selecteer de Kafka-service in de lijst links van de pagina.

    ![Tabblad Lijst met Apache Ambari-service](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. Selecteer Configs in het midden van de pagina.

    ![Tabblad Apache Ambari-serviceconfigs](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. Voer in het veld Filter `auto.create`een waarde in van .

    ![Zoekfilterveld Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Hiermee filtert u de `auto.create.topics.enable` lijst met eigenschappen en wordt de instelling weergegeven.

1. Wijzig de `auto.create.topics.enable` waarde `true`van in , en selecteer **Opslaan**. Voeg een notitie toe en selecteer opnieuw **opslaan.**

    ![Afbeelding van de auto.create.topics.enable entry](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Selecteer de Kafka-service, selecteer __Opnieuw starten__en selecteer __Alle betrokkenen opnieuw starten__. Wanneer u daarom wordt gevraagd, selecteert __u Alles opnieuw starten bevestigen__.

    ![Apache Ambari herstart alle getroffenen](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> U ook Ambari-waarden instellen via de Ambari REST API. Dit is over het algemeen moeilijker, omdat je meerdere REST-oproepen moet voeren om de huidige configuratie op te halen, te wijzigen, enz. Zie de clusters [MANAGE HDInsight met behulp van het Apache Ambari REST API-document](../hdinsight-hadoop-manage-ambari-rest-api.md) voor meer informatie.

## <a name="resource-manager-templates"></a>Resource Manager-sjablonen

Wanneer u een Kafka-cluster maakt met een `auto.create.topics.enable` Azure Resource Manager-sjabloon, u deze rechtstreeks instellen door het toe te voegen in een `kafka-broker`. In het volgende JSON-fragment wordt `true`uitgelegd hoe u deze waarde instelt op:

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

In dit document hebt u geleerd hoe u automatische onderwerpcreatie voor Apache Kafka op HDInsight inschakelen. Zie de volgende links voor meer informatie over het werken met Kafka:

* [Apache Kafka-logboeken analyseren](apache-kafka-log-analytics-operations-management.md)
* [Gegevens repliceren tussen Apache Kafka-clusters](apache-kafka-mirroring.md)
