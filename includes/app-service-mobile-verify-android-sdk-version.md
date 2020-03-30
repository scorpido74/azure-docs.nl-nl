---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 46cfb27b8bde95990d13ec4bca4e96f25cfe9dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176550"
---
Vanwege de voortdurende ontwikkeling komt de Android SDK-versie die in Android Studio is geïnstalleerd mogelijk niet overeen met de versie in de code. De Android SDK waarnaar in deze tutorial wordt verwezen is versie 26, de laatste op het moment van schrijven. Het versienummer kan toenemen naarmate er nieuwe releases van de SDK verschijnen en we raden u aan de nieuwste versie te gebruiken.

Twee symptomen van versie mismatch zijn:

- Wanneer u het project bouwt of opnieuw bouwt, `Gradle sync failed: Failed to find target with hash string 'android-XX'`kunt u foutberichten van Gradle krijgen, zoals .
- Standaard Android-objecten in code `import` die moeten worden opgelost op basis van instructies, genereren mogelijk foutmeldingen.

Als een van deze verschijnt, komt de versie van de Android SDK die in Android Studio is geïnstalleerd mogelijk niet overeen met het SDK-doel van het gedownloade project. Als u de versie wilt verifiëren, voert u de volgende wijzigingen aan:

1. Klik in Android Studio op **Extra** > **Android** > SDK**Manager**. Als u de nieuwste versie van het SDK-platform niet hebt geïnstalleerd, klikt u om deze te installeren. Noteer het versienummer.

2. Open op het tabblad **Project Explorer** onder **Gradle Scripts**de **bestandsbuild.gradle (Module: app).** Zorg ervoor dat de **compileSdkVersion** en **targetSdkVersion** zijn ingesteld op de nieuwste SDK-versie geïnstalleerd. Het `build.gradle` kan er zo uitzien:

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
