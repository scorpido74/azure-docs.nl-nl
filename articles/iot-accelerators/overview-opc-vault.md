---
title: Wat is OPC kluis-Azure | Microsoft Docs
description: Dit artikel bevat een overzicht van de OPC-kluis. Het kan de levens duur van de certificaten voor OPC UA-toepassingen in de Cloud configureren, registreren en beheren.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 66a322d4f60d9553a68207136ae609c1f9b50dbc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826205"
---
# <a name="what-is-opc-vault"></a>Wat is OPC kluis?

OPC-kluis is een micro service die de levens cyclus van de certificaten voor OPC UA-server en client toepassingen in de cloud kan configureren, registreren en beheren. In dit artikel worden de eenvoudige gebruiks voorbeelden van de OPC-kluis beschreven.

## <a name="certificate-management"></a>Certificaatbeheer

Zo moet een productie bedrijf de OPC UA-server machine verbinden met de nieuwe client toepassing. Wanneer de fabrikant de eerste toegang tot de Server computer maakt, wordt er onmiddellijk een fout bericht weer gegeven op de OPC UA-server toepassing om aan te geven dat de client toepassing niet beveiligd is. Dit mechanisme is gebaseerd op de OPC UA-server machine om te voor komen dat onbevoegde toegang tot toepassingen wordt gekraakt, waardoor vicieuze niet op de werk vloer kan worden gehackt.

## <a name="application-security-management"></a>Beheer van toepassings beveiliging
Een beveiligings Professional gebruikt OPC-kluis micro service om de OPC UA-server eenvoudig in te scha kelen voor communicatie met elke client toepassing, omdat OPC-kluis beschikt over alle functies voor certificaat register, opslag en levenscyclus beheer. De OPC UA-server is nu veilig verbonden en kan communiceren met de zojuist opgebouwde client toepassing

## <a name="the-complete-opc-vault-architecture"></a>De volledige OPC-kluis architectuur
In het volgende diagram ziet u de volledige OPC-kluis architectuur.

![OPC-kluis architectuur](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over OPC-kluis en het gebruik ervan, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [OPC-kluis architectuur](overview-opc-vault-architecture.md)
