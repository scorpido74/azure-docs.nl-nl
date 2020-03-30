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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844582"
---
## <a name="deploy-event-grid-iot-edge-module"></a>GebeurtenisrasterIoT Edge-module implementeren

Er zijn verschillende manieren om modules te implementeren op een IoT Edge-apparaat en ze werken allemaal voor Azure Event Grid op IoT Edge. In dit artikel worden de stappen beschreven om gebeurtenisraster op IoT Edge te implementeren vanuit de Azure-portal.

>[!NOTE]
> In deze zelfstudie implementeert u de module Event Grid zonder volharding. Dit betekent dat alle onderwerpen en abonnementen die u in deze zelfstudie maakt, worden verwijderd wanneer u de module opnieuw implementeert. Zie de volgende artikelen: Status blijven bestaan [in Linux](../articles/event-grid/edge/persist-state-linux.md) of Persist status in [Windows](../articles/event-grid/edge/persist-state-windows.md)voor meer informatie over het instellen van persistentie. Voor productieworkloads raden we u aan de Event Grid-module met volharding te installeren.

>[!IMPORTANT]
> In deze zelfstudie wordt de module Event Grid geïmplementeerd met clientverificatie uitgeschakeld en staan HTTP-abonnees toe. Voor productieworkloads raden we u aan alleen HTTPS-verzoeken en abonnees in te schakelen met clientverificatie ingeschakeld. Zie [Beveiliging en verificatie](../articles/event-grid/edge/security-authentication.md)voor meer informatie over het veilig configureren van de gebeurtenisrastermodule.
 
### <a name="select-your-iot-edge-device"></a>Selecteer uw IoT Edge-apparaat

1. Aanmelden bij de [Azure-portal](https://portal.azure.com)
1. Navigeer naar uw IoT-hub.
1. Selecteer **IoT Edge** in het menu in de sectie **Automatisch apparaatbeheer.** 
1. Klik op de id van het doelapparaat in de lijst met apparaten
1. Selecteer **Modules instellen**. Houd de pagina open. U gaat verder met de stappen in het volgende gedeelte.

### <a name="configure-a-deployment-manifest"></a>Een implementatiemanifest configureren

Een implementatiemanifest is een JSON-document dat beschrijft welke modules moeten worden geïmplementeerd, hoe gegevens tussen de modules stromen en de gewenste eigenschappen van de moduletweeling. De Azure-portal heeft een wizard die u door het maken van een implementatiemanifest leidt, in plaats van het JSON-document handmatig te bouwen.  Het heeft drie stappen: **Modules toevoegen,** **Routes opgeven**en **Implementatie controleren**.

### <a name="add-modules"></a>Modules toevoegen

1. Selecteer **Toevoegen** in de sectie **Implementatiemodules**
1. Selecteer **IoT Edge Module** in de typen modules in de vervolgkeuzelijst
1. Geef de opties voor het maken van containers op:

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **Naam**: eventgridmodule
   * **Afbeelding URI**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opties voor het maken van containers:**

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

 1. Klik **op Opslaan**
 1. Klik **op Volgende** om verder te gaan naar de sectie routes

    > [!NOTE]
    > Als u een Azure VM als randapparaat gebruikt, voegt u een inkomende poortregel toe om binnenkomend verkeer op poort 4438 toe te staan. Zie Poorten openen voor [een vm voor](../articles/virtual-machines/windows/nsg-quickstart-portal.md)instructies voor het toevoegen van de regel.


### <a name="setup-routes"></a>Setup routes

 De standaardroutes behouden en **Volgende** selecteren om door te gaan naar de controlesectie

### <a name="review-deployment"></a>Implementatie controleren

1. In de sectie beoordeling ziet u het JSON-implementatiemanifest dat is gemaakt op basis van uw selecties in de vorige twee secties. Controleer of u de twee modules in de lijst ziet: **$edgeAgent** en **$edgeHub**. Deze twee modules vormen de IoT Edge-runtime en zijn vereist standaardinstellingen voor elke implementatie.
1. Controleer uw implementatiegegevens en selecteer **Verzenden**.

### <a name="verify-your-deployment"></a>Uw implementatie verifiëren

1. Nadat u de implementatie hebt verzonden, keert u terug naar de IoT Edge-pagina van uw IoT-hub.
1. Selecteer het **IoT Edge-apparaat** dat u met de implementatie hebt getarget om de details te openen.
1. Controleer in de apparaatgegevens of de module Gebeurtenisraster wordt vermeld als **opgegeven in implementatie** en Gerapporteerd per **apparaat**.

Het kan even duren voordat de module op het apparaat is gestart en vervolgens wordt gerapporteerd aan IoT Hub. Vernieuw de pagina om een bijgewerkte status te zien.