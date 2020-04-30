---
title: Gezondheids zorg oplossingen bouwen met Azure IoT Central | Microsoft Docs
description: Meer informatie over het bouwen van een gezondheids zorg oplossing met behulp van Azure IoT Central-toepassings sjablonen.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 2aee5c56b267f9243b69a48dfd0f5f64196c5f23
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77021483"
---
# <a name="building-healthcare-solutions-with-azure-iot-central"></a>Oplossingen voor de gezondheidszorg bouwen met Azure IoT Central 



Meer informatie over het bouwen van gezondheids oplossingen met Azure IoT Central met behulp van toepassings sjablonen.

## <a name="what-is-continuous-patient-monitoring-template"></a>Wat is een continue patiënten-bewakings sjabloon?

In de IoT-ruimte in de gezondheids zorg is doorlopende patiënt bewaking een van de belangrijkste mogelijkheden van het verminderen van het risico van het vrijmaken van de informatie, het beheer van chronische ziekten effectiever en het verbeteren van de resultaten van patiënten. Doorlopende patiënt bewaking kan worden opgesplitst in twee hoofd categorieën:

1. **Bewaking in de patiënt**: door gebruik te maken van medische wearables en andere apparaten in het zieken huis kunnen zorg teams de vitale symptomen en medische omstandigheden controleren zonder dat ze een verpleegt te hoeven verzenden om meerdere keren per dag op een patiënt te controleren. Care teams kunnen inzicht krijgen in het moment dat een patiënt essentiële aandacht nodig heeft via meldingen en de prioriteit van hun tijd kan aanpassen.
1. **Bewaking van externe patiënten**: door gebruik te maken van medische wearables en patiënten (voor delen) om patiënten buiten het zieken huis te bewaken, kan het risico van de hertoelating worden verlaagd. Gegevens van de chronische ziekte patiënten en de versterkings patiënten kunnen worden verzameld om ervoor te zorgen dat patiënten zich houden aan de onderhouds plannen en dat waarschuwingen over de verslechtering van de patiënt kunnen worden afgesteld op Care teams voordat ze van belang zijn.

Deze toepassings sjabloon kan worden gebruikt voor het ontwikkelen van oplossingen voor beide categorieën van voortdurende patiënten-bewaking. De voordelen zijn:

* Verbind de verschillende soorten medische Wearables naadloos met een IoT Central-exemplaar.
* Bewaak en beheer de apparaten om ervoor te zorgen dat ze in orde blijven.
* Aangepaste regels rond apparaatgegevens maken om de juiste waarschuwingen te activeren.
* Exporteer uw patiënten-status gegevens naar de Azure-API voor FHIR, een compatibele gegevens opslag.
* De geaggregeerde inzichten exporteren naar bestaande of nieuwe zakelijke toepassingen.

>[!div class="mx-imgBorder"] 
>![CPM-dash board](media/in-patient-dashboard.png)

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met het bouwen van een continue patiënten-bewakings oplossing:

* [De toepassings sjabloon implementeren](tutorial-continuous-patient-monitoring.md)
* [Bekijk een voorbeeld architectuur](concept-continuous-patient-monitoring-architecture.md)