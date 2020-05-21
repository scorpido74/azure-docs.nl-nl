---
title: 'Snelstartgids: Speech SDK voor Java (Windows, Linux, macOS) platform Setup-Speech Service'
titleSuffix: Azure Cognitive Services
description: Gebruik deze hand leiding om uw platform in te stellen voor het gebruik van Java (Windows, Linux, macOS) met de Speech Service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 94946b4b6d0f0fc9c94cac7aca1e971b3f6262f9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673217"
---
In deze hand leiding wordt beschreven hoe u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) installeert voor 64-bits Java 8 jre. Als u alleen de naam van het pakket wilt laten beginnen, is de Java-SDK niet beschikbaar in de Maven-centrale opslag plaats. Of u nu Gradle of een `pom.xml` afhankelijkheids bestand gebruikt, u moet een aangepaste opslag plaats toevoegen waarnaar wordt verwezen `https://csspeechstorage.blob.core.windows.net/maven/` (zie hieronder voor de naam van het pakket).

> [!NOTE]
> Zie [Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md) voor de Speech Devices SDK en het Roobo-apparaat.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

- Het pakket java Speech SDK is beschikbaar voor deze besturings systemen:
  - Windows: 64-alleen-bits
  - Mac: macOS X versie 10,13 of hoger
  - Linux: 64-bit alleen op Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 7/8, CentOS 7/8

## <a name="prerequisites"></a>Vereisten

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) of [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclips Java IDE](https://www.eclipse.org/downloads/) (vereist dat Java al is geïnstalleerd)
- Voor ondersteunde Linux-platformen moeten bepaalde bibliotheken zijn geïnstalleerd ( `libssl` voor de ondersteuning van Secure Sockets Layer en `libasound2` voor geluids ondersteuning). Raadpleeg de onderstaande distributie voor de opdrachten die nodig zijn om de juiste versies van deze bibliotheken te installeren.

  - Voer op Ubuntu de volgende opdrachten uit om de vereiste pakketten te installeren:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - Voer op Debian 9 de volgende opdrachten uit om de vereiste pakketten te installeren:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

  - Voer op RHEL/CentOS de volgende opdrachten uit om de vereiste pakketten te installeren:

        ```sh
        sudo yum update
        sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
        ```

> [!NOTE]
> - Volg op RHEL/CentOS 7 de instructies voor het [configureren van RHEL/CentOS 7 voor Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Volg in RHEL/CentOS 8 de instructies voor het [configureren van openssl voor Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- In Windows hebt u [micro soft Visual C++ Redistributable voor Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) voor uw platform nodig. Houd er rekening mee dat als u dit voor de eerste keer installeert, Windows opnieuw moet worden opgestart voordat u doorgaat met deze hand leiding.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Een eclips-project maken en de spraak-SDK installeren

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [windows](../quickstart-list.md)]
