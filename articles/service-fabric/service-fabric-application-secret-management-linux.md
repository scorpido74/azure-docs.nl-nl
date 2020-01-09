---
title: Een versleutelings certificaat voor Linux-clusters instellen
description: Meer informatie over het instellen van een versleutelings certificaat en het versleutelen van geheimen op Linux-clusters.
author: shsha
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: 350718e4ce890fcbfaa7f2b10cc4c47dfac4da90
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614703"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Een versleutelings certificaat instellen en geheimen op Linux-clusters versleutelen
In dit artikel wordt beschreven hoe u een versleutelings certificaat instelt en hoe u het kunt gebruiken voor het versleutelen van geheimen op Linux-clusters. Zie [een versleutelings certificaat instellen en geheimen op Windows-clusters versleutelen][secret-management-windows-specific-link]voor Windows-clusters.

## <a name="obtain-a-data-encipherment-certificate"></a>Een certificaat voor gegevens codering verkrijgen
Een gegevens versleuteling certificaat wordt uitsluitend gebruikt voor het versleutelen en ontsleutelen van [para meters][parameters-link] in de instellingen van een service. XML en [omgevings variabelen][environment-variables-link] in de ServiceManifest. XML van een service. Het wordt niet gebruikt voor verificatie of ondertekening van versleutelings tekst. Het certificaat moet voldoen aan de volgende vereisten:

* Het certificaat moet een persoonlijke sleutel bevatten.
* Het gebruik van de certificaat sleutel moet gegevens codering (10) bevatten en mag geen server verificatie of client verificatie omvatten.

  De volgende opdrachten kunnen bijvoorbeeld worden gebruikt voor het genereren van het vereiste certificaat met behulp van OpenSSL:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Het certificaat in uw cluster installeren
Het certificaat moet worden geïnstalleerd op elk knoop punt in het cluster onder `/var/lib/sfcerts`. Het gebruikers account waarmee de service wordt uitgevoerd (standaard sfuser) **moet lees toegang hebben** tot het geïnstalleerde certificaat (`/var/lib/sfcerts/TestCert.pem` voor het huidige voor beeld).

## <a name="encrypt-secrets"></a>Geheimen versleutelen
Het volgende code fragment kan worden gebruikt voor het versleutelen van een geheim. Met dit fragment wordt alleen de waarde versleuteld. de versleutelings tekst wordt **niet** ondertekend. **U moet** hetzelfde versleuteling certificaat gebruiken dat is geïnstalleerd in uw cluster voor het maken van gecodeerde tekst voor geheime waarden.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
De resulterende met base 64 gecodeerde teken reeks uitvoer naar versleutelde. txt bevat zowel de geheime code ring als informatie over het certificaat dat is gebruikt om het te versleutelen. U kunt de geldigheid ervan controleren door deze te ontsleutelen met OpenSSL.
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
