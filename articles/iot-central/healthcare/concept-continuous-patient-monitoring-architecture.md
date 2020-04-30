---
title: Bewakings architectuur voor voortdurende patiënten in azure IoT Central | Microsoft Docs
description: Meer informatie over de architectuur van een continue patiënten-bewakings oplossing.
author: philmea
ms.author: philmea
ms.date: 10/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 92eb4157abb55b7056952d1fb064c7c7d7500335
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77021693"
---
# <a name="continuous-patient-monitoring-architecture"></a>Architectuur van continue patiëntbewaking



Continue patiënten-bewakings oplossingen kunnen worden gebouwd met behulp van de app-sjabloon die is verschaft en met behulp van de architectuur die hieronder wordt beschreven.

>[!div class="mx-imgBorder"] 
>![CPM-architectuur](media/cpm-architecture.png)

1. Medische apparaten die communiceren met behulp van Bluetooth Low Energy (Bel)
1. Mobiele telefoon gateway die de gegevens van de trein ontvangt en naar IoT Central stuurt
1. Voortdurende gegevens export van patiënten-status gegevens naar de Azure API voor FHIR&reg;
1. Machine learning op basis van interoperabele gegevens
1. Care team-dash board gebaseerd op FHIR-gegevens

## <a name="details"></a>Details
In deze sectie wordt elk deel van het architectuur diagram uitvoeriger beschreven.

### <a name="ble-medical-devices"></a>Uitstaande medische apparaten
Veel medische Wearables die worden gebruikt in de gezondheids zorg, IoT-ruimte zijn Bluetooth Low Energy-apparaten. Ze kunnen niet rechtstreeks aan de Cloud spreken en moeten door een gateway worden door gegeven. Deze architectuur raadt u aan om een mobiele telefoon toepassing te gebruiken als deze gateway.

### <a name="mobile-phone-gateway"></a>Gateway voor mobiele telefoon
De primaire functie van de mobiele-telefoon toepassing is het opnemen van de gegevens van medische apparaten en het communiceren met Azure IoT Central. Daarnaast kan de app ervoor zorgen dat patiënten via een apparaat worden ingesteld en de stroom wordt ingericht en kunnen ze een overzicht krijgen van hun persoonlijke status gegevens. Andere oplossingen kunnen een Tablet gateway of een statische gateway gebruiken in een ziekenhuis kamer om dezelfde communicatie stroom te krijgen.

### <a name="export-to-azure-api-for-fhirreg"></a>Exporteren naar Azure-API voor FHIR&reg;
Azure IoT Central is met HIPAA-compatibel en&reg; HITRUST gecertificeerd, maar u kunt ook aan de Azure-API voor FHIR informatie over de status van een patiënt verzenden. De [Azure-API voor FHIR](../../healthcare-apis/overview.md) is een volledig beheerde, op standaarden gebaseerde compatibele API voor klinische status gegevens waarmee u nieuwe systemen van betrokkenheid kunt maken met uw status gegevens. Hiermee kunnen gegevens snel worden uitgewisseld via FHIR Api's, die worden ondersteund door een beheerde platform-as-a-Service (PaaS) in de Cloud. Met de functie continue gegevens export van IoT Central kunt u gegevens verzenden naar de Azure API voor FHIR.

### <a name="machine-learning"></a>Machine learning
Na het samen voegen van uw gegevens en het vertalen ervan naar de FHIR-indeling, kunt u machine learning modellen bouwen die inzicht kunnen krijgen en slimmere besluit vorming mogelijk maken voor uw Care team. Er zijn verschillende soorten services die kunnen worden gebruikt om machine learning modellen te bouwen, te trainen en te implementeren. Meer informatie over het gebruik van de machine learning-aanbiedingen van Azure vindt u in onze [machine learning documentatie](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Provider dashboard
De gegevens die zich bevinden in de Azure-API voor FHIR kunnen worden gebruikt voor het bouwen van een patiënt Insights-dash board of kunnen rechtstreeks worden geïntegreerd in een EMR om te zorgen dat teams de status van patiënten visualiseren. Care teams kunnen dit dash board gebruiken om ervoor te zorgen dat patiënten op het gebied van ondersteuning en vroegtijdige waarschuwings signalen moeten worden verslechterd. Voor meer informatie over het bouwen van een Power BI real-time provider-dash board kunt u de [hand leiding](howto-health-data-triage.md)volgen.

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het implementeren van een sjabloon voor continue patiënten-bewakings toepassingen](tutorial-continuous-patient-monitoring.md)