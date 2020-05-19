---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 03/30/2020
ms.author: gelecaro
ms.openlocfilehash: 6d7d1d261bce671396d539cb7ecb1ecc3d950662
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980120"
---
In deze hand leiding wordt uitgelegd hoe u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) voor Linux installeert

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Systeemvereisten

Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8)

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze Snelstartgids hebt u het volgende nodig:

* gcc
* Go (1,13 of hoger)

* Voor ondersteunde Linux-platformen moeten bepaalde bibliotheken zijn geïnstalleerd ( `libssl` voor de ondersteuning van Secure Sockets Layer en `libasound2` voor geluids ondersteuning). Raadpleeg de onderstaande distributie voor de opdrachten die nodig zijn om de juiste versies van deze bibliotheken te installeren.

   * Op Ubuntu:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * Op Debian 9:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

   * Op RHEL/CentOS 8:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> Volg in RHEL/CentOS 8 de instructies voor het [configureren van openssl voor Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]


## <a name="configure-go-environment"></a>Go-omgeving configureren

1. Omdat de bindingen afhankelijk `cgo` zijn van, moet u de omgevings variabelen instellen, zodat de SDK kan worden gevonden:

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. Om toepassingen uit te voeren, inclusief de SDK, moeten we ook het besturings systeem vertellen waar de bibliotheken zich bevinden:

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<arch>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [windows](../quickstart-list-go.md)]