---
title: Zelfstudie van het IoT Digital Distribution Center | Microsoft Documenten
description: Een zelfstudie van de toepassingssjabloon voor het digitale distributiecentrum voor IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 93a77d73b5cc249c39609f98f055a7b1927dd6ed
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77025483"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Zelfstudie: Een sjabloon voor een aanvraag voor een digitaal distributiecentrum implementeren en doorlopen



In deze zelfstudie ziet u hoe u aan de slag gaan door een iot **Central-sjabloon voor de toepassing** van het digitale distributiecentrum te implementeren. U leert hoe u de sjabloon implementeert, wat er uit het vak wordt opgenomen en wat u vervolgens wilt doen.

In deze zelfstudie leer je hoe je, 
* Toepassing van een digitaal distributiecentrum maken 
* Loop door de applicatie 

## <a name="prerequisites"></a>Vereisten
* Geen specifieke vereisten nodig om deze app te implementeren
* Aanbevolen om een Azure-abonnement te hebben, maar u het zelfs zonder

## <a name="create-digital-distribution-center-application-template"></a>Toepassingssjabloon voor digitaal distributiecentrum maken

U toepassing maken met de volgende stappen

1. Navigeer naar de website azure IoT Central-toepassingsbeheer. Selecteer **Bouwen** op de navigatiebalk aan de linkerkant en klik op het tabblad **Detailhandel.**

    > [!div class="mx-imgBorder"]
    > ![Digitaal distributiecentrum](./media/tutorial-iot-central-ddc/iotc-retail-homepage.png)

2. Tabblad **Detailhandel** selecteren en **app maken** selecteren onder toepassing digitaal **distributiecentrum**

3. **Maak app** opent Nieuw aanvraagformulier en vul de gevraagde details zoals hieronder weergegeven.
   **Toepassingsnaam:** u de standaard voorgestelde naam gebruiken of uw vriendelijke toepassingsnaam invoeren.
   **URL**: u de voorgestelde standaard-URL gebruiken of uw vriendelijke unieke gedenkwaardige URL invoeren. Vervolgens wordt de standaardinstelling aanbevolen als u al een Azure-abonnement hebt. U beginnen met een gratis proefprijsplan van 7 dagen en ervoor kiezen om op elk gewenst moment om te zetten in een standaardprijsplan voordat de gratis trail verloopt.
   **Factureringsgegevens:** de gegevens directory, Azure-abonnementen en regio's zijn vereist om de resources in te richten.
   **Maken:** Selecteer maken onder aan de pagina om uw toepassing te implementeren.

    > [!div class="mx-imgBorder"]
    > ![Digitaal distributiecentrum](./media/tutorial-iot-central-ddc/ddc-create.png)

    > [!div class="mx-imgBorder"]
    > ![Factureringsgegevens voor digitale distributie](./media/tutorial-iot-central-ddc/ddc-create-billinginfo.png)

## <a name="walk-through-the-application-dashboard"></a>Door het toepassingsdashboard lopen 

Nadat u de app-sjabloon hebt geïmplementeerd, is uw standaarddashboard een portal die op de operator is gericht op een distributiecentrum. Northwind Trader is een fictieve leverancier van distributiecentrumoplossingen die transportsystemen beheren. 

In dit dashboard ziet u één gateway en één camera die fungeert als een IoT-apparaat. Gateway biedt telemetrie over pakketten zoals geldig, ongeldig, niet-geïdentificeerd en grootte, samen met de bijbehorende apparaattweelingeigenschappen. Alle downstream-opdrachten worden uitgevoerd op IoT-apparaten, zoals een camera. Dit dashboard is vooraf geconfigureerd om de activiteit van de kritieke apparaatbewerkingen van het distributiecentrum weer te geven.

Het dashboard is logisch georganiseerd om de mogelijkheden voor apparaatbeheer van de Azure IoT-gateway en het IoT-apparaat weer te geven.  
   * U de opdracht gateway uitvoeren & besturingselementtaken
   * Beheer alle camera's die deel uitmaken van de oplossing. 

> [!div class="mx-imgBorder"]
> ![Digitaal distributiecentrum](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Apparaatsjabloon

Klik op het tabblad Apparaatsjablonen en u ziet het model voor gatewaymogelijkheden. Een capaciteitsmodel is gestructureerd rond twee verschillende interfaces **Camera** en **Digital Distribution Gateway**

> [!div class="mx-imgBorder"]
> ![Digitaal distributiecentrum](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Camera** - Deze interface organiseert alle cameraspecifieke opdrachtmogelijkheden 

> [!div class="mx-imgBorder"]
> ![Digitaal distributiecentrum](./media/tutorial-iot-central-ddc/ddc-camera.png)

**Digital Distribution Gateway** - Deze interface vertegenwoordigt alle telemetrie afkomstig van camera, cloud gedefinieerde apparaat twin eigenschappen en gateway info.

> [!div class="mx-imgBorder"]
> ![Digitaal distributiecentrum](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>Gatewayopdrachten
Deze interface organiseert alle mogelijkheden voor gatewayopdrachten

> [!div class="mx-imgBorder"]
> ![Digitaal distributiecentrum](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Regels
Selecteer het tabblad Regels om twee verschillende regels te zien die in deze toepassingssjabloon bestaan. Deze regels zijn geconfigureerd om meldingen naar de operators te e-mailen voor verder onderzoek.

 **Te veel ongeldige pakketten waarschuwing** - Deze regel wordt geactiveerd wanneer de camera detecteert een groot aantal ongeldige pakketten stroomt door het transportsysteem.
 
**Groot pakket** - Deze regel zal leiden tot als de camera detecteert enorme pakket dat niet kan worden geïnspecteerd op de kwaliteit. 

> [!div class="mx-imgBorder"]
> ![Digitaal distributiecentrum](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Taken
Selecteer het tabblad Taken om vijf verschillende taken te zien die bestaan als onderdeel van deze toepassingssjabloon: u de functie Taken gebruiken om bewerkingen voor de hele oplossing uit te voeren. Hier gebruiken digitale distributiecentrumtaken de apparaatopdrachten & dubbele mogelijkheid om taken uit te voeren, zoals:
   * calibrating camera voor het initiëren van de pakketdetectie 
   * camerafirmware periodiek bijwerken
   * het telemetrie-interval wijzigen om het uploaden van gegevens te beheren

> [!div class="mx-imgBorder"]
> ![Digitaal distributiecentrum](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Resources opschonen
Als u deze toepassing niet meer wilt gebruiken, verwijdert u de toepassingssjabloon door de instellingen **van beheertoepassingen** > **Application settings** te bezoeken en klikt u op **Verwijderen**.

> [!div class="mx-imgBorder"]
> ![Digitaal distributiecentrum](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het concept van digitale distributiecentrumoplossingen [voor digitale distributiecentrum](./architecture-digital-distribution-center-pnp.md)
* Meer informatie over andere [IoT Central-retailsjablonen](./overview-iot-central-retail-pnp.md)
* Meer informatie over IoT Central verwijzen naar [IoT Central-overzicht](../core/overview-iot-central.md)
