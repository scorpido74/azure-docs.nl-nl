---
title: Voorbeeld van API-beheerbeleid - Filteren op IP-adres bij gebruik van Application Gateway
titleSuffix: Azure API Management
description: Voorbeeld van azure API-beheerbeleid - toont aan hoe u filteren op het IP-adres van de aanvraag wanneer u een toepassingsgateway gebruikt.
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 45e16c9aa9e4b04e7225320951e9f839fae75ba3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942475"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Filteren op aanvraag IP-adres bij gebruik van een toepassingsgateway

In dit artikel wordt een voorbeeld van een Azure API-beheerbeleidsvoorbeeld weergegeven waarin wordt uitgelegd hoe filter op het eis-IP-adres wordt gefilterd wanneer de API-beheerinstantie wordt geopend via een Application Gateway of een andere tussenpersoon. Als u een beleidscode wilt instellen of bewerken, volgt u de stappen die zijn beschreven in [Een beleid instellen of bewerken](../set-edit-policies.md). Zie [Beleidsvoorbeelden](../policy-samples.md)voor andere voorbeelden .

## <a name="policy"></a>Beleid

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Beleid voor toegangsbeperkingen](../api-management-access-restriction-policies.md)
+ [Beleidsvoorbeelden](../policy-samples.md)
