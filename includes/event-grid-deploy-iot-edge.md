---
title: bestand opnemen
description: bestand opnemen
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b453a04a170764a037eed7415eaf71e5a4d37526
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844582"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Event Grid IoT Edge-module implementeren

Er zijn verschillende manieren om modules op een IoT Edge apparaat te implementeren en ze werken allemaal voor Azure Event Grid op IoT Edge. In dit artikel worden de stappen beschreven voor het implementeren van Event Grid op IoT Edge van de Azure Portal.

>[!NOTE]
> In deze zelf studie implementeert u de module Event Grid zonder persistentie. Dit betekent dat alle onderwerpen en abonnementen die u in deze zelf studie maakt, worden verwijderd wanneer u de module opnieuw implementeert. Raadpleeg de volgende artikelen voor meer informatie over het instellen van persistentie: [behoud de status in Linux](../articles/event-grid/edge/persist-state-linux.md) of [persistent in Windows](../articles/event-grid/edge/persist-state-windows.md). Voor werk belastingen wordt u aangeraden de Event Grid module met persistentie te installeren.

>[!IMPORTANT]
> In deze zelf studie wordt Event Grid module geïmplementeerd met de client verificatie uitgeschakeld en worden HTTP-abonnees toegestaan. Voor werk belastingen wordt u aangeraden alleen HTTPS-aanvragen en abonnees in te scha kelen met client verificatie ingeschakeld. Zie [beveiliging en verificatie](../articles/event-grid/edge/security-authentication.md)voor meer informatie over het veilig configureren van Event grid module.
 
### <a name="select-your-iot-edge-device"></a>Uw IoT Edge-apparaat selecteren

1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Navigeer naar uw IoT Hub.
1. Selecteer **IOT Edge** in het menu van het gedeelte **Automatic Device Management** . 
1. Klik op de ID van het doel apparaat in de lijst met apparaten
1. Selecteer **Modules instellen**. Laat de pagina geopend. U gaat verder met de stappen in de volgende sectie.

### <a name="configure-a-deployment-manifest"></a>Een manifest van de implementatie configureren

Het manifest voor een implementatie is een JSON-document waarin wordt beschreven welke modules te implementeren, hoe gegevens stromen tussen de modules, en de gewenste eigenschappen van de moduledubbels. De Azure Portal bevat een wizard die u helpt bij het maken van een implementatie manifest, in plaats van het JSON-document hand matig te bouwen.  Er drie stappen: **modules toevoegen**, **routes opgeven**, en **implementatie bekijken**.

### <a name="add-modules"></a>Modules toevoegen

1. Selecteer in de sectie **implementatie modules** de optie **toevoegen**
1. Selecteer **IOT Edge module** uit de typen modules in de vervolg keuzelijst.
1. Geef de naam, de afbeelding en de opties voor het maken van de container van de container op:

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **Naam**: eventgridmodule
   * **Afbeeldings-URI**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opties**voor het maken van containers:

    ```json
        {
          "Env": [
            "inbound__clientAuth:clientCert__enabled=false",
            "outbound__webhook__httpsOnly=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                  "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```

 1. Klik op **Opslaan**.
 1. Klik op **volgende** om door te gaan naar de sectie routes

    > [!NOTE]
    > Als u een virtuele machine van Azure als een edge-apparaat gebruikt, voegt u een regel voor binnenkomende poort toe om binnenkomend verkeer op poort 4438 toe te staan. Zie [poorten openen voor een virtuele machine](../articles/virtual-machines/windows/nsg-quickstart-portal.md)voor instructies over het toevoegen van de regel.


### <a name="setup-routes"></a>Installatie routes

 Behoud de standaard routes en selecteer **volgende** om door te gaan naar de sectie beoordeling

### <a name="review-deployment"></a>Implementatie bekijken

1. De revisie sectie ziet u de JSON-implementatie manifest dat is gemaakt op basis van uw selecties in de vorige twee secties. Controleer of de twee modules in de lijst worden weer geven: **$edgeAgent** en **$edgeHub**. Deze twee modules vormen de IoT Edge runtime en zijn de standaard instellingen voor elke implementatie.
1. Lees de informatie van uw implementatie, en selecteer vervolgens **indienen**.

### <a name="verify-your-deployment"></a>Uw implementatie verifiëren

1. Nadat u de implementatie hebt verzonden, keert u terug naar de pagina IoT Edge van uw IoT-hub.
1. Selecteer het **IOT edge apparaat** waarop de implementatie is gericht om de details ervan te openen.
1. Controleer in de details van het apparaat of de Event Grid module wordt vermeld als **opgegeven in de implementatie** en wordt **gerapporteerd door het apparaat**.

Het kan even duren voordat de module op het apparaat is gestart en vervolgens weer aan IoT Hub is gemeld. Vernieuw de pagina om de bijgewerkte status weer te geven.