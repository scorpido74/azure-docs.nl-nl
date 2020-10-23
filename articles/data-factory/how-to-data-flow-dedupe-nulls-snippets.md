---
title: De duplicaten van rijen opsporen en Null-waarden zoeken met behulp van gegevens stroom fragmenten
description: Meer informatie over het eenvoudig ontdubbelen van rijen en het vinden van Null-waarden met behulp van code fragmenten in gegevens stromen
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 1c630cdd66fa4f8e609524feb9c3f0bcad9711a0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458163"
---
# <a name="dedupe-rows-and-find-nulls-by-using-data-flow-snippets"></a>De duplicaten van rijen opsporen en Null-waarden zoeken met behulp van gegevens stroom fragmenten

[!INCLUDE [appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Door code fragmenten te gebruiken bij het toewijzen van gegevens stromen, kunt u eenvoudig veelvoorkomende taken uitvoeren, zoals gegevensontdubbeling en null-filtering. In dit artikel wordt uitgelegd hoe u deze functies eenvoudig kunt toevoegen aan uw pijp lijnen met behulp van gegevens stroom script fragmenten.
<br>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>Een pijplijn maken

1. Selecteer **nieuwe pijp lijn**.

1. Voeg een gegevens stroom activiteit toe.

1. Selecteer het tabblad **bron instellingen** , voeg een bron transformatie toe en verbind deze vervolgens met een van de gegevens sets.

    ![Scherm afbeelding van het deel venster Bron instellingen voor het toevoegen van een bron type.](media/data-flow/snippet-adf-2.png)

    De controle fragmenten die worden ontdubbeld en NULL gebruiken generieke patronen die gebruikmaken van het gegevens stroom schema drift. De fragmenten werken met elk schema uit uw gegevensset of met gegevens sets die geen vooraf gedefinieerd schema hebben.

1. Kopieer in de sectie ' DISTINCT Row using all columns ' van de [Data flow script (DFS)](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)het code fragment voor distinctrows.

1. Selecteer in de gebruikers interface van de ontwerp functie voor gegevens stromen de knop **script** in de rechter bovenhoek om de Script Editor achter de gegevens stroom grafiek te openen.

    ![Scherm opname van een bron fragment.](media/data-flow/snippet-adf-3.png)

1. In uw script, na de definitie voor `source1` , druk op ENTER en plak het code fragment.

1. Gebruik een van de volgende methoden:

   * Verbind dit geplakte code fragment met de bron transformatie die u eerder in de grafiek hebt gemaakt door **source1** vóór de geplakte code te typen.

   * U kunt ook de nieuwe trans formatie in de ontwerp functie verbinden door de inkomende stroom te selecteren in het nieuwe transformatie knooppunt in de grafiek.

     ![Scherm afbeelding van het deel venster instellingen voor voorwaardelijke splitsing.](media/data-flow/snippet-adf-4.png)

   De gegevens stroom verwijdert nu dubbele rijen uit uw bron door gebruik te maken van de geaggregeerde trans formatie, die door alle rijen wordt gegroepeerd met behulp van een algemene hash voor alle kolom waarden.
    
1. Voeg een code fragment toe voor het splitsen van uw gegevens in één stream die rijen met Null-waarden en een andere stroom zonder null-waarden bevat. Hiervoor doet u het volgende:

   a. Ga terug naar de [fragment bibliotheek](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns)en kopieer de code voor de null-controles.

   b. Selecteer in de ontwerp functie voor gegevens stromen opnieuw **script** en plak vervolgens deze nieuwe transformatie code onderaan. Met deze actie verbindt u het script met uw vorige trans formatie door de naam van die trans formatie vóór het geplakte fragment te plaatsen.

   De grafiek van uw gegevens stroom moet er nu ongeveer als volgt uitzien:

    ![Scherm afbeelding van de grafiek van de gegevens stroom.](media/data-flow/snippet-adf-1.png)

  U hebt nu een werk stroom met algemene deduping en null-controles gemaakt door bestaande code fragmenten uit de gegevensstroom script bibliotheek te nemen en toe te voegen aan uw bestaande ontwerp.

## <a name="next-steps"></a>Volgende stappen

* Bouw de rest van uw gegevensstroom logica met behulp van trans [formaties](concepts-data-flow-overview.md)voor gegevens stromen.
