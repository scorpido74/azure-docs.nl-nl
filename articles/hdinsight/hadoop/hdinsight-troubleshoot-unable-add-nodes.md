---
title: Kan geen knoop punten toevoegen aan een Azure HDInsight-cluster
description: Problemen oplossen waarom kan geen knoop punten toevoegen aan Apache Hadoop cluster in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 089eab563c83384bdb7e1681044fa89f5eb7f6c0
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289047"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Scenario: kan geen knoop punten toevoegen aan een Azure HDInsight-cluster

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Kan geen knoop punten toevoegen aan een Azure HDInsight-cluster.

## <a name="cause"></a>Oorzaak

Redenen kunnen variëren.

## <a name="resolution"></a>Oplossing

Gebruik de functie [cluster grootte](../hdinsight-scaling-best-practices.md) om het aantal extra kernen te berekenen dat nodig is voor het cluster. Dit wordt gebaseerd op het totale aantal cores in de nieuwe werkrolknooppunten. Probeer vervolgens een of meer van de volgende stappen:

* Controleer of er kernen beschikbaar zijn op de locatie van het cluster.

* Bekijk het aantal beschikbare cores op andere locaties. Overweeg het cluster opnieuw te maken op een andere locatie met voldoende beschikbare cores.

* Als u het aantal cores voor een specifieke locatie wilt verhogen, maakt u een ondersteuningsticket aan voor verhoging van het aantal HDInsight-cores.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]