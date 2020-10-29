---
title: Problemen met Azure IoT Hub-fout 401003 IoTHubUnauthorized
description: Meer informatie over het oplossen van fout 401003 IoTHubUnauthorized
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: af057750e81086bf691b87057da97af3de19cd3b
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92909638"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

In dit artikel worden de oorzaken en oplossingen voor **401003 IoTHubUnauthorized** -fouten beschreven.

## <a name="symptoms"></a>Symptomen

### <a name="symptom-1"></a>Symptoom 1

In Logboeken ziet u een patroon van apparaten waarmee de verbinding met **401003 IoTHubUnauthorized** wordt verbroken, gevolgd door **404104 DeviceConnectionClosedRemotely** en binnenkort verbinding maken.

### <a name="symptom-2"></a>Symptoom 2

Aanvragen voor IoT Hub mislukken met een van de volgende fout berichten:

* Autorisatie header ontbreekt
* IotHub ' \* ' bevat het opgegeven apparaat ' ' niet \*
* Autorisatie regel ' \* ' staat geen toegang toe voor ' \* '
* Verificatie is mislukt voor dit apparaat, Token of certificaat vernieuwen en opnieuw verbinding maken
* Vinger afdruk komt niet overeen met configuratie: vinger afdruk: SHA1Hash = \* , SHA2Hash = \* ; Configuratie: PrimaryThumbprint = \* , SecondaryThumbprint =\*

## <a name="cause"></a>Oorzaak

### <a name="cause-1"></a>Oorzaak 1

Voor MQTT zijn sommige Sdk's afhankelijk van IoT Hub om de verbinding te verbreken wanneer het SAS-token verloopt om te weten wanneer het moet worden vernieuwd. Dus 

1. Het SAS-token verloopt
1. IoT Hub het verlopen van het apparaat en het verbreken van de verbinding met **401003 IoTHubUnauthorized**
1. Het apparaat is de verbinding met **404104 DeviceConnectionClosedRemotely** voltooid
1. De IoT SDK genereert een nieuw SAS-token
1. Er wordt opnieuw verbinding gemaakt met het apparaat met IoT Hub

### <a name="cause-2"></a>Oorzaak 2

IoT Hub kan de Authentication Header, regel of sleutel niet verifiëren. Dit kan worden veroorzaakt door een van de redenen die in de symptomen worden genoemd.

## <a name="solution"></a>Oplossing

### <a name="solution-1"></a>Oplossing 1

Er is geen actie nodig als u IoT SDK gebruikt voor verbinding met behulp van het apparaat connection string. IoT SDK genereert het nieuwe token opnieuw om opnieuw verbinding te maken met de verval datum van SAS-tokens. 

Als het fouten volume een probleem is, gaat u naar de C-SDK, waarmee het SAS-token wordt vernieuwd voordat het verloopt. Daarnaast kan voor AMQP het SAS-token worden vernieuwd zonder de verbinding te verbreken.

### <a name="solution-2"></a>Oplossing 2

In het algemeen wordt het fout bericht weer gegeven waarin wordt uitgelegd hoe de fout moet worden opgelost. Als u om een of andere reden geen toegang hebt tot de details van het fout bericht, controleert u het volgende:

- De SAS of een ander beveiligings token dat u gebruikt, is niet verlopen.
- Voor X. 509-certificaat authenticatie is het certificaat van het apparaat of het CA-certificaat dat is gekoppeld aan het apparaat, niet verlopen. Zie [x. 509-beveiliging instellen in uw Azure IOT hub](iot-hub-security-x509-get-started.md)voor meer informatie over het registreren van x. 509 CA-certificaten met IOT hub.
- Voor X. 509 certificaat vingerafdruk verificatie wordt de vinger afdruk van het certificaat van het apparaat geregistreerd bij IoT Hub.
- De autorisatie referentie is goed gevormd voor het protocol dat u gebruikt. Zie [toegang tot IOT hub beheren](iot-hub-devguide-security.md)voor meer informatie.
- De autorisatie regel die wordt gebruikt, heeft de machtiging voor de aangevraagde bewerking.

## <a name="next-steps"></a>Volgende stappen

- Als u het verifiëren van IoT Hub eenvoudiger wilt maken, raden wij u aan [Azure IOT sdk's](iot-hub-devguide-sdks.md)te gebruiken.
- Zie [toegang tot IOT hub beheren](iot-hub-devguide-security.md)voor meer informatie over verificatie met IOT hub.
