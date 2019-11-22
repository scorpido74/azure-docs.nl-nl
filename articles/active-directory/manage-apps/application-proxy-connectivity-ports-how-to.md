---
title: Firewall poorten openen die zijn vereist voor een toepassings proxy-app
description: Ontdek wat poorten openen voor de Azure AD-toepassingsproxy correct te laten werken
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275556"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Het openen van de firewallpoorten die nodig zijn voor een toepassing Application Proxy

Een volledige lijst van de vereiste poorten en de functie van elke poort wilt bekijken, Zie de sectie vereisten van de [Application Proxy documentatie](application-proxy-add-on-premises-application.md). Houd er rekening mee dat Application Proxy alleen uitgaande poorten gebruikt.

U kunt ook controleren of alle vereiste poorten zijn geopend door het [test hulpprogramma voor connector poorten](https://aadap-portcheck.connectorporttest.msappproxy.net/) te openen vanaf uw on-premises netwerk. Meer een groen vinkje betekent meer flexibiliteit. 

## <a name="app-proxy-regions"></a>App-Proxy-regio 's

We werken op een manier om te laten u weten welke van deze regio's moet groen voor u. Op dit moment dient u ervoor dat ze allemaal zijn. VS-midden is ook vereist, ongeacht welke waarin u zich bevindt.

Als u wilt controleren of dat het hulpprogramma biedt u de juiste resultaten, moet u:

-   Open het hulpprogramma op een browser van de server waar u de Connector hebt geïnstalleerd.

-   Zorg ervoor dat alle proxy's of firewalls van toepassing zijn op uw Connector ook worden toegepast op deze pagina. U kunt dit doen in Internet Explorer door te gaan naar **instellingen** -&gt; **Internet opties** -&gt; **verbindingen** -&gt; **LAN-instellingen**. Op deze pagina ziet u het veld 'Met een Proxy-Server voor uw LAN'. Schakel dit selectievakje in en plaats het proxyadres in het veld "Adres".

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over Azure AD Application Proxy connectors](application-proxy-connectors.md)
