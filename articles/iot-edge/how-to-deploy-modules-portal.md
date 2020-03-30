---
title: Modules implementeren vanuit Azure-portal - Azure IoT Edge
description: Gebruik uw IoT-hub in de Azure-portal om een IoT Edge-module van uw IoT-hub naar uw IoT Edge-apparaat te pushen, zoals geconfigureerd door een implementatiemanifest.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5c44561895bc1905328ec0eb357bee1c68a8eb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271445"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Azure IoT Edge-modules implementeren vanuit de Azure-portal

Zodra u IoT Edge-modules hebt gemaakt met uw bedrijfslogica, wilt u deze implementeren op uw apparaten om aan de rand te werken. Als u meerdere modules hebt die samenwerken om gegevens te verzamelen en te verwerken, u ze allemaal tegelijk implementeren en de routeringsregels declareren die deze verbinden.

In dit artikel ziet u hoe de Azure-portal u begeleidt bij het maken van een implementatiemanifest en het pushen van de implementatie naar een IoT Edge-apparaat. Zie [IoT Edge-modules op schaal implementeren en bewaken](how-to-deploy-monitor.md)voor informatie over het maken van een implementatie die zich richt op meerdere apparaten op basis van hun gedeelde tags.

## <a name="prerequisites"></a>Vereisten

* Een [IoT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.
* Een [IoT Edge-apparaat](how-to-register-device.md#register-in-the-azure-portal) met de IoT Edge-runtime geïnstalleerd.

## <a name="configure-a-deployment-manifest"></a>Een implementatiemanifest configureren

Een implementatiemanifest is een JSON-document dat beschrijft welke modules moeten worden geïmplementeerd, hoe gegevens tussen de modules stromen en de gewenste eigenschappen van de moduletweeling. Zie [Begrijpen hoe IoT Edge-modules kunnen worden gebruikt, geconfigureerd en hergebruikt](module-composition.md)voor meer informatie over hoe implementatiemanifesten werken en hoe u deze maken.

De Azure-portal heeft een wizard die u door het implementatiemanifest leidt, in plaats van het JSON-document handmatig te bouwen. Het heeft drie stappen: **Modules toevoegen,** **Routes opgeven**en **Implementatie controleren**.

### <a name="select-device-and-add-modules"></a>Apparaat selecteren en modules toevoegen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw IoT-hub.
1. Selecteer **IoT Edge** in het menu in het linkerdeelvenster.
1. Klik op de id van het doelapparaat in de lijst met apparaten.
1. Selecteer **modules instellen**op de bovenste balk .
1. Geef in de sectie **Containerregisterinstellingen** van de pagina de referenties op om toegang te krijgen tot alle privécontainerregisters die uw moduleafbeeldingen bevatten.
1. Selecteer In de sectie **IoT Edge Modules** van de pagina de optie **Toevoegen**.
1. Bekijk de typen modules in het vervolgkeuzemenu:

   * **IoT Edge Module** - U geeft de modulenaam en containerafbeelding URI. De afbeelding URI voor de module SimulatedTemperatureSensor is `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`bijvoorbeeld . Als de moduleafbeelding is opgeslagen in een privécontainerregister, voegt u de referenties op deze pagina toe om toegang te krijgen tot de afbeelding.
   * **Marketplace-module** - Modules die worden gehost in de Azure Marketplace. Sommige marketplace-modules vereisen extra configuratie, dus bekijk de moduledetails in de lijst [met Azure Marketplace IoT Edge-modules.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)
   * **Azure Stream Analytics-module** - Modules die zijn gegenereerd vanuit een Azure Stream Analytics-workload.

1. Nadat u een module hebt toegevoegd, selecteert u de modulenaam in de lijst om de module-instellingen te openen. Vul indien nodig de optionele velden in. Zie [EdgeAgent gewenste eigenschappen](module-edgeagent-edgehub.md#edgeagent-desired-properties)voor meer informatie over opties voor het maken van containers, het beleid opnieuw starten en de gewenste status. Zie Gewenste eigenschappen definiëren [of bijwerken voor](module-composition.md#define-or-update-desired-properties)meer informatie over de moduletwin.
1. Herhaal indien nodig stap 5 tot en met 8 om extra modules toe te voegen aan uw implementatie.
1. Selecteer **Volgende: Routes** om door te gaan naar de sectie routes.

### <a name="specify-routes"></a>Routes opgeven

Op het tabblad **Routes** bepaalt u hoe berichten worden doorgegeven tussen modules en de IoT-hub. Berichten worden gemaakt met naam/waardeparen. Standaard wordt een route **route** genoemd en gedefinieerd als **FROM\* /messages/ INTO $upstream**, wat betekent dat berichten die door modules worden uitgevoerd naar uw IoT-hub worden verzonden.  

Voeg de routes toe of bij met informatie uit [De routes declareren](module-composition.md#declare-routes)en selecteer **Volgende: Controleren + maken** om door te gaan naar de volgende stap van de wizard.

### <a name="review-deployment"></a>Implementatie controleren

In de sectie beoordeling ziet u het JSON-implementatiemanifest dat is gemaakt op basis van uw selecties in de vorige twee secties. Houd er rekening mee dat er twee modules zijn die zijn opgegeven en die u niet hebt toegevoegd: **$edgeAgent** en **$edgeHub**. Deze twee modules vormen de [IoT Edge-runtime](iot-edge-runtime.md) en zijn vereist standaardinstellingen voor elke implementatie.

Controleer uw implementatiegegevens en selecteer **Vervolgens Maken**.

## <a name="view-modules-on-your-device"></a>Modules weergeven op uw apparaat

Zodra u modules op uw apparaat hebt geïmplementeerd, u ze allemaal bekijken op de pagina met apparaatgegevens van uw IoT-hub. Op deze pagina wordt de naam van elke geïmplementeerde module weergegeven, evenals nuttige informatie zoals de implementatiestatus en afsluitcode.

## <a name="deploy-modules-from-azure-marketplace"></a>Modules implementeren vanuit Azure Marketplace

[Azure Marketplace](https://azuremarketplace.microsoft.com/) is een online marktplaats voor toepassingen en services waar u bladeren door een breed scala aan bedrijfstoepassingen en -oplossingen die zijn gecertificeerd en geoptimaliseerd om op Azure te worden uitgevoerd, inclusief [IoT Edge-modules.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)

U een IoT Edge-module implementeren vanuit de Azure Marketplace en vanuit uw IoT-hub.

### <a name="deploy-from-azure-marketplace"></a>Implementeren vanuit Azure Marketplace

Bekijk de IoT Edge-modules in de markt en wanneer u de gewenste modules vindt, u deze implementeren door **Nu maken** of **te krijgen.** Ga verder met de stappen van de implementatiewizard die kunnen variëren afhankelijk van de IoT Edge-module die u hebt geselecteerd:

1. Erken de gebruiksvoorwaarden en het privacybeleid van de provider door **Doorgaan te selecteren.** Mogelijk moet u eerst contactgegevens verstrekken.
1. Kies uw abonnement en de IoT-hub waaraan het doelapparaat is gekoppeld.
1. Kies **Implementeren naar een apparaat**.
1. Voer de naam van het apparaat in of selecteer **Apparaat zoeken** om te bladeren tussen de apparaten die bij de hub zijn geregistreerd.
1. Selecteer **Maken** om het standaardproces voort te zetten bij het configureren van een implementatiemanifest, inclusief het toevoegen van andere modules indien gewenst. Details voor de nieuwe module, zoals image URI, maakopties en gewenste eigenschappen zijn vooraf gedefinieerd, maar kunnen worden gewijzigd.

Controleer of de module is geïmplementeerd in uw IoT-hub in de Azure-portal. Selecteer uw apparaat, selecteer **Modules instellen** en de module moet worden weergegeven in de sectie **IoT Edge Modules.**

### <a name="deploy-from-azure-iot-hub"></a>Implementeren vanuit Azure IoT Hub

U een module vanuit de Azure Marketplace snel implementeren op uw apparaat in uw IoT-hub in de Azure-portal.

1. Navigeer in de Azure-portal naar uw IoT-hub.
1. Selecteer In het linkerdeelvenster onder **Automatisch apparaatbeheer**de optie **IoT Edge**.
1. Selecteer het IoT Edge-apparaat dat de implementatie moet ontvangen.
1. Selecteer **modules instellen**op de bovenste balk .
1. Klik in de sectie **IoT Edge Modules** op **Toevoegen**en selecteer **Marketplace-module** in het vervolgkeuzemenu.

![Module toevoegen in IoT-hub](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Kies een module op de **marketplace-pagina van iot edge-modules.** De module die u selecteert, wordt automatisch geconfigureerd voor uw abonnement, resourcegroep en apparaat. Het verschijnt dan in uw lijst met IoT Edge-modules. Voor sommige modules is mogelijk een extra configuratie vereist.

> [!TIP]
> De informatie over IoT Edge-modules van de Azure IoT Hub is beperkt. U eerst meer informatie vinden over de [IoT Edge-modules](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) in de Azure Marketplace.

Selecteer **Volgende: Routes** en ga verder met de implementatie zoals beschreven [door Routes opgeven](#specify-routes) en Implementatie [controleren](#review-deployment) eerder in dit artikel.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [implementeren en bewaken van IoT Edge-modules op schaal](how-to-deploy-monitor.md)
