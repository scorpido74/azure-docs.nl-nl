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
ms.openlocfilehash: 42960c25c4124203b64646fdc5cbca833b246e21
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683170"
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
> [!NOTE]
> Het is ook vermeldenswaard dat sommige distributies van Linux niet over een TMP of TMPDIR omgeving variabele gedefinieerd. Dit zal ertoe leiden dat de Speech SDK de Certificate Revocation List (CRL) elke keer downloadt, in plaats van de CRL op schijf te plaatsen voor hergebruik totdat deze verloopt. Als u de initiële verbindingsprestaties wilt verbeteren, u [een omgevingsvariabele met de naam TMPDIR maken en deze instellen op het pad van de door u gekozen tijdelijke map.](https://help.ubuntu.com/community/EnvironmentVariables).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Info over de Speech-SDK](speech-sdk.md)
