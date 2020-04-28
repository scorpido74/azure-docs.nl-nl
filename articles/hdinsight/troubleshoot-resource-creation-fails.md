---
title: Problemen met het maken van resources in azure HDInsight oplossen
description: In dit artikel worden veelvoorkomende problemen met de capaciteit en de oplossings technieken beschreven.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 527d2d8cb8086ed6b5e87417e2bc80dd52aa6e63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188409"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Problemen met het maken van resources in azure HDInsight oplossen

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight.

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Fout: de implementatie zou het quotum van ' 800 ' overschrijden

Azure heeft een quotumlimiet van 800 implementaties per resourcegroep. Verschillende quota's worden toegepast per resource groep, abonnement, account of andere scopes. Uw abonnement mag bijvoorbeeld worden geconfigureerd om het aantal kerngeheugens voor een regio te beperken. Bij het implementeren van een virtuele machine met meer kern geheugens dan de toegestane resultaten is het fout bericht dat het quotum wordt vermeld, overschreden.

U kunt dit probleem oplossen door de implementaties te verwijderen die niet langer nodig zijn met behulp van de Azure Portal, CLI of Power shell.

Zie voor meer informatie [Solve errors for resource quotas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) (Fouten voor resourcequota oplossen).

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Fout: het maximale knoop punt heeft de beschik bare kernen in deze regio overschreden

Uw abonnement mag worden geconfigureerd om het aantal kerngeheugens voor een regio te beperken. Een poging om een resource te implementeren met meer kern geheugens dan de toegestane resultaten in het fout bericht dat het quotum wordt vermeld, is overschreden.

Voer de volgende stappen uit om een quotumverhoging aan te vragen:

1. Ga naar de [Azure Portal](https://portal.azure.com)en selecteer **Help + ondersteuning**.

1. Selecteer een **nieuwe ondersteunings aanvraag**.

1. Op het tabblad **basis principes** van de pagina **nieuwe ondersteunings aanvraag** geeft u de volgende informatie op:

   * **Type probleem:** **Service-en abonnements limieten (quota's)** selecteren.
   * **Abonnement:** Selecteer het abonnement dat u wilt wijzigen.
   * **Quotum type:** Selecteer **HDInsight**.

Raadpleeg [Create a support ticket to increase core](hdinsight-capacity-planning.md#quotas) (Een ondersteuningsticket maken om het aantal kerngeheugens te verhogen) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport) met-het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
