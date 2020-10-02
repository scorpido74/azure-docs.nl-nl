---
title: Protocol ondersteuning voor HTTP-headers in azure front-deur | Microsoft Docs
description: In dit artikel worden de HTTP-header protocollen beschreven die front-deur ondersteunt.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: cd721f13ffa128e83072819a20b17f305118b13c
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91626289"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Protocol ondersteuning voor HTTP-headers in azure front-deur
Dit artikel bevat een overzicht van het protocol dat front-deur ondersteunt met delen van het aanroepende pad (zie afbeelding). De volgende secties bevatten meer informatie over HTTP-headers die worden ondersteund door de voor deur.

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Azure front-deur HTTP-headers Protocol":::

>[!IMPORTANT]
>De voor deur certificeert geen HTTP-headers die hier niet worden beschreven.

## <a name="client-to-front-door"></a>Client naar Front Door
De voor deur accepteert de meeste headers voor de binnenkomende aanvraag zonder deze te wijzigen. Sommige gereserveerde headers worden bij verzen ding verwijderd uit de inkomende aanvraag, inclusief kopteksten met het X-FD-*-voor voegsel.

## <a name="front-door-to-backend"></a>Voor deur naar back-end

De voor deur bevat kopteksten voor een binnenkomende aanvraag, tenzij ze worden verwijderd vanwege beperkingen. Met de voor deur worden ook de volgende headers toegevoegd:

| Koptekst  | Voor beeld en beschrijving |
| ------------- | ------------- |
| Kopen |  Via: 1,1 Azure </br> De voor deur voegt de HTTP-versie van de client, gevolgd door *Azure* , toe als de waarde voor de via-header. Deze header geeft de HTTP-versie van de client aan en de voor deur was een tussenliggende ontvanger voor de aanvraag tussen de client en de back-end.  |
| X-Azure-client | X-Azure-client: 127.0.0.1 </br> Vertegenwoordigt het client-IP-adres dat is gekoppeld aan de aanvraag die wordt verwerkt. Een aanvraag die afkomstig is van een proxy, kan bijvoorbeeld de X-doorgestuurde header toevoegen om het IP-adres van de oorspronkelijke aanroeper aan te geven. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Vertegenwoordigt het IP-adres van de socket dat is gekoppeld aan de TCP-verbinding waarvan de huidige aanvraag afkomstig is. Het client-IP-adres van een aanvraag is mogelijk niet gelijk aan het IP-adres van het socket omdat het wille keurig kan worden overschreven door een gebruiker.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Een unieke verwijzings reeks die een aanvraag identificeert die wordt aangeboden door de voor deur. Het wordt gebruikt voor het zoeken naar Logboeken voor toegang en kritiek voor het oplossen van problemen.|
| X-Azure-RequestChain |  X-Azure-RequestChain: hops = 1 </br> Een koptekst die front-deur gebruikt voor het detecteren van aanvraag lussen en gebruikers mogen geen afhankelijkheid aannemen. |
| X-Azure-FDID | X-Azure-FDID: 55ce4ed1-4b06-4bf1-b40e-4638452104da<br/> Een referentie teken reeks waarmee de aanvraag wordt geïdentificeerd, is afkomstig van een specifieke front-deur resource. De waarde kan worden weer gegeven in azure portal of worden opgehaald met behulp van de beheer-API. U kunt deze header in combi natie met IP-Acl's gebruiken om het eind punt te vergren delen zodat alleen aanvragen van een specifieke voor deur worden geaccepteerd. Raadpleeg de veelgestelde vragen voor [meer informatie](front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door) |
| X-doorgestuurd-voor | X-doorgestuurd-voor: 127.0.0.1 </br> Het veld HTTP-header voor X-forwarding (XFF) identificeert vaak het oorspronkelijke IP-adres van een client die verbinding maakt met een webserver via een HTTP-proxy of load balancer. Als er zich een bestaande XFF-header bevindt, wordt het IP-adres van de client aan de voor deur toegevoegd of wordt de XFF-header toegevoegd met het IP-adres van de client. |
| X-doorgestuurd-host | X-doorgestuurd-host: contoso.azurefd.net </br> Het veld X-forward-host-HTTP-header is een gemeen schappelijke methode voor het identificeren van de oorspronkelijke host die door de client is aangevraagd in de HTTP-aanvraag header van de host. Dit komt doordat de hostnaam van de voor deur afwijkt van de back-endserver waarbij de aanvraag wordt verwerkt. |
| X-doorgestuurd-proto | X-doorgestuurd-proto: http </br> Het veld HTTP-header X-doorgestuurd-proto wordt vaak gebruikt om het oorspronkelijke protocol van een HTTP-aanvraag te identificeren omdat de voor deur, op basis van de configuratie, kan communiceren met de back-end met behulp van HTTPS. Dit geldt ook als de aanvraag voor de omgekeerde proxy HTTP is. |
| X-FD-HealthProbe | X-FD-HealthProbe HTTP-koptekst veld wordt gebruikt om de status test van de voor deur te identificeren. Als deze header is ingesteld op 1, is de status test van de aanvraag. U kunt gebruiken wanneer u de toegang wilt beperken tot een bepaalde front-deur met X-doorgestuurd-host-header veld. |
|X-Azure-FDID | X-Azure-FDID-header: 437c82cd-360a-4a54-94c3-5ff707647783 </br> Dit veld bevat frontdoorID die kunnen worden gebruikt voor het identificeren van de voor deur van de inkomende aanvraag. Dit veld wordt ingevuld door de voor deur-service. | 


## <a name="front-door-to-client"></a>Voor deur aan client

Alle kopteksten die naar de voor deur van de back-end worden verzonden, worden ook door gegeven aan de client. De volgende headers worden verzonden van de voor deur naar clients.

| Koptekst  | Voorbeeld |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Dit is een unieke verwijzings reeks die een aanvraag identificeert die wordt aangeboden door de voor deur, wat van essentieel belang is voor het oplossen van problemen met toegang tot logboeken.|

## <a name="next-steps"></a>Volgende stappen

- [Een voor deur maken](quickstart-create-front-door.md)
- [De werking van de voor deur](front-door-routing-architecture.md)
