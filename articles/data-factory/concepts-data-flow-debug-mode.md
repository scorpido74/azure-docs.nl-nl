---
title: Foutfoutmodus gegevensstroom toewijzen
description: Een interactieve foutopsporingssessie starten bij het bouwen van gegevensstromen
ms.author: makromer
author: kromerm
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/06/2019
ms.openlocfilehash: d5fa34125f1ec712dda3099e2b3596c2566a536d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415535"
---
# <a name="mapping-data-flow-debug-mode"></a>Foutfoutmodus gegevensstroom toewijzen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Overzicht

Met de foutopsporingsmodus van Azure Data Factory u de gegevensvorm interactief bekijken terwijl u uw gegevensstromen bouwt en debugt. De foutopsporingssessie kan zowel worden gebruikt in dataflow-ontwerpsessies als tijdens de uitvoering van gegevensstromen voor het debuggen van pijplijnen. Als u de foutopsporingsmodus wilt inschakelen, gebruikt u de knop Foutopsporing gegevensstroom boven aan het ontwerpoppervlak.

![Schuifregelaar Foutopsporing](media/data-flow/debugbutton.png "Schuifregelaar Foutopsporing")

Zodra u de schuifregelaar inschakelt, wordt u gevraagd te selecteren welke integratieruntime-configuratie u wilt gebruiken. Als AutoResolveIntegrationRuntime wordt gekozen, wordt een cluster met acht kernen van algemene rekenkracht met een 60-minuten tijd om te leven gesponnen. Zie [Prestaties van de gegevensstroom](concepts-data-flow-performance.md#increasing-compute-size-in-azure-integration-runtime)voor meer informatie over de uitvoeringstijden van gegevensstromenintegratie .

![IR-selectie debuggen](media/data-flow/debugbutton2.png "IR-selectie debuggen")

Wanneer de foutopsporingsmodus is ingeschakeld, bouwt u interactief uw gegevensstroom samen met een actief Spark-cluster. De sessie wordt gesloten zodra u foutopsporing uitschakelt in Azure Data Factory. U moet op de hoogte zijn van de uurkosten die Azure Databricks in rekening brengt tijdens de tijd dat u de foutopsporingssessie hebt ingeschakeld.

In de meeste gevallen is het een goede gewoonte om uw gegevensstromen in de foutopsporingsmodus te bouwen, zodat u uw bedrijfslogica valideren en uw gegevenstransformaties bekijken voordat u uw werk publiceert in Azure Data Factory. Gebruik de knop Foutopsporing in het pijplijnpaneel om uw gegevensstroom in een pijplijn te testen.

## <a name="cluster-status"></a>De clusterstatus

De clusterstatusindicator boven aan het ontwerpoppervlak wordt groen wanneer het cluster klaar is voor foutopsporing. Als uw cluster al warm is, verschijnt de groene indicator vrijwel direct. Als uw cluster nog niet actief was toen u de foutopsporingsmodus invoert, moet u 5-7 minuten wachten voordat het cluster is opgedraaid. De indicator zal draaien tot zijn klaar.

Wanneer u klaar bent met uw foutopsporing, schakelt u de foutopsporingsoptie uit, zodat uw Azure Databricks-cluster kan worden beëindigd en u niet langer wordt gefactureerd voor foutopsporingsactiviteiten.

## <a name="debug-settings"></a>Foutopsporingsinstellingen

Foutopsporingsinstellingen kunnen worden bewerkt door op Foutopsporinginstellingen te klikken op de werkbalk Gegevensstroomcanvas. U hier de rijlimiet of bestandsbron selecteren die u voor elk van uw brontransformaties wilt gebruiken. De rijlimieten in deze instelling zijn alleen voor de huidige foutopsporingssessie. U ook de gekoppelde service met tijdelijke bestanden selecteren die moet worden gebruikt voor een SQL DW-bron. 

![Foutopsporingsinstellingen](media/data-flow/debug-settings.png "Foutopsporingsinstellingen")

Als u parameters in uw gegevensstroom of een van de gegevenssets waarnaar wordt verwezen, u opgeven welke waarden u moet gebruiken tijdens het foutopsporing door het tabblad **Parameters** te selecteren.

![Parameters voor foutopsporingsinstellingen](media/data-flow/debug-settings2.png "Parameters voor foutopsporingsinstellingen")

## <a name="data-preview"></a>Voorbeeld van gegevens

Als foutopsporing is ingeschakeld, wordt het tabblad Gegevensvoorbeeld oplicht op het onderste paneel. Zonder de foutopsporingsmodus wordt u alleen de huidige metagegevens in en uit elk van uw transformaties weergegeven op het tabblad Inspect. In het voorbeeld van gegevens wordt alleen het aantal rijen opgevraagd dat u als limiet hebt ingesteld in de foutopsporingsinstellingen. Klik **op Vernieuwen** om het voorbeeld van gegevens op te halen.

![Voorbeeld van gegevens](media/data-flow/datapreview.png "Voorbeeld van gegevens")

> [!NOTE]
> Bestandsbronnen beperken alleen de rijen die u ziet, niet de rijen die worden gelezen. Voor zeer grote gegevenssets is het raadzaam om een klein deel van dat bestand te nemen en het te gebruiken voor het testen. U een tijdelijk bestand selecteren in Foutopsporingsinstellingen voor elke bron die een bestandsgegevenssettype is.

Wanneer u de foutopsporingsmodus uitvoert in gegevensstroom, worden uw gegevens niet naar de transformatie Van de gootsteen geschreven. Een Foutopsporingssessie is bedoeld om te dienen als testharnas voor uw transformaties. Sinks zijn niet vereist tijdens debuggen en worden genegeerd in uw gegevensstroom. Als u wilt testen of de gegevens in uw gootsteen zijn, voert u de gegevensstroom uit een Azure Data Factory Pipeline uit en gebruikt u de uitvoering van foutopsporing vanuit een pijplijn.

### <a name="testing-join-conditions"></a>Testen join voorwaarden

Wanneer de transformaties joins, Exists of Lookup-transformaties van eenheden worden getest, moet u ervoor zorgen dat u een kleine set bekende gegevens voor uw test gebruikt. U de optie Foutopsporingsinstellingen hierboven gebruiken om een tijdelijk bestand in te stellen dat u wilt gebruiken voor het testen. Dit is nodig omdat u bij het beperken of bemonsteren van rijen uit een grote gegevensset niet voorspellen welke rijen en welke toetsen in de stroom worden gelezen om te testen. Het resultaat is niet-deterministisch, wat betekent dat uw join voorwaarden kunnen mislukken.

### <a name="quick-actions"></a>Snelle acties

Zodra u het voorbeeld van gegevens ziet, u een snelle transformatie genereren naar tekstcast, verwijderen of een wijziging uitvoeren in een kolom. Klik op de kolomkop en selecteer een van de opties op de werkbalk van de gegevensvoorbeeld.

![Snelle acties](media/data-flow/quick-actions1.png "Snelle acties")

Zodra u een wijziging selecteert, wordt de gegevensvoorbeeld onmiddellijk vernieuwd. Klik **op Bevestigen** in de rechterbovenhoek om een nieuwe transformatie te genereren.

![Snelle acties](media/data-flow/quick-actions2.png "Snelle acties")

**Typecast** en **Wijzigen** genereren een afgeleide kolomtransformatie en **Verwijderen** genereert een Select-transformatie.

![Snelle acties](media/data-flow/quick-actions3.png "Snelle acties")

> [!NOTE]
> Als u uw gegevensstroom bewerkt, moet u het voorbeeld van gegevens opnieuw ophalen voordat u een snelle transformatie toevoegt.

### <a name="data-profiling"></a>Gegevensprofilering

Als u een kolom selecteert op het tabblad Gegevensvoorbeeld en op **Statistieken** klikt op de werkbalk gegevensvoorbeeld, verschijnt een grafiek rechts van uw gegevensraster met gedetailleerde statistieken over elk veld. Azure Data Factory zal een bepaling maken op basis van de gegevensbemonstering van welk type grafiek moet worden weergegeven. Hoogdinaliteitsvelden worden standaard weergegeven in NULL/NIET NULL-diagrammen, terwijl categorische en numerieke gegevens met een lage kardinaliteit staafdiagrammen weergeven met de frequentie van de gegevenswaarde. U ziet ook de maximale/lenlengte van tekenreeksvelden, min/max-waarden in numerieke velden, standaarddev, percentiels, tellingen en gemiddelde.

![Kolomstatistieken](media/data-flow/stats.png "Kolomstatistieken")

## <a name="next-steps"></a>Volgende stappen

* Zodra u klaar bent met het bouwen en debuggen van uw gegevensstroom, [voert u deze uit vanuit een pijplijn.](control-flow-execute-data-flow-activity.md)
* Wanneer u uw pijplijn test met een gegevensstroom, gebruikt u de [uitvoeringsoptie Foutopsporingsprogramma pijplijn.](iterative-development-debugging.md)
