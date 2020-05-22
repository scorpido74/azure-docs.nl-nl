---
title: Gegevens verwerking optimaliseren voor Apache Spark-Azure HDInsight
description: Meer informatie over het kiezen van de meest efficiënte bewerkingen voor het verwerken van uw gegevens op Apache Spark met Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 1e48573c2b73c10f10f665b5b91759d54d79acdd
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83791062"
---
# <a name="data-processing-optimization"></a>Optimalisatie van gegevens verwerking

In dit artikel wordt beschreven hoe u de configuratie van uw Apache Spark-cluster optimaliseert voor de beste prestaties op Azure HDInsight.

## <a name="overview"></a>Overzicht

Als u langzame taken hebt voor een samen voeging of een wille keurige volg orde, is de oorzaak waarschijnlijk van *gegevens scheef*. Gegevens scheefheid is asymmetrie in uw taak gegevens. Een kaart taak kan bijvoorbeeld 20 seconden duren. Maar het uitvoeren van een taak waarbij de gegevens worden toegevoegd of waarin de rang orde wordt geduurd. Als u gegevens scheefheid wilt verhelpen, moet u de volledige sleutel zouten of een *geïsoleerde Salt* gebruiken voor slechts een deel van de sleutels. Als u een geïsoleerd zout gebruikt, moet u verder filteren om uw subset van gezoute sleutels in kaart verbindingen te isoleren. Een andere optie is om eerst een Bucket kolom en vooraf aggregatie in buckets in te voeren.

Een andere factor veroorzaakt langzame samen voegingen zou het jointype kunnen zijn. Spark maakt standaard gebruik van het `SortMerge` jointype. Dit type samen voeging is het meest geschikt voor grote gegevens sets. Maar is anders rekenbaar kostbaar omdat het eerst de linker-en rechter zijde van de gegevens moet sorteren voordat u ze samenvoegt.

Een `Broadcast` samen voeging is het meest geschikt voor kleinere gegevens sets, of waarbij een zijde van de samen voeging veel kleiner is dan de andere kant. Dit type deelname zendt één zijde naar alle uitvoerende organisaties, en vereist daarom meer geheugen voor broadcasts in het algemeen.

U kunt het jointype in uw configuratie wijzigen door in `spark.sql.autoBroadcastJoinThreshold` te stellen, of u kunt een samenvoegings Hint instellen met behulp van de data frame-api's ( `dataframe.join(broadcast(df2))` ).

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

Als u gekoppelde tabellen gebruikt, hebt u een derde jointype, de `Merge` koppeling. Een juiste vooraf gepartitioneerde en vooraf gesorteerde gegevensset slaat de dure Sorteer fase over van een `SortMerge` koppeling.

De volg orde van de kwesties, met name in complexere query's. Begin met de meeste selectieve samen voegingen. U kunt ook koppelingen verplaatsen die het aantal rijen na aggregaties verg Roten wanneer dat mogelijk is.

Als u de parallelle uitvoering voor Cartesische join's wilt beheren, kunt u geneste structuren en Vensters toevoegen en wellicht een of meer stappen in uw Spark-taak overs Laan.

## <a name="optimize-job-execution"></a>Taak uitvoering optimaliseren

* Sla zo nodig een cache op, bijvoorbeeld als u de gegevens twee keer gebruikt en vervolgens opslaat in de cache.
* Broadcast variabelen naar alle uitvoerende organisaties. De variabelen worden slechts eenmaal geserialiseerd, wat resulteert in snellere zoek acties.
* Gebruik de thread pool op het stuur programma, wat resulteert in een snellere bewerking voor veel taken.

Bewaak uw actieve taken regel matig voor prestatie problemen. Als u meer inzicht wilt in bepaalde problemen, kunt u een van de volgende hulpprogram ma's voor prestatie profilering overwegen:

* [Intel PAL-hulp programma](https://github.com/intel-hadoop/PAT) bewaakt CPU, opslag en netwerk bandbreedte gebruik.
* De beheer profielen voor de [missie van Oracle Java 8](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) Spark en de uitvoerder code.

De sleutel tot Spark 2. x query prestaties is de Tungsten-engine, die afhankelijk is van het genereren van code in een hele fase. In sommige gevallen kan het genereren van code in hele fase worden uitgeschakeld. Als u bijvoorbeeld een niet-ververanderd type ( `string` ) in de expressie aggregatie gebruikt, `SortAggregate` wordt dit weer gegeven in plaats van `HashAggregate` . Als u bijvoorbeeld betere prestaties wilt, kunt u het volgende proberen en vervolgens code generatie opnieuw inschakelen:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Volgende stappen

* [Gegevens opslag voor Apache Spark optimaliseren](optimize-data-storage.md)
* [Geheugen gebruik optimaliseren voor Apache Spark](optimize-memory-usage.md)
* [Cluster configuratie voor Apache Spark optimaliseren](optimize-cluster-configuration.md)