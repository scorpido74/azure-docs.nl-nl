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
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564706"
---
Een van de belangrijkste mogelijkheden van Azure IoT Edge is dat u er code voor uw IoT Edge-apparaten mee kunt implementeren vanuit de cloud. **IoT Edge-modules** zijn uitvoerbare pakketten die zijn geïmplementeerd als containers. In deze sectie implementeert u een vooraf gemaakte module van het [gedeelte IOT Edge modules van de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) rechtstreeks vanuit uw azure-IOT hub.

De module die u in deze sectie implementeert, simuleert een sensor en verzendt gegenereerde gegevens. Deze module is een handig stukje code wanneer u aan de slag gaat met IoT Edge, omdat u de gesimuleerde gegevens kunt gebruiken voor ontwikkel- en testdoeleinden. Als u precies wilt zien wat deze module doet, kunt u de [broncode van de gesimuleerde temperatuursensor bekijken](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Voer de volgende stappen uit als u uw eerste module vanuit de Microsoft Azure Marketplace wilt implementeren:

1. Aanmelden bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw IoT-hub.

1. Selecteer in het menu in het linkerdeel venster onder **automatische Apparaatbeheer**de optie **IOT Edge**.

1. Klik op de apparaat-ID van het doel apparaat in de lijst met apparaten.

1. Selecteer op de bovenste balk **modules instellen**.

1. Klik in de sectie **IOT Edge modules** van de pagina op **toevoegen**.

1. Selecteer in de vervolg keuzelijst **Marketplace-module**.

   ![Gesimuleerde temperatuursensor in Azure Portal zoeken](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

1. Zoek in de Marketplace van de **IOT Edge-module**op ' gesimuleerde temperatuur sensor ' en selecteer die module.

1. U ziet dat de SimulatedTemperatureSensor-module automatisch wordt ingevuld. In de zelfstudies gebruikt u deze pagina om meer modules aan uw implementatie toe te voegen. In deze quickstart implementeert u alleen deze ene module. Er zijn geen referenties vereist omdat deze openbaar zijn.

   ![Modules op apparaat instellen](./media/iot-edge-deploy-module/set-modules-on-device.png)

   Selecteer **volgende: routes** om door te gaan naar de volgende stap van de wizard.

1. Op het tabblad **routes** van de wizard definieert u hoe berichten worden door gegeven tussen modules en de IOT hub. Berichten worden gemaakt met behulp van naam/waarde-paren. In deze snelstart wilt u dat alle berichten van alle modules naar de IoT Hub (`$upstream`) worden gestuurd. Als de **naam** niet automatisch wordt ingevuld, voegt u de volgende code toe voor de **waarde** `upstream`:

   ```sql
    FROM /messages/* INTO $upstream
   ```

   Selecteer **volgende: controleren + maken** om door te gaan naar de volgende stap van de wizard.

1. Op het tabblad **controleren en maken** van de wizard kunt u een voor beeld van het JSON-bestand weer geven waarmee alle modules worden gedefinieerd die worden geïmplementeerd op uw IOT edge-apparaat. U ziet dat de module **SimulatedTemperatureSensor** hiervan deel uitmaakt, evenals twee extra systeemmodules **edgeAgent** en **edgeHub**. Selecteer **maken** wanneer u klaar bent met controleren.

   Wanneer u een nieuwe implementatie bij een IoT Edge-apparaat indient, wordt er niets naar uw apparaat gepusht. In plaats daarvan voert het apparaat regelmatig query’s uit naar eventuele nieuwe instructies. Als het apparaat een manifest van een bijgewerkte implementatie vindt, wordt de informatie over de nieuwe implementatie gebruikt om installatiekopieën van de module op te halen uit de cloud en wordt een lokale uitvoering van de modules gestart. Dit proces kan enkele minuten duren.

1. Nadat u de implementatie details van de module hebt gemaakt, keert de wizard terug naar de pagina **IOT Edge** van uw IOT-hub. Selecteer uw apparaat in de lijst met IoT Edge-apparaten om de details weer te geven.

1. Ga op de pagina Details van apparaat naar het tabblad **modules** . Er moeten drie modules worden weer gegeven: $edgeAgent, $edgeHub en SimulatedTemperatureSensor. Als bij een of meer modules wordt vermeld dat ze worden geïmplementeerd maar niet per apparaat worden gerapporteerd, worden ze nog door uw IoT Edge-apparaat opgestart. Wacht enkele momenten en selecteer **Vernieuwen** bovenaan de pagina.

   ![Weergave SimulatedTemperatureSensor in de lijst met geïmplementeerde modules](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
