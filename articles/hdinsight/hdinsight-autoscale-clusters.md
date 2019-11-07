---
title: Automatisch schalen van Azure HDInsight-clusters
description: De functie voor automatisch schalen van Azure HDInsight gebruiken om clusters Apache Hadoop schalen
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: fff5ad379aa11a0aae14b33f9f82f6da9c794517
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643732"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Automatisch schalen van Azure HDInsight-clusters

> [!Important]
> De functie voor automatisch schalen werkt alleen voor Spark-, Hive-en MapReduce-clusters die zijn gemaakt na 8 mei 2019. 

Met de functie voor automatisch schalen van Azure HDInsight wordt het aantal worker-knoop punten in een cluster omhoog en omlaag geschaald. Andere typen knoop punten in het cluster kunnen momenteel niet worden geschaald.  Tijdens het maken van een nieuw HDInsight-cluster kunnen er mini maal en Maxi maal aantal worker-knoop punten worden ingesteld. Automatisch schalen bewaakt vervolgens de resource vereisten van de analyse belasting en schaalt het aantal worker-knoop punten omhoog of omlaag. Er zijn geen extra kosten verbonden aan deze functie.

## <a name="cluster-compatibility"></a>Cluster compatibiliteit

In de volgende tabel worden de cluster typen en versies beschreven die compatibel zijn met de functie voor automatisch schalen.

| Versie | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3,6 zonder ESP | Ja | Ja | Nee | Nee | Nee | Nee | Nee |
| HDInsight 4,0 zonder ESP | Ja | Ja | Nee | Nee | Nee | Nee | Nee |
| HDInsight 3,6 met ESP | Ja | Ja | Nee | Nee | Nee | Nee | Nee |
| HDInsight 4,0 met ESP | Ja | Ja | Nee | Nee | Nee | Nee | Nee |

## <a name="how-it-works"></a>Hoe werkt het?

U kunt schalen op basis van een werk belasting of schalen op basis van een planning voor uw HDInsight-cluster kiezen. Schalen op basis van een belasting wijzigt het aantal knoop punten in het cluster, binnen een bereik dat u instelt, om ervoor te zorgen dat het CPU-gebruik optimaal verloopt en de lopende kosten worden geminimaliseerd.

Schalen op basis van een schema wijzigt het aantal knoop punten in uw cluster op basis van voor waarden die van kracht worden op specifieke tijdstippen. Deze voor waarden schalen het cluster naar een gewenst aantal knoop punten.

### <a name="metrics-monitoring"></a>Bewaking van metrische gegevens

Automatisch schalen bewaakt het cluster voortdurend en verzamelt de volgende metrische gegevens:

* **Totale CPU in behandeling**: het totale aantal kern geheugens dat nodig is om de uitvoering van alle in behandeling zijnde containers te starten.
* **Totaal geheugen in behandeling**: het totale geheugen (in MB) dat is vereist om de uitvoering van alle in behandeling zijnde containers te starten.
* **Totale vrije CPU**: de som van alle ongebruikte kernen op de actieve worker-knoop punten.
* **Totaal vrij geheugen**: de som van het ongebruikte geheugen (in MB) op de actieve worker-knoop punten.
* **Gebruikt geheugen per knoop punt**: de belasting van een worker-knoop punt. Een worker-knoop punt waarop 10 GB geheugen wordt gebruikt, wordt beschouwd als meer belasting dan een werk nemer met 2 GB gebruikt geheugen.
* **Aantal toepassings Masters per knoop punt**: het aantal toepassings Master-containers dat wordt uitgevoerd op een worker-knoop punt. Een worker-knoop punt dat fungeert als host voor twee AM-containers, wordt beschouwd als belang rijker dan een worker-knoop punt dat wordt gehost op nul AM-containers.

De bovenstaande metrische gegevens worden elke 60 seconden gecontroleerd. Met automatisch schalen kunt u op basis van deze metrische gegevens schalen en omlaag schalen.

### <a name="load-based-cluster-scale-up"></a>Scale-up van cluster op basis van belasting

Wanneer aan de volgende voor waarden wordt voldaan, wordt door automatisch schalen een opschaal aanvraag uitgegeven:

* De totale CPU in behandeling is groter dan de totale vrije CPU gedurende meer dan drie minuten.
* Totaal geheugen in behandeling is groter dan het totale beschik bare geheugen gedurende meer dan drie minuten.

De HDInsight-service berekent hoeveel nieuwe worker-knoop punten er nodig zijn om aan de huidige CPU-en geheugen vereisten te voldoen en geeft vervolgens een opschaal aanvraag uit om het vereiste aantal knoop punten toe te voegen.

### <a name="load-based-cluster-scale-down"></a>Schaal van cluster op basis van belasting-omlaag

Wanneer aan de volgende voor waarden wordt voldaan, wordt door automatisch schalen een aanvraag voor omlaag schalen uitgegeven:

* De totale beschik bare CPU is minder dan de totale vrije CPU voor meer dan 10 minuten.
* Totaal geheugen in behandeling is minder dan het totale beschik bare geheugen gedurende meer dan 10 minuten.

Op basis van het aantal AM-containers per knoop punt en de huidige CPU-en geheugen vereisten, wordt met automatisch schalen een aanvraag voor het verwijderen van een bepaald aantal knoop punten opgelost. De service detecteert ook welke knoop punten kandidaten zijn voor verwijdering op basis van de huidige taak uitvoering. Met de bewerking omlaag schalen worden de knoop punten eerst buiten gebruik gesteld en vervolgens uit het cluster verwijderd.

## <a name="get-started"></a>Aan de slag

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Een cluster maken met automatisch schalen op basis van een werk belasting

Als u automatisch schalen wilt gebruiken op een cluster, moet de optie **automatisch schalen inschakelen** zijn ingeschakeld wanneer het cluster wordt gemaakt. 

Als u de functie voor automatisch schalen wilt inschakelen met schalen op basis van een werk belasting, voert u de volgende stappen uit als onderdeel van het normale proces voor het maken van het cluster:

1. Schakel op het tabblad **configuratie en prijzen** het selectie vakje **automatisch schalen inschakelen** in.
1. Selecteer **op belasting gebaseerd** onder **type automatisch schalen**.
1. Voer de gewenste waarden in voor de volgende eigenschappen:  

    * Oorspronkelijk **aantal knoop punten** voor **worker-knoop punt**.
    * **Min** . aantal worker-knoop punten.
    * **Maximum** aantal worker-knoop punten.

    ![Automatisch schalen op basis van werk knooppunten inschakelen](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

Het eerste aantal worker-knoop punten moet liggen tussen het minimum en het maximum, inclusief. Met deze waarde wordt de oorspronkelijke grootte van het cluster gedefinieerd wanneer het wordt gemaakt. Het minimum aantal worker-knoop punten moet worden ingesteld op drie of meer. . Het schalen van uw cluster naar minder dan drie knoop punten kan ertoe leiden dat de veilige modus vastloopt vanwege onvoldoende bestands replicatie. Zie [vastzitten in de veilige modus]( https://docs.microsoft.com/ azure/hdinsight/hdinsight-scaling-best-practices#getting-stuck-in-safe-mode) voor meer informatie.

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Een cluster maken met automatisch schalen op basis van een planning

Als u de functie voor automatisch schalen wilt inschakelen met schalen op basis van een schema, voert u de volgende stappen uit als onderdeel van het normale proces voor het maken van het cluster:

1. Schakel op het tabblad **configuratie en prijzen** het selectie vakje **automatisch schalen inschakelen** in.
1. Voer het **aantal knoop punten** in voor het **worker-knoop punt**, waarmee de limiet voor het omhoog schalen van het cluster wordt bepaald.
1. Selecteer het optie **schema op basis van** het **type automatisch schalen**.
1. Klik op **configureren** om het venster **configuratie automatisch schalen** te openen.
1. Selecteer uw tijd zone en klik vervolgens op **+ voor waarde toevoegen**
1. Selecteer de dagen van de week waarop de nieuwe voor waarde moet worden toegepast.
1. Bewerk het tijdstip waarop de voor waarde van kracht moet zijn en het aantal knoop punten waaraan het cluster moet worden geschaald.
1. Voeg indien nodig meer voor waarden toe.

    ![Maken van werk knooppunt plannen toestaan](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Het aantal knoop punten moet tussen 3 en het maximum aantal worker-knoop punten zijn dat u hebt ingevoerd voordat voor waarden worden toegevoegd.

### <a name="final-creation-steps"></a>Laatste aanmaak stappen

Voor zowel op de werk belasting gebaseerd schalen als op schema gebaseerd, selecteert u het VM-type voor worker-knoop punten door een virtuele machine te selecteren in de vervolg keuzelijst onder **knooppunt grootte**. Nadat u het VM-type voor elk knooppunt type hebt gekozen, ziet u het geschatte kosten bereik voor het hele cluster. Pas de VM-typen aan uw budget aan.

![Knooppunt grootte voor automatisch schalen van werk knooppunten inschakelen](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Uw abonnement heeft een capaciteits quotum voor elke regio. Het totale aantal kernen van uw hoofd knooppunten in combi natie met het maximum aantal worker-knoop punten mag het capaciteits quotum niet overschrijden. Dit quotum is echter een zachte limiet; u kunt altijd een ondersteunings ticket maken om het probleem te verhelpen.

> [!Note]  
> Als u het totale aantal kern quotum overschrijdt, wordt er een fout bericht weer gegeven met de melding dat het maximum knooppunt de beschik bare kernen in deze regio heeft overschreden. Kies een andere regio of neem contact op met de ondersteuning om het quotum te verhogen.

Zie op [Linux gebaseerde clusters maken in HDInsight met behulp van de Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md)voor meer informatie over het maken van HDInsight-clusters met behulp van de Azure Portal.  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Een cluster maken met een resource manager-sjabloon

#### <a name="load-based-autoscaling"></a>Automatisch schalen op basis van een werk belasting

U kunt een HDInsight-cluster maken met behulp van automatisch schalen op basis van een Azure Resource Manager sjabloon door een `autoscale`-knoop punt toe te voegen aan de sectie `computeProfile` > `workernode` met de eigenschappen `minInstanceCount` en `maxInstanceCount`, zoals wordt weer gegeven in het JSON-code fragment hieronder.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

Zie [Apache Hadoop clusters in HDInsight maken met behulp van Resource Manager-sjablonen](hdinsight-hadoop-create-linux-clusters-arm-templates.md)voor meer informatie over het maken van clusters met Resource Manager-sjablonen.  

#### <a name="schedule-based-autoscaling"></a>Automatisch schalen op basis van een planning

U kunt een HDInsight-cluster maken met behulp van automatisch schalen op basis van een planning Azure Resource Manager sjabloon door een `autoscale`-knoop punt toe te voegen aan de sectie `computeProfile` > `workernode`. Het knoop punt `autoscale` bevat een `recurrence` met een `timezone` en `schedule` die beschrijft wanneer de wijziging wordt doorgevoerd.

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Automatisch schalen voor een actief cluster in-en uitschakelen

#### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Selecteer **cluster grootte** onder **instellingen**om automatisch schalen in te scha kelen op een actief cluster. Klik vervolgens op **automatisch schalen inschakelen**. Selecteer het gewenste type automatisch schalen en voer de opties in voor schalen op basis van de belasting of op basis van een planning. Klik ten slotte op **Opslaan**.

![Op schema gebaseerde automatische schaal aanpassing van werk knooppunten inschakelen](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

#### <a name="using-the-rest-api"></a>Met behulp van de REST API

Als u automatisch schalen op een actief cluster wilt in-of uitschakelen met behulp van de REST API, maakt u een POST-aanvraag naar het eind punt voor automatisch schalen, zoals wordt weer gegeven in het code fragment hieronder:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Gebruik de juiste para meters in de aanvraag lading. De onderstaande JSON-nettolading kan worden gebruikt om automatisch schalen in te scha kelen. Gebruik de payload `{autoscale: null}` om automatisch schalen uit te scha kelen.

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

Zie de vorige sectie over het [inschakelen van automatisch schalen op basis](#load-based-autoscaling) van een werk belasting voor een volledige beschrijving van alle Payload-para meters.

## <a name="best-practices"></a>Aanbevolen procedures

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Schalen op basis van een werk belasting of op basis van een planning kiezen

Houd rekening met de volgende factoren voordat u besluit welke modus u moet kiezen:

* Automatisch schalen inschakelen tijdens het maken van het cluster.
* Het minimum aantal knoop punten moet mini maal drie zijn.
* Afwijking van belasting: de belasting van het cluster volgt een consistent patroon op specifieke tijdstippen op specifieke dagen. Als dat niet het geval is, is planning op basis van belasting een betere optie.
* SLA-vereisten: automatisch schalen schalen is reactief in plaats van voorspellend. Is er voldoende vertraging tussen het moment waarop de belasting begint te verhogen en wanneer het cluster de doel grootte moet hebben? Als er strikte SLA-vereisten zijn en de belasting een vast bekend patroon is, is ' planning gebaseerd ' een betere optie.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Houd rekening met de latentie van omhoog of omlaag schalen van bewerkingen

Het kan 10 tot 20 minuten duren voordat een schaal bewerking is voltooid. Wanneer u een aangepaste planning instelt, moet u deze vertraging plannen. Als u de grootte van het cluster bijvoorbeeld 20 om 9:00 uur nodig hebt, stelt u de plannings trigger in op een eerder tijdstip, bijvoorbeeld 8:30, zodat de schaal bewerking is voltooid door 9:00 uur.

### <a name="preparation-for-scaling-down"></a>Voor bereiding voor Horizon taal schalen

Tijdens het omlaag schalen van een cluster worden de knoop punten buiten gebruik gesteld om te voldoen aan de doel grootte. Als er taken op deze knoop punten worden uitgevoerd, wordt automatisch schalen gewacht totdat de taken zijn voltooid. Omdat elk worker-knoop punt ook een rol in HDFS heeft, worden de tijdelijke gegevens verplaatst naar de resterende knoop punten. Zorg er daarom voor dat er voldoende ruimte is op de resterende knoop punten om alle tijdelijke gegevens te hosten.

De actieve taken blijven worden uitgevoerd en voltooid. De taken die in behandeling zijn, wachten om te worden gepland als normaal met minder beschik bare worker-knoop punten.

### <a name="minimum-cluster-size"></a>Minimale cluster grootte

Schaal uw cluster niet op minder dan drie knoop punten. Het schalen van uw cluster naar minder dan drie knoop punten kan ertoe leiden dat de veilige modus vastloopt vanwege onvoldoende bestands replicatie. Zie [vastzitten in de veilige modus]( https://docs.microsoft.com/ azure/hdinsight/hdinsight-scaling-best-practices#getting-stuck-in-safe-mode) voor meer informatie.

## <a name="monitoring"></a>Bewaking

### <a name="cluster-status"></a>De clusterstatus

De cluster status die in de Azure Portal wordt weer gegeven, kan u helpen bij het controleren van de activiteiten voor automatisch schalen.

![De cluster status automatisch schalen van werk knooppunten inschakelen](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Alle cluster status berichten die u mogelijk ziet, worden beschreven in de onderstaande lijst.

| De clusterstatus | Uitleg |
|---|---|
| In uitvoering | Het cluster werkt normaal. Alle vorige activiteiten voor automatisch schalen zijn voltooid. |
| Dating  | De configuratie van het automatisch schalen van clusters wordt bijgewerkt.  |
| HDInsight-configuratie  | Een cluster omhoog of omlaag schalen wordt uitgevoerd.  |
| Fout bij het bijwerken  | HDInsight heeft problemen aangetroffen tijdens het automatisch schalen van de configuratie. Klanten kunnen ervoor kiezen om de update opnieuw uit te voeren of automatisch schalen uit te scha kelen.  |
| Fout  | Er is iets mis met het cluster en het is niet bruikbaar. Verwijder dit cluster en maak een nieuwe.  |

Als u het huidige aantal knoop punten in uw cluster wilt weer geven, gaat u naar het diagram **cluster grootte** op de pagina **overzicht** voor uw cluster of klikt u op **cluster grootte** onder **instellingen**.

### <a name="operation-history"></a>Bewerkings geschiedenis

U kunt de geschiedenis van het cluster omhoog en omlaag schalen als onderdeel van de metrische gegevens van het cluster. U kunt ook alle schaal acties voor de afgelopen dag, week of andere periode weer geven.

Selecteer **metrische gegevens** onder **bewaking**. Klik vervolgens op **metrische gegevens** en **aantal actieve werk** rollen toevoegen in de vervolg keuzelijst **metriek** . Klik op de knop in de rechter bovenhoek om het tijds bereik te wijzigen.

![Metriek voor automatisch schalen op basis van worker-knoop punten inschakelen](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over best practices voor het schalen van clusters hand matig in [Aanbevolen procedures voor schalen](hdinsight-scaling-best-practices.md)
