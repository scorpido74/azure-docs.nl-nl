---
title: Ontdubbeling van rijen en zoeken naar Null-waarden met behulp van gegevens stroom fragmenten
description: Meer informatie over hoe u gemakkelijk duplicaten kunt maken van rijen en Null-waarden kunt zoeken met code fragmenten in gegevens stromen
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 683d7ffe5549b86a587cd2dc3c9a86a36aee1bba
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91637248"
---
# <a name="dedupe-rows-and-find-nulls-using-data-flow-snippets"></a>Ontdubbeling van rijen en zoeken naar Null-waarden met behulp van gegevens stroom fragmenten

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Door code fragmenten te gebruiken in gegevens stromen toewijzen, kunt u eenvoudig veelvoorkomende taken uitvoeren zoals gegevensontdubbeling en null-filtering. In deze hand leiding wordt uitgelegd hoe u deze functies eenvoudig kunt toevoegen aan uw pijp lijnen met behulp van gegevens stroom script fragmenten.

## <a name="create-a-pipeline"></a>Een pijplijn maken

1. Selecteer **+ nieuwe pijp lijn** om een nieuwe pijp lijn te maken.

2. Voeg een gegevens stroom activiteit toe.

3. Een bron transformatie toevoegen en deze verbinden met een van de gegevens sets

    ![Bron fragment 2](media/data-flow/snippet-adf-2.png)

4. De controle fragmenten die worden ontdubbeld en NULL gebruiken generieke patronen die gebruikmaken van het schema voor gegevens stromen, zodat ze werken met een wille keurig schema uit uw gegevensset of met gegevens sets die geen vooraf gedefinieerd schema hebben.

5. [Ga naar de documentatie pagina voor gegevens stroom scripts en kopieer het code fragment voor afzonderlijke rijen.](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)

6. Klik in de gebruikers interface van de ontwerp functie voor data flow op de knop script rechtsboven om de script editor te openen achter de gegevens stroom grafiek.

    ![Bron fragment 3](media/data-flow/snippet-adf-3.png)

7. Klik na de definitie van ```source1``` in het script op ENTER en plak vervolgens in het code fragment.

8. U gaat dit geplakte code fragment verbinden met de vorige bron transformatie die u in de grafiek hebt gemaakt door ' source1 ' vóór de geplakte code te typen.

9. U kunt ook de nieuwe trans formatie in de ontwerp functie verbinden door de inkomende stroom te selecteren in het nieuwe transformatie knooppunt in de grafiek.

    ![Bron fragment 4](media/data-flow/snippet-adf-4.png)

10. De gegevens stroom verwijdert nu dubbele rijen uit uw bron met behulp van de geaggregeerde trans formatie die door alle rijen wordt gegroepeerd met een algemene hash voor alle kolom waarden.
    
11. Vervolgens voegen we een code fragment toe voor het splitsen van uw gegevens in een stroom met rijen met NULL-waarden en een stroom die geen NULL-waarden bevat.

12. [Ga terug naar de fragment bibliotheek en kopieer de code voor de NULL-controles.](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns)

13. Klik in de ontwerp functie voor gegevens stromen nogmaals op script en plak deze nieuwe transformatie code aan de onderkant door de naam van die trans formatie vóór het geplakte fragment te typen.

14. De grafiek van uw gegevens stroom moet er nu ongeveer als volgt uitzien:

    ![Bron fragment 1](media/data-flow/snippet-adf-1.png)

  U hebt nu een werk stroom met algemene deduping en NULL-controles door bestaande code fragmenten uit de gegevensstroom script bibliotheek te nemen en toe te voegen aan uw bestaande ontwerp.

## <a name="next-steps"></a>Volgende stappen

* Bouw de rest van uw gegevensstroom logica met behulp van trans [formaties](concepts-data-flow-overview.md)voor gegevens stromen.
