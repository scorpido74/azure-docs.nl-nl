---
title: Gegevens over api-gebruik azure maps weergeven | Microsoft Azure Maps
description: In dit artikel leert u hoe u de statistieken voor uw Microsoft Azure Maps API-aanroepen weergeven in de Azure-portal.
author: philmea
ms.author: philmea
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 0eb117af712b3b1f63a3f99c96cba9775f8e3996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335152"
---
# <a name="view-azure-maps-api-usage-metrics"></a>Metrische gegevens over het gebruik van Azure Maps-API weergeven

In dit artikel ziet u hoe u de API-gebruiksstatistieken voor uw Azure Maps-account weergeven in de [Azure-portal.](https://portal.azure.com) De statistieken worden weergegeven in een handige grafiekindeling langs een aanpasbare tijdsduur.

## <a name="view-metric-snapshot"></a>Metrische momentopname weergeven

U enkele algemene statistieken bekijken op de **overzichtspagina** van uw Maps-account. Het toont momenteel *Totale aanvragen,* *totale fouten*en *beschikbaarheid gedurende* een selecteerbare tijdsduur.

![Overzicht van gebruiksstatistieken van Azure Maps](media/how-to-view-api-usage/portal-overview.png)

Ga door naar de volgende sectie als u deze grafieken moet aanpassen voor uw specifieke analyse.

## <a name="view-detailed-metrics"></a>Gedetailleerde statistieken weergeven

1. Meld u aan bij uw Azure-abonnement in de [portal.](https://portal.azure.com)

2. Klik op het **menu-item Alle bronnen** aan de linkerkant en navigeer naar uw *Azure Maps-account*.

3. Zodra uw Maps-account is geopend, klikt u op het menu **Statistieken** aan de linkerkant.

4. Kies **in** het deelvenster Statistieken een van de volgende opties:

   1. **Beschikbaarheid** - die het *gemiddelde* van api-beschikbaarheid over een bepaalde periode weergeeft.
   2. **Gebruik** - dat laat zien hoe het gebruik *telt* voor uw account.

      ![Venster gebruiksstatistieken van Azure Maps](media/how-to-view-api-usage/portal-metrics.png)

5. Vervolgens u het *tijdsbereik* selecteren door op **Laatste 24 uur (Automatisch)** te klikken. Standaard is het tijdsbereik ingesteld op 24 uur. Na het klikken ziet u alle selecteerbare tijdbereiken. U de *granulariteit tijd* selecteren en ervoor kiezen om de tijd als *lokaal* of *GMT* weer te geven in dezelfde vervolgkeuzelijst. Klik op **Toepassen**.

    ![Tijdsbereik voor Azure Maps-statistieken](media/how-to-view-api-usage/time-range.png)

6. Zodra u uw statistiek hebt toegevoegd, u **filter toevoegen** van de eigenschappen die relevant zijn voor die statistiek. Selecteer vervolgens de waarde van de eigenschap die u in de grafiek wilt weergeven.

    ![Filter azure Maps-gebruiksstatistieken](media/how-to-view-api-usage/filter.png)

7. U ook **splitsen toepassen** op basis van uw geselecteerde metrische eigenschap. Hiermee kan de grafiek worden opgesplitst in meerdere grafieken, voor elke waarde van die eigenschap. In de volgende afbeelding komt de kleur van elke grafiek overeen met de eigenschapswaarde die onder aan de grafiek wordt weergegeven.

    ![Azure Maps-gebruiksstatistieken splitsen](media/how-to-view-api-usage/splitting.png)

8. U ook meerdere statistieken in dezelfde grafiek observeren door simpelweg op de knop **Metrische toevoegen** bovenaan te klikken.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de Azure Maps API's waarvoor u het gebruik wilt bijhouden:
> [!div class="nextstepaction"] 
> [Zo-staat azure maps websdk](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Zo-staat van Azure Maps Android SDK](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Azure Maps REST API-documentatie](https://docs.microsoft.com/rest/api/maps)
