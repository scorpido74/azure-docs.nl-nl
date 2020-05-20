---
title: Tegels aan uw dash board toevoegen | Microsoft Docs
description: Als ontwerper leert u hoe u het standaard dash board van Azure IoT Central Application kunt configureren.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 1110f76a792a7e3955d5fd32e01ac1566d467151
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659086"
---
# <a name="configure-the-application-dashboard"></a>Het toepassings dashboard configureren

Het **dash board** is de pagina die wordt geladen wanneer gebruikers die toegang hebben tot de toepassing, naar de URL van de toepassing navigeren. Als u uw toepassing hebt gemaakt vanuit een van de **toepassings sjablonen**, heeft uw toepassing een vooraf gedefinieerd dash board om te starten. Als u uw toepassing hebt gemaakt op basis van een sjabloon voor een **aangepaste toepassings** toepassing, worden in het dash board enkele tips weer gegeven om aan de slag te gaan.

> [!NOTE]
> Gebruikers kunnen naast het standaard toepassings dashboard [meerdere Dash boards maken](howto-create-personal-dashboards.md) . Deze Dash boards kunnen alleen persoonlijk zijn voor de gebruiker of worden gedeeld met alle gebruikers van de toepassing.  

## <a name="add-tiles"></a>Tegels toevoegen

In de volgende scherm afbeelding ziet u het dash board in een toepassing die is gemaakt op basis van de sjabloon voor **aangepaste toepassingen** . Als u het standaard dashboard voor uw toepassing wilt aanpassen, selecteert u **bewerken** in de linkerbovenhoek van de pagina.

> [!div class="mx-imgBorder"]
> ![Dash board voor toepassingen op basis van de sjabloon ' voor beeld Contoso '](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

Als u **bewerken** selecteert, wordt het deel venster dashboard bibliotheek geopend. De bibliotheek bevat de tegels en dashboard primitieven die u kunt gebruiken om het dash board aan te passen.

> [!div class="mx-imgBorder"]
> ![Dashboard bibliotheek](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

U kunt bijvoorbeeld een **telemetrie** -tegel toevoegen voor de huidige Tempe ratuur van het apparaat. Dit doet u als volgt:

1. Een sjabloon voor een **apparaat** selecteren
1. Selecteer een apparaat in **apparaten** voor het apparaat dat u wilt weer geven op een dashboard tegel. Vervolgens ziet u een lijst met de eigenschappen van het apparaat die kunnen worden gebruikt op de tegel.
1. Als u de tegel op het dash board wilt maken, klikt u op **Tempe ratuur** en sleept u deze naar het dashboard gebied. U kunt ook op het selectie vakje naast **Tempe ratuur** klikken en vervolgens op **tegel toevoegen**klikken. In de volgende scherm afbeelding ziet u hoe u een Apparaataccount en een apparaatinstantie selecteert en vervolgens een temperatuur telemetrie-tegel op het dash board maakt.
1. Selecteer in de linkerbovenhoek op **Opslaan** om de tegel op het dash board op te slaan.

> [!div class="mx-imgBorder"]
> ![Formulier Details van apparaat configureren met Details voor instellingen en eigenschappen](media/howto-add-tiles-to-your-dashboard/device-details.png)

Wanneer een operator nu het standaard toepassings dashboard weergeeft, zien ze de nieuwe tegel met de **Tempe ratuur** voor het apparaat. Elke tegel heeft een vooraf geselecteerde grafiek, grafiek, enzovoort die wordt weer gegeven wanneer de tegel wordt gemaakt. Gebruikers kunnen er echter voor kiezen om deze visualisatie te bewerken en te wijzigen.  

> [!div class="mx-imgBorder"]
> ![Tabblad dash board met weer gegeven instellingen en eigenschappen voor de tegel](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>Tegels bewerken

Als u een tegel op het dash board wilt bewerken, klikt u eerst op **bewerken** in de linkerbovenhoek van de pagina, waardoor de bewerkings modus voor het dash board en alle tegels wordt geopend.  

> [!div class="mx-imgBorder"]
> ![Dashboard scherm met bewerkings modus geactiveerd voor een geselecteerde tegel](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Klik vervolgens op het **tandwiel** pictogram in de rechter bovenhoek van de tegel die u wilt bewerken. Hier kunt u aspecten van de tegel bewerken, met inbegrip van de titel, de visualisatie, de aggregatie, enzovoort.

> [!div class="mx-imgBorder"]
> ![Vervolg keuzelijst voor aggregatie-instellingen voor tegel](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

U kunt de grafiek visualisatie ook wijzigen door op het **liniaal** pictogram op de tegel te klikken.

> [!div class="mx-imgBorder"]
> ![Vervolg keuzelijst voor visualisatie-instellingen van Tegel](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Tegel typen

De volgende tabel bevat een overzicht van het gebruik van tegels in azure IoT Central:

| Tegel | Dashboard | Beschrijving
| ----------- | ------- | ------- |
| Inhoud | Dash boards van toepassingen en apparaten instellen |Ondersteunde tegels kunnen worden weer gegeven op tegels die kop-en beschrijvings tekst bevatten. U kunt deze tegel ook gebruiken als koppelings tegel om een gebruiker in staat te stellen te navigeren naar een URL die betrekking heeft op uw toepassing.|
| Installatiekopie | Dash boards van toepassingen en apparaten instellen |Afbeeldings tegels geven een aangepaste afbeelding weer en kunnen worden geklikt. Gebruik een tegel voor afbeeldingen om afbeeldingen toe te voegen aan een dash board en schakel optioneel een gebruiker in om naar een URL te navigeren die relevant is voor uw toepassing.|
| Label | Toepassings dashboards |Met label tegels wordt aangepaste tekst op een dash board weer gegeven. U kunt de grootte van de tekst kiezen. Gebruik een label tegel om relevante informatie toe te voegen aan het dash board, zoals beschrijvingen, contact gegevens of Help.|
| Kaart | Dash boards van toepassingen en apparaten |Kaart tegels geven de locatie van een apparaat op een kaart weer. U kunt ook Maxi maal 100 punten van de locatie geschiedenis van een apparaat weer geven. U kunt bijvoorbeeld een voor beeld van een route weer geven van waaruit een apparaat in de afgelopen week is geweest.|
| Lijn diagram | Dash boards van toepassingen en apparaten |Met lijn diagram tegels wordt een diagram weer gegeven van de cumulatieve meting voor een apparaat gedurende een bepaalde periode. U kunt bijvoorbeeld een lijn diagram weer geven waarin de gemiddelde Tempe ratuur en druk van een apparaat gedurende het afgelopen uur worden weer gegeven.|
| Staaf diagram | Dash boards van toepassingen en apparaten |In staafdiagram tegels wordt een grafiek weer gegeven met statistische metingen voor een apparaat gedurende een bepaalde periode. U kunt bijvoorbeeld een staaf diagram weer geven waarin de gemiddelde Tempe ratuur en druk van een apparaat gedurende het afgelopen uur worden weer gegeven.|
| Cirkel diagram | Dash boards van toepassingen en apparaten instellen |Cirkel diagram tegels geven een diagram van geaggregeerde metingen voor een apparaat gedurende een bepaalde periode.|
| Heat Map | Dash boards van toepassingen en apparaten instellen |Heatmap tegels geven informatie weer over het apparaat, weer gegeven als kleuren.|
| Gebeurtenis geschiedenis | Dash boards van toepassingen en apparaten |Met de tegels van gebeurtenis geschiedenis worden de gebeurtenissen voor een apparaat gedurende een bepaalde periode weer gegeven. U kunt dit bijvoorbeeld gebruiken om alle temperatuur wijzigingen voor een apparaat in het afgelopen uur weer te geven.|
| Status geschiedenis | Dash boards van toepassingen en apparaten |In tegels met status geschiedenis worden de meet waarden voor een tijds periode weer gegeven. U kunt dit bijvoorbeeld gebruiken om de temperatuur waarden voor een apparaat in het afgelopen uur weer te geven.|
| KPI | Dash boards van toepassingen en apparaten | KPI-tegels geven een geaggregeerde telemetrie of gebeurtenis meting voor een tijds periode weer. U kunt dit bijvoorbeeld gebruiken om de maximale Tempe ratuur voor een apparaat in het afgelopen uur weer te geven.|
| Laatst bekende waarde | Dash boards van toepassingen en apparaten |De laatste bekende waarde-tegels geven de meest recente waarde weer voor een telemetrie of status meting. U kunt deze tegel bijvoorbeeld gebruiken om de meest recente metingen van de Tempe ratuur, druk en lucht vochtigheid voor een apparaat weer te geven. |
| Eigenschap | Dash boards van toepassingen en apparaten | Eigenschappen tegels geven de huidige waarde weer voor eigenschappen en Cloud eigenschappen van een apparaat. U kunt deze tegel bijvoorbeeld gebruiken om apparaateigenschappen weer te geven, zoals de fabrikant of firmware versie voor een apparaat. |

### <a name="customizing-visualizations"></a>Visualisaties aanpassen

Voor lijn diagrammen, staaf diagrammen en cirkel diagrammen kunt u de kleuren aanpassen die worden weer gegeven door verschillende teleelementen in uw grafiek. Als u dit wilt doen, selecteert u het palet pictogram naast de telemetrie die u wilt aanpassen en kiest u een kleur.

> [!div class="mx-imgBorder"]
> ![Vervolg keuzelijst voor de instellingen voor telemetrie-kleuren weer geven](media/howto-add-tiles-to-your-dashboard/color-customization.png)

Voor teleelementen of eigenschappen die van het type teken reeks zijn, kunt u kiezen hoe u de tekst wilt visualiseren. Als uw apparaat bijvoorbeeld een URL als een teken reeks-telemetrie verzendt, kunt u die URL visualiseren als een klikable koppeling. Als de URL verwijst naar een afbeelding, kunt u de installatie kopie weer geven in een laatste bekende waarde of eigenschaps tegel. U kunt de weer gave van de telemetrie van de teken reeks wijzigen door de tand wiel naast de naam van de telemetrie te selecteren. Op deze manier kunt u de tekst weer geven als tekst, een koppeling of een afbeelding.

> [!div class="mx-imgBorder"]
> ![Vervolg keuzelijst voor visualisatie-instellingen voor teken reeksen](media/howto-add-tiles-to-your-dashboard/string-viz-customization.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw Azure IoT Central standaard toepassings dashboard configureert, kunt u [meer informatie over het maken van een persoonlijk dash board](howto-create-personal-dashboards.md).
