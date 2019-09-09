---
title: HDInsight-cluster upgraden naar een nieuwere versie-Azure
description: Meer informatie over richt lijnen voor het bijwerken van uw Azure HDInsight-cluster naar een nieuwere versie.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: f4458b33edb9d55c30de8c5168668743abb33161
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811639"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>HDInsight-cluster upgraden naar een nieuwere versie
Als u gebruik wilt maken van de nieuwste HDInsight-functies, raden we aan dat HDInsight-clusters worden bijgewerkt naar de nieuwste versie. Volg de onderstaande richt lijnen om de versies van uw HDInsight-cluster bij te werken.

> [!NOTE]  
> Zie [HDInsight-onderdeel versies](hdinsight-component-versioning.md#supported-hdinsight-versions)voor meer informatie over ondersteunde versies van hdinsight.

## <a name="upgrade-tasks"></a>Upgrade taken
De werk stroom voor het upgraden van een HDInsight-cluster is als volgt.

![Werk stroom diagram bijwerken](./media/hdinsight-upgrade-cluster/upgrade-workflow.png)

1. Lees elke sectie van dit document voor meer informatie over wijzigingen die mogelijk vereist zijn bij het upgraden van uw HDInsight-cluster.
2. Maak een cluster als test-of kwaliteits borgings omgeving. Zie [informatie over het maken van HDInsight-clusters op basis van Linux](hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie over het maken van een cluster.
3. Kopieer bestaande taken, gegevens bronnen en sinks naar de nieuwe omgeving.
4. Voer validatie tests uit om ervoor te zorgen dat uw taken werken zoals verwacht op het nieuwe cluster.

Zodra u hebt gecontroleerd of alles werkt zoals verwacht, kunt u de downtime voor de migratie plannen. Voer tijdens deze downtime de volgende acties uit:

1.  Maak een back-up van tijdelijke gegevens die lokaal zijn opgeslagen op de cluster knooppunten. Bijvoorbeeld als u gegevens rechtstreeks op een hoofd knooppunt hebt opgeslagen.
2.  Verwijder het bestaande cluster.
3.  Maak een cluster in hetzelfde VNET-subnet met de nieuwste (of ondersteunde) HDI-versie met behulp van hetzelfde standaard gegevens archief dat het vorige cluster gebruikt. Hierdoor kan het nieuwe cluster blijven werken met uw bestaande productie gegevens.
4.  Importeer alle tijdelijke gegevens waarvan u een back-up hebt gemaakt.
5.  Start taken/Ga door met de verwerking met het nieuwe cluster.

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het maken van HDInsight-clusters op basis van Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Verbinding maken met HDInsight via SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Een op Linux gebaseerd cluster beheren met Apache Ambari](hdinsight-hadoop-manage-ambari.md)

