---
title: Grafana gebruiken in azure HDInsight
description: Meer informatie over het openen van het Grafana-dash board met Apache Hadoop clusters in azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: cd515bfd1dc57e78a041ed96686e1ba692bf6d3f
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082860"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Toegang tot Grafana in azure HDInsight

[Grafana](https://grafana.com/) is een populaire, open-source grafiek en dash board Builder. Grafana is een uitgebreide functie; het is niet alleen mogelijk dat gebruikers aanpas bare en delende Dash boards kunnen maken, maar biedt ook sjablonen/script Dash boards, LDAP-integratie, meerdere gegevens bronnen en nog veel meer.

Op dit moment wordt in azure HDInsight Grafana ondersteund met de cluster typen Spark, HBase, Kafka en Interactive query. Het wordt niet ondersteund voor clusters waarvoor Enter prise Security Pack is ingeschakeld.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-an-apache-hadoop-cluster"></a>Een Apache Hadoop-cluster maken

Zie [Apache Hadoop clusters maken met behulp van de Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md). Selecteer voor **cluster type** **Spark**, **Kafka**, **HBase**of **interactieve query**.

## <a name="access-the-grafana-dashboard"></a>Het Grafana-dash board openen

1. Ga in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net/grafana/` waarbij CLUSTERNAME de naam van uw cluster is.

1. Voer de gebruikers referenties voor het Hadoop-cluster in.

1. Het dash board Grafana wordt weer gegeven en ziet eruit als in dit voor beeld:

    ![HDInsight Grafana Web-dash board](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "HDInsight Grafana-dash board")

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet wilt blijven gebruiken, verwijdert u het cluster dat u hebt gemaakt met de volgende stappen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Typ **HDInsight** in het **Zoekvak** bovenaan.

1. Selecteer onder **Services** de optie **HDInsight-clusters**.

1. Selecteer in de lijst met HDInsight-clusters die wordt weer gegeven, de **...** naast het cluster dat u hebt gemaakt.

1. Selecteer **Verwijderen**. Selecteer **Ja**.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het analyseren van gegevens met HDInsight:

* [Gebruik Apache Hive met HDInsight](../hadoop/hdinsight-use-hive.md).

* [Gebruik MapReduce met HDInsight](../hadoop/hdinsight-use-mapreduce.md).

* [Aan de slag met Visual Studio Hadoop-hulpprogram ma's voor HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
