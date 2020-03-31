---
title: Apache Ambari verouderde waarschuwingen in Azure HDInsight
description: Discussie en analyse van mogelijke redenen en oplossingen voor Apache Ambari verouderde waarschuwingen in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f9dfcb930e3fe4f862f9f51ff00270d0eb0c66ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539107"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Scenario: Apache Ambari verouderde waarschuwingen in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

In de Apache Ambari-gebruikersinterface ziet u mogelijk een waarschuwing als volgt:

![Voorbeeld van Apache Ambari-verouderde waarschuwing](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Oorzaak

Ambari agenten voortdurend toezicht op de gezondheid van vele middelen. *Waarschuwingen* kunnen worden geconfigureerd om u te laten weten of specifieke clustereigenschappen zich binnen vooraf bepaalde drempelwaarden bevinden. Nadat elke resourcecontrole is uitgevoerd, als aan de waarschuwingsvoorwaarde is voldaan, rapporteren Ambari-agents de status terug naar de Ambari-server en activeren ze een waarschuwing. Als een waarschuwing niet is gecontroleerd op basis van het interval in het waarschuwingsprofiel, activeert de server een waarschuwing *voor het verouderde waarschuwingen van Ambari Server Tale.*

Er zijn verschillende redenen waarom een statuscontrole mogelijk niet wordt uitgevoerd met het gedefinieerde interval:

* De hosts worden intensief gebruikt (hoog CPU-gebruik), zodat de Ambari-agent niet genoeg systeembronnen kan krijgen om de waarschuwingen op tijd uit te voeren.

* Het cluster is bezig met het uitvoeren van veel taken of services tijdens een periode van zware belasting.

* Een klein aantal hosts in het cluster zijn hosting veel componenten en dus zijn nodig om veel waarschuwingen uit te voeren. Als het aantal onderdelen groot is, kunnen waarschuwingstaken hun geplande intervallen missen.

## <a name="resolution"></a>Oplossing

Probeer de volgende methoden om problemen met ambari verouderde waarschuwingen op te lossen.

### <a name="increase-the-alert-interval-time"></a>De waarschuwingsintervaltijd verhogen

U de waarde van een afzonderlijk waarschuwingsinterval verhogen op basis van de reactietijd en belasting van uw cluster:

1. Selecteer op het tabblad Apache Ambari het tabblad **Waarschuwingen.**
1. Selecteer de gewenste naam van de waarschuwingsdefinitie.
1. Selecteer bewerken in de definitie **.**
1. Verhoog de waarde **Interval controleren** en selecteer **Opslaan**.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>De waarschuwingsintervaltijd voor Ambari-serverwaarschuwingen verhogen

1. Selecteer op het tabblad Apache Ambari het tabblad **Waarschuwingen.**
1. Selecteer IN de vervolgkeuzelijst **Groepen** de optie **AMBARI Default**.
1. Selecteer de waarschuwing **ambariserverwaarschuwingen.**
1. Selecteer bewerken in de definitie **.**
1. Verhoog de waarde **Controle-interval.**
1. Verhoog de waarde **Intervalmultiplier** en selecteer **Opslaan**.

### <a name="disable-and-reenable-the-alert"></a>De waarschuwing uitschakelen en opnieuw inschakelen

Als u een verouderde waarschuwing wilt verwijderen, schakelt u deze uit en schakelt u deze vervolgens opnieuw in:

1. Selecteer op het tabblad Apache Ambari het tabblad **Waarschuwingen.**
1. Selecteer de gewenste naam van de waarschuwingsdefinitie.
1. Selecteer in de definitie **Ingeschakeld** in het uiterst rechtse deel van de gebruikersinterface.
1. Selecteer **bevestigen** **disable**. in het pop-upvenster Bevestiging .
1. Wacht een paar seconden tot alle waarschuwingswaarschuwingen op de pagina worden gewist.
1. Selecteer **uitgeschakeld** in het uiterst rechtse gedeelte van de gebruikersinterface in de definitie.
1. Selecteer **Bevestigen inschakelen**in het pop-upvenster **Bevestiging** .

### <a name="increase-the-alert-grace-period"></a>De waarschuwingsrespijtperiode verlengen

Er is een respijtperiode voordat een Ambari-agent meldt dat een geconfigureerde waarschuwing het schema heeft gemist. Als de waarschuwing de geplande tijd heeft gemist, maar binnen de respijtperiode is uitgevoerd, wordt de verouderde waarschuwing niet gegenereerd.

De `alert_grace_period` standaardwaarde is 5 seconden. U deze instelling configureren in /etc/ambari-agent/conf/ambari-agent.ini. Voor hosts waarop verouderde waarschuwingen regelmatig voorkomen, probeert u de waarde te verhogen tot 10. Start vervolgens de Ambari-agent opnieuw op.

## <a name="next-steps"></a>Volgende stappen

Als uw probleem hier niet is vermeld of als u het niet oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts bij [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) contact met op Twitter. Dit is het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. Het verbindt de Azure-community met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, dient u een ondersteuningsaanvraag in van de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer Help (**?**) in het portalmenu of open het **ondersteuningsvenster Help + ondersteuning** om daar te komen. Zie [Een Azure-ondersteuningsaanvraag maken](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)voor meer informatie. 

  Ondersteuning voor abonnementsbeheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement. Technische ondersteuning is beschikbaar via de [Azure Support Plans](https://azure.microsoft.com/support/plans/).
