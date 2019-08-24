---
title: De huidige POP-IP-lijst voor Azure CDN ophalen | Microsoft Docs
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
ms.openlocfilehash: bc8e8219c8f8de75b01c584a2a5ce13cc1429fec
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991780"
---
# <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>De huidige Verizon POP IP-lijst voor Azure CDN ophalen

U kunt de REST API gebruiken om de set IP-adressen op te halen voor de POP-servers (Point of Presence) van Verizon. Deze POP-servers maken aanvragen voor de oorspronkelijke servers die zijn gekoppeld aan de Azure Content Delivery Network (CDN)-eind punten op een Verizon-Profiel (**Azure CDN Standard van Verizon** of **Azure CDN Premium van Verizon**). Houd er rekening mee dat deze set IP-adressen verschilt van de IP-adressen die een client zou zien wanneer ze aanvragen indienen bij de Pop's. 

Zie [Edge nodes-List](https://docs.microsoft.com/rest/api/cdn/edgenodes/list)(Engelstalig) voor de syntaxis van de rest API bewerking voor het ophalen van de POP-lijst.

# <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>De huidige micro soft POP-IP-lijst voor Azure CDN ophalen

Als u uw toepassing wilt vergren delen zodat alleen verkeer van Azure CDN van micro soft wordt geaccepteerd, moet u IP-Acl's instellen voor uw back-end. U kunt ook de set geaccepteerde waarden voor de header X-forward-host die door Azure CDN van micro soft is verzonden, beperken. Deze stappen worden hieronder beschreven:

Configureer IP-Acl's voor voor uw back-ends om verkeer te accepteren van Azure CDN van de back-end-IP-adres ruimte van micro soft en alleen de infrastructuur services van Azure. 

* Azure CDN van de IPv4-back-end-IP-ruimte van micro soft: 147.243.0.0/16
* Azure CDN van de IPv6-back-end-IP-ruimte van micro soft: 2a01:111:2050::/44

IP-adresbereiken en service tags voor micro soft-Services vindt u [hier](https://www.microsoft.com/download/details.aspx?id=56519)

Filter op de waarden voor de inkomende header X-forward-host die is verzonden door Azure CDN van micro soft. De enige toegestane waarden voor de header moeten alle endpoint-hosts zijn zoals gedefinieerd in uw CDN-configuratie. Meer specifiek, alleen de hostnamen waarvoor u verkeer wilt accepteren, op deze specifieke oorsprong van uw bedrijf.

## <a name="typical-use-case"></a>Typische gebruiksscenario’s

Uit veiligheids overwegingen kunt u deze IP-lijst gebruiken om af te dwingen dat aanvragen naar uw oorspronkelijke server alleen worden gemaakt op basis van een geldige Verizon-POP. Als iemand bijvoorbeeld de hostnaam of het IP-adres voor de oorspronkelijke server van een CDN-eind punt heeft gedetecteerd, kan de aanvraag rechtstreeks naar de oorspronkelijke server worden uitgevoerd, waardoor de schaal-en beveiligings mogelijkheden van Azure CDN worden omzeild. Dit scenario kan worden voor komen door de IP-adressen in de geretourneerde lijst als de enige toegestane IP-adressen op een bron server in te stellen. Om ervoor te zorgen dat u de meest recente POP-lijst hebt, moet u deze mini maal één keer per dag ophalen. 

## <a name="next-steps"></a>Volgende stappen

Zie [Azure CDN rest API](https://docs.microsoft.com/rest/api/cdn/)voor meer informatie over de rest API.
