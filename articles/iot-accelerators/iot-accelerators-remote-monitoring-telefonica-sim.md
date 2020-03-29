---
title: SIM-gegevens integreren in de remote monitoring-oplossing - Azure| Microsoft Documenten
description: In dit artikel wordt beschreven hoe u Telefónica SIM-gegevens integreren in de remote monitoring-oplossing.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: b07e21131d9560a49d99644525835ac5ee3bac9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61442212"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>SIM-gegevens integreren in de oplossing voor externe monitoring

IoT-apparaten maken vaak verbinding met de cloud met behulp van een simkaart waarmee ze vanaf elke locatie gegevensstromen kunnen verzenden. De Azure IoT Remote Monitoring-oplossing maakt de integratie van IoT Managed Connectivity-gegevens mogelijk, zodat operators ook de status van het apparaat kunnen volgen via de gegevens die door de IoT-sim worden geleverd.

Remote Monitoring biedt out-of-the-box integratie met Telefónica IoT Connectivity, waardoor klanten die gebruik maken van het IoT Connectivity Platform hun apparaat-SIM-connectiviteitsgegevens synchroniseren met hun oplossingen. Deze oplossing kan worden uitgebreid om andere IoT Connectivity-providers te ondersteunen via [GitHub-repository.](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)

In deze zelfstudie leert u het volgende:

* Integreer IoT SIM-gegevens van Telefónica in de Remote Monitoring-oplossing
* Telemetrie in realtime weergeven
* SIM-gegevens weergeven

## <a name="telefnica-iot-integration-setup"></a>Telefónica IoT-integratie-installatie

### <a name="prerequisites"></a>Vereisten

Deze extra functie voor externe controle is momenteel in preview. Voer de volgende stappen uit om uw verbindingsgegevens te synchroniseren met Azure Remote Monitoring Solution:

1. Vul een aanvraag in op [de site van Telefónica,](https://iot.telefonica.com/contact)selecteer de optie **Azure Remote Monitoring**, inclusief uw contactgegevens.
2. Telefónica activeert uw account.
3. Als u nog geen Telefónica-client bent en u wilt genieten van deze of andere IoT Connectivity Cloud Ready-services, gaat u naar [de site van Telefónica](https://iot.telefonica.com/) en selecteert u de optie **Connectiviteit.**

### <a name="telefnica-sim-setup"></a>Telefónica SIM-installatie
Telefónica SIM & Azure Twin device ID association is gebaseerd op de eigenschap "alias" van Telefónica IoT SIM. 

Navigeer naar [Telefónica IoT Connectivity Platform Portal](https://m2m-movistar-es.telefonica.com/) > SIM-inventaris > Selecteer uw SIM-kaart en werk elke SIM-alias bij met uw gewenste Twin-apparaatID. Deze taak kan ook worden uitgevoerd in bulk-modus (zie Telefónica IoT Connectivity Platform handleidingen).

Deze taak kan ook worden uitgevoerd in bulkmodus (zie de gebruikershandleidingen van het Telefónica IoT Connectivity Platform)

![Telefónica-update](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Als u uw apparaat wilt verbinden met de bewaking op afstand, u deze zelfstudies volgen met [C](iot-accelerators-connecting-devices-linux.md) of [Node.](iot-accelerators-connecting-devices-node.md) 

## <a name="view-device-telemetry-and-sim-properties"></a>Telemetrie en SIM-eigenschappen van het apparaat weergeven

Zodra uw Telefónica-account goed is geconfigureerd en uw apparaat is verbonden, u apparaatgegevens en SIM-gegevens bekijken.

De volgende verbindingsparameters worden gepubliceerd:

* ICCID
* IP
* Aanwezigheid van het netwerk
* SIM-status
* Locatie op basis van netwerk
* Verbruikt dataverkeer

![Dashboard](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht hebt van hoe simgegevens kunnen worden geïntegreerd in Azure IoT Remote Monitoring, worden hier de volgende stappen voorgesteld voor versnellers van oplossingen:

* [De Azure IoT Remote Monitoring-oplossing bedienen](quickstart-remote-monitoring-deploy.md)
* [Geavanceerde bewaking uitvoeren](iot-accelerators-remote-monitoring-monitor.md)
* [Uw apparaten beheren](iot-accelerators-remote-monitoring-manage.md)
* [Apparaatproblemen oplossen](iot-accelerators-remote-monitoring-maintain.md)

