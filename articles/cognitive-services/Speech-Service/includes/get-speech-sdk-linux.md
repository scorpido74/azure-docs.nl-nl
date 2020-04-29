---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: e47c8bc4dc814f1d4c5cb115a2da911544dd55f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399992"
---
:::row:::
    :::column span="3":::
        De Speech SDK ondersteunt alleen **Ubuntu 16.04/18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 7/8**en **CentOS 7/8** op de volgende doel architecturen wanneer deze worden gebruikt in combi natie met Linux:
        - x64
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Bij het richten op Linux-ARM64 en het gebruik van C#-het .NET Core 3. x (DotNet-SDK-3. x-pakket) is vereist. Als u bent gericht op ARM32 of ARM64, wordt python niet ondersteund.

> [!NOTE]
> De x86-architecturen van Ubuntu 16,04, Ubuntu 18,04 en Debian 9 ondersteunen alleen C++-ontwikkeling met de spraak-SDK.

### <a name="system-requirements"></a>Systeemvereisten

Voor een systeem eigen toepassing is de spraak-SDK afhankelijk `libMicrosoft.CognitiveServices.Speech.core.so`van. Zorg ervoor dat de doel architectuur (x86, x64) overeenkomt met de toepassing. Afhankelijk van de Linux-versie zijn mogelijk extra afhankelijkheden vereist.

- De gedeelde bibliotheken van de GNU C-bibliotheek (met inbegrip van de programmeer `libpthreads`bibliotheek POSIX-threads)
- De OpenSSL-bibliotheek`libssl.so.1.0.0` ( `libssl.so.1.0.2`of)
- De gedeelde bibliotheek voor ALSA-toepassingen`libasound.so.2`()

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
> Volg de instructies voor het [configureren van RHEL/CentOS 7 voor Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).

> [!TIP]
> Volg in RHEL/CentOS 8 de instructies voor het [configureren van openssl voor Linux](../how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
