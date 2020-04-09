---
title: Azure IoT Central persoonlijke dashboards maken | Microsoft Documenten
description: Leer als gebruiker hoe u uw persoonlijke dashboards maken en beheren.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 06225e284000d7f10f575be08cd683488abec339
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985487"
---
# <a name="create-and-manage-multiple-dashboards"></a>Meerdere dashboards maken en beheren

Het **dashboard** is de pagina die wordt geladen wanneer u voor het eerst naar uw toepassing navigeert. Een **bouwer** in uw toepassing definieert het standaardtoepassingsdashboard voor alle gebruikers. U bovendien uw eigen, gepersonaliseerde applicatiedashboard maken. U verschillende dashboards hebben die verschillende gegevens weergeven en er tussen schakelen.

Als u een **beheerder** van de toepassing bent, u ook maximaal 10 dashboards op toepassingsniveau maken om te delen met andere gebruikers van de toepassing. Alleen **beheerders** hebben de mogelijkheid om dashboards op toepassingsniveau te maken, bewerken en verwijderen. 

## <a name="create-dashboard"></a>Dashboard maken

In de volgende schermafbeelding wordt het dashboard weergegeven in een toepassing die is gemaakt met de sjabloon **Aangepaste toepassing.** U het standaardtoepassingsdashboard vervangen door een persoonlijk dashboard of als u een beheerder bent, een ander dashboard op toepassingsniveau. Selecteer hiervoor **+ Nieuw** linksboven op de pagina.
 
> [!div class="mx-imgBorder"]
> ![Dashboard voor toepassingen op basis van de sjabloon 'Aangepaste toepassing'](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Als **u /Nieuw selecteert,** wordt de dashboardeditor geopend. In de editor u uw dashboard een naam geven en items uit de bibliotheek kiezen. De bibliotheek bevat de tegels en dashboardprimitieven die u gebruiken om het dashboard aan te passen.

> [!div class="mx-imgBorder"]
> ![Dashboardbibliotheek](media/howto-create-personal-dashboards/dashboard-library.png)

Als u een **beheerder** van de toepassing bent, krijgt u de optie om te schakelen als u een dashboard op persoonlijk niveau of een dashboard op toepassingsniveau wilt maken. Als u een dashboard op persoonlijk niveau maakt, alleen u het zien. Als u een dashboard op toepassingsniveau maakt, kan elke gebruiker van de toepassing deze zien. Nadat u een titel hebt ingevoerd en het type dashboard hebt geselecteerd dat u wilt maken, u later tegels opslaan en toevoegen. Of als u nu klaar bent en een apparaatsjabloon en apparaatexemplaar hebt toegevoegd, u doorgaan en uw eerste tegel maken. 

> [!div class="mx-imgBorder"]
> ![Formulier apparaatgegevens configureren met details voor temperatuur](media/howto-create-personal-dashboards/device-details.png)

U bijvoorbeeld een **telemetrietegel** toevoegen voor de huidige temperatuur van het apparaat. Dit doet u als volgt:
1. Een **apparaatsjabloon selecteren**
1. Selecteer een **apparaatinstantie** voor het apparaat dat u op een dashboardtegel wilt zien. Vervolgens ziet u een lijst met de eigenschappen van het apparaat die op de tegel kunnen worden gebruikt.
1. Als u de tegel op het dashboard wilt maken, klikt u op **Temperatuur** en sleept u deze naar het dashboardgebied. U ook op het selectievakje naast **Temperatuur** klikken en op **Combineren**klikken. In de volgende schermafbeelding wordt een apparaatsjabloon en apparaatinstantie geselecteerd en wordt een tegel Temperatuurtelemetrie op het dashboard gemaakt.
1. Selecteer **Opslaan** linksboven om de tegel op te slaan in het dashboard.

> [!div class="mx-imgBorder"]
> ![Tabblad Dashboard met details voor de tegel Temperatuur](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Wanneer u nu uw persoonlijke dashboard bekijkt, ziet u de nieuwe tegel met de **instelling Temperatuur** voor het apparaat:

> [!div class="mx-imgBorder"]
> ![Tabblad Dashboard met details voor de tegel Temperatuur](media/howto-create-personal-dashboards/temperature-tile-complete.png)

U andere tegeltypen in de bibliotheek verkennen om te ontdekken hoe u uw persoonlijke dashboards verder aanpassen.

Zie [Tegels toevoegen aan uw dashboard voor](howto-add-tiles-to-your-dashboard.md)meer informatie over het gebruik van tegels in Azure IoT Central.

## <a name="manage-dashboards"></a>Dashboards beheren

U verschillende persoonlijke dashboards hebben en er tussen schakelen of kiezen uit een van de standaardtoepassingsdashboards:

> [!div class="mx-imgBorder"]
> ![Schakelen tussen dashboards](media/howto-create-personal-dashboards/switch-dashboards.png)

U uw persoonlijke dashboards bewerken en alle dashboards verwijderen die u niet meer nodig hebt. Als u een **beheerder bent,** hebt u ook de mogelijkheid om dashboards op toepassingsniveau ook te bewerken of te verwijderen.

> [!div class="mx-imgBorder"]
> ![Dashboards verwijderen](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u persoonlijke dashboards maken en beheren, u [meer informatie krijgen over het beheren van uw toepassingsvoorkeuren](howto-manage-preferences.md)
