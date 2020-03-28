---
title: Zelfstudie - Het operatordashboard aanpassen in Azure IoT Central
description: In deze zelfstudie ziet u hoe u het bedieningsdashboard in een IoT Central-toepassing aanpassen en apparaten beheren.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: fea3440dd7c017b85d6fffa2ec0fe5d26cd98b97
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77022168"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>Zelfstudie: Het operatordashboard aanpassen en apparaten beheren in Azure IoT Central


In deze zelfstudie leert u als bouwer hoe u het operatordashboard aanpassen in uw Azure IoT Central-analysetoepassing. Toepassingsbeheerders kunnen het aangepaste dashboard gebruiken om de toepassing uit te voeren en de aangesloten apparaten te beheren.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * De naam van het dashboard wijzigen
> * Afbeeldingstegels op het dashboard aanpassen
> * Tegels rangschikken om de indeling te wijzigen
> * Telemetrietegels toevoegen om voorwaarden weer te geven
> * Eigenschaptegels toevoegen om apparaatgegevens weer te geven
> * Opdrachttegels toevoegen om opdrachten uit te voeren

## <a name="prerequisites"></a>Vereisten

De bouwer moet de zelfstudie voltooien om de Azure IoT Central-analysetoepassing in de winkel te maken en apparaten toe te voegen:

* [Een in-store analytics-toepassing maken in Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) (Vereist)

## <a name="change-the-dashboard-name"></a>De naam van het dashboard wijzigen
Als u het bedieningsdashboard wilt aanpassen, moet u het standaarddashboard in uw toepassing bewerken. U ook extra nieuwe dashboards maken. De eerste stap om het dashboard in uw toepassing aan te passen, is het wijzigen van de naam.

1. Navigeer naar de website [azure IoT Central-toepassingsbeheer.](https://aka.ms/iotcentral)

1. Open de toepassing voor conditiebewaking die u hebt gemaakt in de [analysetoepassing Een analyse maken in Azure IoT Central.](./tutorial-in-store-analytics-create-app-pnp.md)

1. Selecteer **Bewerken** op de werkbalk van het dashboard. In de bewerkingsmodus u het uiterlijk, de lay-out en de inhoud van het dashboard aanpassen.

    ![Azure IoT Central-bewerkingsdashboard](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-edit.png)

1. Verberg eventueel het linkerdeelvenster. Als u het linkerdeelvenster verbergt, krijgt u een groter werkgebied voor het bewerken van het dashboard.

1. Voer een vriendelijke naam in voor uw dashboard in **dashboardnaam.** Deze zelfstudie maakt gebruik van een fictief bedrijf genaamd Contoso, en het voorbeeld dashboard naam is *Contoso dashboard*. 

1. Selecteer **Opslaan**. Uw wijzigingen worden opgeslagen in het dashboard en de bewerkingsmodus is uitgeschakeld.

    ![Azure IoT Central wijzigt dashboardnaam](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>Afbeeldingstegels op het dashboard aanpassen
Een Azure IoT Central-toepassingsdashboard bestaat uit een of meer tegels. Een tegel is een rechthoekige container voor het weergeven van inhoud op een dashboard. U koppelt verschillende soorten inhoud aan tegels en u sleept, neerzet en formaat tegels om een dashboardindeling aan te passen. Er zijn verschillende soorten tegels voor het weergeven van inhoud. Afbeeldingstegels bevatten afbeeldingen en u een URL toevoegen waarmee gebruikers op de afbeelding kunnen klikken. Labeltegels geven platte tekst weer. Markeringstegels bevatten opgemaakte inhoud en u een afbeelding, een URL, een titel en afwaarderingscode instellen die wordt weergegeven als HTML. Telemetrie-, eigenschap- of opdrachttegels geven apparaatspecifieke gegevens weer. 

In deze sectie leert u hoe u afbeeldingstegels op het dashboard aanpassen.

Ga als u erop aan de afbeeldingstegel aanpassen die een merkafbeelding op het dashboard weergeeft:

1. Selecteer **Bewerken** op de werkbalk van het dashboard. 

1. Selecteer **Configureren** op de afbeeldingstegel die de merkafbeelding van Northwind weergeeft. 

    ![Azure IoT Central bewerken merkimage](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-edit.png)

1. De **titel wijzigen**. De titel wordt weergegeven wanneer een gebruiker boven de afbeelding zweeft.

1. Selecteer **Afbeelding**. Er wordt een dialoogvenster geopend en u een aangepaste afbeelding uploaden. 

1. Geef eventueel een URL op voor de afbeelding.

1. Selecteer **Configuratie bijwerken**. Met de **knop Configuratie bijwerken** worden wijzigingen in het dashboard opgeslagen en wordt de bewerkingsmodus ingeschakeld.

    ![Azure IoT Central opgeslagen merkafbeelding](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-save.png)

1. Selecteer optioneel **Configureren** op de tegel met de titel **Documentatie**en geef een URL op voor ondersteuningsinhoud. 

Ga als u erop toe zoek naar de afbeeldingstegel waarop een kaart van de sensorzones in de winkel wordt weergegeven:

1. Selecteer **Configureren** op de afbeeldingstegel waarop de standaardkaart voor winkelzone wordt weergegeven. 

1. Selecteer **Afbeelding**en gebruik het dialoogvenster om een aangepaste afbeelding van een winkelzonekaart te uploaden. 

1. Selecteer **Configuratie bijwerken**.

    ![Azure IoT Central bewaar winkelkaart](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-map-save.png)

    De voorbeeld-Contoso-winkelkaart toont vier zones: twee kassazones, een zone voor kleding en persoonlijke verzorging en een zone voor boodschappen en delicatessenwinkel. In deze zelfstudie associeert u sensoren met deze zones om telemetrie te bieden.

    ![Azure IoT Central-winkelzones](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-zones.png)

1. Selecteer **Opslaan**. 

## <a name="arrange-tiles-to-modify-the-layout"></a>Tegels rangschikken om de indeling te wijzigen
Een belangrijke stap bij het aanpassen van een dashboard is het herschikken van de tegels om een nuttige weergave te maken. Toepassingsbeheerders gebruiken het dashboard om apparaattelemetrie te visualiseren, apparaten te beheren en de omstandigheden in een winkel te controleren. Azure IoT Central vereenvoudigt de taak van de toepassingsbouwer voor het maken van een dashboard. Met de dashboardbewerkingsmodus u tegels snel toevoegen, verplaatsen, vergroten en verwijderen. De **in-store analytics - checkout** applicatie template vereenvoudigt ook de taak van het maken van een dashboard. Het biedt een werkende dashboardlay-out, met aangesloten sensoren, en tegels die kassalijntellingen en omgevingscondities weergeven.

In deze sectie herschikt u het dashboard in de **sjabloon In-store analytics -** checkout-toepassingsom een aangepaste lay-out te maken.

Ga als volgende keer op het volgende voor het verwijderen van tegels die u niet in uw toepassing wilt gebruiken:

1. Selecteer **Bewerken** op de werkbalk van het dashboard. 

1. Selecteer **X Verwijderen** om de volgende tegels te verwijderen: Terug naar alle **zones,** Bezoek **winkeldashboard,** **Wachttijd**en alle drie de tegels die zijn gekoppeld aan **Afhandeling 3**. Het dashboard van de Contoso-winkel gebruikt deze tegels niet. 

    ![Azure IoT Central verwijdert tegels](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles.png)

1. Schuif om de resterende dashboardtegels in beeld te brengen.

1. Selecteer **X Delete** om de volgende tegels te verwijderen: **Opwarm-checkout zone**, **Cool-down checkout zone,** **Bezettingsgraad sensor instellingen,** Thermostaat sensor **instellingen,** en **Omgeving voorwaarden**. 

   ![Azure IoT Central verwijdert resterende tegels](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles-2.png)

1. Selecteer **Opslaan**. Als u ongebruikte tegels verwijdert, wordt ruimte vrijgemaakt op de bewerkingspagina en wordt de dashboardweergave voor operators vereenvoudigd.

1. Bekijk uw wijzigingen in het dashboard.

   ![Azure IoT Central na het verwijderen van tegels](./media/tutorial-in-store-analytics-customize-dashboard-pnp/after-delete-tiles.png)

Nadat u ongebruikte tegels hebt verwijderd, herschikt u de resterende tegels om een georganiseerde indeling te maken. De nieuwe lay-out bevat ruimte voor tegels die u in een latere stap toevoegt.

Ga als u de resterende tegels opnieuw rangschikken:

1. Selecteer **Bewerken**.

1. Selecteer de **firmwaretegel Bezetting** en sleep deze naar rechts van de tegel **Bezettingsbatterij.**

1. Selecteer de **firmwaretegel Thermostaat** en sleep deze naar rechts van de batterijtegel **Thermostaat.**

1. Selecteer **Opslaan**.

1. Uw lay-outwijzigingen weergeven. 

    ![Azure IoT Central-firmwarebatterijtegels](./media/tutorial-in-store-analytics-customize-dashboard-pnp/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>Telemetrietegels toevoegen om voorwaarden weer te geven
Nadat u de indeling van het dashboard hebt aangepast, u tegels toevoegen om telemetrie weer te geven. Als u een telemetrietegel wilt maken, selecteert u een apparaatsjabloon en apparaatinstantie en selecteert u apparaatspecifieke telemetrie die in de tegel wordt weergegeven. De **sjabloon In-store analytics - checkout** application bevat verschillende telemetrietegels in het dashboard. De vier tegels in de twee kassazones geven telemetrie weer van de gesimuleerde bezettingssensor. De **verkeerstegel Personen** toont tellingen in de twee afrekenzones. 

In deze sectie voegt u nog twee telemetrietegels toe om omgevingstelemetrie weer te geven van de RuuviTag-sensoren die u hebt toegevoegd in de [analysetoepassing Een analyse maken maken in Azure IoT Central.](./tutorial-in-store-analytics-create-app-pnp.md) 

Ga als u tegels toevoegen om omgevingsgegevens van de RuuviTag-sensoren weer te geven:

1. Selecteer **Bewerken**.

1. Selecteer `RuuviTag` in de lijst **Met apparaatsjabloon.** 

1. Selecteer een **apparaatexemplaar** van een van de twee RuuviTag-sensoren. Selecteer in het voorbeeld Contoso-archief de optie `Zone 1 Ruuvi` om een telemetrietegel voor zone 1 te maken. 

1. Selecteer `Relative humidity` `temperature` en in de lijst **Telemetrie.** Dit zijn de telemetrie-items die voor elke zone op de tegel worden weergegeven.

1. Selecteer **Combineren**. 

    ![Azure IoT Central voegt RuuviTag-tegel 1 toe](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-zone1-ruuvi.png)

    Een nieuwe tegel lijkt gecombineerde vochtigheid en temperatuurtelemetrie voor de geselecteerde sensor weer te geven. 

1. Selecteer **Configureren** op de nieuwe tegel voor de RuuviTag-sensor. 

1. Wijzig de **titel** in *zone 1-omgeving*. 

1. Selecteer **Configuratie bijwerken**.

1. Herhaal de vorige stappen om een tegel te maken voor de tweede sensorinstantie. Stel de **eigenschap** in *op zone 2-omgeving* en selecteer Vervolgens De configuratie **bijwerken.**

1. Sleep de tegel met de titel **Zone 2-omgeving** onder de **firmwaretegel Thermostaat.** 

1. Sleep de tegel met de titel **Zone 1-omgeving** onder de **verkeerstegel Personen.** 

1. Selecteer **Opslaan**. Het dashboard toont zonetelemetrie in de twee nieuwe tegels.

    ![Azure IoT Central alle RuuviTag-tegels](./media/tutorial-in-store-analytics-customize-dashboard-pnp/all-ruuvitag-tiles.png)

Ga als u de **verkeerstegel Personen wilt** bewerken om telemetrie weer te geven voor slechts twee afrekenzones:

1. Selecteer **Bewerken**. 

1. Selecteer **Configureren** op de **verkeerstegel Personen.**

1. Selecteer **in Telemetrie** **nummer 1,** **tel 2**en tel **3**. 

1. Selecteer **Configuratie bijwerken**. Hiermee wordt de bestaande configuratie op de tegel gewist. 

1. Selecteer Opnieuw **configureren** op de **verkeerstegel Personen.**

1. Selecteer **in Telemetrie** **nummer 1**en tel **2**. 

1. Selecteer **Configuratie bijwerken**. 

1. Selecteer **Opslaan**.  Het bijgewerkte dashboard wordt weergegeven voor slechts uw twee kassazones, die zijn gebaseerd op de gesimuleerde bezettingssensor.

    ![Azure IoT Central mensen verkeer twee rijstroken](./media/tutorial-in-store-analytics-customize-dashboard-pnp/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>Eigenschaptegels toevoegen om apparaatgegevens weer te geven
Toepassingsbeheerders gebruiken het dashboard om apparaten te beheren en de status te controleren. Voeg voor elke RuuviTag een tegel toe om operators in staat te stellen de softwareversie te bekijken. 

Ga als lid van het werk om voor elke RuuviTag een eigenschapstegel toe te voegen:

1. Selecteer **Bewerken**.

1. Selecteer `RuuviTag` in de lijst **Met apparaatsjabloon.** 

1. Selecteer een **apparaatexemplaar** van een van de twee RuuviTag-sensoren. Selecteer in het voorbeeld Contoso-archief de optie `Zone 1 Ruuvi` om een telemetrietegel voor zone 1 te maken. 

1. Selecteer **Eigenschappen > Software-versie**.

1. Selecteer **Combineren**. 

1. Selecteer **Configureren** op de nieuw gemaakte tegel met de titel **Softwareversie**. 

1. Wijzig de **titel in** *ruuvi 1-softwareversie.*

1. Selecteer **Configuratie bijwerken**. 

1. Sleep de tegel met de titel **Ruuv 1-softwareversie** onder de **tegel Zone 1-omgeving.**

1. Herhaal de vorige stappen om een eigenschaptegel voor softwareversies te maken voor de tweede RuuviTag. 

1. Selecteer **Opslaan**.  

    ![Azure IoT Central RuuviTag-eigenschaptegels](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>Opdrachttegels toevoegen om opdrachten uit te voeren
Toepassingsbeheerders gebruiken het dashboard ook om apparaten te beheren door opdrachten uit te voeren. U opdrachttegels toevoegen aan het dashboard waarmee vooraf gedefinieerde opdrachten op een apparaat worden uitgevoerd. In deze sectie voegt u een opdrachttegel toe waarmee operators de Rigado-gateway opnieuw kunnen opstarten. 

Ga als u een opdrachttegel toevoegt om de gateway opnieuw op te starten:

1. Selecteer **Bewerken**. 

1. Selecteer `C500` in de lijst **Met apparaatsjabloon.** Het is de sjabloon voor de Rigado C500 gateway. 

1. Selecteer de gateway-instantie in **apparaatinstantie**.

1. Selecteer **Opdracht > Opnieuw opstarten** en sleep deze naar het dashboard naast de winkelkaart. 

1. Selecteer **Opslaan**. 

1. Bekijk uw voltooide Contoso-dashboard. 

    ![Azure IoT Central complete dashboardaanpassing](./media/tutorial-in-store-analytics-customize-dashboard-pnp/completed-dashboard.png)

1. Selecteer de tegel **Opnieuw opstarten** om de opdracht opnieuw opstarten op uw gateway uit te voeren.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u het volgende geleerd:

* De naam van het dashboard wijzigen
* Afbeeldingstegels op het dashboard aanpassen
* Tegels rangschikken om de indeling te wijzigen
* Telemetrietegels toevoegen om voorwaarden weer te geven
* Eigenschaptegels toevoegen om apparaatgegevens weer te geven
* Opdrachttegels toevoegen om opdrachten uit te voeren

Nu u het dashboard in uw Azure IoT Central-analysetoepassing in de winkel hebt aangepast, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Gegevens exporteren en inzichten visualiseren](./tutorial-in-store-analytics-export-data-visualize-insights-pnp.md)
