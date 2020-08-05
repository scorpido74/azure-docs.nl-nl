---
title: Integratie van Notification Hubs met App Service Mobile Apps
description: Meer informatie over hoe Azure Notification Hubs werken met Azure App Service Mobile Apps.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 647c5f40c7b02b8d9b488ce0812f27c0c0dde1a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87022137"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integratie met App Service Mobile Apps

Voor een naadloze werking van alle Azure-services biedt [App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) ingebouwde ondersteuning voor pushmeldingen via Notification Hubs. Met [App Service - Mobiele apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) kunnen ontwikkelaars van ondernemingen en systeemintegrators gebruikmaken van een zeer schaalbaar, algemeen beschikbaar ontwikkelplatform voor mobiele toepassingen, dat uitgebreide mogelijkheden biedt voor ontwikkelaars van mobiele apps.

Mobiele apps van ontwikkelaars kunnen gebruikmaken van Notification Hubs in de volgende werkstroom:

1. PNS-ingang van het apparaat ophalen
2. Apparaat registreren bij Notification Hubs via de handige registratie-API uit de Client-SDK van Mobile Apps

    > [!NOTE]
    > Om beveiligingsreden worden alle tags op registraties door mobiele apps verwijderd. Gebruik Notification Hubs om vanuit de back-end rechtstreeks tags aan apparaten koppelen.

3. Meldingen verzenden vanuit de back-end van uw app met Notification Hubs

Hier zijn een aantal voordelen voor ontwikkelaars:

- **Client-SDK's van Mobile Apps**: deze SDK's voor meerdere platforms bieden eenvoudige API's voor de registratie en het contact met de meldingshub die automatisch aan de mobiele app is gekoppeld. Ontwikkelaars hoeven niet op zoek naar de aanmeldgegevens voor Notification Hubs of met een extra service te werken.
  - *Pushen naar gebruiker*: De SDK's coderen een apparaat automatisch met de geverifieerde gebruikers-id van de mobiele app om het verzenden van pushberichten mogelijk te maken.
  - *Pushen naar apparaat*: de SDK's gebruiken de installatie-id van een mobiele app automatisch als GUID om te registreren bij Notification Hubs. Hierdoor is het niet meer nodig om meerdere service-GUID's bij te houden.
- **Installatiemodel**: mobiele apps werken met het meest recente pushmodel van Notification Hubs. Hierdoor zijn alle pusheigenschappen beschikbaar die aan een apparaat zijn gekoppeld in een JSON-installatie, die aansluit op de Push Notification Services en eenvoudig te gebruiken is.
- **Flexibiliteit**: Ontwikkelaars kunnen altijd beslissen om rechtstreeks met Notification Hubs te werken, zelfs na integratie.
- **Geïntegreerde ervaring in [Azure Portal](https://portal.azure.com)** : de pushfunctie in mobiele apps is zeer visueel en ontwikkelaars kunnen eenvoudig via mobiele apps met de bijbehorende meldingshub werken.
