---
title: Modules implementeren vanuit Azure Portal-Azure IoT Edge | Microsoft Docs
description: De Azure Portal gebruiken om modules te implementeren op een IoT Edge apparaat
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4c2adc8ef0d426617dc85dd507907d612bbdabaa
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964924"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Azure IoT Edge modules implementeren vanuit de Azure Portal

Wanneer u IoT Edge modules met uw bedrijfs logica hebt gemaakt, wilt u deze implementeren op uw apparaten zodat ze aan de rand kunnen worden uitgevoerd. Als u meerdere modules hebt die samen werken om gegevens te verzamelen en te verwerken, kunt u ze allemaal tegelijk implementeren en de routerings regels declareren waarmee ze verbinding maken.

In dit artikel wordt beschreven hoe de Azure Portal u begeleidt bij het maken van een implementatie manifest en het pushen van de implementatie naar een IoT Edge-apparaat. Voor informatie over het maken van een implementatie die is gericht op meerdere apparaten op basis van hun gedeelde labels, Zie [IOT Edge modules implementeren en bewaken op schaal](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Vereisten

* Een [IOT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.
* Een [IOT edge apparaat](how-to-register-device.md#register-in-the-azure-portal) waarop de IOT Edge-runtime is geïnstalleerd.

## <a name="select-your-device"></a>Uw apparaat selecteren

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw IOT-hub.
1. Selecteer **IOT Edge** in het menu.
1. Klik op de ID van het doel apparaat in de lijst met apparaten.
1. Selecteer **Modules instellen**.

## <a name="configure-a-deployment-manifest"></a>Een implementatie manifest configureren

Een implementatie manifest is een JSON-document waarin wordt beschreven welke modules moeten worden geïmplementeerd, hoe gegevens stromen tussen de modules en gewenste eigenschappen van de module apparaatdubbels. Zie [begrijpen hoe IOT Edge modules kunnen worden gebruikt, geconfigureerd en opnieuw worden gebruikt](module-composition.md)voor meer informatie over hoe implementatie manifesten werken en hoe u ze kunt maken.

De Azure Portal bevat een wizard die u helpt bij het maken van het implementatie manifest, in plaats van het JSON-document hand matig te bouwen. Er zijn drie stappen: **modules toevoegen**, **routes opgeven**en de **implementatie controleren**.

### <a name="add-modules"></a>Modules toevoegen

1. Geef in de sectie **container Registry-instellingen** van de pagina de referenties op voor toegang tot persoonlijke container registers die uw module installatie kopieën bevatten.

1. Selecteer in de sectie **implementatie modules** van de pagina **toevoegen**.

1. Bekijk de typen modules in de vervolg keuzelijst:

   * **Module IOT Edge** : de standaard optie.
   * **Azure stream Analytics** modules die zijn gegenereerd op basis van een Azure stream Analytics workload.
   * **Azure machine learning** model installatie kopieën die zijn gegenereerd op basis van een Azure machine learning-werk ruimte.

1. Selecteer de **Module IOT Edge**.

1. Geef een naam op voor de module en geef vervolgens de container installatie kopie op. Bijvoorbeeld:

   * **Naam** -SimulatedTemperatureSensor
   * **URI van installatie kopie** -MCR.Microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

1. Vul zo nodig de optionele velden in. Zie [EdgeAgent gewenste eigenschappen](module-edgeagent-edgehub.md#edgeagent-desired-properties)voor meer informatie over de opties voor het maken van containers, het opnieuw opstarten van beleid en de gewenste status. Zie voor meer informatie over de module twee [gewenste eigenschappen definiëren of bijwerken](module-composition.md#define-or-update-desired-properties).

1. Selecteer **Opslaan**.

1. Herhaal stap 2-6 om extra modules toe te voegen aan uw implementatie.

1. Selecteer **volgende** om door te gaan naar de sectie routes.

### <a name="specify-routes"></a>Routes opgeven

De wizard biedt standaard een route met de naam **route** en gedefinieerd als **van/* naar $upstream * *. Dit betekent dat alle berichten die door modules worden uitgevoerd, worden verzonden naar uw IOT-hub.  

Voeg de routes toe of werk deze bij met informatie van de [Declareer routes](module-composition.md#declare-routes)en selecteer vervolgens **volgende** om door te gaan naar de sectie beoordeling.

### <a name="review-deployment"></a>Implementatie controleren

In het gedeelte beoordeling ziet u het JSON-implementatie manifest dat is gemaakt op basis van uw selecties in de vorige twee secties. Houd er rekening mee dat er twee modules zijn gedeclareerd die u niet hebt toegevoegd: **$edgeAgent** en **$edgeHub**. Deze twee modules vormen de [IOT Edge runtime](iot-edge-runtime.md) en zijn de standaard instellingen voor elke implementatie.

Controleer uw implementatie gegevens en selecteer vervolgens **verzenden**.

## <a name="view-modules-on-your-device"></a>Modules op uw apparaat weer geven

Zodra u modules hebt geïmplementeerd op uw apparaat, kunt u ze weer geven op de pagina **Details van apparaat** van de portal. Op deze pagina ziet u de naam van elke geïmplementeerde module, evenals nuttige informatie, zoals de implementatie status en afsluit code.

## <a name="deploy-modules-from-azure-marketplace"></a>Modules implementeren vanuit Azure Marketplace

Azure Marketplace is een online Marketplace voor toepassingen en services waar u kunt bladeren door een breed scala aan bedrijfs toepassingen en oplossingen die zijn gecertificeerd en geoptimaliseerd om te worden uitgevoerd op Azure, met inbegrip van [IOT Edge-modules](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure Marketplace kan ook worden geopend via de Azure Portal onder **een resource maken**.

U kunt een IoT Edge module installeren vanuit Azure Marketplace of via de Azure Portal:

1. Zoek een module en begin met het implementatie proces.

   * Azure Portal: u vindt een module en selecteert u **maken**.

   * Azure Marketplace:

     1. Zoek een module en selecteer **nu ophalen**.
     1. Bevestig de gebruiks voorwaarden en het privacybeleid van de provider door **door gaan**te selecteren.

1. Kies uw abonnement en het IoT Hub waaraan het doel apparaat is gekoppeld.

1. Kies **implementeren op een apparaat**.

1. Voer de naam van het apparaat in of selecteer **apparaat zoeken** om te bladeren tussen de apparaten die zijn geregistreerd bij de hub.

1. Selecteer **maken** om door te gaan met het standaard proces voor het configureren van een implementatie manifest, inclusief het toevoegen van andere modules, indien gewenst. Details voor de nieuwe module, zoals afbeeldings-URI, opties voor maken en gewenste eigenschappen, zijn vooraf gedefinieerd, maar kunnen worden gewijzigd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [implementeren en bewaken van IOT Edge modules op schaal](how-to-deploy-monitor.md)
