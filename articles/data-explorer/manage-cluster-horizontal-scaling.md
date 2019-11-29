---
title: Horizon taal schalen van het cluster beheren (uitschalen) in azure Data Explorer voor het wijzigen van de vraag
description: In dit artikel worden de stappen beschreven voor het uitschalen en schalen in een Azure Data Explorer-cluster op basis van het wijzigen van de vraag.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: eb204701b42436a5ae95bac97ed6fd97cf272860
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561867"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>Horizon taal schalen van het cluster beheren (uitschalen) in azure Data Explorer voor het wijzigen van de vraag

Het formaat van een cluster op de juiste wijze is essentieel voor de prestaties van Azure Data Explorer. De grootte van een statisch cluster kan leiden tot minder gebruik of over meer gebruiks mogelijkheden.

Omdat de vraag naar een cluster niet met absolute nauw keurigheid kan worden voor speld, is het beter om een cluster te *schalen* , capaciteit en CPU-resources toe te voegen en te verwijderen met veranderende vraag. 

Er zijn twee werk stromen voor het schalen van een Azure Data Explorer-cluster: 

* Horizon taal schalen, ook wel in-en uitschalen genoemd.
* [Verticaal schalen](manage-cluster-vertical-scaling.md), ook wel omhoog en omlaag schalen genoemd.

In dit artikel wordt de werk stroom voor Horizon taal schalen uitgelegd.

## <a name="configure-horizontal-scaling"></a>Horizon taal schalen configureren

Als u horizon taal schalen gebruikt, kunt u het aantal exemplaren automatisch schalen op basis van vooraf gedefinieerde regels en schema's. De instellingen voor automatisch schalen voor uw cluster opgeven:

1. Ga in het Azure Portal naar de cluster bron van Azure Data Explorer. Onder **instellingen**selecteert u **uitschalen**. 

2. Selecteer in het venster **uitschalen** de gewenste methode voor automatisch schalen: **hand matig schalen**, **geoptimaliseerd**automatisch schalen of **aangepast automatisch schalen**.

### <a name="manual-scale"></a>Hand matig schalen

Hand matig schalen is de standaard instelling tijdens het maken van het cluster. Het cluster heeft een statische capaciteit die niet automatisch wordt gewijzigd. U selecteert de statische capaciteit met behulp van de **instantie aantal exemplaren** . De schaal van het cluster blijft in die instelling totdat u een andere wijziging aanbrengt.

   ![Methode hand matig schalen](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale-preview"></a>Geoptimaliseerd automatisch schalen (preview)

Geoptimaliseerd automatisch schalen is de aanbevolen methode voor automatisch schalen. Deze methode optimaliseert de prestaties en kosten van het cluster. Als het cluster de status onder gebruik nadert, wordt het geschaald in. Deze actie verlaagt de kosten, maar houdt het prestatie niveau bij. Als het cluster de status over het gebruik nadert, wordt het uitgeschaald om optimale prestaties te behouden. Geoptimaliseerde automatisch schalen configureren:

1. Selecteer **geoptimaliseerde automatisch schalen**. 

1. Selecteer een minimum aantal instanties en een maximum aantal exemplaren. De automatische schaal aanpassing van het cluster tussen de twee getallen, op basis van de belasting.

1. Selecteer **Opslaan**.

   ![Geoptimaliseerde methode voor automatisch schalen](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

Geoptimaliseerd automatisch schalen wordt gestart. De bijbehorende acties zijn nu zichtbaar in het Azure-activiteiten logboek van het cluster.

### <a name="custom-autoscale"></a>Aangepaste automatisch schalen

Met aangepaste automatisch schalen kunt u uw cluster dynamisch schalen op basis van de metrische gegevens die u opgeeft. In de volgende afbeelding ziet u de stroom en de stappen voor het configureren van aangepaste automatisch schalen. Raadpleeg de afbeelding voor meer informatie.

1. Voer in het vak **instellings naam automatisch schalen** een naam in, zoals *uitschalen: cache gebruik*. 

   ![Schaal regel](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. Voor **schaal modus**selecteert u **schalen op basis van een metriek**. Deze modus biedt dynamische schaling. U kunt ook **schalen naar een specifiek aantal instanties**selecteren.

3. Selecteer **+ een regel toevoegen**.

4. Voer in de sectie **schaal regel** aan de rechter kant waarden in voor elke instelling.

    **Criteria**

    | Instelling | Beschrijving en waarde |
    | --- | --- |
    | **Tijd aggregatie** | Selecteer een aggregatie criterium, zoals **gemiddeld**. |
    | **Metrische naam** | Selecteer de metrische gegevens waarop u de schaal bewerking wilt baseren, zoals het **cache gebruik**. |
    | **Statistieken voor tijd korrels** | Kies een **gemiddelde**, een **minimum**, een **maximum**en een **som**. |
    | **And** | Kies de gewenste optie, zoals **groter dan of gelijk aan**. |
    | **Spreek** | Kies een geschikte waarde. Bijvoorbeeld: voor cache gebruik is 80 procent een goed uitgangs punt. |
    | **Duur (in minuten)** | Kies een geschikte hoeveelheid tijd om het systeem terug te laten kijken bij het berekenen van metrische gegevens. Begin met de standaard waarde van 10 minuten. |
    |  |  |

    **Actie**

    | Instelling | Beschrijving en waarde |
    | --- | --- |
    | **Bewerking** | Kies de gewenste optie om in of uit te schalen. |
    | **Aantal exemplaren** | Kies het aantal knoop punten of exemplaren dat u wilt toevoegen of verwijderen wanneer aan een voor waarde voor een metriek wordt voldaan. |
    | **Afkoelen (minuten)** | Kies een geschikt tijds interval om te wachten tussen schaal bewerkingen. Begin met de standaard waarde van vijf minuten. |
    |  |  |

5. Selecteer **Toevoegen**.

6. Voer in de sectie **limieten** van het exemplaar aan de linkerkant waarden in voor elke instelling.

    | Instelling | Beschrijving en waarde |
    | --- | --- |
    | **Maal** | Het aantal exemplaren dat door uw cluster niet kan worden geschaald, ongeacht het gebruik. |
    | **Gehalte** | Het aantal exemplaren dat door uw cluster niet kan worden geschaald, ongeacht het gebruik. |
    | **Standaard** | Het standaard aantal exemplaren. Deze instelling wordt gebruikt als er problemen zijn met het lezen van de metrische gegevens van de resource. |
    |  |  |

7. Selecteer **Opslaan**.

U hebt nu horizon taal schalen geconfigureerd voor uw Azure Data Explorer-cluster. Voeg nog een regel toe voor verticaal schalen. Als u hulp nodig hebt bij problemen met cluster schaal, [opent u een ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) in de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

* [Prestaties, status en gebruik van Azure Data Explorer controleren met metrische gegevens](using-metrics.md)

* [Verticaal schalen van clusters beheren](manage-cluster-vertical-scaling.md) voor de juiste grootte van een cluster.
