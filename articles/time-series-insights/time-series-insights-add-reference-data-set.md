---
title: Referentiegegevenssets toevoegen aan uw omgeving - Azure Time Series Insights | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u een referentiegegevensset toevoegt om gegevens uit te breiden in uw Azure Time Series Insights-omgeving.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18~~~~
ms.openlocfilehash: 79628ed44753577023464ef6208027e1b7996d30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087258"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Een referentiegegevensset maken voor uw Time Series Insights-omgeving met behulp van de Azure-portal

In dit artikel wordt beschreven hoe u een referentiegegevensset toevoegt aan uw Azure Time Series Insights-omgeving. Referentiegegevens zijn handig om samen te werken met uw brongegevens om de waarden uit te breiden.

Een referentiegegevensset is een verzameling items die de gebeurtenissen uit uw gebeurtenisbron vergroten. Time Series Insights ingress engine voegt elke gebeurtenis van uw gebeurtenisbron toe met de bijbehorende gegevensrij in uw referentiegegevensset. Deze uitgebreide gebeurtenis is vervolgens beschikbaar voor query’s. Deze join is gebaseerd op de kolom(s) primaire sleutel die is gedefinieerd in uw referentiegegevensset.

Referentiegegevens worden niet met terugwerkende kracht samengevoegd. Zo worden alleen huidige en toekomstige binnenkomende gegevens afgestemd en gekoppeld aan de referentiedatum die is ingesteld, zodra deze zijn geconfigureerd en geüpload.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Meer informatie over het referentiegegevensmodel van Time Series Insight.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Een referentiegegevensset toevoegen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Zoek uw bestaande Azure Time Series Insights-omgeving. Selecteer **Alle bronnen** in het menu aan de linkerkant van de Azure-portal. Selecteer uw Time Series Insights-omgeving.

1. Selecteer de **overzichtspagina.** Vouw de sectie **Essentials** boven aan de pagina uit om de URL van **Time Series Insights explorer** te zoeken en de koppeling te openen.  

   [![Sectie Essentiële stoffen uitvouwen](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   Bekijk de verkenner voor uw Time Series Insights-omgeving.

1. Vouw de omgevingskiezer uit in de Time Series Insights-verkenner. Kies de actieve omgeving. Selecteer het pictogram referentiegegevens rechtsboven op de verkennerspagina.

   [![Referentiegegevens toevoegen](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. Selecteer de knop **+ Een gegevensset toevoegen** om een nieuwe gegevensset toe te voegen.

   [![Gegevensset toevoegen](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. Kies op de pagina **Nieuwe referentiegegevensset** de indeling van de gegevens:

   - Kies **CSV** voor gegevens met beperkte gegevens. De eerste rij wordt behandeld als een koptekstrij.
   - Kies **JSON Array** voor Json-objectnotatiegegevens (Json) van JavaScript-objectnotatie.

   [![Kies gegevensindeling.](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. Geef de gegevens op met behulp van een van de twee methoden:

   - Plak de gegevens in de teksteditor. Selecteer vervolgens de knop **Parse-referentiegegevens.**
   - Selecteer **Knop Bestand kiezen** om gegevens uit een lokaal tekstbestand toe te voegen.

   CSV-gegevens plakken: [ ![GEPLAKTe CSV-gegevens](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   Json-arraygegevens plakken: [ ![JSON-gegevens plakken](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   Als er een fout optreedt bij het ontzeggen van de gegevenswaarden, `CSV parsing error, no rows extracted`wordt de fout onder aan de pagina in het rood weergegeven, zoals .

1. Zodra de gegevens zijn ontleed, wordt een gegevensraster weergegeven met de kolommen en rijen die de gegevens vertegenwoordigen. Controleer het gegevensraster om de juistheid te garanderen.

   [![Referentiegegevens controleren](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. Controleer elke kolom om inzicht te krijgen in het veronderstelde gegevenstype en wijzig het gegevenstype indien nodig.  Selecteer het symbool van het **#** gegevenstype in de kolomkop: voor dubbele (numerieke gegevens), **T| F** voor booleaan, of **Abc** voor string.

   [![Kies gegevenstypen in de kolomkoppen.](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. Wijzig indien nodig de naam van de kolomkoppen. De naam van de sleutelkolom is nodig om deel te nemen aan de bijbehorende eigenschap in uw gebeurtenisbron. 

   > [!IMPORTANT]
   > Zorg ervoor dat de kolomnamen van de referentiegegevensexact exact overeenkomen met de gebeurtenisnaam met uw binnenkomende gegevens, inclusief hoofdlettergevoeligheid. De niet-sleutelkolomnamen worden gebruikt om de binnenkomende gegevens uit te breiden met de bijbehorende referentiegegevenswaarden.

1. Typ een waarde in het veld **De rijen filteren...** om specifieke rijen te bekijken als dat nodig is. Het filter is handig voor het controleren van gegevens, maar wordt niet toegepast bij het uploaden van de gegevens.

1. Geef de gegevensset een naam door het veld Naam van de **gegevensset** boven het gegevensraster in te vullen.

    [![Geef de gegevensset een naam.](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. Geef de kolom **Primaire sleutel** op in de gegevensset door de vervolgkeuzelijst boven het gegevensraster te selecteren.

    [![Selecteer de toetskolom(en).](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(Optioneel)** Selecteer **+** de knop om een secundaire toetskolom toe te voegen als een samengestelde primaire sleutel. Als u de selectie ongedaan moet maken, kiest u de lege waarde in de vervolgkeuzelijst om de secundaire toets te verwijderen.

1. Als u de gegevens wilt uploaden, selecteert u de knop **Rijen uploaden.**

    [![Rijen uploaden en gegevens bevestigen.](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    De pagina bevestigt de voltooide upload en geeft het bericht **met succes geüploade gegevensset**.

    > [!WARNING]
    > Kolommen of eigenschappen die worden gedeeld tussen referentiegegevenssets, geven een fout in het uploaden van de **eigenschapDubbele eigenschapsnaam** weer. De fout voorkomt niet dat de referentiegegevenssets succesvol worden geüpload. Het kan worden verwijderd door rijen te combineren die de gedupliceerde eigenschapsnaam delen.

1. Selecteer **Een rij toevoegen**, Rijen voor **bulkimporteren**of **Een kolom toevoegen** om indien nodig meer referentiegegevenswaarden toe te voegen.

    [![Voeg een rij, Bulkimportrijen of Een kolom toevoegen toe.](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > Elke rij die een unieke sleutel deelt met een andere rij, heeft zijn kolommen die worden overschreven door de laatste rij die wordt toegevoegd en die unieke sleutel deelt.

   > [!NOTE]
   > Toegevoegde rijen **hoeven niet** *rechthoekig* te zijn - ze kunnen minder, grotere of verschillende kolommen hebben van de andere vermeldingen in de referentiegegevensset.

## <a name="next-steps"></a>Volgende stappen

* Programmatisch [referentiegegevens beheren](time-series-insights-manage-reference-data-csharp.md).

* Lees voor de volledige API-verwijzing [Api-document.](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api)
