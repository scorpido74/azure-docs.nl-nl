---
title: Jupyter 404-fout - "Cross Origin API blokkeren" - Azure HDInsight
description: Jupyter server 404 "Niet gevonden" fout als gevolg van "Cross Origin API blokkeren" in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: e241657186582955d21981f7dfe18856724aa692
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894417"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Scenario: Jupyter server 404 "Niet gevonden" fout als gevolg van "Cross Origin API blokkeren" in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-componenten in Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Wanneer u de Jupyter-service op HDInsight opent, ziet u een foutvak met de tekst "Niet gevonden". Als u de Jupyter logs, ziet u iets als dit:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

U ook een IP-adres zien in het veld 'Oorsprong' in het Jupyter-logboek.

## <a name="cause"></a>Oorzaak

Deze fout kan worden veroorzaakt door een paar dingen:

- Als u NSG-regels (Network Security Group) hebt geconfigureerd om de toegang tot het cluster te beperken. Als u de toegang met NSG-regels beperkt, u nog steeds rechtstreeks toegang krijgen tot Apache Ambari en andere services met behulp van het IP-adres in plaats van de naam van het cluster. Echter, bij de toegang tot Jupyter, kon je een 404 "Niet gevonden" fout te zien.

- Als u uw HDInsight-gateway een aangepaste DNS-naam hebt gegeven, andere dan de standaard. `xxx.azurehdinsight.net`

## <a name="resolution"></a>Oplossing

1. Wijzig de jupyter.py bestanden op deze twee plaatsen:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Zoek de regel `NotebookApp.allow_origin='\"https://{2}.{3}\"'` die zegt: `NotebookApp.allow_origin='\"*\"'`En verander het in: .

1. Start de Jupyter-service van Ambari opnieuw.

1. Als `ps aux | grep jupyter` u typt bij de opdrachtprompt, moet worden aantoont dat elke URL hiermee verbinding kan maken.

Dit is een minder veilig dan de instelling die we al hadden. Maar er wordt aangenomen dat de toegang tot het cluster is beperkt en dat een van buitenaf is toegestaan om verbinding te maken met het cluster als we NSG op zijn plaats.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
