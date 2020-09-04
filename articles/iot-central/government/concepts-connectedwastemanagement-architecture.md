---
title: Referentiearchitectuur voor een oplossing voor verbonden afvalverwerking die is gebouwd met Azure IoT Central | Microsoft Docs
description: Leer concepten voor een oplossing voor verbonden afvalverwerking die is gebouwd met Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 763fb82e0725e5a98112af3a79856c3703a5c2c3
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "77016082"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Referentiearchitectuur voor verbonden afvalverwerking 



Een oplossing voor het beheer van verbonden afvalverwerking kan worden ontwikkeld met behulp van de **Azure IoT Central app-sjabloon** als uitgangspunt voor een IoT-toepassing. In dit artikel vindt u uitgebreide richtlijnen voor referentiearchitectuur voor het bouwen van een end-to-end-oplossing. 

![Architectuur voor verbonden afvalverwerking](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


Concepten:

1. Apparaten en connectiviteit  
1. IoT Central 
2. Uitbreidbaarheid en integraties
3. Zakelijke toepassingen

Laten we eens kijken naar de belangrijkste onderdelen die in het algemeen een rol spelen in een oplossing voor het bewaken van waterverbruik.

## <a name="devices-and-connectivity"></a>Apparaten en connectiviteit 
Apparaten die worden gebruikt in open omgevingen, zoals afvalbakken, kunnen via een externe netwerkprovider worden verbonden via LPWAN (Low Power Wide Area Networks). Voor deze typen apparaten kunt u [Azure IoT Central Device Bridge](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) gebruiken om uw apparaatgegevens naar uw IoT-toepassing in Azure IoT Central te verzenden. U kunt ook gebruikmaken van apparaatgateways die IP-capabel zijn en rechtstreeks verbinding kunnen maken met IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central is een IoT-app-platform, waarmee u snel aan de slag kunt met uw IoT-oplossing. U kunt uw oplossing voorzien van een merk, aanpassen en integreren met services van derden.
Nadat u uw slimme waterapparaten hebt aangesloten op IoT Central, kunt u het apparaat instructies geven en bedienen, beschikt u over een bewakings- en waarschuwingssysteem, een gebruikersinterface met ingebouwde RBAC, configureerbare dashboards om inzichten te verkrijgen en mogelijkheden om uit te breiden. 

## <a name="extensibility-and-integrations"></a>Uitbreidbaarheid en integraties 
U kunt uw IoT-toepassing uitbreiden in IoT Central, en optioneel:
* uw IoT-gegevens voor geavanceerde analyses transformeren en integreren, bijvoorbeeld voor het trainen van machine-learningmodellen, door middel van continue gegevensexport vanuit de IoT Central-toepassing. 
* werkstromen in andere systemen automatiseren door acties te activeren met behulp van Microsoft Flow of webhooks van de IoT Central-toepassing
* programmatisch toegang krijgen tot uw IoT-toepassing in IoT Central via IoT Central-API's.

## <a name="business-applications"></a>Zakelijke toepassingen 
De IoT-gegevens kunnen worden gebruikt om een groot aantal zakelijke toepassingen binnen een afvalverwerkingsbedrijf aan te sturen. Als u wilt weten hoe u de IoT Central-toepassing voor verbonden afvalverwerking met services in het veld kunt verbinden, volgt u de zelfstudie over [integreren met Dynamics 365-veldservices](./how-to-configure-connected-field-services.md) 

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het maken van IoT Central-toepassing voor [verbonden afvalverwerking](./tutorial-connected-waste-management.md)
* Meer informatie over [IoT Central-sjablonen voor de overheid](./overview-iot-central-government.md)
* Zie [Overzicht van IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central) voor meer informatie over IoT Central

