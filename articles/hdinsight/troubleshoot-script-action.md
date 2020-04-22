---
title: Scriptacties oplossen in Azure HDInsight
description: Algemene stappen voor het oplossen van problemen voor scriptacties in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/21/2020
ms.openlocfilehash: b1e6b674edc155e0aa6c88ad360eb59864eebee4
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771974"
---
# <a name="troubleshoot-script-actions-in-azure-hdinsight"></a>Scriptacties oplossen in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="viewing-logs"></a>Logboeken weergeven

U de gebruikersinterface van het Apache Ambari-web gebruiken om informatie te bekijken die is vastgelegd door scriptacties. Als het script mislukt tijdens het maken van het cluster, bevinden logboeken zich in het standaardclusteropslagaccount. In deze sectie vindt u informatie over het ophalen van de logboeken met behulp van beide opties.

### <a name="apache-ambari-web-ui"></a>Apache Ambari web Gebruikersinterface

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net`naar `CLUSTERNAME` , waar is de naam van uw cluster.

1. Selecteer boven aan de pagina het **ops-item.** In een lijst worden huidige en eerdere bewerkingen weergegeven die via Ambari op het cluster zijn uitgevoerd.

    ![Ambari web-gebruikersinterfacebalk met geselecteerde ops](./media/troubleshoot-script-action/hdi-apache-ambari-nav.png)

1. Zoek de items die **aangepaste\_scriptactie** hebben uitgevoerd in de kolom **Operations.** Deze vermeldingen worden gemaakt wanneer de scriptacties worden uitgevoerd.

    ![Apache Ambari scriptactiebewerkingen](./media/troubleshoot-script-action/ambari-script-action.png)

    Als u de **uitvoer van STDOUT** en **STDERR** wilt weergeven, selecteert u de **run\customscriptaction-vermelding** en zoomt u in via de koppelingen. Deze uitvoer wordt gegenereerd wanneer het script wordt uitgevoerd en mogelijk nuttige informatie heeft.

### <a name="default-storage-account"></a>Standaardopslagaccount

Als clustercreatie mislukt vanwege een scriptfout, worden de logboeken bewaard in het clusteropslagaccount.

* De opslaglogboeken `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`zijn beschikbaar op .

    ![Scriptactielogboeken](./media/troubleshoot-script-action/script-action-logs-in-storage.png)

    Onder deze map worden de logboeken afzonderlijk georganiseerd voor **headnode,** **werknemersknooppunt**en **zookeeper-knooppunt**. Zie de volgende voorbeelden:

    * **Headnode**:`<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Werknemersknooppunt**:`<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Zookeeper knooppunt**:`<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Alle **stdout** en **stderr** van de bijbehorende host worden geüpload naar het opslagaccount. Er is één **uitvoer-\*.txt** en **fouten-\*.txt** voor elke scriptactie. Het **bestand uitvoer-*.txt** bevat informatie over de URI van het script dat op de host is uitgevoerd. De volgende tekst is een voorbeeld van deze informatie:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Het is mogelijk dat u herhaaldelijk een scriptactiecluster met dezelfde naam maakt. In dat geval u de relevante logboeken onderscheiden op basis van de naam van de **datummap.** De mapstructuur voor een cluster, **mycluster,** die op verschillende datums is gemaakt, lijkt bijvoorbeeld op de volgende logboekvermeldingen:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Als u op dezelfde dag een scriptactiecluster met dezelfde naam maakt, u het unieke voorvoegsel gebruiken om de relevante logboekbestanden te identificeren.

* Als u een cluster maakt in de buurt van 12:00 uur, middernacht, is het mogelijk dat de logboekbestanden zich over twee dagen uitstrekken. In dat geval ziet u twee verschillende datummappen voor hetzelfde cluster.

* Het uploaden van logbestanden naar de standaardcontainer kan tot vijf minuten duren, vooral voor grote clusters. Dus als u toegang wilt tot de logboeken, moet u het cluster niet onmiddellijk verwijderen als een scriptactie mislukt.

## <a name="ambari-watchdog"></a>Ambari waakhond

Verander het wachtwoord voor de Ambari waakhond, hdinsightwatchdog, niet op uw Linux-gebaseerde HDInsight-cluster. Een wachtwoordwijziging maakt een stuk voor de mogelijkheid om nieuwe scriptacties uit te voeren op het HDInsight-cluster.

## <a name="cant-import-name-blobservice"></a>Kan naam BlobService niet importeren

__Symptomen.__ De scriptactie mislukt. Tekst die lijkt op de volgende fout wordt weergegeven wanneer u de bewerking in Ambari bekijkt:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Oorzaak__. Deze fout treedt op als u de Python Azure Storage-client upgradet die is opgenomen in het HDInsight-cluster. HDInsight verwacht Azure Storage-client 0.20.0.

__Resolutie__. Als u deze fout wilt oplossen, maakt `ssh`u handmatig verbinding met elk clusterknooppunt met behulp van . Voer de volgende opdracht uit om de juiste opslagclientversie opnieuw te installeren:

```bash
sudo pip install azure-storage==0.20.0
```

Zie Verbinding maken met [HDInsight (Apache Hadoop) met Behulp van SSH](hdinsight-hadoop-linux-use-ssh-unix.md)voor informatie over het verbinden met het cluster met SSH.

## <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Geschiedenis toont niet de scripts die worden gebruikt tijdens het maken van het cluster

Als uw cluster vóór 15 maart 2016 is gemaakt, ziet u mogelijk geen vermelding in de scriptactiegeschiedenis. Als u het formaat van het cluster wijzigen, worden de scripts weergegeven in de scriptactiegeschiedenis.

Er zijn twee uitzonderingen:

* Uw cluster is gemaakt vóór 1 september 2015. Deze datum is wanneer scriptacties werden geïntroduceerd. Een cluster dat vóór deze datum is gemaakt, kan geen scriptacties hebben gebruikt voor het maken van een cluster.

* U hebt meerdere scriptacties gebruikt tijdens het maken van het cluster. Of u gebruikte dezelfde naam voor meerdere scripts of dezelfde naam, dezelfde URI, maar verschillende parameters voor meerdere scripts. In deze gevallen krijgt u de volgende fout:

    ```
    No new script actions can be run on this cluster because of conflicting script names in existing scripts. Script names provided at cluster creation must be all unique. Existing scripts are run on resize.
    ```

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).