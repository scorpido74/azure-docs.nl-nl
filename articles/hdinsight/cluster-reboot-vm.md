---
title: Vm's voor Azure HDInsight-cluster opnieuw opstarten
description: Meer informatie over het opnieuw opstarten van niet-reagerende Vm's voor het HDInsight-cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: c0f0bd9eb423b3de6a602647dff93fd9fce6e13e
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077011"
---
# <a name="reboot-vms-for-hdinsight-cluster"></a>Vm's voor HDInsight-cluster opnieuw opstarten

HDInsight-clusters bevatten groepen Vm's als cluster knooppunten. Voor langlopende clusters kunnen deze knoop punten om verschillende redenen niet meer reageren. In dit artikel wordt beschreven hoe u niet-reagerende Vm's in een HDInsight-cluster opnieuw opstarten.

## <a name="when-to-reboot"></a>Wanneer opnieuw opstarten

> [!WARNING]  
> Als de Vm's in het cluster opnieuw worden opgestart, wordt de downtime van het knoop punt ingeschakeld en worden de services op het knoop punt opnieuw gestart. 

Terwijl het knoop punt opnieuw wordt opgestart, kan het cluster beschadigd raken, kunnen de taken vertragen of mislukken. Als u het actieve hoofd knooppunt opnieuw wilt opstarten, worden alle actieve taken afgebroken en kunt u geen taken naar het cluster verzenden totdat de services weer actief zijn. Overweeg om Vm's alleen opnieuw op te starten als dat nodig is. Hier vindt u enkele richt lijnen voor het opnieuw opstarten van Vm's.

- U kunt geen SSH-service uitvoeren in het knoop punt, maar dit reageert op pings.
- Het worker-knoop punt is niet actief zonder heartbeat in de Ambari-gebruikers interface.
- De tijdelijke schijf is vol op het knoop punt.
- De proces tabel op de virtuele machine bevat veel vermeldingen waar het proces is voltooid, maar het wordt weer gegeven met de status afgesloten.

> [!WARNING]  
> Wees voorzichtig bij het opnieuw opstarten van Vm's voor **HBase** en **Kafka** clustes, omdat dit kan leiden tot verlies van gegevens.

## <a name="use-powershell-to-reboot-vms"></a>Power shell gebruiken voor het opnieuw opstarten van Vm's

Er zijn twee stappen vereist om de bewerking voor het opnieuw opstarten van het knoop punt te gebruiken: een lijst met knoop punten en knoop punten opnieuw starten

1. Knoop punten weer geven. U kunt de lijst met cluster knooppunten ophalen via [Get-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsighthost). 

  ```
  Get-AzHDInsightHost -ClusterName myclustername
  ```

2. Start hosts opnieuw op. Nadat u de namen van de knoop punten die u opnieuw wilt opstarten hebt opgehaald, start u de knoop punten opnieuw op met [restart-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost).

  ```
  Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
  ```

## <a name="use-rest-api-to-reboot-vms"></a>Gebruik REST API om Vm's opnieuw op te starten

U kunt de functie **try it** in het API-document gebruiken om aanvragen te verzenden naar HDInsight. Er zijn twee stappen vereist om de bewerking voor het opnieuw opstarten van het knoop punt te gebruiken: een lijst met knoop punten en knoop punten opnieuw starten

1. Knoop punten weer geven. U kunt de lijst met cluster knooppunten ophalen uit de REST API of in Ambari. Meer informatie vindt u op de [lijst met HDInsight-Hosts rest API bewerking](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

2. Start hosts opnieuw op. Nadat u de namen van de knoop punten hebt opgehaald die u opnieuw wilt opstarten, gebruikt u knoop punten opnieuw starten REST API om de knoop punten opnieuw op te starten. De naam van het knoop punt volgt het patroon van **' NodeType (wn/HN/ZK/GW) ' + ' x ' + ' eerste 6 tekens van cluster naam '**. Meer informatie vindt u op het [opnieuw opstarten van HDInsight-Hosts rest API bewerking](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

De werkelijke namen van de knoop punten die u opnieuw wilt opstarten, worden opgegeven in een JSON-matrix in de hoofd tekst van de aanvraag.

```json
[
  "wn0-abcdef",
  "zk1-abcdef"
]
```

## <a name="next-steps"></a>Volgende stappen

* [Restart-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [Virtuele HDInsight-machines REST API](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [HDInsight-REST API](https://docs.microsoft.com/rest/api/hdinsight/)
