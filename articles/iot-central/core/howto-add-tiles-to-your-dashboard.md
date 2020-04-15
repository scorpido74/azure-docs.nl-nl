---
title: Tegels toevoegen aan uw dashboard | Microsoft Documenten
description: Als bouwer leert u hoe u het standaard Azure IoT Central-toepassingsdashboard configureert.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f75e5a28f7ec56750432e74ee48ba68491a5e481
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310286"
---
# <a name="configure-the-application-dashboard"></a>Het toepassingsdashboard configureren

Het **dashboard** is de pagina die wordt geladen wanneer gebruikers die toegang hebben tot de toepassing naar de URL van de toepassing navigeren. Als u de toepassing hebt gemaakt op basis van een van de **toepassingssjablonen,** heeft uw toepassing een vooraf gedefinieerd dashboard om te starten. Als u uw toepassing hebt gemaakt op de sjabloon Voor de **toepassing Legacy,** is uw dashboard leeg om te starten.

> [!NOTE]
> Gebruikers kunnen naast het standaardtoepassingsdashboard [meerdere dashboards maken.](howto-create-personal-dashboards.md) Deze dashboards kunnen alleen persoonlijk zijn voor de gebruiker of worden gedeeld door alle gebruikers van de toepassing. 

## <a name="add-tiles"></a>Tegels toevoegen

In de volgende schermafbeelding wordt het dashboard weergegeven in een toepassing die is gemaakt met de sjabloon **Aangepaste toepassing.** Als u het standaarddashboard voor uw toepassing wilt aanpassen, selecteert u **Bewerken** linksboven op de pagina.

> [!div class="mx-imgBorder"]
> ![Dashboard voor toepassingen op basis van de sjabloon 'Voorbeeld van Contoso'](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

Als **u Bewerken selecteert,** wordt het deelvenster dashboardbibliotheek geopend. De bibliotheek bevat de tegels en dashboardprimitieven die u gebruiken om het dashboard aan te passen.

> [!div class="mx-imgBorder"]
> ![Dashboardbibliotheek](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

U bijvoorbeeld een **telemetrietegel** toevoegen voor de huidige temperatuur van het apparaat. Dit doet u als volgt:
1. Een **apparaatsjabloon selecteren**
1. Selecteer een **apparaatinstantie** voor het apparaat dat u op een dashboardtegel wilt zien. Vervolgens ziet u een lijst met de eigenschappen van het apparaat die op de tegel kunnen worden gebruikt.
1. Als u de tegel op het dashboard wilt maken, klikt u op **Temperatuur** en sleept u deze naar het dashboardgebied. U ook op het selectievakje naast **Temperatuur** klikken en op **Combineren**klikken. In de volgende schermafbeelding wordt een apparaatsjabloon en apparaatinstantie geselecteerd en wordt een tegel Temperatuurtelemetrie op het dashboard gemaakt.
1. Selecteer **Opslaan** linksboven om de tegel op te slaan in het dashboard.

> [!div class="mx-imgBorder"]
> ![Formulier Apparaatgegevens configureren met details voor instellingen en eigenschappen](media/howto-add-tiles-to-your-dashboard/device-details.png)

Wanneer een operator nu het standaardtoepassingsdashboard bekijkt, ziet deze de nieuwe tegel met de **temperatuur** voor het apparaat. Elke tegel heeft een vooraf geselecteerde grafiek, grafiek, etc. die wordt weergegeven wanneer de tegel wordt gemaakt. Gebruikers kunnen er echter voor kiezen om deze visualisatie te bewerken en te wijzigen. 

> [!div class="mx-imgBorder"]
> ![Tabblad Dashboard met weergegeven instellingen en eigenschappen voor de tegel](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>Tegels bewerken

Als u een tegel op het dashboard wilt bewerken, klikt u eerst op **Bewerken** linksboven op de pagina, waarmee de bewerkingsmodus voor het dashboard en alle tegels wordt geopend. 

> [!div class="mx-imgBorder"]
> ![Dashboardscherm met bewerkingsmodus geactiveerd voor een geselecteerde tegel](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Klik vervolgens op het **tandwielpictogram** in de rechterbovenhoek van de tegel die u wilt bewerken. Hier u aspecten van de tegel bewerken, waaronder de titel, de visualisatie, aggregatie, enz.

> [!div class="mx-imgBorder"]
> ![Vervolgkeuzelijst voor instellingen voor tegelaggregatie](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

U de grafiekvisualisatie ook wijzigen door op het pictogram **Liniaal** op de tegel te klikken.

> [!div class="mx-imgBorder"]
> ![Vervolgkeuzelijst voor instellingen voor tegelvisualisatie](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Tegeltypen

In de volgende tabel wordt een overzicht van het gebruik van tegels in Azure IoT Central:
 
| Tegel | Dashboard | Beschrijving
| ----------- | ------- | ------- |
| Inhoud | Dashboards voor toepassing en apparaatinstellen |Door Markdown ondersteunde tegels zijn klikbare tegels die koptekst en beschrijvingstekst weergeven. U deze tegel ook gebruiken als koppelingstegel om een gebruiker in staat te stellen naar een URL met betrekking tot uw toepassing te navigeren.|
| Installatiekopie | Dashboards voor toepassing en apparaatinstellen |Afbeeldingstegels geven een aangepaste afbeelding weer en kunnen klikbaar zijn. Gebruik een afbeeldingstegel om afbeeldingen aan een dashboard toe te voegen en laat een gebruiker optioneel navigeren naar een URL die relevant is voor uw toepassing.|
| Label | Toepassingsdashboards |Labeltegels geven aangepaste tekst weer op een dashboard. U de grootte van de tekst kiezen. Gebruik een labeltegel om relevante informatie toe te voegen aan het dashboard, zoals beschrijvingen, contactgegevens of help.|
| Kaart | Dashboards voor toepassingen en apparaten |Kaarttegels geven de locatie van een apparaat weer op een kaart. U ook maximaal 100 punten van de locatiegeschiedenis van een apparaat weergeven. U bijvoorbeeld een voorbeeldroute weergeven van waar een apparaat zich de afgelopen week heeft bevindt.|
| Lijndiagram | Dashboards voor toepassingen en apparaten |Lijndiagramtegels geven een grafiek met geaggregeerde meting voor een apparaat voor een bepaalde periode weer. U bijvoorbeeld een lijndiagram weergeven dat de gemiddelde temperatuur en druk van een apparaat voor het laatste uur weergeeft.|
| Staafdiagram | Dashboards voor toepassingen en apparaten |Staafdiagramtegels geven een grafiek weer met geaggregeerde metingen voor een apparaat voor een periode. U bijvoorbeeld een staafdiagram weergeven met de gemiddelde temperatuur en druk van een apparaat van het afgelopen uur.|
| Cirkeldiagram | Dashboards voor toepassing en apparaatinstellen |Cirkeldiagramtegels geven een grafiek met geaggregeerde metingen voor een apparaat voor een periode weer.|
| Heat Map | Dashboards voor toepassing en apparaatinstellen |Heat Map-tegels geven informatie weer over de apparaatset, weergegeven als kleuren.|
| Gebeurtenisgeschiedenis | Dashboards voor toepassingen en apparaten |Gebeurtenisgeschiedenis-tegels geven de gebeurtenissen voor een apparaat gedurende een bepaalde periode weer. U het bijvoorbeeld gebruiken om alle temperatuurwijzigingen voor een apparaat gedurende het laatste uur weer te geven.|
| Staatsgeschiedenis | Dashboards voor toepassingen en apparaten |Op de tegels voor de statusgeschiedenis worden de meetwaarden voor een bepaalde periode weergegeven. U het bijvoorbeeld gebruiken om de temperatuurwaarden voor een apparaat gedurende het laatste uur weer te geven.|
| KPI | Dashboards voor toepassingen en apparaten | KPI-tegels geven een geaggregeerde telemetrie- of gebeurtenismeting voor een periode weer. U het bijvoorbeeld gebruiken om de maximale temperatuur van een apparaat gedurende het laatste uur weer te geven.|
| Laatst bekende waarde | Dashboards voor toepassingen en apparaten |Laatst bekende waardetegels geven de laatste waarde weer voor een telemetrie- of statusmeting. U deze tegel bijvoorbeeld gebruiken om de meest recente metingen van temperatuur, druk en vochtigheid voor een apparaat weer te geven.|

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw Azure IoT Central-standaardtoepassingsdashboard configureren, u [meer informatie geven over het maken van een persoonlijk dashboard.](howto-create-personal-dashboards.md)
