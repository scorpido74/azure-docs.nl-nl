---
title: Wat is OPC Vault - Azure | Microsoft Documenten
description: Dit artikel geeft een overzicht van OPC Vault. Het kan de levenscyclus van certificaten configureren, registreren en beheren voor OPC UA-toepassingen in de cloud.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 66a322d4f60d9553a68207136ae609c1f9b50dbc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826205"
---
# <a name="what-is-opc-vault"></a>Wat is OPC Vault?

OPC Vault is een microservice die de levenscyclus van certificaten voor OPC UA-server- en clienttoepassingen in de cloud kan configureren, registreren en beheren. In dit artikel worden de eenvoudige use cases van de OPC Vault beschreven.

## <a name="certificate-management"></a>Certificaatbeheer

Een productiebedrijf moet bijvoorbeeld zijn OPC UA-servermachine aansluiten op hun nieuw gebouwde clienttoepassing. Wanneer de fabrikant de eerste toegang tot de servermachine maakt, wordt er onmiddellijk een foutmelding weergegeven op de OPC UA-servertoepassing om aan te geven dat de clienttoepassing niet veilig is. Dit mechanisme is gebouwd in de OPC UA-servermachine om ongeautoriseerde toegang tot toepassingen te voorkomen, wat vicieuze hacking op de werkvloer voorkomt.

## <a name="application-security-management"></a>Beheer van toepassingsbeveiliging
Een beveiligingsprofessional maakt gebruik van OPC Vault-microservice om de OPC UA-server eenvoudig in staat te stellen met elke clienttoepassing te communiceren, omdat OPC Vault alle functies heeft voor certificaatregister, opslag en levenscyclusbeheer. Nu de OPC UA-server veilig is verbonden, kan deze communiceren met de nieuw gebouwde clienttoepassing

## <a name="the-complete-opc-vault-architecture"></a>De complete OPC Vault-architectuur
Het volgende diagram illustreert de volledige OPC Vault-architectuur.

![OPC Vault-architectuur](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>Volgende stappen

Nu u meer te weten bent gekomen over OPC Vault en het gebruik ervan, is hier de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [OPC Vault-architectuur](overview-opc-vault-architecture.md)
