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
ms.openlocfilehash: cac6bc9895f2b8778f2b27cc6b1dff4d4b898ae7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471520"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Certificaatautoriteiten toestaan voor het inschakelen van aangepaste HTTPS op Azure Front Door

Voor een aangepast Azure Front Door-domein moet u, wanneer u [de HTTPS-functie inschakelt met behulp van uw eigen certificaat,](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)een toegestane certificaatautoriteit (CA) gebruiken om uw SSL-certificaat te maken. Als u anders een niet-toegestane CA of een zelfondertekend certificaat gebruikt, wordt uw aanvraag afgewezen.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
