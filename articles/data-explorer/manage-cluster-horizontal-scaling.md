---
title: Clusterhorizontale schaling (uitschalen) beheren om aan de vraag in Azure Data Explorer te voldoen
description: In dit artikel worden stappen beschreven om uit te schalen en te schalen in een Azure Data Explorer-cluster op basis van veranderende vraag.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: ff7420619cffc2287ab8ff6332df605d56329549
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664130"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>Clusterhorizontale schaling (uitschalen) beheren in Azure Data Explorer om aan de veranderende vraag te voldoen

Het op de juiste manier aanpassen van het formaat van een cluster is van cruciaal belang voor de prestaties van Azure Data Explorer. Een statische clustergrootte kan leiden tot onderbenutting of overmatig gebruik, waarvan geen van beide ideaal is. Omdat de vraag op een cluster niet met absolute nauwkeurigheid kan worden voorspeld, is het beter om een cluster te *schalen,* capaciteit en CPU-resources toe te voegen en te verwijderen met veranderende vraag. 

Er zijn twee werkstromen voor het schalen van een Azure Data Explorer-cluster: 
* Horizontale schaling, ook wel schalen in en uit genoemd.
* [Verticale schaling](manage-cluster-vertical-scaling.md), ook wel op en neer schalen genoemd.
In dit artikel wordt de horizontale schalingswerkstroom uitgelegd.

## <a name="configure-horizontal-scaling"></a>Horizontale schaling configureren

Door horizontale schaling te gebruiken, u het aantal instance's automatisch schalen op basis van vooraf gedefinieerde regels en schema's. Ga als volgt te werk om de instellingen voor automatisch schalen voor uw cluster op te geven:

1. Ga in de Azure-portal naar uw Azure Data Explorer-clusterbron. Selecteer **onder Instellingen**De optie **Uitschalen**. 

2. Selecteer **in het** venster Uitschalen de gewenste methode voor automatisch schalen: **Handmatigschalen,** **Geoptimaliseerde automatische schaal**of Aangepaste automatische **schaal**.

### <a name="manual-scale"></a>Handmatigschalen

Handmatigschalen is de standaardinstelling tijdens het maken van het cluster. Het cluster heeft een statische capaciteit die niet automatisch verandert. U selecteert de statische capaciteit met de **telbalk van de instantie.** De schaling van het cluster blijft op die instelling totdat u een andere wijziging aanbrengt.

   ![Handmatige schaalmethode](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale-preview"></a>Geoptimaliseerde automatische schaal (voorbeeld)

Geoptimaliseerde automatische schaal is de aanbevolen methode voor automatisch schalen. Deze methode optimaliseert de clusterprestaties en -kosten. Als het cluster een status van onderbenutting nadert, wordt het ingeschaald. Deze actie verlaagt de kosten, maar houdt het prestatieniveau. Als het cluster een status van overgebruik nadert, wordt het uitgekopt om optimale prestaties te behouden. Ga als u de automatische schaal voor geoptimaliseerde instellingen wilt configureren:

1. Selecteer **Geoptimaliseerde automatische schaal**. 

1. Selecteer een minimum aantal instanties en een maximaal aantal instanties. De clusterschaalbereiken tussen deze twee getallen, op basis van belasting.

1. Selecteer **Opslaan**.

   ![Geoptimaliseerde automatische schaalmethode](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

Geoptimaliseerde automatische schaal begint te werken. De acties zijn nu zichtbaar in het Azure-activiteitenlogboek van het cluster.

#### <a name="logic-of-optimized-autoscale"></a>Logica van geoptimaliseerde automatische schaal 

**Uitschalen**

Wanneer uw cluster een staat van overmatig gebruik nadert, schaalt u uit om optimale prestaties te behouden. Scale out zal plaatsvinden wanneer:
* Het aantal clusterexemplaren ligt onder het maximum aantal exemplaren dat door de gebruiker is gedefinieerd.
* Het cachegebruik is meer dan een uur hoog.
* De CPU is hoog voor meer dan een uur.
* Het innamegebruik is meer dan een uur hoog.

> [!NOTE]
> De schaal-outlogica houdt momenteel geen rekening met de statistiek van het innamegebruik. Als deze statistiek belangrijk is voor uw use case, gebruikt u [aangepaste automatische schaal](#custom-autoscale).

**Inschalen**

Wanneer uw cluster een staat van onderbenutting nadert, schaalt u in om de kosten te verlagen, maar de prestaties te behouden. Er worden meerdere statistieken gebruikt om te controleren of het veilig is om te schalen in het cluster. De volgende regels worden elk uur geëvalueerd gedurende 6 uur voordat de schaal wordt uitgevoerd:
* Het aantal exemplaren ligt boven 2 en hoger dan het minimumaantal gedefinieerde exemplaren.
* Om ervoor te zorgen dat resources niet overbelast raken, moeten de volgende statistieken worden geverifieerd voordat de schaal wordt uitgevoerd: 
    * Het cachegebruik is niet hoog
    * CPU is onder het gemiddelde 
    * Inname gebruik is onder het gemiddelde 
    * Streaming ingest gebruik (als streaming ingest wordt gebruikt) is niet hoog
    * Keep alive gebeurtenissen zijn boven een bepaald minimum, goed verwerkt, en op tijd.
    * Geen querybeperking 
    * Het aantal mislukte query's ligt onder een gedefinieerd minimum.

> [!NOTE]
> De schaal in logica vereist momenteel een evaluatie van 7 dagen voordat de optimale schaal wordt geïmplementeerd. Deze evaluatie vindt eens in de 24 uur plaats. Als een snelle wijziging nodig is, gebruikt u [handmatige schaal.](#manual-scale)

### <a name="custom-autoscale"></a>Aangepaste automatische schaal

Met aangepaste automatische schaal u uw cluster dynamisch schalen op basis van statistieken die u opgeeft. In de volgende afbeelding ziet u de stroom en stappen om aangepaste automatische schaal te configureren. Meer details volgen de afbeelding.

1. Voer in het **vak Naam van de instelling Automatisch schalen** een naam in, zoals *Uitschalen: cachegebruik*. 

   ![Schaalregel](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. Selecteer **Bij Schaalmodus**de optie **Schalen op basis van een statistiek**. Deze modus zorgt voor dynamische schaling. U ook Schalen selecteren **naar een specifiek aantal instanties.**

3. Selecteer **+ Voeg een regel toe**.

4. Voer in de sectie **Schaalregel** aan de rechterkant waarden in voor elke instelling.

    **Criteria**

    | Instelling | Beschrijving en waarde |
    | --- | --- |
    | **Tijdverzameling** | Selecteer een aggregatiecriteria, zoals **Gemiddeld**. |
    | **Naam van meetwaarde** | Selecteer de statistiek waarop de schaalbewerking moet worden gebaseerd, zoals **Cachegebruik.** |
    | **Tijdsintervalstatistieken** | Kies tussen **Gemiddeld**, **Minimum**, **Maximum**en **Som**. |
    | **Operator** | Kies de juiste optie, zoals **Groter dan of gelijk aan**. |
    | **Drempel** | Kies een geschikte waarde. Voor cachegebruik is 80 procent bijvoorbeeld een goed uitgangspunt. |
    | **Duur (in minuten)** | Kies een geschikte hoeveelheid tijd voor het systeem om terug te kijken bij het berekenen van statistieken. Begin met de standaardwaarde van 10 minuten. |
    |  |  |

    **Actie**

    | Instelling | Beschrijving en waarde |
    | --- | --- |
    | **Bewerking** | Kies de juiste optie om in of uit te schalen. |
    | **Instantietelling** | Kies het aantal knooppunten of instanties dat u wilt toevoegen of verwijderen wanneer aan een metrische voorwaarde is voldaan. |
    | **Afkoelen (minuten)** | Kies een geschikt tijdsinterval om te wachten tussen schaalbewerkingen. Begin met de standaardinstelling van vijf minuten. |
    |  |  |

5. Selecteer **Toevoegen**.

6. Voer in de sectie **Instantielimieten** aan de linkerkant waarden in voor elke instelling.

    | Instelling | Beschrijving en waarde |
    | --- | --- |
    | **Minimum** | Het aantal exemplaren dat uw cluster hieronder niet schaalt, ongeacht het gebruik. |
    | **Maximum** | Het aantal exemplaren dat uw cluster niet hierboven schaalt, ongeacht het gebruik. |
    | **Standaard** | Het standaardaantal exemplaren. Deze instelling wordt gebruikt als er problemen zijn met het lezen van de resourcestatistieken. |
    |  |  |

7. Selecteer **Opslaan**.

U hebt nu horizontale schaling geconfigureerd voor uw Azure Data Explorer-cluster. Voeg een andere regel toe voor verticaal schalen. Als u hulp nodig hebt bij clusterschalingsproblemen, [opent u een ondersteuningsaanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) in de Azure-portal.

## <a name="next-steps"></a>Volgende stappen

* [Prestaties, status en gebruik van Azure Data Explorer bewaken met statistieken](using-metrics.md)
* [Clusterverticale schaling beheren](manage-cluster-vertical-scaling.md) voor de juiste grootte van een cluster.
