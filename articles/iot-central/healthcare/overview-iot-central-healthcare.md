---
title: Bouw oplossingen voor gezondheidszorg met Azure IoT Central | Microsoft Documenten
description: Meer informatie over het bouwen van zorgoplossingen met Azure IoT Central-toepassingssjablonen.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 2aee5c56b267f9243b69a48dfd0f5f64196c5f23
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77021483"
---
# <a name="building-healthcare-solutions-with-azure-iot-central"></a>Oplossingen voor de gezondheidszorg bouwen met Azure IoT Central 



Leer om oplossingen voor gezondheidszorg te bouwen met Azure IoT Central met behulp van toepassingssjablonen.

## <a name="what-is-continuous-patient-monitoring-template"></a>Wat is een sjabloon voor continue patiëntbewaking?

In de iot-ruimte in de gezondheidszorg is Continuous Patient Monitoring een van de belangrijkste factoren om het risico op heropnames te verminderen, chronische ziekten effectiever te beheren en de resultaten van patiënten te verbeteren. Continue patiëntbewaking kan worden opgesplitst in twee belangrijke categorieën:

1. **Intramurale monitoring**: Met behulp van medische wearables en andere apparaten in het ziekenhuis kunnen zorgteams de vitale functies en medische aandoeningen van de patiënt controleren zonder dat ze een verpleegkundige meerdere keren per dag hoeven te sturen om een patiënt te controleren. Zorgteams kunnen begrijpen op het moment dat een patiënt kritische aandacht nodig heeft door middel van meldingen en hun tijd effectief prioriteren.
1. **Monitoring**op afstand : Door gebruik te maken van medische wearables en door patiënten gerapporteerde resultaten (PROs) om patiënten buiten het ziekenhuis te monitoren, kan het risico op overname worden verminderd. Gegevens van patiënten met chronische ziekten en revalidatiepatiënten kunnen worden verzameld om ervoor te zorgen dat patiënten zich aan de zorgplannen houden en dat waarschuwingen voor verslechtering van de patiënt kunnen worden opgedoken aan zorgteams voordat ze kritiek worden.

Deze toepassingssjabloon kan worden gebruikt om oplossingen te bouwen voor beide categorieën continue patiëntbewaking. De voordelen zijn:

* Sluit verschillende soorten medische wearables naadloos aan op een IoT Central-exemplaar.
* Controleer en beheer de apparaten om ervoor te zorgen dat ze gezond blijven.
* Maak aangepaste regels rond apparaatgegevens om de juiste waarschuwingen te activeren.
* Exporteer uw patiëntstatusgegevens naar de Azure API voor FHIR, een compatibel gegevensarchief.
* Exporteer de geaggregeerde inzichten naar bestaande of nieuwe bedrijfstoepassingen.

>[!div class="mx-imgBorder"] 
>![CPM-dashboard](media/in-patient-dashboard.png)

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met het bouwen van een Continuous Patient monitoring oplossing:

* [De toepassingssjabloon implementeren](tutorial-continuous-patient-monitoring.md)
* [Bekijk een voorbeeldarchitectuur](concept-continuous-patient-monitoring-architecture.md)