---
title: bestand opnemen
description: bestand opnemen
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/31/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: abbde0cb8137650faad563248c8fafe7dc0f469a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75564706"
---
Een van de belangrijkste mogelijkheden van Azure IoT Edge is dat u er code voor uw IoT Edge-apparaten mee kunt implementeren vanuit de cloud. **IoT Edge-modules** zijn uitvoerbare pakketten die zijn geïmplementeerd als containers. In deze sectie implementeert u een vooraf gebouwde module vanuit het [gedeelte IoT Edge Modules van de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) rechtstreeks vanuit uw Azure IoT Hub.

De module die u in deze sectie implementeert, simuleert een sensor en verzendt gegenereerde gegevens. Deze module is een handig stukje code wanneer u aan de slag gaat met IoT Edge, omdat u de gesimuleerde gegevens kunt gebruiken voor ontwikkel- en testdoeleinden. Als u precies wilt zien wat deze module doet, kunt u de [broncode van de gesimuleerde temperatuursensor bekijken](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Voer de volgende stappen uit als u uw eerste module vanuit de Microsoft Azure Marketplace wilt implementeren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw IoT-hub.

1. Selecteer **IoT Edge**in het menu in het linkerdeelvenster onder **Automatisch apparaatbeheer**.

1. Klik op de apparaat-id van het doelapparaat in de lijst met apparaten.

1. Selecteer **modules instellen**op de bovenste balk .

1. Klik in het gedeelte **IoT Edge Modules** van de pagina op **Toevoegen**.

1. Selecteer **Marketplace-module**in het vervolgkeuzemenu .

   ![Gesimuleerde temperatuursensor in Azure Portal zoeken](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

1. Zoek in de **IoT Edge Module Marketplace**naar 'Gesimuleerde temperatuursensor' en selecteer die module.

1. Merk op dat de module SimulatedTemperatureSensor automatisch wordt ingevuld. In de zelfstudies gebruikt u deze pagina om meer modules aan uw implementatie toe te voegen. In deze quickstart implementeert u alleen deze ene module. Er zijn geen referenties vereist omdat deze openbaar zijn.

   ![Modules instellen op apparaat](./media/iot-edge-deploy-module/set-modules-on-device.png)

   Selecteer **Volgende: Routes** om door te gaan naar de volgende stap van de wizard.

1. Op het tabblad **Routes** van de wizard bepaalt u hoe berichten worden doorgegeven tussen modules en de IoT-hub. Berichten worden gemaakt met naam/waardeparen. In deze snelstart wilt u dat alle berichten van alle modules naar de IoT Hub (`$upstream`) worden gestuurd. Als deze niet automatisch wordt ingevuld, voegt u de volgende code toe voor de **waarde** voor de **naam:** `upstream`

   ```sql
    FROM /messages/* INTO $upstream
   ```

   Selecteer **Volgende: Controleren + maken** om door te gaan naar de volgende stap van de wizard.

1. Op het tabblad **Controleren + maken** van de wizard u een voorbeeld bekijken van het JSON-bestand dat alle modules definieert die worden geïmplementeerd op uw IoT Edge-apparaat. U ziet dat de module **SimulatedTemperatureSensor** hiervan deel uitmaakt, evenals twee extra systeemmodules **edgeAgent** en **edgeHub**. Selecteer **Maken** wanneer u klaar bent met bekijken.

   Wanneer u een nieuwe implementatie bij een IoT Edge-apparaat indient, wordt er niets naar uw apparaat gepusht. In plaats daarvan voert het apparaat regelmatig query’s uit naar eventuele nieuwe instructies. Als het apparaat een manifest van een bijgewerkte implementatie vindt, wordt de informatie over de nieuwe implementatie gebruikt om installatiekopieën van de module op te halen uit de cloud en wordt een lokale uitvoering van de modules gestart. Dit proces kan enkele minuten duren.

1. Nadat u de implementatiegegevens van de module hebt gemaakt, brengt de wizard u terug naar de **IoT Edge-pagina** van uw IoT-hub. Selecteer uw apparaat in de lijst met IoT Edge-apparaten om de details weer te geven.

1. Schuif op de pagina apparaatdetails omlaag naar het tabblad **Modules.** Er moeten drie modules worden vermeld: $edgeAgent, $edgeHub en SimulatedTemperatureSensor. Als bij een of meer modules wordt vermeld dat ze worden geïmplementeerd maar niet per apparaat worden gerapporteerd, worden ze nog door uw IoT Edge-apparaat opgestart. Wacht enkele momenten en selecteer **Vernieuwen** bovenaan de pagina.

   ![Weergave SimulatedTemperatureSensor in de lijst met geïmplementeerde modules](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
