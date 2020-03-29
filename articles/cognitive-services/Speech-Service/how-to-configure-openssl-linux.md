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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331141"
---
# <a name="configure-openssl-for-linux"></a>OpenSSL voor Linux configureren

Wanneer u een Speech SDK-versie vóór 1.9.0 gebruikt, wordt [OpenSSL](https://www.openssl.org) dynamisch geconfigureerd voor de host-systeemversie. In latere versies van de Speech SDK is OpenSSL (versie [1.1.1b)](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)statisch gekoppeld aan de kernbibliotheek van de Speech SDK.

Controleer om de connectiviteit te garanderen of OpenSSL-certificaten in uw systeem zijn geïnstalleerd. Voer een opdracht uit:
```bash
openssl version -d
```

De output op Ubuntu/Debian gebaseerde systemen moet zijn:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Controleer of `certs` er subdirectory onder OPENSSLDIR. In het bovenstaande voorbeeld `/usr/lib/ssl/certs`zou het zijn .

* Als er `/usr/lib/ssl/certs` is en het bevat `.crt` veel `.pem` individuele certificaatbestanden (met of extensie), is er geen noodzaak voor verdere acties.

* Als OPENSSLDIR iets `/usr/lib/ssl` anders is dan en/of er één certificaatbundelbestand is in plaats van meerdere afzonderlijke bestanden, moet u een geschikte VARIABELE VOOR SSL-omgeving instellen om aan te geven waar de certificaten kunnen worden gevonden.

## <a name="examples"></a>Voorbeelden

- OPENSSLDIR `/opt/ssl`is . Er `certs` is subdirectory `.crt` `.pem` met veel of bestanden.
Stel omgevingsvariabele `SSL_CERT_DIR` in `/opt/ssl/certs` om te wijzen voordat u een programma uitvoert dat de SpraakSDK gebruikt. Bijvoorbeeld:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR `/etc/pki/tls` is (zoals op RHEL/CentOS gebaseerde systemen). Er `certs` is subdirectory met een certificaat `ca-bundle.crt`bundel bestand, bijvoorbeeld .
Stel omgevingsvariabele `SSL_CERT_FILE` in om op dat bestand te wijzen voordat u een programma uitvoert dat de Spraak-SDK gebruikt. Bijvoorbeeld:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Info over de Speech-SDK](speech-sdk.md)
