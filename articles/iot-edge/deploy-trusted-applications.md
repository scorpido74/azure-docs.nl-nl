---
title: Vertrouwde toepassingen als Azure IoT Edge modules
description: Gebruik de open enclave-SDK en API voor het schrijven van vertrouwde toepassingen en implementeer deze als IoT Edge modules voor vertrouwelijke computing
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: kgremban
ms.openlocfilehash: 3a43d7e3e406e4a248e0ac2c27fb74a2f1b036cf
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936854"
---
# <a name="confidential-computing-at-the-edge"></a>Vertrouwelijk Computing aan de rand

Azure IoT Edge ondersteunt vertrouwde toepassingen die worden uitgevoerd binnen beveiligde enclaves op het apparaat. Versleuteling biedt beveiliging voor gegevens tijdens de overdracht of in rust, maar enclaves bieden beveiliging voor gegevens en workloads die in gebruik zijn. IoT Edge ondersteunt open enclave als standaard voor het ontwikkelen van vertrouwde toepassingen.

Beveiliging is altijd een belang rijke rol van de Internet of Things (IoT), omdat vaak IoT-apparaten vaak op de wereld worden gebruikt in plaats van te worden beveiligd binnen een persoonlijke faciliteit. Deze bloot stelling vormt een risico voor manipulatie en vervalsing van apparaten omdat deze fysiek toegankelijk zijn voor ongeldige actors. IoT Edge-apparaten hebben nog steeds vertrouwen en integriteit nodig, omdat gevoelige werk belastingen aan de rand kunnen worden uitgevoerd. In tegens telling tot algemene Sens oren en actuators zijn deze intelligente apparaten mogelijk gevoelige werk belastingen die voorheen alleen worden uitgevoerd binnen beveiligde Cloud-of on-premises omgevingen.

Het [IOT Edge Security Manager](iot-edge-security-manager.md) behandelt een deel van de Challenge van het vertrouwelijke computer gebruik. De Security Manager gebruikt een HSM (Hardware Security module) om de identiteits-en doorlopende processen van een IoT Edge apparaat te beschermen.

Een ander aspect van vertrouwelijke computing is het beschermen van de gegevens die worden gebruikt aan de rand. Een *Trusted Execution Environment* (Tee) is een veilige, geïsoleerde omgeving op een processor en wordt ook wel een *enclave*genoemd. Een *vertrouwde toepassing* is een toepassing die wordt uitgevoerd in een enclave. Vanwege de aard van enclaves worden vertrouwde toepassingen beschermd tegen andere apps die worden uitgevoerd in de hoofd processor of in de TEE.

## <a name="trusted-applications-on-iot-edge"></a>Vertrouwde toepassingen op IoT Edge

Vertrouwde toepassingen worden tijdens de overdracht en op rest versleuteld en alleen ontsleuteld om te worden uitgevoerd binnen een vertrouwde uitvoerings omgeving. Deze standaard geldt voor vertrouwde toepassingen die als IoT Edge modules zijn geïmplementeerd.

De ontwikkelaar maakt de vertrouwde toepassing en verpakt deze als een IoT Edge module. De toepassing wordt versleuteld voordat deze naar het container register wordt gepusht. De toepassing blijft versleuteld gedurende het IoT Edge-implementatie proces totdat de module op het IoT Edge apparaat wordt gestart. Zodra de vertrouwde toepassing zich in de TEE van het apparaat bevindt, wordt deze ontsleuteld en kan de uitvoering worden uitgevoerd.

![Diagram: vertrouwde toepassingen worden versleuteld binnen IoT Edge modules tot ze zijn geïmplementeerd in de beveiligde enclave](./media/deploy-trusted-applications/trusted-applications-encrypted.png)

Vertrouwde toepassingen op IoT Edge zijn een logische uitbrei ding van [Azure vertrouwelijk computing](../confidential-computing/overview.md). Workloads die worden uitgevoerd binnen beveiligde enclaves in de Cloud, kunnen ook worden geïmplementeerd om te worden uitgevoerd binnen beveiligde enclaves aan de rand.

## <a name="open-enclave"></a>Open Enclave

De [Open ENCLAVE SDK](https://openenclave.io/sdk/) is een open-source project dat ontwikkel aars helpt bij het maken van vertrouwde toepassingen voor meerdere platforms en omgevingen. De open enclave SDK werkt in de vertrouwde uitvoerings omgeving van een apparaat, terwijl de open enclave-API fungeert als een interface tussen de TEE en de niet-TEE-verwerkings omgeving.

Open enclave ondersteunt meerdere hardwareplatforms. Voor de ondersteuning van IoT Edge voor enclaves is momenteel het open Portable-TEE-besturings systeem vereist (OP-TEE OS). Zie voor meer informatie [ENCLAVE SDK openen voor op-t-besturings systeem](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md).

De open enclave-opslag plaats bevat ook voor beelden om ontwikkel aars te helpen aan de slag te gaan. Kies een van de inleidende artikelen voor meer informatie:

* [Open enclave SDK-voor beelden bouwen op Linux](https://github.com/openenclave/openenclave/blob/master/samples/README_Linux.md)
* [Open enclave SDK-voor beelden bouwen op Windows](https://github.com/openenclave/openenclave/blob/master/samples/README_Windows.md)

## <a name="hardware"></a>Hardware

Momenteel is [TrustBox by Scalys](https://scalys.com/trustbox-industrial/) het enige apparaat dat wordt ondersteund door de fabrikant-service overeenkomsten voor het implementeren van vertrouwde toepassingen als IOT Edge modules. De TrustBox is gebaseerd op de TrustBox Edge en TrustBox EdgeXL-apparaten zijn beide vooraf geladen met de open enclave SDK en Azure IoT Edge.

Zie [aan de slag met open enclave voor de Scalys-TrustBox](https://aka.ms/scalys-trustbox-edge-get-started)voor meer informatie.

## <a name="develop-and-deploy"></a>Ontwikkelen en implementeren

Wanneer u klaar bent om uw vertrouwde toepassing te ontwikkelen en implementeren, kan de [micro soft open enclave](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave) -extensie voor Visual Studio code helpen. U kunt Linux of Windows gebruiken als uw ontwikkel computer om modules voor de TrustBox te ontwikkelen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het ontwikkelen van vertrouwde toepassingen als IoT Edge modules met de [Open enclave-extensie voor Visual Studio code](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension)
