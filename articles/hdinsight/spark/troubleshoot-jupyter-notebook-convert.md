---
title: Kan Jupyter-notitieblok niet maken in Azure HDInsight
description: Beschrijft stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen bij interactie met Azure HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: 61e7cd8d37108b8f4eea88c4f6b6b2a8cdbfd605
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186803"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>Kan Jupyter-notitieblok niet maken in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Wanneer u een Jupyter-notitieblok start, ontvangt u een foutbericht met het als resultaat:

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>Oorzaak

Een versie mismatch.

## <a name="resolution"></a>Oplossing

1. Gebruik [de ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Open `_version.py` door de volgende opdracht uit te voeren:

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. Wijzig **5** naar **4** zodat de gewijzigde regel als volgt wordt weergegeven:

    ```python
    version_info = (4, 0, 3)
    ```

    Wijzigingen opslaan door **Ctrl + X**, **Y**, Enter in **te voeren**.

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net/#/main/services/JUPYTER`naar `CLUSTERNAME` , waar is de naam van uw cluster.

1. Start de Jupyter-service opnieuw.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
