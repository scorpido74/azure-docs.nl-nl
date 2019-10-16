---
title: Problemen met Apache HBase-prestaties in azure HDInsight oplossen
description: Diverse richt lijnen en tips voor het afstemmen van Apache HBase-prestaties voor optimale prestaties in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: c67f21a6ed8a7697977bb7737f0e46348efb2530
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "71266651"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Problemen met Apache HBase-prestaties in azure HDInsight oplossen

In dit document worden verschillende richt lijnen voor het afstemmen van Apache HBase-prestaties en tips voor het verkrijgen van optimale prestaties in azure HDInsight beschreven. Veel van deze tips zijn afhankelijk van de specifieke werk belasting en het Lees-en schrijf-en scan patroon. Test configuratie wijzigingen grondig voordat u deze toepast op een productie omgeving.

## <a name="hdinsight-hbase-performance-insights"></a>Prestaties van HDInsight HBase performance Insights

De meest voorkomende bottleneck in de meeste HBase-werk belastingen is het Write-Ahead logboek (WAL). Het heeft een grote invloed op de schrijf prestaties. HDInsight HBase heeft een gescheiden model voor opslag berekening, dat wil zeggen dat gegevens extern worden opgeslagen op Azure Storage, maar dat de regio servers worden gehost op de Vm's. Tot nu toe was het Write-Ahead logboek ook geschreven naar Azure Storage waardoor deze bottleneck in het geval van HDInsight wordt verkort. De functie voor [versnelde schrijf bewerkingen](./apache-hbase-accelerated-writes.md) is ontworpen om dit probleem op te lossen door de Write-Ahead logboek te schrijven naar Azure Premium SSD Managed disks. Deze voor delen bieden een enorme schrijf snelheid en helpen veel problemen bij het maken van een aantal write-intensieve workloads.

Gebruik [Premium blok-blobopslagaccounts](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) als uw externe opslag om prestaties van leesbewerkingen aanzienlijk te verbeteren. Deze optie is alleen mogelijk als de functie Write Ahead Logs is ingeschakeld.

## <a name="compaction"></a>Compressie

Compressie is een andere mogelijke bottleneck die in de Community fundamenteel is overeengekomen.  Primaire compressie is standaard uitgeschakeld in HDInsight HBase-clusters. Dit is omdat het een resource-intensieve proces is, willen we klanten de volledige flexibiliteit geven om deze te plannen volgens hun werkbelasting kenmerken, dat wil zeggen tijdens de piek uren. Het is ook belang rijk dat onze opslag extern (ondersteund door Azure Storage) is in plaats van lokale HDFS, wat gebruikelijk is bij de meeste on-premises instanties, de gegevens locatie is een van de belangrijkste doel stellingen van grote compressie.

De veronderstelling is dat de klant de grote verwerkings periode zo nodig zal plannen. Als dit onderhoud niet wordt uitgevoerd, worden de Lees prestaties in de lange uitvoering aanzienlijk beïnvloed door compressie.

Voor scan bewerkingen met name betekenen de gemiddelde latenties die veel hoger zijn dan 100 MS de oorzaak is van een probleem. Controleer of de grote compressie nauw keurig is gepland.

## <a name="know-your-apache-phoenix-workload"></a>De werk belasting van uw Apache Phoenix kennen

Als u de volgende vragen beantwoordt, hebt u meer inzicht in uw Apache Phoenix-werk belasting:

* Zijn al uw ' Lees bewerkingen ' vertaald naar scans?
    * Als dat het geval is, wat zijn dan de kenmerken van deze scans?
    * Hebt u uw BREDAS tabel schema geoptimaliseerd voor deze scans, inclusief de juiste indexeringen?
* Hebt u de instructie `EXPLAIN` gebruikt om inzicht te krijgen in de query plannen van uw ' Lees bewerkingen '.
* Zijn uw schrijf opdrachten "upsert-selecteren"?
    * Als dat het geval is, worden er ook scans uitgevoerd. De verwachte latentie voor scans is de volg orde 100 MS gemiddeld, in tegens telling tot 10 MS voor punt haalt in HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Bewakings methodiek en metrische gegevens controleren

Als u benchmarks gebruikt zoals YCSB, JMeter of Pherf om prestaties te testen en af te stemmen, controleert u het volgende:

1. De client computers worden niet knelpunten (Controleer het CPU-gebruik op client computers).

1. Configuraties aan client zijde, zoals het aantal threads, enzovoort, worden op de juiste wijze afgestemd op de verzadiging van client bandbreedte.

1. Test resultaten worden nauw keurig en systematisch vastgelegd.

Als uw query's plotseling zijn begonnen met het uitvoeren van problemen, kunt u controleren of er fouten zijn opgegaan in de code van uw toepassing. Dit leidt er plotseling toe dat er grote hoeveel heden gegevens worden gegenereerd, waardoor de lees latentie natuurlijk toeneemt.

## <a name="migration-issues"></a>Migratie problemen

Als u migreert naar Azure HDInsight, moet u ervoor zorgen dat uw migratie systematisch en nauw keurig wordt uitgevoerd, bij voor keur via Automation. Vermijd hand matige migratie. Controleer het volgende:

1. Tabel kenmerken, zoals compressie, bloei filters, enzovoort, moeten nauw keurig worden gemigreerd.

1. Voor Phoenix-tabellen moeten de instellingen voor het zouten correct worden toegewezen aan de nieuwe cluster grootte. Zo wordt bijvoorbeeld het aantal zout buckets aanbevolen om meerdere van het aantal worker-knoop punten in het cluster te zijn. het specifieke veelvoud is een factor van de hoeveelheid Hot herkennen die wordt waargenomen.  

## <a name="server-side-config-tunings"></a>Aanpassings configuratie aan server zijde

In HDInsight HBase worden HFiles op externe opslag opgeslagen. als er een cache verloren gaat, zijn de kosten van Lees bewerkingen dus niet hoger dan on-premises systemen, die gegevens bevatten die zijn opgeslagen door Local HDFS dankzij de netwerk latentie. Voor de meeste scenario's is intelligent gebruik van HBase-caches (Block cache en Bucket cache) ontworpen om dit probleem te omzeilen. Er zijn echter incidentele gevallen waarbij dit een probleem voor de klant kan zijn. Het gebruik van een Premium Block BLOB-account heeft dit enigszins geholpen. Met de WASB-BLOB (Windows Azure Storage driver) wordt echter gebruikgemaakt van bepaalde eigenschappen, zoals `fs.azure.read.request.size`, om gegevens op te halen in blokken op basis van de Lees modus (opeenvolgend versus wille keurig). We hebben geconstateerd dat er empirische experimenten zijn gevonden waarmee de blok grootte van de Lees aanvraag (`fs.azure.read.request.size`) wordt ingesteld op 512 KB en dat de blok grootte van de HBase-tabellen op dezelfde manier overeenkomt met het beste resultaat in de praktijk.

HDInsight HBase biedt voor de meeste grootschalige knoop punten clusters `bucketcache` als een bestand op lokale SSD dat is gekoppeld aan de VM, waardoor de `regionservers` wordt uitgevoerd. In de meeste gevallen kan het gebruik van heap-cache in plaats daarvan enige verbeteringen opleveren. Dit heeft de beperking van het gebruik van het beschik bare geheugen en is mogelijk kleiner dan een op bestanden gebaseerde cache, zodat dit niet altijd de beste keuze is.

Enkele van de andere specifieke para meters die we hebben afgestemd op het hebben van een deel van de vrijheid met een motivering als hieronder:

1. Verhoog de grootte van @no__t 0 van standaard 128 MB tot 256 MB. deze instelling wordt meestal aanbevolen voor een zwaar schrijf scenario.

1. Het aantal threads dat is gereserveerd voor compressie verhogen: van de standaard waarde van 1 tot 4. Deze instelling is relevant als we veelvuldige kleine compressies bekijken.

1. Voorkom dat het leegmaken van @no__t 0 wordt geblokkeerd vanwege een opslag limiet. `Hbase.hstore.blockingStoreFiles` kan worden verhoogd tot 100 om deze buffer op te geven.

1. Voor het beheren van leegmaak acties, kunnen de standaard waarden als volgt worden behandeld:

    1. `Hbase.regionserver.maxlogs` kan worden upped tot 140 van 32 (het leegmaken van leegmaak acties door WAL-limieten wordt voor komen).

    1. `Hbase.regionserver.global.memstore.lowerLimit` = 0,55.

    1. `Hbase.regionserver.global.memstore.upperLimit` = 0,60.

1. Phoenix-specifieke configuraties voor het afstemmen van thread groepen:

    1. `Phoenix.query.queuesize` kan worden verhoogd tot 10000.

    1. `Phoenix.query.threadpoolsize` kan worden verhoogd tot 512.

1. Andere Phoenix-specifieke configuraties:

    1. `Phoenix.rpc.index.handler.count` kan worden ingesteld op 50 als er grote of veel index lookups zijn.

    1. `Phoenix.stats.updateFrequency` – kan upped tot 1 uur in de standaard tijd van 15 minuten.

    1. `Phoenix.coprocessor.maxmetadatacachetimetolivems` – kan upped tot 1 uur en Maxi maal 30 minuten.

    1. `Phoenix.coprocessor.maxmetadatacachesize` – kan upped tot 50 MB van 20 MB zijn.

1. RPC-time-outs – HBase RPC-time-out, time-out voor HBase-client scanner en Phoenix-querytime kunnen tot drie minuten worden verhoogd. Het is belang rijk om te weten dat de para meter @no__t 0 is ingesteld op een waarde die overeenkomt met het einde van de server en client end. Anders leidt deze instelling tot fouten met betrekking tot `OutOfOrderScannerException` bij het einde van de client. Deze instelling moet worden ingesteld op een lage waarde voor grote scans. We stellen deze waarde in op 100.

## <a name="other-considerations"></a>Andere overwegingen

Enkele andere para meters die in aanmerking komen voor afstemming:

1. `Hbase.rs.cacheblocksonwrite`: deze instelling is standaard ingesteld op True op HDI.

1. Instellingen waarmee u de kleine compressie voor later kunt uitstellen.

1. Experimentele instellingen, zoals het aanpassen van het percentage wacht rijen dat is gereserveerd voor lees-en schrijf aanvragen.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

- Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure-account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

- Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
