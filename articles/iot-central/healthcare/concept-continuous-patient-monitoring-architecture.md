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
ms.openlocfilehash: cf8cfc5fe75ff5364f812af034d772c7aadd891c
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027476"
---
# <a name="continuous-patient-monitoring-architecture"></a>Bewakings architectuur voortdurende patiënten

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Continue patiënten-bewakings oplossingen kunnen worden gebouwd door gebruik te maken van de app-sjabloon die is verschaft en de architectuur hieronder als richt lijn te gebruiken.

>[!div class="mx-imgBorder"] 
>![CPM-architectuur](media/cpm-architecture.png)

1. Medische apparaten communiceren via Bluetooth Low Energy (Bel)
1. Mobiele telefoon gateway die de gegevens van de trein ontvangt en naar IoT Central stuurt
1. Voortdurende gegevens export van patiënten-status gegevens naar de Azure API voor FHIR-&reg;
1. Machine learning op basis van interoperabele gegevens
1. Care team-dash board gebaseerd op FHIR-gegevens

## <a name="details"></a>Details
In deze sectie wordt elk deel van het architectuur diagram uitvoeriger beschreven.

### <a name="ble-medical-devices"></a>Uitstaande medische apparaten
Veel medische Wearables die worden gebruikt in de in de gezondheids zorg gebruikte IoT-ruimte zijn Bluetooth Low Energy-apparaten, wat betekent dat ze niet rechtstreeks naar de Cloud kunnen praten en moeten door een gateway worden door gegeven. Deze architectuur raadt u aan om een mobiele telefoon toepassing te gebruiken als deze gateway.

### <a name="mobile-phone-gateway"></a>Gateway voor mobiele telefoon
De primaire functie van de mobiele-telefoon toepassing is het opnemen van de gegevens van medische apparaten en het communiceren met Azure IoT Central. Daarnaast kan de app ervoor zorgen dat patiënten via een apparaat worden ingesteld en de stroom wordt ingericht en kunnen ze een overzicht krijgen van hun persoonlijke status gegevens. Andere oplossingen kunnen een Tablet gateway of een statische gateway gebruiken in een ziekenhuis kamer om dezelfde communicatie stroom te krijgen.

### <a name="export-to-azure-api-for-fhirreg"></a>Exporteren naar Azure-API voor FHIR&reg;
Azure IoT Central is HIPAA-compatibel en HITRUST&reg; gecertificeerd, maar mogelijk wilt u ook aan de Azure-API voor FHIR de status gegevens van patiënten verzenden. De [Azure-API voor FHIR](../../healthcare-apis/overview.md) is een volledig beheerde, op standaarden gebaseerde compatibele API voor klinische status gegevens waarmee u nieuwe systemen van betrokkenheid kunt maken met uw status gegevens. Hiermee kunnen gegevens snel worden uitgewisseld via FHIR Api's, die worden ondersteund door een beheerde platform-as-a-Service (PaaS) in de Cloud. Met de functie continue gegevens export van IoT Central kunt u gegevens verzenden naar de Azure API voor FHIR.

### <a name="machine-learning"></a>Machine Learning
Na het samen voegen van uw gegevens en het vertalen ervan naar de FHIR-indeling, kunt u machine learning modellen bouwen die inzicht kunnen krijgen en slimmere besluit vorming mogelijk maken voor uw Care team. Er zijn diverse services die kunnen worden gebruikt om machine learning modellen te bouwen, te trainen en te implementeren. Meer informatie over het gebruik van de machine learning-aanbiedingen van Azure vindt u in onze [machine learning documentatie](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Provider dashboard
De gegevens die zich bevinden in de Azure-API voor FHIR kunnen worden gebruikt voor het bouwen van een patiënt Insights-dash board of kunnen rechtstreeks worden geïntegreerd in een EMR om te zorgen dat teams de status van patiënten visualiseren. Care teams kunnen dit dash board gebruiken om actie te ondernemen voor patiënten die op het gebied van ondersteuning en vroegtijdige waarschuwings signalen moeten worden gewaarschuwd. Voor meer informatie over het bouwen van een Power BI real-time provider-dash board kunt u de [hand leiding](howto-health-data-triage.md)volgen.

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het implementeren van een sjabloon voor continue patiënten-bewakings toepassingen](tutorial-continuous-patient-monitoring.md)