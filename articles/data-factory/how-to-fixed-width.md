---
title: Tekst bestanden met een vaste lengte verwerken met toewijzing van gegevens stromen in Azure Data Factory
description: Meer informatie over het verwerken van tekst bestanden met een vaste lengte in Azure Data Factory met toewijzing van gegevens stromen.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: e1ba09f459152616941071c23f7a6545ec2a8b73
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210674"
---
# <a name="process-fixed-length-text-files-using-data-factory-mapping-data-flows"></a>Tekst bestanden met een vaste lengte verwerken met Data Factory gegevens stromen toewijzen

Data Factory toewijzing van gegevens stromen ondersteunt transformatie gegevens van tekst bestanden met een vaste breedte. U definieert een gegevensset voor een tekst bestand zonder scheidings teken en stelt vervolgens subtekenreeksen in op basis van de positie van het rang telwoord.

## <a name="create-a-pipeline"></a>Een pijplijn maken

1. Ga naar **+ nieuwe pijp lijn** om een nieuwe pijp lijn te starten

2. Een gegevens stroom activiteit toevoegen die wordt gebruikt voor het verwerken van bestanden met een vaste breedte

  ![Pijp lijn met vaste breedte](media/data-flow/fwpipe.png)

3. Selecteer in de activiteit gegevens stroom de optie nieuwe toewijzings gegevens stroom

4. Een bron transformatie, afgeleide kolom, selecteren en Sink-trans formatie toevoegen

  ![Gegevens stroom met vaste breedte](media/data-flow/fw2.png)

5. De bron transformatie configureren voor het gebruik van een nieuwe gegevensset die van het type scheidings teken is

6. Geen kolom scheidings teken en geen kopteksten instellen

We gaan gewoon het begin punt en de lengte van het veld instellen voor de inhoud van deze bestanden:

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

7. Op het tabblad projectie van de bron transformatie ziet u één teken reeks kolom met de naam ' Column_1 '

8. Maak nu een nieuwe kolom in de afgeleide kolom

9. We geven de kolommen eenvoudige namen zoals Kol1

10. Typ in de opbouw functie voor expressies vervolgens:

  ```substring(Column_1,1,4)```

  ![afgeleide kolom](media/data-flow/fwderivedcol1.png)

10. Herhaal dit voor alle kolommen die u moet parseren

12. Klik op het tabblad controleren om de nieuwe kolommen te zien die worden gegenereerd

  ![controleert](media/data-flow/fwinspect.png)

13. Gebruik de Select Transform om een van de kolommen te verwijderen die u niet nodig hebt voor trans formatie

  ![trans formatie selecteren](media/data-flow/fwselect.png)

14. Gebruik tot slot Sink om de gegevens naar een map uit te voeren:

  ![Sink met vaste breedte](media/data-flow/fwsink.png)

  De uitvoer ziet er als volgt uit:

  ![uitvoer met een vaste breedte](media/data-flow/fxdoutput.png)

  De gegevens met een vaste breedte worden nu gesplitst met vier tekens en worden toegewezen aan Kol1, col2, Col3, Col4,... Op basis van het bovenstaande voor beeld worden de gegevens in vier kolommen gesplitst

## <a name="next-steps"></a>Volgende stappen

* Bouw de rest van uw gegevensstroom logica met gegevens stroom transformaties toewijzen [](concepts-data-flow-overview.md)
