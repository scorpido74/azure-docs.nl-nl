---
title: Een versleutelingscertificaat instellen op Linux-clusters
description: Meer informatie over het instellen van een versleutelingscertificaat en het versleutelen van geheimen op Linux-clusters.
author: shsha
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: b8e0a19e3f654fc561e7c7e26c6a2da463e24d5f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969036"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Een versleutelingscertificaat instellen en geheimen versleutelen op Linux-clusters
In dit artikel ziet u hoe u een versleutelingscertificaat instellen en gebruiken om geheimen op Linux-clusters te versleutelen. Zie Een [versleutelingscertificaat instellen en geheimen versleutelen op Windows-clusters][secret-management-windows-specific-link]voor Windows-clusters.

## <a name="obtain-a-data-encipherment-certificate"></a>Een certificaat voor gegevensvercijfering verkrijgen
Een certificaat voor gegevensversleuteling wordt strikt gebruikt voor versleuteling en decryptie van [parameters][parameters-link] in de instellingen.xml en [omgevingsvariabelen][environment-variables-link] van een service in serviceManifest.xml van een service. Het wordt niet gebruikt voor authenticatie of ondertekening van cijfertekst. Het certificaat moet aan de volgende eisen voldoen:

* Het certificaat moet een privésleutel bevatten.
* Het gebruik van de certificaatsleutel moet gegevenscodering (10) bevatten en mag geen serververificatie of clientverificatie bevatten.

  De volgende opdrachten kunnen bijvoorbeeld worden gebruikt om het vereiste certificaat te genereren met OpenSSL:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Het certificaat in uw cluster installeren
Het certificaat moet op elk knooppunt in `/var/lib/sfcerts`het cluster onder . Het gebruikersaccount waaronder de service wordt uitgevoerd (standaard sfuser) **moet leestoegang hebben tot** het geïnstalleerde certificaat (dat wil zeggen, `/var/lib/sfcerts/TestCert.pem` voor het huidige voorbeeld).

## <a name="encrypt-secrets"></a>Geheimen versleutelen
Het volgende fragment kan worden gebruikt om een geheim te versleutelen. Dit fragment versleutelt alleen de waarde; het ondertekent **niet** de cijfertekst. **U moet** hetzelfde vercijferingscertificaat gebruiken dat in uw cluster is geïnstalleerd om cijfertekst voor geheime waarden te produceren.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt | tr -d '\n' > plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
De resulterende base-64 gecodeerde tekenreeks uitvoer naar encrypted.txt bevat zowel de geheime ciphertext als informatie over het certificaat dat werd gebruikt om het te versleutelen. U de geldigheid ervan verifiëren door deze te decoderen met OpenSSL.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [opgeven van versleutelde geheimen in een toepassing.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
