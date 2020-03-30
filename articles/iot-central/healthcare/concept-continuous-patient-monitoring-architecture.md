---
title: Architectuur voor continue patiëntbewaking in Azure IoT Central | Microsoft Documenten
description: Meer informatie over een continue architectuur van patiëntmonitoringoplossingen.
author: philmea
ms.author: philmea
ms.date: 10/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 92eb4157abb55b7056952d1fb064c7c7d7500335
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77021693"
---
# <a name="continuous-patient-monitoring-architecture"></a>Architectuur van continue patiëntbewaking



Continue oplossingen voor patiëntbewaking kunnen worden gebouwd met behulp van de meegeleverde app-sjabloon en het gebruik van de architectuur die hieronder als leidraad wordt beschreven.

>[!div class="mx-imgBorder"] 
>![CPM-architectuur](media/cpm-architecture.png)

1. Medische apparaten communiceren via Bluetooth Low Energy (BLE)
1. Mobiele telefoon gateway ontvangen BLE-gegevens en verzenden naar IoT Central
1. Continue gegevensexport van patiëntgezondheidsgegevens naar de Azure API voor FHIR&reg;
1. Machine learning op basis van interoperabele gegevens
1. Dashboard van het zorgteam gebouwd op FHIR-gegevens

## <a name="details"></a>Details
In deze sectie wordt elk deel van het architectuurdiagram nader beschreven.

### <a name="ble-medical-devices"></a>BLE medische hulpmiddelen
Veel medische wearables die worden gebruikt in de Gezondheidszorg IoT-ruimte zijn Bluetooth Low Energy-apparaten. Ze kunnen niet rechtstreeks met de cloud praten en moeten door een gateway. Deze architectuur stelt voor het gebruik van een mobiele telefoon applicatie als deze gateway.

### <a name="mobile-phone-gateway"></a>Mobiele telefoongateway
De primaire functie van de mobiele telefoontoepassing is het opnemen van BLE-gegevens van medische apparaten en deze te communiceren naar Azure IoT Central. Daarnaast kan de app patiënten helpen bij het instellen en inrichten van een apparaat en hen helpen hun persoonlijke gezondheidsgegevens te bekijken. Andere oplossingen kunnen gebruik maken van een tablet gateway of een statische gateway als binnen een ziekenhuis kamer om dezelfde communicatie stroom te bereiken.

### <a name="export-to-azure-api-for-fhirreg"></a>Exporteren naar Azure API voor FHIR&reg;
Azure IoT Central is HIPAA-compatibel en HITRUST-gecertificeerd,&reg; maar u ook patiëntstatusgerelateerde gegevens naar de Azure API voor FHIR verzenden. [Azure API voor FHIR](../../healthcare-apis/overview.md) is een volledig beheerde, op standaarden gebaseerde, compatibele API voor klinische gezondheidsgegevens waarmee u nieuwe systemen voor betrokkenheid bij uw gezondheidsgegevens maken. Het maakt een snelle uitwisseling van gegevens mogelijk via FHIR API's, ondersteund door een beheerd Platform-as-a Service (PaaS) aanbod in de cloud. Met behulp van de continuous data export-functionaliteit van IoT Central u gegevens verzenden naar de Azure API voor FHIR.

### <a name="machine-learning"></a>Machine learning
Nadat u uw gegevens hebt samengevoegd en vertaald naar FHIR-formaat, u machine learning-modellen bouwen die inzichten kunnen verrijken en slimmere besluitvorming voor uw zorgteam mogelijk kunnen maken. Er zijn verschillende soorten services die kunnen worden gebruikt voor het bouwen, trainen en implementeren van machine learning-modellen. Meer informatie over het gebruik van het machine learning-aanbod van Azure vindt u in onze [machine learning-documentatie.](../../machine-learning/index.yml)

### <a name="provider-dashboard"></a>Providerdashboard
De gegevens in de Azure API voor FHIR kunnen worden gebruikt om een dashboard voor patiëntinzichten te bouwen of kunnen direct worden geïntegreerd in een EPD om zorgteams te helpen de patiëntstatus te visualiseren. Zorgteams kunnen dit dashboard gebruiken om te zorgen voor patiënten die hulp nodig hebben en vroegtijdige waarschuwingssignalen van verslechtering te herkennen. Volg ons [handleiding om](howto-health-data-triage.md)te leren hoe u een Power BI-dashboard voor realtime provider bouwen.

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het implementeren van een sjabloon voor continue toepassingen voor patiëntbewaking](tutorial-continuous-patient-monitoring.md)