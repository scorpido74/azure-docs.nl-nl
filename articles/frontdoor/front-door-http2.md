---
title: Azure Front Door - HTTP2-ondersteuning | Microsoft Documenten
description: In dit artikel u meer te weten komen over HTTP/2-ondersteuning in Azure Front Door
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
ms.openlocfilehash: 415a89cbccd6b43d64ba1a553d44ac003df5806a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471622"
---
# <a name="http2-support-in-azure-front-door"></a>HTTP/2-ondersteuning in Azure Front Door

Momenteel is HTTP/2-ondersteuning actief voor alle frontdoorconfiguraties. Er is geen verdere actie vereist van klanten.

HTTP/2 is een belangrijke herziening van HTTP/1.1. Het biedt snellere webprestaties, minder responstijd en verbeterde gebruikerservaring, terwijl de bekende HTTP-methoden, statuscodes en semantiek behouden blijven. Hoewel HTTP/2 is ontworpen om te werken met HTTP en HTTPS, ondersteunen veel clientwebbrowsers alleen HTTP/2 via Transport Layer Security (TLS).

### <a name="http2-benefits"></a>HTTP/2 voordelen

De voordelen van HTTP/2 zijn:

*   **Multiplexing en gelijktijdigheid**

    Als u HTTP 1.1 gebruikt, zijn meerdere TCP-verbindingen vereist en is aan elke verbinding de algemene prestaties gekoppeld. Met HTTP/2 kunnen meerdere resources worden aangevraagd op één TCP-verbinding.

*   **Headercompressie**

    Door de HTTP-headers voor geserveerde resources te comprimeren, wordt de tijd op de draad aanzienlijk verkort.

*   **Afhankelijkheden streamen**

    Met streamafhankelijkheden kan de client aan de server aangeven welke resources prioriteit hebben.


## <a name="http2-browser-support"></a>HTTP/2-browserondersteuning

Alle grote browsers hebben HTTP/2-ondersteuning geïmplementeerd in hun huidige versies. Niet-ondersteunde browsers vallen automatisch terug naar HTTP/1.1.

|Browser|Minimale versie|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over HTTP/2 naar de volgende bronnen:

- [HTTP/2-specificatiehomepage](https://http2.github.io/)
- [Officiële HTTP/2 FAQ](https://http2.github.io/faq/)
- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
