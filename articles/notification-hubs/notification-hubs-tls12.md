---
title: Notification Hubs TLS-updates
description: Meer informatie over ondersteuning voor TLS in azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 4da96df50e961f4291029a37e883fdcf88c6c87f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885749"
---
# <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Notification Hubs schakelt de ondersteuning voor TLS-versies 1,0 en 1,1 op 31 mei 2020 (verlengd vanaf 30 april 2020) uit om een hoger beveiligings niveau te garanderen. Deze oudere protocollen leveren zwakke crypto grafie en zijn kwetsbaar voor BEAST-en POODLE-aanvallen. Toepassingen die zijn geïmplementeerd op apparaten met Android versie 5 of hoger of iOS-versie 5 of hoger, worden niet beïnvloed door deze wijziging omdat die besturings systemen TLS 1,2 ondersteunen en de client en server de meest wederzijds ondersteunde versie van het protocol bij de verbinding kunnen onderhandelen.

We raden u aan uw toepassingen die gebruikmaken van Azure Notification Hubs, te controleren om ervoor te zorgen dat ze de meest toepasselijke bibliotheken en TLS-stacks gebruiken die ondersteuning bieden voor TLS 1,2.

## <a name="update-apps"></a>Apps bijwerken

U kunt ervoor zorgen dat uw iOS-apps gebruikmaken van TLS 1,2 met behulp van de beveiligings functie van Apple-netwerk beveiliging (ATS). ATS kan niet worden gebruikt voor Sdk's die ouder zijn dan iOS 9,0 of macOS 10,11, en u kunt meer lezen over it in [de documentatie van Apple](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

Voor Android-toepassingen die gebruikmaken van SSLSocket-instanties, stelt u ingeschakelde protocollen in voor elk SSLSocket-exemplaar, zoals wordt aangegeven in [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

De tabel op de ondersteunings pagina voor [TLS-protocol compatibiliteit](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) helpt met het toewijzen van besturings systemen met compatibele TLS-versies.

Zie het overzicht van de [ondersteuning voor TLS-protocollen in Windows](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Notification Hubs](notification-hubs-push-notification-overview.md)