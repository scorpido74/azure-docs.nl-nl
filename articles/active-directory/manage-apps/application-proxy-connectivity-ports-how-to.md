---
title: Firewallpoorten openen die vereist zijn voor een app Voor proxy van toepassingen
description: Ontdek welke poorten u wilt openen voor de Azure AD-toepassingsproxy om correct te werken
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b676508c73ff0233526f19b865bb9e4f7b80f12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275556"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>De firewallpoorten openen die nodig zijn voor een toepassingsproxytoepassing

Zie het gedeelte vereisten van de documentatie van de [toepassingsproxy](application-proxy-add-on-premises-application.md)om een volledige lijst met vereiste poorten en de functie van elke poort te bekijken. Houd er rekening mee dat Application Proxy alleen uitgaande poorten gebruikt.

U ook controleren of u alle vereiste poorten hebt geopend door de [Connector Ports Test Tool](https://aadap-portcheck.connectorporttest.msappproxy.net/) te openen vanuit uw on-premises netwerk. Meer groene vinkjes betekent meer veerkracht. 

## <a name="app-proxy-regions"></a>Proxyregio's voor apps

We werken aan een manier om u te laten weten welke van deze regio's groen voor u moet zijn. Voor nu, zorg ervoor dat ze allemaal zijn. Centraal VS is ook vereist, ongeacht in welke regio u zich bevindt.

Om ervoor te zorgen dat de tool u de juiste resultaten geeft, moet u:

-   Open het hulpprogramma in een browser vanaf de server waar u de connector hebt geïnstalleerd.

-   Zorg ervoor dat alle proxy's of firewalls die van toepassing zijn op uw connector ook op deze pagina worden toegepast. Dit kan in Internet Explorer door naar **Connections**  - &gt; **Lan-instellingen**voor **instellingen**  - &gt; **internetopties**  - &gt; te gaan. Op deze pagina ziet u het veld "Gebruik een proxyserver voor uw LAN". Selecteer dit vak en plaats het proxyadres in het veld Adres.

## <a name="next-steps"></a>Volgende stappen
[Informatie over Azure AD-toepassingsproxyconnectors](application-proxy-connectors.md)
