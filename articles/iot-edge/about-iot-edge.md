---
title: Wat is Azure IoT Edge | Microsoft Docs
description: Overzicht van de service Azure IoT Edge
author: kgremban
manager: philmea
ms.reviewer: chipalost
ms.service: iot-edge
services: iot-edge
ms.topic: overview
ms.date: 10/28/2019
ms.author: kgremban
ms.custom: mvc
ms.openlocfilehash: 1ba133acda414d9779e2fb10150bbdd57285e9a5
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511209"
---
# <a name="what-is-azure-iot-edge"></a>Wat is Azure IoT Edge?

Azure IoT Edge verplaatst cloud-analyse en aangepaste bedrijfslogica naar apparaten, zodat uw organisatie zich kan richten op zakelijke inzichten in plaats van gegevensbeheer. Breid uw IoT-oplossing uit door uw bedrijfs logica te verpakken in standaard containers en vervolgens deze containers te implementeren op elk van uw apparaten en deze allemaal te controleren vanuit de Cloud.

Analyses stimuleren bedrijfswaarde in IoT-oplossingen, maar niet alle analyses hoeven zich in de cloud te bevinden. Als u zo snel mogelijk op nood gevallen wilt reageren, kunt u afwijkende werk belastingen voor detectie uitvoeren aan de rand. Als u de kosten voor de band breedte wilt verlagen en wilt voor komen dat terabytes van onbewerkte gegevens worden overgebracht, kunt u de gegevens lokaal opschonen en samen voegen. vervolgens verzendt u alleen de inzichten naar de Cloud voor analyse.

Azure IoT Edge bestaat uit drie onderdelen:

* **IOT Edge modules** zijn containers waarop Azure-Services, services van derden of uw eigen code worden uitgevoerd. De modules zijn geïmplementeerd op IoT Edge-apparaten en worden lokaal uitgevoerd op deze apparaten.
* De **IOT Edge runtime** wordt op elk IOT edge apparaat uitgevoerd en beheert de modules die op elk apparaat zijn geïmplementeerd.
* Met een **Cloud interface** kunt u IOT edge apparaten op afstand bewaken en beheren.

>[!NOTE]
>Azure IoT Edge is alleen beschikbaar in de prijscategorieën gratis en standaard van IoT Hub. De gratis optie is alleen bedoeld voor testen en evalueren. Raadpleeg [How to choose the right IoT Hub tier](../iot-hub/iot-hub-scaling.md) (De juiste IoT Hub-prijscategorie kiezen) voor meer informatie over de Basic- en Standard-prijscategorieën.

## <a name="iot-edge-modules"></a>IoT Edge-modules

IoT Edge-modules zijn eenheden van uitvoerbewerking die als compatibele Docker-containers zijn geïmplementeerd, die uw bedrijfslogica aan de rand uitvoeren. Er kunnen meerdere modules worden geconfigureerd om met elkaar te communiceren, waardoor er een pijplijn van gegevensverwerking ontstaat. U kunt aangepaste modules ontwikkelen of bepaalde Azure-services ordenen in modules die offline en aan de rand inzichten geven.

### <a name="artificial-intelligence-at-the-edge"></a>Kunst matige intelligentie aan de rand

Met Azure IoT Edge kunt u verwerking van complexe gebeurtenissen, machine learning, afbeeldingsherkenning en andere vormen van kunstmatige intelligentie (AI) van hoge waarde implementeren zonder deze intern te schrijven. Azure-Services zoals Azure Functions, Azure Stream Analytics en Azure Machine Learning kunnen allemaal on-premises worden uitgevoerd via Azure IoT Edge. U bent echter niet beperkt tot Azure-Services. Iedereen kan AI-modules maken en deze beschikbaar stellen aan de community voor gebruik via Microsoft Azure Marketplace.

### <a name="bring-your-own-code"></a>Uw eigen code gebruiken

Als u uw eigen code op uw apparaten wilt implementeren, ondersteunt Azure IoT Edge deze ook. Azure IoT Edge bevat hetzelfde programmeermodel als de andere Azure IoT-services. U kunt dezelfde code uitvoeren op een apparaat of in de Cloud. Azure IoT Edge ondersteunt Linux en Windows, dus u kunt coderen voor het platform van uw keuze. Het programma biedt ondersteuning voor Java, .NET Core 2.0, Node.js, C en Python. Uw ontwikkelaars kunnen dus in een taal coderen die ze al kennen en bestaande bedrijfslogica gebruiken.

## <a name="iot-edge-runtime"></a>IoT Edge-runtime

De Azure IoT Edge-runtime maakt aangepaste en cloudlogica op IoT Edge-apparaten mogelijk. De runtime bevindt zich op het IoT Edge apparaat en voert beheer-en communicatie bewerkingen uit. De runtime voert verschillende functies uit:

* Installeert en werkt werk belastingen op het apparaat bij.
* Onderhoudt de Azure IoT Edge-beveiligingsstandaarden op het apparaat.
* Zorgt ervoor dat de IoT Edge-modules altijd worden uitgevoerd.
* Rapporteert de status van de module aan de cloud voor externe bewaking.
* Beheert de communicatie tussen de downstream-Leaf-apparaten en een IoT Edge apparaat, tussen modules op een IoT Edge apparaat en tussen een IoT Edge apparaat en de Cloud.

![IoT Edge-runtime verzendt inzichten en rapportages naar IoT Hub](./media/about-iot-edge/runtime.png)

Hoe u een Azure IoT Edge-apparaat gebruikt, is aan u. De runtime wordt vaak gebruikt om AI te implementeren op gateway apparaten die gegevens verzamelen en verwerken van andere on-premises apparaten, maar dit implementatie model is slechts één optie.

De Azure IoT Edge runtime wordt uitgevoerd op een groot aantal IoT-apparaten waarmee het gebruik op een groot aantal manieren kan worden gebruikt. Deze biedt ondersteuning voor Linux- en Windows-besturingssystemen en abstraheert hardwaregegevens. Gebruik een apparaat dat kleiner is dan een Raspberry Pi 3 als u niet veel gegevens verwerkt of een industriële server gebruikt voor het uitvoeren van resource-intensieve workloads.

## <a name="iot-edge-cloud-interface"></a>Cloudinterface van IoT Edge

Het is moeilijk om de levens cyclus van de software te beheren voor miljoenen IoT-apparaten die vaak verschillend zijn, en modellen of geografische sprei ding. Workloads worden gemaakt en geconfigureerd voor een specifiek type apparaat, geïmplementeerd op al uw apparaten en bewaakt om apparaten te detecteren die zich niet normaal gedragen. Deze activiteiten kunnen niet per apparaat worden uitgevoerd, maar moeten op schaal worden uitgevoerd.

Azure IoT Edge integreert naadloos met Azure IoT-oplossingsversnellers en biedt één controlelaag voor de behoeften van uw oplossing. Met cloudservices kunt u het volgende doen:

* een workload maken en configureren die op een specifiek type apparaat wordt uitgevoerd;
* een workload verzenden naar een reeks apparaten;
* workloads bewaken die worden uitgevoerd op apparaten in het veld.

![Apparaattelemetrie en acties worden gecoördineerd in de cloud](./media/about-iot-edge/cloud-interface.png)

## <a name="next-steps"></a>Volgende stappen

Probeer deze concepten uit door [IoT Edge te implementeren op een gesimuleerd apparaat](quickstart.md).
