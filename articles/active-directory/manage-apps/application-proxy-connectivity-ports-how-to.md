---
title: Firewall poorten openen die zijn vereist voor een toepassings proxy-app
description: Ontdek welke poorten er moeten worden geopend om de Azure-AD-toepassingsproxy correct te laten werken
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 192e721e25612019649fed86b44135fb6bfef89c
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84764703"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>De firewall poorten openen die zijn vereist voor een toepassings proxy toepassing

Zie de sectie vereisten van de documentatie van de [toepassings proxy](application-proxy-add-on-premises-application.md)voor een volledige lijst met de vereiste poorten en de functie van elke poort. Houd er rekening mee dat toepassings proxy alleen uitgaande poorten gebruikt.

U kunt ook controleren of alle vereiste poorten zijn geopend door het [test hulpprogramma voor connector poorten](https://aadap-portcheck.connectorporttest.msappproxy.net/) te openen vanaf uw on-premises netwerk. Meer groene vinkjes betekent een grotere tolerantie. 

## <a name="app-proxy-regions"></a>App-proxy regio's

We werken aan een manier om u te laten weten welke van deze regio's voor u groen moet zijn. Zorg er ook voor dat deze allemaal zijn. Central US is ook vereist, ongeacht in welke regio u zich bevindt.

Zorg ervoor dat het hulp programma de juiste resultaten krijgt door het volgende te doen:

-   Open het hulp programma op een browser van de server waarop u de connector hebt geïnstalleerd.

-   Zorg ervoor dat alle proxy's of firewalls die van toepassing zijn op uw connector ook worden toegepast op deze pagina. Dit kan in Internet Explorer worden gedaan door te gaan naar **instellingen**  - &gt; **Internet opties**  - &gt; **verbindingen**  - &gt; **LAN-instellingen**. Op deze pagina ziet u het veld "een proxy server gebruiken voor uw LAN". Schakel dit selectie vakje in en plaats het proxy adres in het veld adres.

## <a name="next-steps"></a>Volgende stappen
[Azure AD-toepassingsproxy-connectors begrijpen](application-proxy-connectors.md)
