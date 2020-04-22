---
title: Beveiligingsaanbevelingen voor Azure IoT | Microsoft Documenten
description: In dit artikel worden aanvullende stappen samengevat om de beveiliging in uw Azure IoT Hub-oplossing te garanderen.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom:
- security-recommendations
- amqp
- mqtt
ms.openlocfilehash: 5dd09988d37982c41b761688492bd2dc3642b2db
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81728984"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Beveiligingsaanbevelingen voor Azure Internet of Things (IoT)-implementatie

Dit artikel bevat beveiligingsaanbevelingen voor IoT. Door deze aanbevelingen uit te voeren, u voldoen aan uw beveiligingsverplichtingen zoals beschreven in ons gedeelde verantwoordelijkheidsmodel. Lees [Gedeelde verantwoordelijkheden voor cloud computing voor](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)meer informatie over wat Microsoft doet om de verantwoordelijkheden van serviceproviders te vervullen.

Sommige aanbevelingen in dit artikel kunnen automatisch worden gecontroleerd door Azure Security Center. Azure Security Center is de eerste verdedigingslinie bij het beveiligen van uw resources in Azure. Het analyseert periodiek de beveiligingsstatus van uw Azure-bronnen om potentiële beveiligingsproblemen te identificeren. Het geeft u vervolgens aanbevelingen over hoe ze aan te pakken.

- Zie [Beveiligingsaanbevelingen in Azure Security Center](../security-center/security-center-recommendations.md)voor meer informatie over aanbevelingen voor Azure Security Center.
- Zie Wat is Azure Security Center voor informatie over Azure Security [Center?](../security-center/security-center-intro.md)

## <a name="general"></a>Algemeen

| Aanbeveling | Opmerkingen | Ondersteund door ASC |
|-|----|--|
| Blijf up-to-date | Gebruik de nieuwste versies van ondersteunde platforms, programmeertalen, protocollen en frameworks. | - |
| Verificatiesleutels veilig houden | Houd de apparaat-id's en hun verificatiesleutels fysiek veilig na de implementatie. Dit voorkomt dat een kwaadaardig apparaat zich voordoet als een geregistreerd apparaat. | - |
| Gebruik apparaat SDKs waar mogelijk | Device SDKs implementeren verschillende beveiligingsfuncties, zoals versleuteling, authenticatie, enzovoort, om u te helpen bij het ontwikkelen van een robuuste en veilige apparaattoepassing. Zie [Azure IoT Hub SDKs begrijpen en gebruiken](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) voor meer informatie. | - |

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer 

| Aanbeveling | Opmerkingen | Ondersteund door ASC |
|-|----|--|
| Toegangscontrole voor de hub definiëren | [Het type toegang dat](iot-security-deployment.md#securing-the-cloud) elk onderdeel in uw IoT Hub-oplossing heeft, begrijpen en definiëren, op basis van de functionaliteit. De toegestane machtigingen zijn *Register Read*, *RegistryReadWrite,* *ServiceConnect*en *DeviceConnect*. Standaardbeleid [voor gedeelde toegang in uw IoT-hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) kan ook helpen bij het definiëren van de machtigingen voor elk onderdeel op basis van de rol ervan. | - |
| Toegangscontrole definiëren voor backendservices | Gegevens die door uw IoT Hub-oplossing worden ingenomen, kunnen worden verbruikt door andere Azure-services, zoals [Cosmos DB,](https://docs.microsoft.com/azure/cosmos-db/) [Stream Analytics,](https://docs.microsoft.com/azure/stream-analytics/) [App Service,](https://docs.microsoft.com/azure/app-service/) [Logic Apps](https://docs.microsoft.com/azure/logic-apps/)en [Blob-opslag.](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) Zorg ervoor dat u de juiste toegangsmachtigingen begrijpt en toestaat zoals gedocumenteerd voor deze services. | - |

## <a name="data-protection"></a>Gegevensbeveiliging

| Aanbeveling | Opmerkingen | Ondersteund door ASC |
|-|----|--|
| Verificatie van beveiligde apparaten | Zorg voor veilige communicatie tussen uw apparaten en uw IoT-hub, door [een unieke identiteitssleutel of beveiligingstoken te](iot-security-deployment.md#iot-hub-security-tokens)gebruiken, of een [X.509-certificaat op het apparaat](iot-security-deployment.md#x509-certificate-based-device-authentication) voor elk apparaat. Gebruik de juiste methode om beveiligingstokens te [gebruiken op basis van het gekozen protocol (MQTT, AMQP of HTTPS)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). | - |
| Beveiligde apparaatcommunicatie | IoT Hub beveiligt de verbinding met de apparaten met behulp van Transport Layer Security (TLS) standaard, ondersteuning van versies 1.2 en 1.0. Gebruik [TLS 1.2](https://tools.ietf.org/html/rfc5246) om maximale beveiliging te garanderen. | - |
| Beveiligde servicecommunicatie | IoT Hub biedt eindpunten om verbinding te maken met backendservices zoals [Azure Storage](/azure/storage/) of [Event Hubs](/azure/event-hubs) met alleen het TLS-protocol en er wordt geen eindpunt weergegeven op een niet-versleuteld kanaal. Zodra deze gegevens deze backendservices voor opslag of analyse bereiken, moet u de juiste beveiligings- en versleutelingsmethoden voor die service gebruiken en gevoelige informatie aan de backend beschermen. | - |

## <a name="networking"></a>Netwerken

| Aanbeveling | Opmerkingen | Ondersteund door ASC |
|-|----|--|
| Toegang tot uw apparaten beveiligen | Houd hardwarepoorten in uw apparaten tot een absoluut minimum om ongewenste toegang te voorkomen. Bovendien, bouwen mechanismen om te voorkomen of fysieke manipulatie van het apparaat te detecteren. Lees aanbevolen procedures voor [IoT-beveiliging](iot-security-best-practices.md) voor meer informatie. | - |
| Veilige hardware bouwen | Neem beveiligingsfuncties op, zoals versleutelde opslag of Trusted Platform Module (TPM), om apparaten en infrastructuur veiliger te houden. Houd het besturingssysteem en stuurprogramma's van het apparaat geüpgraded naar de nieuwste versies en installeer antivirus- en antimalwaremogelijkheden als de ruimte het toelaat. Lees [de IoT-beveiligingsarchitectuur](iot-security-architecture.md) om te begrijpen hoe dit verschillende beveiligingsbedreigingen kan beperken. | - |

## <a name="monitoring"></a>Bewaking

| Aanbeveling | Opmerkingen | Ondersteund door ASC |
|-|----|--|
| Ongeautoriseerde toegang tot uw apparaten controleren |  Gebruik de logboekfunctie van uw apparaat om inbreuken op de beveiliging of fysieke manipulatie van het apparaat of de poorten ervan te controleren. | - |
| Uw IoT-oplossing vanuit de cloud bewaken | Controleer de algehele status van uw IoT Hub-oplossing met behulp van de [statistieken in Azure Monitor.](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) | - |
| Diagnose instellen | Houd uw activiteiten nauwlettend in de gaten door gebeurtenissen in uw oplossing te registreren en vervolgens de diagnostische logboeken naar Azure Monitor te verzenden om inzicht te krijgen in de prestaties. Lees [Problemen met monitoren en diagnosticeren in uw IoT-hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) voor meer informatie. | - |

## <a name="next-steps"></a>Volgende stappen

Voor geavanceerde scenario's met Azure IoT moet u mogelijk rekening houden met aanvullende beveiligingsvereisten. Zie [IoT-beveiligingsarchitectuur](iot-security-architecture.md) voor meer richtlijnen.

