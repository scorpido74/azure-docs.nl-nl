---
title: Tegels aan uw dash board toevoegen | Microsoft Docs
description: Als ontwerper leert u hoe u het standaard dash board van Azure IoT Central Application kunt configureren.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: b13349ae4293f6377429e9dc72b6c2cb43f92348
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435115"
---
# <a name="configure-the-application-dashboard"></a>Het toepassings dashboard configureren

Het **dash board** is de pagina die wordt geladen wanneer gebruikers die toegang hebben tot de toepassing, naar de URL van de toepassing navigeren. Als u uw toepassing hebt gemaakt vanuit een van de **toepassings sjablonen**, heeft uw toepassing een vooraf gedefinieerd dash board om te starten. Als u uw toepassing hebt gemaakt op basis van de **oude** toepassings sjabloon, is uw dash board leeg om te starten.

> [!NOTE]
> Gebruikers kunnen naast het standaard toepassings dashboard [meerdere Dash boards maken](howto-create-personal-dashboards.md) . Deze Dash boards kunnen alleen persoonlijk zijn voor de gebruiker of worden gedeeld met alle gebruikers van de toepassing. 

## <a name="add-tiles"></a>Tegels toevoegen

In de volgende scherm afbeelding ziet u het dash board in een toepassing die is gemaakt op basis van de sjabloon voor **aangepaste toepassingen** . Als u het standaard dashboard voor uw toepassing wilt aanpassen, selecteert u **bewerken** in de linkerbovenhoek van de pagina.

> [!div class="mx-imgBorder"]
> ![dash board voor toepassingen op basis van de sjabloon ' voor beeld Contoso '](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

Als u **bewerken** selecteert, wordt het deel venster dashboard bibliotheek geopend. De bibliotheek bevat de tegels en dashboard primitieven die u kunt gebruiken om het dash board aan te passen.

> [!div class="mx-imgBorder"]
> ![dashboard bibliotheek](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

U kunt bijvoorbeeld een **telemetrie** -tegel toevoegen voor de huidige Tempe ratuur van het apparaat. Dit doet u als volgt:
1. Een sjabloon voor een **apparaat** selecteren
1. Selecteer een **apparaatinstantie voor het apparaat dat** u wilt weer geven op een dashboard tegel. Vervolgens ziet u een lijst met de eigenschappen van het apparaat die kunnen worden gebruikt op de tegel.
1. Als u de tegel op het dash board wilt maken, klikt u op **Tempe ratuur** en sleept u deze naar het dashboard gebied. U kunt ook op het selectie vakje naast **Tempe ratuur** klikken en op **combi neren**klikken. In de volgende scherm afbeelding ziet u hoe u een Apparaataccount en een apparaatinstantie selecteert en vervolgens een temperatuur telemetrie-tegel op het dash board maakt.
1. Selecteer in de linkerbovenhoek op **Opslaan** om de tegel op het dash board op te slaan.

> [!div class="mx-imgBorder"]
> ![formulier Details van apparaat configureren met Details voor instellingen en eigenschappen](media/howto-add-tiles-to-your-dashboard/device-details.png)

Wanneer een operator nu het standaard toepassings dashboard weergeeft, zien ze de nieuwe tegel met de **Tempe ratuur** voor het apparaat. Elke tegel heeft een vooraf geselecteerde grafiek, grafiek, enzovoort die wordt weer gegeven wanneer de tegel wordt gemaakt. Gebruikers kunnen er echter voor kiezen om deze visualisatie te bewerken en te wijzigen. 

> [!div class="mx-imgBorder"]
> ![tabblad dash board met weer gegeven instellingen en eigenschappen voor de tegel](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)


## <a name="edit-tiles"></a>Tegels bewerken

Als u een tegel op het dash board wilt bewerken, klikt u eerst op **bewerken** in de linkerbovenhoek van de pagina, waardoor de bewerkings modus voor het dash board en alle tegels wordt geopend. 

> [!div class="mx-imgBorder"]
> ![Dashboard scherm met bewerkings modus geactiveerd voor een geselecteerde tegel](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Klik vervolgens op het **tandwiel** pictogram in de rechter bovenhoek van de tegel die u wilt bewerken. Hier kunt u aspecten van de tegel bewerken, met inbegrip van de titel, de visualisatie, de aggregatie, enzovoort.

> [!div class="mx-imgBorder"]
> ![vervolg keuzelijst voor de instellingen voor het samen voegen van tegels](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

U kunt de grafiek visualisatie ook wijzigen door op het **liniaal** pictogram op de tegel te klikken.

> [!div class="mx-imgBorder"]
> ![vervolg keuzelijst voor visualisatie-instellingen van Tegel](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Tegel typen

De volgende tabel bevat een overzicht van het gebruik van tegels in azure IoT Central:
 
| Tile | Dashboard | Beschrijving
| ----------- | ------- | ------- |
| Inhoud | Dash boards van toepassingen en apparaten instellen |Ondersteunde tegels kunnen worden weer gegeven op tegels die kop-en beschrijvings tekst bevatten. U kunt deze tegel ook gebruiken als koppelings tegel om een gebruiker in staat te stellen te navigeren naar een URL die betrekking heeft op uw toepassing.|
| afbeelding | Dash boards van toepassingen en apparaten instellen |Afbeeldings tegels geven een aangepaste afbeelding weer en kunnen worden geklikt. Gebruik een tegel voor afbeeldingen om afbeeldingen toe te voegen aan een dash board en schakel optioneel een gebruiker in om naar een URL te navigeren die relevant is voor uw toepassing.|
| Label | Toepassings dashboards |Met label tegels wordt aangepaste tekst op een dash board weer gegeven. U kunt de grootte van de tekst kiezen. Gebruik een label tegel om relevante informatie toe te voegen aan het dash board, zoals beschrijvingen, contact gegevens of Help.|
| Kaart | Dash boards van toepassingen en apparaten instellen |Kaart tegels geven de locatie en status van een apparaat op een kaart weer. U kunt bijvoorbeeld weer geven waar een apparaat zich bevindt en of de ventilator is ingeschakeld.|
| Lijndiagram | Dash boards van toepassingen en apparaten |Met lijn diagram tegels wordt een diagram weer gegeven van de cumulatieve meting voor een apparaat gedurende een bepaalde periode. U kunt bijvoorbeeld een lijn diagram weer geven waarin de gemiddelde Tempe ratuur en druk van een apparaat gedurende het afgelopen uur worden weer gegeven.|
| Staafdiagram | Dash boards van toepassingen en apparaten |In staafdiagram tegels wordt een grafiek weer gegeven met statistische metingen voor een apparaat gedurende een bepaalde periode. U kunt bijvoorbeeld een staaf diagram weer geven waarin de gemiddelde Tempe ratuur en druk van een apparaat gedurende het afgelopen uur worden weer gegeven.|
| Cirkel diagram | Dash boards van toepassingen en apparaten instellen |Cirkel diagram tegels geven een diagram van geaggregeerde metingen voor een apparaat gedurende een bepaalde periode.|
| Heat Map | Dash boards van toepassingen en apparaten instellen |Heatmap tegels geven informatie weer over de apparaatset, weer gegeven als kleuren.|
| Gebeurtenishistorie | Dash boards van toepassingen en apparaten |Met de tegels van gebeurtenis geschiedenis worden de gebeurtenissen voor een apparaat gedurende een bepaalde periode weer gegeven. U kunt dit bijvoorbeeld gebruiken om alle temperatuur wijzigingen voor een apparaat in het afgelopen uur weer te geven.|
| Statusgeschiedenis | Dash boards van toepassingen en apparaten |In tegels met status geschiedenis worden de meet waarden voor een tijds periode weer gegeven. U kunt dit bijvoorbeeld gebruiken om de temperatuur waarden voor een apparaat in het afgelopen uur weer te geven.|
| KPI | Dash boards van toepassingen en apparaten | KPI-tegels geven een geaggregeerde telemetrie of gebeurtenis meting voor een tijds periode weer. U kunt dit bijvoorbeeld gebruiken om de maximale Tempe ratuur voor een apparaat in het afgelopen uur weer te geven.|
| Laatste bekende waarde | Dash boards van toepassingen en apparaten |De laatste bekende waarde-tegels geven de meest recente waarde weer voor een telemetrie of status meting. U kunt deze tegel bijvoorbeeld gebruiken om de meest recente metingen van de Tempe ratuur, druk en lucht vochtigheid voor een apparaat weer te geven.|

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw Azure IoT Central standaard toepassings dashboard configureert, kunt u [leren hoe u installatie kopieën voorbereidt en uploadt](howto-prepare-images.md).