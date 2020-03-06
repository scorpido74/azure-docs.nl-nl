---
title: OpenSSL voor Linux configureren
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
ms.openlocfilehash: 350c2bf3c4d0fc0a16f1b393e7c8d8a372679797
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331141"
---
# <a name="configure-openssl-for-linux"></a>OpenSSL voor Linux configureren

Wanneer u een Speech SDK-versie vóór 1.9.0 gebruikt, wordt [openssl](https://www.openssl.org) dynamisch geconfigureerd voor de versie van het hostsysteem. In latere versies van de Speech SDK is OpenSSL (versie [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) statisch gekoppeld aan de kern bibliotheek van de spraak-SDK.

Controleer of de OpenSSL-certificaten in uw systeem zijn geïnstalleerd om verbinding te garanderen. Een opdracht uitvoeren:
```bash
openssl version -d
```

De uitvoer op systemen op basis van Ubuntu/Debian moet zijn:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Controleer of er `certs` submap onder OPENSSLDIR is. In het bovenstaande voor beeld zou het worden `/usr/lib/ssl/certs`.

* Als er `/usr/lib/ssl/certs` is en deze veel afzonderlijke certificaat bestanden bevat (met `.crt`-of `.pem`-extensie), is er geen verdere actie nodig.

* Als OPENSSLDIR iets anders is dan `/usr/lib/ssl` en/of er één certificaat bundel bestand is in plaats van meerdere afzonderlijke bestanden, moet u een geschikte SSL-omgevings variabele instellen om aan te geven waar de certificaten kunnen worden gevonden.

## <a name="examples"></a>Voorbeelden

- OPENSSLDIR is `/opt/ssl`. Er is `certs` submap met veel `.crt` of `.pem` bestanden.
Stel de omgevings variabele `SSL_CERT_DIR` in op `/opt/ssl/certs` voordat u een programma uitvoert dat gebruikmaakt van de spraak-SDK. Bijvoorbeeld:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR is `/etc/pki/tls` (bijvoorbeeld op systemen met RHEL/CentOS). Er is `certs` submap met een certificaat bundel bestand, bijvoorbeeld `ca-bundle.crt`.
Stel de omgevings variabele `SSL_CERT_FILE` zodanig in dat dit bestand wordt gebruikt voordat een programma wordt uitgevoerd dat gebruikmaakt van de spraak-SDK. Bijvoorbeeld:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Over de Speech SDK](speech-sdk.md)
