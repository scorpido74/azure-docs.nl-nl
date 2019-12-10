---
title: HDInsight-cluster upgraden naar een nieuwere versie-Azure
description: Meer informatie over richt lijnen voor het bijwerken van uw Azure HDInsight-cluster naar een nieuwere versie.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 1a1d4a71786ebb1e68f59084086b3256a1c1ea40
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951153"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>HDInsight-cluster upgraden naar een nieuwere versie

Als u gebruik wilt maken van de nieuwste HDInsight-functies, raden we aan dat HDInsight-clusters worden bijgewerkt naar de nieuwste versie. Volg de onderstaande richt lijnen om de versies van uw HDInsight-cluster bij te werken.

> [!NOTE]  
> Zie [HDInsight-onderdeel versies](hdinsight-component-versioning.md#supported-hdinsight-versions)voor meer informatie over ondersteunde versies van hdinsight.

## <a name="upgrade-tasks"></a>Upgrade taken

De werk stroom voor het upgraden van een HDInsight-cluster is als volgt.
![werk stroom diagram voor HDInsight-upgrade](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Lees elke sectie van dit document voor meer informatie over wijzigingen die mogelijk vereist zijn bij het upgraden van uw HDInsight-cluster.
2. Maak een cluster als test-of kwaliteits borgings omgeving. Zie [informatie over het maken van HDInsight-clusters op basis van Linux](hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie over het maken van een cluster.
3. Kopieer bestaande taken, gegevens bronnen en sinks naar de nieuwe omgeving.
4. Voer validatie tests uit om ervoor te zorgen dat uw taken werken zoals verwacht op het nieuwe cluster.

Zodra u hebt gecontroleerd of alles werkt zoals verwacht, kunt u de downtime voor de migratie plannen. Voer tijdens deze downtime de volgende acties uit:

1. Maak een back-up van tijdelijke gegevens die lokaal zijn opgeslagen op de cluster knooppunten. Bijvoorbeeld als u gegevens rechtstreeks op een hoofd knooppunt hebt opgeslagen.
1. [Verwijder het bestaande cluster](./hdinsight-delete-cluster.md).
1. Maak een cluster in hetzelfde VNET-subnet met de nieuwste (of ondersteunde) HDI-versie met behulp van hetzelfde standaard gegevens archief dat het vorige cluster gebruikt. Hierdoor kan het nieuwe cluster blijven werken met uw bestaande productie gegevens.
1. Importeer alle tijdelijke gegevens waarvan u een back-up hebt gemaakt.
1. Start taken/Ga door met de verwerking met het nieuwe cluster.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het maken van HDInsight-clusters op basis van Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Verbinding maken met HDInsight via SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Een op Linux gebaseerd cluster beheren met Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Opmerkingen bij de release van HDInsight](./hdinsight-version-release.md)
