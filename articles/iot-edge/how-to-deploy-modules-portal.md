---
title: Implementeren van modules in Azure portal - Azure IoT Edge | Microsoft Docs
description: De Azure portal gebruiken voor het implementeren van modules in een IoT Edge-apparaat
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f27484fd1d47a2e29aa5083a7d440e5c7dba11c1
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839641"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Azure IoT Edge-modules van de Azure-portal implementeren

Als u IoT Edge modules met uw zakelijke logica maken, wilt u deze implementeren op uw apparaten te werken aan de rand. Als u meerdere modules die samenwerken om te verzamelen en verwerken van gegevens hebt, kunt u ze in één keer implementeren en declareert de routeringsregels waarmee ze zijn verbonden.

In dit artikel laat zien hoe de Azure-portal begeleidt u bij het maken van een manifest van de implementatie en het pushen van de implementatie naar een IoT Edge-apparaat. Zie voor meer informatie over het maken van een implementatie die gericht is op meerdere apparaten op basis van hun gedeelde labels [implementeren en controleren van IoT Edge-modules op schaal](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Vereisten

* Een [IoT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.
* Een [IoT Edge-apparaat](how-to-register-device-portal.md) met IoT Edge-runtime geïnstalleerd.

## <a name="select-your-device"></a>Selecteer uw apparaat

1. Aanmelden bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw IoT-hub.
1. Selecteer **IoT Edge** in het menu.
1. Klik op de ID van het doelapparaat uit de lijst met apparaten.
1. Selecteer **Modules instellen**.

## <a name="configure-a-deployment-manifest"></a>Een manifest van de implementatie configureren

Het manifest voor een implementatie is een JSON-document waarin wordt beschreven welke modules te implementeren, hoe gegevens stromen tussen de modules, en de gewenste eigenschappen van de moduledubbels. Zie voor meer informatie over hoe implementatie werk manifesten en hoe ze worden gemaakt, [te begrijpen hoe IoT Edge-modules kunnen worden gebruikt, geconfigureerd en opnieuw gebruikt](module-composition.md).

De Azure portal heeft een wizard waarmee wordt beschreven hoe u het manifest van de implementatie, in plaats van het JSON-document handmatig. De oplossing bestaat uit drie stappen: **Modules toevoegen**, **routes opgeven**en de **implementatie controleren**.

### <a name="add-modules"></a>Modules toevoegen

1. Geef in de sectie **container Registry-instellingen** van de pagina de referenties op voor toegang tot persoonlijke container registers die uw module installatie kopieën bevatten.

1. Selecteer in de sectie **implementatie modules** van de pagina **toevoegen**.

1. Kijken naar de soorten modules uit de vervolgkeuzelijst:

   * **IoT Edge-Module** -de standaardoptie.
   * **Azure Stream Analytics-Module** -alleen modules die zijn gegenereerd op basis van een Azure Stream Analytics-workload.
   * **Azure machine learning** model installatie kopieën die zijn gegenereerd op basis van een Azure machine learning-werk ruimte.

1. Selecteer de **IoT Edge-Module**.

1. Geef een naam op voor de module en geeft u de container-installatiekopie. Bijvoorbeeld:

   * **Naam** -SimulatedTemperatureSensor
   * **URI installatiekopie** -mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

1. Vul de optionele velden indien nodig. Voor meer informatie over container maken van opties voor beleid voor opnieuw opstarten en gewenste status zien [EdgeAgent gewenste eigenschappen](module-edgeagent-edgehub.md#edgeagent-desired-properties). Zie voor meer informatie over de moduledubbel [definiëren of update gewenste eigenschappen](module-composition.md#define-or-update-desired-properties).

1. Selecteer **Opslaan**.

1. Herhaal stappen 2 tot en met 6 aanvullende modules toevoegen aan uw implementatie.

1. Selecteer **volgende** om door te gaan naar de sectie routes.

### <a name="specify-routes"></a>Routes opgeven

Standaard de wizard u geeft een route met de naam **route** en gedefinieerd als **FROM /\* in $upstream**, wat betekent dat de uitvoer van alle modules die berichten worden verzonden naar uw IoT-hub.  

Toevoegen of bijwerken van de routes met gegevens uit [declareren routes](module-composition.md#declare-routes)en selecteer vervolgens **volgende** om door te gaan naar de sectie controleren.

### <a name="review-deployment"></a>Implementatie bekijken

De revisie sectie ziet u de JSON-implementatie manifest dat is gemaakt op basis van uw selecties in de vorige twee secties. Houd er rekening mee dat er twee modules aangegeven dat u hebt toegevoegd: **$edgeAgent** en **$edgeHub**. Deze twee modules waaruit de [IoT Edge-runtime](iot-edge-runtime.md) en zijn vereist standaardwaarden in elke implementatie.

Lees de informatie van uw implementatie, en selecteer vervolgens **indienen**.

## <a name="view-modules-on-your-device"></a>Modules weergeven op uw apparaat

Nadat u hebt modules geïmplementeerd op uw apparaat, vindt u alle mappen in de **Apparaatdetails** pagina van de portal. Deze pagina weergegeven de naam van elke geïmplementeerde module, evenals de nuttige informatie als de implementatie de status- en uitgiftemodules code.

## <a name="deploy-modules-from-azure-marketplace"></a>Modules implementeren vanuit Azure Marketplace

Azure Marketplace is een online Marketplace voor toepassingen en services waar u kunt bladeren door een breed scala aan bedrijfs toepassingen en oplossingen die zijn gecertificeerd en geoptimaliseerd om te worden uitgevoerd op Azure, met inbegrip van [IOT Edge-modules](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure Marketplace kan ook worden geopend via de Azure Portal onder **een resource maken**.

U kunt een IoT Edge module installeren vanuit Azure Marketplace of via de Azure Portal:

1. Zoek een module en begin met het implementatie proces.

   * Azure Portal: Zoek een module en selecteer **maken**.

   * Azure Marketplace:

     1. Zoek een module en selecteer **nu ophalen**.
     1. Bevestig de gebruiks voorwaarden en het privacybeleid van de provider door **door gaan**te selecteren.

1. Kies uw abonnement en het IoT Hub waaraan het doel apparaat is gekoppeld.

1. Kies **implementeren op een apparaat**.

1. Voer de naam van het apparaat in of selecteer **apparaat zoeken** om te bladeren tussen de apparaten die zijn geregistreerd bij de hub.

1. Selecteer **maken** om door te gaan met het standaard proces voor het configureren van een implementatie manifest, inclusief het toevoegen van andere modules, indien gewenst. Details voor de nieuwe module, zoals afbeeldings-URI, opties voor maken en gewenste eigenschappen, zijn vooraf gedefinieerd, maar kunnen worden gewijzigd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [implementeren en controleren van IoT Edge-modules op schaal](how-to-deploy-monitor.md)
