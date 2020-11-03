---
title: Apache Spark streaming-toepassing stopt na 24 dagen in azure HDInsight
description: Een Apache Spark streaming-toepassing stopt 24 dagen nadat deze is uitgevoerd en er zijn geen fouten in de logboek bestanden.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 83bdb4a9913ae817204fb37320f5bdb8174d5baf
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288035"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Scenario: Apache Spark streaming-toepassing stopt na het uitvoeren van 24 dagen in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Een Apache Spark streaming-toepassing stopt 24 dagen nadat deze is uitgevoerd en er zijn geen fouten in de logboek bestanden.

## <a name="cause"></a>Oorzaak

De `livy.server.session.timeout` waarde bepaalt hoelang Apache livy moet wachten voordat een sessie is voltooid. Zodra de sessie duur de `session.timeout` waarde bereikt, worden de livy-sessie en de toepassing automatisch afgebroken.

## <a name="resolution"></a>Oplossing

Voor langlopende taken verhoogt u de waarde van `livy.server.session.timeout` de Ambari-gebruikers interface. U kunt toegang krijgen tot de livy-configuratie via de Ambari-gebruikers interface met behulp van de URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` .

Vervang door `<yourclustername>` de naam van uw HDInsight-cluster zoals wordt weer gegeven in de portal.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]