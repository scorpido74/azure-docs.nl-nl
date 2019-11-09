---
title: Maps genereren
description: Hierin wordt beschreven hoe u kaarten in FarmBeats genereert
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 635431fb87e5f164f92ab4b7a1027ee96e9d801a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891006"
---
# <a name="generate-maps"></a>Maps genereren

Met Azure FarmBeats kunt u de volgende kaarten genereren met behulp van satelliet afbeeldings-en sensor gegevens invoer. Maps helpt u bij het weer geven van de geografische locatie van uw farm en het identificeren van de juiste locatie voor uw apparaten.

  -  **Kaart voor plaatsing van sensor** – bevat aanbevelingen over het aantal Sens oren dat moet worden gebruikt en waar u ze in een farm plaatst.
  - **Toewijzing van satelliet indices** : toont een vegetatie index en water index voor een farm.
  - **Kaart voor bodem vocht** : toont bodem vocht distributie door satelliet gegevens en sensor gegevens te weigeren.

## <a name="sensor-placement-maps"></a>Kaarten voor sensor plaatsing

FarmBeats sensor placement map helpt u bij het plaatsen van bodem vocht Sens oren. De kaart uitvoer bestaat uit een lijst met coördinaten voor de implementatie van sensors. De invoer van deze Sens oren wordt samen met satelliet afbeelding gebruikt voor het genereren van de bodem vocht heatmap.  

Deze kaart wordt afgeleid door de Canopy te segmenteren, zoals in het hele jaar over meerdere datums heen is gezien, zelfs bare bodem en gebouwen deel uitmaken van de Canopy. U kunt Sens oren verwijderen die niet vereist zijn op de locatie. Deze kaart is voor hulp en u kunt de positie en getallen enigszins aanpassen op basis van uw aangepaste kennis (het toevoegen van Sens oren zal geen Regress, maar er is een risico op de nauw keurigheid van de hitte kaart als het sensor nummer wordt gereduceerd).  

## <a name="before-you-begin"></a>Voordat u begint  

Zorg ervoor dat u het volgende voordat u probeert een sensor plaatsings kaart te genereren:

- De farm grootte moet meer dan één acre zijn.
- Het aantal beschik bare verklikker scènes in de Cloud moet meer dan zes zijn voor het geselecteerde datum bereik.  
- Ten minste zes beschik bare Sentinel-scènes in de Cloud moeten NDVI > = 0.3 hebben.

    > [!NOTE]
    > Met Sentinel kunnen slechts twee gelijktijdige threads per gebruiker worden toegestaan. Als gevolg hiervan worden taken in de wachtrij geplaatst en kan het langer duren om te volt ooien.

### <a name="dependencies-on-sentinel"></a>Afhankelijkheden van Sentinel  

Dit zijn de volgende Sentinel-afhankelijkheden:

- We zijn afhankelijk van de verklikker prestaties voor het downloaden van satelliet installatie kopieën. Controleer de status van de verklikker prestaties en de onderhouds [activiteiten](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome).
- Met Sentinel kunnen Maxi maal twee gelijktijdige threads per gebruiker worden [gedownload](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) .
- Het genereren van precisie kaarten wordt beïnvloed door de [verklikker dekking en de frequentie van de herbezoek]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage).

## <a name="create-sensor-placement-map"></a>Plaatsings kaart sensor maken
De sectie bevat informatie over de procedures voor het maken van kaarten voor sensor plaatsing.

> [!NOTE]
> U kunt de plaatsing van sensors starten vanaf de pagina **kaarten** of vanuit de vervolg keuzelijst **precisie kaarten genereren** op de pagina **Details van Farm** .

Voer de volgende stappen uit:

1. Ga op de start pagina naar **kaarten** vanuit het navigatie menu links.
2. Selecteer **kaarten maken** en selecteer **plaatsing van sensors** uit vervolg keuzelijst.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Selecteer **plaatsing van sensors**.
  Het venster positie sensor plaatsing wordt weer gegeven.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Selecteer een farm in de vervolg keuzelijst **Farm** .  
   Als u een farm wilt zoeken en selecteren, kunt u in de vervolg keuzelijst schuiven of de naam van de farm in het tekstvak typen.
5. Als u een kaart voor het afgelopen jaar wilt genereren, selecteert u **Aanbevolen** optie.
6. Als u een kaart wilt genereren voor een aangepast datum bereik, selecteert u **datum bereik selecteren**en geeft u de begin-en eind datum op voor het genereren van de plaatsings kaart van de sensor.
7. Selecteer **Maps genereren**.
 Er wordt een bevestigings bericht met taak details weer gegeven.

  Zie de sectie **taken weer geven**voor meer informatie over de taak status. Als de status van de taak *niet*wordt weer gegeven, wordt er een gedetailleerd fout bericht weer gegeven op de knop Info van de status *mislukt* .. In dit geval herhaalt u de hierboven vermelde stappen en probeert u het opnieuw.

  Als het probleem zich blijft voordoen, raadpleegt u de sectie [problemen oplossen](troubleshoot-project-farmbeats.md) of neemt u contact op met het [Azure FarmBeats-forum voor ondersteuning](https://aka.ms/FarmBeatsMSDN) bij relevante Logboeken.

### <a name="view-and-download-sensor-placement-map"></a>Plaatsings kaart van sensor weer geven en downloaden

Voer de volgende stappen uit:

1. Ga op de start pagina naar **kaarten** vanuit het navigatie menu links.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecteer **filter** in de linkernavigatiebalk van het venster.
  In het venster **filter** worden zoek criteria weer gegeven.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selecteer **type**, **datum** en **naam** in de vervolg keuzelijst en selecteer vervolgens **Toep assen** om te zoeken naar de kaart die u wilt weer geven.
  De datum waarop de taak is gemaakt, wordt weer gegeven in de notatie type_farmname_YYYY-MM-DD.
4. Blader door de lijst met kaarten die beschikbaar zijn via de Navigatie balken aan het einde van de pagina.
5. Selecteer de kaart die u wilt weer geven. In een pop-upvenster wordt het voor beeld voor de geselecteerde kaart weer gegeven.
6. Selecteer **downloaden**en down load het geojson-bestand met sensor coördinaten.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Kaart voor satelliet indexen

In de volgende secties worden de procedures beschreven voor het maken en weer geven van de toewijzing van satelliet indices.

> [!NOTE]
> U kunt de kaart voor satelliet-indexen starten vanaf de pagina **kaarten** of vanuit de vervolg keuzelijst **precisie kaarten genereren** op de pagina **Details van Farm** .

FarmBeats biedt u de mogelijkheid om een genormaliseerde diff-index (NDVI), Enhanced vegetatie index (EVI) en genormaliseerde (diff-index) voor farms te genereren. Deze indices helpen te bepalen hoe het gewas momenteel groeit, of in het verleden is toegenomen, en de representatieve water niveaus op het wegdek.


> [!NOTE]
> Er is een verklikker afbeelding vereist voor de dagen waarvoor de kaart wordt gegenereerd.


## <a name="create-satellite-indices-map"></a>Toewijzing van satelliet indices maken

Voer de volgende stappen uit:

1. Ga op de start pagina naar **kaarten** vanuit het navigatie menu links.
2. Selecteer **Maps maken** en selecteer **satelliet indexen** in de vervolg keuzelijst.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Selecteer **satelliet-indexen**.
  Het venster satelliet-indexen wordt weer gegeven.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Selecteer een farm in de vervolg keuzelijst.  
   Als u een farm wilt zoeken en selecteren, kunt u in de vervolg keuzelijst schuiven of de naam van de farm typen.   
5. Als u een kaart wilt genereren voor de afgelopen week, selecteert u **deze week** optie.
6. Als u een kaart wilt genereren voor aangepast datum bereik, selecteert u **datum bereik selecteren**en geeft u de begin-en eind datum op waarvoor u de kaart met satelliet indexen wilt genereren.
7. Selecteer **Maps genereren**.
    Er wordt een bevestigings bericht met taak details weer gegeven.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Zie de sectie **taken weer geven**voor meer informatie over de taak status. Als de status van de taak *niet*wordt weer gegeven, wordt er een gedetailleerd fout bericht weer gegeven op de knop Info van de status *mislukt* . In dit geval herhaalt u de hierboven vermelde stappen en probeert u het opnieuw.

    Als het probleem zich blijft voordoen, raadpleegt u de sectie [problemen oplossen](troubleshoot-project-farmbeats.md) of neemt u contact op met het [Azure FarmBeats-forum voor ondersteuning](https://aka.ms/FarmBeatsMSDN) bij relevante Logboeken.

### <a name="view-and-download-map"></a>Kaart weer geven en downloaden

Voer de volgende stappen uit:

1. Ga op de start pagina naar **kaarten** vanuit het navigatie menu links.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecteer **filter** in de linkernavigatiebalk van het venster. in het venster **filter** worden zoek criteria weer gegeven.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selecteer **type**, **datum** en **naam** in de vervolg keuzelijst en selecteer vervolgens **Toep assen** om te zoeken naar de kaart die u wilt weer geven.
  De datum waarop de taak is gemaakt, wordt weer gegeven in de notatie type_farmname_YYYY-MM-DD.

4. Blader door de lijst met kaarten die beschikbaar zijn via de Navigatie balken aan het einde van de pagina.
5. Voor elke combi natie van naam en **datum**van het **bedrijf** zijn de volgende drie toewijzingen beschikbaar:
    - NDVI
    - EVI
    - NDWI
6. Selecteer de kaart die u wilt weer geven. In een pop-upvenster wordt het voor beeld voor de geselecteerde kaart weer gegeven.
7. Selecteer **down load** vervolg keuzelijst om de Download indeling te selecteren en de kaart wordt gedownload en opgeslagen in uw lokale map op uw computer.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="get-soil-moisture-heatmap"></a>Bodem vocht heatmap ophalen

Bodem vocht is het water dat in de ruimten tussen de bodem deeltjes wordt bewaard. De bodem vocht heatmap helpt u bij het begrijpen van de bodem vocht gegevens, met een hoge resolutie in uw farms. Om een nauw keurige en bruikbare bodem vocht heatmap te genereren, is een uniforme implementatie van Sens oren vereist, waarbij alle Sens oren van dezelfde provider zijn. Verschillende providers hebben verschillen in de manier waarop bodem vocht wordt gemeten, samen met verschillen in kalibratie. De heatmap wordt gegenereerd voor een bepaalde diepte met behulp van de Sens oren die op die diepte zijn geïmplementeerd.

### <a name="before-you-begin"></a>Voordat u begint  

Zorg ervoor dat u het volgende voordat u probeert een bodem vocht heatmap te genereren:

- Ten minste drie bodem vocht Sens oren moeten worden geïmplementeerd. Micro soft raadt u aan geen kaart met een bodem vocht te maken voordat Sens oren worden geïmplementeerd en gekoppeld aan de farm.  
- Het genereren van bodem vocht heatmap wordt beïnvloed door de padvariabelen van het Sentinel, de dekking van de Cloud en de Cloud schaduw. Er moet ten minste één Cloud vrije Sentinel-scène beschikbaar zijn voor de afgelopen 120 dagen, van de dag waarop de kaart voor het bodem vocht is aangevraagd.
- Ten minste de helft van de Sens oren die in de farm zijn geïmplementeerd, moet online zijn en gegevens moeten worden gestreamd naar de data hub.
- De heatmap moet worden gegenereerd met behulp van sensor metingen van dezelfde provider.


## <a name="create-soil-moisture-heatmap"></a>Bodem vocht heatmap maken

Voer de volgende stappen uit:

1. Ga op de start pagina naar **kaarten** vanuit het navigatie menu links om de pagina kaarten weer te geven.
2. Selecteer **kaarten maken** en selecteer **bodem vocht** in vervolg keuzemenu.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Selecteer **bodem vocht**.
    In het bodem vocht venster wordt weer gegeven.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Selecteer een farm in de vervolg keuzelijst **Farm** .  
   Als u uw farm wilt zoeken en selecteren, kunt u in de vervolg keuzelijst schuiven of de naam van de farm typen in de vervolg keuzelijst Farm selecteren.
5. Selecteer in de vervolg keuzelijst meting van de **bodem vochtigheid selecteren** de meting van de bodem vocht sensor (diepte) waarvoor u de kaart wilt genereren.
Als u de sensor meting wilt vinden, gaat u naar **Sens oren**en selecteert u een bodem vocht sensor. Gebruik vervolgens onder **sensor eigenschappen** de waarde voor in naam van **maat eenheid**
6. Selecteer een van de opties als u voor **vandaag** of **deze week**wilt genereren.
7. Als u wilt genereren voor een aangepast datum bereik, selecteert u **datum bereik selecteren**en geeft u de begin-en eind datum op waarvoor u de kaart voor bodem vocht wilt genereren.
8. Selecteer **Maps genereren**.
 Er wordt een bevestigings bericht met taak details weer gegeven.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Zie de sectie **taken weer geven**voor meer informatie over de taak status. Als de status van de taak *niet*wordt weer gegeven, wordt er een gedetailleerd fout bericht weer gegeven op de knop Info van de status *mislukt* . In dit geval herhaalt u de hierboven vermelde stappen en probeert u het opnieuw.

    Als het probleem zich blijft voordoen, raadpleegt u de sectie [problemen oplossen](troubleshoot-project-farmbeats.md) of neemt u contact op met het [Azure FarmBeats-forum voor ondersteuning](https://aka.ms/FarmBeatsMSDN) bij relevante Logboeken.

### <a name="view-and-download-map"></a>Kaart weer geven en downloaden

Voer de volgende stappen uit:

1. Ga op de start pagina naar **kaarten** vanuit het navigatie menu links.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecteer **filter** in de linkernavigatiebalk van het venster. in het venster **filter** wordt weer gegeven waar u kunt zoeken naar kaarten.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Selecteer **type**, **datum** en **naam** in de vervolg keuzelijst en selecteer vervolgens **Toep assen** om te zoeken naar de kaart die u wilt weer geven. De datum waarop de taak is gemaakt, wordt weer gegeven in de notatie type_farmname_YYYY-MM-DD.
4. Selecteer het **Sorteer** pictogram naast de tabelkop teksten om te sorteren op basis van de farm, datum, gemaakt op, taak-id en taak type.
5. Blader door de lijst met kaarten die beschikbaar zijn via de navigatie knoppen aan het einde van de pagina.
6. Selecteer de kaart die u wilt weer geven. In een pop-upvenster wordt het voor beeld voor de geselecteerde kaart weer gegeven.
7. Selecteer **down load** vervolg keuzelijst om de Download indeling te selecteren en de kaart wordt gedownload en opgeslagen in de opgegeven map.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
