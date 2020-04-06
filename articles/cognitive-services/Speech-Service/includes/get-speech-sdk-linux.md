---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 14f9d0936a4d1949cf8d7fc69bbb782ee447bdba
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668762"
---
:::row:::
    :::column span="3":::
        De Speech SDK ondersteunt alleen **Ubuntu 16.04/18.04**, **Debian 9,** **Red Hat Enterprise Linux (RHEL) 7/8**en **CentOS 7/8** op de volgende doelarchitecturen bij gebruik met Linux:
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
> Bij het targeten van Linux ARM64 en het gebruik van C# - is de .NET Core 3.x (dotnet-sdk-3.x-pakket) vereist. Als u ZICH richt op ARM32 of ARM64, wordt Python niet ondersteund.

> [!NOTE]
> De x86-architecturen van Ubuntu 16.04, Ubuntu 18.04 en Debian 9 ondersteunen alleen C++ ontwikkeling met de Speech SDK.

### <a name="system-requirements"></a>Systeemvereisten

Voor een native toepassing vertrouwt de `libMicrosoft.CognitiveServices.Speech.core.so`Speech SDK op . Zorg ervoor dat de doelarchitectuur (x86, x64) overeenkomt met de toepassing. Afhankelijk van de Linux-versie kunnen extra afhankelijkheden nodig zijn.

- De gedeelde bibliotheken van de GNU C-bibliotheek (waaronder `libpthreads`de POSIX Threads Programmeerbibliotheek, )
- De OpenSSL-bibliotheek (`libssl.so.1.0.0` of `libssl.so.1.0.2`)
- De gedeelde bibliotheek voor`libasound.so.2`ALSA-toepassingen ( )

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
> Volg de instructies voor [het configureren van RHEL/CentOS 7 voor Speech SDK.](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)

> [!TIP]
> Volg op RHEL/CentOS 8 de instructies voor [het configureren van OpenSSL voor Linux.](../how-to-configure-openssl-linux.md)

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
