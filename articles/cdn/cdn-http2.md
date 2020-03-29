---
title: HTTP/2-ondersteuning in Azure CDN | Microsoft Documenten
description: Meer informatie over http/2- en CDN-ondersteuning.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: ril
ms.openlocfilehash: 703623e3f7f314d87417458f3f9a218dfdf45427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67849976"
---
# <a name="http2-support-in-azure-cdn"></a>HTTP/2-ondersteuning in Azure CDN

HTTP/2 is een belangrijke herziening van HTTP/1.1\. Het biedt snellere webprestaties, minder responstijd en verbeterde gebruikerservaring, terwijl de bekende HTTP-methoden, statuscodes en semantiek behouden blijven. Hoewel HTTP/2 is ontworpen om te werken met HTTP en HTTPS, ondersteunen veel clientwebbrowsers alleen HTTP/2 via TLS.

### <a name="http2-benefits"></a>HTTP/2-voordelen

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

## <a name="enabling-http2-support-in-azure-cdn"></a>HTTP/2-ondersteuning inschakelen in Azure CDN

Momenteel is HTTP/2-ondersteuning actief voor alle Azure CDN-profielen. Er is geen verdere actie vereist van klanten.

## <a name="next-steps"></a>Volgende stappen

Om de voordelen van HTTP/2 in actie te zien, zie [deze demo van Akamai.](https://http2.akamai.com/demo)

Ga voor meer informatie over HTTP/2 naar de volgende bronnen:

*   [HTTP/2-specificatiehomepage](https://http2.github.io/)
*   [Officiële HTTP/2 FAQ](https://http2.github.io/faq/)
*   [Akamai HTTP/2 informatie](https://http2.akamai.com/)

Zie het [Azure CDN-overzicht](https://azure.microsoft.com/documentation/articles/cdn-overview/)voor meer informatie over de beschikbare functies van Azure CDN.