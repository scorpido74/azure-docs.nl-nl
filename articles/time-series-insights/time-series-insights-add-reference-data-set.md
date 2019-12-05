---
title: Referentie gegevens sets toevoegen aan uw omgeving-Azure Time Series Insights | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een set met referentie gegevens kunt toevoegen om gegevens in uw Azure Time Series Insights omgeving uit te breiden.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: seodec18
ms.openlocfilehash: d1bbfb43c6e2319706f5eeac15fa1d60791b62b9
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807212"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Maak een referentie gegevensverzameling voor uw Time Series Insights-omgeving met behulp van de Azure Portal

In dit artikel wordt beschreven hoe u een set met referentie gegevens toevoegt aan uw Azure Time Series Insights omgeving. Referentie gegevens zijn handig om samen te voegen met de bron gegevens om de waarden te verbeteren.

Een referentie gegevensset is een verzameling items waarmee de gebeurtenissen worden uitgebreid van de bron van de gebeurtenis. Time Series Insights ingangs engine koppelt elke gebeurtenis uit de bron van de gebeurtenis met de overeenkomstige gegevensrij in uw referentie gegevensverzameling. Deze uitgebreide gebeurtenis is vervolgens beschikbaar voor query’s. Deze samen voeging is gebaseerd op de kolom (men) van de primaire sleutel die is gedefinieerd in uw referentie gegevensverzameling.

Referentie gegevens worden niet met terugwerkende kracht samengevoegd. Daarom worden alleen huidige en toekomstige ingangs gegevens vergeleken en samengevoegd met de referentie datumset, zodra deze is geconfigureerd en geüpload.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Meer informatie over het referentie gegevens model van Time Series Insight.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Een set met referentie gegevens toevoegen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Zoek uw bestaande Azure Time Series Insights-omgeving. Selecteer **alle resources** in het menu aan de linkerkant van het Azure Portal. Selecteer uw Time Series Insights-omgeving.

1. Selecteer de pagina **overzicht** . Ga naar de **URL van Time Series Insights Explorer** en open de koppeling.  

   Bekijk de Verkenner voor uw Time Series Insights-omgeving.

1. Vouw de omgevings kiezer uit in de Time Series Insights Explorer. Kies de actieve omgeving. Selecteer het pictogram referentie gegevens in de rechter bovenhoek van de Explorer-pagina.

   [Referentie gegevens ![toevoegen](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. Selecteer de knop **+ een gegevensset toevoegen** om een nieuwe gegevensset toe te voegen.

   [gegevensset ![toevoegen](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. Kies op de pagina **nieuwe referentie gegevensgroep** de indeling van de gegevens:

   - Kies **CSV** voor door komma's gescheiden gegevens. De eerste rij wordt beschouwd als een veldnamenrij.
   - Kies **JSON-matrix** voor gegevens in JSON-indeling (Java Script object Notation).

   [![gegevens indeling kiezen.](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. Geef de gegevens op met behulp van een van de twee methoden:

   - Plak de gegevens in de tekst editor. Selecteer vervolgens de knop **referentie gegevens parseren** .
   - Selecteer de knop **bestand kiezen** om gegevens uit een lokaal tekst bestand toe te voegen.

   Plak bijvoorbeeld CSV-gegevens: [![geplakte CSV-gegevens](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   Plak bijvoorbeeld JSON array data: [![JSON-gegevens plakken](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   Als er een fout optreedt bij het parseren van de gegevens waarden, wordt de fout rood weer gegeven aan de onderkant van de pagina, zoals `CSV parsing error, no rows extracted`.

1. Zodra de gegevens zijn geparseerd, wordt een gegevens raster weer gegeven waarin de kolommen en rijen worden weer gegeven die de gegevens vertegenwoordigen.  Controleer het gegevens raster om te controleren of het juist is.

   [Referentie gegevens ![toevoegen](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. Bekijk elke kolom om te zien welk gegevens type wordt gebruikt en wijzig zo nodig het gegevens type.  Selecteer het gegevens type symbool in de kolomkop: **#** voor dubbele (numerieke gegevens), **t | F** voor Booleaanse waarde, of **ABC** voor teken reeks.

   [![gegevens typen kiezen in de kolom koppen.](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. Wijzig de naam van de kolom koppen, indien nodig. De naam van de sleutel kolom is nodig om lid te worden van de bijbehorende eigenschap in de bron van de gebeurtenis. 

   > [!IMPORTANT]
   > Zorg ervoor dat de kolom namen van de referentie gegevens sleutel exact overeenkomen met de naam van de gebeurtenis voor uw binnenkomende gegevens, met inbegrip van hoofdletter gevoeligheid. De niet-sleutel kolom namen worden gebruikt voor het verbeteren van de inkomende gegevens met de bijbehorende referentie gegevens waarden.

1. Typ een waarde in het veld **rijen...** om specifieke rijen naar behoefte te controleren. Het filter is handig voor het controleren van gegevens, maar wordt niet toegepast bij het uploaden van de gegevens.

1. Geef de gegevensset een naam door het veld **naam van gegevensset** boven het gegevens raster in te vullen.

    [![de naam van de gegevensset.](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. Geef de **primaire-sleutel** kolom in de gegevensset op door de vervolg keuzelijst boven het gegevens raster te selecteren.

    [![de sleutel kolom (men) selecteren.](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(Optioneel)** Selecteer de knop **+** om een secundaire sleutel kolom als samengestelde primaire sleutel toe te voegen. Als u de selectie ongedaan wilt maken, kiest u de lege waarde uit de vervolg keuzelijst om de secundaire sleutel te verwijderen.

1. Als u de gegevens wilt uploaden, selecteert u de knop **rijen uploaden** .

    [![het uploaden van rijen en het bevestigen van gegevens.](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    Op de pagina wordt de voltooide upload bevestigd en wordt het bericht met de **gegevensset geüpload**weer gegeven.

    > [!WARNING]
    > Kolommen of eigenschappen die worden gedeeld tussen referentie gegevens sets, bevatten een dubbele fout bij het uploaden van **eigenschaps namen** . De fout verhindert niet dat het uploaden van de referentie gegevens sets is geslaagd. Het kan worden verwijderd door het combi neren van rijen die de gedupliceerde eigenschaps naam delen.

1. Selecteer **een rij toevoegen**, **rijen bulksgewijs importeren**of **Voeg een kolom** toe om meer referentie gegevens waarden toe te voegen, indien nodig.

    [![het toevoegen van een rij, het bulksgewijs importeren van rijen of het toevoegen van een kolom.](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > Voor elke rij die een unieke sleutel deelt met een andere rij, worden de kolommen overschreven door de laatste rij die wordt toegevoegd en die de unieke sleutel deelt.

   > [!NOTE]
   > Toegevoegde rijen hoeven **niet** *rechthoekig* te zijn: ze hebben mogelijk minder, grotere of verschillende kolommen van de andere vermeldingen in de referentie gegevensverzameling.

## <a name="next-steps"></a>Volgende stappen

* Programmatisch [referentiegegevens beheren](time-series-insights-manage-reference-data-csharp.md).

* Zie voor de volledige API-verwijzing het document [Reference Data API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api).
