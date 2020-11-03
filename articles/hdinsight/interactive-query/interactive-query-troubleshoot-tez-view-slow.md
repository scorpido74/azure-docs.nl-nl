---
title: De Ambari TEZ-weer gave van Apache wordt langzaam in azure HDInsight geladen
description: De Apache Ambari TEZ-weer gave kan langzaam worden geladen of helemaal niet worden geladen in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 0e294566da4c6f514704abc2ac014b8345020b5a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288848"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Scenario: de Ambari-weer gave van Apache TEZ wordt langzaam in azure HDInsight geladen

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van interactieve query onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De Apache Ambari TEZ-weer gave kan langzaam worden geladen of helemaal niet worden geladen. Wanneer u de weer gave Ambari TEZ laadt, ziet u mogelijk processen op hoofd knooppunten niet meer reageert.

## <a name="cause"></a>Oorzaak

Het openen van garen-Api's van coonderdelen kan soms slechte prestaties hebben voor clusters die zijn gemaakt vóór okt 2017 als het cluster een groot aantal Hive-taken uitvoert.

## <a name="resolution"></a>Oplossing

Dit is een probleem dat is opgelost in OCT 2017. Als u het cluster opnieuw maakt, wordt het niet opnieuw uitgevoerd in dit probleem.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]