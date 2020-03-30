---
title: Kaarten genereren
description: In dit artikel wordt beschreven hoe u kaarten genereert in Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 92228c691c323bc0b9621dfc7413d86c5c2669e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271770"
---
# <a name="generate-maps"></a>Kaarten genereren

Met Azure FarmBeats u de volgende kaarten genereren met behulp van satellietbeelden en sensorgegevensingangen. Kaarten helpen u om de geografische locatie van uw bedrijf te zien en de juiste locatie voor uw apparaten te identificeren.

  - **Sensor plaatsing kaart**: Geeft aanbevelingen over hoeveel sensoren te gebruiken en waar ze te plaatsen op een boerderij.
  - **Satellietindexindexkaart**: Toont de vegetatie-index en waterindex voor een boerderij.
  - **Bodem vocht heatmap**: Toont bodemvocht verdeling door het smelten van satellietgegevens en sensorgegevens.

## <a name="sensor-placement-map"></a>Sensorplaatsingskaart

Een FarmBeats Sensor Placement kaart helpt u bij het plaatsen van bodemvochtsensoren. De kaartuitvoer bestaat uit een lijst met coördinaten voor de inzet van sensoren. De ingangen van deze sensoren worden gebruikt samen met satellietbeelden om de soil moisture heatmap te genereren.

Deze kaart is afgeleid door het segmenteren van de luifel zoals gezien over meerdere data gedurende het hele jaar. Zelfs kale grond en gebouwen maken deel uit van de luifel. U sensoren verwijderen die niet op de locatie nodig zijn. Deze kaart is voor begeleiding, en u de positie en nummers enigszins wijzigen op basis van uw aangepaste kennis. Het toevoegen van sensoren zal niet achteruit bodem vocht heatmap resultaten, maar er is een mogelijkheid van verslechtering van heatmap nauwkeurigheid als de sensor nummer wordt verminderd.

## <a name="before-you-begin"></a>Voordat u begint

Voldoe aan de volgende vereisten voordat u een sensorplaatsingskaart probeert te genereren:

- De grootte van de boerderij moet meer dan een hectare groot zijn.
- Het aantal cloudvrije Sentinel-scènes moet meer dan zes zijn voor het geselecteerde datumbereik.
- Ten minste zes cloudvrije Sentinel-scènes moeten een Genormaliseerde Difference Vegetation Index (NDVI) hebben die groter is dan of gelijk is aan 0,3.

    > [!NOTE]
    > Sentinel staat slechts twee gelijktijdige threads per gebruiker toe. Als gevolg hiervan worden taken in de wachtrij geplaatst en kan het langer duren voordat taken zijn voltooid.

### <a name="dependencies-on-sentinel"></a>Afhankelijkheden op Sentinel

De volgende afhankelijkheden hebben betrekking op Sentinel:

- We zijn afhankelijk van sentinel prestaties voor het downloaden van satellietbeelden. Controleer sentinel-prestatiestatus en [onderhoudsactiviteiten](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome).
- Sentinel staat slechts twee gelijktijdige [downloads threads](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) per gebruiker toe.
- Precisie kaart generatie wordt beïnvloed door [Sentinel dekking en opnieuw frequentie]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage).

## <a name="create-a-sensor-placement-map"></a>Een sensorplaatsingskaart maken
In dit gedeelte worden de procedures beschreven voor het maken van sensorplaatsingskaarten.

> [!NOTE]
> U een sensorplaatsingskaart starten vanaf de pagina **Kaarten** of via het vervolgkeuzemenu **Precision Maps genereren** op de pagina **Farmdetails.**

Volg deze stappen.

1. Ga op de startpagina naar **Kaarten** in het linkernavigatiemenu.
2. Selecteer **Kaarten maken**en selecteer **Sensorplaatsing** in het vervolgkeuzemenu.

    ![Sensorplaatsing selecteren](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Nadat u **Sensorplaatsing hebt**geselecteerd, wordt het venster **Sensorplaatsing** weergegeven.

    ![Venster Sensorplaatsing](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Selecteer een farm in het vervolgkeuzemenu **Farm.**
   Als u uw farm wilt zoeken en selecteren, u in de vervolgkeuzelijst schuiven of de naam van de farm invoeren in het tekstvak.
5. Als u een kaart voor het afgelopen jaar wilt genereren, selecteert u **Aanbevolen**.
6. Als u een kaart wilt genereren voor een aangepast datumbereik, selecteert u de optie **Datumbereik selecteren**. Voer de begin- en einddatum in waarvoor u de sensorplaatsingskaart wilt genereren.
7. Selecteer **Kaarten genereren**.
 Er verschijnt een bevestigingsbericht met taakgegevens.

  Zie de sectie **Vacatures weergeven**voor informatie over de status van een taak. Als de taakstatus *Mislukt*wordt weergegeven, verschijnt er een gedetailleerd foutbericht op het knopinfo van de *status Mislukt.* Herhaal in dit geval de vorige stappen en probeer het opnieuw.

  Als het probleem blijft bestaan, [raadpleegt](troubleshoot-azure-farmbeats.md) u de sectie Problemen oplossen of neemt u contact op met het [Azure FarmBeats-forum voor ondersteuning](https://aka.ms/FarmBeatsMSDN) met relevante logboeken.

### <a name="view-and-download-a-sensor-placement-map"></a>Een sensorplaatsingskaart weergeven en downloaden

Volg deze stappen.

1. Ga op de startpagina naar **Kaarten** in het linkernavigatiemenu.

    ![Kaarten selecteren in het linkernavigatiemenu](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecteer **Filter** links in het venster.
  In **het venster Filter** worden zoekcriteria weergegeven.

    ![Filtervenster](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selecteer **Waarden typen,** **datum**en **naam in** de vervolgkeuzemenu's. Selecteer vervolgens **Toepassen** om te zoeken naar de kaart die u wilt weergeven.
  De datum waarop de taak is gemaakt, wordt weergegeven in de indeling type_farmname_YYYY-MM-DD.
4. Blader door de lijst met beschikbare kaarten met behulp van de navigatiebalken aan het einde van de pagina.
5. Selecteer de kaart die u wilt weergeven. In een pop-upvenster wordt het voorbeeld voor de geselecteerde kaart weergegeven.
6. Selecteer **Downloaden**en download het GeoJSON-bestand met sensorcoördinaten.

    ![Voorbeeld van sensorplaatsingskaart](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Satellietindexindexenkaart

In de volgende secties worden de procedures uitgelegd die betrokken zijn bij het maken en bekijken van een satellietindexkaart.

> [!NOTE]
> U een satellietindexkaart starten vanaf de pagina **Kaarten** of via het vervolgkeuzemenu **Precision Maps genereren** op de pagina **Farmdetails.**

FarmBeats biedt u de mogelijkheid om NDVI, Enhanced Vegetation Index (EVI) en Normalized Difference Water Index (NDWI) kaarten voor boerderijen te genereren. Deze indexcijfers helpen bepalen hoe het gewas momenteel groeit, of is gegroeid in het verleden, en de representatieve waterstanden in de grond.


> [!NOTE]
> Een Sentinel-afbeelding is vereist voor de dagen waarvoor de kaart wordt gegenereerd.


## <a name="create-a-satellite-indices-map"></a>Een satellietindexkaart maken

Volg deze stappen.

1. Ga op de startpagina naar **Kaarten** in het linkernavigatiemenu.
2. Selecteer **Kaarten maken**en selecteer **satellietindexen in** het vervolgkeuzemenu.

    ![Satellietindexen selecteren in het vervolgkeuzemenu](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Nadat u **satellietindexen hebt**geselecteerd, wordt het **satellietindexvenster** weergegeven.

    ![Satellietindexenvenster](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Selecteer een farm in het vervolgkeuzemenu.
   Als u uw farm wilt zoeken en selecteren, u in de vervolgkeuzelijst schuiven of de naam van de farm invoeren.   
5. Als u een kaart voor de afgelopen week wilt genereren, selecteert u **Deze week**.
6. Als u een kaart wilt genereren voor een aangepast datumbereik, selecteert u de optie **Datumbereik selecteren**. Voer de begin- en einddatum in waarvoor u de satellietindexkaart wilt genereren.
7. Selecteer **Kaarten genereren**.
    Er verschijnt een bevestigingsbericht met taakgegevens.

    ![Satellietindexen kaart bevestigingsbericht](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Zie de sectie **Vacatures weergeven**voor informatie over de status van een taak. Als de taakstatus *Mislukt*wordt weergegeven, verschijnt er een gedetailleerd foutbericht op het knopinfo van de *status Mislukt.* Herhaal in dit geval de vorige stappen en probeer het opnieuw.

    Als het probleem blijft bestaan, [raadpleegt](troubleshoot-azure-farmbeats.md) u de sectie Problemen oplossen of neemt u contact op met het [Azure FarmBeats-forum voor ondersteuning](https://aka.ms/FarmBeatsMSDN) met relevante logboeken.

### <a name="view-and-download-a-map"></a>Een kaart weergeven en downloaden

Volg deze stappen.

1. Ga op de startpagina naar **Kaarten** in het linkernavigatiemenu.

    ![Kaarten selecteren](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecteer **Filter** links in het venster. In **het venster Filter** worden zoekcriteria weergegeven.

    ![In filtervenster worden zoekcriteria weergegeven](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selecteer **Waarden typen,** **datum**en **naam in** de vervolgkeuzemenu's. Selecteer vervolgens **Toepassen** om te zoeken naar de kaart die u wilt weergeven.
  De datum waarop de taak is gemaakt, wordt weergegeven in de indeling type_farmname_YYYY-MM-DD.

4. Blader door de lijst met beschikbare kaarten met behulp van de navigatiebalken aan het einde van de pagina.
5. Voor elke combinatie van **farmnaam** en **-datum**zijn de volgende drie kaarten beschikbaar:
    - Ndvi
    - Evi
    - NDWI NDWI
6. Selecteer de kaart die u wilt weergeven. In een pop-upvenster wordt het voorbeeld voor de geselecteerde kaart weergegeven.
7. Selecteer **Downloaden** in het vervolgkeuzemenu om de downloadindeling te selecteren. De kaart wordt gedownload en opgeslagen in uw lokale map op uw computer.

    ![Voorbeeld van geselecteerde satellietindexindexen](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>Bodemvocht heatmap

Bodemvocht is het water dat wordt gehouden in de ruimtes tussen de bodemdeeltjes.De Soil Moisture heatmap helpt u inzicht te krijgen in de bodemvochtgegevens op elke diepte, met een hoge resolutie binnen uw bedrijf. Om een nauwkeurige en bruikbare Soil Moisture heatmap te genereren, is een uniforme inzet van sensoren nodig. Alle sensoren moeten van dezelfde provider zijn. Verschillende aanbieders hebben verschillen in de manier waarop bodemvocht wordt gemeten, samen met verschillen in kalibratie. De heatmap wordt gegenereerd voor een bepaalde diepte met behulp van de sensoren ingezet op die diepte.

### <a name="before-you-begin"></a>Voordat u begint

Voldoe aan de volgende voorwaarden voordat u een heatmap voor bodemvocht probeert te genereren:

- Er moeten ten minste drie bodemvochtsensoren worden ingezet. Probeer niet om een bodemvocht heatmap te maken voordat sensoren worden ingezet en gekoppeld aan de boerderij.
- Het genereren van een soil moisture heatmap wordt beïnvloed door Sentinel's paddekking, bewolking en wolkenschaduw. Ten minste één cloudvrije Sentinel Scene moet beschikbaar zijn voor de laatste 120 dagen, vanaf de dag waarvoor de Soil Moisture heatmap werd aangevraagd.
- Ten minste de helft van de sensoren die op de boerderij worden ingezet, moet online zijn en gegevens streamen naar de datahub.
- De heatmap moet worden gegenereerd met behulp van sensormetingen van dezelfde provider.


## <a name="create-a-soil-moisture-heatmap"></a>Maak een aarde vocht heatmap

Volg deze stappen.

1. Ga op de startpagina naar **Kaarten** in het linkernavigatiemenu om de pagina **Kaarten** te bekijken.
2. Selecteer **Kaarten maken**en selecteer **Bodemvocht** in het vervolgkeuzemenu.

    ![Selecteer Bodemvocht in het vervolgkeuzemenu](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Nadat u **Bodemvocht hebt**geselecteerd, wordt het venster **Bodemvocht** weergegeven.

    ![Het venster van het GrondVocht](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Selecteer een farm in het vervolgkeuzemenu **Farm.**
   Als u uw farm wilt zoeken en selecteren, u schuiven in de vervolgkeuzelijst of de naam van de farm invoeren in het vervolgkeuzemenu **Farm selecteren.**
5. Selecteer **in** het vervolgkeuzemenu Select Soil Moisture Sensor Measure de bodemvochtsensormaat (diepte) waarvoor u de kaart wilt genereren.
Als u de sensormeting wilt vinden, gaat u naar **Sensoren**en selecteert u een bodemvochtsensor. Gebruik vervolgens onder de sectie **Sensoreigenschappen** de waarde in **Naam meten**.
6. Als u een kaart wilt genereren voor **Vandaag** of **Deze Week,** selecteert u een van de opties.
7. Als u een kaart wilt genereren voor een aangepast datumbereik, selecteert u de optie **Datumbereik selecteren**. Voer de begin- en einddatum in waarvoor u de heatmap Bodemvocht wilt genereren.
8. Selecteer **Kaarten genereren**.
 Er verschijnt een bevestigingsbericht met taakgegevens.

   ![Bodem vocht kaart bevestiging bericht](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Zie de sectie **Vacatures weergeven**voor informatie over de status van een taak. Als de taakstatus *Mislukt*wordt weergegeven, verschijnt er een gedetailleerd foutbericht op het knopinfo van de *status Mislukt.* Herhaal in dit geval de vorige stappen en probeer het opnieuw.

    Als het probleem blijft bestaan, [raadpleegt](troubleshoot-azure-farmbeats.md) u de sectie Problemen oplossen of neemt u contact op met het [Azure FarmBeats-forum voor ondersteuning](https://aka.ms/FarmBeatsMSDN) met relevante logboeken.

### <a name="view-and-download-a-map"></a>Een kaart weergeven en downloaden

Volg deze stappen.

1. Ga op de startpagina naar **Kaarten** in het linkernavigatiemenu.

    ![Ga naar Kaarten](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecteer **Filter** links in het venster. Het **filtervenster** wordt weergegeven vanaf de plaats waar u naar kaarten zoeken.

    ![Filter selecteren aan de linkerkant van de navigatie](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Selecteer **Waarden typen,** **datum**en **naam in** de vervolgkeuzemenu's. Selecteer vervolgens **Toepassen** om te zoeken naar de kaart die u wilt weergeven. De datum waarop de taak is gemaakt, wordt weergegeven in de indeling type_farmname_YYYY-MM-DD.
4. Selecteer het pictogram **Sorteren** naast de tabelkoppen om te sorteren op **Farm**, **Datum**, **Gemaakt op** **, Taak-id**en **Taaktype**.
5. Blader door de lijst met beschikbare kaarten met behulp van de navigatieknoppen aan het einde van de pagina.
6. Selecteer de kaart die u wilt weergeven. In een pop-upvenster wordt het voorbeeld voor de geselecteerde kaart weergegeven.
7. Selecteer **Downloaden** in het vervolgkeuzemenu om de downloadindeling te selecteren. De kaart wordt gedownload en opgeslagen in de opgegeven map.

    ![Bodem vocht heatmap preview](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
