---
title: Het operator dashboard aanpassen in azure IoT Central | Microsoft Docs
description: In deze zelf studie leert u hoe u het operator dashboard kunt aanpassen in een IoT Central-toepassing en hoe u apparaten beheert.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 10/03/2019
ms.openlocfilehash: cb43facfc6b838a6e29a1b08e50648abf0f04911
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615301"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>Zelf studie: het operator dashboard aanpassen en apparaten beheren in azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In deze zelf studie ziet u als een bouwer hoe u het operator dashboard kunt aanpassen in uw Azure IoT Central in-Store Analytics-toepassing. Toepassings exploitanten kunnen het aangepaste dash board gebruiken om de toepassing uit te voeren en de gekoppelde apparaten te beheren.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * De naam van het dash board wijzigen
> * Afbeeldings tegels op het dash board aanpassen
> * Tegels schikken om de lay-out te wijzigen
> * Telemetrie-tegels toevoegen aan weergave voorwaarden
> * Eigenschappen tegels toevoegen om de details van het apparaat weer te geven
> * Opdracht tegels toevoegen om opdrachten uit te voeren

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelf studie begint, moet de opbouw functie de eerste zelf studie volt ooien om de Azure IoT Central in-Store Analytics-toepassing te maken en apparaten toe te voegen:

* [Een in-Store Analytics-toepassing maken in Azure IOT Central](./tutorial-in-store-analytics-create-app-pnp.md) (vereist)

## <a name="change-the-dashboard-name"></a>De naam van het dash board wijzigen
Als u het operator dashboard wilt aanpassen, bewerkt u het standaard dashboard in uw toepassing. U kunt desgewenst extra nieuwe Dash boards maken. De eerste stap bij het aanpassen van het dash board in uw toepassing is het wijzigen van de naam.

1. Ga naar de website van [Azure IOT Central Application Manager](https://aka.ms/iotcentral) .

1. Open de toepassing voor het controleren van de voor waarde die u hebt gemaakt in de zelf studie [een in-Store Analytics-toepassing maken in Azure IOT Central](./tutorial-in-store-analytics-create-app-pnp.md) .

1. Selecteer **bewerken** op de werk balk van het dash board. In de bewerkings modus kunt u het uiterlijk, de indeling en de inhoud van het dash board aanpassen.

    ![Dash board van Azure IoT Central bewerken](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-edit.png)

1. U kunt het linkerdeel venster eventueel verbergen. Het linkerdeel venster verbergen biedt een groter werk gebied voor het bewerken van het dash board.

1. Voer een beschrijvende naam in voor uw dash board in de naam van het **dash board.** In deze zelf studie wordt gebruikgemaakt van een fictief bedrijf met de naam Contoso en de naam van het voorbeeld dashboard is *Contoso dash board*. 

1. Selecteer **Opslaan**. Hiermee slaat u de wijzigingen in het dash board op en schakelt u de bewerkings modus uit.

    ![Dash board naam van Azure IoT Central wijzigen](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>Afbeeldings tegels op het dash board aanpassen
Een Azure IoT Central-toepassings dashboard bestaat uit een of meer tegels. Een tegel is een rechthoekige container voor het weer geven van inhoud op een dash board. U kunt verschillende typen inhoud koppelen aan tegels en u kunt tegels slepen, neerzetten en het formaat ervan wijzigen om een dashboard indeling aan te passen. Er zijn verschillende soorten tegels voor het weer geven van inhoud. Afbeeldings tegels bevatten afbeeldingen en u kunt een URL toevoegen waarmee gebruikers kunnen klikken op de installatie kopie. In label tegels wordt tekst zonder opmaak weer gegeven. Geprijsde tegels bevatten opgemaakte inhoud en stellen u in staat om een afbeelding, een URL, een titel en een prijs verlaging te definiëren die als HTML wordt weer gegeven. Op tegels voor telemetrie, eigenschappen of opdrachten worden apparaatspecifieke gegevens weer gegeven. 

In deze sectie leert u hoe u afbeeldings tegels kunt aanpassen in het dash board.

De tegel afbeelding aanpassen waarin een merk afbeelding op het dash board wordt weer gegeven:

1. Selecteer **bewerken** op de werk balk van het dash board. 

1. Selecteer **configureren** op de tegel installatie kopie met de voorbeeld afbeelding van het noorden wind. 

    ![Huisstijl afbeelding van Azure IoT Central bewerken](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-edit.png)

1. Wijzig de **titel**. De titel wordt weer gegeven wanneer een gebruiker de afbeelding aanwijst.

1. Selecteer **afbeelding**. Er wordt een dialoog venster geopend en u kunt een aangepaste installatie kopie uploaden. 

1. Geef desgewenst een URL op voor de installatie kopie.

1. Selecteer **update configuratie**. Met de knop **configuratie bijwerken** worden wijzigingen in het dash board opgeslagen en blijft de bewerkings modus ingeschakeld.

    ![Een merk afbeelding van Azure IoT Central opslaan](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-save.png)

1. Selecteer eventueel **configureren** op de tegel **documentatie**en geef een URL op voor de ondersteunings inhoud. 

De tegel installatie kopie aanpassen waarin een kaart van de sensor zones in de Store wordt weer gegeven:

1. Selecteer **configureren** in de tegel installatie kopie die de standaard opslag zone toewijzing weergeeft. 

1. Selecteer **afbeelding**en gebruik het dialoog venster voor het uploaden van een aangepaste installatie kopie van een opslag zone kaart. 

1. Selecteer **update configuratie**.

    ![Archief overzicht van Azure IoT Central opslaan](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-map-save.png)

    In het voor beeld contoso Store Maps worden vier zones weer gegeven: twee afhandelings zones, een zone voor kleding en persoonlijke verzorging, en een zone voor boodschappen en r. In deze zelf studie koppelt u Sens oren aan deze zones om telemetrie te leveren.

    ![Azure IoT Central Store-zones](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-zones.png)

1. Selecteer **Opslaan**. 

## <a name="arrange-tiles-to-modify-the-layout"></a>Tegels schikken om de lay-out te wijzigen
Een belang rijke stap bij het aanpassen van een dash board is het rangschikken van de tegels om een nuttige weer gave te maken. Toepassings operatoren gebruiken het dash board om telemetrie van apparaten te visualiseren, apparaten te beheren en voor waarden in een Store te bewaken. Azure IoT Central vereenvoudigt de Application Builder-taak voor het maken van een dash board. Met de bewerkings modus van het dash board kunt u snel tegels toevoegen, verplaatsen, verg Roten of verkleinen en verwijderen. Met de sjabloon **in-Store Analytics-Checker** wordt ook de taak voor het maken van een dash board vereenvoudigd. Het biedt een werkende dashboard indeling, met Sens oren die zijn verbonden en tegels die het aantal uitcheck lijnen en omgevings voorwaarden weer geven.

In deze sectie rangschikt u het dash board in de toepassings sjabloon **in-Store analyse-uitchecken** om een aangepaste indeling te maken.

Tegels verwijderen die u niet in uw toepassing wilt gebruiken:

1. Selecteer **bewerken** op de werk balk van het dash board. 

1. Selecteer **X verwijderen** om de volgende tegels te verwijderen: **terug naar alle zones**, **Ga naar de Store-dash board**, **wacht tijd**en alle drie de tegels die zijn gekoppeld aan de **kassa 3**. Het contoso Store-dash board maakt geen gebruik van deze tegels. 

    ![Tegels van Azure IoT Central verwijderen](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles.png)

1. Blader om de resterende dashboard tegels in beeld te brengen.

1. Selecteer **X verwijderen** om de volgende tegels te verwijderen: zone voor opheffen **van opruiming**, **zone voor uitchecken**, **instellingen voor bezetting**, instellingen van de **Thermo**staat en **omgevings omstandigheden**. 

   ![De resterende tegels van Azure IoT Central verwijderen](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles-2.png)

1. Selecteer **Opslaan**. Als u ongebruikte tegels verwijdert, maakt u ruimte vrij op de pagina bewerken en vereenvoudigt u de dashboard weergave voor Opera tors.

1. Bekijk uw wijzigingen in het dash board.

   ![Azure IoT Central na het verwijderen van tegels](./media/tutorial-in-store-analytics-customize-dashboard-pnp/after-delete-tiles.png)

Nadat u ongebruikte tegels hebt verwijderd, rangschikt u de overige tegels opnieuw om een geordende indeling te maken. De nieuwe indeling bevat ruimte voor tegels die u in een latere stap toevoegt.

De overige tegels opnieuw indelen:

1. Selecteer **Bewerken**.

1. Selecteer de tegel firmware voor de **bezetting** en sleep deze naar rechts van de tegel batterij voor de **bezetting** .

1. Selecteer de tegel firmware van de **Thermo** staat en sleep deze naar rechts van de **Thermo** -batterij tegel.

1. Selecteer **Opslaan**.

1. Bekijk de wijzigingen in de indeling. 

    ![Batterij tegels voor Azure IoT Central-firmware](./media/tutorial-in-store-analytics-customize-dashboard-pnp/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>Telemetrie-tegels toevoegen aan weergave voorwaarden
Nadat u de indeling van het dash board hebt aangepast, kunt u tegels toevoegen om telemetrie weer te geven. Als u een telemetrie-tegel wilt maken, selecteert u een sjabloon en apparaatinstantie en selecteert u apparaatspecifieke telemetrie om weer te geven op de tegel. De toepassings sjabloon **in de Store analyse-uitchecken** bevat verschillende telemetrie-tegels in het dash board. De vier tegels in de twee afhandelings zones tonen telemetrie van de gesimuleerde bezettings sensor. De tegel **personen verkeer** bevat aantallen in de twee afhandelings zones. 

In deze sectie voegt u twee meer telemetrie-tegels toe voor het weer geven van de telemetrie van de omgeving van de RuuviTag Sens oren die u hebt toegevoegd in de zelf studie [een in-Store Analytics-toepassing maken in Azure IOT Central](./tutorial-in-store-analytics-create-app-pnp.md) . 

Tegels toevoegen voor het weer geven van omgevings gegevens van de RuuviTag Sens oren:

1. Selecteer **Bewerken**.

1. Selecteer `RuuviTag` in de lijst met **apparaatprofielen** . 

1. Selecteer een **apparaatinstantie** van een van de twee RuuviTag Sens oren. Selecteer in het voor beeld contoso-archief `Zone 1 Ruuvi` om een telemetrie-tegel voor Zone 1 te maken. 

1. Selecteer `Relative humidity` en `temperature` in de **telemetrie** -lijst. Dit zijn de telemetriegegevens die voor elke zone op de tegel worden weer gegeven.

1. Selecteer **combi neren**. 

    ![RuuviTag-tegel 1 toevoegen aan Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-zone1-ruuvi.png)

    Er wordt een nieuwe tegel weer gegeven om de gecombineerde lucht vochtigheid en de temperatuur telemetrie voor de geselecteerde sensor weer te geven. 

1. Selecteer **configureren** op de nieuwe tegel voor de RuuviTag-sensor. 

1. Wijzig de **titel** in *zone 1 omgeving*. 

1. Selecteer **update configuratie**.

1. Herhaal de vorige stappen om een tegel te maken voor het tweede sensor exemplaar. Stel de **titel** in op *zone 2 omgeving* en selecteer vervolgens **update configuratie.**

1. Sleep de tegel met de titel **zone 2 omgeving** onder de tegel firmware van de **Thermo** staat. 

1. Sleep de tegel met de titel **zone 1 omgeving** onder de tegel **personen verkeer** . 

1. Selecteer **Opslaan**. In het dash board wordt de zone-telemetrie in de twee nieuwe tegels weer gegeven.

    ![Azure IoT Central alle RuuviTag-tegels](./media/tutorial-in-store-analytics-customize-dashboard-pnp/all-ruuvitag-tiles.png)

De tegel **personen verkeer** bewerken om telemetrie weer te geven voor slechts twee afhandelings zones:

1. Selecteer **Bewerken**. 

1. Selecteer **configureren** op de tegel **personen verkeer** .

1. Selecteer in **telemetrie** de optie **aantal 1**, **aantal 2**en **aantal 3**. 

1. Selecteer **update configuratie**. Hiermee wist u de bestaande configuratie op de tegel. 

1. Selecteer opnieuw **configureren** op de tegel **personen verkeer** .

1. Selecteer in **telemetrie** **aantal 1**en **aantal 2**. 

1. Selecteer **update configuratie**. 

1. Selecteer **Opslaan**.  Het bijgewerkte dash board geeft alleen aantallen weer voor uw twee afhandelings zones, die zijn gebaseerd op de gesimuleerde bezettings sensor.

    ![Azure IoT Central mensen verkeer twee banen](./media/tutorial-in-store-analytics-customize-dashboard-pnp/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>Eigenschappen tegels toevoegen om de details van het apparaat weer te geven
Toepassings operatoren gebruiken het dash board om apparaten te beheren en de status van de monitor te controleren. Voeg een tegel toe voor elke RuuviTag om Opera tors in staat te stellen de software versie te bekijken. 

Een eigenschappen tegel toevoegen voor elke RuuviTag:

1. Selecteer **Bewerken**.

1. Selecteer `RuuviTag` in de lijst met **apparaatprofielen** . 

1. Selecteer een **apparaatinstantie** van een van de twee RuuviTag Sens oren. Selecteer in het voor beeld contoso-archief `Zone 1 Ruuvi` om een telemetrie-tegel voor Zone 1 te maken. 

1. Selecteer **eigenschappen > software versie**.

1. Selecteer **combi neren**. 

1. Selecteer **configureren** op de zojuist gemaakte tegel met de titel **Software versie**. 

1. Wijzig de **titel** in *Ruuvi 1-software versie*.

1. Selecteer **update configuratie**. 

1. Sleep de tegel met de titel **Ruuv 1-software versie** onder de tegel **zone 1 omgeving** .

1. Herhaal de vorige stappen om een tegel voor de eigenschappen van een software versie voor de tweede RuuviTag te maken. 

1. Selecteer **Opslaan**.  

    ![Eigenschappen tegels van Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>Opdracht tegels toevoegen om opdrachten uit te voeren
Toepassings operators maken ook gebruik van het dash board om apparaten te beheren door opdrachten uit te voeren. U kunt opdracht tegels toevoegen aan het dash board waarmee vooraf gedefinieerde opdrachten op een apparaat worden uitgevoerd. In deze sectie voegt u een opdracht tegel toe om Opera tors in te scha kelen om de Rigado-Gateway opnieuw op te starten. 

Een opdracht tegel toevoegen om de gateway opnieuw op te starten:

1. Selecteer **Bewerken**. 

1. Selecteer `C500` in de lijst met **apparaatprofielen** . Dit is de sjabloon voor de Rigado C500-gateway. 

1. Selecteer de instantie van de gateway in het **apparaatexemplaar**.

1. Selecteer de **opdracht > opnieuw** op te starten en sleep deze naar het dash board naast de Store-kaart. 

1. Selecteer **Opslaan**. 

1. Bekijk uw voltooide contoso-dash board. 

    ![Aanpassing van Azure IoT Central volt ooien van dash board](./media/tutorial-in-store-analytics-customize-dashboard-pnp/completed-dashboard.png)

1. Selecteer desgewenst de tegel **opnieuw opstarten** om de opdracht opnieuw opstarten op de gateway uit te voeren.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie heeft u het volgende geleerd:

* De naam van het dash board wijzigen
* Afbeeldings tegels op het dash board aanpassen
* Tegels schikken om de lay-out te wijzigen
* Telemetrie-tegels toevoegen aan weergave voorwaarden
* Eigenschappen tegels toevoegen om de details van het apparaat weer te geven
* Opdracht tegels toevoegen om opdrachten uit te voeren

Nu u het dash board hebt aangepast in uw Azure IoT Central in-Store Analytics-toepassing, volgt u de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Gegevens exporteren en inzichten visualiseren](./tutorial-in-store-analytics-export-data-visualize-insights-pnp.md)
