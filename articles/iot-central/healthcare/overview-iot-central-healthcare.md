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
ms.openlocfilehash: 7051a4257bc45f0c9266be14678a1409be66fe9e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958026"
---
# <a name="building-healthcare-solutions-with-azure-iot-central"></a>Gezondheids oplossingen bouwen met Azure IoT Central 

Meer informatie over het bouwen van gezondheids oplossingen met Azure IoT Central met behulp van toepassings sjablonen.

## <a name="what-is-continuous-patient-monitoring-template"></a>Wat is een continue patiënten-bewakings sjabloon?

In de IoT-ruimte in de gezondheids zorg is doorlopende patiënt bewaking een van de belangrijkste mogelijkheden van het verminderen van het risico van het vrijmaken van de informatie, het beheer van chronische ziekten effectiever en het verbeteren van de resultaten van patiënten. Doorlopende patiënt bewaking kan worden opgesplitst in twee hoofd categorieën:

1. **Bewaking in de patiënt**: door gebruik te maken van medische wearables en andere apparaten in het zieken huis kunnen zorg teams de vitale symptomen en medische omstandigheden controleren zonder dat ze een verpleegt te hoeven verzenden om meerdere keren per dag op een patiënt te controleren. Care teams kunnen inzicht krijgen in het moment dat een patiënt essentiële aandacht nodig heeft via meldingen en de prioriteit van hun tijd kan aanpassen.
1. **Bewaking van externe patiënten**: door gebruik te maken van medische wearables en door patiënten gerapporteerde uitkomsten voor het bewaken van patiënten buiten het zieken huis, kan het risico van hersynchronisatie aanzienlijk worden verminderd. Gegevens van de chronische ziekte patiënten en de versterkings patiënten kunnen worden verzameld om ervoor te zorgen dat patiënten zich houden aan de onderhouds plannen en dat waarschuwingen over de verslechtering van de patiënt kunnen worden afgesteld op Care teams voordat ze van belang zijn.

Deze toepassings sjabloon kan worden gebruikt voor het ontwikkelen van oplossingen voor beide categorieën van voortdurende patiënten-bewaking. De voor delen zijn onder andere:

* Verbind op naadloze manieren een verscheidenheid aan medische Wearables met een IoT Central-exemplaar.
* Bewaak en beheer de apparaten om ervoor te zorgen dat ze in orde blijven.
* Aangepaste regels rond apparaatgegevens maken om de juiste waarschuwingen te activeren.
* Exporteer uw patiënten-status gegevens naar de Azure-API voor FHIR, een compatibele gegevens opslag.
* De geaggregeerde inzichten exporteren naar bestaande of nieuwe zakelijke toepassingen die zorg teams bieden.

>[!div class="mx-imgBorder"] 
>![CPM-dash board](media/in-patient-dashboard.png)

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met het bouwen van een continue patiënten-bewakings oplossing:

* [De toepassings sjabloon implementeren](tutorial-continuous-patient-monitoring.md)
* [Bekijk een voorbeeld architectuur](concept-continuous-patient-monitoring-architecture.md)