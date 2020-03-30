---
title: Clustercapaciteitsplanning in Azure HDInsight
description: Identificeer belangrijke vragen voor capaciteits- en prestatieplanning van een Azure HDInsight-cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 69627c961d9224a124fda09f40901f837d627281
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272641"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Capaciteitsplanning voor HDInsight-clusters

Plan voor de gewenste cluster door de gewenste clustercapaciteit te implementeren door de benodigde prestaties en schaal te bepalen. Deze planning helpt bij het optimaliseren van zowel bruikbaarheid als kosten. Sommige beslissingen over clustercapaciteit kunnen niet worden gewijzigd na implementatie. Als de prestatieparameters veranderen, kan een cluster worden ontmanteld en opnieuw worden gemaakt zonder opgeslagen gegevens te verliezen.

De belangrijkste vragen voor capaciteitsplanning zijn:

* In welke geografische regio moet u uw cluster implementeren?
* Hoeveel opslagruimte heb je nodig?
* Welk clustertype moet u implementeren?
* Welke grootte en type virtuele machine (VM) moeten uw clusterknooppunten gebruiken?
* Hoeveel werkknooppunten moet uw cluster hebben?

## <a name="choose-an-azure-region"></a>Een Azure-gebied kiezen

Het Azure-gebied bepaalt waar uw cluster fysiek is ingericht. Om de latentie van lees- en schrijfbewerkingen te minimaliseren, moet het cluster zich in de buurt van uw gegevens bevinden.

HDInsight is beschikbaar in veel Azure-regio's. Zie [Producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight)als u de dichtstbijzijnde regio wilt vinden.

## <a name="choose-storage-location-and-size"></a>Opslaglocatie en -grootte kiezen

### <a name="location-of-default-storage"></a>Locatie van de standaardopslag

De standaardopslag, een Azure Storage-account of Azure Data Lake Storage, moet zich op dezelfde locatie bevinden als uw cluster. Azure Storage is beschikbaar op alle locaties. Data Lake Storage Gen1 is beschikbaar in sommige regio's - zie de huidige [beschikbaarheid van Data Lake Storage](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

### <a name="location-of-existing-data"></a>Locatie van bestaande gegevens

Als u al een opslagaccount of Data Lake Storage hebt met uw gegevens en deze opslag wilt gebruiken als de standaardopslag van uw cluster, moet u uw cluster op dezelfde locatie implementeren.

### <a name="storage-size"></a>Opslaggrootte

Nadat u een HDInsight-cluster hebt geïmplementeerd, u aanvullende Azure Storage-accounts toevoegen of toegang krijgen tot andere Gegevensmeeropslag. Al uw opslagaccounts moeten zich op dezelfde locatie bevinden als uw cluster. Een Data Lake Storage kan zich op een andere locatie bevinden, hoewel dit een aantal lees-/schrijflatentie voor gegevens kan introduceren.

Azure Storage heeft een aantal [capaciteitslimieten,](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)terwijl Data Lake Storage Gen1 vrijwel onbeperkt is.

Een cluster heeft toegang tot een combinatie van verschillende opslagaccounts. Typische voorbeelden zijn:

* Wanneer de hoeveelheid gegevens waarschijnlijk de opslagcapaciteit van één blobopslagcontainer zal overschrijden.
* Wanneer de toegangssnelheid tot de blobcontainer de drempelwaarde kan overschrijden waar beperking optreedt.
* Wanneer u gegevens wilt maken, hebt u al geüpload naar een blobcontainer die beschikbaar is voor het cluster.
* Wanneer u verschillende delen van de opslag wilt isoleren om veiligheidsredenen of het beheer wilt vereenvoudigen.

Gebruik voor betere prestaties slechts één container per opslagaccount.

## <a name="choose-a-cluster-type"></a>Een clustertype kiezen

Het clustertype bepaalt de werkbelasting die uw HDInsight-cluster moet uitvoeren, zoals [Apache Hadoop,](https://hadoop.apache.org/) [Apache Storm](https://storm.apache.org/), Apache [Kafka](https://kafka.apache.org/)of [Apache Spark.](https://spark.apache.org/) Zie [Inleiding tot Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight)voor een gedetailleerde beschrijving van de beschikbare clustertypen. Elk clustertype heeft een specifieke implementatietopologie die vereisten bevat voor de grootte en het aantal knooppunten.

## <a name="choose-the-vm-size-and-type"></a>De vm-grootte en -type kiezen

Elk clustertype heeft een set knooppunttypen en elk knooppunttype heeft specifieke opties voor de grootte en het type VM.

Om de optimale clustergrootte voor uw toepassing te bepalen, u de clustercapaciteit benchmarken en de grootte vergroten zoals aangegeven. U bijvoorbeeld een gesimuleerde werkbelasting of een *kanariequery*gebruiken. Met een gesimuleerde werkbelasting voert u uw verwachte workloads uit op clusters van verschillende grootte, waardoor de grootte geleidelijk toeneemt totdat de gewenste prestaties zijn bereikt. Een kanariequery kan periodiek worden ingevoegd tussen de andere productiequery's om aan te geven of het cluster voldoende resources heeft.

Zie [De juiste VM-grootte voor uw cluster selecteren voor](hdinsight-selecting-vm-size.md)meer informatie over het kiezen van de juiste VM-familie voor uw werkbelasting.

## <a name="choose-the-cluster-scale"></a>De clusterschaal kiezen

De schaal van een cluster wordt bepaald door de hoeveelheid VM-knooppunten. Voor alle clustertypen zijn er knooppunttypen met een specifieke schaal en knooppunttypen die scale-out ondersteunen. Een cluster kan bijvoorbeeld precies drie [Apache ZooKeeper-knooppunten](https://zookeeper.apache.org/) of twee hoofdknooppunten vereisen. Worker-knooppunten die gegevensverwerking op een gedistribueerde manier uitvoeren, kunnen profiteren van uitschaling door extra werknemersknooppunten toe te voegen.

Afhankelijk van uw clustertype voegt het verhogen van het aantal werkknooppunten extra rekencapaciteit toe (zoals meer kernen), maar kan het ook de totale hoeveelheid geheugen toevoegen die nodig is voor het hele cluster om in-memory opslag van gegevens die worden verwerkt, te ondersteunen. Net als bij de keuze van vm-grootte en -type wordt het selecteren van de juiste clusterschaal meestal empirisch bereikt, met behulp van gesimuleerde workloads of kanariequery's.

U uw cluster schalen om te voldoen aan de piekbelastingseisen en het vervolgens weer naar beneden schalen wanneer die extra knooppunten niet meer nodig zijn. Met de [functie Autoscale](hdinsight-autoscale-clusters.md) u uw cluster automatisch schalen op basis van vooraf bepaalde statistieken en timings. Zie Clusters schalen [HDInsight](hdinsight-scaling-best-practices.md)voor meer informatie over het handmatig schalen van uw clusters.

### <a name="cluster-lifecycle"></a>Clusterlevenscyclus

Er worden kosten in rekening gebracht voor de levensduur van een cluster. Als er alleen specifieke tijden zijn waarop uw cluster actief moet worden, u [on-demandclusters maken met Azure Data Factory.](hdinsight-hadoop-create-linux-clusters-adf.md) U ook PowerShell-scripts maken die uw cluster in- en verwijderen en deze scripts vervolgens plannen met [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Wanneer een cluster wordt verwijderd, wordt de standaard Hive-metastore ook verwijderd. Als u de metastore voor het volgende cluster opnieuw wilt maken, gebruikt u een extern metagegevensarchief zoals Azure Database of [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Clustertaakfouten isoleren

Soms kunnen fouten optreden als gevolg van de parallelle uitvoering van meerdere kaarten en het verminderen van componenten op een cluster met meerdere nodes. Als u het probleem wilt isoleren, probeert u gedistribueerde tests uit te voeren door gelijktijdige meerdere taken uit te voeren op een cluster met een knooppunt van één werknemer en deze aanpak vervolgens uit te vouwen om meerdere taken tegelijk uit te voeren op clusters die meer dan één knooppunt bevatten. Als u een HDInsight-cluster met één knooppunt in Azure wilt maken, gebruikt u de optie *Aangepast(grootte, instellingen, apps)* en gebruikt u een waarde van 1 voor *aantal werknemersknooppunten* in de sectie **Clustergrootte** bij het inrichten van een nieuw cluster in de portal.

## <a name="quotas"></a>Quota

Controleer na het bepalen van de vm-grootte, schaal en type van uw doelcluster de huidige limietvoor quotumcapaciteit van uw abonnement. Wanneer u een quotumlimiet bereikt, u mogelijk geen nieuwe clusters implementeren of bestaande clusters schalen door meer werknemersknooppunten toe te voegen. De enige quotumlimiet is het CPU-coresquotum dat op regioniveau voor elk abonnement bestaat. Uw abonnement kan bijvoorbeeld een limiet van 30 cores hebben in de regio Oost-VS. 

Ga als volgt te werk om uw beschikbare cores te controleren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Navigeer naar de **overzichtspagina** voor het HDInsight-cluster. 
3. Klik in het linkermenu op **Quotalimieten**.

   De pagina geeft het aantal gebruikte cores weer, het aantal beschikbare kernen en de totale kernen.

Als u een quotumverhoging moet aanvragen, gaat u als volgt te werk:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Selecteer **Help + ondersteuning** linksonder op de pagina.
1. Selecteer **Nieuw ondersteuningsverzoek**.
1. Selecteer op de pagina **Nieuw ondersteuningsverzoek** onder tabblad **Basisbeginselen** de volgende opties:

   - **Probleemtype**: **Service- en abonnementslimieten (quota)**
   - **Abonnement**: het abonnement dat u wilt wijzigen
   - **Quotumtype**: **HDInsight**

     ![Een ondersteuningsaanvraag maken om het kernquotum van HDInsight te verhogen](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Selecteer **Volgende: oplossingen >>**.
1. Selecteer op de pagina **Details** een beschrijving van het probleem, selecteer de ernst van het probleem, de gewenste contactmethode en andere vereiste velden.
1. Selecteer **Volgende: Controleren + >>maken. **
1. Selecteer op het tabblad **Controleren + maken** de optie **Maken**.

> [!NOTE]  
> Als u het HDInsight-kernquotum in een privéregio wilt verhogen, [dient u een whitelist-aanvraag in.](https://aka.ms/canaryintwhitelist)

U [contact opnemen met ondersteuning om een quotumverhoging aan te vragen.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)

Er zijn echter enkele vaste quotumlimieten, bijvoorbeeld een enkel Azure-abonnement kan maximaal 10.000 cores hebben. Zie [Azure-abonnements- en servicelimieten, quota's en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)voor meer informatie over deze limieten.

## <a name="next-steps"></a>Volgende stappen

* [Clusters instellen in HDInsight met Apache Hadoop, Spark, Kafka en meer:](hdinsight-hadoop-provision-linux-clusters.md)Meer informatie over het instellen en configureren van clusters in HDInsight met Apache Hadoop, Spark, Kafka, Interactive Hive, HBase, ML Services of Storm.
* [Monitor clusterprestaties:](hdinsight-key-scenarios-to-monitor.md)meer informatie over de belangrijkste scenario's die u moet bewaken voor uw HDInsight-cluster die van invloed kunnen zijn op de capaciteit van uw cluster.
