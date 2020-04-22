---
title: Azure IoT Hub Ontwikkelen voor apparaten met beperkte beperking met IoT Hub C SDK
description: Handleiding voor ontwikkelaars - richtlijnen voor het ontwikkelen met Azure IoT-SDK's voor apparaten met beperkte beperkingen.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 9010ff582f05e81e17e280e20f180ceccf0e746f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733203"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Ontwikkelen voor apparaten met beperkte beperkingen met Azure IoT C SDK

Azure IoT Hub C SDK is geschreven in ANSI C (C99), waardoor het zeer geschikt is om een verscheidenheid aan platforms met een kleine schijf- en geheugenvoetafdruk te bedienen. Het aanbevolen RAM-geheugen is ten minste 64 KB, maar de exacte geheugenvoetafdruk is afhankelijk van het gebruikte protocol, het aantal geopende verbindingen en het beoogde platform.
> [!NOTE]
> * Azure IoT C SDK publiceert regelmatig informatie over het verbruik van resources om u te helpen bij de ontwikkeling.  Bezoek onze [GitHub repository](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) en bekijk de nieuwste benchmark.
>

C SDK is verkrijgbaar in pakketvorm van apt-get, NuGet en MBED. Als u apparaten met beperkte beperking wilt targeten, u de SDK lokaal bouwen voor uw doelplatform. Deze documentatie laat zien hoe bepaalde functies te verwijderen om de voetafdruk van de C SDK te verkleinen met behulp van [cmake](https://cmake.org/). Bovendien bespreekt deze documentatie de best practice-programmeermodellen voor het werken met beperkte apparaten.

## <a name="building-the-c-sdk-for-constrained-devices"></a>De C SDK bouwen voor apparaten met beperkte beperkingen

Bouw de C SDK voor apparaten met beperkte beperkingen.

### <a name="prerequisites"></a>Vereisten

Volg deze [C SDK setup guide](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) om uw ontwikkelomgeving voor te bereiden op het bouwen van de C SDK. Voordat u bij de stap voor het bouwen met cmake, u beroep doen op cmake vlaggen om ongebruikte functies te verwijderen.

### <a name="remove-additional-protocol-libraries"></a>Extra protocolbibliotheken verwijderen

C SDK ondersteunt vandaag vijf protocollen: MQTT, MQTT via WebSocket, AMQPs, AMQP via WebSocket en HTTPS. Voor de meeste scenario's zijn één tot twee protocollen vereist die op een client worden uitgevoerd, vandaar dat u de protocolbibliotheek die u niet gebruikt, uit de SDK verwijderen. Aanvullende informatie over het kiezen van het juiste communicatieprotocol voor uw scenario vindt u in [Kies een IoT Hub-communicatieprotocol](iot-hub-devguide-protocols.md). MQTT is bijvoorbeeld een lichtgewicht protocol dat vaak beter geschikt is voor apparaten met beperkte beperkingen.

U de AMQP- en HTTP-bibliotheken verwijderen met de volgende cmake-opdracht:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Sdk-logboekregistratie verwijderen

De C SDK biedt uitgebreide logging in de hele om te helpen met debuggen. U de logboekregistratiemogelijkheid voor productieapparaten verwijderen met de volgende cmake-opdracht:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Uploaden naar blob-mogelijkheden verwijderen

U grote bestanden uploaden naar Azure Storage met de ingebouwde mogelijkheid in de SDK. Azure IoT Hub fungeert als dispatcher voor een gekoppeld Azure Storage-account. U deze functie gebruiken om mediabestanden, grote telemetriebatches en logboeken te verzenden. U meer informatie krijgen over [het uploaden van bestanden met IoT Hub.](iot-hub-devguide-file-upload.md) Als uw toepassing deze functionaliteit niet vereist, u deze functie verwijderen met de volgende opdracht cmake:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Strip uitvoeren op Linux-omgeving

Als uw binaire bestanden op het Linux-systeem worden uitgevoerd, u de [stripopdracht](https://en.wikipedia.org/wiki/Strip_(Unix)) gebruiken om de grootte van de uiteindelijke toepassing te verkleinen na het compileren.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Programmeermodellen voor apparaten met beperkte beperking

Kijk vervolgens naar programmeermodellen voor apparaten met beperkte beperkingen.

### <a name="avoid-using-the-serializer"></a>Vermijd het gebruik van de Serializer

De C SDK heeft een optionele [C SDK serializer,](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer)waarmee u declaratieve kaarttabellen gebruiken om methoden en apparaattweelingeigenschappen te definiëren. De serializer is ontworpen om de ontwikkeling te vereenvoudigen, maar het voegt overhead toe, wat niet optimaal is voor apparaten met beperkte beperkingen. In dit geval u overwegen primitieve client API's en parse JSON te gebruiken met behulp van een lichtgewicht parser zoals [parson.](https://github.com/kgabis/parson)

### <a name="use-the-lower-layer-_ll_"></a>Gebruik de onderste laag _(LL)_

De C SDK ondersteunt twee programmeermodellen. Een set heeft API's met een LL-infix, wat staat voor lagere laag. _LL_ Deze set API's is lichter van gewicht en spin t/m worker threads niet, wat betekent dat de gebruiker handmatig de planning moet regelen. Voor de apparaatclient zijn bijvoorbeeld de _API's_ van de LL te vinden in dit [headerbestand.](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h) 

Een andere set API's zonder de _LL-index_ wordt de gemakslaag genoemd, waarbij een werknemersthread automatisch wordt gesponnen. De API's voor de gegevenslaag voor de apparaatclient zijn bijvoorbeeld te vinden in dit [Header-headerbestand van iot-apparaatclient.](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h) Voor apparaten met beperkte beperkingen waarbij elke extra thread een aanzienlijk percentage van de systeembronnen kan innemen, u overwegen _API's_ van LL te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Ga als voor meer informatie over Azure IoT C SDK-architectuur:
-    [Azure IoT C SDK-broncode](https://github.com/Azure/azure-iot-sdk-c/)
-    [Azure IoT-apparaat SDK voor C-introductie](iot-hub-device-sdk-c-intro.md)
