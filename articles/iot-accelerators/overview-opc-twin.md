---
title: 'Wat is OPC: twee Azure | Microsoft Docs'
description: In dit artikel vindt u een overzicht van OPC, twee. OPC twee biedt detectie, registratie en extern beheer van industriële apparaten via REST-Api's.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 91448f55f0ebb88ba6c685b960ece9d91cb98e25
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73826226"
---
# <a name="what-is-opc-twin"></a>Wat is OPC twee?

OPC twee bestaat uit micro services die gebruikmaken van Azure IoT Edge en IoT Hub om verbinding te maken met de Cloud en het Factory-netwerk. OPC twee biedt detectie, registratie en extern beheer van industriële apparaten via REST-Api's. OPC dubbele vereist geen OPC-UA-SDK (Unified Architecture), is programmeer taal neutraal en kan worden opgenomen in een serverloze werk stroom. In dit artikel worden verschillende OPC-twee-use cases beschreven.

## <a name="discovery-and-control"></a>Detectie en beheer
U kunt OPC-twee gebruiken voor eenvoudig detectie en registratie.

### <a name="simple-discovery-and-registration"></a>Eenvoudige detectie en registratie
OPC twee biedt fabrieks operators de mogelijkheid om het fabrieks netwerk te scannen, zodat OPC UA-servers kunnen worden gedetecteerd en geregistreerd. Als alternatief kunnen Factory-Opera tors ook niet hand matig OPC UA-apparaten registreren met behulp van een bekende detectie-URL. Als u bijvoorbeeld verbinding wilt maken met alle OPC UA-apparaten nadat de IoT Edge gateway met een OPC-dubbele module op de fabriek is geïnstalleerd, kan de fabrieks operator een scan van het netwerk op afstand activeren en de OPC UA-servers visueel weer geven. 

### <a name="simple-control"></a>Eenvoudig beheer
Met OPC dubbele kunnen Factory-Opera tors reageren op gebeurtenissen en hun fabrieks machines automatisch of hand matig opnieuw configureren vanuit de Cloud. OPC twee biedt REST Api's voor het aanroepen van services op de OPC UA-server, blader door de adres ruimte en lees/schrijf variabelen en voer methoden uit. Een ketel maakt bijvoorbeeld gebruik van een temperatuur KPI om de productie lijn te beheren. De temperatuur sensor publiceert de wijziging in gegevens met behulp van de OPC-Uitgever. De fabrieks operator ontvangt de waarschuwing dat de Tempe ratuur de drempel waarde heeft bereikt. De productie lijn wordt automatisch uitgelicht via OPC dubbele. De fabrieks operator wordt op de hoogte gesteld van de afkoelen.

## <a name="authentication"></a>Verificatie
U kunt OPC-twee gebruiken om eenvoudig te authenticeren voor verificatie en voor een eenvoudige ervaring voor ontwikkel aars.

### <a name="simple-authentication"></a>Eenvoudige verificatie 
OPC twee maakt gebruik van Azure Active Directory (AAD)-verificatie en controles van end-to-end. Zo kan OPC twee bijvoorbeeld de toepassing boven op OPC twee maken om te bepalen wat een operator op een computer heeft uitgevoerd. Aan de kant van de computer wordt gepaard met OPC UA auditing. Aan de Cloud zijde wordt een onveranderlijk client audit logboek en AAD-verificatie opgeslagen op de REST API.

### <a name="simple-developer-experience"></a>Eenvoudige ervaring voor ontwikkel aars 
OPC dubbele kan worden gebruikt met toepassingen die zijn geschreven in een programmeer taal via REST-Api's. Omdat ontwikkel aars een OPC UA-client integreren in een oplossing, is kennis van de OPC UA SDK niet nodig. OPC twee kan naadloos worden geïntegreerd in stateless, serverloze architecturen. Zo kan een volledige stack webontwikkelaar die een toepassing ontwikkelt voor een alarm en gebeurtenis dashboard de logica schrijven om te reageren op gebeurtenissen in Java script of type script met behulp van OPC, zonder de kennis van C, C# of de volledige OPC UA-stack implementatie. 

## <a name="next-steps"></a>Volgende stappen

Nu u over OPC en het gebruik ervan hebt geleerd, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Wat is OPC Vault?](overview-opc-vault.md)
