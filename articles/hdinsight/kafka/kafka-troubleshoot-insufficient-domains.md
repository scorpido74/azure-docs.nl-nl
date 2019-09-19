---
title: Het maken van het cluster is mislukt vanwege onvoldoende fout domeinen in de regio in azure HDInsight
description: Het maken van het cluster is mislukt vanwege onvoldoende fout domeinen in de regio in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 1e0fae699752f5e2417a4a3cbd42f4f1d2bd891f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091248"
---
# <a name="scenario-cluster-creation-failed-due-to-not-sufficient-fault-domains-in-region-in-azure-hdinsight"></a>Scenario: Het maken van het `not sufficient fault domains in region` cluster is mislukt, omdat in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Er wordt een fout bericht `not sufficient fault domains in region` weer gegeven dat vergelijkbaar is met het maken van Apache Kafka cluster.

## <a name="cause"></a>Oorzaak

Een foutdomein is een logische groepering van de onderliggende hardware in een Azure-datacenter. Elk foutdomein deelt een algemene voedingsbron en netwerkswitch. De virtuele machines en beheerde schijven die de knooppunten in een HDInsight-cluster implementeren zijn verdeeld over deze foutdomeinen. Deze architectuur beperkt de potentiële impact van problemen met de fysieke hardware.

Elke Azure-regio heeft een bepaald aantal foutdomeinen. Raadpleeg de documentatie over [beschikbaarheids sets](../../virtual-machines/windows/manage-availability.md)voor een lijst met domeinen en het aantal fout domeinen dat ze bevatten.

In HDInsight moeten Kafka-clusters worden ingericht in een regio met ten minste drie fout domeinen.

## <a name="resolution"></a>Oplossing

Als de regio die u wilt maken het cluster niet voldoende fout domeinen heeft, neemt u contact op met het product team om de inrichting van het cluster toe te staan, zelfs als er niet drie fout domeinen zijn.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport) met-het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
