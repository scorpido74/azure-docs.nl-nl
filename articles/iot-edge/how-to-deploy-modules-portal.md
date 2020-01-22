---
title: Modules implementeren vanuit Azure Portal-Azure IoT Edge
description: Gebruik uw IoT Hub in de Azure Portal om een IoT Edge module te pushen van uw IoT Hub naar uw IoT Edge-apparaat, zoals geconfigureerd door een implementatie manifest.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e4bf079d46271cadc0176fe5602adcdc3954dee5
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290713"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Azure IoT Edge-modules van de Azure-portal implementeren

Als u IoT Edge modules met uw zakelijke logica maken, wilt u deze implementeren op uw apparaten te werken aan de rand. Als u meerdere modules die samenwerken om te verzamelen en verwerken van gegevens hebt, kunt u ze in één keer implementeren en declareert de routeringsregels waarmee ze zijn verbonden.

In dit artikel laat zien hoe de Azure-portal begeleidt u bij het maken van een manifest van de implementatie en het pushen van de implementatie naar een IoT Edge-apparaat. Zie [IOT Edge modules op schaal implementeren en bewaken](how-to-deploy-monitor.md)voor meer informatie over het maken van een implementatie die gericht is op meerdere apparaten op basis van hun gedeelde labels.

## <a name="prerequisites"></a>Vereisten

* Een [IoT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.
* Een [IoT Edge-apparaat](how-to-register-device.md#register-in-the-azure-portal) met IoT Edge-runtime geïnstalleerd.

## <a name="configure-a-deployment-manifest"></a>Een manifest van de implementatie configureren

Het manifest voor een implementatie is een JSON-document waarin wordt beschreven welke modules te implementeren, hoe gegevens stromen tussen de modules, en de gewenste eigenschappen van de moduledubbels. Zie voor meer informatie over hoe implementatie werk manifesten en hoe ze worden gemaakt, [te begrijpen hoe IoT Edge-modules kunnen worden gebruikt, geconfigureerd en opnieuw gebruikt](module-composition.md).

De Azure portal heeft een wizard waarmee wordt beschreven hoe u het manifest van de implementatie, in plaats van het JSON-document handmatig. Er drie stappen: **modules toevoegen**, **routes opgeven**, en **implementatie bekijken**.

### <a name="select-device-and-add-modules"></a>Apparaat selecteren en modules toevoegen

1. Aanmelden bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw IoT-hub.
1. Selecteer in het linkerdeel venster **IOT Edge** in het menu.
1. Klik op de ID van het doelapparaat uit de lijst met apparaten.
1. Selecteer op de bovenste balk **modules instellen**.
1. Geef in de sectie **container Registry-instellingen** van de pagina de referenties op voor toegang tot persoonlijke container registers die uw module installatie kopieën bevatten.
1. Selecteer in de sectie **IOT Edge-modules** van de pagina de optie **toevoegen**.
1. Bekijk de typen modules in de vervolg keuzelijst:

   * **Module IOT Edge** : u geeft de module naam en URI van de container installatie kopie op. De afbeeldings-URI voor de voor beeld-SimulatedTemperatureSensor-module is bijvoorbeeld `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`. Als de module installatie kopie is opgeslagen in een persoonlijk container register, voegt u de referenties op deze pagina toe om de installatie kopie te openen. 
   * **Marketplace-module** : modules die worden gehost op de Azure Marketplace. Voor sommige Marketplace-modules is aanvullende configuratie vereist. Controleer dus de module gegevens in de lijst met [IOT Edge modules van Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) .
   * **Azure stream Analytics module** : modules die zijn gegenereerd op basis van een Azure stream Analytics workload. 

1. Nadat u een module hebt toegevoegd, selecteert u de module naam in de lijst om de module-instellingen te openen. Vul de optionele velden indien nodig. Voor meer informatie over container maken van opties voor beleid voor opnieuw opstarten en gewenste status zien [EdgeAgent gewenste eigenschappen](module-edgeagent-edgehub.md#edgeagent-desired-properties). Zie voor meer informatie over de moduledubbel [definiëren of update gewenste eigenschappen](module-composition.md#define-or-update-desired-properties).
1. Herhaal zo nodig stap 5 tot en met 8 om extra modules toe te voegen aan uw implementatie.
1. Selecteer **volgende: routes** om door te gaan naar de sectie routes.

### <a name="specify-routes"></a>Routes opgeven

Op het tabblad **routes** definieert u hoe berichten worden door gegeven tussen modules en de IOT hub. Berichten worden gemaakt met behulp van naam/waarde-paren. Een route wordt standaard **route ring** genoemd en gedefinieerd als **vanuit/messages/\* in $upstream**. Dit betekent dat alle berichten die door modules worden uitgevoerd, worden verzonden naar uw IOT-hub.  

Voeg de routes toe of werk deze bij met informatie van [Declareer routes](module-composition.md#declare-routes)en selecteer vervolgens **volgende: controleren + maken** om door te gaan naar de volgende stap van de wizard.

### <a name="review-deployment"></a>Implementatie bekijken

De revisie sectie ziet u de JSON-implementatie manifest dat is gemaakt op basis van uw selecties in de vorige twee secties. Houd er rekening mee dat er twee modules aangegeven dat u hebt toegevoegd: **$edgeAgent** en **$edgeHub**. Deze twee modules waaruit de [IoT Edge-runtime](iot-edge-runtime.md) en zijn vereist standaardwaarden in elke implementatie.

Controleer uw implementatie gegevens en selecteer vervolgens **maken**.

## <a name="view-modules-on-your-device"></a>Modules weergeven op uw apparaat

Zodra u modules hebt geïmplementeerd op uw apparaat, kunt u ze weer geven op de pagina Details van apparaat van uw IoT Hub. Deze pagina weergegeven de naam van elke geïmplementeerde module, evenals de nuttige informatie als de implementatie de status- en uitgiftemodules code.

## <a name="deploy-modules-from-azure-marketplace"></a>Modules implementeren vanuit Azure Marketplace

[Azure Marketplace](https://azuremarketplace.microsoft.com/) is een online Marketplace voor toepassingen en services waar u kunt bladeren door een breed scala aan bedrijfs toepassingen en oplossingen die zijn gecertificeerd en geoptimaliseerd om te worden uitgevoerd op Azure, met inbegrip van [IOT Edge-modules](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

U kunt een IoT Edge module implementeren vanuit Azure Marketplace en vanaf uw IoT Hub.

### <a name="deploy-from-azure-marketplace"></a>Implementeren vanuit Azure Marketplace

Bekijk de IoT Edge-modules in de Marketplace en wanneer u het gewenste item kunt implementeren door **maken** of **nu downloaden**te selecteren. Ga door met de stappen in de wizard implementatie die kunnen variëren, afhankelijk van de IoT Edge module die u hebt geselecteerd:

1. Bevestig de gebruiks voorwaarden en het privacybeleid van de provider door **door gaan**te selecteren. Mogelijk moet u eerst contact gegevens opgeven.
1. Kies uw abonnement en het IoT Hub waaraan het doel apparaat is gekoppeld.
1. Kies **implementeren op een apparaat**.
1. Voer de naam van het apparaat in of selecteer **apparaat zoeken** om te bladeren tussen de apparaten die zijn geregistreerd bij de hub.
1. Selecteer **maken** om door te gaan met het standaard proces voor het configureren van een implementatie manifest, inclusief het toevoegen van andere modules, indien gewenst. Details voor de nieuwe module, zoals afbeeldings-URI, opties voor maken en gewenste eigenschappen, zijn vooraf gedefinieerd, maar kunnen worden gewijzigd.

Controleer of de module is geïmplementeerd in uw IoT Hub in de Azure Portal. Selecteer uw apparaat, selecteer **modules instellen** en de module moet worden weer gegeven in de sectie **IOT Edge modules** .

### <a name="deploy-from-azure-iot-hub"></a>Implementeren vanuit Azure IoT Hub

U kunt snel een module implementeren vanuit Azure Marketplace op uw apparaat in uw IoT Hub in de Azure Portal.

1. Ga in het Azure Portal naar uw IoT Hub.
1. Selecteer in het linkerdeel venster onder **automatische Apparaatbeheer**de optie **IOT Edge**.
1. Selecteer het IoT Edge apparaat dat de implementatie moet ontvangen.
1. Selecteer op de bovenste balk **modules instellen**.
1. Klik in de sectie **IOT Edge modules** op **toevoegen**en selecteer **Marketplace module** in de vervolg keuzelijst.

![Module toevoegen in IoT Hub](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Kies een module op de **Marketplace** -pagina van de IOT Edge-module. De module die u selecteert, wordt automatisch geconfigureerd voor uw abonnement, de resource groep en het apparaat. Deze wordt vervolgens weer gegeven in de lijst met IoT Edge-modules. Voor sommige modules is mogelijk aanvullende configuratie vereist.

> [!TIP]
> De informatie over IoT Edge modules van de Azure-IoT Hub is beperkt. U kunt eerst meer te weten komen over de [IOT Edge modules](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) in de Azure Marketplace.

Selecteer **volgende: routes** en ga door met de implementatie zoals beschreven in [routes opgeven](#specify-routes) en de [implementatie controleren](#review-deployment) eerder in dit artikel.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [implementeren en controleren van IoT Edge-modules op schaal](how-to-deploy-monitor.md)
