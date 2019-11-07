---
title: Zelf studie van IoT Connected logistiek | Microsoft Docs
description: Een zelf studie van een verbonden logistiek-toepassings sjabloon voor IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 9d55eca3df8897650f78a6bd85b649349c806e95
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615256"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Zelf studie: een verbonden sjabloon voor een logistiek-toepassing implementeren en door lopen

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In deze zelf studie leert u hoe u aan de slag gaat door een IoT Central verbonden sjabloon van een **logistiek** -toepassing te implementeren. U leert hoe u de sjabloon implementeert, wat is opgenomen in het vak en wat u mogelijk op de volgende manier wilt doen.

In deze zelf studie leert u hoe u 
* een verbonden logistiek-toepassing maken 
* Door loop de toepassing 

## <a name="prerequisites"></a>Vereisten
* Er zijn geen specifieke vereisten vereist voor het implementeren van deze app
* We raden u aan om Azure-abonnement te hebben, maar u kunt zelfs zonder dit te proberen

## <a name="create-connected-logistics-application-template"></a>Een verbonden sjabloon voor een logistiek-toepassing maken
U kunt een toepassing maken met behulp van de volgende stappen
1. Ga naar de website van Azure IoT Central Application Manager. Selecteer **samen stellen** in de navigatie balk aan de linkerkant en klik vervolgens op het tabblad **detail handel** .

> [!div class="mx-imgBorder"]
> ![verbonden logistiek-dash board](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

2. Selecteer **app maken** onder **verbonden logistiek-toepassing**

3. Bij het maken van de **app** wordt een nieuw toepassings formulier geopend en worden de aangevraagde gegevens ingevuld zoals hieronder wordt weer gegeven.
   * **Toepassings naam**: u kunt de voorgestelde standaard naam gebruiken of een beschrijvende toepassings naam invoeren.
   * **URL**: u kunt een aanbevolen standaard-URL gebruiken of uw BESCHRIJVENDE unieke URL voor onthouden opgeven. De standaard instelling wordt aanbevolen als u al een Azure-abonnement hebt. u kunt ook beginnen met een gratis proef versie van zeven dagen en ervoor kiezen om op elk gewenst moment betalen per gebruik te converteren voordat de gratis Trail verlopen.
   * **Facturerings gegevens**: de adres lijst, het Azure-abonnement en de regio gegevens zijn vereist om de resources in te richten.
   * **Maken** : Selecteer maken onder aan de pagina om uw toepassing te implementeren.

> [!div class="mx-imgBorder"]
> ![verbonden logistiek-dash board](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

## <a name="walk-through-the-application"></a>Door loop de toepassing 

## <a name="dashboard"></a>Dashboard

Nadat de app-sjabloon is geïmplementeerd, is uw standaard dashboard een verbonden logistiek-operator die is gericht op de portal. North Wind handelaar is een fictieve logistiek provider die de vloot van de lading beheert in de Oceaan en aan de grond. In dit dash board ziet u twee verschillende gateways die telemetrie over verzen dingen bieden, samen met de bijbehorende opdrachten, taken en acties die u kunt uitvoeren. Dit dash board is vooraf geconfigureerd om de activiteiten van de kritieke logistiek van het apparaat te presen teren.
Het dash board is logisch verdeeld over twee verschillende beheer bewerkingen voor gateway apparaten, 
   * Logistiek route voor verzen ding van vracht wagen en locatie Details van de oceaan-verzen ding is een essentieel element voor alle multi-modaal Trans Port
   * De gateway status & relevante gegevens weer geven 

> [!div class="mx-imgBorder"]
> ![verbonden logistiek-dash board](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png)

   * U kunt eenvoudig het totale aantal gateways, actieve en onbekende Tags bijhouden.
   * U kunt bewerkingen voor Apparaatbeheer uitvoeren, zoals firmware bijwerken, sensor uitschakelen, sensor inschakelen, drempel waarde bijwerken, update van de sensor instellen, telemetrie-intervallen bijwerken, & service contracten bijwerken.
   * Accu verbruik van het apparaat weer geven

> [!div class="mx-imgBorder"]
> ![verbonden logistiek-dash board](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png)

## <a name="device-template"></a>Apparaatprofiel

Klik op het tabblad Apparaatinstellingen en u ziet het gateway-functionaliteits model. Een mogelijkheidsprofiel is gestructureerd rond twee verschillende interfaces- **telemetrie gateway & eigenschap** en **Gateway opdrachten**

**& eigenschap gateway-telemetrie** : deze interface vertegenwoordigt alle telemetrie die betrekking hebben op Sens oren, locatie en apparaatgegevens en de dubbele eigenschaps mogelijkheid van het apparaat, zoals drempel waarden voor de sensor, & update-intervallen.

> [!div class="mx-imgBorder"]
> ![verbonden logistiek-dash board](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png)

**Gateway opdrachten** : deze interface organiseert de opdracht mogelijkheden van de gateway

> [!div class="mx-imgBorder"]
> ![verbonden logistiek-dash board](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png)

## <a name="rules"></a>Regels
Selecteer het tabblad regels om twee verschillende regels te zien die voor komen in deze toepassings sjabloon. Deze regels worden geconfigureerd voor het e-mailen van meldingen aan de Opera tors voor verdere onderzoek.
 
**Waarschuwing voor gateway diefstal**: deze regel wordt geactiveerd wanneer er tijdens de traject een onverwachte licht detectie door de Sens oren wordt uitgevoerd. Opera tors moeten spoed worden geïnformeerd om mogelijke dief stal te onderzoeken.
 
Niet- **reagerende gateway**: deze regel wordt geactiveerd als de gateway niet langer wordt gerapporteerd aan de Cloud. De gateway kan niet worden gereageerd als gevolg van een laag batterij niveau, het verlies van de connectiviteit, de status van het apparaat.

> [!div class="mx-imgBorder"]
> ![verbonden logistiek-dash board](./media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png)

## <a name="jobs"></a>Taken
Selecteer het tabblad taken om vijf verschillende taken weer te geven die als onderdeel van deze toepassings sjabloon bestaan:

> [!div class="mx-imgBorder"]
> ![verbonden logistiek-dash board](./media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png)

U kunt de functie voor taken gebruiken voor het uitvoeren van bewerkingen voor de hele oplossing. Hier worden de & opdrachten van het apparaat gebruikt om taken uit te voeren, zoals het uitschakelen van specifieke Sens oren in de gateway of het wijzigen van de drempel waarde voor de sensor, afhankelijk van de verzend modus en de route. 
   * Het is een standaard bewerking voor het uitschakelen van schokkende Sens oren tijdens de verzen ding om accu te sparen of de drempel waarde voor de Tempe ratuur te verlagen tijdens het vervoer van de koude keten 
 
   * Met taken kunt u bewerkingen uitvoeren op het hele systeem, zoals het bijwerken van firmware op de gateways of het bijwerken van het service contract om op de hoogte te blijven van onderhouds activiteiten.

## <a name="clean-up-resources"></a>Resources opschonen
Als u deze toepassing niet meer wilt gebruiken, verwijdert u de toepassings sjabloon door naar **beheer** > **Toepassings instellingen** te gaan en op **verwijderen**te klikken.

> [!div class="mx-imgBorder"]
> ![verbonden logistiek-dash board](./media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [concept van verbonden logistiek](./architecture-connected-logistics-pnp.md)
* Meer informatie over andere [IOT Central Retail-sjablonen](./overview-iot-central-retail-pnp.md)
* Meer informatie over [IOT Central-overzicht](../core/overview-iot-central-pnp.md)
