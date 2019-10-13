---
title: Een set met referentie gegevens toevoegen aan uw Azure Time Series Insights omgeving | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een set met referentie gegevens kunt toevoegen om gegevens in uw Azure Time Series Insights omgeving uit te breiden.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: c33619ba52246128526bb132dfe94416c7c4de8c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299597"
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

   [Referentie gegevens @no__t 1Add](media/add-reference-data-set/add-reference-data.png)](media/add-reference-data-set/add-reference-data.png#lightbox)

1. Selecteer de knop **+ een gegevensset toevoegen** om een nieuwe gegevensset toe te voegen.

   [![Add-gegevensset](media/add-reference-data-set/add-data-set.png)](media/add-reference-data-set/add-data-set.png#lightbox)

1. Kies op de pagina **nieuwe referentie gegevensgroep** de indeling van de gegevens:

   - Kies **CSV** voor door komma's gescheiden gegevens. De eerste rij wordt beschouwd als een veldnamenrij.
   - Kies **JSON-matrix** voor gegevens in JSON-indeling (Java Script object Notation).

   [![Choose gegevens indeling.](media/add-reference-data-set/add-data.png)](media/add-reference-data-set/add-data.png#lightbox)

1. Geef de gegevens op met behulp van een van de twee methoden:

   - Plak de gegevens in de tekst editor. Selecteer vervolgens de knop **referentie gegevens parseren** .
   - Selecteer de knop **bestand kiezen** om gegevens uit een lokaal tekst bestand toe te voegen.

   Plak bijvoorbeeld CSV-gegevens: [![PASTED CSV-gegevens](media/add-reference-data-set/csv-data-pasted.png)](media/add-reference-data-set/csv-data-pasted.png#lightbox)

   Plak bijvoorbeeld JSON-matrix gegevens: [![PASTE JSON-gegevens](media/add-reference-data-set/json-data-pasted.png)](media/add-reference-data-set/json-data-pasted.png#lightbox)

   Als er een fout optreedt bij het parseren van de gegevens waarden, wordt de fout rood weer gegeven aan de onderkant van de pagina, bijvoorbeeld `CSV parsing error, no rows extracted`.

1. Zodra de gegevens zijn geparseerd, wordt een gegevens raster weer gegeven waarin de kolommen en rijen worden weer gegeven die de gegevens vertegenwoordigen.  Controleer het gegevens raster om te controleren of het juist is.

   [Referentie gegevens @no__t 1Add](media/add-reference-data-set/parse-data.png)](media/add-reference-data-set/parse-data.png#lightbox)

1. Bekijk elke kolom om te zien welk gegevens type wordt gebruikt en wijzig zo nodig het gegevens type.  Selecteer het gegevens type symbool in de kolomkop: **#** voor dubbele (numerieke gegevens), **t | F** voor Booleaanse waarde, of **ABC** voor teken reeks.

   [@no__t 1Choose gegevens typen in de kolom koppen.](media/add-reference-data-set/choose-datatypes.png)](media/add-reference-data-set/choose-datatypes.png#lightbox)

1. Wijzig de naam van de kolom koppen, indien nodig. De naam van de sleutel kolom is nodig om lid te worden van de bijbehorende eigenschap in de bron van de gebeurtenis. Zorg ervoor dat de kolom namen van de referentie gegevens sleutel exact overeenkomen met de naam van de gebeurtenis voor uw binnenkomende gegevens, met inbegrip van hoofdletter gevoeligheid. De niet-sleutel kolom namen worden gebruikt voor het verbeteren van de inkomende gegevens met de bijbehorende referentie gegevens waarden.

1. Selecteer **een rij toevoegen** of **Voeg een kolom** toe om meer referentie gegevens waarden toe te voegen, indien nodig.

1. Typ een waarde in het veld **rijen...** om specifieke rijen naar behoefte te controleren. Het filter is handig voor het controleren van gegevens, maar wordt niet toegepast bij het uploaden van de gegevens.

1. Geef de gegevensset een naam door het veld **naam van gegevensset** boven het gegevens raster in te vullen.

    [de gegevensset @no__t 1Name.](media/add-reference-data-set/name-reference-dataset.png)](media/add-reference-data-set/name-reference-dataset.png#lightbox)

1. Geef de **primaire-sleutel** kolom in de gegevensset op door de vervolg keuzelijst boven het gegevens raster te selecteren.

    [![Select de sleutel kolom (men).](media/add-reference-data-set/set-primary-key.png)](media/add-reference-data-set/set-primary-key.png#lightbox)

    Selecteer desgewenst de knop **+** om een secundaire sleutel kolom toe te voegen als samengestelde primaire sleutel. Als u de selectie ongedaan wilt maken, kiest u de lege waarde uit de vervolg keuzelijst om de secundaire sleutel te verwijderen.

1. Als u de gegevens wilt uploaden, selecteert u de knop **rijen uploaden** .

    [![Upload](media/add-reference-data-set/upload-rows.png)](media/add-reference-data-set/upload-rows.png#lightbox)

    Op de pagina wordt de voltooide upload bevestigd en wordt het bericht met de **gegevensset geüpload**weer gegeven.

## <a name="next-steps"></a>Volgende stappen

* Programmatisch [referentiegegevens beheren](time-series-insights-manage-reference-data-csharp.md).

* Zie voor de volledige API-verwijzing het document [Reference Data API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api).
