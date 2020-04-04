---
title: RHEL/CentOS 7 configureren - Spraakservice
titleSuffix: Azure Cognitive Services
description: Meer informatie over het configureren van RHEL/CentOS 7 zodat de Speech SDK kan worden gebruikt.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: dc09d517d95b5a3f2a88504a14f1451d1de5ffc9
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639161"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>RHEL/CentOS 7 configureren voor Spraak-SDK

Red Hat Enterprise Linux (RHEL) 8 x64 en CentOS 8 x64 worden officieel ondersteund door de Speech SDK versie 1.10.0 en hoger. Het is ook mogelijk om de Speech SDK te gebruiken op RHEL/CentOS 7 x64, maar hiervoor moet de C++ compiler (voor C++ ontwikkeling) en de gedeelde C++ runtime-bibliotheek op uw systeem worden bijgewerkt.

Voer het selectievakje uit om de c++ compilerversie te controleren:

```bash
g++ --version
```

Als de compiler is geïnstalleerd, moet de uitvoer er als volgt uitzien:

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

Dit bericht laat u weten dat GCC belangrijkste versie 4 is geïnstalleerd. Deze versie heeft geen volledige ondersteuning voor de C++ 11-standaard, die de Speech SDK gebruikt. Proberen om een C + + programma samen te stellen met deze GCC versie en de Speech SDK headers zal resulteren in compilatie fouten.

Het is ook belangrijk om de versie van de gedeelde C++ runtime bibliotheek (libstdc++) te controleren. Het grootste deel van de Speech SDK is geïmplementeerd als native C++ bibliotheken, wat betekent dat het afhankelijk is van libstdc++ ongeacht de taal die u gebruikt om toepassingen te ontwikkelen.

Voer het als nog op om de locatie van libstdc++ op uw systeem te vinden:

```bash
ldconfig -p | grep libstdc++
```

De output op vanille RHEL/CentOS 7 (x64) is:

```
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

Op basis van dit bericht wilt u de versiedefinities met deze opdracht controleren:

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

De output moet zijn:

```
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

De SpraakSDK vereist **CXXABI_1.3.9** en **GLIBCXX_3.4.21**. U deze informatie `ldd libMicrosoft.CognitiveServices.Speech.core.so` vinden door te draaien op de Speech SDK-bibliotheken van het Linux-pakket.

> [!NOTE]
> Het wordt aanbevolen dat de versie van GCC geïnstalleerd op het systeem is ten minste **5.4.0**, met bijpassende runtime bibliotheken.

## <a name="example"></a>Voorbeeld

Dit is een voorbeeldopdracht die illustreert hoe u RHEL/CentOS 7 x64 configureert voor ontwikkeling (C++, C#, Java, Python) met de SpraakSDK 1.10.0 of hoger:

```bash
# Only run ONE of the following two commands
# - for CentOS 7:
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
# - for RHEL 7:
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl python3
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free

# Build GCC 5.4.0 and runtimes and install them under /usr/local
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip

# Set SSL cert file location
# (this is required for any development/testing with Speech SDK)
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt

# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-1.10.0/lib/x64:$LD_LIBRARY_PATH

# For Python: install the Speech SDK module
python3 -m pip install azure-cognitiveservices-speech --user
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Info over de Speech-SDK](speech-sdk.md)
