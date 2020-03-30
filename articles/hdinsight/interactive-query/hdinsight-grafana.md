---
title: Grafana gebruiken op Azure HDInsight
description: Meer informatie over hoe u toegang krijgt tot het Grafana-dashboard met Apache Hadoop-clusters in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: cd515bfd1dc57e78a041ed96686e1ba692bf6d3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082860"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Toegang tot Grafana in Azure HDInsight

[Grafana](https://grafana.com/) is een populaire, open-source grafiek en dashboardbouwer. Grafana is rijk aan functies; niet alleen laat het gebruikers aanpasbare en deelbare dashboards maken, het biedt ook sjabloon/ gescripte dashboards, LDAP-integratie, meerdere gegevensbronnen en meer.

Momenteel wordt Grafana in Azure HDInsight ondersteund met de clustertypen Spark, HBase, Kafka en Interactive Query. Het wordt niet ondersteund voor clusters met Enterprise Security Pack ingeschakeld.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-an-apache-hadoop-cluster"></a>Een Apache Hadoop-cluster maken

Zie [Apache Hadoop-clusters maken met behulp van de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md). Selecteer **Voor clustertype** **Spark**, **Kafka**, **HBase**of **Interactieve query**.

## <a name="access-the-grafana-dashboard"></a>Toegang tot het Grafana-dashboard

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net/grafana/` naar de plaats waar CLUSTERNAME de naam van uw cluster is.

1. Voer de gebruikersreferenties van het Hadoop-cluster in.

1. Het Grafana-dashboard wordt weergegeven en ziet er als volgt uit:

    ![HDInsight Grafana-webdashboard](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "HDInsight Grafana-dashboard")

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet meer wilt gebruiken, verwijdert u het cluster dat u met de volgende stappen hebt gemaakt:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Typ **HDInsight** in het **Zoekvak** bovenaan.

1. Selecteer onder **Services** de optie **HDInsight-clusters**.

1. Selecteer in de lijst met HDInsight-clusters die worden weergegeven de **...** naast het cluster dat u hebt gemaakt.

1. Selecteer **Verwijderen**. Selecteer **Ja**.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het analyseren van gegevens met HDInsight:

* [Gebruik Apache Hive met HDInsight](../hadoop/hdinsight-use-hive.md).

* [Mapreduce gebruiken met HDInsight](../hadoop/hdinsight-use-mapreduce.md).

* [Aan de slag met Visual Studio Hadoop-tools voor HDInsight.](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
