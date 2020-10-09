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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81683170"
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

Controleer of er een `certs` submap onder OPENSSLDIR is. In het bovenstaande voor beeld zou het zijn `/usr/lib/ssl/certs` .

* Als `/usr/lib/ssl/certs` dat het geval is en een groot aantal afzonderlijke certificaat bestanden (met `.crt` of `.pem` uitbrei ding) bevat, is er geen verdere actie nodig.

* Als OPENSSLDIR iets anders is dan `/usr/lib/ssl` en/of er één certificaat bundel bestand is in plaats van meerdere afzonderlijke bestanden, moet u een geschikte SSL-omgevings variabele instellen om aan te geven waar de certificaten kunnen worden gevonden.

## <a name="examples"></a>Voorbeelden

- OPENSSLDIR is `/opt/ssl` . Er is een `certs` submap met veel `.crt` of `.pem` bestanden.
Stel de omgevings variabele `SSL_CERT_DIR` in op om aan te wijzen `/opt/ssl/certs` voordat een programma wordt uitgevoerd dat gebruikmaakt van de spraak-SDK. Bijvoorbeeld:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR is `/etc/pki/tls` (zoals op RHEL/CentOS gebaseerde systemen). Er is `certs` bijvoorbeeld een submap met een certificaat bundel bestand `ca-bundle.crt` .
Stel de omgevings variabele `SSL_CERT_FILE` zodanig in dat deze naar dat bestand verwijst voordat u een programma uitvoert dat gebruikmaakt van de spraak-SDK. Bijvoorbeeld:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```
> [!NOTE]
> Het is ook een goed idee dat er voor sommige distributies van Linux geen omgevings variabele TMP of TMPDIR is gedefinieerd. Dit zorgt ervoor dat de spraak-SDK elke keer de certificaatintrekkingslijst (CRL) downloadt, in plaats van de CRL in de cache te plaatsen op de schijf voor hergebruik totdat deze verloopt. Als u de initiële verbindings prestaties wilt verbeteren, kunt u [een omgevings variabele met de naam tmpdir maken en deze instellen op het pad van de gekozen tijdelijke map.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Info over de Speech-SDK](speech-sdk.md)
