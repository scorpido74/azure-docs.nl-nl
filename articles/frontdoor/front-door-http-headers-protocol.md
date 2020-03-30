---
title: Protocolondersteuning voor HTTP-headers in Azure Front Door | Microsoft Documenten
description: In dit artikel worden HTTP-headerprotocollen beschreven die Door door ondersteunt.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: bb1de5d51afd01cf0aa519f12aa3665bee804efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471673"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Protocolondersteuning voor HTTP-headers in Azure Front Door
In dit artikel wordt het protocol beschreven dat Voordeur ondersteunt met delen van het oproeppad (zie afbeelding). In de volgende secties vindt u meer informatie over HTTP-headers die door Voordeur worden ondersteund.

![Azure Front Door HTTP-headers protocol][1]

>[!IMPORTANT]
>Voordeur certificeert geen HTTP-headers die hier niet zijn gedocumenteerd.

## <a name="client-to-front-door"></a>Klant aan Voordeur
Front Door accepteert de meeste headers van het binnenkomende verzoek zonder ze te wijzigen. Sommige gereserveerde kopteksten worden uit het binnenkomende verzoek verwijderd als ze worden verzonden, inclusief kopteksten met het X-FD-* voorvoegsel.

## <a name="front-door-to-backend"></a>Voordeur naar backend

Voordeur bevat kopteksten van een binnenkomende aanvraag, tenzij verwijderd vanwege beperkingen. Front Door voegt ook de volgende kopteksten toe:

| Header  | Voorbeeld en beschrijving |
| ------------- | ------------- |
| Via |  Via: 1.1 Azure </br> Front Door voegt de HTTP-versie van de client toe, gevolgd door *Azure* als de waarde voor de Via-header. Deze koptekst geeft de HTTP-versie van de client aan en dat Voordeur een tussenpersoon was voor de aanvraag tussen de client en de backend.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Vertegenwoordigt het IP-adres van de client dat is gekoppeld aan de aanvraag die wordt verwerkt. Een aanvraag van een proxy kan bijvoorbeeld de x-doorgestuurde voor-voor-header toevoegen om het IP-adres van de oorspronkelijke beller aan te geven. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Vertegenwoordigt het ip-adres van sockets dat is gekoppeld aan de TCP-verbinding waarvan de huidige aanvraag afkomstig is. Het IP-adres van een client van een aanvraag is mogelijk niet gelijk aan het IP-adres van de socket, omdat het willekeurig kan worden overschreven door een gebruiker.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2TY4TY6SQVjC0zV1NURURTA2MTkANDM3YzgyY2QtMzYwYSYSYTU0LTk0YzMtNWZMNzA3NjQ3Nzgz </br> Een unieke referentiestring die een verzoek van Front Door identificeert. Het wordt gebruikt om toegangslogboeken te doorzoeken en kritieke voor het oplossen van problemen.|
| X-Azure-RequestChain |  X-Azure-RequestChain: hops=1 </br> Een koptekst die Front Door gebruikt om aanvraaglussen te detecteren en gebruikers mogen er geen afhankelijkheid van nemen. |
| X-Doorgestuurd-Voor | X-Forwarded-For: 127.0.0.1 </br> Het HTTP-headerveld X-Forwarded-For (XFF) identificeert vaak het oorspronkelijke IP-adres van een client die verbinding maakt met een webserver via een HTTP-proxy of load balancer. Als er een bestaande XFF-header is, voegt Front Door het IP-socket van de clientsocket eraan toe of voegt u de XFF-header toe aan het IP-socket van de clientsocket. |
| X-Forwarded-Host | X-Forwarded-Host: contoso.azurefd.net </br> Het HTTP-headerveld X-Forwarded-Host is een veelgebruikte methode die wordt gebruikt om de oorspronkelijke host te identificeren die door de client is aangevraagd in de koptekst van de HTTP-aanvraaghost. Dit komt omdat de hostnaam van Front Door kan verschillen voor de backendserver die de aanvraag verwerkt. |
| X-Forwarded-Proto | X-Forwarded-Proto: http X-Forwarded-Proto: http X-Forwarded-Proto: http X- </br> Het kopveld X-Forwarded-Proto HTTP wordt vaak gebruikt om het oorspronkelijke protocol van een HTTP-aanvraag te identificeren, omdat Front Door, op basis van configuratie, met de backend kan communiceren met https. Dit geldt zelfs als het verzoek om de omgekeerde proxy HTTP is. |
| X-FD-HealthProbe | X-FD-HealthProbe HTTP header veld wordt gebruikt om de gezondheid sonde van Front Door te identificeren. Als deze koptekst is ingesteld op 1, is de aanvraag een statussinspectie. U gebruik maken wanneer u toegang wilt nemen vanaf bepaalde frontdoor met het kopveld X-Forwarded-Host. |

## <a name="front-door-to-client"></a>Voordeur naar klant

Eventuele headers die vanaf de backend naar de voordeur worden verzonden, worden ook doorgegeven aan de klant. De volgende zijn headers verzonden van Voordeur naar klanten.

| Header  | Voorbeeld |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2TY4TY6SQVjC0zV1NURURTA2MTkANDM3YzgyY2QtMzYwYSYSYTU0LTk0YzMtNWZMNzA3NjQ3Nzgz* </br> Dit is een unieke referentiestring die een verzoek van Front Door identificeert. Dit is essentieel voor het oplossen van problemen, omdat deze wordt gebruikt om toegangslogboeken te zoeken.|

## <a name="next-steps"></a>Volgende stappen

- [Een Front Door maken](quickstart-create-front-door.md)
- [Hoe Voordeur werkt](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
