---
title: Azure HDInsight-clusters automatisch schalen
description: Gebruik de functie Azure HDInsight Autoscale om automatisch Apache Hadoop-schaalclusters te gebruiken
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 4f9b43b6f800bb47942ccc00fee0fac4536d2ec0
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640585"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Azure HDInsight-clusters automatisch schalen

> [!Important]
> De Azure HDInsight Autoscale-functie is op 7 november 2019 uitgebracht voor algemene beschikbaarheid voor Spark- en Hadoop-clusters en bevat verbeteringen die niet beschikbaar zijn in de preview-versie van de functie. Als u vóór 7 november 2019 een Spark-cluster hebt gemaakt en de functie Autoscale op uw cluster wilt gebruiken, is het aanbevolen pad om een nieuw cluster te maken en Automatisch schalen in het nieuwe cluster in te schakelen.
>
> Autoscale for Interactive Query (LLAP) en HBase clusters is nog steeds in preview. Autoscale is alleen beschikbaar op spark-, hadoop-, interactieve query- en HBase-clusters.

De clusterautoscale-functie van Azure HDInsight schaalt automatisch het aantal werknemersknooppunten in een cluster op en neer. Andere typen knooppunten in het cluster kunnen momenteel niet worden geschaald.  Tijdens het maken van een nieuw HDInsight-cluster kan een minimum- en maximumaantal werknemersknooppunten worden ingesteld. Autoscale controleert vervolgens de resourcevereisten van de analysebelasting en schaalt het aantal werknemersknooppunten omhoog of omlaag. Er zijn geen extra kosten verbonden aan deze functie.

## <a name="cluster-compatibility"></a>Clustercompatibiliteit

In de volgende tabel worden de clustertypen en -versies beschreven die compatibel zijn met de functie Automatisch schalen.

| Versie | Spark | Hive | LLAP LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 zonder ESP | Ja | Ja | Ja | Ja* | Nee | Nee | Nee |
| HDInsight 4.0 zonder ESP | Ja | Ja | Ja | Ja* | Nee | Nee | Nee |
| HDInsight 3.6 met ESP | Ja | Ja | Ja | Ja* | Nee | Nee | Nee |
| HDInsight 4.0 met ESP | Ja | Ja | Ja | Ja* | Nee | Nee | Nee |

\*HBase-clusters kunnen alleen worden geconfigureerd voor op schema's gebaseerde schaling, niet op basis van belasting.

## <a name="how-it-works"></a>Hoe werkt het?

U op lasten gebaseerde schaling of op planning gebaseerde schaling kiezen voor uw HDInsight-cluster. Op basis van laden schaalvergroting wijzigt het aantal knooppunten in uw cluster, binnen een bereik dat u instelt, om een optimaal CPU-gebruik te garanderen en de bedrijfskosten te minimaliseren.

Op planning gebaseerde schaling wijzigt het aantal knooppunten in uw cluster op basis van voorwaarden die op specifieke tijdstippen van kracht worden. Deze voorwaarden schalen het cluster naar een voorgenomen aantal knooppunten.

### <a name="metrics-monitoring"></a>Metrische gegevens

Autoscale bewaakt het cluster continu en verzamelt de volgende statistieken:

|Gegevens|Beschrijving|
|---|---|
|Totaal in behandeling in CPU|Het totale aantal cores dat nodig is om de uitvoering van alle in behandeling zijnde containers te starten.|
|Totaal in behandeling in levenzijnd geheugen|Het totale geheugen (in MB) dat nodig is om de uitvoering van alle in behandeling zijnde containers te starten.|
|Totaal gratis CPU|De som van alle ongebruikte kernen op de actieve werkknooppunten.|
|Totaal gratis geheugen|De som van ongebruikt geheugen (in MB) op de actieve werkknooppunten.|
|Gebruikt geheugen per knooppunt|De belasting op een werknemersknooppunt. Een werkknooppunt waarop 10 GB geheugen wordt gebruikt, wordt beschouwd als onder meer belasting dan een werknemer met 2 GB gebruikt geheugen.|
|Aantal toepassingsmasters per knooppunt|Het aantal Am-containers (Application Master) dat op een werknemersknooppunt wordt uitgevoerd. Een werknemersknooppunt dat twee AM-containers host, wordt als belangrijker beschouwd dan een werknemersknooppunt dat nul AM-containers host.|

De bovenstaande statistieken worden elke 60 seconden gecontroleerd. Autoscale neemt beslissingen op basis van deze statistieken.

### <a name="load-based-scale-conditions"></a>Op belasting gebaseerde schaalvoorwaarden

Wanneer de volgende voorwaarden worden gedetecteerd, geeft Autoscale een schaalaanvraag uit:

|Omhoog schalen|Schaal-omlaag|
|---|---|
|Totaal in behandeling CPU is groter dan de totale gratis CPU voor meer dan 3 minuten.|Totaal in behandeling CPU is minder dan de totale gratis CPU voor meer dan 10 minuten.|
|Totaal in behandeling geheugen is groter dan de totale gratis geheugen voor meer dan 3 minuten.|Totaal in behandeling geheugen is minder dan de totale gratis geheugen voor meer dan 10 minuten.|

Voor scale-up geeft Autoscale een scale-upaanvraag uit om het vereiste aantal knooppunten toe te voegen. De scale-up is gebaseerd op het aantal nieuwe worker nodes dat nodig is om te voldoen aan de huidige CPU- en geheugenvereisten.

Voor scale-down geeft Autoscale een verzoek uit om een bepaald aantal knooppunten te verwijderen. De scale-down is gebaseerd op het aantal AM-containers per knooppunt. En de huidige CPU en geheugen eisen. De service detecteert ook welke knooppunten kandidaten zijn voor verwijdering op basis van de huidige taakuitvoering. De schaaldown-bewerking ontmantelt eerst de knooppunten en verwijdert ze vervolgens uit het cluster.

## <a name="get-started"></a>Aan de slag

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Een cluster maken met automatisch schalen op basis van laden

Voer de volgende stappen uit als onderdeel van het normale proces voor het maken van een cluster:

1. Schakel op het tabblad **Configuratie + prijzen** het selectievakje Automatisch schalen **inschakelen** in.
1. Selecteer **Load-based** onder **type Autoscale**.
1. Voer de beoogde waarden in voor de volgende eigenschappen:  

    * **Initieel aantal knooppunten** voor **worker node**.
    * **Min** aantal werkknooppunten.
    * **Maximaal** aantal werknemersknooppunten.

    ![Automatische schaal voor werknemersknooppunten inschakelen](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

Het initiële aantal werknemersknooppunten moet tussen het minimum en het maximum, inclusief, vallen. Met deze waarde wordt de oorspronkelijke grootte van het cluster gedefinieerd wanneer het wordt gemaakt. Het minimumaantal werknemersknooppunten moet worden ingesteld op drie of meer. Als u uw cluster schaalt naar minder dan drie knooppunten, kan dit ertoe leiden dat het in de veilige modus vastkomt te zitten vanwege onvoldoende bestandsreplicatie.  Zie [Vast komen komen zitten in de veilige modus](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)voor meer informatie.

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Een cluster maken met op planning gebaseerde automatisch schalen

Voer de volgende stappen uit als onderdeel van het normale proces voor het maken van een cluster:

1. Schakel op het tabblad **Configuratie + prijzen** het selectievakje Automatisch schalen **inschakelen** in.
1. Voer het aantal knooppunten voor **worker node**in, waarmee de limiet voor het opschalen **van** het cluster wordt ingesteld.
1. Selecteer de optie **Schema op basis van** type **Autoscale**.
1. Selecteer **Configureren** om het **configuratievenster Automatisch schalen te** openen.
1. Selecteer uw tijdzone en klik op **+ Voorwaarde toevoegen**
1. Selecteer de dagen van de week waarop de nieuwe voorwaarde moet worden toegepast.
1. Bewerk de tijd waarop de voorwaarde van kracht moet worden en het aantal knooppunten waarop het cluster moet worden geschaald.
1. Voeg indien nodig meer voorwaarden toe.

    ![Creatie op basis van werknemersknooppuntplannen inschakelen](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Het aantal knooppunten moet tussen 3 en het maximum aantal werknemersknooppunten liggen dat u hebt ingevoerd voordat u voorwaarden toevoegt.

### <a name="final-creation-steps"></a>Laatste creatiestappen

Selecteer het VM-type voor werknemersknooppunten door een VM te selecteren in de vervolgkeuzelijst onder **Knooppuntgrootte**. Nadat u het VM-type voor elk knooppunttype hebt gekozen, u het geschatte kostenbereik voor het hele cluster zien. Pas de VM-typen aan uw budget aan.

![Grootte van het werkknooppunt op basis van een automatisch schaalknooppunt inschakelen](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Uw abonnement heeft een capaciteitsquotum voor elke regio. Het totale aantal kernen van uw hoofdknooppunten en de maximale werkknooppunten mogen het capaciteitsquotum niet overschrijden. Dit quotum is echter een zachte limiet; u altijd een ondersteuningsticket maken om het gemakkelijk te verhogen.

> [!Note]  
> Als u de totale kernquotumlimiet overschrijdt, ontvangt u een foutbericht met de tekst 'het maximale knooppunt heeft de beschikbare kernen in deze regio overschreden, kies dan een andere regio of neem contact op met de ondersteuning om het quotum te verhogen.'

Zie Clusters op basis van Linux maken [in HDInsight met behulp van de Azure-portal](hdinsight-hadoop-create-linux-clusters-portal.md)voor meer informatie over het maken van HDInsight-clusters.  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Een cluster maken met een resourcemanagersjabloon

#### <a name="load-based-autoscaling"></a>Automatisch schalen op basis van laden

U een HDInsight-cluster maken met een op laden gebaseerde Sjabloon Voor automatisch schalen van een Azure Resource Manager- sjabloon, door een `autoscale` knooppunt toe te voegen `computeProfile`  >  `workernode` aan de sectie met de eigenschappen `minInstanceCount` en `maxInstanceCount` zoals weergegeven in het onderstaande JSON-fragment.

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

#### <a name="schedule-based-autoscaling"></a>Automatisch schalen op basis van planning

U een HDInsight-cluster maken met op planning gebaseerde `autoscale` Autoscaling-sjabloon `computeProfile`  >  `workernode` voor automatisch schalen door een knooppunt aan de sectie toe te voegen. Het `autoscale` knooppunt bevat `recurrence` een `timezone` die `schedule` een en dat beschrijft wanneer de wijziging zal plaatsvinden.

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

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Automatisch schalen in- en uitschakelen voor een lopend cluster

#### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Als u Automatisch schalen op een lopend cluster wilt inschakelen, selecteert u **Clustergrootte** onder **Instellingen**. Selecteer vervolgens **Automatisch schalen inschakelen**. Selecteer het gewenste type autoschaal en voer de opties in voor op belasting gebaseerde of op planning gebaseerde schaling. Selecteer tot slot **Opslaan**.

![Automatisch uitvoerencluster op basis van werknemerknooppuntplanning inschakelen](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>Met behulp van de REST API

Als u Automatisch schalen op een actief cluster wilt in- of uitschakelen met de REST-API, voert u een POST-verzoek in bij het eindpunt Automatisch schalen:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Gebruik de juiste parameters in het laadvermogen van het verzoek. De json payload hieronder kan worden gebruikt om Autoscale in te schakelen. Gebruik de `{autoscale: null}` payload om Autoscale uit te schakelen.

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

Zie de vorige sectie over [het inschakelen van op belasting gebaseerde autoscale](#load-based-autoscaling) voor een volledige beschrijving van alle payload parameters.

## <a name="guidelines"></a>Richtlijnen

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Op basis van laden of op planning gebaseerde schalen kiezen

Houd rekening met de volgende factoren voordat u een beslissing neemt over welke modus u moet kiezen:

* Autoscale inschakelen tijdens het maken van het cluster.
* Het minimum aantal knooppunten moet ten minste drie zijn.
* Verschilvariantie van belasting: volgt de belasting van het cluster een consistent patroon op specifieke tijdstippen, op specifieke dagen. Zo niet, dan is load based scheduling een betere optie.
* SLA-vereisten: Automatisch schalen is reactief in plaats van voorspellend. Zal er voldoende vertraging zijn tussen het moment waarop de belasting begint te stijgen en wanneer het cluster op de doelgrootte moet zijn? Als er strenge SLA-vereisten zijn en de belasting een vast bekend patroon is, is 'schedule based' een betere optie.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Houd rekening met de latentie van scale-up- of schaalbewerkingen

Het kan 10 tot 20 minuten duren voordat een schalingbewerking is voltooid. Plan bij het instellen van een aangepast schema deze vertraging. Als u bijvoorbeeld de clustergrootte om 20 uur nodig hebt, stelt u de planningstrigger in op een eerdere tijd, zoals 08:30 uur, zodat de schaalbewerking om 9:00 uur is voltooid.

### <a name="preparation-for-scaling-down"></a>Voorbereiding op afschaling

Tijdens het clusterschalingproces zal Autoscale de knooppunten ontmantelen om de doelgrootte te bereiken. Als taken op deze knooppunten worden uitgevoerd, wacht Autoscale totdat de taken zijn voltooid. Aangezien elk werknemersknooppunt ook een rol in HDFS vervult, worden de tijdelijke gegevens verplaatst naar de resterende knooppunten. Zorg er dus voor dat er voldoende ruimte is op de resterende knooppunten om alle tijdelijke gegevens te hosten.

De lopende taken zullen worden voortgezet. De lopende taken wachten op planning met minder beschikbare werknemersknooppunten.

### <a name="minimum-cluster-size"></a>Minimale clustergrootte

Schaal uw cluster niet op minder dan drie knooppunten. Als u uw cluster schaalt naar minder dan drie knooppunten, kan dit ertoe leiden dat het in de veilige modus vastkomt te zitten vanwege onvoldoende bestandsreplicatie.  Zie [Vast komen komen zitten in de veilige modus](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)voor meer informatie.

## <a name="monitoring"></a>Bewaking

### <a name="cluster-status"></a>De clusterstatus

Met de clusterstatus in de Azure-portal u de activiteiten van Autoscale controleren.

![Status van taakknooppunt op basis van automatisch schalen inschakelen](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Alle clusterstatusberichten die u mogelijk ziet, worden uitgelegd in de onderstaande lijst.

| De clusterstatus | Beschrijving |
|---|---|
| In uitvoering | Het cluster werkt normaal. Alle eerdere Autoscale-activiteiten zijn voltooid. |
| Bijwerken  | De configuratie van de clusterautoscale wordt bijgewerkt.  |
| HDInsight-configuratie  | Er wordt gewerkt aan een clusterscale-up of schaalbewerking.  |
| Updatefout  | HDInsight kwam problemen tegen tijdens de autoscale-configuratie-update. Klanten kunnen ervoor kiezen om de update opnieuw te proberen of automatisch schalen uit te schakelen.  |
| Fout  | Er is iets mis met het cluster, en het is niet bruikbaar. Verwijder dit cluster en maak een nieuw cluster.  |

Als u het huidige aantal knooppunten in uw cluster wilt weergeven, gaat u naar de grafiek **Clustergrootte** op de pagina **Overzicht** voor uw cluster. Of selecteer **Clustergrootte** onder **Instellingen**.

### <a name="operation-history"></a>De geschiedenis van de verrichting

U de clusterscale-up- en scale-downgeschiedenis bekijken als onderdeel van de clusterstatistieken. U ook alle schalingsacties van de afgelopen dag, week of andere periode weergeven.

Selecteer **Statistieken** onder **Controleren**. Selecteer vervolgens Metric en **Aantal actieve werknemers** **toevoegen** in het vervolgkeuzeveld **Metrische.** Selecteer de knop rechtsboven om het tijdsbereik te wijzigen.

![Statistiek voor automatische schaal van werknemersknooppuntinschakelen](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>Volgende stappen

Lees meer over richtlijnen voor het handmatig schalen van clusters in [Richtlijnen schalen](hdinsight-scaling-best-practices.md)
