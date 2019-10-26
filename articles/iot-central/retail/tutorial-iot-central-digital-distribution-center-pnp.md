---
title: Zelf studie van IoT Digital Distribution Center | Microsoft Docs
description: Een zelf studie van de toepassings sjabloon Digital Distribution Center voor IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 20ed04efc1d10e419148cb4f6c75c3eab4ab40a6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957914"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Zelf studie: een Digital Distribution Center-toepassings sjabloon implementeren en door lopen

In deze zelf studie wordt uitgelegd hoe u aan de slag gaat door een IoT Central **Digital Distribution Center** -toepassings sjabloon te implementeren. U leert hoe u de sjabloon implementeert, wat is opgenomen in het vak en wat u mogelijk op de volgende manier wilt doen.

## <a name="details"></a>Details

In deze zelf studie leert u hoe u 
* Digital Distribution Center-toepassing maken 
* Door loop de toepassing 

## <a name="prerequisites"></a>Vereisten
* Er zijn geen specifieke vereisten vereist voor het implementeren van deze app
* We raden u aan om Azure-abonnement te hebben, maar u kunt zelfs zonder dit te proberen

## <a name="create-digital-distribution-center-application-template"></a>Een Digital Distribution Center-toepassings sjabloon maken

U kunt een toepassing maken met behulp van de volgende stappen

1. Ga naar de website van Azure IoT Central Application Manager. Selecteer **samen stellen** in de navigatie balk aan de linkerkant en klik vervolgens op het tabblad **detail handel** .

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/iotc-retail-homepage.png)

2. Selecteer het tabblad **detail handel** en selecteer **app maken** onder * * Digital Distribution Center-toepassing * *

3. Bij het maken van de **app** wordt een nieuw toepassings formulier geopend en worden de aangevraagde gegevens ingevuld zoals hieronder wordt weer gegeven.
   **Toepassings naam**: u kunt de voorgestelde standaard naam gebruiken of een beschrijvende toepassings naam invoeren.
   **URL**: u kunt de voorgestelde standaard-URL gebruiken of uw beschrijvende, unieke, onthouden-URL invoeren. Vervolgens wordt de standaard instelling aanbevolen als u al een Azure-abonnement hebt. U kunt ook beginnen met een gratis proef versie van zeven dagen en ervoor kiezen om op elk gewenst moment betalen per gebruik te converteren voordat de gratis Trail-periode verloopt.
   **Facturerings gegevens**: de adres lijst, het Azure-abonnement en de regio gegevens zijn vereist om de resources in te richten.
   **Maken**: Selecteer maken onder aan de pagina om uw toepassing te implementeren.

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-create.png)

## <a name="walk-through-the-application"></a>Door loop de toepassing 

## <a name="dashboard"></a>Dashboard 

Nadat u de app-sjabloon hebt geïmplementeerd, is het standaard dashboard een portal voor de distributie centrum-operator met focus. North Wind handelaar is een fictieve oplossings provider voor distributie centra die transport systemen beheert. 

In dit dash board ziet u één Edge-gateway en één camera die fungeert als een IoT-apparaat. De Gateway levert telemetrie over pakketten zoals geldig, ongeldig, niet geïdentificeerd en grootte samen met gekoppelde apparaatspecifieke eigenschappen van het apparaat. Alle downstream-opdrachten worden uitgevoerd op IoT-apparaten, zoals een camera. Dit dash board is vooraf geconfigureerd om de activiteiten van het kritieke distributie centrum te demonstreren.

Het dash board is logisch ingedeeld om de mogelijkheden voor Apparaatbeheer van de Azure IoT Edge gateway en IoT-apparaat weer te geven.  
   * U kunt de rand gateway opdracht & besturings taken uitvoeren
   * Alle camera's beheren die deel uitmaken van de oplossing. 

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Apparaatprofiel

Klik op het tabblad **device templates** en u ziet de afzonderlijke apparaatprofielen voor Azure IOT Edge & camera. 

Azure IoT Edge gateway-apparaat sjabloon vertegenwoordigt een blauw druk die de kenmerken en het gedrag van een Edge gateway-apparaat definieert. Enkele van de onderdelen van een edge-apparaat zijn,
   * Distributiemanifest
   * Relatie met downstream-apparaten
   * Software modules
   * Eigenschappen van modules & opdrachten 

Het implementatie manifest bevat de lijst met software modules die worden uitgevoerd op het Azure IoT Edge apparaat & module apparaatdubbels geconfigureerd met de gewenste eigenschappen. Wanneer u het implementatie manifest gebruikt dat deel uitmaakt van de sjabloon voor het apparaat, weet Azure IoT Edge uitvoerings tijd welke modules moeten worden geïnstalleerd en hoe deze moeten worden geconfigureerd om samen te werken.

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

In dit apparaatprofiel ziet u de mogelijkheden van de module apparaten die worden gegenereerd vanuit het implementatie manifest. 

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-devicetemplate2.png)

Hier kunt u downstream-objectrelaties toevoegen, eigenschap Cloud, weer gaven maken en de mogelijkheden van modules en apparaten aanpassen.

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-moduletemplate.png)

Klik op het tabblad **apparaat** en kies het **ddcgateway** -apparaat dat is gekoppeld aan Digital Distribution Center Azure IOT Edge. Hier ziet u de aangepaste rand modules & downstream-camera die deel uitmaakt van de apparaat-relatie. IoT Edge modules zijn de kleinste reken eenheid en kunnen Azure-Services (zoals Azure Stream Analytics, AI-modules) of uw Solution-specifieke code bevatten.

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-modules.png)

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-downstream.png)

## <a name="gateway-commands"></a>Gateway opdrachten
Deze interface organiseert de gateway opdracht mogelijkheden

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Regels
Selecteer het tabblad regels om twee verschillende regels te zien die voor komen in deze toepassings sjabloon. Deze regels worden geconfigureerd voor het e-mailen van meldingen aan de Opera tors voor verdere onderzoek.
 **Waarschuwing voor te veel ongeldige pakketten** : deze regel wordt geactiveerd wanneer de camera een groot aantal ongeldige pakketten detecteert die via het transport systeem lopen.
 
**Groot pakket** : deze regel wordt geactiveerd als de camera een enorm pakket detecteert dat niet voor de kwaliteit kan worden geïnspecteerd. 

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Taken
Selecteer het tabblad taken om vijf verschillende taken weer te geven die als onderdeel van deze toepassings sjabloon bestaan: u kunt gebruikmaken van de functie voor het uitvoeren van bewerkingen voor de hele oplossing. Hier worden de & opdrachten voor het digitale distributie centrum gebruikt voor het uitvoeren van taken zoals,
   * de camera kalibreren voordat de detectie van het pakket wordt gestart 
   * camera firmware regel matig bijwerken
   * het telemetrie-interval wijzigen om het uploaden van gegevens te beheren

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Resources opschonen
Als u deze toepassing niet meer wilt gebruiken, verwijdert u de toepassings sjabloon door naar **beheer** > **Toepassings instellingen** te gaan en op **verwijderen**te klikken.

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het Digital Distribution Center- [concept](./architecture-digital-distribution-center-pnp.md) van Digital Distribution Center (oplossings architectuur)
* Meer informatie over andere [IOT Central Retail-sjablonen](./overview-iot-central-retail-pnp.md)
* Raadpleeg [IOT Central Overview](../core/overview-iot-central-pnp.md) voor meer informatie over IOT Central
