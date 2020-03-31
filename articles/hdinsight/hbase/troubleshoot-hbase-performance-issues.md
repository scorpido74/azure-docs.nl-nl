---
title: Prestatieproblemen van Apache Hbase in Azure HDInsight oplossen
description: Verschillende richtlijnen en tips voor het afstemmen van Apache HBase-prestaties voor het afstemmen van de prestaties op Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 93698fadcecf190dd8bbc24a9d03978899d3c5e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887152"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Prestatieproblemen van Apache Hbase in Azure HDInsight oplossen

In dit artikel worden verschillende richtlijnen en tips voor het afstemmen van apache HBase-prestaties beschreven voor het verkrijgen van optimale prestaties op Azure HDInsight. Veel van deze tips zijn afhankelijk van de specifieke werkbelasting en lees /write /scan patroon. Voordat u configuratiewijzigingen toepast in een productieomgeving, test u deze grondig.

## <a name="hbase-performance-insights"></a>HBase-prestatieinzichten

De belangrijkste bottleneck in de meeste HBase-workloads is de Write Ahead Log (WAL). Het heeft ernstige gevolgen voor de schrijfprestaties. HDInsight HBase heeft een gescheiden storage-compute model. Gegevens worden op afstand opgeslagen in Azure Storage, ook al hosten virtuele machines de regioservers. Tot voor kort werd de WAL ook naar Azure Storage geschreven. In HDInsight versterkte dit gedrag dit knelpunt. De [functie Versnelde schrijf is](./apache-hbase-accelerated-writes.md) ontworpen om dit probleem op te lossen. Het schrijft de WAL naar Azure Premium SSD-beheerde schijven. Dit komt enorm ten goede aan de schrijfprestaties en het helpt veel problemen waarmee sommige schrijfintensieve workloads worden geconfronteerd.

Als u de leesbewerkingen aanzienlijk wilt verbeteren, gebruikt u [Premium Block Blob Storage Account](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) als uw externe opslag. Deze optie is alleen mogelijk als de WAL-functie is ingeschakeld.

## <a name="compaction"></a>Compressie

Verdichting is een ander potentieel knelpunt dat fundamenteel is overeengekomen in de gemeenschap. Standaard is grote verdichting uitgeschakeld op HDInsight HBase-clusters. Verdichting is uitgeschakeld omdat, ook al is het een resource-intensief proces, klanten hebben volledige flexibiliteit om het te plannen op basis van hun workloads. Ze kunnen het bijvoorbeeld tijdens de daluren plannen. Ook is gegevensplaats geen probleem omdat onze opslag op afstand is (ondersteund door Azure Storage) in plaats van naar een lokaal Hadoop Distributed File System (HDFS).

Klanten moeten grote verdichting plannen op hun gemak. Als ze dit onderhoud niet doen, zal verdichting de leesprestaties op de lange termijn negatief beïnvloeden.

Voor scanbewerkingen moeten gemiddelde latencies die veel hoger zijn dan 100 milliseconden een reden tot bezorgdheid zijn. Controleer of grote verdichting nauwkeurig is gepland.

## <a name="apache-phoenix-workload"></a>Apache Phoenix werklast

Als u de volgende vragen beantwoordt, u uw Apache Phoenix-workload beter begrijpen:

* Zijn al uw "leest" vertalen naar scans?
    * Zo ja, wat zijn de kenmerken van deze scans?
    * Hebt u uw Phoenix-tabelschema geoptimaliseerd voor deze scans, inclusief de juiste indexering?
* Hebt u `EXPLAIN` de instructie gebruikt om inzicht te krijgen in de queryplannen die uw 'leest' genereren?
* Zijn uw schrijft "upsert-selecteert"?
    * Als dat zo is, zouden ze ook doen scans. Verwachte latentie voor scans gemiddeld ongeveer 100 milliseconden, vergeleken met 10 milliseconden voor punt krijgt in HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Testmethodologie en metrische gegevensmonitoring

Als u benchmarks zoals Yahoo! Cloud Serving Benchmark, JMeter of Pherf om de prestaties te testen en af te stemmen, zorg ervoor dat:

- De clientmachines worden geen knelpunt. Controleer hiervoor het CPU-gebruik op clientmachines.

- Clientconfiguraties, zoals het aantal threads, zijn op de juiste manier afgestemd op de bandbreedte van de client.

- De testresultaten worden nauwkeurig en systematisch geregistreerd.

Als uw query's plotseling veel slechter begonnen te doen dan voorheen, controleert u op mogelijke bugs in uw toepassingscode. Genereert het plotseling grote hoeveelheden ongeldige gegevens? Als dit het is, kan het leeslatenverhogen.

## <a name="migration-issues"></a>Migratieproblemen

Als u migreert naar Azure HDInsight, moet u ervoor zorgen dat uw migratie systematisch en nauwkeurig wordt uitgevoerd, bij voorkeur via automatisering. Vermijd handmatige migratie. Vereisten:

- Tabelkenmerken worden nauwkeurig gemigreerd. Kenmerken kunnen bestaan uit compressie, bloeifilters, enzovoort.

- De zoutinstellingen in Phoenix-tabellen worden op de juiste manier toegewezen aan de nieuwe clustergrootte. Het aantal zoutemmers moet bijvoorbeeld een veelvoud zijn van het aantal werknemersknooppunten in het cluster. En je moet gebruik maken van een veelvoud dat is een factor van de hoeveelheid hete spotten.

## <a name="server-side-configuration-tunings"></a>Configuratieafstemmingaan de serverzijde

In HDInsight HBase worden HFiles opgeslagen op externe opslag. Wanneer er een cache ontbreekt, zijn de kosten van reads hoger dan on-premises systemen, omdat gegevens over on-premises systemen worden ondersteund door lokale HDFS. Voor de meeste scenario's is intelligent gebruik van HBase-caches (blokcache en bucketcache) ontworpen om dit probleem te omzeilen. In gevallen waarin het probleem niet wordt omzeild, kan het gebruik van een premium block blob-account dit probleem helpen. Het Windows Azure Storage Blob-stuurprogramma is `fs.azure.read.request.size` afhankelijk van bepaalde eigenschappen, zoals het ophalen van gegevens in blokken op basis van wat het bepaalt om leesmodus te zijn (sequentiële versus willekeurige), dus er kunnen nog steeds gevallen van hogere latencies met leest zijn. Door middel van empirische experimenten hebben we`fs.azure.read.request.size`vastgesteld dat het instellen van de grootte van het leesverzoekblok () op 512 KB en het afstemmen van de blokgrootte van de HBase-tabellen op dezelfde grootte het beste resultaat oplevert in de praktijk.

Voor de meeste grote knooppuntenclusters biedt `bucketcache` HDInsight HBase als bestand op een lokale Premium SSD `regionservers`die is gekoppeld aan de virtuele machine, die wordt uitgevoerd. Het gebruik van off-heap cache in plaats daarvan zou kunnen bieden enige verbetering. Deze tijdelijke oplossing heeft de beperking van het gebruik van beschikbaar geheugen en mogelijk kleiner dan bestandsgebaseerde cache, dus het is misschien niet altijd de beste keuze.

Hieronder volgen enkele van de andere specifieke parameters die we hebben afgestemd, en dat leek te helpen in verschillende mate:

- Vergroot `memstore` de grootte van standaard 128 MB naar 256 MB. Deze instelling wordt doorgaans aanbevolen voor zware schrijfscenario's.

- Verhoog het aantal threads dat is toegewezen voor verdichting, van de standaardinstelling van **1** naar **4**. Deze instelling is relevant als we regelmatig kleine verdichtingen waarnemen.

- Vermijd `memstore` het blokkeren van flush vanwege winkellimiet. Als u deze buffer `Hbase.hstore.blockingStoreFiles` wilt bieden, verhoogt u de instelling naar **100**.

- Als u flushes wilt beheren, gebruikt u de volgende instellingen:

    - `Hbase.regionserver.maxlogs`: **140** (vermijdt flushes vanwege WAL-limieten)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0,55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0,60**

- Phoenix-specifieke configuraties voor thread pool tuning:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`: **512**

- Andere Phoenix-specifieke configuraties:

    - `Phoenix.rpc.index.handler.count`: **50** (als er grote of veel index lookups zijn)

    - `Phoenix.stats.updateFrequency`: **1 uur**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 uur**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- RPC-time-outs: **3 minuten**

   - RPC-time-outs omvatten HBase RPC-time-outs, een time-out voor HBase-clientscanner en een time-out voor Phoenix-query's. 
   - Zorg ervoor `hbase.client.scanner.caching` dat de parameter is ingesteld op dezelfde waarde aan zowel het servereinde als het clienteinde. Als ze niet hetzelfde zijn, leidt deze instelling tot `OutOfOrderScannerException`client-endfouten die gerelateerd zijn aan . Deze instelling moet worden ingesteld op een lage waarde voor grote scans. We stellen deze waarde in op **100**.

## <a name="other-considerations"></a>Andere overwegingen

De volgende parameters zijn aanvullende parameters om te overwegen tuning:

- `Hbase.rs.cacheblocksonwrite`– standaard op HDI, is deze instelling ingesteld op **true**.

- Instellingen die het mogelijk maken om kleine verdichting uit te stellen voor later.

- Experimentele instellingen, zoals het aanpassen van percentages wachtrijen die zijn gereserveerd voor lees- en schrijfaanvragen.

## <a name="next-steps"></a>Volgende stappen

Als uw probleem onopgelost blijft, gaat u naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

- Maak [@AzureSupport](https://twitter.com/azuresupport)verbinding met . Dit is het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. Het verbindt de Azure-community met de juiste bronnen: antwoorden, ondersteuning en experts.

- Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Uw Microsoft Azure-abonnement bevat toegang tot abonnementsbeheer en factureringsondersteuning en technische ondersteuning wordt geboden via een van de [Azure-ondersteuningsplannen.](https://azure.microsoft.com/support/plans/)
