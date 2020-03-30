---
title: Het account dat wordt geopend, biedt geen ondersteuning voor http-fout in Azure HDInsight
description: In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 14c43e4557275d6a425127acfee7495f68d1d354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165551"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>Het account dat wordt geopend, biedt geen ondersteuning voor http-fout in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Het volgende foutbericht wordt ontvangen:

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>Oorzaak

Er zijn meerdere redenen waarom het foutbericht wordt ontvangen:

* Het opslagaccount heeft [beveiligde overdracht](../../storage/common/storage-require-secure-transfer.md) ingeschakeld en het onjuiste [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) wordt gebruikt.

* Er is een cluster gemaakt met een opslagaccount waarbij de beveiligde overdracht *is uitgeschakeld.* Daarna werd een veilige overdracht ingeschakeld op het opslagaccount.

## <a name="resolution"></a>Oplossing

Als beveiligde overdracht is ingeschakeld voor Azure Storage of Data `wasbs://` Lake `abfss://`Storage Gen2, is de URI respectievelijk.  Zie ook, [veilige overdracht](../../storage/common/storage-require-secure-transfer.md).

Voor nieuwe clusters gebruikt u een opslagaccount dat al de gewenste instelling voor veilige overdracht heeft. Wijzig de instelling voor veilige overdracht niet voor een opslagaccount dat door een bestaand cluster wordt gebruikt.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
