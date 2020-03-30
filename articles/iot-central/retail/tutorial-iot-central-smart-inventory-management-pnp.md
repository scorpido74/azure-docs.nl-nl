---
title: Zelfstudie van IoT Smart-voorraadbeheer | Microsoft Documenten
description: Een zelfstudie van de sjabloon voor slimme voorraadbeheertoepassingen voor IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 5632d98d9d853d9a4b0882c28cad1836bb6f3cef
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77025466"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Zelfstudie: Een sjabloon voor een slimme voorraadbeheertoepassing implementeren en doorlopen



In deze zelfstudie ziet u hoe u aan de slag gaan door een **iot Central-sjabloon voor slimme voorraadbeheer** te implementeren. U leert hoe u de sjabloon implementeert, wat er uit het vak wordt opgenomen en wat u vervolgens wilt doen.

In deze zelfstudie leer je hoe je, 
* slimme voorraadbeheertoepassing maken 
* loop door de applicatie 

## <a name="prerequisites"></a>Vereisten

* Geen specifieke vereisten nodig om deze app te implementeren
* Aanbevolen om een Azure-abonnement te hebben, maar u het zelfs zonder

## <a name="create-smart-inventory-management-application-template"></a>Sjabloon voor slimme voorraadbeheertoepassingen maken

U toepassing maken met de volgende stappen

1. Navigeer naar de website azure IoT Central-toepassingsbeheer. Selecteer **Bouwen** op de navigatiebalk aan de linkerkant en klik op het tabblad **Detailhandel.**

    > [!div class="mx-imgBorder"]
    > ![Dashboard voor slim voorraadbeheer](./media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png)

2. Het tabblad **Detailhandel** selecteren en **app maken** selecteren onder **slim voorraadbeheer**

3. **Maak app** opent Nieuw aanvraagformulier en vul de gevraagde details zoals hieronder weergegeven.
   **Toepassingsnaam:** u de standaard voorgestelde naam gebruiken of uw vriendelijke toepassingsnaam invoeren.
   **URL**: u de voorgestelde standaard-URL gebruiken of uw vriendelijke unieke gedenkwaardige URL invoeren. Vervolgens wordt de standaardinstelling aanbevolen als u al een Azure-abonnement hebt. U beginnen met een gratis proefprijsplan van 7 dagen en ervoor kiezen om op elk gewenst moment om te zetten in een standaardprijsplan voordat de gratis trail verloopt.
   **Factureringsgegevens:** de gegevens directory, Azure-abonnementen en regio's zijn vereist om de resources in te richten.
   **Maken:** Selecteer maken onder aan de pagina om uw toepassing te implementeren.

    > [!div class="mx-imgBorder"]
    > ![Dashboard voor slim voorraadbeheer](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png)

    > [!div class="mx-imgBorder"]
    > ![Factureringsgegevens voor slim voorraadbeheer](./media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>Loop door de applicatie 

### <a name="dashboard"></a>Dashboard 

Nadat u de app-sjabloon hebt geïmplementeerd, is uw standaarddashboard een portal die op een slimme voorraadbeheerbeheerder is gericht. Northwind Trader is een fictieve slimme voorraadleverancier die magazijn beheert met Bluetooth low energy (BLE) en een winkel met Radio-frequency identification (RFID). In dit dashboard ziet u twee verschillende gateways die telemetrie bieden over voorraad, samen met bijbehorende opdrachten, taken en acties die u uitvoeren. Dit dashboard is vooraf geconfigureerd om de kritieke activiteit voor de bewerkingen van slimme apparatuur voor voorraadbeheer te laten zien.
Het dashboard is logisch verdeeld tussen twee verschillende gateway-apparaatbeheerbewerkingen, 
   * Het magazijn wordt geïmplementeerd met een vaste BLE-gateway & BLE-tags op pallets om & voorraad te traceren in een grotere faciliteit
   * De winkel wordt uitgevoerd met een vaste RFID-gateway & RFID-tags op individueel artikelniveau om de voorraad in een winkelstopcontact te volgen en te traceren
   * De gatewaylocatie, status & gerelateerde details weergeven 

> [!div class="mx-imgBorder"]
> ![Dashboard voor slim voorraadbeheer](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * U eenvoudig het totale aantal gateways, actieve en onbekende tags bijhouden.
   * U apparaatbeheerbewerkingen uitvoeren, zoals firmware bijwerken, de sensor uitschakelen, sensor, sensordrempel bijwerken, telemetrie-intervallen bijwerken & servicecontracten voor apparaten bijwerken
   * Gateway-apparaten kunnen on-demand voorraadbeheer uitvoeren met een volledige of incrementele scan.

> [!div class="mx-imgBorder"]
> ![Dashboard voor slim voorraadbeheer](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Apparaatsjabloon
Klik op het tabblad Apparaatsjablonen en u ziet het model voor gatewaymogelijkheden. Een capaciteitsmodel is gestructureerd rond twee verschillende interfaces **Gateway Telemetrie & Eigenschap-** en **Gatewayopdrachten**

**Gateway Telemetrie & eigenschap** - Deze interface vertegenwoordigt alle telemetrie met betrekking tot sensoren, locatie, apparaatinformatie en apparaattwee eigenschapsmogelijkheden, zoals gatewaydrempels en update-intervallen.

> [!div class="mx-imgBorder"]
> ![Dashboard voor slim voorraadbeheer](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Gateway opdrachten** - Deze interface organiseert alle mogelijkheden voor gatewayopdrachten

> [!div class="mx-imgBorder"]
> ![Dashboard voor slim voorraadbeheer](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Regels
Selecteer het tabblad Regels om twee verschillende regels te zien die in deze toepassingssjabloon bestaan. Deze regels zijn geconfigureerd om meldingen naar de operators te e-mailen voor verder onderzoek.

**Gateway offline:** deze regel wordt geactiveerd als de gateway gedurende een langere periode niet rapporteert aan de cloud. Gateway kan niet reageren vanwege de lage batterijmodus, het verlies van connectiviteit, de status van het apparaat.

**Onbekende tags**: Het is essentieel om elke RFID-& BLE-tags in verband met een asset te volgen. Als de gateway te veel onbekende tags detecteert, is dit een indicatie van synchronisatieproblemen met tagsourcingtoepassingen.

> [!div class="mx-imgBorder"]
> ![Dashboard voor slim voorraadbeheer](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>Taken
Selecteer het tabblad Taken om vijf verschillende taken te zien die bestaan als onderdeel van deze toepassingssjabloon: u de functie Taken gebruiken om bewerkingen voor de hele oplossing uit te voeren. Hier gebruiken voorraadbeheertaken de apparaatopdrachten en de dubbele mogelijkheid om taken uit te voeren, zoals:
   * lezers uit schakelen in alle gateway's
   * de telemetriedrempel tussen 
   * on-demand voorraadscannen uit te voeren in de hele oplossing.

> [!div class="mx-imgBorder"]
> ![Dashboard voor slim voorraadbeheer](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet meer wilt gebruiken, verwijdert u de toepassingssjabloon door de instellingen **van beheertoepassingen** > **Application settings** te bezoeken en klikt u op **Verwijderen**.

> [!div class="mx-imgBorder"]
> ![Dashboard voor slim voorraadbeheer](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over slim [voorraadbeheer smart inventory management concept](./architecture-smart-inventory-management-pnp.md)
* Meer informatie over andere [IoT Central-retailsjablonen](./overview-iot-central-retail-pnp.md)
* Meer informatie over IoT Central verwijzen naar [IoT Central-overzicht](../core/overview-iot-central.md)
