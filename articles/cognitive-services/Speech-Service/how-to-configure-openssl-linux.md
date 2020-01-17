---
title: OpenSSL configureren voor Linux
titleSuffix: Azure Cognitive Services
description: Meer informatie over het configureren van OpenSSL voor Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: cadf31dede8ee81323076013d00b9431f597bda6
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156485"
---
# <a name="configure-openssl-for-linux"></a>OpenSSL configureren voor Linux

Wanneer u een Speech SDK-versie vóór 1.9.0 gebruikt, wordt [openssl](https://www.openssl.org) dynamisch geconfigureerd voor de versie van het hostsysteem. In latere versies van de Speech SDK is OpenSSL (versie [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) statisch gekoppeld aan de kern bibliotheek van de spraak-SDK.

## <a name="troubleshoot-connectivity"></a>Problemen met verbindingen oplossen

Als er verbindings fouten optreden bij het gebruik van de 1.9.0-release van de Speech SDK, moet u ervoor zorgen dat de `ssl/certs` Directory bestaat in `/usr/lib` Directory, die wordt gevonden in het Linux-bestands systeem. Als de `ssl/certs` map *niet bestaat*, controleert u of openssl in uw systeem is geïnstalleerd met behulp van de volgende opdracht:

```bash
which openssl
```

Ga vervolgens naar de map OpenSSL `certs` en kopieer de inhoud van die map naar `/usr/lib/ssl/certs` map. Probeer vervolgens opnieuw om te zien of er verbindings problemen zijn opgelost.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Over de Speech SDK](speech-sdk.md)