---
title: De huidige IP-lijst POP ophalen voor Azure CDN| Microsoft Documenten
description: Meer informatie over het ophalen van de huidige POP-lijst.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 95b85aa11d99ddd48c90c8d9fa28789e79ee979f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299250"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>De huidige POP-IP-lijst voor Azure CDN ophalen

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>De huidige Verizon POP IP-lijst voor Azure CDN ophalen

U de REST API gebruiken om de set IP's voor pop-servers (Point of Presence) van Verizon op te halen. Deze POP-servers doen aanvragen voor oorsprongservers die zijn gekoppeld aan CDN-eindpunten (Azure Content Delivery Network) op een**Verizon-profiel (Azure CDN-standaard van Verizon** of **Azure CDN Premium van Verizon).** Houd er rekening mee dat deze set IP's verschilt van de IP's die een client zou zien bij het indienen van verzoeken aan de POP's. 

Zie [Randknooppunten - Lijst](https://docs.microsoft.com/rest/api/cdn/edgenodes/list)voor de syntaxis van de REST-API-bewerking voor het ophalen van de POP-lijst .

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>De huidige Microsoft POP IP-lijst voor Azure CDN ophalen

Als u uw toepassing wilt vergrendelen om alleen verkeer van Azure CDN van Microsoft te accepteren, moet u IP-ACL's instellen voor uw back-end. U ook de set geaccepteerde waarden beperken voor de koptekst 'X-Forwarded-Host' die door Azure CDN van Microsoft wordt verzonden. Deze stappen worden hieronder beschreven:

Configureer IP ACLing voor uw backends om verkeer van Azure CDN alleen te accepteren vanuit de backend IP-adresruimte van Microsoft en alleen de infrastructuurservices van Azure. 

* Azure CDN uit de IP-ruimte voor IPv4-backend van Microsoft: 147.243.0.0/16
* Azure CDN uit de IP-ruimte voor IPv6-backend van Microsoft: 2a01:111:2050::/44

IP-bereiken en servicetags voor Microsoft-services zijn [hier](https://www.microsoft.com/download/details.aspx?id=56519) te vinden


## <a name="typical-use-case"></a>Typische gebruiksscenario’s

Voor beveiligingsdoeleinden u deze IP-lijst gebruiken om af te dwingen dat aanvragen naar uw oorspronkelijke server alleen worden uitgevoerd vanaf een geldige Verizon POP. Als iemand bijvoorbeeld de hostnaam of het IP-adres voor de oorsprongsserver van een CDN-eindpunt heeft ontdekt, kan deze rechtstreeks aanvragen indienen bij de oorspronkelijke server, waardoor de schaal- en beveiligingsmogelijkheden van Azure CDN worden omzeild. Door de IP's in de geretourneerde lijst in te stellen als de enige toegestane IP's op een origin-server, kan dit scenario worden voorkomen. Om ervoor te zorgen dat u de nieuwste POP-lijst hebt, haalt u deze ten minste eenmaal per dag op. 

## <a name="next-steps"></a>Volgende stappen

Zie [Azure CDN REST API](https://docs.microsoft.com/rest/api/cdn/)voor informatie over de REST API.
