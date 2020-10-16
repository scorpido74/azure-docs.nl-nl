---
title: bestand opnemen
description: bestand opnemen
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 7246375468b3419c3d52ee3d5a51a95aa20050a9
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876625"
---
De voorbeeldtoepassing bevat twee gesimuleerde apparaten en één IoT Edge-gateway. De volgende zelfstudies bieden twee benaderingen om te experimenteren met de mogelijkheden van de gateway en deze te begrijpen:

* Maak de IoT Edge-gateway in een Azure-VM en verbind een gesimuleerde camera.
* Maak de IoT Edge-gateway op een echt apparaat, zoals een Intel NUC, en verbind een echte camera.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * De Azure IoT Central-toepassingssjabloon voor videoanalyse gebruiken om een detailhandelstoepassing te maken
> * De toepassingsinstellingen aanpassen
> * Een apparaatsjabloon maken voor een IoT Edge-gatewayapparaat
> * Een gatewayapparaat toevoegen aan uw IoT Central-toepassing

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudiereeks hebt u het volgende nodig:

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u er een maken via de [Azure-registratiepagina](https://aka.ms/createazuresubscription).
* Als u een echte camera gebruikt, moet er een verbinding zijn tussen het IoT Edge-apparaat en de camera en hebt u het kanaal **Real-Time Streaming Protocol** nodig.

## <a name="initial-setup"></a>Eerste configuratie

In deze zelfstudies gaat u een aantal configuratiebestanden bijwerken en gebruiken. Eerste versies van deze bestanden zijn beschikbaar in de GitHub-opslagplaats [LVA-gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway). De opslagplaats bevat ook een kladblokbestand dat u kunt downloaden en gebruiken om configuratiewaarden vast te leggen van de services die u implementeert.

Maak een map genaamd *lva-configuration* op uw lokale computer om exemplaren van deze bestanden op te slaan. Klik vervolgens met de rechtermuisknop op elk van de volgende koppelingen en kies **Opslaan als** om het bestand op te slaan in de map *lva-configuration*:
