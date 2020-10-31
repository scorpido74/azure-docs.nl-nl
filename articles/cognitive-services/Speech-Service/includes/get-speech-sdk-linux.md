---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: d0298bcd675b1b94999dab3a1ad1c40a6feb7438
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135648"
---
:::row:::
    :::column span="3":::
        De Speech SDK ondersteunt alleen **Ubuntu 16.04/18.04/20.04** , **Debian 9/10** , **Red Hat Enterprise Linux (RHEL) 7/8** en **CentOS 7/8** op de volgende doel architecturen wanneer deze worden gebruikt in combi natie met Linux:
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

# <a name="ubuntu-160418042004"></a>[Ubuntu 16.04/18.04/20.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

> [!NOTE]
> Als libssl 1.0. x niet beschikbaar is, installeert u in plaats daarvan libssl 1.1.

# <a name="debian-910"></a>[Debian 9/10](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

> [!NOTE]
> Als libssl 1.0. x niet beschikbaar is, installeert u in plaats daarvan libssl 1.1.

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 en CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> - Volg op RHEL/CentOS 7 de instructies op [RHEL/CentOS 7 configureren voor Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Volg op RHEL/CentOS 8 de instructies op [OpenSSL configureren voor Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
