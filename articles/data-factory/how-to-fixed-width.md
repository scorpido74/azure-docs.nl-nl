---
title: Tekstbestanden met een vaste lengte verwerken met toewijzingsgegevensstromen in Azure Data Factory
description: Meer informatie over het verwerken van tekstbestanden met een vaste lengte in Azure Data Factory met behulp van toewijzingsgegevensstromen.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: d6885e9b30cc71bda822a29574c4d574f2b020a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72387055"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Tekstbestanden met een vaste lengte verwerken met gegevensstromen in kaartbrengen

Door gegevensstromen in Microsoft Azure Data Factory te toewijzen, u gegevens transformeren uit tekstbestanden met een vaste breedte. In de volgende taak definiëren we een gegevensset voor een tekstbestand zonder scheidingsteken en stellen we subtekenreekssplitsingen in op basis van een ordinale positie.

## <a name="create-a-pipeline"></a>Een pijplijn maken

1. Selecteer **+Nieuwe pijplijn** om een nieuwe pijplijn te maken.

2. Voeg een gegevensstroomactiviteit toe, die wordt gebruikt voor het verwerken van bestanden met een vaste breedte:

    ![Pijplijn met vaste breedte](media/data-flow/fwpipe.png)

3. Selecteer nieuwe **toewijzingsgegevensstroom**in de activiteit gegevensstroom van gegevens .

4. Een bron-, afgeleide kolom-, selectie- en sinktransformatie toevoegen:

    ![Gegevensstroom met vaste breedte](media/data-flow/fw2.png)

5. Configureer de brontransformatie om een nieuwe gegevensset te gebruiken, die van het type Delimited Text is.

6. Stel geen kolomscheidingof-kopteksten in.

   Nu stellen we veldbeginpunten en lengtes in voor de inhoud van dit bestand:

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. Op het tabblad **Projectie** van de brontransformatie ziet u een tekenreekskolom met de naam *Column_1*.

8. Maak in de kolom Afgeleid een nieuwe kolom.

9. We geven de kolommen eenvoudige namen zoals *col1*.

10. Typ in de opbouwfunctie expressie het volgende:

    ```substring(Column_1,1,4)```

    ![afgeleide kolom](media/data-flow/fwderivedcol1.png)

11. Herhaal stap 10 voor alle kolommen die u moet ontlopen.

12. Selecteer het tabblad **Inspecteren** om de nieuwe kolommen te bekijken die worden gegenereerd:

    ![Inspecteren](media/data-flow/fwinspect.png)

13. Gebruik de transformatie Selecteren om een van de kolommen te verwijderen die u niet nodig hebt voor transformatie:

    ![transformatie selecteren](media/data-flow/fwselect.png)

14. Gebruik Sink om de gegevens naar een map uit te zetten:

    ![vaste breedte gootsteen](media/data-flow/fwsink.png)

    Zo ziet de uitvoer eruit:

    ![uitvoer met vaste breedte](media/data-flow/fxdoutput.png)

  De gegevens met een vaste breedte worden nu gesplitst, met vier tekens elk en toegewezen aan Col1, Col2, Col3, Col4, enzovoort. Op basis van het voorgaande voorbeeld worden de gegevens opgesplitst in vier kolommen.

## <a name="next-steps"></a>Volgende stappen

* Bouw de rest van uw logica voor gegevensstromen met behulp van [transformaties](concepts-data-flow-overview.md)van gegevensstromen .
