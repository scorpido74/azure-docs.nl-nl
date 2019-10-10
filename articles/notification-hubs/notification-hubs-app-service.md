---
title: Integratie met App Service Mobile Apps
description: Meer informatie over de werking van Azure Notification Hubs met Azure App Service Mobile Apps.
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
ms.openlocfilehash: 8c3bc90b282092ede0e924d32b50b67e5c4e22b8
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244508"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integratie met App Service Mobile Apps

Voor een naadloze werking van alle Azure-services biedt [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) ingebouwde ondersteuning voor pushmeldingen via Notification Hubs. Met [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) kunnen ontwikkelaars van ondernemingen en systeemintegrators gebruikmaken van een zeer schaalbaar, algemeen beschikbaar ontwikkelplatform voor mobiele toepassingen, dat uitgebreide mogelijkheden biedt voor ontwikkelaars van mobiele apps.

Visual Studio App Center levert vandaag nog een moderne mobiele oplossing voor ontwikkel aars. Het biedt ondersteuning voor end-to-end en geïntegreerde services voor de ontwikkeling van mobiele apps. Ontwikkel aars kunnen services **bouwen**, **testen** en **distribueren** om een continue integratie-en leverings pijplijn in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkel aars de status en het gebruik van hun app bewaken met behulp van de **analyse** -en **diagnose** Services en gebruikers benaderen met behulp van de **Push** service. Ontwikkel aars kunnen ook gebruikmaken van **auth** voor het verifiëren van hun gebruikers en **gegevens** service om app-gegevens in de Cloud op te slaan en te synchroniseren.

> [!NOTE]
> Als u Cloud Services wilt integreren in uw mobiele toepassing, meldt u zich aan bij [app Center](https://appcenter.ms/signup?utm_source=NotificationHubs&utm_medium=Azure&utm_campaign=docs) vandaag.

Mobiele apps van ontwikkelaars kunnen gebruikmaken van Notification Hubs in de volgende werkstroom:

1. PNS-ingang van het apparaat ophalen
2. Apparaat registreren bij Notification Hubs via de handige registratie-API uit de Client-SDK van Mobile Apps

    > [!NOTE]
    > Om beveiligingsreden worden alle tags op registraties door mobiele apps verwijderd. Gebruik Notification Hubs om vanuit de back-end rechtstreeks tags aan apparaten koppelen.

3. Meldingen verzenden vanuit de back-end van uw app met Notification Hubs

Hier zijn een aantal voordelen voor ontwikkelaars:

- **Client-SDK's van Mobile Apps**: deze SDK's voor meerdere platforms bieden eenvoudige API's voor de registratie en het contact met de meldingshub die automatisch aan de mobiele app is gekoppeld. Ontwikkelaars hoeven niet op zoek naar de aanmeldgegevens voor Notification Hubs of met een extra service te werken.
  - *Pushen naar gebruiker*: de SDK's coderen een apparaat automatisch met de geverifieerde gebruikers-id van de mobiele app om het verzenden van pushberichten mogelijk te maken.
  - *Pushen naar apparaat*: de SDK's gebruiken de installatie-id van een mobiele app automatisch als GUID om te registreren bij Notification Hubs. Hierdoor is het niet meer nodig om meerdere service-GUID's bij te houden.
- **Installatiemodel**: mobiele apps werken met het meest recente pushmodel van Notification Hubs. Hierdoor zijn alle pusheigenschappen beschikbaar die aan een apparaat zijn gekoppeld in een JSON-installatie, die aansluit op de Push Notification Services en eenvoudig te gebruiken is.
- **Flexibiliteit**: ontwikkelaars kunnen altijd beslissen om rechtstreeks met Notification Hubs te werken, zelfs na integratie.
- **Geïntegreerde ervaring in [Azure Portal](https://portal.azure.com)** : de pushfunctie in mobiele apps is zeer visueel en ontwikkelaars kunnen eenvoudig via mobiele apps met de bijbehorende meldingshub werken.
