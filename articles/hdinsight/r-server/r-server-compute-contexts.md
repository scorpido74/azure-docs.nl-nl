---
title: Contextopties voor gegevens berekenen voor ML-services op HDInsight - Azure
description: Meer informatie over de verschillende rekencontextopties die beschikbaar zijn voor gebruikers met ML Services op HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: b67bd5b6310e1f8ce35dc14690757209ef62c9d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660253"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Contextopties berekenen voor ML Services op HDInsight

ML Services op Azure HDInsight bepaalt hoe oproepen worden uitgevoerd door de rekencontext in te stellen. In dit artikel worden de opties beschreven die beschikbaar zijn om aan te geven of en hoe de uitvoering wordt parallellopen tussen kernen van het edge-node- of HDInsight-cluster.

Het randknooppunt van een cluster biedt een handige plek om verbinding te maken met het cluster en uw R-scripts uit te voeren. Met een randknooppunt hebt u de mogelijkheid om de parallelle gedistribueerde functies van RevoScaleR over de kernen van de edge node-server uit te voeren. U ze ook uitvoeren op de knooppunten van het cluster met behulp van RevoScaleR's Hadoop Map Reduce of Apache Spark compute contexten.

## <a name="ml-services-on-azure-hdinsight"></a>ML-services op Azure HDInsight

[ML Services op Azure HDInsight](r-server-overview.md) biedt de nieuwste mogelijkheden voor R-gebaseerde analyses. Het kan gegevens gebruiken die zijn opgeslagen in een Apache Hadoop HDFS-container in uw [Azure Blob-opslagaccount,](../../storage/common/storage-introduction.md "Azure Blob Storage") een Data Lake Store of het lokale Linux-bestandssysteem. Aangezien ML Services is gebouwd op open-source R, kunnen de R-gebaseerde applicaties die u bouwt een van de meer dan 8000 open-source R-pakketten toepassen. Ze kunnen ook gebruik maken van de routines in [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), Microsoft's big data analytics pakket dat is opgenomen in ML Services.  

## <a name="compute-contexts-for-an-edge-node"></a>Contexten berekenen voor een randknooppunt

In het algemeen wordt een R-script uitgevoerd in het ML Services-cluster op het randknooppunt binnen de R-interpreter op dat knooppunt. De uitzonderingen zijn de stappen die een RevoScaleR-functie aanroepen. De RevoScaleR-aanroepen worden uitgevoerd in een compute-omgeving die wordt bepaald door de manier waarop u de rekencontext RevoScaleR instelt.  Wanneer u uw R-script uitvoert vanaf een randknooppunt, zijn de mogelijke waarden van de rekencontext:

- lokale sequentiële (*lokale*)
- lokale parallel *(localpar)*
- Kaart verkleinen
- Spark

De *lokale* en *localpar-opties* verschillen alleen in de manier waarop **rxExec-gesprekken** worden uitgevoerd. Ze voeren beide andere rx-functie aanroepen op een parallelle manier uit in alle beschikbare kernen, tenzij `rxOptions(numCoresToUse=6)`anders gespecificeerd door gebruik te maken van de revoScaleR **numCoresToUse-optie,** bijvoorbeeld . Parallelle uitvoeringsopties bieden optimale prestaties.

In de volgende tabel worden de verschillende opties voor de compute-context samengevat om in te stellen hoe oproepen worden uitgevoerd:

| Compute-context  | Instellen                      | Context voor uitvoering                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Lokale sequentiële | rxSetComputeContext('lokaal')    | Parallelle uitvoering over de kernen van de edge node-server, met uitzondering van rxExec-aanroepen, die serieel worden uitgevoerd |
| Lokale parallel   | rxSetComputeContext('localpar') | Parallelle uitvoering over de kernen van de edge-nodeserver |
| Spark            | RxSpark()                       | Parallelle gedistribueerde uitvoering via Spark over de knooppunten van het HDI-cluster |
| Kaart verkleinen       | RxHadoopMR()                    | Parallelle gedistribueerde uitvoering via Map Reduce over de knooppunten van het HDI-cluster |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Richtlijnen voor het bepalen van een rekencontext

Welke van de drie opties die u kiest voor parallelle uitvoering, is afhankelijk van de aard van uw analysewerk, de grootte en de locatie van uw gegevens. Er is geen eenvoudige formule die je vertelt welke rekencontext je moet gebruiken. Er zijn echter een aantal leidende principes die u kunnen helpen de juiste keuze te maken, of, op zijn minst, u helpen uw keuzes te beperken voordat u een benchmark uitvoert. Deze leidende principes omvatten:

- Het lokale Linux-bestandssysteem is sneller dan HDFS.
- Herhaalde analyses zijn sneller als de gegevens lokaal zijn en als deze zich in XDF bevinden.
- Het is beter om kleine hoeveelheden gegevens te streamen van een tekstgegevensbron. Als de hoeveelheid gegevens groter is, converteert u deze naar XDF voordat deze wordt geanalyseerd.
- De overhead van het kopiëren of streamen van de gegevens naar het randknooppunt voor analyse wordt onhandelbaar voor zeer grote hoeveelheden gegevens.
- ApacheSpark is sneller dan Map Reduce voor analyse in Hadoop.

Gezien deze principes bieden de volgende secties een aantal algemene vuistregels voor het selecteren van een rekencontext.

### <a name="local"></a>Lokaal

- Als de hoeveelheid te analyseren gegevens klein is en geen herhaalde analyse vereist, stream deze dan rechtstreeks naar de analyseroutine met behulp van *lokale* of *localpar.*
- Als de hoeveelheid gegevens die moet worden geanalyseerd klein of middelgroot is en herhaalde analyse vereist, kopieert u deze naar het lokale bestandssysteem, importeert u deze naar XDF en analyseert u deze via *lokale* of *lokale par.*

### <a name="apache-spark"></a>Apache Spark

- Als de hoeveelheid te analyseren gegevens groot is, importeer deze dan naar een Spark DataFrame met **RxHiveData** of **RxParquetData**of naar XDF in HDFS (tenzij opslag een probleem is) en analyseer deze met behulp van de Spark-compute-context.

### <a name="apache-hadoop-map-reduce"></a>Apache Hadoop Kaart verminderen

- Gebruik de map Reduce compute context alleen als u een onoverkomelijk probleem tegenkomt met de Spark compute-context, omdat deze over het algemeen langzamer is.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Inline help op rxSetComputeContext
Zie voor meer informatie en voorbeelden van RevoScaleR-rekencontexten de inline help in R op de rxSetComputeContext-methode, bijvoorbeeld:

    > ?rxSetComputeContext

U ook verwijzen naar het [overzicht van Distributed computing](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) in machine learning [server-documentatie.](https://docs.microsoft.com/machine-learning-server/)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u meer te weten gekomen over de opties die beschikbaar zijn om aan te geven of en hoe de uitvoering wordt parallellopen tussen kernen van het cluster randknooppunt of HDInsight. Zie de volgende onderwerpen voor meer informatie over het gebruik van ML-services met HDInsight-clusters:

- [Overzicht van ML Services voor Apache Hadoop](r-server-overview.md)
- [Azure-opslagopties voor ML-services op HDInsight](r-server-storage.md)
