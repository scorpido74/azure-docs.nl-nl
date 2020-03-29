---
title: Versnelde schrijfbewerkingen van Azure HDInsight voor Apache HBase
description: Geeft een overzicht van de azure HDInsight Accelerated Writes-functie, die premium beheerde schijven gebruikt om de prestaties van het Apache HBase Write Ahead Log te verbeteren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 7165bab96d037f6782bc9aa6767cadd9b35f058c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764588"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Versnelde schrijfbewerkingen van Azure HDInsight voor Apache HBase

In dit artikel vindt u achtergrondinformatie over de functie **Versnelde schrijfbewerkingen** voor Apache HBase in Azure HDInsight en hoe deze effectief kan worden gebruikt om de schrijfprestaties te verbeteren. **Accelerated Writes** maakt gebruik van [Azure premium SSD managed disks](../../virtual-machines/linux/disks-types.md#premium-ssd) om de prestaties van de Apache HBase Write Ahead Log (WAL) te verbeteren. Zie [Wat is Apache HBase in HDInsight](apache-hbase-overview.md)voor meer informatie over Apache HBase.

## <a name="overview-of-hbase-architecture"></a>Overzicht van HBase-architectuur

In HBase bestaat een **rij** uit een of meer **kolommen** en wordt deze aangeduid met een **rijsleutel.** Meerdere rijen vormen een **tabel.** Kolommen bevatten **cellen**, die tijdstempelversies van de waarde in die kolom zijn. Kolommen zijn gegroepeerd in **kolomfamilies**en alle kolommen in een kolom-familie worden samen opgeslagen in opslagbestanden met de naam **HFiles**.

**Regio's** in HBase worden gebruikt om de gegevensverwerkingsbelasting in evenwicht te brengen. HBase slaat eerst de rijen van een tabel op in één regio. De rijen zijn verspreid over meerdere gebieden naarmate de hoeveelheid gegevens in de tabel toeneemt. **Regioservers** kunnen aanvragen voor meerdere regio's afhandelen.

## <a name="write-ahead-log-for-apache-hbase"></a>Schrijf Vooruit Log voor Apache HBase

HBase schrijft eerst gegevensupdates voor een type commit log genaamd een Write Ahead Log (WAL). Nadat de update is opgeslagen in de WAL, wordt deze naar de **memStore**in het geheugen geschreven. Wanneer de gegevens in het geheugen de maximale capaciteit bereiken, wordt deze naar de schijf geschreven als een **HFile.**

Als een **RegionServer** vastloopt of niet meer beschikbaar is voordat de MemStore wordt doorgespoeld, kan het logboek voor schrijven worden gebruikt om updates opnieuw af te spelen. Zonder de WAL, als een **RegionServer** crasht voordat updates van een **HFile**doorspoelen, gaan al deze updates verloren.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Versnelde schrijffunctie in Azure HDInsight voor Apache HBase

De functie Versnelde schrijfbewerkingen lost het probleem op van hogere schrijflatencies die worden veroorzaakt door het gebruik van Write Ahead-logboeken die zich in cloudopslag bevinden.  De functie Versnelde schrijfbewerkingen voor HDInsight Apache HBase-clusters, koppelt premium SSD-beheerde schijven aan elke RegionServer (worker node). Write Ahead Logs worden vervolgens geschreven naar het Hadoop File System (HDFS) gemonteerd op deze premium managed-disks in plaats van cloud opslag.  Premium managed-disks maken gebruik van Solid-State Disks (SSD's) en bieden uitstekende I/O-prestaties met fouttolerantie.  In tegenstelling tot onbeheerde schijven heeft deze geen invloed op andere opslageenheden in dezelfde beschikbaarheidsset als één opslageenheid uitvalt.  Als gevolg hiervan bieden managed-disks een lage schrijflatentie en een betere tolerantie voor uw toepassingen. Zie Inleiding tot door Azure [beheerde schijven voor](../../virtual-machines/windows/managed-disks-overview.md)meer informatie over door Azure beheerde schijven.

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Versnelde schrijfbewerkingen voor HBase inschakelen in HDInsight

Als u een nieuw HBase-cluster wilt maken met de functie Versnelde schrijfbewerking, voert u de stappen in [Clusters instellen in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) totdat u **stap 3, Opslag bereikt.** Schakel onder **Metastore-instellingen**het selectievakje in naast **HBase-versnelde schrijfbewerkingen inschakelen**. Ga vervolgens verder met de resterende stappen voor het maken van het cluster.

![Versnelde schrijfoptie inschakelen voor HDInsight Apache HBase](./media/apache-hbase-accelerated-writes/azure-portal-cluster-storage-hbase.png)

## <a name="other-considerations"></a>Andere overwegingen

Als u de duurzaamheid van gegevens wilt behouden, maakt u een cluster met minimaal drie werknemersknooppunten. Eenmaal gemaakt, u het cluster niet schalen naar minder dan drie werkknooppunten.

Spoel of schakel uw HBase-tabellen uit voordat u het cluster verwijderd, zodat u de gegevens van Het Logboek van Write Ahead niet verliest.

```
flush 'mytable'
```

```
disable 'mytable'
```

Volg vergelijkbare stappen bij het verkleinen van uw cluster: spoel de tabellen door en schakel de tabellen uit om binnenkomende gegevens te stoppen. U uw cluster niet naar minder dan drie knooppunten schalen.

Het volgen van deze stappen zal zorgen voor een succesvolle scale-down en voorkomen dat de mogelijkheid van een naamnode in te gaan op de veilige modus als gevolg van onder-gerepliceerde of tijdelijke bestanden.

Als uw naamnode na een schaal naar safemode gaat, gebruikt u hdfs-opdrachten om de ondergerepliceerde blokken opnieuw te repliceren en hdfs uit de veilige modus te halen. Met deze re-replicatie u HBase opnieuw opstarten.

## <a name="next-steps"></a>Volgende stappen

* Officiële Apache HBase documentatie over de [Write Ahead Log functie](https://hbase.apache.org/book.html#wal)
* Zie [Een Apache HBase-cluster migreren naar een nieuwe versie om](apache-hbase-migrate-new-version.md)uw HDInsight Apache HBase-cluster te upgraden om uw HDInsight Apache HBase-cluster te upgraden om versnelde schrijfbewerkingen te gebruiken.
