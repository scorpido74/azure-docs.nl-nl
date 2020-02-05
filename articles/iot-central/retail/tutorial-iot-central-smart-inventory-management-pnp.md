---
title: Zelf studie van IoT Smart Inventory Management | Microsoft Docs
description: Een zelf studie van de sjabloon slimme inventaris beheer toepassing voor IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 5632d98d9d853d9a4b0882c28cad1836bb6f3cef
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025466"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Zelf studie: een sjabloon voor slimme inventaris beheer toepassing implementeren en door lopen



In deze zelf studie leert u hoe u aan de slag gaat door een IoT Central **Smart Inventory Management** -toepassings sjabloon te implementeren. U leert hoe u de sjabloon implementeert, wat is opgenomen in het vak en wat u mogelijk op de volgende manier wilt doen.

In deze zelf studie leert u hoe u 
* Smart Inventory Management-toepassing maken 
* door loop de toepassing 

## <a name="prerequisites"></a>Vereisten

* Er zijn geen specifieke vereisten vereist voor het implementeren van deze app
* We raden u aan om Azure-abonnement te hebben, maar u kunt zelfs zonder dit te proberen

## <a name="create-smart-inventory-management-application-template"></a>Sjabloon voor slimme inventaris beheer toepassing maken

U kunt een toepassing maken met behulp van de volgende stappen

1. Ga naar de website van Azure IoT Central Application Manager. Selecteer **samen stellen** in de navigatie balk aan de linkerkant en klik vervolgens op het tabblad **detail handel** .

    > [!div class="mx-imgBorder"]
    > ![dash board voor Smart Inventory Management](./media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png)

2. Selecteer het tabblad **Retail** en selecteer **app maken** onder **Smart Inventory Management**

3. Bij het maken van de **app** wordt een nieuw toepassings formulier geopend en worden de aangevraagde gegevens ingevuld zoals hieronder wordt weer gegeven.
   **Toepassings naam**: u kunt de voorgestelde standaard naam gebruiken of een beschrijvende toepassings naam invoeren.
   **URL**: u kunt een aanbevolen standaard-URL gebruiken of uw BESCHRIJVENDE unieke URL voor onthouden opgeven. Vervolgens wordt de standaard instelling aanbevolen als u al een Azure-abonnement hebt. U kunt beginnen met een gratis proef abonnement van 7 dagen en ervoor kiezen om op elk gewenst moment te converteren naar een Standard-prijs plan voordat het gratis Trail verloopt.
   **Facturerings gegevens**: de adres lijst, het Azure-abonnement en de regio gegevens zijn vereist om de resources in te richten.
   **Maken**: Selecteer maken onder aan de pagina om uw toepassing te implementeren.

    > [!div class="mx-imgBorder"]
    > ![dash board voor Smart Inventory Management](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png)

    > [!div class="mx-imgBorder"]
    > facturerings gegevens voor Smart Inventory Management ![](./media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>door loop de toepassing 

### <a name="dashboard"></a>Dashboard 

Nadat de app-sjabloon is geïmplementeerd, is uw standaard dashboard een webbeheer operator die gericht is op de portal. North Wind handelaar is een fictieve Smart Inventory-provider die Warehouse beheert met behulp van een laag energie verbruik van Bluetooth en een Retail Store met RFID (Radio-Frequency Identification). In dit dash board ziet u twee verschillende gateways die telemetrie over de inventaris bieden, samen met de bijbehorende opdrachten, taken en acties die u kunt uitvoeren. Dit dash board is vooraf geconfigureerd om de activiteiten activiteit van het kritieke Smart Inventory Management-apparaat te presen teren.
Het dash board is logisch verdeeld over twee verschillende beheer bewerkingen voor gateway apparaten, 
   * Het magazijn wordt geïmplementeerd met een vaste baan gateway & de baan codes op pallets om de inventaris van & tracering bij een grotere faciliteit te volgen
   * Retail Store wordt geïmplementeerd met een vaste RFID-gateway & RFID-tags op het niveau van een item om het aandeel in een Store-uitgang te volgen en te traceren
   * De gateway locatie, status & gerelateerde gegevens weer geven 

> [!div class="mx-imgBorder"]
> ![dash board voor Smart Inventory Management](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * U kunt eenvoudig het totale aantal gateways, actieve en onbekende Tags bijhouden.
   * U kunt bewerkingen voor Apparaatbeheer uitvoeren, zoals firmware bijwerken, sensor uitschakelen, sensor inschakelen, drempel waarde voor bijwerken van sensors instellen, telemetrie-intervallen bijwerken & contract service contracten bijwerken
   * Gateway apparaten kunnen voorraad beheer op aanvraag uitvoeren met een volledige of incrementele scan.

> [!div class="mx-imgBorder"]
> ![dash board voor Smart Inventory Management](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Apparaatprofiel
Klik op het tabblad Apparaatinstellingen en u ziet het gateway-functionaliteits model. Een mogelijkheidsprofiel is gestructureerd rond twee verschillende interfaces- **telemetrie gateway & eigenschap** en **Gateway opdrachten**

**& eigenschap gateway-telemetrie** : deze interface vertegenwoordigt alle telemetrie die betrekking hebben op Sens oren, locatie, apparaatgegevens en de dubbele eigenschap van het apparaat, zoals gateway drempels en update-intervallen.

> [!div class="mx-imgBorder"]
> ![dash board voor Smart Inventory Management](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Gateway opdrachten** : deze interface organiseert de opdracht mogelijkheden van de gateway

> [!div class="mx-imgBorder"]
> ![dash board voor Smart Inventory Management](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Regels
Selecteer het tabblad regels om twee verschillende regels te zien die voor komen in deze toepassings sjabloon. Deze regels worden geconfigureerd voor het e-mailen van meldingen aan de Opera tors voor verdere onderzoek.

**Gateway offline**: deze regel wordt geactiveerd als de gateway gedurende een lange periode niet wordt gerapporteerd aan de Cloud. De gateway kan niet worden gereageerd vanwege een laag batterij niveau, de connectiviteit is verbroken, de status van het apparaat.

**Onbekende Tags**: het is essentieel om elke RFID-&e-tag voor een Asset bij te houden. Als de gateway te veel onbekende tags detecteert, is dit een indicatie van de synchronisatie uitdagingen met behulp van tag sourcing-toepassingen.

> [!div class="mx-imgBorder"]
> ![dash board voor Smart Inventory Management](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>Taken
Selecteer het tabblad taken om vijf verschillende taken weer te geven die als onderdeel van deze toepassings sjabloon bestaan: u kunt de functie taken gebruiken voor het uitvoeren van bewerkingen voor de hele oplossing. In deze taken voor voorraad beheer worden de opdrachten voor het apparaat gebruikt en dubbele mogelijkheden om taken uit te voeren, zoals,
   * lezers uitschakelen op alle gateways
   * de limiet voor telemetrie wijzigen tussen 
   * scan op aanvraag inventarisatie op basis van de volledige oplossing.

> [!div class="mx-imgBorder"]
> ![dash board voor Smart Inventory Management](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet meer wilt gebruiken, verwijdert u de toepassings sjabloon door naar **beheer** > **Toepassings instellingen** te gaan en op **verwijderen**te klikken.

> [!div class="mx-imgBorder"]
> ![dash board voor Smart Inventory Management](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over Smart Inventory Management [Smart Inventory Management concept](./architecture-smart-inventory-management-pnp.md)
* Meer informatie over andere [IOT Central Retail-sjablonen](./overview-iot-central-retail-pnp.md)
* Raadpleeg [IOT Central Overview](../core/overview-iot-central.md) voor meer informatie over IOT Central
