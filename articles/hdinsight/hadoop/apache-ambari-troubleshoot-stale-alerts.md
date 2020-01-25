---
title: Verlopen waarschuwingen voor Apache Ambari in azure HDInsight
description: Discussie en analyse van mogelijke oorzaken en oplossingen voor verlopen Apache Ambari-waarschuwingen in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f19d499b5e50fbb5030a0f396296eed46fc6eee3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722809"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Scenario: Apache Ambari verouderde waarschuwingen in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Vanuit de Apache Ambari-gebruikers interface wordt er mogelijk een waarschuwing weer gegeven die vergelijkbaar is met de volgende afbeelding:

![Voor beeld van een verlopen Ambari-waarschuwing voor Apache](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Oorzaak

Ambari agents voeren voortdurend status controles uit om de status van veel resources te controleren. Elke waarschuwing is geconfigureerd om te worden uitgevoerd op basis van vooraf gedefinieerde tijds intervallen. Na de uitvoering van elke waarschuwing rapporteert Ambari agents de status naar de Ambari-server. Op dit moment als Ambari-server detecteert dat een van de waarschuwingen niet tijdig wordt uitgevoerd, activeert het een ' Ambari server-waarschuwingen '. Er zijn verschillende redenen waarom een status controle niet kan worden uitgevoerd met het gedefinieerde interval:

* Wanneer hosts onder intensief gebruik (hoge CPU) worden gebruikt, is er mogelijk dat de Ambari-agent onvoldoende systeem bronnen krijgt om de waarschuwingen tijdig uit te voeren.

* Het cluster is bezig met het uitvoeren van veel taken/Services tijdens zware belasting.

* Op weinig hosts in het cluster kunnen veel onderdelen worden gehost en daarom moet er een groot aantal waarschuwingen worden uitgevoerd. Als het aantal onderdelen groot is, is het mogelijk dat waarschuwings taken hun geplande intervallen kunnen missen

## <a name="resolution"></a>Resolutie

### <a name="increase-alert-interval-time"></a>Waarschuwings interval duur verhogen

U kunt ervoor kiezen om de waarde van een afzonderlijk waarschuwings interval te verhogen op basis van de reactie tijd van het cluster en de belasting.

1. Selecteer in de Apache Ambari-gebruikers interface het tabblad **waarschuwingen** .
1. Selecteer de gewenste naam van de waarschuwings definitie.
1. Selecteer in de definitie **bewerken**.
1. Wijzig de waarde van het **controle-interval** naar wens en selecteer vervolgens **Opslaan**.

### <a name="increase-alert-interval-time-for-ambari-server-alerts"></a>Waarschuwings interval duur verhogen voor waarschuwingen van Ambari-server

1. Selecteer in de Apache Ambari-gebruikers interface het tabblad **waarschuwingen** .
1. Selecteer in de vervolg keuzelijst **groepen** de optie **AMBARI standaard**.
1. Selecteer alert **Ambari server-waarschuwingen**.
1. Selecteer in de definitie **bewerken**.
1. Wijzig de waarde van het **controle-interval** naar wens.
1. Wijzig de waarde van de **interval-multiplier** naar wens en selecteer vervolgens **Opslaan**.

### <a name="disable-and-enable-the-alert"></a>De waarschuwing uitschakelen en inschakelen

U kunt de waarschuwing uitschakelen en vervolgens weer inschakelen om eventuele verlopen waarschuwingen te verwijderen.

1. Selecteer in de Apache Ambari-gebruikers interface het tabblad **waarschuwingen** .
1. Selecteer de gewenste naam van de waarschuwings definitie.
1. Selecteer in de definitie nu **ingeschakeld** .
1. Selecteer in **het pop-upvenster** bevestigen de optie **uitschakelen**.
1. Wacht een paar seconden totdat alle waarschuwingen die op de pagina worden weer gegeven, zijn gewist.
1. Selecteer in de definitie ' **uitgeschakeld** ' aan de rechter kant.
1. Selecteer in **het pop-upvenster** bevestigen de optie **inschakelen**.

### <a name="increase-alert-grace-time"></a>Respijt tijd van waarschuwing verhogen

Voordat de Ambari-agent rapporteert dat een geconfigureerde waarschuwing het schema mist, wordt er een respijt tijd toegepast. Zelfs als de waarschuwing de geplande tijd niet heeft gemist, maar is geactiveerd binnen de respijt tijd van de waarschuwing, wordt de verouderde waarschuwing niet geactiveerd.

De standaard waarde voor `alert_grace_period` is 5 seconden. Deze `alert_grace_period` instelling kan worden geconfigureerd in `/etc/ambari-agent/conf/ambari-agent.ini`. Voor hosts waarvan de verouderde waarschuwingen met regel matige tussen pozen worden geactiveerd, kunt u een waarde van 10 verhogen. Start de Ambari-agent opnieuw op

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
