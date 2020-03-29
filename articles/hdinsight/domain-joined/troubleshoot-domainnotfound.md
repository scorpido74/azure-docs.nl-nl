---
title: Clustercreatie mislukt met DomainNotFound-fout in Azure HDInsight
description: Problemen oplossen met stappen en mogelijke oplossingen voor problemen bij interactie met Azure HDInsight-clusters
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776233"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Scenario: Clustercreatie mislukt met DomainNotFound-fout in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Het maken van hdi secure (Enterprise Security Package) clustercreatie mislukt met `DomainNotFound` een foutmelding.

## <a name="cause"></a>Oorzaak

Onjuiste DNS-instellingen.

## <a name="resolution"></a>Oplossing

Wanneer de samengevoegde clusters van het domein worden geïmplementeerd, maakt HDI een interne gebruikersnaam en wachtwoord in AAD DS (voor elk cluster) en voegt alle clusterknooppunten samen met dit domein. De domeinjoin wordt uitgevoerd met sambatools. Zorg ervoor dat aan de volgende voorwaarden wordt voldaan:

* De domeinnaam moet worden opgelost via DNS.
* Het IP-adres van de domeincontrollers moet worden ingesteld in de DNS-instellingen voor het virtuele netwerk waar het cluster wordt geïmplementeerd.
* Als het virtuele netwerk is peered met het virtuele netwerk van AAD DS, dan moet het handmatig worden gedaan.
* Als u DNS-expediteurs gebruikt, moet de domeinnaam correct worden opgelost binnen het virtuele netwerk.
* Beveiligingsbeleid (NSG's) mag de domeinjoin niet blokkeren.

### <a name="additional-debugging-steps"></a>Aanvullende foutopsporingsstappen

* Een windows-VM implementeren in hetzelfde subnet, domein toetreden tot de machine met behulp van een gebruikersnaam en wachtwoord (dit kan worden gedaan via de gebruikersinterface van het configuratiescherm), of

* Een ubuntu VM implementeren in hetzelfde subnet en domein join the machine
  * SSH in de machine
  * sudo su
  * Het script uitvoeren met gebruikersnaam en wachtwoord
  * Het script pingt, maakt de vereiste configuratiebestanden en vervolgens domein. Als dit lukt, zijn uw DNS-instellingen goed.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
