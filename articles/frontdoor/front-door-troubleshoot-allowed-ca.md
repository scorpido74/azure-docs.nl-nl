---
title: Ca toegestaan voor het inschakelen van aangepaste HTTPS op Azure Front Door
description: Als u uw eigen certificaat gebruikt om HTTPS in te schakelen op een aangepast domein, moet u een toegestane certificaatautoriteit (CA) gebruiken om het te maken.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: 611f5730afed4c3a84b81d6acfd33b633c532bbc
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874667"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Certificaatautoriteiten toestaan voor het inschakelen van aangepaste HTTPS op Azure Front Door

Voor een aangepast Azure Front Door-domein moet u, wanneer u [de HTTPS-functie inschakelt met behulp van uw eigen certificaat,](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)een toegestane certificaatautoriteit (CA) gebruiken om uw TLS/SSL-certificaat te maken. Als u anders een niet-toegestane CA of een zelfondertekend certificaat gebruikt, wordt uw aanvraag afgewezen.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
