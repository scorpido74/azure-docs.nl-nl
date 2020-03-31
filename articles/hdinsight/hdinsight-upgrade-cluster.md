---
title: Cluster migreren naar een nieuwere versie
titleSuffix: Azure HDInsight
description: Meer informatie over richtlijnen om uw Azure HDInsight-cluster te migreren naar een nieuwere versie.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: f7198aeff5e9ef6d37e29c2336dc38e4eec0dda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023970"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>HDInsight-cluster migreren naar een nieuwere versie

Om te profiteren van de nieuwste HDInsight-functies, raden we aan om HDInsight-clusters regelmatig te migreren naar de nieuwste versie. HDInsight biedt geen ondersteuning voor in-place upgrades waarbij een bestaand cluster wordt geüpgraded naar een nieuwere componentversie. U moet een nieuw cluster maken met de gewenste component- en platformversie en vervolgens uw toepassingen migreren om het nieuwe cluster te gebruiken. Volg de onderstaande richtlijnen om uw HDInsight-clusterversies te migreren.

> [!NOTE]  
> Zie [HDInsight-componentversies](hdinsight-component-versioning.md#supported-hdinsight-versions)voor informatie over ondersteunde versies van HDInsight.

## <a name="migration-tasks"></a>Migratietaken

De workflow voor het upgraden van HDInsight Cluster is als volgt.
![HDInsight-upgradewerkstroomdiagram](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Lees elk gedeelte van dit document om inzicht te krijgen in wijzigingen die nodig kunnen zijn bij het upgraden van uw HDInsight-cluster.
2. Maak een cluster als test-/kwaliteitsborgingsomgeving. Zie Meer informatie over het maken van een cluster voor meer informatie over het [maken van hdinsight-clusters op basis van Linux](hdinsight-hadoop-provision-linux-clusters.md)
3. Kopieer bestaande taken, gegevensbronnen en sinks naar de nieuwe omgeving.
4. Voer validatietests uit om ervoor te zorgen dat uw taken werken zoals verwacht in het nieuwe cluster.

Zodra u hebt geverifieerd dat alles werkt zoals verwacht, plant u downtime voor de migratie. Ga tijdens deze downtime de volgende acties uitvoeren:

1. Een back-up maken van tijdelijke gegevens die lokaal zijn opgeslagen op de clusterknooppunten. Als u bijvoorbeeld gegevens direct op een hoofdknooppunt hebt opgeslagen.
1. [Het bestaande cluster verwijderen](./hdinsight-delete-cluster.md).
1. Maak een cluster in hetzelfde VNET-subnet met de nieuwste (of ondersteunde) HDI-versie met dezelfde standaardgegevensarchief als het vorige cluster. Hierdoor kan het nieuwe cluster blijven werken tegen uw bestaande productiegegevens.
1. Importeer tijdelijke gegevens waaru een back-up van maakt.
1. Taken starten/verder verwerken met het nieuwe cluster.

## <a name="workload-specific-guidance"></a>Workloadspecifieke richtlijnen

De volgende documenten geven richtlijnen voor het migreren van specifieke workloads:

* [HBase migreren](./hbase/apache-hbase-migrate-new-version.md)
* [Kafka migreren](./kafka/migrate-versions.md)
* [Hive/interactieve query migreren](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>Back-ups en herstellen

Zie [Een Azure SQL-database herstellen met behulp van geautomatiseerde databaseback-ups](../sql-database/sql-database-recovery-using-backups.md)voor meer informatie over databaseback-ups en herstel.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het maken van HDInsight-clusters op basis van Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Verbinding maken met HDInsight via SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Een op Linux gebaseerd cluster beheren met Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [HDInsight release notes HDInsight release notes HDInsight release notes HDInsight](./hdinsight-version-release.md)
