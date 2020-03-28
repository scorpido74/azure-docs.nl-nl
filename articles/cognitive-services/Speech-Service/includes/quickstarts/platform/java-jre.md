---
title: 'Quickstart: Speech SDK voor Java (Windows, Linux, macOS) platform setup - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Gebruik deze handleiding om uw platform in te stellen voor het gebruik van Java (Windows, Linux, macOS) met de Spraakservice SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 7147f0d13c88c1d2e17e81a360a5aee55ee760ed
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925894"
---
Deze handleiding laat zien hoe je de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) voor 64-bits Java 8 JRE installeert. Als u alleen wilt dat de pakketnaam zelf aan de slag gaat, is de Java SDK niet beschikbaar in de centrale opslagplaats van Maven. Of u nu Gradle `pom.xml` of een afhankelijkheidsbestand gebruikt, u moet `https://csspeechstorage.blob.core.windows.net/maven/` een aangepaste opslagplaats toevoegen waarnaar wordt gericht (zie hieronder voor de naam van het pakket).

> [!NOTE]
> Zie [Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md) voor de Speech Devices SDK en het Roobo-apparaat.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

- Het Java Speech SDK-pakket is beschikbaar voor deze besturingssystemen:
  - Windows: alleen 64-bits
  - Mac: macOS X versie 10.13 of hoger
  - Linux: 64-bits alleen op Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8

## <a name="prerequisites"></a>Vereisten

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) of [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/) (vereist Java al geïnstalleerd)
- Ondersteunde Linux-platforms vereisen bepaalde geïnstalleerde`libssl` bibliotheken (voor ondersteuning `libasound2` van beveiligde sockets laag en voor geluidsondersteuning). Raadpleeg hieronder uw distributie voor de opdrachten die nodig zijn om de juiste versies van deze bibliotheken te installeren.

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

  - Voer op RHEL/CentOS 8 de volgende opdrachten uit om de vereiste pakketten te installeren:

        ```sh
        sudo yum update
        sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
        ```

> [!NOTE]
> Volg op RHEL/CentOS 8 de instructies voor [het configureren van OpenSSL voor Linux.](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)

- In Windows heb je de [Microsoft Visual C++ Redistributable voor Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) nodig voor je platform. Houd er rekening mee dat het installeren van deze voor de eerste keer kan vereisen dat u Windows opnieuw opstart voordat u verder gaat met deze handleiding.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Maak een Eclipse-project en installeer de Speech SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [windows](../quickstart-list.md)]
