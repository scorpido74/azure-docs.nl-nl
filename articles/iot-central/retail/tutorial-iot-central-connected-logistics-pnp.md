---
title: Zelfstudie van IoT Connected logistics | Microsoft Documenten
description: Een zelfstudie van connected logistics application template voor IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: d7050cfdae2c726a3a5bd44b4399792a400572a3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77025517"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Zelfstudie: Een sjabloon voor verbonden logistieke toepassingen implementeren en doorlopen



In deze zelfstudie ziet u hoe u aan de slag gaan door een door IoT Central **verbonden logistieke** toepassingssjabloon te implementeren. U leert hoe u de sjabloon implementeert, wat er uit het vak wordt opgenomen en wat u vervolgens wilt doen.

In deze tutorial leer je hoe je,

* connected logistics applicatie maken
* loop door de applicatie 

## <a name="prerequisites"></a>Vereisten

* Geen specifieke vereisten nodig om deze app te implementeren
* Aanbevolen om een Azure-abonnement te hebben, maar u het zelfs zonder

## <a name="create-connected-logistics-application-template"></a>Sjabloon voor verbonden logistieke toepassingen maken

U toepassing maken met de volgende stappen

1. Navigeer naar de website azure IoT Central-toepassingsbeheer. Selecteer **Bouwen** op de navigatiebalk aan de linkerkant en klik op het tabblad **Detailhandel.**

    > [!div class="mx-imgBorder"]
    > ![Verbonden logistiek dashboard](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

2. Selecteer **App maken** onder Connected **Logistics-toepassing**

3. **Maak app** opent Nieuw aanvraagformulier en vul de gevraagde details zoals hieronder weergegeven.
   * **Toepassingsnaam:** u de standaard voorgestelde naam gebruiken of uw vriendelijke toepassingsnaam invoeren.
   * **URL**: u de voorgestelde standaard-URL gebruiken of uw vriendelijke unieke gedenkwaardige URL invoeren. Vervolgens wordt de standaardinstelling aanbevolen als u al een Azure-abonnement hebt. U beginnen met een gratis proefprijsplan van 7 dagen en ervoor kiezen om op elk gewenst moment om te zetten in een standaardprijsplan voordat de gratis trail verloopt.
   * **Factureringsgegevens:** de gegevens directory, Azure-abonnementen en regio's zijn vereist om de resources in te richten.
   * **Maken:** Selecteer maken onder aan de pagina om uw toepassing te implementeren.

    > [!div class="mx-imgBorder"]
    > ![Verbonden logistiek dashboard](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

    > [!div class="mx-imgBorder"]
    > ![Factureringsgegevens verbonden logistiek](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>Loop door de applicatie 

## <a name="dashboard"></a>Dashboard

Nadat u de app-sjabloon met succes hebt geïmplementeerd, is uw standaarddashboard een portal die is gericht op verbonden logistiek operatoren. Northwind Trader is een fictieve logistiek dienstverlener die de vrachtvloot in de oceaan en op het land beheert. In dit dashboard ziet u twee verschillende gateways die telemetrie bieden over zendingen, samen met bijbehorende opdrachten, taken en acties die u uitvoeren. Dit dashboard is vooraf geconfigureerd om de kritieke activiteiten op het gebied van logistieke apparatuur te laten zien.
Het dashboard is logisch verdeeld tussen twee verschillende gateway-apparaatbeheerbewerkingen, 
   * Logistieke route voor vrachtwagenverzending en locatiegegevens van de zeezending is een essentieel element voor al het multimodale transport
   * De gatewaystatus & relevante informatie weergeven 

> [!div class="mx-imgBorder"]
> ![Verbonden logistiek dashboard](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png)

   * U eenvoudig het totale aantal gateways, actieve en onbekende tags bijhouden.
   * U apparaatbeheerbewerkingen uitvoeren, zoals firmware bijwerken, de sensor uitschakelen, sensor, sensordrempel bijwerken, telemetrie-intervallen bijwerken, & servicecontracten voor apparaten bijwerken.
   * Batterijverbruik van apparaten weergeven

> [!div class="mx-imgBorder"]
> ![Verbonden logistiek dashboard](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png)

## <a name="device-template"></a>Apparaatsjabloon

Klik op het tabblad Apparaatsjablonen en u ziet het model voor gatewaymogelijkheden. Een capaciteitsmodel is gestructureerd rond twee verschillende interfaces **Gateway Telemetrie & Eigenschap-** en **Gatewayopdrachten**

**Gateway Telemetry & Eigenschap** - Deze interface vertegenwoordigt alle telemetrie met betrekking tot sensoren, locatie- en apparaatgegevens, evenals de capaciteit van apparaattweelingeigendom, zoals sensordrempels & updateintervallen.

> [!div class="mx-imgBorder"]
> ![Verbonden logistiek dashboard](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png)

**Gateway opdrachten** - Deze interface organiseert alle mogelijkheden voor gatewayopdrachten

> [!div class="mx-imgBorder"]
> ![Verbonden logistiek dashboard](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png)

## <a name="rules"></a>Regels
Selecteer het tabblad Regels om twee verschillende regels te zien die in deze toepassingssjabloon bestaan. Deze regels zijn geconfigureerd om meldingen naar de operators te e-mailen voor verder onderzoek.
 
**Waarschuwing voor diefstal van**gateways : deze regel wordt geactiveerd wanneer er tijdens de reis onverwachte lichtdetectie door de sensoren is. Operators moeten zo snel mogelijk op de hoogte worden gesteld om mogelijke diefstal te onderzoeken.
 
**Niet-reagerende gateway:** deze regel wordt geactiveerd als de gateway gedurende een langere periode niet rapporteert aan de cloud. Gateway kan niet reageren vanwege de lage batterijmodus, het verlies van connectiviteit, de status van het apparaat.

> [!div class="mx-imgBorder"]
> ![Verbonden logistiek dashboard](./media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png)

## <a name="jobs"></a>Taken
Selecteer het tabblad Taken om vijf verschillende taken te zien die in deze toepassingssjabloon bestaan:

> [!div class="mx-imgBorder"]
> ![Verbonden logistiek dashboard](./media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png)

U de functie Taken gebruiken om voor de hele oplossing te werken. Hier worden de apparaatopdrachten en de dubbele mogelijkheid gebruikt om taken uit te voeren, zoals het uitschakelen van specifieke sensoren in alle gatewayof het wijzigen van de sensordrempel, afhankelijk van de verzendmodus en -route. 
   * Het is een standaardoperatie om schoksensoren uit te schakelen tijdens de verzending van de oceaan om de batterij te sparen of de temperatuurdrempel te verlagen tijdens het transport van de koude keten. 
 
   * Met taken u systeembrede bewerkingen uitvoeren, zoals het bijwerken van firmware op de gateways of het bijwerken van servicecontracten om op de hoogte te blijven van onderhoudsactiviteiten.

## <a name="clean-up-resources"></a>Resources opschonen
Als u deze toepassing niet meer wilt gebruiken, verwijdert u de toepassingssjabloon door de instellingen **van beheertoepassingen** > **Application settings** te bezoeken en klikt u op **Verwijderen**.

> [!div class="mx-imgBorder"]
> ![Verbonden logistiek dashboard](./media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [connected logistics concept](./architecture-connected-logistics-pnp.md)
* Meer informatie over andere [IoT Central-retailsjablonen](./overview-iot-central-retail-pnp.md)
* Meer informatie over [het IoT Central-overzicht](../core/overview-iot-central.md)
