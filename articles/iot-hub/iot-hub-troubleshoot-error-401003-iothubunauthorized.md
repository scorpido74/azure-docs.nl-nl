---
title: Azure IoT Hub-fout oplossen 401003 IoTHubOngeautoriseerd
description: Begrijpen hoe u fout 401003 IoTHubUnauthorized oplossen
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d0a9f643516af5945037acb3dd1da24b06944171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284406"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

In dit artikel worden de oorzaken en oplossingen voor **401003 IoTHubUnauthorized-fouten** beschreven.

## <a name="symptoms"></a>Symptomen

### <a name="symptom-1"></a>Symptoom 1

In diagnostische logboeken ziet u een patroon van apparaten die de verbinding verbreken met **401003 IoTHubUnauthorized**, gevolgd door **404104 DeviceConnectionConnectionOp afstand**gesloten en kort daarna met succes verbinding maken.

### <a name="symptom-2"></a>Symptoom 2

Aanvragen voor IoT Hub mislukken met een van de volgende foutmeldingen:

* Autorisatiekoptekst ontbreekt
* IotHub\*' ' bevat niet\*het opgegeven apparaat ' '
* Autorisatieregel\*' ' staat geen\*toegang toe voor '
* Verificatie is mislukt voor dit apparaat, token of certificaat vernieuwen en opnieuw verbinding maken
* Duimafdruk komt niet overeen met de\*configuratie: Duimafdruk: SHA1Hash= , SHA2Hash=\*; Configuratie: PrimaryThumbprint=\*, SecondaryThumbprint=\*

## <a name="cause"></a>Oorzaak

### <a name="cause-1"></a>Oorzaak 1

Voor MQTT vertrouwen sommige SDK's op IoT Hub om de verbinding uit te koppelen wanneer het SAS-token verloopt om te weten wanneer het moet worden vernieuwd. Dus 

1. Het SAS-token verloopt
1. IoT Hub merkt de vervaldatum op en koppelt het apparaat los met **401003 IoTHubUnauthorized**
1. Het apparaat voltooit de verbinding met **404104 DeviceConnectionRemotely**
1. De IoT SDK genereert een nieuw SAS-token
1. Het apparaat maakt opnieuw verbinding met IoT Hub

### <a name="cause-2"></a>Oorzaak 2

IoT Hub kan de auth-header, regel of sleutel niet verifiëren.

## <a name="solution"></a>Oplossing

### <a name="solution-1"></a>Oplossing 1

Er is geen actie nodig als u IoT SDK gebruikt voor verbinding met de verbindingstekenreeks van het apparaat. IoT SDK regenereert het nieuwe token om opnieuw verbinding te maken op de vervaldatum van SAS-token. 

Als het volume van fouten een punt van zorg is, schakelt u over naar de C SDK, die het SAS-token vóór het verstrijken vernieuwt. Bovendien kan het SAS-token voor AMQP worden vernieuwd zonder verbinding.

### <a name="solution-2"></a>Oplossing 2

In het algemeen moet de gepresenteerde foutmelding uitleggen hoe de fout moet worden opgelost. Als u om de een of andere reden geen toegang hebt tot de foutberichten, moet u het als:

- Het SAS- of ander beveiligingstoken dat u gebruikt, is niet verlopen. 
- De autorisatiereferentie is goed gevormd voor het protocol dat u gebruikt. Zie [IoT Hub-toegangscontrole](iot-hub-devguide-security.md)voor meer informatie.
- De gebruikte autorisatieregel heeft de machtiging voor de gevraagde bewerking.

## <a name="next-steps"></a>Volgende stappen

Om het authenticeren naar IoT Hub eenvoudiger te maken, raden we u aan [Azure IoT SDKs te](iot-hub-devguide-sdks.md)gebruiken.