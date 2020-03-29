---
title: TLS-updates voor meldingenhubs
description: Meer informatie over ondersteuning voor TLS in Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/30/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87309e20efd9d6f8bd1a659451e5a603e6b95bc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908525"
---
# <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Om een hoger beveiligingsniveau te garanderen, schakelt Notification Hubs de ondersteuning voor TLS-versies 1.0 en 1.1 op 30 april 2020 uit. Deze oudere protocollen leveren zwakke cryptografie, en zijn kwetsbaar voor BEAST en POODLE aanvallen. Toepassingen die worden geïmplementeerd op apparaten met Android-versie 5 of hoger, of iOS-versie 5 of meer, worden niet beïnvloed door deze wijziging, aangezien deze besturingssystemen TLS 1.2 ondersteunen en de client en server onderhandelen over de hoogste wederzijds ondersteunde versie van de protocol bij verbinding.

We raden u aan al uw toepassingen te controleren die Azure Notification Hubs gebruiken, om ervoor te zorgen dat ze de meest toepasselijke bibliotheken en TLS-stacks gebruiken die TLS 1.2 ondersteunen.

## <a name="update-apps"></a>Apps bijwerken

U ervoor zorgen dat uw iOS-apps TLS 1.2 gebruiken met de netwerkbeveiligingsfunctie van Apple, de zogenaamde App Transport Security (ATS). ATS kan niet worden gebruikt voor SDK's ouder dan iOS 9.0 of macOS 10.11, en je er verder over lezen uit [de documentatie van Apple.](https://developer.apple.com/documentation/security/preventing_insecure_network_connections)

Voor Android-toepassingen met SSLSocket-exemplaren stelt u ingeschakelde protocollen in op elke SSLSocket-instantie zoals aangegeven in [setEnabledProtocols.](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D))

De tabel op de ondersteuningspagina [voor compatibiliteit van TLS-protocol](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) helpt besturingssystemen in kaart te brengen met compatibele TLS-versies.

Zie voor meer informatie het overzicht van de [ondersteuning voor TLS-protocollen op Windows.](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows)

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van meldingshubs](notification-hubs-push-notification-overview.md)