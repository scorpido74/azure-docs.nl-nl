---
title: Wat is OPC Twin - Azure | Microsoft Documenten
description: Dit artikel geeft een overzicht van OPC Twin. OPC Twin biedt detectie, registratie en afstandsbediening van industriële apparaten via REST API's.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 91448f55f0ebb88ba6c685b960ece9d91cb98e25
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826226"
---
# <a name="what-is-opc-twin"></a>Wat is OPC Twin?

OPC Twin bestaat uit microservices die Azure IoT Edge en IoT Hub gebruiken om de cloud en het fabrieksnetwerk met elkaar te verbinden. OPC Twin biedt detectie, registratie en afstandsbediening van industriële apparaten via REST API's. OPC Twin vereist geen OPC Unified Architecture (OPC UA) SDK, programmeert taalagnostisch en kan worden opgenomen in een serverloze workflow. Dit artikel beschrijft verschillende OPC Twin use cases.

## <a name="discovery-and-control"></a>Ontdekking en controle
U OPC Twin gebruiken voor eenvoudige voor ontdekking en registratie.

### <a name="simple-discovery-and-registration"></a>Eenvoudige ontdekking en registratie
MET OPC Twin kunnen fabrieksoperators het fabrieksnetwerk scannen, zodat OPC UA-servers kunnen worden ontdekt en geregistreerd. Als alternatief kunnen fabrieksoperators ook handmatig OPC UA-apparaten registreren met behulp van een bekende detectie-URL. Als u bijvoorbeeld verbinding wilt maken met alle OPC UA-apparaten nadat de IoT Edge-gateway met een OPC Twin-module op de fabrieksvloer is geïnstalleerd, kan de fabrieksoperator op afstand een scan van het netwerk activeren en alle OPC UA-servers visueel bekijken. 

### <a name="simple-control"></a>Eenvoudige bediening
MET OPC Twin kunnen fabrieksoperators automatisch of handmatig reageren op gebeurtenissen en hun fabrieksvloermachines automatisch of handmatig opnieuw configureren. OPC Twin biedt REST API's om services op de OPC UA-server aan te roepen, door de adresruimte te bladeren en variabelen te lezen/schrijven en methoden uit te voeren. Een ketel gebruikt bijvoorbeeld temperatuur-KPI om de productielijn te regelen. De temperatuursensor publiceert de wijziging in gegevens met OPC Publisher. De fabrieksexploitant ontvangt de waarschuwing dat de temperatuur de drempel heeft bereikt. De productielijn koelt automatisch af via OPC Twin. De fabrieksexploitant wordt op de hoogte gebracht van de afkoeling.

## <a name="authentication"></a>Authentication
U OPC Twin gebruiken voor eenvoudige verificatie en voor een eenvoudige ontwikkelaarservaring.

### <a name="simple-authentication"></a>Eenvoudige verificatie 
OPC Twin maakt gebruik van azure active directory (AAD)-gebaseerde verificatie en controle van eind tot eind. Opc Twin maakt het bijvoorbeeld mogelijk om de toepassing bovenop OPC Twin te bouwen om te bepalen wat een operator op een machine heeft uitgevoerd. Aan de machinekant is het via OPC UA auditing. Aan de cloudkant is dit het door een onveranderlijke clientauditlog en AAD-verificatie op te slaan op de REST API.

### <a name="simple-developer-experience"></a>Eenvoudige ontwikkelaarservaring 
OPC Twin kan worden gebruikt met toepassingen geschreven in elke programmeertaal via REST API's. Aangezien ontwikkelaars een OPC UA-client integreren in een oplossing, is kennis van de OPC UA SDK niet nodig. OPC Twin kan naadloos integreren in stateloze, serverloze architecturen. Een webontwikkelaar met volledige stack die bijvoorbeeld een toepassing voor een alarm- en gebeurtenisdashboard ontwikkelt, kan de logica schrijven om te reageren op gebeurtenissen in JavaScript of TypeScript met OPC Twin zonder medeweten van C, C#, of de volledige OPC UA-stackimplementatie. 

## <a name="next-steps"></a>Volgende stappen

Nu dat je hebt geleerd over OPC Twin en het gebruik ervan, hier is de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Wat is OPC Vault?](overview-opc-vault.md)
