---
title: Het maken van een cluster mislukt met DomainNotFound-fout in azure HDInsight
description: Probleemoplossings stappen en mogelijke oplossingen voor problemen bij interactie met Azure HDInsight-clusters
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776233"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Scenario: het maken van een cluster mislukt met DomainNotFound-fout in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Het maken van een HDI Secure (Enterprise Security Package)-cluster mislukt met `DomainNotFound` fout bericht.

## <a name="cause"></a>Oorzaak

Onjuiste DNS-instellingen.

## <a name="resolution"></a>Resolutie

Wanneer de aan het domein gekoppelde clusters zijn geïmplementeerd, maakt HDI een interne gebruikers naam en wacht woord in AAD DS (voor elk cluster) en worden alle cluster knooppunten toegevoegd aan dit domein. De toevoeging aan het domein is voltooid met behulp van samba-hulpprogram ma's. Zorg ervoor dat aan de volgende vereisten wordt voldaan:

* De domein naam moet worden omgezet via DNS.
* Het IP-adres van de domein controllers moet worden ingesteld in de DNS-instellingen voor het virtuele netwerk waarin het cluster wordt geïmplementeerd.
* Als het virtuele netwerk is gekoppeld aan het virtuele netwerk van AAD DS, moet het hand matig worden uitgevoerd.
* Als u DNS-doorstuur servers gebruikt, moet de domein naam correct worden omgezet in het virtuele netwerk.
* Het beveiligings beleid (Nsg's) mag het lidmaatschap van het domein niet blok keren.

### <a name="additional-debugging-steps"></a>Aanvullende stappen voor fout opsporing

* Een Windows-VM implementeren in hetzelfde subnet, domein lid worden van de computer met een gebruikers naam en wacht woord (dit kan worden gedaan via de gebruikers interface van het configuratie scherm) of

* Een Ubuntu-VM implementeren in hetzelfde subnet en domein lid worden van de computer
  * SSH in de computer
  * sudo su
  * Voer het script uit met de gebruikers naam en het wacht woord
  * Het script wordt gepingd, de vereiste configuratie bestanden en vervolgens het domein gemaakt. Als de service slaagt, zijn uw DNS-instellingen goed.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
