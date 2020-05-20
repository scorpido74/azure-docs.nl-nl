---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: a93c478f0621bf62b710f58f3e6f06298bad9954
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673047"
---
:::row:::
    :::column span="3":::
        De Speech SDK ondersteunt alleen **Ubuntu 16.04/18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 7/8**en **CentOS 7/8** op de volgende doel architecturen wanneer deze worden gebruikt in combi natie met Linux:
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

- x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) en ARM64 (Debian/Ubuntu) voor C++-ontwikkeling
- x64, ARM32 (Debian/Ubuntu) en ARM64 (Debian/Ubuntu) voor Java
- x64, ARM32 (Debian/Ubuntu) en ARM64 (Debian/Ubuntu) voor .NET core
- x64 voor python

> [!IMPORTANT]
> Voor C# op Linux ARM64 is het .NET Core 3. x (DotNet-SDK-3. x-pakket) vereist.

### <a name="system-requirements"></a>Systeemvereisten

Voor een systeem eigen toepassing is de spraak-SDK afhankelijk van `libMicrosoft.CognitiveServices.Speech.core.so` . Zorg ervoor dat de doel architectuur (x86, x64) overeenkomt met de toepassing. Afhankelijk van de Linux-versie zijn mogelijk extra afhankelijkheden vereist.

- De gedeelde bibliotheken van de GNU C-bibliotheek (met inbegrip van de programmeer bibliotheek POSIX-threads `libpthreads` )
- De OpenSSL-bibliotheek ( `libssl.so.1.0.0` of `libssl.so.1.0.2` )
- De gedeelde bibliotheek voor ALSA-toepassingen ( `libasound.so.2` )

# <a name="ubuntu-16041804"></a>[Ubuntu 16.04/18.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 en CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> - Volg op RHEL/CentOS 7 de instructies voor het [configureren van RHEL/CentOS 7 voor Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Volg in RHEL/CentOS 8 de instructies voor het [configureren van openssl voor Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
