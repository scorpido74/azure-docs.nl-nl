---
title: Toegestane CA voor het inschakelen van aangepaste HTTPS op Azure front-deur
description: Als u uw eigen certificaat gebruikt om HTTPS in te scha kelen op een Azure front deur 0custom-domein, moet u een toegestane certificerings instantie (CA) gebruiken om deze te maken.
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: duau
ms.openlocfilehash: 973df2505eefc2a46aa105b874f32b61fe6e8b36
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91269795"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Toegestane certificerings instanties voor het inschakelen van aangepaste HTTPS op Azure front-deur

Wanneer u [de https-functie inschakelt met behulp van uw eigen certificaat](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), moet u voor een aangepast Azure front deur-domein een toegestane certificerings instantie (CA) gebruiken om uw TLS/SSL-certificaat te maken. Anders wordt de aanvraag geweigerd als u een niet-toegestane CA of een zelfondertekend certificaat gebruikt.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
