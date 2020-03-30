---
title: hbase hbck retourneert inconsistenties in Azure HDInsight
description: hbase hbck retourneert inconsistenties in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: fa02ac0dfe229f3e82d1c1c62d83ca06a81efca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887322"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Scenario: `hbase hbck` opdracht retourneert inconsistenties in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue-region-is-not-in-hbasemeta"></a>Probleem: Regio is niet in`hbase:meta`

Regio xxx op HDFS, maar `hbase:meta` niet vermeld in of geïmplementeerd op een regioserver.

### <a name="cause"></a>Oorzaak

Varieert.

### <a name="resolution"></a>Oplossing

1. Repareer de metatabel door het uitvoeren van:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Gebieden toewijzen aan RegionServers door het uitvoeren van:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Probleem: regio is offline

Regio xxx niet geïmplementeerd op een RegioServer. Dit betekent dat `hbase:meta`de regio zich binnen, maar offline bevindt.

### <a name="cause"></a>Oorzaak

Varieert.

### <a name="resolution"></a>Oplossing

Regio's online brengen door te draaien:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Probleem: regio's hebben dezelfde begin-/eindtoetsen

### <a name="cause"></a>Oorzaak

Varieert.

### <a name="resolution"></a>Oplossing

Deze overlappende regio's handmatig samenvoegen. Ga naar de tabelsectie HBase HMaster Web UI, selecteer de tabelkoppeling met het probleem. U ziet startsleutel/eindsleutel van elke regio die tot die tabel behoort. Voeg vervolgens die overlappende gebieden samen. In HBase shell, doen `merge_region 'xxxxxxxx','yyyyyyy', true`. Bijvoorbeeld:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

In dit scenario moet u RegioA en RegionC samenvoegen en RegionD met hetzelfde sleutelbereik krijgen als RegioB en vervolgens RegioB en RegioD samenvoegen. xxxxxxx en yyyyyy zijn de hash string aan het einde van elke regio naam. Wees voorzichtig hier niet te fuseren twee discontinu regio's. Na elke samenvoeging, zoals samenvoegen A en C, zal HBase een verdichting starten op RegionD. Wacht tot de verdichting is voltooid voordat u een nieuwe samenvoeging met RegionD doet. U vindt de verdichtingsstatus op die regioserverpagina in HBase HMaster UI.

---

## <a name="issue-cant-load-regioninfo"></a>Probleem: Kan niet laden`.regioninfo`

Kan niet `.regioninfo` laden `/hbase/data/default/tablex/regiony`voor regio.

### <a name="cause"></a>Oorzaak

Dit is waarschijnlijk te wijten aan gedeeltelijke verwijdering van regio's wanneer RegionServer vastloopt of VM opnieuw wordt opgestart. Momenteel is de Azure Storage een plat blob-bestandssysteem en sommige bestandsbewerkingen zijn niet atoom.

### <a name="resolution"></a>Oplossing

Deze resterende bestanden en mappen handmatig opschonen:

1. Uitvoeren `hdfs dfs -ls /hbase/data/default/tablex/regiony` om te controleren welke mappen/bestanden er nog onder zitten.

1. Uitvoeren `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` om alle onderliggende bestanden/mappen te verwijderen

1. Uitvoeren `hdfs dfs -rmr /hbase/data/default/tablex/regiony` om de regiomap te verwijderen.

---

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
