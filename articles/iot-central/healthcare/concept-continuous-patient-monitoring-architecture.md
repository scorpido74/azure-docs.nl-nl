---
title: Continue patiëntbewakingsarchitectuur in Azure IoT Central | Microsoft Docs
description: Meer informatie over de architectuur van een continue patiëntbewakingsoplossing.
author: philmea
ms.author: philmea
ms.date: 7/23/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 0032f341330ad394241806a4fe61add530253f09
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116863"
---
# <a name="continuous-patient-monitoring-architecture"></a>Architectuur van continue patiëntbewaking



Continue patiëntbewakingsoplossingen kunnen gebouwd worden met behulp van de aangeboden app-sjabloon en met behulp van de architectuur die hieronder wordt beschreven.

>[!div class="mx-imgBorder"] 
>![CPM-architectuur](media/cpm-architecture.png)

1. Medische apparaten die communiceren met behulp van Bluetooth Low Energy (BLE)
1. Mobiele telefoongateway die de BLE-gegevens ontvangt en naar IoT Central stuurt
1. Voortdurende gegevensexport van patiëntstatusgegevens naar de Azure API for FHIR&reg;
1. Machine learning op basis van interoperabele gegevens
1. Zorgteam-dashboard gebaseerd op FHIR-gegevens

## <a name="details"></a>Details
In deze sectie wordt elk deel van het architectuurdiagram uitvoeriger beschreven.

### <a name="ble-medical-devices"></a>BLE medische apparaten
Veel medische wearables die worden gebruikt in de IoT-ruimte van de gezondheidszorg zijn Bluetooth Low Energy-apparaten. Ze kunnen niet rechtstreeks met de cloud communiceren en moeten via een gateway worden doorgegeven. Met deze architectuur is het aan te raden om een mobiele telefoon-toepassing te gebruiken als deze gateway. 

### <a name="mobile-phone-gateway"></a>Gateway voor mobiele telefoon
De primaire functie van de mobiele telefoon-toepassing is het opnemen van de BLE-gegevens van medische apparaten en deze aan Azure IoT Central doorgeven. Daarnaast kan de app patiënten door het installatieproces en de inrichtingsstroom van een apparaat leiden en kunnen ze een overzicht krijgen van hun persoonlijke gezondheidsgegevens. Andere oplossingen kunnen een tablet-gateway of een statische gateway gebruiken indien deze zich in een ziekenhuiskamer bevinden om dezelfde communicatiestroom te krijgen. We hebben een open source-voorbeeld van een mobiele toepassing gemaakt dat beschikbaar is voor Android en iOS. U kunt dit voorbeeld gebruiken als uitgangspunt om uw toepassingen te ontwikkelen. Zie [Azure Samples](https://docs.microsoft.com/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/) voor meer informatie over het voorbeeld van een mobiele app voor de doorlopende patiëntbewaking van IoT Central.

### <a name="export-to-azure-api-for-fhirreg"></a>Exporteren naar Azure API for FHIR&reg;
Azure IoT Central is HIPAA-conform en HITRUST&reg;-gecertificeerd, maar u wilt wellicht ook gegevens verzenden die betrekking hebben op de status van patiënten naar de Azure API for FHIR. [Azure API for FHIR](../../healthcare-apis/overview.md) is een volledig beheerde, op standaarden gebaseerde conforme API voor klinische gezondheidsgegevens waarmee u nieuwe betrokkenheidssystemen kunt maken met uw gezondheidsgegevens. Hiermee kunnen gegevens snel worden uitgewisseld via FHIR API's, die worden ondersteund door een beheerd Platform-as-a-Service (PaaS) in de c cloud. Met de functie Continue gegevensexport van IoT Central kunt u gegevens verzenden naar de Azure API for FHIR via de [Azure IoT-connector for FHIR](https://docs.microsoft.com/azure/healthcare-apis/iot-fhir-portal-quickstart).

### <a name="machine-learning"></a>Machine learning
Nadat u uw gegevens hebt samengevoegd en ze naar de FHIR-indeling hebt vertaald, kunt u machine learning-modellen bouwen die inzichten kunnen verrijken en slimmere besluitvorming mogelijk maken voor uw zorgteam. Er zijn verschillende soorten services die kunnen worden gebruikt om machine learning-modellen te bouwen, te trainen en te implementeren. Meer informatie over het gebruik van de machine learning-aanbiedingen van Azure vindt u in onze [documentatie voor machine learning](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Provider-dashboard
De gegevens die zich bevinden in de Azure API for FHIR kunnen worden gebruikt voor het bouwen van een dashboard voor patiëntinzichten of kunnen rechtstreeks worden geïntegreerd in een EMR om teams te helpen de status van patiënten te visualiseren. Zorgteams kunnen dit dashboard gebruiken om patiënten die assistentie nodig hebben te ondersteunen en om vroegtijdige waarschuwingssignalen voor verslechtering van hun situatie te zien. Als u wilt weten hoe u een Power BI real-time provider-dashboard kunt bouwen, volgt u onze [instructiegids](howto-health-data-triage.md).

## <a name="next-steps"></a>Volgende stappen
* [Leer hoe u een toepassingssjabloon voor continue patiëntbewaking kunt implementeren](tutorial-continuous-patient-monitoring.md)