---
title: RHEL/CentOS 7-speech service configureren
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80639161"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>RHEL/CentOS 7 voor Speech SDK configureren

Red Hat Enterprise Linux (RHEL) 8 x64 en CentOS 8 x64 worden officieel ondersteund door de Speech SDK-versie 1.10.0 en hoger. Het is ook mogelijk om de Speech SDK te gebruiken op RHEL/CentOS 7 x64, maar hiervoor moet de C++-compiler worden bijgewerkt (voor C++-ontwikkeling) en de gedeelde C++ runtime-bibliotheek op uw systeem.

Als u de versie van C++ compiler wilt controleren, voert u de volgende handelingen uit:

```bash
g++ --version
```

Als de compiler is geïnstalleerd, ziet de uitvoer er als volgt uit:

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

Dit bericht laat u weten dat GCC primaire versie 4 is geïnstalleerd. Deze versie biedt geen volledige ondersteuning voor de C++ 11-standaard, die door de Speech SDK wordt gebruikt. Bij het compileren van een C++-programma met deze GCC-versie en de Speech SDK-headers zullen compilatie fouten optreden.

Het is ook belang rijk om de versie van de gedeelde C++ Runtime Library (libstdc + +) te controleren. De meeste spraak-SDK wordt geïmplementeerd als systeem eigen C++-bibliotheken, wat betekent dat dit afhankelijk is van libstdc + +, ongeacht de taal die u gebruikt om toepassingen te ontwikkelen.

Voer de volgende opdracht uit om de locatie van libstdc + + op uw systeem te vinden:

```bash
ldconfig -p | grep libstdc++
```

De uitvoer op vanille RHEL/CentOS 7 (x64) is:

```
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

Op basis van dit bericht wilt u de versie definities controleren met deze opdracht:

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

De uitvoer moet:

```
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

Voor de Speech SDK zijn **CXXABI_1.3.9** en **GLIBCXX_3.4.21**vereist. U kunt deze informatie vinden door uit `ldd libMicrosoft.CognitiveServices.Speech.core.so` te voeren op de Speech SDK-bibliotheken vanuit het Linux-pakket.

> [!NOTE]
> Het is raadzaam de versie van GCC die op het systeem is geïnstalleerd, ten minste **5.4.0**te hebben, met overeenkomende runtime-bibliotheken.

## <a name="example"></a>Voorbeeld

Dit is een voorbeeld opdracht die illustreert hoe u RHEL/CentOS 7 x64 configureert voor ontwikkeling (C++, C#, Java, python) met de Speech SDK 1.10.0 of hoger:

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
